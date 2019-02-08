---
title: 'Oktatóanyag: Észlelheti és face adatok megjelenítése a képet a .NET SDK használatával'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban létrehozhat egy Windows-alkalmazást, amely a Face API-t használja és keret arcok a képen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 11/05/2018
ms.author: pafarley
ms.openlocfilehash: ff3063193fa2a8f71f441aa64c8835fd7528ff4e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882624"
---
# <a name="tutorial-create-a-wpf-app-to-display-face-data-in-an-image"></a>Oktatóanyag: Face adatok megjelenítéséhez a képet a WPF-alkalmazás létrehozása

Ebben az oktatóanyagban, megtudhatja, hogyan arcok észlelése a képet, és majd az adatok szerepelnek a felhasználói felületen keresztül a .NET ügyféloldali SDK-t, az Azure Face API használatával. Egy egyszerű, arcokat észleli, minden kerettel megrajzolja, és megjeleníti a face leírását az állapotsorban Windows Presentation Framework (WPF) alkalmazást fog létrehozni. 

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> - WPF-alkalmazás létrehozása
> - A Face API-ügyfélkódtárát telepítése
> - Az ügyfélkódtár használata a képeken lévő arcok észleléséhez
> - Keret rajzolása minden észlelt arc köré
> - A kijelölt face leírását megjelenítése az állapotsor

![Képernyőfelvétel téglalappal bekeretezett arcokról](../Images/getting-started-cs-detected.png)

A teljes minta kódja megtalálható a [Cognitive Face #-példában](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) tárházban a Githubon.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt. 


## <a name="prerequisites"></a>Előfeltételek

- A Face API előfizetési kulcs. Megjelenik a származó ingyenes próba-előfizetését kulcsok [próbálja meg a Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Másik lehetőségként kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Face API szolgáltatás és a kulcs beszerzése.
- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.

## <a name="create-the-visual-studio-project"></a>A Visual Studio-projekt létrehozása

Kövesse az alábbi lépéseket egy új WPF-alkalmazás projekt létrehozásához.

1. A Visual Studióban nyissa meg az új projekt párbeszédpanel. Bontsa ki a **telepített**, majd **Visual C#** , majd **WPF App (.NET Framework)**.
1. Adja a **FaceTutorial** nevet az alkalmazásnak, majd kattintson az **OK** gombra.
1. Szerezze be a szükséges NuGet-csomagokat. Kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza ki **NuGet-csomagok kezelése**; majd keresse meg és telepítse a következő csomagot:
    - Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview

## <a name="add-the-initial-code"></a>Kezdeti kód hozzáadása

Ebben a szakaszban az alapvető keretét, a face-specifikus szolgáltatások nélkül az alkalmazás fogja hozzáadni.

### <a name="create-the-ui"></a>A felhasználói felület létrehozása

Nyissa meg *MainWindow.xaml* , és cserélje ki annak tartalmát az alábbira&mdash;Ez létrehozza a felhasználói felület ablakot. Vegye figyelembe, hogy `FacePhoto_MouseMove` és `BrowseButton_Click` eseménykezelőket, amelyek később határoz vannak.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?range=1-18)]

### <a name="create-the-main-class"></a>Hozzon létre a fő osztályban

Nyissa meg *MainWindow.xaml.cs* , és adja hozzá az ügyfél könyvtár névtereket, és egyéb szükséges névtereket. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=1-12)]

Ezután helyezze be a következő kódot a **MainWindow** osztály. Ez létrehoz egy **FaceClient** példány használatával az előfizetési kulcsot, amelynek meg kell adnia a saját maga. Meg kell adnia a régió karakterláncot `faceEndpoint` a megfelelő régióba az előfizetéséhez (lásd: a [Face API-dokumentumok](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) minden régióban végpontok listáját).

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=18-46)]

Ezután illessze be az alábbi kódot a **MainWindow** metódust.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=50-61)]

Végül adja hozzá a **BrowseButton_Click** és **FacePhoto_MouseMove** az osztály metódusokat. Ezek felelnek meg az eseménykezelőket, deklarált *MainWindow.xaml*. A **BrowseButton_Click** módszer létrehoz egy **OpenFileDialog**, amely lehetővé teszi, hogy a felhasználó számára egy .jpg képet. Ezután megjeleníti a lemezképet a fő ablakban. A fennmaradó kódját beszúrja **BrowseButton_Click** és **FacePhoto_MouseMove** a későbbi lépésekben. Azt is vegye figyelembe a `faceList` referencia&mdash;listáját **DetectedFace** objektumokat. Ez a, amelyben az alkalmazás fogja tárolni és hívja meg a tényleges arcfelismerési adatokat.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=64-90,146)]

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=148-150,187)]

### <a name="try-the-app"></a>Próbálja ki az alkalmazást

Nyomja meg a **Start** (Indítás) gombot a menüben az alkalmazás teszteléséhez. Amikor az alkalmazás ablaka, kattintson az **Tallózás** bal alsó sarkában található. A **fájl megnyitása** párbeszédpanelen meg kell jelennie. Kiválaszthat egy képet, a fájlrendszer, és győződjön meg arról, hogy az ablakban megjelenik. Ezt követően zárja be az alkalmazást, és folytassa a következő lépéssel.

![Képernyőfelvétel arcokat ábrázoló képről, amely nem lett módosítva](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Arcok észlelése és a Rendszerkép feltöltése

Az alkalmazás meghívásával arcokat észleli a **FaceClient.Face.DetectWithStreamAsync** metódussal, amely becsomagolja a [hibakeresés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) feltöltenek egy helyi lemezképet REST API-val.

Helyezze be a következő metódust a **MainWindow** osztályhoz, az alábbiakban a **FacePhoto_MouseMove** metódust. Ez meghatározása arctulajdonságok lekéréséhez listáját, és beolvassa a beküldött képfájl be egy **Stream**. Ezután átadja mindkét ezek az objektumok a **DetectWithStreamAsync** metódus hívása.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=189-226)]

## <a name="draw-rectangles-around-faces"></a>Arcok téglalapokat rajzolhat

Ezután hozzáadja a kódot, amellyel rajzoljon egy téglalapot köré minden észlelt arcok a képen. Az a **MainWindow** osztály, az alábbi kód beszúrása a végén a **BrowseButton_Click** metódust, miután a `FacePhoto.Source = bitmapSource` sor. Ez kitölti a hívást észlelt arcokat listáját **UploadAndDetectFaces**. Ezután négyszögbe minden körül, és a módosított lemezképet a fő ablakban jeleníti meg.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=92-145)]

## <a name="describe-the-faces"></a>Az arcok leírása

Adja hozzá a következő metódust a **MainWindow** osztályhoz, az alábbiakban a **UploadAndDetectFaces** metódust. A lekérdezett arc attribútumok alakítja a face leíró karakterlánc.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=228-286)]

## <a name="display-the-face-description"></a>Az arc leírásának megjelenítése

Adja hozzá a következő kódot a **FacePhoto_MouseMove** metódust. Az eseménykezelő jeleníti meg a face leíró karakterlánc `faceDescriptionStatusBar` amikor a a kurzorral rámutat egy észlelt négyszög meghatározása.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=151-186)]


## <a name="run-the-app"></a>Az alkalmazás futtatása

Futtassa az alkalmazást, és keressen egy képet, amelyen egy arc látható. Várjon néhány másodpercet, amíg az arcfelismerési szolgáltatás válaszol. Az egyes az arcok a képen piros téglalap megjelennie. Ha Ön vigye az egérmutatót egy négyszög meghatározása, leírását, amely meg kell jelennie az állapotsorban.

![Képernyőfelvétel téglalappal bekeretezett arcokról](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte az alapvető folyamat a Face szolgáltatás .NET SDK-t, és létrehozott egy alkalmazást, és alkalmas keretet biztosítanak az arcok a képen. Ezután további információ az arcfelismerés részleteit.

> [!div class="nextstepaction"]
> [Arcok észlelése egy képen](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
