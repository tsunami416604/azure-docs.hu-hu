---
title: Oktatóanyag – hozzáférés saját felhőhöz
description: Ismerje meg, hogyan férhet hozzá egy Azure VMware-megoldás saját felhőhöz
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: b7f21dbba5256712607241bb1258962fc22418fb
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578418"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>Oktatóanyag: Azure VMware-megoldás privát felhőhöz való hozzáférése

Az Azure VMware-megoldás nem teszi lehetővé, hogy a helyszíni vCenter kezelhesse a saját felhőjét. A helyi vCenter-példányhoz további beállítás és kapcsolódás szükséges a Jump Box használatával. 

Ebben az oktatóanyagban létrehoz egy Jump Box-t az [előző oktatóanyagban](tutorial-configure-networking.md) létrehozott erőforráscsoporthoz, és bejelentkezik a vCenter. A Jump Box egy Windows rendszerű virtuális gép (VM) ugyanazon a virtuális hálózaton, amelyet Ön hozott létre.  Hozzáférést biztosít a vCenter és a NSX Managerhez. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A vCenter való kapcsolódáshoz használandó Windows rendszerű virtuális gép létrehozása
> * Bejelentkezés a vCenter a virtuális gépről

## <a name="create-a-new-windows-virtual-machine"></a>Új Windows rendszerű virtuális gép létrehozása

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Kapcsolódás a saját felhő helyi vCenter

1. A Jump (ugrás) mezőben jelentkezzen be a vSphere-ügyfélre a VMware vCenter SSO használatával egy Felhőbeli rendszergazdai felhasználónévvel és valósággal, hogy a felhasználói felület sikeresen megjelenik.

1. A Azure Portal válassza ki saját felhőjét, majd az **Áttekintés** nézetben válassza az **identitás > alapértelmezett**lehetőséget. 

   A Private Cloud vCenter és a NSX-T Manager kijelző URL-címei és felhasználói hitelesítő adatai.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Saját Felhőbeli vCenter és NSX-kezelő URL-címek és hitelesítő adatok megjelenítése." border="true":::

1. Navigáljon az előző lépésben létrehozott virtuális GÉPRE, és kapcsolódjon a virtuális géphez. 

   Ha segítségre van szüksége a virtuális GÉPHEZ való csatlakozáshoz, a részletekért lásd: [Csatlakozás virtuális géphez](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) .

1. A Windows rendszerű virtuális gépen nyisson meg egy böngészőt, és navigáljon a vCenter és a NSX-T jászol URL-címeihez két lapon. 

1. A vCenter lapon adja meg az `cloudadmin@vmcp.local` előző lépésben használt felhasználói hitelesítő adatokat.

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Saját Felhőbeli vCenter és NSX-kezelő URL-címek és hitelesítő adatok megjelenítése." border="true":::

   :::image type="content" source="media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="Saját Felhőbeli vCenter és NSX-kezelő URL-címek és hitelesítő adatok megjelenítése." border="true":::

1. A böngésző második lapján jelentkezzen be a NSX-T Managerbe.

   :::image type="content" source="media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Saját Felhőbeli vCenter és NSX-kezelő URL-címek és hitelesítő adatok megjelenítése." border="true":::



## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A vCenter való kapcsolódáshoz használandó Windows rendszerű virtuális gép létrehozása
> * Bejelentkezés a vCenter a virtuális gépről

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre virtuális hálózatot a saját felhőalapú fürtök helyi felügyeletének beállításához.

> [!div class="nextstepaction"]
> [Virtual Network létrehozása](tutorial-configure-networking.md)


