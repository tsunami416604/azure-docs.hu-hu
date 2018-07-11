---
title: C# SDK-t beszéd használata az intelligens hangfelismerési szolgáltatással – Azure |} A Microsoft Docs
titleSuffix: Azure
description: A beszédfelismerés C# SDK-minta használatával beszéljen a mikrofon és visszaadott LUIS szándékot és entitások előrejelzés beolvasása.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/26/2018
ms.author: v-geberr;
ms.openlocfilehash: 3b225a012abe4796172327d8a723338e8de67749
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930018"
---
# <a name="integrate-speech-service"></a>Beszédszolgáltatás integrálása
A [beszédszolgáltatás](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) lehetővé teszi, hogy egyetlen kérés fogadásához hang, és a LUIS-előrejelzési JSON objektumokat adjanak vissza.

Ebben a cikkben töltse le, és a Visual Studióban egy C#-projektben használatával az utterance (kifejezés) mikrofon beszél, és a LUIS előrejelzési információkat kapni. A projekt használja, a beszéd [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) csomag már szerepel a hivatkozásként van listázva. 

Ez a cikk szüksége lesz egy ingyenes [LUIS] [ LUIS] webhely fiók annak érdekében, hogy az alkalmazás importálása.

## <a name="create-luis-endpoint-key"></a>A LUIS végponti kulcs létrehozása
Az Azure Portalon [létrehozása](luis-how-to-azure-subscription.md#create-luis-endpoint-key) egy **Language Understanding** (LUIS) kulcsa. 

## <a name="import-human-resources-luis-app"></a>Importálja az emberi erőforrások LUIS alkalmazás
A leképezések és a kimondott szöveg ebben a cikkben vannak az emberi erőforrások LUIS érhető el az alkalmazásból a [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples) Github-adattárban. Töltse le a [HumanResources.json](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json) *.json kiterjesztésű, mentse a fájlt, és [importálása](luis-how-to-start-new-app.md#import-new-app) LUIS be azt. 

Az alkalmazás van, szándék fog vonatkozni, az entitások és az emberi erőforrások tartományhoz kapcsolódó kimondott szöveg. Példa utterances a következők:

```
Who is John Smith's manager?
Who does John Smith manage?
Where is Form 123456?
Do I have any paid time off?
```

## <a name="add-keyphrase-prebuilt-entity"></a>Adja hozzá a KeyPhrase előre összeállított entitások
Válassza ki az alkalmazást az importálás után **entitások**, majd **előre összeállított entitások kezelése**. Adja hozzá a **KeyPhrase** entitás. A KeyPhrase entitás kulcs tárgyára kigyűjti az utterance (kifejezés).

## <a name="train-and-publish-the-app"></a>Betanítása és az alkalmazás közzététele
1. A felső, jobb oldali navigációs sávon válassza ki a **betanításához** gomb a LUIS alkalmazás betanításához.

2. Válassza ki **közzététel** , nyissa meg a közzétételi oldalon. 

3. Alsó részén a **közzététel** lap, adja hozzá a létrehozott LUIS-kulcsot a [létrehozása LUIS végponti kulcs](#create-luis-endpoint-key) szakaszban.

4. Kijelölésével a LUIS alkalmazás közzététele a **közzététel** a közzététel bővítőhely jobbra látható gombra. 

  Az a **közzététel** lapon gyűjtése az Alkalmazásazonosító, régió és előfizetés-azonosítója a LUIS kulcsnak közzé a [létrehozása LUIS végponti kulcs](#create-luis-endpoint-key) szakaszban. Meg kell módosítania a kódot, ezeket az értékeket a cikk későbbi részében. 

  Ezeket az értékeket minden szerepelnek a végponti URL-cím alján a **közzététel** lap a létrehozott kulcs. 
  
  Tegye **nem** ebben a gyakorlatban az ingyenes alapszintű kulcs használatára. Csak egy **Language Understanding** ebben a gyakorlatban az Azure Portalon létrehozott kulcsot fog működni. 

  https://**régió**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**? előfizetés-kulcs =**LUISKEY**& q =

## <a name="audio-device"></a>Hangeszköz
Ebben a cikkben az eszköz a számítógépen. Lehet, amely a mikrofon vagy egy beépített hangeszköz mikrofonos. Ellenőrizze a megtekintéséhez, ha hangosabb általában, mintha a beszéd, az eszköz által észlelt rendelkeznie kell beszélt hang bemeneti szintek. 

## <a name="download-the-luis-sample-project"></a>Töltse le a LUIS mintaprojektet
 Klónozás vagy letöltés a [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples) tárház. Nyissa meg a [beszéd szándékának projekthez](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-speech-intent-recognition) a Visual Studióval és a NuGet-csomagok visszaállítására. A VS-megoldásfájlt.\LUIS-Samples-master\documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

A beszédfelismerés SDK már elérhető az egy rá mutató hivatkozást. 

[![](./media/luis-tutorial-speech-to-intent/nuget-package.png "Képernyőfelvétel a Visual Studio 2017 megjelenítésének Microsoft.CognitiveServices.Speech NuGet-csomag")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>A C#-kód módosítása
Nyissa meg a **LUIS_samples.cs** fájlt, és módosítsa az alábbi változókat:

|Változó neve|Cél|
|--|--|
|luisSubscriptionKey|A közzétételi oldalon végpont URL-ekhez előfizetés-kulcs-érték felel meg|
|luisRegion|Végpont URL-cím első altartomány felel meg|
|luisAppId|A következő végpont URL-útvonal megfelel **apps /**|

[![](./media/luis-tutorial-speech-to-intent/change-variables.png "Képernyőfelvétel a Visual Studio 2017 LUIS_samples.cs változók megjelenítése")](./media/luis-tutorial-speech-to-intent/change-variables.png#lightbox)

A fájl már rendelkezik az emberi erőforrások leképezések leképezve.

[![](./media/luis-tutorial-speech-to-intent/intents.png "Képernyőfelvétel a Visual Studio 2017 LUIS_samples.cs leképezések megjelenítése")](./media/luis-tutorial-speech-to-intent/intents.png#lightbox)

Hozhat létre, és futtassa az alkalmazást. 

## <a name="test-code-with-utterance"></a>Tesztelje a kódot az utterance (kifejezés)
Válassza ki **1** , és beszéljen a mikrofon "Ki is Kovács János igazgatója".

```cmd
1. Speech recognition of LUIS intent.
0. Stop.
Your choice: 1
LUIS...
Say something...
ResultId:cc83cebc9d6040d5956880bcdc5f5a98 Status:Recognized IntentId:<GetEmployeeOrgChart> Recognized text:<Who is the manager of John Smith?> Recognized Json:{"DisplayText":"Who is the manager of John Smith?","Duration":25700000,"Offset":9200000,"RecognitionStatus":"Success"}. LanguageUnderstandingJson:{
  "query": "Who is the manager of John Smith?",
  "topScoringIntent": {
    "intent": "GetEmployeeOrgChart",
    "score": 0.617331
  },
  "entities": [
    {
      "entity": "manager of john smith",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 31
    }
  ]
}

Recognition done. Your Choice:

```

A megfelelő leképezés **GetEmployeeOrgChart**, a munkavállalók 61 %-os található. A keyPhrase entitást adott vissza. 

A beszédfelismerés SDK-t a teljes LUIS választ adja vissza. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a LUIS emberi alkalmazást. Ehhez válassza a három pontot (***...*** ) az alkalmazások listájában, jelölje be az alkalmazás nevétől jobbra látható gombra **törlése**. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.

Ne felejtse el törölni a LUIS-Samples-könyvtár, amikor elkészült a mintakóddal.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A ROBOT a LUIS integrálása](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website