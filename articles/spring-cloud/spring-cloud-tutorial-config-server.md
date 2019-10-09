---
title: A konfigurációs kiszolgáló beállítása az Azure Spring Cloud-ban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthat be egy Spring Cloud config-kiszolgálót az Azure Spring Cloud-hoz a Azure Portal
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 08/08/2019
ms.openlocfilehash: f959d216859ded54de52de474ee3150f91bcfff1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038922"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>Oktatóanyag: Rugós Felhőbeli konfigurációs kiszolgáló beállítása a szolgáltatáshoz

Ez az oktatóanyag bemutatja, hogyan csatlakozhat egy Spring Cloud config-kiszolgálót az Azure Spring Cloud Service-hez.

A Spring Cloud config kiszolgáló-és ügyféloldali támogatást nyújt a külső konfigurációhoz egy elosztott rendszeren. A konfigurációs kiszolgálóval központi helyen kezelheti az alkalmazások külső tulajdonságait az összes környezetben. További tudnivalókért tekintse meg a [Spring Cloud config Server-referenciát](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 
* Már üzembe lett helyezve, és futtatja az Azure Spring Cloud Service-t.  Fejezze be [ezt](spring-cloud-quickstart-launch-app-cli.md) a rövid útmutatót egy Azure Spring Cloud-szolgáltatás üzembe helyezéséhez és elindításához.


## <a name="restriction"></a>Korlátozás

A __konfigurációs kiszolgáló__ git-háttérrel való használata néhány korlátozást is igénybe vehet. Bizonyos tulajdonságok automatikusan bekerülnek az alkalmazási környezetbe a __konfigurációs kiszolgáló__ és a __szolgáltatás felderítésének__eléréséhez. Ha ezeket a tulajdonságokat a **konfigurációs kiszolgáló** fájljaiból is konfigurálja, ütközéseket és váratlan viselkedést tapasztalhat.  A tulajdonságok a következők: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```
> [!CAUTION]
> Javasoljuk __, hogy ne helyezze el__ a fenti tulajdonságokat a __konfigurációs kiszolgáló__ alkalmazás fájljaiban.

## <a name="create-your-config-server-files"></a>A konfigurációs kiszolgáló fájljainak létrehozása

Az Azure Spring Cloud támogatja az Azure DevOps, a GitHub, a GitLab és a bitbucket a konfigurációs kiszolgáló fájljainak tárolásához.  Ha a tárháza elkészült, végezze el a konfigurációs fájlokat az alábbi utasításokkal, és tárolja őket.

Emellett néhány konfigurálható tulajdonság csak bizonyos típusok esetében érhető el. A következő alszakaszok felsorolják az egyes adattár-típusok tulajdonságait.


### <a name="public-repository"></a>Nyilvános tárház

Nyilvános tárház használata esetén a konfigurálható tulajdonságok korlátozottabbak lesznek.

A nyilvános `Git` tárház beállításához használt összes konfigurálható tulajdonság alább látható.

> [!NOTE]
> Most már csak kötőjel ("-") adható meg a különálló szavak elnevezési konvenciója. Például `default-label` értéket használ, de nem `defaultLabel`.

| Tulajdonság        | Szükséges | Funkció                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | A konfigurációs kiszolgálói háttérként használt `Git` tárház `uri`, `http://`, `https://`, `git@` vagy `ssh://` használatával kell elindítani. |
| `default-label` | `no`     | @No__t-0 tárház alapértelmezett címkéje `branch name`, `tag name` vagy `commit-id` adattár lehet. |
| `search-paths`  | `no`     | A `Git` tárház alkönyvtáraiban való kereséshez használt karakterláncok tömbje. |

------

### <a name="private-repository-with-ssh-authentication"></a>Privát adattár SSH-hitelesítéssel

Az alábbi lista a privát `Git` adattár beállításához használt összes konfigurálható tulajdonságot tartalmazza `Ssh`.

> [!NOTE]
> Most már csak kötőjel ("-") adható meg a különálló szavak elnevezési konvenciója. Például `default-label` értéket használ, de nem `defaultLabel`.

| Tulajdonság                   | Szükséges | Funkció                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | A konfigurációs kiszolgálói háttérként használt `Git` tárház `uri`, `http://`, `https://`, `git@` vagy `ssh://` használatával kell elindítani. |
| `default-label`            | `no`     | @No__t-0 tárház alapértelmezett címkéje `branch name`, `tag name` vagy `commit-id` adattár lehet. |
| `search-paths`             | `no`     | A `Git` tárház alkönyvtáraiban való kereséshez használt karakterláncok tömbje. |
| `private-key`              | `no`     | A `Ssh` titkos kulcs a `Git` adattár eléréséhez __szükséges__ , ha a `uri` `git@` vagy `ssh://` használatával indult el. |
| `host-key`                 | `no`     | A git-tárház kiszolgálójának gazdagép kulcsa nem tartalmazhatja a `host-key-algorithm` által jelzett algoritmus-előtagot. |
| `host-key-algorithm`       | `no`     | A gazdagép kulcsának algoritmusa a `ssh-dss` egyikének kell lennie. `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` és `ecdsa-sha2-nistp521`. Csak akkor szükséges, ha `host-key` létezik. |
| `strict-host-key-checking` | `no`     | Azt jelzi, hogy a konfigurációs kiszolgáló nem fog-e elindulni a `host-key` megadása esetén, `true` (alapértelmezett érték) vagy a `false` értéknek kell lennie. |

-----

### <a name="private-repository-with-basic-authentication"></a>Egyéni adattár alapszintű hitelesítéssel

Az alapszintű hitelesítéssel rendelkező privát git-tárház beállításához használt összes konfigurálható tulajdonság alább látható.

> [!NOTE]
> Most már csak kötőjel ("-") adható meg a különálló szavak elnevezési konvenciója. Használhatja például a következőt: `default-label`, de nem `defaultLabel`.

| Tulajdonság        | Szükséges | Funkció                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | A konfigurációs kiszolgálói háttérként használt `Git` tárház `uri`, `http://`, `https://`, `git@` vagy `ssh://` használatával kell elindítani. |
| `default-label` | `no`     | @No__t-0 tárház alapértelmezett címkéje `branch name`, `tag name` vagy `commit-id` adattár lehet. |
| `search-paths`  | `no`     | A `Git` tárház alkönyvtáraiban való kereséshez használt karakterláncok tömbje. |
| `username`      | `no`     | A `username` a `Git` adattár-kiszolgáló eléréséhez __szükséges__ , `Git` tárház-kiszolgáló támogatásához `Http Basic Authentication`. |
| `password`      | `no`     | A `Git` adattár-kiszolgáló eléréséhez használt jelszó, a __szükséges__ `Git` tárház-kiszolgáló támogatása `Http Basic Authentication`. |

> [!NOTE]
> Néhány `Git` adattár-kiszolgáló, például a GitHub, a "Personal-token" vagy a "hozzáférési jogkivonat" használatát támogatja a `HTTP Basic Authentication` számára. Ezt a tokent itt is használhatja jelszóként, és a "Personal-token" vagy a "hozzáférési jogkivonat" nem jár le. A git-tárház-kiszolgálók, például a BitBucket és az Azure DevOps esetében azonban a token egy vagy két órán belül lejár, amely az Azure Spring Cloud használatával nem valósítható meg.

### <a name="git-repositories-with-pattern"></a>Git-adattárak mintázattal

Az alábbi listában a git-adattárak beállításához használt összes konfigurálható tulajdonság szerepel.

> [!NOTE]
> Most már csak kötőjel ("-") adható meg a különálló szavak elnevezési konvenciója. Használhatja például a következőt: `default-label`, de nem `defaultLabel`.

| Tulajdonság                           | Szükséges         | Funkció                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | A Térkép @no__t – 0 adattárakat tartalmaz a megadott névvel. |
| `repos."uri"`                      | @no__t – 0 `repos` | A konfigurációs kiszolgálói háttérként használt `Git` tárház `uri`, `http://`, `https://`, `git@` vagy `ssh://` használatával kell elindítani. |
| `repos."name"`                     | @no__t – 0 `repos` | Egy `Git` tárház azonosítására szolgáló név, amely csak akkor __szükséges__ , ha `repos` létezik. A fentiek közül például `team-A`, `team-B`. |
| `repos."pattern"`                  | `no`             | Az alkalmazásnév megfeleltetéséhez használt karakterlánc tömbje minden mintához `{application}/{profile}` formátumot használhat helyettesítő karakterekkel. |
| `repos."default-label"`            | `no`             | @No__t-0 tárház alapértelmezett címkéje `branch name`, `tag name` vagy `commit-id` adattár lehet. |
| `repos."search-paths`"             | `no`             | A `Git` tárház alkönyvtáraiban való kereséshez használt karakterláncok tömbje. |
| `repos."username"`                 | `no`             | A `username` a `Git` adattár-kiszolgáló eléréséhez __szükséges__ , `Git` tárház-kiszolgáló támogatásához `Http Basic Authentication`. |
| `repos."password"`                 | `no`             | A `Git` adattár-kiszolgáló eléréséhez használt jelszó, a __szükséges__ `Git` tárház-kiszolgáló támogatása `Http Basic Authentication`. |
| `repos."private-key"`              | `no`             | A `Ssh` titkos kulcs a `Git` adattár eléréséhez __szükséges__ , ha a `uri` `git@` vagy `ssh://` használatával indult el. |
| `repos."host-key"`                 | `no`             | A git-tárház kiszolgálójának gazdagép kulcsa nem tartalmazhatja a `host-key-algorithm` által jelzett algoritmus-előtagot. |
| `repos."host-key-algorithm"`       | `no`             | A gazdagép kulcsának algoritmusa a `ssh-dss` egyikének kell lennie. `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` és `ecdsa-sha2-nistp521`. Csak akkor __szükséges__ , ha `host-key` létezik. |
| `repos."strict-host-key-checking"` | `no`             | Azt jelzi, hogy a konfigurációs kiszolgáló nem fog-e elindulni a `host-key` megadása esetén, `true` (alapértelmezett érték) vagy a `false` értéknek kell lennie. |

### <a name="import-applicationyml-file-from-spring-cloud-config"></a>@No__t-0 fájl importálása a Spring Cloud config-ból

Az alapértelmezett konfigurációs kiszolgáló beállításait közvetlenül a [Spring Cloud config](https://spring.io/projects/spring-cloud-config) webhelyről importálhatja. Ezt közvetlenül a Azure Portal teheti meg, így nem kell végrehajtania a konfigurációs kiszolgáló fájljainak vagy tárházának előkészítéséhez szükséges lépéseket.

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>A konfigurációs kiszolgáló adattárának csatlakoztatása az Azure Spring Cloud-hoz

Most, hogy a konfigurációs fájlokat mentette egy adattárba, hozzá kell csatlakoznia az Azure Spring Cloud-hoz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Navigáljon az Azure Spring Cloud **Áttekintés** oldalára.

1. Nyissa meg a **konfigurációs kiszolgáló** fület a **Beállítások** fejléc alatt a bal oldali menüben.

### <a name="public-repository"></a>Nyilvános tárház

Ha a tárház nyilvános, egyszerűen kattintson a **nyilvános** gombra, és illessze be az URL-címet.

### <a name="private-repository"></a>Privát tárház

Az Azure Spring Cloud támogatja az SSH-hitelesítést. Kövesse a Azure Portal útmutatását, és adja hozzá a nyilvános kulcsot a tárházhoz. Ezt követően ügyeljen arra, hogy a konfigurációs fájlban szerepeljen a titkos kulcs.

Kattintson az **alkalmaz** gombra a konfigurációs kiszolgáló beállításának befejezéséhez.


## <a name="delete-your-app-configuration"></a>Az alkalmazás konfigurációjának törlése

A konfigurációs fájl mentése után megjelenik az alkalmazás- **konfiguráció törlése** gomb a **konfiguráció** lapon. Ezzel a művelettel teljesen törli a meglévő beállításokat. Ezt akkor kell megtennie, ha a konfigurációs kiszolgálót más forráshoz kívánja kapcsolni, például a GitHubról az Azure DevOps-ra való áttéréssel.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan engedélyezheti és konfigurálhatja a konfigurációs kiszolgálót. Ha többet szeretne megtudni az alkalmazások kezeléséről, folytassa az alkalmazás manuális méretezését ismertető oktatóanyaggal.

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan méretezheti manuálisan az Azure Spring Cloud-alkalmazást](spring-cloud-tutorial-scale-manual.md).

