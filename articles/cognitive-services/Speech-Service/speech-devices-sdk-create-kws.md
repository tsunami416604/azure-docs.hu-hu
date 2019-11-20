---
title: Egyéni kulcsszó-beszédfelismerési szolgáltatás létrehozása
titleSuffix: Azure Cognitive Services
description: Az eszköz mindig egy kulcsszót (vagy kifejezést) figyel. Ha a felhasználó a kulcsszót mondja, az eszköz az összes további hangot elküldi a felhőbe, amíg a felhasználó nem állítja a beszédet. A kulcsszó személyre szabása hatékony módszert tesz lehetővé az eszköz megkülönböztetésére és a saját arculatának megerősítésére.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: erhopf
ms.openlocfilehash: 9a5b5de71ee290b39603968cf4309171689e22e4
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184828"
---
# <a name="create-a-custom-keyword-by-using-the-speech-service"></a>Egyéni kulcsszó létrehozása a Speech Service használatával

Az eszköz mindig egy kulcsszót (vagy kifejezést) figyel. Például a "Hey Cortana" a Cortana segéd egyik kulcsszója. Ha a felhasználó a kulcsszót mondja, az eszköz az összes további hangot elküldi a felhőbe, amíg a felhasználó nem állítja a beszédet. A kulcsszó személyre szabása hatékony módszert tesz lehetővé az eszköz megkülönböztetésére és a saját arculatának megerősítésére.

Ebből a cikkből megtudhatja, hogyan hozhat létre egyéni kulcsszót az eszközhöz.

## <a name="choose-an-effective-keyword"></a>Érvényes kulcsszó kiválasztása

A kulcsszó kiválasztásakor vegye figyelembe a következő irányelveket:

* A kulcsszónak angol szónak vagy kifejezésnek kell lennie. Érdemes igénybe legfeljebb két másodpercen övezi.

* A 4-7 szótagokat határoznak szavak működnek a legjobban. Például a "Hé, számítógép" egy jó kulcsszó. Csak a "Hey", adott gyenge.

* A kulcsszavaknak a gyakori angol kiejtési szabályokat kell követniük.

* Egy egyedi vagy akár egy közös angol nyelvű írásmódja szabályok a következő feldolgozott szó előfordulhat, hogy csökkenti a vakriasztások. Előfordulhat például, hogy a "computerama" kulcsszó jó.

* Ne adja meg egy gyakori szót. Például "étkezési" és "go" olyan szavak, amelyek az emberek gyakran tegyük fel, a szokásos beszélgetésekben. Az eszköz hamis eseményindítók lehetnek.

* Ne használjon olyan kulcsszót, amely alternatív kiejtésekkel rendelkezhet. Felhasználók a saját eszköz válaszol "megfelelő" kiejtés ismernie kell. Ha például olyan "509" is ejtsd, "öt nulla nine," "öt hoppá kilenc," vagy "öt száz és kilenc." "R.E.I." "az r-e-i" vagy "ray." ejtsd is Is lehet ejtsd a "Live", "/līv/" vagy "/liv/".

* Ne használja a speciális karaktereket, szimbólumok és számjegy. Például a "Go #" és a "20 + Cats" nem lenne jó kulcsszó. Azonban "Ugrás éles" vagy "húsz macskák plusz" működhetnek. Továbbra is a szimbólumok használata a márkajelzési beállításokat, és marketing- és dokumentáció segítségével a megfelelő kiejtés megerősítése.

> [!NOTE]
> Ha kulcsszóként választ egy védjeggyel ellátott szót, akkor győződjön meg arról, hogy a védjegy tulajdonosa, vagy hogy a védjegy tulajdonosának van-e engedélye a szó használatára. A Microsoft nem vállal felelősséget az Ön által választott kulcsszótól esetlegesen felmerülő jogi problémákért.

## <a name="create-your-keyword"></a>A kulcsszó létrehozása

Egyéni kulcsszó használata előtt létre kell hoznia egy kulcsszót a [Speech Studio](https://aka.ms/sdsdk-speechportal) [Egyéni kulcsszavas](https://aka.ms/sdsdk-wakewordportal) oldalának használatával. A kulcsszó megadása után létrehoz egy fájlt, amelyet központilag telepít az eszközre.

1. Lépjen a [Speech studióba](https://aka.ms/sdsdk-speechportal) , és **Jelentkezzen be** , vagy ha még nem rendelkezik beszédfelismerési előfizetéssel, válassza az [**előfizetés létrehozása**](https://go.microsoft.com/fwlink/?linkid=2086754)lehetőséget.

1. Az [egyéni kulcsszó](https://aka.ms/sdsdk-wakewordportal) lapon írja be a kívánt kulcsszót, majd kattintson a **kulcsszó hozzáadása**lehetőségre. Van néhány [irányelvünk](#choose-an-effective-keyword) , amely segítséget nyújt egy hatékony kulcsszó kiválasztásához. A támogatás jelenleg az en-US nyelvre korlátozódik.

    ![Adja meg a kulcsszót](media/speech-devices-sdk/custom-kws-portal-enter-keyword.png)

1. A portál mostantól jelölt kiejtéseket hoz létre a kulcsszava számára. Hallgassa meg az egyes jelölteket a lejátszás gombokra kattintva, és távolítsa el az összes helytelen kiejtés melletti ellenőrzéseket. Ha csak a jó kiejtéseket jelölte be, válassza a **Submit (Küldés** ) lehetőséget a kulcsszó létrehozásának megkezdéséhez. Ha módosítani szeretné a kulcsszót, először távolítsa el a meglévőt úgy, hogy a sor jobb oldalán megjelenő Törlés gombra kattint, miközben fölé viszi.

    ![A kulcsszó áttekintése](media/speech-devices-sdk/custom-kws-portal-review-keyword.png)

1. A modell létrehozása akár egy percet is igénybe vehet. Ekkor a rendszer kérni fogja a fájl letöltését.

    ![A kulcsszó letöltése](media/speech-devices-sdk/custom-kws-portal-download-keyword.png)

1. Mentse a .zip-fájlt a számítógépre. Erre a fájlra szüksége lesz az egyéni kulcsszó üzembe helyezéséhez az eszközön.

## <a name="next-steps"></a>Következő lépések

Tesztelje egyéni kulcsszavait a [Speech Devices SDK](https://aka.ms/sdsdk-quickstart)gyors útmutatójában.
