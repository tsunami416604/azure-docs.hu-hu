---
title: Próbálja ki ingyenesen a Beszédszolgáltatást
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás első lépései egyszerűek és megfizethetők. Két lehetőség áll rendelkezésre díjmentesen, így felfedezheti, hogy mit tehet a szolgáltatás, és eldöntheti, hogy az igényeinek megfelelő-e.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: f74e3ea3d20ad2666b434e009cf62add6f88d200
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219664"
---
# <a name="try-the-speech-service-for-free"></a>Próbálja ki ingyenesen a Beszédszolgáltatást

Ebben a cikkben egy olyan lehetőséget választ, amellyel ingyenesen tesztelheti a beszédfelismerési szolgáltatást, így felfedezheti, hogy mit tehet a szolgáltatás, és eldöntheti, hogy az igényeinek megfelelő-e. Válasszon az alábbi két lehetőség közül a helyzettől és a használati esettől függően:

- [1. lehetőség](#no-card): az **ingyenes próbaverziós** API-kulcsok azonnali beszerzése a bankkártya-információk megadása nélkül (meglévő Azure-fiókkal kell rendelkeznie). Az **ingyenes próbaidőszak** 30 napig tart, és a rendszer törli az adatvesztést a végén. Ez a lehetőség a szolgáltatással való gyors kísérletezéshez ajánlott.
- [2. lehetőség](#new-resource): hozzon létre egy új Speech-erőforrást az Azure-ban **ingyenes előfizetés** használatával (hitelkártya-adatok szükségesek). Az **ingyenes előfizetés** elsősorban szigorúbb díjszabású, mint a díjköteles előfizetés. Ez a lehetőség akkor a legjobb, ha tesztelni szeretné a szolgáltatást, de azt is tervezi, hogy később egy fizetős előfizetésre frissít, és nem szeretné elveszíteni az adatvesztést.

## <a id="no-card"></a>Próbálja ki a beszédfelismerési szolgáltatást bankkártyás adatok nélkül

Hajtsa végre a következő lépéseket egy 30 napos ingyenes próbaverzió aktiválásához és az API-kulcsok lekéréséhez. A próbaidőszak a következő lépések elvégzése után azonnal elindul.

1. Lépjen a [kognitív szolgáltatás kipróbálása](https://azure.microsoft.com/try/cognitive-services/)oldalra.
1. Válassza a **beszédfelismerési API** -k fület.
1. Válassza az **API-kulcs beolvasása**elemet.

A számlázási lehetőségek közül választhat. Válassza az ingyenes lehetőséget, majd olvassa el és hagyja jóvá a felhasználói szerződést. Olyan kulcsokkal fog megjelenni, amelyek segítségével 30 napig ingyenesen kipróbálhatja a Speech szolgáltatást.

## <a id="new-resource"></a>Próbálja ki a Speech szolgáltatást egy Azure-erőforrás létrehozásával

A következő lépésekhez egy Microsoft-fiók és egy Azure-fiókra van szükség. Ha nem rendelkezik Microsoft-fiókval, a [Microsoft-fiók portálon](https://account.microsoft.com/account)ingyenesen regisztrálhat. Válassza a **Bejelentkezés Microsoft-fiókkal** lehetőséget, majd amikor a rendszer kéri a bejelentkezést, válassza **a Microsoft-fiók létrehozása**lehetőséget. A lépésekkel hozhat létre, és ellenőrizze az új Microsoft-fiókjával.

Ha Microsoft-fiók, nyissa meg az [Azure regisztrációs oldalát](https://azure.microsoft.com/free/ai/), válassza az **ingyenes indítás**lehetőséget, és hozzon létre egy új Azure-fiókot egy Microsoft-fiók használatával.

> [!NOTE]
> A beszédfelismerési szolgáltatás két szolgáltatási szintet tartalmaz: ingyenes és előfizetést, amelyek eltérő korlátozásokkal és előnyökkel rendelkeznek. Ha regisztrál egy ingyenes Azure-fiókra, az $200-as szolgáltatási Kredittel rendelkezik, amely a fizetős szolgáltatások előfizetéséhez tartozik, és legfeljebb 30 napig érvényes.
>
> Ha az ingyenes, kis mennyiségű Speech Service-szintet használja, akkor az ingyenes próbaverzió vagy a szolgáltatási jóváírás lejárta után is megtarthatja ezt az ingyenes előfizetést.
>
> További információ: [Cognitive Services díjszabása-Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-resource"></a>Az erőforrás létrehozása

A beszédfelismerési szolgáltatás-erőforrás (ingyenes vagy fizetős szint) hozzáadása az Azure-fiókjával:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a Microsoft-fiók használatával.

1. Válassza az **erőforrás létrehozása** lehetőséget a portál bal felső részén. Ha nem látja az **erőforrás létrehozása**lehetőséget, a bal felső sarokban található összecsukott menü kiválasztásával bármikor megtalálhatja a következőt:

   ![összecsukott navigációs gomb](media/index/collapsed-nav.png)

1. Az **új** ablakban írja be a "Speech" kifejezést a keresőmezőbe, majd nyomja le az ENTER billentyűt.

1. A keresési eredmények között válassza a **Speech (beszéd**) lehetőséget.

   ![beszédfelismerési találatok](media/index/speech-search.png)

1. Válassza a **Létrehozás**lehetőséget, majd:

   - Adjon egyedi nevet az új erőforrásnak. A név segít különbséget tenni az ugyanahhoz a szolgáltatáshoz kötött több előfizetés között.
   - Válassza ki az Azure-előfizetést, amelyhez az új erőforrás a határozza meg, hogy a díjak számlázása van társítva.
   - Válassza ki azt a [régiót](regions.md) , ahol az erőforrást használni szeretné.
   - Válassza az ingyenes (F0) vagy a fizetős (S0) árképzési szintet. Az egyes szintek díjszabásával és használati kvótákkal kapcsolatos teljes információkért válassza a **teljes díjszabás**megjelenítése lehetőséget.
   - Hozzon létre egy új erőforráscsoportot, beszéd ehhez az előfizetéshez, vagy az előfizetés hozzárendelése egy meglévő erőforráscsoportot. Erőforráscsoportokat tarthatja rendezve különböző Azure-előfizetéseit.
   - Kattintson a **Létrehozás** gombra. Ezzel áttekintheti a központi telepítés áttekintését és megjeleníti az üzembe helyezési folyamat üzeneteit.

> [!NOTE]
> Standard szintű előfizetés korlátlan számú egy vagy több régióban hozhat létre. Azonban csak egy ingyenes szintű előfizetést hozhat létre. A 7 napig használatban lévő, ingyenes szinten lévő központi telepítéseket a rendszer automatikusan leszereli.

Az új beszédfelismerési erőforrás üzembe helyezése néhány percet vesz igénybe. Az üzembe helyezés befejezése után válassza az **erőforráshoz való ugrás** lehetőséget, majd a bal oldali navigációs panelen válassza a **kulcsok** lehetőséget a beszédfelismerési szolgáltatás előfizetési kulcsai megjelenítéséhez. Minden előfizetési csomaghoz tartozik két kulcs; mindkét kulcsot is használhatja az alkalmazásban. Ha gyorsan szeretne másolni/beilleszteni egy kulcsot a Kódszerkesztő vagy más helyre, válassza az egyes kulcsok melletti másolás gombot, a Windowst a vágólap tartalmának a kívánt helyre való beillesztéséhez.

> [!IMPORTANT]
> Ezek az előfizetési kulcsok hozzáférnek a kognitív szolgáltatás API-hoz. Ne ossza meg a kulcsokat. Biztonságos tárolás – például Azure Key Vault használatával. Javasoljuk továbbá, hogy rendszeresen újragenerálja ezeket a kulcsokat. API-hívások létrehozásához csak egy kulcs szükséges. Az első kulcs újragenerálásakor a második kulcsot használhatja a szolgáltatás folyamatos eléréséhez.

## <a name="switch-to-a-new-subscription"></a>Új előfizetésre Váltás

Váltson egy előfizetésből egy másikba, például amikor az ingyenes próbaverzió érvényessége lejár, vagy az alkalmazás közzétételekor cserélje le a régió és előfizetési kulcsot a kódban a régió és egy előfizetési kulcsra az új Azure-erőforrás.

## <a name="about-regions"></a>A régiókról

- Ha az alkalmazás egy [SPEECH SDK](speech-sdk.md)-t használ, akkor a beszédfelismerési konfiguráció létrehozásakor megadja a régiókódot, például a `westus`.
- Ha az alkalmazás a beszédfelismerési szolgáltatás [REST API](rest-apis.md)-jának egyikét használja, akkor a régió a kérések végrehajtásakor használt végponti URI része.
- Régió létrehozott kulcsok csak az adott régióban érvényesek. Próbál használni őket a más régiókban hitelesítési hibákat eredményez.

## <a name="next-steps"></a>Következő lépések

Hajtsa végre a 10 perces gyors útmutatók, vagy tekintse meg az SDK-minták:

> [!div class="nextstepaction"]
> Gyors útmutató [: beszédfelismerés felismerése C# ](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [Speech SDK-mintákban](speech-sdk.md#get-the-samples)
