---
title: 'Oktatóanyag: Arcadatok észlelése és megjelenítése a .NET SDK használatával'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy Windows-alkalmazást hoz létre, amely a Face szolgáltatást használja a kép arcok észlelésére és keretezésére.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: b4458920ec8b3e0c302f6e0654891b83ed07264f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402899"
---
# <a name="tutorial-create-a-windows-presentation-framework-wpf-app-to-display-face-data-in-an-image"></a>Oktatóanyag: Hozzon létre egy Windows-bemutatókeret (WPF) alkalmazást a kép arcadatainak megjelenítéséhez

Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure Face szolgáltatást a .NET ügyfél SDK-n keresztül a lemezkép arcok észlelésére, majd az adatok bemutatására a felhasználói felületen. Hozzon létre egy WPF alkalmazást, amely észleli az arcokat, keretet rajzol az egyes lapok köré, és megjeleníti az arc leírását az állapotsorban. 

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> - WPF-alkalmazás létrehozása
> - A Face ügyféltár telepítése
> - Az ügyfélkódtár használata a képeken lévő arcok észleléséhez
> - Keret rajzolása minden észlelt arc köré
> - A kiemelt lap leírásának megjelenítése az állapotsoron

![Képernyőfelvétel téglalappal bekeretezett arcokról](../Images/getting-started-cs-detected.png)

A teljes mintakód érhető el a [Cognitive Face CSharp mintatár](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) a GitHubon.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené. 


## <a name="prerequisites"></a>Előfeltételek

- Face-előfizetési kulcs. Ingyenes próba-előfizetési kulcsot a [Cognitive Services kipróbálásával](https://azure.microsoft.com/try/cognitive-services/?api=face-api)szerezhetbe. Vagy kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) című részben található utasításokat, hogy előiratkozzon a Face szolgáltatásra, és levegye a kulcsot. Ezután [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a `FACE_SUBSCRIPTION_KEY` kulcs- és szolgáltatásvégpont-karakterlánchoz, amelyet elnevezett, illetve `FACE_ENDPOINT`a.
- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/)bármely kiadása.

## <a name="create-the-visual-studio-project"></a>A Visual Studio-projekt létrehozása

Új WPF alkalmazásprojekt létrehozásához kövesse az alábbi lépéseket.

1. A Visual Studióban nyissa meg az Új projekt párbeszédpanelt. Bontsa **ki a Telepített**elemet, majd a Visual **C#** elemet, majd válassza a **WPF-alkalmazást (.NET Framework)**.
1. Adja a **FaceTutorial** nevet az alkalmazásnak, majd kattintson az **OK** gombra.
1. Szerezze be a szükséges NuGet-csomagokat. Kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza **a NuGet csomagok kezelése parancsot**; ezután keresse meg és telepítse a következő csomagot:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.5.0-preview.1](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1)

## <a name="add-the-initial-code"></a>Kezdeti kód hozzáadása

Ebben a szakaszban az alkalmazás alapvető keretrendszerét az arcspecifikus funkciók nélkül adja hozzá.

### <a name="create-the-ui"></a>A felhasználói felület létrehozása

Nyissa meg *a MainWindow.xaml fájlt,* és cserélje le a tartalmát a következő kódra,&mdash;ez a kód létrehozza a felhasználói felület ablakát. A `FacePhoto_MouseMove` `BrowseButton_Click` és a metódusok olyan eseménykezelők, amelyeket később definiál.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?name=snippet_xaml)]

### <a name="create-the-main-class"></a>A főosztály létrehozása

Nyissa *meg MainWindow.xaml.cs,* és adja hozzá az ügyféltár névtereit más szükséges névterekkel együtt. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_using)]

Ezután szúrja be a következő kódot a **MainWindow** osztályba. Ez a kód létrehoz egy **FaceClient-példányt** az előfizetési kulcs és a végpont használatával.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_fields)]

Ezután adja hozzá a **MainWindow** konstruktora. Ellenőrzi a végpont URL-karakterláncát, majd társítja azt az ügyfélobjektumhoz.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_constructor)]

Végül adja hozzá a **BrowseButton_Click** és **FacePhoto_MouseMove** metódust az osztályhoz. Ezek a módszerek megfelelnek a *MainWindow.xaml-ben*deklarált eseménykezelőknek. A **BrowseButton_Click** metódus **openfiledialog**-ot hoz létre, amely lehetővé teszi a felhasználó számára . jpg kép kiválasztását. Ezután megjeleníti a képet a főablakban. A **BrowseButton_Click** és **FacePhoto_MouseMove** fennmaradó kódját a későbbi lépésekbe szúrja be. Is vegye `faceList` &mdash;figyelembe a hivatkozás listáját **DetectedFace** objektumok. Ez a hivatkozás az a hely, ahol az alkalmazás tárolja és meghívja a tényleges arcadatokat.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_end)] -->

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_end)] -->

### <a name="try-the-app"></a>Próbálja ki az alkalmazást

Nyomja meg a **Start** (Indítás) gombot a menüben az alkalmazás teszteléséhez. Amikor megnyílik az alkalmazásablak, kattintson a Bal alsó sarokban a **Tallózás** gombra. Meg kell jelennie **egy Fájlmegnyitás** párbeszédpanelnek. Jelöljön ki egy képet a fájlrendszerből, és ellenőrizze, hogy az megjelenik-e az ablakban. Ezután zárja be az alkalmazást, és lépjen tovább a következő lépésre.

![Képernyőfelvétel arcokat ábrázoló képről, amely nem lett módosítva](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Kép feltöltése és arcok észlelése

Az alkalmazás észleli az arcokat a **FaceClient.Face.DetectWithStreamAsync** metódus hívásával, amely a [REST-észlelési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API-t egy helyi lemezkép feltöltéséhez csomagolja.

Szúrja be a következő módszert a **MainWindow** osztályba, az **FacePhoto_MouseMove** módszer alá. Ez a módszer az arcattribútumok listáját határozza meg a beküldött képfájl beolvasásához és **beolvasásához.** Ezután mindkét objektumot továbbítja a **DetectWithStreamAsync** metódushíváshoz.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_uploaddetect)]

## <a name="draw-rectangles-around-faces"></a>Téglalapok rajzolása arcok köré

Ezután hozzáadja a kódot, hogy egy téglalapot rajzoljon a kép minden észlelt arca körül. A **MainWindow** osztályba szúrja be a következő **BrowseButton_Click** kódot a `FacePhoto.Source = bitmapSource` BrowseButton_Click módszer végén, a sor után. Ez a kód feltölti az észlelt arcok listáját az **UploadAndDetectFaces**hívásból. Ezután rajzol egy téglalapot az egyes lapok köré, és megjeleníti a módosított képet a főablakban.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_mid)]

## <a name="describe-the-faces"></a>Írja le az arcokat

Adja hozzá a következő módszert a **MainWindow** osztályhoz az **UploadAndDetectFaces** metódus alatt. Ez a módszer a beolvasott arcattribútumokat az arcot leíró karakterláncsá alakítja.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_facedesc)]

## <a name="display-the-face-description"></a>Az arc leírásának megjelenítése

Adja hozzá a következő kódot a **FacePhoto_MouseMove** metódushoz. Ez az eseménykezelő akkor `faceDescriptionStatusBar` jeleníti meg az arcleíró karakterláncot, amikor a kurzor egy észlelt arctéglalap fölé viszi az egérmutatót.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_mid)]

## <a name="run-the-app"></a>Az alkalmazás futtatása

Futtassa az alkalmazást, és keressen egy képet, amelyen egy arc látható. Várjon néhány másodpercet, amíg az arcfelismerési szolgáltatás válaszol. A képen lévő lapokon piros téglalapjelenik meg. Ha az egeret egy arctéglalap fölé mozgatja, az adott lap leírásának meg kell jelennie az állapotsorban.

![Képernyőfelvétel téglalappal bekeretezett arcokról](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta a Face service .NET SDK használatának alapvető folyamatát, és létrehozott egy alkalmazást a lemezképek arcának észlelésére és keretezésére. Ezután tudjon meg többet az arcfelismerés részleteiről.

> [!div class="nextstepaction"]
> [Arcok észlelése egy képen](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
