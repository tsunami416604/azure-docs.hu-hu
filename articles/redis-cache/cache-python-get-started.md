---
title: Rövid útmutató az Azure Redis Cache-t használó Python-alkalmazás létrehozásához | Microsoft Docs
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre a Redis gyorsítótárat használó Python-alkalmazást
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: v-lincan
ms.assetid: f186202c-fdad-4398-af8c-aee91ec96ba3
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/11/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: b66df55043e4fd29f352c6e9ec3b8674800bc4be
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="quickstart-use-azure-redis-cache-with-python"></a>Rövid útmutató: Az Azure Redis Cache használata Pythonnal
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

## <a name="introduction"></a>Bevezetés

Ez a rövid útmutató ismerteti, hogyan használható a Python egy Azure Redis Cache-hez való csatlakozáshoz a gyorsítótár olvasása és írása érdekében. 

![Python-teszt befejezve](./media/cache-python-get-started/cache-python-completed.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* [Python 2 vagy Python 3-környezetben](https://www.python.org/downloads/) telepített [pip](https://pypi.org/project/pip/). 

## <a name="create-a-redis-cache-on-azure"></a>Redis Cache gyorsítótár létrehozása az Azure-ban
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>A redis-py telepítése

A [Redis-py](https://github.com/andymccurdy/redis-py) a Redis Cache Python-felülete. A *pip* nevű Python-csomag eszközzel telepítheti a redis-py csomagot. 

Az alábbi példa a Python3 *pip3* ügyfelével telepíti a redis-py csomagot a Windows 10 rendszeren emelt szintű rendszergazdai jogosultságokkal futó Visual Studio 2017 fejlesztői parancssor használatával.

    pip3 install redis

![A redis-py telepítése](./media/cache-python-get-started/cache-python-install-redis-py.png)


## <a name="read-and-write-to-the-cache"></a>Olvasás és írás a gyorsítótárban

Futtassa a Pythont, és teszteljen a gyorsítótárral a parancssorból. Cserélje le a `<Your Host Name>` és `<Your Access Key>` helyőrzőt a Redis Cache értékeivel. 

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>.redis.cache.windows.net',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

## <a name="create-a-python-script"></a>Python-szkript létrehozása

Hozzon létre egy *PythonApplication1.py* nevű új szöveges szkriptfájlt.

Adja a következő szkriptet a *PythonApplication1.py* fájlhoz, és mentse a fájlt. Ez a szkript teszteli a gyorsítótár hozzáférését. Cserélje le a `<Your Host Name>` és `<Your Access Key>` helyőrzőt a Redis Cache értékeivel. 

```python
import redis

myHostname = "<Your Host Name>.redis.cache.windows.net"
myPassword = "<Your Access Key>"

r = redis.StrictRedis(host=myHostname, port=6380,password=myPassword,ssl=True)

result = r.ping()
print("Ping returned : " + str(result))

result = r.set("Message", "Hello!, The cache is working with Python!")
print("SET Message returned : " + str(result))

result = r.get("Message")
print("GET Message returned : " + result.decode("utf-8"))

result = r.client_list()
print("CLIENT LIST returned : ") 
for c in result:
    print("id : " + c['id'] + ", addr : " + c['addr'])
```

Futtassa a szkriptet a Pythonnal.

![Python-teszt befejezve](./media/cache-python-get-started/cache-python-completed.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy egy másik oktatóanyaggal folytatja, megtarthatja és újból felhasználhatja az ebben a rövid útmutatóban létrehozott erőforrásokat.

Ha azonban befejezte az oktatóanyag mintaalkalmazásának használatát, a díjak elkerülése érdekében törölheti az ebben a rövid útmutatóban létrehozott Azure-erőforrásokat. 

> [!IMPORTANT]
> Az erőforráscsoport törlése nem visszaállítható; az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha a jelen minta üzemeltetését végző erőforrásokat egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforrásokat az erőforráscsoport törlése helyett külön-külön törölheti a megfelelő panelekről.
>

Jelentkezzen be az [Azure portálra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

A **Szűrés név alapján...** mezőbe írja be az erőforráscsoport nevét. A jelen cikk utasításai egy *TestResources* nevű erőforráscsoportot használtak. Az eredménylistában kattintson a **…** ikonra az erőforráscsoport mellett, majd kattintson az **Erőforráscsoport törlése** lehetőségre.

![Törlés](./media/cache-web-app-howto/cache-delete-resource-group.png)

A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be az erőforráscsoport nevét, és kattintson a **Törlés** gombra.

A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egy Azure Redis Cache-t használó egyszerű ASP.NET-webalkalmazás létrehozása.](./cache-web-app-howto.md)



<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png
