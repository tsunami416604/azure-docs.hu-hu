---
title: Azure-beli virtuális hálózat (klasszikus) migrálása az affinitási csoportból egy régióba | Microsoft Docs
description: Megtudhatja, hogyan telepíthet át egy virtuális hálózatot (klasszikus) egy affinitási csoportból egy régióba.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: d33d9ec4eadeaa3a082103f1ad699e2fc3010e3b
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058398"
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Virtuális hálózat (klasszikus) áttelepítése egy affinitási csoportból egy régióba

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy a legtöbb új központi telepítés a Resource Manager-alapú üzemi modellt használja.

Az affinitási csoportok biztosítják, hogy az egyazon affinitási csoporton belül létrehozott erőforrásokat fizikailag a egymáshoz közeledő kiszolgálók üzemeltetik, így ezek az erőforrások gyorsabban kommunikálhatnak. A múltban az affinitási csoportok a virtuális hálózatok (klasszikus) létrehozásához szükségesek voltak. Ekkor a virtuális hálózatok (klasszikus) felügyelt hálózati kezelő szolgáltatás csak fizikai kiszolgálókon vagy méretezési egységen belül működhet. Az architektúra fejlesztése megnövelte a hálózatkezelés hatókörét egy adott régióban.

Ezen architektúra-tökéletesítések eredményeképpen az affinitási csoportok már nem ajánlottak, vagy a virtuális hálózatok (klasszikus) esetében szükségesek. A virtuális hálózatok (klasszikus) affinitási csoportjainak használatát a régiók váltja fel. A régiókkal társított virtuális hálózatok (klasszikus) neve regionális virtuális hálózatok.

Azt javasoljuk, hogy ne használjon affinitási csoportokat általánosságban. A virtuális hálózat követelményeitől eltekintve az affinitási csoportok is fontosak voltak az erőforrások, például a számítás (klasszikus) és a tárolás (klasszikus) biztosításához. Az aktuális Azure hálózati architektúrával azonban ezek az elhelyezési követelmények már nem szükségesek.

> [!IMPORTANT]
> Habár még műszakilag lehetséges az affinitási csoporttal társított virtuális hálózatok létrehozása, ennek nincs kényszerítő oka. Számos virtuális hálózati szolgáltatás, például a hálózati biztonsági csoportok csak regionális virtuális hálózatok használata esetén érhetők el, és nem érhetők el az affinitási csoportokhoz társított virtuális hálózatokhoz.
> 
> 

## <a name="edit-the-network-configuration-file"></a>A hálózati konfigurációs fájl szerkesztése

1. Exportálja a hálózati konfigurációs fájlt. A hálózati konfigurációs fájlok a PowerShell vagy az Azure parancssori felület (CLI) 1,0 használatával történő exportálásával kapcsolatban lásd: [virtuális hálózat konfigurálása hálózati konfigurációs fájl használatával](virtual-networks-using-network-configuration-file.md#export).
2. Szerkessze a hálózati konfigurációs fájlt, és cserélje le a **AffinityGroup** **helyet**. Meg kell adnia egy Azure- [régiót](https://azure.microsoft.com/regions) a **helyhez**.
   
   > [!NOTE]
   > A **hely** az a régió, amelyet a virtuális hálózathoz (klasszikus) társított affinitási csoporthoz adott meg. Ha például a virtuális hálózat (klasszikus) az USA nyugati régiójában található affinitási csoporttal van társítva, akkor az áttelepítés során a helynek az USA nyugati **régiójának** kell mutatnia. 
   > 
   > 
   
    Szerkessze a következő sorokat a hálózati konfigurációs fájlban, és cserélje le az értékeket a saját értékeire: 
   
    **Régi érték:** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\> 
   
    **Új érték:** \<VirtualNetworkSitename="VNetUSWest" Location="West US"\>
3. Mentse a módosításokat, és [importálja](virtual-networks-using-network-configuration-file.md#import) a hálózati konfigurációt az Azure-ba.

> [!NOTE]
> Ez az áttelepítés nem okoz leállást a szolgáltatásokhoz.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>Mi a teendő, ha van egy virtuális gép (klasszikus) egy affinitási csoportban
A jelenleg egy affinitási csoportban lévő virtuális gépeket (klasszikus) nem kell eltávolítani az affinitási csoportból. A virtuális gép üzembe helyezését követően egyetlen méretezési egységbe kerül. Az affinitási csoportok korlátozhatják a rendelkezésre álló virtuálisgép-méretek készletét egy új virtuálisgép-telepítéshez, de az üzembe helyezett meglévő virtuális gépek már csak a virtuális gép üzembe helyezési egységében elérhető virtuálisgép-méretek készletére korlátozódnak. Mivel a virtuális gép már telepítve van egy méretezési egységre, a virtuális gép egy affinitási csoportból való eltávolítása nincs hatással a virtuális gépre.
