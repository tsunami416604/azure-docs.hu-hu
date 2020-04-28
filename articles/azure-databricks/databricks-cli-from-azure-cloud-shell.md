---
title: 'A Databricks parancssori felület használata az Azure Cloud Shellből '
description: Megtudhatja, hogyan használhatja a Databricks CLI-t a Azure Cloud Shellból a Azure Databricks műveletek végrehajtásához.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: efb0d3222bfd98b15502163979425d47fa459e07
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "73605721"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>A Databricks parancssori felület használata az Azure Cloud Shellből

Megtudhatja, hogyan használhatja a Databricks CLI-t a Azure Cloud Shell a Databricks-műveletek végrehajtásához.

## <a name="prerequisites"></a>Előfeltételek

* Azure Databricks munkaterület és fürt. Útmutatásért lásd: [Azure Databricks első lépései](quickstart-create-databricks-workspace-portal.md). 

* Hozzon létre egy személyes hozzáférési jogkivonatot a Databricks-ben. Útmutatásért lásd a [jogkivonat-kezelés](/azure/databricks/dev-tools/api/latest/authentication)című témakört.

## <a name="use-the-azure-cloud-shell"></a>A Azure Cloud Shell használata

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).
 
2. Kattintson a jobb felső sarokban található **Cloud Shell** ikonra.

   ![Cloud Shell elindítása](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Az Azure Cloud Shell indítása")

3. Győződjön meg arról, hogy a Cloud Shell-környezet **bash** elemét választotta. A legördülő menüben a következő képernyőképen látható módon választhat.

   ![Bash kiválasztása a Cloud Shell-környezethez](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Bash kiválasztása") 

4. Hozzon létre egy virtuális környezetet, amelyben telepítheti a Databricks CLI-t. Az alábbi kódrészletben létrehoz egy nevű `databrickscli`virtuális környezetet.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Váltson a létrehozott virtuális környezetre.

       source databrickscli/bin/activate

6. Telepítse a Databricks CLI-t.

       pip install databricks-cli

7. Állítsa be a hitelesítést a Databricks a létrehozott hozzáférési jogkivonat használatával, amely az előfeltételek részeként jelenik meg. Használja az alábbi parancsot:

       databricks configure --token

    A következő kérdéseket fogja kapni:

    * Először meg kell adnia a Databricks-gazdagépet. Adja meg az értéket a formátumban `https://eastus2.azuredatabricks.net`. Itt az **USA 2. keleti** régiója az az Azure-régió, ahol létrehozta Azure Databricks munkaterületét.

    * Ezután meg kell adnia egy jogkivonatot. Adja meg a korábban létrehozott jogkivonatot.

A lépések elvégzése után elkezdheti a Databricks CLI használatát Azure Cloud Shell.

## <a name="use-databricks-cli"></a>A Databricks CLI használata

Most már elkezdheti használni a Databricks CLI-t. Például futtassa a következő parancsot a munkaterületen található összes Databricks-fürt listázásához.

    databricks clusters list

A következő parancs használatával is elérheti a Databricks fájlrendszert (DBFS).

    databricks fs ls


A parancsokkal kapcsolatos teljes hivatkozásért lásd: [DATABRICKS CLI](/azure/databricks/dev-tools/databricks-cli).


## <a name="next-steps"></a>További lépések

* További információ az Azure CLI-ről: az [Azure CLI áttekintése](../cloud-shell/overview.md)
* Az Azure CLI-vel kapcsolatos parancsok listájának megtekintéséhez tekintse meg az [Azure CLI-referenciát](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* A Databricks parancssori felülettel kapcsolatos parancsok listájának megtekintéséhez lásd: [DATABRICKS CLI](/azure/databricks/dev-tools/databricks-cli)


