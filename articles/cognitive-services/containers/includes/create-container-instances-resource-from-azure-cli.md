---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre Azure Container instance-erőforrást az Azure CLI-vel.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/06/2020
ms.author: dapine
ms.openlocfilehash: 700a04b58c13a9c7fd5301875226ca234cabeb96
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689461"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Azure Container instance-erőforrás létrehozása az Azure CLI-ből

Az alábbi YAML határozza meg az Azure Container instance-erőforrást. Másolja és illessze be a tartalmakat egy új fájlba, `my-aci.yaml`, és cserélje le a kommentált értékeket saját értékre. Tekintse meg a [sablon formátumát][template-format] az érvényes YAML. Tekintse meg a [tároló adattárait és lemezképeit][repositories-and-images] a rendelkezésre álló képnevekhez és a hozzájuk tartozó adattárhoz. A Container instances YAML-hivatkozásával kapcsolatos további információkért lásd [: YAML-hivatkozás: Azure Container instances][aci-yaml-ref].

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials: # This is required when pulling a non-public image
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
properties:
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> Nem minden helyen ugyanaz a processzor és a memória rendelkezésre állása. A tárolók és az operációs rendszerek számára elérhető erőforrások listázásához tekintse meg a [hely és erőforrások][location-to-resource] táblát.

Az [`az container create`][azure-container-create] parancshoz létrehozott YAML-fájlra támaszkodunk. Az Azure CLI-ből futtassa a `az container create` parancsot a saját `<resource-group>` helyett. Emellett a YAML-telepítésben lévő értékek biztonságossá tételéhez tekintse meg a [biztonságos értékeket][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

A parancs kimenete `Running...`, ha érvényes, egy kis ideig, miután a kimenet módosult egy JSON-karakterláncra, amely az újonnan létrehozott ACI-erőforrást jelképezi. A tároló képe több, mint valószínű, hogy egy ideig nem érhető el, de az erőforrás már telepítve van.

> [!TIP]
> Ügyeljen arra, hogy a nyilvános előzetes verziójú Azure kognitív szolgáltatás ajánlatait is figyelemmel kísérje, mivel a YAML ennek megfelelően kell módosítani, hogy az megfeleljen a helynek.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
