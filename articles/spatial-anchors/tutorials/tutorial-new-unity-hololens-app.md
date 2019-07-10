---
title: Oktatóanyag – részletes útmutató Azure térbeli horgonyok használatával új HoloLens Unity-alkalmazás létrehozása |} A Microsoft Docs
description: Ebben az oktatóanyagban megismerheti, hogyan hozhat létre egy új Azure térbeli horgonyok használatával HoloLens Unity-alkalmazást.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 07/05/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 57244dd9f3365b3899bcc1dde6382cc3b51719d9
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722926"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Oktatóanyag: Azure térbeli horgonyok használatával új HoloLens Unity-alkalmazás létrehozásának részletes útmutatóját

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy új HoloLens Unity-alkalmazás Azure térbeli horgonyok.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel:

1. Olyan Windows-gépeken, <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> telepített a **univerzális Windows Platform fejlesztési** számítási feladatok és a **Windows 10 SDK (10.0.17763.0 vagy újabb)** az összetevőt, és <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a>.
2. A [ C++WinRT Visual Studio bővítmény (VSIX)](https://aka.ms/cppwinrt/vsix) a Visual Studióban telepítenie kell a a [Visual Studio-piactér](https://marketplace.visualstudio.com/).
3. HoloLens eszköz [fejlesztői mód](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) engedélyezve van. Ez a cikk a HoloLens eszköz van szükség a [Windows 2018. október 10. frissítés](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (más néven RS5). A legújabb verziót a HoloLens frissítéséhez nyissa meg a **beállítások** alkalmazást, lépjen a **frissítés és biztonság**, majd válassza a **frissítések keresése** gombra.

## <a name="getting-started"></a>Első lépések

Először beállítjuk a projekt és a Unity jelenet:
1. Indítsa el a Unity.
2. Válassza az **Új** lehetőséget.
4. Győződjön meg, hogy **3D** van kiválasztva.
5. Nevezze el a projektet, és adja meg a mentési **hely**.
6. Kattintson a **Create project**.
7. Mentse a üres alapértelmezett jelenet egy új fájl használatával: **Fájl** > **Mentés másként**.
8. Nevezze el az új jelenetet **fő** , és nyomja le az **mentése** gombra.

**A Projektbeállítások között beállítása**

Most már értékre állítjuk néhány Unity project beállításait, hogy segítsen a cél a Windows Holographic SDK-fejlesztéshez. 

Először is lehetővé teszi a minőség beállítása az alkalmazáshoz. 
1. Válassza ki **szerkesztése** > **beállításai a** > **minősége**
2. Az oszlop alatt a **Windows Store** embléma, kattintson a nyílra a **alapértelmezett** sort, és válassza ki **nagyon alacsony**. Tudni fogja, a beállítás lesz érvényes megfelelően Ha a mezőben a **Windows Store** oszlop és **nagyon alacsony** sor zöld.

Szükségünk ahhoz, hogy a Unity tudja, hogy szeretne exportálni az alkalmazás helyett 2 nap nézet ragadó nézetet kell létrehoznia. Engedélyezi a virtuális valóságban támogatása és a Windows 10 SDK célzó Unity ragadó képet hozunk létre.

1. Lépjen a **szerkesztése** > **beállításai a** > **Player**.
2. Az a **Megtekintő panelen** a **Player beállítások**, jelölje be a **Windows Store** ikonra.
3. Bontsa ki a **XR beállítások** csoport.
4. Az a **Rendering** négyzet a szakaszban a **virtuális valóságban támogatott** jelölőnégyzetet, adjon hozzá egy új **virtuális valóságban SDK** listája.
5. Ellenőrizze, hogy **Windows vegyes valóság** megjelenik a listában. Ha nem, válassza ki a **+** gombra a lista alján, és válasszon **Windows vegyes valóság**.
 
> [!NOTE]
> Ha nem látja, akkor a Windows Store ikonra, duplán ellenőrizze, hogy a Windows Store .NET parancsfájl-háttérrendszer a telepítés előtt választotta. Ha nem, telepítse újra a Unity a megfelelő Windows-telepítést szeretne.

**.NET-konfiguráció ellenőrzése**
1. Lépjen a **szerkesztése** > **Projektbeállítások** > **Player** (még akkor is **Player** nyissa meg az előző lépés).
2. Az a **Megtekintő panelen** a **Player beállítások**, jelölje be a **Windows Store** ikonra.
3. Az a **egyéb beállítások** konfigurációs szakaszt, ellenőrizze, hogy **Scripting háttérrendszer** értékre van állítva **.NET**.

**set-képességek**
1. Lépjen a **szerkesztése** > **Projektbeállítások** > **Player** (még akkor is **Player** nyissa meg az előző lépés).
2. Az a **Megtekintő panelen** a **Player beállítások**, jelölje be a **Windows Store** ikonra.
3. Az a **közzétételi beállítások** ellenőrizze a konfigurációs szakaszban **InternetClientServer** és **SpatialPerception**.

**a fő virtuális kamera beállítása**
1. Az a **hierarchia Panel**válassza **fő kamera**.
2. Az a **vizsgáló**, az átalakítás pozíció beállítása **0,0,0**.
3. Keresse meg a **egyértelmű jelzők** tulajdonságot, és módosítsa a legördülő **Skybox** való **egyszínű**.
4. Kattintson a **háttér** mezőt, nyissa meg a színválasztó.
5. Állítsa be **R, a G, a B és a egy** való **0**.
6. Válassza ki **összetevő felvétele** , és keresse meg és adja hozzá a **térbeli leképezési Collider**.

**a parancsfájl létrehozása**
1. Az a **projekt** panelen hozzon létre egy új mappát **parancsfájlok**alatt a **eszközök** mappát. 
2. Kattintson jobb gombbal a mappára, majd válassza ki **létrehozás >** ,  **C# parancsfájl**. Ez a cím **AzureSpatialAnchorsScript**. 
3. Lépjen a **GameObject** -> **hozzon létre üres**. 
4. Válassza ki, majd a a **vizsgáló** nevezze át a **GameObject** való **MixedRealityCloud**. Válassza ki **összetevő felvétele** , és keresse meg és adja hozzá a **AzureSpatialAnchorsScript**.

**A sphere prefab létrehozása**
1. Lépjen a **GameObject** -> **3D objektum** -> **Sphere**.
2. Az a **vizsgáló**, a méretezési csoport beállítása **0,25, 0,25, 0,25**.
3. Keresse meg a **Sphere** objektumának a **hierarchia** ablaktáblán. Kattintson rá és húzza a **eszközök** mappájában a **projekt** ablaktáblán.
4. Kattintson a jobb gombbal, és **törlése** az eredeti sphere létrehozott a **hierarchia** ablaktáblán.

Most már egy prefab a sphere a **projekt** ablaktáblán.

## <a name="trying-it-out"></a>Kipróbálás
Ajánlatos, hogy minden működik, az alkalmazás létrehozása **Unity** és hogyan telepítheti a **Visual Studio**. A 6. fejezet kövesse a [ **100 MR alapjai: Ismerkedés a Unity** tanfolyam](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) ennek a végrehajtására. A Unity, indítsa el a képernyőn, majd törölje megjelenítési kell megjelennie.

## <a name="place-an-object-in-the-real-world"></a>Az objektum helyét a való világból
Hozzunk létre & az alkalmazás használatát egy objektum helyét. Nyissa meg a Visual Studio-megoldás, amely hoztunk létre, amikor azt [üzembe helyezett alkalmazás](#trying-it-out). 

Először adja hozzá az alábbi importálásokat be a `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Adja hozzá az alábbi tagokat változókat a `AzureSpatialAnchorsScript` osztály: 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-42,48-52,60-79)]

A folytatás előtt kell beállítani a sphere prefab a spherePrefab tag változó létrehozott. Lépjen vissza a **Unity**.
1. A **Unity**, jelölje be a **MixedRealityCloud** objektumának a **hierarchia** ablaktáblán.
2. Kattintson a a **Sphere** mentett prefab a **projekt** ablaktáblán. Húzza a **Sphere** , amire kattintott a **Sphere Prefab** alatti terület **Azure térbeli horgonyok parancsfájl (szkript)** a a **vizsgáló** panel .

Most már a **Sphere** állítja be a parancsfájlt a prefab. Build **Unity** , majd nyissa meg a létrejövő **Visual Studio** megoldás újra, például csak tette azt [kipróbálja](#trying-it-out). 

A **Visual Studio**, nyisson meg `AzureSpatialAnchorsScript.cs` újra. Adja hozzá az alábbi kódot a `Start()` metódust. Ez a kód fogja kapcsolni `GestureRecognizer`, amely észleli, ha van egy légi és hívás `HandleTap`.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-90,93&highlight=4-10)]

Ezzel kapunk, adja hozzá a következő `HandleTap()` metódus az alábbi `Update()`. Ez ray típuskonverzió tegye, és termelési találatok, amelynél a sphere helyezze. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-277,299-300,304-312)]

Most kell létrehozni a sphere. A sphere kezdetben fehér, de ezt az értéket később módosítható. Adja hozzá a következő `CreateAndSaveSphere()` módszer:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-325,390)]

Futtassa az alkalmazást a **Visual Studio** még egyszer érvényesítéséhez. Most, koppintson a képernyő létrehozása, és helyezze el a fehér sphere tetszőleges felületén.

## <a name="set-up-the-dispatcher-pattern"></a>Állítsa be az eseményelosztóra minta

Unity dolgozik, az összes Unity API-k, például API-k segítségével hajtsa végre a felhasználói felületi frissítések, kell a főszálban fordulhat elő. A kódban, azonban azt fog írni más szálak visszahívást kaphat azt. Szeretnénk frissítése felhasználói felület ezen visszahívások, így egy alakzatot a főszálban ügyféloldali szállal mód szükséges. Kód a főszálban egy ügyféloldali szálból végrehajtásához az eseményelosztóra mintát fogjuk használni. 

Adjunk hozzá egy tag változó dispatchQueue, azaz egy Témakörműveleteket. Azt fogja az üzenetsorban, az alakzatot műveletek leküldéses eltávolítása a sorból és a műveletek futtatására a főszálban. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=38-51&highlight=6-9)]

Ezután vegye fel az üzenetsorba művelet hozzáadása lehetőséget. Adjon hozzá `QueueOnUpdate()` után `Update()` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=107-117)]

Most már használja a Update() hurok annak ellenőrzéséhez, hogy egy művelet várólistára. Ha igen, azt fogja a művelet eltávolítása a sorból, és futtathatja.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=95-105&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Az Azure térbeli horgonyok SDK beszerzése

Azt a térbeli horgonyok Azure SDK letöltése most lesz. Lépjen a [Azure térbeli horgonyok GitHub-kiadások oldaláról](https://github.com/Azure/azure-spatial-anchors-samples/releases). Eszközök, alatt töltse le a **AzureSpatialAnchors.unitypackage** fájlt. 

A Unity, váltson **eszközök**, kattintson a **csomag importálása** > **egyéni csomag...** . Keresse meg a csomagot, és válasszon **nyílt**.

Az új **Unity-csomag importálása** ablak, amely a POP, válassza ki **nincs** bal alsó. Majd **AzureSpatialAnchorsPlugin** > **beépülő modulok**válassza **közös**, **szerkesztő**, és  **HoloLens**. Kattintson a **importálás** jobb alsó sarokban.

Most meg kell ahhoz, hogy az első Azure térbeli horgonyok SDK Nuget-csomagok visszaállítására. Build **Unity** , majd nyissa meg, és hozhat létre, a létrejövő **Visual Studio** megoldás újra, a részletes [kipróbálja](#trying-it-out). 

Az a **Visual Studio** megoldást, adja hozzá a következő importálható a `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=23-26&highlight=1)]

Adja hozzá az alábbi tag változókat a `AzureSpatialAnchorsScript` osztály:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=48-63&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Egy helyi Azure térbeli kapcsolati alapot csatolja a helyi forráshorgony

Állítsa be az Azure térbeli Forráshorgony CloudSpatialAnchorSession. Adja hozzá a következő kezdjük `InitializeSession()` belül metódus az `AzureSpatialAnchorsScript` osztály. Miután nevű, biztosítani fogja az Azure térbeli horgonyok munkamenet létrejött, és az alkalmazás indításakor megfelelően inicializálva.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=174-202,205-209)]

Most meg kell írási delegált hívások kezeléséhez szükséges kódot. Hozzáadjuk több hozzájuk módon folytatódik.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=211-226)]

Most tekintsük környezet igénybe vételét a `initializeSession()` metódus be a `Start()` metódus.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-93&highlight=12)]

Végül adja hozzá az alábbi kódot a `CreateAndSaveSphere()` metódust. Ez egy helyi Azure térbeli kapcsolati alapot csatolása a sphere, amely azt a való világból elhelyezése.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-338,390&highlight=14-25)]

A folytatás előtt minden, szüksége lesz egy Azure térbeli horgonyokat létrehozni fiók azonosítóját és kulcsát, ha már nincs rájuk. Hajtsa végre a következő szakaszban azok beszerzési.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>A helyi forráshorgony feltöltése a felhőbe

Miután a térbeli horgonyok Azure-fiók azonosítója és kulcsa, nyissa meg, és illessze be a `Account Id` be `SpatialAnchorsAccountId` és a `Account Key` be `SpatialAnchorsAccountKey`.

Végezetül hozzunk környezet igénybe vételét minden együtt. Az a `SpawnNewAnchoredObject()` metódust, adja hozzá a következő kódot. Ez meghívja a `CreateAnchorAsync()` metódus, amint a sphere jön létre. Ha a metódus visszatért, az alábbi kód elvégzi a sphere, a kék szín módosítása egy utolsó frissítése.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-391&highlight=26-77)]

Futtassa az alkalmazást a **Visual Studio** még egyszer. A fő mozgását, és ezután légi koppintással helyezze el a sphere. Amint rendelkezésünkre áll elég keretek, a sphere sárga be fog kapcsolni, és a felhő feltöltés indul. Miután a feltöltés befejezését követően a sphere kék fog kapcsolni. Igény szerint is használhat a kimeneti ablakban belül **Visual Studio** figyelése a az alkalmazás által küldött üzeneteket. Láthatja, hogy a létrehozás folyamatban van, valamint a horgony azonosítója, amely a felhő adja vissza a feltöltés befejeződése után az ajánlott megtekintéshez.

> [!NOTE]
> Ha a "DllNotFoundException: "AzureSpatialAnchors" DLL nem tölthető be: A megadott modul nem található. ", akkor **tisztaként** és **hozhat létre** újra megoldását.

## <a name="locate-your-cloud-spatial-anchor"></a>Keresse meg a felhő térbeli forráshorgony

A horgony egy feltöltött a felhőbe, készen állunk való elhelyezése ismét megkísérli. Adjuk hozzá az alábbi kódot a `HandleTap()` metódust. Ez a kód fogja végrehajtani:

* Hívás `ResetSession()`, amely le fog állni a `CloudSpatialAnchorSession` , és távolítsa el a meglévő kék sphere a képernyőről.
* Inicializálása `CloudSpatialAnchorSession` újra. Hogy ezt az így meg arról, hogy a horgony, keresse meg fogjuk származik helyett folyamatban van a helyi jegyzetobjektum hoztunk létre a felhőben.
* Hozzon létre egy **megfigyelő** , amely megkeresi a horgony Azure térbeli horgonyok feltölti azt.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-305&highlight=13-31,35-36)]

Most adjuk hozzá a `ResetSession()` és `CleanupObjects()` módszereket. Helyezheti őket az alábbi `QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=119-172)]

Most meg kell kapcsolni a kódot, amely akkor kell meghívni, ha azt a lekérdezésekor a horgony helye. Található `InitializeSession()`, adja hozzá a következő visszahívások:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=200-206&highlight=4-5)]

 
Most már lehetővé teszi, hogy adja hozzá a kódot, amely után a CloudSpatialAnchor megtalálható, helyezze el egy zöld sphere & létrehozása lesz. Azt is engedélyezheti a képernyőn koppintson újra, így a teljes forgatókönyv még egyszer ismételje meg: hozzon létre egy másik helyi forráshorgony, töltse fel, és keresse meg újra.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=228-265)]

Ennyi az egész! Futtassa az alkalmazást a **Visual Studio** próbálhatja ki a teljes forgatókönyv teljes körű egyszer utoljára. Navigálás az eszközt, és helyezze el a fehér sphere. Ezután folyamatosan áthelyezése a fő környezeti adatok rögzítéséhez, amíg a sphere sárga színűre váltott. A helyi forráshorgony fel lesz töltve, és a sphere kék fog kapcsolni. Végül koppintson a képernyő még egyszer, hogy a helyi forráshorgony törlődik, és majd azt fogja lekérdezni a felhő párjukhoz. Továbbra is a Navigálás az eszközt, amíg a térbeli felhőbeli horgony nem található. A megfelelő helyre meg kell jelennie egy zöld sphere, és le, és ismételje meg újra a teljes forgatókönyvet.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]