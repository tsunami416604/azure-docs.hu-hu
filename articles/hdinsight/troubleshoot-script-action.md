---
title: Parancsfájlműveletek – hiba elhárítása az Azure HDInsightban
description: Az Azure HDInsight parancsfájl-műveletekáltalános hibaelhárítási lépései.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/21/2020
ms.openlocfilehash: b1e6b674edc155e0aa6c88ad360eb59864eebee4
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771975"
---
# <a name="troubleshoot-script-actions-in-azure-hdinsight"></a>Parancsfájlműveletek – hiba elhárítása az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="viewing-logs"></a>Naplók megjelenítése

Az Apache Ambari webes felhasználói felületével megtekintheti a parancsfájlműveletek által naplózott információkat. Ha a parancsfájl a fürt létrehozása során sikertelen, a naplók az alapértelmezett fürttárfiókban találhatók. Ez a szakasz a naplók mindkét beállítás használatával történő beolvasásával kapcsolatos információkat tartalmazza.

### <a name="apache-ambari-web-ui"></a>Apache Ambari webes felhasználói felület

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net`meg `CLUSTERNAME` a , ahol a fürt neve.

1. A lap tetején lévő sávból válassza ki a **műveleti** bejegyzést. A lista az Ambari-n keresztül a fürtön végzett aktuális és korábbi műveleteket jeleníti meg.

    ![Ambari webes felhasználói felület sáv jattot kijelölt műveleti](./media/troubleshoot-script-action/hdi-apache-ambari-nav.png)

1. Keresse meg azokat a bejegyzéseket, amelyek az Operations oszlopban **futtatták\_** az egyéni **parancsfájlt.** Ezek a bejegyzések a parancsfájlműveletek futtatásakor jönnek létre.

    ![Apache Ambari parancsfájlműveleti műveletek](./media/troubleshoot-script-action/ambari-script-action.png)

    Az **STDOUT** és az **STDERR** kimenet megtekintéséhez jelölje ki a **run\customscriptaction** bejegyzést, és ásson le a hivatkozásokközött. Ez a kimenet akkor jön létre, amikor a parancsfájl fut, és hasznos információkat tartalmazhat.

### <a name="default-storage-account"></a>Alapértelmezett tárfiók

Ha a fürt létrehozása parancsfájlhiba miatt sikertelen, a naplók a fürttárfiókban maradnak.

* A tárolónaplók a `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`helyen érhetők el.

    ![Parancsfájl-műveletnaplók](./media/troubleshoot-script-action/script-action-logs-in-storage.png)

    Ebben a könyvtárban a naplók külön vannak rendezve a **headnode**, **a feldolgozócsomópont**és **az állattartó csomópont**számára. Lásd az alábbi példákat:

    * **Csomópont**:`<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Feldolgozó csomópont:**`<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Zookeeper csomópont:**`<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* A megfelelő gazdagép összes **stdout** és **stderr** feltöltésre kerül a tárfiókba. Minden parancsfájlművelethez egy **kimenet\*van- .txt** és **errors-\*.txt.** A **output-*.txt** fájl az állomáson futtatott parancsfájl URI-járól tartalmaz információkat. A következő szöveg egy példa erre az információra:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Lehetséges, hogy ismételten létrehoz egy parancsfájl-műveletfürtet ugyanazzal a névvel. Ebben az esetben a **DÁTUM** mappa neve alapján megkülönböztetheti a megfelelő naplókat. Például egy fürt mappaszerkezete, a különböző időpontokban létrehozott **mycluster**a következő naplóbejegyzésekhez hasonlóan jelenik meg:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Ha ugyanazon a napon azonos nevű parancsfájl-műveletfürtöt hoz létre, az egyedi előtaggal azonosíthatja a megfelelő naplófájlokat.

* Ha 12:00 óra, éjfél közelében hoz létre fürtöt, lehetséges, hogy a naplófájlok két napig terjednek. Ebben az esetben két különböző dátummappa jelenik meg ugyanahhoz a fürthöz.

* A naplófájlok feltöltése az alapértelmezett tárolóba akár öt percet is igénybe vehet, különösen nagy fürtök esetén. Tehát ha szeretné elérni a naplókat, ne azonnal törölje a fürtöt, ha egy parancsfájl művelet sikertelen.

## <a name="ambari-watchdog"></a>Ambari házőrző

Ne módosítsa az Ambari figyelő, hdinsightwatchdog jelszavát a Linux-alapú HDInsight-fürtön. A jelszómódosítása megszakítja az új parancsfájlműveletek futtatásának lehetőségét a HDInsight-fürtön.

## <a name="cant-import-name-blobservice"></a>A BlobService név nem importálható

__Tünetek__. A parancsfájlművelet sikertelen. Az Ambari művelet megtekintésekor a következőhöz hasonló szöveg jelenik meg:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Oka__. Ez a hiba akkor fordul elő, ha frissíti a Python Azure Storage-ügyfél, amely a HDInsight-fürt részét képezi. A HDInsight 0.20.0-s Azure Storage-ügyfélre számít.

__felbontás .__ A hiba elhárításához manuálisan csatlakozzon az `ssh`egyes fürtcsomópontokhoz a használatával. A megfelelő tárolóügyfél-verzió újratelepítéséhez futtassa a következő parancsot:

```bash
sudo pip install azure-storage==0.20.0
```

A fürthöz az SSH-val való csatlakozásról a [Csatlakozás hdinsighthoz (Apache Hadoop) ssh használatával](hdinsight-hadoop-linux-use-ssh-unix.md)című témakörben talál további információt.

## <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Az előzmények nem jelenítik meg a fürt létrehozása során használt parancsfájlokat

Ha a fürt 2016. A fürt átméretezése azt eredményezi, hogy a parancsfájlok megjelennek a parancsfájlok műveletelőzményeiben.

Két kivétel van:

* A fürt 2015. Ez a dátum az, amikor parancsfájlműveleteket vezettek be. Az ezt a dátum előtt létrehozott fürt nem használhatott parancsfájlműveleteket a fürt létrehozásához.

* A fürt létrehozása során több parancsfájlműveletet használt. Vagy ugyanazt a nevet használta több parancsfájlhoz, vagy ugyanazt a nevet, ugyanazt az URI-t, de több parancsfájl különböző paramétereit. Ezekben az esetekben a következő hibaüzenet jelenik meg:

    ```
    No new script actions can be run on this cluster because of conflicting script names in existing scripts. Script names provided at cluster creation must be all unique. Existing scripts are run on resize.
    ```

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.