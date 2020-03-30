---
title: Bevezetési követelmények az SAP HANA az Azure-ban (nagy példányok) | Microsoft dokumentumok
description: Bevezetési követelmények az SAP HANA az Azure-ban (nagy példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 141a75a75a214ff4a6f136df7570d6e81f7f4e82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617000"
---
# <a name="onboarding-requirements"></a>Előkészítési követelmények

Ez a lista az SAP HANA Azure-on (nagyobb példányok) futtatásához vonatkozó követelményeket gyűjti.

**Microsoft Azure**

- Egy Azure-előfizetés, amely az Azure-beli SAP HANA-hoz (nagy példányok) kapcsolható.
- Microsoft Premier támogatási szerződés. Az SAP Azure-beli futtatásával kapcsolatos konkrét információkért tekintse meg az [SAP támogatási megjegyzését #2015553 – SAP a Microsoft Azure-ban: Támogatási előfeltételek.](https://launchpad.support.sap.com/#/notes/2015553) Ha a HANA nagy példány egységek 384 és több CPU-k, akkor is ki kell terjesztenie a Premier szintű támogatási szerződést, hogy az Azure Rapid Response.
- A HANA nagypéldány-skus, amire szüksége van, miután az SAP méretezési gyakorlat végrehajtása után szükséges.

**Hálózati kapcsolat**

- ExpressRoute a helyszíni Azure között: A helyszíni adatközpont azure-hoz való csatlakoztatásához rendeljen legalább 1 Gbit/s-os kapcsolatot az isp-től. A HANA large Instance egységek és az Azure közötti kapcsolat ExpressRoute-technológiát is használ. Ez az ExpressRoute-kapcsolat a HANA nagy példány egységek és az Azure között a HANA nagy példány egységek ára, beleértve az összes adat-bejövő és kimenő díjak at adott ExpressRoute-kapcsolat. Ezért Ön, mint ügyfél, nem ütközik további költségeket a helyszíni és az Azure közötti ExpressRoute-kapcsolaton kívül.

**Operációs rendszer**

- Licencek a SUSE Linux Enterprise Server 12 SAP-alkalmazásokhoz.

   > [!NOTE] 
   > A Microsoft által szállított operációs rendszer nincs regisztrálva a SUSE-nál. Nem kapcsolódik előfizetés-kezelő eszköz példányhoz.

- Az Azure-ban virtuális gépen üzembe helyezett SUSE Linux-előfizetés-kezelő eszköz. Ez az eszköz lehetővé teszi az SAP HANA az Azure-ban (nagy példányok) a SUSE által regisztrálott és megfelelően frissített. (A HANA nagy példány adatközpontjában nincs internet-hozzáférés.) 
- Licencek a Red Hat Enterprise Linux 6.7 vagy 7.x SAP HANA.

   > [!NOTE]
   > A Microsoft által szállított operációs rendszer nincs regisztrálva a Red Hat rendszerben. Nem csatlakozik red hat előfizetés-kezelői példányhoz.

- A Red Hat Subscription Manager az Azure-ban virtuális gépen van telepítve. A Red Hat-előfizetés-kezelő lehetővé teszi, hogy az SAP HANA az Azure-ban (nagy példányok) regisztrálható, és megfelelően a Red Hat által ilegezve. (Nincs közvetlen internet-hozzáférés a bérlőn belül telepített az Azure nagy példány bélyegző.)
- Az SAP megköveteli, hogy a Linux-szolgáltatóval is támogatási szerződést kössön. Ezt a követelményt nem távolítja el a HANA nagy példány megoldása vagy az a tény, hogy Linuxot futtat az Azure-ban. A Linux Azure-katalógus lemezképeivel ellentétben a szolgáltatási díj *nem* szerepel a HANA nagy példány megoldásajánlatában. Az Ön felelőssége, hogy teljesítse az SAP követelményeit a Linux forgalmazóval kötött támogatási szerződések tekintetében. 
   - SUSE Linux esetén keresse meg a támogatási szerződések követelményeit az [SAP Note #1984787 - SUSE Linux Enterprise Server 12: Telepítési megjegyzések](https://launchpad.support.sap.com/#/notes/1984787) és az SAP Note #1056161 - [SUSE prioritás támogatása az SAP alkalmazásokhoz](https://launchpad.support.sap.com/#/notes/1056161).
   - A Red Hat Linux esetében a megfelelő előfizetési szintekkel kell rendelkeznie, amelyek tartalmazzák a HANA Large Instance operációs rendszereinek támogatását és szolgáltatásfrissítéseit. A Red Hat a Red Hat Enterprise Linux előfizetést ajánlja az SAP-megoldáshoz. Lásd https://access.redhat.com/solutions/3082481a hivatkozást. 

A különböző SAP HANA-verziók különböző Linux-verziókkal rendelkező támogatási mátrixáról az [SAP Note #2235581.](https://launchpad.support.sap.com/#/notes/2235581)

Az operációs rendszer kompatibilitási mátrixát és a HLI firmware/illesztőprogram verzióit lásd [az OS Upgrade for HLI](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> Típus II egységek csak az SLES 12 SP2 operációs rendszer verziója támogatott ezen a ponton. 


**Adatbázis**

- Licencek és szoftvertelepítési összetevők az SAP HANA (platform vagy vállalati kiadás).

**Alkalmazások**

- Licencek és szoftvertelepítési összetevők minden SAP-alkalmazások, amelyek csatlakoznak az SAP HANA és a kapcsolódó SAP támogatási szerződések.
- Licencek és szoftvertelepítési összetevők az SAP HANA-val azure-beli (nagy példányok) környezetekben és a kapcsolódó támogatási szerződésekben használt nem SAP-alkalmazásokhoz.

**Készségek**

- Az Azure IaaS és összetevőivel kapcsolatos tapasztalatok és ismeretek.
- Az SAP-számítási feladatok Azure-beli üzembe helyezésének és ismerete.
- SAP HANA telepítés minősített személyes.
- SAP-tervezői képességek tervezése magas rendelkezésre állású és vész-helyreállítási körül SAP HANA.

**SAP**

- Elvárás az, hogy Ön egy SAP-ügyfél, és támogatási szerződést kötött az SAP-val.
- Különösen a HANA nagypéldány-skus II típusú osztályának implementációihoz konzultáljon az SAP-val az SAP HANA verzióiról és a nagy méretű skálázású hardver ek végleges konfigurációiról.

**További lépések**
- SAP [HANA (nagy példányok) architektúrájának ajánlása az Azure-ban](hana-architecture.md)