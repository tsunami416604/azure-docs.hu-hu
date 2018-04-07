---
title: Az Azure SQL adatbázis Azure esettanulmány - GEP |} Microsoft Docs
description: 'További tudnivalók: hogyan GEP használja az SQL-adatbázis több globális vásárlók és nagyobb hatékonyság elérése'
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: c63076f9e3ed2e9ec16507e62a37e966218ec4d6
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="azure-gives-gep-global-reach-and-greater-efficiency"></a>Azure ad globális reach GEP és hatékonyságot
![GEP embléma](./media/sql-database-implementation-gep/geplogo.png)

GEP nyújt a szoftverek és -szolgáltatások, amelyek lehetővé teszik a vállalatok számára műveleteit, azokat a stratégiákat és pénzügyi teljesítményének gyakorolt hatásának maximalizálása a világ különböző beszerzési vezetők. Tanácsadási és felügyelt szolgáltatások a vállalat INTELLIGENS GEP®, egy felhőalapú, átfogó beszerzési szoftver platform által lehetőséget biztosít. Azonban GEP hibába ütközött az támogatja a megoldások, például saját GEP által INTELLIGENS a helyszíni adatközpontját próbál korlátozások: a szükséges beruházások voltak elsajátíthatják a használatát, és más országokban szabályozási követelmények volna végzett a szükséges beruházásokat tűnhet még több. Át a felhőbe, GEP informatikai erőforrások felszabadult lehetővé téve nyugodt kisebb IT-üzemeltetők és az új adatforrások érték az ügyfelek a világszerte több összpontosíthat.

## <a name="expanding-services-and-growth-by-using-azure"></a>Bővülő szolgáltatások és növekedés Azure használatával
INTELLIGENS GEP ügyfelei által kedvelt a platform funkciók és könnyen használható; az ügyfelek kezelheti a folyamatok bárhonnan, bármikor, bármilyen készülékről – hordozható számítógép, táblagép vagy telefon. Microsoft Azure mozgatásával GEP korábban már sikeresen a gyors növekedést és új piacokra bontsa ki a lehetséges. Megfelelően GEP tartozó VP of Technology, Dhananjay Nagalkar "Microsoft Azure már játszott kulcsfontosságú szerepet GEP a sikeres a azáltal, hogy a gyors méretezést szolgáltatások agilitást és, hogy segítsen ügyfeleink globális szabályozó igényeinek kielégítése regionális adatközpontok biztosításával."

## <a name="the-limitations-of-a-do-it-yourself-datacenter"></a>A saját munka datacenter vonatkozó korlátozások
2013-ban GEP vezetők felismeri, hogy azok szükség, azok nőtt az ügyfelek alap biztosítja a méretezhetőséget és teljesítményt nyújt. Nagalkar ismertetése, "annak kiszolgálásához a meglévő adatközpontjainkba, azt kellett volna bontsa ki az infrastruktúra és a nagy mennyiségű informatikai erőforrásra jelentősen. A beruházási és az adott időkereten lett volna hatalmas." A helyszíni fizikai és virtuális gépek kiterjedt konfigurációs, felügyeleti, skálázás, biztonsági mentés és szükséges, amely a GEP akadályozó költség lett volna sebességgel javítását. Megoldások, másrészt kínálnak az egyszerűség és kényelmi célokat szolgál, amely további a fejlesztési kezelése helyett nagy figyelmet GEP engedélyezve – és növekvő – IT-üzemeltetők. Nagalkar tudtak GEP telepítse át a felhőbe csökkentheti az infrastruktúra megvásárlásáról, beállítási és kezelési terhelés.

GEP oly módon, hogy az egyes globális piacokon kívül szabályozó korlátok megoldásához is szükséges. Több GEP tartozó lehetséges Európai ügyfeleket előírásoknak való megfelelés igényelnének, mivel a helyi földrajzi régióban tárolt adatok. De nem lett volna gyakorlati a GEP több adatközpontot felé. "Széles körű infrastrukturális beruházások értékét, és informatikai munka költségek jelentős hatással a margók volna kerüljön" Nagalkar megfelelően. "Ennek eredményeképpen azt ténylegesen kellett számítógépnél kapcsolja a lehetséges ügyfelek a helyben tárolt adatok szükséges."

Nagalkar tudta, hogy egy felhőalapú megoldás lehet a kapcsolatos dilemma megoldása a választ. A felhőbeli szolgáltató globális jelenlét GEP sikerült áthelyezni, ha azt sikerült jobban megfelelni az ügyfelei szabályozó teljesítmény az ügyfelek fizikai helyekre közelebb adatokat tárol, és.

## <a name="finding-smooth-skies-in-the-cloud"></a>Zökkenőmentes skies keresése a felhőben
Nagalkar és csapata felfedezte több felhőalapú lehetőség, de a legtöbb infrastruktúra,--szolgáltatás (IaaS)-alapú megoldások, amelyek lenne szükség GEP fokozottan beruházásának informatikai erőforrásokat a szolgáltatás konfigurálására és kezelésére. Az Azure platform,--szolgáltatás (PaaS) megoldás van kapcsolva, sokkal jobban kell oszlopnál.

"Az Azure-GEP nem szükséges az adatbázis felügyeleti, a virtuális gépekkel kapcsolatos konfigurációját, a javítás vagy más infrastruktúra-kezelési feladatok kezelésére" közölt Nagalkar. "Ehelyett összpontosíthatunk az erőforrások milyen műveleteket végezzük legjobb: a szoftver, amely megteremti az igazi kézbesíti az eredmények adhatunk ügyfeleink írni beszerzési szakértelmet kihasználva." 

Az Azure-bA az áthelyezés valójában GEP nagyobb funkció egyidejű engedélyezése az ügyfelek közben az informatikai dolgozók zsugorítása engedélyezve van.

Ha kihasználja a Azure adatközpontjaiban világszerte, GEP könnyen kiterjesztheti a reach Európában és Ázsiában között. Ezek globális adatközpontjai engedélyezése GEP méretezése a agilitást és helyben tárolja az adatokat, amely csökkenti a késést és szabályozási követelmények megfelel a felhasználói igényeknek.

## <a name="smart-by-gep-architecture"></a>INTELLIGENS által GEP architektúrája
GEP létrehozott INTELLIGENS által GEP egészen az alapoktól az Azure-on. Egy kritikus kifejlesztésének GEP a nagyobb méretezhetőségét, kevesebb mint állásidővel, és csökkentheti a karbantartási költségek, hogy az Azure SQL Database milyen GEP képest GEP sikerült el lehet érni, a helyszíni. Azonban helyezi át a felhőbe, miután GEP az új fejlesztési lehetőségek a felhőben, például a gyors prototípusának és az ügyfelek igényeinek jobban válaszolni lean mérnöki felderítése. Fejlesztés az Azure-ban lehetővé teszik a számítógépnél elvégezni a segítségével a szoftver-licencszerződés fejfájást okozott saját helyszíni futtatható a fejlesztők GEP. GEP által az INTELLIGENS központi az Azure SQL Database, bár GEP sok más Azure-szolgáltatások segítségével egyszerűen és gyorsan további INTELLIGENS által GEP javítása.

![INTELLIGENS által GEP architektúra](./media/sql-database-implementation-gep/figure1.png) 1. ábra. INTELLIGENS által GEP architektúrája

## <a name="structured-data"></a>Strukturált adatok
Az INTELLIGENS GEP alkalmazás középpontjában van az Azure SQL adatbázis-példány adott power a vállalati beszerzési-felügyeleti megoldás. GEP által GEP INTELLIGENS fejthetők vissza, ha az architektúra, amely lehetővé tenné a vállalati adatok védelme a legmagasabb szintű eléréséhez és a szabályozási követelmények teljesítéséhez tökéletes, látott Azure SQL Database. GEP tesz az adatvédelem rétege Azure SQL Database által kínált, beleértve:

* Átlátható adattitkosítás keresztül inaktív adatok titkosítása.
* A hitelesítés biztonságossá tétele az Azure SQL Database integrálja az Azure Active Directoryban.
* Korlátozza a hozzáférést egy sorszintű biztonsággal rendelkező adatok megfelelő részhalmazát.
* Házirendek valós idejű adatok maszkolása.
* Adatbázis-események keresztül Azure SQL Database Auditing követi nyomon.

> "Is használhatók. Ezek közül az összes fő kód módosítása nélkül és minimális hatással van a teljesítményre," említett Nagalkar.
> 
> 

Azure SQL Database segítségével GEP automatikusan rendelkezik nagyobb vész-helyreállítási képességek, mint az sikerült rendelkezik gazdaságosan fejthetők vissza a helyszíni miatt az Azure SQL Database beépített hibatűrési szolgáltatásokat. GEP a aktív georeplikáció funkció az Azure SQL Database, a több aktív, olvasható és online másodlagos replika (az Always On rendelkezésre állási csoportok) alapján kialakulhat használ a különböző földrajzi régióban, magas rendelkezésre állású párok kialakításához. INTELLIGENS által GEP replikálása adatok régiók közötti azt jelenti, hogy, hogy a régió kiterjedő katasztrófa esetén GEP egyszerűen helyre lehet állítani egy olyan minimális helyreállítási-helyreállításipont-célkitűzés (RPO) és a helyreállítási idő célkitűzése (RTO) ügyféladatokat.

Minden INTELLIGENS GEP ügyfél két Azure SQL Database példánya van: egy online tranzakció-feldolgozási (OLTP), egy, az elemzés (például az ügyfél töltött és elemző jelentés). Az Azure SQL Database rugalmas készletek könnyedén kezelheti az akár több ezer előre nem látható adatbázis-erőforrás iránti igények kielégítése érdekében kezelendő globálisan adatbázis GEP engedélyezése. Rugalmas készletek biztosít annak érdekében, hogy felhasználói adatbázisok túlzott kiosztása vagy a-kiosztása nélkül, miközben is lehetővé teszi a GEP költségek szabályozása az szükséges, méretezhető GEP. Ezenkívül, mivel ez egy PaaS szolgáltatás GEP minden új Azure SQL Database szolgáltatás az automatikus frissítések kap.

## <a name="unstructured-and-semi-structured-data"></a>Strukturálatlan és félig strukturált adatok számára
Néhány INTELLIGENS által GEP ügyféladatok azonban kevésbé szigorúan strukturált tárolási kell. Az ilyen típusú adatok GEP Azure Blob storage, Azure Table Storage és Azure Redis Cache funkcióit használja. Az Azure Blob storage Kezelőkód bármilyen tetszés szerinti INTELLIGENS GEP felhasználók töltse fel az alkalmazásba. Akkor is ha INTELLIGENS GEP tárolók statikus tartalom, például egymásra épülő stíluslapok (CSS) és a JavaScript-fájlt.

GEP GEP biztosít Azure Table Storage-ban tárolja a nem ügyfélkapcsolati adatok, például INTELLIGENS által GEP naplóadatokat, gyakorlatilag korlátlan költséghatékony tárolás és a gyors lekérés alkalommal nem állítja be az adatok sémát foglalkoznia kell. GEP Azure Redis Cache fő gyorsítótárat használ.

## <a name="authentication-and-routing"></a>Hitelesítés és az Útválasztás
Az Azure Access Control Service (ACS) GEP felhasználó jelentkezik be a szoftverfrissítési beállítások különböző INTELLIGENS biztosít. Az Azure ACS összevonni bármely identitásszolgáltató, amelyek használatával a Security Assertion Markup Language (SAML), például az Active Directory tartományi szolgáltatásokban, Ping identitás, OneLogin vagy SiteMinder hitelesítési adatokat is. Ezzel a megoldással valósítja meg az egyszeri bejelentkezés (SSO) GEP ügyfelek számára anélkül, hogy felhasználói hitelesítő adatok tárolása és ügyfél-jelszó házirendek karbantartására.

Miután bejelentkezett, az ügyfelek által GEP INTELLIGENS rendelkezik a megfelelő vállalati erőforrásokhoz való hozzáférés. GEP átirányítása Azure Traffic Manager és az ügyfelek mobileszközök és a böngésző-munkamenetek érkező terheléselosztásához kérelmek használja.

## <a name="other-azure-services"></a>Más Azure-szolgáltatásokkal
GEP alkalmazta a más Azure-szolgáltatások INTELLIGENS ellenőrizze számos ügyfél válaszol GEP által kell. GEP Azure felhőszolgáltatások (a webes és feldolgozói szerepkörök) használ a fogadó alkalmazás-bemutató és a védett logikájának szolgáltatásokhoz. Felhőszolgáltatások teszi a fejlesztők (IAC) kódú infrastruktúra kezelése és központi telepítéséhez új INTELLIGENS GEP alkalmazások, amelyek azt a helyszíni adatközpontokkal szükséges idő töredéke alatt – a bármely beavatkozás nélkül informatikai. GEP fejlesztők használhatják a felhőalapú szolgáltatások átmeneti környezet új kiadásokat tesztelése az aktuális éles környezet befolyásolása nélkül. Tesztel, miután GEP a saját Azure felhőszolgáltatások VIP swap részeit, helyezi át az átmeneti kód az éles tárolóhelyre egy percen belül csökkentve a központi telepítés állásidő.

Alkalmazás késés csökkentésére, GEP használja az Azure tartalom Delivery Network (CDN) a peremhálózati kiszolgálóinak megközelíti a felhasználók (például CSS- és JavaScript) Azure Blob storage-ban tárolt statikus tartalmak. GEP által használt Azure Service Bus közötti alkalmazás-architektúra minták támogatásához a részlegesen parancs lekérdezés rugalmas elkülönítése (CQRS) közzétételi-feliratkozási, és réteges architektúra laza kapcsoló és aszinkron kommunikációt tesznek lehetővé. GEP Azure Media Services használatával az ügyfél-támogatási szolgáltatás javítására. GEP található, hogy azt könnyen feltehet videók felhasználói-támogatás az Azure Media Services. Videók kérdésekre közös felhasználói, amely segít INTELLIGENS továbbfejlesztésében GEP felhasználói elégedettségének során néhány kijelentkezés GEP tartozó ügyfél-támogatási személyzet a támogatási terhelés.

Naponta által GEP INTELLIGENS által generált tranzakciós e-mailek több ezer elküldéséhez a vállalkozás használja a SendGrid .NET API integrálása az Azure. A GEP fejlesztők számára, ez csupán az – a SendGrid bővítmény, az Azure-bA az Azure piactéren elérhető jobbra. GEP fejlesztők által GEP INTELLIGENS tudta konfigurálni a SendGrid NuGet csomag jobb használatával a Microsoft Visual Studio; GEP informatikai figyelheti a szoftver SendGrid e-mail forgalom közvetlenül az Azure-ból.

Végül INTELLIGENS GEP által használ Azure virtuális gépek – az Azure IaaS szolgáltatásába – alkalmazások üzemeltetését és szolgáltatásokról, amelyek nem hajtott végre érthető, műszaki osztály-,-a-szoftverszolgáltatás (SaaS) vagy PaaS-megoldásokkal együtt cserélni időpontjában. Például GEP futtatja a vállalatok (B2B) integrációs ügyfelek helyszíni vállalati erőforrás-tervező (ERP) rendszerek például SAP, Oracle, PeopleSoft, JD Edwards, Microsoft Dynamics GP és Lawson és a Szolgáltatottszoftver-megoldások ügyfél hatékonyan a helyszíni Exchange beszerzési dokumentumok, például számlák virtuális gépek integrációs API szolgáltatásokat.

> "Által a Microsoft Azure felhőben GEP INTELLIGENS épület teljesen eltávolított szükség helyszíni informatikai, nem csupán a is a felhasználóink beszerzési műveletek GEP." 
> 
> – Dhananjay Nagalkar, VP technológia megoldások
> 
> 

## <a name="expand-customer-satisfaction-without-expanding-it"></a>Bontsa ki az ügyfelek elégedettségének kiterjesztése nélkül informatikai
A helyszíni adatközpontját áttelepítése az Azure-ba, és teljesen új által GEP INTELLIGENS létrehozása az Azure platformon, mert GEP nőtt méretezhetőséget és a rugalmasságot az infrastruktúra és az informatikai munkatársak kibontása nélkül. Valójában a vállalat informatikai erőforrások még nem szerepel négy évnél hosszabb. Azure kényelmes PaaS modelljét engedélyezte GEP a szállító támogatása és az operations management költségeik csökkentése érdekében. Ennek eredményeképpen GEP lett képes fókusz erőforrások szoftverfejlesztői; a felhőben fejlesztése lehetővé teszi a GEP fejlesztők gyorsan tesztelése töltött idő összehangolja nélkül új ötleteket és informatikai vagy aggódni helyszíni licencelőírások. Az Azure SQL Database segítségével jobban győződjön meg arról, hogy az ügyfelek mindig kivételes szolgáltatás- és GEP.

## <a name="more-information"></a>További információ
* GEP kezdőlap: [GEP](http://www.gep.com)
* Intelligens által GEP: [intelligens GEP által](http://www.smartbygep.com)

## <a name="gep-contributors"></a>GEP közreműködők
* Huzaifa Matawala társítása igazgató – felelős mérnök, GEP
* Sathyan Narasingh, műszaki osztály Manager GEP
* Deepa Velukutty, adatbázis rendszerfejlesztők GEP

