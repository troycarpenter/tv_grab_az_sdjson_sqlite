# tv_grab_zz_sdjson_sqlite
XMLTV grabber for Schedules Direct JSON service

Extra Options
-------------

* --merge-split=n
Movies that are split in to two segments by news programmes will be automatically merged if the news is <=_n_ minutes long.
* --artwork-max-width=n
Prefer artwork with a width no larger than _n_. If no artwork exists at that size then larger artwork may be used.
* --channel-regex=regex
When outputting xmltv file, only output details for channels matching the _regex_. This allows the user to run the grabber multiple times and output extra days for important channels.
* --no-download
Do not download the schedule, use database cache only.
* --no-prune
Do not prune the database of old schedules. Useful if running multiple times.
* --force-download
Erase schedules from DB and force a complete download of data.
* --use-category-for-keyword
Instead of outputting keyword tags, output category tags instead. Useful for programs that cannot parse keywords.
* --update-description-with-credits
Add credits (actors) to description.
* --update-description-with-year
Add year to description, for example year movie was made.
* --update-description-with-season-episode
Add season/episode information to description.
* --update-description-with-icons
Add icons to the description to indicate category.

Extra Supported Tags and Features
---------------------------------

* Prefer ttvdb episode numbers if they differ from Gracenote episode numbers. This is useful since most metadata lookups use ttvdb episode numbers which can often be different.
* Download artwork for programmes.
* Support multiple xmltv episode number formats include series/ and episode/ formats.
* Tag new showings as premiere so they can be recorded in tvheadend.
