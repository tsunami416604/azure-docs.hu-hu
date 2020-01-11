---
title: Nem lehet bejelentkezni a Zeppelinbe az Azure HDInsight
description: Nem lehet bejelentkezni a Zeppelinbe az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/12/2019
ms.openlocfilehash: e9a81d458d1bab68bf94e9e9d0ebd87fac4580c8
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896153"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>Forgatókönyv: nem lehet bejelentkezni az Apache Zeppelinbe az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Nem lehet bejelentkezni az Apache Zeppelin-be, miután módosította a jelszó HOZZÁADÁSát az Active Directoryban.

## <a name="cause"></a>Ok

A `shiro_ini` fájl `activeDirectoryRealm.systemUsername` megemlített felhasználó megváltoztatta az Active Directory-jelszót.

## <a name="resolution"></a>Felbontás

1. Győződjön meg arról, hogy a módosított jelszó a kiváltó ok, ha a Ambari-ben a Zeppelin `shiro_ini` config `activeDirectoryRealm.systemPassword = <new password>` is tartalmazza. Távolítsa el a `activeDirectoryRealm.hadoopSecurityCredentialPath` beállítást. Az alábbiakban a `shiro ini`helye található.

    ![Shiro](./media/domain-joined-zeppelin-signin/shiro.png)

1. Ha a felhasználók most már az 1. lépés után bejelentkezhetnek a Zeppelinbe, hozzon létre egy új `jceks` fájlt az új jelszóval, és cserélje le a `activeDirectoryRealm.hadoopSecurityCredentialPath`t az új fájlra.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiókot a felhasználói élmény javításához. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
