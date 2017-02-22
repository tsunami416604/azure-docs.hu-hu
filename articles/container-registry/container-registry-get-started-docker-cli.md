---
title: "Docker-rendszerképek az Azure tároló-beállításjegyzékben | Microsoft Docs"
description: "Docker-rendszerképek leküldése és lekérése egy Azure-beli tároló beállításjegyzékbe a Docker parancssori felületével"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 64fbe43f-fdde-4c17-a39a-d04f2d6d90a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: f299cff22d00a1c765a32838647818d18f3df85d
ms.openlocfilehash: df15eebf0052aa4713263a810df605fa1016c306

---
# <a name="push-your-first-image-to-a-container-registry-using-the-docker-cli"></a>Az első rendszerkép leküldése egy tároló-beállításjegyzékbe a Docker parancssori felületével
Az Azure-beli tároló-beállításjegyzékek privát [Docker](http://hub.docker.com)-tárolórendszerképeket tárol és felügyel, hasonlóan ahhoz, ahogyan a [Docker Hub](https://hub.docker.com/) nyilvános Docker-rendszerképeket tárol. A[Docker parancssori felületével](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) végezheti a [bejelentkezést](https://docs.docker.com/engine/reference/commandline/login/), a [leküldést](https://docs.docker.com/engine/reference/commandline/push/), a [lekérést](https://docs.docker.com/engine/reference/commandline/pull/) és a tároló-beállításjegyzék további műveleteit. 

Részletes háttérinformációkért és a fogalmakkal kapcsolatban lásd: [Mi az az Azure Container Registry?](container-registry-intro.md)


> [!NOTE]
> A Container Registry jelenleg előzetes verzióban érhető el.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
* **Azure Container Registry** – Létrehozhat egy tároló-beállításjegyzéket Azure-előfizetésében. Ehhez például használhatja az [Azure Portalt](container-registry-get-started-portal.md) vagy az [Azure CLI 2.0 előzetes verzióját](container-registry-get-started-azure-cli.md).
* **A Docker parancssori felülete** – Ha szeretné helyi számítógépét Docker-gazdagépként beállítani és elérni a Docker parancssori felületének parancsait, telepítse a [Docker Engine-t](https://docs.docker.com/engine/installation/).

## <a name="log-in-to-a-registry"></a>Bejelentkezés beállításjegyzékbe
Futtassa a(z) `docker login` parancsot a tároló-beállításjegyzékbe való bejelentkezéshez a [beállításjegyzékhez tartozó hitelesítő adataival](container-registry-authentication.md).

Az alábbi példában a rendszer egy Azure Active Directory [egyszerű szolgáltatás](../active-directory/active-directory-application-objects.md) azonosítóját és jelszavát adja át. Például lehet, hogy hozzárendelt egy egyszerű szolgáltatást a beállításjegyzékhez egy automatizálási forgatókönyvhöz. 

```
docker login myregistry-contoso.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

> [!TIP]
> Ügyeljen arra, hogy megadja a teljes beállításjegyzéknevet (csak kisbetűvel). Ebben a példában ez `myregistry-contoso.azurecr.io`.

## <a name="steps-to-pull-and-push-an-image"></a>Rendszerképek leküldésének és lekérésének lépései
Az alábbi példa egy Nginx rendszerképet tölt le a nyilvános Docker Hub-beállításjegyzékből, címkézi azt a privát, Azure-beli tároló beállításjegyzékhez, leküldi a beállításjegyzékbe, majd újból lekéri.

**1. A Docker hivatalos Nginx-rendszerképének lekérése**

Először kérje le a nyilvános Nginx-rendszerképet a helyi számítógépre.

```
docker pull nginx
```
**2. A Nginx-tároló indítása**

A következő paranccsal a helyi Nginx-tároló interaktívan indítható (tehát megjelenik a Nginxből kimenete), és a rendszer a 8080-as számú port figyel. Ha leállítja, azzal eltávolítja a futó tárolót.

```
docker run -it --rm -p 8080:80 nginx
```

Lépjen a [http://localhost:8080](http://localhost:8080) helyre a futó tároló megtekintéséhez. A következőhöz hasonló képernyő jelenik meg.

![Nginx egy helyi számítógépen](./media/container-registry-get-started-docker-cli/nginx.png)

A futó tároló leállításához nyomja le a [CTRL]+[C] billentyűkombinációt.

**3. Rendszerkép aliasának létrehozása a beállításjegyzékben**

A következő parancs létrehoz egy aliast a rendszerképről a beállításjegyzékre mutató teljes elérési úttal. A példa a(z) `samples` névteret határozza meg, hogy ne legyen zsúfolt a beállításjegyzék gyökere.

```
docker tag nginx myregistry-exp.azurecr.io/samples/nginx
```  

**4. A rendszerkép leküldése a beállításjegyzékbe**

```
docker push myregistry-contoso.azurecr.io/samples/nginx
``` 

**5. A rendszerkép lekérése a beállításjegyzékből**

```
docker pull myregistry-contoso.azurecr.io/samples/nginx
``` 

**6. A Nginx-tároló elindítása a beállításjegyzékből**

```
docker run -it --rm -p 8080:80 myregistry-exp.azurecr.io/samples/nginx
```

Lépjen a [http://localhost:8080](http://localhost:8080) helyre a futó tároló megtekintéséhez.

A futó tároló leállításához nyomja le a [CTRL]+[C] billentyűkombinációt.

**6. A rendszerkép eltávolítása**

```
docker rmi myregistry-contoso.azurecr.io/samples/nginx
```



## <a name="next-steps"></a>Következő lépések
Most, hogy elsajátította az alapokat, készen áll a beállításjegyzéke használatára. Például üzembe helyezhet tárolórendszerképeket egy [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/)-fürtön.






<!--HONumber=Jan17_HO4-->


