---
title: App Service linuxon – gyakori kérdések – Azure |} A Microsoft Docs
description: Az Azure App Service linuxon – gyakori kérdések.
keywords: az Azure app service, webalkalmazás, – gyakori kérdések, linux, oss, tárolók, többtárolós, multicontainer webalkalmazás
services: app-service
documentationCenter: ''
author: yili
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: yili
ms.custom: seodec18
ms.openlocfilehash: e3b6eed6f70eb2803ef4fa4e6b5d32fb0a4d843a
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59525126"
---
# <a name="azure-app-service-on-linux-faq"></a>Az Azure App Service linuxon – gyakori kérdések

A Linuxon futó App Service kezdve dolgozunk a platform fejlesztései szolgáltatások hozzáadására és a. Ez a cikk ismerteti, hogy ügyfeleink kérésére USA nemrég kérdésekre kaphat választ.

Ha kérdése van, ez a cikk fűzni.

## <a name="built-in-images"></a>Beépített rendszerképek

**Szeretnék ágaztatnia a platform által biztosított beépített Docker-tárolót. Hol találhatok ezeket a fájlokat?**

Az összes Docker-fájlok találhat [GitHub](https://github.com/azure-app-service). Az összes Docker-tárolókban található [Docker Hub](https://hub.docker.com/u/appsvc/).

<a id="#startup-file"></a>

**Mik azok a várt értékek az indítási fájl szakaszt, ha konfigurálható a futtatókörnyezeti verem?**

| Verem     | Várt érték                                                                |
|-----------|-------------------------------------------------------------------------------|
| Java SE   | Indítsa el a parancsot a `.jar` alkalmazás                                    |
| Tomcat    | a parancsfájl végrehajtása a konfigurációkat az alkalmazás helyét          |
| Node.js   | a PM2-konfigurációs fájl vagy a parancsfájl                                |
| .Net Core | a lefordított DLL-nevet: `dotnet <myapp>.dll`                                 |
| Ruby      | a Ruby-szkriptet, amelyet szeretne az alkalmazás inicializálása                     |

## <a name="management"></a>Kezelés

**Mi történik, az Azure Portalon az újraindítás gomb megnyomásakor?**

Ez a művelet nem ugyanaz, mint a Docker újraindítás.

**Secure Shell (SSH) használatával kapcsolódhat az alkalmazást tároló virtuális gép (VM)?**

Igen, akkor ehhez a forrás control management (SCM) webhelyen keresztül.

> [!NOTE]
> Az alkalmazástárolóhoz közvetlenül a helyi fejlesztési számítógépről is csatlakozhat SSH, SFTP vagy Visual Studio Code segítségével (Node.js-alkalmazások élő hibakereséséhez). További információkért tekintse meg a [Linuxon futó App Service-ben elérhető távoli hibakereséssel és SSH-val](https://aka.ms/linux-debug) kapcsolatos cikket.
>

**Hogyan hozható létre egy SDK-t vagy az Azure Resource Manager-sablon egy Linux App Service-csomag?**

Állítsa be a **fenntartott** mezőjét, az app service- *igaz*.

## <a name="continuous-integration-and-deployment"></a>Folyamatos integráció és üzembe helyezés

**Webalkalmazás továbbra is használ egy régi Docker-tároló rendszerképét, miután frissítése már megtörtént a rendszerképet a Docker hubon. Támogatják a folyamatos integráció és egyéni tárolók üzembe helyezését?**

Igen, állítsa be a folyamatos integráció/üzembe helyezés az Azure Container Registry vagy a DockerHub, a következő [folyamatos üzembe helyezés a Web App for Containers](./app-service-linux-ci-cd.md). Privát beállításjegyzék a tároló frissítéséhez majd a a webalkalmazás elindítása és leállítása. Vagy módosíthatja, vagy adjon hozzá egy helyőrző alkalmazást beállítást a tároló frissítésének kényszerítése.

**Átmeneti környezetek támogatására?**

Igen.

**Használható *WebDeploy/MSDeploy* webalkalmazás üzembe helyezéséhez?**

Igen, szükség nevű beállítása alkalmazás `WEBSITE_WEBDEPLOY_USE_SCM` való *hamis*.

**Az alkalmazásom Git üzemelő példánnyal sikertelen, ha a Linuxos web app használatával. Hogyan lehet a probléma megkerüléséhez?**

Ha a Linux-webalkalmazás Git-telepítés nem sikerül, válassza ki az alkalmazáskód üzembe helyezéséhez az alábbi lehetőségek közül:

- A folyamatos Készregyártás (előzetes verzió) szolgáltatással: Az alkalmazás forráskódjának tárolhatja az Azure DevOps Git-adattár vagy az Azure folyamatos készregyártással GitHub-adattárat. További információkért lásd: [a folyamatos teljesítés konfigurálása Linux-webalkalmazás](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Használja a [ZIP API üzembe helyezése](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): Ez az API használatára [SSH-t a webalkalmazás](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support) , és keresse meg a mappát, ahol szeretné telepíteni a kódot. Futtassa a következő kódot:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Ha hibaüzenetet kap, amely a `curl` parancs nem található, ellenőrizze, hogy a curl használatával telepítse `apt-get install curl` ahhoz, hogy futtatni az előző `curl` parancsot.

## <a name="language-support"></a>Nyelvi támogatás

**Szeretném használni a Node.js alkalmazás, minden olyan speciális beállításokat vagy konfigurációk websockets beállítani?**

Igen, tiltsa le `perMessageDeflate` a kiszolgálóoldali Node.js-kódban. Például a Socket.IO kódtár használja, ha használja a következő kódot:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Támogatja a .NET Core-alkalmazások nem fordított?**

Igen.

**Támogatják a Composer függőségi vezető PHP-alkalmazások?**

Igen, a Git telepítése során a Kudu kell észleli, hogy telepít egy PHP-alkalmazás (köszönhetően a composer.lock-fájljának jelenlétét,), és a Kudu majd aktivál egy zeneszerző telepítése.

## <a name="custom-containers"></a>Egyéni tárolók

**Saját egyéni tároló használok. A platform csatlakoztatni az SMB-megosztáson szeretném a `/home/` könyvtár.**

Azt is teheti meg, hogy a `WEBSITES_ENABLE_APP_SERVICE_STORAGE` Alkalmazásbeállítás *igaz*. Ne feledje, hogy ennek hatására tároló újraindul, ha a platform tárolási végighalad a változást.

>[!NOTE]
>Ha a `WEBSITES_ENABLE_APP_SERVICE_STORAGE` beállítás nincs megadva, vagy állítsa *hamis*, a `/home/` könyvtár nem megoszthatók méretezési példány, és ott írt fájlok nem maradnak meg újraindítások között.

**Saját egyéni tároló indítása hosszú időt vesz igénybe, és a platform újraindítja a tárolót, mielőtt végzett indul el.**

A platform vár, a tároló újraindítása előtti idő konfigurálhatja. Ehhez állítsa a `WEBSITES_CONTAINER_START_TIME_LIMIT` Alkalmazásbeállítás a kívánt értéket. Az alapértelmezett érték 230 másodperc, a maximális érték pedig 1800 másodperc.

**Mi az a privát tárolójegyzék URL formátuma?**

Adja meg a teljes beállításjegyzék URL-CÍMÉT, beleértve a `http://` vagy `https://`.

**Mi az a privát beállításjegyzék-beállítás a rendszerkép nevének formátuma?**

Adja hozzá a teljes rendszerképnevet, beleértve a privát tárolójegyzék URL-CÍMÉT (például myacr.azurecr.io/dotnet:latest). Egy egyéni portot használó képnév [nem lehet megadni a portálon keresztül](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Beállítása `docker-custom-image-name`, használja a [ `az` parancssori eszköz](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set).

**Tehetők több port is közzé a saját egyéni tároló rendszerképét?**

A Microsoft jelenleg nem támogatja egynél több-es port.

**Hozzáadhatom a saját storage?**

Igen, [saját tároló használata](https://docs.microsoft.com/azure/app-service/containers/how-to-serve-content-from-azure-storage) előzetes verzióban érhető el.

**Miért nem tud tallózni a saját egyéni tároló file system vagy a futó az SCM helyet a folyamatok?**

Az SCM helyet fut egy külön tárolót. Az alkalmazás tároló fájlt system vagy a futó folyamatok nem tudja ellenőrizni.

**Saját egyéni tároló a 80-as porttól eltérő portokat figyeli. Hogyan konfigurálhatok erre a portra irányíthatja a kérelmeket az alkalmazásom?**

Port automatikus észlelési van. Azt is megadhatja az alkalmazás nevű beállítása *WEBSITES_PORT* és adjon neki a várt portszám értékét. Korábban, a használt platform a *PORT* alkalmazásbeállítást. Ennek az alkalmazásbeállításnak kivezetjük, és használjon tervezzük *WEBSITES_PORT* kizárólag.

**Kell megvalósítani a HTTPS saját egyéni tárolóban?**

Nem, a platform kezeli a megosztott előtérrendszerek HTTPS-lezárást.

## <a name="multi-container-with-docker-compose-and-kubernetes"></a>Többtárolós docker Compose és a Kubernetes

**Hogyan konfigurálhatom az Azure Container Registry (ACR) többtárolós használata?**

Többtárolós, az ACR használatához **összes tárolórendszerkép** kell-e üzemeltetni az ACR beállításjegyzék ugyanarra a kiszolgálóra. Ha ugyanarra a beállításjegyzék-kiszolgálóra, szüksége lesz a Alkalmazásbeállítások létrehozása, és frissítse a Docker Compose- vagy Kubernetes konfigurációs fájl ACR rendszerkép neve is.

Hozza létre az alkalmazás következő beállításait:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (teljes URL-CÍMÉT, például: `https://<server-name>.azurecr.io`)
- DOCKER_REGISTRY_SERVER_PASSWORD (rendszergazdai hozzáférés engedélyezése az ACR-beállítások)

A konfigurációs fájlban hivatkozzon az ACR-lemezképet az alábbi példához hasonlóan:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Hogyan tudom, hogy milyen elérhető az internetről?**

- Csak egy tároló lehet nyissa meg a hozzáféréshez
- Csak a 80-as és 8080-as portot az elérhető-e (elérhetővé tett port)

Az alábbiakban a szabályokat, mely tároló érhető el – sorrendjében sorrend meghatározására:

- Nastavení aplikace `WEBSITES_WEB_CONTAINER_NAME` a tároló nevének beállítása
- Az első tároló a 80-as és 8080-as port definiálása
- Ha a fentiek egyike sem teljesül, az első tároló, a fájlban lesz elérhető (elérhető)

## <a name="pricing-and-sla"></a>Díjszabás és SLA

**Mi az a díjszabás, most, hogy a szolgáltatás általánosan elérhető?**

A számlázás a normál Azure App Service díjszabását, amely az alkalmazás fut órák száma.

## <a name="other-questions"></a>Egyéb kérdések

**Mik azok a beállítások alkalmazásnevek támogatott karakterek?**

Csak betűket (A – Z, a – z), számokat (0 – 9), és az aláhúzás karakterrel (_) használható alkalmazások beállításait.

**Hol juthatok új funkciókat?**

Is beküldi egy ötletét, a [Web Apps-visszajelzési fórumon](https://aka.ms/webapps-uservoice). Adja hozzá az ötlet címe "[Linux]".

## <a name="next-steps"></a>További lépések

- [Mi az Linuxon futó Azure App Service?](app-service-linux-intro.md)
- [Átmeneti környezetek beállítása az Azure App Service-ben](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Folyamatos üzembe helyezés a Web App for containers szolgáltatásban](./app-service-linux-ci-cd.md)
