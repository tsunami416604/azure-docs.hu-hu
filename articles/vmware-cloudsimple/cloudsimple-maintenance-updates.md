---
title: Azure VMware-megoldás által CloudSimple - CloudSimple karbantartás és frissítések
description: Azt ismerteti, hogyan CloudSimple szolgáltatás az ütemezett karbantartás és frissítések
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4dde358f10e9ac5054297ff68a0971404c0dc135
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160245"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple karbantartás és frissítések

A magánfelhő-környezethez van úgy tervezték, hogy nem rendszerkritikus meghibásodási pontot:

* ESXi-fürtök úgy vannak konfigurálva, a vSphere magas rendelkezésre állás. A fürtök mérete rugalmasság legalább egy tartalék csomóponttal rendelkezik.
* Redundáns elsődleges tárolót biztosít a vsan-hoz, amelyhez szükség van legalább három csomópontok meghibásodása elleni védelem biztosításához. vSAN beállítható úgy, hogy nagyobb rugalmasságot biztosítanak a nagyobb fürtök esetén.
* vCenter PSC és NSX Manager virtuális gépek tárolási meghibásodása elleni védelem érdekében a RAID 10-es storage házirend van beállítva. A virtuális gépek vSphere magas rendelkezésre ÁLLÁS által védett csomópont és a hálózati hibák ellen.
* ESXi-gazdagépek redundáns rajongói és a hálózati adapterrel rendelkezik.
* Magas rendelkezésre ÁLLÁSÚ párok biztosítható a rugalmasság fióktípusa és a TOR-kapcsolók konfigurálhatók.

CloudSimple folyamatosan figyeli a következő virtuális gépek rendelkezésre állását és a rendelkezésre állás, és rendelkezésre állási SLA-kat biztosít:

* ESXi-gazdagépek
* vCenter
* PSC
* NSX Manager

CloudSimple is a következő hibák folyamatosan figyeli:

* Merevlemezek
* Fizikai NIC-portok
* Kiszolgálók
* Ventilátor
* Energiagazdálkodási
* Kapcsolók
* Kapcsoló portjaihoz

Ha a lemez vagy csomópont meghibásodik, egy új csomópont automatikusan hozzáadódik az érintett VMware-fürt – az életre vissza egészségügyi azonnal.

CloudSimple biztonsági mentését, tart fenn, és ezeket a VMware az elemeket a privát felhők a frissíti:

* ESXi
* vCenter Adatplatform-szolgáltatásaival
* Vezérlő
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Biztonsági mentése és visszaállítása

CloudSimple biztonsági mentésének:

* A vCenter, PSC és DVS éjszakai növekményes biztonsági szabályokat.
* VCenter használatát natív API-k biztonsági összetevők az alkalmazásréteg szintjén mentéséhez.
* Automatikus biztonsági mentés előtt minden frissítést, illetve a VMware-felügyeleti szoftver.
* Adattitkosítás a forrásnál, a vCenter, mielőtt adatátviteli TLS1.2 titkosított csatornán keresztül az Azure-bA. Az adatok Azure-blobba régiók közötti replikálására hol tárolja.

Nyissa meg a visszaállítás kérheti egy [támogatási kérelem](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Karbantartás

Tervezett karbantartás számos különböző típusú CloudSimple hajtja végre.

### <a name="backendinternal-maintenance"></a>Háttér vagy belső karbantartás

Ez a karbantartás általában magában foglalja a fizikai eszközök újrakonfigurálása vagy a szoftverjavítások telepítésével. Ez nincs hatással a normál használatalapú szervizelés alatt álló eszközök. Minden egyes fizikai rack fog redundáns hálózati adapterrel a normál hálózati forgalom és a privát felhőbeli műveletek nem befolyásolja. A teljesítményre gyakorolt hatás előfordulhat, hogy figyelje meg, csak akkor, ha a szervezet vár a karbantartási időszakban a teljes redundáns sávszélesség használatára.

### <a name="cloudsimple-portal-maintenance"></a>CloudSimple portál karbantartás

Néhány korlátozott szolgáltatás állásidőre szükség, a vezérlősík CloudSimple vagy infrastruktúra frissítésekor. Karbantartási időszakok jelenleg is gyakori, havonta egyszer lehet. A gyakoriság elutasítása idővel várható. CloudSimple portál karbantartási értesítés biztosít, és a lehető legrövidebb időköz tartja. A portál karbantartási időszakban a következő szolgáltatások továbbra is működnek nem érinti:

* VMware-felügyeleti sík és az alkalmazások
* vCenter-hozzáférés
* Az összes hálózati és tárolási
* Minden Azure-forgalmat

### <a name="vmware-infrastructure-maintenance"></a>VMware-infrastruktúra karbantartása

Alkalmanként legyen szükséges, módosíthatja a VMware-infrastruktúra konfigurálása.  Jelenleg intervallumok fordulhat elő, 1 – 2 havonta, de idővel elutasítása várt gyakorisága. Az ilyen típusú karbantartási általában elvégezhető a CloudSimple szolgáltatások normál használatalapú megszakítása nélkül. A VMware karbantartási időszakban a következő szolgáltatások továbbra is működnek nem érinti:

* VMware-felügyeleti sík és az alkalmazások
* vCenter-hozzáférés
* Az összes hálózati és tárolási
* Minden Azure-forgalmat

## <a name="updates-and-upgrades"></a>Frissítések és verziófrissítések

CloudSimple felelős VMware szoftvereket (ESXi, vCenter, PSC és NSX) a magánfelhő életciklus-felügyelete.

Szoftverfrissítések a következők:

* **Javítások**. Biztonsági javítások vagy a VMware által kiadott hibajavításokat tartalmaz.
* **Frissítések**. A VMware-verem összetevő alverzió megváltozott.
* **Frissítések**. A VMware-verem összetevő főverzió megváltozott.

CloudSimple teszteli a kritikus fontosságú biztonsági javítást, amint a VMware-ből elérhetővé válik. Egy SLA-t CloudSimple vezet be a biztonsági javítási szintnek a magánfelhő-környezetekben egy héten belül.

CloudSimple negyedéves karbantartási frissíti a VMware biztosít. Ha VMware szoftvereket fő új verziója érhető el, CloudSimple működik, az ügyfelek számára, hogy koordinálja a frissítésre alkalmas karbantartási időszak.

## <a name="next-steps"></a>További lépések

[Veeam használatával munkaterhelési virtuális gépek biztonsági mentése](https://docs.azure.cloudsimple.com/backup-workloads-veeam/).