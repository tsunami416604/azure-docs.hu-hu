---
title: Mi az a beszédszolgáltatás?
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás a beszéd-szöveg, a szöveg-beszéd és a beszéd fordításának egységesítése egyetlen Azure-előfizetésbe. Beszédet adhat hozzá alkalmazásaihoz, eszközeihez és eszközeihez a Speech SDK, a Speech Devices SDK vagy a REST API-k használatával.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 08/20/2020
ms.author: trbye
ms.openlocfilehash: cc29eb959876a0c9c6f8c8e5dee2d18aaa5443ac
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88825355"
---
# <a name="what-is-the-speech-service"></a>Mi az a beszédszolgáltatás?

A beszédfelismerési szolgáltatás a beszéd-szöveg, a szöveg és a beszéd egyesítése, valamint a beszédfelismerés egyetlen Azure-előfizetésbe való fordítása. A Speech [parancssori](spx-overview.md)felülettel, a Speech [SDK](speech-sdk-reference.md)-val, a Speech [Device SDK](https://aka.ms/sdsdk-quickstart)-val, a [Speech Studióval](https://speech.microsoft.com/)és a [REST API](rest-apis.md)-kkal egyszerűen elvégezheti az alkalmazások, eszközök és eszközök használatát.

> [!IMPORTANT]
> A Speech szolgáltatás leváltotta Bing Speech API és Translator Speech. Tekintse át az áttelepítési utasítások _> áttelepítési_ útmutatókat.

A következő funkciók a Speech Service részét képezik. A táblázatban található hivatkozásokat követve további információkat tudhat meg az egyes szolgáltatásokhoz tartozó általános használati esetekről, vagy böngészhet az API-referenciában.

| Szolgáltatás | Szolgáltatás | Leírás | SDK | REST |
|---------|---------|-------------|-----|------|
| [Beszéd – szöveg](speech-to-text.md) | Valós idejű beszéd – szöveg | A beszéd-szöveg szöveggé alakítja át vagy lefordítja a hangadatfolyamokat vagy helyi fájlokat valós időben, hogy az alkalmazások, eszközök vagy eszközök képesek legyenek a felhasználásra vagy a megjelenítésre. A beszéd-szöveg és a [Language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/) használatával származtatott beszédből származó felhasználói leképezéseket származtathat, és hangparancsokat alkalmazhat. | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Batch – beszéd – szöveg](batch-transcription.md) | A Batch-beszédfelismerés lehetővé teszi, hogy az Azure Blob Storageban tárolt nagy mennyiségű hangalapú beszéd-szöveg átírása aszinkron módon történjen. A beszédfelismerési hang szövegre való konvertálása mellett a Batch beszéd-szöveg is lehetővé teszi a diarization és a hangulat-elemzést. | Nem | [Igen](https://westus.cris.ai/swagger/ui/index) |
| | [Több eszközre kiterjedő beszélgetés](multi-device-conversation.md) | Több eszköz vagy ügyfél csatlakoztatása egy beszélgetésben beszéd-vagy szöveges üzenetek küldéséhez egyszerű támogatással a transzkripció és a fordítás számára| Igen | Nem |
| | [Beszélgetés átirata](conversation-transcription-service.md) | Lehetővé teszi a valós idejű beszédfelismerést, a hangszórók azonosítását és a diarization. A személyes értekezletek átírása tökéletes választás a hangszórók megkülönböztetésére. | Igen | Nem |
| | [Custom Speech modellek létrehozása](#customize-your-speech-experience) | Ha egy egyedi környezetben beszéd-szövegeket használ az elismeréshez és az átíráshoz, létrehozhat és betaníthat egyéni akusztikai, nyelvi és kiejtési modelleket a környezeti zajok vagy az iparági specifikus szókincsek kezeléséhez. | Nem | [Igen](https://westus.cris.ai/swagger/ui/index) |
| [Szöveg – beszéd](text-to-speech.md) | Szövegfelolvasás | A szöveg és a beszéd szöveggé alakítja át a bemeneti szöveget az emberi környezetbe, például a [Speech szintézis Markup Language (SSML)](speech-synthesis-markup.md)használatával. A standard hangok és a neurális hangok közül választhat (lásd a [nyelvi támogatást](language-support.md)). | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Egyéni hangok létrehozása](#customize-your-speech-experience) | Egyedi hangbetűkészletek létrehozása a márka vagy a termék számára. | Nem | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Speech Translation](speech-translation.md) | Beszédfordítás | A beszédfelismerés valós idejű, többnyelvű fordítást tesz lehetővé alkalmazásaihoz, eszközeihez és eszközeihez. Használja ezt a szolgáltatást beszéd – beszéd és beszéd – szöveg fordításához. | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nem |
| [Hangvezérelt asszisztensek](voice-assistants.md) | Hangvezérelt asszisztensek | A beszédfelismerési szolgáltatást használó hangsegédek lehetővé teszik a fejlesztők számára, hogy természetes, emberi jellegű társalgási felületet hozzanak létre alkalmazásaikban és tapasztalataikban. A hangsegéd szolgáltatás gyors, megbízható interakciót biztosít egy eszköz és egy asszisztens implementáció között, amely a bot Framework közvetlen vonalas vagy integrált egyéni parancsait (előzetes verzió) használja a feladatok befejezéséhez. | [Igen](voice-assistants.md) | Nem |
| [Speaker Recognition](speaker-recognition-overview.md) | A hangszórók ellenőrzése & azonosítása | A Speaker Recognition szolgáltatás olyan algoritmusokat biztosít, amelyek egyedi hangtulajdonságokkal rendelkeznek a hangszórók ellenőrzéséhez és azonosításához. Speaker Recognition a "ki beszél?" kérdésre válaszol. | Igen | [Igen](https://docs.microsoft.com/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service-for-free"></a>Próbálja ki ingyenesen a Beszédszolgáltatást

A következő lépésekhez egy Microsoft-fiók és egy Azure-fiókra van szükség. Ha nem rendelkezik Microsoft-fiókval, a [Microsoft-fiók portálon](https://account.microsoft.com/account)ingyenesen regisztrálhat. Válassza a **Bejelentkezés Microsoft-fiókkal** lehetőséget, majd amikor a rendszer kéri a bejelentkezést, válassza **a Microsoft-fiók létrehozása**lehetőséget. Az új Microsoft-fiók létrehozásához és ellenőrzéséhez kövesse az alábbi lépéseket.

Ha Microsoft-fiók, nyissa meg az [Azure regisztrációs oldalát](https://azure.microsoft.com/free/ai/), válassza az **ingyenes indítás**lehetőséget, és hozzon létre egy új Azure-fiókot egy Microsoft-fiók használatával.

> [!NOTE]
> A beszédfelismerési szolgáltatás két szolgáltatási szintet tartalmaz: ingyenes és előfizetést, amelyek eltérő korlátozásokkal és előnyökkel rendelkeznek. Ha regisztrál egy ingyenes Azure-fiókra, az $200-as szolgáltatási Kredittel rendelkezik, amely a fizetős szolgáltatások előfizetéséhez tartozik, és legfeljebb 30 napig érvényes.
>
> Ha az ingyenes, kis mennyiségű Speech Service-szintet használja, akkor az ingyenes próbaverzió vagy a szolgáltatási jóváírás lejárta után is megtarthatja ezt az ingyenes előfizetést.
>
> További információ: [Cognitive Services díjszabása-Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-azure-resource"></a>Az Azure-erőforrás létrehozása

A Speech Service-erőforrások (ingyenes vagy fizetős szintek) hozzáadása az Azure-fiókhoz:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a Microsoft-fiókjával.

1. Válassza az **erőforrás létrehozása** lehetőséget a portál bal felső részén. Ha nem látja az **erőforrás létrehozása**lehetőséget, a képernyő bal felső sarkában található összecsukott menü kiválasztásával bármikor megkeresheti.

1. Az **új** ablakban írja be a "Speech" kifejezést a keresőmezőbe, majd nyomja le az ENTER billentyűt.

1. A keresési eredmények között válassza a **Speech (beszéd**) lehetőséget.

   ![beszédfelismerési találatok](media/index/speech-search.png)

1. Válassza a **Létrehozás**lehetőséget, majd:

   - Adjon egyedi nevet az új erőforrásnak. A név segít különbséget tenni az ugyanahhoz a szolgáltatáshoz kötött több előfizetés között.
   - Válassza ki azt az Azure-előfizetést, amelyhez az új erőforrás társítva van, hogy megtudja, hogyan történik a díjak számlázása.
   - Válassza ki azt a [régiót](regions.md) , ahol az erőforrást használni szeretné.
   - Válassza az ingyenes (F0) vagy a fizetős (S0) árképzési szintet. Az egyes szintek díjszabásával és használati kvótákkal kapcsolatos teljes információkért válassza a **teljes díjszabás**megjelenítése lehetőséget.
   - Hozzon létre egy új erőforráscsoportot ehhez a beszédfelismerési előfizetéshez, vagy rendelje hozzá az előfizetést egy meglévő erőforráscsoporthoz. Az erőforráscsoportok segítségével megőrizheti különböző Azure-előfizetéseit.
   - Kattintson a **Létrehozás** gombra. Ezzel áttekintheti a központi telepítés áttekintését és megjeleníti az üzembe helyezési folyamat üzeneteit.

> [!NOTE]
> Korlátlan számú standard szintű előfizetést hozhat létre egy vagy több régióban. Azonban csak egy ingyenes rétegbeli előfizetést hozhat létre. A 7 napig használatban lévő, ingyenes szinten lévő központi telepítéseket a rendszer automatikusan leszereli.

Az új beszédfelismerési erőforrás üzembe helyezése néhány percet vesz igénybe. Az üzembe helyezés befejezése után válassza az **erőforráshoz való ugrás** lehetőséget, majd a bal oldali navigációs panelen válassza a **kulcsok** lehetőséget a beszédfelismerési szolgáltatás előfizetési kulcsai megjelenítéséhez. Minden előfizetés két kulccsal rendelkezik; az alkalmazásban bármelyik kulcsot használhatja. Ha gyorsan szeretne másolni/beilleszteni egy kulcsot a Kódszerkesztő vagy más helyre, válassza az egyes kulcsok melletti másolás gombot, a Windowst a vágólap tartalmának a kívánt helyre való beillesztéséhez.

> [!IMPORTANT]
> Ezek az előfizetési kulcsok hozzáférnek a kognitív szolgáltatás API-hoz. Ne ossza meg a kulcsokat. Biztonságos tárolás – például Azure Key Vault használatával. Javasoljuk továbbá, hogy rendszeresen újragenerálja ezeket a kulcsokat. API-hívások létrehozásához csak egy kulcs szükséges. Az első kulcs újragenerálásakor a második kulcsot használhatja a szolgáltatás folyamatos eléréséhez.

## <a name="complete-a-quickstart"></a>Bevezetés befejezése

A legnépszerűbb programozási nyelveken olyan gyors útmutatókat találunk, amelyek az alapszintű tervezési mintákat tanítják, és kevesebb, mint 10 perc alatt futtatják a kódot. Az egyes szolgáltatásokra vonatkozó rövid útmutató az alábbi listában található.

* [Beszéd – szöveg – gyors útmutató](speech-to-text-basics.md)
* [Szövegfelolvasás – gyorsútmutató](get-started-text-to-speech.md)
* [Beszéd fordítási útmutató](speech-translation-basics.md)
* [Szándék-felismerés – rövid útmutató](quickstarts/intent-recognition.md)
* [A hangszórók felismerésének rövid útmutatója](speaker-recognition-basics.md)

A beszédfelismerési szolgáltatás használatának megkezdése után próbálja ki az oktatóanyagokat, amelyek bemutatják, Hogyan oldhatók meg a különböző forgatókönyvek.

- [Oktatóanyag: beszédfelismerési szándékok felismerése a Speech SDK és a LUIS, C használatával #](how-to-recognize-intents-from-speech-csharp.md)
- [Oktatóanyag: hang engedélyezése a robotnak a Speech SDK-val, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Oktatóanyag: a szöveg fordítását, a hangulat elemzését és a fordítás szövegét beszédre, REST-re alakító lombik-alkalmazás létrehozása](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Mintakód letöltése

Mintakód elérhető a GitHubon a Speech Service-hez. Ezek a minták olyan gyakori forgatókönyveket érintenek, mint például a hang fájlból vagy streamből való olvasása, a folyamatos és az egyszeri felvétel felismerése, valamint az egyéni modellek használata. Az alábbi hivatkozásokkal megtekintheti az SDK-t és a REST-mintákat:

- [Beszéd – szöveg, szöveg-beszéd és beszéd fordítási minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch-átírási minták (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Szöveg – beszéd minták (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Hangsegéd-minták (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>A beszédfelismerési élmény testreszabása

A beszédfelismerési szolgáltatás a beépített modellekkel jól működik, azonban érdemes lehet tovább testreszabni és hangolni a termék vagy a környezet élményét. A testreszabási lehetőségek köre az akusztikus modell finomhangolása és a márka egyedi hangbetűkészletei között.

Más termékek olyan beszédfelismerési modelleket ajánlanak, mint például az egészségügyi ellátás vagy a biztosítás, de mindenki számára elérhetők. Az Azure Speech-ben való Testreszabás az *egyedülálló* versenyelőny részévé válik, amely bármely más felhasználó vagy ügyfél számára nem érhető el. Ez azt jelenti, hogy a modelleket saját és egyéni használatra beállították a használati esetekhez.

| Beszédszolgáltatás | Platform | Leírás |
| -------------- | -------- | ----------- |
| Diktálás | [Custom Speech](https://aka.ms/customspeech) | Testreszabhatja a beszédfelismerési modelleket igényeihez és rendelkezésre álló adataihoz. Leküzdheti a beszédfelismerési akadályokat, például a beszéd stílusát, a szókincset és a háttérzajt. |
| Szövegfelolvasás | [Egyéni hang](https://aka.ms/customvoice) | Létrehozhat egy felismerhető és egyedülálló hangot szövegfelolvasási alkalmazásaihoz az elérhető beszédadatokkal. A hangkimeneteket tovább finomíthatja a hangparaméterek készletének módosításával. |

## <a name="reference-docs"></a>Dokumentációs dokumentumok

- [Beszéd SDK](speech-sdk-reference.md)
- [Beszédeszközök SDK](speech-devices-sdk.md)
- [REST API: beszéd – szöveg](rest-speech-to-text.md)
- [REST API: szövegről beszédre](rest-text-to-speech.md)
- [REST API: kötegelt átírás és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a beszédfelismerési szöveggel](speech-to-text-basics.md) 
>  [Ismerkedés a szöveg és a beszéd](get-started-text-to-speech.md) között
