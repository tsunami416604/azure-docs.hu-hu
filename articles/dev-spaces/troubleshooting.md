---
title: Hibakeresés
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Az Azure dev Spaces engedélyezése és használata során felmerülő gyakori problémák elhárítása és megoldása
keywords: 'Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s '
ms.openlocfilehash: 0cf8eb7b07622a989bc78637b1601ba68b9b5f6f
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251121"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Az Azure dev Spaces hibaelhárítása

Ez az útmutató az Azure fejlesztési tárolóhelyek használata esetén előfordulhat, hogy rendelkezik kapcsolatos gyakori problémák információkat tartalmaz.

Ha problémába ütközik az Azure dev Spaces használata során, hozzon létre egy [problémát az Azure dev Spaces GitHub-tárházban](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Előkészületek

Ha hatékonyabban szeretné elhárítani a problémákat, érdemes lehet részletesebb naplókat létrehozni a felülvizsgálathoz.

A Visual Studio bővítménynél állítsa a `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` környezeti változó értékét 1-re. Ügyeljen arra, hogy a Visual Studio for a környezeti változó érvénybe léptetéséhez indítsa újra. Ha engedélyezve van, a részletes naplók a `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` könyvtárba íródnak.

A CLI-ben további információkat adhat meg a parancs végrehajtása során a `--verbose` kapcsoló használatával. `%TEMP%\Azure Dev Spaces`részletesebb naplókat is kereshet. Mac gépen a *temp* könyvtár a `echo $TMPDIR` futtatásával érhető el egy terminál-ablakban. A Linux rendszerű számítógépeken általában a *temp* könyvtár `/tmp`. Továbbá ellenőrizze, hogy a naplózás engedélyezve van-e az [Azure CLI konfigurációs fájljában](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables).

Az Azure dev Spaces is működik a legjobban, ha egyetlen példányt vagy Pod-t tesz elérhetővé. A `azds.yaml` fájl egy *replicaCount*-beállítást tartalmaz, amely megadja, hogy a Kubernetes hány hüvelyt futtat a szolgáltatásban. Ha úgy módosítja a *replicaCount* , hogy úgy konfigurálja az alkalmazást, hogy több hüvelyt futtasson egy adott szolgáltatáshoz, akkor a hibakereső az első hüvelyhez csatlakozik, amikor betűrendbe van sorolva. A hibakereső egy másik Pod-hoz csatlakozik, amikor az eredeti Pod újrahasznosítja, ami valószínűleg váratlan viselkedést eredményez.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Az Azure dev Spaces engedélyezésekor felmerülő gyakori problémák

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Hiba: "nem sikerült létrehozni az Azure dev Spaces Controllert"

Előfordulhat, hogy ezt a hibaüzenetet, ha probléma merül fel a vezérlő létrehozása. Ha átmeneti hiba történt, törölje, majd hozza létre újra a vezérlőt a javításhoz.

A vezérlőt is törölheti:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Vezérlő törléséhez használja az Azure dev Spaces CLI-t. A vezérlőt nem lehet törölni a Visual studióból. Nem telepítheti az Azure dev Spaces CLI-t a Azure Cloud Shell, így nem törölhet vezérlőt a Azure Cloud Shellból.

Ha nem telepítette az Azure dev Spaces CLI-t, először telepítse azt a következő parancs használatával, majd törölje a vezérlőt:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

A vezérlő újbóli létrehozása a parancssori felület vagy a Visual Studio elvégezhető. Példákért tekintse meg a [csapat fejlesztését](quickstart-team-development.md) , vagy [fejlesszen a .net Core](quickstart-netcore-visualstudio.md) gyors útmutatókkal.

### <a name="controller-create-failing-because-of-controller-name-length"></a>A vezérlő létrehozása sikertelen a vezérlő nevének hossza miatt

Az Azure dev Spaces-vezérlő neve nem lehet 31 karakternél hosszabb. Ha a vezérlő neve meghaladja a 31 karaktert, amikor egy AK-fürtön engedélyezi a dev Spaces-t, vagy létrehoz egy vezérlőt, hibaüzenet jelenik meg. Például:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

A probléma megoldásához hozzon létre egy másik nevű vezérlőt. Például:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>A fejlesztői szóközök engedélyezése a Windows-csomópontok AK-fürthöz való hozzáadásakor

Jelenleg az Azure dev Spaces kizárólag Linux-hüvelyeken és-csomópontokon futtatható. Ha egy AK-fürthöz Windows-csomópont tartozik, akkor biztosítania kell, hogy az Azure dev Spaces hüvelyek csak Linux-csomópontokon legyenek ütemezve. Ha egy Azure dev Spaces Pod Windows-csomóponton való futtatásra van ütemezve, a pod nem indul el, és a fejlesztői szóközök engedélyezése sikertelen lesz.

A probléma megoldásához [vegyen fel egy Taint](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) az AK-fürtbe, hogy a Linux-hüvelyek ne legyenek ütemezve Windows-csomóponton való futtatásra.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Hiba: nem található a fürtön kész állapotú, nem fertőzött Linux-csomópontok. A hüvelyek "azds" névtérben való üzembe helyezéséhez kész állapotban kell lennie legalább egy nem fertőzött linuxos csomópontnak. "

Az Azure dev Spaces nem tudta létrehozni a vezérlőt az AK-fürtön, mert nem talált egy nem fertőzött csomópontot *készenléti* állapotban a hüvelyek ütemezett beléptetéséhez. Az Azure dev Spaces-ben legalább egy Linux-csomópontra *kész* állapotban kell lennie, amely lehetővé teszi a hüvelyek ütemezését a tolerancia megadása nélkül.

A probléma megoldásához [frissítse a szennyező konfigurációt](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) az AK-fürtön, hogy legalább egy Linux-csomópont lehetővé tegye a hüvelyek ütemezését a tolerancia megadása nélkül. Győződjön meg arról is, hogy legalább egy Linux-csomópont, amely lehetővé teszi a hüvelyek ütemezését, a tolerálás nélkül *kész* állapotban van. Ha a csomópont hosszú időt vesz elérhetővé a *kész* állapot elérése érdekében, próbálja meg újraindítani a csomópontot.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Hiba: "az Azure dev Spaces CLI nincs megfelelően telepítve" `az aks use-dev-spaces` futtatásakor

Az Azure dev Spaces parancssori felületének frissítése megváltoztatta a telepítési útvonalat. Ha a 2.0.63-nél korábbi Azure CLI-verziót használja, akkor ez a hiba látható. Az Azure CLI verziójának megjelenítéséhez használja a `az --version`.

```azurecli
az --version
azure-cli                         2.0.60 *
...
```

Annak ellenére, hogy a `az aks use-dev-spaces` fut az Azure CLI egy verziójával a 2.0.63 előtt, a telepítés sikeres lesz. A `azds`t továbbra is használhatja problémák nélkül.

A probléma megoldásához frissítse az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) telepítését 2.0.63 vagy újabb verzióra. Ez a frissítés a `az aks use-dev-spaces`futtatásakor kapott hibaüzenetet fogja feloldani. Másik lehetőségként továbbra is használhatja az Azure CLI aktuális verzióját és az Azure dev Spaces CLI-t.

### <a name="error-unable-to-reach-kube-apiserver"></a>Hiba: "nem sikerült elérni a Kube-apiserver"

Ez a hiba akkor fordulhat elő, ha az Azure dev Spaces nem tud csatlakozni az AK-fürt API-kiszolgálójához. 

Ha az AK-alapú fürt API-kiszolgálójához való hozzáférés le van tiltva, vagy ha az AK-fürthöz engedélyezve van az [API-kiszolgáló által engedélyezett IP-címtartományok](../aks/api-server-authorized-ip-ranges.md) , akkor a fürtön a [régión alapuló további tartományokat](https://github.com/Azure/dev-spaces/tree/master/public-ips)is [létre kell hoznia](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) vagy [frissítenie](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) kell.

A kubectl parancsok futtatásával győződjön meg arról, hogy az API-kiszolgáló elérhető. Ha az API-kiszolgáló nem érhető el, forduljon az AK-támogatáshoz, és próbálkozzon újra, ha az API-kiszolgáló működik.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Gyakori problémák a projekt előkészítésekor az Azure dev Spaces szolgáltatásban

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Figyelmeztetés: "a Docker nem hozható létre a nem támogatott nyelv miatt"
Az Azure fejlesztési tárolóhelyek C# és Node.js natív támogatást biztosít. Ha a `azds prep`t egy olyan könyvtárban futtatja, amelyben az egyik nyelven írt kód szerepel, az Azure dev Spaces automatikusan létrehoz egy megfelelő Docker.

Továbbra is használhatja az Azure dev Spaces szolgáltatást más nyelveken írt kóddal, de a `azds up` első futtatása előtt manuálisan kell létrehoznia a Docker.

Ha az alkalmazás olyan nyelven íródott, amelyet az Azure dev Spaces nem támogat, meg kell adnia egy megfelelő Docker a kódot futtató tároló-rendszerkép létrehozásához. A Docker egy [listát biztosít a Dockerfiles írásához ajánlott eljárásokról](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) és egy olyan [Docker-hivatkozásról](https://docs.docker.com/engine/reference/builder/) , amely segíthet az igényeinek megfelelő Docker írására.

Ha megfelelő Docker rendelkezik, a `azds up` futtatásával futtathatja az alkalmazást az Azure dev Spaces szolgáltatásban.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Gyakori problémák az Azure dev Spaces szolgáltatás indításakor vagy leállításakor

### <a name="error-config-file-not-found"></a>Hiba "a konfigurációs fájl nem található:"

`azds up`futtatásakor a következő hibaüzenet jelenhet meg:. Mind a `azds up`, mind a `azds prep` a fejlesztői térben futtatni kívánt projekt gyökérkönyvtárában kell futnia.

A probléma megoldása:
1. Módosítsa az aktuális könyvtár a legfelső szintű mappát, amely tartalmazza a szolgáltatás kódot. 
1. Ha nem rendelkezik _azds. YAML_ fájllal a Code mappában, futtassa a `azds prep` eszközt a Docker, a Kubernetes és az Azure dev Spaces-eszközök létrehozásához.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Időtúllépés: "Várakozás a tároló rendszerképének összeállítására..." lépés AK virtuális csomópontokkal

Ez az időkorlát akkor fordul elő, ha a fejlesztői szóközök használatával futtat olyan szolgáltatást, amely egy AK-beli [virtuális csomóponton](https://docs.microsoft.com/azure/aks/virtual-nodes-portal)való futtatásra van konfigurálva. A dev Spaces jelenleg nem támogatja a virtuális csomópontokon futó szolgáltatások létrehozását vagy hibakeresését.

Ha a `--verbose` kapcsolóval `azds up` futtat, vagy engedélyezi a részletes naplózást a Visual Studióban, további részleteket láthat:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

A fenti parancs azt mutatja, hogy a szolgáltatás Pod hozzá lett rendelve a *virtuális csomópont-ACI-Linux*rendszerhez, amely egy virtuális csomópont.

A probléma megoldásához frissítse a szolgáltatás Helm diagramját, és távolítsa el azokat a *nodeSelector* vagy *toleráló* értékeket, amelyek lehetővé teszik, hogy a szolgáltatás virtuális csomóponton fusson. Ezek az értékek általában a diagram `values.yaml` fájljában vannak meghatározva.

Továbbra is használhat olyan AK-fürtöt, amelyen engedélyezve van a virtuális csomópontok funkciója, ha a fejlesztéshez és a hibakereséshez használni kívánt szolgáltatás egy virtuálisgép-csomóponton fut. A szolgáltatás egy virtuálisgép-csomóponton található fejlesztői szóközökkel való futtatása az alapértelmezett konfiguráció.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Hiba: "nem található a Ready up Pod" a dev Spaces indításakor

Ez a hiba akkor fordul elő, ha a Helm-ügyfél már nem képes kommunikálni a tiller valóban pod a fürtben futó.

A probléma megoldásához indítsa újra a fürtben lévő ügynökök csomópontjait.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Hiba "kiadás azds-\<azonosító\>-\<spacename\>-\<szolgáltatásnév\> sikertelen: a (z)"\<szolgáltatásnév\>"már létezik" vagy "lekéréses hozzáférés megtagadva a \<szolgáltatásnév\>, adattár nem létezik, vagy a" Docker bejelentkezhessen "szükséges.

Ezek a hibák akkor fordulhatnak elő, ha összekeveri a Direct Helm-parancsokat (például `helm install`, `helm upgrade`vagy `helm delete`) a dev Spaces parancsokkal (például `azds up` és `azds down`) ugyanazon a fejlesztői területen belül. Ezek azért történnek, mert a dev Spaces saját kormányrúd-példánnyal rendelkezik, ami ütközik a saját, azonos fejlesztői térben futó példányával.

A Helm-parancsok és a dev Spaces-parancsok is használhatók ugyanazon az AK-fürtön, de az egyes fejlesztői szóközöket használó névtereknek vagy az egyiket kell használniuk.

Tegyük fel például, hogy egy Helm parancs használatával futtatja a teljes alkalmazást egy szülő fejlesztői térben. A gyermek fejlesztési területeit kikapcsolhatja a szülőtől, a fejlesztői szóközök használatával egyéni szolgáltatásokat futtathat a gyermek-fejlesztési területeken belül, és tesztelheti a szolgáltatásokat. Ha készen áll a módosítások beadására, a Helm parancs használatával telepítse a frissített kódot a szülő fejlesztői területére. Ne használja a `azds up` a frissített szolgáltatás futtatására a szülő fejlesztői térben, mert az ütközik a Helm használatával futtatott szolgáltatással.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Meglévő Docker nem használatos tároló létrehozásához

Az Azure dev Spaces konfigurálható úgy, hogy a projekt egy adott _Docker_ mutasson. Ha úgy tűnik, hogy az Azure dev Spaces nem a tárolók felépítésére várt _Docker_ használja, akkor előfordulhat, hogy explicit módon meg kell adnia az Azure dev Spaces-Docker, amelyeket használni kíván. 

A probléma megoldásához nyissa meg az _azds. YAML_ fájlt, amelyet az Azure dev Spaces hozott létre a projektben. Frissítési *konfigurációk: fejlesztés: build: Docker* , hogy a használni kívánt Docker mutasson. Például:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>"Jogosulatlan: hitelesítés szükséges" hibaüzenet, ha Docker-rendszerképet szeretne használni egy privát beállításjegyzékből

Olyan Docker-rendszerképet használ, amely hitelesítést igénylő privát beállításjegyzékből áll.

A probléma megoldásához engedélyezheti a fejlesztői terek számára, hogy a [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets)használatával hitelesítsék és lehívhatják a képeket ebből a privát beállításjegyzékből. A imagePullSecrets használatához [hozzon létre egy Kubernetes titkot](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) abban a névtérben, amelyben a rendszerképet használja. Ezután adja meg a titkot `azds.yaml`imagePullSecret.

Alább látható egy példa a imagePullSecrets megadására a `azds.yaml`ban.

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> A `azds.yaml` imagePullSecrets beállítása felülírja a `values.yaml`megadott imagePullSecrets.

### <a name="error-service-cannot-be-started"></a>Hiba: a szolgáltatás nem indítható el.

Előfordulhat, hogy ezt a hibaüzenetet, ha a szolgáltatás kód nem indul el. Oka általában a felhasználói kódban. További diagnosztikai adatok megjelenítéséhez engedélyezze a szolgáltatás indításakor részletesebb naplózást.

A parancssorban használja a `--verbose` a részletesebb naplózás engedélyezéséhez. `--output`használatával kimeneti formátumot is megadhat. Például:

```cmd
azds up --verbose --output json
```

A Visual Studióban:

1. Nyissa meg az **eszközök > lehetőségeket** , és a **projektek és megoldások**területen válassza a **Létrehozás és Futtatás**lehetőséget.
2. Az MSBuild-projekt beállításainak módosításához adja meg a **kimeneti részletességet** **részletes** vagy **diagnosztikai**értékre.

    ![Képernyőkép a beállítások panel](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Szolgáltatás újrafuttatása a vezérlő ismételt létrehozása után

A szolgáltatás *nem indítható el* , ha a szolgáltatás újrafuttatására tett kísérletet követően eltávolította a szolgáltatást, majd újból létrehozta az ehhez a fürthöz társított Azure dev Spaces-vezérlőt. Ebben az esetben a részletes kimenet a következő szöveget tartalmazza:

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Ez a hiba azért fordul elő, mert a dev Spaces vezérlő eltávolítása nem távolítja el a vezérlő által korábban telepített szolgáltatásokat. A vezérlő újbóli létrehozását, és ezután megpróbálja futtatni a szolgáltatásokat az új vezérlőt használó meghiúsul, mert a régi szolgáltatások helyükön vannak.

A probléma megoldásához a `kubectl delete` parancs használatával távolítsa el manuálisan a régi szolgáltatásokat a fürtből, majd futtassa újra a dev Spaces szolgáltatást az új szolgáltatások telepítéséhez.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Hiba: a szolgáltatás nem indítható el. többfázisú Dockerfiles használata esetén

Egy *szolgáltatás nem indítható el* , ha többfázisú Docker használ. Ebben az esetben a részletes kimenet a következő szöveget tartalmazza:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Ez a hiba azért fordul elő, mert az Azure dev Spaces jelenleg nem támogatja a többfázisú buildeket. A többfázisú buildek elkerüléséhez írja át a Docker.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>A hálózati forgalmat nem továbbítja a rendszer az AK-fürthöz a fejlesztői gép csatlakoztatásakor

Ha [Az Azure dev Spaces használatával csatlakozik az AK-fürthöz a fejlesztői géphez](how-to/connect.md), előfordulhat, hogy olyan problémába ütközik, amelyben a hálózati forgalom nem továbbítódik a fejlesztési gép és az AK-fürt között.

Ha a fejlesztési gépet az AK-fürthöz csatlakoztatja, az Azure dev Spaces a fejlesztési gép `hosts` fájljának módosításával továbbítja a hálózati forgalmat az AK-fürt és a fejlesztői számítógép között. Az Azure dev Spaces létrehoz egy bejegyzést a `hosts`ban annak a Kubernetes-szolgáltatásnak a címeként, amelyet állomásnévként cserél. Ez a bejegyzés a port továbbításával használható a fejlesztői gép és az AK-fürt közötti közvetlen hálózati forgalomhoz. Ha a fejlesztési gépen lévő szolgáltatás ütközik a cserélni kívánt Kubernetes szolgáltatás portjával, az Azure dev Spaces nem tudja továbbítani a Kubernetes szolgáltatás hálózati forgalmát. Például a *Windows BranchCache* szolgáltatás általában *0.0.0.0:80*-ra van kötve, amely ütközést okoz a 80-es port összes helyi IP-címeinél.

A probléma megoldásához le kell állítania minden olyan szolgáltatást vagy folyamatot, amely ütközik a cserélni kívánt Kubernetes szolgáltatás portjával. Az eszközök, például a *netstat*segítségével megvizsgálhatja, hogy a fejlesztői gépen milyen szolgáltatások és folyamatok ütköznek.

Például a *Windows BranchCache* szolgáltatás leállításához és letiltásához:
* Futtassa `services.msc` parancsot a parancssorból.
* Kattintson a jobb gombbal a *BranchCache* elemre, és válassza a *Tulajdonságok*lehetőséget.
* Kattintson a *Leállítás*gombra.
* Igény szerint letilthatja azt az *indítási típus* *letiltásának beállításával.*
* Kattintson az *OK* gombra.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>A Visual Studio és a Visual Studio Code együttes használata az Azure dev Spaces használatával – gyakori problémák

### <a name="error-required-tools-and-configurations-are-missing"></a>Hiba: a szükséges eszközök és konfigurációk hiányoznak.

Ez a hiba akkor fordulhat elő, a VS Code elindításához: "[Azure fejlesztési tárolóhelyek] szükséges eszközök és konfigurációk fejlesztése és hibakeresése a"[projekt neve]"hiányoznak."
A hiba azt jelenti, hogy adott azds.exe nem szerepel a PATH környezeti változóhoz, a VS Code-ban látható módon.

Próbálkozzon a VS Code indításával egy parancssorban, ahol a PATH környezeti változó helyesen van beállítva.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Hiba: a "projektnév" létrehozásához és hibakereséséhez szükséges eszközök elavultak. "

Ez a hibaüzenet akkor jelenik meg a Visual Studio Code-ban, ha az Azure dev Spaces-hez a VS Code bővítmény újabb verziója van telepítve, de az Azure dev Spaces CLI egy régebbi verziója.

Próbálja meg letölteni és telepíteni az Azure dev Spaces CLI legújabb verzióját:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Hiba: "a hibakereső bővítmény nem található a következő típushoz: coreclr"

Ez a hiba a Visual Studio Code Debugger futtatásakor fordulhat elő. Lehet, hogy nincs C# telepítve a vs Code bővítmény a fejlesztői gépen. A C# bővítmény a .net Core (CoreCLR) hibakeresési támogatását is tartalmazza.

A probléma megoldásához telepítse a [vs Code-bővítményt C#a következőhöz: ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Hiba: a "coreclr" konfigurált hibakeresési típus nem támogatott "

Ez a hiba a Visual Studio Code Debugger futtatásakor fordulhat elő. Előfordulhat, hogy a fejlesztői gépen nincs telepítve a VS Code bővítmény az Azure dev Spaces szolgáltatáshoz.

A probléma megoldásához telepítse a [vs Code-bővítményt az Azure dev Spaces szolgáltatáshoz](get-started-netcore.md).

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Hiba "Érvénytelen" CWD "/src" érték. A rendszer nem találja a megadott fájlt." vagy "indítása: program"/ src / [projekt bináris elérési út]"nem létezik."

Ez a hiba a Visual Studio Code Debugger futtatásakor fordulhat elő. Alapértelmezés szerint a VS Code bővítmény a tárolóban lévő projekt munkakönyvtáraként `src` használja. Ha frissítette a `Dockerfile` egy másik munkakönyvtár megadására, akkor ez a hiba látható.

A probléma megoldásához frissítse a `launch.json` fájlt a Project mappa `.vscode` alkönyvtárában. Módosítsa úgy a `configurations->cwd` direktívát, hogy ugyanarra a könyvtárba mutasson, mint a projekt `Dockerfile`ban definiált `WORKDIR`. Előfordulhat, hogy frissítenie kell az `configurations->program` direktívát is.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Hiba: a "azds" pipe program váratlanul kilépett a 126 kóddal. "

Ez a hiba a Visual Studio Code Debugger futtatásakor fordulhat elő.

A probléma megoldásához zárjuk be és nyissa meg újra a Visual Studio Code-ot. Indítsa újra a hibakeresőt.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Hiba "a belső figyelés sikertelen: ENOSPC megtekintése" egy Node. js-alkalmazás hibakeresésének csatolásakor

Ez a hiba akkor fordul elő, ha a pod-t futtató csomópont a hibakeresőhöz csatlakoztatni kívánt Node. js-alkalmazással túllépte az *FS. inotify. max_user_watches* értéket. Bizonyos esetekben előfordulhat, [hogy az *FS. inotify. max_user_watches* alapértelmezett értéke túl kicsi ahhoz, hogy a hibakeresőt közvetlenül egy Pod-hez csatolja](https://github.com/Azure/AKS/issues/772).

A probléma ideiglenes megkerülő megoldásként növelje az *FS. inotify. max_user_watches* értékét a fürt mindegyik csomópontján, majd indítsa újra a csomópontot a módosítások érvénybe léptetéséhez.

## <a name="other-common-issues"></a>Egyéb gyakori problémák

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>A "azds" hiba nem ismerhető fel belső vagy külső parancsként, futtatható programként vagy batch-fájlként.

Ez a hiba akkor fordulhat elő, ha `azds.exe` nincs telepítve vagy nem megfelelően van konfigurálva.

A probléma megoldása:

1. Keresse meg a (z)% ProgramFiles%/Microsoft SDKs\Azure\Azure dev Spaces CLI-t a `azds.exe`. Ha nincs, adott helyen hozzáadása a PATH környezeti változóhoz.
2. Ha `azds.exe` nincs telepítve, futtassa a következő parancsot:

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>"Microsoft. DevSpaces/regisztráció/művelet" engedélyezési hiba

Az Azure fejlesztői tárhelyek kezeléséhez *tulajdonosi* vagy *közreműködői* hozzáférésre van szüksége az Azure-előfizetésben. Ha a fejlesztői helyeket szeretné kezelni, és nem rendelkezik *tulajdonosi* vagy *közreműködői* hozzáféréssel a társított Azure-előfizetéshez, akkor egy engedélyezési hiba jelenhet meg. Például:

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

A probléma megoldásához az Azure-előfizetéshez *tulajdonosi* vagy *közreműködői* hozzáféréssel rendelkező fiók használatával manuálisan regisztrálja az `Microsoft.DevSpaces` névteret:

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Az új hüvelyek nem indulnak el

A Kubernetes inicializáló nem tudja alkalmazni a PodSpec az új hüvelyek esetében, mert a fürtben a *fürt-rendszergazdai* szerepkör RBAC engedélyei változnak. Az új Pod is rendelkezhet érvénytelen PodSpec, például a pod-hoz társított szolgáltatásfiók már nem létezik. Az inicializálási probléma miatt *függőben* lévő állapotban lévő hüvelyek megtekintéséhez használja a `kubectl get pods` parancsot:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Ez a probléma a fürt *összes névterében* hatással lehet a hüvelyekre, beleértve azokat a névtereket is, amelyekben nincs engedélyezve az Azure dev Spaces.

A probléma megoldásához [frissítse a dev Spaces CLI-t a legújabb verzióra](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) , majd törölje a *Azds InitializerConfiguration* az Azure dev Spaces-vezérlőből:

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Miután eltávolította a azds- *InitializerConfiguration* az Azure dev Spaces-vezérlőből, a `kubectl delete` használatával távolítsa el a *függő* állapotú hüvelyeket. Miután az összes függőben lévő hüvely el lett távolítva, telepítse újra a hüvelyeket.

Ha az új hüvelyek továbbra is *függőben* vannak egy újratelepítést követően, a `kubectl delete` használatával távolíthatja el a *függő* állapotú hüvelyeket. Miután az összes függőben lévő hüvely el lett távolítva, törölje a vezérlőt a fürtből, és telepítse újra:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

A vezérlő újratelepítése után telepítse újra a hüvelyeket.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Helytelen RBAC engedélyek a dev Spaces-vezérlő és API-k hívásához

Az Azure dev Spaces-vezérlőhöz hozzáférő felhasználónak hozzáféréssel kell rendelkeznie az AK-fürt rendszergazdai *kubeconfig* olvasásához. Ez az engedély például a [beépített Azure Kubernetes szolgáltatás-fürt rendszergazdai szerepkörében](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions)érhető el. Az Azure dev Spaces-vezérlőhöz hozzáférő felhasználónak a vezérlő *közreműködői* vagy *tulajdonosi* RBAC szerepkörével is rendelkeznie kell. A felhasználó egy AK-fürtre vonatkozó engedélyeinek frissítéséről [itt talál](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)további információt.

A felhasználó RBAC szerepkörének frissítése a vezérlőhöz:

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
1. Navigáljon a vezérlőt tartalmazó erőforráscsoporthoz, amely általában ugyanaz, mint az AK-fürt.
1. Engedélyezze a *rejtett típusok megjelenítése* jelölőnégyzetet.
1. Kattintson a vezérlőre.
1. Nyissa meg a *Access Control (iam)* ablaktáblát.
1. Kattintson a *szerepkör-hozzárendelések* fülre.
1. Kattintson a *Hozzáadás* , majd a *szerepkör-hozzárendelés hozzáadása*lehetőségre.
    * A *szerepkör*területen válassza a *közreműködő* vagy a *tulajdonos*lehetőséget.
    * A *hozzáférésének hozzárendeléséhez*válassza az *Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév*lehetőséget.
    * A *Select (kiválasztás*) lehetőségnél keresse meg azt a felhasználót, akinek engedélyeket szeretne adni.
1. Kattintson a *Save* (Mentés) gombra.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>DNS-névfeloldás nem sikerül, egy fejlesztői tárolóhelyek szolgáltatással társított nyilvános URL-cím

A szolgáltatáshoz egy nyilvános URL-végpontot is beállíthat, ha megadja a `--enable-ingress` kapcsolót a `azds prep` parancsra, vagy ha a Visual Studióban a `Publicly Accessible` jelölőnégyzetet választja. A nyilvános DNS-nevet a rendszer automatikusan regisztrálja, amikor a szolgáltatást a fejlesztői tárhelyeken futtatja. Ha ez a DNS-név nincs regisztrálva, akkor egy *lap nem jeleníthető meg* , vagy a webböngészőben *nem érhető el* hibaüzenet, ha a nyilvános URL-címhez csatlakozik.

A probléma megoldása:

* Győződjön meg arról, hogy a dev Spaces szolgáltatáshoz társított összes URL-cím állapota:

  ```console
  azds list-uris
  ```

* Ha egy URL-cím *függő* állapotban van, a dev Spaces továbbra is vár a DNS-regisztráció befejeződésére. Egyes esetekben regisztráció befejezése néhány percet vesz igénybe. Fejlesztői szóközöket is megnyílik a localhost alagút minden egyes szolgáltatás, amellyel DNS-regisztráció történő várakozás során.
* Ha egy URL-cím 5 percnél hosszabb ideig marad a *függőben* lévő állapotban, akkor a külső DNS-Pod-mel kapcsolatos problémát jelezhet, amely létrehozza a nyilvános végpontot vagy az Nginx bemenő vezérlő Pod-t, amely a nyilvános végpontot szerzi be. A következő parancsokkal törölheti ezeket a hüvelyeket, és engedélyezheti, hogy az AK automatikusan újra létrehozza őket:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Hiba: "felsőbb rétegbeli kapcsolódási hiba vagy leválasztás/visszaállítás a fejlécek előtt"

Ez a hiba jelenhet meg a szolgáltatás elérésére tett kísérlet során. Például ha, nyissa meg a szolgáltatás URL-címét egy böngészőben. Ez a hiba azt jelenti, hogy a tároló portja nem érhető el. Ez a következő okok miatt lehetséges:

* A tároló továbbra is a beépített és a telepített folyamatban van. Ez a probléma akkor fordulhat elő, ha `azds up` futtat, vagy elindítja a hibakeresőt, majd megpróbál hozzáférni a tárolóhoz a sikeres üzembe helyezése előtt.
* A port konfigurációja nem konzisztens a _Docker_, a Helm diagramon és bármely olyan kiszolgáló kódján, amely egy portot nyit meg.

A probléma megoldása:

1. Ha a tároló folyamatban létrehozott és üzembe helyezése folyamatban van, várjon 2-3 másodpercet, és próbálkozzon újra a szolgáltatás elérésével. 
1. A port konfigurációjának ellenőrzését a következő eszközökön találja:
    * **[Helm-diagram](https://docs.helm.sh):** A `service.port` és `deployment.containerPort` értékekben adja meg a Values. YAML `azds prep` paranccsal.
    * Az alkalmazás kódjában megnyitott portok, például a Node. js-ben: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Nem található a következő típus vagy névtér neve: "MyLibrary".

Az Ön által használt függvénytár-projekt nem található. A fejlesztői szóközökkel a Build környezet alapértelmezés szerint a projekt/szolgáltatás szintjén van.  

A probléma megoldása:

1. Módosítsa a `azds.yaml` fájlt úgy, hogy a felépítési környezetet a megoldás szintjére állítsa.
2. Módosítsa a `Dockerfile` és `Dockerfile.develop` fájlokat úgy, hogy azok a projektfájlok, például `.csproj`, megfelelően az új Build-környezethez képest legyenek átirányítva.
3. Vegyen fel egy `.dockerignore`t a `.sln` fájllal megegyező könyvtárba.
4. Szükség szerint frissítse a `.dockerignore` további bejegyzésekkel.

[Itt](https://github.com/sgreenmsft/buildcontextsample)talál egy példát.

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>A horizontális Pod automatikus skálázás nem működik a fejlesztői térben

Ha egy fejlesztési térben futtat egy szolgáltatást, a szolgáltatás Pod-je [további tárolókkal van befecskendezve](how-dev-spaces-works.md#prepare-your-aks-cluster) , és a pod összes tárolójának erőforrás-korláttal és kérelmekkel kell rendelkeznie a vízszintes Pod automatikus skálázáshoz.

A probléma megoldásához alkalmazzon egy erőforrás-kérelmet, és korlátozza a befecskendezett dev Spaces-tárolókat. A befecskendezett tárolóhoz (devspaces) az erőforrás-kérelmeket és a korlátozásokat a `azds.io/proxy-resources` jegyzetnek a pod spechez való hozzáadásával lehet alkalmazni. Az értéket egy olyan JSON-objektumra kell beállítani, amely a proxyhoz tartozó tároló specifikációjának erőforrások szakaszát jelképezi.

Alább látható egy példa arra a proxy-erőforrások jegyzetre, amelyet alkalmazni kell a pod spec-ra.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Az Azure dev Spaces használatának engedélyezése meglévő névterek futtatásával

Lehet, hogy van egy meglévő AK-fürt és-névtér, amelyen fut a hüvely, ahol engedélyezni szeretné az Azure dev Spaces szolgáltatást.

Ha engedélyezni szeretné az Azure dev Spaces-t egy meglévő névtérben egy AK-fürtben, futtassa `use-dev-spaces` és a `kubectl` használatával indítsa újra az összes hüvelyt a névtérben.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

A hüvely újraindítása után megkezdheti a meglévő névtér használatát az Azure dev Spaces használatával.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Az Azure dev-szóközök engedélyezése az AK-fürtökön a fürt csomópontjainak korlátozott kimenő forgalmával

Ha engedélyezni szeretné az Azure dev Spaces szolgáltatást egy AK-fürtön, amelynél a fürtcsomópontok kimenő forgalmának korlátozása korlátozott, akkor engedélyeznie kell a következő teljes tartományneveket:

| TELJES TARTOMÁNYNÉV                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | A Linux Alpine és más Azure dev Spaces-lemezképek lekérése |
| gcr.io | HTTP: 443 | A Helm/Tiller-lemezképek lekérése|
| storage.googleapis.com | HTTP: 443 | A Helm/Tiller-lemezképek lekérése|
| azds –<guid>.<location>. azds.io | HTTPS:443 | Kommunikáció az Azure dev Spaces háttér-szolgáltatásaival a vezérlőhöz. A pontos FQDN a (z) "dataplaneFqdn" elemben található a következőben:% felhasználói név%\.azds\settings.JSON|

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Hiba: "nem található a fürt \<a fürt\> az előfizetésben \<subscriptionId\>"

Ez a hiba akkor fordulhat elő, ha a kubeconfig-fájl egy másik fürtöt vagy előfizetést céloz meg, mint amennyit az Azure dev Spaces ügyféloldali eszközeivel szeretne használni. Az Azure dev Spaces ügyféloldali eszközkészlete replikálja a *kubectl*viselkedését, amely [egy vagy több kubeconfig-fájlt](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) használ a fürt kiválasztásához és az azokkal való kommunikációhoz.

A probléma megoldása:

* Az aktuális környezet frissítéséhez használja a `az aks use-dev-spaces -g <resource group name> -n <cluster name>`. Ez a parancs azt is lehetővé teszi, hogy az Azure dev-helyek az AK-fürtön legyenek, ha még nincs engedélyezve. Azt is megteheti, hogy a `kubectl config use-context <cluster name>` segítségével frissíti az aktuális környezetet.
* A `az account show` használatával jelenítse meg az aktuálisan megcélzott Azure-előfizetést, és ellenőrizze, hogy helyes-e. Megváltoztathatja `az account set`használatával megcélzott előfizetést.

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Hiba történt a fejlesztői szóközök használatával az AK-tanúsítványok elforgatása után

Miután [elforgatta a tanúsítványokat az AK-fürtben](../aks/certificate-rotation.md), bizonyos műveletek, például a `azds space list` és a `azds up` sikertelenek lesznek. Emellett frissítenie kell a tanúsítványokat az Azure dev Spaces-vezérlőn a fürtön lévő tanúsítványok elforgatása után.

A probléma megoldásához ellenőrizze, hogy a *kubeconfig* rendelkezik-e a frissített tanúsítványokkal `az aks get-credentials` majd futtassa a `azds controller refresh-credentials` parancsot. Például:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
