---
title: Az Azure Container Registry Termékváltozatai
description: Hasonlítsa össze a különböző szolgáltatásszintek elérhető az Azure Container Registryben.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: 7fdc1417ac524b422a12a087cf1661040efb9f8a
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521958"
---
# <a name="azure-container-registry-skus"></a>Az Azure Container Registry Termékváltozatai

Az Azure Container Registry (ACR) több szolgáltatási szinten, SKU-k más néven érhető el. Termékváltozatokban biztosítanak, kiszámítható díjszabás és a kapacitás és a használati minták, a privát Docker-tárolójegyzék az Azure-nak megfelelően számos lehetőség közül választhat.

| SKU | Managed | Leírás |
| --- | :-------: | ----------- |
| **Basic** | Igen | Költségoptimalizált belépési pont az Azure Container Registryt tanulmányozó fejlesztőknek. Alapszintű beállításjegyzékek rendelkezik, a Standard és prémium szintű ugyanazokat a programozott képességeket (például az Azure Active Directory [hitelesítési integrációs](container-registry-authentication.md#individual-login-with-azure-ad), [kép törlése][container-registry-delete], és [webhookok][container-registry-webhook]). Azonban a belefoglalt tárterület és a lemezkép átviteli sebesség különösen leginkább megfelelő alacsonyabb használati forgatókönyvek. |
| **Standard** | Igen | Szabványos regisztrációs adatbázisokkal a Basic, a nagyobb foglalt tároló- és képfájlok átviteli sebesség megegyező funkciókat kínál. A Standard beállításjegyzékek a legtöbb éles forgatókönyvhöz megfelelők. |
| **Prémium** | Igen | Prémium szintű beállításjegyzékek adja meg a belefoglalt tárterület és a párhuzamos műveletek nagy mennyiségű forgatókönyveket lehetővé legmagasabb összege. Mellett lemezkép nagyobb átviteli sebességet, prémium szintű funkciók, többek között ad [georeplikációs] [ container-registry-geo-replication] egyetlen regisztrációs adatbázis kezeléséhez több régióban [megbízhatóság (előzetes verzió)tartalom](container-registry-content-trust.md) kép címke aláírására, és [tűzfalak és virtuális hálózatok (előzetes verzió)](container-registry-vnet.md) a beállításjegyzékben való hozzáférés korlátozásához. |
|  Klasszikus (*nem érhető el, április 2019 után*) | Nem | Ez a Termékváltozat engedélyezve van az Azure-ban az Azure Container Registry szolgáltatás kezdeti verziójában. Klasszikus beállításjegyzékek élvezik egy tárfiókot, amelyet az Azure létrehozza az előfizetésében, ami lehetővé teszi az ACR-hez, például a nagyobb átviteli sebesség és georeplikáció útján magasabb szintű képességeket biztosít. |

> [!IMPORTANT]
> A klasszikus beállításjegyzékben folyamatban van a Termékváltozat **elavult**, és nem lesz elérhető után **április 2019**. Az összes új beállításjegyzékek alapszintű, Standard vagy prémium szintű használatát javasoljuk. Az összes meglévő klasszikus beállításjegyzékek április 2019 előtt kell frissíteni. Frissítési információ: [klasszikus beállításjegyzék frissítése][container-registry-upgrade].

A Basic, Standard és prémium szintű termékváltozatok (összefoglaló néven *felügyelt beállításjegyzékek*) összes ugyanazokat a programozott képességeket biztosítanak. Emellett az összes részesülnek [képtárolás] [ container-registry-storage] teljes egészében az Azure által kezelt. Az egy magasabb szintű Termékváltozat biztosít, további teljesítmény és méretezhetőség. A több szolgáltatási szintekkel megkezdheti az alapszintű, majd a a rendszerleíró adatbázis használattal növekedése Standard és prémium szintű átalakítása.

## <a name="sku-feature-matrix"></a>Termékváltozat funkció mátrix

Az alábbi táblázat ismerteti az a funkciók és az alapszintű, Standard és prémium szolgáltatásszintek korlátait.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>SKU-k módosítása

Módosíthatja a beállításjegyzék-Termékváltozat az Azure CLI-vel vagy az Azure Portalon. Áthelyezheti szabadon felügyelt termékváltozatok közötti mindaddig, amíg a Termékváltozat való váltás rendelkezik a szükséges maximális tárolási kapacitását. Ha klasszikusról az egyik a felügyelt termékváltozatok vált, nem válthat vissza klasszikus--egy egyirányú átalakítás.

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI-ben termékváltozatok közötti áthelyezéséhez használja a [az acr update] [ az-acr-update] parancsot. Ha például szeretne váltani a prémium szintű:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

A tároló-beállításjegyzék **áttekintése** az Azure Portalon válassza ki a **frissítés**, majd egy új **Termékváltozat** a Termékváltozat legördülő listából.

![Az Azure Portalon a Termékváltozat a tárolóregisztrációs adatbázis frissítése][update-registry-sku]

Ha klasszikus beállításjegyzék, az Azure Portalon a felügyelt Termékváltozat nem választhat. Ehelyett először [frissítése] [ container-registry-upgrade] , egy felügyelt beállításjegyzéket.

## <a name="pricing"></a>Díjszabás

Az egyes az Azure Container Registry Termékváltozatai díjszabásról lásd: [Container Registry díjszabás][container-registry-pricing].

Az adatforgalmi díjszabás részleteiért lásd: [adatforgalmi díjszabás részletei](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>További lépések

**Az Azure Container Registry-ütemterv**

Látogasson el a [ACR ütemterv] [ acr-roadmap] a közeljövőben megjelenő funkciókról információkat találhat a szolgáltatás a Githubon.

**Azure Container Registry UserVoice**

Küldje el, és szavazzon az új szolgáltatási javaslataikat [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
