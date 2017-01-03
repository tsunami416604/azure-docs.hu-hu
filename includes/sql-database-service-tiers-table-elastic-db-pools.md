<!--
Used in:
sql-database-elastic-pool.md   
sql-database-resource-limits.md
sql-database-service-tiers.md  
-->

### <a name="basic-elastic-pool-limits"></a>Alapszintű rugalmas készletek korlátai

| Készlet mérete (eDTU)  | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Maximális tárterület készletenként* | 5 GB| 10 GB| 20 GB| 29 GB| 39 GB| 78 GB| 117 GB| 156 GB|
| Adatbázisok maximális száma készletenként | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Egyidejű feldolgozók maximális száma készletenként | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Egyidejű bejelentkezések maximális száma készletenként | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| eDTU-k minimális száma adatbázisonként | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} |
| eDTU-k maximális száma adatbázisonként | {5} | {5} | {5} | {5} | {5} | {5} | {5} | {5} | {5} |
||||||||

### <a name="standard-elastic-pool-limits"></a>Standard rugalmas készletek korlátai

| Készlet mérete (eDTU)  | **50** | **100** | **200** | **300** | **400** | **800** | 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Maximális tárterület készletenként* | 50 GB| 100 GB| 200 GB | 300 GB| 400 GB | 800 GB | 
| Adatbázisok maximális száma készletenként | 100 | 200 | 500 | 500 | 500 | 500 | 
| Egyidejű feldolgozók maximális száma készletenként | 100 | 200 | 400 | 600 |  800 | 1600 |
| Egyidejű bejelentkezések maximális száma készletenként | 100 | 200 | 400 | 600 |  800 | 1600 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| eDTU-k minimális száma adatbázisonként | {0,10,20,<br>50} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} |
| eDTU-k maximális száma adatbázisonként | {10,20,<br>50} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | 
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Standard rugalmas készletek korlátai (folytatás) 

| Készlet mérete (eDTU)  |  **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Maximális tárterület készletenként* | 1,2 TB | 1,6 TB | 2 TB | 2,4 TB | 2,9 TB | 
| Adatbázisok maximális száma készletenként | 500 | 500 | 500 | 500 | 500 | 500 |
| Egyidejű feldolgozók maximális száma készletenként |  2400 | 3200 | 4000 | 5000 | 6000 |
| Egyidejű bejelentkezések maximális száma készletenként |  2400 | 3200 | 4000 | 5000 | 6000 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 | 30000 | 
| eDTU-k minimális száma adatbázisonként | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} |
| eDTU-k maximális száma adatbázisonként | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Prémium rugalmas készletek korlátai

| Készlet mérete (eDTU)  | **125** | **250** | **500** | **1000** | **1500** | 
|:---|---:|---:|---:| ---: | ---: | 
| Maximális tárterület készletenként* | 250 GB| 500 GB| 750 GB| 750 GB| 750 GB| 
| Adatbázisok maximális száma készletenként | 50 | 100 | 100 | 100 | 100 |  
| Egyidejű feldolgozók maximális száma készletenként | 200 | 400 | 800 | 1600 |  2400 | 
| Egyidejű bejelentkezések maximális száma készletenként | 200 | 400 | 800 | 1600 |  2400 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 | 30000 | 
| eDTU-k minimális száma adatbázisonként | {0,25,50,75,<br>125} | {0,25,50,75,<br>125,250} | {0,25,50,75,<br>125,250,500} | {0,25,50,75,<br>125,250,500,<br>1000} | {0,25,50,75,<br>125,250,500,<br>1000,1500} | 
| eDTU-k maximális száma adatbázisonként | {25,50,75,<br>125} | {25,50,75,<br>125,250} | {25,50,75,<br>125,250,500} | {25,50,75,<br>125,250,500,<br>1000} | {25,50,75,<br>125,250,500,<br>1000,1500} |  
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Prémium rugalmas készletek korlátai (folytatás) 

| Készlet mérete (eDTU)  |  **2000** | **2500** | **3000** | **3500** | **4000** |
|:---|---:|---:|---:| ---: | ---: | 
| Maximális tárterület készletenként* | 750 GB | 750 GB | 750 GB | 750 GB | 750 GB |
| Adatbázisok maximális száma készletenként | 100 | 100 | 100 | 100 | 100 | 
| Egyidejű feldolgozók maximális száma készletenként |  3200 | 4000 | 4800 | 5600 | 6400 |
| Egyidejű bejelentkezések maximális száma készletenként |  3200 | 4000 | 4800 | 5600 | 6400 |
| Egyidejű munkamenetek maximális száma készletenként | 30000 | 30000 | 30000 | 30000 | 30000 | 
| eDTU-k minimális száma adatbázisonként | {0,25,50,75,<br>125,250,500,<br>1000,1750} | {0,25,50,75,<br>125,250,500,<br>1000,1750} | {0,25,50,75,<br>125,250,500,<br>1000,1750} | {0,25,50,75,<br>125,250,500,<br>1000,1750} |  {0,25,50,75,<br>125,250,500,<br>1000,1750,4000} | 
| eDTU-k maximális száma adatbázisonként | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750,4000} | 
||||||||

\* A rugalmas adatbázisok osztoznak a készlettárolón, így az adatbázistár számára a készletek fennmaradó tárolókapacitása vagy az adatbázisonkénti engedélyezett tárterület közül a kisebbiknek megfelelő tárterület jut.



<!--HONumber=Jan17_HO1-->


