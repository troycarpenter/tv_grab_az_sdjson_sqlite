# tv_grab_az_sdjson_sqlite
XMLTV grabber for Schedules Direct JSON service with local caching of xmltv output.

This program adds a number of extra features for updating the description with extra information.
This is useful because many GUIs do not readily display details about programmes such as season
and episode information or cast information.

Information about artwork is also downloaded and this can also be included in the description
as URL tags that are displayed by some clients.

See the options --update-description-with-all and --update-description-with-artwork for extra information.

Many of the options can be prefixed with "no-" in order to disable them, such as
"--update-all --no-update-description-with-premiere --no-update-description-with-live"

Abbreviated samples of descriptions are given below for a movie and a tv programme episode.

====

Rambo: First Blood (1982).

(1982). (R). ☆☆☆. Vietnam veteran...

Advisory: Adult Language, Adult Situations, Brief Nudity, Graphic Violence.

Cast: Sylvester Stallone, Richard Crenna...

Categories: Action, Feature Film, Movie.

====

How I Met Your Mother - S03E16 - Sandcastles in the Sand.

S3/9 E16/20. (2008-04-21). (TV-PG). Robin rekindles...

Cast: Josh Radnor, Jason Segel...

Categories: Episode, Series, Show, Sitcom.

====



Local Caching of Formatted Data
-------------------------------

Local caching of formatted programme data can be enabled.
This will cache xmltv output to a local server such as to Redis.
The sqlite database (containing details of downloaded data)
is separate from the xmltv-output formatted caching done
with redis.

This significantly reduces the time taken for subsequent runs when
fetching multiple days of listings.  To enable this you will need the
Perl module "CHI" and "CHI::Driver::Redis" and a working Redis server
running locally.

The cache can then be used via the extra arguments:
"--cache-driver=Redis"

The first time the programme is run, the formatted output is cached.
On the second and subsequent runs, the cache is checked, and if the
formatted output is cached, then it is retrieved.

With multiple days and hundreds of channels, this can significantly
reduce overhead. On my test system, it reduced the run time from 134
seconds to 14 seconds.

To check that caching is occurring, you can use "redis-cli stat"
to monitor keys/memory usage of the redis server. The server
must have enough space to hold the cached data (maxmemory setting
in the redis server's redis.conf).

* --cache-driver
Currently only "Redis" is tested and needs the extra Perl modules
installed. The "File" backend appears to work but requires a fast
drive/tmpfs to provide peak performance and needs File would need the
"--cache-purge-expired" option to ensure old entries are expired.
* --cache-namespace-extra
Extra text to use when generating a cache namespace.  This is used if
you run the programme multiple times and alter options that affect the
generated output. For example if you run it with
--update-description-with-icons on one run, and with
--no-update-description-with-icons on a second run then you should specify
a different caching namespace to avoid retrieving incorrect programme details.
* --cache-expiry
String specifying expiry to use. By default the cached programme data expires
after a few days to recover space and ensure listings are regenerated.
An example would be "--cache-expiry='10 days'"
* --cache-ignore-unchanged-programmes (experimental)
If the programme was already in the cache then do not include it in the
generated xmltv file. This avoids PVRs having to constantly reprocess
programmes that have not changed.
This option is experimental and may be removed/renamed in the future.
* --cache-purge-expired
Purge expired entries from the cache. This is _not_ needed for
Redis and is inefficient for many other cache backend drivers
(such as File) and not implemented at all in some other drivers.

Extra Options
-------------

* --merge-split=n
Movies that are split in to two segments by news programmes will be automatically merged if the news is <=_n_ minutes long.
An example is "--merge-split=5" to remove programmes that are only five minutes long if they programmes either side have the same title.
* --artwork-max-width=n
Prefer artwork with a width no larger than _n_. If no artwork exists at that size then larger artwork may be used.
An example is "--artwork-max-width-720".
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
The default is based on US ratings, with fallbacks to other countries.
* --use-category-for-keyword
Instead of outputting keyword tags, output category tags instead. Useful for programs that cannot parse keywords.
* --update-description-with-all
Enable all the below "update-description-with-" options except for --update-description-with-icons, --update-description-with-icons-basic, --update-description-with-icons-entity and --update-description-with-artwork.
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
Add programme age rating in to description. For example "TV-PG".
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

Extra Packages Required
-----------------------
A list of extra (potentially non-standard) packages you may need to install is below:

- DateTime::Format::SQLite
- DBD::SQLite
- File::Homedir
- JSON
- JSON::XS
- LWP::Protocols::HTTPS
- LWP::UserAgent::Determined
- XMLTV

For local (advanced) caching you also need:
- CHI (libchi-perl)
- CHI::Driver::Redis (libchi-driver-redis-perl)
- A redis-server running on the local machine (redis-server)

The other modules used are typically already installed as part of xmltv.

Miscellaneous Information
-------------------------

In Kodi, to view the star rating you may have to change the font to "Arial Based"
in Settings/Interface Settings/Skin/Fonts, otherwise it may appear as square
blocks instead of stars.

The airdate may be incorrect for your country since it tends to be the
first worldwide airdate.

Total number of seasons is known to be sometimes incorrect when a new
series is released.

Programmes marked as "new/premiere" are based on information provided
to Schedules Direct by the networks.  So, a movie may be "new" because
it's the first time a network has purchased the rights to show the
movie.  This means a movie from 1970 may be marked as "new" on one
network but could be a "repeat" on a different network. Similarly,
some networks mark programmes as "new" based on the timeslot.  So a
programme could be "new" at 9pm and the same episode could also be
"new" the following day at 2am.
