---
title: Példa Azure-infrastruktúra bemutató |} A Microsoft Docs
description: Ismerje meg a főbb tervezési és megvalósítási irányelvek egy példa infrastruktúra az Azure-beli üzembe helyezéséhez.
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7032b586-e4e5-4954-952f-fdfc03fc1980
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ddbaed6704fd32f7fd4fe5a790424cbf829d2f1c
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932857"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Windows virtuális gépek Azure-infrastruktúra bemutató példa
Ez a cikk bemutatja a például alkalmazás-infrastruktúrák létrehozását. A Microsoft részletes információkat talál, egy egyszerű online áruházban, amely egyesíti az irányelvek és elnevezési konvenciók, a rendelkezésre állási csoportok, virtuális hálózatok és terheléselosztók döntések infrastruktúra tervezése és ténylegesen üzembe helyezése a virtuális gépek (VM).

## <a name="example-workload"></a>Példa munkaterhelés
Az Adventure Works Cycles szeretne egy online áruházból-alkalmazások az Azure-ban, amely a következőkből áll:

* A webes szintű előtér-ügyfelet futtató két IIS-kiszolgálók
* Adatok és a egy alkalmazásrétegbe a rendelések feldolgozása két IIS-kiszolgálókkal
* Két Microsoft SQL Server-példány AlwaysOn rendelkezésre állási csoportokkal (két különböző SQL Server és a legtöbb csomópont tanúsító) termék adatok és a rendelések tárolására az adatbázis-rétegből
* Két felhasználói fiókok és a egy hitelesítési szint szállítók Active Directory-tartományvezérlők
* Összes kiszolgáló két alhálózat helye:
  * a webkiszolgálói az előtér-alhálózat 
  * Az alkalmazáskiszolgálók, az SQL-fürt és a tartományvezérlők egy háttérbeli alhálózatot

![Az alkalmazás-infrastruktúra különböző rétegek ábrája](./media/infrastructure-example/example-tiers.png)

Biztonságos bejövő webes forgalmat kell kell kiegyenlített terhelésű a webkiszolgálók között, az ügyfelek, keresse meg az online áruházból. A rendelés feldolgozása forgalom HTTP-kéréseket a webes kiszolgálók a kell application kiszolgálók között elosztott terhelésű formájában. Ezenkívül az infrastruktúra úgy kell megtervezni, magas rendelkezésre állás érdekében.

Az eredményül kapott kialakítási tartalmaznia kell:

* Azure-előfizetések és fiók
* Egyetlen erőforráscsoportra
* Azure Managed Disks
* Egy virtuális hálózatot két alhálózattal
* Egy hasonló szerepkörrel rendelkező virtuális gépek rendelkezésre állási csoportok
* Virtual machines (Virtuális gépek)

Az összes a fenti ezek elnevezési konvencióinak:

* Adventure Works Cycles használ **[informatikai munkaterhelés]-[helye]-[Azure-erőforrás]** előtagjaként
  * Ebben a példában "**azos**" (az Azure Online Store) az informatikai számítási feladat neve és a "**használata**" (USA keleti RÉGIÓJA 2) az a hely
* Virtuális hálózatok használata AZOS-használat – VN **[száma]**
* A rendelkezésre állási csoportok használata azos-használata-as-**[szerepkör]**
* Virtuálisgép-neveknek azos használata – használata – virtuálisgép -**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Az Azure-előfizetések és fiókok
Az Adventure Works Cycles nevű Adventure Works nagyvállalati előfizetéssel, a vállalati előfizetést, segítségével az informatikai számítási feladathoz tartozó számlázási.

## <a name="storage"></a>Storage
Az Adventure Works Cycles határozza meg, hogy az Azure Managed Disks kell használniuk. Virtuális gépek létrehozásakor a mindkét elérhető tárolási rétegek használhatók:

* **Standard szintű storage** a webkiszolgálók, az alkalmazáskiszolgálókat, és a tartományvezérlők és az adatlemezek.
* **A Premium storage** az SQL Server virtuális gépek és az adatlemezek.

## <a name="virtual-network-and-subnets"></a>Virtuális hálózat és alhálózatok
Mivel a virtuális hálózat nem kell a folyamatban lévő kapcsolódástól az Adventure Work ciklusokat a helyszíni hálózathoz, csak felhőalapú virtuális hálózaton döntöttek.

Általuk létrehozott egy kizárólag felhőalapú virtuális hálózatot az Azure portal használatával a következő beállításokkal:

* Name: AZOS-USE-VN01
* Hely: East US 2
* Virtuális hálózat címtere: 10.0.0.0/8
* Első alhálózat:
  * Neve: FrontEnd
  * Címtér: 10.0.1.0/24
* Második alhálózatot:
  * Neve: BackEnd
  * Címtere: 10.0.2.0/24

## <a name="availability-sets"></a>Rendelkezésre állási csoportok
Az Adventure Works Cycles magas rendelkezésre állásának összes négy szinten az online áruház fenntartására, úgy döntött, négy rendelkezésre állási csoportok:

* **azos-használat-as-web** a webkiszolgálók számára
* **azos-használat-as-app** az alkalmazáskiszolgálók
* **azos-használjon-t-sql-** az SQL-kiszolgálók
* **azos-használat-as-dc** a tartományvezérlőknek

## <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
Az Adventure Works Cycles kiválasztotta az Azure virtuális gépek a következő nevekkel:

* **azos-használat – virtuálisgép-web01** az első webkiszolgáló
* **azos-használat – virtuálisgép-web02** a második webkiszolgáló
* **azos-használat – virtuálisgép-app01** az első alkalmazás-kiszolgálón
* **azos-használat – virtuálisgép-app02** az alkalmazás második kiszolgálón
* **azos-használat – virtuálisgép-sql01** a fürt első SQL Server-kiszolgáló
* **azos-használat – virtuálisgép-sql02** a második az SQL Server-kiszolgáló a fürtben
* **azos-használat – virtuálisgép-dc01** az első tartományvezérlő
* **azos-használat – virtuálisgép-dc02** a második tartományvezérlő

Íme a létrejövő konfigurációt.

![Az Azure-ban üzembe helyezett alkalmazások végleges infrastruktúra](./media/infrastructure-example/example-config.png)

Ez a konfiguráció a következőket tartalmazza:

* Egy kizárólag felhőalapú virtuális hálózatot két alhálózattal (előtérbeli és háttérbeli)
* Az Azure Managed Disks, Standard és prémium szintű lemezek
* Egy, az egyes szintek az online áruház négy rendelkezésre állási csoportok
* A virtuális gépek a négy szinten
* Egy külső elosztott terhelésű készlet webkiszolgálók az internetről érkező HTTPS-alapú webes forgalom
* Az elosztott terhelésű készlet az alkalmazáskiszolgálók a webkiszolgálók titkosítatlan webes forgalmat a belső
* Egyetlen erőforráscsoportra
