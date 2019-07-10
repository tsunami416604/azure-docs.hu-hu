---
title: Etikai és felelős használatát – Personalizer
titleSuffix: Azure Cognitive Services
description: Ezen irányelvek célja annak megakadályozása, hogy személyre szabása megvalósítása oly módon, amely segítséget nyújt vállalata és a szolgáltatás a megbízhatósági kapcsolat létrehozása. Győződjön meg arról, kutatási szüneteltetése, ismerje meg, és a személyre az emberek életét hatását a szándékos. Ha kétségei vannak, kérjen útmutatást.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: edjez
ms.openlocfilehash: ceb32e9e378ba1e58db11e55be8bd551faf08c2a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722409"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Útmutató a Personalizer felelős megvalósítása

Személyek és társadalom AI teljes kiaknázásához megvalósításokhoz kell, hogy elköteleződéssel AI ad hozzá az alkalmazások megbízhatóságát, és a felhasználók alkalmazásokat beépített mesterséges intelligenciával úgy kell megtervezni. Ezen irányelvek célja annak megakadályozása, hogy megvalósítása Personalizer oly módon, amely segítséget nyújt vállalata és a szolgáltatás a megbízhatósági kapcsolat létrehozása. Győződjön meg arról, kutatási szüneteltetése, ismerje meg, és a személyre az emberek életét hatását a szándékos. Ha kétségei vannak, kérjen útmutatást.

Ezeket az irányelveket nem permanens jogi tanácsadásként, és külön-külön győződjön meg arról, hogy az alkalmazás megfelel a ritmusú fejlesztések ezen a területen, és az ágazatban joga.

Is az alkalmazás használatával Personalizer tervezésének, érdemes rendelkezik minden olyan adatközpontú AI rendszert, amely többek között a etikai, adatvédelmi, biztonsági, biztonság, belefoglalási, átláthatóság és elszámoltathatóság fejlesztése során feladatkörök széles körét. Tudjon meg többet az ezekkel a [ajánlott irodalom](#recommended-reading) szakaszban.

Egy alapszintű ellenőrzőlista, használja az alábbi tartalommal és testreszabható, pontosítsa a forgatókönyvéhez. Ez a dokumentum két fő részből áll: Az első feladata használati szempontok kiemelése, forgatókönyvek, szolgáltatásainak és Personalizer a felhőtechnológia kiválasztásakor van kijelölve. A második hajtsa végre a megfelelő értékek, a Microsoft szerint a érdemes figyelembe venni AI rendszereket, és ez a gyakorlatban hasznosítható javaslatokat és a kockázatok hogyan Personalizer használatát viselkedésre őket. 


## <a name="your-responsibility"></a>A felelősség

Minden irányelvek feladata végrehajtásához a kiindulási alapot, hogy a fejlesztők és Personalizer használó vállalatoknál is felelős, és ezek az algoritmusok használatával társadalom hatásait a kiszolgálófelügyeletért épülnek. Fejleszt egy alkalmazást, amely a szervezet helyezi üzembe, akkor fel kell ismernie a szerepkör és a felelősség annak működését, valamint annak hatásairól személyek. Ha tervez üzembe helyezni egy alkalmazás egy harmadik fél, a végső soron felelős az alkalmazás viselkedését a velük megosztott ismertetése, és való dokumentálja a ismertetése.

Megbízhatósági teljesített kötelezettségeket fogalma épül – fontolja meg a felhasználók számára, egyesületre, és a jogi keretrendszer, az alkalmazások működését, lehet, hogy explicit és implicit követelmények azonosításához.

A Microsoft a folyamatosan üzembe helyezése erőfeszítés az eszközök és a dokumentumok segítséget nyújtanak e kötelezettségek cselekedhet. [Visszajelzés küldése a Microsoftnak](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) Ha úgy gondolja, hogy további eszközöket, a szolgáltatások és dokumentumok lenne segítségével megvalósíthatja Personalizer használatára vonatkozó irányelveket.


## <a name="factors-for-responsibly-implementing-personalizer"></a>Osztott végrehajtási Personalizer tényezők

Végrehajtási Personalizer lehet a felhasználókhoz és az üzleti származzon. Personalizer osztott implementálásához indítsa el az alábbi irányelvek figyelembe vételével során:

* Használati esetek személyre szabása az alkalmazandó kiválasztása.
* Épület [funkciók díjazza](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-rewards.md).
* Kiválasztása, amely [funkciók](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-features.md) személyre szabáshoz használandó környezet és a lehetséges műveletekre.


## <a name="choosing-use-cases-for-personalizer"></a>A Personalizer használati esetek kiválasztása

Egy szolgáltatás, amely megtanulja személyre szabhatja a tartalom és a felhasználói felületek segítségével hasznos. Azt is is lehet misapplied módja a személyre szabás hozott létre negatív hatásai a való világból, ha a felhasználók a tartalom személyre szabása nem is beleértve. 

Personalizer fokozása lehetséges a használati példák negatív hatásai vagy az átláthatóság hiánya tartalmazzák a forgatókönyvek, ahol a "ellenszolgáltatás" függ, hogy számos hosszú távú komplex tényezőket, amikor túlterhelt egyszerűsített be egy azonnali ellenszolgáltatás kedvezőtlen is rendelkezhet. eredmények a felhasználók számára. Ezek általában a "következményi" lehetőségek vagy veszélyt érintő döntések figyelembe kell venni. Példa: 


* **Pénzügyi**: Személyre szabás kínál kölcsön, pénzügyi és biztosítási termékek, ahol kockázati tényezők alapján adatokat a személyek számára ismeretlen, nem lehet megszerezni, vagy nem vitát. 
* **Oktatási**: Személyre szabás holtversenyben iskolai tanfolyamok és oktatási intézmények, ahol javaslatok milyen előítéletek befolyásolják propagálása és csökkentse a felhasználók ismertségét, más lehetőségek.
* **Demokrácia és Civic részvételi**: A cél befolyásoló vélemények rendelkező felhasználók számára a tartalom személyre szabásához, a következményi, és olyan.
* **Külső ellenszolgáltatás értékelési**: Személyre szabás elemek, ahol a ellenszolgáltatás alapul egy utóbbi 3. fél kiértékelése a felhasználó egy, a felhasználó saját viselkedését által generált ellenszolgáltatás nem.
* **Feltárás szembeni**: Minden olyan helyzetet, ahol Personalizer feltárás viselkedését károsíthatja.

Amikor kiválasztja a használati esetek Personalizer számára:

* Indítsa el a mérlegeli, hogyan segít a személyre szabás a a felhasználók tervezési folyamatát.
* Fontolja meg a való világból a negatív következményekkel, ha bizonyos elemek nem rangsorolva, amelyekről a felhasználók számára személyre szabása minták vagy feltárás miatt.
* Fontolja meg a helyi fulfilling prophecy hurkokat. Ez történik, ha egy személyre szabása ellenszolgáltatás betanítja a modell, ezért előfordulhat, hogy ezt követően további kizárhatja a demográfiai csoport kapcsolódó tartalom hozzáférését. Például egy alacsony jövedelmű hálózatok a legtöbb ember nem szerezze be a prémium szintű biztosítási ajánlat, és lassan senki sem található a hálózatok általában az ajánlat minden megtekintéséhez.
* Mentse a modellek és tanulási házirendek, abban az esetben, ha a jövőben reprodukálnia Personalizer szükséges. Ehhez rendszeres időközönként vagy minden modell frissítési időszakban.
* Fontolja meg a feltárás szintjét megfelelő-e a hely és a használata eszközként "echo kamra" hatás csökkentése érdekében.


## <a name="selecting-features-for-personalizer"></a>Válassza ki a funkciók a Personalizer

Tartalom személyre szabásához függ attól, hogy a tartalom és a felhasználó hasznos információkat. Vegye figyelembe, egyes alkalmazások és az iparágban, bizonyos funkciók is közvetlenül vagy közvetve tekinthető megkülönböztető és szabálytalan potenciális felhasználói.

Vegye figyelembe ezeket a szolgáltatásokat a hatás:

* **Felhasználói demográfiai**: Az funkciók nemét, nem, kor, faji, vallási kapcsolatban: Ezeket a funkciókat nem megengedettek az egyes alkalmazásokban szabályozási okokból, és nem lehet etikai személyre őket, mert eltérés és az általánosítást a személyre továbbítja. Ez a propagálás időeltolódás egyik példája egy feladatot, a mérnöki csapathoz idős vagy nemek-alapú célközönséggel nem látható a könyvelési.
* **Területi információval kapcsolatos adatok**: A világ számos helyen (például a zip-kód, irányítószám vagy hálózatok neve) a helyadatok magas összekapcsolhatók legyenek bevétel, faji és vallási.
* **Igazságos felhasználói Érzete**: Olyan esetekben, ahol az alkalmazás helyes döntések fertőzésre, még akkor is, vegye figyelembe, hogy az alkalmazás módosításokat úgy, hogy jelenik meg, amely megkülönböztető funkciók megfeleltetendő megjelenő tartalom perceiving felhasználók hatását.
* **Az funkciók nem kívánt eltérés**:  Milyen előítéletek befolyásolják, hogy az funkciók egy részét a lakosság érintő használatával vihetők típusai vannak. Ez további figyelmet igényel, ha algorithmically generált funkciók, például amikor rendszerkép használatával kibontásához elemzési elemeket egy képet vagy szöveget analytics szöveges entitások felderítéséhez. Győződjön meg arról, saját maga hozhat létre ezeket a szolgáltatásokat a szolgáltatások jellemzőit figyelembe.

Az alábbi eljárásokat alkalmazza, küldjön a környezet és műveletek Personalizer funkciók kiválasztásakor:

* Vegye figyelembe a jogszerűségét és etikai bizonyos funkciók használatával az egyes alkalmazások, és e funkciók álcázva megjelenésű lehet másokkal proxyk szeretne, illetve kerülendő,
* Felhasználók számára átlátható, lehet, hogy algoritmusok és az adatok elemzése a beállítások, látják személyre használják.
* Tegye fel magának: Ehhez a felhasználók gondoskodik, és szereti, ha ezt az információt a tartalom személyre számukra használtam? Lenne szükségem való ismerkedésre terveztünk meg őket, hogyan a döntés született kiemeli vagy elrejtheti az egyes elemek?
* Ahelyett, hogy más jellemzők alapján adatok besorolása vagy Szegmentálás viselkedési használja. Demográfiai adatok hagyományosan használta a kiskereskedők – demográfiai attribútumok tűnt gyűjteni és kezelni a digitális korszakban, mielőtt egyszerű – korábbi okok miatt azonban a kérdés hogyan megfelelő demográfiai adatait, a tényleges kapcsolati, ha környezetfüggő, és az előzményadatok adatokat, hogy jobban vonatkozik, a beállítások és a felhasználók identitását.
* Gondolja át, hogyan megakadályozza, hogy a szolgáltatások "hamisíthatók" ártó szándékú felhasználók, ami nagyszámú esetleges Personalizer képzés a módokon tudja zavarja, embarrass, és bizonyos osztályokat, a felhasználók zaklat félrevezető lehet. 
* Megfelelő és megvalósítható, ha tervezése az alkalmazás lehetővé teszi a felhasználók részt, vagy bizonyos személyes tulajdonságokkal használt tilthatók le. Ezek csoportosítható, például a"hely", "Eszközadatokat", "Korábbi Vásárlási előzmények" stb.


## <a name="computing-rewards-for-personalizer"></a>A Personalizer jutalmakat számítástechnika

Personalizer nagy hangsúlyt fektet a kiválasztott végrehajtandó művelet kiválasztásához a az alkalmazás üzleti logikát által biztosított ellenszolgáltatás pontszám alapján ellenszolgáltatás javítására.

Egy jól felépített ellenszolgáltatás pontszám fog proxyként rövid távú, egy üzleti cél, amely a szervezet üzleti szempontból alapvető van kötve.

Például kattintással a követőink fogja elérhetővé tenni a Personalizer szolgáltatás pozicionálási kattintással rovására minden mást, akkor is, ha a felhasználó kattint zavaró, vagy egy üzleti eredmény nem kötött.

Kontrasztos például egy news hely esetleg érdemes beállítania jutalmakat kötött könnyebben beazonosítható névre, mint a gombra kattint, mint például "Adta a felhasználó időt elegendő olvasni a tartalmat?" "Volt, kattintson ide tartozó cikkekre vagy hivatkozások?". A Personalizer könnyedék elősegítsék a felhőtechnológia szorosan mérőszámok. De ügyeljen arra, hogy rövid távú felhasználói érdeklődést a megfelelő eredményeket confound.

### <a name="unintended-consequences-from-reward-scores"></a>A pontszámok ellenszolgáltatás nem kívánt következményekkel
Ellenszolgáltatás pontszámok előfordulhat, hogy céljaira a legjobb el sestaveno, de továbbra is vagy hozhat létre váratlan következményekkel eredmények elkerülésében a hogyan Personalizer rangsorolja a tartalmat. 

Vegye figyelembe az alábbi példák:

* Videó tartalom személyre szabása, a videó hossza nézte aránya a követőink lesz valószínűleg általában rangsorolják rövidebb videók.
* Közösségi megosztások, hogyan osztja vagy a tartalmat, hangulatelemzés nélkül követőink vezethet a ranglistán megjelenő sértő, cenzúrázatlan vagy gyulladásos tartalmat, amely általában nagy mennyiségű "engagement" helyezhetnek, de nagy értéket ad.
* A művelet a felhasználói felületi elemekre, hogy a felhasználók nem várt módosítása követőink zavarhatják használhatósága és előreláthatóságának növelését a felhasználói felület, ahol gombok meglepően módosítja helye vagy célú figyelmeztetés, megnehezíti az egyes nélkül csoportok felhasználók számára a hatékony munkavégzést.

Ajánlott eljárások megvalósításához:

* A rendszer különböző ellenszolgáltatás módszerekkel megérteni a hatás és a mellékhatásai offline kísérletek futtassa.
* Ellenszolgáltatás függvényeit értékelje ki és hogyan lenne egy rendkívül naiv személy hajlító, hogy hogyan értelmezte és elérni a nemkívánatos kilátásainak tegye fel magának.


## <a name="responsible-design-considerations"></a>Felelős kialakításával kapcsolatos szempontok

Az alábbiakban a mesterséges Intelligencia felelős megvalósítását kialakítása területéhez. Ismerje meg, további eredményez, ezt a keretrendszert a [a jövőben számított](https://news.microsoft.com/futurecomputed/).

![A jövőben számított AI értékek](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Felelősségre vonhatóság
*Akik megtervezéséhez és üzembe helyezéséhez a mesterséges Intelligencia rendszerek kell lennie a hogyan működnek a rendszereik kiszolgálófelügyeletért*. 

* Hozzon létre belső való megvalósítása Personalizer, dokumentum-, és azok a csapat, vezetők és szállítók.
* Hogyan számítja ki ellenszolgáltatás pontszámok rendszeres felülvizsgálatai hajtsa végre, offline értékelések mely szolgáltatásokra hatással vannak a Personalizer megtekintéséhez hajtsa végre és az eredmények segítségével kiküszöbölheti a szükségtelen és a szükségtelen funkciókat.
* Kommunikáció egyértelműen a felhasználók számára Personalizer használatáról, milyen célra, és milyen adatokkal.
* Információk és eszközök – például a modellek, tanulás házirendeket és egyéb adatok – Personalizer használó működik, amelyek sikerült reprodukálnia eredmények archiválhatja.

### <a name="transparency"></a>Átláthatóság
*AI-rendszerek kell lennie a Understandable*. A Personalizer:

* *Hogyan lett a tartalom személyre szabott információkat biztosít a felhasználóknak.* A felhasználók megjelenítheti például nevű gomb `Why These Suggestions?` legnépszerűbb szolgáltatásokat a felhasználó és a műveletek szerepet játszott Personalizer eredményeinek megjelenítése.
* Ellenőrizze, hogy a feltételek használata győződjön megemlíteni, hogy használandó felhasználók és a felhasználók viselkedését a környezetének testreszabása.

### <a name="fairness"></a>Igazságos
*AI-rendszerek viszonylag kell kezelnie minden munkatársát*.

* Használati esetek, ahol az eredményekkel hosszú távú, KÖVETKEZMÉNYES, vagy olyan valós kihagyása kárt Personalizer ne használja.
* Ne használja a szolgáltatások, amelyek nem megfelelő, a tartalom személyre szabásához, és megkönnyíthetik propagálása nemkívánatos milyen előítéletek befolyásolják. Például a pénzügyi termékek azonos személyre szabott javaslatok megjelennie bárki hasonló pénzügyi körülmények között.
* Ismerje meg, milyen előítéletek befolyásolják, előfordulhat, hogy meglévő szerkesztők, algoritmikus eszközök vagy felhasználók maguk származási jellemzőket.

### <a name="reliability-and-safety"></a>Megbízhatóság és biztonság
*AI-rendszerek végre kell hajtania, megbízhatóan és biztonságosan*. A Personalizer:

* *Műveletek nem ad meg, hogy nem kell megválasztani Personalizer*. Például nem megfelelően filmek szűri ki azokat a műveleteket, ha egy javaslat a névtelen vagy korrigáljuk kora felhasználó személyre szabása.
* *Kezelheti a vállalati eszközként Personalizer modell*.  Fontolja meg, milyen gyakran szeretné menteni, és készítsen biztonsági másolatot a modell és a szabályzatok a Personalizer hurok mögött tanulási és egyéb fontos üzleti eszközként kezelni őket. Elmúlt eredmények szaporodó fontos önálló naplózása és mérési szorulnak.
* *Adja meg a felhasználók közvetlen visszajelzést kérhet csatornák*. Kódolási győződjön meg arról, hogy a biztonsági ellenőrzés mellett csak a megfelelő célközönséggel tekintse meg a megfelelő tartalmat, a felhasználók számára, amely knál, vagy zavaró lehet jelentés tartalmának visszajelzés mechanizmus biztosítására. Különösen akkor, ha a tartalmakat a felhasználók vagy a 3. fél származik, fontolja meg, tekintse át és tartalomérvényesítést a Microsoft Content Moderator, illetve további eszközök használatával.
* *Hajtsa végre a gyakori offline értékelések*. Ez segít figyelheti a trendeket, és ellenőrizze, hogy hatékonyságát is ismert.
* *Észlelése és reagálás a rosszindulatú adatkezelési folyamatot*. Nincsenek actors, amely a környezetükben, az eredmény a célok megvalósítását shift tanuljon gépi tanulási és AI-rendszerek kihasználni lesz. Ha Personalizer használatát befolyásolhatja a fontos döntések olyan helyzetben, győződjön meg arról, hogy a megfelelő módon észlelheti és csökkentheti a támadásokkal szemben, beleértve az emberi ellenőrző megfelelő körülmények esetén ezeket az osztályokat.

### <a name="security-and-privacy"></a>Biztonság és adatvédelem
*AI-rendszerek kell a biztonságos és adatainak védelmét*. Personalizer használatakor:

* *Tájékoztassa a felhasználókat meghozni a gyűjtött adatokat és azok felhasználási módjáról, és szerezze be a hozzájárulásukat előre*, a következő a helyi és iparági előírásoknak.
* *Adja meg a felhasználói vezérlők adatvédelmi védelme.* Olyan alkalmazások, amelyek személyes adatokat tárolnak vegye figyelembe, függvények, például egy egyszerű keresés gomb kezeléséről: 
   * `Show me all you know about me`    
   * `Forget my last interaction` 
   * `Delete all you know about me`

Bizonyos esetekben ezek jogilag szükség lehet. Fontolja meg a kompromisszumot kínál a rendszeres időközönként átképezési modellek, így a nyomkövetéseket a törölt adatok nem tartalmaznak.

### <a name="inclusiveness"></a>Inclusiveness
*Cím széles és az emberi igények*.
* *Személyre szabott élmény nyújtása kisegítő-szolgáltatást.* A hatékonyságot, jó személyre szabása - kevesebb erőfeszítés, áthelyezési és -interakciók-lehet fölösleges ismétlési alkalmazott érkező lehet különösen hasznos a fogyatékkal élők számára.
* *Helyi alkalmazás viselkedésének módosítása*. Personalizer segítségével közötti leképezések csevegőrobot, például a félreérthetőség, lehet, hogy a megfelelő értelmezése környezetfüggő, és előfordulhat, hogy egy mérete nem alkalmazható. 


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Proaktív készültségi nagyobb az adatvédelem és irányítás

Nehéz előre jelezni, szabályozási kontextusban egyes módosításokat, de általában lenne célszerű előre megtervezni és a személyes adatok respectful használata annak biztosítása, valamint a magas felhőköltségeit átláthatóan és algoritmikus döntéshozatal kapcsolatos választási lehetőség a minimális jogi kereteinek túl is.


* Fontolja meg olyan helyzetet, ahol új korlátozások az egyéni felhasználók számára gyűjtött adatokat, és szükség van a tervezéssel megjelenítéséhez, hogyan használták döntéseket hozhat.
* Fontolja meg további készültségi, ahol felhasználókat tartalmazhatnak marginalized sebezhető feltöltések, a gyermekek, a gazdasági biztonsági rés a felhasználók vagy a felhasználók más ki vannak téve a algoritmikus adatkezelési befolyásolhatja.
* Vegye figyelembe a széles körű elégedetlenségének hogyan célközönség-célzás és célközönség befolyásoló az adatgyűjtést a programok és algoritmusok játszott, és hogyan bevált stratégiai hibák elkerülése érdekében.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>A projekt életciklusa alatt proaktív értékelés

Érdemes lehet létrehozni a módszerek csapat tagjai, a felhasználók és az üzleti tulajdonosai számára a jelentés a problémák felelős használatát, valamint az olyan folyamat, amely priorizálja a megoldás, és megakadályozza a megtorlás létrehozása.

Minden olyan személy, minden technológiai felhasználásának hatásai mértékegységeként szempontjából, illetve életre szerzett korlátozza. Bontsa ki a tartomány elérhető vélemények, és a több különböző beszédhangot a teams, a felhasználók vagy a tanácsadó tanácsok; hogy lehetséges és ajánlott nyilvánít, hogy legyen. Fontolja meg képzési és oktatási anyagok további bontsa ki a csapat ismeretek ebben a tartományban, majd adja hozzá a teszi, hogy összetett és bizalmas témakörök tárgyalják.

Fontolja meg, az alkalmazásfejlesztési életciklust, a feladatok felelős használatához hasonlóan más crosscutting feladatok kezelésére, például a felhasználó kapcsolatos feladatot észlel, biztonsági vagy a devops. Ezeket a feladatokat és azok követelményeinek ügyként nem lehet. Felelős használatát tárgyalja és alkalmazás életciklusa során ellenőrizni kell.
 
## <a name="questions-and-feedback"></a>Kérdések és visszajelzés

A Microsoft van folyamatos üzembe helyezése erőfeszítés eszközöket és a dokumentumok segítséget nyújtanak e kötelezettségek cselekedhet. Csapatunk felkéri, hogy [visszajelzés küldése a Microsoftnak](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) Ha úgy gondolja, hogy további eszközöket, a termék szolgáltatásainak, és a dokumentumok segít az ezeket az irányelveket a Personalizer használatával valósítható meg.

## <a name="recommended-reading"></a>Javasolt elolvasni

* Tekintse meg a felelős a 2018 január könyv közzétett AI-fejlesztéshez a Microsoft a hat alapelvek [a jövőben kiszámítása](https://news.microsoft.com/futurecomputed/)
* [Kié a jövő? ](https://www.goodreads.com/book/show/15802693-who-owns-the-future) Jaron Lanier szerint.
* [Matematikai bontási fegyverek](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction) által - Kati O'Neil
* [Etikai és az Adattudomány](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) DJ Patil, Hilary Mason, Mike Loukides.
* [Etikai ACM kód](https://www.acm.org/code-of-ethics)
* [Genetikai információt Nondiscrimination Act - GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [FATML elveket, Elszámoltathatóvá algoritmusok](https://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>További lépések

[Funkciók: a művelet és a környezet](concepts-features.md).
