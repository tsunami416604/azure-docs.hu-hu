---
title: "Azure-felhőbe rendszerhéjból Databricks CLI használata |} Microsoft Docs"
description: "Ismerje meg, hogyan használható az Azure felhőalapú rendszerhéjból Databricks CLI."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: nitinme
ms.openlocfilehash: 212789e8189abf362bb8eaf12e4c551b113b7adb
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Azure-felhőbe rendszerhéjból Databricks CLI használata

Útmutató a Databricks CLI Azure Cloud rendszerhéjból Databricks a műveletek végrehajtásához.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Databricks munkaterület és a fürt. Útmutatásért lásd: [Ismerkedés az Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Állítsa be a Databricks személyes hozzáférési jogkivonat. Útmutatásért lásd: [felügyeleti Token](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="use-the-azure-cloud-shell"></a>Az Azure-felhőbe rendszerhéj használata

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
 
2. Kattintson a jobb felső sarokban, a **felhő rendszerhéj** ikonra.

   ![Indítsa el a felhő rendszerhéjat](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "indítsa el az Excelből ODBC")

3. Válasszon **Bash** az a felhő rendszerhéj felhasználásához. A legördülő lista lehetőséget választhat az alábbi képernyőfelvételen látható módon.

   ![Indítsa el a felhő rendszerhéjat](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "indítsa el az Excelből ODBC") 

4. Hozzon létre egy virtuális környezetet, amelyben a Databtricks parancssori felület telepítése. Az alábbi részlet hoz létre a virtuális környezet nevű `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Váltás a létrehozott virtuális környezet.

       source databrickscli/bin/activate

6. A Databricks parancssori felület telepítése.

       pip install databricks-cli

7. Hitelesítés beállítása az Databricks kell hozott létre, a hozzáférési jogkivonat segítségével felsorolt Előfeltételek részeként. Használja az alábbi parancsot:

       databricks configure --token

    Megjelenik az alábbi utasításokat:

    * Adja meg a Databricks gazdagép kéri. Ente formátumú érték `https://eastus2.azuredatabricks.net`. Itt **USA keleti régiója 2** van az Azure-régió, amelyben létrehozta az Azure Databricks munkaterületen.

    * Adjon meg egy felhasználónevet kéri. Adja meg **token**.

    * Végezetül kéri a jelszót. Adja meg a korábban létrehozott jogkivonat.

Miután elvégezte ezeket a lépéseket, kezdheti Databricks CLI Azure Cloud rendszerhéjból.

## <a name="use-databricks-cli"></a>Databricks CLI használata

Most elindíthatja a Databricks parancssori felület használatával. Például a következő parancsot, hogy rendelkezik a munkaterületen lévő összes Databricks fürt listázásához.

    databricks clusters list

A következő parancsot használhatja, ha az a Databricks fájlrendszer (DBFS) eléréséhez.

    databricks fs ls


A parancsok teljes referenciáért lásd: [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).


## <a name="next-steps"></a>További lépések

* Azure parancssori Felülettel kapcsolatos további információkért lásd: [Azure CLI áttekintése](../cloud-shell/overview.md)
* Az Azure parancssori felület parancsok listájának megtekintéséhez lásd: [Azure CLI-hivatkozás](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)
* Databricks CLI parancsok listájának megtekintéséhez lásd: [Databricks parancssori felület](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)


