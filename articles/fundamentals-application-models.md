<properties  umbracoNaviHide="0" pageTitle="Application Model" metaKeywords="Azure, Azure, application model, Azure application model, development model, Azure development model, hosted service, Azure hosted service, web role, worker role" description="Learn about the Azure hosted service application model. Understand core concepts, design considerations, defining and configuring your application, and scaling." linkid="dev-net-fundamentals-application-model" urlDisplayName="Application Model" headerExpose="" footerExpose="" disqusComments="1" title="Application Model" authors="" />

# Az Azure üzemeltetési modelljei

Az Azure különböző üzemeltetési modelleket biztosít az alkalmazások futtatásához. Minden modell más szolgáltatások együtteséből áll, ezért a modellválasztást a végrehajtani kívánt tevékenységek határozzák meg. Ez a cikk három modellt mutat be, ismerteti az egyes technológiákat, és példákat ad a használatukra.

## Tartalom

* [Virtuális gépek](#VMachine)
* [Webhelyek](#WebSites)
* [Felhőszolgáltatások](#CloudServices)
* [Milyen modellt használjak? A választás szempontjai](#WhatShouldIUse)

<h2><a  id="VMachine" ></a>Virtuális gépek</h2>


Az Azure Virtuális gépek szolgáltatással a fejlesztők, az üzemeltetők és mások létrehozhatnak és használhatnak virtuális gépeket a felhőben. Ez az *infrastruktúraszolgáltatás (IaaS)* sokoldalúan használható. Az [1.ábra](#Fig1) bemutatja az alapösszetevőket.

<a name="Fig1"></a>![Virtuális gépek
létrehozása](./media/fundamentals-application-models/ExecModels_01_CreatingVMs.png)

**1. ábra: Az Azure Virtuális gépek infrastruktúraszolgáltatást biztosít**

Amint az ábrán látható, virtuális gépeket létrehozhat akár az Azure felügyeleti portálján, akár a REST-alapú Azure szolgáltatásfelügyeleti API-val. A felügyeleti portál megnyitható bármilyen elterjedt böngészőprogram, például Internet Explorer, Mozilla vagy Chrome használatával. A REST API-hoz a Microsoft ügyféloldali parancsprogramozási eszközöket biztosít Windows, Linux és Macintosh rendszerre. Más szoftverekkel is szabadon használható ez a felület.

Bármilyen módon éri el a platformot, új virtuális gép létrehozásához választania kell egy virtuális merevlemezt (VHD) a virtuális gép lemezképe számára. A virtuális merevlemezek az Azure blobjaiban találhatók.

Induláskor két lehetősége van:

1.  Saját virtuális merevlemez feltöltése. 2.  A Microsoft és partnerei által az Azure virtuálisgép-tárában vagy a
    Microsoft nyílt forráskódú szoftvereket szolgáltató [VMDepot][1]
    webhelyén rendelkezésre bocsátott virtuális merevlemezek használata.

A tárban és a VMDepot webhelyen található virtuális merevlemezek között vannak Microsoftos és Linux operációs rendszereket önállóan tartalmazó lemezképek, valamint olyanok, amelyekre a Microsoft és külső gyártók termékei vannak telepítve. A lehetőségek állandóan bővülnek. Az alábbiakhoz elérhetők például különböző verziók, kiadások és konfigurációk:

* Windows Server
* Linux-kiszolgálók, például Suse, Ubuntu és CentOS
* SQL Server
* BizTalk Server
* SharePoint Server

A virtuális merevlemezen kívül meg kell adni az új virtuális gép méretét is. Az egyes méretek részleteit az [Azure könyvtárban][2] találja meg.

* **Nagyon kicsi**, megosztott maggal és 768 MB memóriával.
* **Kicsi**, 1 maggal és 1,75 GB memóriával.
* **Közepes**, 2 maggal és 3,5 GB memóriával.
* **Nagy**, 4 maggal és 7 GB memóriával.
* **Nagyon nagy**, 8 maggal és 14 GB memóriával.
* **A6**, 4 maggal és 28 GB memóriával.
* **A7**, 8 maggal és 56 GB memóriával.

Végül válassza ki, hogy mely Azure-adatközpontban fusson az új virtuális gépe: az Egyesült Államokban, Európában vagy Ázsiában.

A virtuális gépek használatának díjazása üzemórákon alapul, és az egyes gépek után csak eltávolításukig kell fizetni. A fizetendő összeget nem befolyásolja az adott gép kihasználtságának foka, a díjazás tehát tisztán az üzemidőn alapul. Az egy órán át tétlenül várakozó virtuális gép ugyanannyiba kerül, mint az erősen kihasznált.

Minden futó virtuális géphez tartozik egy *operációsrendszer-lemez*, amely egy blobban található. Ha tárbeli virtuális merevlemezzel hoz létre virtuális gépet, akkor az adott virtuális merevlemez a virtuális gép operációsrendszer-lemezére lesz másolva. A futó virtuális gép operációs rendszerén végzett minden módosítás itt van tárolva. Ha például olyan alkalmazást telepít, amely módosítja a Windows beállításjegyzékét, ez a módosítás a virtuális gép operációsrendszer-lemezén lesz tárolva. Amikor legközelebb virtuális gépet hoz létre abból az operációsrendszer-lemezből, a virtuális gép az előzőleg létrejött állapotban fog futni. A tárbeli virtuális merevlemezek esetén a Microsoft alkalmazza a szükséges frissítéseket, például az operációs rendszerek javítócsomagjait. A saját operációsrendszer-lemezeken lévő virtuális merevlemezek esetében azonban az Ön dolga ezen frissítések alkalmazása (bár a Windows Update alapértelmezésben be van kapcsolva).

A futó virtuális gépek is módosíthatók, majd a sysprep eszközzel rögzíthetők. A sysprep eltávolítja a példányspecifikus dolgokat, például a gépnevet, hogy a virtuális merevlemez lemezképéből további virtuális gépeket lehessen létrehozni ugyanazzal az általános konfigurációval. Ezek a lemezképek a felügyeleti portálon találhatók a feltöltött lemezképekkel együtt, ahol felhasználhatók további virtuális gépek létrehozására.

A Virtuális gépek szolgáltatás figyeli az egyes létrehozott virtuális gépeket futtató hardvert is. Ha egy virtuális gépet futtató fizikai kiszolgáló meghibásodik, a platform ezt észleli, és elindítja ugyanazt a virtuális gépet egy másik gépen. Ha rendelkezik megfelelő licenccel, a módosított virtuális merevlemezt kimásolhatja az operációsrendszer-lemezről, majd futtathatja máshol, például saját helyi adatközpontjában vagy egy másik felhőszolgáltatónál.

Az operációsrendszer-lemezen kívül a virtuális gépnek van egy vagy több adatlemeze is. Bár a virtuális gép számára mindegyik úgy néz ki, mint egy csatlakoztatott lemezmeghajtó, valójában mindegyiknek a tartalma egy blobban van tárolva. Az adatlemezen végzett minden írás maradandóan tárolódik az alapul szolgáló blobban. Mint minden blob esetében, az Azure replikálja ezeket egyetlen adatközponton belül is, és több adatközpont között is, a meghibásodások okozta problémák elkerülése
érdekében.

A futó virtuális gépek kezelhetők a felügyeleti portál, a PowerShell és más parancsfájlkezelő eszközök, valamint közvetlenül a REST API használatával. (Tulajdonképpen ami a felügyeleti portálról elvégezhető, az programozottan is megtehető ezzel az API-val.) A Microsoft partnerei, például a RightScale és a ScaleXtreme szintén biztosítanak a REST API-t használó kezelési szolgáltatásokat.

Az Azure virtuális gépei többféleképpen használhatók. A Microsoft által elsődlegesen megcélzott forgatókönyvek az alábbiak:

* **Virtuális gépek fejlesztéshez és teszteléshez:** A fejlesztői
  csoportoknak gyakran van szükségük virtuális gépekre adott
  konfigurációval alkalmazások létrehozásához. Az Azure Virtuális gépek
  szolgáltatás egyszerű és gazdaságos módot kínál a virtuális gépek
  létrehozására, használatára, valamint az eltávolításukra, ha már nincs
  rájuk szükség.
* **Alkalmazások futtatása a felhőben:** Bizonyos alkalmazásokhoz a
  nyilvános felhőben történő futtatás gazdaságos megoldás. Ilyenek
  például azok az alkalmazások, amelyek kihasználtsága időnként kiugróan
  magas. Az is megoldás, ha elegendő gépet vásárol saját adatközpontjába
  az ilyen alkalmazások futtatásához, azonban a gépek többsége
  valószínűleg kihasználatlan lesz az idő nagy részében. Ha az Azure-ban
  futtatja az ilyen alkalmazásokat, csak akkor kell további virtuális
  gépeket vásárolnia, amikor szükségesek, és leállíthatja őket, amikor a
  kihasználtsági csúcs véget ért. Vagy tételezzük fel, hogy egy start-up
  cége van, amelynek igény szerint elérhető számítási erőforrásokra van
  szüksége -- gyorsan, és elkötelezettség nélkül. Az Azure ilyen esetben
  is megfelelő megoldás lehet.
* **Saját adatközpont kiterjesztése a nyilvános felhőbe:** Az Azure
  Virtuális hálózat szolgáltatással a cége virtuális hálózatot hozhat
  létre, amelyben az Azure-ban lévő virtuálisgép-csoport a saját,
  helyszíni hálózatának részeként látszik. Így futtathatja a
  SharePointot és más alkalmazásokat az Azure-ban, ami adott esetben
  egyszerűbb telepítéssel, illetve alacsonyabb költséggel jár, mintha
  saját adatközpontjában tenné ezt.
* **Katasztrófa utáni helyreállítás:** Ahelyett, hogy folyamatosan
  fizetné egy ritkán használt adatközpont költségét, az
  infrastruktúraszolgáltatáson alapuló helyreállítással csak akkor kell
  fizetnie a számítási kapacitásokért, amikor valóban szüksége van
  rájuk. Ha például az elsődleges adatközpontja meghibásodik,
  létrehozhat az Azure-ban futó virtuális gépeket a legfontosabb
  alkalmazások futtatásához, majd leállíthatja őket, amikor már nincs
  rájuk szükség.

Ezeken kívül egyéb lehetőségei is vannak, de a fentiek tipikus példák arra, hogy mire használhatók az Azure virtuális gépei.

### Virtuális gépek csoportba szervezése: Felhőszolgáltatások

Az Azure Virtuális gépek szolgáltatásban létrehozott virtuális gépek önállóan is futtathatók, de más virtuális gépekkel közös csoportokba, úgynevezett *felhőszolgáltatásba* is szervezhetők. (A hasonló nevek ellenére ne tévessze össze ezt a fogalmat a Felhőszolgáltatásokkal, Azure platformszolgáltatási technológiájával, mert a kettő nem ugyanaz.) Minden önálló virtuális gép saját nyilvános IP-címet kap, az ugyanazon felhőszolgáltatásban lévő összes virtuális gép pedig egy közös nyilvános IP-címen érhető el. A [2. ábra](#Fig2) ezt mutatja be.

<a name="Fig2"></a>![Felhőszolgáltatások](./media/fundamentals-application-models/ExecModels_02_CloudServices.png)

**2. ábra: Minden önálló virtuális gépnek saját nyilvános IP-címe van, a felhőszolgáltatásba csoportosított virtuális gépek pedig egy közös nyilvános IP-címen érhetők el**

Egy egyszerűbb alkalmazás fejlesztéséhez és teszteléséhez nyilvánvalóan elegendő egy önálló virtuális gép. A webes előtérbeli rétegből, adatbázisrétegből és esetleges középső rétegekből álló többrétegű alkalmazásokhoz viszont általában több virtuális gépet kell összekapcsolni egy felhőszolgáltatásba, ahogyan a 2. ábrán látható.

A virtuális gépek felhőszolgáltatásba csoportosításával más lehetőségek is elérhetővé válnak. Az Azure terheléselosztást is biztosít az egy felhőszolgáltatásban lévő virtuális gépekhez, elosztva köztük a felhasználói kérelmeket. Az így összekapcsolt virtuális gépek közvetlenül is kommunikálhatnak egymással a helyi hálózaton az Azure adatközponton belül.

Az egy-egy felhőszolgáltatásban lévő virtuális gépek *rendelkezésre állási készletekbe* csoportosíthatók. Ennek fontossága egy példával szemléltethető: képzeljen el egy webalkalmazást, amely több előtérbeli virtuális gépen fut. Ha az összes virtuális gép ugyanazon a fizikai gépen, vagy akár csak ugyanabban a hardverszekrényben tárolt gépeken van telepítve, egyetlen hardvermeghibásodás az összeset elérhetetlenné teheti. Ha azonban ezek a virtuális gépek egy rendelkezésre állási készletbe vannak csoportosítva, az Azure az adatközpont különböző részeire telepíti őket, hogy ne legyen közös meghibásodási pontjuk.

### Forgatókönyv: SQL Server-adatbázist használó alkalmazás futtatása

Hogy kézzelfoghatóbbá váljon az Azure virtuális gépeinek működése, érdemes megnézni néhány forgatókönyvet részletesebben. Tegyük fel például, hogy megbízható és méretezhető webalkalmazást szeretne létrehozni és az Azure-on futtatni. Ennek egyik módja, hogy az alkalmazáslogikát egy (vagy több) Azure-beli virtuális gépein futtatja, az adatok kezeléséhez pedig SQL Servert használ. A [3. ábra](#Fig3) ezt mutatja be.

<a name="Fig3"></a>![SQL Server-alapú alkalmazás](./media/fundamentals-application-models/ExecModels_03_AppUsingSQLServer.png)

**3. ábra: Az Azure virtuális gépein futó alkalmazás használhatja az SQL
Servert adattároláshoz**

Ebben a példában mindkét virtuális gép a tárban lévő szokásos virtuális merevlemezekből lett létrehozva. Az alkalmazáslogika Windows Server 2008 R2-n fut, ezért a fejlesztőcsapat létrehoz három virtuális gépet ebből a virtuális merevlemezből, majd mindegyikre telepíti az alkalmazást. Mivel mindegyik virtuális gép ugyanabban a felhőszolgáltatásban van, hardveres terheléselosztás is beállítható a kérelmek gépközi elosztására. A fejlesztőcsapat létrehoz még két virtuális gépet a tár SQL Server 2012 rendszert tartalmazó virtuális merevlemezéből. Ezt követően mindkét SQL Serveren beállítják az adatbázis-tükrözést és az automatikus feladatátvételt. Ez nem kötelező -- az alkalmazás használhatna egyetlen SQL Server-példányt is --, azonban ez a megoldás nagyobb megbízhatóságot ad.

### Forgatókönyv: SharePoint-farm futtatása

Tételezzük fel, hogy egy cég létre szeretne hozni egy SharePoint-farmot, de nem szeretné azt saját adatközpontban futtatni. Lehet, hogy a helyben lévő adatközpont nem elegendő erre, vagy a farmot létrehozó részleg nem szeretné belső informatikai csoportjára bízni a feladatot. Ilyen esetekben a SharePoint futtatása az Azure virtuális gépein jó megoldás lehet. A [4. ábra](#Fig4) ezt mutatja be.

<a name="Fig4"></a>![SharePoint-farm](./media/fundamentals-application-models/ExecModels_04_SharePointFarm.png)

**4. ábra: Az Azure Virtuális gépekkel lehetséges egy SharePoint-farm futtatása a felhőben**

A SharePoint-farm számos összetevőből áll, amelyek mindegyike egy másik virtuális merevlemezzel létrehozott Azure-beli virtuális gépen fut. Az ehhez szükséges összetevők:

* Microsoft SharePoint: A tárban vannak próbaverziós rendszereket
  tartalmazó lemezképek, de természetesen a cég saját rendszereit
  tartalmazó virtuális merevlemezek is használhatók.
* Microsoft SQL Server: A SharePointhoz szükséges az SQL Server, ezért a
  tárbeli normál virtuális merevlemezekből olyan virtuális gépeket kell
  létrehozni, amelyeken SQL Server 2012 fut.
* Windows Server Active Directory: A SharePointhoz az Active Directory
  is szükséges, ezért létre kell hozni tartományvezérlőket a felhőben
  egy tárbeli Windows Server-lemezképből. Ez nem kötelező -- a
  tartományvezérlőket helyben is létre lehet hozni --, de a SharePoint
  gyakran kommunikál az Active Directoryval, ezért az itt bemutatott
  megoldás jobb teljesítményt nyújt.

Bár az ábrán ez nem látható, ez a SharePoint-farm valószínűleg egy helyszíni hálózathoz csatlakozik az Azure Virtuális hálózat segítségével. Így lehetővé válik, hogy a virtuális gépek -- és a rajtuk futó alkalmazások -- helyiként látsszanak a hálózat felhasználói számára.

Amint a példákban látható, az Azure Virtuális gépekkel létrehozhat és futtathat új alkalmazásokat a felhőben, futtathat meglévő alkalmazásokat, és egyéb lehetőségei is vannak. Bármelyik lehetőséget választja, a technológia célja ugyanaz: általános célú alap biztosítása felhőbeli infrastruktúrához.

<h2><a  id="WebSites" ></a>Webhelyek</h2>


A webes technológiák sokféleképpen használhatók. Előfordulhat, hogy egy cégnek át kell telepítenie vagy ki kell alakítania egy heti több millió kérés kiszolgálására alkalmas kommunikációs portált, amelyet a világ különböző részein üzemelő adatközpontokban szeretne üzemeltetni. Egy webdesigner-ügynökség és egy fejlesztőcsapat közösen szeretne kialakítani egy több ezer felhasználó kezelésére képes saját webalkalmazást. Egy vállalati fejlesztőcsapatnak egy költségkimutatások nyilvántartására szolgáló felhőbeli alkalmazást kell üzembe helyeznie, amelynek a céges Active Directoryban kell hitelesítenie a felhasználókat. Egy informatikai tanácsadó egy népszerű, nyílt forráskódú alkalmazással valósít meg egy tartalomkezelési rendszert egy kis cég számára. Ezek a feladatok mind megoldhatók az Azure virtuális gépeivel. Azonban a nyers virtuális gépek létrehozása és kezelése igényel némi hozzáértést és erőfeszítést. Ha webhelyet vagy webalkalmazást kell megvalósítania, van egyszerűbb (és olcsóbb) megoldás is: a platformszolgáltatás, azaz PaaS nevű lehetőség. Ahogy az 5. ábrán látható, az Azure a Webhelyek szolgáltatással biztosítja ezt.

<a name="Fig5"></a>![Webhelyek](./media/fundamentals-application-models/ExecModels_05_Websites.png)

**5. ábra: Az Azure webhelyek támogatja a statikus webhelyeket, az elterjedt webalkalmazásokat és a különféle technológiákkal készült egyéni webalkalmazásokat**

Az Azure webhelyek alapját az Azure felhőszolgáltatások adják, így a webalkalmazások futtatására optimalizált, szolgáltatásként nyújtott platformot tud biztosítani. Amint az ábrán látható, a Webhelyek önálló virtuális gépek készletén fut, amelyek tartalmazhatják több felhasználó több webhelyét is, valamint használ normál virtuális gépeket is, amelyek az egyes felhasználókhoz tartoznak. A virtuális gépek egy erőforráskészlet részei, amelyet az Azure webhelyek kezel, ezért magas szintű megbízhatóságot és hibatűrést tesznek lehetővé. Az első lépések megtétele egyszerű. Az Azure webhelyek használatakor a felhasználók választhatnak számos alkalmazás, keretrendszer és sablon közül, és pillanatok alatt elkészíthetnek egy webhelyet. Ezután a megszokott fejlesztőeszközeikkel (WebMatrix, Visual Studio vagy bármilyen más kódszerkesztő eszköz) és verziókezelő rendszereikkel beállíthatják a kód folyamatos integrációját, és csapatként dolgozhatnak. A MySQL-adatbázist használó alkalmazások számára elérhető egy MySQL-szolgáltató, amelyet egy Microsoft-partner, a ClearDB biztosít az Azure számára. A fejlesztők létrehozhatnak nagy, méretezhető webalkalmazásokat a Webhelyek használatával. A technológia támogatja az alkalmazások létrehozását ASP.NET, PHP, Node.js és Python használatával. Az alkalmazások használhatnak például fix kiszolgálású munkameneteket, és a meglévő webalkalmazások módosítás nélkül áttehetők a felhőbeli platformra. A Webhelyek szolgáltatáson alapuló alkalmazások használhatják az Azure más lehetőségeit is, például a Szolgáltatásbuszt, az SQL adatbázist és a blobtárolót. Egy alkalmazás több példányát is futtathatja különböző virtuális gépeken, és a Webhelyek automatikusan elvégzi a terheléselosztást ezek között. Mivel a Webhelyek új példányai már meglévő virtuális gépeken jönnek létre, az új alkalmazáspéldány elindítása nagyon gyors, lényegesen gyorsabb annál, mintha új virtuális gép létrehozására kellene várni. Amint az [5. ábrán](#Fig5) látható, a Webhelyeken többféleképpen tehet közzé kódot és más webes tartalmakat. Használhat FTP-t, FTPS-t vagy a Microsoft WebDeploy technológiáját. A Webhelyekre közzétehet kódot verziókövetési rendszerekből is, amilyen például a Git, a GitHub, a CodePlex, a BitBucket, a Dropbox, a Mercurial, a Team Foundation Server és a felhőalapú Team Foundation Service.

<h2><a  id="CloudServices" ></a>Felhőszolgáltatások</h2>


Az Azure Virtuális gépek infrastruktúraszolgáltatást, az Azure Webhelyek pedig webüzemeltetési szolgáltatást biztosít. A harmadik lehetőség, a Felhőszolgáltatások *platformszolgáltatást (PaaS)* kínál. Ez a technológia azzal a céllal készült, hogy méretezhető, megbízható és alacsony költséggel üzemeltethető alkalmazásokat támogasson. Másik célja, hogy felszabadítsa a fejlesztőket a használt platform kezelésének terhe alól, hogy teljesen az alkalmazásokra koncentrálhassanak. A [6.ábra](#Fig6) ezt mutatja be.

<a name="Fig6"></a>![Felhőszolgáltatások](./media/fundamentals-application-models/ExecModels_06_CloudServices2.png)

**6. ábra: Az Azure felhőszolgáltatások platformszolgáltatást kínál**

Az Azure többi IT-erőforrásához hasonlóan a Felhőszolgáltatások is virtuális gépeken alapul. A technológia két kissé eltérő virtuálisgép-lehetőséget kínál: a *webszerepkörök* példányai egy Windows Server-változatot futtatnak IIS-szel, a *munkavégző szerepkörök* pedig ugyanazt a Windows Server-változatot futtatják IIS nélkül. A Felhőszolgáltatásokon alapuló alkalmazás a két lehetőség valamilyen kombinációját használja.

Például lehet, hogy egy egyszerű alkalmazás mindössze egy webszerepkört használ, míg egy összetettebb alkalmazás kezelheti a felhasználóktól beérkező kérelmeket egy webszerepkörrel, majd a kérelmek által generált munkát átadhatja egy munkavégző szerepkörnek feldolgozásra. (Ez a kommunikáció használhatja a Szolgáltatásbuszt vagy az Azure-üzenetsort.)

Ahogy az ábra mutatja, egy alkalmazás összes virtuális gépe ugyanabban a felhőszolgáltatásban fut, amint azt az Azure Virtuális gépekről szóló részben ismertettük. Így a felhasználók egyetlen nyilvános IP-címen
érhetik el az alkalmazást, és a kérelmek terheléselosztása automatikusan történik az alkalmazás virtuális gépei között. Az Azure Virtuális gépekkel létrehozott felhőszolgáltatásokhoz hasonlóan a platform a Felhőszolgáltatásokat használó alkalmazás virtuális gépeit úgy telepíti, hogy ne legyen közös hardvermeghibásodási pontjuk.

Bár az alkalmazások virtuális gépeken futnak, fontos megérteni, hogy a Felhőszolgáltatások platformot, és nem infrastruktúrát szolgáltat. Ez a következőképpen képzelhető el: infrastruktúraszolgáltatás, például az Azure Virtuális gépek esetén először létrehozza és konfigurálja azt a környezetet, amelyben az alkalmazások futni fognak, majd telepíti az alkalmazást ebbe a környezetbe. Az Ön dolga az infrastruktúra nagy részének kezelése, például az operációs rendszer új, javított verzióinak telepítése minden virtuális gépre. Ezzel szemben a platformszolgáltatás esetében már létezik a környezet. Önnek csak az alkalmazást kell telepítenie. A futtatási platform kezelését, például az operációs rendszer új verzióinak telepítését elvégezzük Önnek.

A Felhőszolgáltatások esetében nem kell létrehoznia virtuális gépeket. Egy konfigurációs fájlt kell megadnia, amelyből az Azure tudni fogja, miből mennyit igényel (például három webszerepkörpéldányt és két munkavégzőszerepkör-példányt), és a platform létrehozza ezeket. Ekkor is Ön adja meg, hogy mekkorák legyenek a virtuális gépek -- a lehetőségek megegyeznek az Azure Virtuális gépekben választhatókkal --, de nem Önnek kell létrehoznia azokat. Ha az alkalmazásának nagyobb terheléseket kell kezelnie, kérhet további virtuális gépeket, és az Azure létrehozza ezeket a példányokat. Ha a terhelés csökken, leállíthatja ezeket a példányokat, így nem kell tovább fizetnie értük.

A Felhőszolgáltatásokon alapuló alkalmazást általában két lépésben teszik elérhetővé a felhasználóknak. Először egy fejlesztő feltölti az alkalmazást a platform átmeneti tárolóterületére. Amikor a fejlesztő készen áll az alkalmazás élő környezetben való bevezetésére, az Azure felügyeleti portálon kéri annak közzétételét. Az ideiglenes és az éles indítás közötti átmenet nem jár időkieséssel, így egy futó alkalmazás frissíthető új verzióra a felhasználók megzavarása nélkül.

A Felhőszolgáltatások figyelést is biztosít. Mint az Azure Virtuális gépek esetében, észleli a meghibásodott fizikai kiszolgálót, és újraindítja a rajta futtatott virtuális gépeket egy új gépen. A Felhőszolgáltatások a hardverhibákon kívül észleli a meghibásodott virtuális gépeket és alkalmazásokat is. A Virtuális gépektől eltérően minden web- és munkavégző szerepkörben van egy ügynöke, így hiba esetén új virtuális gépeket és alkalmazáspéldányokat tud indítani.

A Felhőszolgáltatások platformszolgáltatási jellegének más következményei is vannak. Az egyik legfontosabb az, hogy az ezen technológiára épülő alkalmazásokat úgy kell megírni, hogy megfelelően fussanak egy web- vagy munkavégző szerepkör meghibásodása esetén is. Ennek eléréséhez a Felhőszolgáltatásokon alapuló alkalmazás nem tárolhat állapotokat saját virtuális gépe fájlrendszerében. Az Azure Virtuális gépekkel létrehozott virtuális gépektől eltérően a Felhőszolgáltatások virtuális gépein végzett írások nem perzisztensek; nem létezik olyan, hogy a virtuális gép adatlemeze. Ehelyett a Felhőszolgáltatásokon alapuló alkalmazásnak minden állapotot SQL adatbázisba, blobokba, táblákba vagy más külső tárolóba kell írnia. Ha így építi fel az alkalmazásokat, egyszerűbb lesz a méretezésük, és ellenállóbbak lesznek a meghibásodásokkal szemben -- mindkettő fontos célja a Felhőszolgáltatásoknak.

<h2><a  id="WhatShouldIUse" ></a>Milyen modellt használjak? A választás szempontjai</h2>


Az Azure mindhárom üzemeltetési modellje lehetővé teszi a méretezhető és megbízható alkalmazások létrehozását a felhőben. Ha alapvetően hasonlóak, melyiket érdemes választania? Ez attól függ, hogy mire szeretné használni.

A Virtuális gépek biztosítja a legáltalánosabb megoldást. Ha a lehető legtöbb befolyást szeretné, vagy ha általános virtuális gépekre van szüksége, például fejlesztéshez és teszteléshez, ez a legjobb választás. A Virtuális gépek szintén a legjobb választás készen vásárolt, helyszíni alkalmazások felhőbeli futtatására, amint azt a korábbi SharePoint-példában bemutattuk. És mivel az ezen technológiával létrehozott virtuális gépek ugyanúgy nézhetnek ki, mint a helyszíni virtuális gépei, katasztrófa utáni helyreállítás céljára is ez lehet a legmegfelelőbb választás. A hátrány természetesen az, hogy a nagy hatalommal nagy felelősség jár -- az infrastruktúraszolgáltatás felügyeleti és adminisztratív teendőkkel jár.

A Webhelyek a megfelelő választás, ha egy egyszerű webhelyet szeretne létrehozni. Ez főleg igaz akkor, ha a webhelye egy meglévő alkalmazáson (például Joomla, WordPress vagy Drupal) fog alapulni. A Webhelyek jó választás egy kevés adminisztrációt igénylő webalkalmazás létrehozásához is, abban az esetben is, ha méretezhetőnek kell lennie, vagy ha egy meglévő IIS-webalkalmazást kell áthelyezni a nyilvános felhőbe. A telepítés is gyorsan elvégezhető. Az alkalmazás új példányát szinte azonnal futtathatja, míg egy új virtuális gép telepítése a Virtuális gépek vagy a Felhőszolgáltatások használatával percekig is eltarthat.

A Felhőszolgáltatások, amely az Azure eredeti üzemeltetési modellje volt, kimondottan platformszolgáltatási megoldás. Míg a platformszolgáltatás és a webhelyszolgáltatás között nincs éles határvonal, a Felhőszolgáltatások fontos vonatkozásokban eltér a Webhelyektől, például az alábbiakban:

* A Webhelyektől eltérően a Felhőszolgáltatások adminisztratív
  hozzáférést ad az alkalmazásokat futtató virtuális gépekhez. Így az
  alkalmazáshoz szükséges bármilyen szoftvert telepíthet, amire a
  Webhelyekben nincs lehetőség.
* Mivel a Felhőszolgáltatások webszerepköröket és munkavégző
  szerepköröket is biztosít, a Webhelyeknél jobb választás többszintű
  alkalmazásokhoz, amelyek külön virtuális gépeket igényelnek a
  munkavégző programkódjuk futtatásához.
* A Felhőszolgáltatások külön átmeneti és éles környezetet biztosít, így
  az alkalmazások frissítése valamivel gyorsabb, mint a Webhelyek
  esetén.
* A Webhelyektől eltérően használhat hálózati technológiákat, például az
  Azure Virtuális hálózat és az Azure Connect lehetőséget a helyszíni
  számítógépek és a Felhőszolgáltatásokban futó alkalmazások
  összekapcsolásához.
* A Felhőszolgáltatásokban csatlakozhat közvetlenül egy alkalmazás
  virtuális gépéhez távoli asztallal, amire nincs lehetősége a Webhelyek
  esetén.

Platformszolgáltatás lévén, a Felhőszolgáltatások az Azure Virtuális gépekhez képest is nyújt előnyöket. Több kezelési feladatot végzünk el Ön helyett, például telepítjük az operációs rendszerek frissítéseit, így működtetési költségei alacsonyabbak lehetnek, mint az Azure Virtuális gépek infrastruktúraszolgáltatás esetén.

Az Azure mindhárom üzemeltetési modelljének vannak előnyei és hátrányai. Hogy megfelelően döntsön, ismernie kell ezeket, tisztában kell lennie a céljaival, és így kell kiválasztani a legmegfelelőbbet.

Előfordulhat, hogy egyetlen üzemeltetési modell nem felel meg az igényeknek. Ilyen esetekben nyugodtan kombinálhatja a lehetőségeket. Tételezzük fel például, hogy olyan alkalmazást készít, amelyhez szeretné kihasználni a Felhőszolgáltatások webszerepköreinek előnyeit, de szüksége van normál SQL Serverre is a kompatibilitás és a teljesítmény miatt. Ilyenkor a legjobb az üzemeltetési modellek kombinálása, ahogyan a [7. ábrán](#Fig7) látható.

<a name="Fig7"></a>![07_CombineTechnologies](./media/fundamentals-application-models/ExecModels_07_CombineTechnologies.png)

**7. ábra: Egyetlen alkalmazás használhat többféle üzemeltetési modellt**

Ahogyan az ábrán látható, a Felhőszolgáltatásokban és a Virtuális gépekben létrehozott virtuális gépek külön felhőszolgáltatásokban futnak. A kettő mégis hatékonyan tud egymással kommunikálni, ezért bizonyos alkalmazásokhoz ez a legjobb lehetőség.

Az Azure azért biztosít különböző üzemeltetési modelleket, mert a felhőplatformoknak sokféle forgatókönyvet kell támogatniuk. Aki hatékonyan szeretné használni ezt a platformot -- és ha ezt végigolvasta, valószínűleg Ön is ilyen --, annak mindegyikkel tisztában kell lennie.



[1]: http://vmdepot.msopentech.com/
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/dn197896.aspx