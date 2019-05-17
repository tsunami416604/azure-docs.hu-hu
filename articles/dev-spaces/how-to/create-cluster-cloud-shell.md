---
title: Az Azure Cloud Shellt használja az Azure fejlesztési tárolóhelyek engedélyezve van a Kubernetes-fürt létrehozása
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: conceptual
description: Ismerje meg, hogyan hozhat létre gyorsan egy Kubernetes-fürtöt az Azure fejlesztési tárolóhelyek engedélyezve közvetlenül a böngészőből bármit telepítése nélkül.
keywords: Docker, Kubernetes, Azure, az AKS, az Azure Kubernetes Service, tárolók, Helm, a szolgáltatás háló, a szolgáltatás háló útválasztás, a kubectl, a k8s
ms.openlocfilehash: cf518fb0062a44619894059a1b7369fc92ba4f5d
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597185"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Kubernetes-fürt létrehozása az Azure Cloud Shell-lel

Használhat [Azure Cloud Shell](/azure/cloud-shell) fürt létrehozása az Azure fejlesztési tárolóhelyek használatával a **Kipróbálom** gombra ezen az oldalon. Ha nem jelentkezett be, kövesse az utasításokat követve jelentkezzen be egy Azure-fiókjával, majd írja be a parancsokat az Azure Cloud Shell parancssornál, amikor az megjelenik.

## <a name="create-the-cluster"></a>A fürt létrehozása

Először hozza létre az erőforráscsoportot egy [régióban, amely támogatja az Azure fejlesztési tárolóhelyek](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams).

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Hozzon létre egy Kubernetes-fürtöt az alábbi paranccsal:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

A fürt létrehozása néhány percet vesz igénybe.  Amikor végzett, a kimenet JSON formátumban jelenik meg. Keressen `provisioningState` , és ellenőrizze, hogy rendelkezik `Succeeded`.

## <a name="next-steps"></a>További lépések

Lásd: [Azure fejlesztési tárolóhelyek](/azure/dev-spaces/) teljes oktatóanyagok mutató hivatkozásokat.
