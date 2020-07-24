---
title: Oktatóanyag – vSphere-fürt üzembe helyezése az Azure-ban
description: Ismerje meg, hogyan helyezhet üzembe vSphere-fürtöt az Azure-ban az Azure VMWare-megoldás (AVS) használatával
ms.topic: tutorial
ms.date: 07/15/2020
ms.openlocfilehash: 4f3b33ea401c62124ae5f8a4c881d86d2f19b40c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079417"
---
# <a name="tutorial-deploy-an-avs-private-cloud-in-azure"></a>Oktatóanyag: AVS Private Cloud üzembe helyezése az Azure-ban

Az Azure VMware-megoldás (AVS) lehetővé teszi, hogy vSphere-fürtöt helyezzen üzembe az Azure-ban. A minimális kezdeti üzembe helyezés három gazdagép. A további gazdagépek egyenként is hozzáadhatók, legfeljebb 16 gazdagépre. 

Mivel az AVS nem teszi lehetővé a privát felhő kezelését a helyszíni vCenter a indításkor, a helyi vCenter-példányhoz, a virtuális hálózathoz és további kapcsolódáshoz szükséges további konfigurációt és kapcsolatokat. Ezek az eljárások és a kapcsolódó előfeltételek az oktatóanyagban találhatók.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * AVS-magánfelhő létrehozása
> * A privát felhő üzembe helyezésének ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Megfelelő rendszergazdai jogosultságok és engedélyek a privát felhő létrehozásához.
- Győződjön meg arról, hogy a megfelelő hálózatkezelés konfigurálva van az [oktatóanyag: hálózati ellenőrzőlista](tutorial-network-checklist.md)című témakörben leírtak szerint.

## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása

Az AVS használatához először regisztrálnia kell az erőforrás-szolgáltatót az előfizetésében.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Az erőforrás-szolgáltató regisztrálásának további módjaiért tekintse meg az [Azure erőforrás-szolgáltatók és-típusok](../azure-resource-manager/management/resource-providers-and-types.md)című témakört.


## <a name="create-a-private-cloud"></a>Magánfelhő létrehozása

A [Azure Portal](#azure-portal) vagy az [Azure CLI](#azure-cli)használatával létrehozhat egy AVS Private-felhőt.

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza **az új erőforrás létrehozása**lehetőséget. A **Keresés a piactér** szövegmezőbe mezőbe írja be `Azure VMware Solution` az **Azure VMware megoldás** elemet a listából. Az **Azure VMware megoldás** ablakban válassza a **Létrehozás** lehetőséget.

1. Az **alapvető beállítások** lapon adja meg a mezők értékeit. A következő táblázat a mezők tulajdonságait sorolja fel.

   | Mező   | Érték  |
   | ---| --- |
   | **Előfizetés** | Az üzembe helyezéshez használni kívánt előfizetés.|
   | **Erőforráscsoport** | A saját felhőalapú erőforrásainak erőforráscsoport. |
   | **Hely** | Válasszon egy helyet, például az **USA keleti**régióját.|
   | **Erőforrás neve** | Az AVS Private-felhő neve. |
   | **Termékváltozat** | Válassza ki a következő SKU-értéket: AV36 |
   | **Gazdagépek** | A saját felhőalapú fürtbe felvenni kívánt gazdagépek száma. Az alapértelmezett érték 3, amely az üzembe helyezés után növelhető vagy csökkenthető.  |
   | **vCenter rendszergazdai jelszava** | Adja meg a Felhőbeli rendszergazdai jelszót. |
   | **NSX-T kezelő jelszava** | Adjon meg egy NSX-T rendszergazdai jelszót. |
   | **Címterület** | Adja meg a CIDR-hálózat IP-címét a privát felhőhöz, például 10.175.0.0/22. |

   :::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="privát felhő létrehozása" border="true":::

1. Ha elkészült, válassza a **felülvizsgálat + létrehozás**elemet. A következő képernyőn ellenőrizze a megadott adatokat. Ha az adatok helyesek, válassza a **Létrehozás**lehetőséget.

   > [!NOTE]
   > Ez a lépés nagyjából két órát vesz igénybe. 

1. Ellenőrizze, hogy a központi telepítés sikeres volt-e. Navigáljon a létrehozott erőforráscsoporthoz, és válassza ki saját felhőjét.  A telepítés befejeződése után a **sikeres** állapot jelenik meg. 

   :::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="A privát felhő üzembe helyezésének ellenőrzése" border="true":::

### <a name="azure-cli"></a>Azure CLI

Ahelyett, hogy a Azure Portal egy AVS Private Cloud-t hozzon létre, használhatja az Azure CLI-t a Azure Cloud Shell használatával. Ez egy ingyenes, közös Azure-eszközökkel előre telepített és a fiókkal való használatra konfigurált interaktív felület. 

#### <a name="open-azure-cloud-shell"></a>Az Azure Cloud Shell megnyitása

A Cloud Shell megnyitásához válassza a **kipróbálás** elemet a kód jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/bash](https://shell.azure.com/bash) . Válassza a **Másolás** lehetőséget a kód blokkok másolásához, illessze be a Cloud Shellba, majd nyomja le az **ENTER** billentyűt a futtatásához.

#### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```
azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Magánfelhő létrehozása

Adja meg az erőforráscsoport nevét, a saját felhő nevét, helyét, a fürt méretét.

| Tulajdonság  | Leírás  |
| --------- | ------------ |
| **-g** (erőforráscsoport neve)     | A saját felhőalapú erőforrásaihoz tartozó erőforráscsoport neve.        |
| **-n** (saját felhő neve)     | Az AVS Private-felhő neve.        |
| **--hely**     | A saját felhőhöz használt hely.         |
| **– fürt mérete**     | A fürt mérete. A minimális érték 3.         |
| **--Network-Block**     | A CIDR IP-cím hálózati blokkja, amelyet a saját felhőhöz használ. A Címterület nem fedi át az előfizetésben és a helyszíni hálózatokban lévő más virtuális hálózatokban használt címeket.        |
| **--SKU** | Az SKU értéke: AV36 |

```
azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-a-private-cloud-azure-portal"></a>Privát felhő törlése (Azure Portal)

Ha olyan AVS Private-felhővel rendelkezik, amelyhez már nincs szüksége, törölheti is. Ha töröl egy privát felhőt, a rendszer minden fürtöt töröl az összes összetevővel együtt.

Ehhez navigáljon a privát felhőhöz a Azure Portalban, és válassza a **Törlés**lehetőséget. A jóváhagyás lapon erősítse meg a saját felhő nevét, és válassza az **Igen**lehetőséget.

> [!CAUTION]
> A privát felhő törlése visszafordíthatatlan művelet. A privát felhő törlése után az adatok nem állíthatók helyre, mivel az összes futó munkaterhelést, összetevőt és megsemmisítést végez, és megsemmisíti az összes személyes Felhőbeli adatát és konfigurációs beállítását, beleértve a nyilvános IP-címeket is. 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * AVS-magánfelhő létrehozása
> * Ellenőrizte a saját felhő üzembe helyezését

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre virtuális hálózatot a saját felhőhöz való használatra a saját felhőalapú fürtök helyi felügyeletének beállításának részeként.

> [!div class="nextstepaction"]
> [Virtual Network létrehozása](tutorial-configure-networking.md)
