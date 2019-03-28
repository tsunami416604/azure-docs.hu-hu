---
title: Útmutató az Azure térbeli horgonyok használó hatékony forráshorgony élményt |} A Microsoft Docs
description: Irányelvek és szempontok hozhat létre és Azure térbeli horgonyok használatával hatékonyan keresse meg a központi jellegűek.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9e77dcd96ffa0fbd57aa0ed1b4f857279ca768a7
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520802"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Hozzon létre egy hatékony forráshorgony élmény Azure térbeli horgonyok használatával

Ez a cikk útmutatást nyújt, és hatékony segítséget szempontok létrehozása, és keresse meg a központi jellegűek térbeli horgonyok használatával.

## <a name="good-anchors"></a>Jó horgonyok

Térbeli horgonyok segít a helyes horgonyokat létrehozni. Fontos, hogy idő oktatásához, vagy a felhasználói felület (UX) érdemes létrehozni a felhasználói megtett be. A jó horgonyok meghozni létrehozása terhelésnél, a végfelhasználókkal horgonyok megbízhatóan kereséséhez:

- Különböző eszközökön.
- A különféle időpontokban.
- A különböző fényviszonyok.
- A kívánt perspektívából belül a területet.

## <a name="static-and-dynamic-locations"></a>Statikus és dinamikus helyek

A helyek tervezésekor a forráshorgony élmény megválasztása. Lesznek a helyek statikus és a terület a rendszergazda által meghatározott? Vagy a program, a dinamikus és a felhasználó által definiált?

Egy kereskedelmi üzletvezetőhöz érdemes rávenni a felhasználókat, hogy egy statikus az áruházi élményt. De a vegyes valóságon alapuló tábla játék fejlesztői érdemes a felhasználók a play helyének kiválasztása.

Statikus helyeket megtanítható a rendszergazdák számára, hogy a terület a helyes horgonyok a felfedezhetőséget időt.

A dinamikus helyeket gondolja hogyan tanítsa meg, vagy felhasználói útmutató a helyes horgonyokat létrehozni a felhasználói felület.

## <a name="stable-visual-features"></a>Stabil vizuális jellemzőket

Vizuális követési-rendszereket használni a vegyes valóságon alapuló és kibővített valóság eszközök a környezet visual szolgáltatásait használják. A legmegbízhatóbb élmény lekérése:  

- *Tegye* horgonyokat létrehozni, amelyet a stabil visual funkciók (vagyis a Funkciók, amelyek nem változnak gyakran) rendelkezik.

- *Nem* horgonyokat létrehozni nagy üres felületeken, amely nincs megkülönböztetni jellemzőkkel rendelkeznek.

- *Nem* magas reflektív anyagokra horgonyokat létrehozni.

- *Nem* felületeken, ahol a minta megismétlődik, például carpet vagy háttérkép horgonyokat létrehozni.

![Horgonyok egy jó környezetről, és a egy rossz környezetben horgonyok példái](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Különböző megtekintésre szempontok szerint

Horgonyra létrehozásakor gondolja át a személyek, akik később megpróbálja megkeresni a horgony.

Fontolja meg, például két ajtó rendelkező szoba közepén horgonyra. Valószínűleg szeretné engedélyezése a felhasználók számára adja meg a szoba vagy ajtó. A horgony létrehozásakor kell a mindkét átjáróknak pozícióját vizsgálata. Módosíthatja a horgony körül környezeti adatok rögzítése, hogy a felhasználók megtalálja a horgony vagy ajtó a perspektívákat.

Általánosságban véve horgonyra létrehozásakor vizsgálja a perspektívából megpróbálja megkeresni, személy. Így az egy öltözet szobor virtuális tartalom elhelyezése, logikus körül a szobor való létrehozásakor a horgony keresésekor, és megtudhatja, hogyan. Ha a forráshorgony szoba sarkában, nincs a megközelítést csak egy irányban. Ez a horgony létrehozásakor, beolvashatja azt csak a szempontjából.

## <a name="multiple-anchors"></a>Több horgonyok

Világítás is sokat számít, hogy az észlelt alkalmazás visual szolgáltatások. Előfordulhat, hogy az erős természetes világos létrehozott horgonyok nehezen keresse ki a mesterséges fény, és ez fordítva is igaz.  

Ha a probléma, megkönnyíti két horgonyokat létrehozni. Az azonos helyszíni, hozzon létre egy kapcsolati alappal a nyári időszámítás – és a egy másik mesterséges fény a. Az alkalmazás ezután lekérdezheti a mindkét kapcsolati alapokat. Ha mindkét kapcsolati alap található, az alkalmazás számára a horgony egy testtartás tartalmaz. 

Ehhez hasonlóan környezetekben, ahol módosíthatja a vizuális jellemzőket, mert a legtöbb objektum áthelyezése, több horgonyok segítségével. Túl bonyolult miatt jelentős változtatásokat a környezetben található horgonyra válásakor a horgony lecserélheti egy újat. Erre akkor lehet szükség, ha például egy kiskereskedelmi bolt, ahol a elrendezése néhány havonta frissül.

## <a name="targets-and-rooms"></a>Tárolók és a

Sok esetben horgonyra az alkalmazás felhasználói felülete egy belépési pontjához. Érdemes ezt a lépést keresztül gyorsan és megbízhatóan Igen felhasználókat adhat meg a felhasználói élmény. A felhasználók hogyan keresse meg a központi jellegűek költségkeret-beállítási idő egy fontos tervezési lépést. Érdemes úgy gondolja, hogy horgonyok tekintetében két széleskörű forgatókönyvek keresése: *célok* és *termek*.

### <a name="targets"></a>Célok

A cél a forgatókönyvben a horgony helye jól ismert. Például egy képzeletbeli vegyes valóságon alapuló festmény App egy felhasználói helyez egy virtuális vászonalapú a fali. Marcela arra utasítja a más felhasználók észlelnek a helyiségben, mutasson az eszközeiket, keresse meg a horgony és a felhasználói élményt üzenőfalán ugyanazon a helyen.  

Egy másik cél például lehet egy bejelentkezési egy kávézóban, olvasó, a "Vizsgálat üzletek számára." A kávézóban rendelkezik horgonyra itt elhelyezni. A felhasználók a bejelentkezési vizsgálata, keresse meg a horgony, és adja meg a kibővített valóság élmény üzletek megtalálja a kávét.

A cél a forgatókönyvben a fényképek segítségével. Ön felhasználók megjelenítése a szándékolt cél fénykép az eszközén, ha azok gyorsan azonosíthatja a Mi a valós életben vizsgálata. Például akkor előfordulhat, hogy segítségével a felhasználók, érkezik egy szándékolt cél általános területén belül a GPS-adatok használatával. A felhasználó érkezik, az alkalmazás a cél egy képet jelenít meg. A felhasználó körül a helyet úgy tűnik, a cél észleli, és a horgony keres.

![Egy fénykép a cél a felhasználó mobil eszközön ábrázoló horgonyra ábrája](./media/start-here-edit.png)

### <a name="rooms"></a>termek

A hely a forgatókönyvben a felhasználók egyszerűen ismerete nincs valahol a horgony Itt adhatja adja meg. A felhasználók a terület vizsgálat az eszközükön, és gyorsan megtalálhatja a horgony.

Ez a tapasztalat általában megköveteli, hogy jól válogatott horgonyokat létrehozni különböző megtekintésre perspektívák leírt módon. Ha sok perspektívából észlelnek a helyiségben a létrehozása után a horgony beolvasott, a felhasználók szinte bárhonnan beolvashatja amikor találja meg.

![A felhasználó hogyan beolvasás található horgonyra szoba ábrája](./media/scan-room.png)

Alapvetően, több időt a lemezterület ellenőrzése a horgony létrehozásakor, így a későbbi felhasználók vizsgálata, és keresse meg a horgony gyorsan. A felhasználói élmény létrehozása, kell fontolja meg a fontos kompromisszum.

A példában a vegyes valóságon alapuló festmény alkalmazás, amely már volt szó korábbi nem működik jól mint egy szoba eset. Itt a felhasználó, aki helyezi el a horgony szeretné másoknak gyorsan csatlakozni a felhasználói élményt. A felhasználók nem szeretnének várjon addig, amíg a helyiségben is vizsgálja, indítsa el a felhasználói élményt. Minden felhasználó, hogy pontosan hol keresse meg a központi jellegűek eszköz pont, mert ebben a példában jobban, mint egy cél eset működik.

## <a name="anchor-location"></a>Rögzítési helye

Vizuális követési rendszer környezetben a vizuális szolgáltatásait használják. A több vizuális jellemzőket, amely tartalmazza a vizsgálatot, minél nagyobb a valószínűsége horgonyra keresése.

Kövesse az ebben a szakaszban egy felhasználói felület, amely arra ösztönzi a hasznos keresését, a környezetet hozhat létre az általános útmutatást.

Először Ha a felhasználó nem található horgonyra néhány másodpercen belül, az alkalmazás kell ösztönözzék a felhasználókat a helyezheti az eszközöket, további perspektívákat rögzítéséhez. Az alkalmazás is képes ösztönözzék a felhasználókat a Navigálás magukat a szolgáltatást a horgony több szemszögből a környezetben. A további funkciót perspektívákat, amelyek az eszköz látja, annál jobb.

A cél esetben kérje meg a felhasználót, hogy a célként megadott Navigálás a különböző szemszögből megtekintéséhez. Más szóval kérje meg a felhasználó a cél új perspektívából rögzítéséhez, amíg a horgony nem található.

Hely forgatókönyvek esetén kérje meg a felhasználót, hogy lassan vizsgálata észlelnek a helyiségben. Például kérje meg a felhasználó számára, hogy 180 fokos vagy a hely még 360 fok. Vagy kérje meg a felhasználó megtekintheti az új szempontjából észlelnek a helyiségben. 

A legtöbb jelentéssel bíró metódus a szoba különböző vizsgálata. A vizsgálat a szoba különböző keresését egy közeli időpont, a környezet több visual szolgáltatással például rögzíti. Keresését egy közeli időpont alapján, a környezetet tetszőleges számú hasznos vizuális jellemzőinek nem rögzíti.

Nem hasznos ismételten helyezze át az eszköz oldalra horgonyra keresésekor. Ez egyszerűen a azonos szempontjából azonos pontok rögzíti.

## <a name="experience-tests"></a>Élmény tesztek

Ebben a cikkben korábban már volt szó általános irányelveket. A térbeli horgonyok ír le, hogy a való világból együttműködő alkalmazásokat. Ezért az alkalmazás kapcsolati alappal forgatókönyvek tesztelését valós környezetben kell áldozni idő. Ez különösen igaz környezetek, amelyek a várt a felhasználók használhatják az alkalmazást.
