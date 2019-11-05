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
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: bf9afb66163532b4095e0d30b1167010320abbf8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490919"
---
# <a name="create-a-custom-keyword-by-using-the-speech-service"></a>Egyéni kulcsszó létrehozása a Speech Service használatával

Az eszköz mindig egy kulcsszót (vagy kifejezést) figyel. Például a "Hey Cortana" a Cortana segéd egyik kulcsszója. Ha a felhasználó a kulcsszót mondja, az eszköz az összes további hangot elküldi a felhőbe, amíg a felhasználó nem állítja a beszédet. A kulcsszó személyre szabása hatékony módszert tesz lehetővé az eszköz megkülönböztetésére és a saját arculatának megerősítésére.

Ebből a cikkből megtudhatja, hogyan hozhat létre egyéni kulcsszót az eszközhöz.

## <a name="choose-an-effective-keyword"></a>Érvényes kulcsszó kiválasztása

A kulcsszó kiválasztásakor vegye figyelembe a következő irányelveket:

* A kulcsszónak angol szónak vagy kifejezésnek kell lennie. Nem lehet hosszabb két másodpercnél.

* A 4 – 7 szótag szavai a legjobban működnek. Például a "Hé, számítógép" egy jó kulcsszó. Csak a "Hey" egy rossz.

* A kulcsszavaknak a gyakori angol kiejtési szabályokat kell követniük.

* Egy egyedi vagy akár egy, a közös angol Kiejtési szabályt követő szó is csökkentheti a téves pozitív értéket. Előfordulhat például, hogy a "computerama" kulcsszó jó.

* Ne válasszon közös szót. Például az "Eat" és a "Go" kifejezés olyan szavakat mutat be, amelyekben gyakran előfordul a hétköznapi beszélgetés. Lehet, hogy az eszköz hamis eseményindítókat tartalmaz.

* Ne használjon olyan kulcsszót, amely alternatív kiejtésekkel rendelkezhet. A felhasználóknak ismerniük kell a "jobb" kiejtést, hogy az eszközük válaszoljon. Például: "509" lehet "5 0 9", "5 0 9" vagy "509". "R.E.I." "r-e-i" vagy "Ray" lehet. Az "élő" kifejezés "/līv/" vagy "/Liv/" lehet.

* Ne használjon speciális karaktereket, szimbólumokat vagy számokat. Például a "Go #" és a "20 + Cats" nem lenne jó kulcsszó. Azonban a "Go Sharp" vagy a "húsz Plus Cats" is működhet. Továbbra is használhatja a branding szimbólumokat, és a marketing és a dokumentáció használatával megerősítheti a megfelelő kiejtést.

> [!NOTE]
> Ha kulcsszóként választ egy védjeggyel ellátott szót, akkor győződjön meg arról, hogy a védjegy tulajdonosa, vagy hogy a védjegy tulajdonosának van-e engedélye a szó használatára. A Microsoft nem vállal felelősséget az Ön által választott kulcsszótól esetlegesen felmerülő jogi problémákért.

## <a name="create-your-keyword"></a>A kulcsszó létrehozása

Ahhoz, hogy az eszközhöz egyéni kulcsszót lehessen használni, létre kell hoznia egy kulcsszót a Microsoft Custom kulcsszó-létrehozási szolgáltatásával. A kulcsszó megadása után a szolgáltatás létrehoz egy fájlt, amelyet központilag telepít a fejlesztői csomagba, hogy engedélyezze a kulcsszót az eszközön.

1. Lépjen a [Speech studióba](https://aka.ms/sdsdk-speechportal) , és **Jelentkezzen be** , vagy ha még nem rendelkezik beszédfelismerési előfizetéssel, válassza az [**előfizetés létrehozása**](https://go.microsoft.com/fwlink/?linkid=2086754)lehetőséget.

1. Az [egyéni kulcsszó](https://aka.ms/sdsdk-wakewordportal) lapon írja be a kívánt kulcsszót, majd kattintson a **kulcsszó hozzáadása**lehetőségre. Van néhány [irányelvünk](#choose-an-effective-keyword) , amely segítséget nyújt egy hatékony kulcsszó kiválasztásához. A támogatás jelenleg az en-US nyelvre korlátozódik.

    ![Adja meg a kulcsszót](media/speech-devices-sdk/custom-kws-portal-enter-keyword.png)

1. A portál mostantól jelölt kiejtéseket hoz létre a kulcsszava számára. Hallgassa meg az egyes jelölteket a lejátszás gombokra kattintva, és távolítsa el az összes helytelen kiejtés melletti ellenőrzéseket. Ha csak a jó kiejtéseket jelölte be, válassza a **Submit (Küldés** ) lehetőséget a kulcsszó létrehozásának megkezdéséhez. Ha módosítani szeretné a kulcsszót, először távolítsa el a meglévőt úgy, hogy a sor jobb oldalán megjelenő Törlés gombra kattint, miközben fölé viszi.

    ![A kulcsszó áttekintése](media/speech-devices-sdk/custom-kws-portal-review-keyword.png)

1. A modell létrehozása akár egy percet is igénybe vehet. Ekkor a rendszer kérni fogja a fájl letöltését.

    ![A kulcsszó letöltése](media/speech-devices-sdk/custom-kws-portal-download-keyword.png)

1. Mentse a. zip fájlt a számítógépére. Erre a fájlra lesz szüksége, hogy az egyéni kulcsszót a fejlesztői csomagba telepítse.

## <a name="next-steps"></a>További lépések

Tesztelje egyéni kulcsszavait a [Speech Devices SDK](https://aka.ms/sdsdk-quickstart)gyors útmutatójában.
