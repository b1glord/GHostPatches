GameList patch by [uakf.b](https://github.com/uakfdotb)
=========================

[uakf.b](https://github.com/uakfdotb):
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
