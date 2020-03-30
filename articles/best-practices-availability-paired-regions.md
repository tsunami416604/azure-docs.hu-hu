---
title: Az üzletmenet folytonosságának biztosítása & vészhelyreállításaz Azure párosított régiók használatával
description: Az alkalmazások rugalmasságának biztosítása az Azure regionális párosításával
author: jpconnock
manager: angrobe
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jeconnoc
ms.openlocfilehash: 778943dad9a04632797d5d9165b6f1f9a3eb9850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248254"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Üzletmenet-folytonosság és vészhelyreállítás (BCDR): Azure párosított régiók

## <a name="what-are-paired-regions"></a>Mik azok a párosított régiók?

Az Azure-régió egy késésáltal meghatározott peremterületen üzembe helyezett és egy dedikált, alacsony késésű hálózaton keresztül csatlakoztatott adatközpontok készletéből áll.  Ez biztosítja, hogy az Azure-régión belüli Azure-szolgáltatások a lehető legjobb teljesítményt és biztonságot nyújtsák.  

Az Azure-földrajzi meghatározza a világ egy olyan terület, amely legalább egy Azure-régióban. A földrajzi területek egy különálló, általában két vagy több régiót tartalmazó piacot határoznak meg, amely megőrzi az adatok tárolási és megfelelőségi határait.  Az Azure globális infrastruktúrájáról itt talál további [információt.](https://azure.microsoft.com/global-infrastructure/regions/)

A regionális pár két régióból áll ugyanazon a földrajzi területen belül. Az Azure szerializálja a platformfrissítéseket (tervezett karbantartást) a regionális párok között, biztosítva, hogy minden párban egyszerre csak egy régió frissül. Ha egy kimaradás több régiót is érint, minden pár legalább egy régiója lesz rangsorolva a helyreállításhoz.

![AzureGeography (AzureGeography)](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Egyes Azure-szolgáltatások további kihasználása párosított régiók az üzletmenet folytonosságának biztosítása és az adatvesztés elleni védelem érdekében.  Az Azure számos [tárolási megoldást](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) kínál, amelyek kihasználják a párosított régiók előnyeit az adatok rendelkezésre állásának biztosítása érdekében. Például [az Azure georedundáns tárolása](./storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) automatikusan replikálja az adatokat egy másodlagos régióba, biztosítva, hogy az adatok még abban az esetben is tartósak legyenek, ha az elsődleges régió nem helyreállítható. 

Vegye figyelembe, hogy nem minden Azure-szolgáltatás automatikusan replikálja az adatokat, és nem az összes Azure-szolgáltatások automatikusan visszaad egy sikertelen régióa kontrópár.  Ilyen esetekben a helyreállítást és a replikációt az ügyfélnek kell konfigurálnia.

## <a name="can-i-select-my-regional-pairs"></a>Kiválaszthatom a területi párjaimat?

Nem. Egyes Azure-szolgáltatások regionális párokra támaszkodnak, például az Azure [redundáns tárhelyére.](./storage/common/storage-redundancy.md) Ezek a szolgáltatások nem teszik lehetővé új regionális párosítások létrehozását.  Hasonlóképpen, mivel az Azure szabályozza a regionális párok tervezett karbantartását és helyreállítási rangsorolását, nem határozhatja meg saját regionális párjait, hogy kihasználja ezeket a szolgáltatásokat. Azonban létrehozhatja saját vész-helyreállítási megoldását, ha szolgáltatásokat hoz létre tetszőleges számú régióban, és kihasználhatja az Azure-szolgáltatásokat azok párosításához. 

Például használhatja az Azure-szolgáltatások, például [az AzCopy](./storage/common/storage-use-azcopy-v10.md) ütemezheti az adatok biztonsági mentések egy storage-fiók egy másik régióban.  Az Azure DNS és az [Azure Traffic Manager](./networking/disaster-recovery-dns-traffic-manager.md)használatával az ügyfelek rugalmas architektúrát tervezhetnek az alkalmazásaikhoz, amely túléli az elsődleges régió elvesztését.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>Csak a regionális párjaimon belüli szolgáltatások használatára vagyok korlátozva?

Nem. Bár egy adott Azure-szolgáltatás támaszkodhat egy regionális pár, a többi szolgáltatás üzemeltetheti bármely régióban, amely megfelel az üzleti igényeknek.  Az Azure GRS-tárolási megoldás párosíthatja az adatokat a Canada Central egy peer Kelet-Kanadában, míg az USA keleti részén található számítási erőforrások használata.  

## <a name="must-i-use-azure-regional-pairs"></a>Használjam az Azure regionális párjait?

Nem. Az ügyfelek kihasználva azure-szolgáltatások megtervezése rugalmas szolgáltatás nélkül támaszkodva az Azure regionális párok.  Azt javasoljuk azonban, hogy konfigurálja az üzletmenet-folytonossági vészhelyreállítást (BCDR) a regionális párok között az [elkülönítés](./security/fundamentals/isolation-choices.md) előnyeinek kihasználása és a rendelkezésre állás javítása [érdekében.](./availability-zones/az-overview.md) A több aktív régiót is támogató alkalmazások esetében, ahol lehetséges, a régiópárok mindkét tagjának használatát javasoljuk. Ez biztosítja az alkalmazások optimális rendelkezésre állását, és katasztrófa esetén minimálisra csökkenti a helyreállítási időt. Amikor csak lehetséges, tervezze meg az alkalmazást a [maximális rugalmasság](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview) és a katasztrófa utáni helyreállítás megkönnyítése [érdekében.](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)

## <a name="azure-regional-pairs"></a>Azure regionális párok

| Földrajzi hely | A regionális pár | B regionális pár  |
|:--- |:--- |:--- |
| Ázsia és csendes-óceáni térség |Kelet-Ázsia (Hongkong) | Délkelet-Ázsia (Szingapúr) |
| Ausztrália |Kelet-Ausztrália |Délkelet-Ausztrália |
| Ausztrália |Ausztrália középső régiója |Ausztrália 2. középső régiója |
| Brazília |Dél-Brazília |USA déli középső régiója |
| Kanada |Közép-Kanada |Kelet-Kanada |
| Kína |Észak-Kína |Kelet-Kína|
| Kína |Kína Észak 2 |Kína Keleti 2|
| Európa |Észak-Európa (Írország) |Nyugat-Európa (Hollandia) |
| Franciaország |Közép-Franciaország|Dél-Franciaország|
| Németország |Közép-Németország |Északkelet-Németország |
| India |Közép-India |Dél-India |
| India |Nyugat-India |Dél-India |
| Japán |Kelet-Japán |Nyugat-Japán |
| Dél-Korea |Dél-Korea középső régiója |Dél-Korea déli régiója |
| Észak-Amerika |USA keleti régiója |USA nyugati régiója |
| Észak-Amerika |USA 2. keleti régiója |USA középső régiója |
| Észak-Amerika |USA északi középső régiója |USA déli középső régiója |
| Észak-Amerika |USA nyugati régiója, 2. |USA nyugati középső régiója |
| Norvégia | Norvégia Kelet | Norvégia Nyugati |
| Dél-afrikai Köztársaság | Dél-Afrika Észak-Afrika |Dél-Afrika Nyugati |
| Svájc | Észak-Svájc |Svájc Nyugati |
| Egyesült Királyság |Az Egyesült Királyság nyugati régiója |Az Egyesült Királyság déli régiója |
| Egyesült Arab Emírségek | Egyesült Arab Emírségek északi | Egyesült Arab Emírségek központi
| Egyesült Államok Védelmi Minisztériuma |US DoD – Kelet |US DoD – Középső régió |
| Egyesült Államok – Államigazgatás |USA-beli államigazgatás – Arizona |USA-beli államigazgatás – Texas |
| Egyesült Államok – Államigazgatás |US Gov Iowa |USA-beli államigazgatás – Virginia |
| Egyesült Államok – Államigazgatás |USA-beli államigazgatás – Virginia |USA-beli államigazgatás – Texas |

> [!Important]
> - Nyugat-India csak egy irányban van párosítva. Nyugat-India másodlagos régiója Dél-India, de Dél-India másodlagos régiója Közép-India.
> - Brazília Dél egyedülálló, mert párosítva van egy régiókívül a földrajz. Brazília Déli másodlagos régiója az USA déli középső régiója. Dél-Közép-USA másodlagos régió nem Brazília délen.


## <a name="an-example-of-paired-regions"></a>Példa a párosított régiókra
Az alábbi képen egy hipotetikus alkalmazás, amely a regionális pár a vész-helyreállítási. A zöld számok három Azure-szolgáltatás (Azure-beli számítási, tárolási és adatbázis) régióközi tevékenységeit emelik ki, és azt, hogy hogyan vannak konfigurálva a régiók közötti replikálásra. A párosított régiók közötti üzembe helyezés egyedülálló előnyeit a narancssárga számok emelik ki.

![A párosított régió előnyeinek áttekintése](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

2. ábra – Hipotetikus Azure regionális páros

## <a name="cross-region-activities"></a>Régiókközötti tevékenységek
A 2.

1. **Azure Compute (IaaS)** – További számítási erőforrásokat kell előre kiépítenie annak érdekében, hogy az erőforrások egy másik régióban álljanak rendelkezésre egy katasztrófa során. További információ: [Azure rugalmasságtechnikai útmutató.](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md) 

2. **Azure Storage** – Ha felügyelt lemezeket használ, ismerje meg a [régiók közötti biztonsági mentéseket](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) az Azure Backup segítségével, és [replikálja a virtuális gépeket](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) az egyik régióból a másikba az Azure Site Recovery segítségével. Ha tárfiókokat használ, akkor a georedundáns tárolás (GRS) alapértelmezés szerint konfigurálva van, amikor egy Azure Storage-fiók jön létre. A GRS segítségével az adatok automatikusan replikálódik háromszor az elsődleges régióban, és háromszor a párosított régióban. További információ: [Azure Storage Redundancy Options](storage/common/storage-redundancy.md).

3. **Azure SQL Database** – Az Azure SQL Database georeplikációjával avilág bármely régiójába konfigurálhatja a tranzakciók aszinkron replikációját; azonban azt javasoljuk, hogy ezeket az erőforrásokat egy párosított régióban a legtöbb vész-helyreállítási forgatókönyvek. További információ: [Geo-replication in Azure SQL Database](sql-database/sql-database-geo-replication-overview.md).

4. **Azure Resource Manager** – A Resource Manager eredendően biztosítja az összetevők régiók közötti logikai elkülönítését. Ez azt jelenti, logikai hibák az egyik régióban kevésbé valószínű, hogy hatással van egy másik.

## <a name="benefits-of-paired-regions"></a>A párosított régiók előnyei

5. **Fizikai elkülönítés** – Ha lehetséges, az Azure előnyben részesíti legalább 300 mérföldre az adatközpontok közötti elkülönítés egy regionális pár, bár ez nem praktikus vagy nem lehetséges minden földrajzi területen. A fizikai adatközpontok elkülönítése csökkenti a természeti katasztrófák, a polgári zavargások, az áramkimaradások vagy a fizikai hálózati kimaradások valószínűségét, amelyek egyszerre érintik mindkét régiót. Az elkülönítésre a földrajzi korlátok vonatkoznak (földrajzi méret, a hálózati infrastruktúra rendelkezésre állása, szabályozások stb.).  

6. **Platform által biztosított replikáció** – Egyes szolgáltatások, például a georedundáns tárolás, automatikus replikációt biztosítanak a párosított régióba.

7. **Régió helyreállítási rendelés** – Abban az esetben, ha egy széles körű kimaradás, helyreállítása egy régió prioritást élvez minden pár. A párosított régiókban üzembe helyezett alkalmazások esetében az egyik régió garantáltan előnyben részesül a helyreállításkor. Ha egy alkalmazás olyan régiók között van telepítve, amelyek nincsenek párosítva, a helyreállítás késhet – a legrosszabb esetben a kiválasztott régiók lehetnek az utolsó két helyreállítandó régiók.

8. **Szekvenciális frissítések** – A tervezett Azure-rendszerfrissítések egymás után (nem egyidejűleg) kerülnek a párosított régiókba, hogy minimálisra csökkentsék az állásidőt, a hibák hatását és a logikai hibákat a rossz frissítés ritka esetén.

9. **Adatrezidensség** – A régió a párjával azonos földrajzi területen található (Dél-Brazília kivételével), hogy megfeleljen az adózási és bűnüldözési joghatósági célokra vonatkozó adattárolási követelményeknek.
