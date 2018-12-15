---
title: Azure rövid útmutató – Mélytanulási betanítás – Azure CLI | Microsoft Docs
description: Rövid útmutató – Rövid áttekintést kaphat arról, hogyan taníthat be egy TensorFlow mélytanulási neurális hálózatot egy GPU-n Batch AI segítségével az Azure CLI használatával
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
ROBOTS: NOINDEX
ms.openlocfilehash: 979253021f5503295e0572759b510e074ceb1a6b
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408068"
---
# <a name="quickstart-train-a-deep-learning-model-with-batch-ai"></a>Gyors útmutató: A Batch AI egy deep learning-modell betanítása

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Ez a rövid útmutató bemutatja, hogyan taníthat be egy mélytanulási mintamodellt egy Batch AI által felügyelt GPU-kompatibilis virtuális gépen. A Batch AI egy felügyelt szolgáltatás az adatelemzők és MI-kutatók számára, amely lehetővé teszi, hogy nagy számban tanítsanak be mesterségesintelligencia- és gépi tanulási modelleket Azure-beli virtuális gépek fürtjein. 

Ebben a példában az Azure CLI-t használja a Batch AI beállításához, hogy betanítsa egy [TensorFlow](https://www.tensorflow.org/) neurális példahálózatnak a kézzel írt számjegyek [MNIST-adatbázisát](http://yann.lecun.com/exdb/mnist/). A rövid útmutatóból megismerheti a Batch AI használatának fő fogalmait egy mesterséges intelligencia vagy egy gépi tanulási modell betanításához, és készen áll majd különböző modellek nagyobb léptékben történő betanítására.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.38-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). 

Ez a rövid útmutató feltételezi, hogy a parancsokat a Bash-felületen, a Cloud Shellben vagy a helyi számítógépen futtatja. Ha már befejezte a rövid útmutatót, hogy [létrehozzon egy Batch AI-fürtöt az Azure CLI használatával](quickstart-create-cluster-cli.md), hagyja ki az első két lépést egy erőforráscsoport és egy Batch AI-fürt létrehozásához.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az `az group create` paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus2* helyen. Győződjön meg arról, hogy az USA 2. keleti régióját vagy egy másik olyan helyet választ, ahol a Batch AI szolgáltatás elérhető. 

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

Batch AI-fürt létrehozásához használja az `az batchai cluster create` parancsot. Az alábbi példa létrehoz egy egycsomópontos fürtöt a következő tulajdonságokkal:

* Az NC6 virtuálisgép-méretet használja, amely egy NVIDIA Tesla K80 GPU-val rendelkezik. Az Azure számos virtuálisgép-méretet kínál különböző NVIDIA GPU-kkal.
* Egy alapértelmezett Ubuntu Server-rendszerképet futtat tárolóalapú alkalmazások üzemeltetéséhez. A legtöbb betanítási számítási feladatot futtathatja ezen a disztribúción. 
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

A fürt létrehozásának korai szakaszában a kimenet a következőhöz hasonló, és azt mutatja, hogy a fürt `resizing` állapotú:

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```

Folytassa az alábbi lépésekkel a betanítási szkript feltöltéséhez és a betanítási feladat létrehozásához a fürtállapot módosulása közben. A fürt akkor áll készen a betanításra, ha az állapota `steady`, az egyetlen csomópont pedig `Idle` állapotú.

## <a name="upload-training-script"></a>Betanítási szkript feltöltése

Használja az `az storage account create` parancsot egy tárfiók létrehozásához a betanítási szkript és a betanítási anyag kimenetének tárolásához.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

Az `az storage share create` paranccsal hozzon létre egy `myshare` nevű Azure-fájlmegosztást a fiókban:

```azurecli-interactive
az storage share create \
    --name myshare \
    --account-name mystorageaccount
```

Az `az storage directory create` paranccsal hozhat létre könyvtárakat az Azure-fájlmegosztásban. A `scripts` könyvtár létrehozása a betanítási szkripthez, és a `logs` létrehozása a betanítási anyag kimenetéhez:

```azurecli-interactive
# Create /scripts directory in file share
az storage directory create \
    --name scripts \
    --share-name myshare \
    --account-name mystorageaccount

# Create /logs directory in file share 
az storage directory create \
    --name logs \
    --share-name myshare \
    --account-name mystorageaccount
```

A Bash-felületen hozzon létre egy helyi munkakönyvtárat, majd töltse le a TensorFlow [convolutional.py](https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py) mintáját. A Python-szkript betanítja a konvolúciós neurális hálózatnak a MNIST-képkészlet 60 000 kézzel írt számjegyét 0-9-ig. Ezután példákon keresztül teszteli a modellt.

```bash
wget https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py
```

Töltse fel a szkriptet a megosztáson belüli `scripts` könyvtárba az `az storage file upload` paranccsal.

```azurecli-interactive
az storage file upload \
    --share-name myshare \
    --path scripts \
    --source convolutional.py \
    --account-name mystorageaccount
```

## <a name="submit-training-job"></a>Betanítási feladat elküldése

Először hozzon létre egy Batch AI-*kísérletet* a munkaterületen az `az batchai experiment create` paranccsal. A kísérlet egy logikai tároló a Batch AI-hoz kapcsolódó feladatok számára.

```azurecli-interactive
az batchai experiment create \
    --name myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

Hozzon létre egy `job.json` betanításifeladat-konfigurációs fájlt a munkakönyvtárban a következő tartalommal. A betanítás elküldésével átadja a konfigurációs fájlt.

Ez a `job.json` fájl beállításokat tartalmaz a Python-szkriptfájl megkereséséhez és a GPU-csomóponton lévő TensorFlow-tárolóban történő futtatásához. Megadja továbbá a feladat kimeneti fájljainak mentési helyét az Azure Storage-ban. Az `<AZURE_BATCHAI_STORAGE_ACCOUNT>` azt jelenti, hogy a tárfiók neve a feladat küldésekor lesz megadva.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 1,
        "tensorFlowSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/scripts/convolutional.py"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/logs",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

Az `az batchai job create` paranccsal elküldheti a csomóponton lévő feladatot, átadva a `job.json` konfigurációs fájlt és a tárfiók nevét:

```azurecli-interactive
az batchai job create \
    --name myjob \
    --cluster mycluster \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --config-file job.json \
    --storage-account-name mystorageaccount
```

A parancs visszaadja a feladat tulajdonságait, ezután a végrehajtása eltarthat néhány percig. A feladat előrehaladásának monitorozásához használja az `az batchai job file stream` parancsot, hogy streamelje a `stdout-wk-0.txt` fájlt a csomóponton lévő szabványos kimeneti könyvtárból. A feladat futtatásának megkezdése után a betanítási szkript létrehozza a fájlt.  

```azurecli-interactive
az batchai job file stream \
    --job myjob \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --file-name stdout-wk-0.txt
```

Példa a kimenetre:

```
File found with URL "https://mystorageaccount.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/myResourceGroup/workspaces/myworkspace/experiments/myexperiment/jobs/myjob/<JOB_ID>/stdouterr/stdout-wk-0.txt?sv=2016-05-31&sr=f&sig=Kih9baozMao8Ugos%2FVG%2BcsVsSeY1O%2FTocCNvLQhwtx4%3D&se=2018-06-20T22%3A07%3A30Z&sp=rl". Start streaming
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting data/train-images-idx3-ubyte.gz
Extracting data/train-labels-idx1-ubyte.gz
Extracting data/t10k-images-idx3-ubyte.gz
Extracting data/t10k-labels-idx1-ubyte.gz
Initialized!
Step 0 (epoch 0.00), 14.9 ms
Minibatch loss: 8.334, learning rate: 0.010000
Minibatch error: 85.9%
Validation error: 84.6%
Step 100 (epoch 0.12), 9.7 ms
Minibatch loss: 3.240, learning rate: 0.010000
Minibatch error: 6.2%
Validation error: 7.7%
Step 200 (epoch 0.23), 8.3 ms
Minibatch loss: 3.335, learning rate: 0.010000
Minibatch error: 7.8%
Validation error: 4.5%
Step 300 (epoch 0.35), 8.3 ms
Minibatch loss: 3.157, learning rate: 0.010000
Minibatch error: 3.1%
...
Step 8500 (epoch 9.89), 8.3 ms
Minibatch loss: 1.605, learning rate: 0.006302
Minibatch error: 0.0%
Validation error: 0.9%
Test error: 0.8%
```

A streamelés leáll, amikor a feladat befejeződik. A példaszkript 10 *alapidőszakon* keresztül végzi a betanítást, vagy áthalad a betanítási adatkészleten. Ebben a példában 10 alapidőszak után a betanított modell csak 0,8%-os tesztelési hibát produkál.

## <a name="get-job-output"></a>Feladat kimenetének lekérése

A Batch AI mindegyik feladatkimenethez egy egyedi mappaszerkezetet hoz létre a tárfiókban. Állítsa be a JOB_OUTPUT_PATH környezeti változót ezzel az elérési úttal. Ezután listázza a kimeneti fájlokat a tárolóban az `az storage file list` paranccsal:

```azurecli-interactive
export JOB_OUTPUT_PATH=$(az batchai job show --name myjob --experiment myexperiment --workspace myworkspace --resource-group myResourceGroup --query jobOutputDirectoryPathSegment --output tsv)

az storage file list \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr \
    --output table
```

A kimenet a következőhöz hasonló:

```
Name               Content Length  Type    Last Modified
---------------  ----------------  ------  ---------------
execution.log               14866  file
stderr-wk-0.txt              1527  file
stdout-wk-0.txt             11027  file
```

Az `az storage file download` paranccsal letölthet egy vagy több fájlt a helyi munkakönyvtárba. Példa:

```azurecli-interactive
az storage file download \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr/stdout-wk-0.txt
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatni szeretné a Batch AI-oktatóanyagok és -minták használatát, használja az ebben a rövid útmutatóban létrehozott Batch AI-munkaterületet, fürtöt és Storage-fiókot. 

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
    --resource-group myResourceGroup
```

Ha már nincs rá szükség, a `az group delete` paranccsal eltávolítható a Batch AI erőforráscsoportja és a tárerőforrások. 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban megismerhette, hogyan használhatja a Batch AI-t egy TensorFlow mélytanulási példamodell betanításához egy GPU virtuális gépen az Azure CLI-vel. A modellek betanításának nagyobb GPU-fürtön történő elosztásának megismeréséhez folytassa a Batch AI oktatóanyagával.

> [!div class="nextstepaction"]
> [Elosztott betanítási oktatóanyag](./tutorial-horovod-tensorflow.md)