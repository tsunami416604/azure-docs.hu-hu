---
title: Útmutató a hatékony horgonyélményhez
description: A horgonyok létrehozásához és megkereséséhez az Azure Spatial Anchors használatával vonatkozó irányelvek és szempontok.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9a24da8d76f401f534eccf33312fbf0c2bee9f5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270520"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Hatékony horgonyélmény létrehozása az Azure Spatial Anchors használatával

Ez a cikk irányelveket és szempontokat tartalmaz a horgonyok térbeli horgonyok használatával történő hatékony létrehozásához és megkereséséhez.

## <a name="good-anchors"></a>Jó horgonyok

Térbeli horgonyok segít létrehozni jó horgonyok. Fontos, hogy időt fordítson a felhasználók oktatására vagy irányítására a felhasználói élményben (UX), hogy jó horgonyokat hozzon létre. Ha befektet a jó horgonyok létrehozásába, segít a végfelhasználóknak megbízhatóan megtalálni a horgonyokat:

- Különböző eszközökön.
- Különböző időpontokban.
- Különböző fényviszonyok között.
- A kívánt perspektívákból a térben.

## <a name="static-and-dynamic-locations"></a>Statikus és dinamikus helyek

A horgonyélmény tervezésének része a helyszínek kiválasztása. A helyek statikusak lesznek, és a terület rendszergazdája határozza meg őket? Vagy lesznek dinamikus és határozza meg a felhasználó?

Előfordulhat, hogy egy kiskereskedelmi üzletvezető statikus üzleten belüli felhasználói élményt szeretne, hogy a felhasználókat a látogatásra csábítsa. De a fejlesztő egy vegyes valóság társasjáték valószínűleg szeretné, hogy a felhasználók választhatnak, ahol játszani.

Statikus helyek esetén megtaníthatja a rendszergazdáknak, hogy jó horgonyokkal töltsék az időt a terület kurátorával.

Dinamikus helyek esetén érdemes átgondolnia, hogyan taníthatja vagy irányítja a felhasználókat a felhasználói környezetben, hogy jó horgonyokat hozzon létre.

## <a name="stable-visual-features"></a>Stabil vizuális funkciók

A vegyes valóságú és kiterjesztett valóságú eszközökön használt vizuális nyomkövető rendszerek a környezet vizuális jellemzőire támaszkodnak. Ahhoz, hogy a legmegbízhatóbb élmény:

- *Hozzon* létre horgonyokat olyan helyeken, amelyek stabil vizuális funkciókkal rendelkeznek (azaz olyan funkciókkal, amelyek nem változnak gyakran).

- *Ne* hozzon létre horgonyt olyan nagy üres felületeken, amelyek nem rendelkeznek megkülönböztető jellemzőkkel.

- *Ne* hozzon létre horgonyt a rendkívül fényvisszaverő anyagok.

- *Ne* hozzon létre horgonyt olyan felületeken, ahol a minta ismétlődik, például szőnyegen vagy tapétán.

![Példák a horgonyok jó környezetére és a horgonyok rossz környezetére](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Különböző nézőpontok

Amikor létrehoz egy horgonyt, gondoljon az emberekre, akik később megpróbálják megtalálni a horgonyt.

Vegyünk például egy horgonyt egy kétajtós szoba közepén. Valószínűleg engedélyezni szeretné a felhasználóknak, hogy bármelyik ajtóról belépjenek a szobába. A horgony létrehozásakor mindkét ajtóból be kell szkutatnod a helyzetét. A perspektívák módosításával környezeti adatokat rögzíthet a horgony körül, így a felhasználók bármelyik ajtóról megkereshetik a horgonyt.

Általánosságban elmondható, hogy a horgony létrehozásakor szkitelje azt az emberek szemszögéből, akik megpróbálják megtalálni. Tehát, ha virtuális tartalmat helyezel el egy szabadtéri szoboron, érdemes sétálni a szobor körül, miközben beszkavalod, miközben létrehozza a horgonyt. Ha a horgony a sarokban egy szoba, már csak egy irányba, hogy megközelítse azt. Amikor létrehozza ezt a horgonyt, akkor bekés, hogy csak ebből a szempontból.

## <a name="multiple-anchors"></a>Több horgony

A megvilágítás különbséget tehet az alkalmazás által észlelt vizuális funkciókban. Horgonyok létre erős természetes fény nehéz lehet megtalálni a mesterséges fény, és fordítva.

Ha van ez a probléma, segíthet létrehozni két horgonyok. Ugyanazon a helyen hozzon létre egy horgonyt a napfényben, és egy másikat mesterséges fényben. Az alkalmazás ezután lekérdezheti mindkét horgonyt. Ha bármelyik horgony található, az alkalmazás nak lesz egy póza a horgonyhoz.

Hasonlóképpen, olyan környezetekben, ahol a vizuális jellemzők változnak, mert a legtöbb objektum mozog, több horgony segíthet. Ha egy horgony a környezet jelentős változásai miatt túl nehéz lesz megtalálni, a horgonyt lecserélheti egy újra. Ezt például egy kiskereskedelmi üzletben teheti meg, ahol az elrendezés néhány havonta frissül.

## <a name="targets-and-rooms"></a>Célok és szobák

Sok esetben a horgony belépési pont az alkalmazás élményéhez. Ezt a lépést gyorsan és megbízhatóan kell végigcsinálnia, hogy a felhasználók beléphessenek a felhasználói élménybe. Fontos tervezési lépés, hogy a felhasználók hogyan találják meg a horgonyokat. Ez hasznos gondolkodni találni horgonyok szempontjából két tág forgatókönyvek: *célok* és *szobák*.

### <a name="targets"></a>Célokat

A célforgatókönyvben a horgony helye jól ismert. Egy kitalált vegyes valóságú festőalkalmazásban például egy felhasználó virtuális vásznat helyez el a falon. Arra utasítja a szobában lévő többi felhasználót, hogy a falon lévő eszközökre irányítsák a horgonyt, és kezdjék meg az élményt.

A célforgatókönyv egy másik példája lehet egy "Ügyletek beolvasása" feliratot tartalmazó kávézóban található jel. A kávézó betett ide egy horgonyt. Ahogy a felhasználók beszkatoltaak a jelen, megkeresik a horgonyt, és belépnek a kiterjesztett valóság élményébe, hogy megtalálják a kávéval kapcsolatos ajánlatokat.

A célforgatókönyvben a fényképek segíthetnek. Ha a felhasználók nak fényképet mutat a tervezett célról az eszközükön, gyorsan azonosíthatják, hogy mit kell beszkavalni a valós világban. Például segíthet a felhasználóknak a tervezett cél általános területén belül a GPS használatával. Amikor a felhasználó megérkezik, az alkalmazás megjeleníti a cél fényképét. A felhasználó körülnéz a térben, megkeresi a célt, és megvizsgálja a horgonyt.

![Horgony illusztrációja, amelyen a felhasználó mobileszközén látható a cél fényképe](./media/start-here-edit.png)

### <a name="rooms"></a>Szobák

A szoba forgatókönyv, a felhasználók be egy helyet egyszerűen tudva, hogy van egy horgony itt valahol. A felhasználók beszkacozzák a helyet az eszközükkel, és gyorsan megtalálják a horgonyt.

Ez az élmény általában megköveteli, hogy hozzon létre jól válogatott horgonyok, amint azt a különböző megtekintési perspektívák. Ha a horgony létrehozásakor több szempontból is beszkennelte a szobát, a felhasználók szinte bárhol beszkennelhetik a helyiséget, amikor megpróbálják megtalálni.

![Példa arra, hogy a felhasználó hogyan szavalhatja be a helyiséget, hogy megtalálja a horgonyt](./media/scan-room.png)

Lényegében több időt tölt a tér beolvasásával a horgony létrehozásakor, hogy a későbbi felhasználók gyorsan beszkataljíthassák és megtalálhassák a horgonyt. A felhasználói élmény létrehozásakor figyelembe kell vennie ezt a fontos kompromisszumot.

A korábban tárgyalt vegyes valóságú festőalkalmazás példája nem működik jól, mint egy szobaforgatókönyv. Itt az a felhasználó, aki a horgonyt helyezi el, azt szeretné, hogy mások is gyorsan csatlakozzanak az élményhez. A felhasználók nem akarnak várni, hogy indítsa el a tapasztalat, amíg a szoba jól beolvasott. Mivel minden felhasználó pontosan tudja, hogy hová irányítsa az eszközét a horgonyok megkereséséhez, ez a példa jobban működik célforgatókönyvként.

## <a name="anchor-location"></a>Horgony helye

A vizuális nyomkövető rendszerek a környezet vizuális jellemzőire támaszkodnak. Minél több vizuális funkciót tartalmaz a vizsgálat, annál nagyobb a valószínűsége annak, hogy horgonyt talál.

Kövesse az ebben a szakaszban található általános irányelveket, hogy olyan felhasználói felületet hozzon létre, amely a környezet hasznos vizsgálatát ösztönzi.

Először is, ha a felhasználó néhány másodpercen belül nem talál horgonyt, az alkalmazásnak ösztönöznie kell a felhasználókat az eszköz áthelyezésére, hogy több perspektívát rögzítsenek. Az alkalmazás arra is ösztönözheti a felhasználókat, hogy mozogjanak a környezetben, hogy több szempontból keressék a horgonyt. Minél több funkció perspektívát lát az eszköz, annál jobb.

A célforgatókönyvek, kérje meg a felhasználót, hogy mozogjon a cél, hogy megtekinthesse a különböző nézőpontokból. Más szóval kérje meg a felhasználót, hogy rögzítse a célt új perspektívákból, amíg a horgony található.

Szobai esetek esetén kérje meg a felhasználót, hogy lassan szokja be a szobát. Például, kérje meg a felhasználót, hogy kapcsolja be, hogy elfog 180 fok, vagy akár 360 fok a szobában. Vagy kérje meg a felhasználót, hogy tekintse meg a szobát egy új perspektívából.

A legértelmesebb módszer az, hogy átkotsza a szobán. A szoba egészének beszkandrása több vizuális jellemzőt rögzít a környezetben, mint például egy közeli fal beszatatod. A közeli fal beszatatod nem fogja meg a környezet számos hasznos vizuális jellemzőjét.

Nem hasznos, ha többször is áthelyezi a készüléket egyik oldalról a másikra, amikor horgonyt keres. Ez egyszerűen ugyanazokat a pontokat rögzíti ugyanabból a szempontból.

## <a name="experience-tests"></a>Tapasztalati tesztek

Ebben a cikkben általános irányelveket tárgyaltunk. A Térbeli horgonyokkal olyan alkalmazásokat ír, amelyek a valós világgal kommunikálnak. Emiatt érdemes időt szánni az alkalmazás horgonyforgatókönyveinek valós környezetekben való tesztelésére. Ez különösen igaz azokra a környezetekre, amelyek azt jelzik, hogy a felhasználók hol használhatják az alkalmazást.
