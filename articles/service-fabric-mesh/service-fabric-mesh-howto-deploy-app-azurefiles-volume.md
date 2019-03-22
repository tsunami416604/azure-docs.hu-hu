---
title: Használhatja az Azure Files-alapú kötet egy Service Fabric-háló alkalmazásban |} A Microsoft Docs
description: Megtudhatja, hogyan állapot tárolásához az Azure Service Fabric-háló alkalmazások az Azure Files-alapú kötet belül az Azure CLI használatával csatlakoztatja.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 4cb7d04c01ae7173e63778f2768b2f9561dff11d
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200956"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Az Azure Files-alapú kötet egy Service Fabric-háló alkalmazás csatlakoztatása 

Ez a cikk ismerteti, hogyan csatlakoztathat a Service Fabric-háló-alkalmazásba szolgáltatásként az Azure Files-alapú kötet.  Az Azure Files kötet illesztőprogramja csatlakoztatása egy Azure-fájlmegosztási egy tárolóba, amellyel szolgáltatás állapotának megőrzéséhez használja a Docker kötet illesztőprogramot. Kötetek általános célú a file storage biztosítanak, és lehetővé teszi olvasási/írási fájlt normál lemez i/o-fájl API-k használatával.  További információ a kötetek és alkalmazások adatainak tárolására szolgáló beállításai, olvassa el [állapot tárolására](service-fabric-mesh-storing-state.md).

Egy szolgáltatásban kötet csatlakoztatása, kötet erőforrás létrehozása a Service Fabric-háló alkalmazásban, és majd hivatkozni az adott kötet a szolgáltatásban.  A köteterőforrás deklaráló és hivatkozna rá a szolgáltatás-erőforrás végezhető vagy a [YAML-alapú erőforrásfájlok](#declare-a-volume-resource-and-update-the-service-resource-yaml) vagy a [JSON-alapú központi telepítési sablont](#declare-a-volume-resource-and-update-the-service-resource-json). Mielőtt a kötet csatlakoztatása, először hozzon létre egy Azure storage-fiókot és a egy [fájlmegosztás az Azure Files](/azure/storage/files/storage-how-to-create-file-share).

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk végrehajtásához használhatja az Azure Cloud Shell vagy az Azure parancssori felület helyi telepítése. 

Az Azure CLI helyileg használja ezt a cikket, ellenőrizze, hogy `az --version` adja vissza a legalább `azure-cli (2.0.43)`.  Az Azure Service Fabric háló parancssori bővítmény modul a következő telepítéséhez (vagy frissítéséhez) [utasításokat](service-fabric-mesh-howto-setup-cli.md).

Jelentkezzen be az Azure-ba, és az előfizetés beállításához:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Hozzon létre egy storage-fiók és -fájlmegosztást (nem kötelező)
Az Azure Files-kötet csatlakoztatási szükséges egy storage-fiók és -fájlmegosztást.  Használhat meglévő Azure storage-fiók és a fájl megosztást, vagy hozzon létre erőforrásokat:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>A tárfiók nevét és kulcsát, és a fájlmegosztás nevét
A fájlmegosztás nevét, a tárfiók nevét és tárfiókkulcs néven hivatkozott `<storageAccountName>`, `<storageAccountKey>`, és `<fileShareName>` az alábbi szakaszokban található. 

A tárfiókok listája, és a tárfiók a fájlmegosztáshoz használni kívánt nevét:
```azurecli-interactive
az storage account list
```

Kérje le a fájlmegosztás neve:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

Kérje le a tárfiók-kulcsot ("1. kulcs"):
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

Ezeket az értékeket is megkeresheti a [az Azure portal](https://portal.azure.com):
* `<storageAccountName>` -A **Tárfiókok**, a fájlmegosztás létrehozásához használja a tárfiók nevére.
* `<storageAccountKey>` – Válassza ki a storage-fiókja alatt **Tárfiókok** majd **hozzáférési kulcsok** az értéket, és **key1**.
* `<fileShareName>` -Válassza ki a storage-fiókja alatt **Tárfiókok** majd **fájlok**. A nevét, a létrehozott fájlmegosztás neve.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Deklaráljon egy kötet erőforrás és a szolgáltatás-erőforrás (JSON) frissítése

Paraméterek hozzáadása a `<fileShareName>`, `<storageAccountName>`, és `<storageAccountKey>` , az előző lépésben található értékekre. 

Az alkalmazás erőforrás társ hozzon létre egy kötet erőforrás. Adjon meg egy nevet és a szolgáltató (az Azure Files-alapú mennyiségi használandó "SFAzureFile" jelöli). A `azureFileParameters`, adja meg a paramétereket a `<fileShareName>`, `<storageAccountName>`, és `<storageAccountKey>` , az előző lépésben található értékekre.

A szolgáltatás a kötet csatlakoztatásához, adjon hozzá egy `volumeRefs` , a `codePackages` elem a szolgáltatás.  `name` az erőforrás-azonosító, a kötet (vagy egy központi telepítési sablon paramétert a köteterőforrás) és a volume.yaml erőforrás fájlban deklarált a kötet nevét.  `destinationPath` a helyi könyvtárban, amely a kötet csatlakoztatva van.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Deklaráljon egy kötet erőforrás és a szolgáltatás-erőforrás (YAML) frissítése

Vegyen fel egy új *volume.yaml* fájlt a *alkalmazás-erőforrások* könyvtárat az alkalmazás.  Adjon meg egy nevet és a szolgáltató (az Azure Files-alapú mennyiségi használandó "SFAzureFile" jelöli). `<fileShareName>`, `<storageAccountName>`, és `<storageAccountKey>` jelennek meg az előző lépésben található értékek.

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

Frissítés a *service.yaml* fájlt a *szolgáltatási erőforrások* könyvtár a szolgáltatásban a kötet csatlakoztatásához.  Adja hozzá a `volumeRefs` elem a `codePackages` elemet.  `name` az erőforrás-azonosító, a kötet (vagy egy központi telepítési sablon paramétert a köteterőforrás) és a volume.yaml erőforrás fájlban deklarált a kötet nevét.  `destinationPath` a helyi könyvtárban, amely a kötet csatlakoztatva van.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>További lépések

- Az Azure Files kötet mintaalkalmazás megtekintése [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- A Service Fabric-erőforrásmodellel kapcsolatos további tudnivalókért lásd a [Service Fabric Mesh-erőforrásmodellt](service-fabric-mesh-service-fabric-resources.md) bemutató cikket.
- A Service Fabric Meshsel kapcsolatos további információkért olvassa el a [Service Fabric Mesh áttekintésével](service-fabric-mesh-overview.md) foglalkozó cikket.
