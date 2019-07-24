---
title: Hitelesítés Azure Container registryvel
description: Az Azure Container Registry hitelesítési lehetőségei, beleértve a Azure Active Directory identitással való bejelentkezést, az egyszerű szolgáltatásnév használatát és a nem kötelező rendszergazdai hitelesítő adatok használatát.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/21/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 82fe80e098ee95c09c4a1400068ab813910e0e1a
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309835"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Hitelesítés privát Docker-tároló beállításjegyzékével

Az Azure Container Registry szolgáltatással többféleképpen is végezhet hitelesítést, amelyek mindegyike egy vagy több beállításjegyzék-használati forgatókönyvre alkalmazható.

A beállításjegyzékbe közvetlenül [Egyéni bejelentkezéssel](#individual-login-with-azure-ad)is bejelentkezhet, vagy az alkalmazások és a tároló-szervezők felügyelet nélkül vagy "fej nélküli" hitelesítést végezhetnek Azure Active Directory (Azure ad) [egyszerű szolgáltatásnév](#service-principal)használatával.

## <a name="individual-login-with-azure-ad"></a>Egyéni bejelentkezés az Azure AD-vel

Ha közvetlenül dolgozik a beállításjegyzékben, például képeket húz ki és küld el egy fejlesztői munkaállomásról, a hitelesítést az az [ACR login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) paranccsal végezheti el az [Azure CLI](/cli/azure/install-azure-cli)-ben:

```azurecli
az acr login --name <acrName>
```

Amikor bejelentkezik a szolgáltatásba `az acr login`, a parancssori felület az az [login](/cli/azure/reference-index#az-login) használatával zökkenőmentesen hitelesíti a munkamenetet a beállításjegyzékben. Ha így jelentkezett be, a hitelesítő adatai gyorsítótárazva lesznek, és a munkamenetben lévő további `docker` parancsok nem igénylik a felhasználónevet vagy a jelszót. 

A beállításjegyzékhez való hozzáféréshez a által `az acr login` használt jogkivonat 1 órára érvényes, ezért javasoljuk, hogy a `docker` parancs futtatása előtt mindig jelentkezzen be a beállításjegyzékbe. Ha a token lejár, akkor a `az acr login` parancs ismételt hitelesítésével frissítheti azt. 

Az `az acr login` Azure-identitásokkal [való használata szerepköralapú hozzáférést](../role-based-access-control/role-assignments-portal.md)biztosít. Bizonyos esetekben előfordulhat, hogy a saját egyéni identitásával szeretne bejelentkezni egy beállításjegyzékbe az Azure AD-ben. A szolgáltatások közötti forgatókönyvek esetében, illetve egy olyan munkacsoport szükségleteinek kezeléséhez, ahol nem kívánja kezelni az egyéni hozzáférést, az [Azure-erőforrások felügyelt identitásával](container-registry-authentication-managed-identity.md)is bejelentkezhet.

## <a name="service-principal"></a>Egyszerű szolgáltatásnév

Ha egy [egyszerű szolgáltatásnevet](../active-directory/develop/app-objects-and-service-principals.md) rendel hozzá a beállításjegyzékhez, az alkalmazás vagy szolgáltatás a fej nélküli hitelesítéshez használhatja azt. Az egyszerű szolgáltatások lehetővé teszik a [szerepkörön alapuló hozzáférést](../role-based-access-control/role-assignments-portal.md) a beállításjegyzékhez, és több egyszerű szolgáltatást is hozzárendelhet egy beállításjegyzékhez. Több egyszerű szolgáltatás lehetővé teszi különböző alkalmazások eltérő hozzáférésének megadását.

A tároló-beállításjegyzékhez elérhető szerepkörök a következők:

* **AcrPull**: lekérés

* **AcrPush**: lekérés és leküldés

* **Tulajdonos**: szerepkörök lekérése, leküldése és társítása más felhasználóknak

A szerepkörök teljes listáját itt tekintheti meg: [Azure Container Registry szerepkörök és engedélyek](container-registry-roles.md).

Az Azure Container registryben való hitelesítéshez, illetve egy meglévő [Azure Container Registry](container-registry-auth-service-principal.md)egyszerű szolgáltatásnév használatához parancssori felületi parancsfájlok létrehozásához a szolgáltatás egyszerű alkalmazás-azonosítóját és jelszavát kell létrehozni.

Az egyszerű szolgáltatások lehetővé teszik a fej nélküli kapcsolódást egy beállításjegyzékhez a lekéréses és leküldéses forgatókönyvekben, például az alábbiak szerint:

  * *Pull*: Tárolók üzembe helyezése a beállításjegyzékből a Kubernetes, a DC/OS és a Docker Swarm rendszerbe. Lekérheti a tároló-beállításjegyzékeket a kapcsolódó Azure-szolgáltatásokra, például az [Azure Kubernetes Service](container-registry-auth-aks.md), a [Azure Container Instances](container-registry-auth-aci.md), a [app Service](../app-service/index.yml), a [Batch](../batch/index.yml), a [Service Fabric](/azure/service-fabric/)és másokra is.

  * Leküldés: Hozzon létre tároló-lemezképeket, és küldje el őket egy beállításjegyzékbe folyamatos integráció és üzembe helyezési megoldások, például az Azure-folyamatok vagy a Jenkins használatával.

Közvetlenül is bejelentkezhet egy egyszerű szolgáltatásnév használatával. A következő parancs futtatásakor interaktív módon adja meg az egyszerű szolgáltatásnév appID (username) és a jelszót, amikor a rendszer kéri. A bejelentkezési hitelesítő adatok kezelésével kapcsolatos ajánlott eljárásokért tekintse meg a [Docker login](https://docs.docker.com/engine/reference/commandline/login/) parancs referenciáját:

```
docker login myregistry.azurecr.io
```

A bejelentkezést követően a Docker gyorsítótárazza a hitelesítő adatokat, így nem kell megjegyeznünk az alkalmazás AZONOSÍTÓját.

> [!TIP]
> Egy egyszerű szolgáltatásnév jelszavát az az [ad SP reset-hitelesítőadats](/cli/azure/ad/sp?view=azure-cli-latest) parancs futtatásával lehet újból előállítani.
>

## <a name="admin-account"></a>Rendszergazdai fiók

Minden tároló-beállításjegyzék tartalmaz egy rendszergazdai felhasználói fiókot, amely alapértelmezés szerint le van tiltva. Engedélyezheti a rendszergazda felhasználóját, és kezelheti a hitelesítő adatait a Azure Portal, illetve az Azure CLI vagy más Azure-eszközök használatával.

> [!IMPORTANT]
> A rendszergazdai fiók úgy van kialakítva, hogy egyetlen felhasználó hozzáférjen a beállításjegyzékhez, főleg tesztelési célokra. A rendszergazdai fiók hitelesítő adatait nem ajánlott több felhasználóval megosztani. A rendszergazdai fiókkal hitelesítő összes felhasználó egyetlen felhasználóként jelenik meg a beállításjegyzék leküldéses és lekéréses hozzáférésével. A fiók módosítása vagy letiltása letiltja a beállításjegyzék-hozzáférést minden olyan felhasználó számára, aki hitelesítő adatait használja. A felhasználók és a szolgáltatásokhoz egyéni identitást kell használni a fej nélküli forgatókönyvek esetében.
>

A rendszergazdai fiók két jelszóval van ellátva, amelyek közül mindkettő újra létrehozhatók. Két jelszó lehetővé teszi a beállításjegyzékhez való kapcsolódást úgy, hogy egy jelszót használ a másik létrehozásakor. Ha a rendszergazdai fiók engedélyezve van, a felhasználónevet és a jelszót is átadhatja `docker login` a parancsnak, amikor a rendszer az alapszintű hitelesítést kéri a beállításjegyzékben. Példa:

```
docker login myregistry.azurecr.io 
```


Ha engedélyezni szeretné a rendszergazda felhasználót egy meglévő beállításjegyzékben, használja `--admin-enabled` az az [ACR Update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) parancs paraméterét az Azure CLI-ben:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Engedélyezheti a rendszergazda felhasználót a Azure Portal a beállításjegyzékben navigálva, a **hozzáférési kulcsok** elemre kattintva a **Beállítások**területen, majd a **rendszergazda felhasználó**területen **engedélyezheti** .

![Rendszergazdai felhasználói felület engedélyezése a Azure Portal][auth-portal-01]

## <a name="next-steps"></a>További lépések

* [Az első rendszerkép leküldése az Azure CLI használatával](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
