---
title: Elérhető alkalmazás készítése Azure Mapsokkal | Microsoft Docs
description: Elérhető alkalmazás létrehozása Azure Maps használatával
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: 4d997bcb5bbbb66a06bea998577f8163910afce8
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561303"
---
# <a name="building-an-accessible-application"></a>Akadálymentes alkalmazás létrehozása

Az internetes felhasználók 20%-ának kihasználása az elérhető webalkalmazásokhoz szükséges. Ezért fontos meggyőződni arról, hogy az alkalmazás úgy van kialakítva, hogy bármely felhasználó könnyen használhassa azt. Ahelyett, hogy a kisegítő lehetőségeket a teljes felhasználói élmény részeként kellene elvégeznie. Minél könnyebben elérhető az alkalmazás, annál több ember használhatja azt. 

Ha a gazdag interaktív tartalmakhoz, például a térképhez is tartozik, a következő gyakori kisegítő lehetőségek érhetők el:
- Támogassa a képernyőolvasót olyan felhasználók számára, akik nehezen látják a webalkalmazást.
- Több módszer is rendelkezésre áll a webalkalmazások, például az egér, a Touch és a billentyűzet használatával való interakcióhoz és navigáláshoz.
- Ügyeljen arra, hogy a színek ne legyenek összekeverve, és ne legyenek megkülönböztetve egymástól. 

A Azure Maps web SDK a következő számos kisegítő lehetőséggel rendelkezik előre összeépítve:
- A képernyőolvasók leírása a Térkép mozgatásakor és amikor a felhasználó egy vezérlőelemre vagy előugró ablakra fókuszál.
- Egér, érintés és billentyűzet támogatása.
- Elérhető színkontrasztos támogatás az úthálózati Térkép stílusaként.

Az összes Microsoft-termékre vonatkozó teljes körű hozzáférhetőségi részletek [itt](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/)találhatók. Keressen rá a "Azure Maps web" kifejezésre, hogy megkeresse a Azure Maps web SDK-hoz tartozó dokumentumot. 

## <a name="navigating-the-map"></a>Navigálás a térképen
A Térkép több különböző módon nagyítható, megpárolt, elforgatható és felhelyezhető. A következő részletek a Térkép különböző módjaira mutatnak.

**A Térkép nagyítása**
- Egy egér használatával kattintson duplán a térképre egy szint nagyításához.
- Egér használatával a térkép nagyításához görgessen a görgőre.
- Érintőképernyőn keresztül a térképre koppintva két ujjal és csípjük össze a nagyítást, illetve az ujjak kinagyítását, illetve a nagyítást egymás mellett.
- Egy érintőképernyő használatával dupla koppintással nagyíthatja a térképet egy szint nagyításához.
- A térképre koncentrálva használja a pluszjelet (`+`) vagy az * egyenlőségjel (`=`) lehetőséget a nagyításhoz egy szinten.
- A térképre koncentrálva a mínuszjelet, kötőjelet (`-`) vagy aláhúzást (`_`) használhatja egy szint kinagyításához.
- A nagyítás vezérlőelemet egérrel, érintéssel vagy billentyűzettel vagy a kulcsok beírásával használhatja.
- Nyomja le és tartsa nyomva a `Shift` gombot, és nyomja le a bal oldali egérgombot a térképen, és húzza a diagramot a térkép nagyításához.

**A Térkép pásztázása**
- Egér használatával a térképen a bal egérgombot lenyomva tartva bármely irányt áthúzhat.
- Érintse meg a térképet, és húzza a kívánt irányba.
- A térképre koncentrálva a nyílbillentyűk használatával helyezheti át a térképet.

**A Térkép elforgatása**
- Egér használatával nyomja le a lefelé a jobb gombbal a térképen, és húzza balra vagy jobbra. 
- Érintse meg a Térkép két ujjal való használatát, és forgassa el.
- A Térkép fókuszban használja a SHIFT billentyűt, a bal vagy a jobb nyílbillentyűket.
- A forgatás vezérlőelem használata egérrel, érintéssel vagy billentyűzettel, illetve kulcsok bevitele.

**A Térkép feldobása**
- Az egér használatával nyomja le a legördülő menüben a jobb gombbal a térképen, és húzza felfelé vagy lefelé. 
- Érintse meg a térképet két ujjal, és húzza őket egymáshoz.
- A térképre koncentrálva a SHIFT billentyűt, valamint a fel vagy a le nyílbillentyűt használja. 
- A Pitch vezérlő használata egérrel, érintéssel vagy billentyűzettel, illetve kulcsok bevitele.

**Térkép stílusának módosítása** Nem minden fejlesztő szeretné, hogy az összes lehetséges Térkép stílusa elérhető legyen az alkalmazásában. A fejlesztő programozott módon állíthatja be a Térkép stílusát, és tetszés szerint módosíthatja azt. Ha a fejlesztő megjeleníti a Térkép stílusa választó vezérlőelemet, a felhasználó az egérrel, érintéssel vagy a billentyűzettel módosíthatja a Térkép stílusát a TAB/ENTER kulcsok használatával. A fejlesztő megadhatja, hogy mely térképi stílusokat szeretné elérhetővé tenni a Térkép stílusa választó vezérlőelemben. 

## <a name="keyboard-shortcuts"></a>Billentyűparancsok

A Térkép számos billentyűparancsot tartalmaz, amelyek megkönnyítik a Térkép használatát. Ezek a billentyűparancsok akkor működnek, ha a Térkép fókuszban van.

| Jelmagyarázat      | Műveletek                            |
|----------|-----------------------------------|
| `Tab` | Navigáljon a térképen a vezérlők és az előugró ablakok között. |
| `ESC` | Mozgassa a fókuszt a Térkép bármely eleméről a legfelső szintű Térkép elemre. |
| `Ctrl` + `Shift` + `D` | Képernyő-olvasó részletességi szintjének bekapcsolása.  |
| Balra nyíl | A Térkép pásztázása balra 100 képpont |
| Jobbra nyíl | A Térkép jobb 100 képpontban való pásztázása |
| Lefelé nyíl gomb | A Térkép pásztázása lefelé 100 képpont |
| Felfelé nyíl gomb | A Térkép pásztázása 100 képpont |
| `Shift` + felfelé mutató nyíl | A Térkép szurok növelésének mértéke 10 fokkal |
| `Shift` + lefelé mutató nyíl | A térképi szurok csökkentése 10 fokkal |
| `Shift` + jobb nyíl | A Térkép 15 fokos elforgatása jobbra |
| `Shift` + balra nyíl | A Térkép 15 fokos elforgatása balra |
| Pluszjel (`+`) vagy <sup>*</sup>egyenlőségjel (`=`) | Nagyítás |
| Mínusz jel, kötőjel (`-`) vagy <sup>*</sup>aláhúzás (`_`) | Kicsinyítés | 
| `Shift` + egérrel húzza a térképet a terület rajzolásához | Terület nagyítása |

<sup>*</sup> Ezek a billentyűparancsok általában ugyanazt a kulcsot használják a billentyűzeten. Ezek a felhasználói élmény javításához lettek hozzáadva, így nem számít, hogy a felhasználó használja-e a SHIFT billentyűt, vagy sem ezekhez a parancsikonokhoz.

## <a name="screen-reader-support"></a>Képernyőolvasó-támogatás

A felhasználók a billentyűzettel is megkereshetik a térképet. Ha egy képernyőolvasó fut, a Térkép értesíti a felhasználót, hogy az állapota megváltozik. Például a felhasználók értesítést kapnak a Térkép változásairól, amikor a Térkép megpárol vagy kicsinyítve van. Alapértelmezés szerint a Térkép egyszerűsített leírásokat tartalmaz, amelyek kizárja a Térkép középpontjának nagyítási szintjét és koordinátáit. A felhasználók a leírások részletességi szintjét a billentyűzet rövid kivágási `Ctrl` + `Shift` + `D`használatával válthatják ki.

Az alaptérképre helyezett további információknak megfelelő szöveges információval kell rendelkezniük a képernyőolvasó felhasználói számára. Ha szükséges, ügyeljen arra, hogy az [elérhető Rich Internet-alkalmazásokat (ARIA)](https://www.w3.org/WAI/standards-guidelines/aria/), az ALT és a title attribútumokat adja hozzá. 

## <a name="make-popups-keyboard-accessible"></a>Felugró ablakok billentyűzetének elérhetővé tétele

Egy jelölőt vagy szimbólumot gyakran használnak a térképen egy hely ábrázolására. A hellyel kapcsolatos további információ általában egy előugró ablakban jelenik meg, amikor a felhasználó a jelölővel kommunikál. A legtöbb alkalmazás előugró ablaka akkor jelenik meg, ha a felhasználó egy jelölőre kattint vagy koppint, azonban a felhasználónak egeret vagy érintőképernyőt kell használnia. Jó megoldás, ha billentyűzet használatakor elérhetővé teszi a felugró ablakokat. Ez úgy érhető el, hogy létrehoz egy előugró ablakokat az egyes adatpontokhoz, és hozzáadja azt a térképhez. 

A következő példa egy szimbólum réteget használ a Térkép érdeklődési pontjainak betöltésére, és felugró ablakokat helyez el a térképhez minden egyes hasznos ponthoz. Az egyes előugró ablakokra mutató hivatkozást az egyes adatpontok tulajdonságaiban tároljuk, így a jelölőhöz is beolvashatók, például ha egy jelölőre kattintanak. A térképre fókuszálva a TAB billentyű lenyomásával lehetővé válik, hogy a felhasználó átlépjen a térképen lévő összes előugró ablakon.

<br/>

<iframe height='500' scrolling='no' title='Elérhető alkalmazás létrehozása' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg, hogy a toll <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>elérhetővé teszi-e</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alkalmazást a <a href='https://codepen.io'>CodePen</a>. </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>További kisegítő lehetőségek

Íme néhány további tipp, amellyel elérhetővé teheti a webes leképezési alkalmazást.

- Ha sok interaktív pont adatai jelennek meg a térképen, érdemes lehet csökkenteni a zsúfoltságot és használni a fürtözést. 
- Győződjön meg róla, hogy a szöveg/szimbólumok és a háttérszínek közötti színkontraszt arány 4.5:1 vagy több.
- A képernyőolvasó (ARIA, ALT és title attribútumok) rövid, leíró és értelmes maradjon. Kerülje a szükségtelen zsargont és mozaikszavakat.
- Próbálja meg optimalizálni a képernyőolvasónak küldött üzeneteket, hogy rövid, hasznos információkat nyújtson a felhasználó számára a kivonatoló művelethez. Ha például nagy gyakorisággal szeretné frissíteni a képernyőolvasót, például a Térkép áthelyezésekor, vegye figyelembe a következőket:
    - Várjon, amíg a Térkép befejezte a képernyőolvasó frissítését.
    - A frissítések szabályozása néhány másodpercenként egyszer. 
    - Az üzenetek összevonása logikai módon. 
- Ne a szín legyen az információközlés egyetlen eszköze. Szöveg, ikonok vagy mintázatok használata a szín kiegészítéséhez vagy cseréjéhez. Néhány szempont:
    - Ha buborék réteget használ az adatpontok közötti relatív érték megjelenítéséhez, érdemes megfontolnia az egyes buborékok sugarának skálázását, illetve a színezésük alternatívájaként. 
    - A különböző metrikák, például a háromszögek, a csillagok és a négyzetek különböző ikonjait tartalmazó szimbólum-réteget használjon. A szimbólum réteg az ikon méretének méretezését is támogatja. Egy szöveges címke is megjeleníthető.
    - A sorokban lévő értékek megjelenítéséhez a vastagságot és a méretet is felhasználhatja. A kötőjel-tömb minta a sorok különböző kategóriáinak ábrázolására használható. A szimbólum réteg a vonal mentén átfedésben lévő ikonokkal együtt is használható. A nyíl ikon használata hasznos a vonal folyamatának vagy irányának megjelenítéséhez.
    - A sokszögek adatainak megjelenítésekor a mintázat (például a sávok) a szín alternatívájaként is használható. 
- Bizonyos vizualizációk, például a intenzitástérképei, a csempe rétegei és a képrétegek nem érhetők el a látássérültekkel rendelkező felhasználók számára. Néhány szempont:
    - A képernyő-olvasó leírja, hogy a réteg hogyan jelenjen meg a térképhez való hozzáadáskor. Ha például egy időjárási radar csempe réteg jelenik meg, a képernyőolvasóhoz hasonló "időjárási radar-adatelemek tartoznak a térképen."
- Korlátozza az egérmutatót igénylő funkciók mennyiségét. Ezek nem lesznek elérhetők azokhoz a felhasználókhoz, akik billentyűzetet vagy érintőképernyős eszközt használnak az alkalmazással való kommunikációhoz. Vegye figyelembe, hogy az interaktív tartalmak (például a kattintható ikonok, hivatkozások és gombok) esetében továbbra is jó az egérmutató stílusa.
- Próbálja megnavigálni az alkalmazást a billentyűzet használatával. Győződjön meg arról, hogy a tabulátorok sorrendje logikai.
- Ha billentyűparancsokat hoz létre, próbálja meg korlátozni két vagy kevesebb kulcsra. 

## <a name="next-steps"></a>Következő lépések

Ismerje meg a web SDK-modulok hozzáférhetőségét.

> [!div class="nextstepaction"]
> [Rajzeszközök kisegítő lehetőségei](drawing-tools-interactions-keyboard-shortcuts.md)

Ismerkedjen meg az elérhető alkalmazások Microsoft Learnsal való fejlesztésével:

> [!div class="nextstepaction"]
> [A kisegítő lehetőségek a digitális jelvények képzési tervében](https://ready.azurewebsites.net/learning/track/2940)

Tekintse meg ezeket a hasznos kisegítő lehetőségeket biztosító eszközöket:
> [!div class="nextstepaction"]
> [Akadálymentes alkalmazások fejlesztése](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [A WAI – ARIA áttekintése](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Webes kisegítő kiértékelési eszköz (WAVE)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [WebAim szín kontraszt-ellenőrzője](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [Nincs Coffee látási szimulátor](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
