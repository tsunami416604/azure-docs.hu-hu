---
title: C# SDK-t beszéd használata az intelligens hangfelismerési szolgáltatással
titleSuffix: Azure Cognitive Services
description: A Speech service lehetővé teszi, hogy egyetlen kérés fogadásához hang, és a LUIS-előrejelzési JSON objektumokat adjanak vissza. Ebben a cikkben töltse le, és a Visual Studióban egy C#-projektben használatával az utterance (kifejezés) mikrofon beszél, és a LUIS előrejelzési információkat kapni. A projekt használja, a beszéd NuGet-csomagot, referenciaként már tartalmazza.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: fb17e2d8c0ef1df5a6d4965730d3ddd3764d58f5
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868751"
---
# <a name="integrate-speech-service"></a>Beszédszolgáltatás integrálása
A [beszédszolgáltatás](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) lehetővé teszi, hogy egyetlen kérés fogadásához hang, és a LUIS-előrejelzési JSON objektumokat adjanak vissza. Ebben a cikkben töltse le, és a Visual Studióban egy C#-projektben használatával az utterance (kifejezés) mikrofon beszél, és a LUIS előrejelzési információkat kapni. A projekt használja, a beszéd [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) csomag már szerepel a hivatkozásként van listázva. 

Ez a cikk szüksége lesz egy ingyenes [LUIS] [ LUIS] webhely fiók annak érdekében, hogy az alkalmazás importálása.

## <a name="create-luis-endpoint-key"></a>LUIS-végpont kulcsának létrehozása
Az Azure Portalon [létrehozása](luis-how-to-azure-subscription.md#create-luis-endpoint-key) egy **Language Understanding** (LUIS) kulcsa. 

## <a name="import-human-resources-luis-app"></a>Importálja az emberi erőforrások LUIS alkalmazás
A leképezések és a kimondott szöveg ebben a cikkben vannak az emberi erőforrások LUIS érhető el az alkalmazásból a [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples) Github-adattárban. Töltse le a [HumanResources.json](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/HumanResources.json) fájlt mentse azt a `.json` bővítményt, és [importálása](luis-how-to-start-new-app.md#import-new-app) LUIS be azt. 

Az alkalmazás van, szándék fog vonatkozni, az entitások és az emberi erőforrások tartományhoz kapcsolódó kimondott szöveg. Példa utterances a következők:

|Példák kimondott szövegekre|
|--|
|Akik a John Smith manager?|
|Akik Kovács János kezelni?|
|Ha az űrlap 123456?|
|Kell fizetős bármikor?|


## <a name="add-keyphrase-prebuilt-entity"></a>Adja hozzá a KeyPhrase előre összeállított entitások
Válassza ki az alkalmazást az importálás után **entitások**, majd **előre összeállított entitások kezelése**. Adja hozzá a **KeyPhrase** entitás. A KeyPhrase entitás kulcs tárgyára kigyűjti az utterance (kifejezés).

## <a name="train-and-publish-the-app"></a>Az alkalmazás betanítása és közzététele
1. A felső, jobb oldali navigációs sávon válassza ki a **betanításához** gomb a LUIS alkalmazás betanításához.

2. Válassza ki **kezelés** sáv jobb felső sarokban, majd válassza **kulcsokat és a végpontok** a bal oldali navigációs menüben. 

3. Az a **kulcsokat és a végpontok** lapon, a LUIS kulcsnak hozzárendelése a [létrehozása LUIS végponti kulcs](#create-luis-endpoint-key) szakaszban.

  Ezen a lapon gyűjtése az Alkalmazásazonosító, régió közzétételét és előfizetés-azonosító a LUIS kulcs létrehozott a [létrehozása LUIS végponti kulcs](#create-luis-endpoint-key) szakaszban. Meg kell módosítania a kódot, ezeket az értékeket a cikk későbbi részében. 
  
  Tegye **nem** ebben a gyakorlatban az ingyenes alapszintű kulcs használatára. Csak egy **Language Understanding** ebben a gyakorlatban az Azure Portalon létrehozott kulcsot fog működni. 

  https://**régió**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**? előfizetés-kulcs =**LUISKEY**& q =


4. Kijelölésével a LUIS alkalmazás közzététele a **közzététel** sáv jobb felső sarokban található gombra. 

## <a name="audio-device"></a>Hangeszköz
Ebben a cikkben az eszköz a számítógépen. Lehet, amely a mikrofon vagy egy beépített hangeszköz mikrofonos. Ellenőrizze a megtekintéséhez, ha hangosabb általában, mintha a beszéd, az eszköz által észlelt rendelkeznie kell beszélt hang bemeneti szintek. 

## <a name="download-the-luis-sample-project"></a>Töltse le a LUIS mintaprojektet
 Klónozás vagy letöltés a [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples) tárház. Nyissa meg a [beszéd szándékának projekthez](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-speech-intent-recognition) a Visual Studióval és a NuGet-csomagok visszaállítására. A VS-megoldásfájlt.\LUIS-Samples-master\documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

A beszédfelismerés SDK már elérhető az egy rá mutató hivatkozást. 

[![](./media/luis-tutorial-speech-to-intent/nuget-package.png "Képernyőfelvétel a Visual Studio 2017 megjelenítésének Microsoft.CognitiveServices.Speech NuGet-csomag")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>A C#-kód módosítása
Nyissa meg a `Program.cs` fájlt, és módosítsa az alábbi változókat:

|Változó neve|Cél|
|--|--|
|LUIS_assigned_endpoint_key|A végpont URL-CÍMEK hozzárendelve előfizetés-kulcs-érték a közzétételi oldalon felel meg|
|LUIS_endpoint_key_region|A végpont URL-cím első együtt, például felel meg `westus`|
|LUIS_app_ID|A következő végpont URL-útvonal megfelel **apps /**|

A `Program.cs` fájlban már vannak leképezve az emberi erőforrások szándék fog vonatkozni.

Hozhat létre, és futtassa az alkalmazást. 

## <a name="test-code-with-utterance"></a>Tesztelje a kódot az utterance (kifejezés)
A mikrofon beszéljen "Kik a jóváhagyott fogorvoshoz redmondban?".

[!code-console[Command line response from spoken utterance](~/samples-luis/documentation-samples/tutorial-speech-intent-recognition/console-output.txt "Command line response from spoken utterance")]

A megfelelő leképezés **GetEmployeeBenefits**, több mint 85 %-os megbízhatósággal található. A keyPhrase entitást adott vissza. 

A beszédfelismerés SDK-t a teljes LUIS választ adja vissza. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a LUIS emberi alkalmazást. Ehhez jelölje ki az alkalmazást, majd válassza az eszközlista feletti környezetfüggő eszköztárán **törlése**. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.

Ne felejtse el törölni a LUIS-Samples-könyvtár, amikor elkészült a mintakóddal.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A ROBOT a LUIS integrálása](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website