# What's New


<a name="unreleased"></a>
## [Unreleased]

### New Features

- **support renamed Rekordbox application directory**  
  The user can now specify the directory name of the Rekordbox application directory. 
  This supports multiple Rekordbox installations of the same major version.

### Improvements/Bug Fixes

- **add disabled context manager to the RBv6 `RekordboxAgentRegistry`**  
- **only re-enable RBV6 USN tracking if it was enabled**  


<a name="0.2.1"></a>
## [0.2.1] - 2023-10-20

This release migrates to SqlAlchemy 2.0 and fixes some bugs.

### Improvements/Bug Fixes

- **migrate to SqlAlchemy 2.0**  
- **add getters/setters for the mixer gain/peak settings in the RBv6 db ([#88](https://github.com/dylanljones/pyrekordbox/issues/88)).**  
  The gain and peak values are stored as high/low binary values. 
  It is now possible to get or set the gain/peak as a simple decibel value. 
  Thank you [@gsuberland](https://github.com/gsuberland) for the help!
- **automatically set `updated_at` of tables in the RBv6 db**  
  The `updated_at` column is automatically updated via `onupdate` if rows are modified. 
  This only happens if the user did not set the column manually.
- **flush the RBv6 db changes before applying USN auto-increment.**  
  This allows the user to use `before_flush` events more easily without 
  affecting the USN changes

### BREAKING CHANGE

`pyrekordbox` now only supports `sqlcipher3`. `pysqlcipher3` is no longer supported
since it is not compatible with SqlAlchemy 2.0.


<a name="0.2.0"></a>
## [0.2.0] - 2023-10-03

This release adds methods for updating playlists/playlist folders and their contents.

### New Features

- **create or delete playlists/playlist folders in the RBv6 db**  
  It is now possible to create playlists or playlist folders with arbitrary seq number 
  using the Rekordbox v6 database handler. Any playlist or playlist folder can also be 
  deleted. All corresponding songs or sub-playlists will also be deleted.
- **add or remove tracks in RBv6 playlists**  
  It is now possible to add songs with arbitrary track number to playlists in the 
  Rekordbox v6 database handler. Any song can also be removed from playlists. 
  The track numbers of the other songs in the playlist get updated accordingly.
  To make sure all changes are compatible with Rekordbox, a new handler for 
  the `masterPlaylists6.xml` auxiliary file was added.
- **move songs in playlists and playlists/playlists folders in the RBv6 db**  
  The track number of songs in playlists can now be updated. The track numbers of the 
  other songs are updated accordingly. Playlists or playlist folders can also be 
  rearranged or moved to a new parent folder.
- **add method for renaming playlists/playlist folders in the RBv6 db**  
  The update time and USN are updated accordingly.
- **add method for creating a decrypted copy of the RBv6 database ([#86](https://github.com/dylanljones/pyrekordbox/issues/86))**  

### Improvements/Bug Fixes

- **generalize getters of list content tables in the RBv6 db**  
  This makes all getters consistent.
- **fix USN tracking and update times in playlist updates of the RBv6 db**
- **prevent commits to the RBv6 db if Rekordbox is running**
- **improve `Parent` relationship in nested tables.**  
  The `Parent` relationship in nested tables (like playlists) are now declared via `backref`. 
  This fixes a bug when deleting rows.
- **set `updated_at` in the playlist XML when committing the RBv6 db**  


<a name="0.1.8"></a>
## [0.1.8] - 2023-09-15

### New Features

- **add methods for converting RBv6 tables to a dictionary**  
  This can be used to save the database contents to an open file format, for example JSON.

### Improvements/Bug Fixes

- **add getters for the `db_directory` and `share_directory` to the RBv6 database handler**  
  This makes it easier to access the additional data of Rekordbox (ANLZ or artwork files).
- **improve `columns` method of RBv6 tables**  
  The `columns` method now returns the *actual* columns of the table (without relationships). 
  To get a list of the column names with the relationships, use the `keys` method.
- **fix wrong ANLZ root directory in the RBv6 database handler**  
  The user can now also specify the ANLZ root directory if a database object is opened 
  in an unusual location by supplying the `db_dir` argument.
- **cache XML track list to speed up checking for duplicates**  
  The TrackID and Location of each track element is cached to prevent checking each XML 
  element when adding new tracks. In addition, the track count is now 
  incremented/decremented when adding/removing tracks. This makes it much faster to 
  add or remove elements in the XML track collection.
- **fix bug when adding tempo and position marks to XML track elements**  
  Adding new tempo of position marks was not possible due to the wrong object being 
  passed as `parent` element.
- **warn when opening the database and Rekordbox is running**  

### Documentation

- **bump furo version to fix RDT issue**  
- **remove sphinx_toggleprompt (incompatible with sphinx>=7)**  
- **Use RTD's new build process and config**


<a name="0.1.6"></a>
## [0.1.7] - 2023-08-16

This release attempts to add a workaround for the broken key extraction 
and fixes some bugs.

### New Features

- **add CLI command to download and cache the RB6 db key from the web ([#64](https://github.com/dylanljones/pyrekordbox/issues/64))**  
  Pyrekordbox tries to download the key from projects that have hard-coded the key 
  (see issue [#77](https://github.com/dylanljones/pyrekordbox/issues/77)). If the download was successful it writes it to the cache file.

### Improvements/Bug Fixes

- **add method for writing the RB6 db key cache manually ([#64](https://github.com/dylanljones/pyrekordbox/issues/64))**  
  If the extraction of the Rekordbox database key fails (>=6.6.5), the user can now write 
  the key manually to the cache file. After updating the cache the database can be opened 
  without providing the key as argument. To make this work pyrekordbox now caches the 
  decrypted key, not the password for decrypting the key. If an old cache file is found 
  it is upgraded automatically.
- **move `install_sqlcipher.py` script to CLI command**  
  The script is now available as `pyrekordbox install-sqlcipher`.
- **add missing relationships in the RB6 database table declarations**  
  Affected tables and corresponding relationships:
  - `DjmdAlbum`: `AlbumArtist`
  - `DjmdCategory`: `MenuItem`
  - `DjmdCue`: `Content`
  - `DjmdSort`: `MenuItem`
- **fix copy/paste error in date getter of the `RekordboxAgentRegistry`**  
  The `get_date` method was actually setting the value.

### Documentation

- **add section for downloading or manually writing the RB6 db key cache**  
- **add basic docstrings to the `RekordboxAgentRegistry` object**  
- **add basic docstrings to the Rekordbox database tables**  


<a name="0.1.6"></a>
## [0.1.6] - 2023-08-13

This release contains improvements of the handling of incompatible Rekordbox versions
and improves the documentation.

### Improvements/Bug Fixes

- **raise exception with hint when opening the RB6 database if the key extraction failed ([#64](https://github.com/dylanljones/pyrekordbox/issues/64))**  
- **cache pw extracted from the rekordbox `app.asar` file**  
  This speeds up the initialization of the package

### Documentation

- **fix modified path in update filename example ([#81](https://github.com/dylanljones/pyrekordbox/issues/81))**  
- **add workaround for key extraction of the RBv6 database ([#64](https://github.com/dylanljones/pyrekordbox/issues/64))**  
- **migrate documentation to markdown**  
- **small fixes in documentation**


<a name="0.1.5"></a>
## [0.1.5] - 2023-04-09

This release contains bug fixes and improves error handling.

### Improvements/Bug Fixes

- **Improve RBv6 configuration handling**  
  Don't warn if no Rekordbox installation was found instead raise an error if no config exists when opening the `Rekordbox6Database`.
- **improve error handling for incompatible RB versions ([#64](https://github.com/dylanljones/pyrekordbox/issues/64))**
- **fix sqlalchemy iso-format error**  
  Using SQLAlchemy v2 results in ValueError's.
- **don't fail on incompatible Rekordbox database**  
  This allows the library to be used with reduced functionality, for example, RekordboxXML still works.


<a name="0.1.4"></a>
## [0.1.4] - 2022-10-30

This release improves the Rekordbox v6 database handling and fixes bugs in the 
USN tracking.

### New Features

- **add ``Parent`` and ``Children`` relationships to nested RBv6 list-tables.**  
  This enables walking thorugh the nested list structure.
  Affected tables:
  - ``DjmdHistory``
  - ``DjmdHotCueBanklist``
  - ``DjmdMyTag``
  - ``DjmdPlaylist``
  - ``DjmdRelatedTracks``
  - ``DjmdSampler``

### Improvements/Bug Fixes

- **fix small error in query handling and also try to import ``sqlcipher3``**  
- **improve RBv6 database update tracking and USN handling**  
  The updates to the database are now tracked directly in the table/database objects.
  All the logic for handling the local update sequence number was moved to a dedicated object ``RekordboxAgentRegistry``.
- **prevent autoflush in ``autoincrement_local_usn`` in the RBv6 database object**  
- **small improvements of the ``Rekordbox6Database``**  
- **fix bug in ``pformat`` of RBv6 database tables**  


<a name="0.1.3"></a>
## [0.1.3] - 2022-10-28

This release mainly consists of improvements on the Rekordbox v6 database
handling.

### New Features

- **support Python3.11**  
- **add auto-increment of USN's for uncommited changes to the RBv6 database**  
  The new ``autoincrement_usn`` method auto-increments the local USN for each uncommited created, changed or deleted row. The ``rb_local_usn`` attribute of added or changed rows are updated according to the update sequence
- **add session-event callbacks to the RBv6 database object**  
- **add update and transaction tracking to the RBv6 database object**  
  This feature is intended for automatic tracking of the USN's.
- **add local USN handlers to RBv6 database object**  
  Methods added:
  - ``get_local_usn``
  - ``set_local_usn``
  - ``increment_local_usn``
- **add process-id getters**  

### Improvements/Bug Fixes

- **make ``columns`` in the RBv6 table classes a class method** 
- **fix small bug in ``read_rekordbox6_asar``**  
- **fix bugs in RBv6 database object**  

### Documentation

- **add missing My-Setting docstrings**  
- **add missing Rekordbox v6 database docstrings**  
- **add missing XML docstrings**  


<a name="0.1.2"></a>
## [0.1.2] - 2022-10-19

This release contains documentation fixes.

### Documentation

- **fix typos and formatting**  
- **fix light theme styling**  


<a name="0.1.1"></a>
## [0.1.1] - 2022-10-19

### New Features

- **`AnlzFile` now stores the path of the parsed file**  
- **add `update_content_path` and `update_content_filename` to RB6 database**  
  These methods update the file path in the entire Rekordbox collection (database and ANLZ files)

### Improvements/Bug Fixes

- **fix bugs in PQTZ/PQT2 tag handler of ANLZ files**  
- **improve ANLZ file path handling**  
- **fix bug when reading the pyrekordbox config files**  
- **remove wrong type hint in ``AbstractAnlzTag``**  
- **Use path instead of extension as key in `read_anlz_files` output**  
  This helps for saving ANLZ files after making changes.

### Documentation

- **update Quick-Start and change reference labels**  
- **add initial version of API Reference**  
- **fix links in ANLZ file documentation**  


<a name="0.1.0"></a>
## [0.1.0] - 2022-10-16

### New Features

- **add `set_content_path` to `Rekordbox6Database` object**  
- **add `set_path` to `AnlzFile` object**  
- **add name properties for linked tables in the ``DjmdContent`` table of the RB6 database**  
  The new properties include:
  - ArtistName
  - AlbumName
  - GenreName
  - RemixerName
  - LabelName
  - OrgArtistName
  - KeyName
  - ColorName
  - ComposerName
- **add relationship for `Content` in the RB6 database tables**  

### Improvements/Bug Fixes

- **return first query result when using ID as argument**  
- **add type annotation to ``read_mysetting_file``**  
- **fix ``items()`` method in MySettings objects**  
- **also try to import ``pysqlcipher3`` on Windows**  

### Documentation

- **add missing ``FolderPath`` in RB6 database documentation**  
- **add MySettings tutorial to documentation**  
- **Add simple XML playlist tutorial**  
- **Add logo to documentation**  
- **Update installation guide for SQLCipher**  


<a name="0.0.8"></a>
## [0.0.8] - 2022-10-15

### New Features

- **add relationships between lists and contents ([#37](https://github.com/dylanljones/pyrekordbox/issues/37))**  
  Affected tables:
  - DjmdHistory
  - DjmdMyTag
  - DjmdPlaylist
  - DjmdRelatedTracks
  - DjmdSampler

### Improvements/Bug Fixes

- **fix incorrect table in `get_related_tracks`**  
- **fix incorrect foreign key in `DjmdHotCueBanklist`**  

### Documentation

- **remove duplicate entry in the Rekordbox v6 database format documentation**  


<a name="0.0.7"></a>
## [0.0.7] - 2022-06-12

### New Features

- **add SQLCipher support for macOS (see [#27](https://github.com/dylanljones/pyrekordbox/issues/27))**  

### Documentation

- **add installation instructions for SQLCipher on macOS**  


<a name="0.0.6"></a>
## [0.0.6] - 2022-05-27

### Improvements/Bug Fixes

- **fix encoding errors on MacOS**  
- **improve ANLZ getters**  


<a name="0.0.5"></a>
## [0.0.5] - 2022-05-06

### Improvements/Bug Fixes

- **improve XML playlist handling and fix refactoring bugs**  
- **raise ValueError if duplicate track is added**  
  Checks for
  - Location
  - TrackID
- **improve XML key errors**  
- **add implementation of crc16xmodem to support Python 3.10 ([#21](https://github.com/dylanljones/pyrekordbox/issues/21))**  


<a name="0.0.4"></a>
## [0.0.4] - 2022-05-06

### New Features

- **add auto-increment of XML TrackID when adding new tracks**  

### Improvements/Bug Fixes

- **fix wrong MySetting default values**  
- **simplify names of playlist (folder) creation methods**  
- **add method to remove tracks in XML database and fix bug in track count update**  
- **fix position argument of XPath in XML file (starts at 1)**  
- **file paths in the XML file are now encoded and decoded as URI's**  
- **fix XML tests with new API**  
- **Improve Rekordbox XML handling and API**  
  The attributes of track can now be accessed with a dict interface. Additionally, the object attributes now correspond to the keys in the XML file


<a name="0.0.3"></a>
## [0.0.3] - 2022-04-24

### New Features

- **add get-methods for `master.db` database tables**  

### Improvements/Bug Fixes

- **fix table name in `get_artist`**  
- **fix typo in settingFile table name**  

### Documentation

- **switch back to rtd theme since furo code blocks don't render properly**  
- **use furo sphinx theme**  
- **add quick-start**  
- **add installation section**  
- **add tutorial sections**  
- **rename file-format headers**  
- **add development section**  
  contains the change-log and contributing information


<a name="0.0.2"></a>
## [0.0.2] - 2022-04-20

### New Features

- **use SQLAlchemy for the  Rekordbox6 `master.db` database**  

### Improvements/Bug Fixes

- **fix import error and README.md**  
- **set logging level to warning**  
- **fix loading the Rekordbox setting file twice in config initialization**  
- **add context for Rekordbox 6 database**  
- **inherit AnlzFile from Mapping to implement dict interface**  
- **unify binary file API**  
  The Settings files now also use the `parse` and `parse_file` class-methods

### Documentation

- **add missing djmd tables to `master.db` database documentation**


[Unreleased]: https://github.com/dylanljones/pyrekordbox/compare/0.2.1...HEAD
[0.2.1]: https://github.com/dylanljones/pyrekordbox/compare/0.2.0...0.2.1
[0.2.0]: https://github.com/dylanljones/pyrekordbox/compare/0.1.8...0.2.0
[0.1.8]: https://github.com/dylanljones/pyrekordbox/compare/0.1.7...0.1.8
[0.1.7]: https://github.com/dylanljones/pyrekordbox/compare/0.1.6...0.1.7
[0.1.6]: https://github.com/dylanljones/pyrekordbox/compare/0.1.5...0.1.6
[0.1.5]: https://github.com/dylanljones/pyrekordbox/compare/0.1.4...0.1.5
[0.1.4]: https://github.com/dylanljones/pyrekordbox/compare/0.1.3...0.1.4
[0.1.3]: https://github.com/dylanljones/pyrekordbox/compare/0.1.2...0.1.3
[0.1.2]: https://github.com/dylanljones/pyrekordbox/compare/0.1.1...0.1.2
[0.1.1]: https://github.com/dylanljones/pyrekordbox/compare/0.1.0...0.1.1
[0.1.0]: https://github.com/dylanljones/pyrekordbox/compare/0.0.8...0.1.0
[0.0.8]: https://github.com/dylanljones/pyrekordbox/compare/0.0.7...0.0.8
[0.0.7]: https://github.com/dylanljones/pyrekordbox/compare/0.0.6...0.0.7
[0.0.6]: https://github.com/dylanljones/pyrekordbox/compare/0.0.5...0.0.6
[0.0.5]: https://github.com/dylanljones/pyrekordbox/compare/0.0.4...0.0.5
[0.0.4]: https://github.com/dylanljones/pyrekordbox/compare/0.0.3...0.0.4
[0.0.3]: https://github.com/dylanljones/pyrekordbox/compare/0.0.2...0.0.3
[0.0.2]: https://github.com/dylanljones/pyrekordbox/compare/0.0.1...0.0.2
