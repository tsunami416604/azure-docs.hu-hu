---
title: 'Gyors útmutató: az Azure cache-t használó Python-alkalmazás létrehozása a Redis-hez'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre olyan Python-alkalmazást, amely Azure cache-t használ a Redis.
author: yegu-ms
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: yegu
ms.custom:
- mvc
- seo-python-october2019
ms.openlocfilehash: 1ac1767d1ac3f6333d73a1e1c66ff7250942d266
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122670"
---
# <a name="quickstart-create-a-python-app-that-uses-azure-cache-for-redis"></a>Gyors útmutató: az Azure cache-t használó Python-alkalmazás létrehozása a Redis-hez

Ebben a cikkben egy Python-alkalmazásba foglalja bele az Azure cache-t a Redis-be, hogy hozzáférhessen egy biztonságos, dedikált gyorsítótárhoz, amely az Azure-on belül bármely alkalmazásból elérhető.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Python 2 vagy 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Azure cache létrehozása a Redis-példányhoz
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>A redis-py telepítése

A [Redis-](https://github.com/andymccurdy/redis-py) a Redis egy Python-felület az Azure cache-hez. A *Redis-* a Python-csomagok eszközzel telepítheti a *pip*-et a parancssorból. 

A következő példa a Python 3 *pip3* használta a *Redis-* a Windows 10-es verzióra való telepítéséhez a rendszergazdai parancssorból.

![A Redis-a Python-felület telepítése az Azure cache-be a Redis-hez](./media/cache-python-get-started/cache-python-install-redis-py.png)

## <a name="read-and-write-to-the-cache"></a>Olvasás és írás a gyorsítótárban

Futtassa a Pythont a parancssorból, és tesztelje a gyorsítótárat a következő kód használatával. Cserélje le a `<Your Host Name>` és a `<Your Access Key>` értéket a Redis-példány Azure-gyorsítótárának értékeire. Az állomásnév *\<DNS-név: >. Redis. cache. Windows. net*.

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

> [!IMPORTANT]
> Az Azure cache 3,0-es vagy újabb verziójának Redis az SSL-tanúsítvány ellenőrzését érvényesíti. a Redis Azure cache-hez való csatlakozáskor explicit módon be kell állítani a ssl_ca_certs. A RedHat Linux esetében ssl_ca_certs az */etc/PKI/TLS/certs/CA-Bundle.CRT* Certificate modulban találhatók.

## <a name="create-a-python-sample-app"></a>Python-minta alkalmazás létrehozása

Hozzon létre egy új szövegfájlt, adja hozzá a következő parancsfájlt, és mentse a fájlt *PythonApplication1.py*. Cserélje le a `<Your Host Name>` és a `<Your Access Key>` értéket a Redis-példány Azure-gyorsítótárának értékeire. Az állomásnév *\<DNS-név: >. Redis. cache. Windows. net*.

```python
import redis

myHostname = "<Your Host Name>"
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

*PythonApplication1.py* futtatása Python-val. Az eredmények a következő példához hasonlóan jelennek meg:

![Python-szkript futtatása a gyorsítótár-hozzáférés teszteléséhez](./media/cache-python-get-started/cache-python-completed.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte az Azure-erőforráscsoportot és az ebben a rövid útmutatóban létrehozott erőforrásokat, törölheti őket a díjak elkerülése érdekében.

> [!IMPORTANT]
> Egy erőforráscsoport törlése nem vonható vissza, és az erőforráscsoport és az ahhoz tartozó összes erőforrás véglegesen törölve lesz. Ha létrehozta az Azure cache-t a Redis-példányhoz egy meglévő erőforráscsoporthoz, amelyet meg szeretne őrizni, törölheti a gyorsítótár **Áttekintés** lapján található **Törlés** lehetőség kiválasztásával. 

Az erőforráscsoport és az Azure-példány Redis Cacheának törlése:

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki az **erőforráscsoportok**elemet.
1. A **szűrés név alapján** szövegmezőbe írja be annak az erőforráscsoportnak a nevét, amely tartalmazza a gyorsítótár-példányt, majd válassza ki a keresési eredmények közül. 
1. Az erőforráscsoport lapon válassza az **erőforráscsoport törlése**elemet.
1. Írja be az erőforráscsoport nevét, majd válassza a **Törlés**lehetőséget.
   
   ![Az Azure cache Redis tartozó erőforráscsoport törlése](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hozzon létre egy egyszerű ASP.NET-webalkalmazást, amely egy Azure cache-t használ a Redis.](./cache-web-app-howto.md)

