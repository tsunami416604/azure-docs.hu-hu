---
title: AZ SAP HANA elérhetősége az Azure virtuális gépeken – áttekintés | Microsoft dokumentumok
description: Ismerteti az SAP HANA-műveletek az Azure natív virtuális gépeken.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bccaf45cf617bd31a584b6c73f3dd08877bc8587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71266058"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>SAP HANA magas rendelkezésre állás az Azure virtuális gépekhez

Számos Azure-képesség használatával üzembe helyezheti a kritikus fontosságú adatbázisokat, például az SAP HANA-t az Azure virtuális gépeken. Ez a cikk útmutatást nyújt az Azure-beli virtuális gépeken üzemeltetett SAP HANA-példányok rendelkezésre állásának eléréséhez. A cikk számos forgatókönyvet ismertet, amelyek az Azure-infrastruktúrával az SAP HANA azure-beli rendelkezésre állásának növeléséhez valósíthatók meg. 

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy ismeri az azure-beli infrastruktúra szolgáltatásként (IaaS) alapjait, többek között: 

- Virtuális gépek vagy virtuális hálózatok üzembe helyezése az Azure Portalon vagy a PowerShellen keresztül.
- Az Azure cross-platform parancssori felület (Azure CLI), beleértve a javascript objektumnotítás (JSON) sablonok használatával.

Ez a cikk azt is feltételezi, hogy ismeri az SAP HANA-példányok telepítését, valamint az SAP HANA-példányok felügyeleti és kezelő. Különösen fontos, hogy tisztában legyen a HANA rendszer replikációjának beállításával és működésével. Ez magában foglalja a feladatok, például az SAP HANA-adatbázisok biztonsági mentését és visszaállítását.

Ezek a cikkek jó áttekintést nyújtanak az SAP HANA azure-beli használatáról:

- [Egypéldányos SAP HANA manuális telepítése az Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [SAP HANA-rendszerreplikáció beállítása az Azure virtuális gépein](sap-hana-high-availability.md)
- [SAP HANA biztonsági mentése Azure-beli virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Az SAP HANA-ról szóló cikkek ismerete is jó ötlet:

- [Az SAP HANA magas rendelkezésre állása](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [GYAKORI KÉRDÉSEK: Az SAP HANA magas rendelkezésre állása](https://archive.sap.com/documents/docs/DOC-66702)
- [Rendszerreplikáció végrehajtása az SAP HANA számára](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 SPS 01 Újdonságok: Magas rendelkezésre állás](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Az SAP HANA rendszer replikációjának hálózati javaslatai](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [SAP HANA rendszer replikációja](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA szolgáltatás automatikus újraindítása](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [SAP HANA rendszerreplikáció konfigurálása](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Azon túl, hogy már ismeri a virtuális gépek üzembe helyezését az Azure-ban, mielőtt definiálná a rendelkezésre állási architektúráját az Azure-ban, azt javasoljuk, hogy olvassa el [a Windows virtuális gépek azure-beli elérhetőségének kezelése](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)című olvasnivalót.

## <a name="service-level-agreements-for-azure-components"></a>Szolgáltatásiszint-szerződések az Azure-összetevőkhöz

Az Azure különböző rendelkezésre állási SLOS-okkal rendelkezik a különböző összetevőkhöz, például a hálózatkezeléshez, a tároláshoz és a virtuális gépekhez. Minden SLA dokumentálva van. További információt a [Microsoft Azure szolgáltatásiszint-szerződésekkel](https://azure.microsoft.com/support/legal/sla/)talál. 

[Virtuális gépek SLA-ja](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) három különböző SLA-t ír le három különböző konfigurációban:

- Egyetlen virtuális gép, amely [az Azure prémium szintű SSD-k](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) az operációs rendszer lemeze és az összes adatlemezek. Ez a beállítás 99,9 százalékos havi felkészültségi időt biztosít.
- Több (legalább két) virtuális gép, amelyek [egy Azure rendelkezésre állási csoportba](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)vannak rendezve. Ez a beállítás 99,95 százalékos havi felkészültségi időt biztosít.
- Több (legalább két) virtuális gép, amelyek [availablity zónába](https://docs.microsoft.com/azure/availability-zones/az-overview)vannak rendezve. Ez a beállítás 99,99 százalékos havi rendelkezésre időt biztosított.

Mérje a rendelkezésre állási követelményt az Azure-összetevők által biztosított SLOS-okkal. Ezután válassza ki az SAP HANA forgatókönyveit a szükséges rendelkezésre állási szint eléréséhez.

## <a name="next-steps"></a>További lépések

- Ismerje meg [az SAP HANA egyetlen Azure-régión belüli elérhetőségét.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- Ismerje meg [az SAP HANA rendelkezésre állását az Azure-régiókban.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 















  


