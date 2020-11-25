---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 131b21ea7bc47df9654dd7c163eb22adb68e6678
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95971462"
---
## <a name="create-a-spatial-anchors-resource"></a>Térbeli horgonyok erőforrásának létrehozása

### <a name="portal"></a>[Portál](#tab/azure-portal)

Nyissa meg az <a href="https://portal.azure.com" target="_blank">Azure Portalt</a>.

A bal oldali panelen válassza az **erőforrás létrehozása** lehetőséget.

A keresőmező segítségével megkeresheti a **térbeli horgonyokat**.

![A térbeli horgonyok keresésének eredményét bemutató képernyőkép.](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Válassza a **térbeli horgonyok** lehetőséget, majd válassza a **Létrehozás** lehetőséget.

A **térbeli horgonyok fiók** panelen tegye a következőket:

* Adjon meg egy egyedi nevet a normál alfanumerikus karakterek használatával.  
* Válassza ki azt az előfizetést, amelyhez az erőforrást csatolni kívánja.  
* Hozzon létre egy erőforráscsoportot az **új létrehozása** lehetőség kiválasztásával. Nevezze el **myResourceGroup**, majd kattintson **az OK gombra**.  

  [!INCLUDE [resource group intro text](resource-group.md)]
  
* Válassza ki azt a helyet (régiót), ahová az erőforrást helyezni kívánja.  
* Válassza az **új** lehetőséget az erőforrás létrehozásának megkezdéséhez.

![Képernyőkép a térbeli horgonyok panelről az erőforrás létrehozásához.](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Az erőforrás létrehozása után a Azure Portal mutatja, hogy a telepítés befejeződött. 
   
![Az erőforrás-telepítés befejezését bemutató képernyőkép.](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Válassza az **Erőforrás megnyitása** lehetőséget. Ekkor megtekintheti az erőforrás tulajdonságait. 
   
Másolja az erőforrás **Account ID** értékét egy szövegszerkesztőbe későbbi használatra.

![Képernyőkép az erőforrás-tulajdonságok panelről.](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Másolja át az erőforrás **fiók tartományának** értékét egy szövegszerkesztőbe későbbi használatra.

![Az erőforrás fiók tartományának értékét ábrázoló képernyőkép.](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

A **Beállítások** területen válassza a **kulcs** elemet. Másolja az **elsődleges kulcs** értékét, a **fiók kulcsát** egy szövegszerkesztőbe későbbi használatra.

![Képernyőkép a fiókhoz tartozó kulcsok panelről.](./media/spatial-anchors-get-started-create-resource/view-account-key.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Első lépésként készítse elő a környezetét az Azure CLI-re:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](azure-cli-prepare-your-environment-no-header.md)]

1. A bejelentkezés után az az [Account set](/cli/azure/account#az_account_set) paranccsal válassza ki azt az előfizetést, amelyben be szeretné állítani a térbeli horgonyok fiókját:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Futtassa az az [Group Create](/cli/azure/group#az_group_create) parancsot egy erőforráscsoport létrehozásához, vagy használjon egy meglévő erőforráscsoportot:

   ```azurecli
   az group create --name myResourceGroup --location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   Egy erőforráscsoport esetében az az [térbeli-Anchors-Account List](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_list) paranccsal tekintheti meg az aktuális térbeli horgonyokat:

   ```azurecli
   az spatial-anchors-account list --resource-group myResourceGroup
   ```

   Az előfizetéshez tartozó térbeli horgonyok fiókjait is megtekintheti:

   ```azurecli
   az spatial-anchors-account list
   ```

1. Futtassa az az [térbeli-Anchors-Account Create](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_create) parancsot a térbeli horgonyok fiók létrehozásához:

   ```azurecli
   az spatial-anchors-account create --resource-group myResourceGroup --name MySpatialAnchorsQuickStart --location eastus2
   ```

1. Tekintse meg az erőforrás tulajdonságait az az [térbeli-Anchors-Account show](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_show) parancs használatával:

   ```azurecli
   az spatial-anchors-account show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Másolja az erőforrásfiók- **azonosító** értékét és az erőforrás- **fiók tartományának** értékét egy szövegszerkesztőbe későbbi használatra.

1. Az elsődleges és másodlagos kulcsok lekéréséhez futtassa az az [térbeli-Anchors-Account Key show](/cli/azure/ext/mixed-reality/spatial-anchors-account/key#ext_mixed_reality_az_spatial_anchors_account_key_show) parancsot:

   ```azurecli
   az spatial-anchors-account key show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Másolja a kulcs értékeit egy szövegszerkesztőbe későbbi használatra.

   Ha újra kell generálnia a kulcsokat, használja az az [térbeli-Anchors-Account Key renew](/cli/azure/ext/mixed-reality/spatial-anchors-account/key#ext_mixed_reality_az_spatial_anchors_account_key_renew) parancsot:

   ```azurecli
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key primary
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key secondary
   ```

A fiókot az az [térbeli-Anchors-Account delete](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_delete) paranccsal törölheti:

```azurecli
az spatial-anchors-account delete --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
```

---
