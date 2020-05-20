---
title: A beállításjegyzék szolgáltatási szintjei és funkciói
description: Ismerje meg a Azure Container Registry alapszintű, standard és prémium szintű szolgáltatási szintjeinek funkcióit és korlátait.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 35f5d4ebd4a2b427aadc6e82e265a7da9b6409f8
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683420"
---
# <a name="azure-container-registry-service-tiers"></a>Azure Container Registry szolgáltatási szintek

Azure Container Registry több szolgáltatási rétegben (más néven SKU) is elérhető. Ezek a szintek kiszámítható díjszabást és számos lehetőséget biztosítanak az Azure-beli privát Docker-beállításjegyzék kapacitás-és használati mintáinak összehangolásához.

| Szint | Description |
| --- | ----------- |
| **Basic** | Költségoptimalizált belépési pont az Azure Container Registryt tanulmányozó fejlesztőknek. Az alapszintű beállításjegyzékek ugyanazokkal a programozási képességekkel rendelkeznek, mint a standard és a prémium (például Azure Active Directory [Authentication Integration](container-registry-authentication.md#individual-login-with-azure-ad), a [képek törlése][container-registry-delete]és a [webhookok][container-registry-webhook]). A tartalmazott tárolási és képátviteli teljesítmény azonban a legmegfelelőbb az alacsonyabb használati forgatókönyvek esetében. |
| **Standard** | A standard szintű beállításjegyzékek ugyanazokat a képességeket kínálnak, mint az alapszintű, a megnövekedett tárterület és a képátviteli teljesítmény. A Standard beállításjegyzékek a legtöbb éles forgatókönyvhöz megfelelők. |
| **Prémium** | A prémium szintű kibocsátásiegység-forgalmi jegyzékek biztosítják a legnagyobb mennyiségű foglalt tárterületet és az egyidejű műveleteket, ami lehetővé teszi a nagy mennyiségű forgatókönyvek A magasabb képátviteli teljesítmény mellett a Premium olyan funkciókat is tartalmaz, mint például a [geo-replikáció][container-registry-geo-replication] több régióban, a Képcímke-aláírásban a [tartalom megbízhatósága](container-registry-content-trust.md) , a privát [végpontokkal való privát hivatkozás](container-registry-private-link.md) a beállításjegyzékhez való hozzáférés korlátozása érdekében. |

Az alapszintű, a standard és a prémium szintű csomag mind ugyanazokat a programozási képességeket biztosítja. Emellett minden előnyt élveznek az Azure által teljes mértékben felügyelt [képtárolóból][container-registry-storage] . A magasabb szintű szint kiválasztásával nagyobb teljesítményt és méretezést biztosít. Több szolgáltatási szint esetén megkezdheti az alapszintű, majd a standard és a prémium szintű konverziót, ha a beállításjegyzék-használat növekszik.

## <a name="service-tier-features-and-limits"></a>Szolgáltatási rétegek funkciói és korlátai

Az alábbi táblázat az alapszintű, standard és prémium szintű szolgáltatási szintek funkcióit és korlátait részletezi.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-tiers"></a>Rétegek módosítása

A beállításjegyzék szolgáltatási szintje az Azure CLI-vel vagy a Azure Portal is módosítható. A csomagok között szabadon áthelyezheti a kapacitást, ha az átváltott szinten a maximális tárolókapacitás szükséges. 

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI szolgáltatási szintjei közötti váltáshoz használja az az [ACR Update][az-acr-update] parancsot. Ha például a prémium szintre szeretne váltani:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

A Azure Portal tároló beállításjegyzékének **Áttekintés** területén válassza a **frissítés**lehetőséget, majd válasszon ki egy új **SKU** -t az SKU legördülő menüből.

![Tároló beállításjegyzékbeli SKU frissítése Azure Portal][update-registry-sku]

## <a name="pricing"></a>Díjszabás

A Azure Container Registry szolgáltatási szintjeinek díjszabásáról a [Container Registry díjszabása][container-registry-pricing]című témakörben olvashat.

Az adatforgalom díjszabásával kapcsolatos részletekért lásd: a [sávszélesség díjszabása](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>További lépések

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
