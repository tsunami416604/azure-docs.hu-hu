---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre egy Azure container instance erőforrást az Azure CLI-ből.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e3542b976921aa45794d62cad9517984c8348ce3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875120"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Hozzon létre egy Azure Container Instance erőforrást az Azure CLI-ből

Az alábbi YAML határozza meg az Azure Container Instance erőforrást. Másolja és illessze be a `my-aci.yaml` tartalmat egy új fájlba, amelyet elnevez, és a megjegyzésekkel ellátott értékeket a sajátjára cserélje le. Az érvényes YAML [sablonformátumban][template-format] tájékformulmát. Tekintse meg a [tároló tárolók és a rendszerképek][repositories-and-images] a rendelkezésre álló rendszerképek nevét és a hozzájuk tartozó tárház. A tárolópéldányok YAML-hivatkozásáról további információt a [YAML-hivatkozás: Azure Container Instances című témakörben talál.][aci-yaml-ref]

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
> Nem minden hely rendelkezik azonos processzor- és memória-rendelkezésre állással. Tekintse meg a [hely és az erőforrások][location-to-resource] tábla a rendelkezésre álló erőforrások listázása a tárolók helyenként és operációs rendszer.

A [`az container create`][azure-container-create] parancshoz létrehozott YAML fájlra támaszkodunk. Az Azure CLI,hajtsa végre a `az container create` parancsot helyett a `<resource-group>` saját. Ezenkívül a YAML-telepítésen belüli értékek védelméhez tekintse meg a [biztonságos értékeket.][secure-values]

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

A parancs kimenete, `Running...` ha érvényes, miután valamikor a kimenet az újonnan létrehozott ACI-erőforrást képviselő JSON-karakterláncra változik. A tárolórendszerkép több mint valószínű, hogy egy ideig nem érhető el, de az erőforrás már telepítve van.

> [!TIP]
> Fordítson különös figyelmet a nyilvános előzetes verziójú Azure Cognitive Service-ajánlatok helyére, mivel a YAML-t ennek megfelelően módosítani kell, hogy megfeleljen a helynek.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
