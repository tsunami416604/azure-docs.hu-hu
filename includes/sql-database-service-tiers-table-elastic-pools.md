<!--
Used in:
sql-database-elastic-pool.md 
-->

 
### <a name="basic-elastic-pool-limits"></a>Alapszintű rugalmas készletek korlátai

| eDTU-k száma készletenként | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Belefoglalt tárterület készletenként (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximális tárolási lehetőségek száma készletenként (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximális In-Memory OLTP storage (GB) készletenként | – | N/A | N/A | N/A | N/A | N/A | N/A | – |
| Adatbázisok maximális száma készletenként | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Egyidejű feldolgozók (kérelmek) maximális száma készletenként | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Egyidejű bejelentkezések maximális száma készletenként | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Az adatbázisonkénti minimális edtu-k választási lehetőségek | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Az adatbázisonkénti maximális edtu-k választási lehetőségek | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Maximális tárterület adatbázisonként (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 
||||||||

### <a name="standard-elastic-pool-limits"></a>Standard rugalmas készletek korlátai

| eDTU-k száma készletenként | **50** | **100** | **200** | **300** | **400** | **800**| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Belefoglalt tárterület készletenként (GB) | 50 | 100 | 200 | 300 | 400 | 800 | 
| Maximális tárolási lehetőségeket (GB) készletenként * | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 | 
| Maximális In-Memory OLTP storage (GB) készletenként | – | N/A | N/A | N/A | N/A | – | 
| Adatbázisok maximális száma készletenként | 100 | 200 | 500 | 500 | 500 | 500 | 
| Egyidejű feldolgozók (kérelmek) maximális száma készletenként | 100 | 200 | 400 | 600 | 800 | 1600 |
| Egyidejű bejelentkezések maximális száma készletenként | 100 | 200 | 400 | 600 | 800 | 1600 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Az adatbázisonkénti minimális edtu-k választási lehetőségek | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Az adatbázisonkénti maximális edtu-k választási lehetőségek | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Maximális tárterület (GB) adatbázisonként* | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Standard rugalmas készletek korlátai (folytatás) 

| eDTU-k száma készletenként | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Belefoglalt tárterület készletenként (GB) | 1200 | 1600 | 2000 | 2500 | 3000 | 
| Maximális tárolási lehetőségeket (GB) készletenként * | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Maximális In-Memory OLTP storage (GB) készletenként | – | N/A | N/A | N/A | – | 
| Adatbázisok maximális száma készletenként | 500 | 500 | 500 | 500 | 500 | 
| Egyidejű feldolgozók (kérelmek) maximális száma készletenként | 2400 | 3200 | 4000 | 5000 | 6000 |
| Egyidejű bejelentkezések maximális száma készletenként | 2400 | 3200 | 4000 | 5000 | 6000 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Az adatbázisonkénti minimális edtu-k választási lehetőségek | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Az adatbázisonkénti maximális edtu-k választási lehetőségek | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| Maximális tárolási lehetőségeket (GB) adatbázisonként * | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Prémium rugalmas készletek korlátai

| eDTU-k száma készletenként | **125** | **250** | **500** | **1000** | **1500**| 
|:---|---:|---:|---:| ---: | ---: | 
| Belefoglalt tárterület készletenként (GB) | 250 | 500 | 750 | 1024 | 1536 | 
| Maximális tárolási lehetőségeket (GB) készletenként * | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Maximális In-Memory OLTP storage (GB) készletenként | 1 | 2 | 4 | 10 | 12 | 
| Adatbázisok maximális száma készletenként | 50 | 100 | 100 | 100 | 100 | 
| Egyidejű feldolgozók (kérelmek) maximális száma készletenként | 200 | 400 | 800 | 1600 | 2400 | 
| Egyidejű bejelentkezések maximális száma készletenként | 200 | 400 | 800 | 1600 | 2400 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 | 30000 | 
| eDTU-k minimális száma adatbázisonként | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 | 
| eDTU-k maximális száma adatbázisonként | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Maximális tárterület (GB) adatbázisonként* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Prémium rugalmas készletek korlátai (folytatás) 

| eDTU-k száma készletenként | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: | 
| Belefoglalt tárterület készletenként (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Maximális tárolási lehetőségeket (GB) készletenként * | 2048 | 2560 | 3072 | 3548 | 4096|
| Maximális In-Memory OLTP storage (GB) készletenként | 16 | 20 | 24 | 28 | 32 |
| Adatbázisok maximális száma készletenként | 100 | 100 | 100 | 100 | 100 | 
| Egyidejű feldolgozók (kérelmek) maximális száma készletenként | 3200 | 4000 | 4800 | 5600 | 6400 |
| Egyidejű bejelentkezések maximális száma készletenként | 3200 | 4000 | 4800 | 5600 | 6400 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Az adatbázisonkénti minimális edtu-k választási lehetőségek | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Az adatbázisonkénti maximális edtu-k választási lehetőségek | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Maximális tárterület (GB) adatbázisonként* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||


> [!IMPORTANT]
> \* A szolgáltatási keretbe foglaltnál nagyobb tárterületek előzetes verzióban érhetők el, és extra költségek vonatkoznak rájuk. További információkért lásd: a [SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/). A szolgáltatási keretbe foglaltnál nagyobb tárterületek előzetes verzióban érhetők el, és további költségek vonatkoznak rájuk. További információkért lásd: a [SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/).
>
> \* Az 1 TB tárhelyméretet meghaladó prémium szintű készletek jelenleg a következő régiókban érhetők el: Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-Kanada, Kelet-Kanada, USA középső régiója, Közép-Franciaország, Közép-Németország, Kelet-Japán, Nyugat-Japán, Korea középső régiója, USA északi középső régiója, Észak-Európa, USA déli középső régiója, Délkelet-Ázsia, az Egyesült Királyság déli régiója, az Egyesült Királyság nyugati régiója, USA keleti régiója 2, USA nyugati régiója, USA-beli államigazgatás – Virginia, és Nyugat-Európa. Lásd: [P11–P15 – Aktuális korlátozások](../articles/sql-database/sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
>

