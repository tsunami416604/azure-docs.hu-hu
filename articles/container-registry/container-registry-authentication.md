---
title: Beállításjegyzék-hitelesítési beállítások
description: Az Azure Container Registry hitelesítési lehetőségei, beleértve a Azure Active Directory identitással való bejelentkezést, az egyszerű szolgáltatásnév használatát és a nem kötelező rendszergazdai hitelesítő adatok használatát.
ms.topic: article
ms.date: 12/21/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fbe77dee4104e3c654aad58db82765733b2c3e1d
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264509"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Hitelesítés privát Docker-tároló beállításjegyzékével

Az Azure Container Registry szolgáltatással többféleképpen is végezhet hitelesítést, amelyek mindegyike egy vagy több beállításjegyzék-használati forgatókönyvre alkalmazható.

Az ajánlott módszerek közé tartozik a beállításjegyzék közvetlen hitelesítése [Egyéni bejelentkezéssel](#individual-login-with-azure-ad), illetve az alkalmazások és a tároló-rendszerszervezők felügyelet nélküli vagy "fej nélküli" hitelesítést végezhetnek Azure Active Directory (Azure ad) [egyszerű szolgáltatásnév](#service-principal)használatával.

## <a name="individual-login-with-azure-ad"></a>Egyéni bejelentkezés az Azure AD-vel

Ha közvetlenül dolgozik a beállításjegyzékben, például képeket húz ki és küld el egy fejlesztői munkaállomásról, a hitelesítést az az [ACR login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) paranccsal végezheti el az [Azure CLI](/cli/azure/install-azure-cli)-ben:

```azurecli
az acr login --name <acrName>
```

Ha `az acr login`ba jelentkezik be, a parancssori felület az az [login](/cli/azure/reference-index#az-login) használatával zökkenőmentesen hitelesíti a munkamenetet a beállításjegyzékben. Ha így jelentkezett be, a hitelesítő adatai gyorsítótárazva lesznek, és a munkamenet későbbi `docker` parancsai nem igénylik a felhasználónevet vagy a jelszót. 

A beállításjegyzékhez való hozzáféréshez az `az acr login` által használt jogkivonat **3 órára**érvényes, ezért javasoljuk, hogy a `docker` parancs futtatása előtt mindig jelentkezzen be a beállításjegyzékbe. Ha a token lejár, akkor a `az acr login` parancs ismételt hitelesítésével frissítheti. 

A `az acr login` és az Azure-identitások használata [szerepköralapú hozzáférést](../role-based-access-control/role-assignments-portal.md)biztosít. Bizonyos esetekben előfordulhat, hogy az Azure AD-ben a saját egyéni identitásával szeretne bejelentkezni egy beállításjegyzékbe. A szolgáltatások közötti forgatókönyvek esetében, illetve egy olyan munkacsoport szükségleteinek kezeléséhez, ahol nem kívánja kezelni az egyéni hozzáférést, az [Azure-erőforrások felügyelt identitásával](container-registry-authentication-managed-identity.md)is bejelentkezhet.

## <a name="service-principal"></a>Szolgáltatásnév

Ha egy [egyszerű szolgáltatásnevet](../active-directory/develop/app-objects-and-service-principals.md) rendel hozzá a beállításjegyzékhez, az alkalmazás vagy szolgáltatás a fej nélküli hitelesítéshez használhatja azt. Az egyszerű szolgáltatások lehetővé teszik a [szerepkörön alapuló hozzáférést](../role-based-access-control/role-assignments-portal.md) a beállításjegyzékhez, és több egyszerű szolgáltatást is hozzárendelhet egy beállításjegyzékhez. Több egyszerű szolgáltatás lehetővé teszi különböző alkalmazások eltérő hozzáférésének megadását.

A tároló-beállításjegyzékhez elérhető szerepkörök a következők:

* **AcrPull**: lekérés

* **AcrPush**: lekérés és leküldés

* **Tulajdonos**: szerepkörök lekérése, leküldése és társítása más felhasználóknak

A szerepkörök teljes listáját itt tekintheti meg: [Azure Container Registry szerepkörök és engedélyek](container-registry-roles.md).

Ha parancssori parancsfájlokat szeretne létrehozni az Azure Container registryvel való hitelesítéshez, valamint útmutatást az egyszerű szolgáltatásnév használatáról, tekintse meg a következőt: [Azure Container Registry hitelesítés az egyszerű szolgáltatásokkal](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Rendszergazdai fiók

Minden tároló-beállításjegyzék tartalmaz egy rendszergazdai felhasználói fiókot, amely alapértelmezés szerint le van tiltva. Engedélyezheti a rendszergazda felhasználóját, és kezelheti a hitelesítő adatait a Azure Portal, illetve az Azure CLI vagy más Azure-eszközök használatával.

> [!IMPORTANT]
> A rendszergazdai fiók úgy van kialakítva, hogy egyetlen felhasználó hozzáférjen a beállításjegyzékhez, főleg tesztelési célokra. A rendszergazdai fiók hitelesítő adatait nem ajánlott több felhasználó között megosztani. A rendszergazdai fiókkal hitelesítő összes felhasználó egyetlen felhasználóként jelenik meg a beállításjegyzék leküldéses és lekéréses hozzáférésével. A fiók módosítása vagy letiltása letiltja a beállításjegyzék-hozzáférést minden olyan felhasználó számára, aki hitelesítő adatait használja. A felhasználók és a szolgáltatásokhoz egyéni identitást kell használni a fej nélküli forgatókönyvek esetében.
>

A rendszergazdai fiók két jelszóval van ellátva, amelyek közül mindkettő újra létrehozhatók. Két jelszó lehetővé teszi a beállításjegyzékhez való kapcsolódást úgy, hogy egy jelszót használ a másik létrehozásakor. Ha a rendszergazdai fiók engedélyezve van, átadhatja a felhasználónevet és a jelszót a `docker login` parancshoz, amikor a rendszer az alapszintű hitelesítést kéri a beállításjegyzékben. Példa:

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
