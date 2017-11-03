---
title: "Azure-adatbázis tarifacsomagjainak a MySQL |} Microsoft Docs"
description: "A MySQL az Azure-adatbázis árképzési szinteket"
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 05/23/2017
ms.openlocfilehash: d9ec4556d57ff1975a93d806237ad0c7416b9988
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-mysql-options-and-performance-understand-whats-available-in-each-pricing-tier"></a>Azure-adatbázis a MySQL beállításai és teljesítménye: az egyes tarifacsomagok elérhető
Egy MySQL-kiszolgálóhoz tartozó Azure-adatbázis létrehozásakor három fő lehetőségek konfigurálása a kiszolgáló számára lefoglalt erőforrások határoz meg. Ezek a lehetőségek a teljesítmény és a kiszolgáló méretezési hatással.
- Tarifacsomag
- Számítási egységek
- Storage (GB)

Minden tarifacsomag tartománnyal rendelkező teljesítményszintek (számítási egység), a munkaterhelések követelményeitől függően választhat. Magasabb teljesítményszintet Amennyiben további erőforrás a kiszolgáló továbbít nagyobb átviteli sebességgel. Módosíthatja a tarifacsomag gyakorlatilag alkalmazásleállás belül a kiszolgáló teljesítményszint szükséges.

> [!IMPORTANT]
> A szolgáltatás pedig nyilvános előzetes verziójában nincs egy garantált szolgáltatási szint szerződés (SLA).

Egy MySQL-hez készült Azure Database-en belül egy vagy több adatbázist is fenntarthat. Dönthet úgy, hogy az erőforrások teljes kihasználása érdekében kiszolgálónként egy adatbázist hoz létre, vagy úgy, hogy több adatbázis létrehozásával megosztja az erőforrásokat. 

## <a name="choose-a-pricing-tier"></a>Válasszon tarifacsomagot
A képen MySQL az Azure-adatbázis két tarifacsomagok kínál: Basic és Standard. Prémium csomagban még nem érhető el, de hamarosan elérhető. 

Az alábbi táblázat példákat az ajánlott tarifacsomagok olyan másik alkalmazás igénylő munkaterhelésekhez ajánlott.

| Tarifacsomag | Kívánt teljesítményprofilok |
| :----------- | :----------------|
| Basic | Kis szolgáltatások, amelyek méretezhető számítási és tárolási IOPS biztosítása nélkül a legalkalmasabb. Ilyen például a fejlesztési vagy tesztelési használt kiszolgálók, vagy csak kevés számítógépet érintő ritkán használt alkalmazások. |
| Standard | A megoldás felhő IOPS igénylő alkalmazások biztosítása a nagy átviteli sebességgel. Például a web- vagy analitikai alkalmazások. |
| Prémium | Kis késleltetésű tranzakciók és IO igénylő munkaterheléseknél leginkább megfelelő. A legjobb támogatja a nagyszámú egyidejű felhasználót. Adatbázisok, amelyek támogatják az alapvető fontosságú alkalmazások alkalmazható.<br />A prémium tarifacsomag nem érhető el az előzetes verzió. |

Döntse el, hogy a tarifacsomagot, először meghatározhatja, hogy a munkaterhelés kell egy IOPS garantált. Ha igen, használja a Standard tarifacsomag.

| **Árképzési szint szolgáltatások** | **Basic** | **Standard** |
| :------------------------ | :-------- | :----------- |
| Maximális számítási egység | 100 | 800 | 
| Teljes tároló maximális mérete | 1 TB | 1 TB | 
| Tárolási IOPS garancia | N/A | Igen | 
| Maximális tárolási iops-érték | N/A | 3,000 | 
| Adatbázis biztonsági mentés megőrzési időtartam | 7 nap | 35 nap | 

Az előnézet időtartam alatt nem módosítható a tarifacsomagot, ha a kiszolgáló akkor jön létre. A jövőben lesz lehetséges egy másik réteghez egy tarifacsomagot kiszolgáló visszaminősítését vagy frissítéséhez.

## <a name="understand-the-price"></a>Az ár ismertetése
Amikor létrehoz egy új Azure-adatbázis a MySQL belül a [Azure Portal](https://portal.azure.com/#create/Microsoft.MySQLServer), kattintson a **tarifacsomag** panelen, és a havi költségét is megjelennek alapján a kiválasztott beállításokat. Ha még nem rendelkezik Azure-előfizetéssel, az Azure árképzési Számológép segítségével egy becsült ár. Látogasson el a [Azure árképzési Számológép](https://azure.microsoft.com/pricing/calculator/) webhely, kattintson a **elemek hozzáadása**, bontsa ki a **adatbázisok** kategóriát, és válassza a **MySQL az Azure-adatbázis** a beállítások testreszabása.

## <a name="choose-a-performance-level-compute-units"></a>Itt választhatja ki a teljesítmény (számítási egység)
Miután megadta, hogy a tarifacsomag kiválasztása az Azure-adatbázis MySQL-kiszolgáló, készen áll a teljesítményszintet meghatározásához szükséges számítási egységek számának kiválasztásával. Jó kiindulási pont 200 vagy 400 számítási egység van szüksége a magasabb szintű felhasználói párhuzamossági webes és analitikai munkaterhelések, és fokozatosan igény szerint módosítsa alkalmazások esetében. 

Számítási egység garantáltan MySQL-kiszolgáló egy Azure-adatbázis számára elérhető, a Processzor feldolgozási átviteli sebesség mértéke. A számítási egység mérőszáma kevert CPU és memória-erőforrásokat.  További információkért lásd: [ismertető számítási egység](concepts-compute-unit-and-storage.md)

### <a name="basic-pricing-tier-performance-levels"></a>Alapszintű árképzési szint teljesítményszintet:

| **Teljesítményszint szükséges** | **50** | **100** |
| :-------------------- | :----- | :------ |
| Maximális számítási egység | 50 | 100 |
| Belefoglalt tároló mérete | 50 GB | 50 GB |
| Kiszolgáló tároló maximális\* | 1 TB | 1 TB |

### <a name="standard-pricing-tier-performance-levels"></a>Standard árképzési szint teljesítményszintet:

| **Teljesítményszint szükséges** | **100** | **200** | **400** | **800** |
| :-------------------- | :------ | :------ | :------ | :------ |
| Maximális számítási egység | 100 | 200 | 400 | 800 |
| Belefoglalt és a kiosztott IOPS | 125 GB<br/> 375 IOPS | 125 GB<br/> 375 IOPS | 125 GB<br/> 375 IOPS | 125 GB<br/> 375 IOPS |
| Kiszolgáló tároló maximális\* | 1 TB | 1 TB | 1 TB | 1 TB |
| Kiszolgáló maximális IOPS kiépítve | 3000 IOPS | 3000 IOPS | 3000 IOPS | 3000 IOPS |
| Maximális server kiépített IOPS / GB | Rögzített 3 IOPS / GB | Rögzített 3 IOPS / GB | Rögzített 3 IOPS / GB | Rögzített 3 IOPS / GB |

\*Kiszolgáló tároló maximális a kiszolgáló maximális kiosztott tárolási méretét jelenti.

## <a name="storage"></a>Storage 
A tárolási konfiguráció egy Azure-adatbázishoz MySQL-kiszolgáló határozza meg a rendelkezésre álló tárolókapacitás mennyiségét. A szolgáltatás által használt tároló tartalmazza, az adatbázisfájlokat, a tranzakciós naplók és a MySQL-kiszolgáló naplóit. Vegye figyelembe a futtatásához az adatbázisok és a teljesítménykövetelményekhez (IOPS) lehetőséget választva a tárolási konfiguráció szükséges tárterület mérete.

Bizonyos tárolási kapacitás megtalálható legalább az egyes tarifacsomagok, a tartalomjegyzékben említett előző méreteként"befoglalt tárolási." Ha a kiszolgáló akkor jön létre, akár a maximális megengedett tárolási 125 GB-os lépésekben további tárolási kapacitás adható hozzá. A további tárolási kapacitás a számítási egység konfigurációs függetlenül konfigurálhatók. A kiválasztott tárolókapacitást alapján ár módosításokat.

Az egyes IOPS konfigurálása az árképzési szint és a választott tárméret vonatkozik. Az alapszintű csomag nem biztosít egy IOPS garantált. A Standard tarifacsomag, belül az IOPS méretezhető arányosan egy rögzített 3:1 arányban maximális mérete. A belefoglalt tárolási 125 GB-os biztosítja, hogy a 375 kiosztott IOPS, az egy IO mérete legfeljebb 256 KB-os. Ha szeretné, további tárhely 1 TB-os legfeljebb 3000 olyan kiosztott IOPS biztosításához.

Az Azure-portálon a metrikák diagramhoz figyelése, vagy tárolási és IOPS mérésére Azure CLI-parancsok írásával. Figyelése vonatkozó adatok gyűjtése le tárolási kapacitása, tárolási százalékos, használt tárolási és IO százalék.

>[!IMPORTANT]
> Kép nézetben, válassza ki a tárolókapacitást a kiszolgáló létrehozásának időpontjában. A tárhely méretét, a meglévő kiszolgáló módosítása még nem támogatott. 

## <a name="scaling-a-server-up-or-down"></a>A kiszolgáló skálázás feljebb vagy lejjebb
Akkor kezdetben Itt választhatja ki a tarifacsomagot és teljesítményszintet a MySQL az Azure-adatbázis létrehozásakor. Később, a számítási egység méretezheti felfelé vagy lefelé dinamikusan, az azonos árképzési szint tartományán belül. Az Azure portálon, húzza a számítási egység a kiszolgáló árazás réteg panelen, vagy parancsfájl az ebben a példában a következő: [figyelő és a skála egy Azure-adatbázis MySQL-kiszolgáló Azure parancssori felület használatával](scripts/sample-scale-server.md)

A számítási egység skálázás történik, függetlenül a választott maximális mérete.

A háttérben módosítása az adatbázis teljesítményszintjét alkalmazás egy replikát készít az eredeti adatbázist az új teljesítmény szintjén, és majd kapcsolatok való működésre vált a replikát. Adatok nem vesztek el a folyamat során. Során mikor azt átválthat a replika rövid néhány percet kapcsolatok az adatbázis le vannak tiltva, így felé továbbított egyes tranzakciók előfordulhat, hogy állítható vissza. Ennek időtartama eltérő lehet, de átlagosan 4 másodpercnél rövidebb, az esetek 99%-ában pedig kevesebb mint 30 másodperc. Ha a kapcsolatok letiltásának pillanatában nagy számú tranzakció van folyamatban, ez az időtartam hosszabb lehet.

A teljes méretezési eljárás ideje alatt méretét és függ IP-címek a kiszolgáló előtt és után a változás. Például egy kiszolgálót, amely a számítási egység módosítás, a Standard tarifacsomag belül kell végeznie néhány percen belül. Az új tulajdonságok a kiszolgáló nem érvényesek, amíg be nem fejeződik a módosításokat.

## <a name="next-steps"></a>Következő lépések
- A számítási egységek kapcsolatban bővebben lásd: [ismertető számítási egység](concepts-compute-unit-and-storage.md)
- Megtudhatja, hogyan [figyelő és a skála egy Azure-adatbázis MySQL-kiszolgáló Azure parancssori felület használatával](scripts/sample-scale-server.md)
