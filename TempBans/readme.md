Temporary Ban Patch
===================
Allow temporary bans for admins on your GHost++.
Bans can now expire after a specific time. You can choose the time.

Changed commands:
-----------------
!addban <playername> <reason>
!ban <playername> <reason>
!banlast

Will now ban a user permanently from the server.


New commands:
-------------
!tempban <playername> <amount> <suffix> <reason>
!tban <playername> <amount> <suffix> <reason>
!tbl

Will now ban temporarily.
Description:
- Amount can be every number which is matching logical to the suffix
- Suffix can be:
   - m, month, months
   - w, week, weeks
   - d, day, days
   - h, hour, hours

Example:
--------
!tban Grief-Code 6 d For no reason.

Player [Grief-Code] got banned now for [6 days] for the reason [For no reason.].
You should not use decimal numbers like [0.5] weeks, however it will be rounded in any case to 1, also on [0.1].


Comment:
--------
I decreased the banlist refreshing time. The reason is simple that expired bans will be faster refreshed.
The remove does only delete them from the database, but the GHost++ uses a temp array, this array was refreshed now only all 60 minutes.
If someone got unbanned by an expiration and the array was refreshed one minute before he need to wait 59 more minutes. So i decreased this value to 5 minutes.
Its a decent value and tested overall on our hosting systems for a long time. You shouldnt decrease this time.

The !tbl (!tempbanlast) value is currently on 5 days, you can change the value by change the following line:
https://github.com/OHSystem/GHostPatches/blob/master/TempBans/game.cpp#L698

Keep in mind that the time is given in the UNIX TIMESTAMP. This does mean the values are for example:
1 MINUTE: 60
1 HOUR: 3600
1 DAY: 86400
1 WEEK: 604800
1 MONTH: 2419200
The can be multiplied and replaced on the number.
