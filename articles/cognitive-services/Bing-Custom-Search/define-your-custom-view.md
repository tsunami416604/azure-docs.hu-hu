---
title: 'Bing egyéni keresés: Egyéni nézet definiálása |} Microsoft Docs'
description: A hely és a függőleges search szolgáltatás létrehozása
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 8ffe3087df398d6310828e41d0c6992199fafbed
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347887"
---
# <a name="configure-your-custom-search-experience"></a>Az egyéni keresési funkciók konfigurálása
Egy egyéni keresési példányát lehetővé teszi testre szabni a keresési élményt biztosít a webhelyeket, amelyeket a felhasználók érdeklik csak tartalmát is. Webes kiterjedő keresés végrehajtása helyett a Bing csak az Önt érdeklő webes szelet keres.
A webes egyéni nézet létrehozásához használja a Bing egyéni keresési [portal](https://customsearch.ai). További információ a portálra való bejelentkezéskor: [hozza létre az első Bing egyéni keresési példányát](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/quick-start). A portál segítségével hozzon létre egy keresési példányt, amely meghatározza a tartományok, mindegyik lapot tünteti, és azt szeretné, hogy internetes keresés a weblapokat és azokat, amelyeket nem kíván úgy, hogy a keresés. Mellett adja meg a tartalom tudnia: az URL-címeket, a portálhoz, és felajánlja a tartalmat, előfordulhat, hogy szeretne a nézethez is kérhet. A következő módon az, hogy a szelet a Web adhat meg: 

1.  Tartomány. A tartomány szelet internetes tartományokon belül található összes tartalmat tartalmazza. Például www.microsoft.com. Bing is kereshet a tartomány altartományok kihagyva "www" okoz. Például ha megadja a Microsoft.com webhelyre mutat, a Bing is eredményeket ad vissza a support.microsoft.com vagy technet.microsoft.com.
2.  Melléklap. A Melléklap szelet magában foglalja a program a Melléklap lent azt. Legfeljebb két mindegyik lapot tünteti előfordulhat, hogy adja meg az elérési útban. Például www.microsoft.com/en-us/windows/ 
3.  A weblap. A weblap szelet csak a weblap megadható egyéni keresés. Azt nem kötelezően megadhatja, hogy mindegyik lapot tünteti tartalmazza.

Minden tartomány, mindegyik lapot tünteti és azon weblapjaira, amelyek a megadott nyilvános és a Bing által indexelt kell lennie. Ha Ön a tulajdonosa a nyilvános webhely, amelyet szeretne belefoglalni a keresésbe, és a Bing még nem indexelt azt, tekintse meg a Bing [gazdáját dokumentáció](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) Bing indexelésre az első vonatkozó további információért. Emellett a bejárt helyen frissítéséhez, ha az index elavult Bing első vonatkozó további információért gazdáját dokumentációjában találhat.

## <a name="adding-slices-to-your-custom-search"></a>Az egyéni keresési szeletek hozzáadása
Ha az egyéni keresési példányát, az aktív és a blokkolt tartományok, mindegyik lapot tünteti és szeretne keresni, vagy keressen weblapokat adja meg.  

- Aktív: Listája tartományok, mindegyik lapot tünteti vagy weblapok ahhoz, hogy a keresés szerepeljen. 
- Letiltva: Tartomány, mindegyik lapot tünteti, vagy listáját szeretné kizárni a Keresés a weboldalakon. Blokkolja kell lennie a tartalom a tartományok és mindegyik lapot tünteti alatt található elemek szerepel-e az aktív.

Minden lista eléréséhez kattintson az egyéni keresési példányát az aktív és a blokkolt lapokon. 

<a name="active-and-blocked-lists"></a>
## <a name="active-and-blocked-lists"></a>Aktív és a blokkolt listája 
Az internetes keresés a kívánt webhely szelet megadásához kattintson a **aktív** lapra, és a tartományok, mindegyik lapot tünteti és weblapok kereséséhez listája. A szelet közvetlenül hozzáadni a listához, vagy egynél több szelet hozzáadása a feltöltési ikonnal szövegfájlba feltöltésével.

Fájl feltöltése részletei: 

- Fájl feltöltése érhető el csak az aktív listához való hozzáadás szeletek, szeletek hozzáadása a blokkolt listája nem használható. 
- Hozzon létre egy szövegfájlt, és adja meg a weblap soronként, egyetlen tartományvezérlő vagy melléklap. A teljes feltöltést nem fogadja el, ha hiba történik. 
- Ha a blokkolt listája a tartomány, Melléklap vagy a feltöltendő fájl megadott weblap tartalmaz, a szolgáltatás eltávolítja a blokkolt listája, és hozzáadja azt az aktív lista. 
- A szolgáltatás figyelmen kívül hagyja a feltöltendő fájl ismétlődő elemek.

Szerkesztése vagy törlése szeletek, használja a vezérlők oszlopban beállításait. 

Hasonlóképpen adhat hozzá szeletek a blokkolt listája (kivéve a feltöltendő fájl használatával nem adja meg a szeletek).

## <a name="pinned-list"></a>Rögzített listájához
A portál segítségével rögzítheti egy adott weboldal a keresési eredmény tetejére, ha a felhasználó adja meg a keresési kifejezés is. A **Pinned** lapon a lekérdezés és a weblap tartalmaznak, amelyek adja meg a képernyőn látható weblapon, hogy megjelenik-e egy adott lekérdezés felső eredményeként listáját tartalmazza. A felhasználó lekérdezés kifejezés pontosan egyeznie kell a rögzített lekérdezési kifejezésre.
Eredmények rögzítési kapcsolatos információkért lásd: [beállítása dimenziószáma](#adjustrank).

Eredmények rögzítési nincs elérhető a kép keresési élményt biztosít.

## <a name="site-suggestions"></a>Hely javaslatok
A szolgáltatás után az aktív listához való hozzáadás szeletek, állít elő, hely- és Melléklap javaslatok, amelyeket érdemes hozzáadni a keresést. A **hozzáadni kívánt** a szakasz a javaslatokat tartalmaz. A példány beállítások lapon ez a szakasz tartalmazza, csak akkor, ha a javaslatok érhetők el. 

Javaslatok az aktív listához történő hozzáadásához kattintson a + ikonra.  Mivel a szolgáltatás javaslatok a beállítások alapján hoz létre, mindenképp kattintson **frissítése** a javaslatok hozzáadása után. 

## <a name="preview-pane"></a>Előnézeti ablaktáblában láthatja
A keresési példányát, a jobb oldali az előnézeti ablaktáblában láthatja segítségével keresési lekérdezések és az eredmények megtekintéséhez a kimenő tesztelheti. Válassza ki **saját példány**, válassza ki a biztonságos keresési szűrőt, és mi piaci kereséséhez (lásd: [lekérdezésparamétereket](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters). Adjon meg egy lekérdezést, és nyomja le az adja meg, vagy kattintson a keresés ikonra a jelenlegi konfiguráció eredményeinek megtekintése. Kattintson a webes eredmények megtekintése érdekében **webes**, kattintson a kép eredmények megtekintése érdekében **kép**. 

 Az előnézeti ablaktáblában láthatja használva is felhasználhatja a Bing eredmények kiválasztásával **Bing** helyett **saját példány**. Ez lehet hasznos, ha szeretné az eredményeket a keresési élményt biztosít a Bing által adott eredmények összehasonlítása.

<a name="adjustrank"></a>
## <a name="adjust-rank"></a>Dimenziószáma beállítása
A portál segítségével kezelheti a Bing visszaadó eredmények prioritás beállítása. Az előnézeti ablaktáblában láthatja írja be a keresési kifejezést, és a lekérdezés futtatásához. Az előnézeti ablaktáblában láthatja a keresési eredményt a lekérdezés listázza. Az egyes eredmények jobb beállításainak listája, a végezheti el. 

- Blokk. A tartomány, Melléklap vagy weblap áthelyezi a blokkolt listája. Akkor válassza ki a szintet, hogy. Bing tartalom nem tartalmazza a kijelölt webhelyet, a keresési eredmények között. 
- Növelése. A tartomány vagy magasabb a keresési eredmények Melléklap növekedhet tartalmat. Kiválaszthatja, hogy a tartalom a tartományból növelése vagy subpage, hogy a képernyőn látható weblapon tartozik-e.
- Fokozni. Úgy fokozza le a tartalmat a tartomány vagy Melléklap alacsonyabb a keresési eredmények között. Kiválaszthatja, hogy a tartalom a tartomány lefokozása vagy subpage, hogy a képernyőn látható weblapon tartozik-e. 
- PIN-kód fent. Adja meg a weblap, hogy az eredmények tetején jelenik meg, ha a felhasználó lekérdezési kifejezés pontosan megfelel a lekérdezési kifejezés. Az aktív lista nem kell tartalmaznia ahhoz, hogy rögzítheti a képernyőn látható weblapon. 

Dimenziószáma beállítása nincs elérhető a kép keresési élményt biztosít.

## <a name="boosting-and-demoting"></a>Kiemelése és lefokozása
Super növelése, növelése, vagy a tartomány lefokozása vagy is az aktív lista subpage. Alapértelmezés szerint az összes szeletek az azonos súlyozással rendelkező lettek hozzáadva. Super súlyozott vagy súlyozott elemek rendszer előrébb a keresési eredmények között (a felügyelői program prioritása nagyobb, mint a program). A keresési eredmények elemeket, amelyek lefokozása rangsora alsó.

Fontos megjegyezni, hogy super növelése, növelése, valamint adjon súlyozási értéket változatok a tartományok vagy mindegyik lapot tünteti lefokozása. Ez az csak az egyik sok jelek a ranker használják az eredmények rendezéséhez. Ez azt jelenti, hogy egy adott lekérdezés hatásuk nem garantált, számos tényező befolyásolhatja a webes eredmények általános besorolását.  Lehetséges megállapításához, hogy kiemelése vagy lefokozása van a teszteléshez használt ranker, a keresési élményt biztosít a betekintő ablaktáblában.

Is super növelése, növelése, vagy elemek lefokozásához, a prioritás beállítása szabályozza a aktív lista használatával vagy a program használatával és lefokozása vezérlők az előnézeti ablaktáblában láthatja. A szolgáltatás a szelet ad hozzá az aktív listához, és beállítja a rangsorolási ennek megfelelően.

Super növelése növelése és a lefokozás módosításokat a rendszer automatikusan menti, és azonnal tükrözze az egyéni keresési végpontjának ellen. 

Super növelése növelése és a lefokozás nem állnak rendelkezésre a kép keresési élményt biztosít.

## <a name="pin-to-top"></a>PIN-kód a lap tetejére
PIN-kód egy weblap, a keresési eredmények között egy adott lekérdezés tetején, az alábbi lehetőségek közül választhat:

1.  A Pinned lapon adja meg az eredmények és a pontos lekérdezés, amelyek kiváltják a rögzítési tetején rögzítése a weblap URL-CÍMÉT. 
2.  Az előnézeti ablaktáblában láthatja írja be a lekérdezés kifejezést, majd kattintson a keresés. A következő azonosítsa az eredmények között, az eredmények tetején PIN-kód, ha a felhasználó adja meg az ugyanabban a lekérdezésben használni kívánt weblap. Kattintson a PIN-kód elejére. A szolgáltatás ad hozzá a weblapok és a lekérdezés Pinned listájához. 

A PIN-kódok a Pinned lapon követheti nyomon. A PIN-kód látható másként funkciót\<lekérdezés\>, \<weblap\>"párokat. 

A weblap csak akkor, ha a felhasználó lekérdezés pontosan megegyezik a lekérdezés van rögzítve. 

Egy adott lekérdezés PIN-kód legfeljebb egy weblap, az eredmények tetején.

PIN-kódok nem érhetők el a kép keresési élményt biztosít.

## <a name="use-bing-to-specify-slices"></a>Adja meg a szeletek a Bing használatával
Többféle módon lehet megadni a szeletek a webhely az egyéni keresési alkotó. Ha tudja, hogy a példány szerepeltetni kívánt szeletek, vegye fel a példány aktív listára. Hozzáadásával kapcsolatos információkat elem az aktív listához magát, a következő témakörben: [aktív és a blokkolt](#active-and-blocked-lists).
De ha nem biztos abban, hogy mely szeletek tartalmazza, a Bing lekérdezéseket futtathat az előnézeti ablaktáblában láthatja, és tekintse meg a Bing adja vissza. Ezután kiválaszthatja az egyéni keresési szerepeltetni kívánt a szeleteket. Valószínűleg futtatásához szükséges több lekérdezés feltételeket, hogy győződjön meg arról, hogy a szeleteket, amelyeket a példányok esetén. 

A lépések a Bing ezen szeletek hozzá az egyéni keresési példányát. 
1.  Jelentkezzen be a Bing egyéni keresés [portal](https://customsearch.ai).
2.  Hozzon létre egy példányt, vagy válasszon ki egy példányt frissíteni.
3.  Az Előnézet ablaktáblán a jobb oldalon válassza ki a Bing a legördülő listából.
4.  A keresési mezőbe írja be a lekérdezési kifejezés ehhez a példányhoz tartozó.
5.  Kattintson a **hely hozzáadása a** mellett a felvenni kívánt eredményt.
6.  Kattintson az **OK** gombra.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="view-statistics"></a>Statisztika megjelenítése
Egyéni keresés megfelelő szinten előfizetett (lásd a [lapok árképzési](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), egy **statisztika** lapon hozzáadódik a termelési példányok. A statisztika fülre az egyéni keresés végpontok használata, beleértve a hívásainak mennyiségére, leggyakoribb lekérdezések, földrajzi eloszlása, válaszkódot és biztonságos keresési részleteit jeleníti meg. A megadott vezérlők használatával részletek jeleníthetők meg.

## <a name="understanding-quota"></a>Understanding kvóta
- Minden egyéni keresési példány rangsorolási módosítását, hogy az lehet, hogy legfeljebb hány **aktív** és **zárolva** szeletek 400 korlátozódik.
- Egy rangsorolási módosítás számít a szelet felvétele az aktív vagy letiltva lapokat.
- Kiemelése és lefokozása száma, két rangsorolási módosítását.
- Minden egyéni keresési példányát a maximális számát, amelyeket PIN-kódok korlátozódik 200.

## <a name="next-steps"></a>További lépések

- [Az egyéni keresési hívás](./search-your-custom-view.md)
- [A központi felhasználói felületi élmény konfigurálása](./hosted-ui.md)
- [Jelölje ki a szöveget decoration jelölők használatával](./hit-highlighting.md)
- [Lap szolgáltatásának konfigurálása](./page-webpages.md)