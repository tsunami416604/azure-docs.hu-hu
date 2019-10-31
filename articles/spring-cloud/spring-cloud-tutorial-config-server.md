---
title: A konfigurációs kiszolgáló beállítása az Azure Spring Cloud-ban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthat be egy Spring Cloud config-kiszolgálót az Azure Spring Cloud-hoz a Azure Portal
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 10/18/2019
ms.openlocfilehash: 3a091c22f49ec31029a1808c10e675a4d0960fb4
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177922"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>Oktatóanyag: rugós Felhőbeli konfigurációs kiszolgáló beállítása a szolgáltatáshoz

Ez az oktatóanyag bemutatja, hogyan csatlakozhat egy Spring Cloud config-kiszolgálót az Azure Spring Cloud Service-hez.

A Spring Cloud config kiszolgáló-és ügyféloldali támogatást nyújt a külső konfigurációhoz egy elosztott rendszeren. A konfigurációs kiszolgálóval központi helyen kezelheti az alkalmazások külső tulajdonságait az összes környezetben. További tudnivalókért tekintse meg a [Spring Cloud config Server-referenciát](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 
* Már üzembe lett helyezve, és futtatja az Azure Spring Cloud Service-t.  Fejezze be [ezt](spring-cloud-quickstart-launch-app-cli.md) a rövid útmutatót egy Azure Spring Cloud-szolgáltatás üzembe helyezéséhez és elindításához.

## <a name="restriction"></a>Korlátozás

A __konfigurációs kiszolgáló__ git-háttérrel való használata néhány korlátozást is igénybe vehet. Bizonyos tulajdonságok automatikusan bekerülnek az alkalmazási környezetbe a __konfigurációs kiszolgáló__ és a __szolgáltatás felderítésének__eléréséhez. Ha ezeket a tulajdonságokat a **konfigurációs kiszolgáló** fájljaiból is konfigurálja, ütközéseket és váratlan viselkedést tapasztalhat. A tulajdonságok a következők: 

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

Az Azure Spring Cloud támogatja az Azure DevOps, a GitHub, a GitLab és a bitbucket a konfigurációs kiszolgáló fájljainak tárolásához. Ha a tárháza elkészült, végezze el a konfigurációs fájlokat az alábbi utasításokkal, és tárolja őket.

Emellett néhány konfigurálható tulajdonság csak bizonyos típusok esetében érhető el. A következő alszakaszok felsorolják az egyes adattár-típusok tulajdonságait.

### <a name="public-repository"></a>Nyilvános tárház

Nyilvános tárház használata esetén a konfigurálható tulajdonságok korlátozottabbak lesznek.

A nyilvános `Git` adattár beállításához használt összes konfigurálható tulajdonság alább látható.

> [!NOTE]
> Ha kötőjelet ("-") használ a szavak elkülönítésére, az egyetlen olyan elnevezési konvenció, amely jelenleg támogatott. Használhatja például a `default-label`, de nem `defaultLabel`.

| Tulajdonság        | Szükséges | Szolgáltatás                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | A konfigurációs kiszolgálói háttérként használt `Git` adattár `uri` a `http://`, `https://`, `git@`vagy `ssh://`használatával kell elindítani. |
| `default-label` | `no`     | Az `Git` adattár alapértelmezett címkéje legyen az adattár `branch name`, `tag name`vagy `commit-id`. |
| `search-paths`  | `no`     | A `Git` tárház alkönyvtáraiban való kereséshez használt karakterláncok tömbje. |

------

### <a name="private-repository-with-ssh-authentication"></a>Privát adattár SSH-hitelesítéssel

Az alábbi lista a privát `Git` adattár beállításához használt összes konfigurálható tulajdonságot tartalmazza `Ssh`.

> [!NOTE]
> Ha kötőjelet ("-") használ a szavak elkülönítésére, az egyetlen olyan elnevezési konvenció, amely jelenleg támogatott. Használhatja például a `default-label`, de nem `defaultLabel`.

| Tulajdonság                   | Szükséges | Szolgáltatás                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | A konfigurációs kiszolgálói háttérként használt `Git` adattár `uri` a `http://`, `https://`, `git@`vagy `ssh://`használatával kell elindítani. |
| `default-label`            | `no`     | Az `Git` adattár alapértelmezett címkéje legyen az adattár `branch name`, `tag name`vagy `commit-id`. |
| `search-paths`             | `no`     | A `Git` tárház alkönyvtáraiban való kereséshez használt karakterláncok tömbje. |
| `private-key`              | `no`     | A `Ssh` titkos kulcsa a `Git` adattár eléréséhez, amely akkor __szükséges__ , ha a `uri` a `git@` vagy a `ssh://`használatával kezdődik. |
| `host-key`                 | `no`     | A git-tárház kiszolgálójának gazdagép kulcsa nem tartalmazhatja a `host-key-algorithm`által lefedett algoritmus-előtagot. |
| `host-key-algorithm`       | `no`     | A gazda kulcs algoritmusának `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384`vagy `ecdsa-sha2-nistp521`nak kell lennie. Csak akkor szükséges, ha `host-key` létezik. |
| `strict-host-key-checking` | `no`     | Azt jelzi, hogy a konfigurációs kiszolgáló a magánhálózati `host-key`kihasználása esetén nem fog-e elindulni. `true` (alapértelmezett érték) vagy `false`kell lennie. |

-----

### <a name="private-repository-with-basic-authentication"></a>Egyéni adattár alapszintű hitelesítéssel

Az alapszintű hitelesítéssel rendelkező privát git-tárház beállításához használt összes konfigurálható tulajdonság alább látható.

> [!NOTE]
> Ha kötőjelet ("-") használ a szavak elkülönítésére, az egyetlen olyan elnevezési konvenció, amely jelenleg támogatott. Használja például a `default-label` nem `defaultLabel`.

| Tulajdonság        | Szükséges | Szolgáltatás                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | A konfigurációs kiszolgálói háttérként használt `Git` adattár `uri` a `http://`, `https://`, `git@`vagy `ssh://`használatával kell elindítani. |
| `default-label` | `no`     | Az `Git` adattár alapértelmezett címkéje legyen az adattár `branch name`, `tag name`vagy `commit-id`. |
| `search-paths`  | `no`     | A `Git` tárház alkönyvtáraiban való kereséshez használt karakterláncok tömbje. |
| `username`      | `no`     | Az `Git` adattár-kiszolgáló eléréséhez használt `username`, __Ha az__ `Git` adattár-kiszolgáló támogatja a `Http Basic Authentication`. |
| `password`      | `no`     | Az `Git` adattár-kiszolgáló eléréséhez használt jelszó, amely akkor __szükséges__ , ha az `Git` adattár-kiszolgáló támogatja a `Http Basic Authentication`. |

> [!NOTE]
> Egyes `Git` adattár-kiszolgálók, például a GitHub, támogatják a "Personal-token" vagy a "hozzáférési token" jelszót a `HTTP Basic Authentication`hoz. Ezt a tokent itt is használhatja jelszóként, és a "Personal-token" vagy a "hozzáférési jogkivonat" nem jár le. A git-tárház kiszolgálók, például a BitBucket és az Azure DevOps esetében azonban a token egy vagy két órán belül lejár, így ez a lehetőség nem valósítható meg az Azure Spring Cloud-hoz való használatra.

### <a name="git-repositories-with-pattern"></a>Git-adattárak mintázattal

Az alábbi listában a git-adattárak beállításához használt összes konfigurálható tulajdonság szerepel.

> [!NOTE]
> Ha kötőjelet ("-") használ a szavak elkülönítésére, az egyetlen olyan elnevezési konvenció, amely jelenleg támogatott. Használja például a `default-label` nem `defaultLabel`.

| Tulajdonság                           | Szükséges         | Szolgáltatás                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | Egy olyan Térkép, amely egy adott nevű `Git` adattár beállításait tartalmazza. |
| `repos."uri"`                      | `yes` on `repos` | A konfigurációs kiszolgálói háttérként használt `Git` adattár `uri` a `http://`, `https://`, `git@`vagy `ssh://`használatával kell elindítani. |
| `repos."name"`                     | `yes` on `repos` | Egy `Git` adattár azonosítására szolgáló név, amely csak akkor __szükséges__ , ha `repos` létezik. Például a fenti, `team-A`, `team-B`. |
| `repos."pattern"`                  | `no`             | Az alkalmazás nevének megfeleltetéséhez használt karakterláncok tömbje. Minden mintához használja a `{application}/{profile}` formátumot helyettesítő karakterekkel. |
| `repos."default-label"`            | `no`             | Az `Git` adattár alapértelmezett címkéje legyen az adattár `branch name`, `tag name`vagy `commit-id`. |
| `repos."search-paths`"             | `no`             | A `Git` tárház alkönyvtáraiban való kereséshez használt karakterláncok tömbje. |
| `repos."username"`                 | `no`             | Az `Git` adattár-kiszolgáló eléréséhez használt `username`, __Ha az__ `Git` adattár-kiszolgáló támogatja a `Http Basic Authentication`. |
| `repos."password"`                 | `no`             | Az `Git` adattár-kiszolgáló eléréséhez használt jelszó, amely akkor __szükséges__ , ha az `Git` adattár-kiszolgáló támogatja a `Http Basic Authentication`. |
| `repos."private-key"`              | `no`             | A `Ssh` titkos kulcsa `Git` adattár eléréséhez, amely akkor __szükséges__ , ha a `uri` a `git@` vagy a `ssh://`tel kezdődik. |
| `repos."host-key"`                 | `no`             | A git-tárház kiszolgálójának gazdagép kulcsa nem tartalmazhatja a `host-key-algorithm`által lefedett algoritmus-előtagot. |
| `repos."host-key-algorithm"`       | `no`             | A gazda kulcs algoritmusának `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384`vagy `ecdsa-sha2-nistp521`nak kell lennie. Csak akkor __szükséges__ , ha `host-key` létezik. |
| `repos."strict-host-key-checking"` | `no`             | Azt jelzi, hogy a konfigurációs kiszolgáló a magánhálózati `host-key`kihasználása esetén nem fog-e elindulni. `true` (alapértelmezett érték) vagy `false`kell lennie. |

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>A konfigurációs kiszolgáló adattárának csatlakoztatása az Azure Spring Cloud-hoz

Most, hogy a konfigurációs fájlokat mentette egy adattárba, hozzá kell csatlakoznia az Azure Spring Cloud-hoz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Navigáljon az Azure Spring Cloud **Áttekintés** oldalára.

1. Nyissa meg a **konfigurációs kiszolgáló** fület a **Beállítások** fejléc alatt a bal oldali menüben.

![ablak képernyőképe](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="input-repository-information-directly-to-the-azure-portal"></a>Bemeneti adattár adatai közvetlenül a Azure Portal

#### <a name="default-repository"></a>Alapértelmezett adattár

* Nyilvános tárház: az **alapértelmezett adattár** szakaszban illessze be az adattár URI-ját az **URI** szakaszba, és győződjön meg arról, hogy a **hitelesítési** beállítás **nyilvános**. Ezután kattintson az **alkalmaz** gombra. 

* Privát tárház: az Azure Spring Cloud támogatja az alapszintű jelszó/jogkivonat-alapú hitelesítést és az SSH-t.

    * Alapszintű hitelesítés: az **alapértelmezett adattár** szakaszban illessze be az adattár URI-ját az **URI** szakaszban, majd kattintson a **hitelesítésre**. Válassza az **alapszintű** **hitelesítési típust** , és adja meg felhasználónevét és jelszavát/jogkivonatát az Azure Spring Cloud elérésének biztosításához. Kattintson **az OK gombra** , és **alkalmazza** a konfigurációs kiszolgáló beállításának befejezéséhez.

    ![ablak képernyőképe](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Egyes git-tárház-kiszolgálók, például a GitHub egy `personal-token` vagy egy `access-token`, például az **egyszerű hitelesítéshez**használt jelszót használják. Ezt a tokent jelszóként használhatja az Azure Spring Cloud-ban, mivel a rendszer soha nem jár le. Más git-tárház-kiszolgálók, például a BitBucket és az Azure DevOps esetében azonban a `access-token` egy vagy két órán belül lejár. Ez azt jelenti, hogy ez a lehetőség nem életképes, ha ezeket az adattár-kiszolgálókat az Azure Spring Cloud használatával használja.]

    * SSH: az **alapértelmezett adattár** szakaszban illessze be az adattár URI-ját az **URI** szakaszban, majd kattintson a **hitelesítésre**. A **hitelesítési típusként** válassza az **SSH** lehetőséget, és adja meg a **titkos kulcsát**. Opcionálisan megadhatja a **gazdagép kulcsát** és a **gazdagép kulcsának algoritmusát**is. Ügyeljen arra, hogy a nyilvános kulcsot a konfigurációs kiszolgáló adattárában is tartalmazza. Kattintson **az OK gombra** , és **alkalmazza** a konfigurációs kiszolgáló beállításának befejezéséhez.

    ![ablak képernyőképe](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Minta adattár

Ha a szolgáltatás konfigurálásához egy opcionális **minta-tárházat** szeretne használni, adja meg az **URI** -t és a **hitelesítést** ugyanúgy, mint az **alapértelmezett tárházat**. Ügyeljen arra, hogy tartalmazza a minta **nevét** , majd kattintson az **alkalmaz** gombra, hogy csatolja a példányhoz. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Adja meg a tárház adatait egy YAML-fájlba

Ha YAML-fájlt írt az adattár beállításaival, a YAML-fájlt közvetlenül a helyi gépről importálhatja az Azure Spring Cloud-ba. Az alapszintű hitelesítéssel rendelkező privát tárház egyszerű YAML-fájlja a következőképpen fog kinézni:

```yml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

Kattintson az **importálási beállítások** gombra, majd válassza ki a `.yml` fájlt a projekt könyvtárából. Kattintson az **Importálás**elemre, majd az **értesítések** `async` művelet jelenik meg. 1-2 perc elteltével a sikeres jelentésnek kell lennie.

![ablak képernyőképe](media/spring-cloud-tutorial-config-server/local-yml-success.png)


Ekkor meg kell jelennie a Azure Portalban megjelenő YAML-fájl információinak. A befejezéshez kattintson az **alkalmaz** gombra. 


## <a name="delete-your-app-configuration"></a>Az alkalmazás konfigurációjának törlése

A konfigurációs fájl mentése után megjelenik az alkalmazás- **konfiguráció törlése** gomb a **konfiguráció** lapon. Ezzel a művelettel teljesen törli a meglévő beállításokat. Ezt akkor kell megtennie, ha a konfigurációs kiszolgálót más forráshoz kívánja kapcsolni, például a GitHubról az Azure DevOps-ra való áttéréssel.



## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan engedélyezheti és konfigurálhatja a konfigurációs kiszolgálót. Ha többet szeretne megtudni az alkalmazások kezeléséről, folytassa az alkalmazás manuális méretezését ismertető oktatóanyaggal.

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan méretezheti manuálisan az Azure Spring Cloud-alkalmazást](spring-cloud-tutorial-scale-manual.md).

