# gdal-grande

Ubuntu base Docker image with GDAL compiled with ESRI FileGDB and Oracle (OCI)
driver support.

Dockerfile based on https://github.com/haies/gdal

Ubuntu version: 20.04 (Focal)

Library versions:

- GDAL: 3.0.0
- Oracle client: 19.6
- OpenJPEG: 2.3.1
- Proj: 8.2.1

# Build new image

1. Download the Oracle Instant Client package RPM files (Basic, SQL*Plus and SDK Packages) from here:
https://www.oracle.com/database/technologies/instant-client/linux-x86-64-downloads.html
2. Make note of the version number, and update the `Dockerfile` argument `ORACLECLIENT_VERSION`.
3. Copy the department's `tnsnames.ora` file next to the Dockerfile.
4. Run `docker image build --tag ghcr.io/dbca-wa/gdal-grande`

# Usage

Example command to dump a PostgreSQL database query to file geodatabase:

```bash
docker container run -v `pwd`:/out ghcr.io/dbca-wa/gdal-grande \
ogr2ogr -progress -f "FileGDB" /out/<table_name>.gdb \
PG:"host=<dbhost> user=<username> dbname=<dbname> password=<pw>" \
-sql "SELECT * FROM <tablename>" -nlt POLYGON -nln <table_name>
```

Dump an Oracle table to Excel spreadsheet:

```bash
docker container run -v `pwd`:/out ghcr.io/dbca-wa/gdal-grande \
ogr2ogr -progress -overwrite -f "XLSX" /out/out.xlsx \
OCI:"<oracle user>/<oracle password>@(DESCRIPTION=(ADDRESS_LIST=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>)))(CONNECT_DATA=(SID =<sid name>))):<oracle schema>.<oracle table>"
```

Copy an Oracle database table to PostgreSQL:

```bash
docker container run ghcr.io/dbca-wa/gdal-grande \
ogr2ogr -a_srs EPSG:4326 -overwrite -f "PostgreSQL" -nln <postgis schema>.<postgis table> \
PG:"host=<postgis host> user=<postgis user> password=<postgis password> dbname=<postgis database>" \
OCI:"<oracle user>/<oracle password>@(DESCRIPTION=(ADDRESS_LIST=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>)))(CONNECT_DATA=(SID =<sid name>))):<oracle schema>.<oracle table>"
```
