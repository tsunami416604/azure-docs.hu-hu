---
title: Azure container registry a hitelesítéshez
description: Hitelesítési beállítások az Azure container registry, beleértve a bejelentkezés Azure Active Directory-identitással, szolgáltatásnevek és választható rendszergazdai hitelesítő adatokkal.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/21/2018
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 665ceabe062fce454db377a384b1d12ba6868c40
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54851725"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Hitelesítés a egy privát Docker regisztrációs adatbázist

Többféleképpen is lehet egy Azure container registryt, amelyek mindegyike esetében alkalmazható egy vagy több beállításjegyzék használati forgatókönyvek a hitelesítéshez.

Egy beállításjegyzék-on keresztül bejelentkezhet [egyéni bejelentkezési](#individual-login-with-azure-ad), vagy az alkalmazások és tárolóvezénylőt hajthat végre felügyelet nélküli vagy "távfelügyelt" hitelesítés Azure Active Directory (Azure AD) használatával [ egyszerű szolgáltatás](#service-principal).

Az Azure Container Registry támogatja a Docker-műveletek nem hitelesített vagy névtelen hozzáférés. A nyilvános lemezképeket használhatja [Docker Hub](https://docs.docker.com/docker-hub/).

## <a name="individual-login-with-azure-ad"></a>Egyéni bejelentkezési az Azure ad-vel

Például a képek lekérése és leküldése a képek egy fejlesztői munkaállomáson közvetlenül, a beállításjegyzék használata során hitelesítést végezni a [az acr bejelentkezési](/cli/azure/acr?view=azure-cli-latest#az-acr-login) parancsot a [Azure CLI-vel](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Amikor bejelentkezés a következővel `az acr login`, a parancssori Felületet használja a jogkivonatot, jön létre, amikor hajtja végre [az bejelentkezési](/cli/azure/reference-index#az-login) zökkenőmentesen hitelesítéséhez a beállításjegyzék-munkamenet. Miután ezzel a módszerrel bejelentkezett a hitelesítő adatok-e a gyorsítótárazott és az azt követő `docker` parancsok nem igényelnek, egy felhasználónevet vagy jelszót. Ha a jogkivonat lejár, frissítheti, használatával a `az acr login` újra hitelesítse magát újra a parancsot. Használatával `az acr login` biztosít az Azure-identitások [szerepköralapú hozzáférés-](../role-based-access-control/role-assignments-portal.md).

Bizonyos esetekben érdemes lehet, hogy jelentkezzen be a beállításjegyzék a saját egyéni identitás az Azure AD-ben. Szolgáltatások közötti forgatókönyvek esetén, vagy nem kívánja az egyes hozzáférés kezelése a munkacsoport igényeinek kezeléséhez is bejelentkezhet a következővel egy [-identitás az Azure-erőforrások](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Szolgáltatásnév

Ha egy [szolgáltatásnév](../active-directory/develop/app-objects-and-service-principals.md) a regisztrációs adatbázisba, az alkalmazás vagy szolgáltatás használhatja azt hitelesítésre távfelügyelt. Az egyszerű szolgáltatások engedélyezése [szerepköralapú hozzáférés-](../role-based-access-control/role-assignments-portal.md) egy regisztrációs adatbázisba, és több szolgáltatásnevek hozzárendelhet egy regisztrációs adatbázisba. Több szolgáltatásnevek lehetővé teszik a különböző alkalmazások különböző hozzáférés meghatározásához.

Egy tároló-beállításjegyzékhez a következő szerepkörök a következők:

* **AcrPull**: lekéréses

* **AcrPush**: leküldésének és lekérésének

* **Tulajdonos**: lekéréses, push és szerepköröket hozzárendelni más felhasználókhoz

Szerepkörök teljes listáját lásd: [Azure Container Registry-szerepkörökről és engedélyekről](container-registry-roles.md).

CLI-szkriptek a szolgáltatás egyszerű alkalmazás azonosítója és a egy Azure container registrybe való hitelesítéshez használt jelszót hozzon létre, vagy egy meglévő egyszerű szolgáltatást használja, lásd: [Azure Tárolóregisztrációs adatbázis hitelesítési szolgáltatásnevekkel](container-registry-auth-service-principal.md).

A szolgáltatásnevek lekéréses és a leküldéses helyzetek, például a következő jegyzékből távfelügyelt csatlakozásának engedélyezéséhez:

  * *Lekéréses*: Vezénylési rendszerekhez, beleértve a Kubernetes, DC/OS és Docker Swarm-beállításjegyzékből tárolók üzembe helyezése. Is olvashatók a tároló-beállításjegyzékek kapcsolódó Azure-szolgáltatások, például [Azure Kubernetes Service](container-registry-auth-aks.md), [Azure Container Instances](container-registry-auth-aci.md), [App Service-ben](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/), és másokkal.

  * *Leküldéses*: Tárolórendszerképek létrehozása, és azok leküldése egy beállításjegyzéket, folyamatos integrációs és üzembe helyezési megoldások, mint az Azure-folyamatok vagy a Jenkins használatával.

Akkor is bejelentkezhet közvetlenül a szolgáltatásnévvel. Adja meg az alkalmazás Azonosítóját és jelszavát, az egyszerű szolgáltatás a `docker login` parancsot:

```
docker login myregistry.azurecr.io -u <SP_APP_ID> -p <SP_PASSWD>
```

Miután bejelentkezett, Docker gyorsítótárazza-e a hitelesítő adatokat, így nem kell észben az alkalmazás azonosítóját.

Telepített Docker verziójától függően előfordulhat, hogy egy biztonsági figyelmeztetést látja használatát javasolja a `--password-stdin` paraméter. Bár a paraméter használatát a cikk nem tárgyalja, javasoljuk, kövesse ezt az ajánlott eljárást. További információkért lásd: a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/) referencia parancsot.

> [!TIP]
> Egyszerű szolgáltatás jelszava futtatásával létrehozhatja a [az ad sp reset-credentials](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-reset-credentials) parancsot.
>

## <a name="admin-account"></a>Rendszergazdai fiók

Minden tároló-beállításjegyzékhez magában foglalja a rendszergazdai felhasználói fiókhoz, amely alapértelmezés szerint le van tiltva. Engedélyezze a rendszergazdai felhasználót, és a hozzá tartozó hitelesítő adatok kezelése a [az Azure portal](container-registry-get-started-portal.md#create-a-container-registry), vagy az Azure CLI-vel vagy más Azure-eszközök használatával.

> [!IMPORTANT]
> A rendszergazdai fiók egy egyetlen felhasználó hozzáférni a beállításjegyzékhez, elsősorban a tesztelési célokra tervezték. A rendszergazdai fiók hitelesítő adatai több felhasználókkal való megosztás nem ajánlott. Egyetlen felhasználó lekérést és a beállításjegyzék-hozzáféréssel rendelkező összes felhasználót a rendszergazdai fiókkal való hitelesítés közben jelennek meg. Módosítja vagy letiltja a fiókját, azzal letiltja az összes felhasználó számára a hitelesítő adatok használata a beállításjegyzék elérésével. Egyéni identitás-felhasználók és az egyszerű szolgáltatások távfelügyelt forgatókönyvek esetén ajánlott.
>

A rendszergazdai fiók kerül a két jelszavakkal, amelyek helyreállíthatja. Két jelszó-használatával hozza létre újra a másik egy jelszót a beállításjegyzék kapcsolat fenntartását teszi lehetővé. Ha a rendszergazdai fiókon engedélyezve van, a felhasználónevet és jelszót vagy átadható a `docker login` parancsot az egyszerű hitelesítést a regisztrációs adatbázisba. Példa:

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

Ismét Docker javasolja, hogy a `--password-stdin` paraméter helyett azokat a biztonság fokozása érdekében a parancssorban megadja. Csak a felhasználónév is megadhat nélkül `-p`, és adja meg a jelszót, amikor a rendszer kéri.

Ahhoz, hogy a rendszergazdai felhasználó meglévő beállításjegyzékhez, használhatja a `--admin-enabled` paraméterében a [az acr update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) parancsot az Azure CLI-ben:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Engedélyezheti a rendszergazdai felhasználó, az Azure Portalon lépjen a beállításjegyzék kiválasztásával **hozzáférési kulcsok** alatt **beállítások**, majd **engedélyezése** alatt **rendszergazda felhasználói**.

![Engedélyezze a rendszergazdai felhasználó felhasználói felület az Azure Portalon][auth-portal-01]

## <a name="next-steps"></a>További lépések

* [Az Azure CLI-vel az első rendszerkép leküldése](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
