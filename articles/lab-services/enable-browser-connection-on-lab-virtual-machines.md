---
title: Böngészőalapú kapcsolatok engedélyezése Azure DevTest Labs virtuális gépeken | Microsoft Docs
description: A DevTest Labs mostantól integrálva van az Azure Bastion szolgáltatással, a labor tulajdonosaként pedig engedélyezheti a tesztkörnyezet összes virtuális gépe elérését egy böngészőben.
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
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 2ddc56c60c547bd4ce48d620a83fb79246762bfb
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642486"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Böngészőalapú kapcsolatok engedélyezése a labor virtuális gépeken 

A DevTest Labs integrálható az [Azure Bastion](https://docs.microsoft.com/azure/bastion/)szolgáltatással, amely lehetővé teszi a virtuális gépekhez való kapcsolódást egy böngészőben. Először engedélyeznie kell a böngészőalapú kapcsolódást a labor virtuális gépeken.

A labor tulajdonosaként engedélyezheti az összes Tesztkörnyezet virtuális gép elérését egy böngészőben. Nincs szüksége további ügyfélre, ügynökre vagy szoftverre. Az Azure Bastion biztonságos és zökkenőmentes RDP/SSH-kapcsolatot biztosít a virtuális gépekhez közvetlenül a Azure Portal SSL-en keresztül. Amikor az Azure Bastion-n keresztül kapcsolódik, a virtuális gépeknek nincs szükségük nyilvános IP-címekre. További információ: [Mi az az Azure Bastion?](../bastion/bastion-overview.md)

> [!NOTE]
> A böngészőalapú kapcsolatok engedélyezése a labor virtuális gépeken előzetes verzióban érhető el.

Ebből a cikkből megtudhatja, hogyan engedélyezheti a böngészőalapú kapcsolatokat a labor virtuális gépeken.

## <a name="prerequisites"></a>Előfeltételek 
Helyezzen üzembe egy megerősített gazdagépet a meglévő labor virtuális hálózatában **(vagy)** a labort egy megerősített konfigurált VNet. 

Ha meg szeretné tudni, hogyan helyezhet üzembe egy megerősített gazdagépet egy VNet, tekintse meg [Az Azure Bastion-gazdagép (előzetes verzió) létrehozását](../bastion/bastion-create-host-portal.md)ismertető témakört. A megerősített gazdagép létrehozásakor válassza ki a labor virtuális hálózatát. 

Ha meg szeretné tudni, hogyan csatlakoztatható a labor egy megerősített VNet, tekintse meg [a virtuális hálózat konfigurálása Azure DevTest Labsban](devtest-lab-configure-vnet.md)című témakört. Válassza ki azt a VNet, amelyen telepítve van a megerősített gazdagép, valamint a **AzureBastionSubnet** . A részletes lépések a következők: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs menüben válassza a **minden szolgáltatás** lehetőséget. 
1. Válassza a **DevTest Labs** elemet a listából. 
1. A Labs listából válassza ki *a labort*. 

    > [!NOTE]
    > Az Azure Bastion jelenleg előzetes verzióban érhető el. A következő régiókra korlátozódik: USA nyugati régiója, USA keleti régiója, Nyugat-Európa, az USA déli középső régiója, Kelet-Ausztrália és Kelet-Japán. Ezért hozzon létre egy labort ezekben a régiókban, ha a labor egyike sincs bennük. 
1. A bal oldali menüben a **Beállítások** szakaszban válassza a **konfiguráció és szabályzatok** lehetőséget. 
1. Válassza a **virtuális hálózatok**lehetőséget.
1. Válassza a **Hozzáadás** lehetőséget az eszköztáron. 
1. Válassza ki azt a **VNet** , amelyen telepítve van a megerősített gazdagép. 
1. Válassza ki az alhálózatot: **AzureBastionSubnet**. 

    ![Subnet](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. Válassza **a használat a virtuális gép létrehozásakor** lehetőséget. 
1. Válassza az eszköztár **Save** (Mentés) elemét. 
1. Ha a laborhoz már van egy régi VNet, a * *...* gombra kattintva távolítsa el.  és **távolítsa el**. 

## <a name="enable-browser-connection"></a>Böngészőalapú kapcsolatok engedélyezése 

Miután a laborban beállította a megerősített VNet, a tesztkörnyezet tulajdonosaként engedélyezheti a böngészőhöz való csatlakozási szolgáltatást.

Az alábbi lépéseket követve engedélyezheti a böngésző kapcsolódását a labor virtuális gépeken:

1. A Azure Portal navigáljon a *laborhoz*.
1. Válassza **a konfiguráció és szabályzatok**lehetőséget.
1. A **Beállítások**területen válassza a **böngészőbeli kapcsolat (előzetes verzió)** lehetőséget.

![Böngészőalapú kapcsolatok engedélyezése](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>További lépések
A következő cikkből megtudhatja, hogyan csatlakozhat a virtuális gépekhez egy böngésző használatával: [Kapcsolódás a virtuális gépekhez egy böngészőben](connect-virtual-machine-through-browser.md)