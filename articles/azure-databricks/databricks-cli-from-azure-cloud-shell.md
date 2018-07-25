---
title: Az Azure Cloud Shell a Databricks parancssori felület használata |} A Microsoft Docs
description: Ismerje meg, hogyan használható a Databricks parancssori felület az Azure Cloud Shellben.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 3ea4ebbd95237b50054fb0e344f260120d597ab5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225234"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>A Databricks parancssori felület használata az Azure Cloud Shellből

Ismerje meg, hogyan hajtson végre műveleteket a Databricks a Databricks parancssori felület az Azure Cloud Shell használatával.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Databricks-munkaterület és a fürt. Útmutatásért lásd: [Azure Databricks használatának első lépései](quickstart-create-databricks-workspace-portal.md). 

* Állítsa be a személyes hozzáférési tokent a Databricksben. Útmutatásért lásd: [felügyeleti Token](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="use-the-azure-cloud-shell"></a>Az Azure Cloud Shell használata

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
 
2. A jobb felső sarokban, kattintson a **Cloud Shell** ikonra.

   ![A Cloud Shell indítása](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Azure Cloud Shell indítása")

3. Győződjön meg arról, hogy ki **Bash** a Cloud Shell-környezet számára. Az alábbi képernyőképen látható módon a legördülő beállítás közül választhat.

   ![Válassza ki a Bash Cloud Shell környezetre](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Bash kiválasztása") 

4. Hozzon létre egy virtuális környezethez, amelyben a Databricks parancssori felület telepítése. Az alábbi kódrészletben hoz létre egy virtuális környezethez nevű `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Váltson a létrehozott virtuális környezethez.

       source databrickscli/bin/activate

6. A Databricks parancssori felület telepítéséhez.

       pip install databricks-cli

7. Hitelesítés beállítása a Databricks a hozzáférési jogkivonatot kell hozott létre, amelyek használatával az Előfeltételek részeként szerepel. Használja az alábbi parancsot:

       databricks configure --token

    Az alábbi utasításokat kapnak:

    * Először kéri, adja meg a Databricks-gazdagép. A következő formátumban írja be az értéket `https://eastus2.azuredatabricks.net`. Itt **USA keleti RÉGIÓJA 2** az az Azure-régió, amelyben létrehozta az Azure Databricks-munkaterület.

    * Ezután kéri, adja meg a jogkivonatot. Adja meg a jogkivonatot, amelyet korábban hozott létre.

Miután elvégezte ezeket a lépéseket, elkezdheti az Azure Cloud Shell a Databricks parancssori felület használatával.

## <a name="use-databricks-cli"></a>Databricks parancssori felület használata

Most elkezdheti a Databricks parancssori felület használatával. Például a következő paranccsal listázhatja az összes a Databricks-fürt, amely rendelkezik a munkaterületen.

    databricks clusters list

A következő parancs használatával a Databricks-fájlrendszer (dbfs-SZEL) eléréséhez.

    databricks fs ls


A parancsok egy teljes körű referenciáért lásd: [a Databricks parancssori felület](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).


## <a name="next-steps"></a>További lépések

* Azure CLI-vel kapcsolatos további információkért lásd: [Azure CLI áttekintése](../cloud-shell/overview.md)
* Azure CLI parancsok listájának megtekintéséhez lásd: [Azure parancssori Felületével – referencia](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* A Databricks parancssori parancsok listájának megtekintéséhez lásd: [a Databricks parancssori felület](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)


