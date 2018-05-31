---
title: Azure rövid útmutató – CNTK-betanítás a Batch AI és az Azure CLI használatával | Microsoft Docs
description: Rövid áttekintést kaphat arról, hogyan futtathat CNTK betanítási feladatokat a Batch AI és az Azure CLI használatával
services: batch-ai
documentationcenter: na
author: AlexanderYukhanov
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: Alexander.Yukhanov
ms.openlocfilehash: 3601ea412790c991892a0c05210d2551810287b8
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33869019"
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>CNTK-betanítási feladatok futtatása az Azure CLI-vel

Az Azure CLI 2.0 lehetővé teszi a Batch AI-erőforrások létrehozását és kezelését – Batch AI-fájlkiszolgálók és -fürtök létrehozását és törlését, valamint betanítási feladatok elküldését, leállítását, törlését és monitorozását.

Ez a rövid útmutató ismerteti, hogyan hozható létre egy GPU-fürt és hogyan futtatható egy betanítási feladat a Microsoft Cognitive Toolkit használatával.

A [ConvNet_MNIST.py](https://github.com/Azure/BatchAI/blob/master/recipes/CNTK/CNTK-GPU-Python/CNTK-GPU-Python.ipynb) betanítási szkript a Batch AI GitHub-oldalon érhető el. A szkript megtanítja a konvolúciós neurális hálózatnak a kézzel írt számjegyek MNIST-adatbázisát.

A hivatalos CNTK-példa úgy lett módosítva, hogy elfogadja a betanítási adatkészlet helyét és a kimeneti könyvtár helyét parancssori argumentumként.

## <a name="quickstart-overview"></a>Rövid útmutató – áttekintés

* Hozzon létre egy egy csomópontos GPU-fürtöt (`Standard_NC6` virtuálisgép-mérettel) `nc6` néven;
* Hozzon létre egy új tárfiókot a feladatok bemenetének és kimenetének tárolásához;
* Hozzon létre egy Azure-fájlmegosztást két mappával (`logs` és `scripts` néven), amelyek a feladatok kimenetét és a betanítási szkripteket fogják tartalmazni;
* Hozzon létre egy `data` nevű Azure-blobtárolót a betanítási adatok tárolásához;
* Helyezze üzembe a betanítási szkriptet és a betanítási adatokat a létrehozott fájlmegosztásban és tárolóban;
* Konfigurálja a feladatot úgy, hogy csatlakoztassa az Azure-fájlmegosztást és az Azure-blobtárolót a fürtcsomóponton, és hagyományos fájlrendszerként elérhetővé tegye őket a következő helyeken: `$AZ_BATCHAI_JOB_MOUNT_ROOT/logs`, `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts` és `$AZ_BATCHAI_JOB_MOUNT_ROOT/data`.
Az `AZ_BATCHAI_JOB_MOUNT_ROOT` egy környezeti változó, amelyet a Batch AI állít be a feladathoz.
* A feladat monitorozásához streamelje a feladat standard kimenetét;
* A feladat befejezése után ellenőrizze a kimenetét és a létrehozott modelleket;
* Végül töröljön minden lefoglalt erőforrást.

# <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Hozzáférés az Azure CLI 2.0-hoz (a 2.0.31-es vagy újabb verzióval). Használhatja a [Cloud Shellben](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) elérhető Azure CLI 2.0-t, vagy telepítheti helyileg [ezen utasítások](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) alapján.

# <a name="cloud-shell-only"></a>Csak Cloud Shell

A Cloud Shell használata esetén módosítsa a munkakönyvtárat az `/usr/$USER/clouddrive` könyvtárra, mert a kezdőkönyvtárban nincs szabad hely:

```azurecli
cd /usr/$USER/clouddrive
```

# <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport egy logikai tároló az Azure-erőforrások üzembe helyezéséhez és felügyeletéhez. Az alábbi parancs létrehoz egy ```batchai.quickstart``` nevű új erőforráscsoportot az USA keleti régiójában:

```azurecli
az group create -n batchai.quickstart -l eastus
```

# <a name="create-gpu-cluster"></a>GPU-fürt létrehozása

A következő parancs egy egy csomópontos GPU-fürtöt hoz létre (a virtuális gép mérete Standard_NC6) egy Ubuntu DSVM operációsrendszer-kép használatával:

```azurecli
az batchai cluster create -n nc6 -g batchai.quickstart -s Standard_NC6 -i UbuntuDSVM -t 1 --generate-ssh-keys
```

Az Ubuntu DSVM lehetővé teszi bármilyen betanítási feladat Docker-tárolókban való futtatását, valamint a legnépszerűbb mély tanulási keretrendszerek futtatását közvetlenül a virtuális gépeken.

A `--generate-ssh-keys` kapcsoló a privát és nyilvános SSH-kulcsok létrehozására utasítja az Azure CLI-t, ha még nem léteznek. A fürtcsomópontokhoz az aktuális felhasználónév és a létrehozott SSH-kulcs használatával férhet hozzá.

Megjegyzés: Ha a Cloud Shellt használja, készítsen biztonsági másolatot az ~/.ssh mappáról egy állandó tárolóra.

Példa a kimenetre:
```json
{
  "allocationState": "steady",
  "allocationStateTransitionTime": "2018-04-11T21:17:26.345000+00:00",
  "creationTime": "2018-04-11T20:12:10.758000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/clusters/nc6",
  "location": "eastus",
  "name": "nc6",
  "nodeSetup": null,
  "nodeStateCounts": {
    "additionalProperties": {},
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T20:12:11.445000+00:00",
  "resourceGroup": "batchai.quickstart",
  "scaleSettings": {
    "additionalProperties": {},
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": null,
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "additionalProperties": {},
    "adminUserName": "alex",
    "adminUserPassword": null,
    "adminUserSshPublicKey": "<YOUR SSH PUBLIC KEY HERE>"
  },
  "virtualMachineConfiguration": {
    "additionalProperties": {},
    "imageReference": {
      "additionalProperties": {},
      "offer": "linux-data-science-vm-ubuntu",
      "publisher": "microsoft-ads",
      "sku": "linuxdsvmubuntu",
      "version": "latest",
      "virtualMachineImageId": null
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
}
```

# <a name="create-a-storage-account"></a>Storage-fiók létrehozása

A következő parancs létrehoz egy új tárfiókot ugyanabban a régióban, ahol a batchai.repices erőforráscsoport található. Frissítse a parancsot egy egyedi tárfióknévvel.

```azurecli
az storage account create -n <storage account name> --sku Standard_LRS -g batchai.quickstart
```

Ha a kívánt tárfióknév nem érhető el, a fenti parancs jelenti a megfelelő hibát. Ebben az esetben válasszon egy másik nevet, és próbálkozzon újra.

# <a name="data-deployment"></a>Adatok üzembe helyezése

## <a name="download-the-training-script-and-training-data"></a>A betanítási szkript és a betanítási adatok letöltése

* Töltse le és bontsa ki az előfeldolgozott MNIST-adatbázist [erről a helyről](https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D) az aktuális mappába.

GNU/Linux vagy Cloud Shell esetén:

```azurecli
wget "https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D" -O mnist_dataset.zip
unzip mnist_dataset.zip
```

Lehetséges, hogy telepítenie kell az `unzip` parancsot, ha a GNU/Linux-disztribúciója nem tartalmazza.

* Töltse le a [ConvNet_MNIST.py](https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py) példaszkriptet az aktuális mappába:

GNU/Linux vagy Cloud Shell esetén:

```azurecli
wget https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py
```

## <a name="create-azure-file-share-and-deploy-the-training-script"></a>Azure-fájlmegosztás létrehozása és a betanítási szkript üzembe helyezése

A következő parancsok létrehozzák a `scripts` és a `logs` nevű Azure fájlmegosztást, majd bemásolják a betanítási szkriptet a `scripts` megosztásban található `cntk` mappába:

```azurecli
az storage share create -n scripts --account-name <storage account name>
az storage share create -n logs --account-name <storage account name>
az storage directory create -n cntk -s scripts --account-name <storage account name>
az storage file upload -s scripts --source ConvNet_MNIST.py --path cntk --account-name <storage account name> 
```

## <a name="create-a-blob-container-and-deploy-training-data"></a>Blobtároló létrehozása és betanítási adatok üzembe helyezése

A következő parancsok létrehoznak egy `data` nevű Azure-blobtárolót, és bemásolják a betanítási adatokat az `mnist_cntk` nevű mappába:
```azurecli
az storage container create -n data --account-name <storage account name>
az storage blob upload-batch -s . --pattern '*28x28_cntk*' --destination data --destination-path mnist_cntk --account-name <storage account name>
```

# <a name="submit-training-job"></a>Betanítási feladat elküldése

## <a name="prepare-job-configuration-file"></a>Feladatkonfigurációs fájl előkészítése

Hozzon létre egy `job.json` nevű betanításifeladat-konfigurációs fájlt az alábbi tartalommal:
```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-03-01/cntk.json",
    "properties": {
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs"
        }],
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/logs",
                    "relativeMountPath": "logs"
                },
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/scripts",
                    "relativeMountPath": "scripts"
                }
            ],
            "azureBlobFileSystems": [
                {
                    "accountName": "<AZURE_BATCHAI_STORAGE_ACCOUNT>",
                    "containerName": "data",
                    "relativeMountPath": "data"
                }
            ]
        }
    }
}
```

Ez a konfigurációs fájl a következőket adja meg:

* `nodeCount` – a feladathoz szükséges csomópontok száma (ebben a rövid útmutatóban 1);
* `cntkSettings` – a betanítási szkript és a parancssori argumentumok elérési útja. A parancssori argumentumok közé tartozik a betanítási adatok elérési útja és a létrehozott modellek tárolási helyének elérési útja. Az `AZ_BATCHAI_OUTPUT_MODEL` egy környezeti változó, amelyet a Batch AI a kimeneti könyvtár konfigurációja alapján állít be (lásd alább);
* `stdOutErrPathPrefix` – az az elérési út, ahol a Batch AI létrehozza a feladat kimeneteit és naplóit tartalmazó könyvtárakat;
* `outputDirectories` – a Batch AI által létrehozandó kimeneti könyvtárak gyűjteménye. A Batch AI minden könyvtárhoz létrehoz egy `AZ_BATCHAI_OUTPUT_<id>` nevű környezeti változót, ahol az `<id>` a könyvtár azonosítója;
* `mountVolumes` – a feladat végrehajtása során csatlakoztatandó fájlrendszerek listája. A fájlrendszerek csatlakoztatási útvonala: `AZ_BATCHAI_JOB_MOUNT_ROOT/<relativeMountPath>`. Az `AZ_BATCHAI_JOB_MOUNT_ROOT` egy környezeti változó, amelyet a Batch AI állít be;
* Az `<AZURE_BATCHAI_STORAGE_ACCOUNT>` azt jelenti, hogy a tárfiók neve a feladat küldésekor lesz megadva a --storage-account-name paraméterrel vagy az `AZURE_BATCHAI_STORAGE_ACCOUNT` környezeti változóval a számítógépen.

## <a name="submit-the-job"></a>Feladat küldése

A fürtön lévő feladatot a következő paranccsal küldheti el:

```azurecli
az batchai job create -n cntk_python_1 -r nc6 -g batchai.quickstart -c job.json --storage-account-name <storage account name>
```

Példa a kimenetre:
```
{
  "additionalProperties": {},
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "additionalProperties": {},
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/clusters/nc6",
    "resourceGroup": "batchai.quickstart"
  },
  "cntkSettings": {
    "additionalProperties": {},
    "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py"
  },
  "constraints": {
    "additionalProperties": {},
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": null,
  "creationTime": "2018-04-11T21:48:10.303000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": null,
  "executionState": "queued",
  "executionStateTransitionTime": "2018-04-11T21:48:10.303000+00:00",
  "experimentName": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/jobs/cntk_python_1",
  "inputDirectories": null,
  "jobOutputDirectoryPathSegment": "00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/b9576bae-e878-4fb2-9390-2e962356b5b1",
  "jobPreparation": null,
  "location": null,
  "mountVolumes": {
    "additionalProperties": {},
    "azureBlobFileSystems": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "containerName": "data",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "mountOptions": null,
        "relativeMountPath": "data"
      }
    ],
    "azureFileShares": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>,
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/logs",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "logs"
      },
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/scripts",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "scripts"
      }
    ],
    "fileServers": null,
    "unmanagedFileSystems": null
  },
  "name": "cntk_python_1",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "additionalProperties": {},
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
      "pathSuffix": null,
      "type": "custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T21:48:11.577000+00:00",
  "pyTorchSettings": null,
  "resourceGroup": "batchai.quickstart",
  "secrets": null,
  "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "cntk",
  "type": "Microsoft.BatchAI/Jobs"
}
```

# <a name="monitor-job-execution"></a>A feladat végrehajtásának monitorozása

A betanítási szkript az `stderr.txt` fájlban jelenti a betanítás előrehaladását. Ez a fájl a szabványos kimeneti könyvtárban található. Az előrehaladás a következő paranccsal monitorozható:

```azurecli
az batchai job file stream -n cntk_python_1 -g batchai.quickstart -f stderr.txt
```

Példa a kimenetre:
```
File found with URL "https://<YOU STORAGE ACCOUNT>.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>/stdouterr/stderr.txt?sv=2016-05-31&sr=f&sig=n86JK9YowV%2BPQ%2BkBzmqr0eud%2FlpRB%2FVu%2FFlcKZx192k%3D&se=2018-04-11T23%3A05%3A54Z&sp=rl". Start streaming
Selected GPU[0] Tesla K80 as the process wide default device.
-------------------------------------------------------------------
Build info:

        Built time: Jan 31 2018 15:03:41
        Last modified date: Tue Jan 30 03:26:13 2018
        Build type: release
        Build target: GPU
        With 1bit-SGD: no
        With ASGD: yes
        Math lib: mkl
        CUDA version: 9.0.0
        CUDNN version: 7.0.4
        Build Branch: HEAD
        Build SHA1: a70455c7abe76596853f8e6a77a4d6de1e3ba76e
        MPI distribution: Open MPI
        MPI version: 1.10.7
-------------------------------------------------------------------
Training 98778 parameters in 10 parameter tensors.

Learning rate per 1 samples: 0.001
Momentum per 1 samples: 0.0
Finished Epoch[1 of 40]: [Training] loss = 0.405960 * 60000, metric = 13.01% * 60000 21.741s (2759.8 samples/s);
Finished Epoch[2 of 40]: [Training] loss = 0.106030 * 60000, metric = 3.09% * 60000 3.638s (16492.6 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.078542 * 60000, metric = 2.32% * 60000 3.477s (17256.3 samples/s);
...
Final Results: Minibatch[1-11]: errs = 0.54% * 10000
```

A streamelés leáll, amikor a feladat (sikeresen vagy sikertelenül) befejeződött.

# <a name="inspect-generated-model-files"></a>Létrehozott modellfájlok vizsgálata

A feladat a kimeneti könyvtárban tárolja a létrehozott modellfájlokat, és az `id` attribútum értéke `MODEL` lesz. A következő paranccsal listázhatja a modellfájlokat és lekérheti a letöltési URL-címeket:

```azurecli
az batchai job file list -n cntk_python_1 -g batchai.quickstart -d MODEL
```

Példa a kimenetre:
```
[
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:51+00:00",
    "name": "ConvNet_MNIST_0.dnn"
  },
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:55+00:00",
    "name": "ConvNet_MNIST_1.dnn"
  },
...

```

A létrehozott fájlok vizsgálatához a portál vagy az Azure Storage Explorer is használható. A különböző feladatok kimeneteinek megkülönböztetése érdekében a Batch AI mindegyikhez egy egyedi mappaszerkezet hoz létre. A kimenetet tartalmazó mappa elérési útját az elküldött feladat `jobOutputDirectoryPathSegment` attribútuma jelzi:

```azurecli
az batchai job show -n cntk_python_1 -g batchai.quickstart --query jobOutputDirectoryPathSegment
```

Példa a kimenetre:
```
"00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>"
```

# <a name="delete-resources"></a>Erőforrások törlése

Az erőforráscsoportot és az összes lefoglalt erőforrást a következő paranccsal törölheti:

```azurecli
az batchai group delete -n batchai.quickstart -y
```
