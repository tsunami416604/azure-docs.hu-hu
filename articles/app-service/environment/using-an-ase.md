---
title: A bevezetési szolgáltató használata és kezelése
description: Alkalmazások létrehozása, közzététele és méretezése Azure App Service-környezetben. Egy dokumentum általános feladatainak megkeresése.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e5c127673e4b0cb6c68aaf0a9790abbf78758670
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687097"
---
# <a name="use-an-app-service-environment"></a>App Service-környezet használata #

Azure App Service Environment a Azure App Service központi telepítése az ügyfél Azure-beli virtuális hálózatában lévő alhálózatba. A következőkből áll:

- **Előtér**: az ELŐTÉR a HTTP/HTTPS megszakítása egy app Service környezetben (bemutató).
- **Dolgozók**: a dolgozók az alkalmazásokat üzemeltető erőforrások.
- **Adatbázis**: az adatbázis a környezetet meghatározó információkat tartalmazza.
- **Tárolás**: a tároló a felhasználó által közzétett alkalmazások üzemeltetésére szolgál.

> [!NOTE]
> A App Service Environment két verziója létezik: ASEv1 és ASEv2. A ASEv1-ben a használat előtt kezelnie kell az erőforrásokat. A ASEv1 konfigurálásával és kezelésével kapcsolatos információkért lásd: [app Service Environment v1 konfigurálása][ConfigureASEv1]. A cikk többi része a ASEv2 koncentrál.
>
>

Az alkalmazások eléréséhez külső vagy belső virtuális IP-cím (ASEv1 és ASEv2) is üzembe helyezhető. A külső virtuális IP-vel való üzembe helyezést általában külső bevezetésnek nevezik. A belső verzió neve a ILB-központnak, mivel belső terheléselosztó (ILB) használatával működik. További információ a ILB-szolgáltatóról: ILB-betanító [létrehozása és használata][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Alkalmazás létrehozása a központban ##

Ha egy alkalmazáshoz szeretne létrehozni egy adatszolgáltatót, ugyanazt a folyamatot kell használnia, mint a normál létrehozásakor, de néhány kisebb eltéréssel. Új App Service terv létrehozásakor:

- Ahelyett, hogy egy földrajzi helyet válasszon az alkalmazás üzembe helyezéséhez, válasszon egy bevezetési helyet.
- A kiegészítő csomagban létrehozott összes App Service csomagnak elkülönített árképzési szinten kell lennie.

Ha nem rendelkezik központilag, a létrehozásához kövesse az [app Service-környezet létrehozása][MakeExternalASE]című témakör utasításait.

Alkalmazás létrehozása a kiegészítőben:

1. Válassza **az erőforrás létrehozása** > **web és mobil** > **webalkalmazás**lehetőséget.

2. Adja meg az alkalmazás nevét. Ha már kiválasztott egy App Service-csomagot a központhoz, az alkalmazás tartományneve a betanító tartománynevet is tartalmazza.

    ![Alkalmazás nevének kiválasztása][1]

1. Válasszon előfizetést.

1. Adja meg az új erőforráscsoport nevét, vagy válassza a **meglévő használata** lehetőséget, és válasszon egyet a legördülő listából.

1. Válassza ki az operációs rendszert. 

1. Válasszon ki egy meglévő App Service-csomagot a központból, vagy hozzon létre egy újat a következő lépések végrehajtásával:

    a. Válassza az **új létrehozása**lehetőséget.

    b. Adja meg a App Service terv nevét.

    c. Válassza ki a központot a **hely** legördülő listából. 
    
    d. Válasszon egy **elkülönített** árképzési szintet. Válassza a **kiválasztás**lehetőséget.

    e. Kattintson az **OK** gombra.
    
    ![Elkülönített árképzési szintek][2]

    > [!NOTE]
    > A Linux-alkalmazások és a Windows-alkalmazások nem lehetnek ugyanabban a App Servicei csomagban, de lehetnek ugyanabban a App Service Environmentban. 
    >

2. Kattintson a **Létrehozás** gombra.

## <a name="how-scale-works"></a>A skálázás működése ##

Minden App Service alkalmazás egy App Service tervben fut. A tároló modell a környezetek App Service csomagok, és a App Service csomagok megtartják az alkalmazásokat. Az alkalmazások skálázásakor a App Service tervet méretezheti, és így az összes alkalmazást átméretezi egy csomagban.

A ASEv2-ben a App Service-csomag skálázásakor a rendszer automatikusan hozzáadja a szükséges infrastruktúrát. Az infrastruktúra hozzáadásakor az idő késlelteti a műveletek méretezését. A ASEv1-ben a szükséges infrastruktúrát fel kell venni a App Service terv létrehozása vagy méretezése előtt. 

A több-bérlős App Service a skálázás általában azonnali, mivel az erőforrások készlete könnyen elérhető a támogatásához. Egy központhoz nem tartoznak ilyen pufferek, és az erőforrások igény szerint vannak lefoglalva.

Egy kiegészítő szolgáltatásban akár 100 példányra is méretezhető. Ezek a 100-példányok egyetlen App Service csomagban lehetnek, vagy több App Service-csomag között oszlanak meg.

## <a name="ip-addresses"></a>IP-címek ##

A App Service képes dedikált IP-címet lefoglalni egy alkalmazáshoz. Ez a képesség az IP-alapú SSL konfigurálása után érhető el, a következő témakörben leírtak szerint: [meglévő egyéni SSL-tanúsítvány kötése Azure app Servicehoz][ConfigureSSL]. Egy központból azonban jelentős kivétel történt. Nem adhat hozzá további IP-címeket az IP-alapú SSL-hez egy ILB-előadásan.

A ASEv1-ben az IP-címeket erőforrásként kell lefoglalni, mielőtt használni lehetne őket. A ASEv2-ben ugyanúgy használhatja őket az alkalmazásból, mint a több-bérlős App Service. A ASEv2-ben mindig van egy tartalék cím, amely akár 30 IP-címet is felhasználhat. Minden alkalommal, amikor használja az egyiket, a rendszer egy másikat ad hozzá, hogy a címek mindig azonnal elérhetők legyenek. Egy másik IP-cím lefoglalására vonatkozó késleltetési idő szükséges, amely megakadályozza az IP-címek gyors örökléssel való hozzáadását.

## <a name="front-end-scaling"></a>Előtér-méretezés ##

A ASEv2-ben a App Service-csomagok vertikális felskálázásakor a rendszer automatikusan hozzáadja a feldolgozókat a támogatáshoz. Minden előkészítés két előtéri végponttal jön létre. Emellett az előtér automatikusan felskálázást végez a App Service-csomagok minden 15 példányára. Ha például 15 példánya van, akkor három előtér van. Ha 30 példányra méretezi a méretezést, akkor négy előtér van, és így tovább.

Az előfizetések számának a legtöbb forgatókönyvnél nagyobbnak kell lennie. Azonban a felskálázás gyorsabb ütemben elvégezhető. Minden öt példány esetében megváltoztathatja az arányt az egyik előtérként. Az arány módosításához díjat számítunk fel. További információ: [Azure app Service díjszabása][Pricing].

Az előtér-erőforrások a szolgáltatáshoz tartozó HTTP/HTTPS-végpont. Az alapértelmezett előtér-konfigurációnál az előtér-memóriahasználat körülbelül 60%-kal egyenletes. Az ügyfelek munkaterhelései nem futnak a kezelőfelületen. A méretezésre vonatkozó alaptényező a CPU, amely elsősorban a HTTPS-forgalomon alapul.

## <a name="app-access"></a>Alkalmazás-hozzáférés ##

Egy külső beadásban az alkalmazások létrehozásakor használt tartomány eltér a több-bérlős App Servicetól. Magában foglalja a kiegészítő csomag nevét. További információ a külső kiegészítő szolgáltatás létrehozásáról: [app Service környezet][MakeExternalASE]létrehozása. Egy külső betekintő tartománynév a következőhöz hasonló: *.&lt;asename&gt;. p.azurewebsites.net*. Ha például a szolgáltató neve _külső-_ bemutató, és egy _contoso_ nevű alkalmazást üzemeltet a kiegészítőben, akkor a következő URL-címeken érheti el:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

A contoso.scm.external-ase.p.azurewebsites.net URL-cím a kudu-konzol eléréséhez, illetve az alkalmazás Web Deploy használatával történő közzétételéhez használható. További információ a kudu-konzolról: [kudu-konzol a Azure app Service][Kudu]. A kudu-konzol webes felhasználói felületet biztosít a hibakereséshez, a fájlok feltöltéséhez és a fájlok szerkesztéséhez.

Egy ILB-bevezetési időszakban a tartományt a telepítéskor kell meghatározni. A ILB-előkészítés létrehozásával kapcsolatos további információkért lásd: ILB-bekészítés [létrehozása és használata][MakeILBASE]. Ha a _ILB-ASE.info_tartománynevet adja meg, az abban található alkalmazások az adott tartományt használják az alkalmazás létrehozása során. A _contoso_nevű alkalmazáshoz az URL-címek a következők:

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Közzététel ##

Mint a több-bérlős App Service esetében, a következőket teheti közzé:

- Webes telepítés.
- FTP.
- Folyamatos integráció.
- Húzza a mutatót a kudu-konzolon.
- IDE, például a Visual Studio, az Eclipse vagy a IntelliJ IDEA.

A külső kiegészítő funkció esetében ezek a közzétételi lehetőségek mindegyike azonosnak kell lennie. További információ: [Deployment in Azure app Service][AppDeploy]. 

A közzétételsel kapcsolatos legfontosabb különbség egy ILB-bevezető. A ILB-kiegészítő szolgáltatással a közzétételi végpontok csak a ILB érhetők el. A ILB egy magánhálózati IP-címen található a virtuális hálózatban lévő bekapcsolási alhálózaton. Ha nincs hálózati hozzáférése a ILB, akkor nem tehet közzé alkalmazásokat az adott kiegészítő csomagon. Amint azt a [ILB-előállítók létrehozása és használata][MakeILBASE]című része feljegyezte, konfigurálnia kell a DNS-t a rendszer alkalmazásaihoz. , Amely tartalmazza az SCM-végpontot. Ha nincsenek megfelelően definiálva, nem tehet közzé. A közvetlen közzétételhez az ide-nek hálózati hozzáférésre is szüksége van a ILB.

Az internetalapú CI-rendszerek (például a GitHub és az Azure DevOps) nem működnek a ILB-bevezetéssel, mert a közzétételi végpont nem érhető el az interneten. Az Azure DevOps esetében úgy is megkerülhető, ha a belső hálózatban telepít egy saját üzemeltetésű kiadási ügynököt, ahol elérheti a ILB. Azt is megteheti, hogy olyan CI-rendszer használatát is használja, amely lekéréses modellt, például Dropbox-t használ.

Az ILB ASE alkalmazásainak közzétételi végpontjai az ILB ASE létrehozásakor megadott tartományt használják. Megtekintheti az alkalmazás közzétételi profiljában és az alkalmazás portál paneljén (az **áttekintés** > **Essentials** , valamint a **Tulajdonságok**részben). 

## <a name="pricing"></a>Díjszabás ##

Az **elkülönített** árképzési SKU csak a ASEv2-hez készült. A ASEv2-ben üzemeltetett összes App Service az elkülönített díjszabási SKU-ban található. Az elkülönített App Service csomag díjszabása régiónként eltérő lehet. 

A App Service-csomagok díjszabásán felül a beadási csomagra vonatkozó átalány is érvényes. Az átalány nem változik a Bea beosztási méretével, és a teljes 15 App Service-os díjcsomag esetében 1 további előtér alapértelmezett skálázási sebességével fizet a bemutató infrastruktúra számára.  

Ha az összes 15 App Service-csomag esetében az 1 előtér alapértelmezett skálázási sebessége nem elég gyors, beállíthatja, hogy a rendszer milyen arányban adja hozzá az előtér-végpontokat, vagy az előtér méretét.  Ha módosítja az arányt vagy a méretet, akkor az előtérbeli, alapértelmezés szerint nem hozzáadott előtér-magokért kell fizetnie.  

Ha például a skálázási arányt 10 értékre állítja, akkor a rendszer a App Service csomagok minden 10 példányához beadja az előtért. A átalánydíjas díj minden 15 példányra kiterjed az egyik előtér skálázási sebességére. A 10-es skálázási aránnyal a 10 App Service csomag példányaihoz hozzáadott harmadik előtéri díjat kell fizetni. Nem kell fizetnie, ha 15 példányt ér el, mert azt automatikusan hozzáadták.

Ha az előtérben beállított méretet 2 mag értékre igazította, de nem módosítja az arányt, akkor a további magokért kell fizetnie.  A kisegítő lehetőség 2 előtérben jön létre, így még az automatikus skálázási küszöb alá is kell fizetnie, ha a méretet 2 Magos előtér-végpontra növelte.

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
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
