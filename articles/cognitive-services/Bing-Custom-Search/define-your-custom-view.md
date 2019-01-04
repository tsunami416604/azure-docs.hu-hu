---
title: A Bing egyéni keresés felhasználói beállítása |} A Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Ismerteti, hogyan hozhat létre a hely és a függőleges keresési szolgáltatások
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: aahi
ms.openlocfilehash: ac5ba80740c47dd71a30bb20aab4a54829eac822
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53597800"
---
# <a name="configure-your-bing-custom-search-experience"></a>A Bing egyéni keresés felhasználói beállítása

Egy egyéni keresési példány lehetővé teszi a keresési funkciók csak tartalmát, hogy a felhasználók számára is megfelelő kódolási sebesség kiválasztását. Helyett a webes keresés, a Bing csak szeleteit a weben az Ön által megszabott keres. Az egyéni kívánalmaknak megfelelő webes keresés létrehozásához használja a Bing Custom Search [portált](https://customsearch.ai).

A portálon teszi lehetővé, amely meghatározza a szeletek a webes keresés példányt hoz létre: tartományok, mindegyik lapot tünteti, és a weblapok, amelyek azt szeretné, hogy a Bing keresés, és azokkal, amelyek nem szeretné, hogy a keresés. A portálon is tud javaslatot tartalmakat, amelyeket érdemes felvenni.

Ha a webes webterületek definiálása, használja a következő:

| Szelet neve | Leírás                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domain     | Tartomány szelet magában foglalja az internetes tartományban található összes tartalmat. Például: `www.microsoft.com`. Felsorolhatja `www.` hatására a Bing, a tartomány altartományokra is kereshet. Például, ha a megadott `microsoft.com`, a Bing is eredményét adja vissza `support.microsoft.com` vagy `technet.microsoft.com`. |
| Melléklap    | Melléklap szelet magában foglalja a Melléklap és elérési utak alatta található összes tartalmat. Legfeljebb két mindegyik lapot tünteti előfordulhat, hogy adja meg az elérési utat. Például: `www.microsoft.com/en-us/windows/`                                                                                                                       |
| Weblap    | A weblap szelet belefoglalhatja egyéni keresés csak weblapról. Azt igény szerint megadhatja, hogy mindegyik lapot tünteti tartalmazza.                                                                                                                                                                                  |

> [!IMPORTANT]
> Minden tartomány, mindegyik lapot tünteti és, amelyek a megadott nyilvános és a Bing által indexelt kell lennie. Ha fel szeretne venni a Keresés nyilvános helyen a saját, és a Bing nem indexelt azt, tekintse meg a Bing [gazdáját dokumentáció](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) részletes információ a Bing indexelés céljából. Emellett a részleteinek beolvasása a bejárt hely frissítéséhez, ha az index nem naprakész a Bing a gazdáját dokumentációjában talál.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>A Web ad hozzá az egyéni keresési példány

Az egyéni keresőpéldányok létrehozásakor megadhatja a webes szeleteit: tartományok, mindegyik lapot tünteti és weblapok, kívánt tartalmazza vagy blokkolva van a keresési eredmények közül. 

Ha tudja, hogy az egyéni keresőpéldányok szerepeltetni szeretne a szeletek, vegye fel őket a példány **aktív** listája. 

Ha nem biztos abban, hogy mely szeletek tartalmazza, elküldheti a Bing keresési lekérdezések a **előzetes** ablaktáblán, és válassza ki a kívánt szeleteket. Ehhez tegye a következőket: 

1. az előnézeti ablaktáblában láthatja a legördülő listából válassza ki a "Bing", és adja meg a keresési lekérdezést

2. Kattintson a **Hozzáadás hely** mellett fel szeretne venni az eredményt. Ezután kattintson az OK gombra.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Az aktív és a letiltott listákkal keresési élmény testreszabása 

Az aktív és a blokkolt szeletek hosszabb listájának kattintva érheti a **aktív** és **letiltott** az egyéni keresőpéldányok lapjait. Az aktív listához hozzáadott szeletek szerepelni fog az egyéni keresés. Letiltott szeletek nem fog keresni, és a keresési eredmények nem jelennek meg.

Azt szeretné, hogy a Keresés a Bing webes szeleteit megadásához kattintson a **aktív** lapra, és adjon hozzá egy vagy több URL-címeket. URL-címek módosításához vagy eltávolításához használja a beállítások alatt a **vezérlők** oszlop. 

Amikor hozzáadása az URL-címek használatával a **Active** lista is hozzáadhat egyetlen URL-címek, vagy több URL-címeket egyszerre, ha feltölt egy szöveges fájlt, a Feltöltés ikonra.

![A Bing Custom Search aktív lapon](media/file-upload-icon.png)

Töltsön fel egy fájlt, hozzon létre egy szövegfájlt, és adja meg a tartományra, Melléklap vagy külön sorba weblap. A fájl elutasításra kerül, ha nincs megfelelően formázva.

> [!NOTE]
> * Csak feltölthet egy fájlt a **aktív** listája. A szeletek hozzáadása nem használható a **letiltott** listája.  
> * Ha a **letiltott** lista tartalmaz egy tartományt, Melléklap vagy weblap, amelyen a feltöltendő fájl megadott azt eltávolítjuk a **letiltott** listában, és hozzá a **Active** lista .
> * A feltöltendő fájl ismétlődő bejegyzéseket a Bing Custom Search figyelmen kívül. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Webhely telepheti a keresési funkciót

Gyakori való hozzáadása után a **aktív** listájában, a Bing Custom Search portál webhelyére és Melléklap javaslatok a lap alján hoz létre. Ezek a szeletet, amelyet a Bing Custom Search fenyegetésként észlel, érdemes felvenni. Kattintson a **frissítése** frissített javaslatokat beolvasni az egyéni keresőpéldányok beállításainak frissítése után. Ez a szakasz csak akkor látható, ha a javaslat érhető el.

## <a name="search-for-images-and-videos"></a>Képek és videók keresése

Kereshet képek és videók hasonlóan a webtartalomhoz való használatával a [Bing Custom-Image Search API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference) vagy a [Bing egyéni Videókeresési API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference). Ezeket az eredményeket megjelenítheti a [felhasználói felület üzemeltetett](hosted-ui.md), vagy az API-k. 

Egyéni-API-k hasonlóak [a Bing Képkeresés](../Bing-Image-Search/overview.md) és [Bing Video Search](../Bing-Video-Search/search-the-web.md) API-k, de a teljes webes keresés, és nem igénylik a `customConfig` lekérdezési paraméter. További információ ezen dokumentáció készletek használata a képek és videók jelenik meg. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Az előnézeti ablaktáblában a keresés példány tesztelése

Tesztelheti a keresési példány használatával az előnézeti ablaktáblában láthatja a portál jobb oldalán a keresési lekérdezések elküldéséhez, és tekintse meg az eredményeket. 

1. A keresőmező alatt válassza ki a **saját példány**. Összehasonlíthatja az eredményeket a keresési funkciót, a Bing, kiválasztásával **Bing**. 
2. Válassza ki a biztonságos keresés szűrési és kereséséhez, amelyek piaci (lásd: [lekérdezési paraméterek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)).
3. Adjon meg egy lekérdezést, és nyomja le adja meg, vagy kattintson a keresés ikonra az aktuális konfiguráció eredményeinek megtekintéséhez. Módosíthatja a keresési típust kattintva elvégezheti **webes**, **kép**, vagy **videó** megfelelő eredmények eléréséhez. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>A keresési eredmények rangsor módosítása

A portál lehetővé teszi a keresési rangsorolás, a tartalom tartományok, mindegyik lapot tünteti és weblapok történő beállítása. Keresési lekérdezés küldése a betekintő ablaktáblában, után minden keresési eredmény módosítások is hozzá listáját tartalmazza:  

|            |                                                                                                                                                                      |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Blokk      | A tartomány, Melléklap vagy weblap áthelyezi a letiltott listába. Bing ki fogja zárni a tartalom a kiválasztott hely megjelenjenek a keresési eredmények között.                    |
| Kiemelés      | Felgyorsíthatók a tartalom a tartomány vagy Melléklap magasabb a keresési eredmények között lehet.                                                                                        |
| Lefokozása     | Értéksorrá minősíti vissza a tartományt vagy a keresési eredmények alsó Melléklap tartalmat. Akkor válassza ki, hogy fokozza le a tartalmat a tartományból vagy subpage, hogy a képernyőn látható weblapon tartozik. |
| Rögzítse a tetejére | A tartomány, Melléklap vagy a weblap helyezi a **Pinned** listája. Ez kényszeríti a weblap jelenik meg, mint a felső keresési eredmény számára egy adott keresési lekérdezésnek.                   |

Rangsor módosítása nem áll rendelkezésre a kép vagy videó kereséseket.

### <a name="boosting-and-demoting-search-results"></a>Kiemelési és a keresési eredmények lefokozása

Super növelése, növelheti, vagy bármilyen tartomány lefokozása vagy is a subpage a **aktív** listája. Alapértelmezés szerint az összes szelet rangsorolási módosítás nélkül kerülnek. A webes, hogy a rendszer Super súlyozott vagy Boosted szeletek újabb sorrendje a keresési eredmények között (a felügyelői boost rangsorolási boost nagyobb). A keresési eredmények között, hogy a rendszer visszalép elemek rangsora alacsonyabb.

Super növelése, növelése vagy elemek le használatával a **prioritás beállítása** vezérlőket az a **aktív** listájában, vagy a használatával a Boost és lefokozása szabályozza az előnézeti ablaktáblában láthatja. A szolgáltatás a szelet ad hozzá az aktív listához, és beállítja a rangsorolás ennek megfelelően.

> [!NOTE] 
> Kiemelési és a tartományokat, és mindegyik lapot tünteti lefokozása Bing Custom Search segítségével határozza meg, a keresési eredmények sorrendje számos módszer egyike. Eredő rangsor módosítása miatt a különböző webes tartalom alapján a rangsorolás befolyásoló egyéb tényezők, eltérőek lehetnek. Az előnézeti ablaktáblában láthatja segítségével tesztelheti a keresési eredmények rangsorát módosításával hatásait. 

Super növelése jelentősen növelheti és lefokozása nem érhetők el a lemezkép és a videó kereséseket.

## <a name="pin-slices-to-the-top-of-search-results"></a>PIN-kód szeletek tetején a keresési eredmények

A portálon is lehetővé teszi használatával adott keresési kifejezéseket, a keresési eredmények tetejére URL-címek rögzítése a **Pinned** fülre. Adjon meg egy URL-cím és a lekérdezés, amely a legjobb eredmény jelenik meg a weblap megadása. Vegye figyelembe, hogy egy weblap keresési lekérdezésenként legfeljebb rögzíthet, és csak indexelt weblapjait keresések megjelennek. Rögzítés eredmények nem áll rendelkezésre a kép vagy videó keresések.

Két módon is rögzíthet egy weblap, a lista tetejére:

* Az a **Pinned** lapra, adja meg a felső, és a megfelelő lekérdezés rögzítése a weblap URL-CÍMÉT.

* Az a **előzetes** panelen adjon meg egy keresési lekérdezést, és kattintson a Keresés gombra. Keresse meg a PIN-kód, a lekérdezés, és kattintson a kívánt weblap **felső PIN-kód**. a weblap és a lekérdezés hozzáadódik a **Pinned** listája.

### <a name="specify-the-pins-match-condition"></a>Adja meg a PIN-kód egyezési feltételei

Alapértelmezés szerint weblapok csak rögzítve tetején a keresési eredmények egy felhasználói lekérdezési karakterlánc pontosan megegyezik egy szerepel a **Pinned** listája. A következő egyezési feltételei megadásával módosíthatja ezt a viselkedést:

> [!NOTE]
> Között a felhasználó keresési lekérdezést, és a PIN-kód keresési lekérdezés minden összehasonlítások megkülönböztetik a kis-és nagybetűket.

| Érték | Leírás                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Kezdete | A PIN-kód egyezés, ha a lekérdezési karakterlánc a felhasználó a PIN-kód lekérdezési karakterlánccal kezdődik |
| Vége   | A PIN-kód egyezés esetén a felhasználói lekérdezési karakterláncot a PIN-kód lekérdezési karakterlánccal végződik.  |
| Contains    | A PIN-kód egyezés esetén a felhasználói lekérdezési karakterláncot a PIN-kód lekérdezési karakterláncot tartalmaz.   |


A PIN-kódot az egyezési feltétellel módosításához kattintson a Szerkesztés ikonra a PIN-kódot. Az a **lekérdezés az egyezési feltétellel** oszlopot, kattintson a legördülő listára, és válassza ki az új feltétel használatához. Ezután kattintson a Mentés ikonra a módosítás mentéséhez.

### <a name="change-the-order-of-your-pinned-sites"></a>A rögzített webhelyek sorrendjének módosítása

A PIN-kódok sorrendjének módosításához is, és húzza a őket, vagy sorszámuk szerkesztéséhez kattintson a "edit" ikonra a **vezérlők** oszlopa a **Pinned** listája.

Ha több PIN-kód felel meg az egyeztetési feltételt, a Bing Custom Search fogja használni a lista legnagyobb.

## <a name="view-statistics"></a>Statisztikák megtekintése

Ha egyéni keresés megfelelő szinten előfizetett (lásd a [díjszabási lapjait](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), amely egy **statisztika** lapra kerül az éles környezetben üzemelő példányok. A Statisztika lapon részletesebb információt nyújt az egyéni keresés végpontok használata, beleértve a hívások mennyisége, a leggyakoribb lekérdezések, a földrajzi eloszlása, a válaszkódot és a biztonságos keresés. Ismerteti a megadott vezérlők használatával szűrheti.

## <a name="usage-guidelines"></a>Használati útmutató

- Egyéni keresési példányonként maximális számát, előfordulhat, hogy győződjön meg arról, hogy ennek a területnek korrekciók **Active** és **letiltott** szeletek 400 korlátozódik.
- Egy rangsorolást korrekciós számít az aktív vagy letiltott lapokat ad hozzá egy szeletet.
- Kiemelési és lefokozása száma, két rangsorolási módosítását.
- Egyéni keresési példányonként PIN-kódok, hogy legfeljebb 200 korlátozva.

## <a name="next-steps"></a>További lépések

- [Egyéni keresés meghívása](./search-your-custom-view.md)
- [Üzemeltetett felhasználói felület konfigurálása](./hosted-ui.md)
- [Díszítő jelölők használata szövegkiemeléshez](./hit-highlighting.md)
- [Weboldalak lapozása](./page-webpages.md)
