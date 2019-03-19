---
title: Beszéd használata C# SDK
titleSuffix: Azure Cognitive Services
description: A Speech Service-szel egyetlen kérésben kérhet le hanganyagokat, és adathat vissza LUIS-előrejelzési JSON-objektumokat. A cikkben egy C#-projektet fog letölteni és felhasználni a Visual Studióban arra, hogy mikrofonnal rögzítsen egy kimondott szöveget, és LUIS-előrejelzési adatokhoz jusson.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 6f775ffaf53019cc50bc38c294b4d5f40c8eca90
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076751"
---
# <a name="integrate-speech-service-with-your-language-understanding-app"></a>Beszédszolgáltatás integráció a Language Understanding alkalmazással
A [Speech Service-szel](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) egyetlen kérésben kérhet le hanganyagokat, és adathat vissza LUIS-előrejelzési JSON-objektumokat. A cikkben egy C#-projektet fog letölteni és felhasználni a Visual Studióban arra, hogy mikrofonnal rögzítsen egy kimondott szöveget, és LUIS-előrejelzési adatokhoz jusson. A projekt a Speech [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/)-csomagot használja, amelyet referenciaként már tartalmaz. 

Szüksége lesz egy ingyenes [LUIS][LUIS]-webhelyfiókra az alkalmazás importálásához.

## <a name="create-luis-endpoint-key"></a>LUIS-végpont kulcsának létrehozása
Az Azure Portalon [hozzon létre](luis-how-to-azure-subscription.md) egy **Language Understanding-** (LUIS-) kulcsot. 

## <a name="import-human-resources-luis-app"></a>A Human Resources LUIS-alkalmazás importálása
A leképezések és a kimondott szöveg ebben a cikkben vannak az emberi erőforrások LUIS érhető el az alkalmazásból a [Azure-minták](https://github.com/Azure-Samples/cognitive-services-language-understanding) GitHub-adattárban. Töltse le a [HumanResources.json](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources.json) fájlt, mentse `.json` kiterjesztéssel, és [importálja](luis-how-to-start-new-app.md#import-new-app) a LUIS-ba. 

Ez az alkalmazás az emberi erőforrások (Human Resources, HR) tárgykörébe tartozó szándékokat, entitásokat és kimondott szövegeket tartalmaz. Példák kimondott szövegekre:

|Példák kimondott szövegekre|
|--|
|Ki Kovács János felettese?|
|Kik Kovács János beosztottai?|
|Hol van az 123456 számú formanyomtatvány?|
|Kapok fizetett szabadságot?|


## <a name="add-keyphrase-prebuilt-entity"></a>Előre összeállított KeyPhrase entitás hozzáadása
Az alkalmazás importálását követően válassza az **Entities** (Entitások), majd az **Add prebuilt entity** (Előre összeállított entitás hozzáadása) lehetőséget. Adja hozzá a **KeyPhrase** entitást. A KeyPhrase entitás kinyeri a kulcstémákat a kimondott szövegekből.

## <a name="train-and-publish-the-app"></a>Az alkalmazás betanítása és közzététele
1. A LUIS-alkalmazás betanításához a jobb felső navigációs sávban kattintson a **Train** (Betanítás) gombra.

2. Válassza a jobb felső sávon a **Manage** (Kezelés), majd a bal oldali navigációs területen a **Keys and endpoints** (Kulcsok és végpontok) lehetőséget. 

3. A **Keys and endpoints** (Kulcsok és végpontok) lapon rendelje hozzá a [LUIS-végpont kulcsának létrehozása](#create-luis-endpoint-key) szakaszban létrehozott LUIS-kulcsot.

   Ezen a lapon gyűjtse be a [LUIS-végpont kulcsának létrehozása](#create-luis-endpoint-key) szakaszban létrehozott LUIS-kulcs alkalmazásazonosítóját, közzétételi régióját és előfizetés-azonosítóját. Módosítania kell a kódot, hogy a cikk későbbi szakaszaiban ezeket az értékeket használja. 
  
   Ebben a gyakorlatban **ne** használja az ingyenes indulókulcsot. A gyakorlathoz csak az Azure Portalon létrehozott **Language Understanding**-kulcs használható. 

   https://**RÉGIÓ**.api.cognitive.microsoft.com/luis/v2.0/apps/**ALKALMAZÁSAZONOSÍTÓ**?subscription-key=**LUISKULCS**&q=


4. Tegye közzé a LUIS-alkalmazást. Ehhez válassza ki a jobb felső sávon a **Publish** (Közzététel) gombot. 

## <a name="audio-device"></a>Audioeszköz
Ebben a cikkben a számítógép audioeszközét használjuk. Ez lehet egy mikrofonos fejhallgató vagy egy beépített hangeszköz. Ellenőrizze a bemeneti hangszinteket. Előfordulhat, hogy a szokottnál hangosabban kell majd beszélnie, hogy a hangeszköz észlelje a hangját. 

## <a name="download-the-luis-sample-project"></a>LUIS-mintaprojekt letöltése
 Klónozás vagy letöltés a [Azure-minták](https://github.com/Azure-Samples/cognitive-services-language-understanding) tárház. Nyissa meg a [Speech to intent (Beszédből szándék) projektet](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-speech-intent-recognition) a Visual Studióban, és állítsa vissza a NuGet-csomagokat. A VS-megoldásfájlt documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

A projekt már tartalmazza referenciaként a Speech SDK-t. 

[![Képernyőfelvétel a Visual Studio 2017 megjelenítésének Microsoft.CognitiveServices.Speech NuGet-csomag](./media/luis-tutorial-speech-to-intent/nuget-package.png "képernyőfelvétel a Visual Studio 2017 megjelenítésének Microsoft.CognitiveServices.Speech NuGet-csomag")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>A C#-kód módosítása
Nyissa meg a `Program.cs` fájlt, és módosítsa a következő változókat:

|Változó neve|Cél|
|--|--|
|LUIS_assigned_endpoint_key|A végponti URL-hez hozzárendelt előfizetői azonosító értékét adja meg a Közzététel oldalról|
|LUIS_endpoint_key_region|A végponti URL-cím első altartományát adja meg, például `westus`.|
|LUIS_app_ID|A végponti URL-cím útvonalát adja meg az **apps/** előtag után|

A `Program.cs` fájlban az emberierőforrás- (HR-) szándékok már le vannak képezve.

Hozza létre és futtassa az alkalmazást. 

## <a name="test-code-with-utterance"></a>A kód tesztelése kimondott szöveggel
Mondja a mikrofonba a következő szöveget: „Who are the approved dentists in Redmond?” („Kik a szerződéses fogorvosok Redmondban?”).

[!code-console[Command line response from spoken utterance](~/samples-luis/documentation-samples/tutorial-speech-intent-recognition/console-output.txt "Command line response from spoken utterance")]

A rendszer 85%-os megbízhatósággal megtalálta a megfelelő szándékot (**GetEmployeeBenefits**, alkalmazotti juttatások lekérése). A rendszer visszaadta a KeyPhrase entitást. 

A Speech SDK visszaadja a teljes LUIS-választ. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a HumanResources LUIS-alkalmazást. Ehhez jelölje ki az alkalmazást, majd a lista felett megjelenő helyi eszköztárban válassza a **Delete** (Törlés) lehetőséget. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.

Ne felejtse el, törölje a könyvtárat, ha elkészült, azzal a mintakóddal.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A LUIS integrálása robotokkal](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
