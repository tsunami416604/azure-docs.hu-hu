---
title: "Egy Azure-tárolót beállításjegyzék hitelesítéshez |} Microsoft Docs"
description: "Bejelentkezés az Azure-tárolót beállításjegyzék, az Azure Active Directory szolgáltatás egyszerű vagy egy rendszergazdai fiók használatával"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 128a937a-766a-415b-b9fc-35a6c2f27417
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9d7d2ae0e9b1f7850332d151d78a4a5fdb013777
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>A hitelesítést egy titkos Docker-tároló beállításjegyzék
Egy Azure-tárolót beállításjegyzék lemezképeit tároló használatához jelentkezzen be a `docker login` parancsot. Segítségével bejelentkezhet egy  **[Azure Active Directory szolgáltatás egyszerű](../active-directory/active-directory-application-objects.md)**  vagy a beállításjegyzék-specifikus **rendszergazdai fiók**. A cikkben az identitások további információkra.

## <a name="service-principal"></a>Egyszerű szolgáltatásnév

Rendelje hozzá egy egyszerű szolgáltatást a beállításjegyzékben, és a Docker egyszerű hitelesítést használni. Legtöbb esetben egy egyszerű szolgáltatás használata ajánlott. Az alkalmazás Azonosítóját és jelszavát az egyszerű szolgáltatásnév az a `docker login` parancs, a következő példában látható módon:

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Miután bejelentkezett, Docker gyorsítótárazza a hitelesítő adatokat, így nem kell jegyezze meg az alkalmazás azonosítót.

> [!TIP]
> Ha azt szeretné, a jelszót egyszerű szolgáltatás futtatásával helyreállíthatók a `az ad sp reset-credentials` parancsot.
>

Engedélyezi szolgáltatás rendszerbiztonsági tagok [szerepkörön alapuló hozzáférés](../active-directory/role-based-access-control-configure.md) a beállításjegyzékhez. A következő szerepkörök választhatók:
  * (Csak hozzáférésre lekéréses)-olvasó.
  * Közreműködő (lekérési és leküldési).
  * Tulajdonos (lekéréses leküldéses és más felhasználók szerepkörök hozzárendelése).

Névtelen hozzáférés az Azure-tároló nyilvántartó nem érhető el. A nyilvános képek használhatják [Docker Hub](https://docs.docker.com/docker-hub/).

A beállításjegyzék, amely lehetővé teszi a különböző felhasználók vagy alkalmazások hozzáférés adható meg több szolgáltatásnevekről rendelhet. Szolgáltatás rendszerbiztonsági tagoknak is egy fejlesztői vagy DevOps forgatókönyvek használhatók, mint az alábbi példák a beállításjegyzék "távfelügyeleti" kapcsolódásának engedélyezése:

  * Vezénylési rendszerekre, beleértve a DC/OS, Docker Swarm és Kubernetes a beállításjegyzékből üzemelő tárolópéldányokat. Tároló nyilvántartó kapcsolódó Azure-szolgáltatásokhoz való is például lekéréses [Tárolószolgáltatás](../container-service/index.yml), [App Service](../app-service/index.yml), [kötegelt](../batch/index.md), [Service Fabric](/azure/service-fabric/), stb.

  * Folyamatos integrációt és telepítést megoldások (például a Visual Studio Team Services vagy Jenkins) tároló lemezképeket, és azok leküldése a beállításjegyzékben.


## <a name="admin-account"></a>Rendszergazdai fiók
Az egyes beállításjegyzék hoz létre rendszergazdai fiók jön létre automatikusan. Alapértelmezés szerint a fiók le van tiltva, de engedélyezheti és beállíthatja a hitelesítő adatok, például keresztül a [portal](container-registry-get-started-portal.md#create-a-container-registry) használatával vagy a [Azure CLI 2.0 parancsok](container-registry-get-started-azure-cli.md#create-a-container-registry). Minden rendszergazdai fiók kerül a két jelszavakkal, amelyek mindegyikét helyreállíthatja. A két jelszó lehetővé teszik egy jelszó használatával, amíg a többi jelszó újragenerálja a beállításjegyzék kapcsolatok karbantartása. Ha a fiók engedélyezve van, a felhasználónév és jelszó vagy átadhatók a `docker login` parancsot az egyszerű hitelesítés a beállításjegyzékhez. Példa:

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

> [!IMPORTANT]
> A rendszergazdai fiók egy-egy felhasználóhoz hozzáférni a beállításjegyzékhez, főleg tesztelési célokra tervezték. Nem ajánlott a rendszergazdai fiók hitelesítő adatait, más felhasználók között megosztható. Minden felhasználó egy-egy felhasználóhoz a beállításjegyzék jelennek meg. Módosítása vagy a fiók letiltása letiltja az összes felhasználó számára a hitelesítő adatok használata a beállításjegyzék elérésének.
>

### <a name="next-steps"></a>Következő lépések
* [Az első kép a Docker parancssori felület használatával leküldéses](container-registry-get-started-docker-cli.md).
* A tároló beállításjegyzék Preview hitelesítéssel kapcsolatos további információkért lásd: a [blogbejegyzés](https://blogs.msdn.microsoft.com/stevelasker/2016/11/17/azure-container-registry-user-accounts/).
