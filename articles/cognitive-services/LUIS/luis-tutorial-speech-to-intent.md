---
title: Beszéd C# SDK használata LUIS - Azure |} Microsoft Docs
titleSuffix: Azure
description: Használja a beszédfelismerés C# SDK minta visszaadott LUIS leképezés és entitások előrejelzéseket, és beszéljen mikrofon.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/26/2018
ms.author: v-geberr;
ms.openlocfilehash: b8a2c0dbadb0124b9250849a0260f5b34d38a5c3
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021663"
---
# <a name="integrate-speech-service"></a>Beszéd szolgáltatás integrálása
A [beszéd szolgáltatás](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) lehetővé teszi hang kapnak, és térjen vissza a LUIS előrejelzés JSON-objektumok egy kérelemhez használatát.

Ebben a cikkben töltse le, és C#-projektet a Visual Studio egy utterance mikrofon és LUIS előrejelzési adatokat fogadni. A projekt használja a beszédfelismerés [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) csomag már szerepel egy hivatkozásként. 

Ez a cikk van szüksége egy szabad [LUIS] [ LUIS] weboldal-elérési fiókot importálni az alkalmazást.

## <a name="create-luis-endpoint-key"></a>LUIS végpont-kulcs létrehozása
Az Azure-portálon [létrehozása](luis-how-to-azure-subscription.md#create-luis-endpoint-key) egy **nyelvi ismertetése** (LUIS) kulcs. 

## <a name="import-human-resources-luis-app"></a>Importálja az emberi erőforrások LUIS alkalmazás
A leképezések, és ez a cikk utterances vannak az emberi erőforrások LUIS érhetők el az alkalmazásból a [LUIS-minták](https://github.com/Microsoft/LUIS-Samples) Github-tárházban. Töltse le a [HumanResources.json](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json) fájlt menteni a *.json kiterjesztésű és [importálása](create-new-app.md#import-new-app) LUIS be azt. 

Ez az alkalmazás leképezések, entitások és az emberi erőforrások tartományhoz kapcsolódó utterances rendelkezik. Példa utterances a következők:

```
Who is John Smith's manager?
Who does John Smith manage?
Where is Form 123456?
Do I have any paid time off?
```

## <a name="add-keyphrase-prebuilt-entity"></a>Adja hozzá a KeyPhrase előre elkészített entitás
Az alkalmazás importálása, után válassza ki a **entitások**, majd **előre elkészített entitások kezelése**. Adja hozzá a **KeyPhrase** entitás. A KeyPhrase entitás kulcs témában kiolvassa a utterance a.

## <a name="train-and-publish-the-app"></a>A vonat és az alkalmazás közzététele
1. A felső, jobb oldali navigációs sávon, válassza ki a **betanítása** kell még betanítani a LUIS alkalmazás gomb.

2. Válassza ki **közzététel** a közzététel lap megnyitásához. 

3. Alján a **közzététel** lapon, adja hozzá a LUIS kulcsnak a [létrehozása LUIS végpontkulcs](#create-luis-endpoint-key) szakasz.

4. A LUIS alkalmazás közzététele kiválasztásával a **közzététel** a közzététel bővítőhely jobbra látható gombra. 

  Az a **közzététel** lapon gyűjteni az alkalmazás Azonosítóját, valamint régió és előfizetés-azonosítója a LUIS kulcsnak közzététele a [létrehozása LUIS végpontkulcs](#create-luis-endpoint-key) szakasz. Módosítania kell a kódot, és ezeket az értékeket használja a cikk későbbi részében. 

  Ezek az értékek összes szerepelnek a végponti URL-cím alján a **közzététel** létrehozott kulcs lapján. Tegye **nem** szabad alapszintű kulcs használata ehhez a gyakorlathoz. 

  https://**régió**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**? előfizetés-kulcs =**LUISKEY**& q =

## <a name="audio-device"></a>Audioeszköz
Ez a cikk a Hang-eszközt használ. a számítógépen. Lehet, amely a mikrofon vagy egy beépített hangeszköz mikrofonos. Ellenőrizze a hang bemeneti szintek megjelenítéséhez, ha hangosabb mint normális esetben tenné a eszköz által észlelt beszéd rendelkeznie kell elemre. 

## <a name="download-the-luis-sample-project"></a>Töltse le a LUIS mintaprojektet
 Klónozza, vagy töltse le a [LUIS-minták](https://github.com/Microsoft/LUIS-Samples) tárházba. Nyissa meg a [leképezési projekthez beszéd](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-speech-intent-recognition) a Visual Studio és a NuGet-csomagok helyreállításához. A Visual STUDIO megoldás fájl.\LUIS-Samples-master\documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

A beszédfelismerés SDK már egy hivatkozási dimenzióként szerepel. 

[![](./media/luis-tutorial-speech-to-intent/nuget-package.png "Képernyőfelvétel a Visual Studio 2017 megjelenítését Microsoft.CognitiveServices.Speech NuGet-csomag")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>Módosítsa a C#-kódban
Nyissa meg a **LUIS_samples.cs** fájlt, és módosítsa a következő változókat:

|Változó neve|Cél|
|--|--|
|luisSubscriptionKey|Közzététel oldalról végpont URL-előfizetés-kulcs-érték megfelel|
|luisRegion|Végpont URL-cím első altartomány felel meg|
|luisAppId|A következő végpont URL-útvonal megfelel **apps /**|

[![](./media/luis-tutorial-speech-to-intent/change-variables.png "Képernyőfelvétel a Visual Studio 2017 LUIS_samples.cs változók megjelenítésekor")](./media/luis-tutorial-speech-to-intent/change-variables.png#lightbox)

A fájl már van leképezve a HR-leképezések.

[![](./media/luis-tutorial-speech-to-intent/intents.png "Képernyőfelvétel a Visual Studio 2017 LUIS_samples.cs leképezések megjelenítése")](./media/luis-tutorial-speech-to-intent/intents.png#lightbox)

Hozza létre, és futtassa az alkalmazást. 

![Képernyőkép a parancssor futtatása](./media/luis-tutorial-speech-to-intent/cmdline-1.png)

## <a name="test-code-with-utterance"></a>Utterance kódot tesztelése
Válassza ki **8** és a mikrofon beszéljen "Ki a John Smith-kezelő".

```cmd
1. Speech recognition with microphone input.
2. Speech recognition in the specified language.
3. Speech recognition with file input.
4. Speech recognition using customized model.
5. Speech continuous recognition using events.
6. Translation with microphone input.
7. Translation with file input.
8. Speech recognition of LUIS intent.
0. Stop.
Your choice: 8
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

A megfelelő cél **GetEmployeeOrgChart**, egy 61 %-os található. A keyPhrase entitást adott vissza. 

A beszédfelismerés SDK a teljes LUIS választ ad vissza. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szükség, a LUIS emberi alkalmazás törlése. Ehhez az szükséges, válassza a három pont menü (...) az alkalmazás nevétől jobbra alkalmazáslistájában, jelölje be **törlése**. Az előugró párbeszédpanelen **Delete app?**, jelölje be **Ok**.

Ne felejtse el, törölje a LUIS-minták könyvtárat, ha befejezte a minta kód használatával.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [LUIS integrálása BOT](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website