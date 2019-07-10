---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre egy Azure container instance-erőforrást az Azure parancssori felületen.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 5e7a3d849f726ae4dbbd559d541464404e427775
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717079"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Az Azure Container Instance-erőforrás létrehozásához az Azure parancssori felületen

Az alábbi YAML határozza meg az Azure Container Instance-erőforrást. Másolja és illessze be a tartalmát egy új fájlba nevű `my-aci.yaml` , és cserélje le a megjegyzésekkel értékeket a saját. Tekintse meg a [sablon formátuma] [sablon-formant] esetében érvényes YAML. Tekintse meg a [tároló adattárak és rendszerképek][repositories-and-images] elérhető rendszerkép nevét és a megfelelő tárház.

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
> Nem minden helyen van ugyanazon Processzor- és rendelkezésre állását. Tekintse meg a [helyét és erőforrások][location-to-resource] tábla tárolók / hely és az operációs rendszer rendelkezésre álló erőforrások listáját.

Azt a létrehozott YAML-fájlt fogja támaszkodnak az [ `az container create` ][azure-container-create] parancsot. Az Azure parancssori felületen, hajtsa végre a `az container create` parancsot cserélje le a `<resource-group>` saját. Emellett biztonságossá tétele egy YAML között szereplő telepítési tekintse meg [értékek biztonságos][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

A parancs kimenete `Running...` ha érvényes, kis idő múlva változik a kimenet az újonnan létrehozott ACI-erőforrásokra JSON-karakterláncot. A tároló rendszerképét a több mint valószínűleg nem érhető el egy ideig, de az erőforrás üzembe van helyezve.

> [!TIP]
> Figyeljen a helyek nyilvános előzetes verzióban az Azure Cognitive Services-szolgáltatás ajánlatok a YAML ennek megfelelően módosítani kell egyeznie a hely lesz szükség szerint.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format

[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values