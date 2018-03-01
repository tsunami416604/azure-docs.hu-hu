---
title: "Rövid útmutató – Privát Docker-tárolójegyzék létrehozása az Azure-ban az Azure Portallal"
description: "Gyorsan megismerheti egy privát Docker-tárolójegyzék az Azure Portallal való létrehozásának módját."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 12/06/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 1a4c5b365b93b30987ff6541aba762cbf8a4b7a5
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-container-registry-using-the-azure-portal"></a>Tároló-beállításjegyzék létrehozása az Azure Portalon

Az Azure-beli tároló-beállításjegyzék egy privát Docker-tárolójegyzék az Azure-ban, amelyben tárolhatja és kezelheti privát Docker-tárolóinak rendszerképeit. Ebben a rövid útmutatóban létrehozhat egy tároló-beállításjegyzéket az Azure Portallal.

A rövid útmutató elvégzéséhez a Docker helyi telepítése szükséges. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Válassza az **Erőforrás létrehozása** > **Tárolók** > **Azure Container Registry** elemet.

![Tároló-beállításjegyzék létrehozása az Azure Portalon][qs-portal-01]

Adjon meg értékeket az **Adatbázis neve** és **Erőforráscsoport** mezőkben. A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Hozzon létre egy `myResourceGroup` nevű új erőforráscsoportot, majd a **Termékváltozat** mezőben válassza az „Alapszintű” lehetőséget. Kattintson a **Létrehozás** elemre az ACR-példány üzembe helyezéséhez.

![Tároló-beállításjegyzék létrehozása az Azure Portalon][qs-portal-03]

Ebben a rövid útmutatóban egy *Alapszintű* beállításjegyzéket hozunk létre. Az Azure Container Registry több különböző termékváltozatban érhető el, amelyek rövid leírása az alábbi táblázatban található. Bővebb részletekért lásd a [Container Registry termékváltozatait][container-registry-skus] ismertető cikket.

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Amikor az **Üzembe helyezés sikeres** üzenet megjelenik, válassza ki a tároló-beállításjegyzéket a portálon, majd kattintson a **Hozzáférési kulcsok** elemre.

![Tároló-beállításjegyzék létrehozása az Azure Portalon][qs-portal-05]

A **Rendszergazdai felhasználó** elem alatt válassza az **Engedélyezés** lehetőséget. Jegyezze fel a következő értékeket:

* Bejelentkezési kiszolgáló
* Felhasználónév
* jelszó

Ezekre az értékekre a következő lépésekben, a beállításjegyzékben a Docker parancssori felülettel történő munka során lesz szüksége.

![Tároló-beállításjegyzék létrehozása az Azure Portalon][qs-portal-06]

## <a name="log-in-to-acr"></a>Jelentkezzen be az ACR-be

A tárolórendszerképek mozgatásához először be kell jelentkeznie az ACR-példányba. Ehhez használja a [docker login][docker-login] parancsot. Cserélje le a *felhasználónév*, *jelszó*, és *bejelentkezési kiszolgáló* értékeket az előző lépésben feljegyzett értékekre.

```bash
docker login --username <username> --password <password> <login server>
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött. Előfordulhat, hogy megjelenik egy biztonsági figyelmeztetés, amely a `--password-stdin` paraméter használatát javasolja. Bár a paraméter használatát a cikk nem tárgyalja, javasoljuk, kövesse ezt az ajánlott eljárást. További információkért tekintse át a [docker login][docker-login] parancs leírását.

## <a name="push-image-to-acr"></a>Rendszerkép leküldése az ACR-be

Ahhoz, hogy rendszerképet tudjon küldeni az Azure Container Registry-be, először szüksége van egy rendszerképre. Ha szükséges, futtassa a következő parancsot meglévő rendszerkép lekéréshez a Docker Hubból.

```bash
docker pull microsoft/aci-helloworld
```

Mielőtt leküldi a rendszerképet a beállításjegyzékbe, fel kell címkéznie az ACR bejelentkezési kiszolgáló nevével. Címkézze fel a rendszerképet a [docker tag][docker-tag] parancs használatával. Cserélje le a *bejelentkezési kiszolgáló* értéket a korábban feljegyzett bejelentkezési kiszolgáló nevére.

```
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

Végül a [docker push][docker-push] paranccsal küldje le a rendszerképet az ACR-példányba. Cserélje le a *bejelentkezési kiszolgáló* értéket ACR-példánya bejelentkezési kiszolgálójának nevére.

```
docker push <login server>/aci-helloworld:v1
```

A sikeres `docker push` parancs kimenete ehhez hasonló:

```
The push refers to a repository [uniqueregistryname.azurecr.io/aci-helloworld]
7c701b1aeecd: Pushed
c4332f071aa2: Pushed
0607e25cc175: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:f2867748615cc327d31c68b1172cc03c0544432717c4d2ba2c1c2d34b18c62ba size: 1577
```

## <a name="list-container-images"></a>Tárolórendszerképek listázása

Az ACR-példányában található rendszerképek listázásához navigáljon a beállításjegyzékhez a portálon, válassza az **Adattárak** lehetőséget, majd válassza ki a `docker push` segítségével létrehozott adattárat.

Ebben a példában az **aci-helloworld** adattárat választjuk; a **CÍMKÉK** alatt látható a `v1` címkével ellátott rendszerkép.

![Tároló-beállításjegyzék létrehozása az Azure Portalon][qs-portal-09]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a **myResourceGroup** erőforráscsoportot. Ezzel törli az erőforráscsoportot, az ACR-példányt és az összes tárolórendszerképet.

![Tároló-beállításjegyzék létrehozása az Azure Portalon][qs-portal-08]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container Registry tárolóregisztrációs adatbázist az Azure Portalon. Ha az Azure Container Registry és az Azure Container Instances együttes használatának módját szeretné megismerni, folytassa az Azure Container Instances oktatóanyagával.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyagai][container-instances-tutorial-prepare-app]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-04]: ./media/container-registry-get-started-portal/qs-portal-04.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-06]: ./media/container-registry-get-started-portal/qs-portal-06.png
[qs-portal-07]: ./media/container-registry-get-started-portal/qs-portal-07.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
