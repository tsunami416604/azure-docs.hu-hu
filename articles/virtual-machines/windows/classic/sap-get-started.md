---
title: "A Windows virtuális gépek SAP használatával |} Microsoft Docs"
description: "A Windows virtuális gépek (VM) a Microsoft Azure-ban SAP használatáról törlése"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: MSSedusch
manager: timlt
editor: 
tags: azure-service-management
keywords: 
ms.assetid: 1b455be4-c02f-43e3-8d39-c2d5f216e646
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: campaign-page
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/04/2016
ms.author: sedusch
ms.openlocfilehash: 1d6326d5f79ea4e975abd1aa90b0d4a635f4c31a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="using-sap-on-windows-virtual-machines-in-azure"></a>SAP használata a Windows virtuális gépek Azure-ban
A felhőalapú számítástechnika széles körben használt fogalom, amely egyre fontosabbá válik az informatikai iparágban, a kisvállalatok esetében ugyanúgy, mint a nagy, nemzetközi vállalatok számára. A Microsoft Azure a Microsoft új lehetőségek széles skáláját nyújtó felhőszolgáltatás-platformja. Az ügyfelek most már gyorsan építhetnek ki és vonhatnak ki az üzemből felhőszolgáltatásként rendelkezésre bocsájtott alkalmazásokat, ami azt jelenti, hogy többé nem érvényesülnek a technikai vagy költségvetési korlátozások. A vállalatoknak nem kell időt és forrásokat befektetni a hardverinfrastruktúrába: ehelyett az alkalmazásra, az üzleti folyamatokra és az ügyfeleknek és felhasználóknak nyújtott értékre koncentrálhatnak.

Microsoft Azure virtuális gépekkel Microsoft biztosít az átfogó infrastruktúra (IaaS) szolgáltatás platformként. Az Azure virtuális gépek támogatják az SAP NetWeaver-alapú alkalmazásokat (IaaS). Az alábbi tanulmányok azt ismertetik, hogyan tervezésével és megvalósításával SAP NetWeaver-alapú alkalmazások a Windows virtuális gépek Azure-ban. SAP NetWeaver-alapú alkalmazások valósítja meg a [Linux virtuális gépek](../../linux/classic/sap-get-started.md).

[!INCLUDE [virtual-machines-common-classic-sap-get-started](../../../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure---ha"></a>SAP NetWeaver Azure - magas rendelkezésre ÁLLÁSÚ
Cím: SAP NetWeaver Azure - fürtszolgáltatási SAP ASC/SCS példányok SIOS DataKeeper az Azure-on Windows Server feladatátvevő fürt használatával

Összefoglalás: "Ez a dokumentum egy magas rendelkezésre állású SAP ASC/SCS konfiguráció Azure SIOS DataKeeper használatát ismerteti. SAP védelme hiba összetevők, például SAP ASC/SCS vagy sorba helyezni replikációs szolgáltatása a Windows Server feladatátvevő fürt konfigurációjának megosztott lemezek igénylő az egyetlen pont. Az SAP-összetevők kell mindenhonnan elérhetőnek egy SAP rendszer működésére. Ezért a magas rendelkezésre állású funkcióit kell kell bevezetni győződjön meg arról, hogy ezek az összetevők képes elviselni a kiszolgálót vagy egy virtuális Gépet, mert fürtkonfigurációk Windows operációs rendszer nélküli és a Hyper-V környezetben végzett sikertelen. 2015. augusztus frissítésétől magát az Azure nem adhatók meg a megosztott lemez, amely szükséges a Windows alapú ezen kritikus SAP-összetevők szükséges magas rendelkezésre állású konfigurációkat. Segítségével. a termék által SIOS DataKeeper, azonban a Windows Server feladatátvevő fürt konfigurációk az SAP ASC/SCS igény szerint az Azure infrastruktúra-szolgáltatási platformon építhetők. A dokumentum lépés-lépés megközelítéssel telepítését ismerteti a Windows Server feladatátvevő fürt konfigurációjának SIOS Datakeeper az Azure által biztosított megosztott lemezzel. A dokumentum alapján meghatározható a konfigurációban szereplő részletek a Azure, a Windows és az SAP oldalon, amelyek révén a magas rendelkezésre állású konfiguráció optimális módon működik. A dokumentum kiegészíti az SAP-dokumentáció és az SAP megadott platformok telepítések és a SAP szoftver központi telepítését az elsődleges erőforrások képviselik.

Frissített: 2015. augusztus

[Ez az útmutató letöltése](http://go.microsoft.com/fwlink/?LinkId=613056)

