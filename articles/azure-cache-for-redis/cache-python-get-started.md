---
title: 'Gyors útmutató: az Azure cache-t használó Python-alkalmazás létrehozása a Redis-hez'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre olyan Python-alkalmazást, amely az Azure cache-t használja a Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: v-lincan
ms.assetid: f186202c-fdad-4398-af8c-aee91ec96ba3
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 05/11/2018
ms.author: yegu
ms.custom:
- mvc
- seo-python-october2019
ms.openlocfilehash: 87c22d3497765fca6f0dcae445152e6e2923510e
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329870"
---
# <a name="quickstart-use-azure-cache-for-redis-with-python"></a>Gyors útmutató: az Azure cache használata a Redis és a Python használatával

Ebben a cikkben egy Python-alkalmazásba foglalja bele az Azure cache-t a Redis-be, hogy hozzáférhessen egy biztonságos, dedikált gyorsítótárhoz, amely az Azure-on belül bármely alkalmazásból elérhető.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Python 2 vagy 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-on-azure"></a>Azure cache létrehozása az Azure-beli Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>A redis-py telepítése

A [Redis-](https://github.com/andymccurdy/redis-py) a Redis egy Python-felület az Azure cache-hez. A *pip* nevű Python-csomag eszközzel telepítheti a redis-py csomagot. 

Az alábbi példa a *pip3* for Python3 használatával telepíti a Redis-a Windows 10-es verzióját a Visual Studio 2019 fejlesztői parancssorból, amely emelt szintű rendszergazdai jogosultságokkal fut.

```python
    pip3 install redis
```

![A redis-py telepítése](./media/cache-python-get-started/cache-python-install-redis-py.png)


## <a name="read-and-write-to-the-cache"></a>Olvasás és írás a gyorsítótárban

Futtassa a Pythont, és teszteljen a gyorsítótárral a parancssorból. Cserélje le a `<Your Host Name>` és a `<Your Access Key>` értéket a Redis Azure-gyorsítótárának értékeire. 

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>.redis.cache.windows.net',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

> [!IMPORTANT]
> A Redis verziószáma 3,0 vagy magasabb, az SSL-tanúsítvány-ellenőrzési funkció érvénybe lép. a ssl_ca_certs explicit módon be kell állítani a Redis való csatlakozáskor. RH Linux esetén a ssl_ca_certs a "/etc/PKI/TLS/certs/CA-Bundle.CRT" tanúsítvány-modulban található.

## <a name="create-a-python-script"></a>Python-szkript létrehozása

Hozzon létre egy *PythonApplication1.py* nevű új szöveges szkriptfájlt.

Adja a következő szkriptet a *PythonApplication1.py* fájlhoz, és mentse a fájlt. Ez a szkript teszteli a gyorsítótár hozzáférését. Cserélje le a `<Your Host Name>` és a `<Your Access Key>` értéket a Redis Azure-gyorsítótárának értékeire. 

```python
import redis

myHostname = "<Your Host Name>.redis.cache.windows.net"
myPassword = "<Your Access Key>"

r = redis.StrictRedis(host=myHostname, port=6380,
                      password=myPassword, ssl=True)

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

Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

A **szűrés név szerint...** szövegmezőbe írja be az erőforráscsoport nevét. A jelen cikk utasításai egy *TestResources* nevű erőforráscsoportot használtak. Az erőforráscsoport az eredmény listán válassza a **...** , majd az **erőforráscsoport törlése**elemet.

![Törlés](./media/cache-web-app-howto/cache-delete-resource-group.png)

A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. Adja meg a megerősíteni kívánt erőforráscsoport nevét, majd válassza a **Törlés**lehetőséget.

A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Hozzon létre egy egyszerű ASP.NET-webalkalmazást, amely egy Azure cache-t használ a Redis.](./cache-web-app-howto.md)

<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png
