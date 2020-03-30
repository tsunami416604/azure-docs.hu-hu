---
title: Példa az Azure-infrastruktúra forgatókönyve
description: Ismerje meg a példainfrastruktúra Azure-beli üzembe helyezésére vonatkozó legfontosabb tervezési és megvalósítási irányelveket.
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7032b586-e4e5-4954-952f-fdfc03fc1980
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ab6f304d78357e261c68ebbcfcb3746844edce8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74038567"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Példa az Azure-infrastruktúra forgatókönyve windowsos virtuális gépekhez
Ez a cikk végigvezeti egy példa alkalmazás-infrastruktúra kiépítése. Részletesen tervezzük meg egy egyszerű online áruház infrastruktúrájának tervezését, amely egyesíti az elnevezési konvenciókkal, a rendelkezésre állási készletekkel, a virtuális hálózatokkal és a terheléselosztókkal kapcsolatos irányelveket és döntéseket, és ténylegesen üzembe helyezi a virtuális gépeket (VM).We detailing ing a infrastructure for a simple store that together the guidelines and decisions around naming conventions, availability sets, virtual networks and load balancers, and tényleges deploying your virtual machines (VMs).

## <a name="example-workload"></a>Példa munkaterhelésre
A Adventure Works Cycles online áruházbeli alkalmazást szeretne építeni az Azure-ban, amely a következőkből áll:

* Két, az ügyfél előtér-kiszolgálója webes rétegben
* Két IIS-kiszolgáló, amelyek adatokat és rendeléseket dolgoznak fel egy alkalmazásszinten
* Két Microsoft SQL Server példány AlwaysOn rendelkezésre állási csoportokkal (két SQL-kiszolgáló és egy csomóponttanúsító többségi) a termékadatok és a rendelések adatbázisszinten való tárolására
* Két Active Directory-tartományvezérlő ügyfélfiókokhoz és szállítókhoz hitelesítési szinten
* Az összes kiszolgáló két alhálózatban található:
  * előtér-alhálózat a webkiszolgálókszámára 
  * háttér-alhálózat az alkalmazáskiszolgálókhoz, SQL-fürthöz és tartományvezérlőkhöz

![Az alkalmazásinfrastruktúra különböző szintjeinek diagramja](./media/infrastructure-example/example-tiers.png)

A bejövő biztonságos webes forgalmat a webkiszolgálók között terheléselosztásnak kell tekinteni, miközben az ügyfelek az online áruházban böngésznek. A webkiszolgálókról HTTP-kérelmek formájában létrejövő rendelésfeldolgozási forgalmat egyensúlyba kell hozni az alkalmazáskiszolgálók között. Ezenkívül az infrastruktúrát magas rendelkezésre állásra kell megtervezni.

Az így kapott formatervezési mintának a következőket kell tartalmaznia:

* Azure-előfizetés és fiók
* Egyetlen erőforráscsoport
* Azure Managed Disks
* Két alhálózattal rendelkező virtuális hálózat
* A hasonló szerepkörrel rendelkező virtuális gépek rendelkezésre állási készletei
* Virtuális gépek

A fentiek mindegyike követi az alábbi elnevezési konvenciókat:

* A Adventure Works Cycles előtagként az **[INFORMATIKAI munkaterhelést]-[hely]-[Azure-erőforrás]** használja előtagként
  * Ebben a példában az **"azos**" (Azure Online Store) az IT számítási feladatok neve, a "**use**" (USA keleti része 2) pedig az a hely,
* Virtuális hálózatok használata AZOS-USE-VN **[szám]**
* Az elérhetőségi csoportok az azos-use-as-**[role]**
* A virtuálisgép-nevek azos-use-vm-**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Azure-előfizetések és fiókok
A Adventure Works Cycles a Nagyvállalati előfizetését, az Adventure Works Enterprise Subscription-t használja az informatikai munkaterhelés számlázásának biztosításához.

## <a name="storage"></a>Storage
A Kalandor-works ciklusok meghatározták, hogy az Azure felügyelt lemezeit kell használniuk. Virtuális gépek létrehozásakor mindkét rendelkezésre álló tárolási szint használatos:

* **A** webkiszolgálók, az alkalmazáskiszolgálók, a tartományvezérlők és adatlemezeik szabványos tárolása.
* **Az** SQL Server virtuális gépek és adatlemezeik prémium szintű tárolása.

## <a name="virtual-network-and-subnets"></a>Virtuális hálózat és alhálózatok
Mivel a virtuális hálózatnak nincs szüksége folyamatos kapcsolatra a helyszíni kalandmunka-ciklusokkal, úgy döntöttek, hogy csak felhőalapú virtuális hálózatot hoznak létre.

Az Azure Portalon a következő beállításokkal hoztak létre csak felhőalapú virtuális hálózatot:

* Név: AZOS-USE-VN01
* Helyszín: USA keleti része 2
* Virtuális hálózati címtér: 10.0.0.0/8
* Első alhálózat:
  * Név: Előtér
  * Címterület: 10.0.1.0/24
* Második alhálózat:
  * Név: Háttérnév
  * Címterület: 10.0.2.0/24

## <a name="availability-sets"></a>Rendelkezésre állási csoportok
Annak érdekében, hogy online áruházuk mind a négy szintjének magas rendelkezésre állását fenn tartsa, a Kalandor-ciklusok négy rendelkezésre állási csoportról döntöttek:

* **azos-use-as-web** a web szerverek
* **azos-use-as-app** az alkalmazás szerverek
* **azos-use-as-sql** az SQL-kiszolgálók
* **azos-use-as-dc** tartományvezérlőkhöz

## <a name="virtual-machines"></a>Virtuális gépek
A Kalandor-worksciklusok az alábbi neveket határozta meg az Azure-beli virtuális gépekhez:

* **azos-use-vm-web01** az első webszerver
* **azos-use-vm-web02** a második webszerverhez
* **azos-use-vm-app01** az első alkalmazáskiszolgálóhoz
* **azos-use-vm-app02** a második alkalmazáskiszolgálóhoz
* **azos-use-vm-sql01** a fürt első SQL Server-kiszolgálójához
* **azos-use-vm-sql02** a fürt második SQL Server-kiszolgálóján
* **azos-use-vm-dc01** az első tartományvezérlőhöz
* **azos-use-vm-dc02** a második tartományvezérlőhöz

Itt van az eredményül kapott konfiguráció.

![Az Azure-ban telepített végső alkalmazásinfrastruktúra](./media/infrastructure-example/example-config.png)

Ez a konfiguráció a következőket foglalja magában:

* Csak felhőalapú virtuális hálózat két alhálózattal (Előtér és Háttérrendszer)
* Azure felügyelt lemezek standard és prémium szintű lemezekkel egyaránt
* Négy rendelkezésre állási csoport, egy az online áruház minden rétegére
* A négy réteg virtuális gépei
* Külső terheléselosztásos készlet https-alapú webes forgalomhoz az internetről a webkiszolgálókra
* Belső terheléselosztási készlet a webkiszolgálókról az alkalmazáskiszolgálókra irányuló titkosítatlan webes forgalomhoz
* Egyetlen erőforráscsoport
