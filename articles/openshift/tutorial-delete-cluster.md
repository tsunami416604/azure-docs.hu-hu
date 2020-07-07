---
title: Oktatóanyag – Azure Red Hat OpenShift-fürt törlése
description: Ebből az oktatóanyagból megtudhatja, hogyan törölhet egy Azure Red Hat OpenShift-fürtöt az Azure CLI használatával
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 2de60b90eb6fb75ef013a2fd8785f1b8b616fba6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82232138"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>Oktatóanyag: Azure Red Hat OpenShift 4 fürt törlése

Ebben az oktatóanyagban, amely három részből áll, a OpenShift 4-es verzióját futtató Azure Red Hat OpenShift-fürtöt töröljük. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Azure Red Hat OpenShift-fürt törlése


## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy Azure Red Hat OpenShift-fürtöt hoztak létre és csatlakoztattak a OpenShift webkonzol használatával. Ha még nem tette meg ezeket a lépéseket, és követni szeretné a lépéseket, kezdje az [1. oktatóanyag használatával – hozzon létre egy Azure Red Hat Openshift 4 fürtöt.](tutorial-create-cluster.md)

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.75 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha helyileg futtatja az Azure CLI-t, futtassa az parancsot az Azure-ba `az login` való bejelentkezéshez.

```bash
az login
```

Ha több előfizetéshez is rendelkezik hozzáféréssel, `az account set -s {subscription ID}` futtassa `{subscription ID}` a cserét a használni kívánt előfizetéssel.

## <a name="delete-the-cluster"></a>A fürt törlése

Az előző oktatóanyagokban a következő változók lettek beállítva. 

```bash
CLUSTER=yourclustername
RESOURCE_GROUP=yourresourcegroup
```

A következő értékek használatával törölje a fürtöt:

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

Ekkor a rendszer felszólítja, hogy erősítse meg, hogy törölni kívánja-e a fürtöt. A jóváhagyását követően `y` több percet is igénybe vehet, hogy törölje a fürtöt. A parancs befejeződése után a teljes erőforráscsoport és a benne lévő összes erőforrás – beleértve a fürtöt is – törlődik.

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:
> [!div class="checklist"]
> * Azure Red Hat OpenShift 4-fürt törlése

További információ a OpenShift és a hivatalos [Red Hat OpenShift dokumentációjának](https://www.openshift.com/try) használatáról
