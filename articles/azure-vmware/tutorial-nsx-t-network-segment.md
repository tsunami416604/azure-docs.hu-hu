---
title: Oktatóanyag – NSX-T hálózati szegmens hozzáadása az Azure VMware-megoldásban
description: Megtudhatja, hogyan hozhat létre NSX-T hálózati szegmenst a virtuális gépekhez a vCenter-ben.
ms.topic: tutorial
ms.date: 11/09/2020
ms.openlocfilehash: 8ecb37a42e2986bd1c6261b8fe6c23382323b31d
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335048"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>Oktatóanyag: hálózati szegmens hozzáadása az Azure VMware megoldásban 

A vCenter-ben létrehozott virtuális gépek (VM-EK) az NSX-T-ben létrehozott hálózati szegmensekre kerülnek, és a vCenter láthatók.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hálózati szegmensek hozzáadásához navigáljon a NSX-T Managerben
> * Új hálózati szegmens hozzáadása
> * Figyelje meg az új hálózati szegmenst a vCenter-ben

## <a name="prerequisites"></a>Előfeltételek

Egy Azure VMware-megoldás saját felhője, amely hozzáféréssel rendelkezik a vCenter és a NSX-T Manager felületekhez. További információt a [hálózatkezelés konfigurálása](tutorial-configure-networking.md) oktatóanyagban talál.

## <a name="add-a-network-segment"></a>Hálózati szegmens hozzáadása

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy NSX-T hálózati szegmenst, amelyet a vCenter-ben lévő virtuális gépekhez használhat. 

A következő ismereteket sajátította el: 

- [DHCP létrehozása és kezelése az Azure VMware-megoldáshoz](manage-dhcp.md)
- [Tartalom-függvénytár létrehozása virtuális gépek üzembe helyezéséhez az Azure VMware-megoldásban](deploy-vm-content-library.md) 
- [Helyszíni helyi környezetek saját felhőhöz](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
