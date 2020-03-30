---
title: 'A Databricks parancssori felület használata az Azure Cloud Shellből '
description: Ismerje meg, hogyan használhatja az Azure Cloud Shell Databricks CLI-t az Azure Databricks-en végzett műveletek végrehajtásához.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: efb0d3222bfd98b15502163979425d47fa459e07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73605721"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>A Databricks parancssori felület használata az Azure Cloud Shellből

Ismerje meg, hogyan használhatja a Databricks CLI-t az Azure Cloud Shellből a Databricks-en végzett műveletek végrehajtásához.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Databricks munkaterület és fürt. További információt az [Azure Databricks – Első lépések](quickstart-create-databricks-workspace-portal.md). 

* Hozzon létre egy személyes hozzáférési jogkivonatot a Databricks-ben. További információt a [Token-kezelés .](/azure/databricks/dev-tools/api/latest/authentication)

## <a name="use-the-azure-cloud-shell"></a>Az Azure Cloud Shell használata

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
 
2. A jobb felső sarokban kattintson a **Cloud Shell** ikonra.

   ![A Cloud Shell elindítása](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Az Azure Cloud Shell indítása")

3. Győződjön meg arról, hogy a **Bash** lehetőséget választja a Cloud Shell környezetben. A legördülő menüből választhat, ahogy az az alábbi képernyőképen látható.

   ![Válassza a Bash lehetőséget a Cloud Shell környezetben](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Bash kiválasztása") 

4. Hozzon létre egy virtuális környezetet, amelyben telepítheti a Databricks CLI.Create a virtual environment in which you can install the Databricks CLI. Az alábbi kódrészletben hozzon létre `databrickscli`egy virtuális környezetet, amelynek neve .

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Váltson a létrehozott virtuális környezetre.

       source databrickscli/bin/activate

6. Telepítse a Databricks CLI.Install the Databricks CLI.

       pip install databricks-cli

7. Állítsa be a databricks hitelesítést a hozzáférési jogkivonat használatával, amelyet létre kell hoznia, az előfeltételek részeként felsorolt használatával. Használja az alábbi parancsot:

       databricks configure --token

    A következő utasításokat fogja kapni:

    * Először a rendszer kéri, hogy adja meg a Databricks gazdagép. Adja meg az `https://eastus2.azuredatabricks.net`értéket a formátumban. Itt **az USA keleti régiója 2** az Azure-régió, ahol létrehozta az Azure Databricks munkaterületét.

    * Ezután a rendszer kéri, hogy adjon meg egy jogkivonatot. Adja meg a korábban létrehozott jogkivonatot.

Miután elvégezte ezeket a lépéseket, megkezdheti a Databricks CLI használatát az Azure Cloud Shellből.

## <a name="use-databricks-cli"></a>Databricks CLI használata

Most már elkezdheti használni a Databricks CLI.You can now start using the Databricks CLI. Futtassa például a következő parancsot a munkaterületösszes Databricks-fürtlistájának listázásához.

    databricks clusters list

A databricks fájlrendszer (DBFS) eléréséhez a következő paranccsal is hozzáférhet.

    databricks fs ls


A parancsokra vonatkozó teljes körű hivatkozása: [Databricks CLI](/azure/databricks/dev-tools/databricks-cli).


## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni az Azure CLI-ről, olvassa el az [Azure CLI áttekintését](../cloud-shell/overview.md)
* Az Azure CLI parancsainak listáját az [Azure CLI referencia](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* A Databricks CLI parancsainak listáját a [Databricks CLI](/azure/databricks/dev-tools/databricks-cli)


