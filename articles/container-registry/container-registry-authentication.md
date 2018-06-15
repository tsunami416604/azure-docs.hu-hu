---
title: Egy Azure-tárolót beállításjegyzék hitelesítéshez
description: Egy Azure-tárolót beállításjegyzék, például az Azure Active Directory hitelesítési beállítások a rendszerbiztonsági tagok közvetlen és a beállításjegyzék bejelentkezési szolgáltatás.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2018
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c8fcebae21d73db75e19bd1091faa8f389f0ba40
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32165513"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>A hitelesítést egy titkos Docker-tároló beállításjegyzék

Több módon is hitelesítik magukat az Azure-tárolót beállításjegyzék, amelyek egy-egy vagy több beállításjegyzék használati forgatókönyvek alkalmazandó.

A beállításjegyzék közvetlen keresztül bejelentkezhet [egyedi bejelentkezési](#individual-login-with-azure-ad), és az alkalmazások és a tároló orchestrators képes felügyelet nélküli, vagy "távfelügyeleti," hitelesítést végezni egy Azure Active Directory (Azure AD) segítségével [ szolgáltatás egyszerű](#service-principal).

Az Azure tároló beállításjegyzék nem támogatja a nem hitelesített Docker műveletek vagy névtelen hozzáférés. Nyilvános képek használhatja [Docker Hub](https://docs.docker.com/docker-hub/).

## <a name="individual-login-with-azure-ad"></a>Egyéni bejelentkezési az Azure ad szolgáltatással

Az használatakor a beállításjegyzék közvetlenül, például a lemezképeket húzza és lemezképek terjesztése a fejlesztő munkaállomás a hitelesítéshez használja a [az acr bejelentkezési](/cli/azure/acr?view=azure-cli-latest#az_acr_login) parancsot a [Azure CLI](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Ha jelentkezzen be az `az acr login`, a CLI-t használja a jogkivonatot jön létre, amikor hajtja végre `az login` akadálytalanul hitelesíteni a beállításjegyzék-munkamenet. Ha így már bejelentkezett, a hitelesítő adatok gyorsítótárazott, és az azt követő `docker` parancsok nem igényelnek, a felhasználónév vagy jelszó. Ha a jogkivonat lejár, frissítheti azt használatával a `az acr login` újra a parancsot újból hitelesítésre. Használatával `az acr login` Azure identitások nyújt [szerepkörön alapuló hozzáférés](../role-based-access-control/role-assignments-portal.md).

## <a name="service-principal"></a>Szolgáltatásnév

Hozzárendelhet egy [szolgáltatás egyszerű](../active-directory/develop/active-directory-application-objects.md) a beállításjegyzéket, és az alkalmazás vagy szolgáltatás használható a távfelügyeleti hitelesítéshez. Engedélyezi szolgáltatás rendszerbiztonsági tagok [szerepköralapú hozzáférés-](../role-based-access-control/role-assignments-portal.md) a beállításjegyzéket, és több szolgáltatásnevekről rendelhet a beállításjegyzékben. Több szolgáltatásnevekről lehetővé teszik a különböző alkalmazások különböző hozzáférés meghatározása.

A következő szerepkörök választhatók:

  * **Olvasó**: lekéréses
  * **A közreműködői**: lekéréses és leküldéses
  * **Tulajdonos**: lekéréses leküldéses és más felhasználók szerepkörök hozzárendelése

Szolgáltatás rendszerbiztonsági tagok a lekérést és a küldést helyzetekben, például a következő regisztrációs távfelügyeleti kapcsolódásának engedélyezése:

  * *Olvasó*: a beállításjegyzék Kubernetes, a DC/OS és a Docker Swarm vezénylési rendszerekre üzemelő Tárolópéldányokat. Akkor is is lekéréses tároló nyilvántartó a kapcsolódó Azure-szolgáltatásokhoz való például [AKS](../aks/index.yml), [App Service](../app-service/index.yml), [kötegelt](../batch/index.yml), [Service Fabric](/azure/service-fabric/), és mások számára.

  * *A közreműködői*: folyamatos integrációt és telepítést megoldások, például a Visual Studio Team Services (VSTS) vagy a tároló lemezképeket, és azok leküldése a beállításjegyzék Jenkins.

> [!TIP]
> Újragenerálás egyszerű szolgáltatás jelszavának futtatásával a [alaphelyzetbe állítása-hitelesítő adatokat az ad sp](/cli/azure/ad/sp?view=azure-cli-latest#az_ad_sp_reset_credentials) parancsot.
>

Is bejelentkezhet egy egyszerű szolgáltatás közvetlenül az. Az alkalmazás Azonosítóját és jelszavát az egyszerű szolgáltatásnév az a `docker login` parancs:

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Miután bejelentkezett, Docker gyorsítótárazza a hitelesítő adatokat, így nem kell jegyezze meg az alkalmazás azonosítót.

Attól függően, hogy a Docker telepített verzióját, megjelenhet egy biztonsági figyelmeztetés használatát javasolja a `--password-stdin` paraméter. Bár a paraméter használatát a cikk nem tárgyalja, javasoljuk, kövesse ezt az ajánlott eljárást. További információkért lásd: a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/) hivatkozás parancsot.

Az egyszerű szolgáltatás használatával az ACR távfelügyeleti hitelesítés további információkért lásd: [szolgáltatásnevekről Azure tároló beállításjegyzék hitelesítés](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Rendszergazdai fiók

Minden egyes tároló beállításjegyzék magában foglalja a rendszergazda felhasználói fiókkal, amely alapértelmezés szerint le van tiltva. A felhasználót a rendszergazda engedélyezheti és kezelése a saját hitelesítő adatait a [Azure-portálon](container-registry-get-started-portal.md#create-a-container-registry), vagy az Azure parancssori felület használatával.

> [!IMPORTANT]
> A rendszergazdai fiók egy-egy felhasználóhoz hozzáférni a beállításjegyzékhez, különösen ha tesztelési célokra tervezték. Több felhasználó osztja meg a rendszergazdai fiók hitelesítő adatai nem ajánlott. A beállításjegyzék leküldéses és lekéréses hozzáférést egy felhasználó hitelesíti a rendszergazdai fiókkal minden felhasználó jelennek meg. Módosítása vagy a fiók letiltása letiltja az összes felhasználó számára a megszerzett hitelesítő adatokkal beállításjegyzék elérésének. Egyedi azonosító a felhasználók és szolgáltatásnevekről távfelügyeleti forgatókönyvek esetén ajánlott.
>

A rendszergazdai fiók kerül a két jelszavakkal, amelyek mindegyikét helyreállíthatja. Két jelszavak lehetővé teszik a beállításjegyzék kapcsolatot tart egy jelszó használatával, amíg a másikat újragenerálja. Ha a rendszergazdai fiókon engedélyezve van, a felhasználónevet és vagy jelszót átadhatók a `docker login` parancsot az egyszerű hitelesítés a beállításjegyzékhez. Példa:

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

Ebben az esetben Docker javasolja, hogy a `--password-stdin` paraméter a parancssorban a biztonság fokozása érdekében a parancskimenetnél helyett. Azt is megadhatja, csak a felhasználónév, nélkül `-p`, és írja be a jelszót, amikor a rendszer kéri.

Ahhoz, hogy a felhasználót a rendszergazda egy meglévő beállításjegyzék, használhatja a `--admin-enabled` paramétere a [az acr frissítés](/cli/azure/acr?view=azure-cli-latest#az_acr_update) az Azure parancssori felület parancsot:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Engedélyezheti a felhasználót a rendszergazda az Azure portálon lépjen a beállításjegyzék kiválasztásával **hívóbetűk** alatt **beállítások**, majd **engedélyezése** alatt **rendszergazda felhasználói**.

![Engedélyezze a felhasználót a rendszergazda felhasználói felület az Azure-portálon][auth-portal-01]

## <a name="next-steps"></a>További lépések

* [Leküldéses az első lemezkép az Azure parancssori felület használatával](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
