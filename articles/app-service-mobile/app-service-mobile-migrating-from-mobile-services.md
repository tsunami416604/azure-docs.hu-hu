---
title: "Mobilszolgáltatások telepítenek át egy App Service mobilalkalmazás"
description: "Útmutató: a Mobile Services alkalmazás az App Service Mobile Apps könnyen áttelepítése"
services: app-service\mobile
documentationcenter: 
author: conceptdev
manager: crdun
editor: 
ms.assetid: 07507ea2-690f-4f79-8776-3375e2adeb9e
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: crdun
ms.openlocfilehash: e94f08b9b9dad20c6f47367c47eb49aea59f4bd8
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="article-top"></a>A meglévő Azure Mobile szolgáltatás áttelepítése az Azure App Service
Az a [Azure App Service általános rendelkezésre állását], Azure Mobile Services-webhelyek könnyen áttelepíthető helyi előnyeit az Azure App Service összes funkcióját.  Ez a dokumentum azt ismerteti, mi történik, ha az Azure App Service telepítheti át webhelyét az Azure Mobile Services.

## <a name="what-does-migration-do"></a>Mire áttelepítési? és a hely számára
Az Azure Mobile szolgáltatás kapcsolja be a Mobile Service egy [Azure App Service] app anélkül, hogy befolyásolná a kódot.  A Notification Hubs, SQL adatkapcsolat, hitelesítési beállításai, ütemezett feladatokat, és tartománynév változatlanok maradnak.  Az Azure Mobile szolgáltatással mobil ügyfelek továbbra is megfelelően működik.  Áttelepítési újraindítja a szolgáltatást, miután a rendszer áthelyezi az Azure App Service.

[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Miért át kell telepítenie a webhely
Microsoft van javasolja, hogy az áttelepített az Azure Mobile szolgáltatás számára, hogy kihasználja az Azure App Service, beleértve a szolgáltatásait:

* Új üzemeltetési funkciók, például a [WebJobs] és [egyéni tartománynevek].
* Figyelés és hibaelhárítás [Application Insights].
* Beépített DevOps tooling, beleértve a [átmeneti üzembe helyezési ponti]visszaállítási és az éles tesztelése.
* [Automatikus méretezése], terheléselosztás, és [teljesítményfigyelés].

Az Azure App Service előnyeiről további információkért lásd: a [Mobile Services vs. App Service] témakör.

## <a name="before-you-begin"></a>Előkészületek
A webhely fő munka megkezdése előtt készítsen biztonsági másolatot a Mobile Service parancsfájlok és az SQL-adatbázis.

## <a name="migrating-site"></a>A helyek áttelepítése
Az áttelepítési folyamat minden hely egyetlen Azure-régión belül áttelepíti.

A webhely áttelepítése:

1. Jelentkezzen be a [a klasszikus Azure portálon].
2. Válassza ki a Mobile Service áttelepíteni kívánt régiót.
3. Kattintson a **áttelepítése az App Service** gombra.

   ![Az áttelepítés gomb][0]
4. Az áttelepítés az App Service párbeszédpanelen.
5. Adja meg a Mobile Service nevét található megfelelő mezőbe.  Ha a tartomány neve contoso.azure-mobile.net, majd írja be például *contoso* található megfelelő mezőbe.
6. Az osztásjelek gombra.

A tevékenység figyelése az áttelepítés állapotának figyelése. A webhely jelenik meg, *áttelepítése* a klasszikus Azure-portálon.

  ![Áttelepítési tevékenység figyelése][1]

Minden áttelepítési is igénybe vehet 3 az áttelepítés alatt álló mobilszolgáltatás 15 percig.  A webhely az áttelepítés közben elérhető marad.
Az áttelepítési folyamat végén a webhely újraindítása.  A hely nem érhető el az újraindítási folyamatot, amely lehet akár egy néhány másodperc alatt.

## <a name="finalizing-migration"></a>Az áttelepítés befejezése
Tervezze meg a webhely egy mobil ügyfél lezárásakor az áttelepítési folyamat tesztelése.  Gondoskodjon arról, hogy minden közös Ügyfélműveletek a mobil ügyfél módosítása nélkül végezheti el.  

### <a name="update-app-service-tier"></a>Válassza ki a megfelelő IP-címek alkalmazásszolgáltatás
Miután áttelepítette az Azure App Service díjszabás több beleszólása van.

1. Jelentkezzen be az [Azure portálra].
2. Válassza ki **összes erőforrás** vagy **alkalmazásszolgáltatások** kattintson az áttelepített Mobile szolgáltatás nevét.
3. Alapértelmezés szerint a beállítások panel nyílik meg.
4. Kattintson a **App Service-csomag** a beállítások menüben.
5. Kattintson a **Tarifacsomagot** csempére.
6. Kattintson a csempére a követelményeinek megfelelő, majd **válasszon**.  Kattintson esetleg **összes** az elérhető árképzési szinteket megjelenítéséhez.

Kiindulási pontként a következő rétegek javasoljuk:

| Mobilszolgáltatás Tarifacsomagot | Az App Service Tarifacsomagot |
|:--- |:--- |
| Ingyenes |F1 – Ingyenes |
| Alapszintű |B1 Basic |
| Standard |S1 – Standard |

Nincs rugalmasan kiválasztani a tarifacsomag az alkalmazáshoz.  Tekintse meg [App Service szolgáltatás díjszabása] az új App Service díjszabás teljes leírását.

> [!TIP]
> App Service Standard csomagra tartalmaz számos funkciók, előfordulhat, hogy szeretne használni, beleértve a [átmeneti üzembe helyezési ponti], automatikus biztonsági mentésekhez, és az automatikus skálázást.  Tekintse meg az új képességek, amíg hiba nem!
>
>

### <a name="review-migration-scheduler-jobs"></a>Tekintse át az áttelepített Feladatütemező feladatai
A Feladatütemező szolgáltatás körülbelül 30 percet az áttelepítés után nem látható lesz.  Ütemezett feladatok továbbra is fut a háttérben.
Után szeretné megtekinteni az ütemezett feladatok jelenjenek újra:

1. Jelentkezzen be az [Azure portálra].
2. Válassza ki **Tallózás >**, adja meg **ütemezés** a a *szűrő* mezőbe, majd válassza ki **Feladatütemező gyűjtemények**.

Ingyenes scheduler feladatok érhető el az áttelepítést követő korlátozott számú van.  Tekintse át a használati és a [Azure Scheduler tervek].

### <a name="configure-cors"></a>A CORS konfigurálása, ha szükséges
Eltérő eredetű erőforrások megosztása a engedélyezi a webhely egy másik tartományban Web API eléréséhez technika.  Ha az Azure Mobile Services webhelyekhez használ, majd kell a CORS konfigurálása az áttelepítés részeként.  Azure Mobile Services kizárólag a mobileszközök érnek el, ha majd CORS nem kell bizonyos ritkán előforduló esetekben kivéve kell konfigurálni.

Az áttelepített CORS-beállítások állnak rendelkezésre, a **MS_CrossDomainWhitelist** Alkalmazásbeállítás.  A webhely áttelepítése az App Service CORS-létesítményt a személyes:

1. Jelentkezzen be az [Azure portálra].
2. Válassza ki **összes erőforrás** vagy **alkalmazásszolgáltatások** kattintson az áttelepített Mobile szolgáltatás nevét.
3. Alapértelmezés szerint a beállítások panel nyílik meg.
4. Kattintson a **CORS** az API menüjében.
5. Adjon meg egy engedélyezett eredetet található megfelelő mezőbe, minden egyes után nyomja le az ENTER billentyűt.
6. Ha az engedélyezett Eredeteket listája helyes-e, kattintson a Mentés gombra.

> [!TIP]
> Az Azure App Service előnyei egyik futtathatja a mobilszolgáltatás és a webhely ugyanazon a helyen.  További információkért lásd: a [lépések](#next-steps) szakasz.
>
>

### <a name="download-publish-profile"></a>Egy új közzétételi profil letöltése
A közzétételi profil a webhely megváltozik, miközben az Azure App Service áttelepítése.  Ha azt tervezi, a webhely a Visual Studio közzététele, egy új közzétételi profilt kell.  Az új közzétételi profil letöltése:

1. Jelentkezzen be az [Azure portálra].
2. Válassza ki **összes erőforrás** vagy **alkalmazásszolgáltatások** kattintson az áttelepített Mobile szolgáltatás nevét.
3. Kattintson a **Get közzétételi profil**.

A PublishSettings-fájl a számítógép letölti.  Általában nevezik *sitename*. PublishSettings.  A közzétételi beállítások importálása a létező projekt:

1. Nyissa meg a Visual Studio és az Azure Mobile Services mobilszolgáltatás-projektet.
2. Kattintson a jobb gombbal a projektre a a **Megoldáskezelőben** válassza **közzététel...**
3. Kattintson az **Importálás** gombra
4. Kattintson a **Tallózás** válassza ki a letöltött közzététele beállításfájl.  Kattintson az **OK** gombra
5. Kattintson a **kapcsolat ellenőrzése** a közzétételi beállítások munkahelyi biztosításához.
6. Kattintson a **közzététel** a webhely közzététele.

## <a name="working-with-your-site"></a>Az áttelepítés utáni webhely használata
Az új App Service a munka megkezdéséhez a [Azure portálra] áttelepítés utáni.  Az alábbiakban néhány Megjegyzés a konkrét műveletek végrehajtásához használt a [a klasszikus Azure portálon]App Service-megfelelőjükkel együtt,.

### <a name="publishing-your-site"></a>Töltsön le és az áttelepített hely közzététele
A webhely git- vagy ftp segítségével érhető el, és képes különböző különböző mechanizmusokkal, beleértve a WebDeploy, TFS, Mercurial, GitHub vagy FTP újból közzé.  Az üzembe helyezési hitelesítő adatok áttelepítése a hely többi részének.  Ha nem állított az üzembe helyezési hitelesítő adatok, vagy nem tárolja őket, visszaállíthatja őket:

1. Jelentkezzen be az [Azure portálra].
2. Válassza ki **összes erőforrás** vagy **alkalmazásszolgáltatások** kattintson az áttelepített Mobile szolgáltatás nevét.
3. Alapértelmezés szerint a beállítások panel nyílik meg.
4. Kattintson a **üzembe helyezési hitelesítő adatok** a közzététel a menüben.
5. Adja meg az új üzembe helyezési hitelesítő adatok a megfelelő mezőkbe, majd kattintson a Mentés gombra.

Ezek a hitelesítő adatok segítségével klónozza a hely a gitet, vagy állítsa be automatikus telepítések a Githubból, TFS vagy Mercurial.  További információkért lásd: a [Azure App Service üzembe helyezési dokumentációja].

### <a name="appsettings"></a>Alkalmazásbeállítások
A legtöbb beállítások áttelepített mobilszolgáltatás Alkalmazásbeállítások keresztül érhetők el.  Kaphat az alkalmazás beállítások listáját a [Azure portálra].
Megtekintéséhez, vagy az alkalmazás beállításainak módosítása:

1. Jelentkezzen be az [Azure portálra].
2. Válassza ki **összes erőforrás** vagy **alkalmazásszolgáltatások** kattintson az áttelepített Mobile szolgáltatás nevét.
3. Alapértelmezés szerint a beállítások panel nyílik meg.
4. Kattintson a **Alkalmazásbeállítások** az általános menüjében.
5. Görgessen az alkalmazásbeállítások szakaszhoz, és keresse meg az alkalmazásbeállításhoz.
6. Kattintson az Alkalmazásbeállítás értéke pedig az az érték szerkesztéséhez.  Kattintson a **mentése** mentheti az értéket.

Egyszerre több alkalmazás beállításai frissítheti.

> [!TIP]
> Nincsenek két Alkalmazásbeállítások ugyanarra az értékre.  Például előfordulhat, hogy látja *ApplicationKey* és *MS\_ApplicationKey*.  Mindkét Alkalmazásbeállítások frissítése egy időben.
>
>

### <a name="authentication"></a>Hitelesítés
Minden hitelesítési beállítások állnak rendelkezésre, a áttelepített hely Alkalmazásbeállítások.  Frissítse a hitelesítési beállításokat, meg kell változtatnia a megfelelő alkalmazás beállításai.  Az alábbi táblázat a hitelesítésszolgáltató a megfelelő alkalmazás beállításait:

| Szolgáltató | Ügyfél-azonosító | Titkos ügyfélkulcs | Egyéb beállítások |
|:--- |:--- |:--- |:--- |
| Microsoft-fiók |**MS\_MicrosoftClientID** |**MS\_MicrosoftClientSecret** |**MS\_MicrosoftPackageSID** |
| Facebook |**MS\_FacebookAppID** |**MS\_FacebookAppSecret** | |
| Twitter |**MS\_TwitterConsumerKey** |**MS\_TwitterConsumerSecret** | |
| Google |**MS\_GoogleClientID** |**MS\_GoogleClientSecret** | |
| Azure AD |**MS\_AadClientID** | |**MS\_AadTenants** |

Megjegyzés: **MS\_AadTenants** bérlői tartományok (a "Engedélyezett bérlők" mezőket a Mobile Services portálon) vesszővel tagolt listáját tárolja.

> [!WARNING]
> **Ne használja a beállítási menüben a hitelesítési mechanizmusok**
>
> Az Azure App Service biztosít egy külön "no-kód" hitelesítési és engedélyezési rendszer alatt a *hitelesítési / engedélyezési* menüpontra, és a (elavult) *Mobile hitelesítési* beállítás a beállítások menüben.  Ezek a beállítások nem kompatibilisek egy áttelepített Azure Mobile Service.  Is [a hely frissítése](app-service-mobile-net-upgrading-from-mobile-services.md) az Azure App Service hitelesítés előnyeinek kihasználása érdekében.
>
>

### <a name="easytables"></a>Data
A *adatok* a Mobile Servicesben lap váltotta *könnyen táblák* az Azure portálon.  Egyszerű táblák elérése:

1. Jelentkezzen be az [Azure portálra].
2. Válassza ki **összes erőforrás** vagy **alkalmazásszolgáltatások** kattintson az áttelepített Mobile szolgáltatás nevét.
3. Alapértelmezés szerint a beállítások panel nyílik meg.
4. Kattintson a **könnyen táblák** a mobil menüben.

Egy tábla kattintva vehet fel a **Hozzáadás** gombra kattint, vagy nem érhető el a létező táblák gombra kattintva egy tábla nevét.  Ezen a panelen elvégezhető különböző műveletek többek között:

* Tábla engedélyek módosítása
* A működési parancsfájlok szerkesztése
* A következő tábla sémáját kezelése
* A tábla törlése
* Tartalom törlése
* A táblázat sorait törlése

### <a name="easyapis"></a>API
A *API* a Mobile Servicesben lap váltotta *egyszerű API-k* az Azure portálon.  Egyszerű API-k elérésére:

1. Jelentkezzen be az [Azure portálra].
2. Válassza ki **összes erőforrás** vagy **alkalmazásszolgáltatások** kattintson az áttelepített Mobile szolgáltatás nevét.
3. Alapértelmezés szerint a beállítások panel nyílik meg.
4. Kattintson a **egyszerű API-k** a mobil menüben.

Az áttelepített API-kat már szerepel a panelt.  Azt is megteheti az API-k ezen a panelen.  Egy adott API kezeléséhez kattintson az API-t.
Új paneljén módosítsa az engedélyeket, és a parancsfájlok szerkesztése az API-hoz.

### <a name="on-demand-jobs"></a>Feladatütemező feladatai
Az összes Feladatütemező feladatai a Scheduler Feladatgyűjteményei szakasz keresztül érhetők el.  A Feladatütemező szolgáltatás eléréséhez:

1. Jelentkezzen be az [Azure portálra].
2. Válassza ki **Tallózás >**, adja meg **ütemezés** a a *szűrő* mezőbe, majd válassza ki **Feladatütemező gyűjtemények**.
3. Válassza ki a Feladatgyűjtemény a helyhez.  A neve *sitename*-feladatokat.
4. Kattintson a **beállítások**.
5. Kattintson a **ütemezőjének** felügyelete alatt.

Ütemezett feladatok szerepelnek, az áttelepítés előtt megadott gyakorisággal.  Igény szerinti feladatok le vannak tiltva.  Egy igény szerinti feladat futtatásához:

1. Válassza ki a futtatni kívánt feladat.
2. Szükség esetén kattintson **engedélyezése** engedélyezni a feladatot.
3. Kattintson a **beállítások**, majd **ütemezés**.
4. Válassza ki a ismétlődése **egyszer**, majd kattintson a **mentése**

Az igény szerinti feladatok találhatók `App_Data/config/scripts/scheduler post-migration`.  Azt javasoljuk, hogy az összes igény szerinti feladatok [WebJobs] átalakítása vagy [funkciók].  Új Feladatütemező feladatok [WebJobs] írási vagy [funkciók].

### <a name="notification-hubs"></a>A Notification Hubs
A Mobile Services Notification Hubs leküldéses értesítések használ.  A következő beállításokkal segítségével csatolja az értesítési központnak a Mobile Service áttelepítés után:

| Alkalmazás-beállítás | Leírás |
|:--- |:--- |
| **MS\_PushEntityNamespace** |A Notification Hub Namespace |
| **MS\_NotificationHubName** |Az értesítési központ nevét |
| **MS\_NotificationHubConnectionString** |Az értesítési központ kapcsolati karakterlánc |
| **MS\_NamespaceName** |Egy aliast MS_PushEntityNamespace |

Az értesítési központ kezeli a [Azure portálra].  Vegye figyelembe az értesítési központ nevét (található ez az alkalmazás beállításokkal):

1. Jelentkezzen be az [Azure portálra].
2. Válassza ki **Tallózás**> elemre, majd válassza **értesítési központok**
3. Kattintson a mobilszolgáltatáshoz kapcsolódó értesítési központ nevére.

> [!NOTE]
> Ha az értesítési központ egy "Mixed" típust, nincs látható.  "Mixed" írja be a notification hubs Notification Hubs és a korábbi Service Bus-funkciók használatára.  [Alakítsa át a vegyes névterek] folytatása előtt.  Az átalakítás befejeződése után az értesítési központ megjelenik a [Azure portálra].
>
>

További információkért tekintse át a [Notification Hubs] dokumentációját.

> [!TIP]
> Notification Hubs felügyeleti szolgáltatásai a [Azure portálra] továbbra is a képen vannak.  A [a klasszikus Azure portálon] továbbra is elérhető a Notification Hubs kezeléséhez.
>
>

### <a name="legacy-push"></a>A hagyományos leküldéses beállításai
Ha a mobilszolgáltatást, a Notification Hubs megjelenése előtt a leküldéses konfigurált, az *örökölt leküldéses*.  Ha leküldéses használ, és nem jelenik meg egy értesítési központot, szerepel a konfigurációs, akkor valószínűleg használ *örökölt leküldéses*.  Ez a szolgáltatás az összes többi szolgáltatás áttelepítése.  Azt javasoljuk azonban, hogy frissítsen a Notification Hubs hamarosan az áttelepítés befejezése után.

Az idő alatt (kivétellel a figyelmet a jelentősebb az APN szolgáltatás tanúsítványát) örökölt leküldéses beállítások érhetők alkalmazás beállításaiban.  Az APNS-tanúsítvány frissítése azáltal, hogy a fájlrendszer a megfelelő fájlt.

### <a name="app-settings"></a>Egyéb beállítások
A következő további beállításokkal: áttelepített Mobile szolgáltatás és a rendelkezésre álló *beállítások* > *Alkalmazásbeállítások*:

| Alkalmazás-beállítás | Leírás |
|:--- |:--- |
| **MS\_MobileServiceName** |Az alkalmazás neve |
| **MS\_MobileServiceDomainSuffix** |The domain prefix. Egytényezős azure-mobile.net |
| **MS\_ApplicationKey** |Az alkalmazás kulcs |
| **MS\_MasterKey** |Az alkalmazás főkulcs |

Az alkalmazás kulcs és a főkulcs megegyeznek az alkalmazás kulcsok eredeti Mobile szolgáltatás.  Különösen az Alkalmazáskulcsot által küldött mobil ügyfelek használatát a mobil API érvényesítéséhez.

### <a name="cliequivalents"></a>Parancssori megfelelője
Már használhatja a *mobil azure* parancs az Azure Mobile Services hely kezeléséhez.  Ehelyett számos funkciót helyett a *azure hely* parancsot.  A következő táblázat segítségével alakokat található általános jellegű parancsok:

| *Az Azure Mobile* parancs | Egyenértékű *Azure Site* parancs |
|:--- |:--- |
| mobil helyek |webhelylistájának helye |
| mobil listája |webhely-lista |
| mobil megjelenítése *neve* |hely megjelenítése *neve* |
| mobil újraindítás *neve* |Újraindítás hely *neve* |
| mobil helyezze üzembe újra *neve* |hely telepítési helyezze üzembe újra *commitId* *neve* |
| mobil kulcskészlet *neve* *típus* *érték* |hely appsetting törlése *kulcs* *neve* <br/> hely appsetting hozzáadása *kulcs*=*érték* *neve* |
| Mobile config lista *neve* |appsetting webhelylista *neve* |
| Mobile config beolvasása *neve* *kulcs* |hely appsetting megjelenítése *kulcs* *neve* |
| mobil konfiguráció *neve* *kulcs* |hely appsetting törlése *kulcs* *neve* <br/> hely appsetting hozzáadása *kulcs*=*érték* *neve* |
| mobil tartománylista *neve* |tartomány webhelylista *neve* |
| mobil tartomány hozzáadása *neve* *tartomány* |hely tartomány hozzáadása *tartomány* *neve* |
| mobil tartomány törlése *neve* |webhely tartomány törlése *tartomány* *neve* |
| mobil méretezési megjelenítése *neve* |hely megjelenítése *neve* |
| mobil méretezési módosítása *neve* |skála webhelymódot *mód* *neve* <br /> hely méretezési példányok *példányok* *neve* |
| mobil appsetting lista *neve* |appsetting webhelylista *neve* |
| mobil appsetting hozzáadása *neve* *kulcs* *érték* |hely appsetting hozzáadása *kulcs*=*érték* *neve* |
| mobil appsetting törlése *neve* *kulcs* |hely appsetting törlése *kulcs* *neve* |
| mobil appsetting megjelenítése *neve* *kulcs* |hely appsetting törlése *kulcs* *neve* |

A megfelelő alkalmazás-beállítás frissítésével hitelesítés vagy a leküldéses értesítési beállításainak frissítése
Szerkesztheti a fájlokat, és tegye közzé a webhely, ftp vagy git.

### <a name="diagnostics"></a>Diagnosztika és naplózás
Diagnosztikai naplózás általában le van tiltva, az Azure App Service-ben.  Diagnosztikai naplózás engedélyezése:

1. Jelentkezzen be az [Azure portálra].
2. Válassza ki **összes erőforrás** vagy **alkalmazásszolgáltatások** kattintson az áttelepített Mobile szolgáltatás nevét.
3. Alapértelmezés szerint a beállítások panel nyílik meg.
4. Válassza ki **diagnosztikai naplók** szolgáltatások menüjében.
5. Kattintson a **ON** esetében a következő naplók kapcsolódnak: **Alkalmazásnaplózást (fájlrendszer)**, **a részletes hibaüzeneteket**, és **sikertelen kérelmek nyomkövetése**
6. Kattintson a **fájlrendszer** Web server naplózás
7. Kattintson a **mentése**

A naplók megtekintéséhez:

1. Jelentkezzen be az [Azure portálra].
2. Válassza ki **összes erőforrás** vagy **alkalmazásszolgáltatások** kattintson az áttelepített Mobile szolgáltatás nevét.
3. Kattintson a **eszközök** gomb
4. Válassza ki **naplófolyamot** OBSERVE menüjében.

Naplók az ablakban jelennek meg, akkor jönnek létre.  Emellett letöltheti a naplók az üzembe helyezési hitelesítő adatok használatával újabb elemzés céljából. További információkért lásd: a [naplózás] dokumentációját.

## <a name="known-issues"></a>Ismert problémák
### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>Egy hely leállás Mobile App át másolat törlése okoz.
Az Azure PowerShell használatával áttelepített mobilszolgáltatás klónozza, majd törölje a klónozás, a DNS-bejegyzést a termelési service távolítja el.  A webhely már nem elérhető az internetről.  

Megoldás: Ha a webhely klónozza, ehhez a portálon keresztül.

### <a name="changing-webconfig-does-not-work"></a>Web.config módosítása nem működik
Az ASP.NET webhely van, ha módosítja a `Web.config` fájl nem érvényben.  Az Azure App Service összeállít egy megfelelő `Web.config` fájl a Mobile Services futásidejű támogatásához rendszerindítás során.  Egyes beállítások (például egyéni fejlécek) felülbírálhatja egy XML-transzformációs fájl használatával.  Hozzon létre egy fájlt a nevű `applicationHost.xdt` -ezt a fájlt a kell végződnie a `D:\home\site` könyvtárhoz, az Azure szolgáltatásban.  Töltse fel a `applicationHost.xdt` fájlt egy egyéni telepítési parancsfájl használatával, vagy közvetlenül a Kudu használatával.  Az alábbiakban látható egy példa a dokumentum:

```
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

További információkért lásd: a [XDT átalakítási minták] dokumentációnkat a Githubon.

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>Áttelepített Mobile Services nem adható hozzá a Traffic Manager
Traffic Manager-profil létrehozásakor a profilhoz áttelepített mobilszolgáltatás közvetlenül nem választható.  Használja a "külső végpont."  A külső végpont nem vehető Powershellen keresztül.  További információkért lásd: a [Traffic Manager-oktatóanyag](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>További lépések
Most, hogy az alkalmazás az App Service át van még több szolgáltatást is használhatja:

* Központi telepítési [átmeneti üzembe helyezési ponti] engedélyezi, hogy a webhely változásainak tesztelése, és végezze el A / B tesztelés.
* [WebJobs] pótlásáról igény ütemezett feladatokhoz.
* Is [folyamatosan telepítése] GitHub, a TFS vagy Mercurial létrehozhatja, ha a webhely-webhelyét.
* Használhat [Application Insights] a webhely figyelésére.
* A webhely és a mobil API ugyanazt a kódot a szolgálnak.

### <a name="upgrading-your-site"></a>Az Azure Mobile Apps SDK a Mobile Services hely frissítése
* Node.js-alapú kiszolgáló-projektekhez az új [Mobile Apps Node.js SDK] számos új szolgáltatást nyújt. Például mostantól helyi fejlesztési és hibakeresés, bármely fent 0.10 Node.js verziójával, és az összes Express.js köztes testreszabása.
* A. A NET-alapú kiszolgáló-projektek, az új [Mobile Apps SDK NuGet-csomagok](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) NuGet függőségekkel több beleszólása van.  Ezeket a csomagokat az új App Service-hitelesítését támogatja, és bármely ASP.NET-projekt állítható össze. Történő frissítéssel kapcsolatos további tudnivalókért lásd: [frissítse a meglévő .NET Mobile szolgáltatást az App Service](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[App Service díjszabás]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Application Insights]: ../application-insights/app-insights-overview.md
[Automatikus méretezése]: ../app-service/web-sites-scale.md
[Azure App Service]: ../app-service/app-service-web-overview.md
[a klasszikus Azure portálon]: https://manage.windowsazure.com
[Azure portálra]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/en-us/regions/
[Azure Scheduler tervek]: ../scheduler/scheduler-plans-billing.md
[folyamatosan telepítése]: ../app-service/app-service-continuous-deployment.md
[Alakítsa át a vegyes névterek]: https://azure.microsoft.com/en-us/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[egyéni tartománynevek]: ../app-service/app-service-web-tutorial-custom-domain.md
[Fiddler]: http://www.telerik.com/fiddler
[Azure App Service általános rendelkezésre állását]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Hybrid Connections]: ../app-service/app-service-hybrid-connections.md
[naplózás]: ../app-service/web-sites-enable-diagnostic-log.md
[Mobile Apps Node.js SDK]: https://github.com/azure/azure-mobile-apps-node
[Mobile Services vs. App Service]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Notification Hubs]: ../notification-hubs/notification-hubs-push-notification-overview.md
[teljesítményfigyelés]: ../app-service/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[átmeneti üzembe helyezési ponti]: ../app-service/web-sites-staged-publishing.md
[VNet]: ../app-service/web-sites-integrate-with-vnet.md
[XDT átalakítási minták]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[funkciók]: ../azure-functions/functions-overview.md
