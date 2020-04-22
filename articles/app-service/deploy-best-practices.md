---
title: Ajánlott üzembe helyezési eljárások
description: Ismerje meg az Azure App Service üzembe helyezésének kulcsfontosságú mechanizmusait. Keresse meg a nyelv-specifikus ajánlások és egyéb kifogások.
keywords: Azure app szolgáltatás, webapp, üzembe helyezés, üzembe helyezés, folyamatok, build
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 4dd959d75fd582d787e68db4a415a4a694b9cda8
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770686"
---
# <a name="deployment-best-practices"></a>Gyakorlati tanácsok a telepítéshez

Minden fejlesztőcsapat egyedi követelményekkel rendelkezik, amelyek megnehezítik a hatékony üzembe helyezési folyamat megvalósítását bármely felhőszolgáltatáson. Ez a cikk bemutatja az App Service üzembe helyezésének három fő összetevőjét: üzembe helyezési források, folyamatok létrehozása és telepítési mechanizmusok. Ez a cikk néhány gyakorlati tanácsra és tippekre is kiterjed az adott nyelvi halmokkal.

## <a name="deployment-components"></a>Telepítési összetevők

### <a name="deployment-source"></a>Telepítési forrás

A központi telepítési forrás az alkalmazáskód helye. Éles alkalmazások esetében a központi telepítési forrás általában egy adattár, amelyet a verziókezelő szoftverek, például [a GitHub, a BitBucket vagy az Azure Repos](deploy-continuous-deployment.md)üzemeltet. Fejlesztési és tesztelési forgatókönyvek esetén a központi telepítési forrás lehet [egy projekt a helyi számítógépen.](deploy-local-git.md) Az App Service a OneDrive és a [Dropbox mappákat](deploy-content-sync.md) is támogatja telepítési forrásként. Bár a felhőalapú mappák megkönnyíthetiaz App Service használatának megkezdését, általában nem ajánlott ezt a forrást vállalati szintű éles alkalmazásokhoz használni. 

### <a name="build-pipeline"></a>Buildelési folyamat

Miután úgy döntött, egy központi telepítési forrás, a következő lépés az, hogy válasszon egy buildfolyamat. A buildfolyamat beolvassa a forráskódot a központi telepítési forrásból, és egy sor lépést hajt végre (például kód fordítása, a HTML és a JavaScript minifikálása, a tesztek futtatása és a csomagolási összetevők) az alkalmazás futtatható állapotban történő leolvasásához. A buildfolyamat által végrehajtott adott parancsok a nyelvi veremtől függenek. Ezek a műveletek végrehajthatók egy buildelőkiszolgálón, például az Azure-folyamatok, vagy helyileg végrehajtható.

### <a name="deployment-mechanism"></a>Telepítési mechanizmus

Az üzembe helyezési mechanizmus az a művelet, amely a beépített alkalmazást a webalkalmazás */home/site/wwwroot* könyvtárába helyezi. A */wwwroot* könyvtár egy csatlakoztatott tárolóhely, amelyet a webalkalmazás összes példánya megoszt. Amikor a központi telepítési mechanizmus az alkalmazást ebbe a könyvtárba helyezi, a példányok értesítést kapnak az új fájlok szinkronizálásáról. Az App Service a következő telepítési mechanizmusokat támogatja:

- Kudu végpontok: [Kudu](https://github.com/projectkudu/kudu/wiki) a nyílt forráskódú fejlesztői hatékonysági eszköz, amely fut, mint egy külön folyamat a Windows App Service, és a második tároló a Linux App Service. Kudu kezeli a folyamatos központi telepítések és HTTP-végpontok központi telepítés, például a zipdeploy.
- FTP és WebDeploy: A [hely vagy a felhasználói hitelesítő adatok](deploy-configure-credentials.md)használatával [ftp-n](deploy-ftp.md) vagy webtelepítésen keresztül tölthet fel fájlokat. Ezek a mechanizmusok nem megy keresztül Kudu.  

A központi telepítési eszközök, például az Azure Pipelines, a Jenkins és a szerkesztő bővítmények ezen üzembe helyezési mechanizmusok egyikét használják.

## <a name="use-deployment-slots"></a>Központi telepítési tárolóhelyek használata

Amikor csak lehetséges, [használjon központi telepítési helyeket](deploy-staging-slots.md) egy új éles build üzembe helyezésekor. Standard app service-csomag vagy annál jobb használata esetén üzembe helyezheti az alkalmazást egy átmeneti környezetben, ellenőrizheti a módosításokat, és füstteszteket végezhet. Ha készen áll, kicserélheti az átmeneti és a termelési tárolóhelyeket. A csereművelet felmelegíti a szükséges feldolgozópéldányokat, hogy megfeleljen a termelési skálának, így kiküszöbölve az állásidőt.

### <a name="continuously-deploy-code"></a>Kód folyamatos üzembe helyezése

Ha a projekt kijelölt ágak tesztelése, minőségbiztosítási és átmeneti, majd minden egyes ilyen ágak at kell folyamatosan telepíteni egy átmeneti tárolóhelyre. (Ez az úgynevezett [Gitflow design](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow).) Ez lehetővé teszi az érdekelt felek számára, hogy könnyen felmérhessék és teszteljék az üzembe helyezett ágat. 

A folyamatos üzembe helyezés soha nem engedélyezhető az éles környezetben. Ehelyett az éles ág (gyakran fő) kell telepíteni egy nem éles tárolóhelyre. Ha készen áll az alapág felszabadítására, cserélje ki az éles tárolóhelyre. Éles környezetre való felcserélés – ahelyett, hogy éles környezetben lenne üzembe helyezve – megakadályozza az állásidőt, és lehetővé teszi a módosítások ismételt visszagörgetését. 

![Bővítőhely-használat vizualizációja](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>Tárolók folyamatos üzembe helyezése

A Docker-ből vagy más tárolójegyzékekből származó egyéni tárolók esetén telepítse a lemezképet egy átmeneti tárolóhelyre, és cserélje éles környezetbe az állásidő megelőzése érdekében. Az automatizálás bonyolultabb, mint a kód központi telepítése, mert le kell adnia a lemezképet egy tároló rendszerleíró adatbázisba, és frissítenie kell a rendszerképcímkét a webappon.

Minden egyes ág szeretné telepíteni egy tárolóhelyre, állítsa be az automatizálást, hogy a következő minden véglegesítése az ágra.

1. **Építsd meg és címkézze fel a képet.** A buildfolyamat részeként címkézze meg a képet a git véglegesítési azonosítóval, időbélyeggel vagy más azonosítható adatokkal. A legjobb, ha nem használja az alapértelmezett "legújabb" címkét. Ellenkező esetben nehéz nyomon követni, hogy milyen kód van jelenleg telepítve, ami sokkal nehezebbé teszi a hibakeresést.
1. **Nyomja le a címkézett képet**. Miután a rendszerkép megvan építve és címkézve, a folyamat leküldéses a rendszerképet a tároló rendszerleíró adatbázisba. A következő lépésben a központi telepítési hely lekéri a címkézett lemezképet a tároló beállításjegyzékéből.
1. **Frissítse a központi telepítési helyet az új lemezképcímkével.** A tulajdonság frissítésekor a hely automatikusan újraindul, és lekéri az új tárolólemezképet.

![Bővítőhely-használat vizualizációja](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

Az alábbiakban példákat talál a közös automatizálási keretrendszerekre.

### <a name="use-azure-devops"></a>Az Azure DevOps használata

Az App Service [beépített folyamatos szállítással](deploy-continuous-deployment.md) rendelkezik a tárolók hoz a deployment centeren keresztül. Nyissa meg az alkalmazást az [Azure Portalon,](https://portal.azure.com/) és válassza a **Központi telepítés csoportban a Központi** **telepítés**lehetőséget. Kövesse az utasításokat a tárház és az ág kiválasztásához. Ez konfigurálja a DevOps buildelési és kiadási folyamat automatikusan létre, címke és üzembe helyezi a tárolót, amikor új véglegesítések leküldéses a kiválasztott ágra.

### <a name="use-github-actions"></a>GitHub-műveletek használata

A Tároló üzembe helyezését a [GitHub-műveleteksegítségével](containers/deploy-container-github-action.md)is automatizálhatja.  Az alábbi munkafolyamat-fájl létrehozza és címkézi a tárolót a véglegesítési azonosítóval, lenyomja egy tároló rendszerleíró adatbázisába, és frissíti a megadott helytárolót az új lemezcímke címkével.

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@master

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>Más automatizálási szolgáltatók használata

A korábban felsorolt lépések más automatizálási segédprogramokra, például a CircleCI-re vagy a Travis CI-re vonatkoznak. Azonban az Azure CLI-t kell használnia a központi telepítési tárolóhelyek új lemezképcímkékkel való frissítéséhez az utolsó lépésben. Az Azure CLI-t az automation-parancsfájlban való használatához hozzon létre egy egyszerű szolgáltatás a következő paranccsal.

```shell
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

A parancsfájlban jelentkezzen `az login --service-principal`be a használatával, amely megadja a főfelhasználó adatait. Ezután `az webapp config container set` beállíthatja a tároló nevét, címkéjét, a rendszerleíró adatbázis URL-címét és a rendszerleíró adatbázis jelszavát. Az alábbiakban néhány hasznos linket, hogy a konténer CI folyamat.

- [Bejelentkezés az Azure CLI-be a Circle CI-n](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>Nyelvspecifikus szempontok

### <a name="java"></a>Java

Használja a Kudu [zipdeploy/](deploy-zip.md) API jar alkalmazások üzembe helyezéséhez, és [wardeploy/](deploy-zip.md#deploy-war-file) WAR alkalmazásokhoz. Jenkins használata esetén ezeket az API-kat közvetlenül a központi telepítési fázisban használhatja. További információt [ebben a cikkben](../jenkins/execute-cli-jenkins-pipeline.md)talál.

### <a name="node"></a>Csomópont

Alapértelmezés szerint a Kudu végrehajtja a csomópontalkalmazás`npm install`( buildlépéseit. Ha egy buildszolgáltatás, például az Azure DevOps, majd a Kudu build szükségtelen. A Kudu build letiltásához hozzon létre egy alkalmazásbeállítást, `SCM_DO_BUILD_DURING_DEPLOYMENT`amelynek `false`értéke .

### <a name="net"></a>.NET 

Alapértelmezés szerint a Kudu végrehajtja a .NET`dotnet build`alkalmazás ( . Ha egy buildszolgáltatás, például az Azure DevOps, majd a Kudu build szükségtelen. A Kudu build letiltásához hozzon létre egy alkalmazásbeállítást, `SCM_DO_BUILD_DURING_DEPLOYMENT`amelynek `false`értéke .

## <a name="other-deployment-considerations"></a>Egyéb telepítési szempontok

### <a name="local-cache"></a>Helyi gyorsítótár

Az Azure App Service-tartalom az Azure Storage-ban tárolódik, és tartós módon, tartalommegosztásként jelenik meg. Egyes alkalmazásoknak azonban csak egy nagy teljesítményű, csak olvasható tartalomtárra van szükségük, amelyet magas rendelkezésre állással futtathatnak. Ezek az alkalmazások a [helyi gyorsítótár](overview-local-cache.md)használatának előnyeit élvezhetik. Helyi cache nem ajánlott tartalomkezelő oldalak, mint a WordPress.

Mindig használja a helyi gyorsítótárat a [központi telepítési helyekkel](deploy-staging-slots.md) együtt az állásidő elkerülése érdekében. A szolgáltatások együttes használatáról ebben a [szakaszban](overview-local-cache.md#best-practices-for-using-app-service-local-cache) talál tájékoztatást.

### <a name="high-cpu-or-memory"></a>Magas CPU vagy memória

Ha az App Service-csomag a rendelkezésre álló processzor vagy memória több mint 90%-át használja, előfordulhat, hogy az alapul szolgáló virtuális gép nem tudja feldolgozni a központi telepítést. Ha ez történik, ideiglenesen felskálázása a példányok száma a központi telepítés végrehajtásához. Miután a központi telepítés befejeződött, visszaadhatja a példányok számát az előző értékre.

Az ajánlott eljárásokról az [App Service Diagnosztika](https://docs.microsoft.com/azure/app-service/overview-diagnostics) webhelyre talál további információt az erőforrásra vonatkozó, bevált gyakorlati tanácsokért.

- Nyissa meg a webalkalmazást az [Azure Portalon.](https://portal.azure.com)
- Kattintson **a Problémák diagnosztizálása és megoldása** a bal oldali navigációs, amely megnyitja App Service Diagnosztika.
- Válassza az **Ajánlott eljárások** kezdőlapcsempét.
- Kattintson **a gyakorlati tanácsok a rendelkezésre állás & teljesítményéhez,** vagy az optimális **konfigurációra vonatkozó gyakorlati tanácsok ra kattintva** megtekintheti az alkalmazás aktuális állapotát az ajánlott eljárások tekintetében.

Ezen a hivatkozáson keresztül közvetlenül is megnyithatja `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`az App Service Diagnosztikát az erőforráshoz: .
