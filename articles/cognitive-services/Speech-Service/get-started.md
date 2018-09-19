---
title: Próbálja ki ingyenesen a Beszédszolgáltatást
description: Fedezze fel, hogyan próbálhatja a Speech service költségek nélkül.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/17/2018
ms.author: v-jerkin
ms.openlocfilehash: cbb73b199255af7e7a91b1586b331acde035bfd7
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124512"
---
# <a name="try-the-speech-service-for-free"></a>Próbálja ki ingyenesen a Beszédszolgáltatást

A Speech service használatbavétele egyszerű és megfizethető. Egy 30 napos ingyenes próbaverzió lehetővé teszi, hogy Ön mit a szolgáltatás tegye és eldönteni, hogy ez az alkalmazás igényeinek megfelelő felderítése.

Ha több időre van szüksége, iratkozzon fel a Microsoft Azure-fiók – együttműködik a beszédfelismerési szolgáltatás fizetős legfeljebb 30 napig felé alkalmazható szolgáltatási jóváírás a 200 USD.

Végül a Speech service kínál egy ingyenes, az alacsony terheltségű szint, amely lehetővé teszi az alkalmazások fejlesztéséhez. Ez az ingyenes előfizetés még a szolgáltatási jóváírás lejárta után is megtarthatja.

## <a name="free-trial"></a>Ingyenes próbaidőszak

A 30 napos ingyenes próbaverzió hozzáférést biztosít a standard díjcsomag korlátozott időre.

Regisztráció a 30 napos ingyenes próbaverziójára:

1. Lépjen a [a Cognitive Services kipróbálása](https://azure.microsoft.com/try/cognitive-services/).

1. Válassza ki a **Speech API-k** fülre.

   ![Beszéd szolgáltatások lap](media/index/try-speech-api-free-trial1.png)
   
1. A **beszédszolgáltatások <sup>előzetes</sup>**, jelölje be a **API-kulcs lekérése** gombra.

   ![API-kulcs](media/index/try-speech-api-free-trial2.png)

1. Fogadja el a feltételeket, és válassza ki a területi beállítás a legördülő menüből.

   ![Feltételek elfogadása](media/index/try-speech-api-free-trial3.png)

1. Jelentkezzen be a Microsoft, a Facebook, a LinkedIn, vagy a GitHub-fiók használatával.

    Regisztrálhat egy ingyenes Microsoft-fiókot a [Microsoft fiókportál](https://account.microsoft.com/account). Első lépésként kattintson **Bejelentkezés Microsoft-fiókkal** és, ha a rendszer kéri, hogy jelentkezzen be, kattintson a **hozzon létre egyet.** A lépésekkel hozhat létre, és ellenőrizze az új Microsoft-fiókjával.

Miután bejelentkezett, próbálja meg a Cognitive Services, az ingyenes próbaverzió kezdődik. A megjelenő képernyőn látható weblapon, amelyhez jelenleg rendelkezik próba-előfizetések az Azure Cognitive Services szolgáltatásokat sorolja fel. Két előfizetési kulcs van listázva, mellett **beszédszolgáltatások**. Használhatja az egyiket sem az alkalmazásokban.

> [!NOTE]
> Ingyenes próba-előfizetések szerepelnek, az USA nyugati régiója. Amikor a kéréseket, ügyeljen arra, hogy a `westus` végpont.

## <a name="new-azure-account"></a>Új Azure-fiók

Új Azure-fiókok jóváírás 200 USD értékű szolgáltatás, amely legfeljebb 30 napig érhető el. Ez a jóváírás is használhat, részletesebb megismerése a beszédfelismerési szolgáltatás vagy alkalmazás fejlesztésének megkezdése.

Regisztráljon egy új Azure-fiókkal, keresse fel a [oldala az Azure-előfizetési](https://azure.microsoft.com/free/ai/), kattintson a **ingyenes próba megkezdése,** és az új Azure-fiók létrehozása a Microsoft-fiókjával.

Regisztrálhat egy ingyenes Microsoft-fiókot a [Microsoft fiókportál](https://account.microsoft.com/account). Első lépésként kattintson **Bejelentkezés Microsoft-fiókkal** és, ha a rendszer kéri, hogy jelentkezzen be, kattintson a **hozzon létre egyet.** A lépésekkel hozhat létre, és ellenőrizze az új Microsoft-fiókjával.

Miután létrehozta az Azure-fiókjával, kövesse a következő szakaszban a Speech service-előfizetés indítása.

## <a name="create-a-speech-resource-in-azure"></a>Beszéd erőforrás létrehozása az Azure-ban

A beszédfelismerési szolgáltatás-erőforrás (ingyenes vagy fizetős szint) hozzáadása az Azure-fiókjával:

1. Jelentkezzen be a [az Azure portal](https://ms.portal.azure.com/) Microsoft-fiókjával.

1. Válassza ki **erőforrás létrehozása** , a portál bal felső.

    ![Erőforrás létrehozása](media/index/try-speech-api-create-speech1.png)

1. Az a **új** ablakot, és keressen **speech**.

1. A keresési eredmények között, válassza ki a **Speech (előzetes verzió)**.

    ![Válassza ki a Speech (előzetes verzió)](media/index/try-speech-api-create-speech2.png)

1. A **Speech (előzetes verzió)**, kattintson a **létrehozás** gombra.

    ![Kattintson a Létrehozás gombra](media/index/try-speech-api-create-speech3.png)

1. A **létrehozás**, adja meg:

    * Az új erőforrás neve. A név segítségével megkülönböztetésére, amelyek ugyanazt a szolgáltatást több előfizetést.
    * Válassza ki az Azure-előfizetést, amelyhez az új erőforrás a határozza meg, hogy a díjak számlázása van társítva.
    * Válassza ki a régiót, ahol fogja használni az erőforrás. A beszédfelismerési szolgáltatás jelenleg elérhető az USA nyugati RÉGIÓJA, Kelet-Ázsia és Észak-Európa régióban.
    * Válassza ki, vagy egy ingyenes vagy fizetős tarifacsomagra. Kattintson a **díjszabási részletek megtekintése** kapcsolatban az egyes szintek díjszabását és a használati kvótákat.
    * Hozzon létre egy új erőforráscsoportot, beszéd ehhez az előfizetéshez, vagy az előfizetés hozzárendelése egy meglévő erőforráscsoportot. Erőforráscsoportokat tarthatja rendezve különböző Azure-előfizetéseit.
    * A jövőben az előfizetés hozzáférés kényelmes, válassza ki a **rögzítés az irányítópulton** jelölőnégyzetet.
    * Válassza ki **létrehozása.**

    ![Kattintson a Létrehozás gombra](media/index/try-speech-api-create-speech4.png)

    Létrehozni és üzembe helyezni az új Speech-erőforrás egy kis időt vesz igénybe. Válassza ki **rövid** az új erőforrás adatainak megtekintéséhez.

    ![A rövid útmutató panel](media/index/try-speech-api-create-speech5.png)

1. Alatt **rövid**, kattintson a **kulcsok** az előfizetési kulcsok megjelenítése 1. lépés alatti hivatkozásra. Minden előfizetési csomaghoz tartozik két kulcs; mindkét kulcsot is használhatja az alkalmazásban. Válassza ki a gomb mellett minden egyes kulcs másolja a vágólapra a Beillesztés elhelyezni a kódban.

> [!NOTE]
> Standard szintű előfizetés korlátlan számú egy vagy több régióban hozhat létre. Azonban csak egy ingyenes szintű előfizetést hozhat létre. Modell-üzembehelyezések az ingyenes szintet, amely 7 napig marad a fel nem használt automatikusan decomissioned lesz.

## <a name="switch-to-a-new-subscription"></a>Új előfizetésre Váltás

Váltson egy előfizetésből egy másikba, például amikor az ingyenes próbaverzió érvényessége lejár, vagy az alkalmazás közzétételekor cserélje le a régió és előfizetési kulcsot a kódban a régió és egy előfizetési kulcsra az új Azure-erőforrás.

> [!NOTE]
> Ingyenes próbaverziós kulcsok jönnek létre az USA nyugati RÉGIÓJA (`westus`) régióban. Az Azure-irányítópulton keresztül létrehozott előfizetés lehet néhány más régióban található, amennyiben így dönt.

* Ha az alkalmazás egy [beszéd SDK](speech-sdk.md), mint például adja meg a régiókód `westus`hárítható el, `RecognizerFactory`. 
* Ha az alkalmazás használja a beszédfelismerési szolgáltatás egyik [REST API-k](rest-apis.md), a régiót a végpont URI-kérelem indítására használ részét képezi.

Régió létrehozott kulcsok csak az adott régióban érvényesek. Próbál használni őket a más régiókban hitelesítési hibákat eredményez.

## <a name="next-steps"></a>További lépések

Hajtsa végre a 10 perces gyors útmutatók, vagy tekintse meg az SDK-minták:

> [!div class="nextstepaction"]
> [Gyors útmutató: A C# beszédfelismerést](quickstart-csharp-dotnet-windows.md)
> [beszéd SDK-minták](speech-sdk.md#get-the-samples)
