---
title: Hibaelhárítás |} A Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók
manager: douge
ms.openlocfilehash: c5224422c88690ccf063a27b053963ebde622a1e
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172102"
---
# <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

Ez az útmutató az Azure fejlesztési tárolóhelyek használata esetén előfordulhat, hogy rendelkezik kapcsolatos gyakori problémák információkat tartalmaz.

## <a name="error-service-cannot-be-started"></a>Hiba történt "szolgáltatás nem indítható."

Előfordulhat, hogy ezt a hibaüzenetet, ha a szolgáltatás kód nem indul el. Oka általában a felhasználói kódban. További diagnosztikai adatok beszerzéséhez hajtsa végre a következő módosításokat a parancsok és beállítások:

A parancssorban:

Használata esetén _azds.exe_, használja a--részletes parancssori beállítást, és használja az--output parancssori kapcsoló adja meg a kimeneti formátum.
 
    ```cmd
    azds up --verbose --output json
    ```

A Visual Studióban:

1. Nyissa meg **eszközök > Beállítások** és **projektek és megoldások**, válassza ki és **létrehozásához és futtatásához**.
2. Módosítsa a beállításokat a **MSBuild project build kimeneti részletességi** való **részletes** vagy **diagnosztikai**.

    ![Képernyőkép a beállítások panel](media/common/VerbositySetting.PNG)

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
A VS Code hibakereső indítása előfordulhat, hogy időnként ezt a hibát eredményez. Ez egy ismert probléma.

### <a name="try"></a>Próbálja ki:
1. Zárja be és nyissa meg újra a VS Code.
2. Nyomja le az F5 újra.


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