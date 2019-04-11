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
keywords: 'Docker, Kubernetes, Azure, az AKS, az Azure Kubernetes Service, tárolók, Helm, a szolgáltatás háló, a szolgáltatás háló útválasztás, a kubectl, a k8s '
ms.openlocfilehash: b205f7782dc14c9108032d2b4a274f884194874e
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357860"
---
# <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

Ez az útmutató az Azure fejlesztési tárolóhelyek használata esetén előfordulhat, hogy rendelkezik kapcsolatos gyakori problémák információkat tartalmaz.

Ha a probléma az Azure fejlesztési tárolóhelyek használata esetén, hozzon létre egy [probléma az Azure fejlesztési tárolóhelyek GitHub-adattárában](https://github.com/Azure/dev-spaces/issues).

## <a name="enabling-detailed-logging"></a>Részletes naplózás engedélyezése

Problémák hatékonyabb elhárításához, segíthet, tekintse át a részletes naplók létrehozása.

A Visual Studio-bővítmény beállítása a `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` környezeti változót az 1. Ügyeljen arra, hogy a Visual Studio for a környezeti változó érvénybe léptetéséhez indítsa újra. Az engedélyezés után a részletes naplók írt a `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` könyvtár.

A parancssori felületen, a további információk a parancs végrehajtása során küldhetnek kimenetet a `--verbose` váltani. A részletes naplókat is megkeresheti `%TEMP%\Azure Dev Spaces`. Egy Mac gépen a TEMP könyvtárában található futtatásával `echo $TMPDIR` egy terminálablakból. Egy Linux-számítógépek esetében a TEMP könyvtárában nem általában `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Több példányával szolgáltatások hibakeresésekor

Jelenleg az Azure fejlesztői, szóközök works hibakeresése egyetlen példány esetén ajánlott, vagy a pod. A azds.yaml fájl tartalmaz egy beállítást *replicaCount*, amely azt jelzi, hogy a Kubernetes a szolgáltatás futtató podok számát. Ha módosítja a replicaCount az alkalmazás futtatásához az adott szolgáltatás több podok konfigurálása, a hibakereső csatolja az első pod, amikor ábécérendbe. A hibakereső csatolja egy másik pod amikor újraindul az eredeti pod, valószínűleg nem várt viselkedést eredményez.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>"Nem sikerült létrehozni az Azure fejlesztési tárolóhelyek vezérlő" hiba

Előfordulhat, hogy ezt a hibaüzenetet, ha probléma merül fel a vezérlő létrehozása. Ha ez egy átmeneti hiba, törölje és hozza létre újra a tartományvezérlőt a javítást.

### <a name="try"></a>Próbálja ki:

A vezérlő törléséhez használja az Azure fejlesztési tárolóhelyek CLI. Nem alkalmas a Visual Studio vagy a Cloud Shellben. A AZDS parancssori felületének telepítéséhez, először telepítse az Azure CLI-vel, és futtassa ezt a parancsot:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

És futtassa ezt a parancsot a vezérlő törlése:

```cmd
azds remove -g <resource group name> -n <cluster name>
```

A vezérlő újbóli létrehozása a parancssori felület vagy a Visual Studio elvégezhető. Kövesse az utasításokat az oktatóanyagok, mint ha első alkalommal indítása.


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
Kap egy *szolgáltatás nem indítható* hiba többlépcsős docker-fájl használata esetén. Ebben a helyzetben a részletes kimenet tartalmazza a következő szöveget:

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

Ez a hiba akkor fordul elő, mivel egy régebbi verziója, amely nem támogatja a többlépcsős Docker futtatása AKS-csomópontok. Többlépcsős buildek elkerülése érdekében írja újra a docker-fájlban.

### <a name="rerunning-a-service-after-controller-re-creation"></a>A szolgáltatás ismételt vezérlő újbóli létrehozása után
Kap egy *szolgáltatás nem indítható* hiba, majd újra létrehozza a fürthöz társított Azure-fejlesztési tárolóhelyek vezérlő és eltávolítása után futtassa újból a szolgáltatás megkísérlésekor. Ebben a helyzetben a részletes kimenet tartalmazza a következő szöveget:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Ez a hiba oka, hogy a fejlesztői, szóközök eltávolítására nem távolítja el, hogy a tartományvezérlő által korábban telepített szolgáltatások. A vezérlő újbóli létrehozását, és ezután megpróbálja futtatni a szolgáltatásokat az új vezérlőt használó meghiúsul, mert a régi szolgáltatások helyükön vannak.

A probléma megoldása érdekében használja a `kubectl delete` parancs használatával manuálisan a régi szolgáltatások eltávolítása a fürtből, majd futtassa újra a fejlesztési szóközöket a új szolgáltatások telepítése.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>DNS-névfeloldás nem sikerül, egy fejlesztői tárolóhelyek szolgáltatással társított nyilvános URL-cím

Konfigurálhatja egy nyilvános URL-végpontot a szolgáltatáshoz adja meg a `--public` váltson át a `azds prep` parancsot, vagy a válassza a `Publicly Accessible` jelölőnégyzetet a Visual Studióban. A nyilvános DNS-név automatikusan regisztrálva van, a szolgáltatás a fejlesztési tárolóhelyek futtatásakor. Ha a DNS-neve nincs regisztrálva, megjelenik egy *lap nem jeleníthető meg* vagy *hely nem érhető el* hiba a böngészőben, amikor csatlakozik a nyilvános URL-CÍMÉT.

### <a name="try"></a>Próbálja ki:

Használhatja ki a fejlesztői, szóközök szolgáltatásokkal kapcsolatos összes URL-címek listája az alábbi parancsot:

```cmd
azds list-uris
```

Ha egy URL-cím a *függőben lévő* állapot, amely azt jelenti, hogy fejlesztői, szóközök még mindig várakozik DNS-regisztráció végrehajtásához. Egyes esetekben regisztráció befejezése néhány percet vesz igénybe. Fejlesztői szóközöket is megnyílik a localhost alagút minden egyes szolgáltatás, amellyel DNS-regisztráció történő várakozás során.

Ha egy URL-cím marad a *függőben lévő* 5 percnél hosszabb ideig állapot azt jelezheti, hogy a probléma a külső DNS-pod, amely a nyilvános végpontot hoz létre vagy nginx bejövő tartományvezérlő-pod szerez be a nyilvános végponthoz. A következő parancsokat használhatja ezeket a podok törlése. Az AKS automatikusan létrehozza a törölt podokat.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>"Szükséges az eszközök és konfigurációk hiányoznak" hiba

Ez a hiba akkor fordulhat elő, a VS Code elindításához: "[Azure fejlesztési tárolóhelyek] szükséges eszközök és konfigurációk fejlesztése és hibakeresése a"[projekt neve]"hiányoznak."
A hiba azt jelenti, hogy adott azds.exe nem szerepel a PATH környezeti változóhoz, a VS Code-ban látható módon.

### <a name="try"></a>Próbálja ki:

Indítsa el a VS Code a parancssorból, ahol a PATH környezeti változóhoz helyesen van beállítva.

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Hiba történt "fejlesztése és hibakeresése"projectname"szükséges eszközök elavultak."

Ezt a hibaüzenetet a Visual Studio Code Ha rendelkezik egy újabb verziója a VS Code-bővítmény az Azure fejlesztési tárolóhelyek, de az Azure fejlesztési tárolóhelyek CLI egy régebbi verzióját.

### <a name="try"></a>Kipróbálás

Töltse le és telepítse az Azure fejlesztési tárolóhelyek CLI legújabb verzióját:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>"Azds" hiba történt egy belső vagy külső parancs, működtethető program vagy parancsfájl nem ismerhető fel
 
Ez a hiba megjelenhet, ha azds.exe nincs telepítve vagy megfelelően konfigurálva.

### <a name="try"></a>Próbálja ki:

1. Ellenőrizze a azds.exe hely %ProgramFiles%/Microsoft SDKs\Azure\Azure fejlesztési tárolóhelyek CLI (előzetes verzió). Ha nincs, adott helyen hozzáadása a PATH környezeti változóhoz.
2. Ha azds.exe nincs telepítve, futtassa a következő parancsot:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Figyelmeztetés: a docker-fájl nem hozható létre, miatt nem támogatott nyelvet"
Az Azure fejlesztési tárolóhelyek C# és Node.js natív támogatást biztosít. Futtatásakor *azds előkészítő* egy ezeken a nyelveken írt kódot tartalmazó könyvtárban, Azure fejlesztési tárolóhelyek automatikusan létrehoz egy megfelelő docker-fájl az Ön számára.

Továbbra is használhatja az Azure fejlesztési tárolóhelyek más nyelven írt kóddal, de azt manuálisan kell létrehozni a docker-fájl futtatása előtt *mentése azds* először.

### <a name="try"></a>Próbálja ki:
Ha az alkalmazás, hogy az Azure fejlesztési tárolóhelyek nem támogatja natív módon nyelven íródott, meg kell adnia a megfelelő docker-fájlban, állítson össze egy tárolórendszerképet a kódja fut. Docker biztosít egy [ajánlott eljárások a docker-fájlok írása listája](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) és a egy [Dockerfile referencia](https://docs.docker.com/engine/reference/builder/) , amellyel az igényeinek megfelelő, docker-fájlt írni.

Miután már működik a megfelelő docker-fájlban, folytathatja, futtató *mentése azds* az alkalmazás futtatásához az Azure fejlesztési szóközöket.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Hiba történt "felső csatlakozási hiba vagy leválasztása/alaphelyzetbe állítása előtt fejlécek"
Ez a hiba jelenhet meg a szolgáltatás elérésére tett kísérlet során. Például ha, nyissa meg a szolgáltatás URL-címét egy böngészőben. 

### <a name="reason"></a>Ok 
A tároló port nem érhető el. Ez a hiba akkor fordulhat elő, mert: 
* A tároló továbbra is a beépített és a telepített folyamatban van. A probléma akkor merülhet fel, ha futtatja `azds up` vagy indítsa el a hibakeresőt, és próbálja meg elérni a tárolót előtt sikeresen alkalmazva van.
* Port konfigurálása még nem egységes megjelenés a _Dockerfile_, Helm-diagramot, és bármely kiszolgálói kód, amely megnyit egy portot.

### <a name="try"></a>Próbálja ki:
1. Ha a tároló folyamatban létrehozott és üzembe helyezése folyamatban van, várjon 2-3 másodpercet, és próbálkozzon újra a szolgáltatás elérésével. 
1. Ellenőrizze a port konfigurációját. A megadott portszámot kell **azonos** az összes az alábbi eszközöket:
    * **Docker-fájlban:** Azokat a `EXPOSE` utasítást.
    * **[Helm-diagram](https://docs.helm.sh):** Azokat a `externalPort` és `internalPort` értékek szolgáltatáshoz (gyakran található egy `values.yml` fájl),
    * Az alkalmazás kódjában, például a node.js-ben az éppen megnyitott portokat: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Konfigurációs fájl nem található
Futtatása `azds up` és a következő hibaüzenet: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Ok
Kell futtatásakor `azds up` a kód gyökérkönyvtárából szeretné futtatni, és kell inicializálnia a kód mappa futtatásához az Azure fejlesztési szóközöket.

### <a name="try"></a>Próbálja ki:
1. Módosítsa az aktuális könyvtár a legfelső szintű mappát, amely tartalmazza a szolgáltatás kódot. 
1. Ha nem rendelkezik egy _azds.yaml_ futtatása kód mappában található fájl `azds prep` Docker, Kubernetes- és Azure-fejlesztési tárolóhelyek eszközök létrehozásához.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Hiba: "A függőleges vonal program"azds"váratlanul kilépett, hibakód 126."
A VS Code hibakereső indítása előfordulhat, hogy időnként ezt a hibát eredményez.

### <a name="try"></a>Próbálja ki:
1. Zárja be és nyissa meg újra a VS Code.
2. Nyomja le az F5 újra.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Hibakeresés a "Sikertelen hibakereső bővítmény típusa: coreclr található" hiba
A VS Code hibakereső futó jelentést a hiba: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Ok
Nem rendelkezik a VS Code-bővítmény használata a C# a fejlesztői gépen telepítve van. A C# bővítményt tartalmaz, hibakeresési támogatást a .NET Core (CoreCLR).

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

### <a name="reason"></a>Ok 
A build-környezet van alapértelmezés szerint a projekt/szolgáltatási szinten, ezért használata hordozhatóosztálytár-projektjének nem található.

### <a name="try"></a>Próbálja ki:
Mit kell tenni:
1. Módosítsa a _azds.yaml_ fájlt a build-környezet beállítása a megoldás szintre.
2. Módosítsa a _Dockerfile_ és _Dockerfile.develop_ tekintse meg a projekt-fájlok (_.csproj_) fájlok megfelelően képest az új hozhat létre környezetet.
3. Hely egy _.dockerignore_ fájlt mellett az .sln-fájlt, és szükség szerint módosítsa.

Talál példát: https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>"Microsoft.DevSpaces/register/action" engedélyezési hiba
Szükséges *tulajdonosa* vagy *közreműködői* hozzáférés az Azure fejlesztési tárolóhelyek kezeléséhez az Azure-előfizetés. Előfordulhat, hogy ezt a hibaüzenetet, ha szeretne fejlesztői tárolóhelyek kezeléséhez, és nem rendelkezik *tulajdonosa* vagy *közreműködői* a kapcsolódó Azure-előfizetéshez való hozzáférés.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Ok
A kiválasztott Azure-előfizetés nincs regisztrálva a `Microsoft.DevSpaces` névtér.

### <a name="try"></a>Próbálja ki:
Az Azure-előfizetés-tulajdonosi vagy közreműködői hozzáféréssel rendelkező személy futtathatja manuálisan kell regisztrálni a következő Azure CLI-parancsot a `Microsoft.DevSpaces` névteret:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>A fejlesztői, szóközök túllépi az időkorlátot *Várakozás a tároló-rendszerkép összeállítását...*  az AKS-csomópontok virtuális lépés

### <a name="reason"></a>Ok
Ez az időtúllépési akkor fordul elő, amikor megpróbálja futtatni egy szolgáltatás, amely konfigurálva van Futtatás fejlesztési tárolóhelyek használata egy [AKS virtuális csomópont](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Fejlesztői, szóközök jelenleg nem támogatja az létrehozásához, vagy a virtuális csomópontok szolgáltatások hibakeresésében.

Ha `azds up` az a `--verbose` kapcsoló vagy engedélyezése részletes naplózás a Visual Studióban, további részletek megtekintéséhez:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

A fenti parancs bemutatja, hogy a szolgáltatás pod hozzá lett rendelve *virtuális-csomópont-aci-linux*, amely egy virtuális csomópont.

### <a name="try"></a>Próbálja ki:
A Helm-diagramot, távolítsa el a szolgáltatás frissítése *nodeSelector* és/vagy *tolerations* értékek, amelyek lehetővé teszik a szolgáltatást egy virtuális csomópont futtathatók. Ezeket az értékeket a diagram általában meghatározott `values.yaml` fájlt.

AKS-fürt, amely rendelkezik a virtuális csomópontok szolgáltatás engedélyezve van, ha a fejlesztői, szóközök keresztül szeretné build/debug szolgáltatás fut a virtuális gép csomópont továbbra is használhatja. Ez az alapértelmezett beállítás.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>"Hiba: nem található készen áll a tiller valóban podot" fejlesztői, szóközök indításakor

### <a name="reason"></a>Ok
Ez a hiba akkor fordul elő, ha a Helm-ügyfél már nem képes kommunikálni a tiller valóban pod a fürtben futó.

### <a name="try"></a>Próbálja ki:
Az ügynökcsomópontok általában a fürtben lévő újraindítás megszünteti a ezt a problémát.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Az Azure fejlesztői, szóközök proxy zavaró más fejlesztői szóközzel futtató podok

### <a name="reason"></a>Ok
Az AKS-fürt az egy névtérhez engedélyezi fejlesztési szóközt, egy kiegészítő tároló hívják _mindaro-proxy_ telepítve van a podok névtéren belül futó minden. Ez a tároló elfogja a pod, amely integrált fejlesztési tárolóhelyek csapat a fejlesztési funkciók; szolgáltatásban rendszertevékenységét azonban bizonyos ezeket a podok futó szolgáltatásokat. Azure Cache Redis fut, a csatlakozási hibát okoz az elsődleges és tartalék kiszolgálók közötti kommunikáció podok zavarja ismert.

### <a name="try"></a>Próbálja ki:
Egy névtér, amely a fürtön belül viheti át az érintett podok _nem_ fejlesztési tárolóhelyek engedélyezve van. Az alkalmazás többi továbbra is egy fejlesztési tárolóhelyek-kompatibilis névterek futtatható. Fejlesztői, szóközök nem telepíti a _mindaro-proxy_ tároló nem fejlesztési tárolóhelyek belül engedélyezett névterek.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Az Azure fejlesztési szóközt nem a Microsoft saját meglévő docker-fájl használatával hozhat létre egy tárolót

### <a name="reason"></a>Ok
Az Azure fejlesztési tárolóhelyek beállítható úgy, hogy egy adott pontjára _Dockerfile_ a projektben. Ha úgy tűnik, az Azure fejlesztési szóközt nem használ a _Dockerfile_ várhatóan a tárolókat hozhat létre, szüksége lehet, hogy melyik docker-fájlban használni explicit módon az Azure fejlesztési tárolóhelyek. 

### <a name="try"></a>Próbálja ki:
Nyissa meg a _azds.yaml_ fájlt, hogy az Azure fejlesztési szóközöket a projekt jön létre. Használja a *konfigurációk -> Fejlesztés -> build dockerfile ->* irányelv átirányítása a docker-fájlban használni kívánt:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

## <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Hiba történt "nem sikerült belső watch: ENOSPC megtekintése" Hibakeresés a Node.js-alkalmazás csatlakoztatása

### <a name="reason"></a>Ok

A pod a hibakeresőt a csatlakoztatni kívánt Node.js-alkalmazással futó a csomópont processzorhasználata túllépte a *fs.inotify.max_user_watches* értéket. Bizonyos esetekben [az alapértelmezett érték *fs.inotify.max_user_watches* esetleg túl kicsi, kezelni a hibakeresést közvetlenül egy pod](https://github.com/Azure/AKS/issues/772).

### <a name="try"></a>Kipróbálás
A probléma ideiglenes megoldás az, hogy az értékét növelje *fs.inotify.max_user_watches* a fürt minden csomópontján és a csomópont újraindításához, a módosítások érvénybe léptetéséhez.
