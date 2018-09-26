<!--
Used in:
sql-database-performance-guidance.md 
sql-database-single-database-resources.md 
-->

### <a name="basic-service-tier"></a>Alapszintű szolgáltatásszint
| **Teljesítményszint** | **Basic** |
| :--- | --: |
| DTU-k maximális száma | 5 |
| Belefoglalt tárterület (GB) | 2 |
| Maximális tárolási lehetőségeket (GB) | 2 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) |– |
| Egyidejű feldolgozók (kérelmek) maximális | 30 |
| Egyidejű bejelentkezések maximális száma | 30 |
| Egyidejű munkamenetek maximális száma | 300 |
|||

### <a name="standard-service-tier"></a>Standard szolgáltatásszint
| **Teljesítményszint** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|---:|
| Maximális dtu-k ** | 10 | 20 | 50 | 100 |
| Belefoglalt tárterület (GB) | 250 | 250 | 250 | 250 |
| Maximális tárolási lehetőségeket (GB) * | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | – | N/A | N/A | – |
| Egyidejű feldolgozók (kérelmek) maximális| 60 | 90 | 120 | 200 |
| Egyidejű bejelentkezések maximális száma | 60 | 90 | 120 | 200 |
| Egyidejű munkamenetek maximális száma |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Standard szintű szolgáltatáscsomagban (Folytatás)
| **Teljesítményszint** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|---:|
| Maximális dtu-k ** | 200 | 400 | 800 | 1600 | 3000 |
| Belefoglalt tárterület (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximális tárolási lehetőségeket (GB) * | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | – | N/A | N/A | N/A |– |
| Egyidejű feldolgozók (kérelmek) maximális| 400 | 800 | 1600 | 3200 |6000 |
| Egyidejű bejelentkezések maximális száma | 400 | 800 | 1600 | 3200 |6000 |
| Egyidejű munkamenetek maximális száma |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Prémium szolgáltatásszint 
| **Teljesítményszint** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| DTU-k maximális száma | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Belefoglalt tárterület (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Maximális tárolási lehetőségeket (GB) * | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Egyidejű feldolgozók (kérelmek) maximális| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Egyidejű bejelentkezések maximális száma | 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Egyidejű munkamenetek maximális száma | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||


> [!IMPORTANT]
> \* A szolgáltatási keretbe foglaltnál nagyobb tárterületek előzetes verzióban érhetők el, és extra költségek vonatkoznak rájuk. Részletes információ: [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/). 
>
>\* Az 1 TB tárhelyméretet meghaladó prémium szintű készletek jelenleg a következő régiókban érhetők el: Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-Kanada, Kelet-Kanada, USA középső régiója, Közép-Franciaország, Közép-Németország, Kelet-Japán, Nyugat-Japán, Korea középső régiója, USA északi középső régiója, Észak-Európa, USA déli középső régiója, Délkelet-Ázsia, az Egyesült Királyság déli régiója, az Egyesült Királyság nyugati régiója, USA keleti régiója 2, USA nyugati régiója, USA-beli államigazgatás – Virginia, és Nyugat-Európa. Lásd: [P11–P15 – Aktuális korlátozások](../articles/sql-database/sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 
>\*\* Maximális Dtu adatbázisonként 200 dtu-k és újabb kezdve a Standard előzetes verzióként érhetők el.
>

