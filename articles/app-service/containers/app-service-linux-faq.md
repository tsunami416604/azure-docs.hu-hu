---
title: Linux – gyakori kérdések az Azure App Service |} Microsoft Docs
description: Az Azure App Service Linux – gyakori kérdések.
keywords: az Azure app service, webalkalmazás, gyakran ismételt kérdések, linux, oss
services: app-service
documentationCenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2018
ms.author: msangapu
ms.openlocfilehash: 5b3b3d3946b56ff53ad74c2ab93a646baa787d05
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36222977"
---
# <a name="azure-app-service-on-linux-faq"></a>Linux – gyakori kérdések az Azure App Service

App Service Linux kiadása fejlesztjük szolgáltatások hozzáadására és fejlesztjük a portált a platformon. Ez a cikk ismerteti, hogy ügyfeleink kérésére velünk nemrég a kérdésekre adott válaszok.

Ha kérdése van, ez a cikk fűzni.

## <a name="built-in-images"></a>Beépített lemezképek

**Szeretném oszthatja ketté a beépített Docker-tárolókban, amely a platformot biztosít. Hol találok azokat a fájlokat?**

Minden Docker-fájl található [GitHub](https://github.com/azure-app-service). Az összes Docker-tároló található [Docker Hub](https://hub.docker.com/u/appsvc/).

**Amikor konfigurálni a futásidejű verem Mik a várt értékeket az indítási fájl szakaszra?**

A Node.js adja meg a PM2 konfigurációs fájl vagy a parancsfájl. A .NET Core, írja be a lefordított dll-fájl nevét, `dotnet <myapp>.dll`. A Ruby a Ruby inicializálja az alkalmazás a parancsfájl adhat meg.

## <a name="management"></a>Kezelés

**Mi történik, amikor az Újraindítás gombra az Azure-portálon?**

Ez a művelet nem ugyanaz, mint a Docker újraindítása.

**Secure Shell (SSH) segítségével csatlakozzon az alkalmazás tároló virtuális gép (VM)?**

Igen, akkor teheti meg a forrás-vezérlő (SCM) felügyeleti webhelyen keresztül.

> [!NOTE]
> Az alkalmazástárolóhoz közvetlenül a helyi fejlesztési számítógépről is csatlakozhat SSH, SFTP vagy Visual Studio Code segítségével (Node.js-alkalmazások élő hibakereséséhez). További információkért tekintse meg a [Linuxon futó App Service-ben elérhető távoli hibakereséssel és SSH-val](https://aka.ms/linux-debug) kapcsolatos cikket.
>

**Hogyan hozható létre egy Linux App Service-csomag az SDK vagy az Azure Resource Manager-sablon használatával?**

Állítsa be a **fenntartott** mezőjét, az app service-nek *igaz*.

## <a name="continuous-integration-and-deployment"></a>Folyamatos integráció és üzembe helyezés

**A webes alkalmazás továbbra is használ egy Docker-tároló régi lemezképet, után a kép Docker központ frissítése már megtörtént. Támogatják a folyamatos integrációt és telepítést egyéni tároló?**

Igen, a folyamatos integrációs/üzembe helyezés beállítása az Azure-tároló beállításjegyzék vagy DockerHub, a következő [tárolók webalkalmazást a folyamatos üzembe helyezés](./app-service-linux-ci-cd.md). Titkos nyilvántartó, a tároló leállításával és indítsa el a webes alkalmazás is frissítheti. Vagy módosít, vagy adja hozzá a tároló frissítésének kényszerítése dummy Alkalmazásbeállítás.

**Átmeneti környezetek támogatására?**

Igen.

**Használhatok *a web deploy* szeretné telepíteni a webes alkalmazást?**

Igen, be kell állítani az alkalmazások nevű beállítása `WEBSITE_WEBDEPLOY_USE_SCM` való *hamis*.

**Saját-alkalmazás Git-telepítés sikertelen lesz, amikor Linux web app használatával. Hogyan használhatom a probléma?**

A Linux-webalkalmazás Git-telepítés nem sikerül, ha az alkalmazás kódjában telepítéséhez az alábbi lehetőségek közül választhat:

- A folyamatos kézbesítési (előzetes verzió) szolgáltatással: az alkalmazás forráskódjának tárolása egy Team Services Git-tárház vagy a GitHub-tárház Azure folyamatos kézbesítési használatára. További információkért lásd: [folyamatos kézbesítési konfigurálása Linux-webalkalmazás](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Használja a [ZIP telepítése API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): Ez az API használatához [SSH-ból a webalkalmazás](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support#making-a-client-connection) és nyissa meg azt a mappát, ahová a kód telepítésére. Futtassa a következő kódot:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Ha hibaüzenetet kap, amely a `curl` parancs nem található, győződjön meg arról, hogy a curl használatával telepítse `apt-get install curl` az előző futtatása előtt `curl` parancsot.

## <a name="language-support"></a>Nyelvi támogatás

**Használni kívánt webes szoftvercsatornák a Node.js-alkalmazás, bármely speciális beállítások és konfigurációk beállításához?**

Igen, tiltsa le a `perMessageDeflate` a kiszolgálóoldali Node.js-kódban. Például ha socket.io használ, használja a következő kódot:

```nodejs
var io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Támogatja a .NET Core nem fordított alkalmazások?**

Igen.

**Támogatják a szerkesztő függőségi vezető PHP-alkalmazásokhoz?**

Igen, a Git telepítés során a Kudu kell észleli, hogy a PHP-alkalmazások (környezetnek köszönhetően composer.lock fájl jelenléte) telepít, a Kudu akkor indul el, majd a szerkesztő telepítése.

## <a name="custom-containers"></a>Egyéni tárolók

**Saját egyéni tároló használata. A platform, az SMB-megosztáson csatlakoztatni kívánt a `/home/` könyvtár.**

Azt teheti úgy, hogy a `WEBSITES_ENABLE_APP_SERVICE_STORAGE` Alkalmazásbeállítás *igaz*. Ne feledje, hogy ennek hatására tároló újraindítások során a platform tárolási végighalad a módosítása.

>[!NOTE]
>Ha a `WEBSITES_ENABLE_APP_SERVICE_STORAGE` beállítás nincs megadva, vagy állítsa *hamis*, a `/home/` könyvtárat nem lehet megosztva méretezési példányok között, és nem írt fájlok nem maradnak újraindításainál.

**A saját egyéni tároló elindításához hosszú ideig tart, és a platform újraindul a tárolóhoz, mielőtt befejezné indítása.**

A platform várakozik, a tároló újraindítása előtti idő is konfigurálhat. Ehhez az szükséges, állítsa be a `WEBSITES_CONTAINER_START_TIME_LIMIT` Alkalmazásbeállítás a kívánt értéket. Az alapértelmezett érték 230 másodperc, a maximális értéke pedig 1800 másodperc.

**Mi az a személyes beállításjegyzék kiszolgáló URL-cím formátuma?**

Adja meg a teljes beállításjegyzék URL-t, beleértve a `http://` vagy `https://`.

**Mi az, hogy a lemezkép neve, a titkos beállításjegyzékbeli beállítás formátumának?**

Adja hozzá a teljes lemezképet nevét, beleértve a személyes beállításjegyzék URL-CÍMÉT (például myacr.azurecr.io/dotnet:latest). Egyéni portot használó képnév [nem lehet megadni a portálon keresztül](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Beállítása `docker-custom-image-name`, használja a [ `az` parancssori eszköz](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set).

**Is több portot is felfedi a saját egyéni tároló rendszerképre?**

A Microsoft jelenleg nem támogatja az ilyen több port.

**Hozzáadhatom saját storage?**

A Microsoft jelenleg nem támogatja saját tárolási helyeznek.

**Miért nem tud tallózni a saját egyéni tároló fájl rendszer futó folyamatok az SCM helyről?**

Az SCM helyen külön fut. A fájl rendszer futó folyamatok a app tároló nem lehet ellenőrizni.

**A saját egyéni tároló figyeli a 80-as portra. Hogyan konfigurálhatja a kérelem továbbításához porthoz alkalmazásom?**

Tudunk port automatikus észlelését. Azt is megadhatja az alkalmazás nevű beállítása *WEBSITES_PORT* , és adjon neki a várt portszám értékét. Korábban, a használt platformra a *PORT* Alkalmazásbeállítás. Azt tervezi, az Alkalmazásbeállítás érvényteleníthető és használandó *WEBSITES_PORT* kizárólag.

**Van HTTPS megvalósítását a saját egyéni tárolót?**

Nem, a platform kezeli a HTTPS-lezárást a megosztott első végénél.

## <a name="pricing-and-sla"></a>Díjszabás és SLA

**Mi az az árképzés, most, hogy a szolgáltatás általánosan elérhető?**

Van szó, a normál Azure App Service szolgáltatás díjszabása az alkalmazást futtató órák száma.

## <a name="other-questions"></a>Egyéb kérdések

**Mik azok a beállítások az alkalmazásnevekben támogatott karakter?**

Az alkalmazásbeállítások csak betűket (A-Z, a – z), számok (0-9), és az aláhúzás karakterrel (_) használható.

**Ha kérhet új szolgáltatások?**

A képet, elküldheti a [webalkalmazások visszajelzési fórumon](https://aka.ms/webapps-uservoice). Adja hozzá a ötlet címe "[Linux]".

## <a name="next-steps"></a>További lépések

- [Mi az Azure App Service Linux?](app-service-linux-intro.md)
- [Átmeneti környezetek beállítása az Azure App Service-ben](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [A tárolók a webes alkalmazás folyamatos üzembe helyezés](./app-service-linux-ci-cd.md)
