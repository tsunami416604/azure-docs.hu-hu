---
title: Oktatóanyag – Egy Azure Red Hat OpenShift-fürt méretezése
description: Ismerje meg, hogyan skálázhat microsoft Azure Red Hat OpenShift fürtöt az Azure CLI használatával
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c6334aa20b543dfbf87fedcfe45d54bbcf7a219a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477017"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Oktatóanyag: Azure Red Hat OpenShift-fürt méretezése

Ez az oktatóanyag egy sorozat második része. Megtudhatja, hogyan hozhat létre egy Microsoft Azure Red Hat OpenShift-fürtöt az Azure CLI használatával, méretezheti, majd törölheti az erőforrások törléséhez.

A sorozat második részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Red Hat OpenShift fürt méretezése

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md)
> * Azure Red Hat OpenShift-fürt skálázása
> * [Azure Red Hat OpenShift-fürt törlése](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Hozzon létre egy fürtöt az [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md) oktatóanyag követésével.

## <a name="step-1-sign-in-to-azure"></a>1. lépés: Bejelentkezés az Azure-ba

Ha az Azure CLI-t helyileg `az login` futtatja, futtassa a bejelentkezést az Azure-ba.

```azurecli
az login
```

Ha több előfizetéshez is hozzáfér, futtassa `az account set -s {subscription ID}` a cserélni `{subscription ID}` a használni kívánt előfizetéssel.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>2. lépés: A fürt méretezése további csomópontokkal

Bash terminálról állítsa be a CLUSTER_NAME változót a fürt nevére:

```bash
CLUSTER_NAME=yourclustername
```

Most az Azure CLI használatával öt csomópontra méretezhetjük a fürtöt:

```azurecli
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Néhány perc múlva sikeresen befejeződik, `az openshift scale` és visszaadja a méretezett fürt részleteit tartalmazó JSON-dokumentumot.

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * Azure Red Hat OpenShift-fürt skálázása

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift-fürt törlése](tutorial-delete-cluster.md)
