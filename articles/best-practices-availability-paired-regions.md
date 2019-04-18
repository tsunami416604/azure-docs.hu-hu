---
title: 'Üzleti folytonosság és vészhelyreállítás recovery (BCDR): Azure – párosított régiók |} A Microsoft Docs'
description: További információ az Azure régiónkénti párosítás, annak érdekében, hogy alkalmazásokat rugalmas adatközpontok meghibásodásának során.
author: rayne-wiselman
manager: carmon
ms.service: multiple
ms.topic: article
ms.date: 04/17/2019
ms.author: raynew
ms.openlocfilehash: ecbe73e02631e3c3601bd929282d467cb05b41e4
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678870"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Üzleti folytonosság és vészhelyreállítás recovery (BCDR): Azure – párosított régiók

## <a name="what-are-paired-regions"></a>Mik a párosított régiók?

Az Azure világszerte több földrajzi területeken működik. Egy Azure földrajzi területet adtunk a világ, amely tartalmaz legalább egy Azure-régióban. Egy Azure-régióban egy olyan terület, a földrajzi helyen tartalmazó egy vagy több adatközpont tartozhat.

Minden egyes Azure-régió párban áll egy regionális párokból érdemes együtt így azonos földrajzi helyen belül egy másik régióban. A kivétel, Dél-Brazília, amely kívül a földrajzi régió párban áll. A régiópárok között az Azure platform frissítések (tervezett karbantartás) szerializálja, úgy, hogy csak egy párosított régióban egyszerre frissül. Több régiót érintő leállás esetén minden párból legalább egy régió prioritása helyreállítási lehet.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

1. ábra – az Azure regionális párok

| Földrajzi hely | Párosított régiók |  |
|:--- |:--- |:--- |
| Ázsia |Kelet-Ázsia |Délkelet-Ázsia |
| Ausztrália |Kelet-Ausztrália |Délkelet-Ausztrália |
| Ausztrália |Ausztrália középső régiója |Ausztrália 2. középső régiója |
| Brazília |Dél-Brazília |USA déli középső régiója |
| Kanada |Közép-Kanada |Kelet-Kanada |
| Kína |Észak-Kína |Kelet-Kína|
| Kína |Észak-Kína 2 |Kelet-Kína 2|
| Európa |Észak-Európa |Nyugat-Európa |
| Franciaország |Közép-Franciaország|Dél-Franciaország|
| Németország |Közép-Németország |Északkelet-Németország |
| Németország |Észak-Németország | Németország nyugati középső régiója
| India |Közép-India |Dél-India |
| India |Nyugat-India |Dél-India |
| Japán |Kelet-Japán |Nyugat-Japán |
| Korea |Korea középső régiója |Korea déli régiója |
| Észak-Amerika |USA keleti régiója |USA nyugati régiója |
| Észak-Amerika |USA 2. keleti régiója |USA középső régiója |
| Észak-Amerika |USA északi középső régiója |USA déli középső régiója |
| Észak-Amerika |USA nyugati régiója, 2. |USA nyugati középső régiója 
| Észak-Amerika |USA nyugati Régiójában, 3 |USA keleti régiója
| Norvégia |Norvégia keleti régiója |Norvégia nyugati régiója
| Dél-Afrika | Dél-Afrika északi régiója | Dél-Afrika nyugati régiója
| Svédország |Svéd közép-India |Svéd Dél-India
| Svájc | Svájc északi régiója | Svájc nyugati régiója
| Egyesült Királyság |Az Egyesült Királyság nyugati régiója |Az Egyesült Királyság déli régiója |
| Egyesült Királyság |Egyesült Királyság északi régiója |Egyesült Királyság 2. déli régiója
| Egyesült Arab Emírségek | Az Egyesült Arab Emírségek északi régiója | UAE Center
| Védelmi Minisztérium, USA |US DoD – Kelet |US DoD – Középső régió |
| Az USA kormányzata |USA-beli államigazgatás – Arizona |USA-beli államigazgatás – Texas |
| Az USA kormányzata |US Gov Iowa |USA-beli államigazgatás – Virginia |
| Az USA kormányzata |USA-beli államigazgatás – Virginia |USA-beli államigazgatás – Texas |

1. táblázat – Azure regionális párok leképezése

- Nyugat-Indiát, csak egy irányban párosítva van. Nyugat-Indiát, a másodlagos régióba Dél-India, Dél-India másodlagos régióba azonban közép-India.
- Dél-brazíliai régióban egy egyedülálló megoldás, mert kívül a saját földrajzi régió párban áll. Dél-Brazília másodlagos régió az USA déli középső Régiójában. USA déli középső Régiójában a másodlagos régió nem Dél-brazíliai régióban.
- Másodlagos USA beli államigazgatás – Iowa régióban US Gov Virginia.
- US Gov Virginia másodlagos régióba US Gov Texas.
- US Gov Texas másodlagos régió az USA Arizona állam.


Javasoljuk, hogy úgy beállítani, hogy üzleti folytonossági vészhelyreállítás (BCDR) számára, hogy az Azure-elkülönítési és rendelkezésre állás házirendek regionális párok között. Több aktív régiók támogató alkalmazások esetében azt javasoljuk használatával régió párban mindkét régióban, ahol csak lehetséges. Ez biztosítja az optimális rendelkezésre állását, alkalmazások és a egy esetleges vészhelyzet esetén kis méretben helyreállítási idő. 

## <a name="an-example-of-paired-regions"></a>Egy példa párosított régiók
2. ábra alább látható egy képzeletbeli alkalmazást, amely a regionális párokból érdemes használja a vész-helyreállítási. A zöld számok jelölje ki a régiók közötti tevékenységek három Azure-szolgáltatások (Azure számítási, tárolási és adatbázis-) és azok miként vannak konfigurálva a régiók közötti replikálására. Az üzembe helyezést több párosított régióra egyedi előnyeit a narancssárga számok ki vannak emelve.

![Párosított régió előnyt áttekintése](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

2. ábra – elméleti Azure regionális párokból érdemes

## <a name="cross-region-activities"></a>Régiók közötti tevékenységek
2. ábra az említett.

![IaaS](./media/best-practices-availability-paired-regions/1Green.png) **Azure Compute (IaaS)** – további számítási erőforrásokat előzetesen győződjön meg, hogy az erőforrások elérhetők egy másik régióban egy katasztrófa során kell kiépítenie. További információkért lásd: [műszaki útmutatást az Azure rugalmassága](resiliency/resiliency-technical-guidance.md).

![Tárolási](./media/best-practices-availability-paired-regions/2Green.png) **Azure Storage** -Georedundáns tárolás (GRS) alapértelmezés szerint konfigurálva, egy Azure Storage-fiók létrehozásakor. A grs Tárolással az adatok automatikus replikációja háromszor az elsődleges régióban, és három alkalommal a párosított régióban. További információkért lásd: [Azure Storage Redundanciabeállításainál](storage/common/storage-redundancy.md).

![Az Azure SQL](./media/best-practices-availability-paired-regions/3Green.png) **Azure SQL Database** – az Azure SQL Database Georeplikációja, konfigurálhatja a tranzakciók bármely régióba aszinkron replikációját a világ; azonban javasoljuk, hogy ezeket az erőforrásokat a telepít egy párosított régió legtöbb vész-helyreállítási helyzetekben. További információkért lásd: [Georeplikáció az Azure SQL Database](sql-database/sql-database-geo-replication-overview.md).

![Erőforrás-kezelő](./media/best-practices-availability-paired-regions/4Green.png) **Azure Resource Manager** – Resource Manager természetüknél fogva biztosítják azok logikai elkülönítését összetevők régiók között elosztva. Ez azt jelenti, hogy egy adott régióban logikai hibák kevésbé valószínű, hogy egy másik hatással.

## <a name="benefits-of-paired-regions"></a>Párosított régiók előnyei
2. ábra az említett.  

![Elkülönítés](./media/best-practices-availability-paired-regions/5Orange.png)
**fizikai elkülönítése** – Ha lehetséges, az Azure legalább 300 mérföld kellő mértékű elkülönítése a regionális párok adatközpontok részesíti előnyben, bár ez nem praktikus, vagy lehetséges az összes régióban. A fizikai adatközpontban működnek elkülönítése csökkenti a természeti katasztrófák, zavargások, áramkimaradások vagy fizikai hálózat fennakadásai mindkét régióban egyszerre valószínűségét. Elkülönítés a korlátok (földrajzi méret, teljesítmény és a hálózati infrastruktúra elérhetőségét, szabályzat, stb.) földrajzi helyen belül van.  

![Replikációs](./media/best-practices-availability-paired-regions/6Orange.png)
**Platform által biztosított replikáció** – bizonyos szolgáltatások, például a Georedundáns tárolás adja meg a replikálás automatikus, a párosított régióba.

![Helyreállítási](./media/best-practices-availability-paired-regions/7Orange.png)
**régió helyreállítási rendelés** – abban az esetben, ha egy széles körű leállás esetén az egyik régió helyreállítása előnyt élvez minden párból. Párosított régióban üzembe helyezett alkalmazások garantáltan rendelkezik egy helyre prioritással rendelkező régiót. Ha egy alkalmazás, amely a rendszer nem a párosított régióban üzemel, a helyreállítási késésével – a legrosszabb esetben a kiválasztott régióban lehet, hogy az utolsó két kell helyreállítani őket.

![Frissítések](./media/best-practices-availability-paired-regions/8Orange.png)
**egymást követő Adatfrissítés** – tervezett Azure-rendszerfrissítések jelennek meg a társított két régió bármelyikén egymás után (nem egy időben) állásidő, hibák, és a egy hibás, a ritka esemény logikai hibák hatásának minimálisra csökkentése érdekében frissítés.

![Adatok](./media/best-practices-availability-paired-regions/9Orange.png)
**adattárolás** – egy régióban található a pár (kivéve Dél-brazíliai), a megegyező területen található adatok tárolási helyére vonatkozó előírásoknak, ami adóügyi és törvényi végrehajtás szempontjából joghatósága betartása érdekében.
