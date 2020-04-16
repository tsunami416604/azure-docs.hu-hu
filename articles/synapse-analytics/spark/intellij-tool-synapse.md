---
title: Oktatóanyag – Azure Toolkit for IntelliJ (Spark-alkalmazás)
description: Oktatóanyag – az Azure Toolkit for IntelliJ segítségével fejlesztheti a Scala-ban írt Spark-alkalmazásokat, és küldje el őket egy Apache Spark-készletbe (előzetes verzió).
services: synapse-analytics
author: v-jiche
ms.author: v-jiche
ms.reviewer: jrasnick, carlrab
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 310dfec2bbd1bf6cb69f6293becead30487d6cbd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422654"
---
# <a name="tutorial-use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-spark-pools-preview"></a>Oktatóanyag: Az Azure Toolkit for IntelliJ használatával Apache Spark-alkalmazásokat hozhat létre Spark-készletekhez (előzetes verzió)

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Toolkit for IntelliJ beépülő modult a [Scala-ban](https://www.scala-lang.org/)írt Apache Spark-alkalmazások fejlesztéséhez, majd küldje el őket közvetlenül az IntelliJ integrált fejlesztői környezetből (IDE) lévő Spark-készletbe (előzetes verzióra). A beépülő modult többféleképpen is használhatja:

- Scala Spark-alkalmazás fejlesztése és küldése egy Spark-készleten.
- A Spark-készletek erőforrásainak elérése.
- Scala Spark-alkalmazást fejleszthet és futtathat helyileg.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> - Az Azure Toolkit for IntelliJ beépülő modul használata
> - Apache Spark-alkalmazások fejlesztése
> - Kérelem beküldése a Spark-készletekbe

## <a name="prerequisites"></a>Előfeltételek

- [IntelliJ IDEA közösségi verzió](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC).
- Azure toolkit plugin 3.27.0-2019.2 – Telepítés az [IntelliJ Plugin adattárból](/java/azure/intellij/azure-toolkit-for-intellij-installation?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [JDK (1.8-as verzió)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Scala Plugin - Telepítse az [IntelliJ Plugin adattárból.](/hdinsight/spark/apache-spark-intellij-tool-plugin#install-scala-plugin-for-intellij-idea.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- Ez az előfeltétel csak a Windows-felhasználók számára.

  A helyi Spark Scala-alkalmazás Windows rendszeren való futtatása közben kivételt kaphat, ahogy azt a [SPARK-2356 is](https://issues.apache.org/jira/browse/SPARK-2356)ismerteti. A kivétel oka az, hogy a WinUtils.exe hiányzik a Windows rendszerből.
  A hiba megoldásához töltse le a [WinUtils végrehajtható](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) fájlt egy olyan helyre, mint a **C:\WinUtils\bin**. Ezután adja hozzá a **HADOOP_HOME**környezeti változót, és állítsa a változó értékét **C:\WinUtils értékre.**

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Spark Scala-alkalmazás létrehozása spark-készlethez

1. Indítsa el az IntelliJ IDEA programot, és válassza az **Új projekt létrehozása lehetőséget** az Új **projekt** ablak megnyitásához.
2. Válassza az **Apache Spark/HDInsight** lehetőséget a bal oldali ablaktáblában.
3. Válassza ki a **Spark-projekt minták (Scala)** a főablakban.
4. A **Build eszköz** legördülő listájában válasszon az alábbi típusok közül:

   - **Maven** a Scala projektkészítő varázsló támogatásához.
   - **SBT** a függőségek kezeléséhez és a Scala projekt létrehozásához.

    ![IntelliJ IDEA Új projekt párbeszédpanel](./media/intellij-tool-synapse/create-synapse-application01.png)

5. Kattintson a **Tovább** gombra.
6. Az **Új projekt** ablakban adja meg a következő információkat:

    | Tulajdonság | Leírás |
    | ----- | ----- |
    |Projektnév| Írjon be egy nevet. Ebben az oktatóanyagban a következőt használjuk: `myApp`.|
    |Projekt&nbsp;helye| Adja meg a kívánt helyet a projekt mentéséhez.|
    |Projekt SDK| Lehet, hogy üres az első használata IDEA. Válassza az **Új lehetőséget...** és keresse meg a JDK-t.|
    |Spark-verzió|A létrehozási varázsló integrálja a Spark SDK és a Scala SDK megfelelő verzióját. A Synapse csak a **Spark 2.4.0-t**támogatja.|

    ![Az Apache Spark SDK kiválasztása](./media/intellij-tool-synapse/create-synapse-application02.png)

7. Válassza a **Finish** (Befejezés) elemet. Eltarthat néhány percig, amíg a projekt elérhetővé válik.
8. A Spark-projekt automatikusan létrehoz egy műtárgyat. Az műtermék megtekintéséhez végezze el a következő működést:

   a. A menüsorból lépjen a > **Fájlprojekt-struktúra elemre...**. **File**

   b. A **Projektszerkezet ablakban** válassza az **Eltérések**lehetőséget.

   c. Válassza **a Mégse** gombot az műtermék megtekintése után.

    ![Műtermék-adatok a párbeszédpanelen](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. **Keresse meg a LogQuery-t** a **myApp** > **scala** > **fő** > **scala mintából**> **sample**> **LogQuery**. Ez az oktatóanyag **a LogQuery** t használja a futtatáshoz.

   ![Scala-osztály létrehozására szolgáló parancsok a Projectből](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Csatlakozás a Spark-készletekhez

Jelentkezzen be az Azure-előfizetésbe, és csatlakozzon a Spark-készletekhez.

### <a name="sign-in-to-your-azure-subscription"></a>Bejelentkezés Azure-előfizetésbe

1. A menüsorban keresse meg a**Windows** > **Azure Explorer**eszköz **megtekintése** > című eszközt.

   ![Az IntelliJ IDEA megmutatja az Azure Explorert](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. Az Azure Explorerben kattintson a jobb gombbal az Azure-csomópontra, majd válassza **a Bejelentkezés parancsot.** **Azure**

   ![IntelliJ IDEA explorer jobb gombbal az azure](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. Az **Azure Bejelentkezés** párbeszédpanelén válassza az **Eszközbejelentkezés**lehetőséget, majd kattintson **a Bejelentkezés gombra.**

    ![IntelliJ IDEA azure bejelentkezés](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. Az **Azure-eszközbejelentkezés párbeszédpanelen** kattintson **a&másolása megnyitás gombra.**

   ![IntelliJ IDEA azure eszköz bejelentkezés](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. A böngészőfelületen illessze be a kódot, majd kattintson a **Tovább**gombra.

   ![A Microsoft belép a HDI kódpárbeszédpanelébe](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. Adja meg az Azure-hitelesítő adatait, majd zárja be a böngészőt.

   ![A Microsoft belép a HDI e-mail párbeszédablakába](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. Miután bejelentkezett, az **Előfizetések kiválasztása** párbeszédpanel felsorolja a hitelesítő adatokhoz társított összes Azure-előfizetést. Válassza ki az előfizetést, majd kattintson a **Kijelölés gombra.**

    ![Az Előfizetések kiválasztása párbeszédpanel](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. Az **Azure Explorerben**bontsa ki **az Apache Spark ot a Synapse-on** az előfizetésekben lévő munkaterületek megtekintéséhez.

    ![IntelliJ IDEA Azure Explorer főnézete](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. A Spark-készletek megtekintéséhez tovább bővítheti a munkaterületet.

    ![Azure Explorer tárfiókok](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Spark Scala-alkalmazás távoli futtatása egy Spark-készleten

A Scala-alkalmazás létrehozása után távolról futtathatja azt.

1. Az ikonra kattintva nyissa meg a **Futtatás/Hibakeresési beállítások ablakot.**

    ![A Spark-alkalmazás küldése a HDInsightba parancs](./media/intellij-tool-synapse/open-configuration-window.png)

2. A **Futtatás/Hibakeresés párbeszédpanelen** kattintson **+** a lehetőségre, majd válassza az Apache Spark lehetőséget **a Synapse alkalmazásban**.

    ![A Spark-alkalmazás küldése a HDInsightba parancs](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. A **Futtatás/Hibakeresés beállításai** ablakban adja meg a következő értékeket, majd kattintson az **OK gombra:**

    |Tulajdonság |Érték |
    |----|----|
    |Szikramedencék|Válassza ki azokat a Spark-készleteket, amelyeken futtatni szeretné az alkalmazást.|
    |A beküldni kívánt műtermék kiválasztása|Hagyja meg az alapértelmezett beállítást.|
    |Fő osztály neve|Az alapértelmezett érték a kijelölt fájl fő osztálya. Megváltoztathatja az osztályt, ha kiválasztja a három pontot(**...**) és kiválaszt egy másik osztályt.|
    |Feladatkonfigurációk|Módosíthatja az alapértelmezett kulcsot és értékeket. További információ: [Apache Livy REST API.](https://livy.incubator.apache.org./docs/latest/rest-api.html)|
    |Parancssori argumentumok|Szükség esetén a főosztály térközével elválasztott argumentumokat is megadhat.|
    |Hivatkozott tégelyek és hivatkozott fájlok|Megadhatja a hivatkozott jarok és fájlok elérési útjait, ha vannak ilyenek. Az Azure virtuális fájlrendszerben is tallózhat fájlokközött, amelyek jelenleg csak az ADLS Gen 2 fürtöt támogatják. További információ: [Apache Spark konfiguráció](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) és hogyan [tölthető fel erőforrásokat a fürtbe](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).|
    |Feladat feltöltésének tárolása|Bontsa ki, hogy felfedje a további lehetőségeket.|
    |Tárhelytípusa|Válassza az Azure Blob használata a legördülő listából **való feltöltéshez** lehetőséget.|
    |Tárfiók|Adja meg tárfiókját.|
    |Tárolókulcs|Adja meg a tárolókulcsot.|
    |Tároló tároló|Válassza ki a tárolótárolót a legördülő listából, miután **a tárfiók** és **a tárolási kulcs** beírása után.|

    ![A Szikraküldés párbeszédpanel](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. Kattintson a **SparkJobRun** ikonra a projekt kijelölt Spark-készletbe való elküldéséhez. A **Fürt távoli spark-feladata** a feladat végrehajtási folyamatát jeleníti meg alul. A piros gombra kattintva leállíthatja az alkalmazást.

    ![Az Apache Spark beküldéseablak](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![A Szikraküldés párbeszédpanel](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>Helyi futtatási/hibakeresési Apache Spark-alkalmazások

Az alábbi utasításokat követve beállíthatja a helyi futtatást és a helyi hibakeresést az Apache Spark-feladathoz.

### <a name="scenario-1-do-local-run"></a>1. eset: Végezze el a helyi futtatást

1. Nyissa meg a **Futtatás/Hibakeresési beállítások párbeszédpanelt,** és jelölje ki a pluszjelet (**+**). Ezután válassza az **Apache Spark a Synapse** beállítást. Adja meg a Menteni a **Név**, **A főosztály nevének** adatait.

    ![Intellij Futtatás a hibakeresési konfigurációk helyi futtatása](./media/intellij-tool-synapse/local-run-synapse.png)

    - A környezeti változók és a WinUtils.exe hely helye csak windowsos felhasználók számára van.
    - Környezeti változók: A rendszerkörnyezeti változó automatikusan észlelhető, ha korábban beállította, és nincs szükség manuálisan hozzáadásra.
    - [WinUtils.exe hely:](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)A WinUtils helyét a jobb oldali mappa ikonra kattintva adhatja meg.

2. Ezután kattintson a helyi lejátszás gombra.

    ![Intellij Futtatás a hibakeresési konfigurációk helyi futtatása](./media/intellij-tool-synapse/local-run-synapse01.png)

3. Ha a helyi futtatás befejeződött, ha a parancsfájl kimenetet is tartalmaz, ellenőrizheti a kimeneti fájlt az**__alapértelmezett__** **adatból.** > 

    ![Intellij Project helyi futtatási eredménye](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>2. eset: Helyi hibakeresés

1. Nyissa meg a **LogQuery** parancsfájlt, és állítson be töréspontokat.
2. Kattintson **a Helyi hibakeresés** ikonra a helyi hibakereséshez.

    ![Intellij Project helyi futtatási eredménye](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>Hozzáférés és kezelés a Synapse-munkaterület elérése és kezelése

Az Azure Toolkit for IntelliJ segítségével különböző műveleteket hajthat végre az Azure Explorerben. A menüsorban keresse meg a**Windows** > **Azure Explorer**eszköz **megtekintése** > című eszközt.

### <a name="launch-workspace"></a>Munkaterület indítása

1. Az Azure Explorerből keresse meg az **Apache Spark ot a Synapse-on,** majd bontsa ki.

    ![IntelliJ IDEA Azure Explorer főnézete](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. Kattintson a jobb gombbal egy munkaterületre, majd válassza **a Munkaterület indítása**parancsot, a webhely megnyílik.

    ![A Spark-feladatnézet alkalmazásának részletei](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![A Spark-feladatnézet alkalmazásának részletei](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Spark konzol

Futtathatja a Spark Local Console(Scala) vagy a Spark Livy Interactive Session Console(Scala) futtatását.

### <a name="spark-local-console-scala"></a>Spark helyi konzol (Scala)

Győződjön meg róla, hogy elégedett a WINUTILS.Ensure you've satisfied the WINUTILS. EXE előfeltétel.

1. A menüsorból keresse meg a **Konfigurációk szerkesztése** > **parancsot...**.
2. A **Futtatás/Hibakeresés ablakban** a bal oldali ablaktáblában keresse meg az **Apache Spark at Synapse** > **[Spark on Synapse] myApp (Spark on Synapse] myApp (Spark on Synapse] myApp (Spark on Synapse] myApp (Spark on Synapse] myApp (A Synapse-on] myApp**.From the Run/Debug Configurations window, in the left pane, navigate to Apache Spark on S
3. A főablakban válassza a **Helyi futtatás** lapot.
4. Adja meg a következő értékeket, majd kattintson **az OK gombra:**

    |Tulajdonság |Érték |
    |----|----|
    |Környezeti változók|Győződjön meg arról, hogy a HADOOP_HOME értéke helyes.|
    |WINUTILS.exe hely|Ellenőrizze, hogy az elérési út helyes-e.|

    ![Helyi konzolkészlet konfigurációja](./media/intellij-tool-synapse/local-console-synapse01.png)

5. A Projectből keresse meg a **myApp** > **src** > **fő** > **scala** > **myApp -t**.
6. A menüsorban keresse meg az **Eszközök** > **Spark-konzol** > **A Spark helyi konzolja (Scala) futtatását.**
7. Ezután két párbeszéd ablak jelenhet meg, hogy megkérdezze, szeretné-e automatikusan kijavítani a függőségeket. Ha igen, válassza az **Automatikus javítás lehetőséget.**

    ![IntelliJ IDEA Spark Automatikus javítás párbeszédpanel1](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![IntelliJ IDEA Spark Automatikus javítás párbeszédpanel2](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. A konzolnak az alábbi képhez hasonlóan kell kinéznie. A konzolablak `sc.appName`ban írja be a billentyűt, majd nyomja le a ctrl+Enter billentyűkombinációt. Megjelenik az eredmény. A helyi konzolt a piros gombra kattintva állíthatja le.

    ![IntelliJ IDEA helyi konzol eredménye](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Spark Livy interaktív munkamenet-konzol (Scala)

Csak az IntelliJ 2018.2 és 2018.3 támogatott.

1. A menüsorból keresse meg a **Konfigurációk szerkesztése** > **parancsot...**.

2. A **Futtatás/Hibakeresés ablakban** a bal oldali ablaktáblában keresse meg az **Apache Spark ot a szinapszis** > **[Spark on synapse] myApp oldalon.**

3. A főablakban válassza a Távoli futtatás a **fürtben** lapot.

4. Adja meg a következő értékeket, majd kattintson **az OK gombra:**

    |Tulajdonság |Érték |
    |----|----|
    |Szikramedencék|Válassza ki azokat a Spark-készleteket, amelyeken futtatni szeretné az alkalmazást.|
    ||

    ![Interaktív konzolkonfiguráció](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. A Projectből keresse meg a **myApp** > **src** > **fő** > **scala** > **myApp -t**.

6. A menüsorban keresse meg az **Eszközök** > **Spark-konzol** > **futtatása Spark Livy Interactive Session Console(Scala) című szakaszt.**
7. A konzolnak az alábbi képhez hasonlóan kell kinéznie. A konzolablak `sc.appName`ban írja be a billentyűt, majd nyomja le a ctrl+Enter billentyűkombinációt. Megjelenik az eredmény. A helyi konzolt a piros gombra kattintva állíthatja le.

    ![IntelliJ IDEA interaktív konzol eredménye](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Kijelölés küldése a Spark-konzolra

Kényelmes, ha előre látja a parancsfájl eredményét úgy, hogy valamilyen kódot küld a helyi konzolra vagy a Livy Interactive Session Console(Scala) konzolra. Kiemelhet néhány kódot a Scala-fájlban, majd kattintson a jobb gombbal **a Kijelölés küldése a Spark-konzolra parancsra.** A kiválasztott kódot a rendszer elküldi a konzolnak, és kész. Az eredmény a kód után jelenik meg a konzolon. A konzol ellenőrzi a hibákat, ha léteznek.

   ![Kijelölés küldése a Spark-konzolra](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>További lépések

- [Azure Synapse Analytics](../overview-what-is.md)
- [Hozzon létre egy új Apache Spark-készletet egy Azure Synapse Analytics-munkaterülethez](../../synapse-analytics/quickstart-create-apache-spark-pool.md)
