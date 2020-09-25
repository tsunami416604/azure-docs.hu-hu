---
title: VRealize-műveletek beállítása Azure VMware-megoldáshoz
description: Ismerje meg, hogyan állíthat be vRealize-műveleteket az Azure VMware-megoldás privát felhője számára.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 06b88eb610c4633018889315ab1cfd340d3f4b57
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91293151"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>VRealize-műveletek beállítása Azure VMware-megoldáshoz


a vRealize Operations Manager egy Operations Management platform, amely lehetővé teszi a VMware infrastruktúra-rendszergazdák számára a rendszererőforrások figyelését. Ezek a rendszererőforrások lehetnek alkalmazás-vagy infrastruktúra-szintű (fizikai és virtuális) objektumok. A legtöbb VMware-rendszergazda a vRealize-műveletek használatával figyeli és felügyeli a VMware Private Cloud Components – vCenter, ESXi, NSX-T, vSAN és hibrid felhő bővítményt (HCX). Minden Azure VMware-megoldás saját felhője dedikált vCenter, NSX-T, vSAN és HCX üzembe helyezéssel van kiépítve. 

Első lépésként alaposan [áttekintheti](#before-you-begin) az [előfeltételeket](#prerequisites) . Ezt követően végigvezeti a két tipikus üzembe helyezési topológián a vRealize Operations Manager Azure VMware megoldással:

> [!div class="checklist"]
> * [Azure VMware-megoldás üzembe helyezését kezelő helyszíni vRealize-műveletek](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [Az Azure VMware-megoldás üzembe helyezésén futó vRealize-műveletek](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>Előkészületek
* Tekintse át a [vRealize Operations Manager termék dokumentációját](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) , és ismerkedjen meg a VRealize-műveletek telepítésével. 
* Tekintse át az alapszintű Azure VMware Solution Datacenter (SDDC) [oktatóanyag-sorozatot](tutorial-network-checklist.md).
* Ha szeretné, tekintse át a [VRealize Operations Remote Controller](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) termékdokumentációt az Azure VMware-megoldás üzembe helyezési lehetőségeit kezelő helyszíni VRealize-műveletekhez. 



## <a name="prerequisites"></a>Előfeltételek
* A VPN-t vagy az Azure-ExpressRoute a helyszíni és az Azure VMware megoldás SDDC kell konfigurálni.
* Azure-beli VMware-megoldás saját felhő üzembe helyezése az Azure-ban.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>Azure VMware-megoldás üzembe helyezését kezelő helyszíni vRealize-műveletek
A legtöbb ügyfél rendelkezik egy vagy több helyszíni vCenter felügyeletéhez használt vRealize-művelet meglévő helyszíni telepítésével. Amikor az ügyfelek egy új Azure VMware-megoldást használnak az Azure-ban, általában az Azure-ExpressRoute vagy egy 3. rétegbeli VPN-megoldás használatával csatlakoznak a helyszíni környezethez az Azure VMware-megoldással, az alább látható módon.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Azure VMware-megoldás üzembe helyezését kezelő helyszíni vRealize-műveletek"  border="false":::

Ahhoz, hogy a vRealize-üzemeltetési funkciókat az Azure VMware-megoldás privát felhője számára kiterjessze, létre kell hoznia egy adapter- [példányt az Azure VMware-megoldás saját felhőalapú erőforrásaihoz](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) – adatokat gyűjthet az Azure VMware-megoldás saját felhőből, és üzembe helyezheti azokat a helyszíni VRealize-műveletekben. A helyszíni vRealize Operations Manager példány közvetlenül kapcsolódhat a vCenter és a NSX-T kezelőhöz az Azure VMware megoldásban, vagy opcionálisan telepítheti a vRealize Operations Remote Collectort az Azure VMware Solution Private Cloud szolgáltatásban. A vRealize Operations Remote Collector tömöríti és titkosítja az Azure VMware-megoldás privát felhőből gyűjtött adatokat, mielőtt a ExpressRoute vagy a VPN-hálózaton átküldi a helyszíni rendszeren futó vRealize-Operations Manager. 

> [!TIP]
> A vRealize-Operations Manager telepítésének részletes útmutatóját a [VMware dokumentációjában](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) találja. 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>Az Azure VMware-megoldás üzembe helyezésén futó vRealize-műveletek

Egy másik üzembe helyezési lehetőség a Operations Manager vRealize-példány üzembe helyezése az Azure VMware-megoldás saját vSphere egyik-fürtjén, az alábbiakban látható módon. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Azure VMware-megoldáson futó vRealize-műveletek" border="false":::

A vRealize-műveletek Azure VMware Solution példányának üzembe helyezése után vRealize műveleteket konfigurálhat a vCenter, ESXi, NSX-T, vSAN és HCX adatok gyűjtéséhez. 

> [!TIP]
> A vRealize-Operations Manager telepítésének részletes útmutatóját a [VMware dokumentációjában](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) találja.


## <a name="known-limitations"></a>Ismert korlátozások

- Az **cloudadmin@vsphere.local** Azure VMware-megoldás felhasználója [korlátozott jogosultságokkal](concepts-rbac.md)rendelkezik. A VMware-eszközöket használó, a vendégen belüli memória-gyűjtemény nem támogatott az Azure VMware-megoldás virtuális gépei (VM-EK) használatával. Ebben az esetben az aktív és a felhasznált memória kihasználtsága továbbra is működik.
- A gazdagép-alapú üzleti szándékok munkaterhelésének optimalizálása nem működik, mert az Azure VMware-megoldások kezelik a fürt konfigurációit, beleértve a DRS beállításait is.
- A Operations Manager vRealize-alapú üzleti szándék használatával történő SDDC a fürtön belüli elhelyezésének munkaterhelési optimalizálása teljes mértékben támogatott a 8,0-es és újabb verziókban. A számítási feladatok optimalizálása azonban nem ismeri az erőforrás-készleteket, és a virtuális gépeket a fürt szintjén helyezi el. A felhasználó manuálisan is kijavítani ezt az Azure VMware-megoldás vCenter Server felületén.
- Az Azure VMware-megoldás vCenter Server hitelesítő adataival nem tud bejelentkezni a vRealize Operations Managerba. 
- Az Azure VMware-megoldás nem támogatja a vRealize Operations Manager beépülő modult.

Ha vCenter Server Cloud-fiókkal csatlakozik az Azure VMware-megoldás vCenter a Operations Manager vRealize-hez, a következő figyelmeztetés jelenik meg:

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="A figyelmeztetési adapter példányának létrehozása sikerült":::

A figyelmeztetés azért fordul elő **cloudadmin@vsphere.local** , mert az Azure VMware-megoldás felhasználója nem rendelkezik megfelelő jogosultsággal ahhoz, hogy elvégezze a regisztrációhoz szükséges összes vCenter Server műveletet. A jogosultságok azonban elegendőek ahhoz, hogy az adapter példánya adatgyűjtést hajtson végre, ahogy az alábbi képen is látható:

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="Az adatgyűjtést végző adapter-példány":::

További információ: vCenter- [adapter példányának konfigurálásához szükséges jogosultságok](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html).

<!-- LINKS - external -->


<!-- LINKS - internal -->




