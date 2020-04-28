---
title: Parancsfájl-műveletek hibakeresése az Azure HDInsight
description: Általános hibaelhárítási lépések az Azure HDInsight parancsfájl-műveleteihez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: e2a2f6abfd6b7c644e95649f3c9832e4cc986037
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188446"
---
# <a name="troubleshoot-script-actions-in-azure-hdinsight"></a>Parancsfájl-műveletek hibakeresése az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="viewing-logs"></a>Naplók megjelenítése

Az Apache Ambari webes FELÜLETén megtekintheti a parancsfájlok műveletei által naplózott információkat. Ha a parancsfájl meghibásodik a fürt létrehozásakor, a naplók az alapértelmezett fürt Storage-fiókban találhatók. Ez a szakasz azt ismerteti, hogyan lehet lekérdezni a naplókat mindkét lehetőség használatával.

### <a name="apache-ambari-web-ui"></a>Apache Ambari webes felhasználói felület

1. Egy webböngészőből nyissa meg `https://CLUSTERNAME.azurehdinsight.net`a következőt:, ahol `CLUSTERNAME` a a fürt neve.

1. Az oldal tetején található sávban válassza az **Ops** bejegyzést. A lista a fürtön a Ambari használatával végzett aktuális és korábbi műveleteket jeleníti meg.

    ![Ambari webes FELHASZNÁLÓIFELÜLET-sáv kijelölése az Ops-mel](./media/troubleshoot-script-action/hdi-apache-ambari-nav.png)

1. Keresse meg azokat a bejegyzéseket, amelyek a **customscriptaction futtatták\_** az **Operations** oszlopban. Ezek a bejegyzések a parancsfájl műveleteinek futtatásakor jönnek létre.

    ![Apache Ambari parancsfájl műveleti műveletei](./media/troubleshoot-script-action/ambari-script-action.png)

    Az **StdOut** és a **stderr** kimenetének megtekintéséhez válassza ki a **run\customscriptaction** bejegyzést, és részletezse a hivatkozásokat. Ezt a kimenetet a szkript futtatásakor generálja a rendszer, és hasznos információkhoz vezethet.

### <a name="default-storage-account"></a>Alapértelmezett Storage-fiók

Ha a fürt létrehozása parancsfájl hiba miatt meghiúsul, a rendszer a naplókat a fürt Storage-fiókjában tárolja.

* A tárolási naplók a következő címen `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`érhetők el:.

    ![Parancsfájl műveleti naplói](./media/troubleshoot-script-action/script-action-logs-in-storage.png)

    Ebben a könyvtárban a naplók külön vannak rendszerezve a **átjárócsomóponthoz**, a **munkavégző csomópont**és a **Zookeeper csomópont**számára. Lásd az alábbi példákat:

    * **Átjárócsomóponthoz**:`<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Munkavégző csomópont**:`<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Zookeeper csomópont**:`<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* A rendszer feltölti a megfelelő gazdagép összes **StdOut** -és **stderr** a Storage-fiókba. Minden parancsfájl-művelethez létezik egy **output-\*. txt** és **errors-\*. txt fájl** . A **output-*. txt** fájl a gazdagépen futó parancsfájl URI azonosítóját tartalmazza. A következő szöveg egy példa erre az információra:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Lehetséges, hogy ismételten létrehoz egy azonos nevű parancsfájl-műveleti fürtöt. Ebben **az esetben a mappa neve** alapján megkülönböztetni a megfelelő naplókat. Például a fürt **mycluster**, amely különböző dátumokban lett létrehozva, a következő naplóbejegyzések szerint jelenik meg:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Ha azonos nevű parancsfájl-műveleti fürtöt hoz létre ugyanazon a napon, akkor a megfelelő naplófájlok azonosításához használhatja az egyedi előtagot.

* Ha olyan fürtöt hoz létre, amely a 12:00-es számú, éjfélt, akkor lehetséges, hogy a naplófájlok két nap alatt kiterjedhetnek. Ebben az esetben két különböző dátumú mappát láthat ugyanahhoz a fürthöz.

* A naplófájlok alapértelmezett tárolóba való feltöltése akár öt percet is igénybe vehet, különösen a nagyméretű fürtök esetében. Tehát ha el szeretné érni a naplókat, ne törölje azonnal a fürtöt, ha a parancsfájl művelete meghiúsul.

## <a name="ambari-watchdog"></a>Ambari watchdog

Ne módosítsa a Ambari watchdog (hdinsightwatchdog) jelszavát a Linux-alapú HDInsight-fürtön. A jelszó módosítása megszakítja az új parancsfájl-műveletek futtatásának lehetőségét a HDInsight-fürtön.

## <a name="cant-import-name-blobservice"></a>Nem lehet importálni a BlobService nevet

__Tüneteket__. A parancsfájl művelete sikertelen. A következő hibához hasonló szöveg jelenik meg, amikor megtekinti a műveletet a Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__OK__. Ez a hiba akkor fordul elő, ha a HDInsight-fürthöz tartozó Python Azure Storage-ügyfelet frissíti. A HDInsight az Azure Storage ügyféloldali 0.20.0 várja.

__Megoldás__. A hiba megoldásához manuálisan kapcsolódjon az egyes fürtcsomópontokon a használatával `ssh`. Futtassa a következő parancsot a megfelelő tárolási ügyfél verziójának újratelepítéséhez:

```bash
sudo pip install azure-storage==0.20.0
```

További információ a fürt SSH-val történő csatlakoztatásáról: [Csatlakozás HDInsight (Apache Hadoop) SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>A előzmények nem jelenítik meg a fürt létrehozása során használt parancsfájlokat

Ha a fürt a 2016. március 15. előtt lett létrehozva, előfordulhat, hogy nem jelenik meg bejegyzés a parancsfájl-műveleti előzményekben. A fürt átméretezése hatására a parancsfájlok megjelennek a parancsfájl-műveletek előzményeiben.

Két kivétel van:

* A fürt a 2015. szeptember 1. előtt jött létre. Ez a dátum az, amikor parancsfájl-műveletek lettek bevezetve. Az ezen dátum előtt létrehozott összes fürthöz nem használható parancsfájl-művelet a fürt létrehozásához.

* A fürt létrehozása során több parancsfájl-műveletet is használt. Vagy ugyanazt a nevet használta több parancsfájlhoz, vagy ugyanazzal a névvel, azonos URI-val, de különböző paraméterekkel több parancsfájlhoz. Ezekben az esetekben a következő hibaüzenetet kapja:

    ```
    No new script actions can be run on this cluster because of conflicting script names in existing scripts. Script names provided at cluster creation must be all unique. Existing scripts are run on resize.
    ```

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.