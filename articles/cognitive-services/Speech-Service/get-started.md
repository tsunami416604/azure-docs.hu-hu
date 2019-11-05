---
title: A Speech Service ingyenes kipróbálása
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás első lépései egyszerűek és megfizethetők. Egy 30 napos ingyenes próbaverzióval felfedezheti, hogy mit tehet a szolgáltatás, és eldöntheti, hogy az alkalmazás igényeinek megfelelő-e.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 46ada91060aa095b7c041ff75abb6256f3d05853
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464697"
---
# <a name="try-speech-services-for-free"></a>A Speech Services ingyenes kipróbálása

A Speech Services első lépései egyszerűek és megfizethetők. Egy 30 napos ingyenes próbaverzióval felfedezheti, hogy mit tehet a szolgáltatás, és eldöntheti, hogy az alkalmazás igényeinek megfelelő-e.

Ha több időre van szüksége, regisztráljon egy Microsoft Azure-fiókra, amely az $200-as szolgáltatási Kredittel rendelkezik, amelyet akár 30 napig is alkalmazhat a fizetős szolgáltatások előfizetése felé.

Végül a Speech Services egy ingyenes, kis mennyiségű, az alkalmazások fejlesztéséhez megfelelő szintet kínál. Ezt az ingyenes előfizetést akkor is megtarthatja, ha a szolgáltatási kredit lejár.

## <a name="free-trial"></a>Ingyenes próbaidőszak

A 30 napos ingyenes próbaverzió korlátozott ideig biztosít hozzáférést a standard díjszabási szinthez.

Regisztráció a 30 napos ingyenes próbaverzióra:

1. Ugorjon a [Cognitive Services kipróbálása](https://azure.microsoft.com/try/cognitive-services/)gombra.

1. Válassza a **beszédfelismerési API** -k fület.

   ![Speech API – Speech Services lap](media/index/cognitive-services-speech-api-tab.png)

1. A **Speech Services**területen válassza az **API-kulcs beolvasása**elemet.

   ![Beszédfelismerési API – API-kulcs beszerzése](media/index/speech-api-get-api-key.png)

1. Fogadja el a feltételeket, és válassza ki a területi beállítást a legördülő menüből.

   ![Beszédfelismerési API – a feltételek elfogadása](media/index/speech-api-agree-to-terms.png)

1. Jelentkezzen be a Microsoft, a Facebook, a LinkedIn vagy a GitHub-fiók használatával.

    A [Microsoft-fiók portálon](https://account.microsoft.com/account)regisztrálhat egy ingyenes Microsoft-fiókra. Első lépésként válassza a **Bejelentkezés Microsoft-fiókkal** lehetőséget, majd amikor a rendszer kéri a bejelentkezést, válassza a **Létrehozás lehetőséget.** Az új Microsoft-fiók létrehozásához és ellenőrzéséhez kövesse az alábbi lépéseket.

Miután bejelentkezett a Cognitive Services kipróbálására, megkezdődik az ingyenes próbaverzió. A megjelenített weblap felsorolja az összes Azure Cognitive Services szolgáltatást, amelyhez jelenleg próbaverziós előfizetések tartoznak. Két előfizetési kulcs szerepel a **Speech Services**mellett. Az alkalmazásokban bármelyik kulcsot használhatja.

> [!NOTE]
> Az ingyenes próbaverziós előfizetések az USA nyugati régiójában találhatók. A kérések végrehajtásakor ügyeljen arra, hogy az `westus` végpontot használja.

## <a name="new-azure-account"></a>Új Azure-fiók

Az új Azure-fiókok $200 szolgáltatási kreditet kapnak, amely akár 30 napig is elérhető. Ezt a kreditet a Speech Services további megismeréséhez vagy az alkalmazásfejlesztés megkezdéséhez használhatja.

Ha új Azure-fiókot szeretne regisztrálni, lépjen az [Azure regisztrációs oldalára](https://azure.microsoft.com/free/ai/), válassza az **ingyenes indítás lehetőséget,** és hozzon létre egy új azure-fiókot a Microsoft-fiók használatával.

A [Microsoft-fiók portálon](https://account.microsoft.com/account)regisztrálhat egy ingyenes Microsoft-fiókra. Első lépésként válassza a **Bejelentkezés Microsoft-fiókkal** lehetőséget, majd amikor a rendszer kéri a bejelentkezést, válassza a **Létrehozás lehetőséget.** Az új Microsoft-fiók létrehozásához és ellenőrzéséhez kövesse az alábbi lépéseket.

Az Azure-fiók létrehozása után kövesse a következő szakaszban ismertetett lépéseket a Speech Services-előfizetés elindításához.

## <a name="create-a-speech-resource-in-azure"></a>Beszédfelismerési erőforrás létrehozása az Azure-ban

A Speech Services-erőforrások (ingyenes vagy fizetős szintek) hozzáadása az Azure-fiókhoz:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a Microsoft-fiók használatával.

1. Válassza az **erőforrás létrehozása** lehetőséget a portál bal felső részén.

    ![Beszédfelismerési API – erőforrás létrehozása](media/index/speech-api-create-resource.png)

1. Az **új** ablakban keressen **beszédet**.

1. A keresési eredmények között válassza a **Speech (beszéd**) lehetőséget.

    ![Beszédfelismerési API – beszéd kiválasztása](media/index/speech-api-select-speech.png)

1. A **beszédfelismerés**alatt kattintson a **Létrehozás** gombra.

    ![Beszédfelismerési API – létrehozás gomb](media/index/speech-api-create-button.png)

1. A **Létrehozás**alatt írja be a következőket:

   * Az új erőforrás neve. A név segít különbséget tenni több előfizetés között ugyanahhoz a szolgáltatáshoz.
   * Válassza ki azt az Azure-előfizetést, amelyhez az új erőforrás társítva van, hogy megtudja, hogyan történik a díjak számlázása.
   * Válassza ki azt a [régiót](regions.md) , ahol az erőforrást használni szeretné.
   * Válasszon egy ingyenes vagy fizetős díjszabási szintet. Az egyes csomagokra vonatkozó díjszabási és használati kvótákkal kapcsolatos teljes információk megtekintéséhez válassza a **teljes díjszabási részletek megtekintése** lehetőséget.
   * Hozzon létre egy új erőforráscsoportot ehhez a beszédfelismerési előfizetéshez, vagy rendelje hozzá az előfizetést egy meglévő erőforráscsoporthoz. Az erőforráscsoportok segítségével megőrizheti különböző Azure-előfizetéseit.
   * Az előfizetéshez a jövőben való kényelmes hozzáféréshez jelölje be a **rögzítés az irányítópulton** jelölőnégyzetet.
   * Válassza a **Létrehozás lehetőséget.**

     ![Beszédfelismerési API – válassza a létrehozás lehetőséget](media/index/speech-api-select-create.png)

     Az új beszédfelismerési erőforrás létrehozása és üzembe helyezése egy pillanatra is eltarthat. Válassza a rövid útmutató **lehetőséget az új** erőforrással kapcsolatos információk megjelenítéséhez.

     ![Beszédfelismerési API – az erőforrás üzembe helyezése](media/index/speech-api-deploy-resource.png)

1. A gyors üzembe helyezés szakaszban válassza az 1. **lépés alatt lévő** **kulcsok** hivatkozást az előfizetési kulcsok megjelenítéséhez. Minden előfizetés két kulccsal rendelkezik; az alkalmazásban bármelyik kulcsot használhatja. Jelölje be az egyes kulcsok melletti gombot, és másolja a vágólapra a kódban való beillesztéshez.

> [!NOTE]
> Korlátlan számú standard szintű előfizetést hozhat létre egy vagy több régióban. Azonban csak egy ingyenes rétegbeli előfizetést hozhat létre. A 7 napig használatban lévő ingyenes szinten a modell telepítése automatikusan leszerelték válik.

## <a name="switch-to-a-new-subscription"></a>Váltás új előfizetésre

Ha az egyik előfizetésből egy másikra szeretne váltani, például ha az ingyenes próbaidőszak lejár, vagy amikor közzéteszi az alkalmazást, cserélje le a régiót és az előfizetési kulcsot a kódban az új Azure-erőforrás régiója és előfizetési kulcsa alapján.

> [!NOTE]
> Az ingyenes próbaverziós kulcsok az USA nyugati régiójában (`westus`) jönnek létre. Az Azure-irányítópulton létrehozott előfizetés lehet más régióban is, ha ezt választja.

* Ha az alkalmazás egy [SPEECH SDK](speech-sdk.md)-t használ, akkor a beszédfelismerési konfiguráció létrehozásakor megadja a régiókódot, például a `westus`.
* Ha az alkalmazás a Speech Services [REST API](rest-apis.md)-jait használja, a régió a kérelmek elkészítésekor használt végpont URI része.

A régióhoz létrehozott kulcsok csak az adott régióban érvényesek. A más régiókkal való használatának megkísérlése hitelesítési hibákat eredményez.

## <a name="next-steps"></a>További lépések

Fejezze be az egyik 10 perces rövid útmutatót, vagy tekintse meg az SDK-mintákat:

> [!div class="nextstepaction"]
> Gyors útmutató [: beszédfelismerés felismerése C# ](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [Speech SDK-mintákban](speech-sdk.md#get-the-samples)
