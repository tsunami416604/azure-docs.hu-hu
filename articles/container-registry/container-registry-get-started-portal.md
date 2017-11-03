---
title: "Gyors üzembe helyezés – hozzon létre egy saját Docker beállításjegyzék az Azure-ban az Azure-portálon"
description: "Gyorsan bemutatják, hogyan hozzon létre egy saját Docker-tároló beállításjegyzék az Azure-portálon."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 514fa3490e480647f0923c99bd9606a3726d4d30
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="create-a-container-registry-using-the-azure-portal"></a>Tároló-beállításjegyzék létrehozása az Azure Portalon

Egy Azure-tárolót beállításjegyzék egy titkos Docker beállításjegyzék, amelyben tárolni, és a titkos Docker-tároló lemezképek kezelése az Azure-ban. A gyors üzembe helyezés létrehoz egy tároló beállításjegyzék az Azure portálon.

A gyors üzembe helyezés befejezése Docker helyileg telepíteni kell rendelkeznie. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) vagy [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszeren.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure portálon, a https://portal.azure.com.

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Válassza ki **új** > **tárolók** > **Azure tároló beállításjegyzék**.

![Egy tároló beállításjegyzék létrehozása az Azure-portálon][qs-portal-01]

Adja meg az értékeket **beállításjegyzék neve** és **erőforráscsoport**. A neve kell lenniük az Azure, és 5-50 alfanumerikus karaktereket tartalmazhat. Hozzon létre egy új erőforráscsoportot nevű `myResourceGroup`, és a **SKU**, válassza ki a "Basic". Válassza ki **létrehozása** az ACR-példány telepítése.

![Egy tároló beállításjegyzék létrehozása az Azure-portálon][qs-portal-03]

A gyors üzembe helyezés, hogy hozzon létre egy *alapvető* beállításjegyzék. Azure tároló beállításjegyzék több különböző Termékváltozatai, a következő táblázat ismerteti a röviden érhető el. Minden egyes kiterjesztett részletekért lásd: [tároló beállításjegyzék termékváltozatok](container-registry-skus.md).

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Ha a **KözpontiTelepítés sikerült** üzenet jelenik meg, a tároló beállításjegyzék kiválasztása a portálon, majd válasszon **hívóbetűk**.

![Egy tároló beállításjegyzék létrehozása az Azure-portálon][qs-portal-05]

A **rendszergazdai jogú felhasználó**, jelölje be **engedélyezése**. Jegyezze fel a következő értékeket:

* Bejelentkezési kiszolgáló
* Felhasználónév
* jelszó

Ezeket az értékeket az alábbi lépések segítségével a beállításjegyzéket a Docker parancssori felület használata közben.

![Egy tároló beállításjegyzék létrehozása az Azure-portálon][qs-portal-06]

## <a name="log-in-to-acr"></a>Jelentkezzen be ACR

A tárolórendszerképek mozgatásához először be kell jelentkeznie az ACR-példányba. Ehhez használja a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/) parancsot. Cserélje le a *felhasználónév*, *jelszó*, és *bejelentkezési kiszolgáló* érték helyére azokat az előző lépésben feljegyzett.

```bash
docker login --username <username> --password <password> <login server>
```

A parancs visszaadja `Login Succeeded` amint befejeződött. Is láthatja a biztonsági figyelmeztetés használatát javasolja a `--password-stdin` paraméter. A használatával nem ez a cikk hatókörén kívül, de javasolt követően a legmegfelelőbb eljárást alkalmazza. Tekintse meg a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/) parancs további információt.

## <a name="push-image-to-acr"></a>ACR leküldéses lemezképet

Lemezkép leküldése a Azure tároló beállításjegyzék, először egy lemezképet kell rendelkeznie. Ha szükséges, a következő parancsot egy meglévő lemezképet lekérés a Docker központ.

```bash
docker pull microsoft/aci-helloworld
```

A kép leküldése a beállításjegyzék, mielőtt címkével kell ellátnia ACR bejelentkezési server nevű kép. A lemezkép használatával címkét a [docker címke](https://docs.docker.com/engine/reference/commandline/tag/) parancsot. Cserélje le *bejelentkezési kiszolgáló* a bejelentkezési kiszolgálónév, korábban rögzített.

```
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

Végül [docker leküldéses](https://docs.docker.com/engine/reference/commandline/push/) a kép leküldéses ACR példányához. Cserélje le *bejelentkezési kiszolgáló* ACR példány bejelentkezési kiszolgáló nevével.

```
docker push <login server>/aci-helloworld:v1
```

Egy sikeres kimenetét `docker push` parancs hasonlít:

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

Listát a lemezképeket a ACR példányát, keresse meg a portálon, és válasszon a beállításjegyzék **Tárházak**, majd válassza ki a létrehozott tárházba `docker push`.

Ebben a példában azt válassza ki a **aci-helloworld** tárház, és láthatja a `v1`-címkézett lemezkép **CÍMKÉK**.

![Egy tároló beállításjegyzék létrehozása az Azure-portálon][qs-portal-09]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, törölje a **myResourceGroup** erőforráscsoportot. Ezzel törli az erőforráscsoportot, ACR példány és az összes tároló-lemezképeket.

![Egy tároló beállításjegyzék létrehozása az Azure-portálon][qs-portal-08]

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezés, az az Azure-tároló beállításjegyzék az Azure parancssori felület segítségével létrehozott. Ha azt szeretné, Azure tároló beállításjegyzék használatát Azure tároló osztályt, továbbra is az Azure-tároló példányok oktatóanyag.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyagai](../container-instances/container-instances-tutorial-prepare-app.md)

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