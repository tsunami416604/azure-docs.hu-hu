---
title: Hozzon létre egy egyéni ébresztési szó – beszédszolgáltatások
titleSuffix: Azure Cognitive Services
description: Az eszköz mindig figyeli a hálózati ébresztési szó (vagy kifejezés). Amikor a felhasználó szöveget az ébresztési a word, az eszköz küld minden későbbi hang a felhőbe, mindaddig, amíg a felhasználó leállítja a különféle. Hatékony módja az eszköz megkülönböztetéséhez és erősítse a márkajelzési beállításokat az ébresztési word testreszabása.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 2280af4bf37fdb3cd12482da855f979a9180f0ec
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020552"
---
# <a name="create-a-custom-wake-word-by-using-the-speech-service"></a>Hozzon létre egy egyéni ébresztési szót a Speech szolgáltatással

Az eszköz mindig figyeli a hálózati ébresztési szó (vagy kifejezés). Például a "Hey Cortana" a Cortana Segéd az ébresztési szó lesz. Amikor a felhasználó szöveget az ébresztési a word, az eszköz küld minden későbbi hang a felhőbe, mindaddig, amíg a felhasználó leállítja a különféle. Hatékony módja az eszköz megkülönböztetéséhez és erősítse a márkajelzési beállításokat az ébresztési word testreszabása.

Ebből a cikkből megismerheti, hogyan hozhat létre egy egyéni ébresztési word, az eszköz.

## <a name="choose-an-effective-wake-word"></a>Válasszon egy érvényes ébresztési word

Ha úgy dönt, hogy ébresztési szót, ügyeljen a következőkre:

* Az ébresztési word egy angol nyelvű szót vagy kifejezést kell lennie. Érdemes igénybe legfeljebb két másodpercen övezi.

* A 4-7 szótagokat határoznak szavak működnek a legjobban. Például a "Hey, számítógép" egy jó ébresztési szót. Csak a "Hey", adott gyenge.

* Ébresztési szavak közös angol nyelvű írásmódja szabályok kövessék.

* Egy egyedi vagy akár egy közös angol nyelvű írásmódja szabályok a következő feldolgozott szó előfordulhat, hogy csökkenti a vakriasztások. Például a "computerama" egy jó ébresztési szó lehet.

* Ne adja meg egy gyakori szót. Például "étkezési" és "go" olyan szavak, amelyek az emberek gyakran tegyük fel, a szokásos beszélgetésekben. Az eszköz hamis eseményindítók lehetnek.

* Ne használja, előfordulhat, hogy egyéb kiejtés ébresztési szót. Felhasználók a saját eszköz válaszol "megfelelő" kiejtés ismernie kell. Ha például olyan "509" is ejtsd, "öt nulla nine," "öt hoppá kilenc," vagy "öt száz és kilenc." "R.E.I." "az r-e-i" vagy "ray." ejtsd is Is lehet ejtsd a "Live", "/līv/" vagy "/liv/".

* Ne használja a speciális karaktereket, szimbólumok és számjegy. Például "Go #" és "20 + macskák" nem lenne jó ébresztési szavakat. Azonban "Ugrás éles" vagy "húsz macskák plusz" működhetnek. Továbbra is a szimbólumok használata a márkajelzési beállításokat, és marketing- és dokumentáció segítségével a megfelelő kiejtés megerősítése.

> [!NOTE]
> Ha az ébresztési Word Védjegyezett szót, győződjön meg, hogy Ön a tulajdonosa, a védjegyekre, illetve, hogy Ön jogosult a védjegy tulajdonosától a Word alkalmazást. A Microsoft nem vállal jogi Ön választhatja ki a hálózati ébresztési word esetlegesen felmerülő problémákat.

## <a name="create-your-wake-word"></a>Az ébresztési word létrehozása

Egy egyéni ébresztési szót az eszköz használata előtt szüksége, hozzon létre egy ébresztési szót a Microsoft egyéni ébresztési Word generációs szolgáltatással. Miután megadta a ébresztési word-, a szolgáltatás állít elő a fájlnevet, amely a szoftverfejlesztői készlet ahhoz, hogy az ébresztési word, az eszközön telepítheti.

1. Nyissa meg a [Custom Speech Service-portál](https://aka.ms/sdsdk-speechportal) és **jelentkezzen be a** vagy, ha nem rendelkezik válassza a speech előfizetés [ **előfizetés létrehozása**](https://go.microsoft.com/fwlink/?linkid=2086754)

    ![A Custom Speech Service-portál](media/speech-devices-sdk/wake-word-4.png)

1. Jelenleg a [egyéni ébresztési Word](https://aka.ms/sdsdk-wakewordportal) , és kattintson a hálózati ébresztési szót írja be **ébresztési szó hozzáadása**. Van néhány [irányelvek](#choose-an-effective-wake-word) segítség egy hatékony kulcsszó kiválasztásához. Csak jelenleg en-US nyelven.

    ![Adja meg az ébresztési word](media/speech-devices-sdk/wake-word-5.png)

1. Az ébresztési szó három egyéb kiejtés jön létre. Választhat, adja meg az összes kiejtés. Válassza ki **küldés** ébresztési szó létrehozásához. Ha módosítani szeretné az ébresztési szó. Távolítsa el a meglévőt először a írásmódja sor jelenik meg a Törlés ikonra rámutatáskor.

    ![Tekintse át az ébresztési word](media/speech-devices-sdk/wake-word-6.png)

1. A modell létrehozását legfeljebb egy percig is eltarthat. A rendszer kéri le a fájlt.

    ![Az ébresztési word letöltése](media/speech-devices-sdk/wake-word-7.png)

1. Mentse a .zip-fájlt a számítógépre. Ezt a fájlt az egyéni ébresztési word üzembe development Kit kell.

## <a name="next-steps"></a>További lépések

Tesztelje az egyéni ébresztési word- [Speech eszközök SDK rövid](https://aka.ms/sdsdk-quickstart).
