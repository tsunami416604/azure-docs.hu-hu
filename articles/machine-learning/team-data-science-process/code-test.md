---
title: Adatok tudományos kódot a UCI felnőtt bevétel előrejelzése adatkészlet – csoportos adatelemzési folyamat és a Visual Studio Team Services az Azure-beli tesztelése
description: Data science kód UCI felnőtt jövedelem előrejelzési adatokkal tesztelés
services: machine-learning, team-data-science-process
documentationcenter: ''
author: weig
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2018
ms.author: weig
ms.openlocfilehash: 7d9d63d6c3d5c8ccf1777a46832457670d307d4a
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970859"
---
# <a name="data-science-code-testing-with-the-uci-adult-income-prediction-dataset"></a>Adatok adatelemzési kódot a UCI felnőtt bevétel előrejelzése adatkészlettel tesztelése
Ez a cikk tartalmaz útmutatást előzetes kód tesztelése egy adatelemzési munkafolyamathoz. Az ilyen tesztelést adatszakértők egy rendszeres és hatékony módszert kínál ellenőrizze a minőség és a kódját a várt eredményt kapja. A csoportos adatelemzési folyamat (TDSP) használjuk [a UCI felnőtt jövedelem adatkészletet használó projekt](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) azt korábban közzétett megjelenítéséhez a kódot tesztelés menetét. 

## <a name="introduction-on-code-testing"></a>Bevezetés a kódot tesztelés
"Egységtesztelés" egy hosszú időre visszanyúló eljárás szoftverfejlesztői. De adatelemzés, gyakran nem egyértelmű milyen, amely azt jelenti, és hogyan kell tesztelni kódot az adatelemzési életciklus különböző szakaszaiban például:

* Adatok előkészítése
* Adatok minőségi vizsgálata
* Modellezés
* Modell-üzembehelyezés 

Ez a cikk váltja fel az előfizetési időszak "egység tesztelése" a "kód tesztelése." Hivatkozik tesztelésre, az a Funkciók, amelyek segítségével felmérheti, ha egy adattudományi életciklus egyes lépéseihez szükséges kódot az olyan eredmények ", várt." A személy, aki írja a teszt határozza meg, mi az "megfelelően működik," attól függően, a függvény eredménye például, minőségi ellenőrzése vagy modellezési.

Ez a cikk hivatkozásokat hasznos forrásokat biztosít.

## <a name="visual-studio-team-services-for-the-testing-framework"></a>A Visual Studio Team Services esetében a tesztelési keretrendszer
Ez a cikk ismerteti, hogyan hajtsa végre és tesztelése a Visual Studio Team Services (VSTS) használatával automatizálható. Dönthet, hogy más eszközökkel. Azt is bemutatják, hogyan egy automatikus build beállítása a VSTS használatával, és hozhat létre ügynököket. A build-ügynökök használ az Azure Data Science virtuális gépek (Dsvm).

## <a name="flow-of-code-testing"></a>A kód a tesztelési folyamat
Az adatelemzési projektjéhez tesztelési szabályzat a teljes munkafolyamat így néz ki: 

![A kód tesztelési folyamatábra](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Részletes lépések

A következő lépések segítségével állítsa be, és futtassa a kódot tesztelés és a egy automatizált összeállítási fordító-ügynökhöz és a VSTS használatával:

1. Hozzon létre egy projektet a Visual Studio asztali alkalmazásban:

    ![A Visual Studio "Új projekt létrehozása" képernyő](./media/code-test/create_project.PNG)

   Miután létrehozta a projektet, megtalálhatja azt a Megoldáskezelőben a jobb oldali ablaktáblán:
    
    ![A projekt létrehozásának lépései](./media/code-test/create_python_project_in_vs.PNG)

    ![Megoldáskezelő](./media/code-test/solution_explorer_in_vs.PNG)

3. Hírcsatorna projektkódját a VSTS projekt kód tárházba: 

    ![Projekt kódtár](./media/code-test/create_repo.PNG)

4. Tegyük fel, hogy néhány előkészítő munka, például adatbetöltést, funkciófejlesztési és címke oszlopok létrehozásához végezze el. Ellenőrizze, hogy a kód létrehozza az eredményeket várhatóan szeretné. Íme néhány kódot, amely annak megállapítására, hogy megfelelően működik az adatfeldolgozási kódot használhatja:

    * Ellenőrizze, hogy megfelelő oszlopainak nevét:
    
      ![Az oszlopnevek egyező kód](./media/code-test/check_column_names.PNG)

    * Ellenőrizze, hogy megfelelőek-e a válasz szintek:

      ![Kód az egyező szintek](./media/code-test/check_response_levels.PNG)

    * Ellenőrizze, hogy a válasz százalékos ésszerű:

      ![Kód választ százalékának](./media/code-test/check_response_percentage.PNG)

    * A hiányzó sebessége az egyes oszlopok az adatok ellenőrzése:
    
      ![A hiányzó ráta kód](./media/code-test/check_missing_rate.PNG)


5. Miután hajtotta végre az adatfeldolgozás és a szolgáltatás mérnöki munkát, és a megfelelő modellek, hogy betanított, győződjön meg arról, hogy az Ön betanított modell is pontszámot rendelni az új adatkészletek megfelelően. A következő két tesztek segítségével ellenőrizze az előrejelzési szintek és a felirat értékek eloszlása:

    * Ellenőrizze az előrejelzési szintek:
    
      ![Kód ellenőrzése előrejelzési szintek](./media/code-test/check_prediction_levels.PNG)

    * Ellenőrizze az előrejelzési értékek eloszlása:

      ![Előrejelzési értékek ellenőrzésére szolgáló kód](./media/code-test/check_prediction_values.PNG)

6. A vizsgálati függvények együttesen nevű Python-szkriptet, PUT **test_funcs.py**:

    ![Python-szkriptet a tesztelési funkciók](./media/code-test/create_file_test_func.PNG)


7. Után készen áll a teszt kódokat, beállíthatja a tesztelési környezetet a Visual Studióban.

   Hozzon létre egy Python-fájlt nevű **test1.py**. Ezt a fájlt hozzon létre egy osztályt, amely tartalmazza az összes tesztet szeretne tenni. Az alábbi példa bemutatja az előkészített hat tesztek:
    
    ![Python-fájlt osztályban található tesztek listája](./media/code-test/create_file_test1_class.PNG)

8. Ezek a tesztek automatikusan felderíthető, ha beírja a **codetest.testCase** az osztály neve után. A jobb oldali ablaktáblában nyissa meg a teszt Explorert, és válassza ki **összes futtatása**. Az összes teszt egymás után fog futni, és jelzi, ha a teszt sikeres-e vagy sem.

    ![A tesztek futtatása](./media/code-test/run_tests.PNG)

9. Ellenőrizze a kódban, hogy a projekt tárház Git-parancsok használatával. A legutóbbi munkahelyi röviddel a vsts-ben jelennek meg.

    ![Git-parancsok a kód ellenőrzése](./media/code-test/git_check_in.PNG)

    ![Legutóbbi munkahelyi a vsts-ben](./media/code-test/git_check_in_most_recent_work.PNG)

10. Állítsa be az automatikus hozhat létre, és tesztelje a vsts-ben:

    a. Válassza ki a projektadattárat **készítése és kiadása**, majd válassza ki **+ új** új buildelési folyamat létrehozásához.

       ![Új buildelési folyamat indítása szolgáló kiválasztások](./media/code-test/create_new_build.PNG)

    b. Kövesse az utasításokat, jelölje be a forráshely kódja, a projekt nevét, a tárház és a fiókadatokat.
    
       ![Forrás, a nevet, a tárházat és a ág adatokat](./media/code-test/fill_in_build_info.PNG)

    c. Válasszon egy sablont. Mivel az nem érhető el Python projekt sablon, először jelöljön ki **üres folyamat**. 

       ![Sablonok és a "Üres folyamat" gomb](./media/code-test/start_empty_process_template.PNG)

    d. Nevezze el a build, és válassza ki az ügynököt. Az alapértelmezett itt is válassza, ha a buildelési folyamat befejezéséhez a dsvm-hez használni kívánt. A beállítás ügynökök kapcsolatos további információkért lásd: [készítése és kiadása ügynökök](https://docs.microsoft.com/vsts/build-release/concepts/agents/agents?view=vsts).
    
       ![Kiválasztott hozhat létre és az ügynök](./media/code-test/select_agent.PNG)

    e. Válassza ki **+** feladat hozzáadása a build ebben a szakaszban a bal oldali panelen. Mivel a Python-szkript futtatásához fogunk **test1.py** összes ellenőrzés befejeződik, ezt a feladatot használja egy PowerShell-parancsot a Python-kód futtatása.
    
       !["Tevékenységek hozzáadása" panelen a kiválasztott PowerShell-lel](./media/code-test/add_task_powershell.PNG)

    f. A PowerShell részletei között adja meg a szükséges információkat, például a nevét és a PowerShell-verzió. Válasszon **beágyazott parancsfájlja** típusaként. 
    
       Alatti mezőbe **beágyazott parancsfájlja**, beírhatja **python test1.py**. Győződjön meg arról, hogy a környezeti változó megfelelően van beállítva a Pythonhoz készült. Ha egy másik verzió vagy a Python kernel van szüksége, explicit módon adhatja meg az elérési utat az ábrán látható módon: 
    
       ![PowerShell-részletek](./media/code-test/powershell_scripts.PNG)

    g. Válassza ki **várólistára & mentése** a build definíció folyamat befejezéséhez.

       !["A Mentés és a várólista" gomb](./media/code-test/save_and_queue_build_definition.PNG)

Most már minden alkalommal, amikor egy új véglegesítés át lett helyezve a kódtárat, a létrehozási folyamat automatikusan elindul. (Jelen példában használjuk fő mint a tárház, de megadhat bármilyen ág.) A folyamat a **test1.py** az ügynökgépen, győződjön meg arról, hogy minden definiálva a kód megfelelően fut, a fájlt. 

Riasztások megfelelően legyenek beállítva, ha Ön fog értesítést küldünk e-mailben a build elkészült. A build állapota a vsts-ben is ellenőrizheti. Ha nem sikerül, ellenőrizze a részleteket a build, és ismerje meg, hogy megszakad a kódrészlet meghatározásával.

![E-mail értesítés a build success](./media/code-test/email_build_succeed.PNG)

![VSTS-értesítés a build success](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>További lépések
* Tekintse meg a [UCI bevétel előrejelzése tárház](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) konkrét példákat egységteszteket adatelemzési célokra.
* Kövesse az előző szerkezeti és példákat a saját adatelemzési projektek UCI bevétel előrejelzése forgatókönyvhöz.

## <a name="references"></a>Referencia
* [Csoportos adatelemzési folyamat](https://aka.ms/tdsp)
* [Visual Studio-tesztelési eszközök](https://www.visualstudio.com/vs/features/testing-tools/)
* [VSTS-tesztelés erőforrások](https://www.visualstudio.com/team-services/)
* [Adatelemző virtuális gépek](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)