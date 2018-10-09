---
title: Az Azure Cloud Shellt használja az Azure fejlesztési tárolóhelyek engedélyezve van a Kubernetes-fürt létrehozása |} A Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 10/04/2018
ms.topic: article
description: Ismerje meg, hogyan hozhat létre gyorsan egy Kubernetes-fürtöt az Azure fejlesztési tárolóhelyek engedélyezve közvetlenül a böngészőből bármit telepítése nélkül.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók
manager: douge
ms.openlocfilehash: f10a84a602ce152d5c428525aa50f678b50c8b41
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48871690"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Azure Cloud Shellt használja a Kubernetes-fürt létrehozása

Használhat [Azure Cloud Shell](/azure/cloud-shell) fürt létrehozása az Azure fejlesztési tárolóhelyek használatával a **Kipróbálom** gombra ezen az oldalon. Ha nem jelentkezett be, kövesse az utasításokat követve jelentkezzen be egy Azure-fiókjával, majd írja be a parancsokat az Azure Cloud Shell parancssornál, amikor az megjelenik.

## <a name="create-the-cluster"></a>A fürt létrehozása

Első lépésként létre kell hoznia az erőforráscsoportot. A jelenleg támogatott régiók (USA keleti régiója, CentralUS, WestUS2, WestEurope, CanadaCentral vagy CanadaEast) egyikét használhatja.

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Kubernetes-fürt létrehozása a következő paranccsal:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --kubernetes-version 1.11.2 --enable-addons http_application_routing
```

A fürt létrehozása néhány percet vesz igénybe.  Amikor végzett, a kimenet JSON formátumban jelenik meg. Keressen `provisioningState` , és ellenőrizze, hogy rendelkezik `Succeeded`.

## <a name="next-steps"></a>További lépések

Lásd: [Azure fejlesztési tárolóhelyek](/azure/dev-spaces/) teljes oktatóanyagok mutató hivatkozásokat.