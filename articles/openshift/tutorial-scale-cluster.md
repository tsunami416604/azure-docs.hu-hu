---
title: Oktatóanyag – Azure Red Hat OpenShift-fürt méretezése
description: Megtudhatja, hogyan méretezheti Microsoft Azure Red Hat OpenShift-fürtöt az Azure CLI használatával
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: bf9172f0c84834c951446520ff0bfcc3ef756c9c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278308"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Oktatóanyag: Azure Red Hat OpenShift-fürt méretezése

Ez az oktatóanyag egy sorozat második része. Megtudhatja, hogyan hozhat létre Microsoft Azure Red Hat OpenShift-fürtöt az Azure CLI használatával, méretezheti, majd törölheti az erőforrások törléséhez.

A sorozat második részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Red Hat OpenShift-fürt méretezése

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md)
> * Azure Red Hat OpenShift-fürt méretezése
> * [Azure Red Hat OpenShift-fürt törlése](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Hozzon létre egy fürtöt az [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md) oktatóanyag alapján.

## <a name="step-1-sign-in-to-azure"></a>1\. lépés: bejelentkezés az Azure-ba

Ha helyileg futtatja az Azure CLI-t, futtassa `az login` az Azure-ba való bejelentkezéshez.

```bash
az login
```

Ha több előfizetéshez is rendelkezik hozzáféréssel, futtassa `az account set -s {subscription ID}` a használni kívánt előfizetéssel `{subscription ID}` cserélje le.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>2\. lépés: a fürt méretezése további csomópontokkal

Egy bash-terminálon állítsa a CLUSTER_NAME változót a fürt nevére:

```bash
CLUSTER_NAME=yourclustername
```

Most méretezze át a fürtöt öt csomópontra az Azure CLI használatával:

```bash
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Néhány perc elteltével a `az openshift scale` sikeresen befejeződik, és egy JSON-dokumentumot ad vissza, amely a méretezett fürt részleteit tartalmazza.

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * Azure Red Hat OpenShift-fürt méretezése

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift-fürt törlése](tutorial-delete-cluster.md)
