---
title: Oktatóanyag – vSphere-fürt üzembe helyezése az Azure-ban
description: Ismerje meg, hogyan helyezhet üzembe egy vSphere-fürtöt az Azure-ban az Azure VMWare megoldás használatával
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 3fab49640364ef1b2e68953d366b20f77556b486
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578316"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>Oktatóanyag: Azure VMware-megoldás saját Felhőbeli üzembe helyezése az Azure-ban

Az Azure VMware megoldás lehetővé teszi, hogy vSphere-fürtöt helyezzen üzembe az Azure-ban. A minimális kezdeti üzembe helyezés három gazdagép. A további gazdagépek egyenként is hozzáadhatók, legfeljebb 16 gazdagépre. 

Mivel az Azure VMware-megoldás nem teszi lehetővé, hogy az indításkor a helyszíni vCenter kezelhesse saját felhőjét, további konfigurálásra van szükség. Ezek az eljárások és a kapcsolódó előfeltételek az oktatóanyagban találhatók.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure VMware-megoldás saját felhő létrehozása
> * A privát felhő üzembe helyezésének ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Megfelelő rendszergazdai jogosultságok és engedélyek a privát felhő létrehozásához.
- Győződjön meg arról, hogy a megfelelő hálózatkezelés konfigurálva van az [oktatóanyag: hálózati ellenőrzőlista](tutorial-network-checklist.md)című témakörben leírtak szerint.

## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="create-a-private-cloud"></a>Magánfelhő létrehozása

A [Azure Portal](#azure-portal) vagy az [Azure parancssori](#azure-cli)felületének használatával létrehozhat egy Azure VMware-megoldáshoz tartozó saját felhőt is.

### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

Ahelyett, hogy a Azure Portal egy Azure VMware-megoldáshoz tartozó privát felhőt létrehozni, az Azure CLI-t használhatja a Azure Cloud Shell használatával.  Az Azure VMware megoldással használható parancsok listáját itt találja: [Azure VMware-parancsok](https://docs.microsoft.com/cli/azure/ext/vmware/vmware).

#### <a name="open-azure-cloud-shell"></a>Az Azure Cloud Shell megnyitása

Válassza a **kipróbálás** lehetőséget a kódrészlet jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/bash](https://shell.azure.com/bash) . Válassza a **Másolás** lehetőséget a kód blokkok másolásához, illessze be a Cloud Shellba, majd nyomja le az **ENTER** billentyűt a futtatásához.

#### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli-interactive

az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Magánfelhő létrehozása

Adja meg az erőforráscsoport nevét, a saját felhőt, a helyet és a fürt méretét.

| Tulajdonság  | Leírás  |
| --------- | ------------ |
| **-g** (erőforráscsoport neve)     | A saját felhőalapú erőforrásaihoz tartozó erőforráscsoport neve.        |
| **-n** (saját felhő neve)     | Az Azure VMware-megoldás saját Felhőbeli neve.        |
| **--hely**     | A saját felhőhöz használt hely.         |
| **– fürt mérete**     | A fürt mérete. A minimális érték 3.         |
| **--Network-Block**     | A CIDR IP-cím hálózati blokkja, amelyet a saját felhőhöz használ. A Címterület nem fedi át az előfizetésben és a helyszíni hálózatokban lévő más virtuális hálózatokban használt címeket.        |
| **--SKU** | Az SKU értéke: AV36 |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-an-azure-vmware-solution-private-cloud"></a>Azure VMware-megoldás saját felhő törlése

Ha rendelkezik olyan Azure VMware-megoldással, amelyet már nem kell használnia, törölheti. Az Azure VMware-megoldás saját felhője egy elkülönített hálózati tartományt, egy vagy több dedikált vSphere-fürtöt, és általában sok virtuális gépet tartalmaz. Ha töröl egy privát felhőt, a rendszer törli az összes virtuális gépet, az adatmennyiséget és a fürtöket. A dedikált operációs rendszer nélküli csomópontok biztonságosan törlődnek, és az ingyenes készletbe kerülnek vissza. Az ügyfél számára kiosztott hálózati tartomány törölve lett.  

> [!CAUTION]
> A privát felhő törlése visszafordíthatatlan művelet. A privát felhő törlése után az adatok nem állíthatók helyre, mivel leállítja az összes futó munkaterhelést és összetevőt, és megsemmisíti az összes titkos Felhőbeli adat-és konfigurációs beállítást, beleértve a nyilvános IP-címeket is.

### <a name="prerequisites"></a>Előfeltételek

A privát felhő törlése után nem lehet helyreállítani a virtuális gépeket és az azokhoz tartozó adatokat. Ha később szükség lesz a virtuális gépekre, a rendszergazdának először biztonsági másolatot kell készítenie az összes adattal a saját felhő törlése előtt.

### <a name="steps-to-delete-an-azure-vmware-solution-private-cloud"></a>Azure-beli VMware-megoldás saját Felhőbeli törlésének lépései

1. Nyissa meg a Azure Portal Azure VMware Solutions lapját.

2. Válassza ki a törölni kívánt privát felhőt.
 
3. Adja meg a privát felhő nevét, és válassza az **Igen**lehetőséget. A törlési folyamat néhány órán belül befejeződik.  

## <a name="azure-vmware-commands"></a>Azure VMware-parancsok

Az Azure VMware megoldással használható parancsok listáját itt találja: [Azure VMware-parancsok](https://docs.microsoft.com/cli/azure/ext/vmware/vmware).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure VMware-megoldás saját felhő létrehozása
> * A privát felhő üzembe helyezésének ellenőrzése
> * Azure VMware-megoldás saját felhő törlése

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre egy Jump Box-t. A Jump Box használatával csatlakozhat a környezethez, hogy helyileg kezelhesse a saját felhőjét.


> [!div class="nextstepaction"]
> [Hozzáférés egy Azure VMware-megoldáshoz – saját felhő](tutorial-access-private-cloud.md)
