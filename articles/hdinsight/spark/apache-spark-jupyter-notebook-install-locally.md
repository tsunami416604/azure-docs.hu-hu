---
title: Jupyter helyileg telepíteni, és csatlakozzon az Azure HDInsight Spark-fürt |} Microsoft Docs
description: Útmutató a Jupyter notebook helyileg telepíteni a számítógépére, és csatlakoztassa egy Azure HDInsight az Apache Spark-fürt.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48593bdf-4122-4f2e-a8ec-fdc009e47c16
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: eea61586054f34142d77f16333fe70a66d95d529
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31528358"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Jupyter notebook telepítse a számítógépre, és kapcsolódjon az Apache Spark on HDInsight

Ebben a cikkben megismerheti, hogyan kell telepíteni a Jupyter notebook egyéni PySpark (a Python) és a Spark magic (a Scala) Spark mag, és a notebook kapcsolódni HDInsight-fürtök. Számos Jupyter telepítéséhez a helyi számítógépen oka lehet, és némi kihívást is lehet. Bővebben a részben [Miért célszerű telepíteni Jupyter a számítógépemen](#why-should-i-install-jupyter-on-my-computer) Ez a cikk végén.

Három fő lépésből áll részt Jupyter és a Spark magic telepíti a számítógépre.

* Jupyter notebook telepítése
* Telepítse a PySpark és Spark mag a Spark varázsszám
* A HDInsight Spark-fürt eléréséhez Spark magic konfigurálása

Az egyéni kernelek és a Spark HDInsight-fürthöz használt Jupyter notebookokban elérhető magic kapcsolatos további információkért lásd: [Apache Spark Linux és a Jupyter notebookok elérhető kernelek a HDInsight-fürtök](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Előfeltételek
Az itt felsorolt előfeltételeket nem Jupyter telepítéséhez. Ezek azok a Jupyter notebook kapcsolódáshoz HDInsight-fürtöt, a notebook telepítése után.

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* A HDInsight az Apache Spark-fürt. Útmutatásért lásd: [létrehozása az Apache Spark on Azure hdinsight clusters](apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Jupyter notebook telepítse a számítógépre

Jupyter notebookok telepítése előtt telepítenie kell az Python. Python és a Jupyter állnak rendelkezésre, egy részének a [Anaconda terjesztési](https://www.continuum.io/downloads). Ha Anaconda telepíti, a Python egy terjesztési telepítése. Anaconda telepítése után adja hozzá a Jupyter telepítés megfelelő parancsok futtatásával.

1. Töltse le a [Anaconda telepítő](https://www.continuum.io/downloads) a platform és futtassa a telepítőt. A telepítő varázsló futtatása közben, gondoskodjon róla, hogy a beállítás Anaconda hozzáadása a PATH változóban.
2. A következő paranccsal telepítse a Jupyter.

        conda install jupyter

    Jupyter telepítésével kapcsolatos további információkért lásd: [telepítése Jupyter használatával Anaconda](http://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-the-kernels-and-spark-magic"></a>Telepítse a kernelek és Spark varázsszám

A Spark magic telepítéséhez útmutatást a PySpark és Spark mag, hajtsa végre a szereplő telepítési útmutatást a [sparkmagic dokumentáció](https://github.com/jupyter-incubator/sparkmagic#installation) a Githubon. Az első lépés a Spark magic dokumentációjának arra kéri, hogy a Spark magic telepítése. Cserélje le a következő parancsokat a HDInsight-fürthöz fog csatlakozni verziójától függően, hogy a hivatkozás első lépése. Ezután kövesse a további lépéseket a Spark magic dokumentációjában. Ha a különböző kernelek telepíteni kívánja, 3. lépés a Spark magic telepítési utasításokat szakaszban kell elvégeznie.

* A fürtök v3.4 telepítenie sparkmagic 0.2.3 `pip install sparkmagic==0.2.3`

* A fürtök v3.5 és v3.6 telepítse a következő futtatásával sparkmagic 0.11.2 `pip install sparkmagic==0.11.2`

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Spark magic való kapcsolódáshoz a HDInsight Spark-fürt konfigurálása

Ez a szakasz a korábban létrehozott kell már Azure HDInsight az Apache Spark-fürt való csatlakozáshoz telepített Spark magic konfigurálja.

1. A Jupyter konfigurációs adatokat általában tárolja a felhasználók kezdőkönyvtárban. Keresse meg a kezdőkönyvtár az operációsrendszer-platformhoz, írja be a következő parancsokat.

    Indítsa el a Python-rendszerhéjat. Egy parancsablakot írja be a következőt:

        python

    A Python felületet adja meg a kezdőkönyvtár megállapítása a következő parancsot.

        import os
        print(os.path.expanduser('~'))

2. Nyissa meg a kezdőkönyvtár, és hozzon létre egy nevű **.sparkmagic** Ha még nem létezik.
3. A mappában található nevű fájl létrehozása **config.json** , és adja hozzá a következő JSON kódrészletet, azon belül.

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

4. Helyettesítő **{USERNAME}**, **{CLUSTERDNSNAME}**, és **{BASE64ENCODEDPASSWORD}** megfelelő értékekkel. A kedvenc programozási nyelven vagy online segédprogramok számos segítségével készítése base64-kódolású jelszót a tényleges jelszót.

5. Adja meg a megfelelő szívverés-beállításokat a `config.json`. Adja hozzá ezeket a beállításokat ugyanazon a szinten az `kernel_python_credentials` és `kernel_scala_credentials` kódtöredékek a hozzáadott korábban. Például hogyan és hol vegye fel a szívverés-beállításokat, lásd: a [minta config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

    * A `sparkmagic 0.2.3` (v3.4 fürtöket) tartalmazza:

            "should_heartbeat": true,
            "heartbeat_refresh_seconds": 5,
            "heartbeat_retry_seconds": 1

    * A `sparkmagic 0.11.2` (fürtök v3.5 és v3.6), a következők:

            "heartbeat_refresh_seconds": 5,
            "livy_server_heartbeat_timeout_seconds": 60,
            "heartbeat_retry_seconds": 1

    >[!TIP]
    >Annak érdekében, hogy munkamenetek nem szivárognak szívveréseket küld. Ha a számítógép alvó állapotba, vagy le van állítva, a szívverés nem küldi el, ami azt eredményezi, hogy a munkamenet éppen törölni a. Fürtök v3.4, ha szeretné letiltani ezt a viselkedést állíthatók be a Livy config `livy.server.interactive.heartbeat.timeout` való `0` a az Ambari felhasználói Felületéről. A fürtök v3.5 Ha nincs megadva a fenti, 3.5-ös configuration a munkamenet nem törlődik.

6. Indítsa el a Jupyter. Használja a következő parancsot a parancssorból.

        jupyter notebook

7. Győződjön meg arról, hogy a fürt a Jupyter notebook használatával csatlakozhat, és hogy használata a Spark magic érhető el a kernelek. A következő lépésekkel.

    a. Hozzon létre új notebookot. Kattintson a jobb oldali sarokban **új**. Az alapértelmezett kernel kell megjelennie **Python2** és a két új kernelek, a telepítés **PySpark** és **Spark**. Kattintson a **PySpark**.

    ![A Jupyter notebook kernelek](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "kernelek a Jupyter notebook")

    b. Futtassa a következő kódrészletet.

        %%sql
        SELECT * FROM hivesampletable LIMIT 5

    A kimeneti sikeresen le, ha a kapcsolat a HDInsight-fürthöz tesztelése.

    >[!TIP]
    >Ha szeretné frissíteni a notebook konfigurációját egy másik fürthöz való csatlakozáshoz, frissítse a config.json az új értékhalmazt látható a fenti a 3. lépésben.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Miért kell telepíteni a számítógépre Jupyter?
Számos oka, hogy miért érdemes Jupyter telepítse a számítógépre, és csatlakoztassa a HDInsight Spark-fürt lehet.

* Annak ellenére, hogy a Jupyter notebookok már elérhetők az Azure HDInsight Spark-fürt, Jupyter telepíti a számítógépre, lehetőséget biztosít a notebookok helyileg létrehozása, tesztelheti az alkalmazást egy működő fürthöz, majd töltse fel a notebookok a fürthöz. Töltse fel a notebookok a fürthöz, is feltöltheti ezeket a Jupyter notebook futtató vagy a fürt, vagy menti a tárfiók a fürthöz tartozó /HdiNotebooks mappájában. A fürt notebookok tárolásával további információkért lásd: [Jupyter notebookok tároló](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Elérhető notebookok helyileg, akkor csatlakozhatnak különböző Spark-fürtjei alapján az alkalmazás követelményeinek.
* GitHub segítségével valósítja meg a forrásrendszerben vezérlő és a notebookok verzió-vezérlést. A együttműködési környezetekben, ahol több felhasználó dolgozhat ugyanazon a notebook is lehet.
* Dolgozhat notebookok helyileg egy fürtöt is nélkül. Csak akkor kell tesztelni a notebookok ellen, nem kézi kezelését a jegyzetfüzetek és a környezet egy fürt.
* Elképzelhető, hogy könnyebben a saját helyi fejlesztési környezet konfigurálása, mint a Jupyter telepítésének konfigurálása a fürtön.  Kihasználhatja a egy vagy több távoli fürtök beállítása nélkül helyileg telepített összes szoftver.

> [!WARNING]
> Jupyter a helyi számítógépen, a több felhasználó futtathatja a azonos notebook Spark-fürt egyszerre. Ilyen esetben több Livy munkamenetek jönnek létre. Ha problémát tapasztal, és végezzen azon hibakeresést szeretne, mely felhasználó tartozik egy összetett feladat, mely Livy munkamenet nyomkövetéséhez lesz.
>
>

## <a name="seealso"></a>Lásd még:
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark-alkalmazások távoli hibaelhárításához](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
