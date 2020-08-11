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
ms.date: 04/03/2020
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 5dc1f8ed31c8d7199a31643a76e611d828da77ac
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056465"
---
# <a name="try-the-speech-service-for-free"></a>Próbálja ki ingyenesen a Beszédszolgáltatást

Ebben a cikkben egy olyan lehetőséget választ, amellyel ingyenesen tesztelheti a beszédfelismerési szolgáltatást, így felfedezheti, hogy mit tehet a szolgáltatás, és eldöntheti, hogy az igényeinek megfelelő-e. Válasszon az alábbi két lehetőség közül a helyzettől és a használati esettől függően:

- [1. lehetőség](#no-card): az **ingyenes próbaverziós** API-kulcsok azonnali beszerzése a bankkártya-információk megadása nélkül (meglévő Azure-fiókkal kell rendelkeznie). Az **ingyenes próbaidőszak** 30 napig tart, és a rendszer törli az adatvesztést a végén. Ez a lehetőség a szolgáltatással való gyors kísérletezéshez ajánlott.
- [2. lehetőség](#new-resource): hozzon létre egy új Speech-erőforrást az Azure-ban **ingyenes előfizetés** használatával (hitelkártya-adatok szükségesek). Az **ingyenes előfizetés** elsősorban szigorúbb díjszabású, mint a díjköteles előfizetés. Ez a lehetőség akkor a legjobb, ha tesztelni szeretné a szolgáltatást, de azt is tervezi, hogy később egy fizetős előfizetésre frissít, és nem szeretné elveszíteni az adatvesztést.

## <a name="try-the-speech-service-without-credit-card-info"></a><a id="no-card"></a>Próbálja ki a beszédfelismerési szolgáltatást bankkártyás adatok nélkül

Hajtsa végre a következő lépéseket egy 30 napos ingyenes próbaverzió aktiválásához és az API-kulcsok lekéréséhez. A próbaidőszak a következő lépések elvégzése után azonnal elindul.

1. Lépjen a [kognitív szolgáltatás kipróbálása](https://azure.microsoft.com/try/cognitive-services/)oldalra.
1. Válassza a **beszédfelismerési API** -k fület.
1. Válassza az **API-kulcs beolvasása**elemet.

A számlázási lehetőségek közül választhat. Válassza az ingyenes lehetőséget, majd olvassa el és hagyja jóvá a felhasználói szerződést. Olyan kulcsokkal fog megjelenni, amelyek segítségével 30 napig ingyenesen kipróbálhatja a Speech szolgáltatást.

## <a name="try-the-speech-service-by-creating-an-azure-resource"></a><a id="new-resource"></a>Próbálja ki a Speech szolgáltatást egy Azure-erőforrás létrehozásával

A következő lépésekhez egy Microsoft-fiók és egy Azure-fiókra van szükség. Ha nem rendelkezik Microsoft-fiókval, a [Microsoft-fiók portálon](https://account.microsoft.com/account)ingyenesen regisztrálhat. Válassza a **Bejelentkezés Microsoft-fiókkal** lehetőséget, majd amikor a rendszer kéri a bejelentkezést, válassza **a Microsoft-fiók létrehozása**lehetőséget. Az új Microsoft-fiók létrehozásához és ellenőrzéséhez kövesse az alábbi lépéseket.

Ha Microsoft-fiók, nyissa meg az [Azure regisztrációs oldalát](https://azure.microsoft.com/free/ai/), válassza az **ingyenes indítás**lehetőséget, és hozzon létre egy új Azure-fiókot egy Microsoft-fiók használatával.

> [!NOTE]
> A beszédfelismerési szolgáltatás két szolgáltatási szintet tartalmaz: ingyenes és előfizetést, amelyek eltérő korlátozásokkal és előnyökkel rendelkeznek. Ha regisztrál egy ingyenes Azure-fiókra, az $200-as szolgáltatási Kredittel rendelkezik, amely a fizetős szolgáltatások előfizetéséhez tartozik, és legfeljebb 30 napig érvényes.
>
> Ha az ingyenes, kis mennyiségű Speech Service-szintet használja, akkor az ingyenes próbaverzió vagy a szolgáltatási jóváírás lejárta után is megtarthatja ezt az ingyenes előfizetést.
>
> További információ: [Cognitive Services díjszabása-Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-resource"></a>Az erőforrás létrehozása

A Speech Service-erőforrások (ingyenes vagy fizetős szintek) hozzáadása az Azure-fiókhoz:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a Microsoft-fiókjával.

1. Válassza az **erőforrás létrehozása** lehetőséget a portál bal felső részén. Ha nem látja az **erőforrás létrehozása**lehetőséget, a bal felső sarokban található összecsukott menü kiválasztásával bármikor megtalálhatja a következőt:

   ![összecsukott navigációs gomb](media/index/collapsed-nav.png)

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

## <a name="switch-to-a-new-subscription"></a>Váltás új előfizetésre

Ha az egyik előfizetésből egy másikra szeretne váltani, például ha az ingyenes próbaidőszak lejár, vagy amikor közzéteszi az alkalmazást, cserélje le a régiót és az előfizetési kulcsot a kódban az új Azure-erőforrás régiója és előfizetési kulcsa alapján.

## <a name="about-regions"></a>A régiókról

- Ha az alkalmazás egy [SPEECH SDK](speech-sdk.md)-t használ, akkor adja meg a régiókódot `westus` (például) a beszédfelismerési konfiguráció létrehozásakor.
- Ha az alkalmazás a beszédfelismerési szolgáltatás [REST API](rest-apis.md)-jának egyikét használja, akkor a régió a kérések végrehajtásakor használt végponti URI része.
- A régióhoz létrehozott kulcsok csak az adott régióban érvényesek. A más régiókkal való használatának megkísérlése hitelesítési hibákat eredményez.

## <a name="next-steps"></a>További lépések

Fejezze be az egyik 10 perces rövid útmutatót, vagy tekintse meg az SDK-mintákat:

> [!div class="nextstepaction"]
> Gyors útmutató [: beszédfelismerés felismerése a beszédfelismerési parancssori](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programmer-tool-spx) 
>  felülettel [SPEECH SDK-minták](speech-sdk.md#sample-source-code)
