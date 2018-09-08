---
title: 'Bing Custom Search: Egyéni nézet definiálása |} A Microsoft Docs'
description: Ismerteti, hogyan hozhat létre egy hely és a függőleges keresési szolgáltatások
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 8ef8aabc7363db88317a6428301512b0a0d4c055
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158772"
---
# <a name="configure-your-custom-search-experience"></a>Konfigurálja az egyéni keresés
Egy egyéni keresési példány lehetővé teszi a keresési funkciók csak tartalmát, hogy a felhasználók számára is megfelelő kódolási sebesség kiválasztását. Helyett a webes keresés, a Bing keresés csak az Önt érdeklő webes szelet.
A webes egyéni nézetének létrehozásához használja a Bing Custom Search [portál](https://customsearch.ai). További információ a portál bejelentkezés: [az első Bing Custom Search-példány létrehozása](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/quick-start). A portálon egy keresési példányát, amely meghatározza a tartományok, mindegyik lapot tünteti, és, amelyek azt szeretné, hogy a Keresés a Bing és az azokkal, amelyek nem szeretné, hogy a keresés létrehozását teszi lehetővé. A tartalom, hogy megismerkedett az URL-címek megadása, mellett is kérhet, javasolja a tartalmakat, amelyeket érdemes a nézethez hozzáadandó a portálon. A következő módja az, hogy határozhatja meg a webes szelet: 

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

Szeletek módosításához vagy eltávolításához használja a vezérlők oszlopban beállításait. 

Hasonlóképpen adhat hozzá szeletek letiltott listán (kivéve a szeletek megadásához egy feltöltendő fájl nem használható).

## <a name="pinned-list"></a>Rögzített listája
A portál segítségével rögzítheti egy adott weboldal a keresési eredmények tetejére, ha a felhasználó egy adott keresési kifejezést ad meg. A **Pinned** lapon lekérdezési kifejezés, és a weblap párok, melyek adja meg a weblap, egy adott lekérdezésre vonatkozó felső eredményként megjelenő listáját tartalmazza. A felhasználó keresési kifejezésre pontosan egyeznie kell a rögzített keresési kifejezésre.
További információ a rögzítés eredmények: [rangsor módosítása](#adjustrank).

Rögzítés eredmények nem áll rendelkezésre a képkeresési élmény.

## <a name="site-suggestions"></a>Webhelyjavaslatok
A szolgáltatás szeletek az aktív listára felvett állít elő, hely és Melléklap javaslatokat, amelyeket érdemes hozzáadni a keresést. A **érdemes hozzáadni** szakasz tartalmazza a vonatkozó javaslatok. A példány beállításai lapon tartalmazza az ebben a szakaszban csak akkor, ha a javaslat érhető el. 

Javaslatok az aktív lista hozzáadásához kattintson a + ikonra.  Mivel a szolgáltatás javaslatait a beállítások alapján hoz létre, mindenképp kattintson **frissítése** a javaslatok hozzáadása után. 

## <a name="preview-pane"></a>Podokno náhledu
A jobb oldalon a betekintő segítségével keresési lekérdezéseknek és az eredmények megtekintése a search-példány ki tesztelheti. Válassza ki **saját példány**, válassza ki a biztonságos keresési szűrő, és mi piaci keresése (lásd: [lekérdezési paraméterek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters). Adjon meg egy lekérdezést, és nyomja le adja meg, vagy kattintson a keresés ikonra az aktuális konfiguráció eredményeinek megtekintéséhez. Kattintson a webes eredmények megtekintése a **webes**, kattintson a lemezkép eredmények megtekintéséhez **lemezkép**. 

 Használja az előnézeti ablaktáblában láthatja, emellett áttekintheti a Bing-eredmények kiválasztásával **Bing** helyett **saját példány**. Ez a keresés eredményeit a Bing által visszaadott eredmények összehasonlításához hasznos lehet.

<a name="adjustrank"></a>
## <a name="adjust-rank"></a>Rangsor módosítása
A portál segítségével módosíthatja a ranglistán megjelenő módosítására, a Bing adja vissza az eredményeket. A betekintő ablaktáblában meg egy keresett kifejezést, és futtassa a lekérdezést. Az előnézeti ablaktáblában láthatja a keresési eredmények a lekérdezés listázza. Jobbra található minden eredmény látható beállításainak listája, végezhet. 

- Letiltása. A tartomány, Melléklap vagy weblap áthelyezi a letiltott listába. Akkor válassza ki a szintet, hogy. A Bing a keresési eredmények a kiválasztott hely nem tartalmazza a tartalmat. 
- Jelentősen növelheti. A tartomány vagy nagyobb a keresési eredmények Melléklap kiadóvállalat tartalmat. Akkor válassza ki, hogy jelentősen növelheti a tartalom a tartományból vagy subpage, hogy a képernyőn látható weblapon tartozik.
- Lefokozása. Értéksorrá minősíti vissza a tartományt vagy a keresési eredmények alsó Melléklap tartalmat. Akkor válassza ki, hogy fokozza le a tartalmat a tartományból vagy subpage, hogy a képernyőn látható weblapon tartozik. 
- PIN-kód fent. Adja meg a képernyőn látható weblapon, az eredmények legfelül jelenik meg, ha a felhasználó lekérdezési kifejezés pontosan megfelel a lekérdezési kifejezés használt. Az aktív lista nem kell tartalmaznia, hogy rögzítse a képernyőn látható weblapon. 

Rangsor módosítása nem áll rendelkezésre a képkeresési élmény.

## <a name="boosting-and-demoting"></a>Kiemelési és lefokozása
Super növelése, növelheti, vagy bármilyen tartomány lefokozása vagy is az aktív lista subpage. Alapértelmezés szerint az összes szelet a azonos súlyú kerülnek. A keresési eredmények között (a felügyelői boost rangsorolási boost nagyobb) magasabb rangsora Super súlyozott vagy súlyozott cikkeket. A keresési eredmények között, hogy a rendszer visszalép elemek rangsora alacsonyabb.

Fontos megjegyezni, amely super növelése jelentősen növelheti és fokozza a tartományok vagy mindegyik lapot tünteti biztosíthat hozzájuk variantní hodnoty. Ez ez csak egyike az eredmények sorrendjének meghatározásához a rangsorolás által használt számos jelek. Ez azt jelenti, hogy egy adott lekérdezésre vonatkozó azok hatása nem garantált, sok más tényező befolyásolhatja a webes eredmények teljes rangsorolását.  Lehetséges megállapításához, hogy kiemelési vagy lefokozásához rendelkezik a rangsorolás, teszteléshez a keresési funkciót a betekintő ablaktáblában.

Is super növelése, növelheti, vagy lefokozása elemek vezérlőelemeivel sorrend beállítása az aktív lista vagy a Boost használatával és lefokozása szabályozza az előnézeti ablaktáblában láthatja. A szolgáltatás a szelet ad hozzá az aktív listához, és beállítja a rangsorolás ennek megfelelően.

Super növelése jelentősen növelheti és lefokozása módosításokat a rendszer automatikusan menti, és az egyéni keresési végponton azonnal tükrözik. 

Super növelése jelentősen növelheti és lefokozása nem állnak rendelkezésre a lemezkép keresési funkciót.

## <a name="pin-to-top"></a>Rögzítse a tetejére
Egy weblap, egy adott lekérdezésre vonatkozó a keresési eredmények tetejére rögzíteni, válassza ki az alábbi lehetőségek közül:

1.  A Pinned lapon adja meg a felső részén az eredményeket, és a pontos lekérdezés, amelyek kiváltják a rögzítés rögzítése a weblap URL-CÍMÉT. 
2.  A betekintő ablaktáblában adja meg a keresési kifejezésre, és kattintson a Keresés gombra. Ezután azonosítsa a weblapra a szeretne rögzíteni az eredmények tetejére, ha a felhasználó ad ugyanazokkal a lekérdezési eredmények. Kattintson a PIN-kódot a tetejére. A service a weblapon és a lekérdezés hozzáadása a Pinned listához. 

A PIN-kódok a Pinned lapon követheti nyomon. A PIN-kód jelenik meg másként\<lekérdezés\>, \<weblap\>"párokat. 

A weblap rögzítve van, csak akkor, ha a felhasználó lekérdezés pontosan megegyezik a lekérdezést. 

Az eredmények tetejére egy weblap legfeljebb egy adott lekérdezésre vonatkozó is rögzíthet.

PIN-kódok nem érhetők el a rendszerképet a keresési funkciót.

## <a name="use-bing-to-specify-slices"></a>Megadhatja a Bing használatával
Nincsenek többféle módon, adja meg az egyéni keresési alkotó webes szeleteit. Ha fel szeretne venni a példány a szelet ismeri, adja hozzá a példány aktív listájához. Adásával kapcsolatos információkért elem az aktív listához saját magának, lásd: [aktív és a letiltott listák](#active-and-blocked-lists).
Azonban ha nem biztos abban, hogy mely szeletek tartalmazza, a Bing-lekérdezések futtatása a betekintő ablaktáblában, és tekintse meg a Bing adja vissza. Ezután válassza ki, hogy az egyéni keresési szerepeltetni kívánt szelet. Akkor valószínűséggel kell futtatnia, győződjön meg arról, hogy a példány kívánt összes szelet több lekérdezési kifejezéseket. 

Kövesse az alábbi lépéseket a Bing használatával szeleteket ad hozzá az egyéni keresési példány. 
1.  Jelentkezzen be a Bing Custom Search [portál](https://customsearch.ai).
2.  Hozzon létre egy példányt, vagy válasszon ki egy példányt frissíteni.
3.  Az Előnézet ablaktáblán a jobb oldalon válassza ki a Bing a legördülő listából.
4.  A keresőmezőbe adja meg a példány fontos lekérdezési kifejezés.
5.  Kattintson a **Hozzáadás hely** mellett fel szeretne venni az eredményt.
6.  Kattintson az **OK** gombra.

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="view-statistics"></a>Statisztikák megjelenítése
Ha egyéni keresés megfelelő szinten előfizetett (lásd a [díjszabási lapjait](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), amely egy **statisztika** lapra kerül az éles környezetben üzemelő példányok. A Statisztika lapon részletesebb információt nyújt az egyéni keresés végpontok használata, beleértve a hívások mennyisége, a leggyakoribb lekérdezések, a földrajzi eloszlása, a válaszkódot és a biztonságos keresés. Ismerteti a megadott vezérlők használatával szűrheti.

## <a name="understanding-quota"></a>Kvóta ismertetése
- Egyéni keresési példányonként maximális számát, előfordulhat, hogy győződjön meg arról, hogy ennek a területnek korrekciók **Active** és **letiltott** szeletek 400 korlátozódik.
- Egy rangsorolást korrekciós számít az aktív vagy letiltott lapokat ad hozzá egy szeletet.
- Kiemelési és lefokozása száma, két rangsorolási módosítását.
- Egyéni keresési példányonként PIN-kódok, hogy legfeljebb 200 korlátozva.

## <a name="next-steps"></a>További lépések

- [Hívja meg az egyéni keresés](./search-your-custom-view.md)
- [A központi felhasználói felület konfigurálása](./hosted-ui.md)
- [Kiemeli a szöveget decoration jelölők használatával](./hit-highlighting.md)
- [Oldal weblapok](./page-webpages.md)