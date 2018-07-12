---
title: Rendelkezésre állás az SAP HANA az Azure virtuális gépek – áttekintés |} A Microsoft Docs
description: Az Azure natív virtuális gépein az SAP HANA műveleteket ismerteti.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7049a4b5159687ab928cda7ddc6b1a35959529ac
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972107"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>SAP HANA magas rendelkezésre állás az Azure-beli virtuális gépek

Számos Azure-képességek segítségével alapvető fontosságú adatbázisok, például az Azure virtuális gépeken futó SAP HANA üzembe helyezése. Ez a cikk útmutatást nyújt útmutatást az Azure-beli virtuális gépeken futó SAP HANA-példányok rendelkezésre állás. A cikk azt ismerteti, számos különböző helyzetben alkalmazható valósítható meg az Azure-beli SAP HANA rendelkezésre állásának növelése az Azure-infrastruktúra használatával. 

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy ismeri az infrastruktúra-szolgáltatás (IaaS) alapjait az Azure-ban, mint például: 

- Hogyan helyezhet üzembe virtuális gépeket vagy virtuális hálózatok az Azure Portalon vagy a Powershellen keresztül.
- Használja az Azure többplatformos parancssori felület (Azure CLI), valamint sablonok a JavaScript Object Notation (JSON) lehetőséget.

Ez a cikk azt is feltételezi, hogy Ön ismeri a SAP HANA-példányok telepítését, és felügyeletéről és SAP HANA-példányok operációs. Különösen fontos tisztában lennie azzal, hogy a telepítő és a HANA-rendszerreplikálást működését. Ez magában foglalja a feladatokat, mint a biztonsági mentés és visszaállítás az SAP HANA-adatbázisra.

Ezek a cikkek az Azure-beli SAP HANA használata jó áttekintést nyújtanak:

- [Egypéldányos SAP HANA az Azure virtuális gépek manuális telepítése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Azure virtuális gépeken futó SAP HANA rendszerreplikáció beállítása](sap-hana-high-availability.md)
- [Azure virtuális gépek biztonsági mentéséhez az SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Azt is célszerű, hogy ezek a cikkek SAP HANA-ról ismernie kell a:

- [Magas rendelkezésre állás az SAP Hana-hoz](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Gyakori kérdések: Magas rendelkezésre állás az SAP Hana-hoz](https://archive.sap.com/documents/docs/DOC-66702)
- [Hajtsa végre a rendszer replikációs az SAP Hana-hoz](https://archive.sap.com/documents/docs/DOC-47702)
- [Az SAP HANA 2.0 Szervizcsomagok 01 Mi az új: magas rendelkezésre állás](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [SAP HANA rendszerreplikáció hálózati javaslatok](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [Az SAP HANA-rendszerreplikálást](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA szolgáltatás automatikus újraindítás](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [SAP HANA rendszerreplikáció konfigurálása](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Nem ismeri az Azure-ban, a virtuális gépek üzembe helyezéséhez a folyamatban a rendelkezésre állási architektúra az Azure-ban megadhat, előtt javasoljuk, hogy olvasási [Azure-beli Windows virtuális gépek rendelkezésre állásának kezelése](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).

## <a name="service-level-agreements-for-azure-components"></a>Azure-összetevőket a szolgáltatói szerződések

Az Azure különböző rendelkezésre állási SLA-k különböző összetevők, például hálózati, tárolási és virtuális gépeket tartalmaz. Az összes SLA-k szerepelnek. További információkért lásd: [a Microsoft Azure szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/). 

[Virtuális gépekre vonatkozó SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) két különböző SLA-k, a két különböző konfigurációkat ismerteti:

- Egyetlen virtuális gép által használt [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) az operációsrendszer-lemez és az összes adatlemezt. Ez a beállítás egy havi 99,9 %-os üzemidőt biztosít.
- Több (legalább két) virtuális gépet, amely vannak rendszerezve egy [Azure rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Ezt a beállítást egy havi 99,95 %-os üzemidőt biztosít.

A rendelkezésre állás terén, szemben az Azure-összetevők által biztosított SLA-k mérjük. Ezután válassza ki a forgatókönyvek a SAP HANA, a szükséges szintű rendelkezésre állást érhet el.

## <a name="next-steps"></a>További lépések

- Ismerje meg [SAP HANA rendelkezésre állási egy Azure-régión belüli](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region).
- Ismerje meg [SAP HANA rendelkezésre állása az Azure-régiók](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions). 















  


