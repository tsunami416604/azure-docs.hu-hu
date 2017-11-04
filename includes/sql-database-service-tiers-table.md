<!--
Used in:
sql-database-performance-guidance.md 
sql-database-single-database-resources.md 
-->

### <a name="basic-service-tier"></a>Alapszintű szolgáltatásszint
| **Teljesítményszint szükséges** | **Basic** |
| :--- | --: |
| DTU-k maximális száma | 5 |
| Belefoglalt tárhely (GB) | 2 |
| Maximális tárolási lehetőségek (GB) | 2 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) |N/A |
| Maximális párhuzamos munkavállalók (kérelmek) | 30 |
| Egyidejű bejelentkezések maximális száma | 30 |
| Egyidejű munkamenetek maximális száma | 300 |
|||

### <a name="standard-service-tier"></a>Standard szolgáltatásszint
| **Teljesítményszint szükséges** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|---:|
| A maximális i ** | 10 | 20 | 50 | 100 |
| Belefoglalt tárhely (GB) | 250 | 250 | 250 | 250 |
| Maximális tárolási lehetőségek (GB) * | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | N/A | N/A | N/A | N/A |
| Maximális párhuzamos munkavállalók (kérelmek)| 60 | 90 | 120 | 200 |
| Egyidejű bejelentkezések maximális száma | 60 | 90 | 120 | 200 |
| Egyidejű munkamenetek maximális száma |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Standard szintű szolgáltatási réteg (Folytatás)
| **Teljesítményszint szükséges** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|---:|
| A maximális i ** | 200 | 400 | 800 | 1600 | 3000 |
| Belefoglalt tárhely (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximális tárolási lehetőségek (GB) * | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | N/A | N/A | N/A | N/A |N/A |
| Maximális párhuzamos munkavállalók (kérelmek)| 400 | 800 | 1600 | 3200 |6000 |
| Egyidejű bejelentkezések maximális száma | 400 | 800 | 1600 | 3200 |6000 |
| Egyidejű munkamenetek maximális száma |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Prémium szolgáltatásszint 
| **Teljesítményszint szükséges** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| DTU-k maximális száma | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Belefoglalt tárhely (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Maximális tárolási lehetőségek (GB) * | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maximális párhuzamos munkavállalók (kérelmek)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Egyidejű bejelentkezések maximális száma | 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Egyidejű munkamenetek maximális száma | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

### <a name="premium-rs-service-tier"></a>Prémium szintű RS szolgáltatási rétegben 
| **Teljesítményszint szükséges** | **PRS1** | **PRS2** | **PRS4** | **PRS6** |
| :--- |---:|---:|---:|---:|---:|---:|
| DTU-k maximális száma | 125 | 250 | 500 | 1000 |
| Belefoglalt tárhely (GB) | 500 | 500 | 500 | 500 |
| Maximális tárolási lehetőségek (GB) * | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | 1 | 2 | 4 | 8 |
| Maximális párhuzamos munkavállalók (kérelmek)| 200 | 400 | 800 | 1600 |
| Egyidejű bejelentkezések maximális száma | 200 | 400 | 800 | 1600 |
| Egyidejű munkamenetek maximális száma | 30000 | 30000 | 30000 | 30000 |
|||||||

> [!IMPORTANT]
> \* A szolgáltatási keretbe foglaltnál nagyobb tárterületek előzetes verzióban érhetők el, és extra költségek vonatkoznak rájuk. Részletes információ: [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/). 
>
>\* Az 1 TB tárhelyméretet meghaladó prémium szintű készletek jelenleg a következő régiókban érhetők el: USA 2. keleti régiója, USA nyugati régiója, USA-beli államigazgatás – Virginia, Nyugat-Európa, Közép-Németország, Délkelet-Ázsia, Kelet-Japán, Kelet-Ausztrália, Közép-Kanada és Kelet-Kanada. Lásd: [P11–P15 – Aktuális korlátozások](../articles/sql-database/sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 
>\*\*Maximális Dtu adatbázisonkénti indítása, 200 dtu-inak száma és az annál magasabb a Standard még csak előzetes verziójúak.
>
