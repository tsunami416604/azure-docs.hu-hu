---
title: Oktatóanyag – az Azure Red Hat OpenShift fürt méretezése |} A Microsoft Docs
description: Az Azure CLI használatával a Microsoft Azure Red Hat OpenShift fürt méretezése
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: b25e17e7064006a1421142dfcd32997cb4426e8e
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305979"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Oktatóanyag: Az Azure Red Hat OpenShift fürt méretezése

Ez az oktatóanyag egy sorozat második része. Megtudhatja, hogyan hozzon létre egy Microsoft Azure Red Hat OpenShift-fürtöt az Azure CLI-vel, skálázza fel, majd törölje az erőforrások törlése.

A sorozat második részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Red Hat OpenShift fürt skálázása

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md)
> * Az Azure Red Hat OpenShift fürt méretezése
> * [Azure Red Hat OpenShift-fürt törlése](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Hozzon létre egy fürtöt az alábbi a [hozzon létre egy Azure Red Hat OpenShift fürtöt](tutorial-create-cluster.md) oktatóanyag.

## <a name="step-1-sign-in-to-azure"></a>1. lépés: Bejelentkezés az Azure-ba

Ha helyileg futtatja az Azure CLI, futtassa `az login` bejelentkezni az Azure-bA.

```bash
az login
```

Ha több előfizetést is hozzáférhet, futtassa `az account set -s {subscription ID}` cseréje `{subscription ID}` a használni kívánt előfizetést.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>2. lépés: A fürt további csomópontjainak méretezése

Az egy Bash, terminál állítsa be a változó fürtnév a fürt nevére:

```bash
CLUSTER_NAME=yourclustername
```

Most már most skálázhatja a fürt öt csomópontjának az Azure CLI használatával:

```bash
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Néhány perc múlva `az openshift scale` lesz sikeresen befejeződik és a méretezett fürt részleteit tartalmazó JSON-dokumentumok adja vissza.

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * Az Azure Red Hat OpenShift fürt méretezése

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift-fürt törlése](tutorial-delete-cluster.md)