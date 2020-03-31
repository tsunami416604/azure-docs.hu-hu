---
title: Beépített tárolók futtatása – gyakori kérdések
description: Válaszok az Azure App Service beépített Linux-tárolóival kapcsolatos gyakori kérdésekre.
keywords: Azure app service, web app, faq, linux, oss, web app tárolókhoz, többtárolós, többtárolós
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: f0a8b1758571a9473402d11a4d5141a11f76504d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245820"
---
# <a name="azure-app-service-on-linux-faq"></a>Azure App Service Linuxon – gyakori kérdések

Az App Service Linux-on való megjelenésével dolgozunk a funkciók hozzáadásán és a platformunk továbbfejlesztésén. Ez a cikk választ ad az ügyfeleink által az utóbbi időben feltett kérdésekre.

Ha kérdése van, fűzzön megjegyzést a cikkhez.

## <a name="built-in-images"></a>Beépített képek

**Azt akarom, hogy elágazás a beépített Docker-tárolók, hogy a platform biztosítja. Hol találom azokat a fájlokat?**

Az összes Docker-fájlt megtalálhatja a [GitHubon.](https://github.com/azure-app-service) Az összes Docker-tároló megtalálható a [Docker Hubon.](https://hub.docker.com/u/appsvc/)

<a id="#startup-file"></a>

**Milyen értékeket várnak az Indítási fájl szakaszban a futásidejű verem konfigurálásakor?**

| Verem           | Várt érték                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | a JAR alkalmazás indításához (például `java -jar /home/site/wwwroot/app.jar --server.port=80`) |
| Tomcat          | a parancsfájl helye a szükséges konfigurációk végrehajtásához `/home/site/deployments/tools/startup_script.sh`(például )          |
| Node.js         | a PM2 konfigurációs fájl vagy a parancsfájl                                |
| .NET Core       | a lefordított DLL-név`dotnet <myapp>.dll`                                 |
| Ruby            | a Ruby szkriptet, amelyet inicializálni szeretne az alkalmazásba                     |

Ezek a parancsok vagy parancsfájlok a beépített Docker-tároló indítása után, de az alkalmazáskód indítása előtt hajtják végre.

## <a name="management"></a>Kezelés

**Mi történik, ha megnyomom az újraindítás gombot az Azure Portalon?**

Ez a művelet megegyezik a Docker újraindításával.

**Csatlakozhatok a Secure Shell (SSH) rendszerhez az alkalmazástároló virtuális gépéhez való csatlakozáshoz?**

Igen, ezt a forrásvezérlés-kezelési (SCM) webhelyen keresztül teheti meg.

> [!NOTE]
> Az alkalmazástárolóhoz közvetlenül a helyi fejlesztési számítógépről is csatlakozhat SSH, SFTP vagy Visual Studio Code segítségével (Node.js-alkalmazások élő hibakereséséhez). További információkért tekintse meg a [Linuxon futó App Service-ben elérhető távoli hibakereséssel és SSH-val](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html) kapcsolatos cikket.
>

**Hogyan hozhatok létre Linux App Service-csomagot Egy SDK-n vagy egy Azure Resource Manager-sablonon keresztül?**

Állítsa az alkalmazásszolgáltatás **fenntartott** mezőjét *true*értékre.

## <a name="continuous-integration-and-deployment"></a>Folyamatos integráció és üzembe helyezés

**A webalkalmazás továbbra is egy régi Docker-tárolórendszerképet használ, miután frissítettem a lemezképet a Docker Hubon. Támogatja az egyéni tárolók folyamatos integrációját és telepítését?**

Igen, az Azure Container Registry vagy a DockerHub folyamatos integrációjának/üzembe helyezésének beállításához a [Web App for Containers folyamatos üzembe helyezését](./app-service-linux-ci-cd.md)követve. A privát nyilvántartások, frissítheti a tároló leállításával, majd a webalkalmazás elindításával. Vagy módosíthatja vagy hozzáadhat egy dummy alkalmazás beállítást a tároló frissítésének kényszerítéséhez.

**Támogatja az átmeneti környezeteket?**

Igen.

**Használhatom *a WebDeploy/MSDeploy alkalmazást* a webalkalmazás üzembe helyezéséhez?**

Igen, be kell állítania `WEBSITE_WEBDEPLOY_USE_SCM` egy *hamis*nevű alkalmazásbeállítást.

**Az alkalmazásGit-telepítése sikertelen linuxos webalkalmazás használata esetén. Hogyan oldhatom meg a problémát?**

Ha a Git-telepítés nem felel meg a Linux-webalkalmazásnak, válasszon az alábbi lehetőségek közül az alkalmazáskód telepítéséhez:

- A folyamatos kézbesítés (előzetes verzió) funkció használata: Az alkalmazás forráskódját egy Azure DevOps Git-tárházban vagy a GitHub-tárházban tárolhatja az Azure folyamatos kézbesítésének használatához. További információ: [A Folyamatos kézbesítés Linuxhoz webalkalmazás konfigurálása.](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/)

- Az [irányítószám-telepítés API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)használata: Az API használatához az [SSH-t a webalkalmazásban,](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support) és nyissa meg a mappát, ahol telepíteni szeretné a kódot. Futtassa a következő kódot:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Ha olyan hibaüzenetet `curl` kap, hogy a parancs nem található, az előző parancs futtatása `apt-get install curl` előtt győződjön meg arról, hogy a curl-t az előző `curl` parancs futtatása előtt telepíti.

## <a name="language-support"></a>Nyelvi támogatás

**Szeretném használni web sockets az én Node.js alkalmazás, bármilyen speciális beállításokat, vagy konfigurációk at beállítani?**

Igen, `perMessageDeflate` tiltsa le a kiszolgálóoldali Node.js kódot. Ha például socket.io használ, használja a következő kódot:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Támogatja a le nem fordított .NET Core alkalmazásokat?**

Igen.

**Támogatja composer, mint a függőség menedzser PHP alkalmazások?**

Igen, a Git-telepítés során a Kudu nak észlelnie kell, hogy egy PHP alkalmazást telepít (a composer.lock fájl jelenlétének köszönhetően), és a Kudu ezután elindítja a zeneszerző telepítését.

## <a name="custom-containers"></a>Egyéni tárolók

**Saját egyedi tárolót használok. Azt akarom, hogy a platform `/home/` smb-megosztást csatoljon a könyvtárhoz.**

Ha `WEBSITES_ENABLE_APP_SERVICE_STORAGE` a beállítás meg van **adva,** vagy *igaz*értékre van állítva, a könyvtár `/home/` **megosztásra kerül** a méretezési példányok között, és a megírt fájlok az újraindítások között **is megmaradnak.** A `WEBSITES_ENABLE_APP_SERVICE_STORAGE` *hamis* beállítás explicit módon letiltja a csatlakoztatást.

**Az egyéni tároló indítása hosszú időt vesz igénybe, és a platform újraindítja a tárolót, mielőtt befejezné az indítást.**

Beállíthatja, hogy a platform mennyi ideig várjon, mielőtt újraindítja a tárolót. Ehhez állítsa az `WEBSITES_CONTAINER_START_TIME_LIMIT` alkalmazásbeállítást a kívánt értékre. Az alapértelmezett érték 230 másodperc, a maximális érték pedig 1800 másodperc.

**Milyen formátumban van a rendszerleíró adatbázis-kiszolgáló URL-címe?**

Adja meg a rendszerleíró `http://` `https://`adatbázis teljes URL-címét, beleértve a vagy .

**Mi a képnév formátuma a privát beállításjegyzékben?**

Adja meg a teljes képnevét, beleértve a saját rendszerleíró adatbázis URL-címét (például myacr.azurecr.io/dotnet:latest). Az egyéni portot használó képnevek [nem adhatók meg a portálon keresztül.](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650) A `docker-custom-image-name`beállításhoz használja a [ `az` parancssori eszközt](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set).

**Több portot is elérhetővé tehetek az egyéni tárolórendszerképemen?**

Nem támogatjuk egynél több port felfedését.

**Hozhatok saját tárhelyet?**

Igen, [hozza magával a saját tárhelyét](https://docs.microsoft.com/azure/app-service/containers/how-to-serve-content-from-azure-storage) előzetes verzióban.

**Miért nem tudok böngészni az egyéni tároló fájlrendszerében, vagy hogyan futtathatok folyamatokat az SCM-webhelyről?**

Az SCM-hely külön tárolóban fut. Nem ellenőrizheti a fájlrendszert vagy az alkalmazástároló folyamatait.

**Az egyéni tárolóam a 80-as porttól eltérő portot figyel. Hogyan konfigurálhatom úgy az alkalmazásomat, hogy a kérelmeket az adott portra irányítsa?**

Automatikus portészlelés. Megadhat egy *WEBSITES_PORT* nevű alkalmazásbeállítást is, és megadhatja a várt portszám értékét. Korábban a platform a *PORT* alkalmazás beállítását használta. Azt tervezzük, hogy elavultak ezt az alkalmazás beállítást, és használja *WEBSITES_PORT* kizárólag.

**Kell-e https-t implementáljak az egyéni tárolómban?**

Nem, a platform kezeli a HTTPS-végződést a megosztott előtér-végpontokon.

## <a name="multi-container-with-docker-compose"></a>Többtárolós tároló docker-kompállmával

**Hogyan konfigurálhatom az Azure Container Registry (ACR) többtárolós használatával való használatra?**

Az ACR többtárolós használatával való használatához **az összes tárolólemezt** ugyanazon az ACR beállításjegyzék-kiszolgálón kell üzemeltetni. Miután ugyanazon a beállításjegyzék-kiszolgálón vannak, létre kell hoznia az alkalmazásbeállításokat, majd frissítenie kell a Docker Compose konfigurációs fájlt, hogy tartalmazza az ACR-lemezkép nevét.

Hozza létre a következő alkalmazásbeállításokat:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (teljes URL, `https://<server-name>.azurecr.io`pl. )
- DOCKER_REGISTRY_SERVER_PASSWORD (rendszergazdai hozzáférés engedélyezése az ACR-beállításokban)

A konfigurációs fájlon belül hivatkozzon az ACR-lemezképre a következő példához hasonlóan:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Honnan tudhatom, hogy melyik tároló érhető el az interneten?**

- Csak egy tároló lehet megnyitva a hozzáféréshez
- Csak a 80-as és 8080-as port érhető el (szabadon álló portok)

Itt vannak a szabályok meghatározására, amely konténer elérhető - a sorrendben:

- A `WEBSITES_WEB_CONTAINER_NAME` tároló nevére beállított alkalmazásbeállítás
- A 80-as vagy 8080-as portot meghatározó első tároló
- Ha a fentiek egyike sem igaz, a fájlban megadott első tároló elérhető lesz (kitéve)


## <a name="web-sockets"></a>Webes szoftvercsatornák

A webszoftver-szoftvercsatornák linuxos alkalmazásokban támogatottak.

> [!IMPORTANT]
> Az ingyenes alkalmazásszolgáltatási csomagok linuxos alkalmazásai jelenleg nem támogatottak. Dolgozunk a korlátozás megszüntetésén, és tervezzük, hogy akár 5 websocket kapcsolatot is támogatunk az ingyenes app service-csomagokon.

## <a name="pricing-and-sla"></a>Árképzés és SLA

**Mi az árképzés, most, hogy a szolgáltatás általánosan elérhető?**

Az árak termékváltozatonként és régiónként eltérőek lehetnek, de további részleteket az árképzési oldalon láthat: [App Service Pricing](https://azure.microsoft.com/pricing/details/app-service/linux/).

## <a name="other-questions"></a>Egyéb kérdések

**Mit jelent a "Kért funkció nem érhető el az erőforráscsoportban" jelent?**

Ez az üzenet akkor jelenhet meg, amikor az Azure Resource Manager (ARM) használatával webalkalmazást hoz létre. A jelenlegi korlátozás alapján, az azonos erőforráscsoport, nem keverheti a Windows és linuxos alkalmazások ugyanabban a régióban.

**Mik a támogatott karakterek az alkalmazásbeállítások nevében?**

Az alkalmazás beállításaihoz csak betűk (A-Z, a-z), számok (0-9) és aláhúzásjel (_) használhatók.

**Hol kérhetek új funkciókat?**

Küldje el ötletét a [Web Apps visszajelzési fórum](https://aka.ms/webapps-uservoice). Add hozzá a "[Linux]" szót az ötleted címéhez.

## <a name="next-steps"></a>További lépések

- [Mi az Azure App Service Linuxon?](app-service-linux-intro.md)
- [Átmeneti környezetek beállítása az Azure App Service-ben](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Folyamatos telepítés a webalkalmazással tárolókhoz](./app-service-linux-ci-cd.md)
