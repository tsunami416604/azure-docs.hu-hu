---
title: Adja meg egy egyéni nézet – Bing egyéni keresés
titlesuffix: Azure Cognitive Services
description: Ismerteti, hogyan hozhat létre a hely és a függőleges keresési szolgáltatások
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 5e168608202f81f698221b52315de83e9713feab
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282923"
---
# <a name="configure-your-custom-search-experience"></a>Konfigurálja az egyéni keresés

Egy egyéni keresési példány lehetővé teszi a keresési funkciók csak tartalmát, hogy a felhasználók számára is megfelelő kódolási sebesség kiválasztását. Helyett a webes keresés, a Bing keresés csak az Önt érdeklő webes szelet. Az egyéni kívánalmaknak megfelelő webes keresés létrehozásához használja a Bing Custom Search [portált](https://customsearch.ai). További információ a portál bejelentkezés: [az első Bing Custom Search-példány létrehozása](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/quick-start). 

A portálon egy keresési példányát, amely meghatározza a tartományok, mindegyik lapot tünteti, és, amelyek azt szeretné, hogy a Keresés a Bing és az azokkal, amelyek nem szeretné, hogy a keresés létrehozását teszi lehetővé. A tartalom, hogy megismerkedett az URL-címek megadása, mellett is kérhet, javasolja a tartalmakat, amelyeket érdemes a nézethez hozzáadandó a portálon. 

A következő módja az, hogy határozhatja meg a webes szelet: 

1.  A tartomány. Tartomány szelet magában foglalja az internetes tartományban található összes tartalmat. Ha például www.microsoft.com. A tartomány altartományokra is kereshet a Bing kihagyva "www" okoz. Például ha megadja a Microsoft.com webhelyre mutat, a Bing is eredményeket ad vissza a support.microsoft.com vagy technet.microsoft.com.  
  
2.  Melléklap. Melléklap szelet magában foglalja a Melléklap és elérési utak alatta található összes tartalmat. Legfeljebb két mindegyik lapot tünteti előfordulhat, hogy adja meg az elérési utat. Ha például www.microsoft.com/en-us/windows/  
  
3.  Weblap. A weblap szelet belefoglalhatja egyéni keresés csak weblapról. Azt igény szerint megadhatja, hogy mindegyik lapot tünteti tartalmazza.

Minden tartomány, mindegyik lapot tünteti és, amelyek a megadott nyilvános és a Bing által indexelt kell lennie. Ha fel szeretne venni a Keresés nyilvános helyen a saját, és a Bing nem indexelt azt, tekintse meg a Bing [gazdáját dokumentáció](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) részletes információ a Bing indexelés céljából. Emellett a részleteinek beolvasása a bejárt hely frissítéséhez, ha az index nem naprakész a Bing a gazdáját dokumentációjában talál.

## <a name="adding-slices-to-your-custom-search"></a>A szeletek ad hozzá az egyéni keresés

Az egyéni keresőpéldányok határozza meg, ha az aktív és a blokkolt tartományok, mindegyik lapot tünteti és, amelyek szeretne keresni, vagy keres, nem adja meg.  

- Aktív: Listáját tartományok, mindegyik lapot tünteti vagy a keresés foglalandó weblapok.  
  
- Letiltva: Listáját tartomány, mindegyik lapot tünteti vagy kizárása a keresési weblapok. Az aktív listában felsorolt letiltja kell lennie tartalom a tartományokat, és mindegyik lapot tünteti alatt található elemek.

Minden egyes lista eléréséhez kattintson az egyéni keresőpéldányok a aktív és a letiltott lapokon. 

<a name="active-and-blocked-lists"></a>
## <a name="active-and-blocked-lists"></a>Aktív és a letiltott listák 

A webes keresés a Bing kívánt szelet megadásához kattintson a **aktív** lapra, és a tartományok, mindegyik lapot tünteti és keresés weblapok listázása. A szelet közvetlenül hozzáadása a listához, vagy egynél több szelet hozzáadása, ha feltölt egy szöveges fájlt, a Feltöltés ikonra.

Fájl feltöltése részletei: 

- Fájl feltöltése csak a szeleteket ad hozzá az aktív lista elérhető, nem használható szeletek hozzáadása a letiltott listájához.  
  
- Hozzon létre egy szövegfájlt, és adja meg a tartományra, Melléklap vagy külön sorba weblap. A teljes feltöltés nem fogadja el, ha hiba történik.  
  
- Ha a letiltott lista tartalmaz, a tartomány, Melléklap vagy weblap, amelyen a feltöltendő fájl a megadott, a szolgáltatás eltávolítja a letiltott listán, és hozzáadja azt az aktív lista.  
  
- A szolgáltatás figyelmen kívül hagyja a feltöltendő fájl ismétlődő.

Szerkesztése vagy törlése a szeletek használja a beállítások alatt a **vezérlők** oszlop. 

Hasonlóképpen adhat hozzá szeletek letiltott listán (kivéve a szeletek megadásához egy feltöltendő fájl nem használható).

## <a name="pinned-list"></a>Rögzített listája

A portál azt is lehetővé teszi, hogy egy meghatározott weboldalt rögzítsen a keresési eredmények elején, ha a felhasználó egy bizonyos keresési kifejezést ad meg. A **Pinned** lapon lekérdezési kifejezés, és a weblap párok, melyek adja meg a weblap, egy adott lekérdezésre vonatkozó felső eredményként megjelenő listáját tartalmazza. Ezenkívül csak indexelt weblapjait keresések megjelennek. További információ a rögzítés eredmények: [rangsor módosítása](#adjustrank). 

Rögzítés eredmények nem áll rendelkezésre a képkeresési, és a videó keresési megoldásokat.

## <a name="website-suggestions"></a>Webhely-javaslatok

Szeletek az aktív listára felvett esetén a szolgáltatás webhely és Melléklap javaslatokat, amelyeket érdemes hozzáadni a keresés állít elő. A **érdemes hozzáadni** szakasz tartalmazza a vonatkozó javaslatok. A példány beállításai lapon tartalmazza az ebben a szakaszban csak akkor, ha a javaslat érhető el. 

Javaslatok az aktív lista hozzáadásához kattintson a + ikonra.  Mivel a szolgáltatás javaslatait a beállítások alapján hoz létre, mindenképp kattintson **frissítése** a javaslatok hozzáadása után. 

## <a name="preview-pane"></a>Podokno náhledu

A jobb oldalon a betekintő segítségével keresési lekérdezéseknek és az eredmények megtekintése a search-példány ki tesztelheti. 

1. Válassza ki **saját példány**
2. Válassza ki a biztonságos keresési szűrő és a mi piac keresése (lásd: [lekérdezési paraméterek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)).
3. Adjon meg egy lekérdezést, és nyomja le adja meg, vagy kattintson a keresés ikonra az aktuális konfiguráció eredményeinek megtekintéséhez. 

Válassza ki a keresés végrehajtásához, kattintson a **webes** webes lekéréséhez **kép** kép eredményt, vagy **videó** videó eredmények eléréséhez. 

Használja az előnézeti ablaktáblában láthatja, emellett áttekintheti a Bing-eredmények kiválasztásával **Bing** helyett **saját példány**. Ez a keresés eredményeit a Bing által visszaadott eredmények összehasonlításához hasznos lehet.

<a name="adjustrank"></a>
## <a name="adjust-rank"></a>Rangsor módosítása

A portál segítségével módosíthatja a ranglistán megjelenő módosítására, a Bing adja vissza az eredményeket. A betekintő ablaktáblában meg egy keresett kifejezést, és futtassa a lekérdezést. Az előnézeti ablaktáblában láthatja a keresési eredmények a lekérdezés listázza. Jobbra található minden eredmény látható beállításainak listája, végezhet. 

- Letiltása. A tartomány, Melléklap vagy weblap áthelyezi a letiltott listába. Akkor válassza ki a szintet, hogy. A Bing a keresési eredmények a kiválasztott hely nem tartalmazza a tartalmat.  
  
- Jelentősen növelheti. A tartomány vagy nagyobb a keresési eredmények Melléklap kiadóvállalat tartalmat. Akkor válassza ki, hogy jelentősen növelheti a tartalom a tartományból vagy subpage, hogy a képernyőn látható weblapon tartozik. [További](#boosting-and-demoting)  
  
- Lefokozása. Értéksorrá minősíti vissza a tartományt vagy a keresési eredmények alsó Melléklap tartalmat. Akkor válassza ki, hogy fokozza le a tartalmat a tartományból vagy subpage, hogy a képernyőn látható weblapon tartozik. [További információ](#boosting-and-demoting).  
  
- PIN-kód fent. Adja meg a weblap, amelyen a legfelül jelenik meg az eredményeket, ha a felhasználó lekérdezési karakterlánc megfelel a PIN-kódok. lekérdezési karakterlánc alapján a PIN-kódot az egyezési feltétellel. Az aktív lista nem kell tartalmaznia, hogy rögzítse a képernyőn látható weblapon. [További információ](#pin-to-top).

Rangsor módosítása esetén nem áll rendelkezésre a képkeresési, és a videó keresési megoldásokat.

## <a name="boosting-and-demoting"></a>Kiemelési és lefokozása

Super növelése, növelheti, vagy bármilyen tartomány lefokozása vagy is az aktív lista subpage. Alapértelmezés szerint az összes szelet a azonos súlyú kerülnek. A keresési eredmények között (a felügyelői boost rangsorolási boost nagyobb) magasabb rangsora Super súlyozott vagy súlyozott cikkeket. A keresési eredmények között, hogy a rendszer visszalép elemek rangsora alacsonyabb.

Fontos megjegyezni, amely super növelése jelentősen növelheti és fokozza a tartományok vagy mindegyik lapot tünteti biztosíthat hozzájuk variantní hodnoty. Ez ez csak egyike az eredmények sorrendjének meghatározásához a rangsorolás által használt számos jelek. Ez azt jelenti, hogy egy adott lekérdezésre vonatkozó azok hatása nem garantált, sok más tényező befolyásolhatja a webes eredmények teljes rangsorolását.  Lehetséges megállapításához, hogy kiemelési vagy lefokozásához rendelkezik a rangsorolás, teszteléshez a keresési funkciót a betekintő ablaktáblában.

Is super növelése, növelheti, vagy lefokozása elemek vezérlőelemeivel sorrend beállítása az aktív lista vagy a Boost használatával és lefokozása szabályozza az előnézeti ablaktáblában láthatja. A szolgáltatás a szelet ad hozzá az aktív listához, és beállítja a rangsorolás ennek megfelelően.

Super növelése jelentősen növelheti és lefokozása nem állnak rendelkezésre a képkeresési és Videókeresési funkciókat.

## <a name="pin-to-top"></a>Rögzítse a tetejére

Egy weblap, egy adott lekérdezésre vonatkozó a keresési eredmények tetejére rögzíteni, válassza ki az alábbi lehetőségek közül:

1.  Az a **Pinned** lapra, adja meg a felső részén az eredményeket, és a lekérdezés, amely elindítja a rögzítés rögzítése a weblap URL-CÍMÉT.  
  
2.  Az a **előzetes** panelen adja meg a keresési kifejezésre, és kattintson a Keresés gombra. Ezután azonosítsa a weblapra a szeretne rögzíteni az eredmények tetejére, ha a felhasználó ad ugyanazokkal a lekérdezési eredmények. Kattintson a **felső PIN-kód**. A szolgáltatás hozzáadja a weblapon és a lekérdezés a **Pinned** listája. 

A PIN-kódok a nyomon követheti a **Pinned** fülre. A PIN-kód jelenik meg másként\<lekérdezés\>, \<weblap\>"párokat. 

Az eredmények tetejére egy weblap legfeljebb egy adott lekérdezésre vonatkozó is rögzíthet.

PIN-kódok nem érhetők el a képkeresési, és a videó keresési megoldásokat.

### <a name="specifying-the-pins-match-condition"></a>A PIN-kódot az egyezési feltétellel megadása

A weblap rögzítve van az eredmények tetejére csak akkor, ha a felhasználó lekérdezési karakterlánc megfelel a PIN-kód lekérdezési karakterlánc PIN kód egyezés feltétel alapján. Alapértelmezés szerint a PIN-kód csak akkor, ha a PIN-kód lekérdezési karakterláncot, és a felhasználói lekérdezési karakterlánc pontosan egyezik. A következő egyezési feltételei megadásával módosíthatja az alapértelmezett viselkedést.

- Kezdődik &mdash; a PIN-kód egyezés, ha a lekérdezési karakterlánc a felhasználó a PIN-kód lekérdezési karakterlánccal kezdődik.
- Végződik &mdash; a PIN-kód egyezés, ha a felhasználó lekérdezési karakterláncot a PIN-kód lekérdezési karakterlánccal végződik.
- Tartalmaz &mdash; a PIN-kód egyezés, ha a lekérdezési karakterlánc a felhasználó a PIN-kód lekérdezési karakterláncot tartalmaz.

A PIN-kódot az egyezési feltétellel módosításához kattintson a PIN-kód szerkesztési ikonra (a Ceruza tűnik). Az a **lekérdezés az egyezési feltétellel** oszlopot, kattintson a legördülő listára, és válassza ki az új feltétel használatához. Ezután kattintson a Mentés ikonra a módosítás mentéséhez.

Az összes összehasonlítások megkülönböztetik a kis-és nagybetűket.

### <a name="changing-the-order-of-the-pins"></a>A PIN-kódok sorrendjének módosítása

A PIN-kódok sorrendjének módosításához n húzza a PIN-kódot is, vagy szerkesztheti a PIN-kódot, és a sorszám megváltoztatásához. N húzza a PIN-kódot kattintson a PIN-kód áthelyezése, válságos tartani az egérgombot, és a PIN-kód húzza át a PIN-kód lecseréli a listában, és majd engedje el az egér gombját. Figyelje meg, hogy a PIN-kód sorszám ennek megfelelően változik.

A PIN-kód sorszám szerkesztheti is. Az a **vezérlők** oszlopot, kattintson a PIN-kód szerkesztési ikonra (a Ceruza tűnik). Adja meg a számát, ha azt szeretné, hogy a PIN-kód jelenik meg a listában, és nyomja le a adja meg, vagy kattintson a Mentés ikonra. Ha például a PIN-kód jelenleg hatodik a listában, és azt szeretné, hogy a második kell, ha a sorszám megváltoztatásához a két (2).

Több PIN-kód egyezés feltételnek, ha a PIN-kódok sorrendje határozza meg, melyik PIN-kód, a Bing. Például ha PIN-kódok két és három egyezés feltételnek, a Bing PIN-kód két használja.

## <a name="use-bing-to-specify-slices"></a>Megadhatja a Bing használatával

Nincsenek többféle módon, adja meg az egyéni keresési alkotó webes szeleteit. Ha tudja, hogy a szeletek fel szeretne venni példányában, vegye fel őket a példány **aktív** listája. Elemek hozzáadásával kapcsolatos információkat a **aktív** lista, lásd: [aktív és a letiltott listák](#active-and-blocked-lists).

De ha nem biztos abban, hogy mely szeletek tartalmazza, lekérdezéseket is futtathat a Bing a **előzetes** ablaktáblán, és tekintse meg a Bing adja vissza. Ezután válassza ki, hogy az egyéni keresési szerepeltetni kívánt szelet. Akkor valószínűséggel kell futtatnia, győződjön meg arról, hogy a példány kívánt összes szelet több lekérdezési kifejezéseket. 

Kövesse az alábbi lépéseket a Bing használatával szeleteket ad hozzá az egyéni keresési példány. 

1.  Jelentkezzen be a Bing Custom Search [portál](https://customsearch.ai).
2.  Hozzon létre egy példányt, vagy válasszon ki egy példányt frissíteni.
3.  Az Előnézet ablaktáblán a jobb oldalon válassza ki a Bing a legördülő listából.
4.  A keresőmezőbe adja meg a példány fontos lekérdezési kifejezés.
5.  Kattintson a **Hozzáadás hely** mellett fel szeretne venni az eredményt.
6.  Kattintson az **OK** gombra.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="view-statistics"></a>Statisztikák megjelenítése

Ha egyéni keresés megfelelő szinten előfizetett (lásd a [díjszabási lapjait](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), amely egy **statisztika** lapra kerül az éles környezetben üzemelő példányok. A Statisztika lapon részletesebb információt nyújt az egyéni keresés végpontok használata, beleértve a hívások mennyisége, a leggyakoribb lekérdezések, a földrajzi eloszlása, a válaszkódot és a biztonságos keresés. Ismerteti a megadott vezérlők használatával szűrheti.

## <a name="understanding-quota"></a>Kvóta ismertetése

- Egyéni keresési példányonként maximális számát, előfordulhat, hogy győződjön meg arról, hogy ennek a területnek korrekciók **Active** és **letiltott** szeletek 400 korlátozódik.
- Egy rangsorolást korrekciós számít az aktív vagy letiltott lapokat ad hozzá egy szeletet.
- Kiemelési és lefokozása száma, két rangsorolási módosítását.
- Egyéni keresési példányonként PIN-kódok, hogy legfeljebb 200 korlátozva.

## <a name="next-steps"></a>További lépések

- [Egyéni keresés meghívása](./search-your-custom-view.md)
- [Üzemeltetett felhasználói felület konfigurálása](./hosted-ui.md)
- [Díszítő jelölők használata szövegkiemeléshez](./hit-highlighting.md)
- [Weboldalak lapozása](./page-webpages.md)