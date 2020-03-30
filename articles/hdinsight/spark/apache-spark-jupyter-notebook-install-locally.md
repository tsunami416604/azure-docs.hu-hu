---
title: Telepítse a Jupytert helyileg, és csatlakozzon a Sparkhoz az Azure HDInsightban
description: Ismerje meg, hogyan telepítheti a Jupyter notebookhelyien a számítógépre, és csatlakoztassa egy Apache Spark-fürthöz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 225ee7028b9610a4974f9bee05da667d78d3355e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73903741"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Telepítse a Jupyter notebookot a számítógépre, és csatlakozzon az Apache Sparkhoz a HDInsight-on

Ebben a cikkben megtudhatja, hogyan telepítheti a Jupyter-jegyzetfüzetet az egyéni PySpark (Pythonhoz) és az Apache Spark (Scala) kernelekkel a Spark magic segítségével, és csatlakoztassa a jegyzetfüzetet egy HDInsight-fürthöz. A Jupyter helyi számítógépre való telepítésének számos oka lehet, és lehetnek kihívások is. További erről, lásd a szakasz [Miért kell telepíteni Jupyter a számítógépre](#why-should-i-install-jupyter-on-my-computer) végén ezt a cikket.

A Jupyter telepítésével és az Apache Sparkhoz való csatlakozással a HDInsight-on négy kulcsfontosságú lépéssel jár.

* Konfigurálja a Spark-fürtöt.
* Telepítse a Jupyter notebookot.
* Telepítse a PySpark és Spark kerneleket a Spark varázslattal.
* Konfigurálja a Spark magic-et a Spark-fürt HDInsight-on való eléréséhez.

A HdInsight-fürtöt tartalmaza Jupyter-jegyzetfüzetekhez elérhető egyéni kernelekről és a Spark-varázslatról a [HDInsight-fürtökkel rendelkező Jupyter-jegyzetfüzetekhez elérhető kernelek című](apache-spark-jupyter-notebook-kernels.md)témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md). Ez előfeltétele a Jupyter-jegyzetfüzet HDInsight-fürthöz való csatlakoztatásának a jegyzetfüzet telepítése után.

* A Jupyter-notebookok és a HDInsighton futó Spark használatának ismerete.

## <a name="install-jupyter-notebook-on-your-computer"></a>A Jupyter notebook telepítése a számítógépre

A Jupyter-jegyzetfüzetek telepítése előtt telepítenie kell a Pythont. Az [Anaconda disztribúció](https://www.anaconda.com/download/) telepíti mind a Python, mind a Jupyter Notebook.

Töltse le az [Anaconda telepítőt](https://www.anaconda.com/download/) a platformra, és futtassa a telepítést. A telepítővarázsló futtatása közben válassza az Anaconda hozzáadásának lehetőségét a PATH változóhoz.  Lásd még: [A Jupyter telepítése az Anaconda használatával](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>A Spark magic telepítése

1. Írja be az alábbi parancsok egyikét a Spark magic telepítéséhez. Lásd még: [sparkmagic dokumentáció](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Fürt verziója | Telepítés parancs |
    |---|---|
    |3.6 és 3.5-ös |`pip install sparkmagic==0.13.1`|
    |3.4.-es év|`pip install sparkmagic==0.2.3`|

1. Győződjön meg arról, hogy `ipywidgets` megfelelően van telepítve a következő parancs futtatásával:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>PySpark- és Spark-kernelek telepítése

1. A `sparkmagic` következő parancs megadásával azonosítsa, hogy hol van telepítve:

    ```cmd
    pip show sparkmagic
    ```

    Ezután módosítsa a munkakönyvtárat a fenti paranccsal azonosított helyre.

1. Az új munkakönyvtárból adja meg az alábbi parancsok közül egyet a kívánt kernel(ek) telepítéséhez:

    |Kernel | Parancs |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |Szikra|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark között|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3 között|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Választható. Írja be az alábbi parancsot a kiszolgálóbővítmény engedélyezéséhez:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>A Spark magic konfigurálása a HDInsight Spark-fürthöz való csatlakozáshoz

Ebben a szakaszban konfigurálja a Spark varázslat, amely korábban telepített egy Apache Spark-fürthöz való csatlakozáshoz.

1. Indítsa el a Python rendszerhéjat a következő paranccsal:

    ```cmd
    python
    ```

2. A Jupyter konfigurációs adatait általában a felhasználók kezdőkönyvtárában tárolják. Írja be a következő parancsot a kezdőkönyvtár azonosításához, és hozzon létre egy **.sparkmagic**nevű mappát.  A teljes elérési út kimenetre kerül.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. A mappán `.sparkmagic`belül hozzon létre egy **config.json** nevű fájlt, és adja hozzá a következő JSON-kódrészletet.  

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

      "custom_headers" : {
        "X-Requested-By": "livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. A következő módosításokat kell elkészíteni a fájlon:

    |Sablon értéke | Új érték |
    |---|---|
    |{FELHASZNÁLÓNÉV}|Fürtbejelentkezés, az `admin`alapértelmezett érték .|
    |{CLUSTERDNSNAME}|Fürt neve|
    |{BASE64ENCODEDPASSWORD}|A base64 kódolt jelszót a tényleges jelszót.  A rendszer base64 jelszót [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/)hozhat létre a rendszeren.|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Tartsa meg, ha használja `sparkmagic 0.12.7` (3.5-ös és 3.6-os fürt).  Ha `sparkmagic 0.2.3` a (3.4-es csoport) használata esetén cserélje ki a helyére. `"should_heartbeat": true`|

    A [config.json mintában](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json)egy teljes példafájl látható.

   > [!TIP]  
   > A rendszer szívveréseket küld annak érdekében, hogy a munkamenetek ne szivárogjanak ki. Amikor a számítógép alvó állapotba kerül vagy leáll, a rendszer nem küldi el a szívverést, ami a munkamenet et törli. A 3.4-es vagyonos fürtök esetében, ha le szeretné tiltani `livy.server.interactive.heartbeat.timeout` `0` ezt a viselkedést, beállíthatja a Livy config-ot az Ambari felhasználói felületről. A 3.5-ös csoportnál, ha nem állítja be a fenti 3.5-ös konfigurációt, a munkamenet nem törlődik.

5. Indítsd el a Jupytert. Használja a következő parancsot a parancssorból.

    ```cmd
    jupyter notebook
    ```

6. Ellenőrizze, hogy használhatja-e a kernelekkel elérhető Spark-varázslatot. Hajtsa végre a következő lépéseket.

    a. Hozzon létre új notebookot. A jobb oldali sarokban válassza az **Új**lehetőséget. Meg kell jelennie az alapértelmezett **kernel Python 2** vagy Python **3** és a telepített kernelek. A tényleges értékek a telepítési lehetőségektől függően változhatnak.  Válassza a **PySpark**lehetőséget.

    ![Elérhető kernelek a Jupyter notebookban](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Kernelek a Jupyter notebookban")

    > [!IMPORTANT]  
    > Az **Új** áttekintés kiválasztása után a rendszerhéj hiba.  Ha a hibát `TypeError: __init__() got an unexpected keyword argument 'io_loop'` látja, akkor a Tornado bizonyos verzióiban ismert probléma léphet fel.  Ha igen, állítsa le a kernelt, majd `pip install tornado==4.5.3`csökkentse a Tornádó telepítését a következő paranccsal: .

    b. Futtassa a következő kódrészletet.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Ha sikeresen letudja kérni a kimenetet, a rendszer teszteli a HDInsight-fürthöz való csatlakozást.

    Ha frissíteni szeretné a jegyzetfüzet konfigurációját, hogy egy másik fürthöz csatlakozzon, frissítse a config.json t az új értékkészlettel, amint azt a fenti 3.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Miért érdemes telepíteni a Jupyter-t a számítógépre?

Számos oka lehet annak, hogy miért érdemes telepíteni a Jupyter-t a számítógépre, majd csatlakoztatni egy Apache Spark-fürthöz a HDInsighton.

* Annak ellenére, hogy a Jupyter-jegyzetfüzetek már elérhetők az Azure HDInsight Spark-fürtjén, a Jupyter telepítése a számítógépre lehetővé teszi a jegyzetfüzetek helyi létrehozását, az alkalmazás egy futó fürtre való tesztelését, majd a jegyzetfüzeteket a fürthöz. A jegyzetfüzetek fürtbe való feltöltéséhez töltse fel őket a futó Jupyter-jegyzetfüzet vagy a fürt használatával, vagy mentse őket a fürthöz társított tárfiók /HdiNotebooks mappájába. A jegyzetfüzetek fürtön való tárolásáról a [Hol tárolják a Jupyter-jegyzetfüzeteket?](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)
* A helyileg elérhető jegyzetfüzetek segítségével az alkalmazás követelménye alapján különböző Spark-fürtökhöz csatlakozhat.
* A GitHub segítségével egy forrásvezérlő rendszert valósíthat meg, és verzióvezérléssel rendelkezik a jegyzetfüzetekhez. Együttműködési környezetet is létrehozhat, ahol több felhasználó dolgozhat ugyanazzal a jegyzetfüzettel.
* A jegyzetfüzetekkel helyileg is dolgozhat anélkül, hogy fürtfel lenne a kontráda. Csak egy fürtre van szüksége a jegyzetfüzetek teszteléséhez, nem a jegyzetfüzetek vagy a fejlesztői környezet manuális kezeléséhez.
* Előfordulhat, hogy könnyebb beállítani a saját helyi fejlesztői környezetet, mint a Jupyter telepítését a fürtön.  A helyileg telepített összes szoftvert kihasználhatja egy vagy több távoli fürt konfigurálása nélkül.

> [!WARNING]  
> Ha a Jupyter telepítve van a helyi számítógépen, több felhasználó is futtathatja ugyanazt a jegyzetfüzetet ugyanazon a Spark-fürtön egy időben. Egy ilyen helyzetben több Livy ülés jön létre. Ha egy problémába ütközik, és ezt hibakereséssel szeretné ellátni, összetett feladat lesz nyomon követni, hogy melyik Livy munkamenet melyik felhasználóhoz tartozik.  

## <a name="next-steps"></a>További lépések

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)
* [Apache Spark bi-val: Interaktív adatelemzés a Spark használatával a HDInsightban az üzletiintelligencia-eszközökkel](apache-spark-use-bi-tools.md)
* [Apache Spark gépi tanulással: Használja a Sparkot a HDInsightban az épület hőmérsékletének elemzéséhez a fűtés- és légtechnikai adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
