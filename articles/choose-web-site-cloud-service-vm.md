<properties  linkid="manage-scenarios-choose-web-app-service" urlDisplayName="Web Options for Azure" pageTitle="Azure Web Sites, Cloud Services and Virtual Machines comparison" metaKeywords="Cloud Services, Virtual Machines, Web Sites" description="Learn when to use Azure Web Sites, Cloud Services, and Virtual Machines for hosting web applications. Review a feature comparison." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title=" Cloud Services" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

# Az Azure Webhelyek, Felhőszolgáltatások és Virtuális gépek szolgáltatások összehasonlítása

Az Azure számos lehetőséget kínál a webalkalmazások üzemeltetésére. Ilyen például az [Azure Webhelyek][1], a [Felhőszolgáltatások][2] és a
[Virtuális gépek][3] szolgáltatás. A különböző lehetőségeket áttekintve talán bizonytalan lehet abban, hogy melyik felelne meg leginkább igényeinek, illetve előfordulhat, hogy nem teljesen egyértelmű néhány fogalom, például az infrastruktúraszolgáltatás (IaaS) és a platformszolgáltatás (PaaS) fogalma. Ez a cikk segít átlátni a különböző lehetőségeket, valamint meghozni az adott webes használati helyzetnek leginkább megfelelő döntést. Ugyan mindhárom esetben rendkívül jól méretezhető webalkalmazásokat futtathat az Azure-ban, mégis vannak az egyes lehetőségek között különbségek, amelyek megkönnyíthetik a döntést.

Számos helyzetben az Azure webhelyek jelenti a legjobb megoldást. Egyszerű, rugalmas telepítési és felügyeleti lehetőségeket biztosít, és nagyméretű webhelyek tárolására is alkalmas. A webalkalmazások gyűjteményéből gyorsan hozhat létre új webhelyet olyan népszerű szoftverekkel, mint a WordPress, de akár meglévő webhelyet is áttelepíthet az Azure webhelyekbe. A jelenleg előzetes verzióként elérhető [Azure WebJobs SDK][4] a háttérben futó feladatok feldolgozására is lehetőséget nyújt.

Webalkalmazásai üzemeltetéséhez ezenkívül az Azure felhőszolgáltatásokat és az Azure virtuális gépeket is használhatja. Ezek akkor bizonyulhatnak jó megoldásnak, ha az adott webes réteg megköveteli az általuk biztosított további irányítási és testreszabási lehetőségeket. A nagyobb mértékű irányítás ugyanakkor az alkalmazások létrehozásának, kezelésének és telepítésének nagyobb bonyolultságával jár. Az alábbi diagram a három lehetőség előnyeit és hátrányait mutatja be.

![Választható lehetőségek
diagramja](./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_2.png)

A Webhelyek megkönnyíti a beállítást, a kezelést és a figyelést, azonban kevesebb konfigurációs lehetőséget biztosít. A lényeg, hogy ha nincs nyomós oka arra, hogy a webes kezelőfelületet a Felhőszolgáltatásokban vagy a Virtuális gépeken helyezze el, érdemes az Azure webhelyeket választani. Az alábbiakban megtalálhatja a megalapozott döntés meghozatalához szükséges információkat. A dokumentum a következő szakaszokra tagolódik:

* [Alkalmazási területek](#scenarios)
* [A szolgáltatások összefoglalása](#services)
* [A funkciók összehasonlítása](#features)

## <a name="scenarios"></a>Alkalmazási területek

### Kisvállalkozásom van, és költségkímélő, ugyanakkor későbbi növekedést is lehetővé tevő megoldásra van szükségem webhelyem üzemeltetésére

Ebben az esetben remek megoldást jelent az Azure webhelyek (WAWS), hiszen használatát ingyenesen megkezdheti, és a szolgáltatást később szükség szerint további lehetőségekkel egészítheti ki. Minden ingyenes webhelyhez jár például egy Azure-beli tartomány
(*cegnev*.azurewebsites.net). Ha készen áll saját tartományneve használatára, mindössze havi 9,80 USD-ért kiegészítheti a szolgáltatást ezzel a lehetőséggel (az ár a 2014. januári állapotot tükrözi). Számos további szolgáltatás és méretezési lehetőség segíti a webhely fejlődését megnövekedett felhasználói érdeklődés esetén. Az **Azure webhelyek** a következő lehetőségeket kínálja:

* Használat megkezdése ingyenesen, később pedig lehetőség a szükség
  szerinti bővítésre.
* Népszerű webalkalmazások, például a WordPress gyors beállítása az
  alkalmazások gyűjteményéből.
* Az alkalmazás szükség szerinti kiegészítése további
  Azure-szolgáltatásokkal és -funkciókkal.
* A webhely biztonságossá tétele a HTTPS protokoll és a saját
  *cegnev*.azurewebsites.net tartománynevéhez tartozó tanúsítvány
  használatával.

### Webes vagy grafikus tervezőként dolgozom, és webhelyeket szeretnék tervezni, illetve kiépíteni ügyfeleim számára

Az Azure webhelyekben a webes fejlesztők megtalálnak mindent, amire az összetett webalkalmazások létrehozásához szükségük lehet. A Webhelyek szorosan együttműködik a Visual Studióval, az SQL-adatbázissal és más eszközökkel. A **Webhelyek** a következő lehetőségeket kínálja fejlesztőknek:

* [Feladatok automatizálása][5] parancssori eszközökkel.
* Olyan népszerű nyelvek használata, mint a [.NET][6], a [PHP][7], a
  [Node.js][8] és a [Python][9].
* Bővítés három különböző méretezési szint közül választva, akár igen
  nagy kapacitásig is.
* Integráció más Azure-szolgáltatásokkal, például az
  [SQL-adatbázissal][10], a [Szolgáltatásbusszal][11] és a
  [Tárolással][12], valamint partnereink [Azure Áruházból][13] elérhető
  termékeivel, például a MySQL-lel és a MongoDB-vel.
* Integráció olyan eszközökkel, mint a Visual Studio, a Git, a
  WebMatrix, a WebDeploy, a TFS és az FTP.

### Többrétegű, webes kezelőfelülettel rendelkező alkalmazásomat szeretném áttelepíteni a felhőbe

Az Azure számos lehetőséget kínál arra az esetre, ha többrétegű alkalmazást, például egy webhely adatainak tárolása és visszakeresése céljából adatbázis-kiszolgálóval kommunikáló webkiszolgálót futtat. Ilyen architekturális lehetőségek többek között a Webhelyek, a Felhőszolgáltatások és a Virtuális gépek. A **Webhelyek** jó választásnak bizonyulhat a megoldások webes rétegéhez, és az Azure SQL-adatbázissal együttesen alkalmazva kétrétegű architektúra kialakítására is használható. A Webhelyek ezenkívül az Azure WebJobs SDK előzetes verziója segítségével háttérben vagy hosszabb ideig futó folyamatok futtatását is lehetővé teszi. Ha összetettebb architektúrára vagy rugalmasabb méretezési lehetőségekre van szüksége, érdemesebb a Felhőszolgáltatásokat vagy a Virtuális gépeket választani.

A **Felhőszolgáltatások** a következő lehetőségeket kínálja:

* Webes, középső rétegbeli és háttérszolgáltatások üzemeltetése
  méretezhető webes és munkavégző szerepkörökben.
* Csak a középső rétegbeli és háttérszolgáltatások üzemeltetése a
  munkavégző szerepkörökben, miközben a kezelőfelület az Azure
  webhelyekben kap helyet.
* Az előtér- és háttérszolgáltatások egymástól független méretezése.

A **Virtuális gépek** a következő lehetőségeket kínálja:

* Nagymértékben testre szabott környezetek könnyebb áttelepítése
  virtuális gép lemezképeként.
* A Webhelyekben és a Felhőszolgáltatásokban nem konfigurálható
  szoftverek és szolgáltatások futtatása.

### Az alkalmazásomhoz nagymértékben testre szabott Windows- vagy Linux-alapú környezetre van szükségem

Ha alkalmazásához a szoftverekkel és az operációs rendszerrel kapcsolatos összetett telepítési vagy konfigurálási műveletekre van szükség, valószínűleg a Virtuális gépek jelenti a legjobb megoldást. A **Virtuális gépek** a következő lehetőségeket kínálja:

* Kiindulási operációs rendszer -- például Windows vagy Linux --
  választása a virtuális gépek gyűjteményéből, amely aztán testre
  szabható az alkalmazás követelményeinek megfelelően.
* Meglévő helyszíni kiszolgáló egyéni lemezképének létrehozása és
  feltöltése Azure-beli virtuális gépen való futtatásra.

### Nyílt forráskódú szoftvert használó webhelyet szeretnék üzemeltetni az Azure-ban

Mindhárom lehetőség támogatja a nyílt forráskódú nyelveket és keretrendszereket. A **Felhőszolgáltatások** használata némi előkészítési tevékenységekkel jár: telepíteni és konfigurálni kell a Windowson futó nyílt forráskódú szoftvereket. A **Virtuális gépek** esetén az akár Windows-, akár Linux-alapú gép lemezképén kell telepítenie és konfigurálnia a szoftvereket. Ha a Webhelyek támogatja a használni kívánt nyílt forráskódú keretrendszert, egyszerűbb lehetőség kínálkozik az ilyen típusú alkalmazások üzemeltetésére, mivel a Webhelyek automatikusan beállítható az alkalmazás számára szükséges nyelvek és keretrendszerek használatára. A **Webhelyek** a következő lehetőségeket kínálja:

* Számos népszerű nyílt forráskódú nyelv, például a [.NET][6], a
  [PHP][7], a [Node.js][8] és a [Python][9] használata.
* A WordPress, a Drupal, az Umbraco, a DNN és számos más, független
  gyártótól származó webalkalmazás telepítése.
* Meglévő alkalmazás áttelepítése, vagy új létrehozása az alkalmazások
  gyűjteményéből.

### Egy üzleti alkalmazást kell összekapcsolni a vállalati hálózattal

Ha üzleti alkalmazást szeretne létrehozni, előfordulhat, hogy webhelyének közvetlenül hozzá kell férnie a vállalati hálózaton lévő szolgáltatásokhoz és adatokhoz. Erre a **Webhelyek**, a **Felhőszolgáltatások** és a **Virtuális gépek** egyaránt lehetőséget nyújt, az alkalmazott módszer szempontjából azonban vannak közöttük különbségek, például a következők:

* A Webhelyek szolgáltatásbuszon keresztüli közvetítővel tud
  biztonságosan csatlakozni a helyszíni erőforrásokhoz. Ez lehetővé
  teszi, hogy a vállalati hálózaton futó szolgáltatások anélkül
  végezzenek feladatokat a webalkalmazás nevében, hogy mindent át
  kellene telepíteni a felhőbe, illetve virtuális hálózatot kellene
  beállítani.
* A Felhőszolgáltatások és a Virtuális gépek ki tudja aknázni a
  Virtuális hálózat lehetőségeit. A Virtuális hálózat lényegében
  lehetővé teszi, hogy az Azure-ban futó gépek helyszíni hálózathoz
  csatlakozzanak. Az Azure így a vállalati adatközpont kiterjesztésévé
  válik.

### REST-alapú API-t vagy webszolgáltatást szeretnék üzemeltetni mobilügyfelek számára

A HTTP-alapú webszolgáltatásokkal számos különböző ügyfélhez, így mobilügyfelekhez is biztosíthat támogatást. Az ASP.NET Web API és más hasonló keretrendszerek a Visual Studióba beépülve megkönnyítik a REST-alapú szolgáltatások létrehozását és felhasználását. Ezek a szolgáltatások webes végpontról érhetők el, így ez a forgatókönyv az Azure-ban bármilyen webszolgáltatói technikával támogatható. A **Webhelyek** azonban különösen jó megoldást jelent a REST API-k üzemeltetésére. A Webhelyek a következő lehetőségeket kínálja:

* Webhely gyors létrehozása a HTTP-alapú webszolgáltatásnak az Azure
  globálisan elosztott adatközpontjainak valamelyikében való
  üzemeltetéséhez.
* Meglévő szolgáltatások áttelepítése, illetve újak létrehozása, adott
  esetben kihasználva a Visual Studióban az ASP.NET Web API kínálta
  lehetőségeket.
* Rendelkezésre állási SLA teljesítése egyetlen példány használatával,
  vagy kiterjesztés több dedikált gépre.
* REST-alapú API-k szolgáltatása a közzétett webhelyen keresztül
  bármilyen HTTP-alapú ügyfél, így mobilügyfelek számára is.

## <a name="services"></a>A szolgáltatások összefoglalása

Az [Azure webhelyekkel][1] gyorsan építhet ki nagyon jól méretezhető webhelyeket az Azure-ban. Az Azure portál vagy a parancssori eszközök segítségével népszerű nyelvek -- például a .NET, a PHP, a Node.js és a Python -- használatával építhet ki webhelyeket. A támogatott keretrendszerek előre telepítve vannak, így nincs szükség külön telepítési lépésekre. Az Azure webhelyek gyűjteményében számos, független gyártótól származó alkalmazás -- például a Drupal és a WordPress --, valamint olyan fejlesztési keretrendszerek is megtalálhatók, mint a Django és a CakePHP. Webhelye létrehozása után áttelepíthet egy meglévő webhelyet, de akár teljesen újat is kiépíthet. A Webhelyek kiküszöböli a fizikai hardver kezelésének szükségességét, és számos méretezési lehetőséget is biztosít. Megosztott, többvállalatos modellről normál módra állhat át, amelyben dedikált gépek szolgálják ki a bejövő forgalmat. A Webhelyek emellett más Azure-szolgáltatásokkal, például az SQL-adatbázissal, a Szolgáltatásbusszal és a Tárolással is lehetővé teszi az integrációt. Az [Azure WebJobs SDK][4] előzetes verziójával háttérfeladat-feldolgozást is alkalmazhat. Összegzésként elmondható, hogy az Azure webhelyek a nyelvek, nyílt forráskódú alkalmazások és telepítési módszerek (FTP, Git, Web Deploy, illetve TFS) széles választékát támogatja, így megkönnyíti az alkalmazásfejlesztésre való összpontosítást. Ha nincsenek speciális igényei, amelyek indokolnák a Felhőszolgáltatások vagy a Virtuális gépek használatát, valószínűleg az Azure webhelyek jelenti a legjobb választást.

A [Felhőszolgáltatások][2] magas rendelkezésre állású, méretezhető webalkalmazások fejlesztését teszi lehetővé gazdag funkciókészletű PaaS-környezetben. A webhelyekkel ellentétben a felhőszolgáltatásokat az Azure-ban történő üzembe helyezésük előtt létre kell hozni valamilyen fejlesztési környezetben, például a Visual Studióban. A PHP és más keretrendszerek esetén a keretrendszert a szerepkör indításakor egyéni telepítési lépésekkel vagy feladatokkal telepíteni kell. A Felhőszolgáltatások fő előnye, hogy az összetettebb többrétegű architektúrákat is támogatja. Egy adott felhőszolgáltatás tartalmazhat egy előtérbeli webes, valamint egy vagy több munkavégző szerepkört. Az egyes rétegek egymástól függetlenül méretezhetők. A szolgáltatás ezenkívül nagyobb fokú irányítást biztosít a webalkalmazás-infrastruktúra felett. Csatlakozhat például távoli asztali kapcsolattal a szerepkörpéldányokat futtató gépekhez. Ezenkívül parancsfájlokat készíthet a szerepkör indításakor futtatott összetettebb IIS- és számítógép-konfigurációs módosításokhoz, beleértve a rendszergazdai szabályozást igénylő feladatokat.

A [Virtuális gépekkel][3] virtuális gépeken futtathat webalkalmazásokat az Azure-ban. Ezt infrastruktúraszolgáltatásnak (IaaS) is nevezik. A portálon keresztül új Windows Server- vagy Linux-alapú gépeket hozhat létre, illetve meglévő virtuális gép lemezképét is feltöltheti. A Virtuális gépek biztosítja a legnagyobb fokú kontrollt az operációs rendszer, a konfiguráció, valamint a telepített szoftverek és szolgáltatások felett. Jó megoldást jelent abban az esetben, ha gyorsan kell összetett helyszíni webalkalmazásokat áttelepíteni a felhőbe, mivel a gépek teljes egészükben áttelepíthetők. A Virtuális hálózattal helyszíni vállalati hálózatokhoz is csatlakoztathatja a virtuális gépeket. A Felhőszolgáltatásokhoz hasonlóan a gépek táveléréssel is elérhetők, és rendszergazdai szintű konfigurációs módosítások végezhetők. A Webhelyekkel és a Felhőszolgáltatásokkal ellentétben azonban a virtuális gépek lemezképeit és az alkalmazásarchitektúrát teljes egészében az infrastruktúra szintjén kell kezelni. Ez azt jelenti például, hogy saját hatáskörben kell gondoskodni az operációs rendszer javítócsomagjainak telepítéséről.

## <a name="features"></a>A funkciók összehasonlítása

Az alábbi táblázatban a Webhelyek, a Felhőszolgáltatások és a Virtuális gépek lehetőségeinek összehasonlítását tekintheti át, ami segíthet a megfelelő döntés meghozatalában. A csillaggal jelölt rovatokról további információkat találhat a táblázat alatti megjegyzésekben.

<table  cellspacing="0" border="1">
<tr>
   <th  align="left" valign="middle">Funkció</th>

   <th  align="left" valign="middle">Webhelyek</th>

   <th  align="left" valign="middle">Felhőszolgáltatások (webes szerepkörök)</th>

   <th  align="left" valign="middle">Virtuális gépek</th>

</tr>

<tr>
   <td  valign="middle"><p>Hozzáférés olyan szolgáltatásokhoz, mint a Szolgáltatásbusz, a Tárolás és az SQL-adatbázis</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Többrétegű architektúra webes vagy webszolgáltatási rétegének üzemeltetése</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Többrétegű architektúra középső rétegének üzemeltetése</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Integrált MySQL-szolgáltatás támogatása</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X <sup>1</sup>
</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Az ASP.NET, a klasszikus ASP, a Node.js, a PHP és a Python támogatása</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Kiterjesztés több példányra újratelepítés nélkül</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X <sup>2</sup>
</td>

</tr>

<tr>
   <td  valign="middle"><p>SSL-támogatás</p>
</td>

   <td  valign="middle">X <sup>3</sup>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Visual Studio-integráció</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Távoli hibakeresés</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Kódtelepítés a TFS segítségével</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Kódtelepítés a GIT és FTP segítségével</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Kódtelepítés a Web Deploy segítségével</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle"><sup>4</sup>
</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>WebMatrix-támogatás</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Szinte azonnali telepítés</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>A példányok között közös a tartalom és a konfiguráció</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Kiterjesztés nagyobb gépekre újratelepítés nélkül</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Több telepítési környezet használata (éles és átmeneti)</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Hálózatelkülönítés az Azure virtuális hálózat használatával</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Az Azure forgalomkezelő támogatása</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Távoli asztali hozzáférés kiszolgálókhoz</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Indítási feladatok definiálásának/végrehajtásának lehetősége</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Operációs rendszer frissítésének automatikus kezelése</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Integrált végpontfigyelés</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Zökkenőmentes váltás 32 és 64 bites platform között</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

</tr>

</table>

<sup>1</sup>A webes, illetve munkavégző szerepkörök lehetővé teszik a MySQL-szolgáltatás integrációját a ClearDB lehetőségeinek használatával, a kezelési portál munkafolyamatának részeként azonban nem.

<sup>2</sup>A Virtuális gépek ugyan több példányra is kiterjeszthető, a gépeken futó szolgáltatásoknak azonban eleve tudniuk kell kezelni ezt a kiterjesztést. A kérések gépek közötti irányításához külön terheléselosztót kell konfigurálni. Végül pedig javasolt affinitáscsoportot létrehozni az összes, ugyanazon szerepkörben részt vevő géphez, hogy a gépek megóvhatók legyenek a karbantartás vagy hardverhiba miatti egyidejű újraindításoktól.

<sup>3</sup>A Webhelyek szolgáltatásban az SSL-t csak szabványos módban lehet saját tartománynevekhez használni. Az SSL protokoll Webhelyekkel való használatáról az [SSL-tanúsítvány beállítása Azure-webhelyhez][14] című cikkben olvashat részletesebben.

<sup>4</sup> A Web Deploy használata egypéldányos szerepkörökre telepítendő felhőszolgáltatások esetén támogatott. Az éles szerepkörök esetén azonban több példány használatára van szükség az Azure SLA teljesítéséhez. A Web Deploy éppen ezért az éles felhőszolgáltatásokhoz nem megfelelő telepítési mechanizmus.



[1]: http://go.microsoft.com/fwlink/?LinkId=306051
[2]: http://go.microsoft.com/fwlink/?LinkId=306052
[3]: http://go.microsoft.com/fwlink/?LinkID=306053
[4]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
[5]: http://www.windowsazure.com/en-us/documentation/scripts/?services=web-sites
[6]: http://www.windowsazure.com/en-us/develop/net/
[7]: http://www.windowsazure.com/en-us/develop/php/
[8]: http://www.windowsazure.com/en-us/develop/nodejs/
[9]: http://www.windowsazure.com/en-us/develop/python/
[10]: http://www.windowsazure.com/en-us/documentation/services/sql-database/
[11]: http://www.windowsazure.com/en-us/documentation/services/service-bus/
[12]: http://www.windowsazure.com/en-us/documentation/services/storage/
[13]: http://www.windowsazure.com/en-us/gallery/store/
[14]: http://www.windowsazure.com/en-us/develop/net/common-tasks/enable-ssl-web-site/