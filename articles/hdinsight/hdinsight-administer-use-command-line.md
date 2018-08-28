---
title: Azure parancssori felületével – Azure HDInsight Hadoop-fürtök kezelése
description: Útmutató az Azure HDInsight Hadoop-fürtök kezelése az Azure parancssori felület használatával. Az Azure CLI Windows, Mac és Linux rendszereken működik.
services: hdinsight
ms.reviewer: jasonh
author: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jasonh
ms.openlocfilehash: 8d77f6f902ad0d1f6ee2b3dd1da5a38364f1da9b
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43103744"
---
# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Az Azure CLI használatával HDInsight Hadoop-fürtök kezelése
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Ismerje meg, hogyan használható a [Azure parancssori felület](../cli-install-nodejs.md) Azure HDInsight Hadoop-fürtök kezeléséhez. Az Azure parancssori felület implementálva van a Node.js-ben. Használható bármilyen platformon, amely támogatja a Node.js-t, beleértve a Windows, Mac és Linux platformokat. Jelenleg nem támogatja a HDInsight [Azure CLI 2.0](https://docs.microsoft.com/cli/azure).

Ez a cikk ismerteti, hogy csak a HDInsight az Azure CLI használatával. Általános útmutató az Azure CLI használatával, lásd: [telepítése és konfigurálása az Azure parancssori felület][azure-command-line-tools].

## <a name="prerequisites"></a>Előfeltételek
A cikk elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Azure parancssori felület** – A telepítésre és konfigurálásra vonatkozó információkért lásd: [Install and configure the Azure CLI](../cli-install-nodejs.md) (Az Azure parancssori felület telepítése és konfigurálása).
* **Csatlakozás az Azure-bA**, a következő paranccsal:

    ```cli
    azure login
    ```
  
    További információ a munkahelyi vagy iskolai fiók segítségével történő hitelesítésről: [Connect to an Azure subscription from the Azure CLI](/cli/azure/authenticate-azure-cli) (Csatlakozás Azure-előfizetéshez az Azure parancssori felületről).
* **Váltson Azure Resource Manager módra** az alábbi paranccsal:
  
    ```cli
    azure config mode arm
    ```

Segítséget szeretne kérni, használja a **-h** váltani.  Példa:

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>Fürtök létrehozása a CLI-vel
Lásd: [fürtök létrehozása a HDInsight az Azure CLI-vel](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="list-and-show-cluster-details"></a>Listázása és megjelenítése a fürt részletes adatai
A következő parancsok használatával listázása és megjelenítése a fürt részletes adatai:

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![Parancssori nézete fürtlista][image-cli-clusterlisting]

## <a name="delete-clusters"></a>Fürtök törlése
Használja a következő parancsot a fürt törléséhez:

```cli
azure hdinsight cluster delete <Cluster Name>
```

A fürt törölheti az erőforráscsoportot, amely tartalmazza a fürt törlésével is. Megjegyzés: Ez a művelet törli a csoportot, beleértve az alapértelmezett tárfiók található összes erőforrást.

```cli
azure group delete <Resource Group Name>
```

## <a name="scale-clusters"></a>Fürtök méretezése
A Hadoop-fürt méretének módosítása:

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>Fürt HTTP-hozzáférés engedélyezése vagy letiltása

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Fürt RDP-hozzáférés engedélyezése vagy letiltása

```cli
azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
azure hdinsight cluster disable-rdp-access [options] <Cluster Name>
```

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulhatta, hogyan hajthat végre különböző HDInsight fürt felügyeleti feladatokat. További tudnivalókért tekintse meg a következő cikkeket:

* [HDInsight felügyelete az Azure Portal használatával][hdinsight-admin-portal]
* [HDInsight felügyelete az Azure PowerShell használatával][hdinsight-admin-powershell]
* [Azure HDInsight – első lépések][hdinsight-get-started]
* [Az Azure CLI használatával][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "Fürtök listázása és megjelenítése"
