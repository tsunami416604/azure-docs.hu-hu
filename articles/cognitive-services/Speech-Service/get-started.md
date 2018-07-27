---
title: Próbálja ki ingyenesen a Beszédszolgáltatást
description: Fedezze fel, hogyan próbálhatja a Speech service költségek nélkül.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/17/2018
ms.author: v-jerkin
ms.openlocfilehash: a9972d19d33c69ea2eb1b0e00512f37a315b92d8
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285016"
---
# <a name="try-the-speech-service-for-free"></a>Próbálja ki ingyenesen a Beszédszolgáltatást

A Speech service használatbavétele egyszerű és megfizethető. Egy 30 napos ingyenes próbaverzió lehetővé teszi, hogy Ön mit a szolgáltatás tegye és eldönteni, hogy ez az alkalmazás igényeinek megfelelő felderítése.

Ha több időre van szüksége, iratkozzon fel a Microsoft Azure-fiók – együttműködik a beszédfelismerési szolgáltatás fizetős legfeljebb 30 napig felé alkalmazható szolgáltatási jóváírás a 200 USD.

Végül a Speech service kínál egy ingyenes, az alacsony terheltségű szint megfelelő alkalmazások fejlesztéséhez. Ez az ingyenes előfizetés még a szolgáltatási jóváírás lejárta után is megtarthatja.

## <a name="free-trial"></a>Ingyenes próbaidőszak

A 30 napos ingyenes próbaverzió hozzáférést biztosít az S0 standard tarifacsomag korlátozott ideig. Regisztráció a 30 napos ingyenes próbaverziójára, kövesse az alábbi lépéseket.

1. Nyissa meg a [próbálja meg a Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) lapot.

1. Váltás a Speech lapra, majd kattintson a **első API-kulcs** gomb melletti "Beszédszolgáltatások".

   ![Beszéd szolgáltatások lap](media/index/try-speech-api-free-trial1.png)<br>
   ![API-kulcs](media/index/try-speech-api-free-trial2.png)

3. Fogadja el a feltételeket, és válassza ki a területi beállítás a legördülő menüből.

   ![Feltételek elfogadása](media/index/try-speech-api-free-trial3.png)

4. Jelentkezzen be a Microsoft, a Facebook, a LinkedIn, vagy a GitHub-fiók használatával. Vagy előfordulhat, hogy regisztráljon egy ingyenes Microsoft-fiók:

    * Nyissa meg a [Microsoft fiókportál](https://account.microsoft.com/account).
    * Kattintson a **Bejelentkezés Microsoft-fiókkal**.

    ![Bejelentkezés](media/index/try-speech-api-free-trial4.png)

    * Ha a rendszer kéri, hogy jelentkezzen be, kattintson a "Hozzon létre egyet."

    ![Új fiók létrehozása](media/index/try-speech-api-free-trial5.png)

    * A lépések, amelyek hajtsa végre, írja be az e-mail cím vagy telefonszám egy jelszót, és kövesse az utasításokat az új Microsoft-fiók ellenőrzése.

Miután bejelentkezett, az ingyenes próbaidőszak megkezdése. A megjelenő weblap felsorolja az összes, a Cognitive Services, amelyhez jelenleg rendelkezik a próba-előfizetések. Két előfizetési kulcs van listázva, melletti "Beszédszolgáltatások." Egyiket sem használhatja az alkalmazásokban.

> [!NOTE]
> Ingyenes próba-előfizetések szerepelnek, az USA nyugati régiója. Ügyeljen arra, használja a végpont régióhoz tartozó, a kérések küldésekor.

## <a name="new-azure-account"></a>Új Azure-fiók

Új Azure-fiókok legfeljebb 30 napig érvényes 200 USD értékű szolgáltatás értékű kreditet kapnak. Ez a jóváírás a további Fedezze fel a beszédfelismerési szolgáltatás vagy alkalmazás fejlesztésének megkezdése használható.

Regisztráljon egy új Azure-fiókkal, kövesse az alábbi lépéseket.

1. Nyissa meg a [Azure regisztrációs oldalra](https://azure.microsoft.com/free/ai/). 

1. Kattintson a **ingyenes**.

    ![Kezdetben ingyenes](media/index/try-speech-api-new-azure1.png)

3. Jelentkezzen be Microsoft-fiókjával. Ha még nincs fiókja:

    * Nyissa meg a [Microsoft fiókportál](https://account.microsoft.com/account).
    * Kattintson a **Bejelentkezés Microsoft-fiókkal**.
    * Ha a rendszer kéri, hogy jelentkezzen be, kattintson a "Hozzon létre egyet."
    * A lépések, amelyek hajtsa végre, írja be az e-mail cím vagy telefonszám egy jelszót, és kövesse az utasításokat az új Microsoft-fiók ellenőrzése.

1. Adja meg a többi egy fiókot a kért információkat. Adja meg az országot és a nevét, és adjon meg egy telefonszámot és e-mail-címet.

    ![Adja meg adatait](media/index/try-speech-api-new-azure2.png)

    Telefonos és hitelkártyaszám megadásával igazolhatja a személyazonosságát, majd fogadja el az Azure-felhasználó szerződését. (A megadott bankkártyára nem számítjuk fel.)

    ![Szerződés feltételeinek elfogadása](media/index/try-speech-api-new-azure3.png)

Ingyenes Azure-fiók jön létre. Kövesse a következő szakaszban a Speech service-előfizetés elindításához.

## <a name="create-a-speech-resource-in-azure"></a>Beszéd erőforrás létrehozása az Azure-ban

A beszédfelismerési szolgáltatás erőforrás hozzáadása az Azure-fiókjával, kövesse az alábbi lépéseket.

1. Jelentkezzen be a [az Azure portal](https://ms.portal.azure.com/) Microsoft-fiókjával.

1. Kattintson a **erőforrás létrehozása** (a zöld **+** ikon), a portál bal felső.

    ![Erőforrás létrehozása](media/index/try-speech-api-create-speech1.png)

1. Az új ablakban keresse a beszédfelismerés.

    ![Kattintson a beszédfelismerés](media/index/try-speech-api-create-speech2.png)

1. A keresési eredmények között kattintson a "Speech (előzetes verzió)."

1. Kattintson a **létrehozás** gomb a Speech service panel alján.

    ![Kattintson a létrehozás](media/index/try-speech-api-create-speech3.png)

1. A létrehozás panelen adja meg:

    * Az új erőforrás neve. A név segítségével megkülönböztetésére, amelyek ugyanazt a szolgáltatást több előfizetést.
    * Válassza ki az Azure-előfizetést, amelyhez az új erőforrás a határozza meg, hogy a díjak számlázása van társítva.
    * Válassza ki a régiót, ahol fogja használni az erőforrás. A beszédfelismerési szolgáltatás jelenleg elérhető az USA nyugati RÉGIÓJA, Kelet-Ázsia és Észak-Európa régióban.
    * Válassza ki a tarifacsomagot, vagy F0 (korlátozott ingyenes előfizetés) vagy S0 (standard előfizetés). Kattintson a **díjszabási részletek megtekintése** bővebb információt az egyes szintek díjszabását és a használati kvótákat.
    * Hozzon létre egy új erőforráscsoportot, beszéd ehhez az előfizetéshez, vagy rendelje hozzá egy már meglévőt. Erőforráscsoportokat tarthatja rendezve különböző Azure-előfizetéseit.
    * A jövőben az előfizetés hozzáférés kényelmes, jelölje meg a **rögzítés az irányítópulton** jelölőnégyzetet.
    * Kattintson a **létrehozása.**

    ![Kattintson a létrehozás panel](media/index/try-speech-api-create-speech4.png)

    Ez eltarthat egy kis ideig, létrehozni és üzembe helyezni az új Speech-erőforrás. A rövid útmutató panelen jelenik meg az új erőforrás adatainak.

    ![A rövid útmutató panel](media/index/try-speech-api-create-speech5.png)

1. Kattintson a **kulcsok** a rövid útmutató panelen az előfizetési kulcsok megjelenítése 1. lépés alatti hivatkozásra. Minden előfizetési csomaghoz tartozik két kulcs; Előfordulhat, hogy használja az alkalmazásban. A gombra kattintva másolja a vágólapra a Beillesztés elhelyezni a kódban, minden kulcs mellett.

> [!NOTE]
> Minden standard szintű előfizetések száma egy vagy több régióban is létrehozhat. Azonban előfordulhat, hogy hozzon létre csak egy ingyenes szintű előfizetést.

## <a name="next-steps"></a>További lépések

Töltse le az SDK-t és néhány mintakódját tapasztalhat a Speech service.

> [!div class="nextstepaction"]
> [Beszéd SDK-k](speech-sdk.md)

> [!div class="nextstepaction"]
> [Mintakód](samples.md)
