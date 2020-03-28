---
title: 'Oktatóanyag: Új HoloLens Unity alkalmazás létrehozása'
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre új HoloLens Unity alkalmazást az Azure Spatial Anchors használatával.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 07/05/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e1abb759c80e770f1e650c232b6b2e21232b7e6f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75457722"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Oktatóanyag: Lépésenkénti útmutató egy új HoloLens Unity alkalmazás létrehozásához az Azure Spatial Anchors használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre új HoloLens Unity alkalmazást az Azure Spatial Anchors alkalmazással.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel:

1. Olyan Windows-gép, amelyen a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017+</a> telepítve van az **univerzális Windows-platform fejlesztési** munkaterhelésével és a Windows **10 SDK (10.0.18362.0 vagy újabb)** összetevővel, valamint <a href="https://git-scm.com/download/win" target="_blank">a Git for Windows</a>rendszerrel.
2. A Visual Studio [C++/WinRT Visual Studio extension (VSIX) bővítményét](https://aka.ms/cppwinrt/vsix) a [Visual Studio piactérről](https://marketplace.visualstudio.com/)kell telepíteni.
3. HoloLens eszköz, amelynek [fejlesztői módja](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) engedélyezve van. Ez a cikk egy HoloLens-eszközt igényel a [Windows 10 október 2018 frissítéssel](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (más néven RS5). A HoloLens legújabb kiadására való frissítéshez nyissa meg a **Beállítások** alkalmazást, nyissa meg **a & biztonság frissítése**lehetőséget, majd válassza a Frissítések **keresése** gombot.

## <a name="getting-started"></a>Első lépések

Először megalkotjuk a projektünket és a Unity jelenetet:
1. Indítsa el az Egységet.
2. Válassza az **Új**lehetőséget.
4. Győződjön meg arról, hogy a **3D** ki van jelölve.
5. Nevezze el a projektet, és adjon meg egy **mentési helyet.**
6. Kattintson **a Projekt létrehozása gombra.**
7. Mentse az üres alapértelmezett jelenetet egy új fájlba a következő vel: **Fájlmentés** > **másként**.
8. Nevezze el az új jelenetet **Fő,** és nyomja meg a **Mentés** gombot.

**A projektbeállítások beállítása**

Most beállítjuk a Unity projekt beállításait, amelyek segítenek a Windows Holographic SDK fejlesztésében.

Először is, lehetővé teszi, hogy minőségi beállításokat a mi alkalmazás.
1. Projektbeállítások > **Project Settings** > **minőségének** **szerkesztése**
2. A **Windows Áruház** emblémája alatti oszlopban kattintson az **Alapértelmezett** sor nyilára, és válassza a Nagyon **alacsony lehetőséget.** A beállítás akkor lesz megfelelő, ha a **Windows Áruház** oszlopában és a **Nagyon alacsony** sorban lévő mező zöld.

Tudatnunk kell unity, hogy az alkalmazás próbálunk exportálni létre egy magával ragadó nézet helyett a 2D-s nézet. Magával ragadó képet hozunk létre azáltal, hogy engedélyezzük a Virtuális valóság támogatását a Windows 10 SDK-t célzó Unity-ben.

1. Nyissa meg a**Projektbeállítások** > **lejátszó szerkesztése** **Edit** > lehetőséget.
2. A **Lejátszó beállításaihoz** **a Felügyelő panelen** válassza a **Windows Áruház** ikonját.
3. Bontsa ki az **XR beállítások** csoportot.
4. A **Renderelés** szakaszban jelölje be a **Virtuális valóság támogatott** jelölőnégyzetet egy új virtuális valóság **SDK-lista** hozzáadásához.
5. Ellenőrizze, hogy a **Windows Mixed Reality** megjelenik-e a listában. Ha nem, **+** jelölje ki a lista alján lévő gombot, és válassza a **Windows Mixed Reality (Windows Mixed Reality**) lehetőséget.

> [!NOTE]
> Ha nem látható a Windows Áruház ikonja, ellenőrizze, hogy a telepítés előtt kiválasztotta-e a Windows Áruház .NET parancsfájlalapú háttérrendszert. Ha nem, akkor lehet, hogy újra kell telepítenie az Egységet a megfelelő Windows-telepítéssel.

**Parancsfájlok parancsfájlok konfigurációjának ellenőrzése**
1. Nyissa meg a**Projektbeállítások** > **lejátszó** **szerkesztése** > lapot (előfordulhat, hogy a **Lejátszó** még mindig nyitva van az előző lépésből).
2. A **Lejátszó beállításaihoz** **a Felügyelő panelen** válassza a **Windows Áruház** ikonját.
3. Az **Egyéb beállítások konfigurációja** csoportban győződjön meg arról, hogy a **parancsfájlok lejátszási szolgáltatása** **IL2CPP**lesz.

**Képességek beállítása**
1. Nyissa meg a**Projektbeállítások** > **lejátszó** **szerkesztése** > lapot (előfordulhat, hogy a **Lejátszó** még mindig nyitva van az előző lépésből).
2. A **Lejátszó beállításaihoz** **a Felügyelő panelen** válassza a **Windows Áruház** ikonját.
3. A **Közzétételi beállítások** konfigurációja csoportban ellenőrizze az InternetClientServer és a SpatialPerception **(InternetClientServer** és **SpatialPerception ) című**részt.

**A fő virtuális kamera beállítása**
1. A **Hierarchia panelen**válassza a **Fő kamera lehetőséget.**
2. A **felügyelőben**állítsa az átalakítási pozícióját **0,0,0-ra**.
3. Keresse meg a **Jelzők törlése** tulajdonságot, és módosítsa a legördülő menüt **A Skybox** mappáról **egyszínűre.**
4. Kattintson a **Háttér** mezőre a színválasztó megnyitásához.
5. Állítsa **az R, G, B és A** **beállítását 0-ra.**
6. Válassza **az Összetevő hozzáadása lehetőséget,** és keresse meg a **Tértérkép-ütköztetőt,** és adja hozzá azt.

**Készítse el a szkript**
1. A **Project** ablaktáblán hozzon létre egy új **mappát, a Parancsfájlokat**az **Eszközök** mappa alatt.
2. Kattintson a jobb gombbal a mappára, majd válassza **a Create >**, **C# Script parancsot.** Cím ez **AzureSpatialAnchorsScript**.
3. Nyissa meg a **GameObject** -> **Create Empty**című.
4. Jelölje ki, és a **Felügyelő** átnevezi **GameObject-ről** **MixedRealityCloud -ra.** Válassza **az Összetevő hozzáadása lehetőséget,** és keresse meg és adja hozzá az **AzureSpatialAnchorsScript parancsot.**

**A gömb előre gyártott létrehozása**
1. Nyissa meg a GameObject 3D Object Sphere .go to **GameObject** -> **3D Object** -> **Sphere**.
2. A **felügyelőben**állítsa a skálát **0,25, 0,25, 0,25-re.**
3. Keresse meg a **Gömb** objektumot a **Hierarchia** ablaktáblában. Kattintson rá, és húzza a **Project** ablaktábla **Eszközök** mappájába.
4. Kattintson a jobb gombbal, és **törölje** a **Hierarchia** ablaktáblában létrehozott eredeti gömböt.

Most már rendelkeznie kell egy előre gyártott gömbbel a **Projekt** ablaktáblában.

## <a name="trying-it-out"></a>Próbálja ki
Annak teszteléséhez, hogy minden működik-e, építse fel az alkalmazást a **Unity** alkalmazásba, és telepítse a **Visual Studióból.** Kövesse az [ **MR Basics 100: Getting started with Unity** tanfolyam](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) 6. Meg kell jelennie az Egység kezdőképernyőn, majd egy tiszta kijelző.

## <a name="place-an-object-in-the-real-world"></a>Tárgy elhelyezése a való világban
Hozzunk létre & objektumot helyezzen el az alkalmazás használatával. Nyissa meg az [alkalmazás telepítésekor](#trying-it-out)létrehozott Visual Studio-megoldást.

Először adja hozzá a `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`következő importot a következőbe:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Ezután adja hozzá a következő `AzureSpatialAnchorsScript` tagváltozókat az osztályhoz:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-42,48-52,60-79)]

Mielőtt folytatnánk, meg kell állítanunk a gömb előregyártott általunk létrehozott mi spherePrefab tag változó. Menj vissza az **Egységhez.**
1. Az **Egység ablakban**jelölje ki a **MixedRealityCloud** objektumot a **Hierarchia** ablaktáblában.
2. Kattintson a **Projekt** ablaktáblában mentett **Gömb** előregyártott ablaktáblára. Húzza a **Sphere** kattintott a **Gömb előregyártott** terület alatt **Azure Spatial Anchors Script (Script)** a **Felügyelő** ablaktábla.

Most már a **Gömb** beállítása, mint a prefab a forgatókönyvet. Építsd meg a **Unity-ből,** majd nyissa meg újra a létrejövő Visual **Studio-megoldást,** ahogy az imént tette a [Kipróbálása .](#trying-it-out)

A **Visual Studio** `AzureSpatialAnchorsScript.cs` alkalmazásban nyissa meg újra a nyitja meg a megnyitást. Adja hozzá a `Start()` következő kódot a metódushoz. Ez a kód `GestureRecognizer`csatlakozik , amely észleli, `HandleTap`ha van egy levegő csapot, és hívja.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-90,93&highlight=4-10)]

Most hozzá kell adnunk a következő `HandleTap()` módszert az alábbiakban `Update()`. Ez akarat csinál egy sugár öntött és kap egy nyompont aminél a gömb.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-277,299-300,304-312)]

Most létre kell hoznunk a gömböt. A gömb kezdetben fehér lesz, de ez az érték később módosul. Adja hozzá `CreateAndSaveSphere()` a következő módszert:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-325,390)]

Futtassa az alkalmazást a **Visual Studióból,** hogy még egyszer érvényesítse azt. Ezúttal érintse meg a képernyőt, hogy & helyezze a fehér gömböt az Ön által választott felületre.

## <a name="set-up-the-dispatcher-pattern"></a>A diszpécser minta beállítása

A Unity használata során minden Unity API-nak, például a felhasználói felület frissítéseinek megtöltéséhez használt API-knak a fő szálon kell történnie. A kódban azonban írunk, hogy visszahívásokat kapunk más szálakon. Szeretnénk frissíteni ui ezekben a visszahívások, így kell egy módja annak, hogy menjen egy oldalsó szál-ra a fő téma. Ha egy oldalszálból szeretnénk kódot végrehajtani a fő szálon, a diszpécser mintát használjuk.

Adjunk hozzá egy tagváltozót, a dispatchQueue-t, amely egy műveletvára. A műveleteket a várólistára küldjük, majd a várólistát visszaléptetjük, és futtatjuk a műveleteket a fő szálon.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=38-51&highlight=6-9)]

Ezután adjunk hozzá egy módot arra, hogy egy műveletet hozzáadjon a várólistához. Add `QueueOnUpdate()` után `Update()` közvetlenül:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=107-117)]

Most használjuk a Update() hurkot annak ellenőrzésére, hogy van-e művelet várólistára. Ha igen, akkor dequeue a műveletet, és fuss ez.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=95-105&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Az Azure Spatial Anchors SDK beszereznie

## <a name="via-unity-package"></a>[Keresztül Unity csomag](#tab/UnityPackage)

Most letöltjük az Azure Spatial Anchors SDK-t. Nyissa meg az [Azure Spatial Anchors GitHub-kiadások lapját.](https://github.com/Azure/azure-spatial-anchors-samples/releases) Az Eszközök csoportban töltse le az **AzureSpatialAnchors.unitypackage csomagot.** Az Egység ablakban nyissa meg **az Eszközök**, kattintson a Csomag > egyéni csomag **importálása...****parancsra.** Nyissa meg a csomagot, és válassza a **Megnyitás**lehetőséget.

A megjelenő új **Import Unity Package** ablakban törölje a **bővítmények** jelölőnégyzet jelölését, majd **kattintson** a jobb alsó sarokimportálásgombjára.

Most vissza kell állítanunk a Nuget-csomagokat az Azure Spatial Anchors SDK beszerzéséhez. Építsd meg a **Unity,** majd nyissa meg, és épít a kapott **Visual Studio** megoldás újra, a részletes Kipróbálás a [ki](#trying-it-out).

## <a name="via-nugetforunity"></a>[Keresztül NugetforUnity](#tab/NuGetForUnity)

Először is telepítenünk kell a NuGetForUnity-t. Nyissa meg a [NuGetForUnity GitHub releases lapot](https://github.com/GlitchEnzo/NuGetForUnity/releases). Az Eszközök területen töltse le a legfrissebb **NuGetForUnity.unitypackage csomagot.** Az Egység ablakban nyissa meg **az Eszközök**, kattintson a Csomag > egyéni csomag **importálása...****parancsra.** Nyissa meg a csomagot, és válassza a **Megnyitás**lehetőséget. Unity most telepíti NugetForUnity. Ha nem lát új **NuGet** legördülő menüt a Unity alkalmazásban, akkor lehet, hogy a Jobb gombbal a Projektek eszközök területen kell >  **kattintania.****Assets** Ezután válassza **az Összes újraimportálása**lehetőséget.

Miután telepítette a NuGetForUnity csomagot, válassza a **NuGet** > **Manage NuGet Packages**lehetőséget. Ezután keresse meg a Microsoft.Azure.SpatialAnchors.Unity elemet, és válassza **a Telepítés**lehetőséget.

Most kell építeni, hogy a tényleges Azure Spatial Anchors SDK, a NuGet csomag, amit csak letöltött csak tartalmaz segítő szkriptek. Építsd meg a **Unity,** majd nyissa meg, és épít a kapott **Visual Studio** megoldás újra, a részletes Kipróbálás a [ki](#trying-it-out).

---

A **Visual Studio-megoldásban** adja hozzá `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`a következő importálást a következő bekövetkező adatokhoz:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Ezután adja hozzá a következő `AzureSpatialAnchorsScript` tagváltozókat az osztályhoz:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=48-63&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Helyi Azure-térbeli horgony csatolása a helyi horgonyhoz

Állítsuk be az Azure Spatial Anchor CloudSpatialAnchorSession. Kezdjük azzal, hogy `InitializeSession()` a következő `AzureSpatialAnchorsScript` módszert adjuk hozzá az osztályodhoz. A megnevezett, biztosítja az Azure Spatial Anchors munkamenet jön létre, és megfelelően inicializált indításakor az alkalmazás.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=174-202,205-209)]

Most kódot kell írnunk a delegált hívások kezeléséhez. A folytatásban még többet adunk hozzájuk.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=211-226)]

Most, nézzük horog `initializeSession()` a `Start()` módszer a módszer a módszer.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-93&highlight=12)]

Végül adja hozzá a `CreateAndSaveSphere()` következő kódot a metódushoz. Egy helyi Azure Spatial Anchor-t csatol a szférához, amelyet a valós világban helyezünk el.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-338,390&highlight=14-25)]

Mielőtt továbbhaladna, létre kell hoznia egy Azure Spatial Anchors-fiókazonosítót és kulcsot, ha még nem rendelkezik velük. A beszerzésükhöz kövesse a következő szakaszt.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Töltse fel a helyi horgonyt a felhőbe

Miután rendelkezik az Azure Spatial Anchors fiókazonosítójának és `Account Key` `SpatialAnchorsAccountKey`kulcsának, menjen és illessze be a `Account Id` be `SpatialAnchorsAccountId` és a be.

Végül, kössünk össze mindent. A `SpawnNewAnchoredObject()` módszerben adja hozzá a következő kódot. A mint `CreateAnchorAsync()` a gömb létrejöttek, meghívja a metódust. Miután a metódus visszatér, az alábbi kód egy utolsó frissítést hajt végre a gömbön, és a színét kékre változtatja.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-391&highlight=26-77)]

Futtassa újra az alkalmazást a **Visual Studio-ból.** Mozogj a fejed körül, majd a levegő csapot, hogy helyezze el a gömb. Amint elegendő keretünk lesz, a gömb sárgává változik, és a felhőfeltöltés elindul. Amint a feltöltés befejeződik, a gömb kékre vált. Szükség esetén a **Visual Studio** Kimenet ablakával is figyelheti az alkalmazás által küldött naplóüzeneteket. A létrehozási folyamat hozása ajánlott állapotot, valamint a felhő által a feltöltés befejezése után visszaadott horgonyazonosítót is megtekintheti.

> [!NOTE]
> Ha a "DllNotFoundException: Nem tölthető be A DLL 'AzureSpatialAnchors": A megadott modul nem található.", **tisztítsa meg** és **készítse el újra** a megoldást.

## <a name="locate-your-cloud-spatial-anchor"></a>Keresse meg a felhőtérbeli horgonyt

Az egyik a horgony feltöltött a felhőbe, készen állunk, hogy megpróbálja megtalálni újra. Vegyük hozzá a következő `HandleTap()` kódot a módszerhez. Ez a kód:

* Hívja `ResetSession()`meg , `CloudSpatialAnchorSession` amely leállítja a meglévő kék gömböt a képernyőről.
* Inicializálása `CloudSpatialAnchorSession` újra. Azért tesszük ezt, hogy biztosak legyünk benne, hogy a horgony, amit meg fogunk találni, a felhőből származik, ahelyett, hogy a helyi horgony lenne, amit létrehoztunk.
* Hozzon létre egy **figyelőt,** amely megkeresi az Azure Spatial Anchors-ba feltöltött horgonyt.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-305&highlight=13-31,35-36)]

Most adjuk hozzá `ResetSession()` a `CleanupObjects()` mi és módszerek. Tudod őket az alábbi`QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=119-172)]

Most kell csatlakoztatni a kódot, amely akkor lesz meghívva, amikor a horgony, amit lekérdezünk található. A `InitializeSession()`területén belül adja hozzá a következő visszahívásokat:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=200-206&highlight=4-5)]


Most lehetővé teszi, hogy adjunk kódot, amely létrehoz & helyet egy zöld gömb, ha a CloudSpatialAnchor található. Azt is lehetővé teszi a képernyő megérinti újra, így ismételje meg az egész forgatókönyvet még egyszer: hozzon létre egy másik helyi horgony, töltse fel, és keresse meg újra.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=228-265)]

Ennyi az egész! Futtassa az alkalmazást a **Visual Studio-ból** még egyszer utoljára, hogy kipróbálja az egész forgatókönyvet. Mozogjon a készüléken, és helyezze el a fehér gömböt. Ezután mozgassa a fejét, hogy rögzítse a környezeti adatokat, amíg a gömb sárgára nem vált. A helyi horgony lesz feltöltve, és a gömb kéklesz. Végül érintse meg még egyszer a képernyőt, hogy a helyi horgony törlődjön, majd lekérdezzük a felhőbeli megfelelőjét. Folytassa az eszköz mozgatásával, amíg a felhőbeli térbeli horgony meg nem található. A zöld gömbnek a megfelelő helyen kell megjelennie, és öblítheti & megismételheti az egész forgatókönyvet.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]