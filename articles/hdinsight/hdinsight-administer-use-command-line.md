---
title: "Azure CLI - Azure HDInsight Hadoop-fürtök kezelése |} Microsoft Docs"
description: "Útmutató az Azure HDInsight Hadoop-fürtök kezelése az Azure parancssori felület használatával. Az Azure CLI Windows, Mac és Linux rendszeren működik."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: 4f26c79f-8540-44bd-a470-84722a9e4eca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: jgao
ms.openlocfilehash: 093042da6f7d51cec3111f073da0ce3a66f2cddc
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2017
---
# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Az Azure parancssori felület használatával hdinsight Hadoop-fürtök kezelése
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Ismerje meg, hogyan használható a [Azure parancssori felület](../cli-install-nodejs.md) Azure hdinsight Hadoop-fürtök kezeléséhez. Az Azure parancssori felület implementálva van a Node.js-ben. Használható bármilyen platformon, amely támogatja a Node.js-t, beleértve a Windows, Mac és Linux platformokat. Jelenleg nem támogatja a HDInsight [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview).

Ez a cikk ismerteti, hogy csak az Azure parancssori felület használata a HDInsight. Általános útmutató az Azure parancssori felület használatával, lásd: [telepítése és konfigurálása az Azure parancssori felület][azure-command-line-tools].

## <a name="prerequisites"></a>Előfeltételek
A cikk elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Azure parancssori felület** – A telepítésre és konfigurálásra vonatkozó információkért lásd: [Install and configure the Azure CLI](../cli-install-nodejs.md) (Az Azure parancssori felület telepítése és konfigurálása).
* **Csatlakozás Azure**, a következő parancsot:

    ```cli
    azure login
    ```
  
    További információ a munkahelyi vagy iskolai fiók segítségével történő hitelesítésről: [Connect to an Azure subscription from the Azure CLI](/cli/azure/authenticate-azure-cli) (Csatlakozás Azure-előfizetéshez az Azure parancssori felületről).
* **Váltson Azure Resource Manager módra** az alábbi paranccsal:
  
    ```cli
    azure config mode arm
    ```

Ha segítséget szeretne kérni, használja a **-h** váltani.  Példa:

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>Fürtök létrehozása a parancssori felület
Lásd: [létrehozni a fürtöket a HDInsight az Azure parancssori felület használatával](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="list-and-show-cluster-details"></a>Listáról, és a fürt részleteinek megjelenítése
A következő parancsok segítségével listában, és a fürt részleteinek megjelenítése:

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![A fürt listájának parancssori megtekintése][image-cli-clusterlisting]

## <a name="delete-clusters"></a>Fürtök törlése
Az alábbi parancs segítségével törölheti a fürtöt:

```cli
azure hdinsight cluster delete <Cluster Name>
```

A fürt törölni kell a fürt tartalmazó erőforráscsoportot is törli. Vegye figyelembe, többek között az alapértelmezett tárfiók csoportban található összes erőforrást törölni szeretné.

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

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Engedélyezi/letiltja az RDP-hozzáférést a fürthöz

```cli
azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
azure hdinsight cluster disable-rdp-access [options] <Cluster Name>
```

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta rendelkezik különböző HDInsight fürt felügyeleti feladatok elvégzéséhez. További tudnivalókért tekintse meg a következő cikkeket:

* [HDInsight felügyelete az Azure portál használatával][hdinsight-admin-portal]
* [HDInsight felügyelete az Azure PowerShell használatával][hdinsight-admin-powershell]
* [Azure HDInsight – első lépések][hdinsight-get-started]
* [Az Azure parancssori felület használatával][azure-command-line-tools]

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
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "Listában, és a fürt megjelenítése"
