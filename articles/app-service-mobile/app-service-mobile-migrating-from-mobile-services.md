---
title: Migrálás Mobile servicesből, egy App Service mobilalkalmazás
description: Ismerje meg, hogyan lehet egyszerűen migrálhatja a Mobile Services-alkalmazás egy App Service Mobile Apps
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 07507ea2-690f-4f79-8776-3375e2adeb9e
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: crdun
ms.openlocfilehash: 7fdbbee27f83a4583390158e456270324967b28a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52961603"
---
# <a name="article-top"></a>A meglévő Azure Mobile Service migrálása az Azure App Service
Az a [az Azure App Service általános elérhetősége], az Azure Mobile Services-webhelyek könnyen migrálhatók helyi kihasználásához az Azure App Service összes funkcióját.  Ez a dokumentum leírja, mi történik, ha telepítheti át webhelyét az Azure Mobile Services az Azure App Service-ben.

## <a name="what-does-migration-do"></a>Mire áttelepítési? a helyhez
Az Azure Mobile Service migrálása kapcsolja be a mobilszolgáltatás- [Azure App Service] alkalmazás a kód módosítása nélkül.  A Notification Hubs, az SQL adatkapcsolat, hitelesítési beállítások, az ütemezett feladatok és tartománynév változatlan marad.  Az Azure Mobile Service használatával mobil ügyfelek továbbra is a szokott.  Áttelepítési a szolgáltatás újraindítása, miután a rendszer áthelyezi az Azure App Service-ben.

[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Miért kell áttelepítheti webhelyét
A Microsoft javasolja van, az Azure mobilszolgáltatásokhoz az Azure App Service, beleértve a szolgáltatások előnyeinek áttelepíteni:

* Új üzemeltetési funkciók, többek között [WebJobs] és [egyéni tartománynevek].
* Való megfigyelés és hibaelhárítás [Application Insights].
* Beépített DevOps-eszközök, beleértve a [előkészítési ponton], visszaállítási és éles tesztelése.
* [Automatikus vertikális felskálázás], terheléselosztást és [Alkalmazásteljesítmény-figyelés].

Az Azure App Service előnyeiről további információkért lásd: a [A Mobile Services kontra App Service] témakör.

## <a name="before-you-begin"></a>Előkészületek
Mielőtt megkezdené a webhely fő munka, készítsen biztonsági másolatot az SQL database és a Mobile Service parancsfájlok.

## <a name="migrating-site"></a>A webhelyek áttelepítése
Az áttelepítési folyamat akkor is áttelepíti egy adott Azure-régión belül minden hely.

A hely áttelepítéséhez:

1. Jelentkezzen be a [Klasszikus Azure portál].
2. Válassza ki az áttelepíteni kívánt régióban egy Mobilszolgáltatást.
3. Kattintson a **áttelepítése az App Service-** gombra.

   ![A Migrate-gomb][0]
4. Olvassa el a áttelepítése az App Service párbeszédpanelen.
5. Adja meg a mobilszolgáltatás nevét a mezőbe.  Ha a tartománynév contoso.azure-mobile.net, majd adja meg például *contoso* található megfelelő mezőbe.
6. Az osztásjelek gombra.

A tevékenység figyelése az áttelepítés állapotának monitorozásához. A webhely jelenik meg *áttelepítése* a klasszikus Azure portálon.

  ![Áttelepítési tevékenység figyelése][1]

Minden áttelepítést is eltarthat, a 3. az áttelepítés alatt álló mobilszolgáltatásonként 15 perc.  A hely elérhető marad a migrálás során.
Az áttelepítési folyamat végén a hely újraindul.  A hely nem érhető el az újraindítási folyamatot, amely előfordulhat, hogy az elmúlt pár másodperc alatt.

## <a name="finalizing-migration"></a>A Migrálás véglegesítése
Tesztelje webhelyét a mobil ügyfelekből az áttelepítési folyamat a webhelykiszolgáló tervezze.  Győződjön meg arról, a mobile ügyfél módosítása nélkül az összes közös ügyfél műveletek hajthatók végre.  

### <a name="update-app-service-tier"></a>Egy megfelelő App Service-tarifacsomag kiválasztása
A díjszabás az Azure App Service-ben való migrálás után több beleszólása van.

1. Jelentkezzen be az [Azure Portal].
2. Válassza ki **összes erőforrás** vagy **App Services** kattintson az áttelepített mobilszolgáltatás nevét.
3. Alapértelmezés szerint a beállítások panel nyílik meg.
4. Kattintson a **App Service-csomag** a beállítások menüben.
5. Kattintson a **Tarifacsomag** csempére.
6. Kattintson a csempére az igényeinek megfelelő, majd kattintson a **kiválasztása**.  Szükség lehet, majd kattintson **az összes megtekintése** az elérhető tarifacsomagok megtekintéséhez.

Kiindulási pontként javasoljuk, hogy az alábbi szinteken:

| Mobilszolgáltatás Tarifacsomag | Az App Service-Tarifacsomag |
|:--- |:--- |
| Ingyenes |F1 – Ingyenes |
| Alapszintű |Alapszintű B1 |
| Standard |S1 – Standard |

Nincs jelentős rugalmasságot biztosít az alkalmazás tarifacsomagját jobb választás.  Tekintse meg [App Service szolgáltatás díjszabása] az új App Service díjszabás teljes leírását.

> [!TIP]
> Többek között tartalmazza az App Service Standard szintű, előfordulhat, hogy szeretne használni, számos funkciókhoz is hozzáférést [előkészítési ponton], automatikus biztonsági mentést, és az automatikus skálázást.  Tekintse meg az új képességek, amíg Ön van!
>
>

### <a name="review-migration-scheduler-jobs"></a>Tekintse át az áttelepített Scheduler-feladatok
Scheduler-feladatok teljesítése körülbelül 30 percet az áttelepítés után nem látható lesz.  Ütemezett feladatok továbbra is fut a háttérben.
Az ütemezett feladatok megtekintéséhez, miután jelenjenek újra:

1. Jelentkezzen be az [Azure Portal].
2. Válassza ki **Tallózás >**, adja meg **ütemezés** a a *szűrő* mezőbe, majd válassza a **Scheduler gyűjtemények**.

Nincsenek csak korlátozott számú ingyenes scheduler feladatok érhető el az áttelepítést követő.  Tekintse át a használat és a [Az Azure Scheduler-csomagok].

### <a name="configure-cors"></a>Szükség esetén a CORS konfigurálása
Eltérő eredetű erőforrások megosztása a, hogy a webhelyet, hogy hozzáférni egy webes API egy másik tartományba a technika.  Ha az Azure Mobile Services használata egy kapcsolódó webhelyen, majd kell a CORS konfigurálása az áttelepítés részeként.  Ha a hozzáférés az Azure Mobile Services kizárólag a mobileszközökről, majd CORS nem kell kivételével ritka esetekben kell konfigurálni.

Az áttelepített CORS-beállítások érhetők el, a **MS_CrossDomainWhitelist** alkalmazásbeállítást.  A webhely áttelepítése az App Service CORS-funkció:

1. Jelentkezzen be az [Azure Portal].
2. Válassza ki **összes erőforrás** vagy **App Services** kattintson az áttelepített mobilszolgáltatás nevét.
3. Alapértelmezés szerint a beállítások panel nyílik meg.
4. Kattintson a **CORS** API menüjében.
5. Adja meg minden olyan engedélyezett eredetek található megfelelő mezőbe, mindegyik után nyomja le az Enter.
6. Miután a engedélyezett származási helyek listájára, helyes-e, kattintson a Mentés gombra.

> [!TIP]
> Az Azure App Service előnyei egyik, hogy futtatni lehessen a mobilszolgáltatás és a webhely ugyanazon a helyen.  További információkért lásd: a [lépések](#next-steps) szakaszban.
>
>

### <a name="download-publish-profile"></a>Egy új közzétételi profil letöltése
A webhely a közzétételi profil megváltozik, amikor az Azure App Service-ba való migrálás.  Ha azt tervezi, a Visual Studión belül a hely közzé, új közzétételi profilt kell.  Az új közzétételi profil letöltése:

1. Jelentkezzen be az [Azure Portal].
2. Válassza ki **összes erőforrás** vagy **App Services** kattintson az áttelepített mobilszolgáltatás nevét.
3. Kattintson a **közzétételi profil letöltése**.

A PublishSettings-fájl letöltése a számítógépre.  Általában nevezzük *sitename*. PublishSettings.  A közzétételi beállítások importálása a meglévő projekt:

1. Nyissa meg a Visual Studio és az Azure mobilszolgáltatás-projekt.
2. Kattintson a jobb gombbal a projektre a **Megoldáskezelőben** válassza **Publish...**
3. Kattintson az **Importálás** gombra
4. Kattintson a **Tallózás** , és válassza ki a letöltött közzétételi beállítások fájljában.  Kattintson az **OK** gombra
5. Kattintson a **kapcsolat ellenőrzése** annak biztosítása érdekében a közzétételi beállítások kezelése.
6. Kattintson a **közzététel** közzétenni a webhelyen.

## <a name="working-with-your-site"></a>Az áttelepítés utáni webhely használata
Az új App Service-használatának kezdő a [Azure Portal] áttelepítés után.  Az alábbiakban néhány Megjegyzés a kötegműveletekkel kapcsolatban meghatározott műveleteket végrehajtani, amellyel a [klasszikus Azure portál], valamint azokat az App Service-megfelelőikre.

### <a name="publishing-your-site"></a>Töltsön le és az áttelepített hely közzététele
A hely git és ftp-n keresztül érhető el, és is a különböző használható különböző mechanizmusokat, beleértve a WebDeploy, a TFS, a mercurial rendszeren keresztül, a GitHub és a FTP tehető közzé.  Az üzembe helyezési hitelesítő adatok át lesznek telepítve, a többi webhely.  Ha nem adta meg az üzembehelyezési hitelesítő adatokat, vagy nem emlékszik őket, visszaállíthatja őket:

1. Jelentkezzen be az [Azure Portal].
2. Válassza ki **összes erőforrás** vagy **App Services** kattintson az áttelepített mobilszolgáltatás nevét.
3. Alapértelmezés szerint a beállítások panel nyílik meg.
4. Kattintson a **üzembe helyezési hitelesítő adatok** a közzététel a menüben.
5. Adja meg az új üzembe helyezési hitelesítő adatok a megfelelő mezőkbe, majd kattintson a Mentés gombra.

Ezek a hitelesítő adatok segítségével a hely a git használatával klónozza, vagy állítsa be automatikus telepítéseket a GitHub, a TFS vagy a Mercurial.  További információkért lásd: [Azure App Service üzembe helyezési dokumentációja].

### <a name="appsettings"></a>Nastavení aplikace
A legtöbb áttelepített mobilszolgáltatás beállításainak Alkalmazásbeállítások keresztül érhetők el.  Az alkalmazás beállításai listát kap a [Azure Portal].
Megtekintéséhez, vagy az alkalmazás beállításainak módosítása:

1. Jelentkezzen be az [Azure Portal].
2. Válassza ki **összes erőforrás** vagy **App Services** kattintson az áttelepített mobilszolgáltatás nevét.
3. Alapértelmezés szerint a beállítások panel nyílik meg.
4. Kattintson a **Alkalmazásbeállítások** általános menüjében.
5. Görgessen az alkalmazásbeállítások szakaszhoz, és keresse meg az Alkalmazásbeállítás.
6. Kattintson az érték szerkesztéséhez Alkalmazásbeállítás értékét.  Kattintson a **mentése** mentheti az értéket.

Egyszerre több Alkalmazásbeállítások frissítheti.

> [!TIP]
> Nincsenek ugyanazzal az értékkel két alkalmazás beállítását.  Például előfordulhat, hogy látja *applicationkey-hez* és *MS\_applicationkey-hez*.  Mindkét alkalmazás beállításainak frissítése egy időben.
>
>

### <a name="authentication"></a>Hitelesítés
Az áttelepített hely alkalmazásbeállításokként minden hitelesítési beállítások érhetők el.  A hitelesítési beállítások frissítéséhez, meg kell változtatnia a megfelelő beállításokat.  Az alábbi táblázat a hitelesítésszolgáltató megfelelő alkalmazás beállításai láthatók:

| Szolgáltató | Ügyfél-azonosító | Titkos ügyfélkulcs | Egyéb beállítások |
|:--- |:--- |:--- |:--- |
| Microsoft-fiók |**MS\_MicrosoftClientID** |**MS\_MicrosoftClientSecret** |**MS\_MicrosoftPackageSID** |
| Facebook |**MS\_FacebookAppID** |**MS\_FacebookAppSecret** | |
| Twitter |**MS\_TwitterConsumerKey** |**MS\_TwitterConsumerSecret** | |
| Google |**MS\_GoogleClientID** |**MS\_GoogleClientSecret** | |
| Azure AD |**MS\_AadClientID** | |**MS\_AadTenants** |

Megjegyzés: **MS\_AadTenants** tárolódik, (a "Engedélyezett bérlők" mezőket a Mobile Services portálon) bérlő tartományok vesszővel elválasztott listáját.

> [!WARNING]
> **Ne használja a hitelesítési mechanizmusok a beállítások menü**
>
> Az Azure App Service-ben külön "kód nélküli" hitelesítési és engedélyezési rendszert biztosít alatt a *hitelesítési / engedélyezési* (elavult) és a beállítások menüben *Mobile hitelesítési* lehetőség a beállítások menüben.  Ezek a beállítások nem kompatibilisek a migrált Azure mobilszolgáltatásokhoz.  Is [a hely frissítése](app-service-mobile-net-upgrading-from-mobile-services.md) kihasználásához az Azure App Service-hitelesítés.
>
>

### <a name="easytables"></a>Data
A *adatok* a Mobile Servicesben lap váltotta *könnyen kezelhető táblák* az Azure Portalon.  Könnyen kezelhető táblák elérése:

1. Jelentkezzen be az [Azure Portal].
2. Válassza ki **összes erőforrás** vagy **App Services** kattintson az áttelepített mobilszolgáltatás nevét.
3. Alapértelmezés szerint a beállítások panel nyílik meg.
4. Kattintson a **könnyen kezelhető táblák** a mobil menü.

Egy tábla kattintva adhat hozzá a **Hozzáadás** gombra, vagy egy Táblanév kattintva férhet hozzá a létező táblák.  Nincsenek a különféle műveleteket végezhet, ezen a panelen többek között:

* Tábla engedélyeinek módosítása
* A működési szkriptek szerkesztése
* A következő tábla sémáját kezelése
* A tábla törlése
* A tábla tartalmának törlése
* Adott sorokat a tábla törlése

### <a name="easyapis"></a>API
A *API* a Mobile Servicesben lap váltotta *egyszerű API-k* az Azure Portalon.  Egyszerű API-k elérése:

1. Jelentkezzen be az [Azure Portal].
2. Válassza ki **összes erőforrás** vagy **App Services** kattintson az áttelepített mobilszolgáltatás nevét.
3. Alapértelmezés szerint a beállítások panel nyílik meg.
4. Kattintson a **egyszerű API-k** a mobil menü.

Az áttelepített API-k már szerepelnek a panelen.  Azt is megteheti egy API-t ezen a panelen.  Egy adott API kezeléséhez kattintson az API-t.
Az új panelen módosítsa az engedélyeket, és a szkriptek szerkesztése az API-hoz.

### <a name="on-demand-jobs"></a>Scheduler-feladatok
Scheduler-feladatok a Scheduler-Feladatgyűjtemények szakasz keresztül érhetők el.  A scheduler-feladatok elérése:

1. Jelentkezzen be az [Azure Portal].
2. Válassza ki **Tallózás >**, adja meg **ütemezés** a a *szűrő* mezőbe, majd válassza a **Scheduler gyűjtemények**.
3. Válassza ki a Feladatgyűjtemény webhelye számára.  Alkalmazás neve *sitename*-feladatokat.
4. Kattintson a **beállítások**.
5. Kattintson a **Scheduler-feladatok** felügyelet alatt.

Ütemezett feladatok jelennek meg az áttelepítés előtt a megadott gyakorisággal.  Igény szerinti feladatok le vannak tiltva.  Egy igény szerinti feladat futtatása:

1. Válassza ki a futtatni kívánt feladat.
2. Ha szükséges, kattintson a **engedélyezése** engedélyezni a feladatot.
3. Kattintson a **beállítások**, majd **ütemezés**.
4. Válassza ki a ismétlődésének **egyszer**, majd kattintson a **mentése**

Az igény szerinti feladatok találhatók `App_Data/config/scripts/scheduler post-migration`.  Azt javasoljuk, hogy az összes igény szerinti feladatok [webjobs] konvertálása vagy [Functions].  Új scheduler-feladatok [WebJobs], írási vagy [Functions].

### <a name="notification-hubs"></a>Notification hubs használatával
A mobilszolgáltatások leküldéses értesítések a Notification Hubs használ.  Az értesítési központ összekapcsolása a saját mobilszolgáltatás az áttelepítés után a következő beállításokkal használhatók:

| Nastavení aplikace | Leírás |
|:--- |:--- |
| **MS\_PushEntityNamespace** |A Notification Hub Namespace |
| **MS\_NotificationHubName** |Az értesítési központ neve |
| **MS\_NotificationHubConnectionString** |Az értesítési központ kapcsolati karakterlánc |
| **MS\_NamespaceName** |MS_PushEntityNamespace alias |

Az értesítési központ kezelhető a [Azure Portal].  Megjegyzés: az értesítési központ neve (megtalálhatja az alkalmazásbeállítások használatával):

1. Jelentkezzen be az [Azure Portal].
2. Válassza ki **Tallózás**>, majd **Notification hubs használatával**
3. Kattintson a mobilszolgáltatás társított értesítési központ nevére.

> [!NOTE]
> Ha az értesítési központ egy "Mixed" típust, akkor sem látható.  "Vegyes" írja be a notification hubs Notification Hubs és a régebbi Service Bus-funkciók használatához.  [A vegyes névtér konvertálása] a folytatás előtt.  Az átalakítás befejeződése után az értesítési központ megjelenik a [Azure Portal].
>
>

További információkért tekintse át a [Értesítési központ] dokumentációját.

> [!TIP]
> Notification Hubs felügyeleti funkcióit a [Azure Portal] azok továbbra is előzetes verzióban érhető el.  A [klasszikus Azure portál] továbbra is elérhető, a Notification hubs központok felügyeletéhez.
>
>

### <a name="legacy-push"></a>Örökölt leküldési beállítások
A mobilszolgáltatást, a Notification Hubs bevezetése előtt konfigurált leküldéses, ha az *örökölt leküldéses*.  Leküldéses használ, és nem jelenik meg egy értesítési központ szerepel a konfigurációs, akkor valószínűleg használ *örökölt leküldéses*.  Ez a szolgáltatás minden más szolgáltatás áttelepítése.  Azt javasoljuk azonban, hogy frissítsen a Notification Hubs hamarosan az áttelepítés befejeződése után.

Addig minden a régi leküldéses-beállításokat (a jelentős az APNS-tanúsítvány kivétel) érhetők el az alkalmazásbeállítások.  Az APNS-tanúsítvány frissítése azáltal, hogy a fájlrendszer a megfelelő fájlt.

### <a name="app-settings"></a>Más alkalmazás beállításai
A következő további beállításokkal is a saját mobilszolgáltatás áttelepített elérhető alatt *beállítások* > *Alkalmazásbeállítások*:

| Nastavení aplikace | Leírás |
|:--- |:--- |
| **MS\_MobileServiceName** |Az alkalmazás neve |
| **MS\_MobileServiceDomainSuffix** |A megadott tartománynév előtagja. azaz azure-mobile.net |
| **MS\_applicationkey-hez** |Az Alkalmazáskulcs |
| **MS\_MasterKey** |Az alkalmazás főkulcs |

Az Alkalmazáskulcs és a főkulcs megegyeznek az alkalmazás kulcsainak az eredeti Mobile Service-ből.  Különösen az Alkalmazáskulcsot által küldött mobilügyfelek mobil API-használati ellenőrzése.

### <a name="cliequivalents"></a>Parancssori megfelelője
Már használhatja a *azure mobil* parancsot az Azure Mobile Services-hely kezeléséhez.  Helyette számos függvénye felváltotta a *az azure site* parancsot.  Az alábbi táblázat segítségével keresse meg a megfelelő gyakori parancsok:

| *Az Azure Mobile* parancs | Egyenértékű *Azure webhely* parancs |
|:--- |:--- |
| mobil helyek |webhelylistájának helye |
| mobil listája |webhely-lista |
| mobil show *neve* |hely megjelenítése *neve* |
| mobil újraindítás *neve* |Webhely újraindítása *neve* |
| mobil ismételt üzembe helyezés *neve* |hely központi telepítés ismételt üzembe helyezés *commitId* *neve* |
| mobil kulcskészletben *neve* *típus* *érték* |webhely Alkalmazásbeállítás törlése *kulcs* *neve* <br/> webhely Alkalmazásbeállítás hozzáadása *kulcs*=*érték* *neve* |
| Mobile config list *neve* |Alkalmazásbeállítás webhelylista *neve* |
| Mobile config első *neve* *kulcs* |webhely Alkalmazásbeállítás show *kulcs* *neve* |
| Mobile config set *neve* *kulcs* |webhely Alkalmazásbeállítás törlése *kulcs* *neve* <br/> webhely Alkalmazásbeállítás hozzáadása *kulcs*=*érték* *neve* |
| mobil tartománylista *neve* |tartomány webhelylista *neve* |
| mobil tartomány hozzáadása *neve* *tartomány* |Adja hozzá a webhely tartománya *tartomány* *neve* |
| mobil domain delete *neve* |webhely tartomány törlése *tartomány* *neve* |
| mobil méretezési show *neve* |hely megjelenítése *neve* |
| mobil méretezési módosítás *neve* |hely skálázási mód *mód* *neve* <br /> méretezési csoport példányai hely *példányok* *neve* |
| mobil Alkalmazásbeállítás lista *neve* |Alkalmazásbeállítás webhelylista *neve* |
| mobil Alkalmazásbeállítás hozzáadása *neve* *kulcs* *érték* |webhely Alkalmazásbeállítás hozzáadása *kulcs*=*érték* *neve* |
| mobil Alkalmazásbeállítás törlése *neve* *kulcs* |webhely Alkalmazásbeállítás törlése *kulcs* *neve* |
| mobil Alkalmazásbeállítás show *neve* *kulcs* |webhely Alkalmazásbeállítás törlése *kulcs* *neve* |

A megfelelő alkalmazás-beállítás frissítése hitelesítés vagy a leküldéses értesítési beállításainak frissítéséhez.
Szerkesztheti a fájlokat, és tegye közzé webhelyét az ftp-vagy a git.

### <a name="diagnostics"></a>Diagnosztikai és naplózási
Diagnosztikai naplózás általában le van tiltva, az Azure App Service-ben.  Diagnosztikai naplózás engedélyezése:

1. Jelentkezzen be az [Azure Portal].
2. Válassza ki **összes erőforrás** vagy **App Services** kattintson az áttelepített mobilszolgáltatás nevét.
3. Alapértelmezés szerint a beállítások panel nyílik meg.
4. Válassza ki **diagnosztikai naplók** a szolgáltatások menüpont alatt.
5. Kattintson a **ON** esetében a következő naplók kapcsolódnak: **Application Logging (fájlrendszer)**, **részletes hibaüzenetek**, és **sikertelen kérelmek nyomkövetése**
6. Kattintson a **fájlrendszer** a webkiszolgálói naplózás
7. Kattintson a **Mentés** gombra.

A naplók megtekintéséhez:

1. Jelentkezzen be az [Azure Portal].
2. Válassza ki **összes erőforrás** vagy **App Services** kattintson az áttelepített mobilszolgáltatás nevét.
3. Kattintson a **eszközök** gomb
4. Válassza ki **Log Stream** a OBSERVE menüpont alatt.

Naplók az ablakban jelennek meg, akkor jönnek létre.  A naplók újabb elemzési az üzembe helyezési hitelesítő adatok használatával is letöltheti. További információkért lásd: a [Logging] dokumentációját.

## <a name="known-issues"></a>Ismert problémák
### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>Egy hely szolgáltatáskimaradás törlése a Mobile App áttelepített klón okoz
Az Azure PowerShell-lel áttelepített mobilszolgáltatás klónozza, majd törli a klón, a DNS-bejegyzést a termelési service távolítja el.  A webhely már nem elérhető az internetről.  

Megoldás: Ha a hely klónozza, tegye meg a portálon keresztül.

### <a name="changing-webconfig-does-not-work"></a>Módosítja a Web.config nem működik
Egy ASP.NET-hellyel rendelkezik, ha módosítja a `Web.config` fájl nincs alkalmazva.  Az Azure App Service összeállít egy megfelelő `Web.config` fájl támogatásához a mobilszolgáltatások modul indítása során.  XML-átalakítás fájl használatával bizonyos beállításokat (például egyéni fejlécek) felül lehet bírálni.  Hozzon létre egy fájlt a nevű `applicationHost.xdt` -Ez a fájl végződése a `D:\home\site` az Azure Service könyvtárába.  Töltse fel a `applicationHost.xdt` fájlt egy egyéni üzembehelyezési szkript keresztül, vagy közvetlenül a Kudu használatával.  Az alábbiakban látható egy példa a dokumentum:

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

További információkért lásd: a [Átalakítás XDT a minták] dokumentációja a Githubon.

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>A mobilszolgáltatások áttelepített nem adható hozzá a Traffic Manager
Traffic Manager-profil létrehozásakor egy áttelepített mobilszolgáltatást a profilt közvetlenül nem választható.  Használja a "külső végpontját."  A külső végpont csak a Powershellen keresztül lehet hozzáadni.  További információkért lásd: a [Traffic Managerre vonatkozó oktatóanyagban](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>Következő lépések
Most, hogy az alkalmazás az App Service szolgáltatásba való áttelepítése, van még több szolgáltatást is használhatja:

* Üzembe helyezés [előkészítési ponton] lehetővé teszik a webhelyen végrehajtott módosítások előkészítéséhez és végezhet A / B tesztelés.
* [WebJobs] igény szerinti ütemezett feladatok helyett adja meg.
* Is [folyamatos üzembe helyezését] a hely által a hely összekapcsolása a GitHub, a TFS vagy a Mercurial.
* Használhat [Application Insights] monitorozhatja webhelyét.
* Szolgálja ki a webhely és a egy mobil API-jával ugyanazt a kódot.

### <a name="upgrading-your-site"></a>A Mobile Services-hely frissítése az Azure Mobile Apps SDK
* Node.js-alapú kiszolgáló-projektekhez az új [A Mobile Apps Node.js SDK] számos új funkciót kínál. Például mostantól helyi fejlesztés és hibakeresés tegye, használja a fenti 0.10-ás bármilyen Node.js-verzió, és bármely szolgáló Express.js közbenső szabhatja.
* A. NET-alapú kiszolgáló-projektek, az új [Mobile Apps SDK NuGet-csomagok](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) sokkal rugalmasabban adható meg a NuGet-függőségek.  Ezeket a csomagokat az új App Service-hitelesítés támogatásához, és a compose-bármilyen ASP.NET-projekt. Frissítéssel kapcsolatos további tudnivalókért lásd: [a meglévő .NET Mobile Service frissítése az App Service-](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[App Service szolgáltatás díjszabása]: https://azure.microsoft.com/pricing/details/app-service/
[Application Insights]: ../application-insights/app-insights-overview.md
[Automatikus vertikális felskálázás]: ../app-service/web-sites-scale.md
[Azure App Service]: ../app-service/app-service-web-overview.md
[Klasszikus Azure portál]: https://manage.windowsazure.com
[Azure Portal]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/regions/
[Az Azure Scheduler-csomagok]: ../scheduler/scheduler-plans-billing.md
[folyamatos üzembe helyezését]: ../app-service/app-service-continuous-deployment.md
[A vegyes névtér konvertálása]: https://azure.microsoft.com/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: https://curl.haxx.se/
[Egyéni tartománynevek]: ../app-service/app-service-web-tutorial-custom-domain.md
[Fiddler]: https://www.telerik.com/fiddler
[az Azure App Service általános elérhetősége]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Hybrid Connections]: ../app-service/app-service-hybrid-connections.md
[Logging]: ../app-service/web-sites-enable-diagnostic-log.md
[A Mobile Apps Node.js SDK]: https://github.com/azure/azure-mobile-apps-node
[A Mobile Services kontra App Service]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Értesítési központ]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Alkalmazásteljesítmény-figyelés]: ../app-service/web-sites-monitor.md
[Postman]: https://www.getpostman.com/
[előkészítési ponton]: ../app-service/web-sites-staged-publishing.md
[VNet]: ../app-service/web-sites-integrate-with-vnet.md
[Átalakítás XDT a minták]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[Functions]: ../azure-functions/functions-overview.md
