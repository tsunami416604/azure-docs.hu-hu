---
title: Kognitív szolgáltatások és gépi tanulás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogy az Azure Cognitive Services hogyan illik a gépi tanulással kapcsolatos egyéb Azure-ajánlatokhoz.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: diberry
ms.openlocfilehash: cde505e4c95de9b9693a0e9d260d7fa84f3e905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75531479"
---
# <a name="cognitive-services-and-machine-learning"></a>Cognitive Services és gépi tanulás

A Cognitive Services gépi tanulási képességekkel oldhatja meg az általános problémákat, például az érzelmi érzelmek szövegének elemzését vagy a képek elemzését az objektumok vagy arcok felismeréséhez. Ezek nek a szolgáltatásoknak a használatához nincs szükség speciális gépi tanulásra vagy adatelemzési ismeretekre. 

[A Cognitive Services](welcome.md) szolgáltatások egy olyan szolgáltatáscsoport, amely különböző, általános előrejelzési képességeket támogat. A szolgáltatások különböző kategóriákba sorolhatók, hogy segítsen megtalálni a megfelelő szolgáltatást. 

|Szolgáltatás kategória|Cél|
|--|--|
|[Döntés](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|Olyan alkalmazásokat hozhat létre, amelyek javaslatokat jelenítenek meg a megalapozott és hatékony döntéshozatalhoz.|
|[Nyelv](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|Engedélyezheti alkalmazásai számára a természetes nyelv előre beépített szkriptekkel történő feldolgozását, kiértékelheti a hangulatot, valamint megtudhatja, hogyan ismerheti fel, hogy mit szeretnének a felhasználók.|
|[Keresés](https://azure.microsoft.com/services/cognitive-services/directory/search/)|A Bing Search API-jait az alkalmazásaihoz adva több milliárd weboldalt, képet, videót és hírt fésülhet át egyetlen API-hívással.|
|[Beszéd](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|A beszédet szöveggé, a szöveget pedig természetesen hangzó beszéddé alakíthatja. Egyik nyelvtől a másikra fordíthat, valamint engedélyezheti a beszélő ellenőrzését és felismerését.|
|[Vizuális elemek](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|Felismerheti, azonosíthatja, feliratozhatja, indexelheti és moderálhatja képeit, videóit és digitális szabadkézi tartalmait.|
||||

A Cognitive Services használata:

* Használhat általános megoldást.
* A megoldás elérése programozási REST API-ból vagy SDK-ból. 

Használjon másik gépi tanulási megoldást, ha:

* Ki kell választani az algoritmust, és nagyon konkrét adatokat kell betanítania.

## <a name="what-is-machine-learning"></a>Mit jelent a gépi tanulás funkció?

A gépi tanulás egy olyan koncepció, ahol adatokat és egy algoritmust hoz létre egy adott igény megoldásához. Az adatok és az algoritmus betanítása után a kimenet egy olyan modell, amely et újra használhat különböző adatokkal. A betanított modell az új adatok alapján nyújt elemzéseket. 

A gépi tanulási rendszer létrehozásának folyamata a gépi tanulás vagy az adattudomány bizonyos ismeretére van szükség.

A gépi tanulás az [Azure Machine Learning (AML) termékeiés szolgáltatásai](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure/machine-learning/studio/context/ml-context)használatával érhető el.

## <a name="what-is-a-cognitive-service"></a>Mi az a kognitív szolgáltatás?

A Cognitive Service egy gépi tanulási megoldás összetevőinek egy részét vagy egészét biztosítja: adatokat, algoritmust és betanított modellt. Ezek a szolgáltatások célja, hogy általános ismereteket az adatokkal kapcsolatban anélkül, hogy a gépi tanulás vagy az adattudomány. Ezek a szolgáltatások rest API(k) és nyelvalapú SDK-k egyaránt biztosítják. Ennek eredményeképpen a szolgáltatások használatához programozási nyelvtudással kell rendelkeznie.

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>Hogyan hasonlít a Cognitive Services és az Azure Machine Learning (AML)?

Mindkettőnek megvan a végső célja a mesterséges intelligencia (AI) alkalmazása az üzleti műveletek javítása érdekében, bár az, hogy ezek hogyan biztosítják ezt a megfelelő ajánlatokban, más. 

A célközönségek általában mások:

* A Cognitive Services gépi tanulási tapasztalattal nem rendelkező fejlesztők számára készült.
* Az Azure Machine Learning adatszakértők számára készült. 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>Miben különbözik a cognitive Service a gépi tanulástól?

A Cognitive Service egy betanított modellt biztosít az Ön számára. Ez összehozza az adatokat és egy algoritmust, amely egy REST API(ok)ból vagy SDK-ból érhető el. Ezt a szolgáltatást a forgatókönyvtől függően perceken belül megvalósíthatja.  A Cognitive Service általános problémákra adott válaszokat, például a szövegben lévő kulcskifejezésekre vagy a képek elemazonosító azonosítására. 

A gépi tanulás olyan folyamat, amely általában hosszabb ideig tart a sikeres megvalósításhoz. Ezt az időt az adatgyűjtésre, a tisztításra, az átalakításra, az algoritmusok kiválasztására, a modellbetanításra és a telepítésre fordítják, hogy a Cognitive Service által biztosított funkciók azonos szintjére jussanak. A gépi tanulás, lehetőség van arra, hogy választ adjon a magasan specializált és / vagy speciális problémákra. A gépi tanulási problémák megkövetelik a vizsgált probléma konkrét tárgyának és adatainak ismeretségét, valamint az adatelemzésterén szerzett szakértelmet.

## <a name="what-kind-of-data-do-you-have"></a>Milyen típusú adatai vannak?

Cognitive Services szolgáltatások csoportjaként megkövetelheti, hogy nincs, néhány, vagy az összes egyéni adatok a betanított modell. 

### <a name="no-additional-training-data-required"></a>Nincs szükség további betanítási adatokra

A teljesen betanított modellt biztosító szolgáltatások _fekete dobozként_kezelhetők . Nem kell tudnia, hogyan működnek, vagy milyen adatokat használtak fel a betanításukhoz. Az adatokegy teljesen betanított modell, hogy egy előrejelzést. 

### <a name="some-or-all-training-data-required"></a>Néhány vagy az összes szükséges betanítási adat

Egyes szolgáltatások lehetővé teszik, hogy a saját adatait, majd a modell betanítása. Ez lehetővé teszi, hogy a modell tágítsa a szolgáltatás adatai és algoritmusa segítségével a saját adatait. A kimenet megfelel az igényeinek. Amikor saját adatokat hoz, előfordulhat, hogy az adatokat a szolgáltatásra jellemző módon kell címkéznie. Ha például egy modellt tanít a virágok azonosítására, akkor a modell betanításához megadhat egy katalógust a virágképekről a virág helyével együtt. 

Egy szolgáltatás _lehetővé_ teheti, hogy adatokat szolgáltasson saját adatainak javítása érdekében. Előfordulhat, hogy egy szolgáltatás _tól_ adatokat kell megadnia. 

### <a name="real-time-or-near-real-time-data-required"></a>Valós idejű vagy közel valós idejű adatok szükségesek

Egy szolgáltatás nak szüksége lehet valós idejű vagy közel valós idejű adatokra egy hatékony modell létrehozásához. Ezek a szolgáltatások jelentős mennyiségű modelladatot dolgoznak fel. 

## <a name="service-requirements-for-the-data-model"></a>Az adatmodell szolgáltatási követelményei

A következő adatok kategorizálják az egyes szolgáltatások, amely típusú adatokat engedélyez, vagy szükséges.

|Kognitív szolgáltatás|Nincs szükség betanítási adatokra|Ön megad néhány vagy az összes betanítási adatot|Valós idejű vagy közel valós idejű adatgyűjtés|
|--|--|--|--|
|[Anomaly Detector](./Anomaly-Detector/overview.md)|x|x|x|
|Bing kereső |x|||
|[Számítógépes látás](./Computer-vision/Home.md)|x|||
|[Tartalommoderátor](./Content-Moderator/overview.md)|x||x|
|[Custom Vision](./Custom-Vision-Service/home.md)||x||
|[Arcfelismerés](./Face/Overview.md)|x|x||
|[Form Recognizer](./form-recognizer/overview.md)||x||
|[Modern olvasó](./immersive-reader/overview.md)|x|||
|[Ink Recognizer](./Ink-recognizer/overview.md)|x|x||
|[Language Understanding (LUIS)](./LUIS/what-is-luis.md)||x||
|[Personalizer](./personalizer/what-is-personalizer.md)|x*|x*|x|
|[QnA Maker](./QnAMaker/Overview/overview.md)||x||
|[Hangszóró-felismerő](./speaker-recognition/home.md)||x||
|[Beszéd szövegfelolvasás (TTS)](speech-service/text-to-speech.md)|x|x||
|[Beszédbeszéd-szöveg (STT)](speech-service/speech-to-text.md)|x|x||
|[Beszédfordítás](speech-service/speech-translation.md)|x|||
|[Szövegelemzés](./text-analytics/overview.md)|x|||
|[Fordító szövege](./translator/translator-info-overview.md)|x|||
|[Fordító szöveg - egyéni fordító](./translator/custom-translator/overview.md)||x||

*A Personalizer-nak csak a szolgáltatás által gyűjtött (valós idejű) képzési adatokra van szüksége az irányelv és az adatok kiértékeléséhez. A Personalizer-nek nincs szüksége nagy előzményadatkészletekre az előzetes vagy kötegelt képzéshez. 

## <a name="where-can-you-use-cognitive-services"></a>Hol használhatja a Cognitive Services szolgáltatást?
 
A szolgáltatások minden olyan alkalmazásban használatosak, amely REST API-kat vagy SDK-hívásokat kezdeményezhet. Példák az alkalmazások közé weboldalak, botok, virtuális vagy vegyes valóság, asztali és mobil alkalmazások. 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Hogyan kapcsolódik az Azure Cognitive Search a Cognitive Serviceshez?

[Az Azure Cognitive Search](../search/search-what-is-azure-search.md) egy különálló felhőalapú keresési szolgáltatás, amely a Cognitive Services segítségével adja hozzá a kép- és természetes nyelvi feldolgozást a számítási feladatok indexeléséhez. A Cognitive Services az Azure Cognitive Search szolgáltatásban érhető el az egyes [API-kat](../search/cognitive-search-predefined-skills.md) burkoló beépített képességeken keresztül. Használhat egy szabad erőforrást a forgatókönyvekhez, de tervezze meg a [számlázható erőforrás](../search/cognitive-search-attach-cognitive-services.md) létrehozását és csatolását a nagyobb kötetekhez.

## <a name="how-can-you-use-cognitive-services"></a>Hogyan használhatja a Cognitive Services szolgáltatást?

Minden szolgáltatás információt nyújt az adatokról. A szolgáltatásokat kombinálhatja olyan megoldások láncolásával, mint a beszéd (hang) szöveggé alakítása, a szöveg sok nyelvre történő fordítása, majd a lefordított nyelvek használata a tudásbázisból történő válaszok hozásához. Míg a Cognitive Services használható intelligens megoldások létrehozása a saját, ők is kombinálhatók a hagyományos gépi tanulási projektek modellek kiegészítésére vagy felgyorsítja a fejlesztési folyamatot. 

Cognitive Services, amely más gépi tanulási eszközök exportált modelljeit biztosítja:

|Kognitív szolgáltatás|Modelladatai|
|--|--|
|[Custom Vision](./custom-vision-service/home.md)|[Exportálás](./Custom-Vision-Service/export-model-python.md) Az Android Tensorflow, az iOS11-es CoreML, az ONNX for Windows ML rendszerhez|

## <a name="learn-more"></a>Részletek

* [Architecture Guide - Melyek a Microsoft gépi tanulási termékei?](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [Gépi tanulás - Bevezetés a mélytanulásba és a gépi tanulásba](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>További lépések

* Hozza létre cognitive service-fiókját az [Azure Portalon](cognitive-services-apis-create-account.md) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)segítségével.
* Ismerje meg, hogyan [hitelesítheti magát](authentication.md) egy cognitive Service.
* Diagnosztikai [naplózás](diagnostic-logging.md) használata a probléma azonosításához és hibakereséshez. 
* Kognitív szolgáltatás üzembe helyezése egy [Docker-tárolóban.](cognitive-services-container-support.md)
* Tartsa naprakészen a [szolgáltatásfrissítéseit.](https://azure.microsoft.com/updates/?product=cognitive-services)
