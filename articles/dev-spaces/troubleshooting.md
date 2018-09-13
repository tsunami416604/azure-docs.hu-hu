---
title: Hibaelhárítás |} A Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 09/11/2018
ms.topic: article
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók
manager: douge
ms.openlocfilehash: c6ca3003c1338f3e057c76d9e04d8b0cbd2210c7
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44721194"
---
# <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

Ez az útmutató az Azure fejlesztési tárolóhelyek használata esetén előfordulhat, hogy rendelkezik kapcsolatos gyakori problémák információkat tartalmaz.

## <a name="enabling-detailed-logging"></a>Részletes naplózás engedélyezése

Annak érdekében, hogy a problémák hatékonyabb elhárításához, segíthet, tekintse át a részletes naplók létrehozása.

A Visual Studio-bővítmény beállítása a `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` környezeti változót az 1. Ügyeljen arra, hogy a Visual Studio for a környezeti változó érvénybe léptetéséhez indítsa újra. Az engedélyezés után a részletes naplók lesz írva a `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` könyvtár.

A parancssori felületen, a további információk a parancs végrehajtása során küldhetnek kimenetet a `--verbose` váltani.

## <a name="debugging-services-with-multiple-instances"></a>Több példányával szolgáltatások hibakeresésekor

Jelenleg az Azure fejlesztési tárolóhelyek támogatja a csak egy példányban (pod) a hibakeresés. A azds.yaml fájl tartalmaz egy beállítást, replicaCount, amely azt jelzi, amely a szolgáltatás-példányok száma. Ha megváltoztatja az adott szolgáltatás több példányának futtatása, az alkalmazás konfigurálása replicaCount, a hibakereső viselkedését nem várt módon.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>"Nem sikerült létrehozni az Azure fejlesztési tárolóhelyek vezérlő" hiba

Előfordulhat, hogy ezt a hibaüzenetet, ha probléma merül fel a vezérlő létrehozása. Ha ez egy átmeneti hiba, törlését és újbóli létrehozását a vezérlő kijavítja azt.

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
    
## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>DNS-névfeloldás nem sikerül, egy fejlesztői tárolóhelyek szolgáltatással társított nyilvános URL-cím

Ha a DNS-névfeloldás nem sikerül, megjelenhet egy "A lap nem jeleníthető meg." vagy "ezen a helyen nem érhető el" hiba a böngészőben, amikor egy fejlesztési tárolóhelyek szolgáltatás próbál csatlakozni a nyilvános URL-címhez társított.

### <a name="try"></a>Próbálja ki:

Használhatja ki a fejlesztői, szóközök szolgáltatásokkal kapcsolatos összes URL-címek listája az alábbi parancsot:

```cmd
azds list-uris
```

Ha egy URL-cím a *függőben lévő* állapot, amely azt jelenti, hogy fejlesztői, szóközök még mindig várakozik DNS-regisztráció végrehajtásához. Egyes esetekben regisztráció befejezése néhány percet vesz igénybe. Fejlesztői szóközöket is megnyílik a localhost alagút minden egyes szolgáltatás, amellyel DNS-regisztráció történő várakozás során.

Ha egy URL-cím marad a *függőben lévő* több mint 5 perc, a külső DNS-pod, amely a nyilvános végpontot hoz létre és/vagy nginx bejövő tartományvezérlő-pod szerez be a nyilvános végponthoz problémájára utalhat. állapotát. A következő parancsokat használhatja ezeket a podok törlése. Ezek létrejönnek, automatikusan.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>"Szükséges az eszközök és konfigurációk hiányoznak" hiba

Ez a hiba akkor fordulhat elő, a VS Code elindításához: "[Azure fejlesztési tárolóhelyek] szükséges eszközök és konfigurációk fejlesztése és hibakeresése a"[projekt neve]"hiányoznak."
A hiba azt jelenti, hogy adott azds.exe nem szerepel a PATH környezeti változóhoz, a VS Code-ban látható módon.

### <a name="try"></a>Próbálja ki:

Indítsa el a VS Code a parancssorból, ahol a PATH környezeti változóhoz helyesen van beállítva.

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

Továbbra is használhatja az Azure fejlesztési tárolóhelyek más nyelven írt kóddal, de hozza létre a docker-fájl futtatása előtt kell *mentése azds* először.

### <a name="try"></a>Próbálja ki:
Ha az alkalmazás, hogy az Azure fejlesztési tárolóhelyek nem támogatja natív módon nyelven íródott, adjon meg egy megfelelő docker-fájlban, állítson össze egy tárolórendszerképet a kódja fut. szüksége. Docker biztosít egy [ajánlott eljárások a docker-fájlok írása listája](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) , valamint egy [Dockerfile referencia](https://docs.docker.com/engine/reference/builder/) , amellyel az igényeinek megfelelő, docker-fájlt írni.

Miután már működik a megfelelő docker-fájlban, folytathatja, futtató *mentése azds* az alkalmazás futtatásához az Azure fejlesztési szóközöket.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Hiba történt "felső csatlakozási hiba vagy leválasztása/alaphelyzetbe állítása előtt fejlécek"
Ez a hiba jelenhet meg a szolgáltatás elérésére tett kísérlet során. Például ha, nyissa meg a szolgáltatás URL-címét egy böngészőben. 

### <a name="reason"></a>Ok 
A tároló port nem érhető el. Ez a hiba akkor fordulhat elő, mert: 
* A tároló továbbra is a beépített és a telepített folyamatban van. A probléma akkor merülhet fel, ha futtatja `azds up` vagy indítsa el a hibakeresőt, és próbálja meg elérni a tárolót előtt sikeresen alkalmazva van.
* Port konfigurálása még nem egységes megjelenés a _Dockerfile_, Helm-diagramot, és bármely kiszolgálói kód, amely megnyit egy portot.

### <a name="try"></a>Próbálja ki:
1. Ha a tároló folyamatban létrehozott és üzembe helyezése folyamatban van, várjon 2-3 másodpercet, és próbálkozzon újra a szolgáltatás elérésével. 
1. Ellenőrizze a port konfigurációját. A megadott portszámot kell **azonos** az összes, az az alábbi eszközöket:
    * **Docker-fájlban:** által meghatározott a `EXPOSE` utasítást.
    * **[Helm-diagram](https://docs.helm.sh):** által meghatározott a `externalPort` és `internalPort` értékek szolgáltatáshoz (gyakran található egy `values.yml` fájl),
    * Az alkalmazás kódjában, például a node.js-ben az éppen megnyitott portokat: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Konfigurációs fájl nem található
Futtatása `azds up` és a következő hibaüzenet: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Ok
Kell futtatásakor `azds up` a kód gyökérkönyvtárából szeretné futtatni, és kell inicializálnia a kód mappa futtatásához az Azure fejlesztési szóközöket.

### <a name="try"></a>Próbálja ki:
1. Módosítsa az aktuális könyvtár a legfelső szintű mappát, amely tartalmazza a szolgáltatás kódot. 
1. Ha nem rendelkezik egy _azds.yaml_ futtatása kód mappában található fájl `azds prep` Docker, Kubernetes- és Azure-fejlesztési tárolóhelyek eszközök létrehozásához.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Hiba: "a függőleges vonal program váratlanul kilépett, hibakód 126 azds."
A VS Code hibakereső indítása előfordulhat, hogy időnként ezt a hibát eredményez.

### <a name="try"></a>Próbálja ki:
1. Zárja be és nyissa meg újra a VS Code.
2. Nyomja le az F5 újra.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Hibakeresés a "Sikertelen hibakereső bővítmény típusa: coreclr található" hiba
A VS Code hibakereső futó jelentést a hiba: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Ok
Nem rendelkezik a VS Code-bővítmény használata a C# a fejlesztői gépen telepítve van. A C#-bővítményt tartalmazza a hibakeresés támogatása a .net Core (CoreCLR).

### <a name="try"></a>Próbálja ki:
Telepítse a [VS Code-bővítmény használata a C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>A "konfigurált hibakeresési típus"coreclr"nem támogatott" hiba hibakeresés
A VS Code hibakereső futó jelentést a hiba: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Ok
A VS Code-bővítmény nem rendelkezik az Azure fejlesztési szóközöket a fejlesztői gépen telepítve van.

### <a name="try"></a>Próbálja ki:
Telepítse a [VS Code-bővítmény Azure fejlesztési tárolóhelyek](get-started-netcore.md).

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
A következő hiba jelenhet meg, kezeli az Azure-fejlesztési terület, és az Azure-előfizetéssel, amelyhez nem rendelkezik tulajdonosi vagy közreműködői hozzáférés dolgozik.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Ok
A kiválasztott Azure-előfizetés nincs regisztrálva a `Microsoft.DevSpaces` névtér.

### <a name="try"></a>Próbálja ki:
Az Azure-előfizetés-tulajdonosi vagy közreműködői hozzáféréssel rendelkező személy futtathatja manuálisan kell regisztrálni a következő Azure CLI-parancsot a `Microsoft.DevSpaces` névteret:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Az Azure fejlesztési szóközt nem a Microsoft saját meglévő docker-fájl használatával hozhat létre egy tárolót 

### <a name="reason"></a>Ok
Az Azure fejlesztési tárolóhelyek beállítható úgy, hogy egy adott pontjára _Dockerfile_ a projektben. Ha úgy tűnik, az Azure fejlesztési szóközt nem használ a _Dockerfile_ várt hozhat létre a tárolókat, előfordulhat, hogy ossza meg Azure fejlesztési tárolóhelyek, ahol a szolgáltatás explicit módon kell. 

### <a name="try"></a>Próbálja ki:
Nyissa meg a _azds.yaml_ fájlt, amely az Azure fejlesztési tárolóhelyek jött létre a projektben. Használja a `configurations->develop->build->dockerfile` irányelv átirányítása a docker-fájlban használni kívánt:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```
