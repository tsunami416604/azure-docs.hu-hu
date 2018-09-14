---
title: 'Rövid útmutató: Modell módosítása és LUIS-alkalmazás betanítása C# használatával – Azure Cognitive Services | Microsoft Docs'
description: Ebben a rövid C#-útmutatóban kimondott példaszövegeket ad egy otthonautomatizálási alkalmazáshoz, és betanítja az alkalmazást. A kimondott példaszövegek szándékhoz rendelt beszélgetésre szánt felhasználói szövegek. A szándékok kimondott példaszövegeinek megadásával megtanítja a LUIS-t arra, hogy milyen típusú felhasználói szöveg mely szándékhoz tartozik.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: fee0f9009e7ce6cef839010f68ae3487067152b9
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43770065"
---
# <a name="quickstart-change-model-using-c"></a>Rövid útmutató: Modell módosítása a C# használatával

[!include[Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>Előfeltételek

[!include[Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* Legújabb [**Visual Studio Community kiadás**](https://www.visualstudio.com/downloads/).
* Telepített C# programozási nyelv.
* [JsonFormatterPlus](https://www.nuget.org/packages/JsonFormatterPlus) és [CommandLine](https://www.nuget.org/packages/CommandLineParser/) NuGet-csomagok

[!include[Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Kimondott példaszövegek JSON-fájlja

[!include[Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>A rövid útmutató kódjának létrehozása 

A Visual Studióban hozzon létre egy új **Windows klasszikus asztali konzol** alkalmazást a .Net keretrendszer használatával. 

![Visual Studio-projekt típusa](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

### <a name="add-the-systemweb-dependency"></a>A System.Web függőség hozzáadása

A Visual Studio-projektnek szüksége van a következőre: **System.Web**. A Solution Explorer (Megoldáskezelő) ablakban kattintson a jobb gombbal a **References** (Hivatkozások) elemre, és válassza az **Add Reference** (Hivatkozás hozzáadása) lehetőséget.

![A System.Web hivatkozás hozzáadása](./media/luis-quickstart-cs-add-utterance/system.web.png)

### <a name="add-other-dependencies"></a>Más függőségek hozzáadása

A Visual Studio-projektnek szüksége van a következőkre: **JsonFormatterPlus** és **CommandLineParser**. A Solution Explorer (Megoldáskezelő) ablakban kattintson a jobb gombbal a **References** (Hivatkozások) elemre, és válassza a **Manage NuGet Packages...** (NuGet-csomagok kezelése...) lehetőséget. Keresse meg és adja hozzá a két csomagot. 

![Külső függőségek hozzáadása](./media/luis-quickstart-cs-add-utterance/add-dependencies.png)


### <a name="write-the-c-code"></a>A C#-kód megírása
A **Program.cs** fájlnak a következőkből kell állnia:

```CSharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp3
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Adja hozzá a függőségeket.

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=1-11 "Add the dependencies")]


Adja hozzá a LUIS-azonosítókat és -sztringeket a **Program** osztályhoz.

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=19-30&dedent=8 "Add the LUIS IDs and strings")]

Adjon egy osztályt a **Program** osztályhoz a parancssori paraméterek kezelése céljából.

   [!code-csharp[Add class to manage command line parameters.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=32-46 "Add class to manage command-line parameters.")]

Adja hozzá a GET kérelemmetódust a **Program** osztályhoz.

   [!code-csharp[Add the GET request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=49-59 "Add the GET request.")]


Adja hozzá a POST kérelemmetódust a **Program** osztályhoz. 

   [!code-csharp[Add the POST request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=60-76 "Add the POST request.")]

Adjon hozzá kimondott példaszövegeket a fájlmetódusból a **Program** osztályhoz.

   [!code-csharp[Add example utterances from file.
](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=77-86 "Add example utterances from file.
")]

Miután alkalmazta a módosításokat a modellre, tanítsa be a modellt. Adja hozzá a metódust a **Program** osztályhoz.

   [!code-csharp[After the changes are applied to the model, train the model.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=87-96 "After the changes are applied to the model, train the model.")]

Előfordulhat, hogy a képzés nem fejeződik be azonnal. Az állapot ellenőrzésével győződhet meg arról, hogy a képzés befejeződött. Adja hozzá a metódust a **Program** osztályhoz.

   [!code-csharp[Training may not complete immediately, check status to verify training is complete.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=97-103 "Training may not complete immediately, check status to verify training is complete.")]

Adja hozzá a fő kódot a parancssori argumentumok kezeléséhez. Adja hozzá a metódust a **Program** osztályhoz.

   [!code-csharp[To manage command line arguments, add the main code.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=104-137 "To manage command-line arguments, add the main code.")]

### <a name="copy-utterancesjson-to-output-directory"></a>Az utterances.json másolása a kimeneti könyvtárba

A Solution Explorer (Megoldáskezelő) ablakban kattintson a jobb gombbal az `utterances.json` fájlra, és válassza a **Properties** (Tulajdonságok) lehetőséget. A tulajdonságok ablakban jelölje meg a `Content` **Build Action** (Létrehozási művelet) elemét és a `Copy Always` **Copy to Output Directory** (Másolás a kimeneti könyvtárba) elemét.  

![A JSON-fájl megjelölése tartalomként](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="build-code"></a>Kód buildelése

Buildelje a kódot a Visual Studióban. 

## <a name="run-code"></a>Kód futtatása

A projekt /bin/Debug könyvtárában futtassa az alkalmazást egy parancssorból. 

```CMD
ConsoleApp\bin\Debug> ConsoleApp1.exe --add utterances.json --train --status
```

A parancssor megjeleníti a kimondott szövegek hozzáadása API meghívásának eredményét. 

[!include[Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Miután végzett a rövid útmutatóval, távolítsa el a rövid útmutatóban létrehozott összes fájlt. 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [LUIS-alkalmazások felépítése programozott módon](luis-tutorial-node-import-utterances-csv.md) 