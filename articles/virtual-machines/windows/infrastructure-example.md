---
title: "Példa az Azure infrastruktúra forgatókönyv |} Microsoft Docs"
description: "További tudnivalók a főbb tervezési és megvalósítási irányelveket az Azure-ban egy példa infrastruktúra üzembe helyezéséhez."
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7032b586-e4e5-4954-952f-fdfc03fc1980
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee66bf554e8e623ebfaa82bc888fc541da322d2f
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Windows virtuális gépek Azure-infrastruktúra bemutató példa

[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

Ez a cikk végigvezeti egy példa infrastruktúrát létrehozására. A Microsoft részletességi összegyűjti az összes irányelvek és elnevezési konvenciókat, a rendelkezésre állási csoportok, a virtuális hálózatok és terheléselosztók döntések egyszerű online tároló-infrastruktúrák tervezése, és ténylegesen telepítése az a virtuális gépek (VM).

## <a name="example-workload"></a>Példa munkaterhelés
Az Adventure Works Cycles szeretné az Azure-alkotó online áruházbeli alkalmazás létrehozásához:

* Egy webes réteghez az előtér-ügyfelet futtató két IIS-kiszolgálókkal
* Két IIS-kiszolgálókkal, adatokhoz és egy alkalmazás szint kérelmek feldolgozása
* Két Microsoft SQL Server-példány AlwaysOn rendelkezésre állási csoportokkal (két SQL-kiszolgáló és a legtöbb csomópont tanúsító) adatbázis-rétegből termék adatok és a rendeléseket tárolásához
* Két felhasználói fiókok és a szállítók egy hitelesítési réteg Active Directory-tartományvezérlők
* A kiszolgálók két alhálózat találhatók:
  * a webkiszolgálók előtér-alhálózatot 
  * az alkalmazás-kiszolgálókat, az SQL-fürt és a tartományvezérlők háttér-alhálózatot

![Alkalmazás-infrastruktúra különböző rétegek ábrája](./media/infrastructure-example/example-tiers.png)

Bejövő biztonságos webes forgalom kell lennie terhelésű a webkiszolgálók között, az ügyfelek, keresse meg az online áruházból. HTTP formájában forgalom feldolgozása sorrendben kéri le a webes kiszolgálók úgy kell meghatározni az alkalmazás-kiszolgálók között. Emellett az infrastruktúra úgy kell megtervezni a magas rendelkezésre állás érdekében.

Az eredményül kapott terv tartalmaznia kell:

* Egy Azure-előfizetés és a fiók
* Egyetlen erőforráscsoportként működnek
* Azure Managed Disks
* Két alhálózat virtuális hálózat
* A hasonló szerepet rendelkező virtuális gépek rendelkezésre állási készletek
* Virtual machines (Virtuális gépek)

Minden a fenti kövesse az alábbi elnevezési konvenciókat:

* Adventure Works Cycles használ **[informatikai munkaterhelés]-[hely]-[Azure-erőforrás]** előtagjaként
  * Ebben a példában a "**azos**" (Azure Online áruház) a az IT-munkaterhelést neve és a "**használja**" (USA keleti régiója 2) az a hely
* Virtuális hálózatok használata AZOS-használat-VN**[szám]**
* Rendelkezésre állási készletek használata azos-használata-,-**[szerepkör]**
* Virtuális gép nevét használja azos-használata-vm -**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Azure-előfizetések és fiókok
Az Adventure Works Cycles vállalati előfizetésének, az Adventure Works nagyvállalati előfizetéssel nevű segítségével adja meg a számlázás informatikai munkaterhelés.

## <a name="storage"></a>Storage
Az Adventure Works Cycles határozza meg, hogy azok Azure felügyelt lemezeket kell használnia. Virtuális gépek létrehozásakor rendelkezésre álló tár mindkét tárolórétegek használhatók:

* **Standard szintű tárolást** a webkiszolgálók, alkalmazás-kiszolgálókat, és a tartományvezérlők és az adatlemezek.
* **Prémium szintű storage** az SQL Server virtuális gépek és az adatlemezek.

## <a name="virtual-network-and-subnets"></a>Virtuális hálózat és alhálózatok
Mivel a virtuális hálózat nem kell a folyamatban lévő Adventure munkahelyi ciklusok a helyszíni hálózati kapcsolattal, akkor lezárását egy kizárólag felhőalapú virtuális hálózaton.

Hozza létre őket egy kizárólag felhőalapú virtuális hálózatot az Azure portál használatával a következő beállításokkal:

* Neve: AZOS-használat-VN01
* Helye: USA keleti régiója 2. régiója
* Virtuális hálózat címtere: 10.0.0.0/8
* Első alhálózati:
  * Name: előtér
  * Címtér: 10.0.1.0/24
* Második alhálózati:
  * Name: háttér
  * Címtér: 10.0.2.0/24

## <a name="availability-sets"></a>Rendelkezésre állási csoportok
Az online áruházból összes négy meghatározási magas rendelkezésre állás fenntartása érdekében Adventure Works Cycles úgy döntött, a négy rendelkezésre állási csoportok:

* **azos-használja-– weblapként** a webkiszolgálókhoz
* **azos-használat-,-alkalmazás** az alkalmazáskiszolgálók
* **azos-használat-,-sql** SQL-kiszolgálók
* **azos-használatát-szerint – dc** a tartományvezérlők

## <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
Az Adventure Works Cycles határozott meg a következő neveket az Azure virtuális gépek:

* **azos-használat-vm-web01** az első webkiszolgálón
* **azos-használat-vm-web02** a második webkiszolgáló
* **azos-használat-vm-app01** az első alkalmazás-kiszolgálón
* **azos-használat-vm-app02** a második alkalmazáskiszolgáló
* **azos-használat-vm-sql01** a fürt első SQL Server-kiszolgáló
* **azos-használat-vm-sql02** a második SQL Server-kiszolgáló a fürt
* **azos-használat-vm-dc01** az első tartományvezérlő
* **azos-használat-vm-dc02** a második tartományvezérlő

Ez a létrejövő konfigurációt.

![Az Azure-ban telepített alkalmazások végleges infrastruktúra](./media/infrastructure-example/example-config.png)

Ez a konfiguráció a következőket tartalmazza:

* Egy kizárólag felhőalapú virtuális hálózatot, két alhálózattal (előtér- és háttérszolgáltatások)
* Az Azure Managed lemezek a Standard és a Premium lemezek
* Négy rendelkezésre állási csoportok esetében egy, az egyes rétegekhez az online áruház
* A virtuális gépek négy rétegek
* A HTTPS-alapú webes forgalom az internetről webkiszolgálók egy külső elosztott terhelésű készlet
* Belső elosztott terhelésű készlet alkalmazáskiszolgálókra érkező titkosítatlan webes forgalom
* Egyetlen erőforráscsoportként működnek
