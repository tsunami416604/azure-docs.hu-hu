---
title: Azure container registry a hitelesítéshez
description: Hitelesítési lehetőségek az Azure container registry, beleértve az Azure Active Directory rendszerbiztonsági tagok direct- és beállításjegyzék-bejelentkezési szolgáltatás.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2018
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b97a0fffa6f21e7a8b06dc406711f249310fd73
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431589"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Hitelesítés a egy privát Docker regisztrációs adatbázist

Többféleképpen is lehet egy Azure container registryt, amelyek mindegyike esetében alkalmazható egy vagy több beállításjegyzék használati forgatókönyvek a hitelesítéshez.

Egy beállításjegyzék-on keresztül bejelentkezhet [egyéni bejelentkezési](#individual-login-with-azure-ad), és az alkalmazások és tárolóvezénylőt hajthat végre felügyelet nélküli vagy "távfelügyelt" hitelesítés Azure Active Directory (Azure AD) használatával [ egyszerű szolgáltatás](#service-principal).

Az Azure Container Registry támogatja a Docker-műveletek nem hitelesített vagy névtelen hozzáférés. A nyilvános lemezképeket használhatja [Docker Hub](https://docs.docker.com/docker-hub/).

## <a name="individual-login-with-azure-ad"></a>Egyéni bejelentkezési az Azure ad-vel

Például a rendszerképek lekérése és leküldése a lemezképek a fejlesztői munkaállomáson közvetlenül, a beállításjegyzék használata során hitelesítést végezni a [az acr bejelentkezési](/cli/azure/acr?view=azure-cli-latest#az-acr-login) parancsot a [Azure CLI-vel](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Amikor bejelentkezés a következővel `az acr login`, a parancssori Felületet használja a jogkivonatot, jön létre, amikor hajtja végre `az login` zökkenőmentesen hitelesítéséhez a beállításjegyzék-munkamenet. Miután ezzel a módszerrel bejelentkezett a hitelesítő adatok-e a gyorsítótárazott és az azt követő `docker` parancsok nem igényelnek, egy felhasználónevet vagy jelszót. Ha a jogkivonat lejár, frissítheti, használatával a `az acr login` újra hitelesítse magát újra a parancsot. Használatával `az acr login` biztosít az Azure-identitások [szerepköralapú hozzáférés-](../role-based-access-control/role-assignments-portal.md).

## <a name="service-principal"></a>Szolgáltatásnév

Hozzárendelhet egy [szolgáltatásnév](../active-directory/develop/active-directory-application-objects.md) a regisztrációs adatbázisba, és az alkalmazás vagy szolgáltatás használható távfelügyelt hitelesítéshez. Az egyszerű szolgáltatások engedélyezése [szerepköralapú hozzáférés-](../role-based-access-control/role-assignments-portal.md) egy regisztrációs adatbázisba, és több szolgáltatásnevek hozzárendelhet egy regisztrációs adatbázisba. Több szolgáltatásnevek lehetővé teszik a különböző alkalmazások különböző hozzáférés meghatározásához.

A következő szerepkörök választhatók:

  * **Olvasó**: lekéréses
  * **Közreműködői**: leküldésének és lekérésének
  * **Tulajdonos**: lekéréses, push és szerepköröket hozzárendelni más felhasználókhoz

Az egyszerű szolgáltatások beállításjegyzékhez a lekérést és a egy, a következőkhöz hasonló forgatókönyveket távfelügyelt kapcsolódásának engedélyezése:

  * *Olvasó*: a beállításjegyzék vezénylési rendszerekhez, beleértve a Kubernetes, DC/OS és Docker Swarm üzemelő Tárolópéldányokat. Is olvashatók a tároló-beállításjegyzékek kapcsolódó Azure-szolgáltatások, például [AKS](../aks/index.yml), [App Service-ben](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/), és mások.

  * *Közreműködői*: folyamatos integrációs és üzembe helyezési megoldásokat, mint a Visual Studio Team Services (VSTS) vagy a Jenkins, amelyek tárolórendszerképek létrehozása és azok leküldése egy beállításjegyzék.

> [!TIP]
> Egyszerű szolgáltatás jelszava futtatásával létrehozhatja a [az ad sp reset-credentials](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-reset-credentials) parancsot.
>

Akkor is bejelentkezhet közvetlenül a szolgáltatásnévvel. Adja meg az alkalmazás Azonosítóját és jelszavát, az egyszerű szolgáltatás a `docker login` parancsot:

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Miután bejelentkezett, Docker gyorsítótárazza-e a hitelesítő adatokat, így nem kell észben az alkalmazás azonosítóját.

Telepített Docker verziójától függően előfordulhat, hogy egy biztonsági figyelmeztetést látja használatát javasolja a `--password-stdin` paraméter. Bár a paraméter használatát a cikk nem tárgyalja, javasoljuk, kövesse ezt az ajánlott eljárást. További információkért lásd: a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/) referencia parancsot.

Egyszerű szolgáltatás használatával távfelügyelt hitelesítés az ACR-be a további információkért lásd: [Azure Tárolóregisztrációs adatbázis hitelesítési szolgáltatásnevekkel](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Rendszergazdai fiók

Minden tároló-beállításjegyzékhez magában foglalja a rendszergazdai felhasználói fiókhoz, amely alapértelmezés szerint le van tiltva. Engedélyezze a rendszergazdai felhasználót, és a hozzá tartozó hitelesítő adatok kezelése a [az Azure portal](container-registry-get-started-portal.md#create-a-container-registry), vagy az Azure CLI használatával.

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
