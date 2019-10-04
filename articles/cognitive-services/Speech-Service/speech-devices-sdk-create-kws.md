---
title: Egyéni ébresztési Word-Speech szolgáltatás létrehozása
titleSuffix: Azure Cognitive Services
description: Az eszköz mindig figyeli a hálózati ébresztési szó (vagy kifejezés). Amikor a felhasználó szöveget az ébresztési a word, az eszköz küld minden későbbi hang a felhőbe, mindaddig, amíg a felhasználó leállítja a különféle. Hatékony módja az eszköz megkülönböztetéséhez és erősítse a márkajelzési beállításokat az ébresztési word testreszabása.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 2bc1a6cbbf1e0d790326849a41b0788e332daa31
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553102"
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

Ahhoz, hogy az eszközhöz egyéni ébresztési szót lehessen használni, létre kell hoznia egy felébresztő szót a Microsoft Custom Wake Word generálási szolgáltatásával. Az ébresztési szó megadása után a szolgáltatás létrehoz egy fájlt, amelyet központilag telepít a fejlesztői csomagba, hogy engedélyezze az ébresztési szót az eszközön.

1. Lépjen a [Custom Speech Service portálra](https://aka.ms/sdsdk-speechportal) , és **Jelentkezzen be** , vagy ha nem rendelkezik beszédfelismerési előfizetéssel, válassza az [**előfizetés létrehozása**](https://go.microsoft.com/fwlink/?linkid=2086754) lehetőséget.

    ![A Custom Speech Service portál](media/speech-devices-sdk/wake-word-4.png)

1. Az [Egyéni ébresztési szó](https://aka.ms/sdsdk-wakewordportal) lapon írja be az Ön által választott ébresztés szót, és kattintson az **ébresztési szó hozzáadása**lehetőségre. Van néhány [irányelvünk](#choose-an-effective-wake-word) , amely segítséget nyújt egy hatékony kulcsszó kiválasztásához. Jelenleg csak az en-US nyelvet támogatjuk.

    ![Adja meg az ébresztési word](media/speech-devices-sdk/wake-word-5.png)

1. Az ébresztési szó három alternatív kiejtését fogja létrehozni. Kiválaszthatja az összes hasonló kiejtést. Ezután válassza a **Submit (Küldés** ) lehetőséget az ébresztési szó létrehozásához. Ha módosítani szeretné az ébresztési szót, először távolítsa el a meglévőt, amikor a kiejtési sorban megjelenik a Delete (Törlés) ikon.

    ![Az ébresztési szó áttekintése](media/speech-devices-sdk/wake-word-6.png)

1. A modell létrehozása akár egy percet is igénybe vehet. A rendszer kérni fogja a fájl letöltését.

    ![Az ébresztési szó letöltése](media/speech-devices-sdk/wake-word-7.png)

1. Mentse a .zip-fájlt a számítógépre. Erre a fájlra lesz szüksége, hogy az egyéni ébresztést a fejlesztői csomagba telepítse.

## <a name="next-steps"></a>További lépések

Tesztelje az egyéni ébresztési szót a [Speech Devices SDK](https://aka.ms/sdsdk-quickstart)gyors útmutatójában.
