---
title: Oktatóanyag – az Azure Red Hat OpenShift fürt törlése |} A Microsoft Docs
description: Ebben az oktatóanyagban megtudhatja, hogyan törölhet egy Azure Red Hat OpenShift-fürtöt az Azure CLI használatával
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: ce4142bdcdfb7a9ab687bb60dca91d6aab00c7bd
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080715"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Oktatóanyag: Az Azure Red Hat OpenShift fürt törlése

Ez az oktatóanyag vége. Ha elkészült, a minta fürt tesztelése, Íme törlése, és az összes kapcsolódó erőforrás, akkor nem kell fizetnie mi nem használ.

A sorozat harmadik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure Red Hat OpenShift fürt törlése

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Az Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md)
> * [Az Azure Red Hat OpenShift fürt méretezése](tutorial-scale-cluster.md)
> * Az Azure Red Hat OpenShift fürt törlése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Hozzon létre egy fürtöt az alábbi a [hozzon létre egy Azure Red Hat OpenShift fürtöt](tutorial-create-cluster.md) oktatóanyag.

## <a name="step-1-sign-in-to-azure"></a>1. lépés: Bejelentkezés az Azure-ba

Ha helyileg futtatja az Azure CLI, futtassa `az login` bejelentkezni az Azure-bA.

```bash
az login
```

Ha több előfizetést is hozzáférhet, futtassa `az account set -s {subscription ID}` cseréje `{subscription ID}` a használni kívánt előfizetést.

## <a name="step-2-delete-the-cluster"></a>2. lépés: A fürt törlése

Nyisson meg egy Bash terminált, és adja meg a változó fürtnév a fürt nevére:

```bash
CLUSTER_NAME=yourclustername
```

Most már törölje a fürtöt:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

Ön kéri, hogy törli a fürtöt. Miután meggyőződött a `y`, törölje a fürtöt néhány percet vesz igénybe. A parancs befejeződésekor a teljes erőforrás-csoport és az összes erőforrás belül, beleértve a fürt törlődik.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Az Azure portal használatával egy fürt törlése

Azt is megteheti törölheti a fürtöt az online az Azure Portalon keresztül, a társított erőforráscsoportokhoz. Az erőforráscsoport neve ugyanaz, mint a fürt nevét.

Jelenleg a `Microsoft.ContainerService/openShiftManagedClusters` erőforrás, amely a fürt létrehozásakor jön létre az Azure Portalon rejtve marad. Az a `Resource group` nézet, ellenőrzés `Show hidden types` az erőforráscsoport megtekintéséhez.

![Képernyőkép a rejtett írja be a jelölőnégyzetet](./media/aro-portal-hidden-type.png)

Az erőforráscsoport törlésével a kapcsolódó erőforrásokat az Azure Red Hat OpenShift fürt készítése során létrehozott.

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:
> [!div class="checklist"]
> * Az Azure Red Hat OpenShift fürt törlése

További információ a hivatalos az OpenShift [Red Hat OpenShift – dokumentáció](https://access.redhat.com/documentation/openshift_dedicated/3/)