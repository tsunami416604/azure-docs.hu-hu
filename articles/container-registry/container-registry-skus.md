---
title: Szolgáltatási szintek és ski-k
description: Ismerje meg az Azure Container Registry alapszintű, standard és prémium szintű szolgáltatási szintjeinek (SK)- szolgáltatásait és korlátait.
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456268"
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKUs

Az Azure Container Registry (ACR) több szolgáltatási rétegben érhető el, más néven sk-ekben. Ezek a sk-ek kiszámítható árképzést és számos lehetőséget biztosítanak a privát Docker-beállításjegyzék kapacitásához és használati mintáihoz való igazításhoz az Azure-ban.

| SKU | Leírás |
| --- | ----------- |
| **Basic** | Költségoptimalizált belépési pont az Azure Container Registryt tanulmányozó fejlesztőknek. Az alapszintű kibocsátásiegység-forgalmi jegyzékek ugyanazokkal az automatizált képességekkel rendelkeznek, mint a Standard és a Premium (például az Azure Active Directory [hitelesítési integrációja,](container-registry-authentication.md#individual-login-with-azure-ad) [a lemezképek törlése][container-registry-delete]és a [webhookok).][container-registry-webhook] A mellékelt tárolási és lemezkép-átviteli kapacitás azonban alacsonyabb használati forgatókönyvekhez a legmegfelelőbb. |
| **Standard** | A szabványos kibocsátásiegység-forgalmi jegyzékek ugyanazokat a képességeket kínálják, mint az Alapszintű, a megnövekedett tárolási és lemezkép-átviteli. A Standard beállításjegyzékek a legtöbb éles forgatókönyvhöz megfelelők. |
| **Prémium** | A prémium szintű kibocsátásiegység-forgalmi jegyzékek biztosítják a legnagyobb mennyiségű, beépített tárolási és egyidejű műveleteket, lehetővé téve a nagy volumenű forgatókönyveket. A magasabb lemezkép-átviteli érték mellett a Premium olyan funkciókat is hozzáad, mint például a [georeplikáció,][container-registry-geo-replication] amely egyetlen beállításjegyzéket kezel több régióban, [tartalommegbízhatóságot](container-registry-content-trust.md) a lemezképcímke-aláíráshoz, [tűzfalakat és virtuális hálózatokat (előzetes verzió)](container-registry-vnet.md) a beállításjegyzékhez való hozzáférés korlátozására. |

Az alapszintű, standard és prémium szintű termékkészletek ugyanazokat az automatizált képességeket biztosítják. Emellett mindannyian élvezhetik a teljes mértékben az Azure által felügyelt [lemezképek tárolásának][container-registry-storage] előnyeit. A magasabb szintű termékváltozat kiválasztása nagyobb teljesítményt és skálázást biztosít. Több szolgáltatási szint esetén első lépések az alapszintű, majd konvertálni a Standard és a Prémium verzió, mint a rendszerleíró adatbázis használatának növekedése.

## <a name="sku-features-and-limits"></a>Termékváltozat-funkciók és -korlátozások

Az alábbi táblázat az alapszintű, standard és prémium szintű szolgáltatási szintek funkcióit és korlátait ismerteti.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>SKUs módosítása

Módosíthatja a beállításjegyzék termékváltozatát az Azure CLI-vel vagy az Azure Portalon. Szabadon mozoghat a termékváltozatok között, feltéve, hogy a termékváltozat, amelyre vált, rendelkezik a szükséges maximális tárolókapacitással. 

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI-ben lévő sk-ek közötti váltáshoz használja az [az acr update][az-acr-update] parancsot. Például a Prémium ra való váltáshoz:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure portál

A tároló beállításjegyzék **áttekintése** az Azure Portalon válassza **frissítés**, majd válasszon ki egy új **termékváltozat** a termékváltozat legördülő.

![Tároló beállításjegyzék-termékváltozatának frissítése az Azure Portalon][update-registry-sku]

## <a name="pricing"></a>Díjszabás

Az Azure Container Registry ska-k mindegyikével kapcsolatos díjszabási információkért olvassa el a [Container Registry díjszabása című témakört.][container-registry-pricing]

Az adatátvitel díjszabásáról a [Sávszélesség-díjszabás részletei (Sávszélesség-díjszabás részletei) témakörben talál részleteket.](https://azure.microsoft.com/pricing/details/bandwidth/) 

## <a name="next-steps"></a>További lépések

**Az Azure Container-nyilvántartási ütemterv**

Látogasson el az [ACR ütemterv a][acr-roadmap] GitHubon, hogy információkat találjon a szolgáltatás közelgő funkcióiról.

**Azure Container rendszerleíró adatbázis uservoice**

Küldje el és szavazzon az új funkció javaslatokat [ACR UserVoice][container-registry-uservoice].

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
