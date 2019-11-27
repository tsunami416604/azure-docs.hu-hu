---
title: Kubernetes-fürt létrehozása az Azure dev Spaces szolgáltatással engedélyezve – Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Megtudhatja, hogyan hozhat létre gyorsan egy Kubernetes-fürtöt az Azure dev Spaces szolgáltatáshoz közvetlenül a böngészőből anélkül, hogy bármit kellene telepítenie.
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
ms.openlocfilehash: ac825caa7aacbc6ac21dd1f9fe7acbb9cf12e900
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325757"
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


[supported-regions]: ../about.md#supported-regions-and-configurations