---
title: Helyi Jupyter telepítése és csatlakozás a Spark on Azure HDInsight
description: Ismerje meg, hogyan telepítse a Jupyter notebook helyileg a számítógépen, és csatlakoztassa egy Apache Spark-fürt.
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: hrasheed
ms.openlocfilehash: 489685485af4e3c8868f7e0281d2f81464a166f6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066178"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Jupyter notebook telepítse a számítógépre, és csatlakozzon az Apache Spark on HDInsight

Ebben a cikkben megismerheti, hogyan kell telepíteni a Jupyter notebookot, a (a pythonhoz írt) egyéni Pysparkkal és (a Scala) az Apache Spark mag, Spark Magic Quadrant- és a notebook csatlakozás egy HDInsight-fürt. Számos, a Jupyter telepítése a helyi számítógépen oka lehet, és áttekinthet néhány problémát is lehet. Ezzel kapcsolatban további információkért lásd: a szakasz [Miért célszerű telepíteni Jupyter a számítógépemen](#why-should-i-install-jupyter-on-my-computer) Ez a cikk végén található.

Nincsenek a Jupyter telepítése és csatlakozás az Apache Spark on HDInsight négy fő lépést.

* Spark-fürt konfigurálása.
* Telepítse a Jupyter notebookot.
* A Spark és a PySpark kernelt telepítse a Spark Magic Quadrant.
* Állítsa be a Spark Magic Quadrant HDInsight alapú Spark-fürt eléréséhez.

Az egyéni kernelekkel, és a Spark HDInsight-fürttel Jupyter notebookokhoz elérhető Magic Quadrant kapcsolatos további információkért lásd: [notebookokhoz elérhető kernelek Jupyter notebookokhoz az Apache Spark-alapú Linux-fürtök HDInsight az](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Előfeltételek

Az itt felsorolt előfeltételeket nem a Jupyter telepítése. Ezek a, a Jupyter notebook egy HDInsight-fürtön való csatlakozáshoz, miután telepítette a notebookot.

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Jupyter notebook telepítse a számítógépre

A Jupyter notebooks telepítése előtt telepítenie kell az Python. A [Anaconda terjesztési](https://www.anaconda.com/download/) és telepítése is megtörténik, Python, a Jupyter Notebookot.

Töltse le a [Anaconda telepítő](https://www.anaconda.com/download/) a platform és futtassa a telepítőt. A varázsló futtatásakor ellenőrizze, hogy Anaconda hozzáadása a PATH változóban lehetőséget választotta.  Lásd még a [telepítése Jupyter használatával Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>A Spark Magic Quadrant telepítése

1. Adja meg a Spark Magic Quadrant telepítéséhez az alábbi parancsok egyikét. Lásd még a [sparkmagic dokumentáció](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Fürt verziója | Telepítési parancs |
    |---|---|
    |v3.6 és 3.5-ös verzió |`pip install sparkmagic==0.12.7`|
    |v3.4|`pip install sparkmagic==0.2.3`|

1. Győződjön meg, hogy `ipywidgets` megfelelően van-e telepítve a következő parancs futtatásával:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Spark és a PySpark kernelt telepítése

1. Határozza meg, hol `sparkmagic` telepítve van a következő parancs beírásával:

    ```cmd
    pip show sparkmagic
    ```

    Ezután a munkakönyvtár a fenti paranccsal azonosítani a helyet.

1. Az új munkakönyvtárból adja meg a kívánt kernel(s) telepítéséhez az alábbi parancsok valamelyikét:

    |Kernel | Parancs |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Választható. Adja meg a kiszolgáló bővítmény engedélyezéséhez az alábbi parancsot:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>HDInsight Spark-fürthöz való csatlakozáshoz a Spark Magic Quadrant konfigurálása

Ebben a szakaszban konfigurálhatja a Spark Magic Quadrant, amelyet korábban telepített egy Apache Spark-fürthöz való csatlakozáshoz.

1. Indítsa el a Python-rendszerhéj a következő parancsot:

    ```cmd
    python
    ```

2. A Jupyter-konfigurációs adatokat általában a felhasználó kezdőkönyvtárának tárolják. Adja meg a következő parancsot a kezdőkönyvtár azonosításához, és hozzon létre egy nevű **.sparkmagic**.  A teljes elérési útja fog használt kimeneti adattípus.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. A mappában található `.sparkmagic`, hozzon létre egy fájlt nevű **config.json** , és adja hozzá a következő JSON-kódrészletben belül.  

    ```json
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
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. A fájl hajtsa végre a következő módosításokat:

    |Sablon érték | Új érték |
    |---|---|
    |{USERNAME}|Fürt bejelentkezési, alapértelmezett érték a `admin`.|
    |{CLUSTERDNSNAME}|Fürt neve|
    |{BASE64ENCODEDPASSWORD}|A base64 kódolású jelszó a tényleges jelszót.  Létrehozhat egy base64 jelszó [ https://www.url-encode-decode.com/base64-encode-decode/ ](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Ha használatával `sparkmagic 0.12.7` (fürtök a 3.5-ös és v3.6).  Ha használ `sparkmagic 0.2.3` (fürtök v3.4), cserélje le `"should_heartbeat": true`.|

    Teljes példa fájlt láthatja [minta config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > A szívverések küldése a győződjön meg arról, hogy ne szivárogjanak ki munkamenetek. A számítógép alvó állapotba, vagy le van állítva, a szívverés nem küldi el, eredményez a munkamenet éppen törlődik. A fürtök v3.4, ha szeretné tiltani ezt a viselkedést állíthatja be a Livy-konfigurációs `livy.server.interactive.heartbeat.timeout` való `0` az Ambari felhasználói felületen. A fürtök 3.5-ös verzió Ha nem állítja be a fenti, 3.5-ös configuration a munkamenet nem törlődik.

5. Indítsa el a Jupyter. Használja a következő parancsot a parancssorból.

    ```cmd
    jupyter notebook
    ```

6. Győződjön meg arról, hogy használhatja a Spark elérhető Magic Quadrant a kernelekkel. Hajtsa végre a következő lépéseket.

    a. Hozzon létre új notebookot. A jobb oldali sarokban, válassza ki a **új**. Megjelenik az alapértelmezett kernel **Python 2** vagy **Python 3** és a kernelekkel, hogy telepítve van. A tényleges értékek a telepítési beállításoktól függően változhat.  Válassza ki **PySpark**.

    ![A Jupyter notebook kernelekkel](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Kernelekkel, a Jupyter notebook")

    > [!IMPORTANT]  
    > Miután **új** tekintse át a hibákat a rendszerhéjat.  Ha a hibát látja `TypeError: __init__() got an unexpected keyword argument 'io_loop'` egy ismert probléma az egyes verziói tornádó tapasztal.  Ha igen, a kernel leállítani, majd a majd a tornádó telepítés a következő paranccsal: `pip install tornado==4.5.3`.

    b. Futtassa a következő kódrészletet.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Ha sikeresen kérheti le a kimenetet, a HDInsight-fürthöz a kapcsolat tesztelése.

    Ha szeretné frissíteni a jegyzetfüzet-konfigurációt egy másik fürthöz való csatlakozáshoz, frissítse a config.json értékek, az újonnan létrehozott 3. lépésben, a fent látható módon.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Miért érdemes a Jupyter telepítése a számítógépemen?

Számos miért érdemes a Jupyter telepítése a számítógépre, és hogyan csatlakoztathatja a HDInsight Apache Spark-fürt oka lehet.

* Annak ellenére, hogy a Jupyter notebookok már elérhetők az Azure HDInsight Spark-fürtön, a Jupyter telepítése a számítógépre, lehetőséget biztosít helyileg a jegyzetfüzetek létrehozása, az alkalmazás futó fürtön történő teszteléséhez és majd töltse fel a notebookok a fürthöz. A notebookok feltöltése a fürthöz, a is feltölti őket a Jupyter notebookot, hogy fut-e, vagy a fürt használatával, vagy mentse őket a fürthöz társított storage-fiókban a /HdiNotebooks mappába. Hogyan notebookok tárolja a fürt további információkért lásd: [Jupyter notebookok tároló](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* A rendelkezésre álló jegyzetfüzet helyileg, kapcsolódhat a különböző Spark-fürtök, az alkalmazás követelmény alapján.
* GitHub segítségével megvalósítani egy forráskódú verziókezelő rendszer és verzió felügyelheti a jegyzetfüzetekkel. Egy együttműködési környezet, ahol több felhasználó dolgozhat ugyanazon a jegyzetfüzet is rendelkezhet.
* Együttműködve notebookok helyileg egy fürtöt is nélkül. Csak egy fürtöt, szemben a notebookok teszteléséhez manuálisan a saját jegyzetfüzetek vagy a fejlesztési környezet kezelése, nem kell.
* Konfigurálhatja a saját helyi fejlesztői környezetében, konfigurálja a Jupyter telepítése a fürtön, mint egyszerűbb lehet.  Használja ki egy vagy több távoli fürtök konfigurálása nélkül helyileg telepített összes szoftver is igénybe vehet.

> [!WARNING]  
> A Jupyter telepítése a helyi számítógépen, a több felhasználó is a azonos jegyzetfüzet futtatása ugyanazon a Spark-fürtön egyszerre. Ilyen esetben több Livy-munkamenetek jönnek létre. Ha problémába, amelyen hibakeresést végez, amely egy összetett feladat, mely Livy munkamenet nyomon felhasználóhoz tartozik, mely lesz.  

## <a name="next-steps"></a>További lépések

* [Áttekintés: Az Apache Spark on Azure HDInsight](apache-spark-overview.md)
* [Az Apache Spark és BI: Spark on HDInsight használatával, BI-eszközökkel interaktív adatelemzés végrehajtása](apache-spark-use-bi-tools.md)
* [Az Apache Spark és Machine Learning: A Spark használata a HDInsight HVAC-adatok épület-hőmérséklet elemzésére](apache-spark-ipython-notebook-machine-learning.md)