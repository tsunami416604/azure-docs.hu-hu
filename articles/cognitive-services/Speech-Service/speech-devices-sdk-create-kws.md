---
title: Egy egyéni ébresztési word létrehozása |} Microsoft Docs
description: Egy egyéni ébresztési word létrehozása a beszédfelismerés eszközök SDK-ban.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 2575ed24bb931ca4da05dd6663b976406af590e6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349730"
---
# <a name="create-a-custom-wake-word-using-speech-service"></a>Hozzon létre egy egyéni ébresztési word beszéd szolgáltatással

Az eszköz mindig a egy ébresztési (elkezdésekor) figyeli. Például a "Hey Cortana" szót a Cortana Segéd az ébresztési. Amikor a felhasználó azt jelzi, hogy az ébresztési word, az eszköz elindítja a minden ezt követő hang küld a felhő, amíg a felhasználó abbahagyja beszéd. Az ébresztési word testreszabása különböztetni az eszközét, és a vállalati arculat megerősítése hatékony módot.

Ebből a cikkből megismerheti, hogyan hozzon létre egy egyéni ébresztési word, az eszköz számára.

## <a name="choosing-an-effective-wake-word"></a>Egy hatékony ébresztési word kiválasztása

Vegye figyelembe a következő irányelveket, ébresztési szót kiválasztásakor.

* Az ébresztési word egy angol nyelvű szót vagy kifejezést kell lennie. Elméletileg legfeljebb két másodpercen azaz.

* A 4 – 7 szótagokat határoznak szavak a legmegfelelőbb. Például "Hey, számítógép" egy jó ébresztési szót, míg csak "Hey" gyenge egy.

* Ébresztési szavak közös angol kiejtés szabályokat kell követnie.

* A következő általános angol kiejtés szabályok egyedi vagy még feldolgozott szót csökkentheti a vakriasztások. Például a "computerama" jó ébresztési szó lehet.

* Ne válasszon a közös szót. Például "keleti-afrikai" és "Ugrás" szavak, amelyek személyek gyakran szokásos téma szerint. Az eszköz hamis eseményindítók lehetnek.

* Ne használja az alternatív kiejtés rendelkezhetnek ébresztési szót. Felhasználók számára az eszköz válaszolni a "jobb oldali" kiejtés ismernie kell. Például "509" sikerült hangsúlyozottan kell, a "öt nulla kilenc", "öt bizony kilenc", vagy a "öt száz és kilenc." "R.E.I." mint hangsúlyozottan kell "R E vagyok" vagy "Ray." "Live" sikerült hangsúlyozottan mint [līv] vagy [liv].

* Ne használjon speciális karaktereket, a szimbólumok és a számjegy. Például "Ugrás #" és "20 + macska" nem lenne megfelelő ébresztési szavakat. Azonban "Ugrás éles" vagy "20 macska plusz" esetleg fog működni. Továbbra is a szimbólumok használata a márkajelzési beállításokat, és a megfelelő kiejtés megerősítése marketing és dokumentáció segítségével.

> [!NOTE]
> Ha az ébresztési Word védjegyzett szót, győződjön meg, hogy Ön a védjegyekre vonatkozó saját, ellenkező esetben a védjegyekre vonatkozó tulajdonos engedélye a használatára. A Microsoft nem felelős a jogi problémák miatt előfordulhat, hogy a választott ébresztési word.

## <a name="creating-your-wake-word"></a>Az ébresztési word létrehozása

Egyéni ébresztési szót az eszköz használata előtt létre kell hoznia a Microsoft egyéni ébresztés Word létrehozó szolgáltatások használatával. Miután megadta ébresztési szó, a szolgáltatás eredményez a fájlnevet, amely ezután telepíti a fejlesztői kit engedélyezése az eszközön a ébresztési word-kiszolgálóra.

1. Lépjen a [egyéni beszéd portálon](https://cris.ai/).

2. Új fiók létrehozása az Azure Active Directory meghívót kapott e-mail címet. 

    ![Új fiók létrehozása](media/speech-devices-sdk/wake-word-1.png)
 
3.  Miután bejelentkezett, töltse ki az űrlapot, majd kattintson az **indítsa el az út.**

    ![a bejelentkezés sikerült](media/speech-devices-sdk/wake-word-3.png)
 
4. A **egyéni ébresztés Word** lap nem érhető el a nyilvános, nem áll fenn, amely nincs kapcsolat. Kattintson a, vagy inkább illessze be a hivatkozás: https://cris.ai/customkws.

    ![rejtett lap](media/speech-devices-sdk/wake-word-4.png)
 
6. Az Ön által választott, majd az ébresztési Word típus **Submit** azt.

    ![Adja meg az ébresztési word](media/speech-devices-sdk/wake-word-5.png)
 
7. A fájlok generálása néhány percig is eltarthat. A böngésző lapon meg kell jelennie egy forgó kör. Után néhány percet, egy információs sáv megjelenik, amely kéri, hogy töltse le a `.zip` fájlt.

    ![a fogadó .zip fájl](media/speech-devices-sdk/wake-word-6.png)

8. Mentse a `.zip` fájlt a számítógépre. A fájl központi telepítése az egyéni ébresztési word szoftverfejlesztői készlet szükséges utasításait követve [Megismerkedés a beszédfelismerés eszközök SDK](speech-devices-sdk-qsg.md).

9. Most is **jelentkezzen ki.**

## <a name="next-steps"></a>További lépések

A kezdéshez lekérni egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) és a beszédfelismerés eszközök SDK regisztráljon.

> [!div class="nextstepaction"]
> [Iratkozzon fel a beszédfelismerés eszközök SDK](get-speech-devices-sdk.md)

