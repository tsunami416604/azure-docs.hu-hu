---
title: Beállításjegyzék-hitelesítési beállítások
description: A privát Azure Container Registry hitelesítési lehetőségei, beleértve a Azure Active Directory identitással való bejelentkezést, az egyszerű szolgáltatásnév használatát és a nem kötelező rendszergazdai hitelesítő adatok használatát.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5459ac29c1264b18404cb2863b9d4209907ac029
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152943"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Hitelesítés Azure Container registryvel

Az Azure Container Registry szolgáltatással többféleképpen is végezhet hitelesítést, amelyek mindegyike egy vagy több beállításjegyzék-használati forgatókönyvre alkalmazható.

Az ajánlott módszerek közé tartozik a beállításjegyzék közvetlen hitelesítése [Egyéni bejelentkezéssel](#individual-login-with-azure-ad), illetve az alkalmazások és a tároló-rendszerszervezők felügyelet nélküli vagy "fej nélküli" hitelesítést végezhetnek Azure Active Directory (Azure ad) [egyszerű szolgáltatásnév](#service-principal)használatával.

## <a name="authentication-options"></a>Hitelesítési lehetőségek

Az alábbi táblázat az elérhető hitelesítési módszereket és ajánlott forgatókönyveket sorolja fel. További részletekért tekintse meg a csatolt tartalmakat.

| Módszer                               | Hitelesítés                                           | Forgatókönyvek                                                            | RBAC                             | Korlátozások                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Egyedi ad-identitás](#individual-login-with-azure-ad)                | `az acr login` az Azure CLI-ben                             | Interaktív Leküldés/lekérés fejlesztőknek, tesztelőknek                                    | Igen                              | Az AD-jogkivonatot 3 óránként meg kell újítani     |
| [Ad egyszerű szolgáltatás](#service-principal)                  | `docker login`<br/><br/>`az acr login` az Azure CLI-ben<br/><br/> Beállításjegyzék-bejelentkezési beállítások az API-kon vagy az eszközökön<br/><br/> [Kubernetes-lekéréses titkos](container-registry-auth-kubernetes.md)                                           | Felügyelet nélküli leküldés CI/CD-folyamatból<br/><br/> Felügyelet nélküli lekérés az Azure-ba vagy a külső szolgáltatásokra  | Igen                              | Az SP-jelszó alapértelmezett lejárati ideje 1 év       |                                                           
| [Integrálás az AK-val](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Beállításjegyzék csatolása az AK-fürt létrehozásakor vagy frissítésekor  | Felügyelet nélküli lekérés az AK-fürthöz                                                  | Nem, csak a lekéréses hozzáférés             | Csak ak-fürttel érhető el            |
| [Felügyelt identitás az Azure-erőforrásokhoz](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` az Azure CLI-ben                                       | Felügyelet nélküli leküldés az Azure CI/CD-folyamatból<br/><br/> Felügyelet nélküli lekérés az Azure-szolgáltatásokhoz<br/><br/>   | Igen                              | Csak olyan Azure-szolgáltatások használata, amelyek [támogatják az Azure-erőforrások felügyelt identitásait](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Rendszergazda felhasználó](#admin-account)                            | `docker login`                                          | Interaktív leküldéses/lekéréses egyéni fejlesztő vagy teszter                           | Nem, mindig lekéréses és leküldéses hozzáférés  | Egy fiók/beállításjegyzék, nem ajánlott több felhasználó számára         |
| [Adattár – hatókörön belüli hozzáférési jogkivonat](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login` az Azure CLI-ben   | Interaktív leküldéses/lekéréses adattár egyéni fejlesztő vagy teszter által<br/><br/> Felügyelet nélküli Leküldés/lekérés a tárházba egyéni rendszer vagy külső eszköz használatával                  | Igen                              | Jelenleg nincs integrálva az AD Identity szolgáltatással  |

## <a name="individual-login-with-azure-ad"></a>Egyéni bejelentkezés az Azure AD-vel

Ha közvetlenül dolgozik a beállításjegyzékben, például képeket húz ki és küld el egy fejlesztői munkaállomásról, a hitelesítést az az [ACR login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) paranccsal végezheti el az [Azure CLI](/cli/azure/install-azure-cli)-ben:

```azurecli
az acr login --name <acrName>
```

Ha `az acr login`ba jelentkezik be, a parancssori felület az az [login](/cli/azure/reference-index#az-login) használatával zökkenőmentesen hitelesíti a munkamenetet a beállításjegyzékben. A hitelesítési folyamat befejezéséhez telepítenie és futnia kell a Docker környezetében. `az acr login` a Docker-ügyféllel állítja be Azure Active Directory tokent a `docker.config` fájlban. Ha így jelentkezett be, a hitelesítő adatai gyorsítótárazva lesznek, és a munkamenet későbbi `docker` parancsai nem igénylik a felhasználónevet vagy a jelszót.

> [!TIP]
> A `az acr login` használatával is hitelesítheti az egyes identitásokat, ha a Docker-rendszerképektől eltérő összetevőket szeretne leküldeni vagy lekérni a beállításjegyzékbe, például [OCI](container-registry-oci-artifacts.md)-összetevőkre.  


A beállításjegyzékhez való hozzáféréshez az `az acr login` által használt jogkivonat **3 órára**érvényes, ezért javasoljuk, hogy a `docker` parancs futtatása előtt mindig jelentkezzen be a beállításjegyzékbe. Ha a token lejár, akkor a `az acr login` parancs ismételt hitelesítésével frissítheti. 

A `az acr login` és az Azure-identitások használata [szerepköralapú hozzáférést](../role-based-access-control/role-assignments-portal.md)biztosít. Bizonyos esetekben előfordulhat, hogy az Azure AD-ben a saját egyéni identitásával szeretne bejelentkezni egy beállításjegyzékbe. A szolgáltatások közötti forgatókönyvek esetében, illetve egy munkacsoport vagy egy fejlesztési munkafolyamat igényének kezeléséhez, ahol nem szeretne egyéni hozzáférést kezelni, az [Azure-erőforrások felügyelt identitásával](container-registry-authentication-managed-identity.md)is bejelentkezhet.

## <a name="service-principal"></a>Egyszerű szolgáltatásnév

Ha egy [egyszerű szolgáltatásnevet](../active-directory/develop/app-objects-and-service-principals.md) rendel hozzá a beállításjegyzékhez, az alkalmazás vagy szolgáltatás a fej nélküli hitelesítéshez használhatja azt. Az egyszerű szolgáltatások lehetővé teszik a [szerepkörön alapuló hozzáférést](../role-based-access-control/role-assignments-portal.md) a beállításjegyzékhez, és több egyszerű szolgáltatást is hozzárendelhet egy beállításjegyzékhez. Több egyszerű szolgáltatás lehetővé teszi különböző alkalmazások eltérő hozzáférésének megadását.

A tároló-beállításjegyzékhez elérhető szerepkörök a következők:

* **AcrPull**: lekérés

* **AcrPush**: lekérés és leküldés

* **Tulajdonos**: szerepkörök lekérése, leküldése és társítása más felhasználóknak

A szerepkörök teljes listáját itt tekintheti meg: [Azure Container Registry szerepkörök és engedélyek](container-registry-roles.md).

Ahhoz, hogy CLI-parancsfájlok hozzon létre egy egyszerű szolgáltatást az Azure Container Registry használatával történő hitelesítéshez, és további útmutatást talál, lásd: [Azure Container Registry hitelesítés az egyszerű szolgáltatásokkal](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Rendszergazdai fiók

Minden tároló-beállításjegyzék tartalmaz egy rendszergazdai felhasználói fiókot, amely alapértelmezés szerint le van tiltva. Engedélyezheti a rendszergazda felhasználóját, és kezelheti a hitelesítő adatait a Azure Portal, illetve az Azure CLI vagy más Azure-eszközök használatával.

> [!IMPORTANT]
> A rendszergazdai fiók úgy van kialakítva, hogy egyetlen felhasználó hozzáférjen a beállításjegyzékhez, főleg tesztelési célokra. A rendszergazdai fiók hitelesítő adatait nem ajánlott több felhasználó között megosztani. A rendszergazdai fiókkal hitelesítő összes felhasználó egyetlen felhasználóként jelenik meg a beállításjegyzék leküldéses és lekéréses hozzáférésével. A fiók módosítása vagy letiltása letiltja a beállításjegyzék-hozzáférést minden olyan felhasználó számára, aki hitelesítő adatait használja. A felhasználók és a szolgáltatásokhoz egyéni identitást kell használni a fej nélküli forgatókönyvek esetében.
>

A rendszergazdai fiók két jelszóval van ellátva, amelyek közül mindkettő újra létrehozhatók. Két jelszó lehetővé teszi a beállításjegyzékhez való kapcsolódást úgy, hogy egy jelszót használ a másik létrehozásakor. Ha a rendszergazdai fiók engedélyezve van, átadhatja a felhasználónevet és a jelszót a `docker login` parancshoz, amikor a rendszer az alapszintű hitelesítést kéri a beállításjegyzékben. Például:

```
docker login myregistry.azurecr.io 
```

A bejelentkezési hitelesítő adatok kezelésével kapcsolatos ajánlott eljárásokért tekintse meg a [Docker login](https://docs.docker.com/engine/reference/commandline/login/) parancs referenciáját.

Ha engedélyezni szeretné a rendszergazda felhasználót egy meglévő beállításjegyzékben, használja az az [ACR Update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) parancs `--admin-enabled` paraméterét az Azure CLI-ben:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Engedélyezheti a rendszergazda felhasználót a Azure Portal a beállításjegyzékben navigálva, a **hozzáférési kulcsok** elemre kattintva a **Beállítások**területen, majd a **rendszergazda felhasználó**területen **engedélyezheti** .

![Rendszergazdai felhasználói felület engedélyezése a Azure Portal][auth-portal-01]

## <a name="next-steps"></a>Következő lépések

* [Az első rendszerkép leküldése az Azure CLI használatával](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
