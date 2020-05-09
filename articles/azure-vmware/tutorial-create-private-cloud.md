---
title: Oktatóanyag – vSphere-fürt üzembe helyezése az Azure-ban
description: Ismerje meg, hogyan helyezhet üzembe vSphere-fürtöt az Azure-ban az Azure VMWare-megoldás (AVS) használatával
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 6a192454367f2e6ca071e9cfe0a9f1a94868cbdb
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977699"
---
# <a name="tutorial-deploy-an-avs-private-cloud-in-azure"></a>Oktatóanyag: AVS Private Cloud üzembe helyezése az Azure-ban

Az Azure VMware-megoldás (AVS) lehetővé teszi, hogy vSphere-fürtöt helyezzen üzembe az Azure-ban. A minimális kezdeti üzembe helyezés három gazdagép. A további gazdagépek egyenként is hozzáadhatók, legfeljebb 16 gazdagépre. 

Mivel az AVS nem teszi lehetővé a privát felhő kezelését a helyszíni vCenter az indításkor, további konfigurációt kell végrehajtania, és kapcsolódnia kell egy helyi vCenter-példányhoz, a virtuális hálózathoz és más rendszerekhez. A jelen oktatóanyag-sorozat az eljárásokat és a kapcsolódó előfeltételeket tárgyalja.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * AVS-magánfelhő létrehozása
> * A privát felhő üzembe helyezésének ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Megfelelő rendszergazdai jogosultságok és engedélyek a privát felhő létrehozásához.
- Győződjön meg arról, hogy a megfelelő hálózatkezelés konfigurálva van az [oktatóanyag: hálózati ellenőrzőlista](tutorial-network-checklist.md)című témakörben leírtak szerint.

## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása

Az Azure VMWare-megoldás használatához először regisztrálnia kell az erőforrás-szolgáltatót. A következő példa regisztrálja az erőforrás-szolgáltatót az előfizetésében.

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Az erőforrás-szolgáltató regisztrálásának további módjaiért tekintse meg az [Azure erőforrás-szolgáltatók és-típusok](../azure-resource-manager/management/resource-providers-and-types.md)című témakört.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Magánfelhő létrehozása

A [Azure Portal](#azure-portal) vagy az [Azure CLI](#azure-cli)használatával létrehozhat egy AVS Private-felhőt.

### <a name="azure-portal"></a>Azure Portal

A Azure Portal válassza az **+ új erőforrás létrehozása**lehetőséget. A **Keresés a piactér** szövegmezőbe mezőbe írja `Azure VMware Solution`be az **Azure VMware megoldás** elemet a listából. Az **Azure VMware megoldás** ablakban válassza a **Létrehozás** lehetőséget.

Az **alapvető beállítások** lapon adja meg a mezők értékeit. A következő táblázat a tulajdonságok részletes listáját tartalmazza.

| Mező   | Érték  |
| ---| --- |
| **Előfizetés** | Az üzembe helyezéshez használni kívánt előfizetés.|
| **Erőforráscsoport** | A saját felhőalapú erőforrásainak erőforráscsoport. |
| **Hely** | Válasszon egy helyet, például az **USA keleti**régióját.|
| **Erőforrás neve** | Az AVS Private-felhő neve. |
| **SKU** | Válassza ki a followng SKU-értéket: AV36 |
| **Gazdagépek** | A saját felhőalapú fürtbe felvenni kívánt gazdagépek száma. Az alapértelmezett érték 3. Ez az érték az üzembe helyezés után növelhető vagy csökkenthető.  |
| **vCenter rendszergazdai jelszava** | Adja meg a Felhőbeli rendszergazdai jelszót. |
| **NSX-T kezelő jelszava** | Adja meg a NSX-T rendszergazdai jelszót. |
| **Címterület** | Adja meg a CIDR-hálózat IP-címét a privát felhőhöz. Ilyen például a 10.175.0.0/22. |

:::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="privát felhő létrehozása" border="true":::

Ha elkészült, válassza a **felülvizsgálat + létrehozás**elemet. A következő képernyőn ellenőrizze a megadott adatokat. Ha az adatok helyesek, válassza a **Létrehozás**lehetőséget.

> [!NOTE]
> Ez a lépés nagyjából két órát vesz igénybe. 

### <a name="azure-cli"></a>Azure CLI

Másik lehetőségként használhatja az Azure CLI-t egy AVS Private Cloud létrehozásához az Azure-ban. Ennek elvégzéséhez használhatja a Azure Cloud Shell, a következő lépések bemutatják, hogyan teheti ezt meg.

#### <a name="open-azure-cloud-shell"></a>Az Azure Cloud Shell megnyitása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a https://shell.azure.com/bash cím megnyitásával. Válassza a **Másolás** lehetőséget a kód blokkok másolásához, illessze be a Cloud Shellba, majd nyomja le az **ENTER** billentyűt a futtatásához.

#### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Magánfelhő létrehozása

Az AVS Private Cloud létrehozásához meg kell adnia egy erőforráscsoport-nevet, a saját felhő nevét, a helyet, a fürt méretét.


|Tulajdonság  |Leírás  |
|---------|---------|
|Erőforráscsoport neve     | Annak az erőforráscsoportnak a neve, amelyre a saját felhőt telepíti.        |
|Saját felhő neve     | A saját felhő neve.        |
|Hely     | A privát felhőhöz használt hely         |
|Fürt mérete     | A fürt mérete. A minimális érték 3.         |
|Hálózati blokk     | A CIDR használandó tartomány. Javasoljuk, hogy a helyszíni környezet és az Azure-környezet egyedi legyen.        |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22
```

## <a name="verify-deployment-was-successful"></a>A központi telepítés ellenőrzése sikerült

Keresse meg a létrehozott erőforráscsoportot, és válassza ki a saját felhőjét, amikor a telepítés befejeződött, megjelenik a következő képernyő, és látni fogja a **sikeres**állapotot.

:::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="A privát felhő üzembe helyezésének ellenőrzése" border="true":::

## <a name="delete-a-private-cloud"></a>Magánfelhő törlése

Ha olyan AVS Private Cloud-felhővel rendelkezik, amelyet már nem kell megvizsgálnia, törölheti is. Ha töröl egy privát felhőt, a rendszer minden olyan fürtöt töröl, amely az összes összetevőjét együtt törli.

Ehhez navigáljon a privát felhőhöz a Azure Portalban, és válassza a **Törlés**lehetőséget. A jóváhagyás lapon erősítse meg a saját felhő nevét, és válassza az **Igen**lehetőséget.

> [!CAUTION]
> A privát felhő törlése visszafordíthatatlan művelet. A privát felhő törlése után az adatok nem állíthatók helyre, mivel az összes futó munkaterhelést, összetevőt és megsemmisítést végez, és megsemmisíti az összes saját Felhőbeli adatát és konfigurációs beállítását, beleértve a nyilvános IP-címeket is. 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * AVS-magánfelhő létrehozása
> * Ellenőrizte a saját felhő üzembe helyezését

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre virtuális hálózatot a saját felhőhöz való használatra a saját felhőalapú fürtök helyi felügyeletének beállításának részeként.

> [!div class="nextstepaction"]
> [Virtual Network létrehozása](tutorial-configure-networking.md)
