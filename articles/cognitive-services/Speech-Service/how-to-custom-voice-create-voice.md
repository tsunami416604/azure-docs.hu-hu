---
title: Hozzon létre egy egyéni beszédfelismerési – beszédszolgáltatások
titlesuffix: Azure Cognitive Services
description: Ha készen áll az adatok feltöltéséhez, nyissa meg az egyéni beszédfelismerési portálon. Hozzon létre vagy válasszon ki egy egyéni beszédfelismerési projektet. A projekthez meg kell osztania, a megfelelő nyelvi és a területi és a nemek tulajdonságokat, az adatok a leképezés a hangalapú tanítási használatára.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 0c2889cef01345f00d1669e7549a2fe25ac07b97
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603113"
---
# <a name="create-a-custom-voice"></a>Hozzon létre egy egyéni beszédfelismerési

A [előkészíti az adatokat az egyéni beszédfelismerési](how-to-custom-voice-prepare-data.md), hogy leírtak segítségével egyéni beszédfelismerési és a különböző formátumkövetelményeknek különböző adattípusok. Miután előkészítette az adatokat, elkezdheti a feltöltik őket a [egyéni beszédfelismerési portál](https://aka.ms/custom-voice-portal), vagy az egyéni beszédfelismerési képzési API-n keresztül. Itt a lépéseket a portálon keresztül egy egyéni beszédfelismerési képzési ismertetünk.

> [!NOTE]
> Ezen a lapon azt feltételezi, hogy elolvasta [Ismerkedés az egyéni beszédfelismerési](how-to-custom-voice.md) és [előkészíti az adatokat az egyéni beszédfelismerési](how-to-custom-voice-prepare-data.md), és létrehozott egy egyéni beszédfelismerési projektet.

Ellenőrizze az egyéni beszédfelismerési támogatott nyelvek: [nyelvi testreszabás](language-support.md#customization).

## <a name="upload-your-datasets"></a>Az adathalmazok feltöltése

Ha készen áll az adatok feltöltéséhez, nyissa meg a [egyéni beszédfelismerési portál](https://aka.ms/custom-voice-portal). Hozzon létre vagy válasszon ki egy egyéni beszédfelismerési projektet. A projekthez meg kell osztania, a megfelelő nyelvi és a területi és a nemek tulajdonságokat, az adatok a leképezés a hangalapú tanítási használatára. Válassza ki például `en-GB` Ha rendelkezik hangfelvételeket használatával egy UK hangsúlyos angol nyelven történik.

Nyissa meg a **adatok** fülre, és **adatfeltöltés**. A varázslóban válassza ki a megfelelő adattípust, amely megfelel a mi, készítse elő.

Minden egyes feltöltött adatkészlet meg kell felelnie az Ön által választott adattípus követelményeinek. Fontos megfelelően formázza az adatokat, mielőtt fel lesz töltve. Ez biztosítja, hogy a rendszer pontosan feldolgoz az egyéni beszédfelismerési szolgáltatás által. Lépjen a [előkészíti az adatokat az egyéni beszédfelismerési](how-to-custom-voice-prepare-data.md) , és győződjön meg arról, hogy az adatok helyesen formázott.

> [!NOTE]
> Ingyenes előfizetés (F0) felhasználói is feltölthet egyszerre két adatkészletet. Standard szintű előfizetés (S0) felhasználói is feltölthet egyszerre öt adatkészletek. Ha eléri a korlátot, várjon, amíg legalább egy, az adatkészletek importálásának befejezése. Próbálkozzon újra.

> [!NOTE]
> Az adatkészletek importálhatók előfizetésenként engedélyezett maximális számát (F0) előfizetés felhasználóinak és a standard szintű előfizetés (S0) felhasználói 500 10 .zip fájlokat ingyenes.

Az adatkészletek automatikusan érvényesítése után eléri a Feltöltés gombra. Adatok érvényesítése magában foglalja a sor a hang fájlokat, és ellenőrizze a fájlformátum, a méretet, és a mintavételi ráta ellenőrzést. Javítsa ki a hibákat, ha van ilyen, és küldje el újra. Ha az adatok importálása kérelem sikeresen kezdeményezte, megtekintheti az egy bejegyzés az adatok táblázatban, amely megfelel az imént feltöltött adatkészlet.

Az alábbi táblázat az importált adatkészletek feldolgozása állapotai:

| Állapot | Jelentés |
| ----- | ------- |
| Feldolgozás | Az adatkészlet megkaptuk, és a feldolgozása folyamatban van. |
| Sikeres | Az adatkészlet ellenőrzése megtörtént, és előfordulhat, hogy most már használható hangalapú modell létrehozásához. |
| Meghiúsult | Az adatkészlet nem lett sikerült számos oka lehet, például fájlhibák Konfigurációadatok problémái vagy hálózati problémák miatt a feldolgozás során. |

Érvényesítés befejezése után láthatja az egyes az adatkészleteket az egyező kimondott szöveg teljes száma a **beszédmódok** oszlop. Ha a kiválasztott adattípus hosszú – hang Szegmentálás igényel, az ebben az oszlopban csak azt, vagy alapján az átiratok vagy a speech beszédátírási szolgáltatással rendelkezik szegmentált megcímkézzen tükrözi. Az adatkészlet érvényesítése sikeresen importálva megcímkézzen részletes eredményét és azok leképezés átiratok megtekintése további töltheti le. Tipp: hosszú – hang Szegmentálás adatfeldolgozási több, mint egy órát is eltarthat.

En-US és zh-CN-adatkészletek esetében további írásmódja pontszámokat és a zajszint ellenőrzéssel az egyes felvételeit jelentést is letölthető. A írásmódja pontszám értéke 0 és 100. 70 alatti általában azt jelzi, hogy beszéd hiba vagy a parancsfájl nem egyeznek. Nehéz hangsúlyos csökkenthető a írásmódja pontszám, és hatással van a generált digitális hangot.

Nagyobb jel zaj memóriaarányt (SNR) azt jelzi, hogy alacsonyabb lényeget a hangot a. Felvétel a professzionális studios által általában egy több mint 50 SNR is elérheti. Az alábbi 20-SNR hang nyilvánvaló lényeget a generált hangminta eredményezhet.

Vegye figyelembe, hogy az alacsony írásmódja pontszámok vagy gyenge jel zaj arányok megszólalásokat a felvétel. Ha nem tud újra rögzíteni, ezeket a kimondott szöveg, előfordulhat, hogy zárja ki a az adatkészlet.

## <a name="build-your-custom-voice-model"></a>Az egyéni beszédfelismerési modell létrehozása

Az adatkészlet érvényesítése után használhatja az egyéni beszédfelismerési modellek létrehozását.

1.  Navigáljon a **szöveg-hang transzformációs > egyéni beszédfelismerési > képzési**.

2.  Kattintson a **tanítási modell**.

3.  Írja be a **neve** és **leírás** segít azonosítani az ebben a modellben.

    Gondosan válasszon egy nevet. A név, az Itt adhatja meg a hangalapú beszédszintézishez a kérelemből a bemeneti SSML részeként neve lesz. Csak betűket, számokat és például-, néhány írásjeleket \_, és (',') használata engedélyezett. A különböző beszédfelismerési modellek különböző neveket használ.

    Egyik gyakori felhasználási a **leírás** mező jegyezze fel a modell létrehozásához használt az adatkészletek nevei.

4.  Az a **válassza ki a betanítási adatok** lapon a betanítási használni kívánt egy vagy több adatkészletek. Ellenőrizze a kimondott szöveg száma, mielőtt elküldi őket. Kezdésként használhatja az en-US és zh-CN beszédfelismerési modellek kimondott szöveg tetszőleges számú. Más területi beállításokhoz ki kell választania a több mint 2000 utterances tudják a hang betanításához.

    > [!NOTE]
    > Hang ismétlődő nevek a képzés törlődni fog. Ellenőrizze, hogy a kiválasztott adathalmazok nem tartalmaznak hang ugyanazokat a neveket között több .zip-fájlt.

    > [!TIP]
    > Az azonos speaker adatkészleteihez használatával minőségben és szükség. Amikor képzéshez elküldte az adatkészletek száma kisebb, mint 6000 különböző utterances tartalmaz, a statisztikai paraméteres összefoglaló technika keresztül hangalapú modellje, fog betanításához. Abban az esetben, ha a betanítási adatok meghaladja a 6000 különböző utterances teljes száma hogy az összefűzés összefoglaló technikával betanítási folyamat elindít. Általában az összefűzés technológia több természetes és magasabb színvonalú használatot élvezhet eredményt eredményezhet. [Az egyéni beszédfelismerési csapattól](mailto:speechsupport@microsoft.com) szeretné-e a legújabb Neurális Szövegfelolvasás technológia, amely egyenértékű, a nyilvánosan elérhető a digitális hangot a modell betanításához [Neurális beszédhangot](language-support.md#neural-voices).

5.  Kattintson a **Train** a hangalapú modell létrehozásának megkezdéséhez.

A képzési tábla egy új bejegyzést, amely megfelel az újonnan létrehozott modell jeleníti meg. A tábla állapota is látható: Feldolgozás, a sikeres, sikertelen.

Az állapot látható voice-modellhez, az adatkészlet átalakításának folyamata, tartalmazza az itt látható módon.

| Állapot | Jelentés |
| ----- | ------- |
| Feldolgozás | A szóbeli modell létrehozása folyamatban van. |
| Sikeres | A szóbeli modell létrehozását, és is üzembe helyezhetők. |
| Meghiúsult | A beszédfelismerési modell képzés számos oka lehet, például nem látható adatok problémák vagy hálózati problémák miatt leállt. |

Idő képzési hang feldolgozott adatok mennyiségétől függően változik. Tipikus alkalommal között tájékozódhat utterances több száz 30 perc a 20 000 utterances 40 óra. Miután a modell betanítása sikerült van, elkezdheti ki.

> [!NOTE]
> Ingyenes előfizetés (F0) felhasználói egyszerre egy hangtípusú betaníthatja. Standard szintű előfizetés (S0) felhasználói betaníthatja három beszédhangot egyszerre. Ha eléri a korlátot, várjon, amíg legalább egyike a hangtípust képzési befejeződik, és próbálkozzon újra.

> [!NOTE]
> Beszédfelismerési modellek vélik előfizetésenként engedélyezett maximális száma 10 ingyenes előfizetés (F0) felhasználói modelleket és a standard szintű előfizetés (S0) felhasználói 100.

## <a name="test-your-voice-model"></a>Hangalapú modell tesztelése

Miután a hangtípusú sikeresen létrejött, mielőtt központilag telepítené azt használja tesztelheti.

1.  Navigáljon a **szöveg-hang transzformációs > egyéni beszédfelismerési > tesztelés**.

2.  Kattintson a **Hozzáadás teszt**.

3.  Válassza ki a tesztelni kívánt egy vagy több modellt.

4.  Adja meg a szöveg a voice(s) mérnökeinkkel. Ha kiválasztott egy időben a több modell teszteléséhez, ugyanazt a szöveget különböző modell teszteléséhez használható.

    > [!NOTE]
    > A szöveg nyelvét a hangtípusú nyelvi azonosnak kell lennie. Csak a sikeresen betanított modellek tesztelhető legyen. Ez a lépés csak egyszerű szöveg használata támogatott.

5.  Kattintson a **Create** (Létrehozás) gombra.

A tesztelési kérelem elküldését követően fog megjelenni a tesztelési lapot. A táblázat most már tartalmaz egy bejegyzést, amely megfelel az új kérés és az Állapot oszlopban. Beszéd szintetizálásához néhány percet is igénybe vehet. Ha az Állapot oszlop szerint **sikeres**, a hanganyag, vagy töltse le a szövegbevitel (egy .txt fájl), és hang kimeneti (.wav fájl), valamint további audition az utóbbi a minőségi.

A vizsgálati eredmények az egyes választott modellek Részletek lapján tesztelési is megkeresheti. Nyissa meg a **képzési** lapra, majd kattintson a modell részletek lapon adja meg a modell neve.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Létrehozhat és használhat egy egyéni beszédfelismerési végpont

Miután sikeresen létrehozta és tesztelte a hangalapú modell, üzembe helyezésekor egy egyéni szöveg-hang transzformációs végpontot. Ezt a végpontot a szokásos végpont helyett majd a REST API-n keresztül szöveg-hang transzformációs kérések küldésekor használja. Egyéni végpontra a betűtípus üzembe helyezéséhez használt előfizetés csak a nem hívható meg.

Hozzon létre egy új egyéni beszédfelismerési végpontot, lépjen a **szöveg-hang transzformációs > egyéni beszédfelismerési > üzembe helyezési**. Válassza ki **végpont hozzáadása** , és adja meg egy **neve** és **leírás** a egyéni végponthoz. Ezután válassza ki az egyéni beszédfelismerési modell, amelyet szeretne társítani ehhez a végponthoz.

Miután rákattintott a **Hozzáadás** gombra, a táblázatban, megjelenik egy bejegyzés az új végpont. Eltarthat néhány percig, hozza létre az új végpont. Ha a központi telepítés állapota **sikeres**, a végpont készen áll a használatra.

> [!NOTE]
> Ingyenes előfizetés (F0) felhasználók csak egy modell üzembe helyezett rendelkezhet. Standard szintű előfizetés (S0) felhasználók legfeljebb 50 a saját egyéni beszédfelismerési végpontokat hozhat létre.

> [!NOTE]
> Az egyéni beszédfelismerési használatához, kell adja meg a hangalapú modell neve, az egyéni URI-t használja a HTTP-kérést közvetlenül a, és ugyanahhoz az előfizetéshez használt szöveg-beszéd átalakítás szolgáltatás a hitelesítés.

A végpont telepítése után a végpont neve hivatkozásként jelenik meg. Kattintson a hivatkozásra, adott információk megjelenítéséhez a végpontra, például a végponti kulcs, a végpont URL-címe és a mintakódot.

A végpont tesztelését online érhető el az egyéni beszédfelismerési portálon keresztül. A végpont teszteléséhez válassza **ellenőrizze a végpont** származó a **végpont részletei** lap. A végpont tesztelése oldalon jelenik meg. Adja meg a szöveget, (vagy egyszerű szöveges vagy [SSML formátum](speech-synthesis-markup.md) a szövegmezőben. Hallgassa meg az egyéni hangtípusú beszélt szövegét, jelölje be **lejátszása**. A tesztelési funkciója az egyéni beszédfelismerési összefoglaló használati lesznek terhelve.

Az egyéni végpont funkcionálisan megegyezik a standard szintű végpontot, amely a szöveg-hang transzformációs kéréseket szolgál. Lásd: [REST API-val](rest-text-to-speech.md) további információt.

## <a name="next-steps"></a>További lépések

* [Útmutató: Rögzítése a voice-minták](record-custom-voice-samples.md)
* [Szöveg-hang transzformációs API-referencia](rest-text-to-speech.md)
