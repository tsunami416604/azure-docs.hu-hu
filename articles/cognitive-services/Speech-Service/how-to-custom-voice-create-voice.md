---
title: Egyéni hang-beszéd szolgáltatás létrehozása
titleSuffix: Azure Cognitive Services
description: Ha készen áll az adatok feltöltésére, nyissa meg az egyéni hangportált. Hozzon létre vagy válasszon ki egy egyéni hangprojektet. A projektnek meg kell osztania a megfelelő nyelvi/területi beállításokat és a nemek tulajdonságait a hangképzéshez használni kívánt adatként.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 3747033fcaf65e0c6da07e9f1bb625771958bb4f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319061"
---
# <a name="create-a-custom-voice"></a>Egyéni hang létrehozása

Az [Egyéni hangra való felkészülés](how-to-custom-voice-prepare-data.md)során a különböző adattípusokat ismertetjük, amelyek segítségével betaníthatja az egyéni hangokat és a különböző formátumokra vonatkozó követelményeket. Az adatok előkészítése után megkezdheti a feltöltést az [Egyéni hangportálra](https://aka.ms/custom-voice-portal), vagy az egyéni hangképzési API-n keresztül. Itt ismertetjük az egyéni hangoknak a portálon keresztül történő betanításának lépéseit.

> [!NOTE]
> Ez az oldal azt feltételezi, hogy elolvasta az első [lépéseket az egyéni](how-to-custom-voice.md) hangon, és [előkészíti az egyéni](how-to-custom-voice-prepare-data.md)hangfelvételt, és létrehozott egy egyéni hangprojektet.

Győződjön meg arról, hogy milyen nyelveken támogatott az egyéni hang: a [Testreszabás nyelve](language-support.md#customization).

## <a name="upload-your-datasets"></a>Az adatkészletek feltöltése

Ha készen áll az adatok feltöltésére, nyissa meg az [Egyéni hangportált](https://aka.ms/custom-voice-portal). Hozzon létre vagy válasszon ki egy egyéni hangprojektet. A projektnek meg kell osztania a megfelelő nyelvi/területi beállításokat és a nemek tulajdonságait a hangképzéshez használni kívánt adatként. Válassza ki például, hogy `en-GB` az angol nyelvű hangfelvételek angol nyelvűek-e, és az Egyesült Királyság akcentusa.

Lépjen az **adatok** lapra, és kattintson az **adatok feltöltése**elemre. A varázslóban válassza ki a megfelelő adattípust, amely megfelel az előkészített előírásoknak.

A feltöltött adatkészleteknek meg kell felelniük a választott adattípusra vonatkozó követelményeknek. Fontos, hogy a feltöltés előtt megfelelően formázza az adatait. Ez biztosítja, hogy az egyéni hangszolgáltatás pontosan dolgozza fel az adatfeldolgozást. Nyissa meg az [Adatelőkészítés egyéni hangra](how-to-custom-voice-prepare-data.md) lehetőséget, és győződjön meg róla, hogy az adatai helyesen vannak formázva.

> [!NOTE]
> Az ingyenes előfizetés (F0) felhasználói egyszerre két adatkészletet tölthetnek fel. A standard előfizetés (S0) felhasználók öt adatkészletet tölthetnek fel egyszerre. Ha eléri a korlátot, várjon, amíg az adatkészletek közül legalább az egyik be nem fejeződik az importálás. Ezután próbálkozzon újra.

> [!NOTE]
> Az előfizetések által importálandó adatkészletek maximális száma 10. zip-fájl az ingyenes előfizetés (F0) felhasználóinak és a 500 a standard előfizetés (S0) felhasználói számára.

Az adatkészletek automatikusan érvényesítve lesznek a feltöltés gomb megnyomásakor. Az adatellenőrzés a hangfájlok ellenőrzési sorozatát tartalmazza, hogy ellenőrizze a fájlformátumot, a méretet és a mintavételezési sebességet. Javítsa ki a hibákat, és küldje el újra. Az Adatimportálási kérelem sikeres kezdeményezése után egy olyan bejegyzést kell látnia az adattáblában, amely megfelel az imént feltöltött adatkészletnek.

Az alábbi táblázat az importált adatkészletek feldolgozási állapotait mutatja be:

| Állam | Értelmezés |
| ----- | ------- |
| Feldolgozás | Az adatkészlet beérkezett, és feldolgozás alatt áll. |
| Sikeres | Az adatkészlet érvényesítve lett, és most már használható hangmodell létrehozásához. |
| Sikertelen | Az adatkészletet számos ok miatt meghiúsult a feldolgozás során, például a hibák, az adatproblémák vagy a hálózati problémák miatt. |

Az ellenőrzés befejezését követően a **hosszúságú kimondott szöveg** oszlopban megtekintheti az egyes adatkészletek egyező hosszúságú kimondott szöveg teljes számát. Ha a kiválasztott adattípus hosszú hangú szegmentálást igényel, ez az oszlop csak az Ön által az átiratok alapján vagy a Speech transzkripció Service-ben szegmentált hosszúságú kimondott szöveg tükrözi. A sikeresen importált hosszúságú kimondott szöveg és a hozzájuk tartozó leképezési átiratok részletes eredményeinek megtekintéséhez továbbra is letöltheti az érvényesített adatkészletet. Tipp: a hosszú hangú szegmentálás több mint egy órát is igénybe vehet az adatok feldolgozásának befejezéséhez.

Az en-US és a zh-CN adatkészletek esetében tovább letöltheti a jelentést, és megtekintheti a kiejtési pontszámokat és az egyes felvételek zajszintjét. A kiejtés pontszáma 0 és 100 között lehet. A 70-es pontszám általában egy beszédfelismerési hibát vagy parancsfájl-eltérést jelez. A nagy hangsúlysal csökkentheti a kiejtési pontszámot, és hatással lehet a generált digitális hangra.

Egy magasabb, a hang-zaj arány (SNR) azt jelzi, hogy a hang alacsonyabb zajt mutat. A Professional Studióban való felvételsel általában 50 + SNR is elérhet. A 20 alatti SNR rendelkező hang nyilvánvaló zajt eredményezhet a generált hangon.

Gondolja át újra a hosszúságú kimondott szöveg az alacsony kiejtési pontszámokkal vagy a gyenge jelek és zaj arányával. Ha nem tudja újra felvenni a rekordot, kizárhatja ezeket a hosszúságú kimondott szöveg az adatkészletből.

## <a name="build-your-custom-voice-model"></a>Egyéni hangmodell létrehozása

Az adatkészlet ellenőrzése után felhasználhatja az egyéni hangmodell összeállításához.

1.  Navigáljon a **szöveg-beszéd > egyéni hang > [a projekt neve] > képzés**elemre.

2.  Kattintson a **tanítási modell**elemre.

3.  Ezután adjon meg egy **nevet** és egy **leírást** , amely segítséget nyújt a modell azonosításához.

    Gondosan válassza ki a nevet. Az itt megadott név lesz az a név, amelyet a SSML-bevitel részeként a beszédfelismerési kérelemben szereplő hang megadására fog használni. Csak betűket, számokat és néhány írásjelet (például:-, \_ , és (",") tartalmazhat. Használjon különböző neveket a különböző hangmodellekhez.

    A **Leírás** mező gyakori használata a modell létrehozásához használt adatkészletek nevének rögzítése.

4.  A **betanítási adatok kiválasztása** lapon válasszon ki egy vagy több olyan adatkészletet, amelyet a képzéshez használni szeretne. Mielőtt elküldi a hosszúságú kimondott szöveg számát, tekintse át a következőt:. Az en-US és a zh-CN hangmodellek tetszőleges számú hosszúságú kimondott szöveg elkezdheti. Más területi beállítások esetén több mint 2 000 hosszúságú kimondott szöveg kell kijelölnie, hogy egy hang betanítható legyen.

    > [!NOTE]
    > Az ismétlődő hangnevek el lesznek távolítva a betanításból. Győződjön meg arról, hogy a kiválasztott adatkészletek nem tartalmazzák ugyanazokat a hangneveket több. zip-fájlban.

    > [!TIP]
    > Az ugyanahhoz a beszélőhöz tartozó adatkészletek használata szükséges a minőségi eredményekhez. Ha a betanításhoz beküldött adatkészletek teljes száma kevesebb, mint 6 000 különböző hosszúságú kimondott szöveg, akkor a hangmodellt a statisztikai számszerű szintézis technikán keresztül fogja betanítani. Abban az esetben, ha a betanítási adat meghaladja a 6 000 különböző hosszúságú kimondott szöveg, a betanítási folyamat elindítható az összefűzési szintézis technikával. Az összefűzési technológia általában természetesebb és magasabb szintű hangfelismerési eredményeket eredményezhet. [Vegye fel a kapcsolatot az egyéni hangcsapattal](https://go.microsoft.com/fwlink/?linkid=2108737) , ha olyan modellt szeretne betanítani a legújabb neurális TTS-technológiával, amely képes a nyilvánosan elérhető [neurális hangokkal](language-support.md#neural-voices)egyenértékű digitális hang létrehozására.

5.  A hangmodell létrehozásának megkezdéséhez kattintson a **tanítás** elemre.

A betanítási táblázat egy új bejegyzést jelenít meg, amely megfelel az újonnan létrehozott modellnek. A tábla a következő állapotot is megjeleníti: feldolgozás, sikeres, sikertelen.

A megjelenített állapot az adatkészlet hangmodellre alakításának folyamatát tükrözi, ahogy az itt látható.

| Állam | Értelmezés |
| ----- | ------- |
| Feldolgozás | A hangmodell létrehozása folyamatban van. |
| Sikeres | A hangmodell létrehozása megtörtént, és üzembe helyezhető. |
| Sikertelen | A hangmodell sok ok miatt sikertelen volt a képzésben, például a láthatatlan adatproblémákkal vagy a hálózati problémákkal kapcsolatban. |

A betanítási idő a feldolgozott hangadatok mennyiségétől függően változhat. Az átlagos időintervallum körülbelül 30 percet vesz igénybe, több száz hosszúságú kimondott szöveg 40 óráig 20 000 hosszúságú kimondott szöveg. Ha a modell betanítása sikeres volt, elkezdheti a tesztelést.

> [!NOTE]
> Az ingyenes előfizetés (F0) felhasználói egyszerre egy hangbetűtípust tudnak betanítani. A standard előfizetés (S0) felhasználói egyszerre három hangokat is betanítanak. Ha eléri a korlátot, várjon, amíg a hangbetűkészletek közül legalább az egyik befejeződik, majd próbálkozzon újra.

> [!NOTE]
> Az előfizetéshez engedélyezett hangmodellek maximális száma 10 modell ingyenes előfizetés (F0) felhasználóhoz és 100 a standard előfizetés (S0) felhasználói számára.

Ha a neurális hangképzési funkciót használja, kiválaszthatja a valós idejű adatfolyam-forgatókönyvekre optimalizált modellek betanítását, vagy egy olyan HD neurális modellt, amely aszinkron, [hosszú hangszintézisre](long-audio-api.md)van optimalizálva.  

## <a name="test-your-voice-model"></a>A hangmodell tesztelése

A hangalapú betűkészlet sikeres létrehozása után tesztelheti a használatát a használathoz való üzembe helyezése előtt.

1.  Navigáljon a **szöveg-beszéd > egyéni hang > [a projekt neve] > a tesztelés**elemre.

2.  Kattintson a **teszt hozzáadása**gombra.

3.  Válasszon ki egy vagy több modellt, amelyet szeretne tesztelni.

4.  Adja meg azt a szöveget, amelyet a hang (ok) hoz beszélni kíván. Ha egyszerre több modell tesztelését választotta, akkor a rendszer ugyanazt a szöveget fogja használni a különböző modellek teszteléséhez.

    > [!NOTE]
    > A szöveg nyelvének meg kell egyeznie a hang betűkészletének nyelvével. Csak a sikeresen betanított modellek tesztelése lehetséges. Ebben a lépésben csak egyszerű szöveg támogatott.

5.  Kattintson a **Létrehozás** lehetőségre.

A tesztelési kérelem elküldése után vissza fog térni a teszt lapra. A tábla most már tartalmaz egy bejegyzést, amely megfelel az új kérésnek és az állapot oszlopnak. Néhány percet is igénybe vehet a beszédfelismerés. Ha az Állapot oszlopban a **sikeres**művelet látható, lejátszhatja a hangot, vagy letöltheti a szövegbeviteli szöveget (egy. txt fájlt) és hangkimenetet (egy. wav fájlt), és az utóbbit a minőségre is meghallgathatja.

A teszt eredményeit a teszteléshez kiválasztott egyes modellek részletek lapján is megtalálhatja. Lépjen a **képzés** lapra, és kattintson a modell nevére a modell részletei lap megadásához.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Egyéni hang-végpont létrehozása és használata

Miután sikeresen létrehozta és tesztelte a hangmodellt, egy egyéni szöveg-beszéd végponton helyezheti üzembe. Ezután ezt a végpontot használja a szokásos végpont helyett, amikor szöveg-beszéd kéréseket végez a REST APIon keresztül. Az egyéni végpontot csak a betűkészlet üzembe helyezéséhez használt előfizetés hívhatja meg.

Új egyéni hangvégpont létrehozásához lépjen a **szöveg-beszéd > egyéni hang > üzembe helyezés**elemre. Válassza a **végpont hozzáadása** lehetőséget, és adja meg az egyéni végpont **nevét** és **leírását** . Ezután válassza ki azt az egyéni hangmodellt, amelyet hozzá szeretne rendelni ehhez a végponthoz.

Miután rákattintott a **Hozzáadás** gombra, a végpont táblában megjelenik egy bejegyzés az új végponthoz. Egy új végpont létrehozását eltarthat néhány percig. Ha a központi telepítés állapota **sikeres**, a végpont készen áll a használatra.

> [!NOTE]
> Az ingyenes előfizetés (F0) felhasználóinak csak egy modelljét lehet központilag telepíteni. A standard előfizetés (S0) felhasználók legfeljebb 50 végpontot hozhatnak létre, amelyek mindegyike saját egyéni hanggal rendelkezik.

> [!NOTE]
> Az egyéni hang használatához meg kell adnia a hangmodell nevét, az egyéni URI-t közvetlenül egy HTTP-kérelemben kell használnia, és ugyanazt az előfizetést kell használnia a TTS szolgáltatás hitelesítésének továbbítására.

A végpont üzembe helyezése után a végpont neve hivatkozásként jelenik meg. Kattintson a hivatkozásra a végpontra jellemző információk megjelenítéséhez, például a végponti kulcs, a végpont URL-címe és a mintakód számára.

A végpont online tesztelése az egyéni hangportálon keresztül is elérhető. A végpont teszteléséhez válassza a végpont **ellenőrzése** lehetőséget a **végpont részletei** lapon. Megjelenik az Endpoint Testing (végpont tesztelése) oldal. Adja meg a megjelenítendő szöveget (egyszerű szöveges vagy [SSML formátumban](speech-synthesis-markup.md) a szövegmezőben. Ha szeretné meghallgatni az egyéni hangbetűtípusban elhangzó szöveget, válassza a **Lejátszás**lehetőséget. Ez a tesztelési funkció az egyéni beszédfelismerési használatért lesz felszámítva.

Az egyéni végpont funkcionálisan megegyezik a szöveg-beszéd kérelmekhez használt standard végponttal. További információ: [REST API](rest-text-to-speech.md) .

## <a name="next-steps"></a>Következő lépések

* [Útmutató: hangminták rögzítése](record-custom-voice-samples.md)
* [Text-to-Speech API-referencia](rest-text-to-speech.md)
* [Hosszú hang API](long-audio-api.md)
