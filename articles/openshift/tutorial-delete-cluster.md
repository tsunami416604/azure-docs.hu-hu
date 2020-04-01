---
title: Oktatóanyag – Egy Azure Red Hat OpenShift-fürt törlése
description: Ebből az oktatóanyagból megtudhatja, hogyan törölhet egy Azure Red Hat OpenShift-fürtöt az Azure CLI használatával.
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c335236a2b0b05f03bef1ebef37f1129a5d0352b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76278776"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Oktatóanyag: Azure Red Hat OpenShift-fürt törlése

Ez az oktatóanyag vége. Amikor befejezte a mintafürt tesztelését, a következőképpen törölheti azt és a hozzá tartozó erőforrásokat, hogy ne kelljen fizetnie azért, amit nem használ.

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

* Hozzon létre egy fürtöt az [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md) oktatóanyag követésével.

## <a name="step-1-sign-in-to-azure"></a>1. lépés: Bejelentkezés az Azure-ba

Ha az Azure CLI-t helyileg `az login` futtatja, futtassa a bejelentkezést az Azure-ba.

```bash
az login
```

Ha több előfizetéshez is hozzáfér, futtassa `az account set -s {subscription ID}` a cserélni `{subscription ID}` a használni kívánt előfizetéssel.

## <a name="step-2-delete-the-cluster"></a>2. lépés: A fürt törlése

Nyisson meg egy Bash terminált, és állítsa a változó CLUSTER_NAME a fürt nevére:

```bash
CLUSTER_NAME=yourclustername
```

Most törölje a fürtöt:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

A rendszer megkérdezi, hogy törölni kívánja-e a fürtöt. Miután megerősítette `y`a , a fürt törlése több percet vesz igénybe. Amikor a parancs befejeződik, a teljes erőforráscsoport és a benne lévő összes erőforrás törlődik, beleértve a fürtöt is.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Fürt törlése az Azure Portal használatával

Másik lehetőségként törölheti a fürt társított erőforráscsoportját az online Azure Portalon keresztül. Az erőforráscsoport neve megegyezik a fürt nevével.

Jelenleg a `Microsoft.ContainerService/openShiftManagedClusters` fürt létrehozásakor létrehozott erőforrás rejtve van az Azure Portalon. A `Resource group` nézetben `Show hidden types` jelölje be az erőforráscsoport megtekintéséhez.

![Képernyőkép a rejtett típus jelölőnégyzetről](./media/aro-portal-hidden-type.png)

Az erőforráscsoport törlése törli az összes kapcsolódó erőforrást, amely az Azure Red Hat OpenShift fürt létrehozásakor jön létre.

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:
> [!div class="checklist"]
> * Azure Red Hat OpenShift-fürt törlése

További információ az OpenShift és a Red [Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) hivatalos dokumentációjának használatáról
