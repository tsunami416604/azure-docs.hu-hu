---
title: VRealize-műveletek beállítása Azure VMware-megoldáshoz (AVS)
description: ''
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 57b513b681c4d2522b1c92946aab80fe8ba95746
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476069"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution-avs"></a>VRealize-műveletek beállítása Azure VMware-megoldáshoz (AVS)


a vRealize Operations Manager egy Operations Management platform, amely lehetővé teszi a VMware infrastruktúra-rendszergazdák számára a rendszererőforrások figyelését. Ezek a rendszererőforrások lehetnek alkalmazás-vagy infrastruktúra-szintű (fizikai és virtuális) objektumok. A legtöbb VMware-rendszergazda a vRealize-műveletek használatával figyeli és felügyeli a VMware Private Cloud Components – vCenter, ESXi, NSX-T, vSAN és hibrid felhő bővítményt (HCX). Mindegyik AVS Private Cloud egy dedikált vCenter, a NSX-T, a vSAN és a HCX üzembe helyezéssel van kiépítve. 

Első lépésként alaposan [áttekintheti](#before-you-begin) az [előfeltételeket](#prerequisites) . Ezt követően végigvezeti a két tipikus üzembe helyezési topológián a vRealize Operations Manager az AVS használatával:

> [!div class="checklist"]
> * [Az AVS üzembe helyezését kezelő helyszíni vRealize-műveletek](#on-premises-vrealize-operations-managing-avs-deployment)
> * [az AVS-telepítésen futó vRealize-műveletek](#vrealize-operations-running-on-avs-deployment)

## <a name="before-you-begin"></a>Előkészületek
* Tekintse át a [vRealize Operations Manager termék dokumentációját](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) , és ismerkedjen meg a VRealize-műveletek telepítésével. 
* Tekintse át az alapszintű AVS szoftverrel definiált adatközpont (SDDC) [oktatóanyag-sorozatot](tutorial-network-checklist.md).
* Ha kívánja, tekintse át a [VRealize Operations Remote Controller](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) termékdokumentációt az AVS üzembe helyezési lehetőségeit kezelő helyszíni VRealize-műveletekhez. 



## <a name="prerequisites"></a>Előfeltételek
* A VPN-vagy az Azure-ExpressRoute a helyszíni és az AVS-SDDC között kell konfigurálni.
* Az AVS Private Cloud üzembe helyezése az Azure-ban történt.



## <a name="on-premises-vrealize-operations-managing-avs-deployment"></a>Az AVS üzembe helyezését kezelő helyszíni vRealize-műveletek
A legtöbb ügyfél rendelkezik egy vagy több helyszíni vCenter felügyeletéhez használt vRealize-művelet meglévő helyszíni telepítésével. Amikor az ügyfelek egy új AVS Private-felhőt helyeznek üzembe az Azure-ban, általában az Azure ExpressRoute vagy egy 3. rétegbeli VPN-megoldás használatával csatlakoznak a helyszíni környezethez az AVS használatával, az alább látható módon.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Az AVS üzembe helyezését kezelő helyszíni vRealize-műveletek"  border="false":::

Az vRealize működési képességeinek az AVS Private-felhőbe való kiterjesztéséhez létre kell hoznia egy adapter-[ példányt az AVS Private Cloud-erőforrásokhoz](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) – az AVS Private Cloud-ból származó adatok gyűjtéséhez, és a helyszíni vRealize műveletekhez. A helyszíni vRealize Operations Manager példány közvetlenül kapcsolódhat a vCenter és a NSX-T kezelőhöz az AVS-ben, vagy opcionálisan telepítheti a vRealize Operations Remote Collectort az AVS Private Cloud szolgáltatásban. A vRealize Operations Remote Collector az AVS Private Cloud-ból gyűjtött adatokat tömöríti és titkosítja, mielőtt a ExpressRoute vagy a VPN-hálózaton átküldi a helyszíni rendszeren futó vRealize-Operations Manager. 

> [!TIP]
> A vRealize-Operations Manager telepítésének részletes útmutatóját a [VMware dokumentációjában](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) találja. 



## <a name="vrealize-operations-running-on-avs-deployment"></a>az AVS-telepítésen futó vRealize-műveletek

Egy másik üzembe helyezési lehetőség a Operations Manager vRealize-példány üzembe helyezése az AVS Private Cloud egyik vSphere-fürtjén, az alább látható módon. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="az AVS-on futó vRealize-műveletek" border="false":::

A vRealize-műveletek AVS-példányának telepítése után vRealize műveleteket konfigurálhat a vCenter, ESXi, NSX-T, vSAN és HCX adatok gyűjtéséhez. 

> [!TIP]
> A vRealize-Operations Manager telepítésének részletes útmutatóját a [VMware dokumentációjában](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) találja.




<!-- LINKS - external -->


<!-- LINKS - internal -->




