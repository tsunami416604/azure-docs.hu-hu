---
title: Az elérni látogatott fiók nem támogatja az Azure HDInsight http-hibáját
description: Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 14c43e4557275d6a425127acfee7495f68d1d354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165552"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>Az elérni látogatott fiók nem támogatja az Azure HDInsight http-hibáját

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg:

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>Ok

A hibaüzenet fogadásának több oka is van:

* A tárfiók [biztonságos átvitele](../../storage/common/storage-require-secure-transfer.md) engedélyezve van, és a helytelen [URI-séma](../hdinsight-hadoop-linux-information.md#URI-and-scheme) van használatban.

* Egy fürt olyan tárfiókkal jött létre, amelynek biztonságos *átvitele le volt tiltva.* Ezt követően a tárfiókban engedélyezve volt a biztonságos átvitel.

## <a name="resolution"></a>Megoldás:

Ha az Azure Storage vagy a Data Lake Storage Gen2 biztonságos átvitele engedélyezve van, az URI vagy `wasbs://` `abfss://`, illetve.  Lásd még: [biztonságos átvitel](../../storage/common/storage-require-secure-transfer.md).

Új fürtök esetén használjon olyan tárfiókot, amely már rendelkezik a kívánt biztonságos átviteli beállítással. Ne módosítsa a meglévő fürt által használt tárfiók biztonságos átviteli beállítását.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
