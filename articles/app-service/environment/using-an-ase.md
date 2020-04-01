---
title: App Service-környezet használata és kezelése
description: Ismerje meg, hogyan hozhat létre, tehet közzé és méretezhetők fel alkalmazások egy App Service-környezetben. A cikkben található összes gyakori feladat megkeresése.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 8a73c1998203a8696b67a5e7eb3af23898239265
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477619"
---
# <a name="use-an-app-service-environment"></a>App Service-környezet használata

Az App Service-környezet (ASE) az Azure App Service egy alhálózat ba egy ügyfél Azure virtuális hálózati példányában. Az ASE a következőkből áll:

- **Előtér-végek:** Ahol a HTTP vagy HTTPS leáll egy App Service-környezetben.
- **Dolgozók**: Az alkalmazásokat üzemeltető erőforrások.
- **Adatbázis**: A környezetet meghatározó adatokat tartalmazza.
- **Tárolás**: Az ügyfél által közzétett alkalmazások üzemeltetésére szolgál.

Az ASE egy külső vagy belső virtuális IP-cím (VIP) az alkalmazás eléréséhez telepítheti. A külső IP-címzéssel rendelkező központi telepítést általában *külső ASE-nek*nevezik. A belső VIRTUÁLIS IP-című központi telepítést *ILB ASE-nek* nevezik, mert belső terheléselosztót (ILB) használ. Az ILB ASE-ről az [ILB ASE létrehozása és használata][MakeILBASE]című témakörben olvashat bővebben.

## <a name="create-an-app-in-an-ase"></a>Alkalmazás létrehozása ASE-ben

Ha egy alkalmazást egy ASE-ben szeretne létrehozni, ugyanazt a folyamatot használja, mint amikor általában létrehoz egy alkalmazást, de néhány kisebb különbséggel. Új App Service-csomag létrehozásakor:

- Ahelyett, hogy egy földrajzi helyet választana, ahol az alkalmazást telepíti, egy ASE-t választ a helyként.
- Az ASE-ben létrehozott összes App Service-csomag csak egy elkülönített tarifacsomagban lehet.

Ha nem rendelkezik ASE-vel, létrehozhat egyet az [App Service-környezet létrehozása][MakeExternalASE]című útmutató utasításait követve.

Alkalmazás létrehozása ASE-ben:

1. Válassza **az Erőforrás** > létrehozása**Web + Mobile** > **Web App lehetőséget.**

1. Adja meg az alkalmazás nevét. Ha már kiválasztott egy App Service-csomagot egy ASE-ben, az alkalmazás tartományneve az ASE tartománynevét tükrözi:

    ![Alkalmazásnév kiválasztása][1]

1. Válasszon egy előfizetést.

1. Adjon meg egy nevet egy új erőforráscsoportnak, vagy válassza a **Meglévő használata lehetőséget,** és válasszon egyet a legördülő listából.

1. Válassza ki az operációs rendszert.

1. Válasszon ki egy meglévő App Service-csomagot az ASE-ben, vagy hozzon létre egy újat az alábbi lépésekkel:

    a. Az Azure Portal bal oldali menüjében válassza **az Erőforrás létrehozása > webalkalmazás**lehetőséget.

    b. Válassza ki az előfizetést.

    c. Jelölje ki vagy hozza létre az erőforráscsoportot.

    d. Adja meg a webalkalmazás nevét.

    e. Válassza **a Kód** vagy a **DockerContainer lehetőséget.**

    f. Jelöljön ki egy futásidejű vermet.

    g. Válassza a **Linux** vagy **a Windows**lehetőséget. 

    h. Válassza ki az ASE-t a **Régió** legördülő listában. 

    i. Jelöljön ki vagy hozzon létre egy új App Service-csomagot. Új App Service-csomag létrehozása esetén válassza ki a megfelelő **elkülönített** termékváltozat-méretet.

    ![Elkülönített tarifacsomagok][2]

    > [!NOTE]
    > A Linux-alkalmazások és a Windows-alkalmazások nem lehetnek ugyanabban az App Service-csomagban, de ugyanabban az App Service-környezetben lehetnek.
    >

1. Válassza **a Véleményezés + létrehozás**lehetőséget, győződjön meg arról, hogy az adatok helyesek, majd válassza a **Létrehozás lehetőséget.**

## <a name="how-scale-works"></a>A méretezés immára

Minden App Service-alkalmazás egy App Service-csomagban fut. Az App Service-környezetek alkalmazásszolgáltatási csomagokat tartanak, az App Service-csomagok pedig alkalmazásokat. Egy alkalmazás méretezésekénél az App Service-csomag és az ugyanabban a csomagban lévő összes alkalmazás is méretezhető.

Az App Service-csomag méretezésekénél a szükséges infrastruktúra automatikusan hozzáadódik. Az infrastruktúra hozzáadása közben a műveletek méretezése késleltetést okoz. Ha több méretezési műveletet végez egymás után, az első infrastruktúra-méretezési kérelem lesz eltéve, és a többi várólistára kerül. Amikor az első méretezési művelet befejeződik, a többi infrastruktúra-kérelmek együtt működnek. És az infrastruktúra hozzáadásakor az App Service-csomagok vannak hozzárendelve, ha szükséges. Az új App Service-csomag létrehozása önmagában is méretezési művelet, mert további hardvert kér.

A több-bérlős App Service-ben a skálázás azonnali, mert az erőforrások készlete könnyen elérhető, hogy támogassa azt. Az ASE-ben nincs ilyen puffer, és az erőforrások igény alapján vannak lefoglalva.

Egy ASE-ben egy App Service-csomag legfeljebb 100 példányok skálázható. Egy ASE legfeljebb 201 teljes példányok az összes App Service-csomagok az adott ASE-ben.

## <a name="ip-addresses"></a>IP-címek

Az App Service dedikált IP-címet rendelhet egy alkalmazáshoz. Ez a funkció az IP-alapú SSL konfigurálása után érhető el, ahogy azt egy [meglévő egyéni TLS/SSL-tanúsítvány kötése az Azure App Service szolgáltatáshoz][ConfigureSSL]című dokumentum ismerteti. Az ILB ASE-ben nem adhat hozzá további IP-címeket az IP-alapú SSL-hez.

Egy külső ASE, az IP-alapú SSL-t az alkalmazás ugyanúgy konfigurálhatja, mint a több-bérlős App Service. Mindig van egy tartalék cím az ASE-ben, legfeljebb 30 IP-cím. Minden alkalommal, amikor használja az egyiket, egy másik at ad hozzá, hogy egy cím mindig könnyen elérhető legyen. Egy másik IP-cím lefoglalásához időkésleltetés szükséges. Ez a késleltetés megakadályozza az IP-címek gyors egymásutánban történő hozzáadását.

## <a name="front-end-scaling"></a>Előtér-méretezés

Az App Service-csomagok horizontális felskálázásakor a dolgozók automatikusan hozzáadódnak a támogatásukhoz. Minden ASE két előtérrel jön létre. Az előtér-végződések automatikusan horizontális felskálázás a 15 App Service-csomag példányai egy front-end sebességgel. Ha például három App Service-csomaggal rendelkezik, egyenként öt példánysal, akkor összesen 15 példányés három előtér-vége van. Ha összesen 30 példányra skáláz, négy előtér-vége van. Ez a minta a horizontális felskálázáskor folytatódik.

Az alapértelmezés szerint lefoglalt előtér-végződések száma mérsékelt terhelés esetén is jó. Az arány töt példányonként akár egy előtérre is csökkentheti. Módosíthatja az előtér méretét is. Alapértelmezés szerint egymagosak. Az Azure Portalon módosíthatja a méretüket két vagy négy mag helyett.

Van egy díj az arány vagy az előtér méretének megváltoztatásáért. További információ: [Azure App Service-díjszabás.][Pricing] Ha szeretné javítani az ASE terhelhetőségét, nagyobb javulást kap az első méretezés kétmagos előtér-végekre, mielőtt módosítaná a méretezési arányt. Az elülső végek magméretének módosítása az ASE frissítését eredményezi, és a szokásos munkaidőn kívül kell elvégezni.

Előtér-erőforrások az ASE HTTP/HTTPS végpontja. Az alapértelmezett előtér-konfigurációval az előtér-alapú memóriahasználat következetesen 60 százalék körül van. Az előtér-rendszerek méretezésének elsődleges oka a CPU-használat, amelyet elsősorban a HTTPS-forgalom vezérel.

## <a name="app-access"></a>Alkalmazás-hozzáférés

Egy külső ASE-ben az alkalmazás létrehozásához használt tartományutótag *a .&lt; asename&gt;.p.azurewebsites.net*. Ha az ASE neve _external-ase,_ és üzemeltetegy _contoso_ nevű alkalmazást az ASE-ben, akkor a következő URL-címeken éri el:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

A külső ASE létrehozásáról az [App Service-környezet létrehozása című témakörben][MakeExternalASE]talál további információt.

Az ILB ASE-ben az alkalmazás létrehozásához használt tartományutótag *a .&lt; asename&gt;.appserviceenvironment.net*. Ha az ASE neve _ilb-ase,_ és üzemeltet egy _contoso_ nevű alkalmazást az ASE-ben, akkor a következő URL-címeken éri el:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Az ILB ASE létrehozásáról az [ILB ASE létrehozása és használata][MakeILBASE]című témakörben talál további információt.

Az SCM URL-cím a Kudu konzol elérésére vagy az alkalmazás webtelepítés sel történő közzétételére szolgál. A Kudu konzolról további információt az [Azure App Service Kudu konzolja című][Kudu]témakörben talál. A Kudu konzol webes felhasználói felületet biztosít a hibakereséshez, a fájlok feltöltéséhez, a fájlok szerkesztéséhez és még sok máshoz.

## <a name="publishing"></a>Közzététel

Az ASE-ben, csakúgy, mint a több-bérlős App Service, a következő módszerekkel tehet közzé:

- Webes telepítés
- FTP
- Folyamatos integráció (CI)
- Drag and drop a Kudu konzol
- IDE, például Visual Studio, Eclipse vagy IntelliJ IDEA

Külső ASE-vel ezek a közzétételi beállítások ugyanúgy működnek. További információ: [Deployment in Azure App Service][AppDeploy].

A közzététel jelentősen különbözik az ILB ASE-től, amelyhez a közzétételi végpontok csak az ILB-n keresztül érhetők el. Az ILB egy privát IP-cím az ASE alhálózat a virtuális hálózatban. Ha nem rendelkezik hálózati hozzáféréssel az ILB-hez, nem tehet közzé alkalmazásokat az adott ASE-n. Ahogy az [ILB ASE létrehozása és használata][MakeILBASE]című részben is megjegyeztük, konfigurálnia kell a DNS-t a rendszerben lévő alkalmazásokhoz. Ez a követelmény magában foglalja az SCM-végpontot. Ha a végpontok nincsenek megfelelően definiálva, nem tehető közzé. Az IDEs-nek hálózati hozzáféréssel kell rendelkeznie az ILB-hez, hogy közvetlenül közzétehesse azt.

További módosítások nélkül az internetalapú CI-rendszerek, például a GitHub és az Azure DevOps nem működnek ilb ASE-vel, mert a közzétételi végpont nem érhető el az interneten. Engedélyezheti az ILB ASE-ben való közzétételt az Azure DevOps-ból, ha saját üzemeltetésű kiadási ügynököt telepít az ILB ASE-t tartalmazó virtuális hálózatba. Másik lehetőségként használhat olyan CI-rendszert is, amely lekéréses modellt használ, például a Dropboxot.

Az ILB ASE alkalmazásainak közzétételi végpontjai az ILB ASE létrehozásakor megadott tartományt használják. Ezt az alkalmazás közzétételi profiljában és az alkalmazás portálablakában láthatja (az **Áttekintés** > **essentials** és a **Tulajdonságok területen).**

## <a name="storage"></a>Storage

Az ASE 1 TB-os tárhellyel rendelkezik az ASE összes alkalmazásához. Az elkülönített díjszabási termékváltozatban az App Service-csomag alapértelmezés szerint legfeljebb 250 GB-os korláttal rendelkezik. Ha öt vagy több App Service-csomag, ügyeljen arra, hogy ne lépje túl az ASE 1 TB-os korlátját. Ha egy App Service-csomagban a 250 GB-nál nagyobb korlátra van szüksége, forduljon az ügyfélszolgálathoz, és legfeljebb 1 TB-ra módosítsa az App Service-csomag korlátját. A csomagkorlát beállítása kor, még mindig van egy 1 TB-os korlát az összes App Service-csomagok az ASE-ben.

## <a name="logging"></a>Naplózás

Integrálhatja az ASE-t az Azure Monitorral, így az ASE-ről naplókat küldhet az Azure Storage, az Azure Event Hubs vagy a Log Analytics szolgáltatásba. Ezek az elemek ma kerülnek naplózásra:

| Helyzet | Üzenet |
|---------|----------|
| Az ASE nem kifogástalan | A megadott ASE érvénytelen virtuális hálózati konfiguráció miatt nem megfelelő. Az ASE felfüggesztésre kerül, ha a nem megfelelő állapot továbbra is. Győződjön meg arról, https://docs.microsoft.com/azure/app-service/environment/network-infohogy az itt meghatározott iránymutatásokat betartják: . |
| Az ASE alhálózat a helyből már majdnem elfogyott | A megadott ASE egy alhálózatban van, amely szinte nincs szabad hely. Vannak {0} még címek. Ha ezek a címek kimerültek, az ASE nem lesz képes skálázni.  |
| Az ASE megközelíti a teljes példánykorlátot | A megadott ASE megközelíti az ASE teljes példánykorlátját. Jelenleg legfeljebb 201 példányT tartalmazó App Service-csomagpéldányokat tartalmaz. {0} |
| Az ASE nem tudja elérni a függőséget | A megadott ASE nem képes {0}elérni.  Győződjön meg arról, https://docs.microsoft.com/azure/app-service/environment/network-infohogy az itt meghatározott iránymutatásokat betartják: . |
| Az ASE fel van függesztve | A megadott ASE felfüggesztésre kerül. Az ASE felfüggesztése lehet a fiók hiánya vagy érvénytelen virtuális hálózati konfiguráció. Oldja fel a kiváltó ok, és folytassa az ASE a forgalom további kiszolgálásához. |
| Az ASE frissítése megkezdődött | Megkezdődött a platform frissítése a megadott ASE-re. A skálázási műveletek késése várható. |
| Az ASE frissítése befejeződött | A platform frissítése a megadott ASE befejeződött. |
| Megkezdődtek a méretezési műveletek | Az App Service-csomag ( ){0}méretezése megkezdődött. Kívánt {1} állapot:{2} Én munkavállalók.
| A méretezési műveletek befejeződtek | Az App Service-csomag ( ){0}méretezése befejeződött. Jelenlegi {1} állapot:{2} I munkavállalók. |
| A méretezési műveletek sikertelenek voltak | Egy App Service-csomag ({0}) méretezése nem sikerült. Jelenlegi {1} állapot:{2} I munkavállalók. |

Az ASE naplózásának engedélyezése:

1. A portálon nyissa meg a **Diagnosztikai beállítások lehetőséget.**
1. Válassza **a Diagnosztikai beállítás hozzáadása lehetőséget.**
1. Adja meg a naplóintegráció nevét.
1. Jelölje ki és konfigurálja a kívánt naplócélokat.
1. Válassza **az AppServiceEnvironmentPlatformLogs lehetőséget.**

![ASE diagnosztikai napló beállításai][4]

Ha integrálható a Log Analytics szolgáltatással, megtekintheti a naplókat az ASE **portálnaplók** kiválasztásával és az **AppServiceEnvironmentPlatformLogs**lekérdezés létrehozásával.

## <a name="upgrade-preference"></a>Frissítési beállítás

Ha több ASEs, érdemes lehet egyes ases frissíteni kell, mielőtt mások. Az ASE **HostingEnvironment Resource Manager** objektumon belül beállíthat egy értéket a **frissítéspszre .** A **frissítésPreferencia-beállítás** sablon, ARMClient vagy https://resources.azure.com. A három lehetséges érték a következő:

- **Nincs:** Az Azure nem adott kötegben frissíti az ASE-t. Ez az alapértelmezett érték.
- **Korai:** Az ASE az App Service-frissítések első felében frissül.
- **Kése:** Az ASE az App Service-frissítések második felében frissül.

Ha a használata, https://resources.azure.comaz alábbi lépéseket, hogy állítsa be a **frissítésPszértékek:**

1. Nyissa meg resources.azure.com és jelentkezzen be Azure-fiókjával.
1. \/\[Lépjen végig az előfizetések\]\/előfizetési\/\[név\]\/resourceGroup erőforráscsoport névszolgáltatóinak\/a Microsoft.Web\/hostingEnvironments\/\[ASE névhez szükséges erőforrásokon.\]
1. Felül válassza az **Olvasás/írás** lehetőséget.
1. Válassza a **Szerkesztés** elemet.
1. Állítsa be a **frissítésBeállítás** értékét a kívánt három érték egyikére.
1. Válassza a **Javítás**lehetőséget.

![erőforrások azúrkék com megjelenítése][5]

A **frissítésPszek** funkció teszi a legtöbb értelme, ha több ASEs, mert a "korai" ASEs lesz frissítve, mielőtt a "late" ASEs. Ha több ASEs, be kell állítania a fejlesztési és tesztelési ASEs a "korai" és a termelési ASEs a "Late".

## <a name="pricing"></a>Díjszabás

Az *Elkülönített* nevű díjszabási termékváltozat csak ASEs-ekkel használható. Az ASE-ben üzemeltetett összes App Service-csomag az elkülönített díjszabási termékváltozatban található. Az App Service-csomagok elkülönített díjai régiónként eltérőek lehetnek.

Az App Service-csomagok ára mellett az ASE-re is átalánydíj áll. Az átalánydíj nem változik az ASE méretével. Az ASE-infrastruktúráért minden 15 App Service-csomagpéldányután egy további előtér-díj alapértelmezett méretezési arányt fizet.

Ha az alapértelmezett méretezési arány egy előtér minden 15 App Service-csomag példányok nem elég gyors, beállíthatja az arány, amelyelővégek hozzáadása, vagy az előtér-végek méretét. Ha módosítja az arányt vagy a méretet, akkor az alapértelmezés szerint nem hozzáadott előtér-magokért kell fizetnie.

Ha például a skálázási arányt 10-re állítja be, az Alkalmazásszolgáltatási csomagok minden 10 példányához hozzáad egy előtér-végződést. Az átalánydíj minden 15 példányra egy előtér-skálát fedez fel. A skálázási arány 10, akkor fizet díjat a harmadik előtér, amely hozzáadódik a 10 App Service-csomag példányai. Nem kell fizetnie érte, amikor eléri a 15 példányt, mert automatikusan hozzá lett adva.

Ha az előtér méretét két magra állítja, de nem állítja be az arányt, akkor az extra magokért fizet. Az ASE két előtér-végződéssel jön létre, így még az automatikus skálázási küszöbérték alatt is két extra magért fizetne, ha a méretet kétmagos előtér-végekre növelné.

További információ: [Azure App Service-díjszabás.][Pricing]

## <a name="delete-an-ase"></a>ASE törlése

ASE törlése:

1. Válassza a **Törlés** lehetőséget az **App Service-környezet** ablaktábla tetején.

1. Adja meg az ASE nevét annak megerősítéséhez, hogy törölni szeretné. AsE törlésekor is törli az összes tartalom benne.

    ![ASE-törlés][3]

1. Válassza **az OK gombot.**

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
