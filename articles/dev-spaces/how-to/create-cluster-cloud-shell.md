---
title: Kubernetes-fürt létrehozása engedélyezve van az Azure Dev Spaces szolgáltatással – Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Ismerje meg, hogyan hozhat létre gyorsan egy Kubernetes-fürtöt, amely engedélyezve van az Azure Dev Spaces számára közvetlenül a böngészőből, anélkül, hogy bármit is telepítene.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, Helm, szolgáltatásháló, szolgáltatásháló útválasztás, kubectl, k8s
ms.openlocfilehash: 5e2e5cfd22eeedd3554737458caeca0b891b62fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605304"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Kubernetes-fürt létrehozása azure-beli felhőrendszerhéj-tárolóval engedélyezett Azure dev spaces-sel

Az [Azure Cloud Shell](/azure/cloud-shell) segítségével azure Kubernetes-szolgáltatásfürt létrehozásához használja a Try **It** gombot ezen az oldalon. Ha nincs bejelentkezve, kövesse az utasításokat, hogy jelentkezzen be egy Azure-fiókkal, majd írja be a parancsokat az Azure Cloud Shell parancssorba, amikor megjelenik.

## <a name="create-the-cluster"></a>A fürt létrehozása

Először hozza létre az erőforráscsoportot egy [olyan régióban, amely támogatja az Azure Dev Spaces.][supported-regions]

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Hozzon létre egy Kubernetes-fürtöt az alábbi paranccsal:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

A fürt létrehozása néhány percet vesz igénybe.  Ha elkészült, a kimenet JSON formátumban jelenik meg. Keresse `provisioningState` meg és ellenőrizze, hogy ez `Succeeded`.

## <a name="next-steps"></a>További lépések

Tekintse meg az [Azure Dev Spaces](/azure/dev-spaces/) a teljes oktatóanyagokra mutató hivatkozásokat.

> [!IMPORTANT]
> Az Azure Dev Spaces számos rövid útmutatója és oktatóanyaga az Azure Dev Spaces CLI-t használja a műveletek végrehajtásához. Az Azure Dev Spaces CLI nem telepíthető az Azure Cloud Shellben.


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service