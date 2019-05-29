---
title: Oktatóanyag – az Azure Red Hat OpenShift fürt törlése |} A Microsoft Docs
description: Ebben az oktatóanyagban megtudhatja, hogyan törölhet egy Azure Red Hat OpenShift-fürtöt az Azure CLI használatával
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: 627acbfc1f3a460cbb94e322c43445a55fce1ffa
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306168"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Oktatóanyag: Az Azure Red Hat OpenShift fürt törlése

Ez az oktatóanyag vége. Ha elkészült, a minta fürt tesztelése, Íme törlése, és az összes kapcsolódó erőforrás, akkor nem kell fizetnie mi nem használ.

A sorozat harmadik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure Red Hat OpenShift fürt törlése

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md)
> * [Azure Red Hat OpenShift-fürt skálázása](tutorial-scale-cluster.md)
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

További információ a hivatalos az OpenShift [Red Hat OpenShift – dokumentáció](https://docs.openshift.com/aro/welcome/index.html)