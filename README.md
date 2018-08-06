# tv_grab_az_sdjson_sqlite
XMLTV grabber for Schedules Direct JSON service

This program adds a number of extra features for updating the description with extra information.
This is useful because many GUIs do not readily display details about programmes such as season
and episode information or cast information.

Information about artwork is also downloaded and this can also be included in the description
as URL tags that are displayed by some clients.

See the options --update-description-with-all and --update-description-with-artwork for extra information.


Extra Options
-------------

* --merge-split=n
Movies that are split in to two segments by news programmes will be automatically merged if the news is <=_n_ minutes long.
* --artwork-max-width=n
Prefer artwork with a width no larger than _n_. If no artwork exists at that size then larger artwork may be used.
* --channel-regex=regex and ---channel-exclude-regex=regex
When outputting xmltv file, only output details for channels matching/not matching the _regex_. This allows the user to run the grabber multiple times and output extra days for important channels.
* --no-download
Do not download the schedule, use database cache only.
* --no-prune
Do not prune the database of old schedules. Useful if running multiple times.
* --force-download
Erase schedules from DB and force a complete download of data.
* --content-rating-order=s
Use a specific content rating order based on rating agency. For example "MPAA,VCHIP" will prefer those to all other rating systems.
This is useful since many movies have multiple ratings but many systems only take the first content rating.
* --use-category-for-keyword
Instead of outputting keyword tags, output category tags instead. Useful for programs that cannot parse keywords.
* --update-description-with-all
Enable all the below "update-description-with-" options except for --update-description-with-icons-basic, --update-description-with-icons-entity and --update-description-with-artwork.
* --update-description-with-credits
Add credits (actors) to description.
* --update-description-with-categories
Include categories in description.
* --update-description-with-year
Add year to description, for example year movie was made.
* --update-description-with-season-episode
Add season/episode information to description.
* --update-description-with-icons
Add icons to the description to indicate category.
Note that some databases (such as MySQL/MariaDB) have their own idea of utf-8 which defaults to only three-bytes not four-bytes (which they call utf8mb4), so they cannot store all icons correctly.
* --update-description-with-icons-basic
Similar to --update-description-with-icons but only include icons that fit in to basic character set so supported by older databases.
* --update-description-with-icons-entity
Similar to --update-description-with-icons but use html entities for encoding characters. This will only for some clients which cannot handle utf-8 in the server.
* --update-description-with-premiere
Add indicator that an episode is brand new to the description.
* --update-description-with-live
Add indicator that an episode is live to the description.
* --update-description-with-advisory
Add content advisories (such as adult language) to description.
* --update-description-with-artwork
Some clients allow artwork to be embedded in the description using html image URLs. This option enables this.
* --update-description-with-stars
Add star rating to description.
* --update-description-with-rating
Add programme rating in to description.
* --update-description-with-title
Include title details inside the description. So the description will include "ProgTitle - S01E02 - Subtitle".
This is useful for easier recording regex in some programs.
* --update-previously-shown-with-year
Movies often have details of the year they were made but some clients expect those details in the previously-shown field.

Extra Supported Tags and Features
---------------------------------

* Prefer ttvdb episode numbers if they differ from Gracenote episode numbers. This is useful since most metadata lookups use ttvdb episode numbers which can often be different.
* Download artwork for programmes.
* Support multiple xmltv episode number formats include series/ and episode/ formats.
* Tag new showings as premiere so they can be recorded in tvheadend.
