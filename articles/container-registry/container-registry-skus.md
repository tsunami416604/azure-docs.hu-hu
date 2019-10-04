---
title: SKU Azure Container Registry
description: Hasonlítsa össze a Azure Container Registryben elérhető különböző szolgáltatási szinteket.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 05/06/2019
ms.author: danlep
ms.openlocfilehash: bf620178a0c10661126b3e52c7b908ccc9a90d89
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68311888"
---
# <a name="azure-container-registry-skus"></a>SKU Azure Container Registry

Azure Container Registry (ACR) több szolgáltatási rétegben, azaz SKU-ban is elérhető. Ezek az SKU-elemek kiszámítható díjszabást és számos lehetőséget biztosítanak az Azure-beli privát Docker-beállításjegyzék kapacitása és használati mintáinak összehangolásához.

| SKU | Kezelt | Leírás |
| --- | :-------: | ----------- |
| **Basic** | Igen | Költségoptimalizált belépési pont az Azure Container Registryt tanulmányozó fejlesztőknek. Az alapszintű beállításjegyzékek ugyanazokkal a programozási képességekkel rendelkeznek, mint a standard és a prémium (például Azure Active Directory [Authentication Integration](container-registry-authentication.md#individual-login-with-azure-ad), a [képek törlése][container-registry-delete]és a webhookok). [][container-registry-webhook] A tartalmazott tárolási és képátviteli teljesítmény azonban a legmegfelelőbb az alacsonyabb használati forgatókönyvek esetében. |
| **Standard** | Igen | A standard szintű beállításjegyzékek ugyanazokat a képességeket kínálnak, mint az alapszintű, a megnövekedett tárterület és a képátviteli teljesítmény. A Standard beállításjegyzékek a legtöbb éles forgatókönyvhöz megfelelők. |
| **Prémium** | Igen | A prémium szintű kibocsátásiegység-forgalmi jegyzékek biztosítják a legnagyobb mennyiségű foglalt tárterületet és az egyidejű műveleteket, ami lehetővé teszi a nagy mennyiségű forgatókönyvek A magasabb képátviteli teljesítmény mellett a Premium olyan funkciókat is tartalmaz, mint például a [geo-replikáció][container-registry-geo-replication] több régióban, a képcímkék aláírása és a [tűzfalak és virtuális hálózatok (előzetes verzió)](container-registry-vnet.md) [megbízhatósága](container-registry-content-trust.md) a beállításjegyzékhez való hozzáférés korlátozása. |
|  Klasszikus (*nem érhető el április 2019. után*) | Nem | Ez az SKU engedélyezte a Azure Container Registry szolgáltatás kezdeti kiadását az Azure-ban. A klasszikus beállításjegyzékeket egy olyan Storage-fiók támogatja, amelyet az Azure hoz létre az előfizetésében, ami korlátozza az ACR képességét, hogy magasabb szintű képességeket biztosítson, például nagyobb átviteli sebességet és földrajzi replikálást. |

> [!IMPORTANT]
> A klasszikus beállításjegyzékbeli SKU **elavult**, és **április 2019**. után nem lesz elérhető. Azt javasoljuk, hogy minden új beállításjegyzékhez alapszintű, standard vagy prémium szintű támogatást használjon. Az összes meglévő klasszikus beállításjegyzéket a 2019. április előtt frissíteni kell. A frissítéssel kapcsolatos információkért lásd: [klasszikus beállításjegyzék frissítése][container-registry-upgrade].

Az alapszintű, a standard és a prémium SKU (együttesen felügyelt beállításjegyzék) mind ugyanazt a programozási képességeket biztosítja. Emellett minden előnyt élveznek az Azure által teljes mértékben felügyelt képtárolóból. [][container-registry-storage] A magasabb szintű SKU kiválasztása nagyobb teljesítményt és méretezést biztosít. Több szolgáltatási szint esetén megkezdheti az alapszintű, majd a standard és a prémium szintű konverziót, ha a beállításjegyzék-használat növekszik.

## <a name="sku-feature-matrix"></a>SKU-szolgáltatás mátrixa

Az alábbi táblázat az alapszintű, standard és prémium szintű szolgáltatási szintek funkcióit és korlátait részletezi.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>SKU-i módosítása

A beállításjegyzék SKU-jának módosításához használhatja az Azure CLI-t vagy a Azure Portal. A felügyelt SKU-ket szabadon áthelyezheti, ha a váltáshoz használt SKU a szükséges maximális tárolókapacitással rendelkezik. Ha a Klasszikusból az egyik felügyelt SKU-ra vált, nem léphet vissza a Klasszikusba – ez egy egyirányú konverzió.

### <a name="azure-cli"></a>Azure CLI

Az SKU-nak az Azure CLI-ben való áthelyezéséhez használja az az [ACR Update][az-acr-update] parancsot. Ha például a prémium szintre szeretne váltani:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

A Azure Portal tároló beállításjegyzékének **Áttekintés** területén válassza a **frissítés**lehetőséget, majd válasszon ki egy új **SKU** -t az SKU legördülő menüből.

![Tároló beállításjegyzékbeli SKU frissítése Azure Portal][update-registry-sku]

Ha klasszikus beállításjegyzéket tartalmaz, nem választhat felügyelt SKU-t a Azure Portalon belül. Ehelyett először egy felügyelt [][container-registry-upgrade] beállításjegyzékre kell frissítenie.

## <a name="pricing"></a>Díjszabás

Az egyes Azure Container Registry SKU-ra vonatkozó díjszabással kapcsolatban lásd: [Container Registry díjszabása][container-registry-pricing].

Az adatforgalom díjszabásával kapcsolatos részletekért lásd: a [sávszélesség díjszabása](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>További lépések

**Azure Container Registry ütemterv**

Látogasson el az [ACR][acr-roadmap] -ütemtervre a githubon, ahol információt talál a szolgáltatás közelgő szolgáltatásairól.

**Azure Container Registry UserVoice**

Küldje el és szavazzon az [ACR UserVoice][container-registry-uservoice]új funkcióinak javaslataira.

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
