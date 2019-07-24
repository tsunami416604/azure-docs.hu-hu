---
title: Hibaelhárítás
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: conceptual
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: 'Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s '
ms.openlocfilehash: 2434507ac89d631bb96ae9633403075801879a37
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277406"
---
# <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

Ez az útmutató az Azure fejlesztési tárolóhelyek használata esetén előfordulhat, hogy rendelkezik kapcsolatos gyakori problémák információkat tartalmaz.

Ha problémába ütközik az Azure dev Spaces használata során, hozzon létre egy [problémát az Azure dev Spaces GitHub-tárházban](https://github.com/Azure/dev-spaces/issues).

## <a name="enabling-detailed-logging"></a>Részletes naplózás engedélyezése

Ha hatékonyabban szeretné elhárítani a problémákat, érdemes lehet részletesebb naplókat létrehozni a felülvizsgálathoz.

A Visual Studio-bővítmény beállítása a `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` környezeti változót az 1. Ügyeljen arra, hogy a Visual Studio for a környezeti változó érvénybe léptetéséhez indítsa újra. Ha engedélyezve van, a részletes naplók a `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` címtárba íródnak.

A parancssori felületen, a további információk a parancs végrehajtása során küldhetnek kimenetet a `--verbose` váltani. A részletes naplókat is megkeresheti `%TEMP%\Azure Dev Spaces`. Egy Mac gépen a TEMP könyvtárában található futtatásával `echo $TMPDIR` egy terminálablakból. Egy Linux-számítógépek esetében a TEMP könyvtárában nem általában `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Több példányával szolgáltatások hibakeresésekor

Jelenleg az Azure dev Spaces a legmegfelelőbben működik egyetlen példány vagy Pod hibakereséséhez. A azds. YAML fájl egy olyan beállítást tartalmaz , amely a Kubernetes által futtatott hüvelyek számát jelzi. Ha úgy módosítja a replicaCount, hogy úgy konfigurálja az alkalmazást, hogy több hüvelyt futtasson egy adott szolgáltatáshoz, akkor a hibakereső az első hüvelyhez csatlakozik, amikor betűrendbe van sorolva. A hibakereső egy másik Pod-hoz csatlakozik, amikor az eredeti Pod újrahasznosítja, ami valószínűleg váratlan viselkedést eredményez.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>"Nem sikerült létrehozni az Azure fejlesztési tárolóhelyek vezérlő" hiba

### <a name="reason"></a>Reason
Előfordulhat, hogy ezt a hibaüzenetet, ha probléma merül fel a vezérlő létrehozása. Ha átmeneti hiba történt, törölje, majd hozza létre újra a vezérlőt a javításhoz.

### <a name="try"></a>Kipróbálás

A vezérlő törlése:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Vezérlő törléséhez az Azure dev Spaces CLI-t kell használnia. A vezérlőt nem lehet törölni a Visual studióból. Nem telepítheti az Azure dev Spaces CLI-t a Azure Cloud Shell, így nem törölhet vezérlőt a Azure Cloud Shellból.

Ha nem telepítette az Azure dev Spaces CLI-t, először telepítse azt a következő parancs használatával, majd törölje a vezérlőt:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

A vezérlő újbóli létrehozása a parancssori felület vagy a Visual Studio elvégezhető. Példákért tekintse meg a [csapat fejlesztését](quickstart-team-development.md) , vagy fejlesszen a [.net Core](quickstart-netcore-visualstudio.md) gyors útmutatókkal.

## <a name="error-service-cannot-be-started"></a>Hiba történt "szolgáltatás nem indítható."

Előfordulhat, hogy ezt a hibaüzenetet, ha a szolgáltatás kód nem indul el. Oka általában a felhasználói kódban. További diagnosztikai adatok beszerzéséhez hajtsa végre a következő módosításokat a parancsok és beállítások:

### <a name="try"></a>Próbálja ki:

A parancssorban:

Használata esetén _azds.exe_, használja a--részletes parancssori beállítást, és használja az--output parancssori kapcsoló adja meg a kimeneti formátum.
 
```cmd
azds up --verbose --output json
```

A Visual Studióban:

1. Nyissa meg **eszközök > Beállítások** és **projektek és megoldások**, válassza a **létrehozásához és futtatásához**.
2. Módosítsa a beállításokat a **MSBuild project build kimeneti részletességi** való **részletes** vagy **diagnosztikai**.

    ![Képernyőkép a beállítások panel](media/common/VerbositySetting.PNG)
    
### <a name="multi-stage-dockerfiles"></a>Többlépcsős docker-fájlok:
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

Ez a hiba azért fordul elő, mert az AK-csomópontok a Docker egy régebbi verzióját futtatják, amely nem támogatja a többfázisú buildeket. A többfázisú buildek elkerüléséhez írja át a Docker.

### <a name="rerunning-a-service-after-controller-re-creation"></a>Szolgáltatás újrafuttatása a vezérlő ismételt létrehozása után
A szolgáltatás *nem indítható el* , ha a szolgáltatás újrafuttatására tett kísérletet követően eltávolította a szolgáltatást, majd újból létrehozta az ehhez a fürthöz társított Azure dev Spaces-vezérlőt. Ebben az esetben a részletes kimenet a következő szöveget tartalmazza:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Ez a hiba azért fordul elő, mert a dev Spaces vezérlő eltávolítása nem távolítja el a vezérlő által korábban telepített szolgáltatásokat. A vezérlő újbóli létrehozását, és ezután megpróbálja futtatni a szolgáltatásokat az új vezérlőt használó meghiúsul, mert a régi szolgáltatások helyükön vannak.

A probléma megoldásához a `kubectl delete` paranccsal távolítsa el manuálisan a régi szolgáltatásokat a fürtből, majd futtassa újra a dev Spaces szolgáltatást az új szolgáltatások telepítéséhez.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>DNS-névfeloldás nem sikerül, egy fejlesztői tárolóhelyek szolgáltatással társított nyilvános URL-cím

A szolgáltatáshoz egy nyilvános URL-végpontot is beállíthat, ha megadja `--public` a kapcsolót `azds prep` a parancsra, vagy kiválasztja `Publicly Accessible` a jelölőnégyzetet a Visual Studióban. A nyilvános DNS-nevet a rendszer automatikusan regisztrálja, amikor a szolgáltatást a fejlesztői tárhelyeken futtatja. Ha ez a DNS-név nincs regisztrálva, akkor egy *lap nem jeleníthető meg* , vagy a webböngészőben *nem érhető el* hibaüzenet, ha a nyilvános URL-címhez csatlakozik.

### <a name="try"></a>Próbálja ki:

Használhatja ki a fejlesztői, szóközök szolgáltatásokkal kapcsolatos összes URL-címek listája az alábbi parancsot:

```cmd
azds list-uris
```

Ha egy URL-cím a *függőben lévő* állapot, amely azt jelenti, hogy fejlesztői, szóközök még mindig várakozik DNS-regisztráció végrehajtásához. Egyes esetekben regisztráció befejezése néhány percet vesz igénybe. Fejlesztői szóközöket is megnyílik a localhost alagút minden egyes szolgáltatás, amellyel DNS-regisztráció történő várakozás során.

Ha egy URL-cím 5  percnél hosszabb ideig marad a függőben lévő állapotban, akkor a külső DNS-Pod-mel kapcsolatos problémát jelezhet, amely létrehozza a nyilvános végpontot vagy az Nginx bemenő vezérlő Pod-t, amely a nyilvános végpontot szerzi be. A következő parancsokat használhatja ezeket a podok törlése. Az AK automatikusan újra létrehozza a törölt hüvelyeket.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>"Szükséges az eszközök és konfigurációk hiányoznak" hiba

Ez a hiba akkor fordulhat elő, a VS Code elindításához: "[Azure fejlesztési tárolóhelyek] szükséges eszközök és konfigurációk fejlesztése és hibakeresése a"[projekt neve]"hiányoznak."
A hiba azt jelenti, hogy adott azds.exe nem szerepel a PATH környezeti változóhoz, a VS Code-ban látható módon.

### <a name="try"></a>Próbálja ki:

Indítsa el a VS Code a parancssorból, ahol a PATH környezeti változóhoz helyesen van beállítva.

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Hiba: a "projektnév" létrehozásához és hibakereséséhez szükséges eszközök elavultak. "

Ez a hibaüzenet akkor jelenik meg a Visual Studio Code-ban, ha az Azure dev Spaces-hez a VS Code bővítmény újabb verziója van telepítve, de az Azure dev Spaces CLI egy régebbi verziója.

### <a name="try"></a>Kipróbálás

Töltse le és telepítse az Azure dev Spaces CLI legújabb verzióját:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>"Azds" hiba történt egy belső vagy külső parancs, működtethető program vagy parancsfájl nem ismerhető fel
 
Ez a hiba megjelenhet, ha azds.exe nincs telepítve vagy megfelelően konfigurálva.

### <a name="try"></a>Próbálja ki:

1. Keresse meg a (z)% ProgramFiles%/Microsoft SDKs\Azure\Azure dev Spaces CLI-t a azds. exe fájlhoz. Ha nincs, adott helyen hozzáadása a PATH környezeti változóhoz.
2. Ha azds.exe nincs telepítve, futtassa a következő parancsot:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Figyelmeztetés: a docker-fájl nem hozható létre, miatt nem támogatott nyelvet"
Az Azure fejlesztési tárolóhelyek C# és Node.js natív támogatást biztosít. Futtatásakor *azds előkészítő* egy ezeken a nyelveken írt kódot tartalmazó könyvtárban, Azure fejlesztési tárolóhelyek automatikusan létrehoz egy megfelelő docker-fájl az Ön számára.

Továbbra is használhatja az Azure dev Spaces szolgáltatást más nyelveken írt kóddal, de a *azds* első futtatása előtt manuálisan kell létrehoznia a Docker.

### <a name="try"></a>Próbálja ki:
Ha az alkalmazás olyan nyelven íródott, amelyet az Azure dev Spaces nem támogat natív módon, meg kell adnia egy megfelelő Docker a kódot futtató tároló-rendszerkép létrehozásához. A Docker egy [listát biztosít a Dockerfiles írásához ajánlott eljárásokról](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) és egy olyan [Docker](https://docs.docker.com/engine/reference/builder/) -hivatkozásról, amely segíthet az igényeinek megfelelő Docker írására.

Miután már működik a megfelelő docker-fájlban, folytathatja, futtató *mentése azds* az alkalmazás futtatásához az Azure fejlesztési szóközöket.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Hiba történt "felső csatlakozási hiba vagy leválasztása/alaphelyzetbe állítása előtt fejlécek"
Ez a hiba jelenhet meg a szolgáltatás elérésére tett kísérlet során. Például ha, nyissa meg a szolgáltatás URL-címét egy böngészőben. 

### <a name="reason"></a>Ok 
A tároló port nem érhető el. Ez a hiba akkor fordulhat elő, mert: 
* A tároló továbbra is a beépített és a telepített folyamatban van. A probléma akkor merülhet fel, ha futtatja `azds up` vagy indítsa el a hibakeresőt, és próbálja meg elérni a tárolót előtt sikeresen alkalmazva van.
* Port konfigurálása még nem egységes megjelenés a _Dockerfile_, Helm-diagramot, és bármely kiszolgálói kód, amely megnyit egy portot.

### <a name="try"></a>Próbálja ki:
1. Ha a tároló folyamatban létrehozott és üzembe helyezése folyamatban van, várjon 2-3 másodpercet, és próbálkozzon újra a szolgáltatás elérésével. 
1. Ellenőrizze a port konfigurációját. A megadott portszámoknak meg kell **egyezniük** az összes alábbi eszközön:
    * **Docker** Az `EXPOSE` utasítás adja meg.
    * **[Helm-diagram](https://docs.helm.sh):** Egy szolgáltatás és `externalPort` `internalPort` a (gyakran egy `values.yml` fájlban található) értéke határozza meg,
    * Az alkalmazás kódjában, például a node.js-ben az éppen megnyitott portokat: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Konfigurációs fájl nem található
Futtatása `azds up` és a következő hibaüzenet: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Ok
Kell futtatásakor `azds up` a kód gyökérkönyvtárából szeretné futtatni, és kell inicializálnia a kód mappa futtatásához az Azure fejlesztési szóközöket.

### <a name="try"></a>Próbálja ki:
1. Módosítsa az aktuális könyvtár a legfelső szintű mappát, amely tartalmazza a szolgáltatás kódot. 
1. Ha nem rendelkezik egy _azds.yaml_ futtatása kód mappában található fájl `azds prep` Docker, Kubernetes- és Azure-fejlesztési tárolóhelyek eszközök létrehozásához.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Hiba: "A" azds "pipe program váratlanul kilépett a 126-es kóddal."
A VS Code hibakereső indítása előfordulhat, hogy időnként ezt a hibát eredményez.

### <a name="try"></a>Próbálja ki:
1. Zárja be és nyissa meg újra a VS Code.
2. Nyomja le az F5 újra.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Hibakeresés a "Sikertelen hibakereső bővítmény típusa: coreclr található" hiba
A VS Code hibakereső futó jelentést a hiba: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Ok
Nem rendelkezik a VS Code-bővítmény használata a C# a fejlesztői gépen telepítve van. A C# bővítmény a .net Core (CoreCLR) hibakeresési támogatását is tartalmazza.

### <a name="try"></a>Próbálja ki:
Telepítse a [VS Code-bővítmény használata a C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>A "konfigurált hibakeresési típus"coreclr"nem támogatott" hiba hibakeresés
A VS Code hibakereső futó jelentést a hiba: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Ok
A VS Code-bővítmény nem rendelkezik az Azure fejlesztési szóközöket a fejlesztői gépen telepítve van.

### <a name="try"></a>Próbálja ki:
Telepítse a [VS Code-bővítmény Azure fejlesztési tárolóhelyek](get-started-netcore.md).

## <a name="debugging-error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Hibakeresés hiba "érvénytelen"cwd"érték" / src ". A rendszer nem találja a megadott fájlt." vagy "indítása: program"/ src / [projekt bináris elérési út]"nem létezik."
A hiba a VS Code hibakereső futó jelentések `Invalid 'cwd' value '/src'. The system cannot find the file specified.` és/vagy `launch: program '/src/[path to project executable]' does not exist`

### <a name="reason"></a>Ok
Alapértelmezés szerint a VS Code-bővítménnyel használja `src` , a munkakönyvtárban a projekt a tárolón. Ha már frissítette a `Dockerfile` szeretne megadni egy másik működő könyvtárba, előfordulhat, hogy ezt a hibaüzenetet.

### <a name="try"></a>Próbálja ki:
Frissítés a `launch.json` fájlt a `.vscode` alkönyvtára a projektmappába. Módosítsa a `configurations->cwd` irányelv könyvtárába átirányítása a `WORKDIR` a projektben meghatározott `Dockerfile`. Szükség lehet frissíteni a `configurations->program` irányelvnek is.

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>A típus vagy névtér nevének "SajátLista" nem található.

### <a name="reason"></a>Reason 
A létrehozási környezet alapértelmezés szerint a projekt/szolgáltatás szintjén van, ezért nem található egy használt függvénytár-projekt.

### <a name="try"></a>Próbálja ki:
Mit kell tenni:
1. Módosítsa a _azds.yaml_ fájlt a build-környezet beállítása a megoldás szintre.
2. Módosítsa a _Dockerfile_ és _Dockerfile.develop_ tekintse meg a projekt-fájlok ( _.csproj_) fájlok megfelelően képest az új hozhat létre környezetet.
3. Hely egy _.dockerignore_ fájlt mellett az .sln-fájlt, és szükség szerint módosítsa.

Talál példát: https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>"Microsoft.DevSpaces/register/action" engedélyezési hiba
Az Azure  fejlesztői tárhelyek kezeléséhez tulajdonosi vagy *közreműködői* hozzáférésre van szüksége az Azure-előfizetésben. Ez a hiba akkor fordulhat elő, ha a fejlesztői helyeket szeretné kezelni, és nem rendelkezik *tulajdonosi* vagy *közreműködői* hozzáféréssel a társított Azure-előfizetéshez.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Reason
A kiválasztott Azure-előfizetés nincs regisztrálva a `Microsoft.DevSpaces` névtér.

### <a name="try"></a>Próbálja ki:
Az Azure-előfizetés-tulajdonosi vagy közreműködői hozzáféréssel rendelkező személy futtathatja manuálisan kell regisztrálni a következő Azure CLI-parancsot a `Microsoft.DevSpaces` névteret:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>A fejlesztői tárolóhelyek időtúllépéssel *várnak a tároló rendszerképének összeállítására...* lépés AK virtuális csomópontokkal

### <a name="reason"></a>Reason
Ez az időkorlát akkor fordul elő, ha a fejlesztői szóközök használatával futtat olyan szolgáltatást, amely egy AK-beli [virtuális csomóponton](https://docs.microsoft.com/azure/aks/virtual-nodes-portal)való futtatásra van konfigurálva. A dev Spaces jelenleg nem támogatja a virtuális csomópontokon futó szolgáltatások létrehozását vagy hibakeresését.

Ha a kapcsolóval `azds up` fut `--verbose` , vagy engedélyezi a részletes naplózást a Visual Studióban, további részleteket láthat:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

A fenti parancs azt mutatja, hogy a szolgáltatás Pod hozzá lett rendelve a *virtuális csomópont-ACI-Linux*rendszerhez, amely egy virtuális csomópont.

### <a name="try"></a>Próbálja ki:
Frissítse a szolgáltatás Helm diagramját, hogy eltávolítsa az összes olyan *nodeSelector* és/vagy *tolerancia* értéket, amely lehetővé teszi, hogy a szolgáltatás virtuális csomóponton fusson. Ezek az értékek általában a diagram `values.yaml` fájljában vannak meghatározva.

Továbbra is használhat olyan AK-fürtöt, amelyen engedélyezve van a virtuális csomópontok funkciója, ha a fejlesztés/hibakeresés a fejlesztői tárhelyeken keresztül egy virtuálisgép-csomóponton fut. Ez az alapértelmezett konfiguráció.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>"Hiba: nem található készen áll a tiller valóban podot" fejlesztői, szóközök indításakor

### <a name="reason"></a>Ok
Ez a hiba akkor fordul elő, ha a Helm-ügyfél már nem képes kommunikálni a tiller valóban pod a fürtben futó.

### <a name="try"></a>Próbálja ki:
Az ügynökcsomópontok általában a fürtben lévő újraindítás megszünteti a ezt a problémát.

## <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>"Hiba: a kiadás azds\<-\>Identifier-\>\<spacename-szolgáltatásnévsikertelen:\<szolgáltatások szolgáltatásnév\> \<\>a "már létezik" vagy a "pull- \<hozzáférés\>megtagadva szolgáltatásnév számára", a tárház nem létezik, vagy szükség lehet a "Docker bejelentkezhessen"

### <a name="reason"></a>Reason
Ezek a hibák akkor fordulhatnak `helm install`elő, ha a (például,, vagy `azds up` ) `helm upgrade`rendszerű `helm delete`Direct Helm-parancsokat (például, vagy) `azds down`futtatja a dev Spaces parancsokkal (például és). Ezek azért történnek, mert a dev Spaces saját kormányrúd-példánnyal rendelkezik, ami ütközik a saját, azonos fejlesztői térben futó példányával.

### <a name="try"></a>Próbálja ki:
A Helm-parancsok és a dev Spaces-parancsok is használhatók ugyanazon az AK-fürtön, de az egyes fejlesztői szóközöket használó névtereknek vagy az egyiket kell használniuk.

Tegyük fel például, hogy egy Helm parancs használatával futtatja a teljes alkalmazást egy szülő fejlesztői térben. A gyermek fejlesztési területeit kikapcsolhatja a szülőtől, a fejlesztői szóközök használatával egyéni szolgáltatásokat futtathat a gyermek-fejlesztési területeken belül, és tesztelheti a szolgáltatásokat. Ha készen áll a módosítások beadására, a Helm parancs használatával telepítse a frissített kódot a szülő fejlesztői területére. A nem `azds up` használható a frissített szolgáltatás futtatására a szülő fejlesztői térben, mert az ütközik a Helm használatával kezdetben futtatott szolgáltatással.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Az Azure fejlesztői, szóközök proxy zavaró más fejlesztői szóközzel futtató podok

### <a name="reason"></a>Ok
Az AKS-fürt az egy névtérhez engedélyezi fejlesztési szóközt, egy kiegészítő tároló hívják _mindaro-proxy_ telepítve van a podok névtéren belül futó minden. Ez a tároló elfogja a pod-ban található szolgáltatások felé irányuló hívásokat, amely a fejlesztői Spaces fejlesztői képességeinek szerves részét képezi. azonban az ilyen hüvelyekben futó egyes szolgáltatásokkal is zavarhatja. Ismert, hogy zavarja az Azure cache-t futtató hüvelyeket a Redis, ami kapcsolódási hibákat és hibákat okoz az elsődleges/másodlagos kommunikációban.

### <a name="try"></a>Próbálja ki:
Az érintett hüvelyeket áthelyezheti a fürtön belül olyan névtérbe, amelynek _nincs_ engedélyezve a dev Spaces szolgáltatás. Az alkalmazás többi része továbbra is futhat egy dev Spaces-kompatibilis névtéren belül. Fejlesztői, szóközök nem telepíti a _mindaro-proxy_ tároló nem fejlesztési tárolóhelyek belül engedélyezett névterek.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Az Azure fejlesztési szóközt nem a Microsoft saját meglévő docker-fájl használatával hozhat létre egy tárolót

### <a name="reason"></a>Ok
Az Azure fejlesztési tárolóhelyek beállítható úgy, hogy egy adott pontjára _Dockerfile_ a projektben. Ha úgy tűnik, hogy az Azure dev Spaces nem a tárolók felépítésére várt _Docker_ használja, akkor előfordulhat, hogy explicit módon meg kell adnia az Azure dev Spaces-Docker, amelyeket használni kíván. 

### <a name="try"></a>Próbálja ki:
Nyissa meg az _azds. YAML_ fájlt, amelyet az Azure dev Spaces generált a projektben. Használja a *konfigurációk-> fejlesztés-> Build-> Docker* direktívát, hogy a használni kívánt Docker mutasson:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

## <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Hiba "a belső figyelés sikertelen: ENOSPC megtekintése" egy Node. js-alkalmazás hibakeresésének csatolásakor

### <a name="reason"></a>Reason

A-hibakeresőt futtató Node. js-alkalmazással rendelkező csomópont túllépte az *FS. inotify. max _user_watches* értéket. Bizonyos esetekben előfordulhat, [hogy az *FS. inotify. max _user_watches* alapértelmezett értéke túl kicsi ahhoz, hogy a hibakeresőt közvetlenül egy Pod-](https://github.com/Azure/AKS/issues/772)hez csatolja.

### <a name="try"></a>Kipróbálás
A probléma ideiglenes megkerülő megoldásként növelje az *FS. inotify. max _user_watches* értékét a fürt mindegyik csomópontján, majd indítsa újra a csomópontot a módosítások érvénybe léptetéséhez.

## <a name="new-pods-are-not-starting"></a>Az új hüvelyek nem indulnak el

### <a name="reason"></a>Reason

A Kubernetes inicializáló nem tudja alkalmazni a PodSpec az új hüvelyek esetében, mert a fürtben a *fürt-rendszergazdai* szerepkör RBAC engedélyei változnak. Az új Pod is rendelkezhet érvénytelen PodSpec, például a pod-hoz társított szolgáltatásfiók már nem létezik. Az inicializálási probléma miatt függőben lévő  állapotban lévő hüvelyek megtekintéséhez használja az `kubectl get pods` parancsot:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Ez a probléma a fürt *összes névterében* hatással lehet a hüvelyekre, beleértve azokat a névtereket is, amelyekben nincs engedélyezve az Azure dev Spaces.

### <a name="try"></a>Kipróbálás

[Frissítse a dev Spaces CLI-t a legújabb verzióra](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) , majd törölje a *Azds InitializerConfiguration* az Azure dev Spaces vezérlőből:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Miután eltávolította a azds- *InitializerConfiguration* az Azure dev Spaces-vezérlőből, `kubectl delete` a használatával távolítsa el a *függő* állapotú összes hüvelyt. Miután az összes függőben lévő hüvely el lett távolítva, telepítse újra a hüvelyeket.

Ha az új hüvelyek továbbra is *függőben* vannak egy újratelepítést követően `kubectl delete` , a használatával eltávolíthatja a *függőben* lévő állapotú hüvelyeket. Miután az összes függőben lévő hüvely el lett távolítva, törölje a vezérlőt a fürtből, és telepítse újra:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

A vezérlő újratelepítése után telepítse újra a hüvelyeket.

## <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Helytelen RBAC engedélyek a dev Spaces-vezérlő és API-k hívásához

### <a name="reason"></a>Reason
Az Azure dev Spaces-vezérlőhöz hozzáférő felhasználónak hozzáféréssel kell rendelkeznie az AK-fürt rendszergazdai *kubeconfig* olvasásához. Ez az engedély például a [beépített Azure Kubernetes szolgáltatás-fürt rendszergazdai szerepkörében](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions)érhető el. Az Azure dev Spaces-vezérlőhöz hozzáférő felhasználónak a vezérlő *közreműködői* vagy *tulajdonosi* RBAC szerepkörével is rendelkeznie kell.

### <a name="try"></a>Kipróbálás
A felhasználó egy AK-fürtre vonatkozó engedélyeinek frissítéséről [itt talál](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)további információt.

A felhasználó RBAC szerepkörének frissítése a vezérlőhöz:

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
1. Navigáljon a vezérlőt tartalmazó erőforráscsoporthoz, amely általában ugyanaz, mint az AK-fürt.
1. Engedélyezze a *rejtett típusok megjelenítése* jelölőnégyzetet.
1. Kattintson a vezérlőre.
1. Nyissa meg a *Access Control (iam)* ablaktáblát.
1. Kattintson a *szerepkör* -hozzárendelések fülre.
1. Kattintson a *Hozzáadás* , majd a *szerepkör-hozzárendelés hozzáadása*lehetőségre.
    * A *szerepkörhöz* válassza a *közreműködő* vagy a *tulajdonos*lehetőséget.
    * A *hozzáférés hozzárendeléséhez* válassza az *Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév*lehetőséget.
    * A *válassza ki* az engedélyt adni kívánt felhasználó keresését.
1. Kattintson a *Save* (Mentés) gombra.

## <a name="controller-create-failing-due-to-controller-name-length"></a>A vezérlő létrehozása sikertelen a vezérlő nevének hossza miatt

### <a name="reason"></a>Reason
Az Azure dev Spaces-vezérlő neve nem lehet hosszabb 31 karakternél. Ha a vezérlő neve meghaladja a 31 karaktert, amikor egy AK-fürtön engedélyezi a dev Spaces-t, vagy létrehoz egy vezérlőt, a következőhöz hasonló hibaüzenetet kap:

*Nem sikerült létrehozni a dev Spaces-vezérlőt a (z) "a-Controller-Name" nevű fürthöz: Érvénytelen az Azure dev Spaces-vezérlő neve: "a-Controller-Name-this-is-Way-túl hosszú – AK-Kelet – USA". Megsértett korlátozás (ok): Az Azure dev Spaces-vezérlő neve legfeljebb 31 karakter hosszúságú lehet*

### <a name="try"></a>Kipróbálás

Hozzon létre egy másik nevű vezérlőt:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

## <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>A fejlesztői szóközök engedélyezése a Windows-csomópontok AK-fürthöz való hozzáadásakor

### <a name="reason"></a>Reason
Jelenleg az Azure dev Spaces kizárólag Linux-hüvelyeken és-csomópontokon futtatható. Ha egy AK-fürthöz Windows-csomópont tartozik, akkor biztosítania kell, hogy az Azure dev Spaces hüvelyek csak Linux-csomópontokon legyenek ütemezve. Ha egy Azure dev Spaces Pod Windows-csomóponton való futtatásra van ütemezve, akkor a pod nem indul el, és a fejlesztői szóközök engedélyezése sikertelen lesz.

### <a name="try"></a>Kipróbálás
A Linux-hüvelyek Windows-csomóponton való futtatásának biztosítása érdekében [adjon hozzá egy Taint](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) az AK-fürthöz.

## <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Hiba: nem található a fürtön kész állapotú, nem fertőzött Linux-csomópontok. A hüvelyek "azds" névtérben való üzembe helyezéséhez kész állapotban kell lennie legalább egy nem fertőzött linuxos csomópontnak. "

### <a name="reason"></a>Reason

Az Azure dev-helyek nem tudták létrehozni a vezérlőt az AK-fürtön, mert nem talált egy nem fertőzött csomópontot *készenléti* állapotban a hüvelyek ütemezett beléptetéséhez. Az Azure dev Spaces-ben legalább egy Linux-csomópontra *kész* állapotban kell lennie, amely lehetővé teszi a hüvelyek ütemezését a tolerancia megadása nélkül.

### <a name="try"></a>Kipróbálás
[Frissítse a szennyező konfigurációt](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) az AK-fürtön annak biztosításához, hogy legalább egy Linux-csomópont lehetővé tegye a hüvelyek ütemezését a tolerancia megadása nélkül. Győződjön meg arról is, hogy legalább egy Linux-csomópont, amely lehetővé teszi a hüvelyek ütemezését, a tolerálás nélkül *kész* állapotban van. Ha a csomópont hosszú időt vesz elérhetővé a *kész* állapot elérése érdekében, próbálja meg újraindítani a csomópontot.

## <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Hiba: "az Azure dev Spaces CLI nincs megfelelően telepítve", ha fut`az aks use-dev-spaces`

### <a name="reason"></a>Reason
Az Azure dev Spaces parancssori felületének frissítése megváltoztatta a telepítési útvonalat. Ha a 2.0.63-nél korábbi Azure CLI-verziót használja, akkor ez a hiba látható. Az Azure CLI verziójának megjelenítéséhez használja `az --version`a következőt:.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

Annak ellenére, hogy a 2.0.63 `az aks use-dev-spaces` előtt az Azure CLI egy verziójával fut, a telepítés sikeres lesz. Továbbra is problémák `azds` nélkül használhatja tovább.

### <a name="try"></a>Kipróbálás
Frissítse az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) telepítését 2.0.63 vagy újabb verzióra. Ezzel a megoldással a futtatásakor `az aks use-dev-spaces`megjelenő hibaüzenetet fogja feloldani. Másik lehetőségként továbbra is használhatja az Azure CLI aktuális verzióját és az Azure dev Spaces CLI-t.


## <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>A horizontális Pod automatikus skálázás nem működik a fejlesztői térben

### <a name="reason"></a>Reason

Amikor egy szolgáltatást egy fejlesztői térben futtat, a szolgáltatás Pod-je [további tárolókkal](how-dev-spaces-works.md#prepare-your-aks-cluster)lesz befecskendezve a rendszerállapothoz. Ezek a tárolók nem rendelkeznek erőforrás-kérelmekkel vagy-korlátozásokkal, ami azt eredményezi, hogy a vízszintes Pod automéretező le lesz tiltva a pod számára.

### <a name="try"></a>Kipróbálás
Futtassa a vízszintes Pod automéretezőt olyan névtérben, amelyben nincs engedélyezve a dev Spaces.
