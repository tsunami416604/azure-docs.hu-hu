---
title: "SAP HANA elérhetőségét a Azure virtuális gépek – áttekintés |} Microsoft Docs"
description: "SAP HANA műveleteinek Azure natív virtuális gépeken"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: msjuergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9b22f89750234a4715d2b7fd2df6ad8740b1d085
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-high-availability-guide-for-azure-virtual-machines"></a>SAP HANA magas rendelkezésre állású útmutató az Azure virtuális gépek
Azure biztosít számos lehetőséget biztosít, amely lehetővé teszi, hogy telepíteni a kritikus fontosságú adatbázisokhoz például SAP HANA az Azure virtuális gépeken. Ez a dokumentum útmutatást nyújt a hogyan SAP HANA-példányok futó Azure virtuális gépek rendelkezésre állásának eléréséhez. A dokumentum az Azure-infrastruktúra magasabb rendelkezésre állását biztosítja az Azure-on SAP HANA végrehajtott számos forgatókönyv leírása olvasható. 

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató feltételezi, hogy Ön ismeri a ilyen infrastruktúrában található, mint a szolgáltatási (IaaS) alapjait, az Azure platformon: 

- Ügyfélszoftverek központi telepítése a virtuális gépek vagy virtuális hálózatok az Azure portálon vagy a PowerShell segítségével.
- Az Azure platformfüggetlen parancssori felület (CLI), többek között a sablonok JavaScript Object Notation (JSON) lehetőséget.

Ez az útmutató emellett feltételezi, hogy Ön ismeri a SAP HANA-példányok telepítése és felügyelete, és működési SAP HANA-példányok. Különösen beállítása és HANA replikációs vagy feladatok műveletek, például biztonsági mentés és visszaállítás az SAP HANA-adatbázisok.

További cikkeit, osszon ki a helyes áttekintését a SAP HANA-témakörök az Azure-ban a következők:

- [Egypéldányos SAP HANA Azure virtuális gépeken manuális telepítése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [A telepítő SAP HANA replikációs az Azure virtuális gépeken](sap-hana-high-availability.md)
- [Biztonsági mentési útmutató SAP Hana Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

A cikk és meg kell ismernie a SAP HANA kapcsolatos tartalom is listázva lehet hasonló:

- [SAP Hana magas rendelkezésre állás](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Gyakran ismételt kérdések: A magas rendelkezésre állás, a SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Hogyan hajthat végre a rendszer replikációs SAP Hana](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 szervizcsomag-verzió 01 Újdonságok: magas rendelkezésre állás](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [SAP HANA replikációs hálózati javaslatok](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [SAP HANA replikációs](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA szolgáltatás automatikus újraindítás letiltása](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [SAP HANA replikációs konfigurálása](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)


Túl ismeri az Azure virtuális gépek telepítése alatt álló, azt is javasoljuk a cikk elolvasása [a Windows Azure virtuális gépek rendelkezésre állásának kezelése](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) első, a rendelkezésre állási architektúra meghatározása az Azure-ban folytatása előtt.

## <a name="service-level-agreements-for-different-azure-components"></a>Szolgáltatásiszint-szerződések különböző Azure-összetevők
Azure másik rendelkezésre állási SLA-k más összetevők, például hálózati, tárolási és virtuális gépek rendelkezik. Ezek SLA-k szerepelnek, valamint kezdve található a [Microsoft Azure szolgáltatásiszint-megállapodás](https://azure.microsoft.com/support/legal/sla/) lap. Ha tekintse meg a [SLA-t a virtuális gépek](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), akkor vegye figyelembe, hogy az Azure biztosít két különböző SLA-k két különböző konfigurációjú. Például az SLA-k meghatározása:

- Egyetlen virtuális gép használatával [prémium szintű Azure Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) az operációs rendszer a lemez és adatlemezek összes biztosít a 99,9 %-os havi üzemidő százalékos aránya
- Több (legalább két) virtuális gépek vannak rendszerezve egy [Azure rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) 99,95 %-os havi üzemidő százalékos meg

A követelmény az Azure SLA-összetevők rendelkezésre állás adja meg, és ezután döntse el, a különböző alkalmazási helyzetek kell megvalósítani SAP HANA a rendelkezésre állás biztosítása érdekében, hogy a mérték biztosításához szükséges.

## <a name="next-steps"></a>További lépések
Továbbra is a dokumentumok:

- [SAP HANA rendelkezésre állási egy Azure-régión belül](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Azure-régiók közötti SAP HANA rendelkezésre állása](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 















  


