# sport.db Tools & Scripts

_Open Sport(s) Data (Football, Alpine Ski, Formula 1, etc.) Tools & Scripts_


[GitHub](https://github.com/sportdb) // 
[Guides](https://github.com/sportdb/docs) // 
[Datasets](https://github.com/sportdb/datasets) 


![](i/sportdb-models.png)


[Command Line Tools](#command-line-tools) •
[Scripting](#scripting) •
[Services (HTTP JSON APIs)](#services-http-json-apis)



## Quick Starter Guides

See the **[football.db League Starter Sample - Mauritius Premier League](https://github.com/openfootball/league-starter)**
if you want to start from scratch (zero) with your very own league.

See the **[football.db Quick Starter Datafile Templates](https://github.com/openfootball/quick-starter)** if you want to read in ready-to-use /
ready-to-fork dataset packages incl. the English Premier League, the German
Bundesliga, the Spanish Primera División and some more.


## Command Line Tools


### sportdb - sport.db Command Line Tool

The sportdb tool lets you read in (parse) datasets (e.g. leagues, clubs, match schedules, etc.) 
in plain text into your sports SQL database of choice (e.g. SQLite, PostgreSQL, etc.)

```
SYNOPSIS
    sportdb [global options] command [command options] [arguments...]

VERSION
    2.0

GLOBAL OPTIONS
    -d, --dbpath=PATH - Database path (default: .)
    -n, --dbname=NAME - Database name (default: sport.db)
    --verbose         - (Debug) Show debug messages
    --version         - Show version

COMMANDS
    new, n        - Build DB w/ quick starter Datafile templates
    build, b      - Build DB (download/create/read); use ./Datafile - zips get downloaded to ./tmp
    serve, server - Start web service (HTTP JSON API)

MORE COMMANDS    
    create        - Create DB schema
    download, dl  - Download datasets; use ./Datafile - zips get downloaded to ./tmp
    read, r       - Read datasets; use ./Datafile - zips required in ./tmp
    logs          - Show logs
    props         - Show props
    stats         - Show stats
    test          - (Debug) Test command suite
    help          - Shows a list of commands or help for one command
```


#### `new` Command

```
NAME
    new - Build DB w/ quick starter Datafile templates
SYNOPSIS
    sportdb [global options] new NAME

EXAMPLES
    sportdb new eng2019-20
    sportdb new eng
```


#### `build` Command

```
NAME
    build - Build DB (download/create/read); use ./Datafile - zips get downloaded to ./tmp

SYNOPSIS
    sportdb [global options] build

EXAMPLES
    sportdb build
```


#### `serve` Command

```
NAME
    serve - Start web service (HTTP JSON API)

SYNOPSIS
    sportdb [global options] serve

EXAMPLES
    sportdb serve
```

[More documentation »](https://github.com/sportdb/sport.db/tree/master/sportdb)



## Scripting

### Reading Match Datasets in (Structured) Text

**Step 1**

Setup the (SQL) database. Let's use and build a single-file SQLite database (from scratch),
as an example:

``` ruby
require 'sportdb/readers'

SportDb.connect( adapter:  'sqlite3',
                 database: './england.db' )
SportDb.create_all       ## build database schema (tables, indexes, etc.)
```

**Step 2**

Let's read in some leagues, seasons, clubs, and match schedules and results.
Let's use the public domain football.db datasets for England (see [`openfootball/england`](https://github.com/openfootball/england)), as an example:


```
= English Premier League 2015/16

Matchday 1

[Sat Aug 8]
  Manchester United      1-0  Tottenham Hotspur
  AFC Bournemouth        0-1  Aston Villa
  Everton FC             2-2  Watford FC
  Leicester City         4-2  Sunderland AFC
  Norwich City           1-3  Crystal Palace
  Chelsea FC             2-2  Swansea City
[Sun Aug 9]
  Arsenal FC             0-2  West Ham United
  Newcastle United       2-2  Southampton FC
  Stoke City             0-1  Liverpool FC
[Mon Aug 10]
  West Bromwich Albion   0-3  Manchester City

...
```

(Source: [england/2015-16/1-premierleague-i.txt](https://github.com/openfootball/england/blob/master/2015-16/1-premierleague-i.txt))

and let's try:

``` ruby
## assumes football.db datasets for England in ./england directory
##   see github.com/openfootball/england
SportDb.read( './england/2015-16/1-premierleague-i.txt' )
SportDb.read( './england/2015-16/1-premierleague-ii.txt' )

## let's try another season
SportDb.read( './england/2019-20/1-premierleague.txt' )
```

All leagues, seasons, clubs, match days and rounds, match fixtures and results,
and more are now in your (SQL) database of choice.

Bonus: As an alternative pass in the "package" directory or a zip archive and let `read` figure
out what datafiles to read in:

``` ruby
## assumes football.db datasets for England in ./england directory
##   see github.com/openfootball/england
SportDb.read( './england' )
## -or-   use a zip archive download
SportDb.read( './england.zip' )
```

[More documentation »](https://github.com/sportdb/sport.db/tree/master/sportdb-readers)


### Reading Match Datasets in Comma-Separated Values (CSV) Format

**Step 1**

Setup the (SQL) database. Let's use and build a single-file SQLite database (from scratch),
as an example:

``` ruby
require 'sportdb/importers'

SportDb.connect( adapter:  'sqlite3',
                 database: './england.db' )
SportDb.create_all       ## build database schema (tables, indexes, etc.)
```


**Step 2**

Let's use the public domain football.csv datasets for England (see [`footballcsv/england`](https://github.com/footballcsv/england)), as an example:

```
Round, Date,              Team 1,               FT,  HT,  Team 2
1,     (Fri)  9 Aug 2019, Liverpool FC,         4-1, 4-0, Norwich City FC
1,     (Sat) 10 Aug 2019, West Ham United FC,   0-5, 0-1, Manchester City FC
1,     (Sat) 10 Aug 2019, AFC Bournemouth,      1-1, 0-0, Sheffield United FC
1,     (Sat) 10 Aug 2019, Burnley FC,           3-0, 0-0, Southampton FC
1,     (Sat) 10 Aug 2019, Crystal Palace FC,    0-0, 0-0, Everton FC
1,     (Sat) 10 Aug 2019, Watford FC,           0-3, 0-1, Brighton & Hove Albion FC
1,     (Sat) 10 Aug 2019, Tottenham Hotspur FC, 3-1, 0-1, Aston Villa FC
1,     (Sun) 11 Aug 2019, Leicester City FC,    0-0, 0-0, Wolverhampton Wanderers FC
1,     (Sun) 11 Aug 2019, Newcastle United FC,  0-1, 0-0, Arsenal FC
1,     (Sun) 11 Aug 2019, Manchester United FC, 4-0, 1-0, Chelsea FC
...
```
(Source: [england/2019-20/eng.1.csv](https://github.com/footballcsv/england/blob/master/2010s/2019-20/eng.1.csv))


and let's try:

``` ruby
## assumes football.csv datasets for England in ./england directory
##   see github.com/footballcsv/england
SportDb.read_csv( './england/2019-20/eng.1.csv' )

## let's try another season
SportDb.read_csv( './england/2018-19/eng.1.csv' )
SportDb.read_csv( './england/2018-19/eng.2.csv' )
```

All leagues, seasons, clubs, match days and rounds, match fixtures and results,
and more are now in your (SQL) database of choice.


Bonus: Let's import all datafiles for all seasons (from 1888-89 to today)
for England, use:

``` ruby
## note: requires a local copy of the football.csv england datasets
##          see https://github.com/footballcsv/england
SportDb.read_csv( './england' )
# -or-    use a zip archive
SportDb.read_csv( './england.zip' )
```

[More documentation »](https://github.com/sportdb/sport.db/tree/master/sportdb-importers)



## Services (HTTP JSON APIs)

You can run any of the HTTP JSON API (web service) scripts using the `sportdb` command line tool. 
By default the `serve` command will look for
a script named `Service` or `service.rb` (in the working folder, that is, `./`). Example:

```
$ sportdb serve
```

To run any other script - copy the script into the working folder and pass it along as an argument. Example:

```
$ sportdb serve starter      #  note: will (auto-)add the .rb extension  or
$ sportdb serve starter.rb
```

[More documentation »](https://github.com/sportdb/sport.db.service)



## Questions? Comments?

Send them along to the
[Open Sports & Friends Forum/Mailing List](http://groups.google.com/group/opensport).
Thanks!
