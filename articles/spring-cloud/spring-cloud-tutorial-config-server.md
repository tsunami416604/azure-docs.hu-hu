---
title: Oktatóanyag – A Config Server-példány beállítása az Azure Spring Cloud ban
description: Ebben az oktatóanyagban megtudhatja, hogyan állíthatja be az Azure Spring Cloud tavaszi konfigurációs szolgáltatásának tavaszi felhőpéldányát az Azure Portalon
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/18/2019
ms.openlocfilehash: 5e0b5633a153583117cfe0d90ec5c0e7c5f2a147
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277525"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-instance-for-your-service"></a>Oktatóanyag: A szolgáltatás tavaszi felhőalapú config server-példányának beállítása

Ez a cikk bemutatja, hogyan csatlakoztathat egy Spring Cloud Config Server-példányt az Azure Spring Cloud szolgáltatáshoz.

A Spring Cloud Config kiszolgáló- és ügyféloldali támogatást nyújt az elosztott rendszerben lévő külső konfigurációhoz. A Config Server-példánysegítségével központi helyen kezelheti az alkalmazások külső tulajdonságait az összes környezetben. További információ: [Spring Cloud Config Server reference](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené. 
* Egy már kiépített és futó Azure Spring Cloud szolgáltatás. Az Azure Spring Cloud-szolgáltatás beállításához és elindításához olvassa [el a Rövid útmutató: Java Spring alkalmazás indítása az Azure CLI használatával című témakört.](spring-cloud-quickstart-launch-app-cli.md)

## <a name="restriction"></a>Korlátozás

Bizonyos korlátozások vannak, ha a Config Server-t Git háttérrendszerrel használja. Egyes tulajdonságok automatikusan bekerülnek az alkalmazáskörnyezetbe a Config Server és a Service Discovery eléréséhez. Ha ezeket a tulajdonságokat a Config Server-fájlokból is konfigurálja, ütközéseket és váratlan viselkedést tapasztalhat. A tulajdonságok a következők: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```

> [!CAUTION]
> Javasoljuk, hogy ne _helyezze_ a fenti tulajdonságokat a Config Server alkalmazásfájlokba.

## <a name="create-your-config-server-files"></a>A Config Server-fájlok létrehozása

Az Azure Spring Cloud támogatja az Azure DevOps, A GitHub, a GitLab és a Bitbucket a Config Server-fájlok tárolásához. Ha készen áll a tárház, hozza létre a konfigurációs fájlokat az alábbi utasításokkal, és tárolja őket ott.

Ezenkívül egyes konfigurálható tulajdonságok csak bizonyos típusokesetében érhetők el. A következő alszakaszok az egyes tárháztípusok tulajdonságait sorolják fel.

### <a name="public-repository"></a>Nyilvános adattár

Ha nyilvános tárházat használ, a konfigurálható tulajdonságok korlátozottabbak.

A nyilvános Git-tárház beállításához használt összes konfigurálható tulajdonság az alábbi táblázatban található:

> [!NOTE]
> A szavak elkülönítése (-) használata jelenleg csak támogatott elnevezési konvenció. Használhat például *alapértelmezett címkét*, de *alapértelmezettfeliratot*nem.

| Tulajdonság        | Kötelező | Szolgáltatás                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Igen    | A Config Server háttérrendszerként használt Git-tárház URI-ja *http://*, *https://,* *git@* vagy *ssh://* kezdődik. |
| `default-label` | Nem     | A Git-tárház alapértelmezett címkéjének a tárház *ágnevének*, *címkenevének*vagy *véglegesítési azonosítójának* kell lennie. |
| `search-paths`  | Nem     | A Git-tárház alkönyvtárainak kereséséhez használt karakterláncok tömbje. |

------

### <a name="private-repository-with-ssh-authentication"></a>Privát tárház SSH-hitelesítéssel

Az SSH-val rendelkező privát Git-tárház beállításához használt összes konfigurálható tulajdonság az alábbi táblázatban található:

> [!NOTE]
> A szavak elkülönítése (-) használata jelenleg csak támogatott elnevezési konvenció. Használhat például *alapértelmezett címkét*, de *alapértelmezettfeliratot*nem.

| Tulajdonság                   | Kötelező | Szolgáltatás                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | Igen    | A Config Server háttérrendszerként használt Git-tárház URI-ját *http://,* *https://,* *git@* vagy *ssh://* kell indítani. |
| `default-label`            | Nem     | A Git-tárház alapértelmezett címkéjének a tárház *ágnevének*, *címkenevének*vagy *véglegesítési azonosítójának* kell lennie. |
| `search-paths`             | Nem     | A Git-tárház alkönyvtárainak kereséséhez használt karakterláncok tömbje. |
| `private-key`              | Nem     | Az SSH személyes kulcs a Git-tárház eléréséhez, _amely akkor szükséges,_ ha az URI *git@* vagy *ssh://* kezdődik. |
| `host-key`                 | Nem     | A Git-tárház kiszolgáló állomáskulcsa nem tartalmazhat a rendszerben `host-key-algorithm`lefedett algoritmuselőtagot. |
| `host-key-algorithm`       | Nem     | A gazdakulcs algoritmusának *ssh-dss*, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384*vagy *ecdsa-sha2-nistp521 ( ecdsa-dsa-sha2-nistp521*) algoritmusnak kell lennie. *Csak akkor kötelező,* ha `host-key` létezik. |
| `strict-host-key-checking` | Nem     | Azt jelzi, hogy a Config Server példány nem `host-key`indul-e el a privát eszköz kiaknázásakor. *Igaz* (alapértelmezett érték) vagy *hamis*értékűnek kell lennie. |

-----

### <a name="private-repository-with-basic-authentication"></a>Privát tárház alapszintű hitelesítéssel

Az alapfokú hitelesítéssel rendelkező magánGit-tárház beállításához használt összes konfigurálható tulajdonság az alábbiakban látható.

> [!NOTE]
> A szavak elkülönítése (-) használata jelenleg csak támogatott elnevezési konvenció. Használja például *az alapértelmezett címkét*, nem *a defaultLabel beállítást.*

| Tulajdonság        | Kötelező | Szolgáltatás                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Igen    | A Config Server háttérrendszerként használt Git-tárház URI-ját *http://,* *https://,* *git@* vagy *ssh://* kell indítani. |
| `default-label` | Nem     | A Git-tárház alapértelmezett címkéjének a tárház *ágnevének*, *címkenevének*vagy *véglegesítési azonosítójának* kell lennie. |
| `search-paths`  | Nem     | A Git-tárház alkönyvtárainak kereséséhez használt karakterláncok tömbje. |
| `username`      | Nem     | A Git-tárház kiszolgálójának eléréséhez használt felhasználónév, _amely akkor szükséges,_ ha a Git-tárház kiszolgálója támogatja a rendszert. `Http Basic Authentication` |
| `password`      | Nem     | A Git-tárház kiszolgálójának eléréséhez használt jelszó, amely `Http Basic Authentication`akkor _szükséges,_ ha a Git-tárház kiszolgálója támogatja a rendszert. |

> [!NOTE]
> Számos `Git` tárház-kiszolgáló támogatja a tokenek használatát a HTTP-alapfokú hitelesítés hez használt jelszavak helyett. Egyes adattárak, például a GitHub, lehetővé teszik a jogkivonatok határozatlan ideig való megőrzését. Azonban néhány Git tárház-kiszolgálók, beleértve az Azure DevOps, jogkivonatok néhány órán belül lejár. A tokenek lejáratát okozó adattárak nem használhatják a tokenalapú hitelesítést az Azure Spring Cloud szolgáltatással.

### <a name="git-repositories-with-pattern"></a>Git-adattárak mintával

A Git-tárházak mintával történő beállításához használt összes konfigurálható tulajdonság az alábbiakban látható.

> [!NOTE]
> A szavak elkülönítése (-) használata jelenleg csak támogatott elnevezési konvenció. Használja például *az alapértelmezett címkét*, nem *a defaultLabel beállítást.*

| Tulajdonság                           | Kötelező         | Szolgáltatás                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | Nem             | Egy adott nevű Git-tárház beállításaiból álló térkép. |
| `repos."uri"`                      | Igen a`repos` | A Config Server háttérrendszerként használt Git-tárház URI-ját *http://,* *https://,* *git@* vagy *ssh://* kell indítani. |
| `repos."name"`                     | Igen a`repos` | A Git-tárházban azonosítandó név, `repos` csak akkor _szükséges,_ ha létezik. Például *az A csapat*, *a B csapat.* |
| `repos."pattern"`                  | Nem             | Az alkalmazás nevének megfelelő karakterláncok tömbje. Minden mintához használja `{application}/{profile}` a helyettesítő karaktereket használó formátumot. |
| `repos."default-label"`            | Nem             | A Git-tárház alapértelmezett címkéjének a tárház *ágnevének*, *címkenevének*vagy *véglegesítési azonosítójának* kell lennie. |
| `repos."search-paths`"             | Nem             | A Git-tárház alkönyvtárainak kereséséhez használt karakterláncok tömbje. |
| `repos."username"`                 | Nem             | A Git-tárház kiszolgálójának eléréséhez használt felhasználónév, _amely akkor szükséges,_ ha a Git-tárház kiszolgálója támogatja a rendszert. `Http Basic Authentication` |
| `repos."password"`                 | Nem             | A Git-tárház kiszolgálójának eléréséhez használt jelszó, amely `Http Basic Authentication`akkor _szükséges,_ ha a Git-tárház kiszolgálója támogatja a rendszert. |
| `repos."private-key"`              | Nem             | Az SSH személyes kulcs a Git-tárház eléréséhez, _amely akkor szükséges,_ ha az URI *git@* vagy *ssh://* kezdődik. |
| `repos."host-key"`                 | Nem             | A Git-tárház kiszolgáló állomáskulcsa nem tartalmazhat a rendszerben `host-key-algorithm`lefedett algoritmuselőtagot. |
| `repos."host-key-algorithm"`       | Nem             | A gazdakulcs algoritmusának *ssh-dss*, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384*vagy *ecdsa-sha2-nistp521 ( ecdsa-dsa-sha2-nistp521*) algoritmusnak kell lennie. *Csak akkor kötelező,* ha `host-key` létezik. |
| `repos."strict-host-key-checking"` | Nem             | Azt jelzi, hogy a Config Server példány nem `host-key`indul-e el a privát eszköz kiaknázásakor. *Igaz* (alapértelmezett érték) vagy *hamis*értékűnek kell lennie. |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>A Config Server-tárház csatolása az Azure Spring Cloud szolgáltatáshoz

Most, hogy a konfigurációs fájlokat menti egy tárházban, csatlakoztatnia kell az Azure Spring Cloud hozzá.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Nyissa meg az Azure Spring Cloud **Áttekintés lapját.**

1. Válassza ki a konfigurálni kívánt szolgáltatást.

1. A szolgáltatáslap bal oldali ablaktáblájában, a **Beállítások**csoportban válassza a **Config Server** lapot.

![A Config Server ablak](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>Adattáradatainak megadása közvetlenül az Azure Portalon

#### <a name="default-repository"></a>Alapértelmezett adattár

* **Nyilvános tárház:** Az alapértelmezett tárház szakaszban az Uri mezőben illessze be a tárház URI.Public repository : In the **Default repository** section, in the **Uri** box, paste the repository URI.  Állítsa a **címkét** **config**értékre. Győződjön meg arról, hogy a **Hitelesítés** beállítás **nyilvános,** majd válassza az **Alkalmazás gombra** a befejezéshez lehetőséget. 

* **Privát tárház:** Az Azure Spring Cloud támogatja az alapvető jelszó/token alapú hitelesítést és az SSH-t.

    * **Alapfokú hitelesítés:** Az **Alapértelmezett tárház** szakaszban az **Uri** mezőben illessze be a tárház URI-ját, majd válassza a **Hitelesítés** ("ceruza" ikon) gombot. A **Hitelesítés szerkesztése** ablaktáblán a **Hitelesítés típusa** legördülő listában válassza a **HTTP Basic**lehetőséget, majd adja meg a felhasználónevét és jelszavát/tokenjét az Azure Spring Cloud eléréséhez való hozzáférés engedélyezéséhez. Válassza **az OK**gombot, majd az Alkalmaz **gombra** a Config Server-példány beállításának befejezéséhez.

    ![A Hitelesítés szerkesztése ablaktábla](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Egyes Git-tárház-kiszolgálók, például a GitHub, *személyes tokent* vagy *hozzáférési tokent*( például jelszót ) használnak **az alapfokú hitelesítéshez.** Az azure spring cloudban használhatja jelszóként ezt a fajta jogkivonatot, mert soha nem fog lejárni. De más Git tárház-kiszolgálók, például a Bitbucket és az Azure DevOps, a *hozzáférési jogkivonat* lejár egy vagy két óra alatt. Ez azt jelenti, hogy a lehetőség nem életképes, ha ezeket a tárház-kiszolgálókat használja az Azure Spring Cloud szolgáltatással.

    * **SSH**: Az **alapértelmezett tárház** szakaszban az **Uri** mezőben illessze be a tárház URI-ját, majd válassza a **Hitelesítés** ("ceruza" ikon) gombot. A **Hitelesítés szerkesztése** ablaktáblán a **Hitelesítés típusa** legördülő listában válassza az **SSH**lehetőséget, majd írja be a **személyes kulcsot.** Adja meg a **gazdakulcsot** és a **Gazdakulcs algoritmusát.** Ügyeljen arra, hogy a nyilvános kulcsot a Config Server tárházban. Válassza **az OK**gombot, majd az Alkalmaz **gombra** a Config Server-példány beállításának befejezéséhez.

    ![A Hitelesítés szerkesztése ablaktábla](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Mintatár

Ha egy választható **Mintatár-tárházat** szeretne használni a szolgáltatás konfigurálásához, az **URI-t** és a **Hitelesítést** ugyanúgy adja meg, mint az **alapértelmezett tárházat.** Ügyeljen arra, hogy adjon meg egy **nevet** a mintához, majd válassza az **Alkalmaz** lehetőséget, hogy csatolja a példányhoz. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Tárházadatainak bevitele YAML-fájlba

Ha yaml-fájlt írt a tárház beállításaival, importálhatja a fájlt közvetlenül a helyi gépről az Azure Spring Cloudba. Egy egyszerű YAML-fájl egy egyszerű hitelesítéssel rendelkező magántárházhoz a következőkre néz ki:

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

Válassza a **Beállítások importálása** gombot, majd a Projekt könyvtárából válassza ki a YAML-fájlt. Válassza **az Importálás** `async` lehetőséget, majd megjelenik egy művelet az **értesítésekből.** 1-2 perc elteltével a sikerről kell jelentést tennie.

![A Konfigurációs kiszolgáló értesítései ablaktábla](media/spring-cloud-tutorial-config-server/local-yml-success.png)


A YAML-fájlból származó adatokat meg kell jelenjenie az Azure Portalon. Válassza az **Alkalmazás lehetőséget** a befejezéshez. 


## <a name="delete-your-app-configuration"></a>Az alkalmazáskonfiguráció törlése

A konfigurációs fájl mentése után megjelenik az **Alkalmazás konfigurációjának törlése** gomb a **Konfiguráció** lapon. Válassza ki, ha a Config Server-példányt egy másik forráshoz szeretné csatlakoztatni, például a GitHubról az Azure DevOps-ra való áttéréshez.



## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan engedélyezheti és konfigurálhatja a Spring Cloud Config Server-példányt. Ha többet szeretne megtudni az alkalmazás kezeléséről, folytassa az alkalmazás manuális méretezésével kapcsolatos oktatóanyaggal.

> [!div class="nextstepaction"]
> [Oktatóanyag: Alkalmazás méretezése az Azure Spring Cloudban](spring-cloud-tutorial-scale-manual.md)
