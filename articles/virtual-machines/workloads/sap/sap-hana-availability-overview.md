---
title: SAP HANA elérhetőségét a Azure virtuális gépek – áttekintés |} Microsoft Docs
description: Ismerteti az Azure natív virtuális gépeken futó SAP HANA-műveletek.
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
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187157"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>SAP HANA az Azure virtuális gépek magas rendelkezésre állás

Számos Azure-képességek segítségével telepítheti a kritikus fontosságú adatbázisokhoz például SAP HANA Azure virtuális gépeken. Ez a cikk ismerteti az Azure virtuális gépeken futó SAP HANA-példányok rendelkezésre állás biztosítása érdekében nyújt útmutatást. Az itt ismertetett Megvalósíthat több forgatókönyvek az Azure-ban SAP HANA rendelkezésre állásának növeléséhez az Azure-infrastruktúra használatával. 

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy Ön ismeri a infrastruktúra egy szolgáltatási (IaaS) alapjait, az Azure-ban, beleértve: 

- Ügyfélszoftverek központi telepítése a virtuális gépek vagy virtuális hálózatok az Azure portálon vagy a PowerShell segítségével.
- Az Azure platformfüggetlen parancssori felületével (Azure CLI), köztük a JavaScript Object Notation (JSON) sablonok használatával lehetőség használatával.

Ez a cikk is feltételezi, hogy Ön ismeri SAP HANA-példány telepítésével és felügyeletével és működési SAP HANA-példányok. Különösen fontos, hogy ismernie kell a telepítő és HANA replikációs működésére. Ez magában foglalja a feladatokat, mint a biztonsági mentés és visszaállítás az SAP HANA-adatbázisok.

Ezek a cikkek adjon meg egy helyes használatának áttekintése a SAP HANA az Azure-ban:

- [Egypéldányos SAP HANA Azure virtuális gépeken manuális telepítése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Az Azure virtuális gépeken SAP HANA-rendszer replikáció beállítása](sap-hana-high-availability.md)
- [SAP HANA mentésére az Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Emellett érdemes ezen SAP HANA vonatkozó cikkek ismernie kell:

- [SAP Hana magas rendelkezésre állás](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Gyakran ismételt kérdések: A magas rendelkezésre állás, a SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Végezze el a rendszer replikációt SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 szervizcsomag-verzió 01 mi tartozó új: magas rendelkezésre állás](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [A tárolóhálózatra vonatkozó javaslatokkal SAP HANA-rendszer replikációhoz](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [SAP HANA replikációs](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA szolgáltatás automatikus újraindítás letiltása](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [SAP HANA replikációs konfigurálása](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Túl alatt ismeri az Azure virtuális gépek telepítése előtt az Azure ad meg a rendelkezésre állási architektúra, azt javasoljuk, hogy olvassa el [a Windows Azure virtuális gépek rendelkezésre állásának kezelése](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).

## <a name="service-level-agreements-for-azure-components"></a>Szolgáltatásiszint-szerződésekben rögzített Azure összetevők

Azure másik rendelkezésre állási SLA-k más összetevők, például hálózati, tárolási és virtuális gépek rendelkezik. Minden SLA-k szerepelnek. További információkért lásd: [Microsoft Azure szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/). 

[SLA-t a virtuális gépek](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) két különböző SLA-k, a két különböző konfigurációt ismerteti:

- Egyetlen virtuális gép által használt [prémium szintű Azure Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) az operációsrendszer-lemez és adatlemezek összes. Ezt a lehetőséget nyújt a havi hasznos üzemidő 99,9 %-a.
- Több (legalább két) virtuális gépek vannak rendszerezve egy [Azure rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Ezt a lehetőséget nyújt a havi hasznos üzemidő 99,95 %-a.

A rendelkezésre állás terén, szemben a szolgáltatásiszint-szerződések Azure összetevők biztosító mérését. Ezután válassza az esetek SAP Hana a szükséges fokú rendelkezésre állása érdekében.

## <a name="next-steps"></a>További lépések

- További tudnivalók [SAP HANA rendelkezésre állási belül egy Azure-régiót](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region).
- További tudnivalók [SAP HANA rendelkezésre állási Azure-régiók közötti](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions). 















  


