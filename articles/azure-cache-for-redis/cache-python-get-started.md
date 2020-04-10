---
title: 'Rövid útmutató: Python-alkalmazás létrehozása – Azure-gyorsítótár a Redis számára'
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre egy Python-alkalmazást, amely az Azure Cache for Redis használatával.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- seo-python-october2019
ms.date: 11/05/2019
ms.openlocfilehash: 6ce3dd1cbb694988af3555765342a1c4ca3850b5
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010851"
---
# <a name="quickstart-create-a-python-app-that-uses-azure-cache-for-redis"></a>Rövid útmutató: Hozzon létre egy Python-alkalmazást, amely azure-gyorsítótárat használ a Redis-hez

Ebben a cikkben a Redis-gyorsítótárba építi be az Azure Cache for Redis alkalmazást, hogy hozzáférjen egy biztonságos, dedikált gyorsítótárhoz, amely az Azure-on belül bármely alkalmazásból elérhető.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Python 2 vagy 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Azure-gyorsítótár létrehozása a Redis-példányhoz
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>A redis-py telepítése

[Redis-py](https://github.com/andymccurdy/redis-py) egy Python-felület az Azure Cache for Redis. A Python-csomagok eszköz , *pip*, telepítse a *redis-py* csomagot a parancssorból. 

A következő példa *a Python 3-hoz készült pip3-ot* használta a *redis-py* telepítéséhez a Windows 10-re egy rendszergazdai parancssorból.

![Telepítse a redis-py Python felületet az Azure Cache for Redis-hez](./media/cache-python-get-started/cache-python-install-redis-py.png)

## <a name="read-and-write-to-the-cache"></a>Olvasás és írás a gyorsítótárban

Futtassa a Pythont a parancssorból, és tesztelje a gyorsítótárat a következő kód használatával. Cserélje `<Your Host Name>` `<Your Access Key>` le, és az azure-gyorsítótár a Redis-példány értékeit. Az állomásneve * \<>.redis.cache.windows.net típusú DNS-név.*

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
> Az Azure Cache for Redis 3.0-s vagy újabb verzió esetén a TLS/SSL tanúsítvány ellenőrzése érvénybe lép. ssl_ca_certs explicit módon kell beállítani, amikor csatlakozik az Azure Cache for Redis. A RedHat Linux esetében a ssl_ca_certs az */etc/pki/tls/certs/ca-bundle.crt* tanúsítványmodulban találhatók.

## <a name="create-a-python-sample-app"></a>Python-mintaalkalmazás létrehozása

Hozzon létre egy új szövegfájlt, adja hozzá a következő parancsfájlt, és mentse a fájlt *PythonApplication1.py*. Cserélje `<Your Host Name>` `<Your Access Key>` le, és az azure-gyorsítótár a Redis-példány értékeit. Az állomásneve * \<>.redis.cache.windows.net típusú DNS-név.*

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

*Futtassa PythonApplication1.py* pythonnal. A következő példához hasonló eredményeket kell látnia:

![Python-parancsfájl futtatása a gyorsítótár-hozzáférés teszteléséhez](./media/cache-python-get-started/cache-python-completed.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte az Azure erőforráscsoportot és az ebben a rövid útmutatóban létrehozott erőforrásokat, törölheti őket a költségek elkerülése érdekében.

> [!IMPORTANT]
> Az erőforráscsoport törlése visszafordíthatatlan, és az erőforráscsoport és a benne lévő összes erőforrás véglegesen törlődik. Ha az Azure Cache for Redis-példányt egy megtartani kívánt meglévő erőforráscsoportban hozta létre, csak a gyorsítótárat törölheti a **Törlés** a gyorsítótár **áttekintése** lapon lehetőség kiválasztásával. 

Az erőforráscsoport és annak Redis-gyorsítótára az Azure-példányhoz törlése:

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki **az Erőforráscsoportokat.**
1. A **Név szerint szűrés** mezőbe írja be a gyorsítótár-példányt tartalmazó erőforráscsoport nevét, majd jelölje ki a keresési eredmények közül. 
1. Az erőforráscsoport lapján válassza az **Erőforráscsoport törlése**lehetőséget.
1. Írja be az erőforráscsoport nevét, majd válassza **a Törlés**lehetőséget.
   
   ![Az erőforráscsoport törlése a Redis Azure-gyorsítótárához](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hozzon létre egy egyszerű ASP.NET webalkalmazást, amely egy Azure-gyorsítótárat használ a Redis számára.](./cache-web-app-howto.md)

