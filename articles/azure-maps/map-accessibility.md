---
title: Akadálymentes térképalkalmazás létrehozása az Azure Maps segítségével | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan hozhat létre olyan alkalmazást, amely kisegítő lehetőségeket kínál a Microsoft Azure Maps használatával.
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: 5fa9e159fa0ac76ce8c585936059fb7f3151c7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473319"
---
# <a name="building-an-accessible-application"></a>Akadálymentes alkalmazás létrehozása

Az internethasználók több mint 20%-ának van szüksége hozzáférhető webes alkalmazásokra. Ezért fontos, hogy az alkalmazás úgy legyen megtervezve, hogy bármely felhasználó könnyen használhassa. Ahelyett, hogy a hozzáférhetőségre úgy gondolna, mint egy feladathalmazra, gondoljon rá az általános felhasználói élmény részeként. Minél könnyebben elérhető az alkalmazás, annál többen tudják használni. 

Ha gazdag interaktív tartalomról, például térképről van szó, a kisegítő lehetőségeknéhány gyakori szempontja a következő:
- Támogatja a képernyőolvasót azoknak a felhasználóknak, akiknek nehézséget okoz a webes alkalmazás.
- Többféle módszerrel rendelkezik a webes alkalmazással való interakcióra és navigálásra, például az egérrel, az érintéssel és a billentyűzettel.
- Győződjön meg arról, hogy a színkontraszt olyan, hogy a színek nem keverednek össze, és nehezen különböztethetők meg egymástól. 

Az Azure Maps Web SDK előre elkészített számos kisegítő lehetőséget kínál, például:
- A képernyőolvasó leírása, amikor a térkép mozog, és amikor a felhasználó egy vezérlőre vagy felugró ablakra összpontosít.
- Az egér, az érintés és a billentyűzet támogatása.
- Akadálymentes színkontraszt az útitérkép stílusában.

A Microsoft összes termékének teljes hozzáférhetőségi megfelelőségi adatai [itt](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/)találhatók. Keressen rá az "Azure Maps web" kifejezésre, és keresse meg a dokumentumot kifejezetten az Azure Maps Web SDK-hoz. 

## <a name="navigating-the-map"></a>Navigálás a térképen

A térkép nagyításának, pásztázásának, elforgatásának és felverésének számos különböző módja van. Az alábbi adatok a térképen való navigálás különböző módjait ismertetik.

**A térkép nagyítása**

- Az egérrel, kattintson duplán a térképre, hogy nagyítsa ki egy szinten.
- Az egérrel görgessen a keréknagyítsa a térképet.
- Érintőképernyős érintőképernyővel érintse meg a térképet két ujjal, és csippentsen össze a nagyításhoz vagy az ujjak szétterítéséhez.
- Érintőképernyő használatával koppintson duplán a térképre egy szint nagyításához.
- Ha a térkép fókuszált,`+`használja a Plusz`=`jel ( ) vagy az Egyenlőjel ( ) egy szint nagyításához.
- Ha a térkép fókuszált, használja a`-`Mínuszjelet,`_`a Kötőjelet ( ), vagy az Aláhúzást () egy szint kicsinyítéséhez.
- A nagyításvezérlő egérrel történő használata, az érintéses vagy billentyűzetfül/beírás billentyűk használata.
- Tartsa nyomva `Shift` a gombot, és nyomja le a bal egérgombot a térképen, és húzással rajzoljon ki egy területet a térkép nagyításához.

**Pásztázás a térképen**

- Egérrel nyomja le a bal egérgombot a térképen, és húzza bármilyen irányba.
- Érintőképernyő használatával érintse meg a térképet, és húzza bármelyik irányba.
- Ha a térkép fókuszált, a nyílbillentyűkkel mozgassa a térképet.

**A térkép elforgatása**

- Egérrel nyomja le a jobb egérgombbal a térképen, és húzza balra vagy jobbra. 
- Érintőképernyő stapintása közben két ujjal megérintheti a térképet, és elforgathatja.
- Ha a térkép fókuszált, használja a shift gombot és a bal vagy jobb nyílbillentyűket.
- Az elforgatás vezérlő használata egérrel, érintéssel vagy billentyűzettel a gombok at.

**A térkép feldobása**

- Az egérrel nyomja le a jobb egérgombbal a térképen, és húzza felfelé vagy lefelé. 
- Érintőképernyő stapintása segítségével érintse meg a térképet két ujjal, és húzza őket felfelé vagy lefelé.
- Ha a térkép fókuszált, használja a shift gombot, valamint a fel vagy le nyílbillentyűket. 
- A hangmagasság-vezérlő használata egérrel, érintéssel vagy billentyűzettel a billentyűk beírásakor.

## <a name="change-the-map-style"></a>A térképstílus módosítása

Nem minden fejlesztő szeretné, hogy az összes lehetséges térképstílus elérhető legyen az alkalmazásában. Ha a fejlesztő megjeleníti a térkép stílusválasztó vezérlőjét, akkor a felhasználó módosíthatja a térkép stílusát az egérrel, egy érintéssel vagy a billentyűzettel a lap vagy az enter billentyű segítségével. A fejlesztő megadhatja, hogy mely térképstílusokat kívánja elérhetővé tenni a térképstílus-választó vezérlőben. Emellett a fejlesztő programozott módon beállíthatja és módosíthatja a térképstílusát.

**Kontrasztos használata**

- A térképvezérlő betöltésekor ellenőrzi, hogy engedélyezve van-e a kontrasztos megjelenítés, és a böngésző támogatja-e.
- A térképvezérlő nem figyeli a készülék nagy kontrasztú üzemmódját. Ha az eszköz mód megváltozik, a térkép nem fog. Így a felhasználónak újra kell töltenie a térképet az oldal frissítésével.
- Ha kontrasztot észlel, a térképstílus automatikusan nagy kontrasztra vált, és az összes beépített vezérlő kontrasztos stílust használ. A ZoomControl, a PitchControl, a CompassControl, a StyleControl és más beépített vezérlők például kontrasztos stílust fognak használni.
- Kétféle nagy kontrasztú, világos és sötét. Ha a térkép vezérlők észlelik a nagy kontraszt típusát, akkor a térkép viselkedése ennek megfelelően módosul. Ha világos, akkor a grayscale_light térképstílus betöltődik. Ha a típus nem észlelhető vagy sötét, akkor a high_contrast_dark stílus betöltődik.
- Ha egyéni vezérlőket hoz létre, érdemes tudni, hogy a beépített vezérlők kontrasztos stílust használnak-e. A fejlesztők adhat egy css osztály a térképen konténer div ellenőrizni. A css osztályok, hogy `high-contrast-dark` lenne `high-contrast-light`hozzá, és . A JavaScript használatával történő ellenőrzéshez használja a következőket:

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

vagy, használja:

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>Billentyűparancsok

A térkép számos beépített billentyűparancsmal rendelkezik, amelyek megkönnyítik a térkép használatát. Ezek a billentyűparancsok akkor működnek, ha a térkép fókuszban van.

| Kulcs      | Műveletek                            |
|----------|-----------------------------------|
| `Tab` | Navigáljon a vezérlők és a felugró ablakok a térképen. |
| `ESC` | A fókusz áthelyezése a térkép bármely eleméből a legfelső szintű térképelemre. |
| `Ctrl` + `Shift` + `D` | A képernyőolvasó részletességi szintjének be- és be- és kiváltása.  |
| Balra nyíl | Pásztázás a térképbal 100 pixel |
| Jobbra nyíl | A térkép pásztázása jobbra 100 képpont |
| Le nyílbillentyű | A térkép pásztázása 100 képponttal |
| Fel felé mutató nyílbillentyű | A térkép pásztázása 100 képponttal |
| `Shift`+ felfelé mutató nyíl | A térkép hangmagasságának növelése 10 fokkal |
| `Shift`+ lefelé mutató nyíl | A térkép hangmagasságának csökkentése 10 fokkal |
| `Shift`+ jobbra nyíl | A térkép elforgatása 15 fokkal az óramutató járásával megegyező irányban |
| `Shift`+ balra nyíl | A térkép elforgatása 15 fokkal az óramutató járásával ellentétesen |
| Pluszjel`+`( <sup>*</sup>) vagy`=`Egyenlőségjel ( ) | Nagyítás |
| Mínuszjel, Kötőjel`-`( <sup>*</sup>),`_`vagy Aláhúzás ( ) | Kicsinyítés | 
| `Shift`+ egér húzása a térképen, hogy dolgozzon terület | Terület nagyítása |

<sup>*</sup>Ezek a billentyűparancsok általában ugyanazt a billentyűt osztják a billentyűzeten. Ezek a billentyűparancsok a felhasználói élmény javítása érdekében kerültek hozzáadásra. Az sem számít, hogy a felhasználó használja-e a shift billentyűt, vagy sem ezekhez a billentyűparancsokhoz.

## <a name="screen-reader-support"></a>A Képernyőolvasó támogatása

A felhasználók a billentyűzet segítségével navigálhatnak a térképen. Ha a képernyőolvasó fut, a térkép értesíti a felhasználót az állapotának változásairól. Például a felhasználók értesítést kapnak a térkép változásairól, amikor a térképet pásztázott vagy nagyított. Alapértelmezés szerint a térkép egyszerűsített leírásokat biztosít, amelyek kizárják a térkép nagyítási szintjét és koordinátáit. A felhasználó a billentyűzet rövid vágásával `Ctrl`  +  `Shift`  +  `D`válthat a leírások részletességi szintjére.

Az alaptérképen elhelyezett további információknak megfelelő szöveges információkkal kell rendelkezniük a képernyőolvasó-felhasználók számára. Ügyeljen arra, hogy adjunk [hozzáférhető rich internetes alkalmazások (ARIA)](https://www.w3.org/WAI/standards-guidelines/aria/), alt, és a cím attribútumok, ahol szükséges. 

## <a name="make-popups-keyboard-accessible"></a>Az előugró ablakok billentyűzetének akadálymentessé tétele

A jelölőt vagy szimbólumot gyakran használják egy hely ábrázolására a térképen. A helyről szóló további információk általában egy felugró ablakban jelennek meg, amikor a felhasználó interakcióba lép a jelölővel. A legtöbb alkalmazásban előugró ablakok jelennek meg, amikor a felhasználó rákattint vagy koppint egy jelölőre. A kattintásra és koppintásra való kattintáshoz azonban a felhasználónak egér és érintőképernyő használatát igényli. Egy jó gyakorlat, hogy a felugró ablakok elérhetővé, ha a billentyűzet. Ez a funkció úgy érhető el, hogy létrehoz egy felugró ablakot az egyes adatpontokkal, és hozzáadja a térképhez. 

A következő példa egy szimbólumréteg segítségével tölti be a térképen az érdeklődési pontokat, és minden érdekes ponthoz hozzáad egy felugró ablakot a térképhez. Az egyes felugró ablakokra mutató hivatkozások az egyes adatpont-tulajdonságokban tárolódnak. Azt is belehet olvasni egy marker, például ha egy jelölő kattintott. Amikor a térképre összpontosít, a tab billentyű lenyomásával a felhasználó végigléphet a térkép minden felugró ablakán.

<br/>

<iframe height='500' scrolling='no' title='Akadálymentes alkalmazás sábá' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd: A Toll <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Elérhetőalkalmazás</a> létrehozása<a href='https://codepen.io/azuremaps'>@azuremaps</a>az Azure Maps által ( ) a CodePen webhelyen című <a href='https://codepen.io'>témakörben.</a> </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>További kisegítő lehetőségekkel kapcsolatos tippek

Íme néhány további tipp, hogy a web-térképészeti alkalmazás könnyebben hozzáférhető.

- Ha sok interaktív pontadatot jelenít meg a térképen, fontolja meg a zsúfoltság csökkentését és a fürtözés használatát. 
- A szöveg/szimbólumok és a háttérszínek közötti színkontraszt arány ának biztosítása 4,5:1 vagy több.
- Tartsa a képernyőolvasó (ARIA, alt, és cím attribútumok) üzenetek rövid, leíró, és értelmes. Kerülje a felesleges zsargont és a betűszavakat.
- Próbálja meg optimalizálni a képernyőolvasónak küldött üzeneteket, hogy a felhasználó számára könnyen megemészthető rövid, értelmes információkat biztosítson. Ha például a képernyőolvasót magas frekvencián szeretné frissíteni, például amikor a térkép mozog, érdemes lehet a következő pontokat ellátni:
    - Várjon, amíg a térkép befejeződik a képernyőolvasó frissítéséhez.
    - Néhány másodpercenként egyszer fojtsa meg a frissítéseket. 
    - Az üzeneteket logikus módon kombinálhatja. 
- Ne a szín legyen az információközlés egyetlen eszköze. A szín kiegészítéséhez vagy cseréjéhez használjon szöveget, ikonokat vagy mintázatot. Néhány szempont:
    - Ha buborékréteget használ az adatpontok relatív értékének megjelenítésére, fontolja meg az egyes buborékok sugarának méretezését, a buborék vagy mindkettő színezését. 
    - Érdemes lehet különböző képkategóriákhoz , például háromszögekhez, csillagokhoz és négyzetekhez különböző ikonokat használó szimbólumréteget használni. A szimbólumréteg támogatja az ikon méretének méretezését is. Szöveges címke is megjeleníthető.
    - Vonaladatok megjelenítésekor a szélesség a súly vagy a méret jelzésére használható. A vonalsorok különböző kategóriáit ábrázoló szaggatott tömbminta használható. A szimbólumréteg egy vonallal kombinálva is használható az ikonok átfedéséhez a vonal mentén. A nyílikon használata a vonal folyatásának vagy irányának megjelenítéséhez hasznos.
    - Sokszögadatok megjelenítésekor a szín alternatívájaként mintázat, például csíkok használhatók. 
- Egyes vizualizációk, például a hőtérképek, a csemperétegek és a képrétegek nem érhetők el a látássérült felhasználók számára. Néhány szempont:
    - A képernyőolvasó írja le, hogy a réteg mit jelenít meg, amikor hozzáadja a térképhez. Ha például megjelenik egy időjárási radarcsempe-réteg, akkor a képernyőolvasó mondja ki a "Időjárási radar adatok elvan takarja a térképen" parancsot.
- Korlátozza az egérmutatót igénylő funkciók mennyiségét. Ezek a funkciók nem érhetők el azon felhasználók számára, akik billentyűzetet vagy érintőképernyős eszközt használnak az alkalmazás sal való interakcióra. Ne feledje, hogy még mindig jó gyakorlat, hogy az interaktív tartalmakhoz, például a kattintható ikonokhoz, hivatkozásokhoz és gombokhoz rámutathat egy rámutatási stílusra.
- Próbáljon meg az alkalmazásban navigálni a billentyűzet használatával. Győződjön meg arról, hogy a lapok rendezése logikus.
- Billentyűparancsok létrehozásakor próbálja meg két billentyűre vagy annál kevesebbre korlátozni. 

## <a name="next-steps"></a>További lépések

További információ a webes SDK-modulok kisegítő lehetőségeiről.

> [!div class="nextstepaction"]
> [A rajzeszközök kisegítő lehetőségei](drawing-tools-interactions-keyboard-shortcuts.md)

További információ a microsoftos tanulási szolgáltatásakadálymentes alkalmazások fejlesztéséről:

> [!div class="nextstepaction"]
> [Kisegítő lehetőségek a műveletben Digitális jelvény tanulási útvonala](https://ready.azurewebsites.net/learning/track/2940)

Tekintse meg az alábbi hasznos kisegítő lehetőségeket szolgáló eszközöket:
> [!div class="nextstepaction"]
> [Akadálymentes alkalmazások fejlesztése](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [WAI-ARIA áttekintés](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Webes akadálymentesítést értékelő eszköz (WAVE)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [WebAim színkontraszt-ellenőrző](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [Nincs kávélátás-szimulátor](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
