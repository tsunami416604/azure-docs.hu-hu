---
title: Egyéni hang – beszédszolgáltatás létrehozása
titleSuffix: Azure Cognitive Services
description: Ha készen áll az adatok feltöltésére, nyissa meg az Egyéni hang portált. Egyéni hangprojekt létrehozása vagy kijelölése. A projektnek meg kell osztania a megfelelő nyelvet/területi beállításokat és a nemi tulajdonságokat, mint a hangoktatáshoz használni kívánt adatokat.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: bbe1d651a7d2d2cac1b1aa78b815b2797ad185c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717332"
---
# <a name="create-a-custom-voice"></a>Egyéni hang létrehozása

Az [Adatok előkészítése egyéni hangra](how-to-custom-voice-prepare-data.md)című részben ismertetjük az egyéni hang betanításához használható különböző adattípusokat és a különböző formátumkövetelményeket. Miután előkészítette az adatokat, elkezdheti feltölteni őket az [Egyéni hang portálra,](https://aka.ms/custom-voice-portal)vagy az Egyéni hang betanítási API-n keresztül. Itt ismertetjük az egyéni hang betanításának lépéseit a portálon keresztül.

> [!NOTE]
> Ez a lap feltételezi, hogy elolvasta [az Első lépések az Egyéni hanggal](how-to-custom-voice.md) és az Adatok előkészítése az egyéni hanghoz című [olvasnivalót,](how-to-custom-voice-prepare-data.md)és létrehozott egy Egyéni hangprojektet.

Ellenőrizze az egyéni hanghoz támogatott nyelvek: [nyelv testreszabásához](language-support.md#customization).

## <a name="upload-your-datasets"></a>Adatkészletek feltöltése

Ha készen áll az adatok feltöltésére, nyissa meg az [Egyéni hang portált.](https://aka.ms/custom-voice-portal) Egyéni hangprojekt létrehozása vagy kijelölése. A projektnek meg kell osztania a megfelelő nyelvet/területi beállításokat és a nemi tulajdonságokat, mint a hangoktatáshoz használni kívánt adatokat. Megadhatja például, hogy `en-GB` a hangfelvételek angol nyelvűek-e brit akcentussal.

Nyissa meg az **Adatok** lapot, és kattintson **az Adatok feltöltése gombra.** A varázslóban válassza ki a megfelelő adattípust, amely megfelel az elkészített adattípusnak.

Minden feltöltött adatkészletnek meg kell felelnie a kiválasztott adattípus követelményeinek. Fontos, hogy a feltöltés előtt megfelelően formázza az adatokat. Ez biztosítja, hogy az adatokat az egyéni hangszolgáltatás pontosan feldolgozza. Nyissa meg [az Adatok előkészítése az egyéni hangra lehetőséget,](how-to-custom-voice-prepare-data.md) és győződjön meg arról, hogy az adatok megfelelően formázva vannak.

> [!NOTE]
> Az ingyenes előfizetés (F0) felhasználók egyszerre két adatkészletet tölthetnek fel. A standard előfizetéses (S0) felhasználók egyszerre öt adatkészletet tölthetnek fel. Ha eléri a korlátot, várjon, amíg legalább az egyik adatkészlet importálása befejeződik. Akkor próbálja újra.

> [!NOTE]
> Előfizetésenként legfeljebb 10 .zip fájl importálható ingyenes előfizetéses (F0) és 500 normál előfizetéses (S0) felhasználó számára.

Az adatkészletek automatikusan érvényesíthetők, amint megnyomta a feltöltés gombot. Az adatérvényesítés magában foglalja a hangfájlok fájlformátumának, méretének és mintavételi arányának ellenőrzésére szolgáló ellenőrzések sorozatát. Javítsa ki a hibákat, ha van ilyen, és küldje el újra. Az adatimportálási kérelem sikeres kezdeményezése után az adattáblában egy olyan bejegyzésnek kell lennie, amely megfelel az imént feltöltött adatkészletnek.

Az alábbi táblázat az importált adatkészletek feldolgozási állapotait mutatja be:

| Állapot | Jelentés |
| ----- | ------- |
| Feldolgozás | Az adatkészlet megérkezett és feldolgozás alatt áll. |
| Sikeres | Az adatkészlet érvényesítve lett, és most már hangmodell létrehozásához használható. |
| Sikertelen | Az adatkészlet feldolgozás a feldolgozás során számos okból , például fájlhibák, adatproblémák vagy hálózati problémák miatt sikertelen. |

Az ellenőrzés befejezése után láthatja az egyes adatkészletek egyező utterances összesen az **utterances** oszlopban. Ha a kiválasztott adattípus hosszú hangszegmentálást igényel, ez az oszlop csak az átiratok alapján vagy a beszédátírási szolgáltatáson keresztül szegmentált kimondott szövegeket tükrözi. Az ellenőrzött adatkészletet további letöltéssel tekintheti meg a sikeresen importált kimondott szöveg és a leképezési átiratok részletes eredményeinek megtekintéséhez. Tipp: a hosszú hangszegmentálás több mint egy órát is igénybe vehet az adatfeldolgozás befejezéséhez.

Az en-US és a zh-CN adatkészletek esetében további jelentést tölthet le a kiejtési pontszámok és az egyes felvételek zajszintjének ellenőrzéséhez. A kiejtési pontszám 0 és 100 között mozog. A 70 alatti pontszám általában beszédhibát vagy parancsfájleltérést jelez. A nehéz akcentus csökkentheti a kiejtési pontszámot, és hatással lehet a generált digitális hangra.

A nagyobb jel-zaj arány (SNR) alacsonyabb zajt jelez a hangban. Jellemzően elérheti a 50 + SNR felvétel a professzionális stúdiókban. A 20-nál kisebb zajszinttel rendelkező hang nyilvánvaló zajt okozhat a generált hangban.

Fontolja meg az alacsony kiejtési pontszámmal vagy gyenge jel-zaj aránnyal rendelkező kimondott szövegek újbóli rögzítését. Ha nem tudja újra rögzíteni, kizárhatja ezeket a kimondott szövegeket az adatkészletből.

## <a name="build-your-custom-voice-model"></a>Egyéni hangmodell létrehozása

Az adatkészlet érvényesítése után az egyéni hangmodell létrehozásához használhatja.

1.  Nyissa meg a **szövegfelolvasást > egyéni hangalapú > oktatást.**

2.  Kattintson **a Vonat modell gombra.**

3.  Ezután adjon meg egy **nevet** és **egy leírást** a modell azonosításához.

    Gondosan válassz egy nevet. Az itt megadott név lesz az a név, amelyet az SSML-bemenet részeként a beszédszintetizáló kérelemben szereplő hang megadására használ. Csak betűk, számok és néhány írásjelkarakter (például -, \_, , és (', ') engedélyezett. Különböző neveket használjon a különböző hangmodellekhez.

    **A Leírás** mező gyakori használata a modell létrehozásához használt adatkészletek nevének rögzítésére.

4.  A **Betanítási adatok kiválasztása** lapon válasszon ki egy vagy több adatkészletet, amelyet betanításhoz szeretne használni. Ellenőrizze a kimondott szövegek számát, mielőtt elküldené őket. Kezdheti tetszőleges számú kimondott szöveget en-US és zh-CN hangmodellek. Más területi beállítások esetén több mint 2000 utterances kell kiválasztania egy hangot.

    > [!NOTE]
    > A duplikált hangnevek törlődnek a képzésből. Győződjön meg arról, hogy a kijelölt adatkészletek nem ugyanazokat a hangneveket tartalmazzák több .zip fájlban.

    > [!TIP]
    > A minőségi eredményekhez ugyanattól a hangszóróból származó adatkészletek használata szükséges. Ha a betanításra benyújtott adatkészletek összesen kevesebb, mint 6000 különböző kimondott szöveget tartalmaznak, a hangmodellbe a statisztikai parametrikus szintézis technikán keresztül fogja betanításra. Abban az esetben, ha a betanítási adatok meghaladják a 6000 különböző kimondott szöveget, akkor elindul egy betanítási folyamat a összefűzés szintézise technikával. Normális esetben az összefűzési technológia természetesebb és nagyobb hűségű hangeredményeket eredményezhet. [Lépjen kapcsolatba a Custom Voice csapat,](https://go.microsoft.com/fwlink/?linkid=2108737) ha azt szeretné, hogy a vonat egy modell a legújabb Neurális TTS technológia, amely képes a digitális hang egyenértékű a nyilvánosan elérhető [neurális hangok](language-support.md#neural-voices).

5.  Kattintson **a Vonat** gombra a hangmodell létrehozásának megkezdéséhez.

A Betanítás tábla egy új bejegyzést jelenít meg, amely megfelel ennek az újonnan létrehozott modellnek. A tábla a következő állapotot is megjeleníti: Feldolgozás, Sikeres, Sikertelen.

A megjelenített állapot az adatkészlet hangmodellé alakításának folyamatát tükrözi, ahogy az itt látható.

| Állapot | Jelentés |
| ----- | ------- |
| Feldolgozás | A hangmodell létrehozása folyamatban van. |
| Sikeres | A hangmodell létrejött, és telepíthető. |
| Sikertelen | A hangmodell betanítása során számos okból, például láthatatlan adatproblémák vagy hálózati problémák miatt sikertelen ek. |

A betanítási idő a feldolgozott hangadatok mennyiségétől függ. A tipikus idő körülbelül 30 perc alatt több száz utterances a 40 óra 20 000 utterances. Miután a modell betanítási sikeres, elkezdheti tesztelni.

> [!NOTE]
> Ingyenes előfizetés (F0) a felhasználók a vonat egy hang font egyszerre. A normál előfizetéses (S0) felhasználók egyszerre három hangot is betaníthatnak. Ha eléri a korlátot, várjon, amíg legalább az egyik hangbetűtípus befejezi a betanítást, majd próbálkozzon újra.

> [!NOTE]
> Előfizetésenként legfeljebb 10 modell lehet betanításra, 10 modell ingyenes előfizetéses (F0) és 100 normál előfizetéses (S0) felhasználó számára.

Ha a neurális hangbetanítási képességet használja, kiválaszthatja a valós idejű streamelési forgatókönyvekhez optimalizált modell vagy az aszinkron [hosszú hangszintézisre](long-audio-api.md)optimalizált HD neurális modell betanítását.  

## <a name="test-your-voice-model"></a>A hangmodell tesztelése

A hangbetűtípus sikeres létrehozása után tesztelheti, mielőtt üzembe helyezne.

1.  Nyissa meg a **szövegfelolvasást > egyéni hang> tesztelése .**

2.  Kattintson **a Teszt hozzáadása gombra.**

3.  Válassza ki a tesztelni kívánt egy vagy több modellt.

4.  Adja meg azt a szöveget, amelyet a hang(ok) beszélni szeretne. Ha egyszerre több modell tesztelését választotta, akkor ugyanazt a szöveget fogja használni a különböző modellek teszteléséhez.

    > [!NOTE]
    > A szöveg nyelvének meg kell egyeznie a hangbetűtípus nyelvével. Csak sikeresen betanított modellek tesztelhetők. Ebben a lépésben csak az egyszerű szöveg támogatott.

5.  Kattintson **a Létrehozás gombra.**

Miután elküldte a tesztkérelmet, visszatér a tesztoldalra. A tábla most már tartalmaz egy bejegyzést, amely megfelel az új kérelemnek és az állapotoszlopnak. A beszéd szintetizálása eltarthat néhány percig. Ha az **állapotoszlopban**a Sikeres , lejátszhatja a hangot, vagy letöltheti a szövegbevitelt (egy .txt fájl) és a hangkimenetet (egy .wav fájlt), és további meghallgatást az utóbbihoz a minőség érdekében.

A teszteredményeket a tesztelésre kiválasztott modellek részletes oldalán is megtalálhatja. Lépjen a **Betanítás** lapra, és kattintson a modell nevére a modell részletes lapjának megadásához.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Egyéni hangvégpont létrehozása és használata

Miután sikeresen létrehozta és tesztelte a hangmodellt, egy egyéni text-to-speech végpontban telepíti azt. Ezután használja ezt a végpontot a szokásos végpont helyett, amikor a REST API-n keresztül szöveg-beszéd kéréseket hajt elő. Az egyéni végpontot csak a betűtípus üzembe helyezéséhez használt előfizetés hívhatja meg.

Új egyéni hangvégpont létrehozásához nyissa meg **a Szövegfelolvasás > az egyéni hang> központi telepítés című.** Válassza **a Végpont hozzáadása lehetőséget,** és adjon meg egy **nevet** és **leírást** az egyéni végponthoz. Ezután válassza ki a végponthoz társítani kívánt egyéni hangmodellt.

Miután rákattintott a **Hozzáadás** gombra, a végponttáblában megjelenik egy bejegyzés az új végponthoz. Az új végpont létrehozása eltarthat néhány percig. Ha a központi telepítés **állapota sikeres,** a végpont készen áll a használatra.

> [!NOTE]
> Az ingyenes előfizetés (F0) felhasználók csak egy modell üzembe helyezhető. Standard előfizetés (S0) felhasználók hozhat nak létre legfeljebb 50 végpontok, mindegyik saját egyéni hanggal.

> [!NOTE]
> Az egyéni hang használatához meg kell adnia a hangmodell nevét, az egyéni URI-t közvetlenül egy HTTP-kérelemben kell használnia, és ugyanazt az előfizetést kell használnia a TTS-szolgáltatás hitelesítésének áthaladásához.

A végpont üzembe helyezése után a végpont neve hivatkozásként jelenik meg. Kattintson a hivatkozásra a végpontra jellemző információk megjelenítéséhez, például a végpontkulcs, a végpont URL-címe és a mintakód.

A végpont online tesztelése az egyéni hangportálon keresztül is elérhető. A végpont teszteléséhez válassza a Végpont **ellenőrzése** lehetőséget a **Végpont részletei** lapon. Megjelenik a végponttesztelési lap. Írja be a kimondandó szöveget (egyszerű szövegvagy [SSML formátumban](speech-synthesis-markup.md) a szövegmezőbe. Az egyéni hangbetűtípussal beszélt szöveg et a Lejátszás gombra, válassza a **Lejátszás**lehetőséget. Ez a tesztelési funkció az egyéni beszédszintézis-használat tal szemben kerül felszámolásra.

Az egyéni végpont funkcionálisan megegyezik a szöveg-beszéd kérésekhez használt szabványos végponttal. További információt a [REST API-ban](rest-text-to-speech.md) talál.

## <a name="next-steps"></a>További lépések

* [Útmutató: Hangminták rögzítése](record-custom-voice-samples.md)
* [Text-to-Speech API-referencia](rest-text-to-speech.md)
* [Hosszú audio API](long-audio-api.md)
