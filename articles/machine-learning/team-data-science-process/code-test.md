---
title: Adatelemzési kód tesztelése az Azure DevOps-szolgáltatásokkal – Csapatadat-elemzési folyamat
description: Adatelemzési kód tesztelése az Azure-ban az UCI felnőttjövedelem-előrejelzési adatkészletével a Team Data Science Process és az Azure DevOps-szolgáltatások segítségével
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721994"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Adatelemzési kód tesztelése az Azure-ban a team data science folyamattal és az Azure DevOps-szolgáltatásokkal
Ez a cikk előzetes irányelveket ad egy adatelemzési munkafolyamatban a kód teszteléséhez. Az ilyen tesztelés szisztematikus és hatékony módot biztosít az adatszakértők számára a kódjuk minőségének és várható eredményének ellenőrzésére. Egy Team Data Science Process (TDSP) projektet használunk, amely a korábban közzétett [UCI felnőttjövedelem adatkészletet használja](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) a kódtesztelés elvégzésének bemutatására. 

## <a name="introduction-on-code-testing"></a>Bevezetés a kódtesztelésbe
Az "egységtesztelés" a szoftverfejlesztés régóta fennálló gyakorlata. Az adatelemzés azonban gyakran nem egyértelmű, hogy mit jelent az "egységtesztelés", és hogyan kell tesztelni a kódot az adatelemzési életciklus különböző szakaszaihoz, például:

* Adatok előkészítése
* Adatminőség-vizsgálat
* Modellezés
* Modell üzembe helyezése 

Ez a cikk az "egységtesztelés" kifejezést "kódteszteléssel" helyettesíti. A tesztelésre olyan függvényként hivatkozik, amely segít felmérni, hogy az adatelemzési életciklus egy bizonyos lépésének kódja "a várt módon" eredményez-e eredményeket. A tesztet író személy határozza meg, hogy mi a "várt módon", a függvény eredményétől függően – például adatminőség-ellenőrzés vagy modellezés.

Ez a cikk hasznos forrásként hivatkozásokat tartalmaz.

## <a name="azure-devops-for-the-testing-framework"></a>Azure DevOps a tesztelési keretrendszerhez
Ez a cikk ismerteti, hogyan hajthatja végre és automatizálhatja a tesztelést az Azure DevOps használatával. Dönthet úgy, hogy alternatív eszközöket használ. Azt is bemutatjuk, hogyan állíthat be egy automatikus build et az Azure DevOps használatával, és ügynököket hozhat létre. A buildügynökök esetében az Azure Data Science virtuális gépek (DSVM).

## <a name="flow-of-code-testing"></a>A kódvizsgálat áramlása
Egy adatelemzési projektben a kód tesztelésének általános munkafolyamata így néz ki: 

![A kódtesztelés folyamatábrája](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Részletes lépések

Az alábbi lépésekkel állíthatja be és futtathat kódtesztelést, valamint automatikus buildet egy buildügynök és az Azure DevOps használatával:

1. Projekt létrehozása az asztali Visual Studio alkalmazásban:

    !["Új projekt létrehozása" képernyő a Visual Studióban](./media/code-test/create_project.PNG)

   Miután létrehozta a projektet, a megoldáskezelőben a jobb oldali ablaktáblában találja meg:
    
    ![A projekt létrehozásának lépései](./media/code-test/create_python_project_in_vs.PNG)

    ![Megoldáskezelő](./media/code-test/solution_explorer_in_vs.PNG)

1. A projektkód betáplálása az Azure DevOps projektkódtárba: 

    ![Projektkód-tárház](./media/code-test/create_repo.PNG)

1. Tegyük fel, hogy elvégzett néhány adat-előkészítési munkát, például az adatok betöltését, a szolgáltatástervezést és a címkeoszlopok létrehozását. Győződjön meg arról, hogy a kód a várt eredményeket hozza létre. Íme néhány kód, amelynek segítségével tesztelheti, hogy az adatfeldolgozási kód megfelelően működik-e:

    * Ellenőrizze, hogy az oszlopnevek helyesek-e:
    
      ![Az egyező oszlopnevek kódja](./media/code-test/check_column_names.PNG)

    * Ellenőrizze, hogy a válaszszintek helyesek-e:

      ![Az egyezési szintek kódja](./media/code-test/check_response_levels.PNG)

    * Ellenőrizze, hogy a válasz százaléka ésszerű-e:

      ![A válasz százalékának kódja](./media/code-test/check_response_percentage.PNG)

    * Ellenőrizze az adatok egyes oszlopainak hiányzó sebességét:
    
      ![Hiányzó árfolyam kódja](./media/code-test/check_missing_rate.PNG)


1. Miután elvégezte az adatfeldolgozási és szolgáltatásmérnöki munkát, és betanított egy jó modellt, győződjön meg arról, hogy a betanított modell képes-e megfelelően új adatkészleteket szerezni. A következő két teszt segítségével ellenőrizheti az előrejelzési szinteket és a címkeértékek eloszlását:

    * Ellenőrizze az előrejelzési szinteket:
    
      ![Az előrejelzési szintek ellenőrzésére szolgáló kód](./media/code-test/check_prediction_levels.PNG)

    * Ellenőrizze az előrejelzési értékek eloszlását:

      ![Az előrejelzési értékek ellenőrzésére szolgáló kód](./media/code-test/check_prediction_values.PNG)

1. Az összes tesztfüggvény tegyünk össze egy **test_funcs.py**nevű Python-parancsfájlba:

    ![Python-parancsfájl tesztfüggvényekhez](./media/code-test/create_file_test_func.PNG)


1. A tesztkódok elkészítése után beállíthatja a tesztelési környezetet a Visual Studióban.

   Hozzon létre egy **test1.py**nevű Python-fájlt. Ebben a fájlban hozzon létre egy osztályt, amely tartalmazza az összes kívánt tesztet. A következő példa hat elvégzett vizsgálatot mutat be:
    
    ![Python-fájl egy osztály teszteinek listájával](./media/code-test/create_file_test1_class.PNG)

1. Ezek a tesztek automatikusan felderíthetők, ha **a codetest.testCase-t** az osztályneve után helyezi el. Nyissa meg a Test Explorer t a jobb oldali ablaktáblában, és válassza **az Összes futtatása lehetőséget.** Minden teszt egymás után fog futni, és megmondja, hogy a teszt sikeres-e vagy sem.

    ![A tesztek futtatása](./media/code-test/run_tests.PNG)

1. Adja be a kódot a projekttárházba a Git-parancsok használatával. A legutóbbi munkája hamarosan megjelenik az Azure DevOps-ban.

    ![Git-parancsok a kód ellenőrzéséhez](./media/code-test/git_check_in.PNG)

    ![Legutóbbi munka az Azure DevOps-ban](./media/code-test/git_check_in_most_recent_work.PNG)

1. Automatikus létrehozás és tesztelés beállítása az Azure DevOps-ban:

    a. A projekttárban válassza a **Build and Release**lehetőséget, majd az **+Új** lehetőséget az új létrehozási folyamat létrehozásához.

    ![Új összeállítási folyamat indításának kiválasztása](./media/code-test/create_new_build.PNG)

    b. A forráskód helyének, a projekt nevének, a tárháznak és az elágazási adatoknak a kiválasztásához kövesse a utasításokat.
    
    ![Forrás-, név-, adattár- és fiókadatok](./media/code-test/fill_in_build_info.PNG)

    c. Jelöljön ki egy sablont. Mivel nincs Python-projektsablon, először válassza az **Üres folyamat**lehetőséget. 

    ![Sablonok listája és "Folyamat kiürítése" gomb](./media/code-test/start_empty_process_template.PNG)

    d. Nevezze el a buildet, és válassza ki az ügynököt. Itt választhatja ki az alapértelmezett et, ha DSVM-et szeretne használni a létrehozási folyamat befejezéséhez. Az ügynökök beállításáról további információt az Ügynökök létrehozása és kiadása című [témakörben talál.](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=vsts)
    
    ![Build és ügynökválasztás](./media/code-test/select_agent.PNG)

    e. A **+** bal oldali ablaktáblában válassza ki a feladatot ehhez a létrehozási fázishoz. Mivel a Python-parancsfájlt az összes ellenőrzés elvégzéséhez **test1.py** futtatjuk, ez a feladat egy PowerShell-parancs használatával futtatja a Python-kódot.
    
    !["Feladatok hozzáadása" ablaktábla kijelölt PowerShell-lel](./media/code-test/add_task_powershell.PNG)

    f. A PowerShell-adatok, töltse ki a szükséges adatokat, például a nevét és verzióját a PowerShell. Típusként válassza a **Szövegközi parancsfájl lehetőséget.** 
    
    A **Szövegközi parancsfájl**mezőbe írja be a **python test1.py.** Győződjön meg arról, hogy a környezeti változó megfelelően van beállítva a Pythonhoz. Ha a Python egy másik verziójára vagy rendszermagjára van szüksége, explicit módon megadhatja az elérési utat az ábrán látható módon: 
    
    ![PowerShell-részletek](./media/code-test/powershell_scripts.PNG)

    g. A létrehozási folyamat befejezéséhez válassza **& várólista mentése** lehetőséget.

    !["& várólista mentése" gomb](./media/code-test/save_and_queue_build_definition.PNG)

Mostantól minden alkalommal, amikor egy új véglegesítés tolt a kódtárházba, a buildfolyamat automatikusan elindul. (Itt a mestert használjuk adattárként, de bármely ágat meghatározhat.) A folyamat futtatja a **test1.py** fájlt az ügynök gép, hogy megbizonyosodjon arról, hogy a kódban meghatározott minden megfelelően fut. 

Ha a riasztások megfelelően vannak beállítva, e-mailben értesítést kap, amikor a build befejeződött. A buildállapotát az Azure DevOps-ban is ellenőrizheti. Ha ez nem sikerül, akkor ellenőrizze a részleteket a build, és megtudja, melyik darab van törve.

![E-mail értesítés a build sikeréről](./media/code-test/email_build_succeed.PNG)

![Az Azure DevOps értesítése a build sikeréről](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>További lépések
* Tekintse meg az [UCI jövedelem-előrejelzési tárház](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) konkrét példákat az adatok tudományos forgatókönyvek egységtesztek.
* Kövesse az előző vázlatot és példákat az UCI-jövedelem-előrejelzési forgatókönyv a saját adatelemzési projektek.

## <a name="references"></a>Referencia
* [Csoportos adatelemzési folyamat](https://aka.ms/tdsp)
* [Visual Studio tesztelési eszközök](https://www.visualstudio.com/vs/features/testing-tools/)
* [Azure DevOps tesztelési erőforrások](https://www.visualstudio.com/team-services/)
* [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
