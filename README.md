# gdal-grande

Ubuntu base Docker image with GDAL compiled with ESRI FileGDB and Oracle (OCI)
driver support.

Dockerfile based on https://github.com/haies/gdal

- GDAL: v3.0.0
- Oracle client: v19.6
- OpenJPEG: v2.3.1

# Usage

Example command to dump PostgreSQL query to file geodatabase:

```bash
docker run -it -v `pwd`:/out dbcawa/gdal-grande ogr2ogr -progress -f "FileGDB" /out/mygdb.gdb PG:"host=<dbhost> user=<username> dbname=<dbname> password=<pw>" -sql "SELECT * FROM <tablename>" -nlt POLYGON -lco -nln <layer_name>
```

Dump an Oracle table to Excel spreadsheet:

```bash
docker run -it -v `pwd`:/out dbcawa/gdal-grande ogr2ogr -progress -overwrite -f "XLSX" /out/out.xlsx OCI:"<oracle user>/<oracle password>@(DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = <hostname>)(PORT = <port number>)))(CONNECT_DATA = (SID =<sid name>))):<oracle schema>.<oracle table>"
```

Copy an Oracle database table to PostgreSQL:

```bash
docker run -it dbcawa/gdal-grande ogr2ogr -a_srs EPSG:4326 -overwrite -f "PostgreSQL" -nln <postgis schema>.<postgis table> PG:"host=<postgis host> user=<postgis user> password=<postgis password> dbname=<postgis database>" OCI:"<oracle user>/<oracle password>@(DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = <hostname>)(PORT = <port number>)))(CONNECT_DATA = (SID =<sid name>))):<oracle schema>.<oracle table>"
```
