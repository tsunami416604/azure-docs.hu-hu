---
title: A Bing egyéni keresési élményének konfigurálása | Microsoft dokumentumok
titleSuffix: Azure Cognitive Services
description: A portál lehetővé teszi, hogy hozzon létre egy keresési példányt, amely meghatározza a szeletek a weben; tartományok, allapok és weblapok.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: c14376cc80373371ec5fcb8f22a00584a6b2f714
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220214"
---
# <a name="configure-your-bing-custom-search-experience"></a>A Bing egyéni keresési élményének konfigurálása

Az egyéni keresési példány lehetővé teszi, hogy a keresési élményt úgy alakítsa ki, hogy az csak olyan webhelyekről származó tartalmakat tartalmazzon, amelyek a felhasználók számára fontosak. Ahelyett, hogy webes keresést végezne, a Bing csak az Önt érdeklő webes szeleteket keresi meg. Az egyéni kívánalmaknak megfelelő webes keresés létrehozásához használja a Bing Custom Search [portált](https://customsearch.ai).

A portál lehetővé teszi, hogy hozzon létre egy keresési példányt, amely meghatározza a szeleteket a web: tartományok, allapok és weblapok, hogy azt szeretné, bing keresni, és azokat, amelyek nem szeretné, hogy keressen. A portál olyan tartalmakat is javasolhat, amelyeket esetleg szeretne felvenni.

A webszeletek definiálásakor használja az alábbiakat:

| Szelet neve | Leírás                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domain     | A tartományszelet tartalmazza az internetes tartományban található összes tartalmat. Például: `www.microsoft.com`. A kihagyás hatására `www.` a Bing a tartomány altartományaiban is keres. Ha például megadja `microsoft.com`a , a `support.microsoft.com` `technet.microsoft.com`Bing a vagy a ból származó eredményt is visszaadja. |
| Aloldalt    | Az aloldalszelet az aloldalon található összes tartalmat és az alatta lévő görbéket tartalmazza. Az elérési útban legfeljebb két aloldalt adhat meg. Például: `www.microsoft.com/en-us/windows/`                                                                                                                       |
| Weboldal    | A weblapszeletek csak az adott weblapot tartalmazhatják egyéni keresésben. Megadhatja, hogy az aloldalakat is tartalmazza-e.                                                                                                                                                                                  |

> [!IMPORTANT]
> A megadott tartományoknak, allapoknak és weblapoknak nyilvánosnak kell lenniük, és a Bingnek indexelnie kell őket. Ha olyan nyilvános webhelyet birtokol, amelyet fel szeretne venni a keresésbe, és a Bing nem indexelte azt, tekintse meg a Bing [webmester dokumentációjában](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) a Bing indexelésének lefoglalásáról. A bejárt webhely frissítésének lehetővé tésével kapcsolatban a webmester dokumentációjában is tájékozódhat, ha az index elavult.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>A web szeleteinek hozzáadása az egyéni keresési példányhoz

Az egyéni keresési példány létrehozásakor megadhatja a webhely azon szeleteit, hogy milyen tartományokat, aloldalakat és weblapokat szeretne felvenni vagy letiltani a keresési eredmények között. 

Ha ismeri az egyéni keresési példányba felvenni kívánt szeleteket, vegye fel őket a példány **Aktív** listájára. 

Ha nem biztos abban, hogy mely szeleteket szeretné felvenni, a **betekintő** ablaktáblában elküldheti a keresési lekérdezéseket a Bingnek, és kiválaszthatja a kívánt szeleteket. Ehhez tegye a következőket: 

1. válassza ki a "Bing" elemet a Betekintő ablaktábla legördülő listájából, és adjon meg egy keresési lekérdezést

2. Kattintson a felvenni kívánt eredmény melletti **Webhely hozzáadása** gombra. Ezután kattintson az OK gombra.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>A keresési élmény testreszabása aktív és letiltott listákkal 

Az aktív és letiltott szeletek listájához az egyéni keresési példány **Aktív** és **letiltott** fülére kattintva férhet hozzá. Az aktív listához hozzáadott szeletek szerepelni fognak az egyéni keresésben. A letiltott szeletek nem lesznek keresésben, és nem jelennek meg a keresési eredmények között.

A Bing által keresett webes szeletek megadásához kattintson az **Aktív** fülre, és adjon hozzá egy vagy több URL-t. Url-ek szerkesztéséhez vagy törléséhez használja a **Vezérlők** oszlopban található beállításokat. 

Amikor URL-eket ad hozzá az **Aktív** listához, egyszerre egyetlen URL-t vagy több URL-t adhat hozzá egy szöveges fájl feltöltésével a feltöltési ikonhasználatával.

![A Bing egyéni keresés aktív lapja](media/file-upload-icon.png)

Fájl feltöltéséhez hozzon létre egy szövegfájlt, és adjon meg soronként egyetlen tartományt, aloldalt vagy weblapot. Ha nem megfelelően van formázva, a program elutasítja a fájlt.

> [!NOTE]
> * Csak az **Aktív** listára tölthet fel fájlt. Nem használható szeletek hozzáadására a **Letiltott** listához.  
> * Ha a **Letiltott** lista a feltöltési fájlban megadott tartományt, allapot vagy weblapot tartalmaz, akkor a rendszer eltávolítja a **Letiltott** listából, és hozzáadja az **Aktív** listához.
> * A feltöltési fájlban lévő ismétlődő bejegyzéseket a Bing egyéni keresése figyelmen kívül hagyja. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Webhelyjavaslatok beszerzése a keresési élményhez

Miután hozzáadja a webes szeleteket az **Aktív** listához, a Bing egyéni keresési portál javunkra webhely- és aloldaljavaslatokat hoz létre a lap alján. Ezek olyan szeletek, amelyeket a Bing egyéni keresés szerint érdemes felvenni. Kattintson a **Frissítés** gombra, ha az egyéni keresési példány beállításainak frissítése után frissített javaslatokat szeretne kapni. Ez a szakasz csak akkor látható, ha vannak javaslatok.

## <a name="search-for-images-and-videos"></a>Képek és videók keresése

A webes tartalomhoz hasonlóan képeket és videókat is kereshet a [Bing Egyéni képkereső API vagy](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) a Bing Egyéni [videókeresés API használatával.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference) Ezeket az eredményeket a [hosztolt felhasználói](hosted-ui.md)felülettel vagy az API-kkal jelenítheti meg. 

Ezek az API-k hasonlóak a nem egyéni [Bing képkereső](../Bing-Image-Search/overview.md) és [bingvideokeresési](../Bing-Video-Search/search-the-web.md) API-khoz, de a teljes weben keresnek, és nem igénylik a `customConfig` lekérdezési paramétert. A képek és videók segítségével kapcsolatos további információkért tekintse meg ezeket a dokumentációkészleteket. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>A keresési példány tesztelése a Betekintő ablaktáblával

A keresési példányt a portál jobb oldalán található betekintő ablaktáblával tesztelheti a keresési lekérdezések elküldéséhez és az eredmények megtekintéséhez. 

1. A keresőmező alatt válassza a **Saját példány lehetőséget.** A keresési élmény eredményeit a Bing kiválasztásával összehasonlíthatja a **Bing**lehetőséggel. 
2. Válasszon biztonságos keresési szűrőt, és melyik piacon keressen (lásd: [Lekérdezési paraméterek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)).
3. Írjon be egy lekérdezést, és nyomja le az Enter billentyűt, vagy kattintson a keresési ikonra az aktuális konfiguráció eredményeinek megtekintéséhez. A végrehajtott keresési típust a **Web,** **a Kép**vagy **a Videó** gombra kattintva módosíthatja a megfelelő eredmények elérése érdekében. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>Adott keresési eredmények rangsorának módosítása

A portál lehetővé teszi az adott tartományokból, aloldalakról és weblapokról származó tartalmak keresési rangsorolásának módosítását. Miután elküldte a keresési lekérdezést a betekintő ablaktáblán, minden keresési eredmény tartalmaz egy listát a módosításokhoz:  

|            |                                                                                                                                                                      |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Letiltás      | Áthelyezi a tartományt, az allapot vagy a weblapot a Letiltott listára. A Bing kizárja a kiválasztott webhely tartalmát a keresési eredmények között.                    |
| Reklám      | Növeli a tartomány vagy aloldal tartalmát, hogy magasabb legyen a keresési eredmények között.                                                                                        |
| Lefokozás     | Lefokozni a tartalmat a keresési eredmények ben alacsonyabbtartományból vagy aloldalról. Megadhatja, hogy le szeretné-e fokozni a tartalmat abból a tartományból vagy aloldalról, amelyhez a weblap tartozik. |
| Rögzítés a lap tetejére | Áthelyezi a tartományt, az aloldalt vagy a weblapot a **Rögzített listára.** Ez arra kényszeríti a weblapot, hogy egy adott keresési lekérdezés felső keresési eredményeként jelenjen meg.                   |

A rangsorolás beállítása nem érhető el kép- vagy videókereséshez.

### <a name="boosting-and-demoting-search-results"></a>A keresési eredmények kiemelése és lefokozása

Az **Aktív** lista bármely tartományát vagy aloldalát szuperkiemeléssel, kiemeléssel vagy lefokozhatod. Alapértelmezés szerint az összes szelet rangsorolási korrekció nélkül kerül hozzáadásra. A webes szuperkiemelt vagy kiemelt szeletek magasabb helyezést értek el a keresési eredmények között (a szuperkiemelési rangsor magasabb, mint a kiemelés). A lefokozott elemek rangsorolása alacsonyabb a keresési eredmények között.

Az elemeket az **Aktív** lista **Rangsorolási korrekciója** vezérlőivel, illetve a Betekintő ablaktábla Kiemelés és lefokozása vezérlőjével fokozhatja, kiemelheti vagy lefokozhatja. A szolgáltatás hozzáadja a szeletet az Aktív listához, és ennek megfelelően módosítja a rangsort.

> [!NOTE] 
> A tartományok és aloldalak kiemelése és lefokozása egyike azoknak a módszereknek, amelyekkel a Bing Custom Search a keresési eredmények sorrendjének meghatározására szolgál. A különböző webes tartalmak rangsorolását befolyásoló egyéb tényezők miatt a rangsor módosításának hatásai eltérőek lehetnek. A Betekintő ablaktáblán tesztelheti a keresési eredmények rangjának módosításának hatásait. 

A kép- és videókereséshez nem érhetők el a szuperkiemelés, a kiemelés és a lefokozás.

## <a name="pin-slices-to-the-top-of-search-results"></a>Szeletek rögzítése a keresési eredmények tetejére

A portál azt is lehetővé teszi, hogy pin URL-eket a tetején a keresési eredmények az adott keresési kifejezések, a **Rögzített** lapon. Ne feledje, hogy keresési lekérdezésenként legfeljebb egy weblapot rögzíthet, és csak az indexelt weblapok jelennek meg a keresésekben. A rögzítési eredmények nem érhetők el kép- vagy videokereséshez.

A weblapot kétféleképpen rögzítheti a tetejére:

* A **Rögzített** lapon adja meg a tetején rögzíteni szeretne a weblap URL-címét, és a hozzá tartozó lekérdezést.

* A **Betekintő** ablaktáblán adjon meg egy keresési lekérdezést, és kattintson a keresés gombra. Keresse meg a lekérdezéshez rögzíteni kívánt weblapot, és kattintson **a Rögzítés gombra**a lap tetejére . a weblap és a lekérdezés hozzáadódik a **Rögzített** listához.

### <a name="specify-the-pins-match-condition"></a>A pin egyezési feltételének megadása

Alapértelmezés szerint a weblapok csak akkor kerülnek rögzítésre a keresési eredmények tetején, ha a felhasználó lekérdezési karakterlánca pontosan megegyezik a **Rögzített** listában szereplő karakterlánccal. Ezt a viselkedést az alábbi egyezési feltételek egyikének megadásával módosíthatja:

> [!NOTE]
> A felhasználó keresési lekérdezése és a pin keresési lekérdezése közötti összes összehasonlítás nem különbözteti meg a kis- és nagybetűket.

| Érték | Leírás                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Ezzel kezdődik | A pin egyezik, ha a felhasználó lekérdezési karakterlánca a pin lekérdezési karakterláncával kezdődik |
| Végződések   | A pin egyezik, ha a felhasználó lekérdezési karakterlánca a pin lekérdezési karakterláncával végződik.  |
| Contains    | A pin egyezik, ha a felhasználó lekérdezési karakterlánca tartalmazza a pin lekérdezési karakterláncát.   |


A pin egyezési feltételének módosításához kattintson a pin szerkesztési ikonjára. A **Query egyezési feltétel oszlopában** kattintson a legördülő listára, és válassza ki a használni kívánt új feltételt. Ezután kattintson a mentés ikonra a módosítás mentéséhez.

### <a name="change-the-order-of-your-pinned-sites"></a>A rögzített webhelyek sorrendjének módosítása

A kitűzők sorrendjének módosításához húzza és húzza ki őket, vagy módosítsa a rendelési számot a **Rögzített** lista **Vezérlőoszlopában** található "Szerkesztés" ikonra kattintva.

Ha több tű felel meg egyegyezési feltételnek, a Bing egyéni keresés a lista legmagasabb at fogja használni.

## <a name="view-statistics"></a>Statisztika megtekintése

Ha a megfelelő szinten fizetett elő az Egyéni keresésszolgáltatásra (lásd az [árképzési oldalakat),](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)a **Statisztika** lap hozzáadódik az éles példányokhoz. A Statisztika lapon az egyéni keresési végpontok használatának részleteit jeleníti meg, beleértve a hívásmennyiséget, a leggyakoribb lekérdezéseket, a földrajzi eloszlást, a válaszkódokat és a biztonságos keresést. A részleteket a megadott vezérlők kel szűrheti.

## <a name="usage-guidelines"></a>Használati irányelvek

- Az egyes egyéni keresési példányok esetében az **Aktív** és **a Letiltott** szeletek rangsorolási módosításai maximális száma 400-ra korlátozódik.
- Ha szeletet ad az Aktív vagy a Letiltott laphoz, az egy rangsorolási korrekciónak számít.
- A kiemelés és a lefokozása két rangsorolási korrekciónak számít.
- Az egyes egyéni keresési példányok esetében a lehető legfeljebb 200 tűt kereshet.

## <a name="next-steps"></a>További lépések

- [Egyéni keresés meghívása](./search-your-custom-view.md)
- [Üzemeltetett felhasználói felület konfigurálása](./hosted-ui.md)
- [Díszítő jelölők használata szövegkiemeléshez](../bing-web-search/hit-highlighting.md)
- [Weboldalak lapozása](./page-webpages.md)
