---
title: Egy egyéni ébresztési szó létrehozása
description: Egy egyéni ébresztési szó hoz létre a Speech Devices SDK-val.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 615a901c70fff92141442699ea6e4b8fce1c9ace
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282573"
---
# <a name="create-a-custom-wake-word-using-speech-service"></a>Hozzon létre egy egyéni ébresztési szó Speech service használatával

Az eszköz mindig figyeli a hálózati ébresztési szó (vagy kifejezés). Például a "Hey Cortana" a Cortana Segéd az ébresztési szó lesz. Amikor a felhasználó az ébresztési szó szerint, az eszköz elindítja a minden ezt követő hang küld a felhő, mindaddig, amíg a felhasználó leállítja a különféle. Hatékony módja az eszköz megkülönböztetéséhez és erősítse a márkajelzési beállításokat az ébresztési word testreszabása.

Ebből a cikkből megismerheti, hogyan hozhat létre egy egyéni ébresztési word, az eszköz.

## <a name="choosing-an-effective-wake-word"></a>Egy hatékony ébresztési word kiválasztása

Vegye figyelembe az alábbi irányelveket, amikor kiválasztja az ébresztési szót.

* Az ébresztési word egy angol nyelvű szót vagy kifejezést kell lennie. Érdemes igénybe legfeljebb két másodpercen övezi.

* 4 – 7 szótagokat határoznak szavak működnek a legjobban. Például "Hey, számítógép" egy jó ébresztési szót, míg csak a "Hey" gyenge egy.

* Ébresztési szavak közös angol nyelvű írásmódja szabályok kövessék.

* Egy egyedi vagy még feldolgozott szó, a következő általános angol nyelvű írásmódja szabályok által kiváltott hamis pozitív csökkentheti. Például a "computerama" egy jó ébresztési szó lehet.

* Ne adja meg egy gyakori szót. Például "étkezési" és "go" olyan szavak, amelyek az emberek gyakran tegyük fel, a szokásos beszélgetésekben. Azok az eszköz hamis eseményindítók lehet.

* Kerülje a lehet egyéb kiejtés ébresztési szót. Felhasználók a saját eszköz válaszol "megfelelő" kiejtés ismernie kell. Például "509" sikerült kell ejtsd szerint "öt nulla nine", "öt hoppá kilenc", vagy a "öt száz és kilenc." "R.E.I." sikerült kell ejtsd: "e. R I" vagy "Ray." "Live" sikerült ejtsd, [līv] vagy [liv].

* Ne használja a speciális karaktereket, szimbólumok és számjegy. Például "Go #" és "20 + macskák" nem lenne jó ébresztési szavakat. Azonban "Ugrás éles" vagy "húsz macskák plusz" működhetnek. Továbbra is a szimbólumok használata a márkajelzési beállításokat, és marketing- és dokumentáció segítségével a megfelelő kiejtés megerősítése.

> [!NOTE]
> Ha az ébresztési Word Védjegyezett szót, győződjön meg, hogy meg saját, a védjegyekre, vagy pedig a védjegy tulajdonosától engedélye a használatára. A Microsoft nem vállalnak felelősséget semmilyen jogi problémák esetleg felmerülő, Ön választhatja ki a hálózati ébresztési word.

## <a name="creating-your-wake-word"></a>Az ébresztési word létrehozása

Egy egyéni ébresztési szót az eszköz használata előtt létre kell hoznia a Microsoft egyéni ébresztési Word létrehozó szolgáltatások használatával. Miután megadta a ébresztési word-, a szolgáltatás állít elő a fájlnevet, majd üzembe helyezi az alakzatot a fejlesztői csomag ahhoz, hogy az ébresztési word, az eszközön.

1. Nyissa meg a [Custom Speech Service portal](https://cris.ai/).

2. Hozzon létre egy új fiókot, amelyen az Azure Active Directory meghívót kapott e-mail-címmel. 

    ![Új fiók létrehozása](media/speech-devices-sdk/wake-word-1.png)
 
3.  Miután bejelentkezett, töltse ki az űrlapot, majd kattintson a **megkezdheti a folyamatot.**

    ![a sikeres bejelentkezést](media/speech-devices-sdk/wake-word-3.png)
 
4. A **egyéni ébresztési Word** lap nem érhető el nyilvánosan, így nem áll fenn, amely végigvezeti van kapcsolat. Kattintson a, vagy inkább illessze be ezt a hivatkozást a: https://cris.ai/customkws.

    ![rejtett oldalára](media/speech-devices-sdk/wake-word-4.png)
 
6. Írja be a hálózati ébresztési szó, a választott, majd **küldés** azt.

    ![Adja meg az ébresztési word](media/speech-devices-sdk/wake-word-5.png)
 
7. Eltarthat néhány percig, létrejön a fájlokat. Megjelenik egy forgó kör a böngészőlapot. Ezután, egy információs sáv megjelenik rákérdez arra, hogy töltse le a `.zip` fájlt.

    ![fogadó .zip-fájlban](media/speech-devices-sdk/wake-word-6.png)

8. Mentse a `.zip` fájlt a számítógépre. Ezt a fájlt az egyéni ébresztési szó üzembe development Kit kell utasításait követve [a Speech eszközök SDK használatának első lépései](speech-devices-sdk-qsg.md).

9. Most már Ön **jelentkezzen ki.**

## <a name="next-steps"></a>További lépések

Első lépésként kérje le egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/) és iratkozzon fel a Speech Devices SDK-val.

> [!div class="nextstepaction"]
> [Iratkozzon fel a Speech Devices SDK-val](get-speech-devices-sdk.md)

