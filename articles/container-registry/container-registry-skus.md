---
title: Szolgáltatási szintek és SKU-i
description: Ismerje meg a Azure Container Registry alapszintű, standard és prémium szintű szolgáltatási szintjeinek funkcióit és korlátait.
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456268"
---
# <a name="azure-container-registry-skus"></a>SKU Azure Container Registry

Azure Container Registry (ACR) több szolgáltatási rétegben, azaz SKU-ban is elérhető. Ezek az SKU-elemek kiszámítható díjszabást és számos lehetőséget biztosítanak az Azure-beli privát Docker-beállításjegyzék kapacitása és használati mintáinak összehangolásához.

| SKU | Leírás |
| --- | ----------- |
| **Basic** | Költségoptimalizált belépési pont az Azure Container Registryt tanulmányozó fejlesztőknek. Az alapszintű beállításjegyzékek ugyanazokkal a programozási képességekkel rendelkeznek, mint a standard és a prémium (például Azure Active Directory [Authentication Integration](container-registry-authentication.md#individual-login-with-azure-ad), a [képek törlése][container-registry-delete]és a [webhookok][container-registry-webhook]). A tartalmazott tárolási és képátviteli teljesítmény azonban a legmegfelelőbb az alacsonyabb használati forgatókönyvek esetében. |
| **Standard** | A standard szintű beállításjegyzékek ugyanazokat a képességeket kínálnak, mint az alapszintű, a megnövekedett tárterület és a képátviteli teljesítmény. A Standard beállításjegyzékek a legtöbb éles forgatókönyvhöz megfelelők. |
| **Prémium** | A prémium szintű kibocsátásiegység-forgalmi jegyzékek biztosítják a legnagyobb mennyiségű foglalt tárterületet és az egyidejű műveleteket, ami lehetővé teszi a nagy mennyiségű forgatókönyvek A magasabb képátviteli teljesítmény mellett a Premium olyan funkciókat is tartalmaz, mint például a [geo-replikáció][container-registry-geo-replication] több régióban, a képcímkék aláírása, a [tűzfalak és a virtuális hálózatok (előzetes verzió)](container-registry-vnet.md) [megbízhatósága](container-registry-content-trust.md) a beállításjegyzékhez való hozzáférés korlátozása. |

Az alapszintű, a standard és a prémium SKU mind ugyanazt a programozási képességeket biztosítja. Emellett minden előnyt élveznek az Azure által teljes mértékben felügyelt [képtárolóból][container-registry-storage] . A magasabb szintű SKU kiválasztása nagyobb teljesítményt és méretezést biztosít. Több szolgáltatási szint esetén megkezdheti az alapszintű, majd a standard és a prémium szintű konverziót, ha a beállításjegyzék-használat növekszik.

## <a name="sku-features-and-limits"></a>SKU-funkciók és-korlátok

Az alábbi táblázat az alapszintű, standard és prémium szintű szolgáltatási szintek funkcióit és korlátait részletezi.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>SKU-i módosítása

A beállításjegyzék SKU-jának módosításához használhatja az Azure CLI-t vagy a Azure Portal. Az SKU-ket szabadon áthelyezheti, ha a váltáshoz használt SKU a szükséges maximális tárolókapacitással rendelkezik. 

### <a name="azure-cli"></a>Azure CLI

Az SKU-nak az Azure CLI-ben való áthelyezéséhez használja az az [ACR Update][az-acr-update] parancsot. Ha például a prémium szintre szeretne váltani:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

A Azure Portal tároló beállításjegyzékének **Áttekintés** területén válassza a **frissítés**lehetőséget, majd válasszon ki egy új **SKU** -t az SKU legördülő menüből.

![Tároló beállításjegyzékbeli SKU frissítése Azure Portal][update-registry-sku]

## <a name="pricing"></a>Díjszabás

Az egyes Azure Container Registry SKU-ra vonatkozó díjszabással kapcsolatban lásd: [Container Registry díjszabása][container-registry-pricing].

Az adatforgalom díjszabásával kapcsolatos részletekért lásd: a [sávszélesség díjszabása](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Következő lépések

**Azure Container Registry ütemterv**

Látogasson el az [ACR-ütemtervre][acr-roadmap] a githubon, ahol információt talál a szolgáltatás közelgő szolgáltatásairól.

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
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
