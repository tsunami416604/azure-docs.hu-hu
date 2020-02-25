---
title: App Service Environment használata és kezelése
description: Alkalmazások létrehozása, közzététele és méretezése Azure App Service-környezetben. Egy dokumentum általános feladatainak megkeresése.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7be1676c8949cd30d5e1fe93a73afd75a5a9b67f
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565658"
---
# <a name="use-an-app-service-environment"></a>App Service-környezet használata #

A App Service Environment (beszállító) a Azure App Service központi telepítése az ügyfél Azure-beli virtuális hálózatában lévő alhálózatba. A betanítás a következőkből áll:

- **Előtér-végpontok**: az ELŐTÉR a HTTP/HTTPS megszakítása egy app Service környezetben.
- **Dolgozók**: a dolgozók az alkalmazásokat üzemeltető erőforrások.
- **Adatbázis**: az adatbázis a környezetet meghatározó információkat tartalmazza.
- **Tárolás**: a tároló a felhasználó által közzétett alkalmazások üzemeltetésére szolgál.

Az alkalmazás-hozzáféréshez külső vagy belső VIP-t is telepíthet. A külső virtuális IP-vel való üzembe helyezést általában külső bevezetésnek nevezik. A belső verzió neve a ILB-központnak, mivel belső terheléselosztó (ILB) használatával működik. További információ a ILB-szolgáltatóról: ILB-betanító [létrehozása és használata][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Alkalmazás létrehozása a központban ##

Ha egy alkalmazáshoz szeretne létrehozni egy adatszolgáltatót, ugyanazt a folyamatot kell használnia, mint a normál létrehozásakor, de néhány kisebb eltéréssel. Új App Service-csomag (ASP) létrehozásakor:

- Ahelyett, hogy egy földrajzi helyet válasszon az alkalmazás üzembe helyezéséhez, válasszon egy bevezetési helyet.
- A teljes előállítók által létrehozott App Service csomagok csak elszigetelt árképzési szinten lehetnek.

Ha nem rendelkezik központilag, a létrehozásához kövesse az [app Service-környezet létrehozása][MakeExternalASE]című témakör utasításait.

Alkalmazás létrehozása a kiegészítőben:

1. Válassza **az erőforrás létrehozása** > **web és mobil** > **webalkalmazás**lehetőséget.

2. Adja meg az alkalmazás nevét. Ha már kiválasztott egy App Service-csomagot a központhoz, az alkalmazás tartományneve a betanító tartománynevet is tartalmazza.

    ![Alkalmazás nevének kiválasztása][1]

1. Válasszon egy előfizetést.

1. Adja meg az új erőforráscsoport nevét, vagy válassza a **meglévő használata** lehetőséget, és válasszon egyet a legördülő listából.

1. Válassza ki az operációs rendszert. 

1. Válasszon ki egy meglévő App Service-csomagot a központból, vagy hozzon létre egy újat a következő lépések végrehajtásával:

    a. A Azure Portal bal oldali menüben válassza az **erőforrás létrehozása > webalkalmazás**lehetőséget.

    b. Válassza ki az előfizetést.
    
    c. Válassza ki vagy hozza létre az erőforráscsoportot.
    
    d. Adja meg a webalkalmazás nevét.
    
    e. Válassza ki a kód-vagy DockerContainer.
    
    f. Válassza a futásidejű verem lehetőséget.
    
    g. Válassza a Linux vagy a Windows lehetőséget. 
    
    h. Válassza ki a központot a **régió** legördülő listából. 
    
    i. Válasszon ki vagy hozzon létre egy új App Service csomagot. Ha új App Service tervet hoz létre, válassza ki a megfelelő **elkülönített** SKU-méretet.
    
    ![Elkülönített árképzési szintek][2]

    > [!NOTE]
    > A Linux-alkalmazások és a Windows-alkalmazások nem lehetnek ugyanabban a App Servicei csomagban, de lehetnek ugyanabban a App Service Environmentban. 
    >

2. Válassza a **felülvizsgálat + létrehozás** lehetőséget, majd válassza a **Létrehozás** elemet, ha az adatok helyesek.

## <a name="how-scale-works"></a>A skálázás működése ##

Minden App Service alkalmazás egy App Service tervben fut. App Service környezetek App Service terveket tartanak fenn, és a App Service csomagok megtartják az alkalmazásokat. Az alkalmazások skálázásakor a App Service tervet méretezheti, és így az összes alkalmazást átméretezi egy csomagban.

App Service csomag skálázásakor a rendszer automatikusan hozzáadja a szükséges infrastruktúrát. Az infrastruktúra hozzáadásakor az idő késlelteti a műveletek méretezését. Ha több méretezési műveletet hajt végre a sorozatban, az első infrastruktúra-méretezési kérést a rendszer a többi sorba helyezi. Ha az első skálázási művelet befejeződik, a többi infrastruktúra-kérelem együtt működik. Az infrastruktúra hozzáadásakor a App Service csomagokat a rendszer a megfelelő módon rendeli hozzá. Egy új App Service terv létrehozása maga a méretezési művelet, mivel további hardvert kér. 

A több-bérlős App Service esetén a skálázás azonnal megtörténik, mivel az erőforrások készlete azonnal elérhetővé válik a támogatásához. Egy központhoz nem tartoznak ilyen pufferek, és az erőforrások igény szerint vannak lefoglalva.

Egy központból akár 100 példányra is méretezheti az App Service-tervet. A központnak összesen 201 teljes példánya lehet a központhoz tartozó összes App Service csomag esetében. 

## <a name="ip-addresses"></a>IP-címek ##

A App Service képes dedikált IP-címet lefoglalni egy alkalmazáshoz. Az IP-alapú SSL konfigurálása után egy dedikált IP-cím kiosztásának lehetősége elérhető az alkalmazáshoz, a következő témakörben leírtak szerint: [meglévő egyéni SSL-tanúsítvány kötése Azure app Servicehoz][ConfigureSSL]. Egy ILB-ben nem adhat hozzá IP-alapú SSL-hez használandó további IP-címeket.

A külső kiegészítő szolgáltatással az alkalmazáshoz tartozó IP-alapú SSL-t ugyanúgy konfigurálhatja, mint a több-bérlős App Service. A szolgáltatásban mindig van egy tartalék cím, amely akár 30 IP-címet is felhasználhat. Minden alkalommal, amikor használja az egyiket, a rendszer egy másikat ad hozzá, hogy a címek mindig azonnal elérhetők legyenek. Egy másik IP-cím lefoglalására vonatkozó késleltetési idő szükséges, amely megakadályozza az IP-címek gyors örökléssel való hozzáadását.

## <a name="front-end-scaling"></a>Előtér-méretezés ##

A App Service-csomagok méretezése után a rendszer automatikusan hozzáadja a feldolgozókat a támogatáshoz. Minden kiegészítő szolgáltatás két előtérben jön létre. A rendszer automatikusan kibővíti az előtérben lévő összes 15 App Service-es csomag példányát egy előtérben. Ha három App Service-csomaggal rendelkezik, amelyek mindegyike öt példányból áll, akkor összesen 15 példányt és három előtért fog tartalmazni. Ha összesen 30 példányra méretezi a méretezést, akkor négy előtérben és így tovább. 

Az alapértelmezés szerint lefoglalt előtér-végpontok száma megfelelő a mérsékelt terheléshez. Az arányt beállíthatja úgy, hogy a rendszer minden öt példány esetében csak egy előtérben legyen. Az előtér méretét is módosíthatja. Alapértelmezés szerint egyetlen mag. Ehelyett megváltoztathatja a portálon az előtér-végpontok méretét kettő vagy négy fő méretre. Az arány vagy az előtérben lévő méretek módosításához díjat számítunk fel. További információ: [Azure app Service díjszabása][Pricing]. Ha javítani szeretné a betöltési kapacitást, akkor a méretezési arány módosítása előtt az első skálázás két alapszintű előtér-végponttal is javulni fog. Ha megváltoztatja az előtér alapméretét, a rendszer frissíti a központot, és a szokásos munkaidőn kívül kell végeznie.

Az előtér-erőforrások a központhoz tartozó HTTP/HTTPS-végpontok. Az alapértelmezett előtér-konfigurációnál az előtér-memóriahasználat körülbelül 60%-kal egyenletes. Az előtér-méretezés elsődleges oka a CPU, amelyet elsődlegesen a HTTPS-forgalom vezérel.

## <a name="app-access"></a>Alkalmazás-hozzáférés ##

Külső kiegészítő szolgáltatásban az alkalmazás létrehozásához használt tartománynév a *.&lt;asename&gt;. p.azurewebsites.net*. Ha a szolgáltató neve _külső-_ bemutató, és egy _contoso_ nevű alkalmazást üzemeltet a kiegészítőben, akkor a következő URL-címeken érheti el:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

További információ a külső kiegészítő szolgáltatás létrehozásáról: [app Service környezet létrehozása][MakeExternalASE]

A ILB-ben az alkalmazás létrehozásához használt tartománynév a *.&lt;asename&gt;. appserviceenvironment.net*. Ha a szolgáltató neve _ILB-_ befogadó, és a _contoso_ nevű alkalmazást üzemelteti a kiegészítő szolgáltatásban, akkor a következő URL-címeken érheti el:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

A ILB-előkészítés létrehozásával kapcsolatos további információkért lásd: ILB-bekészítés [létrehozása és használata][MakeILBASE]. 

Az SCM URL-cím a kudu-konzol eléréséhez, illetve az alkalmazás Web Deploy használatával történő közzétételéhez használható. További információ a kudu-konzolról: [kudu-konzol a Azure app Service][Kudu]. A kudu-konzol webes felhasználói felületet biztosít a hibakereséshez, a fájlok feltöltéséhez és a fájlok szerkesztéséhez.

## <a name="publishing"></a>Közzététel ##

Mint a több-bérlős App Service esetében, a következőket teheti közzé:

- Webes telepítés.
- FTP.
- Folyamatos integráció.
- Húzza a mutatót a kudu-konzolon.
- IDE, például a Visual Studio, az Eclipse vagy a IntelliJ IDEA.

A külső kiegészítő funkció esetében ezek a közzétételi lehetőségek mindegyike azonosnak kell lennie. További információ: [Deployment in Azure app Service][AppDeploy]. 

A közzétételsel kapcsolatos legfontosabb különbség egy ILB-bevezető. A ILB-kiegészítő szolgáltatással a közzétételi végpontok csak a ILB érhetők el. A ILB egy magánhálózati IP-címen található a virtuális hálózatban lévő bekapcsolási alhálózaton. Ha nincs hálózati hozzáférése a ILB, akkor nem tehet közzé alkalmazásokat az adott kiegészítő csomagon. Amint azt a [ILB-előállítók létrehozása és használata][MakeILBASE]című része feljegyezte, konfigurálnia kell a DNS-t a rendszer alkalmazásaihoz. , Amely tartalmazza az SCM-végpontot. Ha nincsenek megfelelően definiálva, nem tehet közzé. A közvetlen közzétételhez az ide-nek hálózati hozzáférésre is szüksége van a ILB.

Az internetalapú CI-rendszerek (például a GitHub és az Azure DevOps) nem működnek a ILB-bevezetéssel, mert a közzétételi végpont nem érhető el az interneten. Engedélyezheti az Azure DevOps-ből származó ILB-előállítók közzétételét egy saját üzemeltetésű kiadási ügynök telepítésével a VNet-ben, amely a ILB-t tartalmazza. Azt is megteheti, hogy olyan CI-rendszer használatát is használja, amely lekéréses modellt, például Dropbox-t használ.

Az ILB ASE alkalmazásainak közzétételi végpontjai az ILB ASE létrehozásakor megadott tartományt használják. Megtekintheti az alkalmazás közzétételi profiljában és az alkalmazás portál paneljén (az **áttekintés** > **Essentials** , valamint a **Tulajdonságok**részben). 

## <a name="storage"></a>Storage

A kiegészítő csomag 1 TB tárterülettel rendelkezik a kiegészítő csomagban található összes alkalmazáshoz. Az elkülönített SKU App Service csomag alapértelmezés szerint 250 GB-os korlátot tartalmaz. Ha öt vagy több App Service-csomaggal rendelkezik, ügyelnie kell arra, hogy ne lépje túl a kiegészítő csomag 1 TB-os korlátját. Ha egy App Service-csomagban több, mint a 250 GB-os korlátra van szüksége, forduljon az ügyfélszolgálathoz, és állítsa be a App Service-csomag korlátját legfeljebb 1 TB-ra. Ha a csomagra vonatkozó korlátot módosítják, akkor a kiegészítő csomagban található összes App Service-csomag esetében továbbra is 1 TB korlát szerepel. 

## <a name="logging"></a>Naplózás ##

Integrálhatja a szolgáltatót a Azure Monitor-nal, hogy a központhoz tartozó naplókat a Storage, az Event hub vagy a Log Analytics segítségével küldje el. A mai napon naplózott elemek a következők:

| Helyzet | Üzenet |
|---------|----------|
| A Bea nem kifogástalan állapotú | A megadott beadási állapot nem kifogástalan, mert érvénytelen a virtuális hálózati konfiguráció. Ha a sérült állapot továbbra is fennáll, a rendszer felfüggeszti a bevezetés állapotát. Győződjön meg arról, hogy az itt definiált irányelvek követik: https://docs.microsoft.com/azure/app-service/environment/network-info |
| A bevezetési alhálózat csaknem el van szabadítva | A megadott beadási pont egy olyan alhálózaton van, amely szinte nem elegendő. {0} fennmaradó címek vannak. A címek kimerítése után a bevezetés nem fogja tudni méretezni a  |
| A Bea teljes példány korlátja közeledik | A megadott beadási szolgáltatás a teljes körű beadási korláthoz közeledik. Jelenleg {0} App Service a maximális 201 példányok megtervezése. |
| A kiegészítő szolgáltatás nem tudja elérni a függőséget | A megadott beadási szolgáltatás nem tudja elérni a {0}.  Győződjön meg arról, hogy az itt definiált irányelvek követik: https://docs.microsoft.com/azure/app-service/environment/network-info |
| A beépítés fel van függesztve | A megadott beadási szolgáltatás fel van függesztve. A bevonási felfüggesztés oka lehet a fiók hiánya vagy az érvénytelen virtuális hálózati konfiguráció. Oldja meg a kiváltó okot, és folytassa a beszállítást, hogy továbbra is kiszolgálja a forgalmat |
| A bevezető csomag frissítése megkezdődött | Megkezdődött a platform frissítése a megadott beadásra. Késések várhatók a skálázási műveletekben |
| A betöltési frissítés befejeződött | A megadott beadási platformra történő verziófrissítés befejeződött |
| A skálázási műveletek elkezdődtek | Egy App Service csomag ({0}) megkezdte a skálázást. Kívánt állapot: {1}{2} feldolgozók 
| A skálázási műveletek befejeződtek | Egy App Service csomag ({0}) befejezte a méretezést. Jelenlegi állapot: {1}{2} feldolgozók |
| A skálázási műveletek sikertelenek voltak | Nem sikerült méretezni a App Service tervet ({0}). Jelenlegi állapot: {1}{2} feldolgozók |

A beléptetés beléptetésének engedélyezése: 

1. A portálon keresse meg a diagnosztikai beállításokat.  
1. Válassza a diagnosztikai beállítás hozzáadása lehetőséget.
1. Adja meg a naplózási integráció nevét
1. Keresse meg és konfigurálja a kívánt napló célhelyeit. 
1. AppServiceEnvironmentPlatformLogs-ellenőrzési

![A beadási diagnosztikai napló beállításai][4]

Ha integrálja Log Analyticsokkal, a naplókat a beadási portál naplók elemének kiválasztásával, valamint a AppServiceEnvironmentPlatformLogs-lekérdezés létrehozásával tekintheti meg. 

## <a name="upgrade-preference"></a>Frissítési beállítások ##

Ha több ASE is rendelkezik, előfordulhat, hogy néhány ASE kell frissítenie mások előtt. A üzemeltetési Resource Manager-objektumon belül megadhatja a UpgradePreference értékét. A upgradePreference beállítását sablon, ARMClient vagy https://resources.azure.comhasználatával lehet konfigurálni.  A három érték közül választhat:

* None – nincs az alapértelmezett érték, és azt jelenti, hogy az Azure nem adott kötegben fogja frissíteni a Bea-t
* A korai állapot azt jelenti, hogy a bevezetés a App Service frissítéseinek első felében frissül
* A késői késői állapot azt jelenti, hogy a bevezetés a App Service frissítéseinek második felében lesz frissítve

Ha https://resources.azure.comhasznál, a upgradePreferences értékét a következők szerint állíthatja be:

1. Resources.azure.com és bejelentkezés az Azure-fiókkal
1. Navigáljon az előfizetések\/\[előfizetés neve\]\/resourceGroups\/\[erőforráscsoport neve\]\/szolgáltatók\/Microsoft. Web\/hostingEnvironments\/\[\]
1. Az olvasás/írás lehetőség kiválasztása felül
1. Szerkesztés kiválasztása
1. Módosítsa a upgradePreference értékét a három lehetőség közül a kívánt értékre.
1. Javítás kiválasztása

![erőforrások Azure com-megjelenítés][5]

A upgradePreferences funkció valójában a legalkalmasabb, ha több ASE van, mivel a "korai" frissített ASE a "késő" ASE előtt frissülni fog. Ha több ASE rendelkezik, a fejlesztési és tesztelési ASE "korai" értékre kell állítani, és az éles ASE "késő" értékre kell állítani.

## <a name="pricing"></a>Díjszabás ##

Az **elkülönített** árképzési SKU csak a benyújtó szolgáltatással használható. A központon belül futtatott összes App Service elkülönített díjszabási SKU-ban található. Az elkülönített App Service csomag díjszabása régiónként eltérő lehet. 

A App Service-csomagok díjszabásán felül a beadási csomagra vonatkozó átalány is érvényes. Az átalány nem változik a Bea beosztási méretével, és a teljes 15 App Service díjcsomag esetében egy további előtér alapértelmezett skálázási sebességével fizet a benyújtó infrastruktúra számára.  

Ha egy előtér alapértelmezett skálázási sebessége minden 15 App Service-csomag esetében nem elég gyors, beállíthatja, hogy milyen arányban legyenek hozzáadva az előtér-végpontok, vagy az előtér mérete.  Ha módosítja az arányt vagy a méretet, akkor az olyan előtér-magokért kell fizetnie, amelyek alapértelmezés szerint nem lesznek hozzáadva.  

Ha például a skálázási arányt 10 értékre állítja, a rendszer a App Service-csomagok minden 10 példányához bekerül egy előtérbe. A átalánydíjas díj minden 15 példányra kiterjed egy előtérbeli skálázási sebességre. A 10 méretarányú skálázási arányban díjat számítunk fel a 10 App Service csomag példányaihoz hozzáadott harmadik előtéri előfizetésért. Nem kell fizetnie, ha 15 példányt ér el, mert azt automatikusan hozzáadták.

Ha az előtér méretét két mag fölé igazította, de nem módosítja az arányt, akkor a további magokért kell fizetnie.  A kisegítő lehetőség két előtérrel jön létre, így még az automatikus skálázási küszöb alatt is kifizethető két további mag, ha a méretet két alapszintű előtérben növelte.

További információ: [Azure app Service díjszabása][Pricing].

## <a name="delete-an-ase"></a>Bemutató törlése ##

A bemutató rendszer törlése: 

1. Használja a **delete (Törlés** ) parancsot a **app Service Environment** panel tetején. 

1. Adja meg a beadási hely nevét annak megerősítéséhez, hogy törölni szeretné. Ha töröl egy beadást, akkor a benne található összes tartalmat is törli. 

    ![Bemutatás törlése][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
