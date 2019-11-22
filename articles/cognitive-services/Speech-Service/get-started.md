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
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 30bdbf9fa0ea346892622c3e7f24f9f31652a650
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280501"
---
# <a name="try-the-speech-service-for-free"></a>Próbálja ki ingyenesen a Beszédszolgáltatást

A beszédfelismerési szolgáltatás használata egyszerű és megfizethető. Két lehetőség áll rendelkezésre díjmentesen, így felfedezheti, hogy mit tehet a szolgáltatás, és eldöntheti, hogy az Ön igényeinek megfelelő-e:

- Ingyenes próbaverziót kaphat bankkártyás adatok megadása nélkül (meglévő Azure-fiókkal kell rendelkeznie)
- Új Azure-fiók létrehozása díjmentesen a Próbaidőszakban (hitelkártya-adatok szükségesek)

Ebben a cikkben az igényeinek leginkább megfelelő lehetőségek közül választhat.

> [!NOTE]
> A beszédfelismerési szolgáltatás két szolgáltatási szintet tartalmaz: ingyenes és előfizetést, amelyek eltérő korlátozásokkal és előnyökkel rendelkeznek. Ha regisztrál egy ingyenes Azure-fiókra, az $200-as szolgáltatási Kredittel rendelkezik, amely a fizetős szolgáltatások előfizetéséhez tartozik, és legfeljebb 30 napig érvényes.
>
> Ha az ingyenes, kis mennyiségű Speech Service-szintet használja, akkor az ingyenes próbaverzió vagy a szolgáltatási jóváírás lejárta után is megtarthatja ezt az ingyenes előfizetést.
>
> További információ: [Cognitive Services díjszabása-Speech Services](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="try-the-speech-service-without-credit-card-info"></a>Próbálja ki a beszédfelismerési szolgáltatást bankkártyás adatok nélkül

Habár javasoljuk, hogy a beszédfelismerési szolgáltatást a következő szakaszban található utasítások segítségével próbálja meg használni, a szakasz utasításait érdemes megtekinteni, ha az alábbiak érvényesek:

- Már rendelkezik aktív Azure-fiókkal.
- Új Azure-fiók létrehozása nélkül szeretné kiértékelni a beszédfelismerési szolgáltatást.
- A próbaidőszak után nem kell hitelkártyát megadnia, és nem kell mentenie azokat.

> [!NOTE]
> A próbaidőszak a következő lépések elvégzése után azonnal elindul.

1. Lépjen a [kognitív szolgáltatás kipróbálása](https://azure.microsoft.com/try/cognitive-services/)oldalra.
1. Válassza ki a **Speech API-k** fülre.
1. Válassza az **API-kulcs beolvasása**elemet.

A számlázási lehetőségek közül választhat. Válassza az ingyenes lehetőséget, majd olvassa el és hagyja jóvá a felhasználói szerződést. Olyan kulcsokkal fog megjelenni, amelyekkel korlátozott ideig kipróbálhatja a beszédfelismerési szolgáltatást.

## <a name="try-the-speech-service-using-a-new-azure-account"></a>Próbálja ki a Speech szolgáltatást egy új Azure-fiók használatával

Új Azure-fiók regisztrálásához szüksége lesz egy Microsoft-fiókra. Ha nem rendelkezik Microsoft-fiókval, a [Microsoft-fiók portálon](https://account.microsoft.com/account)ingyenesen regisztrálhat. Válassza a **Bejelentkezés Microsoft-fiókkal** lehetőséget, majd amikor a rendszer kéri a bejelentkezést, válassza **a Microsoft-fiók létrehozása**lehetőséget. A lépésekkel hozhat létre, és ellenőrizze az új Microsoft-fiókjával.

Ha Microsoft-fiók, nyissa meg az [Azure regisztrációs oldalát](https://azure.microsoft.com/free/ai/), válassza az **ingyenes indítás**lehetőséget, és hozzon létre egy új Azure-fiókot egy Microsoft-fiók használatával.

### <a name="create-a-speech-resource-in-azure"></a>Beszéd erőforrás létrehozása az Azure-ban

> [!NOTE]
> Standard szintű előfizetés korlátlan számú egy vagy több régióban hozhat létre. Azonban csak egy ingyenes szintű előfizetést hozhat létre. A 7 napig használatban lévő, ingyenes szinten lévő központi telepítéseket a rendszer automatikusan leszereli.

A beszédfelismerési szolgáltatás-erőforrás (ingyenes vagy fizetős szint) hozzáadása az Azure-fiókjával:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) Microsoft-fiókjával.

1. Válassza ki **erőforrás létrehozása** , a portál bal felső. Ha nem látja az **erőforrás létrehozása**lehetőséget, a bal felső sarokban található összecsukott menü kiválasztásával bármikor megtalálhatja a következőt:

   ![összecsukott navigációs gomb](media/index/collapsed-nav.png)

1. Az **új** ablakban írja be a "Speech" kifejezést a keresőmezőbe, majd nyomja le az ENTER billentyűt.

1. A keresési eredmények között, válassza ki a **Speech**.

   ![beszédfelismerési találatok](media/index/speech-search.png)

1. Válassza a **Létrehozás**lehetőséget, majd:

   - Adjon egyedi nevet az új erőforrásnak. A név segítségével megkülönböztetésére, amelyek ugyanazt a szolgáltatást több előfizetést.
   - Válassza ki az Azure-előfizetést, amelyhez az új erőforrás a határozza meg, hogy a díjak számlázása van társítva.
   - Válassza ki azt a [régiót](regions.md) , ahol az erőforrást használni szeretné.
   - Válassza az ingyenes (F0) vagy a fizetős (S0) árképzési szintet. Az egyes szintek díjszabásával és használati kvótákkal kapcsolatos teljes információkért válassza a **teljes díjszabás**megjelenítése lehetőséget.
   - Hozzon létre egy új erőforráscsoportot, beszéd ehhez az előfizetéshez, vagy az előfizetés hozzárendelése egy meglévő erőforráscsoportot. Erőforráscsoportokat tarthatja rendezve különböző Azure-előfizetéseit.
   - Kattintson a **Létrehozás** gombra. Ezzel áttekintheti a központi telepítés áttekintését és megjeleníti az üzembe helyezési folyamat üzeneteit.

Az új beszédfelismerési erőforrás üzembe helyezése néhány percet vesz igénybe. Az üzembe helyezés befejezése után válassza az **erőforráshoz való ugrás** lehetőséget, majd a bal oldali navigációs panelen válassza a **kulcsok** lehetőséget a beszédfelismerési szolgáltatás előfizetési kulcsai megjelenítéséhez. Minden előfizetési csomaghoz tartozik két kulcs; mindkét kulcsot is használhatja az alkalmazásban. Ha gyorsan szeretne másolni/beilleszteni egy kulcsot a Kódszerkesztő vagy más helyre, válassza az egyes kulcsok melletti másolás gombot, a Windowst a vágólap tartalmának a kívánt helyre való beillesztéséhez.

> [!IMPORTANT]
> Ezek az előfizetési kulcsok hozzáférnek a kognitív szolgáltatás API-hoz. Ne ossza meg a kulcsokat. Biztonságos tárolás – például Azure Key Vault használatával. Javasoljuk továbbá, hogy rendszeresen újragenerálja ezeket a kulcsokat. API-hívások létrehozásához csak egy kulcs szükséges. Az első kulcs újragenerálásakor a második kulcsot használhatja a szolgáltatás folyamatos eléréséhez.

## <a name="switch-to-a-new-subscription"></a>Új előfizetésre Váltás

Váltson egy előfizetésből egy másikba, például amikor az ingyenes próbaverzió érvényessége lejár, vagy az alkalmazás közzétételekor cserélje le a régió és előfizetési kulcsot a kódban a régió és egy előfizetési kulcsra az új Azure-erőforrás.

## <a name="about-regions"></a>A régiókról

- Ha az alkalmazás egy [beszéd SDK](speech-sdk.md), például adja meg a régiókód `westus`, a beszéd-konfiguráció létrehozása során.
- Ha az alkalmazás használja a beszédfelismerési szolgáltatás egyik [REST API-k](rest-apis.md), a régiót a végpont URI-kérelem indítására használ részét képezi.
- Régió létrehozott kulcsok csak az adott régióban érvényesek. Próbál használni őket a más régiókban hitelesítési hibákat eredményez.

## <a name="next-steps"></a>Következő lépések

Hajtsa végre a 10 perces gyors útmutatók, vagy tekintse meg az SDK-minták:

> [!div class="nextstepaction"]
> [Gyors útmutató: A C# beszédfelismerést](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [beszéd SDK-minták](speech-sdk.md#get-the-samples)
