Az alábbi táblázat a különböző szolgáltatásszintek korlátait tartalmazza (S1, S2, S3, F1). Az egyes szintek *egységeihez* tartozó költségekről további információt az [IoT Hub Díjszabás](https://azure.microsoft.com/pricing/details/iot-hub/) című oldalon talál.

| Erőforrás | S1 – Standard | S2 – Standard | S3 – Standard | F1 – Ingyenes |
| --- | --- | --- | --- | --- |
| Üzenet/nap |400,000 |6,000,000 |300,000,000 |8,000 |
| Egységek maximális száma |200 |200 |10 |1 |

> [!NOTE]
> Ha egy S1 vagy S2 szintű Hubbal előreláthatóan 200-nál több, illetve egy S3 Hubbal 10-nél több egységet fog használni, lépjen kapcsolatba a Microsoft támogatási szolgálatával.
> 
> 

Az alábbi táblázat az IoT Hub-erőforrásokra vonatkozó korlátokat tartalmazza:

| Erőforrás | Korlát |
| --- | --- |
| Azure-előfizetésenkénti fizetős IoT Hubok maximális száma |10 |
| Azure-előfizetésenkénti ingyenes IoT Hubok maximális száma |1 |
| Eszközidentitások egyetlen hívásban visszaadott<br/> maximális száma |1000 |
| Egy eszközről a felhőbe irányuló IoT Hub-üzenetek maximális megőrzési ideje |7 nap |
| Egy eszközről a felhőbe irányuló üzenet maximális mérete |256 KB |
| Egy eszközről a felhőbe irányuló köteg maximális mérete |256 KB |
| Egy eszközről a felhőbe irányuló kötegben található üzenetek maximális száma |500 |
| A felhőből egy eszközre irányuló üzenet maximális mérete |64 KB |
| A felhőből egy eszközre irányuló üzenetek maximális élettartama |2 nap |
| A felhőből egy eszközre történő kézbesítések maximális száma <br/> üzenet |100 |
| A felhőből egy eszközre irányuló üzenetre válaszoló <br/> visszajelzési üzenetek maximális kézbesítésszáma |100 |
| A felhőből egy eszközre irányuló üzenetre válaszoló <br/> visszajelzési üzenetek maximális élettartama |2 nap |
| Ikereszköz maximális mérete <br/> (címkék, jelentett tulajdonságok és kívánt tulajdonságok) | 8 KB |
| Az ikereszköz karakterláncértékének maximális mérete | 512 bájt |
| Az ikereszközben található objektum maximális mélysége | 5 |
| A közvetlen metódus hasznos adatainak maximális mérete | 8 KB |
| Feladatelőzmények maximális megőrzési ideje | 30 nap |
| Egyidejű feladatok maximális száma | 10 (S3 esetén), 5 (S2 esetén), 1 (S1 esetén) |
| További végpontok maximális száma | 10 (S1, S2 és S3 esetén) |
| Üzenet-útválasztási szabályok maximális száma | 100 (S1, S2 és S3 esetén) |


> [!NOTE]
> Ha egy Azure-előfizetésben 10-nél több fizetős IoT Hubra van szüksége, lépjen kapcsolatba a Microsoft támogatási szolgálatával.


> [!NOTE]
> Csatlakozhat egy IoT-központ eszközök maximális számát jelenleg 500 000 értéket. Ha azt szeretné, a korlát növeléséhez, forduljon a [Microsoft Support](https://azure.microsoft.com/en-us/support/options/).

Az IoT Hub szolgáltatás szabályozza a kéréseket az alábbi kvóták átlépése esetén:

| Szabályozás | Kötegenkénti érték |
| --- | --- |
| Identitásjegyzék műveletei <br/> (létrehozás, lekérdezés, listázás, frissítés, törlés), <br/> egyéni vagy tömeges importálás/exportálás |83.33/sec/Unit (5000 min/egységenként) (a S3) <br/> 1.67/sec/Unit (100 perc/egységenként) (a S1 és S2). |
| Eszközkapcsolatok |6000/mp/egység (S3 esetén), 120/mp/egység (S2 esetén), 12/mp/egység (S1 esetén). <br/>Legalább 100/mp. |
| Az eszközről a felhőbe irányuló küldések |6000/mp/egység (S3 esetén), 120/mp/egység (S2 esetén), 12/mp/egység (S1 esetén). <br/>Legalább 100/mp. |
| Küldések a felhőből az eszközökre | 83.33/sec/Unit (5000/perc/egysége) (S3), 1.67/sec/unit (100 perc/egységenként) (a S1 és S2). |
| Fogadások a felhőből az eszközökön |833.33/sec/Unit (50000/perc/egysége) (S3), 16.67/sec/unit (1000/perc / (S1 és) egység S2). |
| Fájlfeltöltési műveletek |83.33 fájl feltöltése értesítések/mp/egység (5000/perc/egysége) (S3), 1.67 fájl feltöltése értesítések/mp / (100 perc/egységenként) (S1 és egység S2). <br/> Egy Azure Storage-tárfiók esetén egyszerre 10000 SAS URI lehet használatban.<br/> Eszközönként egyszerre 10 SAS URI lehet használatban. |
| Közvetlen metódusok | 3000/mp/egységet (S3), 60 másodperc/egységenként (S2), a 20/mp/egységet (S1) |
| Ikereszköz-olvasások | 50/mp/egység (S3 esetén), legfeljebb 10/mp vagy 1/mp/egység (S2 esetén), 10/mp (S1 esetén) |
| Ikereszköz-frissítések | 50/mp/egység (S3 esetén), legfeljebb 10/mp vagy 1/mp/egység (S2 esetén), 10/mp (S1 esetén) |
| Feladatműveletek <br/> (létrehozás, frissítés, listázás, törlés) | 83.33/sec/Unit (5000/perc/egysége) (S3), 1.67/sec/unit (100 perc/egységenként) (az S2), 1.67/sec/unit (100 perc/egységenként) (a S1) |
| Feladatok eszközönkénti műveleti teljesítménye | 50/mp/egység (S3 esetén), legfeljebb 10/mp vagy 1/mp/egység (S2 esetén), 10/mp (S1 esetén) |
