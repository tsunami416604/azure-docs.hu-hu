---
title: Böngészőkapcsolat engedélyezése az Azure DevTest Labs virtuális gépeken | Microsoft dokumentumok
description: A DevTest Labs mostantól integrálható az Azure Bastion szolgáltatással, a labor tulajdonosaként engedélyezheti az összes tesztkörnyezetbeli virtuális gép böngészőn keresztüli elérését.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: 074d512767769fe434cd4a6d4f4e8e6a88f7ed2a
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549116"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Böngészőkapcsolat engedélyezése tesztkörnyezetben lévő virtuális gépeken 
A DevTest Labs integrálható [az Azure Bastion](https://docs.microsoft.com/azure/bastion/)szolgáltatással, amely lehetővé teszi, hogy egy böngészőn keresztül csatlakozzon a virtuális gépekhez. Először engedélyeznie kell a böngészőkapcsolatot a tesztkörnyezetben lévő virtuális gépeken.

A tesztkörnyezet tulajdonosaként engedélyezheti az összes tesztkörnyezetbeli virtuális gép böngészőn keresztüli elérését. Nincs szüksége további ügyfélre, ügynökre vagy szoftverre. Az Azure Bastion biztonságos és zökkenőmentes RDP/SSH-kapcsolatot biztosít a virtuális gépekhez közvetlenül az Azure Portalon a TLS-en keresztül. Amikor az Azure Bastion on keresztül csatlakozik, a virtuális gépek nem kell egy nyilvános IP-címet. További információ: [Mi az Azure-bástya?](../bastion/bastion-overview.md)


Ez a cikk bemutatja, hogyan engedélyezheti a böngésző kapcsolatot a tesztkörnyezetben lévő virtuális gépeken.

## <a name="prerequisites"></a>Előfeltételek 
Telepítsen egy megerősített állomást a meglévő tesztkörnyezet virtuális hálózatában **(OR),** csatlakoztassa a tesztkörnyezetet egy megerősített konfigurált virtuális hálózattal. 

Ha tudni szeretné, hogyan helyezhet üzembe egy bástya-állomást egy virtuális hálózatban, olvassa el az [Azure Bastion gazdagép létrehozása című témakört.](../bastion/bastion-create-host-portal.md) A megerősített gazdagép létrehozásakor válassza ki a tesztkörnyezet virtuális hálózatát. 

Először létre kell hoznia egy második alhálózatot a bastion virtuális hálózatban, mert az AzureBastionSubnet nem engedélyezi a nem megerősített erőforrások létrehozását. 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>Második alhálózat létrehozása a Bastion virtuális hálózatban
Az Azure-bastion alhálózatban nem hozhat létre laborvirtuális gépeket. Hozzon létre egy másik alhálózatot a bastion virtuális hálózaton belül az alábbi képen látható módon:

![Második alhálózat az Azure Bastion virtuális hálózatában](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>Virtuális gép létrehozásának engedélyezése az alhálózatban
Most engedélyezze a virtuális gépek létrehozását ebben az alhálózatban az alábbi lépésekkel: 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza a bal oldali navigációs menü **Minden szolgáltatás lehetőséget.** 
1. Válassza a **DevTest Labs** elemet a listából. 
1. A laborok listájából válassza ki *a labort.* 

    > [!NOTE]
    > Az Azure Bastion mostantól általánosan elérhető a következő régiókban: USA nyugati régiói, USA keleti régiói, Nyugat-Európa, USA déli középső régiója, Kelet-Ausztrália és Kelet-Japán. Ezért hozzon létre egy tesztkörnyezetet az egyik ilyen régióban, ha a labor nincs az egyikben. 
    
1. A bal oldali menü **Beállítások** szakaszában válassza a **Konfiguráció és házirendek** lehetőséget. 
1. Válassza **a Virtuális hálózatok lehetőséget.**
1. Válassza az eszköztár **Hozzáadás parancsát.** 
1. Válassza ki azt a **virtuális hálózatot,** amelyen telepítve van a megerősített állomás. 
1. Válassza ki a virtuális gépek alhálózatát, nem pedig az **AzureBastionSubnet-et,** a korábban létrehozott at. Zárja be a lapot, és nyissa meg újra, ha nem látja az alhálózatot az alsó listában. 

    ![Virtuális gép létrehozásának engedélyezése az alhálózatban](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. Válassza **a Használat a virtuális gép létrehozásához** lehetőséget. 
1. Válassza az eszköztár **Save** (Mentés) elemét. 
1. Ha van egy régi virtuális hálózat a laborban, távolítsa el kiválasztásával **...*  és távolítsa el a **parancsot.** 

## <a name="enable-browser-connection"></a>Böngészőkapcsolat engedélyezése 

Miután rendelkezik egy bástya konfigurált virtuális hálózat a laborban, mint a labor tulajdonosa, engedélyezheti a böngésző csatlakozás labor virtuális gépeken.

A böngészőcsatlakozás engedélyezéséhez a tesztkörnyezetben lévő virtuális gépeken hajtsa végre az alábbi lépéseket:

1. Az Azure Portalon keresse meg *a labort.*
1. Válassza a **Konfiguráció és házirendek**lehetőséget.
1. A **Beállítások csoportban**válassza a **Böngésző csatlakozás lehetőséget.** Ha nem látja ezt a lehetőséget, zárja be a **Konfigurációs házirendek** lapot, és nyissa meg újra. 

    ![Böngészőkapcsolat engedélyezése](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Következő lépések
Az alábbi cikkből megtudhatja, hogyan csatlakozhat a virtuális gépekhez böngészővel: [Böngészőn keresztül csatlakozhat a virtuális gépekhez](connect-virtual-machine-through-browser.md)
