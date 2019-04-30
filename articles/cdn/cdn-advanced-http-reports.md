---
title: Az Azure CDN fejlett HTTP-jelentések kihasználtságának statisztikai adatai elemzéséhez |} A Microsoft Docs
description: Ismerje meg, a Microsoft Azure CDN fejlett HTTP-jelentések létrehozása. Ezek a jelentések CDN tevékenység részletes tájékoztatást nyújt.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: c8cb4713e38ca0da610c687325f3810f57da2b26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216151"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Az Azure CDN fejlett HTTP-jelentések kihasználtságának statisztikai adatai elemzéséhez
## <a name="overview"></a>Áttekintés
Ez a dokumentum ismerteti a Microsoft Azure CDN fejlett HTTP jelentéskészítés. Ezek a jelentések CDN tevékenység részletes tájékoztatást nyújt.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Speciális HTTP-jelentések elérése
1. A CDN-profil panelje, kattintson a **kezelés** gombra.
   
    ![CDN-profil panelje kezelése gomb](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portálját.
2. A kurzort a **Analytics** lapfülre, majd mutasson a **speciális HTTP-jelentések** úszó menü.  Kattintson a **HTTP nagy Platform**.
   
    ![CDN felügyeleti portálját - jelentések Speciális menü](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Jelentésbeállítások jelennek meg.

## <a name="geography-reports-map-based"></a>Földrajzi hely jelentések (térkép-alapú)
Nincsenek öt jelentéseket, amelyek kihasználják a leképezés jelzi a régiók, amelyről a tartalmat kéri. Ezek a jelentések Világtérkép, Egyesült Államok térkép, Kanada térkép, térkép Európa és Ázsia Csendes-óceáni térkép.

Minden egyes térképes jelentés holtversenyben földrajzi entitások (azaz a országok, államok és tartományok) százalékos aránya, amelyek adott régióból származik, a találatok alapján. Ezenkívül egy térkép segítségével vizualizálhatja a helyek, amelyről a tartalmat kért biztosítunk. Fontos tudni. Ehhez minden egyes régió mértékű igény szerint az adott régióban tapasztalt színkódolás. Bárka árnyékolt régiók-tartalomra vonatkozó alacsonyabb igény szerint azt jelzik, miközben sötétebb régiók jelzi a tartalmak iránti kereslet magasabb szintű.

Részletes és sávszélesség-információk minden olyan régió esetében közvetlenül a térkép lentebb. Ez lehetővé teszi, hogy a találatok, a találatok aránya, az adatok teljes mennyisége teljes számának megtekintéséhez történik (gigabájtban), és minden olyan régió esetében átvitt adatok aránya. Megtekintheti a leírásukat minden ezeket a metrikákat. Végül ha az egérmutatót egy régió (azaz a ország, állam vagy megye), a nevét és a találatok régióban bekövetkezett százalékos megjelenik elemleírásként.

Egy rövid leírást az egyes földrajzi hely térképes jelentés lejjebb találja.

| Jelentés neve | Leírás |
| --- | --- |
| World Map |Ez a jelentés lehetővé teszi a világszerte igény szerint a CDN-tartalom megtekintése. Egyes országok a világ térképen jelzi, hogy az adott régióban származik a találatok színkódolt. |
| Egyesült Államok térkép |Ez a jelentés lehetővé teszi az Egyesült Államokban iránti igény a CDN-tartalom megtekintését. A színekkel állapotaiban végrehajtható ezen a térképen jelzi, hogy az adott régióban származik a találatok jelölve témájával. |
| Kanada térkép |Ez a jelentés lehetővé teszi az igény a CDN-tartalom megtekintését Kanadában. Minden tartományban a színekkel jelölve témájával százalékos aránya, amelyek adott régióból származik, a találatok jelzi ezen a térképen. |
| Európa-térkép |Ez a jelentés lehetővé teszi az igény a CDN-tartalom megtekintését Európában. Minden ország a színekkel jelölve témájával százalékos aránya, amelyek adott régióból származik, a találatok jelzi ezen a térképen. |
| Ázsia Csendes-óceáni térség térkép |Ez a jelentés lehetővé teszi az ázsiai iránti igény a CDN-tartalom megtekintését. Minden ország a színekkel jelölve témájával százalékos aránya, amelyek adott régióból származik, a találatok jelzi ezen a térképen. |

## <a name="geography-reports-bar-charts"></a>Földrajzi hely jelentések (sávdiagramok)
Nincsenek két további jelentéseket statisztikai megfelelően földrajzi hely, amelyek felső város és a Top országok. Ezek a jelentések rangsor város és ország, osztályban, ezekben a régiókban származó találatok száma szerint. Esetén az ilyen típusú jelentés létrehozásáról, a sávdiagram jelzi, a felső 10 városok vagy országok, amely a kért tartalom egy adott platformon keresztül. Ezen a sávdiagramon segítségével mérheti fel gyorsan a régiók, amelyek létrehozzák-tartalomra vonatkozó kérelmek számát vesszük figyelembe.

A bal oldalon a gráf (y tengely) azt jelzi, hogy hány találatok történt a megadott régióban. Közvetlenül a diagram (x tengely) alatt található egy címkét a felső 10 régióban.

### <a name="using-the-bar-charts"></a>A sávdiagramok használatával
* Ha a kurzort egy, a nevét és az összes régióban bekövetkezett a találatok elemleírásként megjelennek.
* A felső városok jelentés elemleírásában azonosítja egy város nevét, államot/megyét, és az ország kifejezés rövidítését jelöli.
* Nem sikerült meghatározni a város vagy régió (azaz a állam/megye), amelyből egy kérés származik, majd azt jelzik, hogy azok ismeretlen. Ha az ország, ismeretlen, akkor két kérdőjel (azaz??) fog megjelenni.
* Jelentések tartalmazhatják metrikáinak "Európa" vagy "Ázsiai és csendes-óceáni térség." Azok az elemek nem jelentenek a statisztikai információkat ezekben a régiókban összes IP-címet. Ehelyett csak kell alkalmazni az IP-címekről, futó Európa vagy Ázsia és csendes-óceáni helyett egy adott városban vagy ország kérelmekkel.

A sávdiagram létrehozásához használt adatok alá is megtekinthetők. Hiba található a találatok, a találatok aránya, adatmennyiség teljes száma (gigabájtban) történik, és az első 250 régióinak átvitt adatok aránya. Megtekintheti a leírásukat minden ezeket a metrikákat.

Az alábbi jelentések mindkét típusú biztosítunk egy rövid leírást.

| Jelentés neve | Leírás |
| --- | --- |
| Felső városok |Ez a jelentés városok, adja meg a találatok száma szerint rangsorolja a régió. |
| Leggyakoribb országok |Ez a jelentés országok, adja meg a találatok száma szerint rangsorolja a régió. |

## <a name="daily-summary"></a>Napi összesítés
A napi összegző jelentés lehetővé teszi teljes száma a találatok és a egy adott platformon keresztül naponta továbbított adatok megtekintését. Ez az információ segítségével gyorsan tesz különbséget az adatok CDN tevékenységi mintáit. Például ez a jelentés segítségével melyik nappal tapasztalt magasabb vagy alacsonyabb, mint a várt forgalom észleléséhez.

Esetén az ilyen típusú jelentés létrehozásáról, a sávdiagram biztosít egy visual tapasztalt platformspecifikus igény mennyisége feltüntetése naponta a jelentés által lefedett időszakon. Ez fog. Ehhez egy sáv megjelenítése a jelentésben szereplő minden egyes nap. Ha például kiválasztja az adott időszakban nevű "Elmúlt hét" egy oszlopdiagram a hét sávok hoz létre. Minden egyes sáv jelzi, hogy az adott napon tapasztalt találatok teljes száma.

A bal oldalon a gráf (y tengely) azt jelzi, hogy hány találatok történt a megadott dátum. Közvetlenül a diagram (x tengely) alatt található egy címke, amely azt jelzi, hogy a dátum (formátum: YYYY-MM-DD) szerepelnek a jelentésben minden nap.

> [!TIP]
> Ha a kurzort egy sáv, azon a napon bekövetkezett találatok száma elemleírásként jelenik meg.
> 
> 

A sávdiagram létrehozásához használt adatok alá is megtekinthetők. Hiba található a találatok teljes száma és a (gigabájtban) továbbított adatok mennyisége minden nap, a jelentés által lefedett.

## <a name="by-hour"></a>Óránként
A által óra jelentés lehetővé teszi, hogy a találatok és a egy adott platform átvitt óránként összesített számának megtekintéséhez. Ez az információ segítségével gyorsan tesz különbséget az adatok CDN tevékenységi mintáit. Például ez a jelentés segítségével az időszakok a nap folyamán, hogy a magasabb vagy alacsonyabb, mint a várt forgalom észleléséhez.

Esetén az ilyen típusú jelentés létrehozásáról, a sávdiagram platformspecifikus igény szerint a jelentés által lefedett időszakon óránként észlelt mennyisége visual feltüntetése biztosít. Akkor teszi ezt, egy sáv minden órában, a jelentés által lefedett megjelenítésével. Például kiválasztása egy 24 órás időszakban generál egy oszlopdiagram a huszonnégy sávok. Minden egyes sáv jelzi, hogy az adott órában tapasztalt találatok teljes száma.

A bal oldalon a gráf (y tengely) azt jelzi, hogy hány találatok száma az adott órában történt. Közvetlenül a diagram (x tengely) alatt található egy címke, amely azt jelzi, hogy a dátum/idő (formátum: YYYY-MM-DD óó: PP) minden egyes órában szerepelnek a jelentésben. Idő jelentett 24 órás formátumban, és az UTC/GMT időzóna használatával van megadva.

> [!TIP]
> Ha a kurzort egy sáv, órában fellépő találatok száma elemleírásként jelenik meg.
> 
> 

A sávdiagram létrehozásához használt adatok alá is megtekinthetők. Hiba található találatok száma és a (gigabájtban) továbbított adatok mennyisége az adott órában, a jelentés által lefedett.

## <a name="by-file"></a>A fájl
A fájl által a jelentés lehetővé teszi igény és a felmerült a leginkább kért eszközök platformon keresztül a forgalom mennyisége megtekintését. Esetén az ilyen típusú jelentés létrehozásáról, a sávdiagram jön létre, a felső 10 leginkább kért eszközök a megadott időszakra.

> [!NOTE]
> Ez a jelentés az alkalmazásában edge CNAME URL-címek URL-címeik azonos CDN konvertálja. Ez lehetővé teszi egy pontos tally a CDN- vagy peremtábla CNAME URL-cím kérésére, függetlenül az eszközhöz társított találatok teljes számát.
> 
> 

A bal oldalon a gráf (y tengely) azt jelzi, hogy az egyes eszközökre vonatkozó kérelmek száma a megadott időszakra. Közvetlenül a grafikon (x tengely) alatt található egy címke, amely azt jelzi, hogy a felső 10 kért objektumok mindegyike a fájl nevét.

A sávdiagram létrehozásához használt adatok alá is megtekinthetők. Nincs megtalálja az egyes felső 250 kért eszközök a következő információkat: relatív elérési út, a találatok, a találatok aránya, adatmennyiség száma történik (gigabájtban), és a továbbított adatok aránya.

## <a name="by-file-detail"></a>Fájl részletei
A szerint részletei jelentés igény és a egy adott eszközhöz egy adott platformon keresztül felmerült a forgalom mennyisége megtekintését teszi lehetővé. Ez a jelentés nagyon tetején a fájl részleteit a beállítás. Ez a beállítás a kiválasztott platformon a leginkább kért eszközök listáját tartalmazza. Annak érdekében, hogy a fájl részletei jelentés készítése, szüksége lesz, válassza ki a kívánt eszközt a fájl részleteit a beállításhalmazok. Utána sávdiagram fogja jelezni a napi igény szerint a megadott időszakban előállított mennyiségét.

A bal oldalon a gráf (y tengely) azt jelzi, hogy az eszköz egy bizonyos napon észlelt kérelmek teljes száma. Közvetlenül a diagram (x tengely) alatt található egy címke, amely azt jelzi, hogy a dátum (formátum: YYYY-MM-DD) mely CDN igény szerint az eszköz jelentett.

A sávdiagram létrehozásához használt adatok alá is megtekinthetők. Hiba található a találatok teljes száma és a (gigabájtban) továbbított adatok mennyisége minden nap, a jelentés által lefedett.

## <a name="by-file-type"></a>Fájltípus
A fájl típusa szerint-jelentés igény és a felmerült fájltípus forgalom mennyisége megtekintését teszi lehetővé. Esetén az ilyen típusú jelentés létrehozásához, egy fánkdiagramon találatok száma a felső 10 fájltípusokat által generált aránya jelzi.

> [!TIP]
> Ha a kurzort a fánkdiagram szeletet, az internetes média típusa, hogy fájltípus elemleírásként jelenik meg.
> 
> 

Alatta a fánkdiagram létrehozásához használt adatokat tekinthet meg. Hiba található a fájl neve bővítmény/internetes média típusa, a találatok teljes száma, a találatok, aránya (gigabájtban) továbbított adatok mennyisége, és minden, a felső 250 típusú átvitt adatok aránya.

## <a name="by-directory"></a>Címtár
A könyvtár által a jelentés lehetővé teszi igény és a egy adott címtárhoz tartalmat egy adott platformon keresztül felmerült a forgalom mennyisége megtekintését. Esetén az ilyen típusú jelentés létrehozásáról, a sávdiagram jelzi, találatok száma a felső 10 címtárat a tartalom által generált teljes száma.

### <a name="using-the-bar-chart"></a>A sávdiagram használatával
* A kurzort egy sáv a megfelelő könyvtár relatív elérési útjának megtekintéséhez.
* Egy könyvtár almappájában tárolt tartalmat nem számít, ha igény szerint kiszámításának Directory. Ehhez a számításhoz kizárólag támaszkodik a tényleges címtárban tárolt tartalom generált kérelmek száma.
* Ez a jelentés az alkalmazásában edge CNAME URL-címek URL-címeik azonos CDN konvertálja. Ez lehetővé teszi, hogy a CDN- vagy peremtábla CNAME URL-cím kérésére, függetlenül az eszközhöz társított összes statistics egy pontos tally.

A bal oldalon a gráf (y tengely) azt jelzi, hogy a teljes száma a felső 10 címtárakban tárolt tartalomhoz. A diagramon a sávok könyvtár jelöli. A color-coding séma használatával egy sáv ahhoz a könyvtárhoz, az első 250 teljes könyvtárak szakaszban felsorolt egyeztetni.

A sávdiagram létrehozásához használt adatok alá is megtekinthetők. Nincs megtalálja a következő információkat az első 250 könyvtárak mindegyike esetében: relatív elérési út, a találatok, a találatok aránya, adatmennyiség száma történik (gigabájtban), és a továbbított adatok aránya.

## <a name="by-browser"></a>Böngésző szerint
A böngésző által a jelentés megtekintéséhez a böngészők szolgáló tartalomkérelem teszi lehetővé. Kördiagram esetén az ilyen típusú jelentés létrehozásáról, a 10 leggyakoribb böngészők által kezelt kérések aránya jelzi.

### <a name="using-the-pie-chart"></a>A tortadiagram használatával
* A szelet megtekintéséhez a böngészőben neve és verziója a tortadiagram a kurzort.
* Ez a jelentés az alkalmazásában minden egyedi böngészőverzió kombináció számít egy másik böngészőben.
* Az "Egyéb" nevű szelet más böngészőkkel és verziók által kezelt kérések arányát jelzi.

A tortadiagram létrehozásához használt adatok alá is megtekinthetők. Hiba található a böngésző alkalmazástípus-verzió számát, a találatok száma és százalékos aránya a találatok az első 250 böngészők mindegyikéhez.

## <a name="by-referrer"></a>Hivatkozó szerint
A hivatkozó a jelentés lehetővé teszi, hogy a felső hivatkozók tartalom megtekintése a kiválasztott platformon. A hivatkozó azt jelzi, hogy az állomásnevet, amelyről a kérelmek jött létre. Sávdiagram esetén ez a jelentés típusú generálása, igény szerinti (azaz a találatok) a felső 10 hivatkozók által generált mennyisége jelzi.

A bal oldalon a gráf (y tengely) azt jelzi, hogy az eszköz az egyes hivatkozó ütközött kérelmek teljes száma. Minden egyes sávot a diagramon egy hivatkozó jelöli. Hasonlítsa össze a hivatkozó, az első 250 hivatkozó szakaszban felsorolt, egy sáv color-coding sémát használó.

A sávdiagram létrehozásához használt adatok alá is megtekinthetők. Hiba az URL-cím, a találatok teljes száma és százalékos aránya találatok száma a felső 250 hivatkozók minden egyes létrehozott lesz található.

## <a name="by-download"></a>Letöltés
A le a jelentés lehetővé teszi, hogy a legtöbbet kért tartalom letöltése mintáinak elemzése. A jelentés felső részén a sávdiagramot oszlopdiagramra cseréli, hogy összehasonlítja megkísérelt letöltések felső 10 kért eszközök befejezett frissítéseket tartalmazza. Sávok színkódolt megfelelően, hogy-e az megkísérelt letöltése (kék) vagy a letöltés befejeződött (zöld).

> [!NOTE]
> Ez a jelentés az alkalmazásában edge CNAME URL-címek URL-címeik azonos CDN konvertálja. Ez lehetővé teszi, hogy a CDN- vagy peremtábla CNAME URL-cím kérésére, függetlenül az eszközhöz társított összes statistics egy pontos tally.
> 
> 

A bal oldalon a gráf (y tengely) azt jelzi, hogy a felső 10 kért objektumok mindegyike a fájl nevét. Közvetlenül a diagram (x tengely) alatt található címkék, amelyek jelzik a letöltések megkísérelt/befejeződött teljes száma.

Közvetlenül a sáv diagram alatt a következő információkat fog szerepelni a felső 250 kért eszközök: relatív elérési út (beleértve a fájl neve), befejezését lett letöltve hányszor, a kért száma és százalékos aránya a teljes letöltési eredményező kérelmek.

> [!TIP]
> CDN-en nem szerez egy HTTP-ügyfél (pl. webböngésző) Ha az eszköz teljes mértékben letöltötte. Ennek eredményeképpen van alapján számítja ki, hogy az eszköz teljesen letöltötte állapotkódok és bájttartomány-kérelmeket. Áttekintjük az ehhez a számításhoz létrehozásakor először is, hogy eredményezi, a kérés 200 OK állapotkódot. Ha igen, majd áttekintjük, győződjön meg arról, hogy a teljes eszköz fedhetik bájttartomány-kérelmeket. Végül összehasonlítva a kért objektum mérete továbbított adatok mennyisége. Ha az átvitt adatok egyenlő vagy nagyobb, mint a fájl méretét és a bájttartomány-kérelmeket is, hogy az eszköz megfelelő, majd a találati számít egy teljes letöltési.
> 
> Ez a jelentés értelmező jellege miatt érdemes megtartani szem előtt az alábbi pontokat, amelyek megváltoztathatják a konzisztencia és a jelentés pontosságára.
> 
> * Forgalmi minták nem lehet tudták pontosan előre jelezni, amikor a felhasználó-ügynökök eltérően viselkednek. Ez eredményekhez vezethet letöltés befejeződött, amely 100 %-nál nagyobb.
> * Eszközök, amelyek kihasználják a progresszív letöltés HTTP, előfordulhat, hogy nem kell pontosan jönnek létre ez a jelentés. Felhasználók keresése egy videóban különböző helyre okozza.
> 
> 

## <a name="by-404-errors"></a>404-es hibát szerint
A 404-es hiba a jelentés lehetővé teszi, hogy azonosíthatja a legtöbb száma 404-es nem található állapotkódok által létrehozott tartalom típusát. A jelentés felső részén a sávdiagramot oszlopdiagramra cseréli, amelynek 404-es nem található állapotkódot adott vissza az első 10-eszközök tartalmazza. Ezen a sávdiagramon hasonlítja össze a kérelmek, a 404-es nem található állapotkódot adott eszközökön eredményező kérelmek teljes száma. Sávok színkódolt. Egy sárga sáv jelzi, hogy a kérést eredményezett a 404-es nem található állapotkódot szolgál. Egy piros színű sáv jelzi az eszköz kérelmek teljes száma szolgál.

> [!NOTE]
> Ez a jelentés az alkalmazásában vegye figyelembe a következőket:
> 
> * Találat állapotkód függetlenül az adott eszköz számára bármilyen kérelmet jelöl.
> * Edge CNAME URL-címek URL-címeik azonos CDN konvertálja. Ez lehetővé teszi, hogy a CDN- vagy peremtábla CNAME URL-cím kérésére, függetlenül az eszközhöz társított összes statistics egy pontos tally.
> 
> 

A bal oldalon a gráf (y tengely) azt jelzi, hogy a fájl nevét az egyes felső 10 kért eszközök, amelyek 404-es nem található állapotkódot eredményezett. Közvetlenül a diagram (x tengely) alatt található címkék, amelyek jelzik, hogy a kérelmek teljes számának és a 404-es nem található állapotkódot eredményező kérések száma.

Közvetlenül a sáv diagram alatt a következő információkat fog szerepelni a felső 250 kért eszközök: relatív elérési út (beleértve a fájlnév), a 404-es nem található állapotkódot, teljes száma, hogy az eszköz a kért, hányszor eredményező kérések száma és a 404-es nem található állapotkódot eredményező kérelmek aránya.

## <a name="see-also"></a>Lásd még
* [Az Azure CDN áttekintése](cdn-overview.md)
* [A Microsoft Azure CDN Szolgáltatásban valós idejű statisztikák](cdn-real-time-stats.md)
* [A rules engine használatával a HTTP alapértelmezés felülbírálása](cdn-rules-engine.md)
* [Edge-teljesítmény elemzése](cdn-edge-performance.md)

