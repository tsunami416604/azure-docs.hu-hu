---
title: Oktatóanyag – Azure Red Hat OpenShift-fürt törlése
description: Ebből az oktatóanyagból megtudhatja, hogyan törölhet egy Azure Red Hat OpenShift-fürtöt az Azure CLI használatával
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c335236a2b0b05f03bef1ebef37f1129a5d0352b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278776"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Oktatóanyag: Azure Red Hat OpenShift-fürt törlése

Ez az oktatóanyag vége. Ha befejezte a minta-fürt tesztelését, a következőképpen törölheti és hozzárendelheti a hozzá tartozó erőforrásokat, így nem számít fel díjat a nem használt eszközökért.

A sorozat harmadik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Red Hat OpenShift-fürt törlése

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md)
> * [Azure Red Hat OpenShift-fürt skálázása](tutorial-scale-cluster.md)
> * Azure Red Hat OpenShift-fürt törlése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Hozzon létre egy fürtöt az [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md) oktatóanyag alapján.

## <a name="step-1-sign-in-to-azure"></a>1\. lépés: bejelentkezés az Azure-ba

Ha helyileg futtatja az Azure CLI-t, futtassa `az login` az Azure-ba való bejelentkezéshez.

```bash
az login
```

Ha több előfizetéshez is rendelkezik hozzáféréssel, futtassa `az account set -s {subscription ID}` a használni kívánt előfizetéssel `{subscription ID}` cserélje le.

## <a name="step-2-delete-the-cluster"></a>2\. lépés: a fürt törlése

Nyisson meg egy bash-terminált, és állítsa a CLUSTER_NAME változót a fürt nevére:

```bash
CLUSTER_NAME=yourclustername
```

Most törölje a fürtöt:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

A rendszer megkérdezi, hogy szeretné-e törölni a fürtöt. Miután megerősítte a `y`, több percet is igénybe vehet, hogy törölje a fürtöt. A parancs befejeződése után a teljes erőforráscsoport és az abban belüli összes erőforrás törölve lesz, beleértve a fürtöt is.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Fürt törlése a Azure Portal használatával

Másik lehetőségként törölheti a fürt társított erőforrás-csoportját az online Azure Portal használatával. Az erőforráscsoport neve megegyezik a fürt nevével.

Jelenleg a fürt létrehozásakor létrehozott `Microsoft.ContainerService/openShiftManagedClusters` erőforrás rejtve marad a Azure Portalban. Az `Resource group` nézetben tekintse meg az `Show hidden types` az erőforráscsoport megtekintéséhez.

![Képernyőkép a rejtett típus jelölőnégyzetről](./media/aro-portal-hidden-type.png)

Az erőforráscsoport törlése törli az Azure Red Hat OpenShift-fürt létrehozásakor létrehozott összes kapcsolódó erőforrást.

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:
> [!div class="checklist"]
> * Azure Red Hat OpenShift-fürt törlése

További információ a OpenShift és a hivatalos [Red Hat OpenShift dokumentációjának](https://docs.openshift.com/aro/welcome/index.html) használatáról
