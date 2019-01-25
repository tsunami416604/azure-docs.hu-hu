---
title: Példa Azure-infrastruktúra bemutató |} A Microsoft Docs
description: Ismerje meg a főbb tervezési és megvalósítási irányelvek egy példa infrastruktúra az Azure-beli üzembe helyezéséhez.
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 281fc2c0-b533-45fa-81a3-728c0049c73d
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d4b8cd07e50697139f68084f47c847ef8728c429
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888958"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Linux rendszerű virtuális gépek Azure-infrastruktúra bemutató példa
Ez a cikk bemutatja a például alkalmazás-infrastruktúrák létrehozását. A Microsoft részletes információkat talál, egy egyszerű online áruházban, amely egyesíti az irányelvek és elnevezési konvenciók, a rendelkezésre állási csoportok, virtuális hálózatok és terheléselosztók döntések-infrastruktúra megtervezése, és ténylegesen üzembe helyezése a virtuális gépek (VM).

## <a name="example-workload"></a>Példa munkaterhelés
Az Adventure Works Cycles szeretne egy online áruházbeli alkalmazások az Azure-ban, amely áll:

* A webes szintű előtér-ügyfelet futtató két nginx-kiszolgáló
* Adatok és a egy alkalmazásrétegbe a rendelések feldolgozása két nginx-kiszolgáló
* Két MongoDB kiszolgálók horizontálisan skálázott fürt részét termék adatok és a rendelések tárolására az adatbázis-rétegből
* Két felhasználói fiókok és a egy hitelesítési szint szállítók Active Directory-tartományvezérlők
* Összes kiszolgáló két alhálózat helye:
  * a webkiszolgálói az előtér-alhálózat 
  * Az alkalmazáskiszolgálók, a MongoDB-fürttel és a tartományvezérlők egy háttérbeli alhálózatot

![Az alkalmazás-infrastruktúra különböző rétegek ábrája](./media/infrastructure-example/example-tiers.png)

Biztonságos bejövő webes forgalmat kell kell kiegyenlített terhelésű a webkiszolgálók között, az ügyfelek, keresse meg az online áruház. Rendelésfeldolgozó forgalom HTTP formájában kér a webes kiszolgálókat kell lennie kiegyenlített terhelésű az alkalmazáskiszolgálók között. Ezenkívül az infrastruktúra úgy kell megtervezni, magas rendelkezésre állás érdekében.

Az eredményül kapott kialakítási tartalmaznia kell:

* Azure-előfizetések és fiók
* Egyetlen erőforráscsoportra
* Azure Managed Disks
* Egy virtuális hálózatot két alhálózattal
* Egy hasonló szerepkörrel rendelkező virtuális gépek rendelkezésre állási csoportok
* Virtual machines (Virtuális gépek)

Az összes a fenti ezek elnevezési konvencióinak:

* Adventure Works Cycles használ **[informatikai munkaterhelés]-[helye]-[Azure-erőforrás]** előtagjaként
  * Ebben a példában "**azos**" (az Azure online Store) az informatikai számítási feladat neve és a "**használata**" (USA keleti RÉGIÓJA 2) az a hely
* Virtuális hálózatok használata AZOS-használat – VN **[száma]**
* A rendelkezésre állási csoportok használata azos-használata-as-**[szerepkör]**
* Virtuálisgép-neveknek azos használata – használata – virtuálisgép -**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Az Azure-előfizetések és fiókok
Az Adventure Works Cycles nevű Adventure Works nagyvállalati előfizetéssel, a vállalati előfizetést, segítségével az informatikai számítási feladathoz tartozó számlázási.

## <a name="storage"></a>Storage
Az Adventure Works Cycles határozza meg, hogy az Azure Managed Disks kell használniuk. Virtuális gépek létrehozásakor, mindkét tárolási elérhető tárolási rétegek használhatók:

* **Standard szintű storage** a webkiszolgálók, az alkalmazáskiszolgálókat, és a tartományvezérlők és az adatlemezek.
* **A Premium storage** a horizontálisan skálázott MongoDB kiszolgálókat és az adatlemezek.

## <a name="virtual-network-and-subnets"></a>Virtuális hálózat és alhálózatok
Mivel a virtuális hálózat nem kell a folyamatban lévő kapcsolódástól az Adventure Work ciklusokat a helyszíni hálózathoz, csak felhőalapú virtuális hálózaton döntöttek.

Általuk létrehozott egy kizárólag felhőalapú virtuális hálózatot az Azure portal használatával a következő beállításokkal:

* Név: AZOS-USE-VN01
* Hely: USA 2. keleti régiója
* virtuális hálózat címtere: 10.0.0.0/8
* Első alhálózat:
  * Név: Előtér
  * Címtér: 10.0.1.0/24
* Második alhálózatot:
  * Név: Háttér
  * Címtér: 10.0.2.0/24

## <a name="availability-sets"></a>Rendelkezésre állási csoportok
Az Adventure Works Cycles magas rendelkezésre állásának összes négy szinten az online áruház fenntartására, úgy döntött, négy rendelkezésre állási csoportok:

* **azos-használat-as-web** a webkiszolgálók számára
* **azos-használat-as-app** az alkalmazáskiszolgálók
* **azos-use-as-adatbázis** a horizontálisan skálázott MongoDB-fürtben lévő kiszolgálók
* **azos-használat-as-dc** a tartományvezérlőknek

## <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
Az Adventure Works Cycles kiválasztotta az Azure virtuális gépek a következő nevekkel:

* **azos-használat – virtuálisgép-web01** az első webkiszolgáló
* **azos-használat – virtuálisgép-web02** a második webkiszolgáló
* **azos-használat – virtuálisgép-app01** az első alkalmazás-kiszolgálón
* **azos-használat – virtuálisgép-app02** az alkalmazás második kiszolgálón
* **azos-használat – virtuálisgép-db01** a fürt első MongoDB-kiszolgáló
* **azos-használat – virtuálisgép-db02** a második MongoDB-kiszolgáló a fürtben
* **azos-használat – virtuálisgép-dc01** az első tartományvezérlő
* **azos-használat – virtuálisgép-dc02** a második tartományvezérlő

Íme a létrejövő konfigurációt.

![Az Azure-ban üzembe helyezett alkalmazások végleges infrastruktúra](./media/infrastructure-example/example-config.png)

Ez a konfiguráció a következőket tartalmazza:

* Egy kizárólag felhőalapú virtuális hálózatot két alhálózattal (előtérbeli és háttérbeli)
* Standard és prémium lemezek segítségével az Azure Managed Disks
* Egy, az egyes szintek az online áruház négy rendelkezésre állási csoportok
* A virtuális gépek a négy szinten
* Egy külső elosztott terhelésű készlet webkiszolgálók az internetről érkező HTTPS-alapú webes forgalom
* Az elosztott terhelésű készlet az alkalmazáskiszolgálók a webkiszolgálók titkosítatlan webes forgalmat a belső
* Egyetlen erőforráscsoportra
