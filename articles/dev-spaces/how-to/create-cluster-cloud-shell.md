---
title: Az Azure Cloud Shellt használja az Azure fejlesztési tárolóhelyek engedélyezve van a Kubernetes-fürt létrehozása
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: conceptual
description: Ismerje meg, hogyan hozhat létre gyorsan egy Kubernetes-fürtöt az Azure fejlesztési tárolóhelyek engedélyezve közvetlenül a böngészőből bármit telepítése nélkül.
keywords: Docker, Kubernetes, Azure, az AKS, az Azure Kubernetes Service, tárolók, Helm, a szolgáltatás háló, a szolgáltatás háló útválasztás, a kubectl, a k8s
ms.openlocfilehash: 1217b2cce03c36e4fd354d5cb9a58348b655a314
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193840"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Azure Cloud Shellt használja a Kubernetes-fürt létrehozása

Használhat [Azure Cloud Shell](/azure/cloud-shell) fürt létrehozása az Azure fejlesztési tárolóhelyek használatával a **Kipróbálom** gombra ezen az oldalon. Ha nem jelentkezett be, kövesse az utasításokat követve jelentkezzen be egy Azure-fiókjával, majd írja be a parancsokat az Azure Cloud Shell parancssornál, amikor az megjelenik.

## <a name="create-the-cluster"></a>A fürt létrehozása

Első lépésként létre kell hoznia az erőforráscsoportot. Válassza a jelenleg támogatott régiók valamelyikét (EastUS, EastUS2, CentralUS, WestUS2, WestEurope, SoutheastAsia, CanadaCentral vagy CanadaEast).

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Hozzon létre egy Kubernetes-fürtöt az alábbi paranccsal:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --kubernetes-version 1.10.9
```

A fürt létrehozása néhány percet vesz igénybe.  Amikor végzett, a kimenet JSON formátumban jelenik meg. Keressen `provisioningState` , és ellenőrizze, hogy rendelkezik `Succeeded`.

## <a name="next-steps"></a>További lépések

Lásd: [Azure fejlesztési tárolóhelyek](/azure/dev-spaces/) teljes oktatóanyagok mutató hivatkozásokat.
