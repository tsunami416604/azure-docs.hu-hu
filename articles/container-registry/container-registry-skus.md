---
title: Azure-tárolót beállításjegyzék termékváltozatok
description: Hasonlítsa össze a különböző szolgáltatásrétegeiben használt funkciókkal elérhető Azure tároló beállításjegyzékben.
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 03/15/2018
ms.author: marsma
ms.openlocfilehash: c9b8e072b5ccd89c27d9c46407e472d6bf1e1e84
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-container-registry-skus"></a>Azure-tárolót beállításjegyzék termékváltozatok

Az Azure tároló beállításjegyzék (ACR) több szolgáltatásszinttel termékváltozatok néven érhető el. Ezek termékváltozatok adja meg, kiszámítható díjszabás és a kapacitás és a használati minták, a titkos Docker beállításjegyzék az Azure-ban való számos lehetőség közül választhat.

| SKU | Managed | Leírás |
| --- | :-------: | ----------- |
| **Basic** | Igen | Egy Azure tároló beállításjegyzék megtanulni fejlesztőknek költség optimalizált belépési pont. Alapszintű nyilvántartó azonos programozott funkciókkal rendelkeznek, mint a Standard és Premium (a Azure Active Directory hitelesítési integráció, a lemezkép törlése és a webes hurkok) rendelkezik, azonban van mérete és használati korlátozások. |
| **Standard** | Igen | Standard nyilvántartó, Basic, a megnövekedett tárolási korlátokhoz és átviteli kép ugyanazokat a képességeket kínálnak. Standard nyilvántartó kell a legtöbb éles környezetben igényeinek kielégítéséhez. |
| **Prémium** | Igen | Prémium szintű nyilvántartó magasabb korlátok korlátozásokat, például a tárolási és párhuzamos műveletek engedélyezése a nagy mennyiségű forgatókönyvek szolgálnak. Nagyobb átviteli sebesség a lemezképet, mellett prémium hozzáadja funkciók, például [georeplikáció] [ container-registry-geo-replication] egyetlen beállításjegyzékbeli kezeléséhez különféle régiókban, az egyes hálózati bezárású beállításjegyzékbeli karbantartása központi telepítés. |
| Klasszikus | Nem | A klasszikus beállításjegyzék SKU eredeti kiadásának az Azure-ban az Azure-tároló beállításjegyzék szolgáltatás engedélyezve van. Klasszikus nyilvántartó üzemelnek az előfizetéshez, ezáltal megnehezítve a magasabb szintű képességeit, például a nagyobb átviteli sebesség és a georeplikáció biztosításához ACR létrehozó Azure storage-fiók. Korlátozott platformképességei miatt tervezzük érvényteleníthető a jövőben a klasszikus Termékváltozat. |

Hálózatiadapter-egy magasabb szintű SKU biztosít további teljesítmény és méretezhetőség, azonban az összes felügyelt termékváltozatok a azonos programozott képességeket biztosítják. Több szolgáltatási rétegekkel Basic való ismerkedés, majd a a beállításjegyzék használati növekedése Standard és Premium átalakítása.

> [!NOTE]
> A klasszikus beállításjegyzék SKU tervezett elavulása elkerülése érdekében javasoljuk összes új nyilvántartó Basic, Standard vagy prémium használja. További információ az létező klasszikus beállításjegyzék konvertálása: [egy klasszikus beállításjegyzék frissítése][container-registry-upgrade].
>

## <a name="managed-vs-unmanaged"></a>Nem felügyelt és felügyelete

A Basic, Standard és Premium termékváltozat összefoglaló néven *felügyelt* nyilvántartó, és a klasszikus nyilvántartó mint *nem felügyelt*. Az elsődleges különbség a kettő között, a tároló képek tárolási módját.

### <a name="managed-basic-standard-premium"></a>Felügyelt (Basic, Standard, Premium)

Felügyelt nyilvántartó juttatás teljes mértékben az Azure által kezelt képtároláshoz. Ez azt jelenti, hogy a storage-fiók, amely tárolja a lemezképeket, nem jelenik meg az Azure-előfizetéshez belül. Több előnye is van a felügyelt beállításjegyzék termékváltozatok egyikével szerzett, a részletes ismertetése [tároló lemezkép tárolási Azure tároló beállításjegyzék][container-registry-storage]. Ez a cikk a felügyelt beállításjegyzék termékváltozatok és azok képességeinek szolgál.

### <a name="unmanaged-classic"></a>Nem felügyelt (klasszikus)

Klasszikus nyilvántartó vannak "nem felügyelt" lehet, hogy a tárfiókot, amely a klasszikus beállításjegyzékbeli biztonsági belül helyezkedik el a *a* Azure-előfizetés. Ön felelősséggel tartozik a tárfiókot, az a tároló lemezképeket tároló kezelését. A nem felügyelt nyilvántartó, nem lehet átállítani a termékváltozatok közötti igényeinek módosítása (eltérő [frissítése] [ container-registry-upgrade] felügyelt beállításjegyzék), és több felügyelt nyilvántartó számára nem érhető el (például tároló lemezkép törlése, [georeplikáció][container-registry-geo-replication], és [webhookok][container-registry-webhook]).

Egy, a felügyelt SKU klasszikus beállításjegyzékbeli frissítésével kapcsolatos további információkért lásd: [egy klasszikus beállításjegyzék frissítése][container-registry-upgrade].

## <a name="sku-feature-matrix"></a>Termékváltozat-funkció mátrix

Az alábbi táblázat részletezi, a szolgáltatások és a Basic, Standard és Premium szolgáltatásszintek határain.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>SKU módosítása

Módosíthatja a beállításjegyzék SKU az Azure parancssori felülettel vagy az Azure portálon. Áthelyezhet szabadon felügyelt termékváltozatok mindaddig, amíg a Termékváltozat vált rendelkezik a szükséges maximális tárolási kapacitását. Ha átvált egy, a felügyelt SKU klasszikus, nem helyezhető át vissza klasszikus--egyirányú konverzió.

### <a name="azure-cli"></a>Azure CLI

Az Azure parancssori felületen termékváltozatok közötti áthelyezéséhez használja a [az acr frissítés] [ az-acr-update] parancsot. Ha például váltson prémium át:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

A tároló beállításjegyzék **áttekintése** az Azure portálon, válassza ki a **frissítés**, válassza ki egy új **SKU** a Termékváltozat legördülő.

![Frissítés tároló beállításjegyzék SKU Azure-portálon][update-registry-sku]

Ha a klasszikus beállításjegyzék, nem választhat ki egy felügyelt SKU az Azure portálon. Ehelyett először [frissítése] [ container-registry-upgrade] felügyelt beállításjegyzék (lásd: [módosítása a klasszikus](#changing-from-classic)).

## <a name="changing-from-classic"></a>A klasszikus módosítása

Nincsenek további szempontokat figyelembe kell venni egy nem felügyelt klasszikus beállításjegyzék áttelepítésekor a felügyelt Basic, Standard vagy Premium termékváltozat egyikére. Ha a klasszikus beállításjegyzék képek nagy számú tartalmaz, és sok gigabájtig terjedhetnek, akkor az áttelepítési folyamat némi időbe telhet. Emellett `docker push` műveletek le vannak tiltva, amíg az áttelepítés akkor fejeződött be.

A felügyelt termékváltozatok egyikét a klasszikus beállításjegyzék frissítésével kapcsolatos részletekért lásd: [egy klasszikus tároló beállításjegyzék frissítése][container-registry-upgrade].

## <a name="pricing"></a>Díjszabás

Az egyes az Azure-tároló beállításjegyzék termékváltozatok árakról, lásd: [tároló beállításjegyzék árképzési][container-registry-pricing].

## <a name="next-steps"></a>További lépések

**Azure-tárolót beállításjegyzék terv**

Látogasson el a [ACR terv] [ acr-roadmap] a Githubon tájékozódhat a szolgáltatás a közeljövőben megjelenő funkciókról.

**Azure-tárolót beállításjegyzék UserVoice**

Küldje el, majd az új szolgáltatási javaslataikat szavazhatnak [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-webhook]: container-registry-webhook.md
