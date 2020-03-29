---
title: SAP használata Linuxos virtuális gépeken | Microsoft dokumentumok
description: Ismerje meg az SAP használatát Linux virtuális gépeken (VM) a Microsoft Azure megoldásban
services: virtual-machines-linux,virtual-network,storage
documentationcenter: saponazure
author: MSSedusch
manager: gwallace
editor: ''
tags: azure-service-management
keywords: ''
ms.assetid: f9cd93dc-71ad-48a4-8778-4e48aec484a6
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: na
ms.date: 10/04/2016
ms.author: sedusch
ms.openlocfilehash: 9525f339861b5de8dc22da753f7c36dcc6eede8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70078985"
---
# <a name="using-sap-on-linux-virtual-machines-in-azure"></a>Az SAP használata Linuxos virtuális gépeken az Azure-ban
A felhőalapú számítástechnika széles körben használt fogalom, amely egyre fontosabbá válik az informatikai iparágban, a kisvállalatok esetében ugyanúgy, mint a nagy, nemzetközi vállalatok számára. A Microsoft Azure a Microsoft új lehetőségek széles skáláját nyújtó felhőszolgáltatás-platformja. Az ügyfelek most már gyorsan építhetnek ki és vonhatnak ki az üzemből felhőszolgáltatásként rendelkezésre bocsájtott alkalmazásokat, ami azt jelenti, hogy többé nem érvényesülnek a technikai vagy költségvetési korlátozások. A vállalatoknak nem kell időt és forrásokat befektetni a hardverinfrastruktúrába: ehelyett az alkalmazásra, az üzleti folyamatokra és az ügyfeleknek és felhasználóknak nyújtott értékre koncentrálhatnak.

A Microsoft Azure virtuális gépekkel a Microsoft átfogó infrastruktúra szolgáltatásként (IaaS) platformot kínál. Az Azure virtuális gépek támogatják az SAP NetWeaver-alapú alkalmazásokat (IaaS). Az alábbi tanulmányok ismertetik, hogyan tervezhet és valósítható meg az SAP NetWeaver alapú alkalmazások windowsos virtuális gépeken az Azure-ban. Sap NetWeaver alapú alkalmazásokat is megvalósíthat [Windows virtuális gépeken.](../../virtual-machines-windows-classic-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[!INCLUDE [virtual-machines-common-classic-sap-get-started](../../../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure-suse-linux-virtual-machines"></a>SAP NetWeaver az Azure SUSE Linux virtuális gépeken
Cím: Az SAP NetWeaver tesztelése Microsoft Azure SUSE Linux virtuális gépeken

Összegzés: Nincs hivatalos SAP-támogatás az SAP NetWeaver azure-beli Linuxos virtuális gépeken való futtatásához ebben az időpontban. Mindazonáltal az ügyfelek érdemes lehet néhány tesztelést végezni, vagy érdemes sap-bemutató vagy betanítási rendszerek futtatását az Azure Linux virtuális gépeken, amíg nincs szükség az SAP-támogatáshoz való kapcsolatfelvételre. Ez a cikk segítséget nyújt az Azure SUSE Linux virtuális gépek beállításához az SAP futtatásához, és néhány alapvető tippet ad a gyakori lehetséges buktatások elkerülése érdekében.

Frissítve: 2015.

[Ez a cikk itt található](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

