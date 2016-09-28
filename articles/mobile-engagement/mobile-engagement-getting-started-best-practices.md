<properties 
    pageTitle="Azure Mobile Engagement – Első lépések útmutató ajánlott eljárásokkal"
    description="Első lépések útmutató az Azure Mobile Engagement használatának megkezdéséhez és ajánlott eljárások a bevezetéshez" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="wesmc7777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="07/07/2016"
    ms.author="wesmc;ricksal"/>


# Azure Mobile Engagement – Első lépések útmutató ajánlott eljárásokkal

## Áttekintés

**A mobileszközök képernyője nagyon zsúfolt terület:** Egy tanulmány szerint 2013-ban 27 alkalmazást telepítettek egy átlagos mobileszközre. A felhasználók átlagosan havonta 30 órát foglalkoztak az alkalmazásaikkal. Ezen idő nagy részét a közösségi hálózatok és a játékok vitték el (mintegy 20 órát). 2014-re az Android piacterén a felhasználók körülbelül 1,5 millió alkalmazás közül választhattak. Az Apple áruházában hozzávetőlegesen 1,2 millió alkalmazás volt elérhető. A növekvő piacon versengő fejlesztőknek köszönhetően a mobilalkalmazások használata továbbra is növekvő tendenciát mutat. 

Az átlagos mobilfelhasználó nagy gyakorisággal telepít és távolít el alkalmazásokat a változó érdeklődéstől és az alkalmazáson belüli élményektől függően. Egy alkalmazás sikerességének meghatározásához manapság már többet kell tudni az alkalmazást telepítő felhasználók puszta számánál. Fontos tudni, hogy mennyire hasznos az alkalmazás, illetve hogy használati trendje változik-e. Az alábbi kérdések váltak fontossá:

- Elkezdték a felhasználók kevésbé érdekesnek vagy elavultnak tartani az alkalmazást? 
- Hány felhasználó hagyta abba teljesen az alkalmazás használatát? 
- Növekszik vagy csökken az alkalmazáson belüli vásárlások száma?
- A felhasználók az alkalmazással kapcsolatban felmerült problémák vagy az érdeklődés hiánya miatt nem fejezik be a munkafolyamatokat? 
- Sikerült megőrizni az alkalmazás hasznosságát és fontosságát friss tartalmaknak a felhasználói számára történő biztosításával? 
- Ezek a friss tartalmak azonosak minden felhasználó számára, vagy az egyes felhasználói szegmensekre összpontosítanak az alkalmazáson belüli viselkedés alapján? 
 
A fentiekhez hasonló kérdések megválaszolása hozzájárulhat az alkalmazás élettartamának és az alkalmazásból származó bevételeknek a növeléséhez. Emellett segíthetnek a felhasználói bázis meghatározásában és megtartásában is. 

Általában a médiához köthető alkalmazások rendelkeznek a legmagasabb felhasználómegtartási aránnyal. Ennek az egyik oka az, hogy folyamatosan friss tartalmakat nyújtanak a felhasználóknak. A hasznos, adott felhasználói szegmensnek szóló leküldéses értesítések használatának korai bevezetése általában nagy hatással van az alkalmazás megtartására. 

Az Azure Mobile Engagement program az alkalmazás élettartamának és megtartási arányának növeléséhez nyújt segítséget az alkalmazás használatával kapcsolatos részletes információkat gyűjtő és elemző módszer biztosításával. Segítséget nyújt a felhasználói bázis viselkedés alapján történő besorolásában, és az azonosított felhasználói szegmenseknek szánt leküldéses értesítéseket és alkalmazáson belüli üzeneteket küldő, célzott kampányok létrehozásában. A fő teljesítménymutatók (KPI) mérik, hogy a felhasználók mennyire aktívak az alkalmazás különböző aspektusainak a vonatkozásában. Az Azure Mobile Engagement ezen KPI-k meghatározásához biztosítja a szükséges módszereket. Az alkalmazást használó felhasználók aktivitásának növeléséhez szükséges infrastruktúra biztosításával hozzájárul a befektetés megtérülésének (ROI) a növeléséhez. 

Az Azure Mobile Engagement nyújtotta előnyök lehető legjobb kihasználásához először egy jól kidolgozott felhasználóbevonási tervvel kell rendelkeznie. A terv segíteni fog a felhasználói bázis szegmentálásához szükséges részletes adatok azonosításában. Ez alapulhat a viselkedésen és az alkalmazáson belüli élményeken. A terv sikeres megvalósításához ajánlott eljárás azon KPI egyértelmű meghatározása, amely mérni fogja az alkalmazás célkitűzéseit. Az egyértelműen meghatározott teljesítménymutatók révén könnyedén építheti be az alkalmazásba a részletes adatok gyűjtéséhez szükséges logikát, amelynek használatával elemezni és értékelni fogja a KPI-ket. Ez a témakör ajánlott eljárásokat tartalmazó útmutató a bevonási tervvel használt KPI-k meghatározásához. 


## 1. lépés: A BET-modellhez illeszkedő KPI-k meghatározása


A KPI-k megfelelő meghatározása nehéz feladat lehet. A különböző iparágak számára tervezett alkalmazások eltérő jellemzőkkel és célkitűzésekkel bírnak. Ez zavarólag hathat a megközelítésre. Ennek elkerülése érdekében a célkitűzéseket és a KPI-ket három fő kategóriába kell sorolni: **üzleti** (business), **aktivitási** (engagement) és **műszaki** (technical). Ezt hívjuk **BET-modellnek**.

Egy jó terv általában olyan célkitűzéseket tartalmaz, amelyek KPI mutatói a BET-modell mindegyik alábbi kategóriájában mérik a sikerességet.


#### Üzleti KPI-k

Az üzleti KPI-k meghatározása a legegyszerűbb feladat. Ezeket valamilyen formában valószínűleg már meghatározta, amikor megtervezte a mobilalkalmazást. Ezek a KPI-k általában az alkalmazáshoz kapcsolódó bevételek és ROI méréséhez nyújtanak segítséget. Az alábbi listában néhány példa látható azon üzleti KPI-kre, amelyeket útmutatásként használhat a teljesítménymutatók meghatározásához:

- Médiához kapcsolódó üzleti KPI-k
    - Reklámkattintások száma
    - Oldallátogatások száma felhasználónként
    - Az aktuális előfizetések száma
- Játékokhoz kapcsolódó üzleti KPI-k
    - Alkalmazáson belüli vásárlások száma
    - Átlagos bevétel felhasználónként (ARPU)
    - Alkalmanként játékkal töltött idő
    - Játékkal töltött napok száma és a játékon belüli aktuális szint
- E-kereskedelemhez kapcsolódó üzleti KPI-k
    - Alkalmazás használati napjainak száma
    - Átlagos bevétel felhasználónként (ARPU)
    - Átlagos összeg a kosárban a fizetéskor
    - A legtöbb megtekintést és vásárlást hozó termékkategória
- Banki és biztosítási szolgáltatásokhoz kapcsolódó üzleti KPI-k
    - Számlák száma
    - Aktivált szolgáltatások száma
    - Felkeresett ajánlati oldalak száma
    - Kattintott vagy aktivált figyelmeztetések száma      



#### Bevonási KPI-k

A bevonási KPI olyan teljesítménymutató, amely a felhasználók aktivitásának a mérésére szolgál. A terület trendjei segítenek az alkalmazás megtartásának a meghatározásában. Alább néhány példát láthat az ilyen típusú KPI-kre:

- Aktív felhasználók az elmúlt 7 napban
- Inaktív felhasználók száma az elmúlt 7 napban
- Azon felhasználók száma, akik 30 napja nem használták az alkalmazást  

Bizonyos nyilvánvaló külső tényezők befolyásolhatják a terület mutatóit. Egy mobileszközről például feltételezhető, hogy mindig a felhasználóval van. Ez nem feltétlenül igaz. A játékalkalmazásokat általában többet használják az ünnepek során, amikor a játékosok többet játszanak, mert nem kell munkába vagy iskolába menniük.   

A kategória jól meghatározott KPI mutató segítséget nyújtanak az alkalmazás és az ügyfelek közötti kapcsolat mérésében.



#### Műszaki KPI-k

A kategória teljesítménymutató annak meghatározásában segítenek, hogy az alkalmazás megfelelően működik, nem válaszol vagy összeomlik. A mutatók mérhetik az alkalmazás állapotát, és meghatározhatnak olyan használati problémákat, amelyek megakadályozhatják a felhasználókat az alkalmazás használatában. A kategóriában gyűjtött információk olyan teljesítményadatokat is tartalmazhatnak, amelyek a marketingcsapatok számára is fontosak lehetnek. Az adatok hasznosak lehetnek az informatikai és támogatási csapatok általi hibaelhárításhoz a nem jelentett hibák azonosítása céljából. 
 
Néhány példa a műszaki KPI-kre:

- A nem kezelt vagy kezelt kivételek adatai és száma 
- Az utolsó összeomlás időbélyegzője
- A gomb, amelyre utoljára kattintottak, vagy az utoljára felkeresett oldal
- Az alkalmazás memóriahasználata
- Az alkalmazás képkockasebessége
- Azon operációs rendszer verziója, amelyen az alkalmazás fut
- Az alkalmazás verziója

Ezen KPI-k meghatározása segítséget nyújthat az alkalmazás teljesítményének méréséhez és a lehetséges hibák kiszűréséhez. A mutatók csökkenthetik az ügyfeleknek készített javítások létrehozásához szükséges időt. Emellett segíthetnek az olyan felhasználói szegmensek meghatározásában, amelyek adott problémákkal szembesültek. A felhasználók szegmentálását olyan kampányok létrehozására is használhatja, amelyek az elérhető frissítésekkel vagy lehetséges promóciókkal kapcsolatos értesítéseket küldenek, segítve az ügyfelek elégedettségének a helyreállítását. 


#### Az útmutató 1. feladata: A KPI-irányítópult létrehozása

A marketingstratégia meghatározásakor a KPI-knek mindegyik fő célkitűzést képviselniük kell. Egyértelműen meghatározott adatpontokat kell használni az alkalmazás működésének és a végfelhasználók viselkedésének a megfigyeléséhez szükséges alapvető fontosságú információk gyűjtéséhez.

Hozzon létre olyan KPI-irányítópultot, amely az alábbi információkat tartalmazza

1.  Melyek az alkalmazás KPI-jei?
2.  Mely adatpontok fogják képviselni az egyes KPI-ket?
3.  Hol találhatók ezen adatok az alkalmazásban (például képernyő, beállítások, rendszer stb.)?
4.  Képes vagy szimulálni egy bevonási folyamatot ezen KPI vonatkozásában?

További példákat és útmutatást az [alkalmazástervezési útmutató][alkalmazástervezési útmutatóra mutató hivatkozás] **KPI Builder** (KPI-tervező) munkalapján talál.



## 2. lépés: Bevonási program


Az alkalmazás egyik legfontosabb összetevőjeként kell tekinteni a kiváló bevonási programra. Ennek mindenképpen tartalmaznia kell egy nagyszerű üdvözlőprogramot, amelyet az alkalmazás használatának első napjaiban használhatnak a felhasználók. Ennek általában nagyon pozitív hatása van az alkalmazás felhasználók bevonására és megtartására. A tanulmányok kimutatták, hogy a telepítést követően a felhasználók többsége néhány napon belül abbahagyja egy adott alkalmazás használatát. Ezért törekedni kell a felhasználók elvárásainak a teljesítésére vagy felülmúlására, ami idejekorán növeli az érdeklődést, amikor a felhasználó még az alkalmazásra koncentrál. Mindenképpen mutassa be az alkalmazás legfontosabb értékeit és előnyeit a felhasználóknak. 


![](./media/mobile-engagement-getting-started-best-practices/unsegmented-push-notifications.png)

A leküldéses értesítés a legjobb módja a mobileszközök felhasználóinak korai bevonására. Nagy gondot kell fordítani azonban a felhasználók szegmentálására a leküldéses értesítések tekintetében. Amint egy felhasználó úgy érzi, hogy levélszemetet vagy érdektelen értesítéseket kap, annak súlyos hatása lehet. A felhasználó mindössze néhány kattintással törölheti az alkalmazást, és sosem tér vissza. A felhasználónak nagy mértékben testre szabott, alkalmazáson belüli értéket kell kapnia általános levélszemét helyett.

Ha a felhasználók bevonása sikeres volt, a bevonási program hozzájárulhat az alkalmazás sikerességének előmozdításához más szempontokból is.

Létrehozhat például egy kampányt, amely megkéri az aktív felhasználókat, hogy értékeljék az alkalmazást. Mivel ez a felhasználói szegmens a legaktívabb és rendelkezik a legtöbb tapasztalattal az alkalmazással kapcsolatban, vélhetően a legpontosabb értékelést fogja adni. Ha az alkalmazás kiváló értékeléseket kap, az segíthet az organikus letöltések számának növelésében, csökkentve az új felhasználók megszerzésének költségeit.



#### Bevonási folyamat


A globális bevonási program különböző bevonási folyamatokat tartalmaz. Minden folyamat több célkitűzés elérésére törekszik.


###### Életciklushoz kapcsolódó leküldések folyamata


Az életciklushoz kapcsolódó leküldések folyamatának célkitűzései attól függően különböznek egymástól, hogy a felhasználó mennyire aktívan használja az alkalmazást. Egy adott felhasználó lehet új, inaktív vagy nagyon aktív. A bevonási életciklus különböző szakaszaiban a felhasználók számára előnyös lehet a tippek vagy dokumentációra mutató hivatkozások formájában kapott friss tartalom. 

Az új felhasználóknak például segítségre lehet szükségük az alkalmazás megismeréséhez, vagy az alkalmazás első indításakor élhetnek az új felhasználókat célzó olyan ösztönzők előnyeivel, mint például az alábbi.

*„Örülünk, hogy csatlakozott hozzánk! Ne felejtsen el bejelentkezni az 1. első ingyenes hónap igénybe vételéhez!”*


###### Viselkedéshez kapcsolódó leküldések folyamata

A viselkedéshez kapcsolódó leküldések folyamatának célja a használat növelése az alkalmazás számára gyűjtött felhasználói viselkedés alapján.  

Egy képzeletbeli futballalkalmazás nagyon aktív felhasználójának előnye származhat például abból, ha az alábbi leküldéses értesítésnek megfelelően cselekszik.

*„John, Ön komoly futballrajongó! Jelentkezzen be az NFL-oldalunkra, és ingyenes belépőt nyerhet a Super Bowlra!”*


###### Értesítésekhez kapcsolódó leküldések folyamata

A felhasználók értékelni fogják az érdeklődésüknek megfelelő híreket. Az értesítésekhez kapcsolódó leküldések folyamata a felhasználóval kapcsolatban által egyértelműen kimutatott érdeklődések alapján küldött értesítésekkel növeli a felhasználó aktivitását. Az történhet közvetlenül, ha a felhasználó megadja érdeklődési köreit az alkalmazásban. Ugyanakkor meghatározható közvetetten is, a felhasználó és az alkalmazás közötti interakció során gyűjtött adatok alapján.

Egy e-kereskedelmi alkalmazás felhasználója rendszeresen vásárol például egy adott márkájú kávét, amit egy üzleti KPI használatával rögzít. Az alábbi értesítéssel így növelhető a felhasználó alkalmazáshasználata.
 
*„Üdvözöljük, Wes! Az egyik kedvenc kávémárkája 2015 szeptemberének első hetében 25%-os kedvezménnyel lesz megvásárolható. Nagyra értékeljük Önt ügyfelünkként, és ezzel az értesítéssel szeretnénk tájékoztatni az akcióról.”*

###### Megtartáshoz kapcsolódó leküldések folyamata

Ezen folyamat célja a felhasználók megtartása ismétlődő leküldéses értesítéses kampányokkal, amelyek révén az alkalmazás használata szokássá válhat. Ezzel az alkalmazás megtartása növelhető, ha a felhasználó élvezi az interakciókat. 

Egy sporttal kapcsolatos alkalmazás felhasználója például a következő leküldéses értesítést kaphatja meg minden héten a kedvenc csapatairól:

*„Akár 200 pontot is nyerhet, ha szavaz arra, hogy a New York Yankees legyőzi-e eheti mérkőzésén a Toronto Blue Jayst!”*


#### A 3W megközelítés

A különböző leküldéses folyamatok elsajátítása segíteni fogja a kapcsolattartást a végfelhasználókkal. Ugyanakkor a 3W megközelítést kell használnia az értesítések testre szabásához. A 3W megközelítésnek minden értesítés esetén meg kell adnia arra, hogy kinek, mit és mikor (Who, What, When). Ha egyértelműen megválaszolja ezt a három kérdést, az értesítései megfelelően fognak összpontosítani a bevonásra.

![](./media/mobile-engagement-getting-started-best-practices/who-what-when.png)



###### Ki: Azon felhasználói szegmens, amely az üzeneteket fogja kapni

Az értesítések felhasználóknak történő leküldését nagyon érzékeny kommunikációs csatornaként kell kezelni. Győződjön meg róla, hogy a felhasználói szegmensnek küldeni kívánt értesítések megfelelnek a szegmens érdeklődési köreinek. A helytelenül célzott értesítés jó eséllyel rossz hatással lesz a felhasználóra. Levélszemétnek tekintheti, ami az alkalmazás eltávolításához vezethet. 

Használja adott technikai és viselkedési feltételek kombinációját, amikor meghatározza azt a felhasználói szegmenset, amely az értesítéseket meg fogja kapni. A felhasználói szegmens meghatározásának egyszerű példája az alábbi állításhoz lehet hasonló:

„Minden felhasználó, aki 3 nappal ezelőtt nyitotta meg először a mobilalkalmazást, és kétszer látogatott el a bejelentkezési oldalra anélkül, hogy ténylegesen bejelentkezett volna.”
 
Ez az állítás segít azon adatok meghatározásában, amelyeknek gyűjtése szükséges lehet egy adott forgatókönyv támogatásához.


###### Mi: Az üzenet, amelyet el szeretne küldeni

**Hangnem**

A kapcsolattartás során olyan hangnemet használjon, amely megfelel a felhasználói szegmens tagjainak. Ez mindenképpen egy jó módja a végfelhasználókkal történő kapcsolattartásnak, és a felhasználók az alkalmazás iránti érdeklődésének felkeltésére. 

**Átirányítás**

A leküldéses értesítést többre is lehet használni, mint az alkalmazás megnyitására. Ha az értesítési üzenet kontextusa hírek közvetítésére vagy egy termék népszerűsítésére szolgál, az értesítés az alkalmazáson belüli megfelelő tartalomra mutató mélyhivatkozást is tartalmazhat. Ennek támogatására létre kell hozni egy URL-sémát, amely lehetővé teszi az alkalmazás számára az átirányítás kezelését. Amikor a bevonási folyamatokon dolgozik, ez egy fontos lépés, amelyről nem szabad elfelejtkezni.

Az átirányítás más rendszerek vonatkozásában is kezelhető. Egy művelet URL-címe esetén például a végfelhasználók átirányíthatók számos más rendszerre, például az alábbiakra:

- webhelyre,
- már beállított e-mail-címmel rendelkező postaládába,
- SMS-fiókba,
- tárcsázási szolgáltatásba,
- közvetlenül az alkalmazás-áruházba az alkalmazás értékeléséhez. 

Ez több lehetőséget biztosít a végfelhasználók bevonására, és automatikus szabályok létrehozására a teljesítmény növeléséhez.


**Formátum/tartalom**

Különböző típusok és leküldéses értesítési formátumok:

1. **Közlemények**: lehetővé teszi reklámüzenetek felhasználóknak történő küldését különböző időpontokban (alkalmazáson kívül, belül vagy bármikor).
2. **Szavazások**: lehetővé teszi információ gyűjtését a végfelhasználóktól a nekik feltett kérdések használatával. Ezek a válaszok ezután rendelkezésre fognak állni az elérni kívánt végfelhasználókra vonatkozó feltételek meghatározásakor.
3. **Adatleküldések**: lehetővé teszik bináris vagy base64-adatfájlok leküldését az alkalmazás frissítéséhez. Az adatleküldésben található adatoknak az alkalmazás számára történő elküldésével testre szabhatja az alkalmazáson belüli felhasználói élményt. Az alkalmazást úgy kell megtervezni, hogy támogassa az adatleküldésekben található adatokat.
4. **Csempék (csak Windows Phone esetén)**: lehetővé teszik a Microsoft leküldéses értesítési szolgáltatásának (MPNS) használatát XML-adatokat tartalmazó natív leküldéses értesítés küldéséhez (Az SDK 0.9.0-s verziója óta támogatott. A csempék végső hasznos adatai nem haladhatják meg a 32 kilobájtot.). Az üzenet közvetlenül a tábla csempéjén jelenik meg.
5. **Webes nézet**: A webes nézet webes tartalmat tartalmazó előugró ablak. Ez az előugró ablak akkor jelenik meg, ha a végfelhasználó a leküldéses értesítésre kattintott. A webes nézet több interakciót tesz lehetővé a végfelhasználóval.
 
>[AZURE.NOTE] Győződjön meg róla, hogy a leküldéses értesítésekben küldött tartalom megfelel az adott platform (iOS, Android, Windows) alkalmazások fejlesztésérére és leküldéses értesítések küldésére vonatkozó irányelveinek.

 


###### Mikor: A kampány időzítése

Mikor érdemes elindítani leküldéses értesítéseket használó kampányt? Manuális vagy automatikus legyen? Ismétlődjön? A megfelelő időpont és ismétlődési gyakoriság meghatározása rendkívül fontos a felhasználók lehető leghatékonyabb bevonásához. Minden bevonási folyamat és forgatókönyv esetén meg kell határoznia, hogy mikor érdemes leküldéses értesítéseket küldeni. Néhány lehetséges példa:

![](./media/mobile-engagement-getting-started-best-practices/campaign-timing-examples.png)

Ha naponta sok értesítést küld, komolyan meg kell fontolnia, hogy a felhasználók levélszemétnek tekinthetik-e a kommunikációt. 

Az Azure Mobile Engagement két módot biztosít arra, hogy elkerülje a kommunikáció levélszemétként történő kezelését. Először is finomítsa szegmentálását, hogy biztosan ne célozza meg ugyanazokat a felhasználókat. Emellett az Azure Mobile Engagement egy „kvóta” funkciót is biztosít. Ezzel a funkcióval korlátozhatók a kampány részeként elküldött értesítések. Ha például az alapértelmezett kvótát hetente 5 értékűre állítja be, akkor a kampány felhasználói szegmensébe tartozó felhasználók nem kapnak ötnél több értesítést hetente.





#### Az útmutató 2. feladata: A bevonási program létrehozása

Szánjon némi időt a célkitűzések összegzésére, és azon kampányok meghatározására, amelyeket az adott folyamatok használatával le szeretne folytatni. Mindenképpen alkalmazza a 3W megközelítést a kampány értesítéseire vonatkozóan. 

További példákat és útmutatást az [alkalmazástervezési útmutató][alkalmazástervezési útmutatóra mutató hivatkozás] **Engagement Program** (Bevonási program) munkalapján talál.


## 3. lépés: Alkalmazásintegráció


#### Címkézési terv létrehozása

Az Azure Mobile Engagement szolgáltatásnak az alkalmazásba történő integrálásához létre kell hozni egy címkézési tervet. A címkézési terv a projekt sarokköve. Meghatározza a marketingspecifikációk, az alkalmazás munkafolyamata és a KPI-k méréséhez az alkalmazásban gyűjtött valós címkeadatok közötti kapcsolatot. Megmutatja, hogy a portálon milyen elemzési adatokat fog látni. Emellett segítséget nyújt a felhasználói szegmensek meghatározásában, és a koncentrált leküldéses értesítések küldésében a végfelhasználók bevonásához. Miután meghatározta a címkézési tervet, az alkalmazásba történő integrációhoz szükséges kód hozzáadása egyszerűen végrehajtható az Azure Mobile Engagement SDK használatával.

A címkézési tervnek nem szabad mindent címkéznie az alkalmazásban. Csak olyan címkeadatokat szabad tartalmaznia, amelyek a mobilmarketing-stratégiájának a részét képezik. Az adatok valószínűleg eltérőek lesz alkalmazásonként. Az Azure Mobile Engagement [alkalmazástervezési sablonja][alkalmazástervezési útmutatóra mutató hivatkozás] segítséget nyújt a címkézési terv létrehozásához egy adott módszerrel. Használja a **Tag Plan** (Címkézési terv) munkalapot a címkézési terv létrehozásának útmutatójaként.

A munkalapon minden címkeszakaszt konkrétan meg kell határozni. Ez nagyon fontos a bizonytalanság elkerüléséhez. Részletezzen minden olyan várható forgatókönyvet, amelyben az egyes címkék elküldése megtörténik. Adja meg azon tevékenység nevét, amelybe az egyes címkék be lesznek ágyazva. Mindezt a munkalap **Informative** (Tájékoztató) részén kell megadni. A címkézési terv létrehozására szolgáló munkalapnak kell a teszteléssel történő ellenőrzés fő referenciájának lennie. 

A **Data to collect** (Összegyűjtendő adatok) részen a fejlesztői csapatnak meg kell találnia az alkalmazásba beágyazni kívánt egyes címkékhez szükséges típusokat, neveket, értékeket és további adatokat biztosító kulcs/érték párokat.

Javasoljuk a címkézési terv áttekintését a projekthez kapcsolódó csapatok mindegyikével. Végezze el a szükséges javításokat, és győződjön meg arról, hogy a marketing- és a fejlesztői csapat mindennel tisztában van.

A **Statement of work** (Munkaleírás) munkalap nyújthat útmutatás a projekt összes résztvevője számára.


#### Adattípusok

Az Azure Mobile Engagement által támogatott általános adattípusok.

###### Eszközök és felhasználók

Az Azure Mobile Engagement az egyes eszközök számára létrehozott egyedi azonosítóval azonosítja a felhasználókat. Az azonosítót eszközazonosítónak hívják. A létrehozása olyan módon történik, hogy az ugyanazon eszközön futó összes alkalmazás ugyanazon eszközazonosítót használja.

###### Munkamenetek és tevékenységek

A munkamenet a felhasználó által futtatott alkalmazás egy példánya. A munkamenet az alkalmazás felhasználó általi indításától az alkalmazás leállásáig tart.

A tevékenység azon dolgok összességének logikus csoportosítása, amelyeket az alkalmazás egy munkamenet során tehet. Ez általában egy adott képernyő az alkalmazásban, de lehet bármi, amit az alkalmazás logikája meghatároz. Legalább az alkalmazás egyes képernyőit vagy tevékenységeit meg kell címkéznie. Ez lehetővé teszi a felhasználó által bejárt út megértését.


###### Események

Az események a felhasználó és az alkalmazás közötti interakció jelentésére használhatók. Az események lehetnek azonnali műveletek, például tartalom megosztása vagy videó elindítása. Az események címkézése olyan adatgyűjteményeket fog biztosítani, amelyek megmutatják, hogy a felhasználók hogyan használják az alkalmazással. 

###### Feladatok

A feladatok az időtartammal rendelkező műveletek jelentésére használhatók. Néhány példa ilyen műveletekre:

- API-hívások végrehajtása,
- hirdetések megjelenési ideje,
- háttérben futó feladatok időtartama, 
- vásárlási folyamat időtartama,
- videó megtekintése.


###### Hibák

A hibák az alkalmazás által észlelt problémák jelentésére használhatók. Ilyenek például a helytelen felhasználói műveletek vagy sikertelen API-hívások.

###### Alkalmazásadatok

Az alkalmazásadatok a felhasználónak az alkalmazással kapcsolatos tapasztalatához köthető adatok címkézésére használhatók. Az adatokat a felhasználónak az alkalmazással folytatott interakciója hozza létre. 

Az Azure Mobile Engagement egy adott alkalmazásadat-kulcsnak csak a legutóbbi értékét követi nyomon (az előzményeket nem). Az alkalmazásadatok az alkalmazás vagy a végfelhasználók állapotát mutatják meg. Ez lehet például a bejelentkezési állapot, vagy egy felhasználó kedvenc termékcsoportja.

###### Összeomlási adatok

A Mobile Engagement SDK által automatikusan összegyűjtött összeomlási adatok az alkalmazás által nem kezelt alkalmazáshibákat jelentik. Ilyen például egy bekövetkezett nem kezelt kivétel.


###### További adatok

Az eseményeket, hibákat, tevékenységeket és feladatokat paraméterekkel lehet bővíteni. A fejlesztő így az alkalmazásból származó konkrét adatként biztosíthat további adatokat. Ez a részletes szegmentálás meghatározásához fontos. 

Például egy „cikk” címke értéke lehetővé teszi a végfelhasználók csoportosítását az alapján, hogy ki tekintette meg az adott cikket. Ugyanakkor nem biztos, hogy ez elegendő. Jobb megoldás lehet, ha ugyanazon „cikk” címke további adatokat is tartalmaz, ilyen lehet például a „hír_kategória” egy tevékenységen belül. Ez hasznos lehet a felhasználó kedvenc kategóriáinak dinamikus meghatározásához. 

A további adatok jelentése kulcs/érték párként történik. Ezen médiaalkalmazás példájában a „hír_kategória” további adatai a kategória értéke lenne. Például „sport”, „gazdaság” vagy „politika”.





#### Címke- és SDK-integráció 

Az Azure Mobile Engagement SDK-nak az alkalmazásba történő integrálására vonatkozó részletes utasításokat az [Engagement SDK-integrációval](mobile-engagement-windows-store-integrate-engagement.md) foglalkozó dokumentumban tekintheti meg az Azure webhelyén. Válassza ki a lap tetején található hivatkozások közül a célplatformot.

Javasoljuk, hogy az Azure Mobile Engagementre épült két alkalmazáshoz hozzon létre projekteket. Egyet a fejlesztési és tesztelési szakaszhoz, egy másikat pedig az éles szakaszhoz. Az informatikai csapat így előreléphet a tesztelési szakaszból az éles szakaszba, ha a felhasználói tesztelés sikeres volt.



#### Felhasználói tesztelés

A felhasználói tesztelés magában foglalja annak ellenőrzését, hogy minden a tervek szerint működik-e. A munkafolyamatok befejezhetők, és a címkézési terv alapján minden adat összegyűjthető:
 
- A tájékoztatási célú címkézésnek a dokumentált AZME-koncepciók szerint kell működnie.
- Minden szükséges információ gyűjtése megtörténik (beleértve a további adatok értékeit és alkalmazásadatok értékeit).
- Az elnevezések megfelelnek a címkézési tervben lévőkkel.
- Nincsenek duplán elküldött címkék.

Alaposan tesztelje az alkalmazásba ágyazott értesítési működések mindegyik típusát.

- Közlemények, szavazások, adatleküldések alkalmazáson kívül és belül.
- Szöveges/webes nézetek.
- Jelvényfrissítés, kategóriák.



#### Beállítás

Az Azure Mobile Engagement beállítása nagyon egyszerű. A felhasználói felülethez kapcsolódó teljes dokumentáció elérhető az Azure Mobile Engagement webhelyén [a felhasználói felület használatát](mobile-engagement-user-interface.md) ismertető témakörben.

Ajánlott a projekt felhasználóinak megfelelő szerepköreivel és szerepkörcsoportjaival kezdeni a beállítást. Így könnyebben kezelheti a felhasználóknak a platformhoz való hozzáférését. A szerepkörök az alábbiakat foglalhatják magukban:

- Rendszergazdák
- Fejlesztők
- Megtekintők 

Ezt követően:
- Regisztrálja az eszközazonosítót az eszközön történő teszteléshez.
- Nyissa meg a fiók beállításait, és állítsa be az időzónát, hogy a diagramok és értesítések küldési időpontja az időzónájának megfelelően legyen beállítva.
- Nyissa meg az alkalmazás beállításait, és regisztrálja az elérhető végfelhasználók megcélzásához szükséges alkalmazásadatokat.

Az első leküldéses értesítéses kampány futtatásához szükséges további információk: [How to get started using and managing pushes to reach out to your end users](mobile-engagement-how-tos.md) (A végfelhasználók elérését lehetővé tevő leküldéses értesítések használatának és kezelésének megkezdése).



## Összegzés


A bevonási programok ismétlődőek, érdemes folyamatosan fejleszteni őket, miközben kísérletezik azzal, hogy mi működik a legjobban az alkalmazás tekintetében. 

Jóllehet folyamatosan tapasztalatokat szerez a bevonási stratégiákkal kapcsolatban, kezdetben ne próbáljon egy teljes globális stratégiát felépíteni. Lépésenként azonosítsa a KPI mutatókat, és sajátítsa el azok megfelelő használatát. A bevonási stratégia minden alkalmazás esetén egyedi lesz.

Miután szert tett némi tapasztalatra, vegye fontolóra a bevonási programok bővítését az alábbiakkal:

- Nyomon követés: vélhetően szert tesz felhasználókra, és valószínűleg meghatároz adatgyűjtési forrásokat. Az Azure Mobile Engagement összekapcsolható adatgyűjtési forrásokkal. Ez lehetővé teszi minden forrás teljesítményének a megfigyelését. Ezen információk a felhasználók megszerzéséhez kapcsolódó befektetés maximálásához hasznosak. 

- A/B tesztelés: Ez fontos része a bevonási programnak. Minden alkalmazás saját adottságokkal rendelkezik. Az A/B tesztelés révén fejlesztheti a bevonási programot.

- Földrajzi helymeghatározás: Ez nagy lehetőség a márkáknak. Ennek a szolgáltatásnak köszönhetően a megfelelő helyen és időben érhet el felhasználókat. Javasoljuk, hogy a földrajzi helymeghatározás használatának megkezdése előtt ellenőrizze, összegyűjtött-e elegendő adatot a végfelhasználók viselkedésével kapcsolatban.

- Adatleküldés: Az adatleküldés láthatatlan leküldés. Az adatleküldés lehetővé teszi az alkalmazás testre szabását a végfelhasználói viselkedés alapján. Ha például egy felhasználói szegmens gyakran tekint meg csúcskategóriás műszaki termékeket, az alkalmazás tulajdonosa adatleküldés küldésével szabhatja testre a kezdőlapot csúcskategóriás műszaki termékekhez kapcsolódó tartalmakkal.



## Következő lépések

- [Azure Mobile Engagement-fiók létrehozása](mobile-engagement-create.md).
- További információ a mobilmarketing-stratégiájának meghatározásával kapcsolatban: [A mobilmarketing stratégia meghatározása](mobile-engagement-define-your-mobile-engagement-strategy.md).



  

<!--Image references-->


<!--Link references-->
[alkalmazástervezési útmutatóra mutató hivatkozás]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks



<!--HONumber=Sep16_HO4-->


