---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre Azure Container instance-erőforrást az Azure CLI-vel.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 2080d283c6cb7466dcb4847a81d76a4c3109217a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012160"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Azure Container instance-erőforrás létrehozása az Azure CLI-ből

Az alábbi YAML határozza meg az Azure Container instance-erőforrást. Másolja és illessze be a tartalmat egy új fájlba, és `my-aci.yaml` a megnevezett értékeket cserélje le a saját megjegyzésére. Tekintse meg a [sablon formátumát][template-format] az érvényes YAML. Tekintse meg a [tároló adattárait és lemezképeit][repositories-and-images] a rendelkezésre álló képnevekhez és a hozzájuk tartozó adattárhoz.

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials:
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

A [`az container create`][azure-container-create] parancshoz létrehozott YAML-fájlra támaszkodunk. Az Azure CLI-ből futtassa a `az container create` parancsot, `<resource-group>` és cserélje le a alkalmazást sajátra. Emellett a YAML-telepítésben lévő értékek biztonságossá tételéhez tekintse meg a [biztonságos értékeket][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

A parancs `Running...` kimenete ha érvényes, egy kis ideig a kimenet egy JSON-karakterláncra változik, amely az újonnan létrehozott ACI-erőforrást jelképezi. A tároló képe több, mint valószínű, hogy egy ideig nem érhető el, de az erőforrás már telepítve van.

> [!TIP]
> Ügyeljen arra, hogy a nyilvános előzetes verziójú Azure kognitív szolgáltatás ajánlatait is figyelemmel kísérje, mivel a YAML ennek megfelelően kell módosítani, hogy az megfeleljen a helynek.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format

[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
