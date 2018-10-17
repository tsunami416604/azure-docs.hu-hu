---
title: Azure rövid útmutató – Batch AI-fürt létrehozása – Azure CLI | Microsoft Docs
description: Rövid útmutató – Batch AI-fürt létrehozása gépi tanulási és mesterségesintelligencia-modellek betanításához – Azure CLI
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 09/03/2018
ms.author: danlep
ms.openlocfilehash: 0d4ba7edfb22a6710222c854ceb2bf86284d2d77
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057798"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-cli"></a>Rövid útmutató: Fürt létrehozása Batch AI betanítási feladatokhoz az Azure CLI használatával

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy mesterségesintelligencia- és gépi tanulási modellek betanítására használható Batch AI-fürtöt az Azure CLI-vel. A Batch AI egy felügyelt szolgáltatás az adatelemzők és MI-kutatók számára, amely lehetővé teszi, hogy nagy számban tanítsanak be mesterségesintelligencia- és gépi tanulási modelleket Azure-beli virtuális gépek fürtjein.

A fürt kezdetben egyetlen GPU-csomóponttal rendelkezik. A rövid útmutató befejezése után rendelkezni fog egy vertikálisan felskálázható és modellek betanítására használható fürttel. A betanítási feladatok fürtnek való elküldéséhez használja a Batch AI-t, az [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) eszközeit vagy a [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai) bővítményt.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.38-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). 

Ez a rövid útmutató feltételezi, hogy a parancsokat a Bash-felületen, a Cloud Shellben vagy a helyi számítógépen futtatja.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az `az group create` paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus2* helyen. Ügyeljen arra, hogy olyan helyet válasszon, ahol elérhető a Batch AI szolgáltatás (például az USA 2. keleti régióját).

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-batch-ai-cluster"></a>Batch AI-fürt létrehozása

Első lépésként használja az `az batchai workspace create` parancsot egy Batch AI-*munkaterület* létrehozásához. A Batch AI-fürtök és egyéb erőforrások rendszerezéséhez egy munkaterületre van szüksége.

```azurecli-interactive
az batchai workspace create \
    --workspace myworkspace \
    --resource-group myResourceGroup 
```

Batch AI-fürt létrehozásához használja az `az batchai cluster create` parancsot. Az alábbi példában létrejön egy fürt a következő tulajdonságokkal:

* Egyetlen, NC6 virtuálisgép-méretet használó csomópontot tartalmaz, amely egy NVIDIA Tesla K80 GPU-val rendelkezik. 
* Egy alapértelmezett Ubuntu Server-rendszerképet futtat a tárolóalapú alkalmazások üzemeltetéséhez, amelyet a legtöbb betanítási számítási feladathoz használhat. 
* Hozzáad egy *myusername* nevű felhasználói fiókot, illetve SSH-kulcsokat hoz létre, ha még nem léteznek a kulcsok alapértelmezett helyén (*~/.ssh*) a helyi környezetben. 

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --vm-size Standard_NC6 \
    --target 1 \
    --user-name myusername \
    --generate-ssh-keys
```

A parancs kimenete megjeleníti a fürttulajdonságokat. A csomópont létrehozása és elindítása néhány percet vesz igénybe. A fürt állapotának megtekintéséhez futtassa az `az batchai cluster show` parancsot. 

```azurecli-interactive
az batchai cluster show \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --output table
```

A fürt létrehozásának korai szakaszában a kimenet a következőhöz hasonló, és azt mutatja, hogy a fürt `resizing` állapotban van:

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```
A fürt akkor áll készen a használatra, ha az állapota `steady`, az egyetlen csomóponté pedig `Idle`.

## <a name="list-cluster-nodes"></a>Fürtcsomópontok listázása 

Ha csatlakoznia kell a fürtcsomópontokhoz (ebben az esetben egyetlen csomóponthoz) az alkalmazások telepítéséhez vagy a karbantartás elvégzéséhez, kérje le a kapcsolati adatokat az `az batchai cluster node list` parancs futtatásával:


```azurecli-interactive
az batchai cluster node list \
    --cluster mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup 
 ```

A JSON-kimenet az alábbihoz hasonlóan fog kinézni:

```JSON
[
  {
    "ipAddress": "40.68.254.143",
    "nodeId": "tvm-1816144089_1-20180626t233430z",
    "port": 50000.0
  }
]
```
Ezeket az adatokat használhatja a csomóponttal való SSH-kapcsolat létesítéséhez. Például helyettesítse be a csomópont megfelelő IP-címét a következő parancsba:

```bash
ssh myusername@40.68.254.143 -p 50000
``` 
A folytatáshoz lépjen ki az SSH-munkamenetből.

## <a name="resize-the-cluster"></a>A fürt átméretezése

Ha a fürtöt egy betanítási feladat futtatására használja, előfordulhat, hogy több számítási erőforrásra lesz szüksége. Például ha 2 csomópontra szeretné növelni a méretet egy elosztott betanítási feladathoz, futtassa a [batch ai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize) parancsot:

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 2
```

A fürt átméretezése néhány percet vesz igénybe.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatni szeretné a Batch AI-oktatóanyagok és -minták használatát, használja az ebben a rövid útmutatóban létrehozott Batch AI-munkaterületet. 

A Batch AI-fürt addig díjköteles, amíg a csomópontok futnak. Ha szeretné megtartani a fürtkonfigurációt, amikor nem futtat feladatot, méretezze át a fürtöt 0 csomópontra. 

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 0
```

Később méretezze át őket 1 vagy több csomópontra a feladatok futtatásához. Ha már nincs szüksége a fürtre, törölje azt az `az batchai cluster delete` paranccsal:

```azurecli-interactive
az batchai cluster delete \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
```

Ha már nincs rá szükség, használja az `az group delete` parancsot a Batch AI-erőforrások erőforráscsoportjának törléséhez. 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk, hogyan hozhat létre egy Batch AI-fürtöt az Azure CLI használatával. Ha meg szeretné tudni, hogyan használhatja a Batch AI-fürtöt a modellek betanítására, folytassa a mélytanulási modellek betanításával foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
> [Mélytanulási modell betanítása](./quickstart-tensorflow-training-cli.md)
