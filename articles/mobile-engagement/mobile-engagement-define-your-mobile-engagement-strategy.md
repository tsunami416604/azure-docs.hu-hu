---
title: A Mobile Engagement-stratégia kidolgozása | Microsoft Docs
description: Ismerje meg, hogyan vezethető be és optimalizálható a Mobile Engagement az elemzések és leküldéses értesítések tekintetében.
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 7533e318-81b9-4360-aace-b7be8225985b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 494dd27acfd59eb79da13dae9bbbe0f80ee41370
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="define-your-mobile-engagement-strategy"></a>A Mobile Engagement-stratégia kidolgozása
> [!IMPORTANT]
> Az Azure Mobile Engagement támogatása 2018. március 31-én megszűnik. Az oldal nem sokkal ezután törlésre kerül.
> 

*Alkalmazását okkal készítette el: azért, hogy felhasználói használhassák azt!*

Biztosak vagyunk abban, hogy rengeteg energiát fektetett abba, hogy nagyszerű alkalmazás legyen, amelyet a felhasználók szeretni fognak. Valószínűleg jelentős összeget költött marketingre is, hogy felhasználókat szerezzen. A felhasználók kezdeti szívderítő áradata után azonban esetleg azt látja, hogy a felhasználók lassan abbahagyják az alkalmazás használatát. *Erről szól az Azure Mobile Engagement*: rávenni a felhasználókat a maradásra, és fokozatosan fejleszteni az alkalmazást tesztelés és tanulás útján.

A megtartás és a használat javításának általunk választott megközelítése azon alapul, hogy kapcsolatot teremtünk a felhasználókkal leküldéses értesítésekkel és alkalmazáson belüli üzenetekkel, azonban kifinomult módon, a felhasználókra szabott olyan üzenetekkel és kommunikációval, amelyek mindegyikét az alkalmazáson belüli viselkedésük szerint formálunk. Célunk, hogy a megfelelő célközönséggel tudjon kommunikálni a megfelelő időben és helyen.

Ehhez azonban először *meg kell ismernie a felhasználókat*, majd csoportokat kell létrehoznia a tevékenységük vagy jellemzőik alapján (ezen csoportokat szegmenseknek nevezzük), végül a szegmenseknek szóló kommunikációt kell teremtenie.

## <a name="mobile-engagement-serves-your-objectives"></a>A Mobile Engagement segítséget nyújt a céljai megvalósításához
*Beszéltünk megtartásról és használatról, de mi célból?*

A Mobile Engagement-stratégia kidolgozásához először az alkalmazás céljait és fő teljesítménymutatóit (KPI-k) kell szemügyre vennie.

Kezdje a célok és a KPI-k meghatározásával, amelyek megfelelő megvilágításba helyezik és segítenek meghatározni a bevonási használati eseteket.

A használati esetek azon kampányok egyszerű listája, amelyeket meg szeretne valósítani a felhasználókkal való kommunikációhoz, kezdve egy egyszerű üdvözléssel az informatikai rendszer által aktivált, nagyon fejlett segítő jellegű értesítésig. Egy jól felépített használati esetnek legalább a *mit-kinek-mikor?* kérdéscsoportra adott válaszok alapján kell felépülnie:

1. Egy nagyon rövid megjelölést (például: „Üdvözlőkampány”).
2. **Mit**: Példaüzenet (például: „Örülünk, hogy csatlakozott hozzánk! Ne felejtsen el bejelentkezni az 1. első ingyenes hónap igénybe vételéhez!”). Ez az üzenet semmiképpen sem végleges, bármikor megváltoztathatja, azonban általában segít elindulni a mondanivaló meghatározásakor.
3. **Kinek**: Az üzenetet megkapó szegmens (például: „Minden felhasználó, aki 3 nappal ezelőtt nyitotta meg először a mobilalkalmazást, ellátogatott a bejelentkezési oldalra, de nem jelentkezett be”).
   * Igen, az Azure Mobile Engagement segítségével ezt nagyon egyszerűen elvégezheti :)
   * Ez szintén nem végleges, mint ahogyan a szegmenseket is bármikor meghatározhatja, azonban fontos, hogy a szegmentálási feltételeket hamar meghatározza a megfelelő adatok gyűjtésének a biztosításához.
4. **Mikor**: A kampány időzítése. A kampány indulhat adott napon, egy adott művelet után vagy kiválthatja egy eseményindító. A Mobile Engagement számos lehetőséget kínál a kommunikáció megfelelő időzítéséhez.

Ha definiálta a használati eseteket és a szegmenseket, azok útmutatóként szolgálnak az alkalmazáson belül összegyűjtendő adatok meghatározásához. Ez a *„címkézési terv”* szerepe. A címkézési tervvel biztosíthatja, hogy a fejlesztők ismerjék az adatgyűjtés részleteit. A fejlesztők így beágyazhatják a megfelelően beállított Mobile Engagementet, ami lehetővé teszi, hogy a megfelelő adatokkal dolgozzon a kampányokban. Nagyon fontos tesztek futtatása annak biztosításához, hogy az integráció megfelelő legyen és a szükséges adatokat gyűjtse.

Az integráció alapján, az alkalmazások közzétételét követően már forgalmazóként láthatja az elemzéseket valós időben, szegmentálhatja a célközönséget, továbbá elkezdhet intelligensen célzott leküldéses értesítéseket küldeni a végfelhasználók bevonásához az alkalmazáson kívül vagy belül.

### <a name="use-cases-to-get-started"></a>Használati esetek az első lépésekhez
1. Üdvözlési stratégia: Hozzon létre több leküldéses értesítési kampányt a végfelhasználók alkalmazásindításkor mutatott viselkedésmintái alapján az első munkamenet utáni ismételt kapcsolatfelvétel céljából D+2/5/10/15 időpontban, és növelje az első futtatáshoz kapcsolódó megtartást.
2. Népszerűsítsen új tartalmakat (funkció, cikk/videó vagy termék) a végfelhasználók viselkedése alapján úgy, hogy csak azon végfelhasználóknak küld információt, akik nagyobb eséllyel reagálnak arra.
3. Alkalmazás értékelése: Célozza a felhasználóbázis azon kevesebb mint 1 százalékát, akik a legnagyobb eséllyel adnak 5 csillagos értékelést alkalmazásának az áruházban.
4. Előfizetések számának növelése: Az előfizetések számának növeléséhez népszerűsítsen olyan értékes tartalmakat a végfelhasználók számára, amelyekkel még nem találkoztak.
5. Oktatóanyag: Nincs többé mindenki számára kötelező oktatóanyag. Miért ne hozhatna létre nagyszerű oktatóanyagokat az alkalmazáson belül, és aktiválhatná azokat alkalmazáson belüli üzenetekkel csak akkor, ha a felhasználó nem használja az alkalmazást vagy nehézsége támad egy-egy funkció használatával?

## <a name="why-do-you-need-analytics-to-engage"></a>Miért van szüksége elemzésekre a bevonáshoz?
Ezen a ponton talán már rájött, hogy csak leküldéses értesítések létrehozása nem elegendő. A Mobile Engagement központi elve az, hogy segítsen a forgalmazóknak és a fejlesztőknek a megfelelő végfelhasználókkal felvenni a kapcsolatot a megfelelő időben és helyen. Ezen három fő alapelv ismeretéhez alapvető fontossággal bír az elemzések gyűjtése az alkalmazásból, majd a célközönség szegmentálása azok használatával. Ez még sokkal hatékonyabb, ha a viselkedési szegmensek kiegészítik a többi adatbázisból, a CRM-rendszerből vagy a keresztcsatornából származó adatokat. A Mobile Engagement bárhonnan lehetővé teszi az adatok gyűjtését, és azok felhasználását a megfelelő célközönség megcélzásához.

Ahhoz, hogy a kontextusnak leginkább megfelelő módon vegye fel a kapcsolatot célközönségével, létfontosságú a végfelhasználó viselkedésének és valós idejű állapotának ismerete. Az adatgyűjtés lehetővé teszi a forgalmazók számára, hogy arra összpontosítsanak, ami igazán fontos a használati esetek lejátszásához és mobilmarketing-stratégiájuk céljainak eléréséhez. A korábban meghatározott célok elérése az oka annak is, amiért az ajánlott eljárás tulajdonképpen nem a lehető legtöbb, hanem csak azon információk gyűjtése az elemzésekből, amelyek lehetővé teszik, hogy a használati esetekre és arra összpontosítson, amit meg akar tudni. Ez a jó módszer a kezdéshez, a próbálkozásokhoz, a teszteléshez és annak megismeréséhez, hogyan használható a megoldás, és címezhetők meg az intelligens leküldéses értesítések, illetve hogyan növelhető egy alkalmazás felhasználóinak megtartása, amelyekkel sikertörténet szintjére lehet emelni az alkalmazást.

> [!NOTE]
> Emlékezzen erre: A túl sok adat használhatatlanná teszi az adatokat!
> 
> 

### <a name="use-cases-and-best-practices"></a>Használati esetek és ajánlott eljárások
Az első lépéseket segítendő az alábbi szakaszokban röviden ismertetünk néhány olyan főbb használati esetet, amelyekkel az ügyfeleinkhez kapcsolódóan találkoztunk.

#### <a name="media"></a>Média
Gyűjtse össze a végfelhasználók által fogyasztott tartalmak típusait, és szegmentálja a célközönséget ezen viselkedésük alapján, így az adott típusú tartalmakat kizárólag olyan célközönségnek juttathatja el, akik nagyobb valószínűséggel fogyasztják azokat. Ezzel elkerülheti a teljes felhasználói bázis elárasztását levélszeméttel, és jobb megtartást biztosíthat.

#### <a name="m-commerce"></a>M-kereskedelem
Gyűjtse össze az alkalmazásban a legtöbbet megtekintett termékkategóriákat, és azon célközönség számára népszerűsítse az adott kategóriába tartozó kedvezményt vagy új terméket, amelynek végfelhasználói nagyobb eséllyel fogják azt megvásárolni. Célozza az árbevétel növelését. A cél most is a levélszemét elkerülése!

#### <a name="gaming"></a>Játékok
Gyűjtse össze a végfelhasználók játékszintjét és az adott időtartam alatt a játékkal töltött idő mennyiségét, hogy olyan célközönséget célozhasson meg, amely tagjai megakadhattak és várhatóan új szintre lépnének egy bónuszajánlat segítségével.

Kommunikáljon olyan ösztönzőkkel járó különleges eseményekről, amelyek az egy ideje már nem játszó felhasználóknak kedveznek, a visszatérésre bátorítva őket.

#### <a name="retail"></a>Kiskereskedelem
Gyűjtse össze azokat a termékeket és márkákat, amelyeket a közönség a kedvencei és viselkedése alapján nagyobb eséllyel fogyasztana, és irányítsa célközönségét az áruházba a vásárlási bevételek növeléséhez.

#### <a name="banking"></a>Banki szolgáltatások
Gyűjtsön adatokat azon végfelhasználóktól, akik az alkalmazás első indításakor hoztak létre fiókot. Törekedjen egy üdvözlési kampány indítására célzott leküldéses értesítésekkel, és növelje a fiók-előfizetések számát.

### <a name="how-to-create-a-great-tag-plan"></a>Hogyan hozhatunk létre remek címkézési tervet?
A címkézési tervnek tulajdonképpen a felhasználó által megtett út leírásának vagy az alkalmazás egyfajta munkafolyamatának kell lennie, meg kell adnia minden szükséges címkét (adatot), amelyek gyűjtésével és elemzésével elég információ áll rendelkezésre a felhasználók viselkedésének megértésére és a felhasználói bázis megfelelő szegmentálására. Ez nem műszaki jellegű feladat. A forgalmazók ezért a Mobile Engagement-stratégiájuk alapján adhatják meg a gyűjteni kívánt adatokat.

Egy alkalmazásban legalább a képernyőket (a Mobile Engagementben *tevékenységek* néven ismertek) el kell látni címkével. Ez segítséget nyújt a felhasználók által megtett út meghatározásában.

A tevékenység tartalmazhat *eseményeket*, amelyek műveletinformációkat gyűjtenek, ilyen például egy gombra való kattintás. Ez lehetővé teszi az alkalmazáson belüli interakciók gyűjtését. A forgalmazók így nyomon követhetik, hogy melyik képernyőt látogatják a felhasználók és milyen tevékenységet folytatnak.

`Jobs`időtartammal rendelkező műveletek. Ez nagyon hasznos a forgalmazók számára annak megértéséhez, hogy mennyi ideig tart a felhasználó számára a felhasználói fiók létrehozása vagy a bejelentkezés. Ez annak megfigyelésére is hasznos lehet a fejlesztők számára, hogy mennyi időt vesz igénybe egy webes szolgáltatás hívása.

`Errors`is megfigyelhetők annak meghatározására, hogy a felhasználók problémákba ütköznek-e az alkalmazással kapcsolatban. Például: gyakran előforduló kapcsolódási problémák.

Az összes ilyen típusú adat kiegészíthető paraméterekkel (a Mobile Engagementben *további információkkal*), ami lehetővé teszi dinamikus adatok gyűjtését az alkalmazásból. Ez a részletes szegmentáláshoz fontos. A forgalmazók szegmentálhatják például a felhasználókat az általuk fogyasztott tartalmak típusa alapján. A tartalom típusa lesz a tevékenység vagy esemény dinamikus információja.

Az *alkalmazásadatok* olyan adatok, amelyek lehetővé teszik az alkalmazás vagy a felhasználó állapotának valós idejű ellenőrzését. Ez segíti a közönségbázis gyors kategorizálását és megcélzását is. Az adatok használhatnak például egy igaz/hamis állapotot annak követésére, hogy a felhasználó bejelentkezik-e vagy sem, vagy mikor jár le az előfizetése.

#### <a name="example-of-tags"></a>Példa címkékre
*Használati eset: A célközönség viselkedésének szegmentálása a megfelelő végfelhasználók megfelelő leküldéses értesítési tartalmakkal való ellátására*

1. Leküldéses értesítés küldése egy adott termékkategória népszerűsítéséhez: Gyűjtsön viselkedési adatokat a célközönség szegmentálásához annak alapján, hogy egy bizonyos termékkategóriát hányszor tekintettek meg egy adott időtartam alatt vagy egy adott tételt hányszor adtak hozzá a kosárhoz. Az összegyűjtött adatok lehetővé teszik a szegmentálást, majd leküldéses értesítés küldését a megfelelő célközönségnek.
2. Alkalmazás értékelése: Gyűjtsön adatokat a célközönség közösségi hálózatokon megosztott tartalmai alapján. Célja a célközönség szegmentálása az alkalmazás *nagyköveteinek* meghatározásával. A nagykövetek az alkalmazás legjobb célközönsége, őket a legcélszerűbb arra kérni leküldéses értesítés küldésével, hogy 5 csillagos értékelést adjanak az alkalmazásnak.
   
   ![][1]

*Használati eset: Deklaratív adatok*

1. Szegmensnek szóló hírek: Gyűjtsön deklaratív adatokat a célközönség szegmentálásához a preferenciáik alapján. Ez lehetővé teszi leküldéses értesítések küldését egy olyan témával kapcsolatban, amely ténylegesen érdekel egy adott célközönséget.
2. A célközönség szegmentálása a bejelentkezési állapot alapján. Gyűjtsön adatokat annak meghatározására, hogy egy felhasználó csatlakozott-e vagy létrehozott-e fiókot. Ez segítséget nyújt azon végfelhasználókat megcélzásában, akik még nem jelentkeztek be, és leküldéses értesítést küld, amellyel a csatlakozásra bátorítja őket.
   ![][2]

### <a name="next-steps"></a>További lépések
* A Mobile Engagement fogalmait a [Mobile Engagement fogalmait] ismertető cikkben ismerheti meg bővebben.
* Keresse fel a [Mobile Engagement-alkalmazás létrehozását](mobile-engagement-create.md) ismertető cikket, amely egy új Mobile Engagement-alkalmazásgyűjteménynek az Azure-ban való létrehozását, valamint azt ismerteti, hogy hogyan kezdheti meg az alkalmazások kezelését a Mobile Engagement portál használatával.
* A részletekkel kapcsolatban keresse fel az [ajánlott eljárásokkal](mobile-engagement-getting-started-best-practices.md) foglalkozó cikket.
* Keresse fel a [játékalkalmazásról szóló forgatókönyvet](mobile-engagement-gaming-scenario.md) a Mobile Engagement minta játékalkalmazással való használatával kapcsolatos információkért. 
* Keresse fel a [médiaalkalmazásról szóló forgatókönyvet](mobile-engagement-media-scenario.md) a Mobile Engagement minta médiaalkalmazással való használatával kapcsolatos információkért. 
* Tekintse át az [oktatóanyagokat] a megvalósítással kapcsolatos további részletekért.

<!-- Images. -->
[1]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case1.png
[2]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case2.png

<!-- URLs. -->
[Mobile Engagement fogalmait]: http://azure.microsoft.com/documentation/articles/mobile-engagement-concepts/
[oktatóanyagokat]: http://azure.microsoft.com/documentation/articles/mobile-engagement-ios-get-started/

