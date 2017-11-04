---
title: "Egy Azure virtuális hálózat (klasszikus) telepítenek át egy affinitáscsoporthoz egy régiót |} Microsoft Docs"
description: "Megtudhatja, hogyan kell áttelepíteni egy virtuális hálózat (klasszikus) affinitáscsoport egy régiót."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 84febcb9-bb8b-4e79-ab91-865ad9de41cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: b9b3bd0f2184ac85261166d5fe2ab67e1bf319d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Virtuális hálózat (klasszikus) telepítenek át egy affinitáscsoporthoz terület

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén használja a Resource Manager üzembe helyezési modellben.

Affinitáscsoportok győződjön meg arról, hogy erőforrásokat ugyanabban az affinitáscsoportban jött létre, amely közel vannak egymáshoz, ezekhez az erőforrásokhoz való kommunikációhoz gyorsabb engedélyezése kiszolgálók fizikailag működtetnek. A múltban a virtuális hálózatok (klasszikus) létrehozására vonatkozó követelmény volt affinitáscsoportokra. Idő lejárta után a virtuális hálózatok (klasszikus) kezelt hálózati manager szolgáltatás esetleg csak fog működni a fizikai kiszolgálók vagy a méretezési egység belül. Az architektúra fejlesztései régióban hálózati kezelési hatókör növekedett.

Architekturális fejlesztéseknek miatt affinitáscsoportok már nem ajánlott, vagy a virtuális hálózatok (klasszikus) szükséges. Virtuális hálózatok (klasszikus) az affinitáscsoportok használatát régiók váltotta fel. Virtuális hálózatok (klasszikus) régiók társított regionális virtuális hálózatokba nevezzük.

Azt javasoljuk, hogy általában nem használ affinitáscsoportokat. A virtuális hálózati követelmény vezérelt affinitáscsoportok is fontos, hogy használatával győződjön meg arról, például (klasszikus) számítási és tárolási (klasszikus) erőforrásokhoz, kerültek egymást. Azonban az aktuális Azure hálózati architektúra elhelyezési követelménynek már nincs szükség.

> [!IMPORTANT]
> Bár továbbra is technikailag lehetséges hozzon létre egy virtuális hálózatot, amely egy affinitáscsoporthoz tartozik, nincs ehhez kényszerítő ok. Számos virtuális hálózati szolgáltatás, például a hálózati biztonsági csoportok, csak elérhető regionális virtuális hálózat használatával, és nem érhetők el az affinitáscsoportok társított virtuális hálózatok.
> 
> 

## <a name="edit-the-network-configuration-file"></a>A hálózati konfigurációs fájl szerkesztése

1. A hálózati konfigurációs fájl exportálása. A PowerShell vagy az Azure parancssori felület (CLI) 1.0-s hálózati konfigurációs fájl exportálása további tudnivalókért lásd: [hálózati konfigurációs fájl használatával virtuális hálózat konfigurálása](virtual-networks-using-network-configuration-file.md#export).
2. A hálózati konfigurációs fájl szerkesztésével cseréje **AffinityGroup** rendelkező **hely**. Megad egy Azure [régió](https://azure.microsoft.com/regions) a **hely**.
   
   > [!NOTE]
   > A **hely** a régió, az affinitáscsoport, a virtuális hálózat (klasszikus) társított megadott. Például, ha a virtuális hálózat (klasszikus) társítva, amely áttelepítésekor, USA nyugati régiója található affinitáscsoport a **hely** USA nyugati régiója kell mutatnia. 
   > 
   > 
   
    Szerkessze a következő sorokat az az értékeket a saját cserélje le a hálózati konfigurációs fájlban: 
   
    **Régi érték:** \<VirtualNetworkSitename = "VNetUSWest" AffinityGroup = "VNetDemoAG"\> 
   
    **Új érték:** \<VirtualNetworkSitename = "VNetUSWest" Location "USA nyugati régiója" =\>
3. A módosítások mentéséhez és [importálása](virtual-networks-using-network-configuration-file.md#import) a hálózati konfigurációt, az Azure-bA.

> [!NOTE]
> Az áttelepítés esetén nem tapasztalnak állásidőt a szolgáltatásokhoz.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>Mi a teendő, ha az affinitáscsoportokban lévő virtuális gép (klasszikus)
VMs (klasszikus), amely jelenleg affinitáscsoportokban nem távolítható el az affinitáscsoport kell. Ha egy virtuális Gépet telepít, a rendszer egy méretezési egység van telepítve. Affinitáscsoportok lehet korlátozni elérhető Virtuálisgép-méretek egy új virtuális gép üzembe helyezéséhez, de bármely telepített meglévő virtuális gép már korlátozott, hogy a Virtuálisgép-méretek érhető el a méretezési egység, amely a virtuális gép van telepítve. A virtuális gép már telepítve van a méretezési egység, mert a virtuális gép eltávolítása egy affinitáscsoporthoz hatástalan, a virtuális Gépen.
