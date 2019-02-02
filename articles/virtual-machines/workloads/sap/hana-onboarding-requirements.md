---
title: Az SAP Hana az Azure-ban (nagyméretű példányok) az előkészítés követelményeinek |} A Microsoft Docs
description: Bevezetési követelmények az SAP Hana az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8cfcf9c2f008abb571347e32f2be190dc51901e3
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55563830"
---
# <a name="onboarding-requirements"></a>Bevezetési követelmények

Ebben a listában több, a SAP HANA futtatásához az Azure-ban (nagyobb példányok).

**Microsoft Azure**

- Azure-előfizetést is társítható, az Azure-ban (nagyméretű példányok) SAP Hana-hoz.
- A Microsoft Premier szintű támogatási szerződés. Azure-ban futó SAP kapcsolatos információkért lásd: [SAP támogatási Megjegyzés #2015553 – SAP a Microsoft Azure-ban: Támogatás előfeltételei](https://launchpad.support.sap.com/#/notes/2015553). Ha nagyméretű HANA-példány egységek 384-et és a további processzorokat használja, szükség is kiterjesztheti a Premier szintű támogatási szerződés Azure Rapid Response tartalmazza.
- A HANA nagyméretű példány SKU-k az SAP egy méretezési gyakorlat végrehajtása után kell hívja.

**Hálózati kapcsolat**

- Az Azure-bA helyszíni között ExpressRoute: Csatlakozás a helyi adatközpontban az Azure-ba, ügyeljen arra, hogy az internetszolgáltató által biztosított legalább 1-GB/s kapcsolat order. Nagyméretű HANA-példány egységek és Azure közötti kapcsolatot, valamint az ExpressRoute-technológiát használ. Ez a nagyméretű HANA-példány egységek és az Azure között ExpressRoute-kapcsolat a HANA nagyméretű példányok egységek, az összes adat bejövő és kimenő vonatkozó díjakat az adott ExpressRoute-kapcsolatcsoport a díja tartalmazza. Ezért ügyfélként nem vehető észre további költség mellett a helyszíni és az Azure között ExpressRoute hivatkozásra.

**Operációs rendszer**

- SUSE Linux Enterprise Server 12 SAP-alkalmazások a licencet.

   > [!NOTE] 
   > Az operációs rendszer, amelyeket a Microsoft SUSE nincs regisztrálva. Egy előfizetés felügyeleti eszköz példány nincs csatlakoztatva.

- SUSE Linux előfizetés felügyeleti eszköz üzembe helyezett Azure-beli virtuális gépen. Ez az eszköz lehetővé teszi a SAP Hana az Azure-ban regisztrált és megfelelő frissítette a SUSE (nagyméretű példányok). (Nincs nincs internet-hozzáférés a nagyméretű HANA-példány adatközponton belül nem.) 
- Red Hat Enterprise Linux 6.7 vagy az SAP Hana 7.x licencet.

   > [!NOTE]
   > Az operációs rendszer, amelyeket a Microsoft a Red Hattel nincs regisztrálva. A Red Hat előfizetés-kezelő példány nincs csatlakoztatva.

- Red Hat előfizetés-kezelő üzembe helyezett Azure-beli virtuális gépen. A Red Hat előfizetés-kezelő lehetővé teszi a SAP Hana az Azure-ban regisztrált és megfelelő frissítette a Red Hat (nagyméretű példányok). (Nincs nincs közvetlen internet-hozzáférést a telepített Azure nagyméretű szolgáltatáspéldányban a bérlőn belül nem.)
- SAP használatához szükséges támogatási szerződés, valamint a Linux-szolgáltatónál. Ez a követelmény a nagyméretű HANA-példányt, vagy az a tény, hogy a Linux az Azure-ban fut, a megoldás által nem törlődnek. Ellentétben az egyes Linux rendszerű Azure-katalógus képek a szolgáltatási díj nem *nem* nagyméretű HANA-példányt, a megoldás ajánlatban foglalt. A feladata követelményeit SAP támogatási szerződéssel kapcsolatos a Linux terjesztő teljesítéséhez. 
   - SUSE Linux esetén keresse meg a támogatási szerződés előírásainak [SAP Megjegyzés #1984787 – SUSE Linux Enterprise Server 12: Telepítési jegyzetek](https://launchpad.support.sap.com/#/notes/1984787) és [SAP Megjegyzés #1056161 - SUSE elsőbbségi támogatást az SAP-alkalmazások](https://launchpad.support.sap.com/#/notes/1056161).
   - Red Hat Linux rendszeren kell a megfelelő előfizetés szintű támogatást tartalmaznak, és operációs rendszerét, nagyméretű HANA-példányt, a szolgáltatást. Red Hat az SAP-megoldás a Red Hat Enterprise Linux-előfizetés használatát javasolja. Tekintse meg https://access.redhat.com/solutions/3082481. 

A különböző Linux-verziók a különböző SAP HANA-verziók támogatási mátrixa, lásd: [SAP Megjegyzés #2235581](https://launchpad.support.sap.com/#/notes/2235581).

Tekintse meg a kompatibilitási mátrix az operációs rendszer és a belső vezérlőprogram és illesztőprogram-verziókat HLI [HLI az operációs rendszer frissítése](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> II. típusú egységek csak a SLES 12 SP2 operációsrendszer-verzió támogatott ezen a ponton. 


**Adatbázis**

- Licencek és az SAP Hana (platform vagy enterprise edition) telepítési szoftverösszetevőket.

**Alkalmazások**

- Licencek és olyan SAP-alkalmazások, amelyhez csatlakozni az SAP HANA és a kapcsolódó SAP telepítési szoftverösszetevőket támogatja a szerződéseket.
- A licencek és az SAP alkalmazások telepítése szoftverösszetevőket az SAP HANA Azure-ban (nagyméretű példányok) környezetekben használt, és támogatási szerződéssel kapcsolatos.

**Képességek**

- A tapasztalatokat és ismereteket Azure IaaS és összetevői.
- A tapasztalatokat és ismereteket, amelyekkel telepítheti az SAP számítási feladatok Azure-ban.
- SAP HANA telepítése certified személyes.
- SAP mérnök képességek körül SAP HANA magas rendelkezésre állás és vészhelyreállítás helyreállítási tervezéséhez.

**SAP**

- Az elvárás, hogy már az SAP-ügyfelek és a támogató SAP-szerződés.
- A HANA nagyméretű példány termékváltozatok II. típusú osztályát megvalósítását, különösen a tekintse meg az SAP az SAP HANA és a végleges konfigurációk nagy méretű vertikális felskálázás hardveren verzióit.

**Következő lépések**
- Tekintse meg [Azure-beli SAP HANA (nagyméretű példányok) architektúra](hana-architecture.md)