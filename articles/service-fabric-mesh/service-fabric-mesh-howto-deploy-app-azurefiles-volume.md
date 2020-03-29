---
title: Azure-fájlokon alapuló kötet használata service fabric mesh alkalmazásban
description: Ismerje meg, hogyan tárolhatja az állapotot egy Azure Service Fabric Mesh alkalmazásban egy Azure Files alapú kötet csatlakoztatásával egy szolgáltatáson belül az Azure CLI használatával.
author: dkkapur
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 5bb7ab6c861d958f6811ca852363c59cfced3940
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718820"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Azure-fájlokon alapuló kötet csatlakoztatása egy Service Fabric Mesh alkalmazásban 

Ez a cikk ismerteti, hogyan csatlakoztatása egy Azure-fájlok alapú kötet egy Service Fabric Mesh-alkalmazás szolgáltatásában.  Az Azure Files kötet-illesztőprogram egy Docker-kötet-illesztőprogram, amely az Azure Files-megosztás takarásának egy tárolóhoz való csatlakoztatására szolgál, amelyet a szolgáltatásállapot megőrzéséhez használ. A kötetek általános célú fájltárolást biztosítanak, és lehetővé teszik a fájlok normál lemezI/O-fájl API-k használatával történő olvasását/írását.  Ha többet szeretne megtudni a kötetekről és az alkalmazásadatok tárolására szolgáló lehetőségekről, olvassa el [a tárolóállapotot.](service-fabric-mesh-storing-state.md)

Kötet csatlakoztatása egy szolgáltatásban, hozzon létre egy köteterőforrást a Service Fabric Mesh alkalmazásban, és majd hivatkozzon erre a kötetre a szolgáltatásban.  A köteterőforrás deklarálása és a szolgáltatáserőforrásban való hivatkozása a [YAML-alapú erőforrásfájlokban](#declare-a-volume-resource-and-update-the-service-resource-yaml) vagy a [JSON-alapú telepítési sablonban](#declare-a-volume-resource-and-update-the-service-resource-json)végezhető el. A kötet csatlakoztatása előtt hozzon létre egy Azure-tárfiókot és egy [fájlmegosztást az Azure Files alkalmazásban.](/azure/storage/files/storage-how-to-create-file-share)

## <a name="prerequisites"></a>Előfeltételek
> [!NOTE]
> **Ismert probléma a Windows RS5 fejlesztői gépen történő telepítéssel kapcsolatban:** Van egy nyitott hiba Powershell parancsmag gal New-SmbGlobalMapping RS5 Windows-gépeken, amely megakadályozza az Azurefile volumes csatlakoztatását. Az alábbiakban mintahiba merül fel, amikor az AzureFile alapú kötet helyi fejlesztői gépen van csatlakoztatva.
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
A probléma megoldása: 1)Futtassa a parancs alatt Powershell-rendszergazdaként és 2)Indítsa újra a számítógépet.
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

Használhatja az Azure Cloud Shell vagy az Azure CLI helyi telepítését a cikk befejezéséhez. 

Ha az Azure CLI-t helyileg `az --version` szeretné használni `azure-cli (2.0.43)`ezzel a cikkel, győződjön meg arról, hogy legalább a visszatér.  Telepítse (vagy frissítse) az Azure Service Fabric Mesh CLI bővítménymodult az alábbi [utasítások szerint.](service-fabric-mesh-howto-setup-cli.md)

Jelentkezzen be az Azure-ba, és állítsa be az előfizetést:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Tárfiók és fájlmegosztás létrehozása (nem kötelező)
Az Azure Files-kötet csatlakoztatásához tárfiók és fájlmegosztás szükséges.  Használhat egy meglévő Azure-tárfiókot és fájlmegosztást, vagy hozhat létre erőforrásokat:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>A tárfiók nevének és kulcsának, valamint a fájlmegosztás nevének beszereznie
A tárfiók neve, a tárfiók kulcsa és `<storageAccountName>`a `<storageAccountKey>`fájlmegosztás neve a , és `<fileShareName>` a következő szakaszokban található. 

Sorolja fel a tárfiókokat, és a használni kívánt fájlmegosztással együtt a tárfiók nevét:
```azurecli-interactive
az storage account list
```

A fájlmegosztás nevének beszerezése:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

A tárfiók kulcsának beszereznie ("key1"):
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

Ezeket az értékeket az Azure Portalon is [megtalálhatja:](https://portal.azure.com)
* `<storageAccountName>`- A **Tárfiókok csoportban**a fájlmegosztás létrehozásához használt tárfiók neve.
* `<storageAccountKey>`- Válassza ki a tárfiókot **a Tárfiókok csoportban,** majd válassza **az Access kulcsok at,** és használja a **key1**alatti értéket.
* `<fileShareName>`- Válassza ki a tárfiókot **a Tárfiókok csoportban,** majd válassza a **Fájlok**lehetőséget. A használandó név a létrehozott fájlmegosztás neve.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Köteterőforrás deklarálása és a szolgáltatáserőforrás (JSON) frissítése

Adja hozzá az `<fileShareName>` `<storageAccountName>`előző `<storageAccountKey>` lépésben talált paramétereket és a , és az értékeket. 

Hozzon létre egy kötet erőforrást az Alkalmazás erőforrás egyenrangú jaként. Adja meg a nevet és a szolgáltatót ("SFAzureFile" az Azure Files alapú kötet használatához). A `azureFileParameters`alkalmazásban adja meg `<fileShareName>`a `<storageAccountName>`, `<storageAccountKey>` és az előző lépésben található értékek paramétereit.

A kötet csatlakoztatásához a `volumeRefs` szolgáltatásban, adjunk hozzá egy az `codePackages` elem a szolgáltatás.  `name`a kötet erőforrásazonosítója (vagy a köteterőforrás központi telepítési sablonparamétere) és a volume.yaml erőforrásfájlban deklarált kötet neve.  `destinationPath`az a helyi könyvtár, amelyhez a kötet csatlakoztatva lesz.

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

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Köteterőforrás deklarálása és a szolgáltatáserőforrás (YAML) frissítése

Adjon hozzá egy új *volume.yaml* fájlt az *alkalmazás erőforráskönyvtárában.*  Adja meg a nevet és a szolgáltatót ("SFAzureFile" az Azure Files alapú kötet használatához). `<fileShareName>`, `<storageAccountName>`és `<storageAccountKey>` az előző lépésben talált értékek.

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

Frissítse a *service.yaml* fájlt a *Service Resources* könyvtárban a kötet csatlakoztatásához a szolgáltatásban.  Adja `volumeRefs` hozzá az `codePackages` elemet az elemhez.  `name`a kötet erőforrásazonosítója (vagy a köteterőforrás központi telepítési sablonparamétere) és a volume.yaml erőforrásfájlban deklarált kötet neve.  `destinationPath`az a helyi könyvtár, amelyhez a kötet csatlakoztatva lesz.

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

- Tekintse meg az Azure Files kötetminta-alkalmazást a [GitHubon.](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter)
- A Service Fabric-erőforrásmodellel kapcsolatos további tudnivalókért lásd a [Service Fabric Mesh-erőforrásmodellt](service-fabric-mesh-service-fabric-resources.md) bemutató cikket.
- A Service Fabric Meshsel kapcsolatos további információkért olvassa el a [Service Fabric Mesh áttekintésével](service-fabric-mesh-overview.md) foglalkozó cikket.
