---
title: Hibaelhárítás |} Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Gyors Kubernetes fejlesztése a tárolók és mikroszolgáltatások létrehozására az Azure-on
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók
manager: douge
ms.openlocfilehash: 371bb9195266f3511d115de2532e6b64f49ef26f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

Ez az útmutató előfordulhat, hogy Azure fejlesztői tárolóhelyek használatakor gyakori problémák információkat tartalmaz.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Hiba történt "előtt csatlakozási hiba vagy megszakítása/reset fejlécek előtt"
Ez a hiba jelenhet meg a szolgáltatás elérésére tett kísérlet során. Például ha átvált a szolgáltatás URL-címet egy böngészőben. 

### <a name="reason"></a>Ok 
A tároló port nem érhető el. Ennek oka lehet: 
* A tároló még a beépített és a telepítése folyamatban van. Ez lehet az esetben, ha futtatja `azds up` vagy indítsa el a hibakereső, és próbálkozzon újra a számítógépet a tároló elérésére, mielőtt sikeresen alkalmazva van.
* Port konfigurálása nincs összhangban a Dockerfile, Helm diagram és egy portot nyit összes kiszolgáló kódot.

### <a name="try"></a>Próbálja meg:
1. Ha a tároló beépített/telepítése folyamatban van, várjon 2-3 másodpercet, és próbálja újból megnyitni a szolgáltatást. 
1. Ellenőrizze a port konfigurációját. A megadott kell esniük **azonos** az összes, az az alábbi eszközöket:
    * **Dockerfile:** által meghatározott a `EXPOSE` utasítás.
    * **[Helm diagram](https://docs.helm.sh):** által meghatározott a `externalPort` és `internalPort` értékek szolgáltatáshoz (gyakran található egy `values.yml` fájl),
    * Bármely alkalmazás kódjában, például a Node.js-ben az éppen megnyitott portok: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Konfigurációs fájl nem található
Futtatása `azds up` és a következő hibaüzenet: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Ok
Futtatnia kell a `azds up` a kód gyökérkönyvtárából futtatni szeretné, és a kód mappa Azure fejlesztői szóközök futtatni kell inicializálni.

### <a name="try"></a>Próbálja meg:
1. A jelenlegi címtárral módosítsa a legfelső szintű mappát, amely tartalmazza a szolgáltatáskód hibáit. 
1. Ha egy azds.yaml fájl nem rendelkezik a kód mappában, futtassa `azds prep` Docker Kubernetes és Azure fejlesztői szóközöket eszközök létrehozásához.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Hiba: "a cső program váratlanul kilépett, hibakód 126 azds."
A Visual STUDIO Code hibakereső indítása néha vonhat ezt a hibát. Ez az egy ismert probléma.

### <a name="try"></a>Próbálja meg:
1. Zárja be és nyissa meg újra a VS kódot.
2. Ismét kattintson az F5 billentyűt.


## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Hiba a "konfigurált debug"coreclr"adattípusa nem támogatott" Hibakeresés
A Visual STUDIO Code hibakereső futtató jelenti a hibát: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Ok
A Visual STUDIO Code bővítmény Azure fejlesztői szóközöket a fejlesztői gépen telepítve van a nem rendelkeznek.

### <a name="try"></a>Próbálja meg:
Telepítse a [Azure fejlesztői szóközöket a Visual STUDIO Code bővítmény](get-started-netcore.md).

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>A típus vagy névtér nevének "SajátLista" nem található.

### <a name="reason"></a>Ok 
A build környezet alapértelmezés szerint a projekt/szolgáltatás szintjén van, ezért a hordozhatóosztálytár-projektjének használata nem található.

### <a name="try"></a>Próbálja meg:
Mit kell tenni:
1. Módosítsa az azds.yaml fájlt az összeállítási környezet beállítása a megoldás szintre.
2. Módosítsa a Dockerfile és Dockerfile.develop fájlt tekintse meg a csproj fájlt helyesen, az új összeállítási környezet viszonyítva.
3. Helyezze a .dockerignore fájlt mellett az .sln-fájlt, és szükség szerint módosítsa.

A példa található https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftconnectedenvironmentregisteraction-authorization-error"></a>"Microsoft.ConnectedEnvironment/register/action" hitelesítési hiba
Kezeli az Azure fejlesztői címtér és Azure-előfizetéssel, amelynek nincs tulajdonosa vagy Közreműködője hozzáférés használata a következő hiba jelenhet meg.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.ConnectedEnvironment/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Ok
A kijelölt Azure-előfizetés nincs regisztrálva van a Microsoft.ConnectedEnvironment névtér.

### <a name="try"></a>Próbálja meg:
Az Azure-előfizetés tulajdonosának vagy Közreműködőjének hozzáféréssel rendelkező személy is a következő parancsot az Azure parancssori felület manuális regisztrálásához a Microsoft.ConnectedEnvironment névtér:

```cmd
az provider register --namespace Microsoft.ConnectedEnvironment
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Az Azure fejlesztői szóközöket úgy tűnik, hogy a meglévő Dockerfile használni egy tároló létrehozásához 

### <a name="reason"></a>Ok
Az Azure fejlesztői szóközöket beállítható úgy, hogy a projekt egy adott Dockerfile mutasson. Ha úgy tűnik, hogy Azure fejlesztői szóközöket nem a várt hozhat létre a tárolók Dockerfile használ, szükség lehet explicit módon állapítható meg, hogy Azure fejlesztői szóközöket, ahol van. 

### <a name="try"></a>Próbálja meg:
Nyissa meg a `azds.yaml` fájlt, amely a projekt lett létrehozva, amikor Azure fejlesztői szóközöket. Használja a `configurations->develop->build->dockerfile` irányelv mutasson a Dockerfile szeretné használni:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```