---
title: Útmutató a hatékony forráshorgony környezeteket az Azure térbeli horgonyok |} A Microsoft Docs
description: Irányelvek és szempontok létrehozásához, és hatékonyan az Azure térbeli horgonyok horgonyok megkeresése.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 196958e4818251bd7f2ee78ca472e6f28292d908
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753152"
---
# <a name="guidelines-for-an-effective-anchor-experience-with-azure-spatial-anchors"></a>Útmutató a hatékony forráshorgony felületet nyújt az Azure térbeli horgonyok

Ez a cikk útmutatást és szempontokat létrehozásához, és hatékonyan az Azure térbeli horgonyok horgonyok megkeresése.

## <a name="creating-anchors"></a>Horgonyok létrehozása

Jó horgonyok létrehozásának az egyik a legnagyobb hatású szempontból leegyszerűsítik az Azure térbeli horgonyok használatát. Fontos, hogy az idő oktatásához, vagy a felhasználói felület a felhasználók jó horgonyok létrehozása megtett be. A horgony létrehozásakor meghozni terhelésnél végfelhasználók számára, hogy megbízhatóan keresse meg a központi jellegűek engedélyezése:

- Különböző eszközökön
- Különböző időpontokban
- A különböző fényviszonyok
- A címtéren belüli kívánt perspektívából
- Stb.

Az alábbiakban szempontokat és irányelveket a forráshorgony létrehozását és a hely élmény tervezéséhez nyújt segítséget.

## <a name="static-and-dynamic-locations"></a>Statikus és dinamikus helyek

Tervezésekor a forráshorgony élményt. rész, amely vehet részt a helyek kiválasztása fog lehet. Lesznek a helyek statikus és a terület a rendszergazda által meghatározott? Vagy a program, a dinamikus és a felhasználó által definiált?

Egy kereskedelmi üzletvezetőhöz érdemes rávenni a felhasználókat, hogy egy statikus az áruházi élményt. Mivel a fejlesztői valószínűleg vegyes valóságot tábla használó szeretné, hogy a felhasználók a play helyének kiválasztása.

Statikus helyeket kérje meg a rendszergazdák számára, hogy a terület a helyes horgonyok a felfedezhetőséget időt.

A dinamikus helyeket gondolja hogyan felkészítése a használatára, vagy felhasználói útmutató a helyes horgonyokat létrehozni a felhasználói felület.

## <a name="stable-visual-features"></a>Stabil vizuális jellemzőket

Vizuális követési rendszer használt a vegyes valóságon alapuló és a kiterjesztett valóságban eszközök visual szolgáltatásait használják a környezet. A legmegbízhatóbb élmény lekérése:  

- Stabil visual funkciók (vagyis a Funkciók, amelyek nem változnak gyakran) rendelkező helyeken horgonyokat létrehozni.

- Ne hozzon létre horgonyok nincs megkülönböztetni jellemzőkkel rendelkező nagy üres felületek.

- Ne hozzon létre magas reflektív anyagok horgonyok.

- Ne hozzon létre horgonyok felületek, ahol a kizárólag a minta ismétlődő, például carpet vagy háttérkép.

![A példában képek, amelyek egy jó és a megfelelő környezetben](./media/stable-visual.png)

## <a name="consider-various-viewing-perspectives"></a>Vegye figyelembe a különböző megjelenítési szempontok szerint

Horgonyra létrehozásakor gondolja át a személyek, keresse meg a horgony később talál.

Fontolja meg, például két módjait tette lehetővé a szoba közepén horgonyra. Valószínűleg szeretné engedélyezése a felhasználóknak, hogy adja meg az vagy egy ajtót keresse meg a horgony észlelnek a helyiségben. A horgony létrehozásakor, szüksége lesz a mindkét átjáróknak pozícióját vizsgálata. Ez rögzíti környezeti adatok körül a horgony mindkét perspektívából, hogy a felhasználók megtalálja a horgony vagy ajtó a.

Általánosságban véve horgonyra létrehozásakor, azt kell futtatni a különböző helyeken vagy a perspektívákat, várt lehet állandó megkísérlésekor. Keresse meg a személyek.  

Ha virtuális tartalmat a egy öltözet szobor helyez el, logikus lépésről-lépésre a szobor, létrehozásakor, a horgony megtekintése közben körül.  

Másrészről Ha a forráshorgony szoba sarkában, nincs ezt a megközelítést csak egy irányban. A horgony létrehozásakor, egyszerűen kell ezen szempontból vizsgálja.

## <a name="multiple-anchors"></a>Több horgonyok

Világítás teheti, hogy a vizuális jellemzőket eltérőek lesznek. Lehet, hogy az erős természetes világos létrehozott kapcsolatok alapjainak nehezen mesterséges világítás mellett sötét után keresse meg és ez fordítva is igaz.  

Ezt a problémát tapasztal, hozzon létre két horgonyok – egy, a nyári időszámítás – és a egy másik mesterséges világítás mellett – azonos helyszíni segítséget. Az alkalmazás ezután lekérdezheti a mindkét kapcsolati alapokat. Ha vagy találhatók, az alkalmazás számára a horgony egy testtartás tartalmaz. 

Ehhez hasonlóan környezetekben, ahol módosíthatja a vizuális jellemzőket, mert a legtöbb objektum áthelyezése, több horgonyok segítségével. Túl bonyolult miatt jelentős változtatásokat a környezetben található horgonyra válásakor, lecserélheti egy újat. Ez lehet a helyzet, ha például egy kiskereskedelmi bolt, ahol a elrendezése néhány havonta frissül.

## <a name="targets-and-rooms"></a>Tárolók és a

Sok esetben horgonyra megkeresése jelöli egy belépési pont az alkalmazás egyedi felhasználói felületre. Érdemes ezt a lépést keresztül gyorsan és megbízhatóan Igen felhasználókat adhat meg a felhasználói élmény. A felhasználók hogyan keresse meg a központi jellegűek költségkeret-beállítási idő egy fontos tervezési lépést. Hasznos lehet a széles körű kétféleképpen utasokat: **Célok** és **termek**.

### <a name="targets"></a>Célok

![Kezdje itt szerkesztése](./media/start-here-edit.png)

A cél a forgatókönyvben a horgony helye jól ismert. Például egy képzeletbeli MR festmény App egy felhasználói helyez egy virtuális vászonalapú a fali. Marcela arra utasítja a más felhasználók észlelnek a helyiségben, mutasson az eszközeiket, keresse meg a horgony és a felhasználói élményt üzenőfalán ugyanazon a helyen.  

Egy másik cél például lehet bejelentkezési figyelmezteti a "keresése üzletek" sorban egy kávézóban való várakozás során. A kávézóban korábban helyezte horgonyra itt. A felhasználók a bejelentkezési alapján történő vizsgálata, keresse meg a horgony, és adja meg az AR élmény kávé üzletek számára.

A cél a forgatókönyvben a fényképek segítségével. Ha az eszköz a szándékolt cél felhasználók fényképet is mutatják, azok gyorsan azonosíthatja a Mi a valós életben vizsgálata. Például, előfordulhat, hogy segítségével a felhasználók, a GPS-adatok használatával egy szándékolt cél általános közelében belül érkezik. Miután a felhasználó érkezik, az alkalmazás a cél fénykép jeleníti meg. A felhasználó úgy tűnik, a hely körül, megkeresi a cél, és keressen a horgony abból.

### <a name="rooms"></a>termek

![Scan Room](./media/scan-room.png)

A hely a forgatókönyvben a felhasználók egyszerűen ismerete nincs valahol a horgony Itt adhatja adja meg. Felhasználók a terület vizsgálat az eszközükön, és gyorsan megtalálhatja a horgony.

Ez a tapasztalat általában elérése szükséges jól válogatott horgonyok létrehozása az [fontolja meg a különböző megjelenítési perspektívák](#consider-various-viewing-perspectives) korábban. Ha a hely sok perspektívából megvizsgált a horgony létrehozásakor, majd felhasználók beolvashatja szinte bárhonnan megkeresésében tett kísérlet során.

Alapvetően a horgony létrehozó személy várólistában vizsgálata a területet, így később személyek vizsgálata, és keresse meg a horgony több időt gyorsan. Ez az egy fontos kompromisszum szüksége lesz figyelembe venni a felhasználói élmény.

A korábban említett MR festmény alkalmazás, amelyek nem jól, mint egy szoba eset. A horgony helyezi el a felhasználó itt, a többi gyorsan csatlakozni a felhasználói élményt szeretne. A felhasználók nem szeretne Várjon, amíg a helyiségben jól beolvasott tapasztalatok megkezdéséhez. Minden felhasználó, hogy pontosan hol keresse meg a központi jellegűek eszköz mutasson, mivel ebben a forgatókönyvben jobban számít egy cél forgatókönyvet.

## <a name="effectively-locating-anchors"></a>Horgonyok hatékonyan megkeresése

Vizuális követési rendszer működéséhez a környezetnek a vizuális szolgáltatásait használják. A több vizuális jellemzőket részei a vizsgálatot, minél nagyobb a valószínűsége horgonyra keresése.

Van néhány általános irányelveket követve hozhat létre a felhasználói környezet, amely arra ösztönzi a hasznos keresését, a környezetben.

Először is ha a felhasználó nem található horgonyra néhány másodpercen belül, az alkalmazás kell ösztönözzék a felhasználókat a helyezheti az eszközöket, így további perspektívákat rögzítve lesznek.  Az alkalmazás is ösztönözheti a felhasználók maguk számára a környezet Navigálás a horgony több szemszögből keresése közben. A további perspektívákat, amelyről az eszköz látja, ugyanaz a jobban mutat.

A cél esetben kérje meg a felhasználót, hogy a célként megadott Navigálás mellett tekintse meg a programban különböző szempontok szerint. Más szóval kérje meg a felhasználót, hogy helyezze át, hogy megtalálják a helyüket a cél új perspektívából rögzítéséhez, amíg a horgony nem található.

Hely forgatókönyvek esetén kérje meg a felhasználót, hogy lassan vizsgálata észlelnek a helyiségben. Ha például kéri a felhasználót az Elforgatás 180 vagy a hely még 360 fok rögzítéséhez. Vagy kérje meg a felhasználót, hogy helyezze át egy új perspektíva észlelnek a helyiségben. A legtöbb jelentéssel bíró eszközök vizsgálata, hogy a szoba különböző vizsgálata. Ez rögzíti a több visual funkciót a környezet, mint például egy közeli üzenőfal vizsgálatát. Egy közeli üzenőfal vizsgálata, sokkal hasznos vizuális jellemzőket a környezet nem rögzíti.

Nem hasznos lehet az eszköz a-párhuzamos újra és újra áthelyezése a horgony keresésekor. Ez lenne egyszerűen rögzíti a az azonos szempontjából azonos pontokat.

## <a name="testing-the-experience"></a>A felhasználói élményt tesztelése

A fenti olyan általános irányelveket. Az Azure térbeli horgonyok kell írnia, hogy a való életből vett együttműködő alkalmazásokat. Ezért fontos idő áldozni valós környezetekben az alkalmazás kapcsolati alappal forgatókönyvek tesztelését. Ez különösen igaz környezetekben, amelyek képviselő várt a felhasználók használhatják az alkalmazást.