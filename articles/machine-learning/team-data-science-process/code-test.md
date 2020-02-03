---
title: Tesztelje a data science kódot az Azure DevOps-szolgáltatásokkal – csoportos adatelemzési folyamat
description: Adatok tudományos kódot tesztelés az Azure-ban a UCI felnőtt bevétel előrejelzése adatkészlet a csoportos adatelemzési folyamat és az Azure DevOps-szolgáltatásokkal
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=weig, previous-ms.author=weig
ms.openlocfilehash: 9612114bb368898ccf31b2c8692869b84544b652
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721994"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Adatok tudományos kódot tesztelés az Azure-ban a csoportos adatelemzési folyamat és az Azure DevOps-szolgáltatásokkal
Ez a cikk tartalmaz útmutatást előzetes kód tesztelése egy adatelemzési munkafolyamathoz. Az ilyen tesztelést adatszakértők egy rendszeres és hatékony módszert kínál ellenőrizze a minőség és a kódját a várt eredményt kapja. Olyan csoportos adatelemzési folyamat (TDSP) [projektjét használjuk, amely a korábban közzétett UCI Adult bevételi adatkészletet használja](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) , hogy megmutassa, hogyan lehet elvégezni a kód tesztelését. 

## <a name="introduction-on-code-testing"></a>Bevezetés a kódot tesztelés
"Egységtesztelés" egy hosszú időre visszanyúló eljárás szoftverfejlesztői. Az adatelemzéshez azonban gyakran nem egyértelmű, hogy mit jelent az "egység tesztelése", és hogyan kell tesztelni az adatelemzési életciklus különböző szakaszaihoz tartozó kódokat, például:

* Adatok előkészítése
* Adatok minőségi vizsgálata
* Modellezés
* Modell-üzembehelyezés 

Ez a cikk váltja fel az előfizetési időszak "egység tesztelése" a "kód tesztelése." Hivatkozik tesztelésre, az a Funkciók, amelyek segítségével felmérheti, ha egy adattudományi életciklus egyes lépéseihez szükséges kódot az olyan eredmények ", várt." A személy, aki írja a teszt határozza meg, mi az "megfelelően működik," attól függően, a függvény eredménye például, minőségi ellenőrzése vagy modellezési.

Ez a cikk hivatkozásokat hasznos forrásokat biztosít.

## <a name="azure-devops-for-the-testing-framework"></a>Az Azure DevOps, a tesztelési keretrendszer
Ez a cikk ismerteti, hogyan hajtsa végre és tesztelés az Azure DevOps használatával automatizálhatja. Dönthet, hogy más eszközökkel. Azt is bemutatják, hogyan egy automatikus build beállítása az Azure DevOps használatával, és hozhat létre ügynököket. A build-ügynökök használ az Azure Data Science virtuális gépek (Dsvm).

## <a name="flow-of-code-testing"></a>A kód a tesztelési folyamat
Az adatelemzési projektjéhez tesztelési szabályzat a teljes munkafolyamat így néz ki: 

![A kód tesztelési folyamatábra](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Részletes lépések

A következő lépések segítségével állítsa be, és futtassa a kódot tesztelés és a egy automatizált összeállítási fordító-ügynökhöz és az Azure DevOps használatával:

1. Hozzon létre egy projektet a Visual Studio asztali alkalmazásban:

    ![A Visual Studio "Új projekt létrehozása" képernyő](./media/code-test/create_project.PNG)

   Miután létrehozta a projektet, megtalálhatja azt a Megoldáskezelőben a jobb oldali ablaktáblán:
    
    ![A projekt létrehozásának lépései](./media/code-test/create_python_project_in_vs.PNG)

    ![Megoldáskezelő](./media/code-test/solution_explorer_in_vs.PNG)

1. Hírcsatorna projektkódját az Azure DevOps project kód tárházba: 

    ![Projekt kódtár](./media/code-test/create_repo.PNG)

1. Tegyük fel, hogy néhány előkészítő munka, például adatbetöltést, funkciófejlesztési és címke oszlopok létrehozásához végezze el. Ellenőrizze, hogy a kód létrehozza az eredményeket várhatóan szeretné. Íme néhány kódot, amely annak megállapítására, hogy megfelelően működik az adatfeldolgozási kódot használhatja:

    * Ellenőrizze, hogy megfelelő oszlopainak nevét:
    
      ![Az oszlopnevek egyező kód](./media/code-test/check_column_names.PNG)

    * Ellenőrizze, hogy megfelelőek-e a válasz szintek:

      ![Kód az egyező szintek](./media/code-test/check_response_levels.PNG)

    * Ellenőrizze, hogy a válasz százalékos ésszerű:

      ![Kód választ százalékának](./media/code-test/check_response_percentage.PNG)

    * A hiányzó sebessége az egyes oszlopok az adatok ellenőrzése:
    
      ![A hiányzó ráta kód](./media/code-test/check_missing_rate.PNG)


1. Miután hajtotta végre az adatfeldolgozás és a szolgáltatás mérnöki munkát, és a megfelelő modellek, hogy betanított, győződjön meg arról, hogy az Ön betanított modell is pontszámot rendelni az új adatkészletek megfelelően. A következő két tesztek segítségével ellenőrizze az előrejelzési szintek és a felirat értékek eloszlása:

    * Ellenőrizze az előrejelzési szintek:
    
      ![Kód ellenőrzése előrejelzési szintek](./media/code-test/check_prediction_levels.PNG)

    * Ellenőrizze az előrejelzési értékek eloszlása:

      ![Előrejelzési értékek ellenőrzésére szolgáló kód](./media/code-test/check_prediction_values.PNG)

1. Az összes teszt függvényt egyesítse egy **test_funcs. a.** :

    ![Python-szkriptet a tesztelési funkciók](./media/code-test/create_file_test_func.PNG)


1. Után készen áll a teszt kódokat, beállíthatja a tesztelési környezetet a Visual Studióban.

   Hozzon létre egy **test1.py**nevű Python-fájlt. Ezt a fájlt hozzon létre egy osztályt, amely tartalmazza az összes tesztet szeretne tenni. Az alábbi példa bemutatja az előkészített hat tesztek:
    
    ![Python-fájlt osztályban található tesztek listája](./media/code-test/create_file_test1_class.PNG)

1. Ezeket a teszteket automatikusan felderítheti, ha az **codetest. testCase** az osztály neve után helyezi el. Nyissa meg a test Explorert a jobb oldali ablaktáblán, és válassza az **összes futtatása**lehetőséget. Az összes teszt egymás után fog futni, és jelzi, ha a teszt sikeres-e vagy sem.

    ![A tesztek futtatása](./media/code-test/run_tests.PNG)

1. Ellenőrizze a kódban, hogy a projekt tárház Git-parancsok használatával. A legutóbbi munkahelyi hamarosan az Azure DevOps megjelennek.

    ![Git-parancsok a kód ellenőrzése](./media/code-test/git_check_in.PNG)

    ![Legutóbbi munka az Azure DevOps](./media/code-test/git_check_in_most_recent_work.PNG)

1. Automatikus build beállítása és tesztelése az Azure DevOps:

    a. A projekt adattárában válassza a **Létrehozás és kiadás**lehetőséget, majd az új létrehozási folyamat létrehozásához válassza az **+ új** lehetőséget.

    ![Új build-folyamat indításának kiválasztása](./media/code-test/create_new_build.PNG)

    b. Kövesse az utasításokat, jelölje be a forráshely kódja, a projekt nevét, a tárház és a fiókadatokat.
    
    ![Forrás-, név-, adattár-és ág-információk](./media/code-test/fill_in_build_info.PNG)

    c. Válasszon egy sablont. Mivel nincs Python-projekt sablonja, kezdje az **üres folyamat**kiválasztásával. 

    ![Sablonok listája és "üres folyamat" gomb](./media/code-test/start_empty_process_template.PNG)

    d. Nevezze el a build, és válassza ki az ügynököt. Itt választhatja ki az alapértelmezett értéket, ha DSVM kíván használni a létrehozási folyamat befejezéséhez. Az ügynökök beállításával kapcsolatos további információkért lásd: [létrehozási és kiadási ügynökök](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=vsts).
    
    ![Létrehozás és ügynök kiválasztása](./media/code-test/select_agent.PNG)

    e. Válassza ki **+** a bal oldali ablaktáblán, hogy felvegyen egy feladatot ehhez a Build fázishoz. Mivel a Python-szkript **test1.py** az összes ellenőrzés végrehajtásához futtatjuk, ez a feladat egy PowerShell-parancsot használ a Python-kód futtatásához.
    
    !["Feladatok hozzáadása" ablaktábla a PowerShell-lel kijelölve](./media/code-test/add_task_powershell.PNG)

    f. A PowerShell részletei között adja meg a szükséges információkat, például a nevét és a PowerShell-verzió. A típusként válassza a **beágyazott parancsfájl** lehetőséget. 
    
    A **beágyazott parancsfájl**alatt található mezőbe beírhatja a **Python-test1.py**. Győződjön meg arról, hogy a környezeti változó helyesen van beállítva a Pythonhoz. Ha a Python más verziójára vagy kernelére van szüksége, explicit módon megadhatja az elérési utat az ábrán látható módon: 
    
    ![PowerShell-részletek](./media/code-test/powershell_scripts.PNG)

    g. Válassza a **mentés & üzenetsor** lehetőséget a Build folyamatának befejezéséhez.

    !["& Üzenetsor mentése" gomb](./media/code-test/save_and_queue_build_definition.PNG)

Most már minden alkalommal, amikor egy új véglegesítés át lett helyezve a kódtárat, a létrehozási folyamat automatikusan elindul. (Itt a főkiszolgálót használjuk adattárként, de bármilyen ágat megadhat.) A folyamat futtatja a **test1.py** fájlt az ügynök számítógépén, hogy ellenőrizze, hogy a kódban megadott összes érték megfelelően fut-e. 

Riasztások megfelelően legyenek beállítva, ha Ön fog értesítést küldünk e-mailben a build elkészült. Az Azure DevOps build állapotát is ellenőrizheti. Ha nem sikerül, ellenőrizze a részleteket a build, és ismerje meg, hogy megszakad a kódrészlet meghatározásával.

![E-mail értesítés a build success](./media/code-test/email_build_succeed.PNG)

![Az Azure DevOps értesítés a build success](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Következő lépések
* Az adatelemzési forgatókönyvek esetében lásd: az [UCI bevétel-előrejelző tárháza](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) , amely konkrét példákat tartalmaz az egységek tesztelésére.
* Kövesse az előző szerkezeti és példákat a saját adatelemzési projektek UCI bevétel előrejelzése forgatókönyvhöz.

## <a name="references"></a>Referencia
* [Csoportos adatelemzési folyamat](https://aka.ms/tdsp)
* [Visual Studio-tesztelési eszközök](https://www.visualstudio.com/vs/features/testing-tools/)
* [Azure DevOps-tesztelési erőforrások](https://www.visualstudio.com/team-services/)
* [Adatelemzési Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
