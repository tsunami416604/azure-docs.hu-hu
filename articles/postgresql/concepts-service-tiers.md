---
title: "Árképzési szinteket az Azure PostgreSQL-adatbázishoz"
description: "Árképzési szinteket az Azure PostgreSQL-adatbázishoz"
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 9de2d93735942903df9320741baadf2c934bd7ab
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="azure-database-for-postgresql-options-and-performance-understand-whats-available-in-each-pricing-tier"></a>Azure-adatbázishoz tartozó PostgreSQL beállításai és teljesítménye: az egyes tarifacsomagok elérhető
Egy PostgreSQL-kiszolgálóhoz tartozó Azure-adatbázis létrehozásakor három fő lehetőségek konfigurálása a kiszolgáló számára lefoglalt erőforrások határoz meg. Ezek a lehetőségek a teljesítmény és a kiszolgáló méretezési hatással.
- Tarifacsomag
- Számítási egységek
- Storage (GB)

Minden tarifacsomag tartománnyal rendelkező teljesítményszintek (számítási egység), a munkaterhelések követelményeitől függően választhat. Magasabb teljesítményszintet Amennyiben további erőforrás a kiszolgáló továbbít nagyobb átviteli sebességgel. Módosíthatja a tarifacsomag gyakorlatilag alkalmazásleállás belül a kiszolgáló teljesítményszint szükséges.

> [!IMPORTANT]
> A szolgáltatás pedig nyilvános előzetes verziójában nincs egy garantált szolgáltatási szint szerződés (SLA).

Azure adatbázisban PostgreSQL-kiszolgáló egy vagy több adatbázist is. Dönthet úgy is, egy kiszolgálón szeretné, hogy az adatbázis összes kiszolgáló erőforrásainak használatát, vagy hozzon létre a kiszolgáló erőforrások megosztása több adatbázis egyetlen adatbázist hozhat létre. 

## <a name="choose-a-pricing-tier"></a>Válasszon tarifacsomagot
A képen PostgreSQL az Azure-adatbázis két tarifacsomagok kínál: Basic és Standard. Prémium csomagban még nem érhető el, de hamarosan elérhető. 

Az alábbi táblázat példákat az ajánlott tarifacsomagok olyan másik alkalmazás igénylő munkaterhelésekhez ajánlott.

| Tarifacsomag | Kívánt teljesítményprofilok |
| :----------- | :----------------|
| Alapszintű | Kis szolgáltatások, amelyek méretezhető számítási és tárolási IOPS biztosítása nélkül a legalkalmasabb. Ilyen például a fejlesztési vagy tesztelési használt kiszolgálók, vagy csak kevés számítógépet érintő ritkán használt alkalmazások. |
| Standard | A megoldás felhő IOPS igénylő alkalmazások biztosítása a nagy átviteli sebességgel. Például a web- vagy analitikai alkalmazások. |
| Prémium | Kis késleltetésű tranzakciók és IO igénylő munkaterheléseknél leginkább megfelelő. A legjobb támogatja a nagyszámú egyidejű felhasználót. Adatbázisok, amelyek támogatják az alapvető fontosságú alkalmazások alkalmazható.<br />A prémium tarifacsomag nem érhető el az előzetes verzió. |

Döntse el, hogy a tarifacsomagot, először meghatározhatja, hogy a munkaterhelés kell egy IOPS garantált. Ha igen, használja a Standard tarifacsomag.

| **Árképzési szint szolgáltatások** | **Basic** | **Standard** |
| :------------------------ | :-------- | :----------- |
| Maximális számítási egység | 100 | 800 | 
| Teljes tároló maximális mérete | 1 TB | 1 TB | 
| Tárolási IOPS garancia | – | Igen | 
| Maximális tárolási iops-érték | – | 3,000 | 
| Adatbázis biztonsági mentés megőrzési időtartam | 7 nap | 35 nap | 

Az előnézet időtartam alatt nem módosítható a tarifacsomagot, ha a kiszolgáló akkor jön létre. A jövőben lesz lehetséges egy másik réteghez egy tarifacsomagot kiszolgáló visszaminősítését vagy frissítéséhez.

## <a name="understand-the-price"></a>Az ár ismertetése
Amikor létrehoz egy új Azure-adatbázis a PostgreSQL belül a [Azure-portálon](https://portal.azure.com/#create/Microsoft.PostgreSQLServer), kattintson a **tarifacsomag** lap, és a havi költségeket látható alapján a kiválasztott beállítások. Ha még nem rendelkezik Azure-előfizetéssel, az Azure árképzési Számológép segítségével egy becsült ár. Látogasson el a [Azure árképzési Számológép](https://azure.microsoft.com/pricing/calculator/) webhely, kattintson a **elemek hozzáadása**, bontsa ki a **adatbázisok** kategóriát, és válassza a **PostgreSQL az Azure-adatbázis**  a beállítások testreszabása.

## <a name="choose-a-performance-level-compute-units"></a>Itt választhatja ki a teljesítmény (számítási egység)
Miután megadta, hogy a tarifacsomag kiválasztása az Azure-adatbázis PostgreSQL-kiszolgáló, készen áll a teljesítményszintet meghatározásához szükséges számítási egységek számának kiválasztásával. Jó kiindulási pont 200 vagy 400 számítási egység van szüksége a magasabb szintű felhasználói párhuzamossági webes és analitikai munkaterhelések, és fokozatosan igény szerint módosítsa alkalmazások esetében. 

Számítási egység garantáltan PostgreSQL-kiszolgáló egy Azure-adatbázis számára elérhető, a Processzor feldolgozási átviteli sebesség mértéke. A számítási egység mérőszáma kevert CPU és memória-erőforrásokat.  További információkért lásd: [számítási egység ismertető](concepts-compute-unit-and-storage.md).

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
| Belefoglalt és a kiosztott IOPS | 125 GB,<br/> 375 IOPS | 125 GB,<br/> 375 IOPS | 125 GB,<br/> 375 IOPS | 125 GB,<br/> 375 IOPS |
| Kiszolgáló tároló maximális\* | 1 TB | 1 TB | 1 TB | 1 TB |
| Kiszolgáló maximális IOPS kiépítve | 3000 IOPS | 3000 IOPS | 3000 IOPS | 3000 IOPS |
| Maximális server kiépített IOPS / GB | Rögzített 3 IOPS / GB | Rögzített 3 IOPS / GB | Rögzített 3 IOPS / GB | Rögzített 3 IOPS / GB |

\* Kiszolgáló tároló maximális a kiszolgáló maximális kiosztott tárolási méretét jelenti.

## <a name="storage"></a>Tárolás 
A tárolási konfiguráció egy Azure-adatbázishoz PostgreSQL-kiszolgáló határozza meg a rendelkezésre álló tárolókapacitás mennyiségét. A szolgáltatás által használt tároló tartalmazza, az adatbázisfájlokat, az ideiglenes fájlok, a tranzakciós naplók és a a PostgreSQL-kiszolgáló naplóit. Vegye figyelembe a futtatásához az adatbázisok és a teljesítménykövetelményekhez (IOPS) lehetőséget választva a tárolási konfiguráció szükséges tárterület mérete.

Bizonyos tárolási kapacitás megtalálható legalább az egyes tarifacsomagok, a tartalomjegyzékben említett előző méreteként"befoglalt tárolási." Ha a kiszolgáló akkor jön létre, akár a maximális megengedett tárolási 125 GB-os lépésekben további tárolási kapacitás adható hozzá. A további tárolási kapacitás a számítási egység konfigurációs függetlenül konfigurálhatók. A kiválasztott tárolókapacitást alapján ár módosításokat.

Az egyes IOPS konfigurálása az árképzési szint és a választott tárméret vonatkozik. Az alapszintű csomag nem biztosít egy IOPS garantált. A Standard tarifacsomag, belül az IOPS méretezhető arányosan egy rögzített 3:1 arányban maximális mérete. 125 GB-os garanciák 375 a belefoglalt tárolási IOPS, az egy IO mérete legfeljebb 256 KB-os kiépítve. Ha szeretné, további tárhely 1 TB-os legfeljebb 3000 olyan kiosztott IOPS biztosításához.

Az Azure-portálon a metrikák diagramhoz figyelése, vagy tárolási és IOPS mérésére Azure CLI-parancsok írásával. Figyelésére vonatkozó adatok gyűjtése le tárolási kapacitása, tárolási, tárhelyet,- és IO %.

>[!IMPORTANT]
> Kép nézetben, válassza ki a tárolókapacitást a kiszolgáló létrehozásának időpontjában. A tárhely méretét, a meglévő kiszolgáló módosítása még nem támogatott. 

## <a name="scaling-a-server-up-or-down"></a>A kiszolgáló skálázás feljebb vagy lejjebb
Az árképzési szint és a teljesítmény szint kezdetben az Azure-adatbázis létrehozásakor az PostgreSQL válassza. Később, a számítási egység méretezheti felfelé vagy lefelé dinamikusan, az azonos árképzési szint tartományán belül. Az Azure portálon, húzza a számítási egység a kiszolgáló árazás réteg oldalán, vagy parancsfájl az ebben a példában a következő: [figyelő és a skála egy PostgreSQL egykiszolgálós Azure parancssori felület használatával](scripts/sample-scale-server-up-or-down.md)

A számítási egység skálázás történik, függetlenül a választott maximális mérete.

A háttérben módosítása az adatbázis teljesítményszintjét másolatot hoz létre az eredeti kiszolgálón az új teljesítmény szintjén, és majd kapcsolatok való működésre vált a másolt kiszolgáló. Adatok nem vesztek el a folyamat során. Során a rövid jelenleg, ha az új kiszolgáló átvált a rendszer Ha az adatbázishoz létesített kapcsolatokhoz le vannak tiltva, az egyes tranzakciók útban előfordulhat, hogy állítható vissza. Ebben az ablakban változik, de átlagosan 4 másodperc alatt, és az esetek több mint 99 % kevesebb, mint 30 másodperc. Ha a kapcsolatok letiltásának pillanatában nagy számú tranzakció van folyamatban, ez az időtartam hosszabb lehet.

A teljes méretezési eljárás ideje alatt méretét és függ IP-címek a kiszolgáló előtt és után a változás. Például egy kiszolgálót, amely a számítási egység módosítás, a Standard tarifacsomag belül kell végeznie néhány percen belül. Az új tulajdonságok a kiszolgáló nem érvényesek, amíg be nem fejeződik a módosításokat.

## <a name="next-steps"></a>További lépések
- A számítási egységek kapcsolatban bővebben lásd: [ismertető számítási egység](concepts-compute-unit-and-storage.md)
- Megtudhatja, hogyan [figyelő és a skála egy PostgreSQL egykiszolgálós Azure parancssori felület használatával](scripts/sample-scale-server-up-or-down.md)
