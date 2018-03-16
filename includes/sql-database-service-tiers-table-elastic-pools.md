<!--
Used in:
sql-database-elastic-pool.md 
-->

 
### <a name="basic-elastic-pool-limits"></a>Alapszintű rugalmas készletek korlátai

| eDTU-k száma készletenként | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Belefoglalt tárolási készletenként (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximális tárolási lehetőségek készletenként (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximális memórián belüli online Tranzakciófeldolgozási tárolási készletenként (GB) | – | N/A | N/A | N/A | N/A | N/A | N/A | – |
| Adatbázisok maximális száma készletenként | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Egyidejű feldolgozók (kérelmek) maximális száma készletenként | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Egyidejű bejelentkezések maximális száma készletenként | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Adatbázisonkénti minimális edtu-k választási lehetőségek | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Adatbázisonként maximális edtu-k választási lehetőségek | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Maximális tárterület adatbázisonként (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 
||||||||

### <a name="standard-elastic-pool-limits"></a>Standard rugalmas készletek korlátai

| eDTU-k száma készletenként | **50** | **100** | **200** | **300** | **400** | **800**| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Belefoglalt tárolási készletenként (GB) | 50 | 100 | 200 | 300 | 400 | 800 | 
| Maximális tárolási lehetőségek (GB) készletenként * | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 | 
| Maximális memórián belüli online Tranzakciófeldolgozási tárolási készletenként (GB) | – | N/A | N/A | N/A | N/A | – | 
| Adatbázisok maximális száma készletenként | 100 | 200 | 500 | 500 | 500 | 500 | 
| Egyidejű feldolgozók (kérelmek) maximális száma készletenként | 100 | 200 | 400 | 600 | 800 | 1600 |
| Egyidejű bejelentkezések maximális száma készletenként | 100 | 200 | 400 | 600 | 800 | 1600 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Adatbázisonkénti minimális edtu-k választási lehetőségek | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Adatbázisonként maximális edtu-k választási lehetőségek | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Maximális tárterület (GB) adatbázisonként* | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Standard rugalmas készletek korlátai (folytatás) 

| eDTU-k száma készletenként | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Belefoglalt tárolási készletenként (GB) | 1200 | 1600 | 2000 | 2500 | 3000 | 
| Maximális tárolási lehetőségek (GB) készletenként * | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Maximális memórián belüli online Tranzakciófeldolgozási tárolási készletenként (GB) | – | N/A | N/A | N/A | – | 
| Adatbázisok maximális száma készletenként | 500 | 500 | 500 | 500 | 500 | 
| Egyidejű feldolgozók (kérelmek) maximális száma készletenként | 2400 | 3200 | 4000 | 5000 | 6000 |
| Egyidejű bejelentkezések maximális száma készletenként | 2400 | 3200 | 4000 | 5000 | 6000 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Adatbázisonkénti minimális edtu-k választási lehetőségek | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Adatbázisonként maximális edtu-k választási lehetőségek | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| Maximális tárolási lehetőségek adatbázis (GB) / * | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Prémium rugalmas készletek korlátai

| eDTU-k száma készletenként | **125** | **250** | **500** | **1000** | **1500**| 
|:---|---:|---:|---:| ---: | ---: | 
| Belefoglalt tárolási készletenként (GB) | 250 | 500 | 750 | 1024 | 1536 | 
| Maximális tárolási lehetőségek (GB) készletenként * | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Maximális memórián belüli online Tranzakciófeldolgozási tárolási készletenként (GB) | 1 | 2 | 4 | 10 | 12 | 
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
| Belefoglalt tárolási készletenként (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Maximális tárolási lehetőségek (GB) készletenként * | 2048 | 2560 | 3072 | 3548 | 4096|
| Maximális memórián belüli online Tranzakciófeldolgozási tárolási készletenként (GB) | 16 | 20 | 24 | 28 | 32 |
| Adatbázisok maximális száma készletenként | 100 | 100 | 100 | 100 | 100 | 
| Egyidejű feldolgozók (kérelmek) maximális száma készletenként | 3200 | 4000 | 4800 | 5600 | 6400 |
| Egyidejű bejelentkezések maximális száma készletenként | 3200 | 4000 | 4800 | 5600 | 6400 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Adatbázisonkénti minimális edtu-k választási lehetőségek | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Adatbázisonként maximális edtu-k választási lehetőségek | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Maximális tárterület (GB) adatbázisonként* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

> [!IMPORTANT]
> \* A szolgáltatási keretbe foglaltnál nagyobb tárterületek előzetes verzióban érhetők el, és extra költségek vonatkoznak rájuk. További információkért lásd: a [árképzést ismertető oldalra SQL-adatbázis](https://azure.microsoft.com/pricing/details/sql-database/). Tárolási mérete nagyobb, mint a belefoglalt tárolókapacitást még csak előzetes verziójúak, és további költségek alkalmazni. További információkért lásd: a [árképzést ismertető oldalra SQL-adatbázis](https://azure.microsoft.com/pricing/details/sql-database/).
>
> \* Prémium szint több, mint 1 TB-nyi tárhelyre érhető el jelenleg a következő régiókban: Kanada központi, Kanada keleti, Franciaország központi, Németország központi, kelet-japán, koreai központi, déli középső Régiójában, Dél Kelet-Ázsia, Velünk East2, USA nyugati régiója, Velünk – (kormányzati) Virginia és nyugati régiója Európa. 
>

