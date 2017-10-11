---
title: "Használati statisztikák elemezheti Azure CDN speciális HTTP-jelentések |} Microsoft Docs"
description: "Ismerje meg, speciális HTTP-jelentések létrehozása a Microsoft Azure CDN szolgáltatás használata. Ezek a jelentések részletes információkkal CDN tevékenység."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 2dfbc046674b2da692f30c945aee3ea25ae524eb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Használati statisztikák elemezheti Azure CDN speciális HTTP-jelentések
## <a name="overview"></a>Áttekintés
Ez a dokumentum ismerteti a Microsoft Azure CDN speciális HTTP jelentéskészítés. Ezek a jelentések részletes információkkal CDN tevékenység.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Speciális HTTP-jelentések használata
1. A CDN-profil panelje, kattintson a **kezelése** gombra.
   
    ![CDN-profil panelje kezelése gomb](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    Megnyitja a CDN-felügyeleti portálon.
2. Vigye a **Analytics** lapra, és vigye a **speciális HTTP-jelentések** menü.  Kattintson a **HTTP nagy Platform**.
   
    ![CDN management portal - jelentések Speciális menüben](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    A jelentés beállítások jelennek meg.

## <a name="geography-reports-map-based"></a>A földrajzi jelentések (térkép-alapú)
Nincsenek öt jelentések hasznát a leképezés a régiók, amelyről a tartalmat kért jelzi. Ezek a jelentések a következők: globális térkép, az Amerikai Egyesült Államok térkép, Kanada térkép, Európa térkép és Ázsia Csendes-óceáni térkép.

Minden egyes térkép-alapú jelentés egyes holtversenyekben földrajzi entitások (azaz országok, állapotok és tartományok) régió származó találatok százaléka alapján. Emellett olyan térképet jelenítheti meg a helyeket, ahonnan a tartalmat kért segítségével valósul meg. Ehhez minden egyes régió igény szerint az adott régióban tapasztalt milyen mértékű színkódolás tudni. Bárka árnyékolt régiók a tartalom alacsonyabb igény szerint azt jelzik, amíg sötétebb régiók jelzi a tartalmak iránti igénye nagyobb mértékű.

Mindegyik régióhoz és a sávszélesség tájékoztatáshoz közvetlenül a térkép alább. Így megtekintheti a teljes száma a találatok, a találatok százaléka, a teljes adatmennyiség elküldése (gigabájtban), és mindegyik régióhoz átvitt adatok hány százalékát. Megtekintheti a leírásukat az egyes metrikákat. Végül ha egy régiót (azaz ország, állam vagy megye) mutat, nevét és az régióban történt találatok jelenik meg elemleírásként.

Egy rövid leírást az egyes földrajzi térképen-alapú jelentés alább.

| Jelentés neve. | Leírás |
| --- | --- |
| A globális térkép |Ez a jelentés lehetővé teszi a világ igény szerint a CDN-tartalom megtekintését. Az egyes országok Itt a színkódolás a világ térképen jelzi az adott régióban származó találatok. |
| Az Amerikai Egyesült Államok térkép |Ez a jelentés lehetővé teszi az Amerikai Egyesült Államokban a CDN-tartalom terhelését. Az egyes ezen a térképen jelzi az adott régióban származó találatok színekkel jelölve témájával. |
| Kanadában térkép |Ez a jelentés lehetővé teszi a CDN-tartalom iránti igény Kanada területén. Minden tartomány ezen a térképen jelzi az adott régióban származó találatok színekkel jelölve témájával. |
| Európa térkép |Ez a jelentés lehetővé teszi az Európai a CDN-tartalom terhelését. Az egyes országok ezen a térképen jelzi az adott régióban származó találatok színekkel jelölve témájával. |
| Ázsia Csendes-óceáni térkép |Ez a jelentés lehetővé teszi a CDN-tartalom iránti igény Ázsiában. Az egyes országok ezen a térképen jelzi az adott régióban származó találatok színekkel jelölve témájával. |

## <a name="geography-reports-bar-charts"></a>A földrajzi jelentések (sávdiagramok)
Két jelentés áll rendelkezésre további megfelelően geográfiai, statisztikai adatokat biztosító felső városokat és felső országokban. Ezek a jelentések rangsorolja városokat és országok, osztályban, azokban a régiókban származó találatok száma szerint. Az ilyen típusú jelentés generálásához, akkor a sávdiagram jelzi, a felső 10 várost vagy országok, amely a kért tartalom egy adott platform keresztül. A sávdiagram lehetővé teszi, hogy gyorsan felmérheti a régiókban, amelyek létrehozzák a legnagyobb száma a tartalomhoz.

A bal oldalon a grafikon (y-tengely) azt jelzi, hogy hány találatok történt a megadott régión belül. Közvetlenül a grafikon (x-tengely) alatt található a címke a felső 10 régióban.

### <a name="using-the-bar-charts"></a>A sávdiagramok használatával
* Ha a sáv mutat, a nevét és a régióban történt találatok száma jelenik meg elemleírásként.
* A hozzájuk tartozó a felső városokat jelentés nevét, az állam/megye és ország rövidítése által a város azonosítja.
* Vagy a régiót (azaz, az állam/megye), ahonnan a kérés származik nem határozható meg, majd azt jelzik, hogy azok ismeretlen. Ha az ország ismeretlen, akkor a két kérdőjel (azaz??), fog megjelenni.
* A jelentésben metrikák "Európa" vagy a "Ázsia/Csendes-óceáni térség." Ezen elemekre nem jogosultak a összes IP-azokban a régiókban statisztikai adatok megadása. Ehelyett csak vonatkoznak, amelyek helyezkednek el Európa vagy Ázsia/Csendes-óceáni ahelyett, hogy egy adott város vagy ország IP-címekről kérelmekkel.

Az adatok a sávdiagram létrehozásához használt alatt tekintheti meg. Nem található (gigabájtban) átvitt találatok, százalékos aránya a találatok száma, adatmennyiség teljes száma, és a felső 250 régiókhoz átvitt adatok hány százalékát. Megtekintheti a leírásukat az egyes metrikákat.

Rövid leírása az alábbi jelentések mindkét típusú valósul meg.

| Jelentés neve. | Leírás |
| --- | --- |
| Felső város tartozik. |Ez a jelentés városokat származó találatok száma szerint rangsorolja régió. |
| Felső országok |Ez a jelentés országokból származó találatok száma szerint rangsorolja régió. |

## <a name="daily-summary"></a>Napi összegzése
A napi összegző jelentés lehetővé teszi a találatok és a naponta egy adott platformon keresztül továbbított adatok teljes száma. Ez az információ segítségével gyorsan megfejteni a CDN tevékenység minták behatolók. Például a jelentés segítségével napokat tapasztalt magasabb vagy alacsonyabb, mint a várt forgalom észlelésére.

Esetén az ilyen típusú jelentés létrehozása, a sávdiagram nyújtják a platform-specifikus igény szerinti tapasztalt mennyisége visual feltüntetése naponta a jelentés által lefedett időszakon. Akkor lesz ehhez a sáv megjelenítése a jelentésben szereplő minden nap. Például az adott időszakban kiválasztásával neve "Elmúlt hét" hét sávokkal sávdiagram hoz létre. Minden egyes sáv jelzi az adott napon tapasztalt találatok teljes száma.

A bal oldalon a grafikon (y-tengely) azt jelzi, hogy hány találatok történt a megadott dátum. Közvetlenül a grafikon (x-tengely) alatt található dátumát címkéjét (formátum: éééé-hh-nn) a jelentésben szereplő minden nap.

> [!TIP]
> Ha az egérmutatót egy sávot, elemleírásként által történt a dátumnak a találatok száma jelenik meg.
> 
> 

Az adatok a sávdiagram létrehozásához használt alatt tekintheti meg. Nem található a találatok összesített száma és a (gigabájtban) átvitt adatok mennyisége minden nap, a jelentés által lefedett.

## <a name="by-hour"></a>Óránként
Az óra által a jelentés lehetővé teszi a találatok és a óránként egy adott platformon keresztül továbbított adatok teljes száma. Ez az információ segítségével gyorsan megfejteni a CDN tevékenység minták behatolók. Például a jelentés segítségével az időszakok a nap folyamán, hogy a magasabb vagy alacsonyabb, mint a várt forgalom észlelésére.

Az ilyen típusú jelentés generálásához, akkor sávdiagram platform-specifikus igény szerint a jelentés által lefedett időszakon óránként észlelt mennyisége visual feltüntetése ad meg. Azt fogja ehhez a sáv megjelenítése a jelentés által lefedett óránként. Például kiválasztása egy 24 órás időszakban hoz létre a sávdiagram erdőtopológia kezelésre négy sávokkal. Minden egyes sáv jelzi, hogy az óra során tapasztalt találatok teljes száma.

A bal oldalon a grafikon (y-tengely) azt jelzi, hogy hány találatok történt a megadott órát. Közvetlenül a grafikon (x-tengely) alatt található a címke, amely jelzi a dátum/idő (formátum: éééé-hh-nn óó: PP) számára minden órában szerepelnek a jelentésben. Időt jelenti a 24 órás formátumban, és UTC/GMT időzónájának használatával van megadva.

> [!TIP]
> Ha az egérmutatót egy sávot, adott óra során bekövetkezett találatok száma elemleírásként jelenik meg.
> 
> 

Az adatok a sávdiagram létrehozásához használt alatt tekintheti meg. Nincs megtalálja a találatok összesített száma és a (gigabájtban) átvitt adatok mennyisége az óránként, a jelentés által lefedett.

## <a name="by-file"></a>Fájl segítségével
A fájl által a jelentés lehetővé teszi igény szerint és a legtöbbek által kért eszközök platformon keresztül felmerülő forgalom mennyisége. Az ilyen típusú jelentés generálásához, akkor a sávdiagram jön létre, a felső 10 leginkább kért eszközök a megadott idő alatt.

> [!NOTE]
> Ez a jelentés az alkalmazásában peremhálózati CNAME URL-címeket az egyenértékű CDN URL-címek alakulnak. Ez lehetővé teszi egy pontos egyeztetési találatok kapcsolódó a CDN és a peremhálózati CNAME URL-cím kérésére, függetlenül az eszköz teljes számát.
> 
> 

A bal oldalon a grafikon (y-tengely) azt jelzi, az egyes eszközökre vonatkozó kérelmek száma a megadott idő alatt. Közvetlenül a grafikon (x-tengely) alatt található a címke, amely minden a felső 10 kért eszközhöz a fájl nevét jelzi.

Az adatok a sávdiagram létrehozásához használt alatt tekintheti meg. Nem található a következő információkat az egyes a felső 250 kért eszközök: relatív elérési útja, a találatok, százalékos aránya a találatok száma, adatmennyiség száma elküldése (gigabájtban), és a továbbított adatok hány százalékát.

## <a name="by-file-detail"></a>Fájl részletei
A által részletei jelentés lehetővé teszi igény szerint és a forgalmat egy adott eszközhöz egy adott platformon keresztül felmerülő összege. A jelentés legfelső szintjén a részletek a fájl beállítás. Ezt a beállítást a kiválasztott platformon a legtöbbek által kért eszközök listáját tartalmazza. Ahhoz, hogy által részletei jelentés készítése, szüksége lesz a részletek a fájl lehetőséget válassza ki a kívánt eszköz. Amely után a sávdiagram azt fogja jelezni, ami akkor jön létre, a megadott időszakra napi igény mennyisége.

A bal oldalon a grafikon (y-tengely) azt jelzi, hogy egy eszköz észlelt egy adott napon kérelmek teljes száma. Közvetlenül a grafikon (x-tengely) alatt található dátumát címkéjét (formátum: éééé-hh-nn) mely CDN az eszköz igény szerint történt.

Az adatok a sávdiagram létrehozásához használt alatt tekintheti meg. Nem található a találatok összesített száma és a (gigabájtban) átvitt adatok mennyisége minden nap, a jelentés által lefedett.

## <a name="by-file-type"></a>Fájltípus
A fájl típus szerint a jelentés lehetővé teszi igény szerint és a fájl típusa felmerült forgalom mennyisége. Az ilyen típusú jelentés generálásához, akkor fánk diagram százalékos aránya a felső 10 fájltípusokat által generált találatok jelzi.

> [!TIP]
> Ha a fánk diagram szelet mutat, Internet médiatípusa, hogy megjelenik-e a fájl típusa elemleírásként.
> 
> 

Az adatok a fánk diagram létrehozásához használt alatt tekintheti meg. Nem található a fájl neve bővítmény/Internet adathordozó-típus, a találatok összesített száma, a találatok, aránya (gigabájtban) átvitt adatok mennyiségét, és minden, a felső 250 típusú átvitt adatok hány százalékát.

## <a name="by-directory"></a>Directory
A Directory által a jelentés lehetővé teszi igény szerint és a tartalmat egy adott könyvtár platformon keresztül felmerülő forgalom mennyisége. A sávdiagram esetén az ilyen típusú jelentés generálásához, felső 10 könyvtárak tartalom állítja elő a találatok száma jelzi.

### <a name="using-the-bar-chart"></a>A sávdiagram használatával
* A sáv a megfelelő könyvtár relatív elérési útjának megtekintéséhez mutasson.
* Egy könyvtár almappájában tárolt tartalmat nem számít, igény szerinti Directory számításakor. A számítás kizárólag támaszkodik a tényleges címtárban tárolt tartalom létrehozott kérelmek száma.
* Ez a jelentés az alkalmazásában peremhálózati CNAME URL-címeket az egyenértékű CDN URL-címek alakulnak. Ez lehetővé teszi egy pontos egyeztetési az összes kapcsolódó a CDN és a peremhálózati CNAME URL-cím kérésére, függetlenül az eszköz statisztikáját.

A bal oldalon a grafikon (y-tengely) azt jelzi, hogy a felső 10 címtárakban tárolt tartalmat kérelmek teljes száma. A diagram minden sávján egy könyvtárat jelöl. A color-coding séma segítségével egy sáv felső 250 teljes könyvtárak szakaszában felsorolt könyvtárra felel meg.

Az adatok a sávdiagram létrehozásához használt alatt tekintheti meg. Nem található a következő információkat az egyes a felső 250 könyvtárak: relatív elérési útja, a találatok, százalékos aránya a találatok száma, adatmennyiség száma elküldése (gigabájtban), és a továbbított adatok hány százalékát.

## <a name="by-browser"></a>Böngésző
A böngésző által a jelentés lehetővé teszi a tartalmat igénylő használt böngészők felmérésére. A kördiagram esetén az ilyen típusú jelentés generálásához, a felső 10 böngészők által kezelt kérelem azon százaléka jelzi.

### <a name="using-the-pie-chart"></a>A tortadiagram használatával
* A kördiagram megtekintése a böngészőben neve és verziója szelet mutasson.
* Ez a jelentés alkalmazásában minden egyedi böngészőverzió kombinációja tekinthető egy másik böngészőben.
* A szelet "Egyéb" nevű azt jelzi, hogy minden más böngészők és verziók által kezelt kérelem azon százaléka.

Az adatok a kördiagram létrehozásához használt alatt tekintheti meg. Nem található a böngésző típus-/ verziószám, a találatok összesített száma és a találatok aránya az egyes a felső 250 böngészők.

## <a name="by-referrer"></a>Hivatkozó által
A hivatkozó által jelentés lehetővé teszi, hogy a felső hivatkozó kérelmei tartalom megtekintése a kiválasztott platformon. A hivatkozó azt jelzi, hogy az állomásnevet, ahonnan a kérés jött létre. Esetén az ilyen típusú jelentés létrehozásakor, a sávdiagram igény szerinti (azaz a találatok) a felső 10 hivatkozó kérelmei által generált mennyisége jelzi.

A bal oldalon a grafikon (y-tengely) azt jelzi, hogy egy eszköz észlelt az egyes hivatkozó kérelmek teljes száma. A diagram minden sávban a hivatkozó jelöli. A color-coding séma használatával a menüsávon a hivatkozó, a felső 250 hivatkozó szakaszban felsorolt felel meg.

Az adatok a sávdiagram létrehozásához használt alatt tekintheti meg. Nem található az URL-cím, a találatok összesített száma és a felső 250 hivatkozó kérelmei generált találatok százaléka.

## <a name="by-download"></a>Letöltés
A le a jelentés lehetővé teszi a legtöbbek által kért tartalom letöltése minták elemzéséhez. A jelentés a legfontosabb, hogy az hasonlítja össze a felső 10 kért eszközök befejezett letöltött letöltések megkísérelt sávdiagram tartalmazza. Minden egyes itt a színkódolás megfelelően, hogy-e az megkísérelt letöltése (kék) vagy egy befejezett letöltés (zöld).

> [!NOTE]
> Ez a jelentés az alkalmazásában peremhálózati CNAME URL-címeket az egyenértékű CDN URL-címek alakulnak. Ez lehetővé teszi egy pontos egyeztetési az összes kapcsolódó a CDN és a peremhálózati CNAME URL-cím kérésére, függetlenül az eszköz statisztikáját.
> 
> 

A bal oldalon a grafikon (y-tengely) azt jelzi, hogy a fájl nevét az egyes a felső 10 kért eszközök. Közvetlenül a grafikon (x-tengely) alatt található címkékhez, amelyek próbált/befejezett letöltések teljes számát jelzi.

Közvetlenül alatt a sávdiagram, az alábbi információk jelennek meg a felső 250 kért eszközök: relatív elérési út (beleértve a fájl nevét), hányszor befejezését letöltött, kérték ennyiszer és eredményező teljes letöltési kérelem azon százaléka.

> [!TIP]
> A CDN nem arról tájékoztatja a HTTP-ügyfél (pl. webböngésző) Ha az eszköz teljesen letöltötte. Ennek eredményeképpen kell kiszámítani, hogy az eszköz teljesen fel van töltve megfelelően állapot- és bájttartomány kérelmeket. Elsőként azt keresse meg a számítási meghozásakor, hogy eredményezi, a kérés 200 OK állapotkódot. Ha igen, akkor tekintse meg bájttartomány kérelmeket annak érdekében, hogy a teljes eszköz terjed ki. Végül azt hasonlítsa össze a kért objektum mérete átvitt adatok mennyiségét. Ha az átvitt adatok egyenlő vagy nagyobb, mint a fájlméret és a bájttartomány kérések használata az adott eszköz számára megfelelő, majd a találati megszámlálandó teljes letölthető.
> 
> Ez a jelentés interpretive jellege miatt kell figyelembe venni az alábbiakat, amelyek megváltoztathatják a konzisztencia és a jelentés pontosságára.
> 
> * Forgalmi minták nem tudták pontosan előre jelezni, amikor a felhasználó-ügynökök eltérően viselkednek. Ez nagyobb, mint 100 %-os befejezett letöltési eredményeket hozhat.
> * HTTP progresszív letöltés előnyeit eszközök nem fogják pontosan képviselheti Ez a jelentés. Ez a videó különböző helyre kérő felhasználók okozza.
> 
> 

## <a name="by-404-errors"></a>404-es hibák által
A 404-es hibák által a jelentés lehetővé teszi a tartalomtípushoz, amely hoz létre a legtöbb 404-es nem található állapotkódok száma azonosítását. A jelentés felső tartalmazza a sávdiagram az első 10-eszközök, amelynek a 404-es nem található állapotkódot adott vissza. A sávdiagram hasonlítja össze, amelyek a 404-es nem található állapotkódja azok az eszközök által érintett kérelmek teljes száma. Minden itt a színkódolás. Egy sárga sáv jelzi, hogy a kérelem a 404-es nem található állapotkód eredményezte szolgál. Egy piros sáv segítségével jelzi az eszköz kérelmek teljes száma.

> [!NOTE]
> Ez a jelentés alkalmazásában vegye figyelembe a következőket:
> 
> * Találat függetlenül állapotkód eszköz kérésének jelöli.
> * Peremhálózati CNAME URL-címeket az egyenértékű CDN URL-címek lesznek átalakítva. Ez lehetővé teszi egy pontos egyeztetési az összes kapcsolódó a CDN és a peremhálózati CNAME URL-cím kérésére, függetlenül az eszköz statisztikáját.
> 
> 

A bal oldalon a grafikon (y-tengely) azt jelzi, hogy a fájl nevét az egyes felső 10 kért eszközök 404-es nem található állapotkód eredményezett. Közvetlenül a grafikon (x-tengely) alatt található címkékhez, amelyek jelzik, hogy a kérelmek teljes száma és a 404-es nem található állapotkód eredményező kérelmek számát jelenti.

Közvetlenül alatt a sávdiagram, az alábbi információk jelennek meg a felső 250 kért eszközök: relatív elérési út (beleértve a fájl nevét), a 404-es nem található állapotkód eredményezett kérelmek száma, a teljes száma az eszköz a kért és 404-es nem található állapotkód eredményező kérelem azon százaléka.

## <a name="see-also"></a>Lásd még:
* [Az Azure CDN áttekintése](cdn-overview.md)
* [A Microsoft Azure CDN valós idejű statisztikák](cdn-real-time-stats.md)
* [A szabályok használata alapértelmezett HTTP működés felülbírálata](cdn-rules-engine.md)
* [Peremhálózati teljesítményének elemzése](cdn-edge-performance.md)

