---
title: "Egy Azure-tárolót beállításjegyzék hitelesítéshez"
description: "Egy Azure-tárolót beállításjegyzék, például az Azure Active Directory hitelesítési beállítások a rendszerbiztonsági tagok közvetlen és a beállításjegyzék bejelentkezési szolgáltatás."
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: mmacy
tags: 
keywords: 
ms.assetid: 128a937a-766a-415b-b9fc-35a6c2f27417
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 51fb72fc3c0e9b9e261f19883820f5d7399a57ab
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
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

Ha jelentkezzen be az `az acr login`, a CLI-t használja a jogkivonatot jön létre, amikor hajtja végre `az login` akadálytalanul hitelesíteni a beállításjegyzék-munkamenet. Ha így már bejelentkezett, a hitelesítő adatok gyorsítótárazott, és az azt követő `docker` parancsok nem igényelnek, a felhasználónév vagy jelszó. Ha a jogkivonat lejár, frissítheti azt használatával a `az acr login` újra a parancsot újból hitelesítésre.

## <a name="service-principal"></a>Egyszerű szolgáltatásnév

Hozzárendelhet egy [szolgáltatás egyszerű](../active-directory/develop/active-directory-application-objects.md) a beállításjegyzéket, és az alkalmazás vagy szolgáltatás használható a távfelügyeleti hitelesítéshez. Engedélyezi szolgáltatás rendszerbiztonsági tagok [szerepköralapú hozzáférés-](../active-directory/role-based-access-control-configure.md) a beállításjegyzéket, és több szolgáltatásnevekről rendelhet a beállításjegyzékben. Több szolgáltatásnevekről lehetővé teszik a különböző alkalmazások különböző hozzáférés meghatározása.

A következő szerepkörök választhatók:

  * **Olvasó**: lekéréses
  * **A közreműködői**: lekéréses és leküldéses
  * **Tulajdonos**: lekéréses leküldéses és más felhasználók szerepkörök hozzárendelése

Szolgáltatás rendszerbiztonsági tagok a lekérést és a küldést helyzetekben, például a következő regisztrációs távfelügyeleti kapcsolódásának engedélyezése:

  * *Olvasó*: a beállításjegyzék Kubernetes, a DC/OS és a Docker Swarm vezénylési rendszerekre üzemelő Tárolópéldányokat. Akkor is is lekéréses tároló nyilvántartó a kapcsolódó Azure-szolgáltatásokhoz való például [AKS](../aks/index.yml), [App Service](../app-service/index.yml), [kötegelt](../batch/index.md), [Service Fabric](/azure/service-fabric/), és mások számára.

  * *A közreműködői*: folyamatos integrációt és telepítést megoldások, például a Visual Studio Team Services (VSTS) vagy a tároló lemezképeket, és azok leküldése a beállításjegyzék Jenkins.

> [!TIP]
> Újragenerálás egyszerű szolgáltatás jelszavának futtatásával a [alaphelyzetbe állítása-hitelesítő adatokat az ad sp](/cli/azure/ad/sp?view=azure-cli-latest#az_ad_sp_reset_credentials) parancsot.
>

Is bejelentkezhet egy egyszerű szolgáltatás közvetlenül az. Az alkalmazás Azonosítóját és jelszavát az egyszerű szolgáltatásnév az a `docker login` parancs:

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Miután bejelentkezett, Docker gyorsítótárazza a hitelesítő adatokat, így nem kell jegyezze meg az alkalmazás azonosítót.

Attól függően, hogy a Docker telepített verzióját, megjelenhet egy biztonsági figyelmeztetés használatát javasolja a `--password-stdin` paraméter. A használatával nem ez a cikk hatókörén kívül, de javasolt követően a legmegfelelőbb eljárást alkalmazza. További információkért lásd: a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/) hivatkozás parancsot.

## <a name="admin-account"></a>Rendszergazdai fiók

Minden egyes tároló beállításjegyzék magában foglalja a rendszergazda felhasználói fiókkal, amely alapértelmezés szerint le van tiltva. A felhasználót a rendszergazda engedélyezheti és kezelése a saját hitelesítő adatait a [Azure-portálon](container-registry-get-started-portal.md#create-a-container-registry), vagy az Azure parancssori felület használatával.

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

> [!IMPORTANT]
> A rendszergazdai fiók egy-egy felhasználóhoz hozzáférni a beállításjegyzékhez, különösen ha tesztelési célokra tervezték. Több felhasználó osztja meg a rendszergazdai fiók hitelesítő adatai nem ajánlott. Minden felhasználó a rendszergazdai fiókkal hitelesítése egy-egy felhasználóhoz a beállításjegyzék jelennek meg. Módosítása vagy a fiók letiltása letiltja az összes felhasználó számára a megszerzett hitelesítő adatokkal beállításjegyzék elérésének.
>

## <a name="next-steps"></a>Következő lépések

* [Leküldéses az első lemezkép az Azure parancssori felület használatával](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png