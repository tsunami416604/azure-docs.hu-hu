---
title: Üzletmenet-folytonosság & vész-helyreállítás – Azure párosított régiók
description: Ismerje meg az Azure regionális párosítását, és győződjön meg arról, hogy az alkalmazások rugalmasak az adatközpont meghibásodása során.
author: rayne-wiselman
manager: carmon
ms.service: multiple
ms.topic: article
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: 90111325677e1bdd12a03081ad7513a34f68fd40
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044136"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Üzletmenet-folytonosság és vész-helyreállítási (BCDR): Azure párosított régiók

## <a name="what-are-paired-regions"></a>Mik azok a párosított régiók?

Az Azure világszerte több földrajzi régión működik. Az Azure földrajz a világ egy meghatározott területe, amely legalább egy Azure-régiót tartalmaz. Az Azure-régió egy földrajzon belüli terület, amely egy vagy több adatközpontot tartalmaz.

Minden egyes Azure-régió párosítva van egy másik régióval, amely ugyanabban a földrajzi helyen található, és regionális párokat alkot. A kivétel Dél-Brazília, amely a földrajzon kívüli régióhoz van párosítva. A régió pár részeként az Azure szerializálja a platform frissítéseit (tervezett karbantartás), így egyszerre csak egy párosított régió frissül. Ha egy leállás több régiót érint, akkor az egyes párokban legalább egy régiót előnyben részesíti a helyreállítás.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

1\. ábra – Azure regionális párok

| földrajz | Párosított régiók |  |
|:--- |:--- |:--- |
| Ázsia |Kelet-Ázsia |Délkelet-Ázsia |
| Ausztrália |Ausztrália keleti régiója |Délkelet-Ausztrália |
| Ausztrália |Ausztrália középső régiója |Ausztrália 2. középső régiója |
| Brazília |Dél-Brazília |USA déli középső régiója |
| Kanada |Közép-Kanada |Kelet-Kanada |
| Kína |Észak-Kína |Kelet-Kína|
| Kína |Kína 2. északi régiója |Kína 2. keleti régiója|
| Európa |Észak-Európa (Írország) |Észak-Európa (Hollandia) |
| Franciaország |Közép-Franciaország|Dél-Franciaország|
| Németország |Közép-Németország |Északkelet-Németország |
| India |Közép-India |Dél-India |
| India |Nyugat-India |Dél-India |
| Japán |Kelet-Japán |Nyugat-Japán |
| Dél-Korea |Korea középső régiója |Dél-Korea |
| Észak-Amerika |USA keleti régiója |USA nyugati régiója |
| Észak-Amerika |USA 2. keleti régiója |USA középső régiója |
| Észak-Amerika |USA északi középső régiója |USA déli középső régiója |
| Észak-Amerika |USA 2. nyugati régiója |USA nyugati középső régiója 
| Dél-Afrika | Dél-Afrika északi régiója | Dél-Afrika nyugati régiója
| Egyesült Királyság |Egyesült Királyság nyugati régiója |Egyesült Királyság déli régiója |
| Egyesült Arab Emírségek | Egyesült Arab Emírségek északi régiója | Egyesült Arab Emírségek középső régiója
| Egyesült Államok védelmi Minisztériuma |US DoD – keleti régió |US DoD – középső régió |
| Az USA kormányzata |US Gov Arizona |US Gov Texas |
| Az USA kormányzata |US Gov Iowa |US Gov Virginia |
| Az USA kormányzata |US Gov Virginia |US Gov Texas |

1\. táblázat – az Azure regionális párok leképezése

- Nyugat-India csak egy irányban párosítható. Nyugat-India másodlagos régiója Dél-India, Dél-India másodlagos régiója azonban Közép-India.
- A Dél-Brazília egyedi, mert a saját földrajzán kívüli régióval párosítva van. A Dél-Brazília déli középső régiója, az USA déli régiója. Dél-Közép-USA másodlagos régiója nem Dél-Brazília.
- US Gov Iowa másodlagos régiója US Gov Virginia.
- US Gov Virginia másodlagos régiója US Gov Texas.
- US Gov Texas "másodlagos régió US Gov Arizona.


Javasoljuk, hogy az üzletmenet-folytonossági vész-helyreállítást (BCDR) a regionális párokban konfigurálja az Azure elkülönítési és rendelkezésre állási házirendjeinek kihasználása érdekében. A több aktív régiót támogató alkalmazások esetén ajánlott mindkét régiót a régiós párokban használni, ahol lehetséges. Ez biztosítja az alkalmazások optimális rendelkezésre állását és a lehető legkevesebb helyreállítási időt vészhelyzet esetén. 

## <a name="an-example-of-paired-regions"></a>A párosított régiók példája
Az alábbi 2. ábra egy feltételezett alkalmazást mutat be, amely a regionális párokat használja a vész-helyreállításhoz. A zöld számok kiemelik a három Azure-szolgáltatás (Azure-beli számítási, tárolási és adatbázis) régiók közötti tevékenységeit, valamint azt, hogy ezek hogyan vannak konfigurálva a régiók közötti replikálásra. A párosított régiókban való üzembe helyezés egyedi előnyeit a narancssárga számok kiemelik.

![A párosított régió előnyeinek áttekintése](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

2\. ábra – feltételezett Azure regionális pár

## <a name="cross-region-activities"></a>Régiók közötti tevékenységek
A 2. ábrán említettek szerint.

![IaaS](./media/best-practices-availability-paired-regions/1Green.png) **Azure-beli számítás (IaaS)** – további számítási erőforrásokat kell kiépíteni, hogy az erőforrások egy másik régióban is elérhetők legyenek a katasztrófák során. További információ: az [Azure rugalmasságával kapcsolatos technikai útmutató](resiliency/resiliency-technical-guidance.md).

![Storage](./media/best-practices-availability-paired-regions/2Green.png) az **Azure Storage** -ban – ha felügyelt lemezeket használ, ismerkedjen meg a [régiók közötti biztonsági mentésekkel](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) a Azure Backup, és [replikálja a virtuális gépeket](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) az egyik régióból a másikba Azure site Recovery használatával. Ha Storage-fiókokat használ, a Geo-redundáns tárolás (GRS) alapértelmezés szerint be van állítva egy Azure Storage-fiók létrehozásakor. A GRS az adatai automatikusan replikálódnak az elsődleges régión belül, és háromszor a párosított régióban. További információ: [Azure Storage redundancia-beállítások](storage/common/storage-redundancy.md).

![Azure SQL](./media/best-practices-availability-paired-regions/3Green.png) **Azure SQL Database** – Azure SQL Database geo-replikálással a tranzakciók aszinkron replikációját a világ bármely régiójába beállíthatja. javasoljuk azonban, hogy ezeket az erőforrásokat egy párosított régióban telepítse a legtöbb vész-helyreállítási forgatókönyv esetében. További információ: [geo-replikáció a Azure SQL Databaseban](sql-database/sql-database-geo-replication-overview.md).

![Resource Manager](./media/best-practices-availability-paired-regions/4Green.png) **Azure Resource Manager** – az erőforrás-kezelő eleve az összetevők logikai elkülönítését biztosítja a régiók között. Ez azt jelenti, hogy az egyik régióban a logikai hibák valószínűleg kevésbé befolyásolják a másikat.

## <a name="benefits-of-paired-regions"></a>A párosított régiók előnyei
A 2. ábrán említettek szerint.  

![elkülönítés](./media/best-practices-availability-paired-regions/5Orange.png)
**fizikai elkülönítéssel** – ha lehetséges, az Azure a regionális párokban legalább 300 mérföldes elkülönítést tesz lehetővé az adatközpontok között, bár ez nem gyakorlatias vagy lehetséges az összes földrajzi helyen. A fizikai adatközpontok elkülönítése csökkenti a természeti katasztrófák, a zavargások, az áramkimaradások vagy a fizikai hálózatok kimaradásának valószínűségét egyszerre mindkét régióban. Az elkülönítést a földrajz (földrajzi méret, energia/hálózati infrastruktúra rendelkezésre állása, rendeletek stb.) korlátai határozzák meg.  

![replikáció](./media/best-practices-availability-paired-regions/6Orange.png)
**platform által biztosított replikáció** – egyes szolgáltatások, például a Geo-redundáns tárolás biztosítanak automatikus replikálást a párosított régió számára.

![Recovery](./media/best-practices-availability-paired-regions/7Orange.png)
**régió helyreállítási sorrendje** – széles kimaradás esetén az egyik régió helyreállítása minden pár közül rangsorolva van. A párosított régiókban üzembe helyezett alkalmazások garantálják, hogy a prioritással rendelkező régiók egyike legyen helyreállítva. Ha egy alkalmazás központi telepítése nem párosított régiók között történik, előfordulhat, hogy a helyreállítás késleltethető – a legrosszabb esetben a kiválasztott régiók az utolsó két helyre állíthatók vissza.

![frissítések](./media/best-practices-availability-paired-regions/8Orange.png)
**szekvenciális frissítések** – a tervezett Azure-rendszerfrissítéseket egymás után (egyidőben nem), az állásidő, a hibák és a logikai hibák hatásának csökkentése érdekében a rossz frissítés ritka eseménye okozhatja.

![az adat-](./media/best-practices-availability-paired-regions/9Orange.png)
az **adattárolásban** – egy régió ugyanabban a földrajzi helyen található, mint a párja (a Dél-Brazília kivételével), hogy kielégítse az adó-és bűnüldözési jogszabályokat az adattárolási követelmények teljesítése érdekében.
