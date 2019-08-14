---
title: Nem lehet bejelentkezni a Zeppelinbe az Azure HDInsight
description: Nem lehet bejelentkezni a Zeppelinbe az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/12/2019
ms.openlocfilehash: 14f4cd7588405dbf351124b1e469fd96f9b38274
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977403"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>Forgatókönyv: Nem lehet bejelentkezni az Apache Zeppelinbe az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Nem lehet bejelentkezni az Apache Zeppelin-be, miután módosította a jelszó HOZZÁADÁSát az Active Directoryban.

## <a name="cause"></a>Ok

A `shiro_ini` fájlban említett felhasználó `activeDirectoryRealm.systemUsername` megváltoztatta az Active Directory-jelszót.

## <a name="resolution"></a>Megoldás:

1. Ellenőrizze, hogy a megváltoztatott jelszó a kiváltó ok- `activeDirectoryRealm.systemPassword = <new password>` e, beleértve `shiro_ini` a Ambari-ben található Zeppelin konfigurációt is. Távolítsa `activeDirectoryRealm.hadoopSecurityCredentialPath` el a beállítást. A `shiro ini`következő helyen található:.

    ![Shiro](./media/domain-joined-zeppelin-signin/shiro.png)

1. Ha a felhasználók most már az 1. lépés után bejelentkezhetnek a zeppelinbe, hozzon létre egy új `jceks` fájlt az új jelszóval, és cserélje le az `activeDirectoryRealm.hadoopSecurityCredentialPath` fájlt az új fájlra.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
