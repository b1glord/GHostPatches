GameList patch by [uakf.b](https://github.com/uakfdotb)
=========================

Ready patched bot:
------------------
https://github.com/Grief-Code/GHostPatchedBot/commit/3f2abc6e967dd2615c4679e02b21f5718bc71369

[uakf.b](https://github.com/uakfdotb):
--------
   This patch will put the current lobby game on your GHost++ into a mysql database, so that if you have multiple hosts running you can get one gamelist and you can also display games on a website. It'll also add a !games command to get the list of games.

- You need to add a table:
   ````SQL
   CREATE TABLE gamelist (id INT NOT NULL PRIMARY KEY AUTO_INCREMENT, botid INT, gamename VARCHAR(128),
   ownername VARCHAR(32), creatorname VARCHAR(32), map VARCHAR(100), slotstaken INT, slotstotal INT,
   usernames VARCHAR(512), totalgames INT, totalplayers INT) ENGINE = MEMORY;
   ````

- Then, you need to create entry with botid for each bot. If your bot's id is 1:
   ````SQL
   INSERT INTO gamelist (botid) VALUES ('1');
   ````

- Repeat for all your bots. You might want to change the frequency of updates in ghost.cpp (search for "update gamelist every"). After you're done inserting, you may also want to clean the table so that clients accessing it don't see messages for null values:
   ````SQL
   UPDATE gamelist SET gamename = '', ownername = '', creatorname =  '', map = '', slotstaken = 0, slotstotal = 0,
   usernames = '', totalgames = 0, totalplayers = 0;
   ````
   Also, note that because this uses the memory engine, you'll have to re-add each bot id if the MySQL server is restarted.

   I'll post a PHP file for websites soon. Note that it won't be anything fancy, just an HTML list of games to get you started.

PHP file:
---------

````PHP
<html>
<body>
<h1>Games</h1>

<?php
mysql_connect("localhost", "root", "password");
mysql_select_db("ghost");

$result = mysql_query("SELECT * FROM gamelist");

while($row = mysql_fetch_array($result)) {
    if($row['gamename'] != "") {
        echo $row['gamename'] . " (" . $row['slotstaken'] . "/" . $row['slotstotal'] . ")<br>";
    }
}
?>

</body>
</html>
````

Another PHP file:
-----------------
````PHP
<?php
define('DB_HOST', 'localhost');
define('DB_USER', 'root');
define('DB_PASS', '');
define('DB_NAME', 'ghost');

$id = !empty($_GET['id']) ? (int)$_GET['id'] : 0;

if($id):
   $mysqli = new mysqli(DB_HOST, DB_USER, DB_PASS, DB_NAME);

   if($mysqli->connect_errno)
      exit(printf("Connect failed: %s\n", $mysqli->connect_error));

   $sql = '
      SELECT
         `botid`,
         `gamename`,
         `usernames`
      FROM
         `gamelist`
      WHERE `id` = ' . $id;

   if($finfo = $mysqli->query($sql)->fetch_row())
   {
      $botid = $finfo[0];
      $gamename = $finfo[1];
      $players = $finfo[2];
?>
<style>*{color:#444;font:13px Tahoma;}h2{font:16px Georgia;}table,th,td{border:1px solid #333;border-collapse:collapse;}tr:nth-child(odd) td{background-color:rgba(235,235,235,.75);}th,td{padding:3px 5px;}th{background-color:#407499;color:#FFF;}</style>

<h2>Gamename: <?php echo $gamename; ?></h2>
<table class="lobby">
   <tr>
      <th>Player Name</th>
      <th>Gateway</th>
      <th>Ping</th>
   </tr>
<?php
      $player = explode("\t", $players);
      $x = 0;

      for($i = 0; $i < count($player) - 2; $i += 3):
         $player_name = $player[$i];
         $gateway = $player[$i + 1];
         $ping = $player[$i + 2];

         if(!empty($player_name)):
?>
   <tr>
      <td><?php echo $player_name; ?></td>
      <td><?php echo $gateway; ?></td>
      <td><?php echo $ping; ?> ms</td>
   </tr>
<?php
         else:
?>
   <tr><td>--</td><td>--</td><td>--</td></tr>
<?php
         endif;
      endfor;
   }
   else
      echo '<tr><td colspan="3"><h3>No users found for this game.</h3></td></tr>';
endif;
````
