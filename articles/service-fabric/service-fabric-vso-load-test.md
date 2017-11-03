---
title: "Betöltési az alkalmazás tesztelése a Visual Studio Team Services használatával |} Microsoft Docs"
description: "Megtudhatja, hogyan terhelési tesztelést az Azure Service Fabric-alkalmazások Visual Studio Team Services használatával."
services: service-fabric
documentationcenter: na
author: cawams
manager: timlt
editor: 
ms.assetid: fc743585-0d1b-483f-981d-493f4552ac07
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: e8e270ce865d4da3ee219958b308db2c1c89b11b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="load-test-your-application-by-using-visual-studio-team-services"></a>Betöltési az alkalmazás tesztelése a Visual Studio Team Services használatával
Ez a cikk bemutatja a Microsoft Visual Studio terhelés teszt szolgáltatások használatának terhelési tesztelést kérelmet. Az Azure Service Fabric állapotalapú szolgáltatási háttér és egy állapotmentes szolgáltatások előtér-webkiszolgáló használ. Az itt használt mintaalkalmazás egy repülőgép hely szimulátor. Megadja, hogy egy repülőgép Azonosítót, az indulási idő és a cél. Az alkalmazás háttér feldolgozza a kérelmeket, és az előtér egy térképen jeleníti meg, amely megfelel a feltételeknek a repülőgép.

A következő ábra szemlélteti, hogy tesztelni kell a Service Fabric-alkalmazás.

![A repülőgép hely mintaalkalmazás ábrája][0]

## <a name="prerequisites"></a>Előfeltételek
Az első lépések előtt kell tegye a következőket:

* Hozzon létre egy Visual Studio Team Services-fiókot. Kaphat egy ingyenes [Visual Studio Team Services](https://www.visualstudio.com).
* Szerezze be, és telepítse a Visual Studio 2013 vagy Visual Studio 2015-öt. Ez a cikk a Visual Studio 2015 Enterprise edition használ, de a Visual Studio 2013 és a többi kiadás hasonlóan működnek.
* Átmeneti környezet az alkalmazás telepítése. Lásd: [egy távoli fürtre, a Visual Studio használatával alkalmazások központi telepítése](service-fabric-publish-app-remote-cluster.md) információkat arról.
* Az alkalmazás használati szokások megismerése. Ezt az információt a terhelés mintát szimulálására szolgál.
* Ismerje meg a cél a terhelés teszteléséhez. Ezzel a megoldással értelmezése és elemzése a teszteredmények betöltése.

## <a name="create-and-run-the-web-performance-and-load-test-project"></a>Hozzon létre, és futtassa a webalkalmazás teljesítmény- és terheléstesztet projektet
### <a name="create-a-web-performance-and-load-test-project"></a>Webalkalmazás teljesítmény- és terheléstesztet-projekt létrehozása
1. Nyissa meg a Visual Studio 2015-öt. Válasszon **fájl** > **új** > **projekt** a menüsoron nyissa meg a **új projekt** párbeszédpanel.
2. Bontsa ki a **Visual C#** csomópont válassza **teszt** > **webalkalmazás teljesítmény- és terheléstesztet projekt**. Nevezze el a projektet, és válassza a **OK** gombra.

    ![Képernyőfelvétel az új projekt párbeszédpanel][1]

    Új webalkalmazás teljesítmény- és terheléstesztet projektben a Megoldáskezelőre kell megjelennie.

    ![Képernyőfelvétel a Megoldáskezelőben az új projekt][2]

### <a name="record-a-web-performance-test"></a>Jegyezze fel a webtesztet teljesítmény
1. Nyissa meg a .webtest projektet.
2. Válassza ki a **hozzáadása rögzítése** ikon a böngészőben a rögzítési munkamenet elindításához.

    ![Képernyőfelvétel a böngészőben a adja hozzá a rögzítés ikonja][3]

    ![Képernyőfelvétel a webböngészőben a rekord gombra][4]
3. Tallózással keresse meg a Service Fabric-alkalmazás. A felvétel panelen megjelennek a webes kérelmek.

    ![Képernyőfelvétel a webes kérelmek a felvétel panelen][5]
4. Hajtsa végre, hogy a végrehajtásához a felhasználó egy műveletsorozatot. Ezek a műveletek a terhelés létrehozásához használt mintaként.
5. Amikor elkészült, válassza ki a **leállítása** Rögzítés gombra.

    ![Képernyőfelvétel a Leállítás gombra.][6]

    A Visual Studio .webtest projekt több kérelem kell rögzített. Dinamikus paraméterek automatikusan lecseréli a rendszer. Ezen a ponton a felesleges, ismétlődő függőségi kéréseit, amelyek nem szerepelnek a tesztkörnyezet törlése.
6. Mentse a projektet, és válassza ki a **teszteléséhez futtassa** parancsot futtatja webes helyileg, és győződjön meg arról, hogy minden megfelelően működik-e.

    ![A teszt futtatása parancs képernyőfelvétele][7]

### <a name="parameterize-the-web-performance-test"></a>A webes vizsgálat parametrizálja
A webes vizsgálat is parametrizálja átalakítják kódolt webes teljesítményének teszteléséhez, és majd szerkesztésével a kódot. Alternatív megoldásként a webes vizsgálat kell kötni adatok listáját, hogy a teszt telepítéseket az adatokat. Lásd: [létrehozás és Futtatás kódolt teljesítmény webteszt](https://msdn.microsoft.com/library/ms182552.aspx) a kódolt tesztelési tesztelése módját a webalkalmazás teljesítmény részleteit. Lásd: [adatforrás hozzáadása egy webtesztet teljesítmény](https://msdn.microsoft.com/library/ms243142.aspx) adatok kötődni teljesítmény webteszt információt.

Ehhez a példához nem lesz az átalakítás a webes vizsgálat kódolt tesztelési, cserélje le a repülőgép azonosító egy előállított GUID Azonosítóhoz, és adjon hozzá további kérelmeket járatok küldendő különböző helyeken.

### <a name="create-a-load-test-project"></a>Betöltési teszt projekt létrehozása
Egy teszt a projekt betöltésekor egy vagy több forgatókönyvek szerint a teljesítmény a webteszt és egységteszt további megadott terhelést teszt beállításaival együtt állnak. A következő lépések bemutatják, hogyan terhelés teszt projekt létrehozásához:

1. Válassza ki a helyi menüben a webalkalmazás teljesítmény- és terheléstesztet projekt **Hozzáadás** > **terheléstesztet**. Az a **terhelés tesztelése** varázsló, válassza ki a **következő** gombra a teszt beállítások konfigurálásához.
2. Az a **betöltése mintát** területen válassza ki, hogy egy állandó felhasználói terhelést vagy egy lépés terhelés, ami néhány felhasználó kezdődik, és növeli a felhasználók adott idő alatt.

    Ha a felhasználói terhelést összegének jó becsült, és szeretné látni, a jelenlegi rendszer teljesítményét, válassza a **állandó betöltése**. Ha a cél, hogy ismerje meg, hogy a rendszer hajt végre következetesen különböző terhelés alatt, válassza a **lépés terhelés**.
3. Az a **tesztelése vegyes** területen válassza ki a **Hozzáadás** gombra, majd válassza ki a teszt a terheléstesztet szerepeltetni kívánt. Használhatja a **terjesztési** teljes vizsgálatok futtatása az egyes teszteket százalékos oszlop.
4. Az a **futtatása beállítások** területén adja meg a betöltési vizsgálat időtartama.

   > [!NOTE]
   > A **tesztelése ismétlési** beállítás érhető el csak egy terheléstesztet helyileg a Visual Studio használatával futtatásakor.
   >
   >
5. Az a **hely** szakasza **futtatása beállítások**, adja meg a helyet, ahol teszt terhelésigényét jönnek létre. A varázsló kérheti a Team Services-fiókkal bejelentkezni. Jelentkezzen be, és válassza a földrajzi helyet. Amikor elkészült, válassza ki a **Befejezés** gombra.
6. A terheléstesztet a létrehozása után nyissa meg a .loadtest projektet, és válassza ki a jelenlegi beállítás, például futtassa **futtatása beállítások** > **Settings1 futtatása [aktív]**. Ekkor megnyílik a futtatási beállítások a **tulajdonságok** ablak.
7. Az a **eredmények** szakasza a **futtatása beállítások** tulajdonságai ablakban, a **időzítés részleteit tárolási** beállítást kell **nincs** alapértelmezett értéke. Módosítsa az értéket **minden egyes részletek** további információért a terhelést a vizsgálati eredmények. Lásd: [betöltése tesztelés](https://www.visualstudio.com/load-testing.aspx) további információt a Visual Studio Team Services csatlakoztatása és futtasson egy terheléstesztet.

### <a name="run-the-load-test-by-using-visual-studio-team-services"></a>A betöltési teszt futtatása a Visual Studio Team Services használatával
Válassza ki a **terhelés teszteléséhez futtassa** parancs futtatása a teszt elindításához.

![Kattintás a Futtatás terheléstesztet parancs][8]

## <a name="view-and-analyze-the-load-test-results"></a>Megtekintése és elemzése a teszteredmények betöltése
A terhelés, a vizsgálat történik, a teljesítményadatok grafikon. Meg kell jelennie az alábbi diagramon hasonló.

![Képernyőfelvétel a teljesítmény ábra a teszteredmények betöltése][9]

1. Válassza ki a **töltse le a jelentés** hivatkozás az oldal tetején. Miután a jelentés tölti le, válassza ki azt a **jelentés megtekintéséhez** gombra.

    Az a **Graph** lapon megtekintheti a különböző teljesítményszámlálókkal diagramjait. Az a **összegzés** lapon, a teljes teszt eredményei jelennek meg. A **táblák** lapon átadott és sikertelen terhelés tesztek teljes számát jeleníti meg.
2. Válassza ki a szám hivatkozások a a **teszt** > **sikertelen** és a **hibák** > **száma** oszlopok hiba részleteinek megtekintéséhez.

    A **részletes** lap információkat jeleníti meg virtuális felhasználói és tesztelési forgatókönyvhöz a sikertelen kérelmek. Ezeket az adatokat akkor lehet hasznos, ha a terheléstesztet tartalmaz több forgatókönyv.

Lásd: [elemzése betöltése vizsgálati eredmények a betöltése tesztelése Analyzer diagramjait nézetében](https://www.visualstudio.com/load-testing.aspx) terhelés megjelenítéséről további információ a vizsgálati eredmények.

## <a name="automate-your-load-test"></a>A terheléstesztet automatizálása
A Visual Studio Team Services betöltése teszt terhelés tesztek kezelése és Team Services-fiók eredményeket elemezni API felületeket biztosít. Lásd: [felhő tesztelés betöltése az Rest API-k](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx) további információt.

## <a name="next-steps"></a>Következő lépések
* [Figyelése és diagnosztizálása szolgáltatásai a helyi számítógép fejlesztési beállítása](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png
