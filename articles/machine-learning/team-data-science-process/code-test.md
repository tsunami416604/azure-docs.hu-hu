---
title: Az Azure-on tesztelték a UCI felnőtt bevétel előrejelzés adatkészlet - csapat tudományos folyamata és a Visual Studio Team Services adatok tudományos kódot
description: Adatok tudományos kód UCI felnőtt bevétel előrejelzési adatokat tartalmazó tesztelése
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
ms.openlocfilehash: de1ed0b85957413a254503fc72375866dfd1bea1
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837157"
---
# <a name="data-science-code-testing-with-the-uci-adult-income-prediction-dataset"></a>Adatok tudományos kód a UCI felnőtt bevétel előrejelzés adatkészletben tesztelése
Ez a cikk kód tesztelése egy adatelemezési munkafolyamatot tartalmaz előzetes útmutatást. Ilyen tesztelés adatszakértőkön egy rendszeres és hatékony módot biztosít ellenőrizze a minőségi és a várt eredményt kapja, a kód. Egy csoport adatok tudományos folyamat (TDSP) használjuk [UCI felnőtt bevétel adatkészlet használó projekt](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) azt korábban közzétett jeleníthető meg, hogyan végezhető a kód tesztelése. 

## <a name="introduction-on-code-testing"></a>Bevezetés a kód tesztelése
"Tesztelés" mértékegysége szoftverfejlesztői egy régóta használható. De a adattudomány, gyakran nem egyértelmű milyen, amely azt jelenti, és hogyan kell tesztelni kód egy adatok tudományos életciklus különböző szakaszaiban a, mint:

* Adatok előkészítése
* Adatok minőségi vizsgálata
* Modellezés
* Telepítési modell 

Ez a cikk lecseréli a kifejezés "egység tesztelés" a "tesztelés kód". A Funkciók, amelyek segítenek annak ellenőrzéséhez, ha egy bizonyos lépés egy adatok tudományos életciklus kódját van olyan eredmények "elvárt." vizsgálatánál hivatkozik A személy, aki ír a vizsgálat határozza meg, hogy mi "vártnak" attól függően, hogy a függvény eredménye például, az adatok minőségi ellenőrzése vagy modellezési.

Ez a cikk hasznos források hivatkozásokat biztosít.

## <a name="visual-studio-team-services-for-the-testing-framework"></a>A tesztelési keretrendszer Visual Studio Team Services
Ez a cikk ismerteti, hogyan végrehajtása és a Visual Studio Team Services (VSTS) használatával tesztelési automatizálása. Előfordulhat, hogy alternatív eszközök használata mellett dönt. Azt is bemutatják, hogyan állítsa be az automatikus létrehozási VSTS használatával, és ügynököket építése. Build ügynököket, az Azure Data tudományos virtuális gépek (DSVMs) használjuk.

## <a name="flow-of-code-testing"></a>Kód vizsgálatának folyamata
A tesztelési kód tudományos adatok projektben általános munkafolyamat így néz ki: 

![Folyamatábra kód tesztelése](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Részletes lépések

Az alábbi lépések segítségével beállítása és futtatása a kód tesztelése és az automatizált build a build ügynök és a VSTS használatával:

1. Hozzon létre egy projektet a Visual Studio asztali alkalmazások:

    ![A Visual Studio "Új projekt létrehozása" képernyő](./media/code-test/create_project.PNG)

   A projekt létrehozása után találhatja meg a Megoldáskezelőben a jobb oldali ablaktáblában:
    
    ![A projekt létrehozásának lépései](./media/code-test/create_python_project_in_vs.PNG)

    ![Megoldáskezelő](./media/code-test/solution_explorer_in_vs.PNG)

3. A projekt kódját hírcsatorna a VSTS projekt kód tárházba: 

    ![Projekt kód tárház](./media/code-test/create_repo.PNG)

4. Tegyük fel, hogy néhány előkészítése munkaelem, például az adatfeldolgozást, a szolgáltatás termékgondozó csoportja és a felirat oszlopok létrehozása ezt. Győződjön meg arról, hogy a kód a várt eredményeket előállító szeretné. Íme néhány kódot, amely annak megállapítására, hogy az adatkezelési kód megfelelően működik-e használhatja:

    * Ellenőrizze, hogy az oszlopnevek jobb:
    
      ![Az oszlopnevek egyező kódot](./media/code-test/check_column_names.PNG)

    * Ellenőrizze, hogy megfelelőek-e a válasz szintek:

      ![A megfelelő szintű kód](./media/code-test/check_response_levels.PNG)

    * Ellenőrizze, hogy ésszerű válasz százalékos aránya:

      ![Válasz százalékos kódja](./media/code-test/check_response_percentage.PNG)

    * Ellenőrizze az adatok minden egyes oszlopának hiányzó aránya:
    
      ![Hiányzó arány kódja](./media/code-test/check_missing_rate.PNG)


5. Miután megtette, az adatfeldolgozás és -szolgáltatás mérnöki munka, és meg már jó modell betanítása, győződjön meg arról, hogy a modell betanítása akkor is helyes pontozása új adatkészletek. A következő két tesztek segítségével ellenőrizze a előrejelzés szintek és a terjesztési címke értékek:

    * Ellenőrizze az előrejelzés szintek:
    
      ![Előrejelzés szintek ellenőrzése a következő kódot](./media/code-test/check_prediction_levels.PNG)

    * Az előrejelzés értékek terjesztését ellenőrzése:

      ![Előrejelzés értékek ellenőrzése a következő kódot](./media/code-test/check_prediction_values.PNG)

6. A vizsgálati funkciók együtt történő nevű Python-parancsfájl PUT **test_funcs.py**:

    ![A teszt funkciók Python-parancsfájl](./media/code-test/create_file_test_func.PNG)


7. Miután a teszt kódok készen áll, a Visual Studio környezet állíthat be.

   Egy nevű Python-fájl létrehozásához **test1.py**. Ezt a fájlt hozzon létre egy osztályt, amely tartalmazza az összes tesztet szeretne. A következő példa bemutatja, előkészített hat tesztek:
    
    ![Az osztály tesztek listáját Python-fájl](./media/code-test/create_file_test1_class.PNG)

8. Ezek a tesztek automatikusan észlelhetők Ha **codetest.testCase** az osztály neve után. Nyissa meg a teszt Explorer a jobb oldali ablaktáblán, majd válassza **minden**. A tesztek egymás után futnak, és jelzi, hogy a teszt sikeres-e vagy sem.

    ![A tesztek futtatása](./media/code-test/run_tests.PNG)

9. Ellenőrizze a kódban a projekt tárházba Git-parancsok használatával. A legutóbbi munkahelyi röviddel a VSTS jelenik meg.

    ![Git-parancsok a kódban ellenőrzése](./media/code-test/git_check_in.PNG)

    ![A VSTS legutóbbi munka](./media/code-test/git_check_in_most_recent_work.PNG)

10. Állítsa be automatikus build, és tesztelje a VSTS:

    a. A projekt tárházban, válassza ki **Build és a kibocsátási**, majd válassza ki **+ új** új build folyamatot létrehozni.

       ![Kiválasztott beállításokat egy új build folyamat elindítása](./media/code-test/create_new_build.PNG)

    b. Kövesse az utasításokat, jelölje be a forráshely kódja, a projekt nevét, a tárház és a fiókadatokat.
    
       ![Forrás, a nevet, a tárház és a fiókiroda adatokat](./media/code-test/fill_in_build_info.PNG)

    c. Válasszon olyan sablont. Mivel nincs Python projektsablon, indítsa el a kiválasztásával **üres folyamat**. 

       ![Sablonok és a "Üres folyamat" gomb](./media/code-test/start_empty_process_template.PNG)

    d. A build nevet, és jelölje ki az ügynököt. Itt az alapértelmezett dönthet úgy, ha azt szeretné, hogy egy DSVM használatára a felépítési folyamat befejezéséhez. A beállítás ügynökök kapcsolatos további információkért lásd: [felépítéséhez és az ügynökök kiadási](https://docs.microsoft.com/en-us/vsts/build-release/concepts/agents/agents?view=vsts).
    
       ![Buildelés és ügynök megerősítése](./media/code-test/select_agent.PNG)

    e. Válassza ki **+** a bal oldali ablaktáblán, adja hozzá egy feladatot a build fázisban. Mivel a Python-parancsfájl futtatása az oktatóanyagban módosítjuk **test1.py** az ellenőrzés befejezéséhez ezt a feladatot használja egy PowerShell-parancsot futtasson Python kódot.
    
       ![A PowerShell kiválasztva "Tevékenységek hozzáadása" ablak](./media/code-test/add_task_powershell.PNG)

    f. A PowerShell részleteit töltse ki a szükséges információkat, például a nevét, és a PowerShell-verziót. Válasszon **beágyazott parancsfájlja** típusként. 
    
       A mezőbe a **beágyazott parancsfájlja**, beírhatja **python test1.py**. Győződjön meg arról, hogy a környezeti változó helyesen van beállítva a Python. Ha egy másik verzió vagy a Python kernel van szüksége, explicit módon az elérési utat is megadhat az ábrán látható módon: 
    
       ![PowerShell-részletek](./media/code-test/powershell_scripts.PNG)

    g. Válassza ki **mentés és a feldolgozási sor** definition folyamat befejezéséhez.

       !["& Várólistára mentése" gombra](./media/code-test/save_and_queue_build_definition.PNG)

Most már minden alkalommal, amikor a kód tárház új véglegesítési kerül, a felépítési folyamat automatikusan elindul. (Itt vesszük fő a tárház, de megadhat bármilyen fiókirodai.) A folyamat fut a **test1.py** győződjön meg arról, hogy minden a kódban definiált megfelelően fut a ügynökszámítógépet fájlban. 

Riasztások megfelelően vannak beállítva, ha kell értesítést e-mailben a build befejezésekor. Azt is ellenőrizze, hogy a VSTS build állapotát. Ha a hiba, tekintse meg a részletes a build, és megtudhatja, mely adat megszakad.

![E-mail értesítések build siker](./media/code-test/email_build_succeed.PNG)

![Build sikeres VSTS bejelentése](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>További lépések
* Tekintse meg a [UCI bevétel előrejelzés tárház](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) egység vizsgálatok adatok tudományos forgatókönyvek konkrét példákat.
* Kövesse az előző Vázlat és példákat a saját adatok tudományos projektek UCI bevétel előrejelzés forgatókönyvet.

## <a name="references"></a>Referencia
* [Csoportos adatelemzési folyamat](https://aka.ms/tdsp)
* [Visual Studio tesztelés eszközök](https://www.visualstudio.com/vs/features/testing-tools/)
* [VSTS tesztelés erőforrások](https://www.visualstudio.com/team-services/)
* [Adatok tudományos virtuális gépek](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)