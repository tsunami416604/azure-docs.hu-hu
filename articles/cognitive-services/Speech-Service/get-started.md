---
title: A Speech Service ingyenes kipróbálása
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás első lépései egyszerűek és megfizethetők. Egy 30 napos ingyenes próbaverzió lehetővé teszi, hogy Ön mit a szolgáltatás tegye és eldönteni, hogy ez az alkalmazás igényeinek megfelelő felderítése.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 41d12013ec7eaa4e2aae59e1b366cc511a41f749
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69535110"
---
# <a name="try-speech-services-for-free"></a>Beszédszolgáltatások ingyenes kipróbálása

A Speech Services első lépései egyszerűek és megfizethetők. Egy 30 napos ingyenes próbaverzió lehetővé teszi, hogy Ön mit a szolgáltatás tegye és eldönteni, hogy ez az alkalmazás igényeinek megfelelő felderítése.

Ha több időre van szüksége, regisztráljon egy Microsoft Azure-fiókra, amely az $200-as szolgáltatási Kredittel rendelkezik, amelyet akár 30 napig is alkalmazhat a fizetős szolgáltatások előfizetése felé.

Végül a Speech Services egy ingyenes, kis mennyiségű, az alkalmazások fejlesztéséhez megfelelő szintet kínál. Ez az ingyenes előfizetés még a szolgáltatási jóváírás lejárta után is megtarthatja.

## <a name="free-trial"></a>Ingyenes próbaidőszak

A 30 napos ingyenes próbaverzió hozzáférést biztosít a standard díjcsomag korlátozott időre.

Regisztráció a 30 napos ingyenes próbaverziójára:

1. Lépjen a [a Cognitive Services kipróbálása](https://azure.microsoft.com/try/cognitive-services/).

1. Válassza ki a **Speech API-k** fülre.

   ![Beszéd szolgáltatások lap](media/index/try-speech-api-free-trial1.png)

1. A **Speech Services**területen válassza az **API-kulcs**beolvasása elemet.

   ![API-kulcs](media/index/try-speech-api-free-trial2.png)

1. Fogadja el a feltételeket, és válassza ki a területi beállítás a legördülő menüből.

   ![Feltételek elfogadása](media/index/try-speech-api-free-trial3.png)

1. Jelentkezzen be a Microsoft, a Facebook, a LinkedIn, vagy a GitHub-fiók használatával.

    Regisztrálhat egy ingyenes Microsoft-fiókot a [Microsoft fiókportál](https://account.microsoft.com/account). Első lépésként válassza a **Bejelentkezés Microsoft-fiókkal** lehetőséget, majd amikor a rendszer kéri a bejelentkezést, válassza a **Létrehozás lehetőséget.** A lépésekkel hozhat létre, és ellenőrizze az új Microsoft-fiókjával.

Miután bejelentkezett, próbálja meg a Cognitive Services, az ingyenes próbaverzió kezdődik. A megjelenő képernyőn látható weblapon, amelyhez jelenleg rendelkezik próba-előfizetések az Azure Cognitive Services szolgáltatásokat sorolja fel. Két előfizetési kulcs szerepel a **Speech Services**mellett. Használhatja az egyiket sem az alkalmazásokban.

> [!NOTE]
> Ingyenes próba-előfizetések szerepelnek, az USA nyugati régiója. Amikor a kéréseket, ügyeljen arra, hogy a `westus` végpont.

## <a name="new-azure-account"></a>Új Azure-fiók

Új Azure-fiókok jóváírás 200 USD értékű szolgáltatás, amely legfeljebb 30 napig érhető el. Ezt a kreditet a Speech Services további megismeréséhez vagy az alkalmazásfejlesztés megkezdéséhez használhatja.

Ha új Azure-fiókot szeretne regisztrálni, lépjen az [Azure regisztrációs oldalára](https://azure.microsoft.com/free/ai/), válassza az **ingyenes indítás lehetőséget,** és hozzon létre egy új azure-fiókot a Microsoft-fiók használatával.

Regisztrálhat egy ingyenes Microsoft-fiókot a [Microsoft fiókportál](https://account.microsoft.com/account). Első lépésként válassza a **Bejelentkezés Microsoft-fiókkal** lehetőséget, majd amikor a rendszer kéri a bejelentkezést, válassza a **Létrehozás lehetőséget.** A lépésekkel hozhat létre, és ellenőrizze az új Microsoft-fiókjával.

Az Azure-fiók létrehozása után kövesse a következő szakaszban ismertetett lépéseket a Speech Services-előfizetés elindításához.

## <a name="create-a-speech-resource-in-azure"></a>Beszéd erőforrás létrehozása az Azure-ban

A Speech Services-erőforrások (ingyenes vagy fizetős szintek) hozzáadása az Azure-fiókhoz:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) Microsoft-fiókjával.

1. Válassza ki **erőforrás létrehozása** , a portál bal felső.

    ![Erőforrás létrehozása](media/index/try-speech-api-create-speech1.png)

1. Az a **új** ablakot, és keressen **speech**.

1. A keresési eredmények között, válassza ki a **Speech**.

    ![Beszéd kiválasztása](media/index/try-speech-api-create-speech2.png)

1. A **Speech**, jelölje be a **létrehozás** gombra.

    ![Kattintson a Létrehozás gombra](media/index/try-speech-api-create-speech3.png)

1. A **létrehozás**, adja meg:

   * Az új erőforrás neve. A név segítségével megkülönböztetésére, amelyek ugyanazt a szolgáltatást több előfizetést.
   * Válassza ki az Azure-előfizetést, amelyhez az új erőforrás a határozza meg, hogy a díjak számlázása van társítva.
   * Válassza ki [](regions.md) azt a régiót, ahol az erőforrást használni szeretné.
   * Válassza ki, vagy egy ingyenes vagy fizetős tarifacsomagra. Az egyes csomagokra vonatkozó díjszabási és használati kvótákkal kapcsolatos teljes információk megtekintéséhez válassza a **teljes díjszabási részletek megtekintése** lehetőséget.
   * Hozzon létre egy új erőforráscsoportot, beszéd ehhez az előfizetéshez, vagy az előfizetés hozzárendelése egy meglévő erőforráscsoportot. Erőforráscsoportokat tarthatja rendezve különböző Azure-előfizetéseit.
   * A jövőben az előfizetés hozzáférés kényelmes, válassza ki a **rögzítés az irányítópulton** jelölőnégyzetet.
   * Válassza ki **létrehozása.**

     ![Kattintson a Létrehozás gombra](media/index/try-speech-api-create-speech4.png)

     Létrehozni és üzembe helyezni az új Speech-erőforrás egy kis időt vesz igénybe. Válassza ki **rövid** az új erőforrás adatainak megtekintéséhez.

     ![A rövid útmutató panel](media/index/try-speech-api-create-speech5.png)

1. Agyors üzembe helyezés szakaszban válassza az 1. lépés alatt lévő **kulcsok** hivatkozást az előfizetési kulcsok megjelenítéséhez. Minden előfizetési csomaghoz tartozik két kulcs; mindkét kulcsot is használhatja az alkalmazásban. Válassza ki a gomb mellett minden egyes kulcs másolja a vágólapra a Beillesztés elhelyezni a kódban.

> [!NOTE]
> Standard szintű előfizetés korlátlan számú egy vagy több régióban hozhat létre. Azonban csak egy ingyenes szintű előfizetést hozhat létre. Modell-üzembehelyezések az ingyenes szintet, amely 7 napig marad a fel nem használt automatikusan decomissioned lesz.

## <a name="switch-to-a-new-subscription"></a>Új előfizetésre Váltás

Váltson egy előfizetésből egy másikba, például amikor az ingyenes próbaverzió érvényessége lejár, vagy az alkalmazás közzétételekor cserélje le a régió és előfizetési kulcsot a kódban a régió és egy előfizetési kulcsra az új Azure-erőforrás.

> [!NOTE]
> Ingyenes próbaverziós kulcsok jönnek létre az USA nyugati RÉGIÓJA (`westus`) régióban. Az Azure-irányítópulton keresztül létrehozott előfizetés lehet néhány más régióban található, amennyiben így dönt.

* Ha az alkalmazás egy [beszéd SDK](speech-sdk.md), például adja meg a régiókód `westus`, a beszéd-konfiguráció létrehozása során.
* Ha az alkalmazás a Speech Services [REST API](rest-apis.md)-jait használja, a régió a kérelmek elkészítésekor használt végpont URI része.

Régió létrehozott kulcsok csak az adott régióban érvényesek. Próbál használni őket a más régiókban hitelesítési hibákat eredményez.

## <a name="next-steps"></a>További lépések

Hajtsa végre a 10 perces gyors útmutatók, vagy tekintse meg az SDK-minták:

> [!div class="nextstepaction"]
> [Rövid útmutató: Beszédfelismerés felismerése C# ](quickstart-csharp-dotnet-windows.md)a 
>  [Speech SDK](speech-sdk.md#get-the-samples) -mintákban
