---
title: Helyi Jupyter telepítése és csatlakozás a Spark on Azure HDInsight
description: Ismerje meg, hogyan telepítse a Jupyter notebook helyileg a számítógépen, és csatlakoztassa egy Apache Spark-fürt.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: hrasheed
ms.openlocfilehash: 3cd6ef1716d455c5ac755122b8696dbc43fdf459
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52581867"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Jupyter notebook telepítse a számítógépre, és csatlakozzon az Apache Spark on HDInsight

Ebben a cikkben megismerheti, hogyan kell telepíteni a Jupyter notebookot, a (a pythonhoz írt) egyéni Pysparkkal és (a Scala) az Apache Spark mag, Spark Magic Quadrant- és a notebook csatlakozás egy HDInsight-fürt. Számos, a Jupyter telepítése a helyi számítógépen oka lehet, és áttekinthet néhány problémát is lehet. Ezzel kapcsolatban további információkért lásd: a szakasz [Miért célszerű telepíteni Jupyter a számítógépemen](#why-should-i-install-jupyter-on-my-computer) Ez a cikk végén található.

Három kulcsfontosságú lépésnek vannak a számítógépen a Jupyter és a Spark Magic Quadrant megismeréséhez.

* Telepítse a Jupyter notebook
* A Spark és a PySpark kernelt telepítése a Spark Magic Quadrant-nal
* A Spark Magic Quadrant elérésére, HDInsight Spark-fürt konfigurálása

Az egyéni kernelekkel, és a Spark HDInsight-fürttel Jupyter notebookokhoz elérhető Magic Quadrant kapcsolatos további információkért lásd: [notebookokhoz elérhető kernelek Jupyter notebookokhoz az Apache Spark-alapú Linux-fürtök HDInsight az](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Előfeltételek
Az itt felsorolt előfeltételeket nem a Jupyter telepítése. Ezek a, a Jupyter notebook egy HDInsight-fürtön való csatlakozáshoz, miután telepítette a notebookot.

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Jupyter notebook telepítse a számítógépre

A Jupyter notebooks telepítése előtt telepítenie kell az Python. Python és a Jupyter állnak rendelkezésre, része a [Anaconda terjesztési](https://www.continuum.io/downloads). Anaconda telepítésekor telepíti a Python egy terjesztési. Anaconda telepítése után adja hozzá a Jupyter telepítése megfelelő parancsok futtatásával.

1. Töltse le a [Anaconda telepítő](https://www.continuum.io/downloads) a platform és futtassa a telepítőt. A varázsló futtatásakor ellenőrizze, hogy Anaconda hozzáadása a PATH változóban lehetőséget választotta.
1. A következő parancsot a Jupyter telepítése.

        conda install jupyter

    Jupyter telepítésével kapcsolatos további információkért lásd: [telepítése Jupyter használatával Anaconda](http://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-the-kernels-and-spark-magic"></a>A kernelekkel, és a Spark Magic Quadrant telepítése

A Spark Magic Quadrant telepítésével kapcsolatos útmutatásért PySpark- és Spark mag, kövesse a telepítési utasításait a [sparkmagic dokumentáció](https://github.com/jupyter-incubator/sparkmagic#installation) a Githubon. Az első lépés a Spark magic dokumentációjában kéri, hogy a Spark Magic Quadrant telepítse. Cserélje le a következő parancsokat, a HDInsight-fürtöt fog csatlakozni verziójától függően, hogy a hivatkozás első lépése. Ezt követően hajtsa végre a hátralévő lépéseket a Spark magic dokumentációjában. Ha szeretné telepíteni a különböző kernelekkel, el kell végezni a Spark magic telepítési utasításokat szakasz 3. lépés.

* A fürtök v3.4 telepítse sparkmagic 0.2.3 végrehajtásával: `pip install sparkmagic==0.2.3`

* A fürtök 3.5-ös és v3.6 telepítse sparkmagic 0.11.2 végrehajtásával: `pip install sparkmagic==0.11.2`

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>HDInsight Spark-fürthöz való csatlakozáshoz a Spark Magic Quadrant konfigurálása

Ebben a szakaszban konfigurálhatja a Spark Magic Quadrant, amelyet korábban telepített csatlakozni kell már létrehozott Azure HDInsight az Apache Spark-fürt.

1. A Jupyter-konfigurációs adatokat általában a felhasználó kezdőkönyvtárának tárolják. Keresse meg a kezdőkönyvtár bármely operációs rendszer platformon, írja be a következő parancsokat.

    Indítsa el a Python-rendszerhéjat. Egy parancsablakban írja be a következőt:

        python

    Adja meg a Python rendszerhéj a Ismerje meg a kezdőkönyvtár az alábbi parancsot.

        import os
        print(os.path.expanduser('~'))

1. Keresse meg a kezdőkönyvtár, és hozzon létre egy nevű **.sparkmagic** Ha azt nem létezik.
1. A mappában hozzon létre egy fájlt, nevű **config.json** , és adja hozzá a következő JSON-kódrészletben belül.

        {
          "kernel_python_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          },
          "kernel_scala_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          }
        }

1. A helyettesítő **{USERNAME}**, **{CLUSTERDNSNAME}**, és **{BASE64ENCODEDPASSWORD}** a megfelelő értékekre. A kedvenc programozási nyelven vagy online segédprogramok számos használhatja a tényleges jelszót létrehozni a base64-kódolású jelszót.

1. A jobb oldali szívverés-beállítások konfigurálása `config.json`. Ezek a beállítások azonos szinten, adja hozzá a `kernel_python_credentials` és `kernel_scala_credentials` kódrészletek a hozzáadott korábban. Egy példa, hogyan és hol lehet megadni a szívverés-beállítások: Ez [minta config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

    * A `sparkmagic 0.2.3` (fürtök v3.4), például:

            "should_heartbeat": true,
            "heartbeat_refresh_seconds": 5,
            "heartbeat_retry_seconds": 1

    * A `sparkmagic 0.11.2` (fürtök 3.5-ös és v3.6), a következők:

            "heartbeat_refresh_seconds": 5,
            "livy_server_heartbeat_timeout_seconds": 60,
            "heartbeat_retry_seconds": 1

    >[!TIP]
    >A szívverések küldése a győződjön meg arról, hogy ne szivárogjanak ki munkamenetek. A számítógép alvó állapotba, vagy le van állítva, a szívverés nem küldi el, eredményez a munkamenet éppen törlődik. A fürtök v3.4, ha szeretné tiltani ezt a viselkedést állíthatja be a Livy-konfigurációs `livy.server.interactive.heartbeat.timeout` való `0` az Ambari felhasználói felületen. A fürtök 3.5-ös verzió Ha nem állítja be a fenti, 3.5-ös configuration a munkamenet nem törlődik.

1. Indítsa el a Jupyter. Használja a következő parancsot a parancssorból.

        jupyter notebook

1. Győződjön meg arról, hogy képes-e csatlakozni a fürthöz a Jupyter notebook használatával, és, a Spark elérhető Magic Quadrant használható a kernelekkel az. Hajtsa végre a következő lépéseket.

    a. Hozzon létre új notebookot. Kattintson a jobb oldali sarokban, **új**. Megjelenik az alapértelmezett kernel **Python2** és a két új kernelekkel, a telepítés **PySpark** és **Spark**. Kattintson a **PySpark**.

    ![A Jupyter notebook kernelekkel](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Kernelekkel, a Jupyter notebook")

    b. Futtassa a következő kódrészletet.

        %%sql
        SELECT * FROM hivesampletable LIMIT 5

    Ha sikeresen kérheti le a kimenetet, a HDInsight-fürthöz a kapcsolat tesztelése.

    >[!TIP]
    >Ha szeretné frissíteni a jegyzetfüzet-konfigurációt egy másik fürthöz való csatlakozáshoz, frissítse a config.json értékek, az újonnan létrehozott, ahogyan a 3. lépés.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Miért érdemes a Jupyter telepítése a számítógépemen?
Számos miért érdemes a Jupyter telepítése a számítógépre, és hogyan csatlakoztathatja a HDInsight Apache Spark-fürt oka lehet.

* Annak ellenére, hogy a Jupyter notebookok már elérhetők az Azure HDInsight Spark-fürtön, a Jupyter telepítése a számítógépre, lehetőséget biztosít helyileg a jegyzetfüzetek létrehozása, az alkalmazás futó fürtön történő teszteléséhez és majd töltse fel a notebookok a fürthöz. A notebookok feltöltése a fürthöz, a is feltölti őket a Jupyter notebookot, hogy fut-e, vagy a fürt használatával, vagy mentse őket a fürthöz társított storage-fiókban a /HdiNotebooks mappába. Hogyan notebookok tárolja a fürt további információkért lásd: [Jupyter notebookok tároló](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* A rendelkezésre álló jegyzetfüzet helyileg, kapcsolódhat a különböző Spark-fürtök, az alkalmazás követelmény alapján.
* GitHub segítségével megvalósítani egy forráskódú verziókezelő rendszer és verzió felügyelheti a jegyzetfüzetekkel. Egy együttműködési környezet, ahol több felhasználó dolgozhat ugyanazon a jegyzetfüzet is rendelkezhet.
* Együttműködve notebookok helyileg egy fürtöt is nélkül. Csak egy fürtöt, szemben a notebookok teszteléséhez manuálisan a saját jegyzetfüzetek vagy a fejlesztési környezet kezelése, nem kell.
* Konfigurálhatja a saját helyi fejlesztői környezetében, konfigurálja a Jupyter telepítése a fürtön, mint egyszerűbb lehet.  Használja ki egy vagy több távoli fürtök konfigurálása nélkül helyileg telepített összes szoftver is igénybe vehet.

> [!WARNING]
> A Jupyter telepítése a helyi számítógépen, a több felhasználó is a azonos jegyzetfüzet futtatása ugyanazon a Spark-fürtön egyszerre. Ilyen esetben több Livy-munkamenetek jönnek létre. Ha problémába, amelyen hibakeresést végez, amely egy összetett feladat, mely Livy munkamenet nyomon felhasználóhoz tartozik, mely lesz.
>
>

## <a name="seealso"></a>Lásd még:
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Az Apache Spark és BI: interaktív adatelemzés a Spark on HDInsight használatával, BI-eszközökkel végrehajtása](apache-spark-use-bi-tools.md)
* [Az Apache Spark és Machine Learning: a Spark on HDInsight HVAC-adatok épület-hőmérséklet elemzésére a használata](apache-spark-ipython-notebook-machine-learning.md)
* [Az Apache Spark és Machine Learning: használja a Spark on HDInsight az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a HDInsight az Apache Spark használatával](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az Apache Spark-alkalmazások távoli hibakeresése az IntelliJ IDEA HDInsight-eszközei beépülő használata](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Az Apache Zeppelin notebookok használata a HDInsight Apache Spark-fürt](apache-spark-zeppelin-notebook.md)
* [Notebookokhoz elérhető kernelek Jupyter a HDInsight az Apache Spark-fürt](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
