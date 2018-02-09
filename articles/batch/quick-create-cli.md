---
title: "Azure rövid útmutató – Batch-feladat futtatása – CLI"
description: "Rövid áttekintést kaphat arról, hogyan futtathat Batch-feladatokat az Azure CLI-vel."
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/16/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f4a9235db7431b531864272d220006dbe8340924
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="quickstart-run-your-first-batch-job-with-the-azure-cli"></a>Rövid útmutató: Az első Batch-feladat futtatása az Azure CLI használatával

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató ismerteti, hogyan lehet létrehozni az Azure CLI-vel Batch-fiókot, számítási csomópontok (virtuális gépek) *készletét*, valamint egy olyan *feladatot*, amely alapszintű *tevékenységeket* futtat a készleten. Minden egyes mintatevékenység egy alapvető parancsot futtat a készlet egyik csomópontján. A rövid útmutatóból megismerheti a Batch szolgáltatás fő fogalmait, és készen áll majd a Batch szolgáltatás használatára realisztikusabb számítási feladatokkal, nagyobb léptékben.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.20-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus2* helyen.

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-storage-account"></a>Create a storage account

A Batch-fiók összekapcsolható egy általános célú Azure Storage-fiókkal. Bár a rövid útmutatóhoz nem kötelező, a Storage-fiók hasznos az alkalmazások üzembe helyezéséhez, valamint a legtöbb valós számítási feladat be- és kimeneti adatainak tárolásához. Az [az storage account create](/cli/azure/storage/account#az_storage_account_create) paranccsal hozzon létre egy Storage-fiókot az erőforráscsoportban.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

## <a name="create-a-batch-account"></a>Batch-fiók létrehozása

Az [az batch account create](/cli/azure/batch/account#az_batch_account_create) paranccsal hozzon létre egy Batch-fiókot. Számítási erőforrások (számítási csomópontok készletei) és Batch-feladatok létrehozásához szükség van egy fiókra.

Az alábbi példa egy *mybatchaccount* nevű Batch-fiókot hoz létre a *myResourceGroup* erőforráscsoportban, és összekapcsolja a létrehozott tárfiókkal.  

```azurecli-interactive 
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

A számítási készletek és feladatok létrehozásához és kezeléséhez hitelesítést kell végeznie a Batchben. Jelentkezzen be a fiókba az [az batch account login](/cli/azure/batch/account#az_batch_account_login) paranccsal. A bejelentkezés után az `az batch` parancsok ezt a fiókkörnyezetet használják.

```azurecli-interactive 
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```

## <a name="create-a-pool-of-compute-nodes"></a>Számításicsomópont-készlet létrehozása

Most, hogy már rendelkezik Batch-fiókkal, hozzon létre egy linuxos számítási csomópontokból álló mintakészletet az [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) paranccsal. A következő példában egy *mypool* nevű, 2 *Standard_A1_v2* méretű, Ubuntu 16.04 LTS rendszert futtató csomópontot tartalmazó készletet hozunk létre. A javasolt csomópontméret jó teljesítmény/költség arányt kínál a jelen rövid példában.
 
```azurecli-interactive
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:16.04.0-LTS \
    --node-agent-sku-id "batch.node.ubuntu 16.04" 
```

A Batch azonnal létrehozza a készletet, de a számítási csomópontok lefoglalása és elindítása igénybe vesz néhány percet. Eközben a készlet `resizing` állapotban van. A készlet állapotának megtekintéséhez futtassa az [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) parancsot. Ez a parancs a készlet összes tulajdonságát megjeleníti, és lehetővé teszi konkrét tulajdonságok lekérdezését. A következő parancs a készlet lefoglalási állapotát kérdezi le:

```azurecli-interactive
az batch pool show --pool-id mypool \
    --query "allocationState"
```

Miközben a készlet állapotának változására vár, folytassa az alábbi lépésekkel, és hozzon létre egy feladatot és tevékenységeket. A készlet akkor áll készen tevékenységek futtatására, amikor a lefoglalási állapota `steady` értékre vált, és minden csomópont fut. 

## <a name="create-a-job"></a>Feladat létrehozása

Most, hogy már rendelkezik készlettel, hozzon létre egy feladatot, amelyet azon futtat.  A Batch-feladat egy vagy több tevékenység logikai csoportja. Egy Batch-feladat magában foglalja a tevékenységek közös beállításait, mint a prioritást, illetve a készletet, amelyeken a tevékenységeket futtatni szeretné. Az [az batch job create](/cli/azure/batch/job#az_batch_job_create) paranccsal hozzon létre egy Batch-feladatot. Az alábbi példa egy *myjob* nevű feladatot hoz létre a *mypool* készleten. A feladat kezdetben nem tartalmaz tevékenységeket.

```azurecli-interactive 
az batch job create \
    --id myjob \
    --pool-id mypool
```

## <a name="create-tasks"></a>Tevékenységek létrehozása

Most pedig az [az batch task create](/cli/azure/batch/task#az_batch_task_create) paranccsal hozzon létre a feladatban futtatni kívánt tevékenységeket. Ebben a példában négy azonos tevékenységet hozunk létre. Minden tevékenység egy `command-line` futtatásával megjeleníti a Batch környezeti változókat a számítási csomóponton, majd vár 90 másodpercet. Batch használata esetén ebben a parancssorban adhatja meg az alkalmazást vagy a szkriptet. A Batch számos módszert kínál az alkalmazások és szkriptek számítási csomópontokon történő üzembe helyezésére.

Az alábbi Bash-szkript 4 párhuzamos tevékenységet hoz létre (*mytask1*–*mytask4*).

```azurecli-interactive 
for i in {1..4}
do
   az batch task create \
    --task-id mytask$i \
    --job-id myjob \
    --command-line "/bin/bash -c printenv | grep AZ_BATCH; sleep 90s"
done
```

A parancskimenet megjeleníti az egyes tevékenységek beállításait. A Batch kiosztja a feladatokat a számítási csomópontoknak.

## <a name="view-task-status"></a>Tevékenységek állapotának megtekintése

Tevékenység létrehozása után a Batch várólistára helyezi azt a készleten való futtatáshoz. Amint egy csomópont készen áll a futtatásra, a rendszer futtatja a tevékenységet.

Az [az batch task show](/cli/azure/batch/task#az_batch_task_show) paranccsal tekintheti meg a Batch-tevékenységek állapotát. A következő példa a készlet egyik csomópontján futó *mytask1* tevékenység részleteit jeleníti meg.

```azurecli-interactive 
az batch task show \
    --job-id myjob \
    --task-id mytask1
```

A parancs kimenete számos részletet tartalmaz. Tekintse meg a tevékenység parancssorának `exitCode` elemét, illetve a `nodeId` értékét. Ha az `exitCode` értéke 0, akkor a tevékenység parancssora sikeresen befejeződött. A `nodeId` a készlet azon csomópontjának azonosítóját jelöli, amelyen a tevékenység futott.

## <a name="view-task-output"></a>A tevékenység kimenetének megtekintése

A tevékenységek által egy számítási csomóponton létrehozott fájlok felsorolásához használja az [az batch task file list](/cli/azure/batch/task#az_batch_task_file_list) parancsot. A következő parancs felsorolja a *mytask1* tevékenység által létrehozott fájlokat: 

```azurecli-interactive 
az batch task file list \
    --job-id myjob \
    --task-id mytask1 \
    --output table
```

A kimenet a következőkhöz hasonló:

```
Name        URL                                                                                         Is Directory      Content Length
----------  ------------------------------------------------------------------------------------------  --------------  ----------------
stdout.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stdout.txt  False                  695
certs       https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/certs       True
wd          https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/wd          True
stderr.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stderr.txt  False                     0

```

A kimeneti fájlok az [az batch task file download](/cli/azure/batch/task#az_batch_task_file_download) paranccsal tölthetők le helyi könyvtárba. Ebben a példában a tevékenység kimenete a `stdout.txt` fájlban található. 

```azurecli-interactive
az batch task file download \
    --job-id myjob \
    --task-id mytask1 \
    --file-path stdout.txt \
    --destination ./stdout.txt
```

A `stdout.txt` fájl tartalmát egy szövegszerkesztőben tekintheti meg. A tartalom a csomóponton beállított Azure Batch környezeti változókat jeleníti meg. Saját Batch-feladatok létrehozása során hivatkozhat ezekre a környezeti változókra a tevékenységek parancssorában, illetve a parancssorok által futtatott alkalmazásokban és szkriptekben.

```
AZ_BATCH_TASK_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask3
AZ_BATCH_NODE_STARTUP_DIR=/mnt/batch/tasks/startup
AZ_BATCH_CERTIFICATES_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask3/certs
AZ_BATCH_ACCOUNT_URL=https://mybatchaccount.eastus2batch.azure.com/
AZ_BATCH_TASK_WORKING_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask3/wd
AZ_BATCH_NODE_SHARED_DIR=/mnt/batch/tasks/shared
AZ_BATCH_TASK_USER=_azbatch
AZ_BATCH_NODE_ROOT_DIR=/mnt/batch/tasks
AZ_BATCH_JOB_ID=myjob
AZ_BATCH_NODE_IS_DEDICATED=true
AZ_BATCH_NODE_ID=tvm-1392786932_2-20171026t223740z
AZ_BATCH_POOL_ID=mypool-linux
AZ_BATCH_TASK_ID=mytask3
AZ_BATCH_ACCOUNT_NAME=mybatchaccount
AZ_BATCH_TASK_USER_IDENTITY=PoolNonAdmin
```
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha folytatni szeretné a Batch-oktatóanyagok és -minták használatát, használja az ebben a rövid útmutatóban létrehozott Batch-fiókot és az ahhoz kapcsolt Storage-fiókot. A Batch-fiók használata díjmentes.

A készletekért díjat számítunk fel, amíg a csomópontok futnak, még akkor is, ha nincsenek feladatok ütemezve. Ha már nincs szüksége a készletre, törölje azt az [az batch pool delete](/cli/azure/batch/pool#az_batch_pool_delete) paranccsal:

```azurecli-interactive
az batch pool delete --pool-id mypool
```

Ha már nincs szükség rájuk, az [az group delete](/cli/azure/group#az_group_delete) paranccsal eltávolíthatja az erőforráscsoportot, a Batch-fiókot, a készleteket és az összes kapcsolódó erőforrást. Az erőforrásokat a következőképpen törölheti:

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy Batch-fiókot, egy Batch-készletet és egy Batch-feladatot hozott létre. A feladat mintatevékenységeket futtatott, Ön pedig megtekintette az egyik csomóponton létrejött kimenetet. Most, hogy megismerkedett a Batch szolgáltatás fő fogalmaival, készen áll a Batch szolgáltatás realisztikusabb számítási feladatokkal, nagyobb léptékben történő kipróbálására. Az Azure Batchről további információt az Azure Batch-oktatóanyagokban találhat. 


> [!div class="nextstepaction"]
> [Azure Batch-oktatóanyagok](./tutorial-parallel-dotnet.md)
