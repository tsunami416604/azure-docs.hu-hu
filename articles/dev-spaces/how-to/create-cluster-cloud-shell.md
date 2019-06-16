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
ms.openlocfilehash: cd0c8c3c26feefe3448ada1cf1575706cd17e525
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808689"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Kubernetes-fürt létrehozása az Azure Cloud Shell-lel

Használható [Azure Cloud Shell](/azure/cloud-shell) hozhat létre az Azure Kubernetes Service-fürtök használatával a **Kipróbálom** gombra ezen az oldalon. Ha nem jelentkezett be, kövesse az utasításokat követve jelentkezzen be egy Azure-fiókjával, majd írja be a parancsokat az Azure Cloud Shell parancssornál, amikor az megjelenik.

## <a name="create-the-cluster"></a>A fürt létrehozása

Először hozza létre az erőforráscsoportot egy [régióban, amely támogatja az Azure fejlesztési tárolóhelyek][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Hozzon létre egy Kubernetes-fürtöt az alábbi paranccsal:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

A fürt létrehozása néhány percet vesz igénybe.  Amikor végzett, a kimenet JSON formátumban jelenik meg. Keressen `provisioningState` , és ellenőrizze, hogy rendelkezik `Succeeded`.

## <a name="next-steps"></a>További lépések

Lásd: [Azure fejlesztési tárolóhelyek](/azure/dev-spaces/) teljes oktatóanyagok mutató hivatkozásokat.

> [!IMPORTANT]
> Számos, az Azure fejlesztési tárolóhelyek útmutatóink és oktatóanyagaink segítségével a műveletek végrehajtása az Azure fejlesztési tárolóhelyek CLI használatával. Az Azure Cloud Shellt az Azure fejlesztési tárolóhelyek CLI nem lehet telepíteni.


[supported-regions]: ../about.md#supported-regions-and-configurations