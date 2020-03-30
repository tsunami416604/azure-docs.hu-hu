---
title: Beállításjegyzék-hitelesítési beállítások
description: A privát Azure-tároló beállításjegyzék hitelesítési beállításai, beleértve az Azure Active Directory-identitással való bejelentkezést, a szolgáltatásnévi tagok használatát és a választható rendszergazdai hitelesítő adatok használatát.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5459ac29c1264b18404cb2863b9d4209907ac029
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247045"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Hitelesítés Azure-tároló beállításjegyzékével

Az Azure-tároló beállításjegyzékkel való hitelesítés számos módon lehetséges, amelyek mindegyike egy vagy több beállításjegyzék-használati forgatókönyvre vonatkozik.

Az ajánlott módok közé tartozik a beállításjegyzékbe való hitelesítés közvetlenül [az egyéni bejelentkezésen](#individual-login-with-azure-ad)keresztül, vagy az alkalmazások és a tárolóvezői felügyelet nélküli vagy "fej nélküli" hitelesítést végezhetnek egy Azure Active Directory (Azure AD) [egyszerű szolgáltatás](#service-principal)használatával.

## <a name="authentication-options"></a>Hitelesítési lehetőségek

Az alábbi táblázat felsorolja az elérhető hitelesítési módszereket és az ajánlott forgatókönyveket. A részleteket lásd a csatolt tartalomban.

| Módszer                               | Hogyan lehet hitelesíteni                                           | Forgatókönyvek                                                            | RBAC                             | Korlátozások                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Egyéni AD-identitás](#individual-login-with-azure-ad)                | `az acr login` az Azure CLI-ben                             | Interaktív push / pull a fejlesztők, tesztelők                                    | Igen                              | Az AD-jogkivonatot 3 óránként meg kell újítani     |
| [AD szolgáltatásnév](#service-principal)                  | `docker login`<br/><br/>`az acr login`az Azure CLI-ben<br/><br/> Rendszerleíró adatbázis bejelentkezési beállításai az API-kban vagy az eszközökben<br/><br/> [Kubernetes húzza titkos](container-registry-auth-kubernetes.md)                                           | Felügyelet nélküli leküldéses CI/CD-folyamatból<br/><br/> Felügyelet nélküli lekérése az Azure-hoz vagy külső szolgáltatásokhoz  | Igen                              | SP jelszó alapértelmezett lejárati 1 év       |                                                           
| [Integrálás az AKS-sel](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Rendszerleíró adatbázis csatolása az AKS-fürt létrehozásakor vagy frissítésekor  | Felügyelet nélküli lekérése az AKS-fürthöz                                                  | Nem, csak a hozzáférés lekérése             | Csak AKS-fürtben érhető el            |
| [Felügyelt identitás az Azure-erőforrásokhoz](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` az Azure CLI-ben                                       | Felügyelet nélküli leküldéses az Azure CI/CD-folyamatból<br/><br/> Felügyelet nélküli lekérése az Azure-szolgáltatásokhoz<br/><br/>   | Igen                              | Csak olyan Azure-szolgáltatásokból használja, amelyek [támogatják az Azure-erőforrások felügyelt identitásait](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Rendszergazdai felhasználó](#admin-account)                            | `docker login`                                          | Interaktív push / pull egyéni fejlesztő vagy tesztelő                           | Nem, mindig húzza és nyomja a hozzáférést  | Rendszerleíró adatbázisonként egy fiók, több felhasználó számára nem ajánlott         |
| [Tárház hatókörű hozzáférési jogkivonata](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login`az Azure CLI-ben   | Interaktív push / pull a tárház az egyes fejlesztő vagy tesztelő<br/><br/> Felügyelet nélküli push/pull to repository az egyes rendszer vagy külső eszköz                  | Igen                              | Jelenleg nincs integrálva az AD identitásával  |

## <a name="individual-login-with-azure-ad"></a>Egyéni bejelentkezés az Azure AD-vel

Ha közvetlenül a beállításjegyzékkel dolgozik, például képeket szeretne leadni egy fejlesztési munkaállomásról, és lenyomja a képeket, hitelesítse magát az [Azure CLI](/cli/azure/install-azure-cli) [az acr bejelentkezési](/cli/azure/acr?view=azure-cli-latest#az-acr-login) parancsával:

```azurecli
az acr login --name <acrName>
```

Amikor bejelentkezik `az acr login`a, a CLI az [az bejelentkezés](/cli/azure/reference-index#az-login) végrehajtásakor létrehozott jogkivonatot használja a munkamenet zökkenőmentes hitelesítéséhez a beállításjegyzékkel. A hitelesítési folyamat befejezéséhez a Docker-t telepíteni és futtatni kell a környezetében. `az acr login`a Docker-ügyfél segítségével állítsa be az `docker.config` Azure Active Directory-jogkivonatot a fájlban. Miután így bejelentkezett, a hitelesítő adatok gyorsítótárba `docker` kerülnek, és a munkamenet későbbi parancsaihoz nincs szükség felhasználónévre vagy jelszóra.

> [!TIP]
> Az `az acr login` egyéni identitások hitelesítésére is használható, ha a Docker-rendszerképeken kívül más összetevőket szeretne leadni vagy lehallgatni a rendszerleíró adatbázisba, például [az OCI-összetevőket.](container-registry-oci-artifacts.md)  


A beállításjegyzék-hozzáférés esetében `az acr login` a használt jogkivonat **3 óráig**érvényes, ezért azt `docker` javasoljuk, hogy a parancs futtatása előtt mindig jelentkezzen be a rendszerleíró adatbázisba. Ha a jogkivonat lejár, frissítheti `az acr login` azt a parancs újra hitelesítéséhez a parancs használatával. 

Az `az acr login` Azure-identitások használatával [szerepköralapú hozzáférést](../role-based-access-control/role-assignments-portal.md)biztosít. Bizonyos esetekben előfordulhat, hogy szeretne bejelentkezni egy beállításjegyzékbe a saját egyéni identitásaz Azure AD-ben. Szolgáltatásközi forgatókönyvek esetén, illetve egy munkacsoport vagy egy fejlesztési munkafolyamat igényeinek kezeléséhez, ahol nem szeretné kezelni az egyéni hozzáférést, az [Azure-erőforrások felügyelt identitásával](container-registry-authentication-managed-identity.md)is bejelentkezhet.

## <a name="service-principal"></a>Szolgáltatásnév

Ha egyszerű [szolgáltatást](../active-directory/develop/app-objects-and-service-principals.md) rendel a beállításjegyzékhez, az alkalmazás vagy szolgáltatás használhatja azt a fej nélküli hitelesítéshez. A szolgáltatásnévi tagok [szerepköralapú hozzáférést](../role-based-access-control/role-assignments-portal.md) biztosítanak a beállításjegyzékhez, és több szolgáltatásnévkét is hozzárendelhet a rendszerleíró adatbázishoz. Több egyszerű szolgáltatás lehetővé teszi, hogy különböző hozzáférést definiáljon a különböző alkalmazásokhoz.

A tárolók beállításjegyzékéhez rendelkezésre álló szerepkörök a következők:

* **AcrPull**: húzás

* **AcrPush:** húzza és nyomja

* **Tulajdonos**: szerepkörök lekérése, leküldése és hozzárendelése más felhasználókhoz

A szerepkörök teljes listáját az [Azure Container Registry szerepkörei és engedélyei című témakörben található.](container-registry-roles.md)

Cli-parancsfájlok hozzon létre egy egyszerű szolgáltatás hitelesítéséhez egy Azure container registry, és további útmutatást, lásd: [Azure Container Registry hitelesítés egyszerű szolgáltatásokkal.](container-registry-auth-service-principal.md)

## <a name="admin-account"></a>Rendszergazdai fiók

Minden tároló beállításjegyzék tartalmaz egy rendszergazdai felhasználói fiókot, amely alapértelmezés szerint le van tiltva. Engedélyezheti a rendszergazdai felhasználót, és kezelheti a hitelesítő adatait az Azure Portalon, vagy az Azure CLI vagy más Azure-eszközök használatával.

> [!IMPORTANT]
> Az admin fiók célja, hogy egyetlen felhasználó hozzáférjen a rendszerleíró adatbázishoz, elsősorban tesztelési célokra. Nem javasoljuk, hogy a rendszergazdai fiók hitelesítő adatait több felhasználó között ossza meg. Az admin fiókkal hitelesítő összes felhasználó egyetlen felhasználóként jelenik meg, aki leküldéses és lekéréses hozzáférést biztosít a rendszerleíró adatbázishoz. A fiók módosítása vagy letiltása letiltja a rendszerleíró adatbázis hozzáférését minden olyan felhasználó számára, aki a hitelesítő adatait használja. Egyéni identitás ajánlott a felhasználók és a szolgáltatásnév nélküli forgatókönyvek.
>

Az admin fiók két jelszóval van ellátva, amelyek mindegyike regenerálható. Két jelszó lehetővé teszi, hogy az egyik jelszó használatával fenntartsa a kapcsolatot a beállításjegyzékkel, miközben újragenerálja a másikat. Ha a rendszergazdai fiók engedélyezve van, átadhatja `docker login` a felhasználónevet és a jelszót a parancsnak, amikor az alapfokú hitelesítést kéri a rendszerleíró adatbázisnak. Példa:

```
docker login myregistry.azurecr.io 
```

A bejelentkezési hitelesítő adatok kezeléséhez ajánlott eljárásokat a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/) parancs hivatkozási.

Ha engedélyezni szeretné a rendszergazdai felhasználóegy `--admin-enabled` meglévő beállításjegyzék, használhatja a paraméteraz [az acr frissítési](/cli/azure/acr?view=azure-cli-latest#az-acr-update) parancs az Azure CLI:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Engedélyezheti a rendszergazda felhasználót az Azure Portalon a beállításjegyzékben való navigálással, a **Beállítások**csoportban az **Access-kulcsok,** majd a **Rendszergazda felhasználó** **csoportban az Engedélyezés lehetőséget** választva.

![Rendszergazdai felhasználói felület engedélyezése az Azure Portalon][auth-portal-01]

## <a name="next-steps"></a>További lépések

* [Az első kép leküldése az Azure CLI használatával](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
