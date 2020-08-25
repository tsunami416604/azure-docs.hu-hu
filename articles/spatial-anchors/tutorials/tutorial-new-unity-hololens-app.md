---
title: 'Oktatóanyag: új HoloLens Unity-alkalmazás létrehozása'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre új HoloLens Unity-alkalmazást az Azure térbeli horgonyok használatával.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 06/22/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: ee8b8c2931d006dbb3d472b545030d3aff79c56a
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "85297987"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Oktatóanyag: lépésenkénti útmutató új HoloLens Unity-alkalmazás létrehozásához az Azure térbeli horgonyok használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre új HoloLens Unity-alkalmazást az Azure térbeli Horgonyokkal.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel:

1. Egy Windows rendszerű gép, amelyen a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> telepítve van a **univerzális Windows-platform fejlesztési** számítási feladattal és a **Windows 10 SDK-val (10.0.18362.0 vagy újabb)** és a <a href="https://git-scm.com/download/win" target="_blank">git for Windows</a>rendszerrel.
2. A Visual studióhoz készült [C++/WinRT Visual Studio bővítményt (VSIX)](https://aka.ms/cppwinrt/vsix) a [Visual Studio piactérről](https://marketplace.visualstudio.com/)kell telepíteni.
3. HoloLens-eszköz, amelyen engedélyezve van a [fejlesztői mód](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) . Ehhez a cikkhez egy HoloLens-eszközre van szükség, amely a [Windows 10 október 2018 frissítését](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (más néven RS5) ismerteti. A HoloLens legújabb kiadásának frissítéséhez nyissa meg a **Beállítások** alkalmazást, lépjen a **frissítés & biztonság**elemre, majd kattintson a **frissítések keresése** gombra.

## <a name="getting-started"></a>Első lépések

Először be kell állítania a projekt és az egység jelenetét:
1. Az egység elindítása.
2. Válassza az **Új** lehetőséget.
4. Győződjön meg arról, hogy a **3D** ki van választva.
5. Nevezze el a projektet, és adjon meg egy mentési **helyet**.
6. Kattintson a **projekt létrehozása**gombra.
7. Mentse az üres alapértelmezett jelenetet egy új fájlba a következő paranccsal: **file**  >  **Save as (Mentés másként**).
8. Nevezze el az új jelenet **főoldalát** , és kattintson a **Save (Mentés** ) gombra.

**A projekt beállításainak megadása**

Most olyan Unity Project-beállításokat fogunk beállítani, amelyek segítenek a Windows holografikus SDK fejlesztésében.

Először is lehetővé teszi az alkalmazás minőségi beállításainak megadását.
1. Válassza **Edit**a  >  **projekt beállításainak**  >  **Quality** szerkesztése lehetőséget
2. A **Windows áruház** emblémájának oszlopában kattintson a nyílra az alapértelmezett sorban, és válassza a **nagyon alacsony** **értéket** . A beállítás helyesen lesz alkalmazva, ha a **Windows áruház** oszlopában található mező és a **nagyon alacsony** sor zöld.

Az egységnek tisztában kell lennie azzal, hogy az exportálni próbált alkalmazásnak hozzon létre egy részletes nézetet 2D-nézet helyett. Hozzunk létre egy részletes nézetet a virtuális valóság támogatásának engedélyezésével a Windows 10-es SDK-t célzó Unity-on.

1. Lépjen a **Edit**  >  **Project Settings**  >  **Player**szerkesztése menüpontra.
2. A **Player-beállítások** **ellenőr paneljén** válassza a **Windows áruház** ikont.
3. Bontsa ki az **XR-beállítások** csoportot.
4. A **renderelés** szakaszban jelölje be a **virtuális valóság támogatott** jelölőnégyzetet egy új **Virtual Reality SDK** -lista hozzáadásához.
5. Ellenőrizze, hogy a **Windows vegyes valóság** megjelenik-e a listában. Ha nem, válassza a **+** lista alján található gombot, és válassza a **Windows vegyes valóság**lehetőséget.

> [!NOTE]
> Ha nem látja a Windows áruház ikonját, ellenőrizze, hogy a telepítés előtt a Windows áruház .NET-parancsfájlok futtatásának hátteret választotta-e. Ha nem, lehetséges, hogy újra kell telepítenie az egységet a megfelelő Windows-telepítéssel.

**Parancsfájl-háttér konfigurációjának ellenőrzése**
1. Nyissa meg **a**  >  **Project Settings (projekt beállítása**  >  )**lejátszót** (lehetséges, hogy az előző lépésben megnyitotta a **lejátszót** ).
2. A **Player-beállítások** **ellenőr paneljén** válassza a **Windows áruház** ikont.
3. A **További beállítások** konfigurálása szakaszban győződjön meg arról, hogy a **parancsfájlkezelési háttér** **IL2CPP**értékre van állítva.

**Képességek beállítása**
1. Nyissa meg **a**  >  **Project Settings (projekt beállítása**  >  )**lejátszót** (lehetséges, hogy az előző lépésben megnyitotta a **lejátszót** ).
2. A **Player-beállítások** **ellenőr paneljén** válassza a **Windows áruház** ikont.
3. A **közzétételi beállítások** konfigurálása szakaszban keresse meg a **InternetClientServer** és a **SpatialPerception**.

**A fő virtuális kamera beállítása**
1. A **hierarchia panelen**válassza a **fő kamera**elemet.
2. Az **ellenőrben**állítsa az átalakítási pozícióját **0, 0,** 0 értékre.
3. Keresse meg a **Clear Flags** tulajdonságot, és módosítsa a legördülő listát a **SkyBox** értékről a **Solid Color**értékre.
4. A háttérszín megnyitásához kattintson a **háttér** mezőre.
5. Állítsa be az **R, a G, a B és az a** **értéket 0**értékre.
6. Válassza az **összetevő hozzáadása** lehetőséget, és keresse meg és adja hozzá a **térbeli leképezési ütközést**.

**Szkript létrehozása**
1. A **projekt** ablaktáblán hozzon létre egy új mappát, **parancsfájlokat**az **eszközök** mappában.
2. Kattintson a jobb gombbal a mappára, majd válassza a **létrehozás >**, **C# parancsfájl**lehetőséget. Cím **AzureSpatialAnchorsScript**.
3. Nyissa meg a **GameObject**  ->  **create Empty (üres**) lehetőséget.
4. Jelölje ki, majd a **Inspector** nevezze át a **GameObject** -ből a **MixedRealityCloud**-be. Válassza az **összetevő hozzáadása** lehetőséget, és keresse meg és adja hozzá a **AzureSpatialAnchorsScript**.

**A gömb panel létrehozása**
1. Nyissa meg a **GameObject**  ->  **3D Object**  ->  **szférát**.
2. Az **ellenőrben**állítsa be a méretezést **0,25, 0,25, 0,25**értékre.
3. Keresse meg a **gömb** objektumot a **hierarchia** ablaktáblán. Kattintson rá, és húzza a **projekt** ablaktábla **eszközök** mappájába.
4. Kattintson a jobb gombbal, és **törölje** a **hierarchia** ablaktáblán létrehozott eredeti gömbet.

Most már rendelkeznie kell egy gömb panelrel a **projekt** ablaktáblában.

## <a name="trying-it-out"></a>Kipróbálás
Ha szeretné kipróbálni, hogy minden megfelelően működik-e, hozzon létre egy **egységet** , és telepítse a **Visual studióból**. Kövesse a 6. fejezetet a [ **MR alapjai 100: első lépések az Unity** tanfolyamon](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) . Ekkor látnia kell az Unity Start képernyőt, majd egy tiszta képernyőt.

## <a name="place-an-object-in-the-real-world"></a>Egy objektum elhelyezése a valós világban
Hozzuk létre & helyezzen egy objektumot az alkalmazás használatával. Nyissa meg az [alkalmazás üzembe helyezésekor](#trying-it-out)létrehozott Visual Studio-megoldást.

Először adja hozzá a következő importálásokat a alkalmazáshoz `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Ezután adja hozzá a következő tagok változóit a `AzureSpatialAnchorsScript` osztályhoz:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-47,53-57,65-84)]

A folytatás előtt be kell állítania a spherePrefab-tag változón létrehozott gömb panelt. Lépjen vissza az **egységbe**.
1. Az **egység**területen válassza ki a **MixedRealityCloud** objektumot a **hierarchia** ablaktáblán.
2. Kattintson a **projekt** ablaktáblán mentett **gömb** panelre. Húzza azt a **gömbre** , amelyre rákattintott az **Azure térbeli horgonyok parancsfájlja (szkript)** **alatt a** **gömb panelre** .

Ekkor meg kell **jelennie a** panelnek a parancsfájlban. Hozza létre az **egységet** , majd nyissa meg újra az eredményül kapott **Visual Studio** -megoldást, ahogy azt a [kipróbálás](#trying-it-out)során is tette.

A **Visual Studióban**nyissa meg `AzureSpatialAnchorsScript.cs` újra. Adja hozzá a következő kódot a `Start()` metódushoz. Ez a kód összekapcsol `GestureRecognizer` , ami akkor észlelhető, ha van egy levegő koppintás és hívás `HandleTap` .

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-95,98&highlight=4-10)]

Most a következő módszert kell megadnia `HandleTap()` `Update()` . A művelet elvégzi a Ray castot, és egy olyan találatot mutat be, amelyen egy gömb helyezhető el.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-283,305-306,310-318)]

Most létre kell hozni a gömbet. A gömb kezdetben fehér lesz, de ez az érték később lesz módosítva. Adja hozzá a következő `CreateAndSaveSphere()` metódust:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-331,396)]

Az alkalmazást a **Visual studióból** futtatva ellenőrizheti még egyszer. Ezúttal koppintson a képernyőre a létrehozásához & helyezze el a fehér gömbet a választott felületen.

## <a name="set-up-the-dispatcher-pattern"></a>A diszpécser minta beállítása

Az Unity, az összes Unity API-k, például a felhasználói felület frissítéseinek elvégzéséhez használt API-k esetében a fő szálon kell történnie. A kódban a visszahívások más szálon is elérhetők. Ezeket a visszahívásokat szeretnénk frissíteni a felhasználói felületen, ezért a főszálra való ugráshoz is szükség van egy oldalról. Ha kódot szeretne végrehajtani a fő szálon egy oldalból álló szálból, a diszpécser mintát fogjuk használni.

Vegyünk fel egy tag változót, a dispatchQueue, amely a műveletek várólistája. A rendszer leküldi a műveleteket a várólistára, majd elvégezte a műveletet, majd a fő szálon futtatja a műveleteket.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=43-56&highlight=6-9)]

Következő lépésként adjon hozzá egy műveletet a várólistához. Hozzáadás `QueueOnUpdate()` közvetlenül a következő után `Update()` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=112-122)]

Most az Update () ciklus használatával ellenőrizze, hogy van-e várólistán egy művelet. Ha igen, a műveletet a rendszer a művelet elküldésével és futtatásával elvégezheti.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=100-110&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Azure térbeli horgonyok SDK beszerzése

## <a name="via-unity-package"></a>[Unity-csomagon keresztül](#tab/UnityPackage)

Most letöltjük az Azure térbeli horgonyok SDK-t. Lépjen az [Azure térbeli horgonyok GitHub-verziók oldalára](https://github.com/Azure/azure-spatial-anchors-samples/releases). Az eszközök alatt töltse le a **AzureSpatialAnchors. unitypackage**. Az egység területen válassza az **eszközök**, majd az egyéni csomag **importálása**  >  **...** lehetőséget. Navigáljon a csomaghoz, és válassza a **Megnyitás**lehetőséget.

Az új **importálási egység csomag** ablakban válassza ki a **plugins** elemet, majd kattintson az **Importálás** elemre a jobb alsó sarokban.

Most vissza kell állítania a Nuget-csomagokat az Azure térbeli horgonyok SDK beszerzéséhez. Hozzon létre egy **egységet** , majd nyissa meg és hozza létre újra az eredményül kapott **Visual Studio** -megoldást, ahogy [azt a kipróbálás](#trying-it-out)során is részletezi.

## <a name="via-nugetforunity"></a>[NuGetForUnity-n keresztül](#tab/NuGetForUnity)

Először telepítenie kell a NuGetForUnity. Lépjen a [NuGetForUnity GitHub-kiadások oldalára](https://github.com/GlitchEnzo/NuGetForUnity/releases). Az eszközök alatt töltse le a legújabb **NuGetForUnity. unitypackage**. Az egység területen válassza az **eszközök**, majd az egyéni csomag **importálása**  >  **...** lehetőséget. Navigáljon a csomaghoz, és válassza a **Megnyitás**lehetőséget. Az Unity most telepíti a NugetForUnity-t. Ha nem lát új **NuGet** legördülő listát az egységben, előfordulhat, hogy a jobb gombbal kell kattintania a **projektek**  >  **eszközei**területen. Ezután válassza **az összes újraimportálása**elemet.

Miután telepítette a NuGetForUnity, válassza a **NuGet**  >  **NuGet-csomagok kezelése**lehetőséget. Ezután keressen rá a Microsoft. Azure. SpatialAnchors. Unity kifejezésre, és válassza a **telepítés**lehetőséget.

Most létre kell hozni a tényleges Azure térbeli horgonyok SDK-t, mivel a letöltött NuGet-csomag csak segítő parancsfájlokat tartalmaz. Hozzon létre egy **egységet** , majd nyissa meg és hozza létre újra az eredményül kapott **Visual Studio** -megoldást, ahogy [azt a kipróbálás](#trying-it-out)során is részletezi.

---

A **Visual Studio** -megoldásban adja hozzá a következő importálást a alkalmazásba `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Ezután adja hozzá a következő tag-változókat a `AzureSpatialAnchorsScript` osztályhoz:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=53-68&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Helyi Azure térbeli horgony csatolása a helyi horgonyhoz

Hozzuk létre az Azure térbeli horgony CloudSpatialAnchorSession. Először adja hozzá a következő `InitializeSession()` metódust az `AzureSpatialAnchorsScript` osztályon belül. A meghívást követően az Azure térbeli horgonyok munkamenetét a rendszer az alkalmazás indításakor hozza létre és megfelelően inicializálja.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=179-208,211-215)]

A delegált hívások kezeléséhez most kódot kell írnia. A folytatáshoz további részleteket adunk hozzájuk.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=217-232)]

Most pedig Ismerkedjen meg a metódussal `initializeSession()` `Start()` .

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-98&highlight=12)]

Végül adja hozzá a következő kódot a `CreateAndSaveSphere()` metódushoz. Egy helyi Azure térbeli horgonyt fog csatolni ahhoz a gömbhöz, amelyet a valós világba helyezünk.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-344,396&highlight=14-25)]

Mielőtt továbblépne, létre kell hoznia egy Azure térbeli horgonyt a fiók azonosítójának, kulcsának és tartományának beszerzéséhez, ha még nem tette meg. A beszerzéséhez kövesse az alábbi szakaszt.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Töltse fel a helyi horgonyt a felhőbe

Ha már rendelkezik az Azure térbeli azonosítójának azonosítójával, kulcsával és tartományával, lépjen be és illessze be a `Account Id` into `SpatialAnchorsAccountId` , a into és a into rendszerbe `Account Key` `SpatialAnchorsAccountKey` `Account Domain` `SpatialAnchorsAccountDomain` .

Végül Ismerkedjen meg egymással. A `SpawnNewAnchoredObject()` metódusban adja hozzá a következő kódot. A `CreateAnchorAsync()` metódust a gömb létrehozása után azonnal meghívja. A metódus visszatérése után az alábbi kód egy végső frissítést hajt végre a szférában, és a színét kékre változtatja.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-397&highlight=26-77)]

Futtassa az alkalmazást a **Visual studióból** . Mozgassa a fejét, majd a levegőben koppintva helyezze el a gömbjét. Ha elegendő keret áll rendelkezésre, a gömb sárga színűre vált, a Felhőbeli feltöltés pedig elindul. Miután a feltöltés befejeződik, a gömb kék színűre vált. Igény szerint a **Visual Studióban** található output (kimenet) ablakban is megfigyelheti az alkalmazás által küldött naplóüzenetek figyelését. Megtekintheti a létrehozási folyamat ajánlott állapotát, valamint azt a horgony-azonosítót, amelyet a felhő a feltöltés befejeződése után visszaad.

> [!NOTE]
> Ha "DllNotFoundException: nem tölthető be a DLL-fájl" AzureSpatialAnchors ": a megadott modul nem található.", **törölje** , majd hozza **létre** újra a megoldást.

## <a name="locate-your-cloud-spatial-anchor"></a>A Felhőbeli térbeli horgony megkeresése

A rendszer feltölti az egyik horgonyt a felhőbe, készen áll arra, hogy újra megpróbálja megkeresni. Vegyük fel a következő kódot a `HandleTap()` metódusba. A kód a következőket teszi:

* Hívás `ResetSession()` , amely leállítja `CloudSpatialAnchorSession` és eltávolítja a meglévő kék szférát a képernyőről.
* Inicializálás `CloudSpatialAnchorSession` újra. Így biztosak vagyunk abban, hogy a keresett horgony a felhőből származik, ahelyett, hogy a létrehozott helyi horgonyt létrehoztuk.
* Hozzon létre egy **figyelőt** , amely az Azure térbeli horgonyokra feltöltött horgonyt keresi.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-311&highlight=13-31,35-36)]

Most adjuk hozzá a `ResetSession()` és a `CleanupObjects()` metódusokat. Az alábbi lépéseket helyezheti el `QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=124-177)]

Most meg kell csatlakoztatni a kódot, amely akkor fog megjelenni, amikor a lekérdezett horgony található. `InitializeSession()`A-ben adja hozzá a következő visszahívásokat:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=206-212&highlight=4-5)]


Most pedig lehetővé teszi a létrehozandó kód hozzáadását, & egy zöld gömb elhelyezése után a CloudSpatialAnchor található. Emellett ismét lehetővé teszi a képernyő koppintását, így még egyszer megismételheti az egész forgatókönyvet: hozzon létre egy másik helyi horgonyt, töltse fel, majd keresse meg újra.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=234-271)]

Ennyi az egész! Futtassa az alkalmazást a **Visual studióból** egy utolsó alkalommal, hogy kipróbálja a teljes forgatókönyv végét. Helyezze át az eszközt, és helyezze a fehér gömbbe. Ezután folytassa a fej áthelyezésével, hogy rögzítse a környezeti adatait, amíg a gömb sárgára nem változik. A rendszer feltölti a helyi horgonyt, és a gömb kék színűre vált. Végül koppintson még egyszer a képernyőre, hogy a helyi horgony el legyen távolítva, majd lekérdezjük a Felhőbeli megfelelőjét. Folytassa az eszköz áthelyezését, amíg a Felhőbeli térbeli horgony nem található. Egy zöld gömbnek a megfelelő helyen kell megjelennie, és kiöblítheti & ismételje meg a teljes forgatókönyvet.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]