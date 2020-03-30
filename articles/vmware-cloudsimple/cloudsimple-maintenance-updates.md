---
title: CloudSimple karbantartás és frissítések
titleSuffix: Azure VMware Solution by CloudSimple
description: A CloudSimple szolgáltatás folyamatának ismertetése az ütemezett karbantartáshoz és frissítésekhez
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 826fae1123b355a4143118b53ba649f0939acaf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025027"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple karbantartás és frissítések

A private cloud környezet úgy van kialakítva, hogy egyetlen meghibásodási pont nélkül.

* Az ESXi klaszterek vSphere High Availability (HA) függvényben vannak konfigurálva. A fürtök mérete úgy van méretezve, hogy legalább egy tartalék csomópont ot a rugalmasság.
* Redundáns elsődleges tároló által biztosított vSAN, amely előírja, legalább három csomópont, hogy védelmet nyújtson egyetlen hiba ellen. A vSAN beállítható úgy, hogy nagyobb rugalmasságot biztosítson a nagyobb fürtök számára.
* A vCenter, a PSC és az NSX-kezelő virtuális gépei RAID-10 tárolóval vannak konfigurálva a tárolási hibák elleni védelem érdekében. A virtuális gépek et a vSphere HA védi a csomópont/hálózati hibák ellen.
* Az ESXi házigazdák redundáns ventilátorokkal és hálózati adapterekkel rendelkeznek.
* A TOR és a gerinckapcsolók HA-párokban vannak konfigurálva, hogy rugalmasságot biztosítsanak.

A CloudSimple folyamatosan figyeli a következő virtuális gépeket az rendelkezésre állás és a rendelkezésre állás tekintetében, és rendelkezésre állási SL-eket biztosít:

* ESXi házigazdák
* vCenter
* Pbb
* NSX menedzser

A CloudSimple folyamatosan figyeli a hibákat:

* Merevlemezek
* Fizikai HÁLÓZATI ADAPTER-portok
* Kiszolgálók
* Ventilátor
* Power
* Switchek
* Portok váltása

Ha egy lemez vagy csomópont meghibásodik, egy új csomópont automatikusan hozzáadódik az érintett VMware-fürthöz, hogy azonnal állapotba hozza azt.

A CloudSimple biztonsági másolatot készít, karbantart és frissíti ezeket a VMware-elemeket a privát felhőkben:

* Esxi
* vCenter platformszolgáltatások
* Vezérlő
* vSAN között
* Nsx

## <a name="back-up-and-restore"></a>Biztonsági mentés és visszaállítás

A CloudSimple biztonsági mentése a következőket tartalmazza:

* A vCenter-, PSC- és DVS-szabályok éjszakai növekményes biztonsági mentései.
* vCenter natív API-k az alkalmazásréteg összetevőinek biztonsági mentésére.
* Automatikus biztonsági mentés a VMware felügyeleti szoftver frissítése vagy frissítése előtt.
* vCenter-adattitkosítás a forrásnál, mielőtt az adatok átvitele egy TLS1.2 titkosított csatornán keresztül az Azure-ba. Az adatok egy Azure blobban tárolódnak, ahol régiók között replikálódik.

A visszaállítást [támogatási kérelem](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)megnyitásával kérheti.

## <a name="maintenance"></a>Karbantartás

A CloudSimple többféle tervezett karbantartást végez.

### <a name="backendinternal-maintenance"></a>Háttérrendszer/belső karbantartás

Ez a karbantartás általában magában foglalja a fizikai eszközök újrakonfigurálását vagy a szoftverjavítások telepítését. Ez nem befolyásolja a szervizelt eszközök szokásos felhasználását. Mivel a redundáns hálózati adapterek minden fizikai állványra mennek, a normál hálózati forgalmat és a magánfelhő-műveleteket ez nem érinti. Előfordulhat, hogy csak akkor észleli a teljesítményre gyakorolt hatást, ha a szervezet a teljes redundáns sávszélességet szeretné használni a karbantartási időköz alatt.

### <a name="cloudsimple-portal-maintenance"></a>CloudSimple portál karbantartása

Néhány korlátozott szolgáltatás állásidő szükséges, ha a CloudSimple vezérlősík vagy infrastruktúra frissítése. Jelenleg a karbantartási időközök lehetnek olyan gyakoriak, mint havonta egyszer. A gyakoriság várhatóan csökkenni fog az idő múlásával. A CloudSimple értesítést küld a portál karbantartásáról, és a lehető legrövidebb időközt biztosítja. A portál karbantartási időköze alatt a következő szolgáltatások továbbra is minden hatással járnak:

* VMware felügyeleti sík és alkalmazások
* vCenter-hozzáférés
* Minden hálózat és tárolás
* Minden Azure-forgalom

### <a name="vmware-infrastructure-maintenance"></a>VMware infrastruktúra karbantartása

Esetenként szükség van a VMware-infrastruktúra konfigurációjának módosítására.  Jelenleg ezek az intervallumok 1-2 havonta fordulhatnak elő, de a gyakoriság idővel várhatóan csökkenni fog. Az ilyen típusú karbantartás általában a CloudSimple-szolgáltatások normál felhasználásának megszakítása nélkül végezhető el. A VMware karbantartási időköze alatt a következő szolgáltatások továbbra is minden hatással járnak:

* VMware felügyeleti sík és alkalmazások
* vCenter-hozzáférés
* Minden hálózat és tárolás
* Minden Azure-forgalom

## <a name="updates-and-upgrades"></a>Frissítések és frissítések

A CloudSimple felelős a VMware szoftverek (ESXi, vCenter, PSC és NSX) életciklus-kezeléséért a magánfelhőben.

A szoftverfrissítések a következők:

* **Javítások**. A VMware által kiadott biztonsági javítások vagy hibajavítások.
* **Frissítések**. A VMware veremösszetevő alverziómódosítása.
* **Frissítések**. A VMware veremösszetevő főverzióváltása.

A CloudSimple teszteli a kritikus biztonsági javítást, amint elérhetővé válik a VMware-ből. Az SLA-nként a CloudSimple egy héten belül kiadja a biztonsági javítást a privát felhőbeli környezetekbe.

A CloudSimple negyedéves karbantartási frissítéseket biztosít a VMware szoftverösszetevőihez. Amikor a VMware szoftver új főverziója elérhető, a CloudSimple együttműködik az ügyfelekkel, hogy összehangolja a megfelelő karbantartási időszakot a frissítéshez.

## <a name="next-steps"></a>További lépések

[Számítási feladatok virtuális gépeinek biztonsági és biztonsági tartaléka a Veeam használatával](backup-workloads-veeam.md)
