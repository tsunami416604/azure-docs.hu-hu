---
title: Próbálja ki ingyenesen a Beszédszolgáltatást
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás első lépésekegyszerű és megfizethető. Két lehetőség áll rendelkezésre ingyenesen, így felfedezheti, hogy mit tehet a szolgáltatás, és eldöntheti, hogy az Ön igényeinek megfelelő-e.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 4604bfe7c815f79733f99a1a3727e4c68527e7ec
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656128"
---
# <a name="try-the-speech-service-for-free"></a>Próbálja ki ingyen a Beszéd szolgáltatást

Ebben a cikkben választhat egy lehetőséget, hogy könnyen tesztelje a beszédszolgáltatás ingyenes, így felfedezheti, hogy mit tehet a szolgáltatás, és eldönti, hogy ez a megfelelő az Ön igényeinek. A helyzettől és a használati esettől függően válasszon az alábbi két lehetőség közül:

- [1. lehetőség:](#no-card)Azonnal **ingyenes próbaverziós** API-kulcsokat kap anélkül, hogy bármilyen hitelkártya-adatot megkellene adnia (meglévő Azure-fiókkal kell rendelkeznie). Az **ingyenes próbaverzió** 30 napig tart, és az adatok a végén törlődnek. Ez a beállítás a legjobb a szolgáltatással való gyors kísérletezéshez.
- [2. lehetőség:](#new-resource)Hozzon létre egy új beszédfelismerési erőforrást az Azure-ban, **ingyenes előfizetéssel** (hitelkártyaadatok szükséges). Az **ingyenes előfizetés** ekke idáig csak szigorúbbak, mint a fizetett előfizetések. Ez a lehetőség akkor a legjobb, ha szeretné tesztelni a szolgáltatást, de azt is tervezi, hogy a jövőben fizetős előfizetésre frissít, és nem szeretné elveszíteni az adatokat.

## <a name="try-the-speech-service-without-credit-card-info"></a><a id="no-card"></a>Próbálja ki a Beszédszolgáltatást hitelkártyaadatok nélkül

Hajtsa végre az alábbi lépéseket a 30 napos ingyenes próbaverzió aktiválásához és az API-kulcsok bevételéhez. A próbaidőszak azonnal megkezdődik, amint a következő lépések befejeződtek.

1. Nyissa meg a [Cognitive Service szolgáltatást.](https://azure.microsoft.com/try/cognitive-services/)
1. Válassza a **BeszédAPI-k** lapot.
1. Válassza **az API-kulcs beírása**lehetőséget.

A számlázási lehetőségek között lesznek meg. Válassza ki az ingyenes lehetőséget, majd olvassa el és hagyja jóvá a felhasználói szerződést. A beszédfelismerési szolgáltatás 30 napig ingyenes enciklealkodhat.

## <a name="try-the-speech-service-by-creating-an-azure-resource"></a><a id="new-resource"></a>Próbálja ki a beszédfelismerési szolgáltatást egy Azure-erőforrás létrehozásával

A következő lépésekhez microsoftos és Azure-fiókra is szüksége van. Ha nem rendelkezik Microsoft-fiókkal, egyet ingyenesen regisztrálhat a [Microsoft-fiók portálján.](https://account.microsoft.com/account) Válassza **a Bejelentkezés a Microsofttal** lehetőséget, majd amikor a bejelentkezést kérik, válassza a **Microsoft-fiók létrehozása**lehetőséget. Kövesse az új Microsoft-fiók létrehozásának és ellenőrzésének lépéseit.

Miután rendelkezik Egy Microsoft-fiókkal, lépjen az [Azure-regisztrációs lapra,](https://azure.microsoft.com/free/ai/)válassza az **ingyenes indítás**lehetőséget, és hozzon létre egy új Azure-fiókot egy Microsoft-fiók használatával.

> [!NOTE]
> A beszédfelismerési szolgáltatás két szolgáltatási szinttel rendelkezik: ingyenes és előfizetéses, amelyek különböző korlátozásokkal és előnyökkel rendelkeznek. Amikor feliratkozik egy ingyenes Azure-fiókra, $200-os szolgáltatási jóváírást kap, amelyet akár 30 napig érvényes fizetős beszédszolgáltatási előfizetésre is alkalmazhat.
>
> Ha az ingyenes, kis mennyiségű beszédszolgáltatási szintet használja, akkor az ingyenes próbaverzió vagy szolgáltatáskredit lejárta után is megtarthatja ezt az ingyenes előfizetést.
>
> További információ: [Cognitive Services díjszabása – Beszédszolgáltatás.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

### <a name="create-the-resource"></a>Az erőforrás létrehozása

Beszédszolgáltatási erőforrás (ingyenes vagy fizetős szint) hozzáadása az Azure-fiókjához:

1. Jelentkezzen be az Azure Portalra a [Microsoft-fiókjával.](https://portal.azure.com/)

1. Válassza az **Erőforrás létrehozása** lehetőséget a portál bal felső részén. Ha nem látja az **Erőforrás létrehozása**lehetőséget, mindig megtalálhatja, ha a bal felső sarokban lévő összecsukott menüt választja:

   ![összecsukott navigációs gomb](media/index/collapsed-nav.png)

1. Az **Új** ablakban írja be a "beszéd" szót a keresőmezőbe, és nyomja le az ENTER billentyűt.

1. A keresési eredmények között válassza a **Beszéd**lehetőséget.

   ![beszédkeresési eredmények](media/index/speech-search.png)

1. Válassza **a Létrehozás**lehetőséget, majd:

   - Adjon egyedi nevet az új erőforrásnak. A név segít megkülönböztetni az ugyanahhoz a szolgáltatáshoz kötött több előfizetést.
   - Válassza ki az Azure-előfizetés, amelyhez az új erőforrás van társítva, hogy meghatározza, hogyan a díjak számlázása.
   - Válassza ki azt a [régiót,](regions.md) ahol az erőforrást használni fogja.
   - Válasszon egy ingyenes (F0) vagy fizetős (S0) tarifacsomagot. Az egyes szintek díjszabási és használati kvótáiról a **Teljes díjszabásrészletei megtekintése**lehetőséget.
   - Hozzon létre egy új erőforráscsoportot ehhez a beszédfelismerési előfizetéshez, vagy rendelje hozzá az előfizetést egy meglévő erőforráscsoporthoz. Az erőforráscsoportok segítségével rendszerezheti a különböző Azure-előfizetéseket.
   - Kattintson a **Létrehozás** gombra. Ez elviszi a központi telepítés áttekintéséhez, és megjeleníti a központi telepítés folyamatának üzeneteit.

> [!NOTE]
> Korlátlan számú standard szintű előfizetést hozhat létre egy vagy több régióban. Azonban csak egy ingyenes szintű előfizetést hozhat létre. Az ingyenes szinten lévő, 7 napig használaton kívüli modelltelepítések automatikusan lelesznek szerelve.

Az új beszédfelismerési erőforrás üzembe helyezése néhány percet vesz igénybe. A központi telepítés befejezése után válassza az Ugrás az **erőforráshoz** lehetőséget, és a bal oldali navigációs ablakban válassza a **Kulcsok** lehetőséget a beszédfelismerési szolgáltatás előfizetési kulcsainak megjelenítéséhez. Minden előfizetéshez két kulcs tartozik; bármelyik kulcsot használhatja az alkalmazásban. Ha gyorsan szeretne egy kulcsot a kódszerkesztőbe vagy más helyre beilleszteni, válassza a másolás gombot az egyes billentyűk mellett, váltson át az ablakokközött, hogy beillessze a vágólap tartalmát a kívánt helyre.

> [!IMPORTANT]
> Ezek az előfizetési kulcsok a Cognitive Service API eléréséhez használatosak. Ne ossza meg a kulcsait. Tárolja őket biztonságosan– például az Azure Key Vault használatával. Azt is javasoljuk, hogy rendszeresen regenerálja ezeket a kulcsokat. Csak egy kulcs szükséges egy API-hívás hoz. Az első kulcs újragenerálásakor a második kulccsal folyamatos hozzáférést biztosíthat a szolgáltatáshoz.

## <a name="switch-to-a-new-subscription"></a>Váltás új előfizetésre

Váltás egyik előfizetésről a másikra, például amikor az ingyenes próbaidőszak lejár, vagy amikor közzéteszi az alkalmazást, cserélje le a régió és az előfizetési kulcs a kódot a régió és az új Azure-erőforrás előfizetési kulcsa.

## <a name="about-regions"></a>Régiók – bek

- Ha az alkalmazás [beszédfelismerési SDK-t](speech-sdk.md)használ, `westus`adja meg a régiókódot, például a , beszédkonfiguráció létrehozásakor.
- Ha az alkalmazás a beszédszolgáltatás [REST API-inak](rest-apis.md)egyikét használja, a régió a kérelmek hez használt végpontURI része.
- Egy régióhoz létrehozott kulcsok csak az adott régióban érvényesek. Ha más régiókkal próbálja használni őket, hitelesítési hibák lépnek fel.

## <a name="next-steps"></a>További lépések

Hajtsa végre 10 perces rövid útmutatóink egyikét, vagy nézze meg SDK-mintáinkat:

> [!div class="nextstepaction"]
> [Rövid útmutató: Beszédfelismerés C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [Beszéd SDK-mintákban](speech-sdk.md#sample-source-code)
