---
title: Projekt akusztikai útmutatója egységgel
titlesuffix: Azure Cognitive Services
description: A mintaadatok használatával kísérletezzen a Project akusztikai tervezési vezérlőkkel az egységben, és telepítse a Windows asztalra.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fabdd221ef99414eae0156babbd76dedb1f0745d
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243025"
---
# <a name="project-acoustics-unity-quickstart"></a>Projekt akusztikai egység – rövid útmutató
A Project akusztikai minta tartalmának használata az egységhez a szimulált kialakítási vezérlőkkel való kísérletezéshez.

A szoftverre vonatkozó követelmények:
* [Unity 2018.2 +](https://unity3d.com) Windowshoz
* [Projekt akusztikai minta Content Package](https://www.microsoft.com/download/details.aspx?id=57346)

Mit tartalmaz a minta csomag?
* Egységbeli jelenet geometria, hangforrások és gameplay-vezérlőkkel
* Projekt akusztikai beépülő modulja
* A jelenethez készült sült akusztikai eszközök

## <a name="import-the-sample-package"></a>A mintavételi csomag importálása
Importálja a mintavételi csomagot egy új Unity-projektbe.
1. Az egység területen lépjen az **eszközök** > **Importálás csomag** > **Egyéni csomag**elemre.

    ![Az egység importálása csomag beállításai](media/import-package.png)  

1. Válassza a **ProjectAcoustics. unitypackage**elemet.

1. Válassza az **Importálás** gombot az egység csomag projektbe való integrálásához.  
  
    ![Az egység importálása csomag párbeszédpanel](media/import-dialog.png)  

Ha a csomagot egy meglévő projektbe szeretné importálni, tekintse meg a további lépések és megjegyzések [Unity Integration](unity-integration.md) című témakört.

>[!NOTE]
>Az importálás befejezése után több hibaüzenet fog megjelenni a konzol naplójában. Folytassa a következő lépéssel, és indítsa újra az egységet.

## <a name="restart-unity"></a>Egység újraindítása
A akusztikai eszközkészlet sütni részében a .NET 4 szükséges. *x* parancsfájl-futtatókörnyezet verziója. A csomag importálása frissíti az egység lejátszójának beállításait. A beállítás érvénybe léptetéséhez indítsa újra az egységet. A beállítás érvénybe léptetésének ellenőrzéséhez nyissa meg a **lejátszó** beállításait:

![Az Unity Project Settings menü](media/player-settings.png)  

![A Unity Player Settings panel és a .NET 4. x kiválasztva](media/net45.png)  

>[!NOTE]
>Ez a képernyőkép a 2018-es egységből készült. *x*. A rendszerkép eltérő lehet az Unity újabb verzióiban.

## <a name="open-the-project-acoustics-bake-window"></a>A Project akusztikai sütni ablak megnyitása
Az egység területen válassza az **akusztika** elemet az **ablak** menüben.

![Az egység-szerkesztő az ablak menüjében Kiemelt akusztikai lehetőséggel](media/window-acoustics.png)

Ekkor megnyílik egy lebegő **akusztikai** ablak. Ebben az ablakban állíthatja be az akusztikus szimuláció tulajdonságait.

![Megnyitotta az akusztikai ablakban lévő Unity-szerkesztőt](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>Kísérletezés a tervezési vezérlőkkel
Nyissa meg a minta jelenetet a *ProjectAcousticsSample* mappában, és kattintson a Play (lejátszás) gombra az Unity Editorban. A W, A, S és D billentyűk és az egér használatával mozoghat. Ha össze szeretné hasonlítani, hogy a jelenet akusztika nélkül szólaljon meg, válassza az R-kulcsot, amíg az átfedésben lévő szöveg pirosra vált, és az "Akusztika: letiltva" felirat jelenik meg. A további vezérlőkhöz tartozó billentyűparancsok megjelenítéséhez válassza az F1 lehetőséget. Azt is megteheti, hogy a jobb gombbal rákattint egy művelet kiválasztására, majd a művelet elvégzéséhez kattintson rá.

A *AcousticsAdjust* parancsfájl a minta jelenet hangforrásaihoz van csatolva. Lehetővé teszi a forrásként szolgáló tervezési paramétereket.

![Az Unity AcousticsAdjust-szkript](media/acoustics-adjust.png)

Az alábbi részekben néhány olyan effektust ismertetünk, amelyeket a rendelkezésre álló vezérlők használatával lehet létrehozni. Az egyes vezérlőkkel kapcsolatos részletes információkért tekintse meg a [projekt akusztikai egységének kialakítását ismertető oktatóanyagot](unity-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Távolságon alapuló csillapítás módosítása
A **Project akusztikai** egység spatializer beépülő moduljának hangvezérelt digitális jelfeldolgozása az egység-szerkesztőbe beépített, forrásként szolgáló távolságon alapuló csillapítást veszi figyelembe. A távolságon alapuló csillapításhoz tartozó vezérlők az **audio forrás** összetevőben találhatók, amely a **3D hangbeállításokban**a hangforrások **felügyelő** paneljén található:

![Az Unity Distance csillapítási beállítások panel](media/distance-attenuation.png)

A Project akusztikai számítások egy "szimulációs régió" mezőben, amely a lejátszó helyét középpontba kerül. A mintavételi csomagban található akusztikai eszközök egy 45 méteres méretű szimulációs régióba kerültek a lejátszó körül. Így a hangcsillapítást úgy kell kialakítani, hogy az 45 m körüli érték 0 legyen.

### <a name="modify-occlusion-and-transmission"></a>Elzáródás és átvitel módosítása
* Ha az **elzáródási** szorzó nagyobb, mint 1 (az alapértelmezett érték 1), a elzáródás eltúlzott. Annak érdekében, hogy az elzáródás hatása finomabb legyen, állítsa azt 1-nél kisebbre.

* A falon belüli átvitel engedélyezéséhez helyezze át az **átviteli (db)** csúszkát a legalacsonyabb beállítással.

### <a name="modify-wetness-for-a-source"></a>A nedvesség módosítása a forrásnál
* Ha módosítani szeretné, hogy a nedvesség milyen gyorsan változik a távolsággal, használja az **észlelési távolságot**. A szimuláción keresztül a Project Acoustics a nedves szintet is kiszámítja, ami érzékelhető távolsági jelzéseket biztosít, és a távolságtól zökkenőmentesen változhat. A távolsági lánc növelésével a távolsághoz kapcsolódó nedves szintek egyre nagyobb mértékben eltúlozzák ezt a hatást. 1-nél kisebb értékek hajlításával a távolságon alapuló visszaverődés-változás finomabb.

   A hatás finomabb finomításához módosítsa a **nedvesség (adatbázis)** beállítást.

* A teljes terület romlási idejének növeléséhez állítsa be a **romlási idő skáláját**. Ha egy adott forrás-figyelő elhelyezési pár esetében a szimulációs eredmény 1,5 másodperces bomlási idő, a **romlási idő mérete** pedig 2, a forrásra alkalmazott bomlási idő 3 másodperc.

## <a name="next-steps"></a>Következő lépések
* Az [Unity-alapú Project akusztikai tervezési vezérlők](unity-workflow.md)részleteinek olvasása.
* Ismerje meg a [tervezési folyamat](design-process.md)hátterében rejlő fogalmakat.
* [Hozzon létre egy Azure-fiókot](create-azure-account.md) az előzetesen sütni és a sütni folyamatok megismeréséhez.
