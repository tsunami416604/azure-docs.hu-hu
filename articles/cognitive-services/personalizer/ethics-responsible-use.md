---
title: Etika és felelősségteljes használat - Személyre szabott
titleSuffix: Azure Cognitive Services
description: Ezek nek az irányelveknek az a célja, hogy segítsenek a személyre szabás oly módon történő megvalósításában, amely segít a vállalatba és a szolgáltatásba vetett bizalom kiépítésében. Ügyeljen arra, hogy szünet a kutatás, tanulni és szándékos hatása a személyre szabott emberek életét. Ha kétségei vannak, kérjen útmutatást.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: e422284b871214dbeca31b5dd17b9177a18ad3c8
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478106"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>A Personalizer felelősségteljes végrehajtásának irányelvei

Ahhoz, hogy az emberek és a társadalom teljes mértékben kiaknázható vájkálásban vegye nek részt a a mi-ben, a megvalósításokat úgy kell megtervezni, hogy elnyerjék azoknak a bizalmát, akik a mi-t hozzáadják az alkalmazásaikhoz, valamint a mi-vel készített alkalmazások felhasználóinak bizalmát. Ezek az irányelvek arra irányulnak, hogy segítsenek önnek a Personalizer oly módon történő megvalósításában, amely segít a vállalatba és a szolgáltatásba vetett bizalom kiépítésében. Ügyeljen arra, hogy szünet a kutatás, tanulni és szándékos hatása a személyre szabott emberek életét. Ha kétségei vannak, kérjen útmutatást.

Ezek az iránymutatások nem jogi tanácsadásnak minősülnek, és külön kell biztosítania, hogy alkalmazása megfeleljen az e területen és az ön ágazatában a jogszabályok gyors ütemű fejleményeinek.

Továbbá, az alkalmazás tervezésekor a Personalizer használatával, figyelembe kell vennie az adatközpontú AI-rendszer fejlesztése során fennálló felelősségek széles körét, beleértve az etikát, az adatvédelmet, a biztonságot, a biztonságot, a befogadást, az átláthatóságot és az elszámoltathatóságot. Ezekről bővebben az [Ajánlott olvasás](#recommended-reading) című részben olvashat.

A következő tartalmat használhatja kezdő ellenőrzőlistaként, és testreszabhatja és finomíthatja a forgatókönyvhöz. Ez a dokumentum két fő részből áll: Az első a felelős használati szempontok kiemelésére szolgál a personalizer forgatókönyvek, funkciók és jutalmak kiválasztásakor. A második olyan értékhalmazt tartalmaz, amelyet a Microsoft úgy véli, figyelembe kell venni az AI-rendszerek létrehozásakor, és végrehajtható javaslatokat és kockázatokat ad arra vonatkozóan, hogy a Personalizer használata hogyan befolyásolja őket.


## <a name="your-responsibility"></a>Az Ön felelőssége

A felelős végrehajtásra vonatkozó összes irányelv azon az alapon alapul, amelyet a Personalizer-t használó fejlesztők és vállalkozások felelősek és elszámoltathatók ezen algoritmusok társadalmi használatának hatásaiért. Ha olyan alkalmazást fejleszt, amelyet a szervezet telepít, fel kell ismernie a szerepkörét és a működéséért való felelősségét, valamint azt, hogy az milyen hatással van az emberekre. Ha egy harmadik fél által telepítendő alkalmazást tervez, közös megegyezéssel tudja meg, hogy végül ki felelős az alkalmazás viselkedéséért, és dokumentálja ezt a megértést.

A bizalom a teljesített kötelezettségvállalások fogalmára épül - vegye figyelembe a felhasználókat, a társadalmat és az alkalmazások jogi kereteit, hogy azonosítsa az általuk vállalt kifejezett és implicit kötelezettségvállalásokat.

A Microsoft folyamatosan erőfeszítéseket tesz eszközeiés dokumentumai segítségével, hogy segítsen önnek a feladatok ellátásában. [Ha](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) úgy gondolja, hogy további eszközök, termékfunkciók és dokumentumok segíthetnek a Personalizer használatára vonatkozó irányelvek megvalósításában.


## <a name="factors-for-responsibly-implementing-personalizer"></a>Tényezők a Personalizer felelősségteljes megvalósításához

A Personalizer megvalósítása nagy értéket képviselhet a felhasználók és a vállalkozás számára. A Personalizer felelősségteljes megvalósításához először a következő irányelveket kell figyelembe venni, ha:

* A Személyre szabás alkalmazásához használt esetek kiválasztása.
* Épület [jutalom funkciók](concept-rewards.md).
* Annak kiválasztása, hogy mely [funkciókat](concepts-features.md) fogja használni a környezetről és a személyre szabáshoz szükséges lehetséges műveleteket.


## <a name="choosing-use-cases-for-personalizer"></a>Használati esetek kiválasztása a Personalizer számára

Hasznos lehet olyan szolgáltatást használni, amely megtanulja a tartalom és a felhasználói felületek személyre szabását. Az is misapplied, ha az út a személyre szabott teremt negatív mellékhatások a valós világban, beleértve, ha a felhasználók nincsenek tisztában a tartalom személyre.

Példák a felhasználási Personalizer fokozott potenciális negatív mellékhatások, vagy az átláthatóság hiánya közé tartozik a forgatókönyvek, ahol a "jutalom" függ sok hosszú távú összetett tényezők, hogy ha túl egyszerűsített azonnali jutalom lehet kedvezőtlen eredményeket az egyének számára. Ezek általában "következményes" döntéseknek minősülnek, vagy olyan döntések, amelyek a károsodás kockázatával járnak. Példa:


* **Pénzügy**: A hitel-, pénzügyi és biztosítási termékekre vonatkozó ajánlatok személyre szabása, ahol a kockázati tényezők olyan adatokon alapulnak, amelyekről az egyének nem tudnak, nem szerezhetnek be, vagy nem vitathatják.
* **Oktatás**: Személyre szabása soraiban az iskolai tanfolyamok és oktatási intézmények, ahol ajánlásokat is szaporítják elfogultság és csökkenti a felhasználók figyelmét más lehetőségeket.
* **Demokrácia és polgári részvétel**: A tartalom személyre szabása a felhasználók számára a vélemények befolyásolása céljából következményes és manipulatív.
* **Harmadik fél jutalomértékelése:** Olyan elemek személyre szabása, amelyeknél a jutalom a felhasználó utolsó harmadik fél általi értékelésén alapul, ahelyett, hogy a felhasználó saját viselkedése által generált jutalom lenne.
* **Intolerancia feltárása:** Minden olyan helyzetben, amikor a feltárási viselkedése Personalizer kárt okozhat.

Amikor a Personalizer használati esetét választja:

* Indítsa el a tervezési folyamatot, figyelembe véve, hogy a személyre szabás hogyan segíti a felhasználókat.
* Vegye figyelembe a negatív következményeket a valós világban, ha egyes elemek nem rangsorolják a felhasználók számára a személyre szabási minták vagy feltárása miatt.
* Fontolja meg, hogy az Ön használati esete automatizált adatkezelésnek minősül-e, amely jelentősen érinti a [GDPR](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32016R0679) 22.
* Fontolja meg az önbeteljesítő prófécia hurkok. Ez akkor fordulhat elő, ha egy személyre szabási jutalom beprogramoz egy modellt, így később további kizárásra lehet egy demográfiai csoportot a releváns tartalomhoz való hozzáférésből. Például, a legtöbb ember egy alacsony jövedelmű környéken nem kapnak prémium biztosítási ajánlatot, és lassan senki sem a környéken hajlamos látni az ajánlatot egyáltalán, ha nincs elég feltárása.
* Mentse a modellek és a tanulási szabályzatok másolatait arra az esetre, ha a jövőben reprodukálni kell a Personalizer-t. Ezt rendszeresen vagy minden modellfrissítési időszakban megteheti.
* Tekintsük a terület feltárásának szintjét, és azt, hogy hogyan használjuk eszközként az "echo kamra" hatások enyhítésére.


## <a name="selecting-features-for-personalizer"></a>A Personalizer funkcióinak kiválasztása

A tartalom személyre szabása atartalommal és a felhasználóval kapcsolatos hasznos információktól függ. Ne feledje, hogy egyes alkalmazások és iparágak esetében egyes felhasználói funkciók közvetlenül vagy közvetve diszkriminatívnak és potenciálisan illegálisnak tekinthetők.

Tekintsük a hatását ezeket a funkciókat:

* **Felhasználói demográfiai adatok:** A nemre, a nemre, az életkorra, a rasszra, a vallásra vonatkozó jellemzők: Előfordulhat, hogy ezek a funkciók szabályozási okokból nem engedélyezettek bizonyos alkalmazásokban, és nem etikus a körülöttük lévő személyre szabás, mert a személyre szabás általánosításokat és elfogultságot terjesztene. Egy példa erre az elfogultság terjedését egy álláshirdetést a mérnöki nem jelenik meg az idős vagy nemi alapú közönség.
* **Területi adatok**: A világ számos pontján a helyadatok (például irányítószám, irányítószám vagy a szomszédság neve) nagymértékben korrelálhatnak a jövedelemmel, a fajral és a vallással.
* **A méltányosság felhasználói érzékelése**: Még azokban az esetekben is, amikor az alkalmazás megbízható döntéseket hoz, fontolja meg azon felhasználók hatását, akik úgy érzékelik, hogy az alkalmazásban megjelenő tartalom olyan módon változik, amely úgy tűnik, hogy összefügg a diszkriminatív funkciókkal.
* **Nem szándékolt torzítás a jellemzőkben**: Vannak olyan típusú elfogultságok, amelyek olyan funkciók használatával vezethetők be, amelyek csak a sokaság egy részét érintik. Ez további figyelmet igényel, ha a funkciók atalgoritmikusan jönnek létre, például amikor képelemzéssel kinyeri a kép elemeit vagy szöveges elemzéseket a szövegben lévő entitások felderítéséhez. Tegye fel a figyelmét a szolgáltatások létrehozásához használt szolgáltatások jellemzőire.

Alkalmazza az alábbi eljárásokat, amikor olyan funkciókat választ, amelyeket környezetben és műveletekben szeretne küldeni a Personalizer szolgáltatásnak:

* Fontolja meg a jogszerűségét és etikáját bizonyos funkciók egyes alkalmazások, és hogy ártatlannak látszó funkciók lehetnek proxy mások szeretne, vagy el kell kerülni,
* Legyen átlátható a felhasználók számára, hogy algoritmusok és adatelemzés segítségével személyre szabott a beállításokat látnak.
* Tegye fel magának a kérdést: Érdekelné-e a felhasználókat, és boldoglennék, ha ezeket az információkat a tartalom személyre szabására használnám fel? Szívesen mutatnám meg nekik, hogyan döntöttek úgy, hogy kiemelnek vagy elrejtenek bizonyos elemeket?
* Eltérő jellemzőkön alapuló besorolási vagy szegmentálási adatok helyett használjon viselkedési adatokat. A demográfiai adatokat hagyományosan történelmi okokból használták a kiskereskedők – a demográfiai jellemzőket egyszerűnek tűnt a digitális korszak előtt összegyűjteni és eljárni -, de megkérdőjelezi, hogy mennyire relevánsak a demográfiai adatok, ha tényleges interakció, kontextuális és történelmi adatok kapcsolódnak a felhasználók preferenciáihoz és identitásához.
* Fontolja meg, hogyan lehet megakadályozni, hogy a rosszindulatú felhasználók "meghamisítsák" a funkciókat, amelyek nagy számban történő kihasználásesetén félrevezető módon képezhetik a Personalizer képzését, hogy szándékosan megzavarják, zavarba hozzák és zaklassák a felhasználók bizonyos osztályait.
* Ha ez helyénvaló és megvalósítható, tervezze meg az alkalmazást úgy, hogy a felhasználók leiratkozhassanak bizonyos személyes funkciókról, vagy leiratkozhassanak róluk. Ezek csoportosíthatók, például "Helyadatok", "Eszközadatok", "Korábbi vásárlási előzmények" stb.


## <a name="computing-rewards-for-personalizer"></a>Számítástechnikai jutalmak a Personalizer számára

Personalizer arra törekszik, hogy javítsa a választás, hogy melyik intézkedés jutalmazza alapján a jutalom pontszám által nyújtott alkalmazás üzleti logika.

A jól felépített jutalom pontszám fog működni, mint egy rövid távú proxy egy üzleti cél, amely kötődik a szervezet küldetése.

Például a kattintások jutalmazása a Personalizer Szolgáltatás nak minden más rovására kell keresnie a kattintásokat, még akkor is, ha a kattintás zavaró vagy nem kapcsolódik az üzleti eredményhez.

Például egy hírwebhely a kattintásoknál valami értelmesebb hez kötött jutalmakat szeretne beállítani, például "A felhasználó elegendő időt fordított a tartalom olvasására?" "Rákattintottak-e a vonatkozó cikkekre vagy hivatkozásokra?". A Personalizer segítségével könnyen összelehet kötni a mutatókat a jutalmakkal. De legyen óvatos, hogy ne zavarja a rövid távú felhasználói elkötelezettség jó eredményeket.

### <a name="unintended-consequences-from-reward-scores"></a>Nem szándékolt következmények a jutalompontszámokból
A jutalompontszámok a legjobb szándékkal épülhetnek fel, de továbbra is váratlan következményekkel vagy nem kívánt eredményekkel járhatnak arra vonatkozóan, hogy a Personalizer hogyan rangsorolja a tartalmat.

Tekintse meg a következő példákat:

* A videótartalom személyre szabásának jutalmazása a megnézett videóhosszának százalékán valószínűleg rövidebb videókat rangsorol.
* A közösségi média megosztásainak jutalmazása, anélkül, hogy a megosztott tartalom vagy maga a tartalom hangulatelemzése alapján rangsorolhatja a sértő, nem moderált vagy gyulladásos tartalmakat, ami hajlamos sok "elkötelezettségre" uszítani, de kevés értéket ad hozzá.
* Jutalmazza a keresetet a felhasználói felület elemeit, hogy a felhasználók nem várják, hogy változtatni zavarhatja a használhatóság és a kiszámíthatóság a felhasználói felület, ahol a gombok meglepően változó helyét vagy célját figyelmeztetés nélkül, ami megnehezíti az egyes felhasználói csoportok, hogy felfüggeszti a produktív.

Az alábbi bevált gyakorlatok végrehajtása:

* Futtasson offline kísérleteket a rendszerrel különböző jutalom-megközelítésekkel a hatás és a mellékhatások megértéséhez.
* Értékelje a jutalom funkciókat, és kérdezd meg magadtól, hogyan lenne egy rendkívül naiv személy hajlítsa értelmezését, és eléri a nemkívánatos eredmények vele.


## <a name="responsible-design-considerations"></a>Felelős tervezési szempontok

A következő tervezési területek a mik felelős megvalósítását. Tudjon meg többet abut ez a keretrendszer [a The Future Computed](https://news.microsoft.com/futurecomputed/).

![AI-értékek a jövőbeli számításból](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Elszámoltathatóság
*Az AI rendszereket tervező és üzembe helyező személyeknek elszámoltathatónak kell lenniük a rendszereik működéséért.*

* Hozzon létre belső irányelveket a Rról, hogyan valósíthatja meg a Personalizer-t, dokumentálhatja és kommunikálhatja azokat a csapatával, a vezetőkkel és a beszállítókkal.
* Rendszeresen átkell tekintenie, hogyan számítják ki a jutalompontszámokat, végezzen offline értékeléseket, hogy lássa, milyen funkciók befolyásolják a Personalizert, és az eredmények segítségével kiküszöbölheti a szükségtelen és szükségtelen funkciókat.
* Világosan tájékoztassa a felhasználókat aRról, hogyan, milyen célból és milyen adatokkal használja a Personalizert.
* Archiválja azokat az információkat és eszközöket – például modelleket, tanulási irányelveket és egyéb adatokat –, amelyeket a Personalizer az eredmények reprodukálására használ.

### <a name="transparency"></a>Átlátszóság
*Az AI rendszereknek érthetőnek kell lenniük.* A Personalizer:

* *Adjon tájékoztatást a felhasználóknak a tartalom személyre szabásáról.* Megjeleníthet például a felhasználóknak egy `Why These Suggestions?` gombot, amely en látható, hogy a felhasználó és a műveletek mely legnépszerűbb funkciói játszottak szerepet a Personalizer eredményeiben.
* Győződjön meg arról, hogy a használati feltételek említést tesznek arról, hogy a felhasználókra és viselkedésükre vonatkozó információkat a felhasználói élmény személyre szabásához fogja használni.

### <a name="fairness"></a>Igazságosság
*Az AI Systems-nek minden embert tisztességesen kell kezelnie.*

* Ne használja a Personalizer-t olyan esetekben, amikor az eredmények hosszú távúak, következményesek vagy valódi károkat okoznak.
* Ne használjon olyan funkciókat, amelyek nem alkalmasak a tartalom személyre szabására, vagy amelyek segíthetnek a nem kívánt elfogultság okán. Például, aki hasonló pénzügyi körülmények között kell látni a személyre szabott ajánlásokat a pénzügyi termékek.
* Ismerje meg a szerkesztőktől, algoritmikus eszközöktől vagy maguktól a felhasználóktól származó funkciókban esetleg létező előítéleteket.

### <a name="reliability-and-safety"></a>Megbízhatóság és biztonság
*Az AI systems-nek megbízhatóan és biztonságosan kell működnie.* A Personalizer:

* *Ne adjon meg olyan műveleteket a Personalizer számára, amelyeket nem szabad kiválasztani.* Például a nem megfelelő enciklett filmeket ki kell szűrni a személyre szabási műveletekből, ha névtelen vagy kiskorú felhasználónak ajánlanak.
* *A Personalizer modell kezelése üzleti eszközként.*  Fontolja meg, hogy milyen gyakran mentse és biztonsági másolatot a modell és a tanulási szabályzatok mögött a personalizer loop, és egyébként kezelje azt fontos üzleti eszköz. A múltbeli eredmények reprodukálása fontos az önellenőrzés és a javulás méréséhez.
* *Csatornák biztosítása a közvetlen visszajelzéshez a felhasználóktól.* A biztonsági ellenőrzések kódolása mellett, hogy csak a megfelelő közönség lássa a megfelelő tartalmat, adjon meg egy visszajelzési mechanizmust a felhasználók számára, hogy olyan tartalmat jelentsenek, amely meglepő vagy zavaró lehet. Különösen akkor, ha a tartalom felhasználóktól vagy harmadik felektől származik, fontolja meg a Microsoft Content Moderator vagy további eszközök használatát a tartalom ellenőrzéséhez és érvényesítéséhez.
* *Gyakori offline kiértékelések végrehajtása*. Ez segít nyomon követni a trendeket, és győződjön meg arról, hogy a hatékonyság ismert.
* *Hozzon létre egy folyamatot a rosszindulatú manipuláció észlelésére és az azokkal való fellépésre.* Vannak olyan szereplők, akik kihasználják a gépi tanulást és a a mi-rendszerek azon képességét, hogy tanuljanak a környezetükből, hogy az eredményt a céljaik felé mozdítsák el. Ha a Personalizer használata olyan helyzetben van, hogy befolyásolja a fontos döntéseket, győződjön meg róla, hogy megfelelő eszközökkel rendelkezik a támadások ezen osztályainak észlelésére és enyhítésére, beleértve az emberi felülvizsgálatot a megfelelő körülmények között.

### <a name="security-and-privacy"></a>Biztonság és adatvédelem
*Az AI systems-nek biztonságosnak kell lennie, és tiszteletben kell tartania az adatvédelmet.* A Personalizer használatakor:

* *Tájékoztassa a felhasználókat előre az összegyűjtött adatokról és azok felhasználásáról, és előzetesen szerezze be hozzájárulásukat*a helyi és iparági előírásoknak megfelelően.
* *Adatvédelmet védő felhasználói vezérlők biztosítása.* A személyes adatokat tároló alkalmazások esetében fontolja meg egy könnyen megtalálható gomb megadását az olyan funkciókhoz, mint például:
   * `Show me all you know about me`
   * `Forget my last interaction`
   * `Delete all you know about me`

Bizonyos esetekben, ezek jogilag szükséges. Fontolja meg a kompromisszumokat átképzési modellek rendszeres időközönként, így azok nem tartalmaznak nyomkövetései törölt adatokat.

### <a name="inclusiveness"></a>Befogadás
*Az emberi szükségletek és tapasztalatok széles körével foglalkozik.*
* *Személyre szabott felhasználói élményt biztosítanak a kisegítő lehetőségeket támogató felületekhez.* A hatékonyság, hogy jön a jó személyre szabott - alkalmazott mennyiségének csökkentése érdekében az erőfeszítés, a mozgás, és szükségtelen ismétlés kölcsönhatások- lehet különösen előnyös a fogyatékkal élők számára.
* *Az alkalmazás viselkedésének beállítása a környezethez.* A Personalizer segítségével például félreteheti a csevegőrobot szándékait, mivel a helyes értelmezés lehet kontextuális, és egy méret nem feltétlenül fér el mindenszámára.


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Proaktív felkészültség a fokozott adatvédelemre és irányításra

Nehéz megjósolni a szabályozási környezetkonkrét változásait, de általában bölcs dolog lenne túllépni a minimális jogi kereten a személyes adatok tiszteletteljes felhasználásának biztosítása, valamint az algoritmikus döntéshozatallal kapcsolatos átláthatóság és választás biztosítása terén.


* Fontolja meg egy olyan helyzet előre tervezését, amelyben az egyénektől gyűjtött adatokra vonatkozóan új korlátozások lehetnek érvényben, és meg kell mutatni, hogyan használták fel a döntéseket.
* Fontolja meg az extra készenlétet, ha a felhasználók közé tartozhatnak a marginalizált veszélyeztetett populációk, a gyermekek, a gazdasági sebezhetőségben lévő felhasználók vagy az algoritmikus manipuláció által befolyásolható felhasználók.
* Vegye figyelembe a széles körben elterjedt elégedetlenség, hogy a közönség-célzás és a közönség-befolyásoló adatgyűjtési programok és algoritmusok játszottak ki, és hogyan lehet elkerülni a bizonyított stratégiai hibákat.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Proaktív értékelések a projekt életciklusa során

Fontolja meg a csapattagok, a felhasználók és a vállalkozások tulajdonosai számára a felelős használattal kapcsolatos aggodalmak bejelentésére szolgáló módszerek létrehozását, valamint egy olyan folyamat létrehozását, amely prioritásként kezeli a megoldásukat, és megakadályozza a megtorlást.

Minden olyan személy, gondolkodás mellékhatások használata bármilyen technológia korlátozza a perspektíva és az élettapasztalat. Bővítse ki a rendelkezésre álló vélemények körét azáltal, hogy változatosabb hangokat hoz be a csapatokba, felhasználókba vagy tanácsadó testületekbe; hogy lehetséges legyen, és arra ösztönözzék őket, hogy felszólaljanak. Fontolja meg a képzési és tananyagok további bővítése a csapat tudás ebben a tartományban, és adjunk hozzá képesség, hogy megvitassák az összetett és bizalmas témákat.

Fontolja meg a felelős használattal kapcsolatos feladatok kezelését, csakúgy, mint az alkalmazás életciklusának más, például a felhasználói élményhez, a biztonsághoz vagy a DevOps-hoz kapcsolódó feladatokat. Ezek a feladatok és követelményeik nem lehetnek utógondolatok. A felelős felhasználást az alkalmazás teljes életciklusa során meg kell vitatni és ellenőrizni kell.

## <a name="questions-and-feedback"></a>Kérdések és visszajelzések

A Microsoft folyamatosan erőfeszítéseket tesz olyan eszközökés dokumentumok segítségével, amelyek segítenek önnek e feladatok ellátásában. Csapatunk arra kéri Önt, hogy [küldjön visszajelzést a Microsoftnak,](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) ha úgy gondolja, hogy további eszközök, termékfunkciók és dokumentumok segíthetnek a Personalizer használatára vonatkozó irányelvek megvalósításában.

## <a name="recommended-reading"></a>Ajánlott olvasás

* Lásd a Microsoft hat alapelvét a mi felelős fejlesztésére vonatkozóan, amelyeket a 2018 januári [The Future Computed](https://news.microsoft.com/futurecomputed/) című könyvben tettek közzé
* [Kié a jövő?](https://www.goodreads.com/book/show/15802693-who-owns-the-future) írta: Jaron Lanier.
* [Fegyverek math megsemmisítése](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction) által - Cathy O'Neil
* [Etika és adattudomány](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) DJ Patil, Hilary Mason, Mike Loukides.
* [ACM etikai kódex](https://www.acm.org/code-of-ethics)
* [Genetikai információk megkülönböztetésmentességéről szóló törvény - GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [FatML alapelvei a felelős algoritmusokhoz](https://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>További lépések

[Jellemzők: cselekvés és kontextus](concepts-features.md).
