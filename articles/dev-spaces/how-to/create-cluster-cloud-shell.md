---
title: Kubernetes-fürt létrehozása az Azure dev Spaces szolgáltatással engedélyezve – Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Megtudhatja, hogyan hozhat létre gyorsan egy Kubernetes-fürtöt az Azure dev Spaces szolgáltatáshoz közvetlenül a böngészőből anélkül, hogy bármit kellene telepítenie.
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
ms.openlocfilehash: 35ea52b940c74b34817b4ed12d224de83f4dda74
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771104"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Kubernetes-fürt létrehozása az Azure dev Spaces szolgáltatással Azure Cloud Shell

A [Azure Cloud Shell](/azure/cloud-shell) használatával létrehozhat egy Azure Kubernetes Service-fürtöt az oldal **kipróbálása** gombjának használatával. Ha nincs bejelentkezve, kövesse az utasításokat az Azure-fiókkal való bejelentkezéshez, majd írja be a parancsokat a Azure Cloud Shell üzenet megjelenésekor.

## <a name="create-the-cluster"></a>A fürt létrehozása

Először hozza létre az erőforráscsoportot egy olyan [régióban, amely támogatja az Azure dev Spaces használatát][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Hozzon létre egy Kubernetes-fürtöt az alábbi paranccsal:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

A fürt létrehozása néhány percet vesz igénybe.  Ha elkészült, a kimenet JSON formátumban jelenik meg. Keresse meg a `provisioningState`, és ellenőrizze, hogy `Succeeded`-e.

## <a name="next-steps"></a>Következő lépések

A teljes oktatóanyagokra mutató hivatkozásokat az [Azure dev Spaces](/azure/dev-spaces/) című részben tekintheti meg.

> [!IMPORTANT]
> Az Azure dev Spaces rövid útmutatók és oktatóanyagok az Azure dev Spaces CLI használatával végeznek műveleteket. Az Azure dev Spaces CLI nem telepíthető a Azure Cloud Shell.


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service