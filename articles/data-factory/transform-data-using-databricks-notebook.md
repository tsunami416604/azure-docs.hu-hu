---
title: Databricks-jegyzetfüzet futtatása a Databricks-jegyzetfüzet tevékenységeivel az Azure Data Factoryban
description: Ismerje meg a Databricks-jegyzetfüzetek tevékenységeinek használatát egy Azure adat-előállítóban a Databricks-jegyzetfüzetek Databricks-feladatfürtön való futtatásához.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/12/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 85c3c913564dd38ece76ddd7f72b8ea4da52958e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57778824"
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Databricks-jegyzetfüzet futtatása a Databricks-jegyzetfüzet tevékenységeivel az Azure Data Factoryban

Ebben az oktatóanyagban egy olyan Azure Data Factory-folyamatot hoz létre az Azure Portal használatával, amely végrehajt egy Databricks-jegyzetfüzetet a Databricks-feladatfürtön. Továbbítja továbbá az Azure Data Factory paramétereit a Databricks-jegyzetfüzetnek a végrehajtás során.

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

  - Adat-előállító létrehozása

  - Hozzon létre egy Databricks-jegyzetfüzetek tevékenységeit használó folyamatot.

  - Folyamat futtatásának aktiválása

  - A folyamat futásának monitorozása.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként létrehozhat egy  [ingyenes](https://azure.microsoft.com/free/)  fiókot.

Az alábbi videóban a funkció bemutatását és ismertetését tekintheti meg tizenegy percben:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/ingest-prepare-and-transform-using-azure-databricks-and-data-factory/player]

## <a name="prerequisites"></a>Előfeltételek

  - **Azure Databricks-munkaterület**. [Hozzon létre egy Databricks-munkaterületet](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal), vagy használjon egy már meglévőt. Hozzon létre egy Python-jegyzetfüzetet az Azure Databricks-munkaterületen. Ezután hajtsa végre a jegyzetfüzetet, és továbbítson neki paramétereket az Azure Data Factory segítségével.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1.  Indítsa el a **Microsoft Edge** vagy **Google Chrome** webböngésző. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.

1.  Válassza ki **erőforrás létrehozása** a bal oldali menüben válassza ki a **Analytics**, majd válassza ki **adat-előállító**.

    ![Új adat-előállító létrehozása](media/transform-data-using-databricks-notebook/new-azure-data-factory-menu.png)

1.  Az a **új adat-előállító** panelen adja meg **ADFTutorialDataFactory** alatt **neve**.

    Az Azure-beli adat-előállító nevét kell *globálisan egyedi*. Ha a következő hibát látja, módosítsa az adat-előállító nevét. (Például **\<sajátneve\>ADFTutorialDataFactory**). Data Factory-összetevők elnevezési szabályait, tekintse meg a [Data Factory – elnevezési szabályok](https://docs.microsoft.com/azure/data-factory/naming-rules) cikk.

    ![Az új adat-előállító elnevezése](media/transform-data-using-databricks-notebook/new-azure-data-factory.png)

1.  A **előfizetés**, válassza ki az Azure-előfizetés, amelyben az adat-előállítót létre szeretné.

1.  A **erőforráscsoport**, hajtsa végre a következő lépések egyikét:
    
    - Válassza ki **meglévő** és a legördülő listából válasszon ki egy meglévő erőforráscsoportot.
    
    - Válassza ki **új létrehozása** , és adja meg az erőforráscsoport nevét.

    Ebben a rövid útmutatóban a lépések azt feltételezik, hogy a név **ADFTutorialResourceGroup** az erőforráscsoportnak. Erőforráscsoportok kapcsolatos további információkért lásd: [erőforráscsoportok használata az Azure-erőforrások kezelése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

1.  A **verzió**válassza **V2**.

1.  A **hely**, válassza ki az adat-előállító helyét.

    Azure-régióban, amelyben a Data Factory jelenleg listája, válassza ki a régiók, amelyek a következő oldalon érdeklődésére számot tartó, és bontsa ki **Analytics** található **adat-előállító**: [Régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/). A Data Factory által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.
1.  Válassza ki **létrehozása**.


1.  A létrehozás befejezése után megjelenik a **adat-előállító** lapot. Válassza ki a **létrehozás és Monitorozás** csempére kattintva indítsa el a Data Factory felhasználói felülete alkalmazás külön lapon.

    ![Az adat-előállító felhasználói felület alkalmazás indítása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Ebben a szakaszban létrehoz egy Databricks társított szolgáltatást. Ez a társított szolgáltatás tartalmazza a Databricks-fürt kapcsolatadatait:

### <a name="create-an-azure-databricks-linked-service"></a>Azure Databricks-beli társított szolgáltatás létrehozása

1.  Az a **első lépések** lapon, váltson át a **szerkesztése** lapra a bal oldali panelen.

    ![Az új társított szolgáltatás szerkesztése](media/transform-data-using-databricks-notebook/get-started-page.png)

1.  Válassza ki **kapcsolatok** alján az ablakot, és válassza ki, **+ új**.
    
    ![Új kapcsolat létrehozása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

1.  Az a **új társított szolgáltatás** ablakban válassza **számítási** \> **Azure Databricks**, majd válassza ki ** Továbbra is**.
    
    ![Databricks társított szolgáltatás megadása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

1.  Az a **új társított szolgáltatás** ablakban hajtsa végre a következő lépéseket:
    
    1.  A **Név** mezőben adja meg a következőt: ***AzureDatabricks\_LinkedService***
    
    1.  Válassza ki a megfelelő **Databricks-munkaterületet**, amelyen a jegyzetfüzetet futtatni fogja

    1.  A **Fürt kiválasztása** mezőben válassza az **Új feladatfürt** lehetőséget
    
    1.  A **Tartomány/régió** mezőben az adatoknak automatikusan ki kell töltődnie

    1.  A **Hozzáférési jogkivonatot** hozza létre az Azure Databricks-munkaterületről. A lépéseket [itt](https://docs.databricks.com/api/latest/authentication.html#generate-token) találhatja meg.

    1.  A **fürtverzió**válassza **4.2** (az Apache Spark 2.3.1, Scala 2.11-et)

    1.  A **Fürtcsomópont típusa** mezőben válassza a **Standard\_D3\_v2** lehetőséget az **Általános célú (HDD)** területen ehhez az oktatóanyaghoz. 
    
    1.  A **Feldolgozók** mezőben adja meg a **2** mennyiséget.
    
    1.  Válassza a **Befejezés** lehetőséget.

        ![A társított szolgáltatás létrehozásának befejezése](media/transform-data-using-databricks-notebook/new-databricks-linkedservice.png)

## <a name="create-a-pipeline"></a>Folyamat létrehozása

1.  Válassza ki a **+** (plusz) gombra, és válassza ki **folyamat** menüben.

    ![Új folyamat létrehozására szolgáló gombok](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

1.  Hozzon létre egy, a **folyamatban** használni kívánt **paramétert**. Később ezt a paramétert átadhatja a Databricks-jegyzetfüzet tevékenységeinek. Az üres folyamatban kattintson a **Paraméterek** lapra, majd az **Új** lehetőségre, és adja neki a „**name**” nevet.

    ![Új paraméter létrehozása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![A név paraméter létrehozása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

1.  Az a **tevékenységek** eszközkészletben bontsa ki a **Databricks**. Húzza a **Notebook** tevékenységet a **tevékenységek** eszközkészletből a folyamat tervezőfelületére.

    ![A jegyzetfüzet áthúzása a tervezőfelületre](media/transform-data-using-databricks-notebook/new-adf-pipeline.png)

1.  Tulajdonságai között a **Databricks** **Notebook** tevékenység ablak alján, a következő lépéseket:

    a. Váltson a **Azure Databricks** fülre.

    b. Válassza ki **az Azure Databricks\_LinkedService** (amely az előző eljárásban létrehozott).

    c. Váltson a **Beállítások** lapra

    c. Tallózással válasszon ki egy Databricks-**jegyzetfüzet útvonalat**. Hozzunk létre egy jegyzetfüzetet, és adjuk meg itt az útvonalat. A következő néhány lépést követve megkaphatja a jegyzetfüzet útvonalat.

       1. Az Azure Databricks-munkaterület indítása

       1. Hozzon létre egy **Új mappát** a munkaterületen, és adja neki az **adftutorial** nevet.

          ![Új mappa létrehozása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       1. [Hozzon létre egy új jegyzetfüzetet](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (Python), adja neki a **mynotebook** nevet az **adftutorial** mappában **,** kattintson a **Létrehozás** lehetőségre.

          ![Új jegyzetfüzet létrehozása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![Az új jegyzetfüzet tulajdonságainak beállítása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       1. Az újonnan létrehozott „mynotebook” jegyzetfüzetben adja hozzá a következő kódot:

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           dbutils.widgets.get("input")
           y = getArgument("input")
           print "Param -\'input':"
           print y
           ```

           ![Widgetek létrehozása paraméterekhez](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       1. Ebben az esetben a **jegyzetfüzet útvonala** **/adftutorial/mynotebook**.

1.  Váltson vissza a **Data Factory felhasználói felületet létrehozó eszközre**. Navigáljon a **Beállítások** lapra az **1. jegyzetfüzet tevékenységei** területen.

    a.  **Adjon paramétert** a jegyzetfüzet tevékenységeihez. Ugyanazt a paramétert használja, mint amit korábban hozzáadott a **folyamathoz**.

       ![Paraméter hozzáadása](media/transform-data-using-databricks-notebook/new-adf-parameters.png)

    b.  A paraméter neve legyen **bemeneti** , és az értéket kifejezésként  **\@folyamat ().parameters.name**.

1.  A folyamat érvényesítéséhez kattintson az **ellenőrzése** gombra az eszköztáron. Az érvényesítési ablak bezárásához válassza a **\>\>** (jobbra mutató nyíl) gombra.

    ![A folyamat érvényesítése](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

1.  Válassza ki **összes közzététele**. A Data Factory felhasználói felülete entitásokat (társított szolgáltatásokat és folyamatot) tesz közzé az Azure Data Factory szolgáltatásban.

    ![Az új adat-előállító entitások közzététele](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>Folyamat futtatásának aktiválása

Válassza ki **eseményindító** az eszköztáron, majd válassza ki a **Aktiválás most**.

![Az Aktiválás most parancs kiválasztása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

A **Folyamatfuttatás** párbeszédpanel kéri a **név** paramétert. Itt paraméterként használja a következőt: **/path/filename**. Kattintson a **Befejezés** gombra.

![Érték megadása a név paraméterekhez](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1.  Váltson a **figyelő** fülre. Ellenőrizze, hogy megjelenik-e a futó folyamat. Egy, a jegyzetfüzetet végrehajtó Databricks feladatfürt létrehozása körülbelül 5–8 percet vesz igénybe.

    ![A folyamat figyelése](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

1.  Válassza ki **frissítése** rendszeres időközönként, hogy a folyamat futási állapotának ellenőrzéséhez.

1.  A folyamat futásához társított tevékenységfuttatások megtekintéséhez válasszon **Tevékenységfuttatások megtekintése** a a **műveletek** oszlop.

    ![A tevékenységfuttatások megtekintése](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

Válthat vissza a folyamatfuttatások nézetre való kiválasztásával a **folyamatok** a fenti hivatkozásra.

## <a name="verify-the-output"></a>Kimenet ellenőrzése

Jelentkezzen be az **Azure Databricks-munkaterületre**, lépjen a **Fürtökhöz**, és láthatja, hogy a **Feladat** állapota *végrehajtás függőben, fut vagy megszakítva*.

![A feladatfürt és a feladat megtekintése](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

A **Feladat nevére** kattintva megtekintheti a további részleteket. Ha a futás sikeres, érvényesítheti az átadott paramétereket és a Python-jegyzetfüzet kimenetét.

![A futtatás részleteinek és kimenetének megtekintése](media/transform-data-using-databricks-notebook/databricks-output.png)

## <a name="next-steps"></a>További lépések

A mintában szereplő folyamat elindít egy Databricks-jegyzetfüzet tevékenységet, és átad neki egy paramétert. Megismerte, hogyan végezheti el az alábbi műveleteket:

  - Adat-előállító létrehozása

  - Hozzon létre egy Databricks-jegyzetfüzetek tevékenységeit használó folyamatot.

  - Folyamat futtatásának aktiválása

  - A folyamat futásának monitorozása.
