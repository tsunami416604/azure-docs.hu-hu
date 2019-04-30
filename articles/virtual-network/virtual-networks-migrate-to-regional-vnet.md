---
title: Migrálás affinitáscsoportból régióba az Azure virtuális hálózat (klasszikus) |} A Microsoft Docs
description: 'Útmutató: virtuális hálózat (klasszikus) migrálás affinitáscsoportból régióba.'
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 84febcb9-bb8b-4e79-ab91-865ad9de41cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: d3bb93d12a217e6d9066d037ff92f071b6139ab3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648635"
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Migrálás affinitáscsoportból régióba egy virtuális hálózat (klasszikus)

> [!IMPORTANT]
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén használja a Resource Manager üzemi modell.

Az affinitáscsoportok győződjön meg arról, hogy ugyanahhoz az affinitáscsoporthoz belül létrehozott erőforrás, amely egymáshoz közel, ezekhez az erőforrásokhoz való kommunikációhoz gyorsabb engedélyezése kiszolgálók fizikailag működtetnek. Múltbeli időpont a virtuális hálózatok (klasszikus) létrehozására vonatkozó követelmény volt affinitáscsoportokra. Ugyanakkor a virtuális hálózatok (klasszikus) felügyelt hálózati manager szolgáltatás csak működhetnek a fizikai kiszolgálók vagy a skálázási egység belül. Architekturális fejlesztéseket régióba hálózati kezelési hatókör nőtt.

Ezek a architekturális fejlesztések eredményeként affinitáscsoportok már nem ajánlott, vagy virtuális hálózatok (klasszikus) szükséges. A az affinitáscsoportok használatát a virtuális hálózatok (klasszikus) régióban váltotta fel. Virtuális hálózatok (klasszikus) társított régiók regionális virtuális hálózatok nevezzük.

Azt javasoljuk, hogy általában nem használ affinitáscsoportokat. Azokat a virtuális hálózat követelmény, kivéve az affinitáscsoportok is fontos, hogy ellenőrizze az erőforrások, például a (klasszikus) számítási és tárolási (klasszikus) használatával, egymáshoz közel kerültek. Azonban az aktuális Azure-beli hálózati architektúra az elhelyezési követelménynek nem lesznek szükséges.

> [!IMPORTANT]
> Bár ez továbbra is lehetséges az hozzon létre egy virtuális hálózatot, amely egy affinitáscsoporthoz tartozik, nem indokolt meggyőző ennek a végrehajtására. Sok virtuális hálózati funkciók, például a hálózati biztonsági csoportok csak érhetők el egy regionális virtuális hálózat használata esetén, és nem érhetők el az affinitáscsoportok társított virtuális hálózatok.
> 
> 

## <a name="edit-the-network-configuration-file"></a>A hálózati konfigurációs fájl szerkesztése

1. Exportálhatja a hálózati konfigurációs fájlt. Ismerje meg, hogyan exportálhatja a hálózati konfigurációs fájlt a PowerShell vagy az Azure parancssori felület (CLI), 1.0-t, tekintse meg [egy virtuális hálózatot a hálózati konfigurációs fájl segítségével konfigurálja](virtual-networks-using-network-configuration-file.md#export).
2. Módosítsa a hálózati konfigurációs fájlt, és cserélje le **AffinityGroup** a **hely**. Megadhatja az Azure-beli [régió](https://azure.microsoft.com/regions) a **hely**.
   
   > [!NOTE]
   > A **hely** a régió, a virtuális hálózat (klasszikus) társított az affinitáscsoport megadott. Ha a virtuális hálózat (klasszikus) társítva egy affinitáscsoportban található USA nyugati RÉGIÓJA, ha telepít át, például a **hely** kell mutatnia, USA nyugati RÉGIÓJA. 
   > 
   > 
   
    A következő sorokat a hálózati konfigurációs fájlt, és cserélje le az értékeket saját szerkesztése: 
   
    **Régi érték:** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\> 
   
    **Új érték:** \<VirtualNetworkSitename="VNetUSWest" Location="West US"\>
3. A módosítások mentéséhez és [importálása](virtual-networks-using-network-configuration-file.md#import) a hálózati konfigurációt, az Azure-bA.

> [!NOTE]
> Az áttelepítés esetén nem tapasztalnak állásidőt az Ön felhőszolgáltatásainak.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>Mi a teendő, ha egy affinitáscsoportban található virtuális gépek (klasszikus)
Virtuális gépek az (klasszikus), amely jelenleg egy affinitáscsoportban található nem távolítható el az affinitáscsoportot kell. Virtuális gép üzembe helyezése után egy egyetlen skálázási egység van telepítve. Affinitáscsoportok korlátozhatja az egy új virtuális gép üzembe helyezésének elérhető Virtuálisgép-méretek készletét, de bármely meglévő virtuális Gépet üzembe helyezett már korlátozódik, a skálázási egység, amely a virtuális gép üzembe lesz helyezve az elérhető Virtuálisgép-méretek készletét. A virtuális gép már telepítve van egy méretezési egység, mert affinitáscsoport egy virtuális gép eltávolítása nem befolyásolja a virtuális Gépet.
