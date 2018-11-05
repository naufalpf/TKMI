# TKMI

## Step

0. https://extract.bbbike.org/ tempat extract osm
1. install postgres + postgis (jangan lupa username + passwordnya)
2. download osm
3. setting environment variable ke binnya (D:\Program Files\PostgreSQL\bin)(punya saya ini)
4. run cmd
5. masuk ke folder osmnya
6. cobain "psql -U postgres" (punya saya postgres)
7. masukin passwordmu
8. ketik "CREATE DATABASE db_clearroute;"
9. ctrl+c (keluar dari psql)
10. "psql db_clearroute postgres"
11. masukin password
12. "CREATE EXTENSION postgis;"
13. "CREATE EXTENSION pgRouting;"
14. ctrl+c (keluar dari psql)

Cara 1 using osm2pgrouting

15. download mapconfig.xml di https://github.com/pgRouting/osm2pgrouting-build/blob/master/mapconfig.xml, taroh di folder yang sama kayak osmnya
16. "osm2pgrouting --f surabaya.osm --conf mapconfig.xml --dbname db_clearroute --username postgres --password <password> --clean"
17. tungguin
18. "psql db_clearroute postgres"
19. masukin password
20. testing "SELECT name FROM ways;"
21. selamat, anda mendapatkan semua nama jalan
22. jalanin

ALTER TABLE configuration ADD COLUMN penalty FLOAT;
UPDATE configuration SET penalty=1.0;
UPDATE configuration SET penalty=2.0 WHERE tag_id = 112;
UPDATE configuration SET penalty=1.5 WHERE tag_id = 110;
UPDATE configuration SET penalty=0.8 WHERE tag_id = 109;
UPDATE configuration SET penalty=0.5 WHERE tag_id = 124;
UPDATE configuration SET penalty=0.5 WHERE tag_id = 108;

Itu buat gantiin yang
UPDATE osm_way_classes SET penalty=2.0 WHERE class_id = 112;
UPDATE osm_way_classes SET penalty=1.5 WHERE class_id = 110;
UPDATE osm_way_classes SET penalty=0.8 WHERE class_id = 109;
UPDATE osm_way_classes SET penalty=0.5 WHERE class_id = 124;
UPDATE osm_way_classes SET penalty=0.5 WHERE class_id = 108;

23. konekin postgres ke xampp
http://philippekouadio.com/blog/?p=5304&lang=en
http://subhra.me/install-postgresql-in-xampp-on-windows-and-integrate-phppgadmin/
cobain itu dah, kalo mau phppgadmin monggo
nek ubuntu : https://www.howtoforge.com/tutorial/ubuntu-postgresql-installation/

Benahi environtment variable jika postgremu tidak di install di dalam xampp
setting php.ini pada folder xampp>php dan cari pgsql lalu ubah menjadi seperti ini 
extension = pgsql
extension = pdo_pgsql

24. coba di php buat query
http://php.net/manual/en/function.pg-connect.php (mirip mysql, coba google aja cara pakenya)

25. coba query yang ini
SELECT ST_AsGeoJSON(ST_UNION(c.geom)) AS geojson FROM (SELECT a.seq AS seq, b.gid AS gi, b.name AS name, a.cost AS cost, b.the_geom AS geom, b.source, b.target, b.x1 AS x, b.y1 AS y FROM pgr_dijkstra('SELECT gid::integer AS id, source::integer, target::integer, cost::double precision AS cost, reverse_cost::double precision AS reverse_cost, x1, y1, x2, y2 FROM backup_ways', 912, 920, true, true) AS a LEFT JOIN ways AS b ON (a.id2 = b.gid) ORDER BY a.seq) AS c

harusnya keluar geojsonnya, coba di echo aja
http://prntscr.com/jl7k5e (kek gitu keluarnya)

25b. https://gist.github.com/rezkyal/577b178053522d0cb366d412f9108dc2 (save as cek.php)
jangan lupa ganti passwordnya

26. nah dari data geojson itu bisa digambar nanti diatas mapnya, pake data layer (aku pakenya openlayer, tapi jenis lain ada banyak juga)(coba explore google yak)
https://openlayers.org/en/latest/examples/geojson.html
itu contoh kodingannya
 
27.https://gist.github.com/rezkyal/26a0383e91195ec9dde764549918ea53 (save as script.js)
https://gist.github.com/rezkyal/743ab3fcf64b554a34730906e5be5937(save as index.php)

