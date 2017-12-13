---
title: "Az Azure Container Registry ajánlott eljárásai"
description: "Az ajánlott eljárások követésével megismerkedhet az Azure Container Registry leghatékonyabb használatával."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 11/05/2017
ms.author: marsma
ms.openlocfilehash: 5ccbb3022dc38f13eed9b5aa24beb14dfdb3b5b6
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="best-practices-for-azure-container-registry"></a>Az Azure Container Registry ajánlott eljárásai

Az itt leírt ajánlott eljárások követésével a legjobb teljesítménnyel és költséghatékonysággal használhatja az Azure-ban található privát Docker regisztrációs adatbázist.

## <a name="network-close-deployment"></a>Hálózatközeli központi telepítés

A tárolóregisztrációs adatbázist ugyanabban az Azure-régióban hozza létre, amelyben a tárolókat helyezi üzembe. Ha a regisztrációs adatbázist a tároló gazdagéphez hálózatban közeli régióba telepíti, az csökkentheti a késést és a költségeket is.

A hálózatközeli központi telepítés a privát tárolóregisztrációs adatbázis használatának egyik fő oka. A Docker-rendszerképek [rétegezett felépítésüknek](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) köszönhetően növekményesen is telepíthetők. Az új csomópontoknak azonban egy adott rendszerképhez minden szükséges réteget le kell kérniük. A kezdeti `docker pull` hamar akár több gigabájttá nőheti ki magát. Az üzemelő példányához közeli privát regisztrációs adatbázis csökkenti a hálózati késést.
A nyilvános felhők – köztük az Azure is – hálózati forgalmi díjat számítanak fel. A rendszerképek egyes adatközpontok közötti mozgatása hálózati forgalmi díjjal is jár a nagyobb mértékű késés mellett.

## <a name="geo-replicate-multi-region-deployments"></a>Többrégiós üzemelő példányok georeplikációja

Ha több régióban telepít tárolókat, használja az Azure Container Registry [georeplikációs](container-registry-geo-replication.md) funkcióját. Ha globális ügyfélbázist szolgál ki helyi adatközpontokból, vagy fejlesztői csapatának tagjai különböző helyeken tartózkodnak, a regisztrációs adatbázis georeplikálásával egyszerűsítheti a regisztrációs adatbázis kezelését és minimalizálhatja a késést. Ez a jelenleg előzetes kiadású szolgáltatás a [prémium](container-registry-skus.md#premium) regisztrációs adatbázisokhoz érhető el.

A georeplikáció használatának megismeréséhez tekintse meg háromrészes útmutatónkat: [Georeplikáció az Azure Container Registry-ben](container-registry-tutorial-prepare-registry.md).

## <a name="repository-namespaces"></a>Adattárnévterek

Az adattárnévterek használatával engedélyezheti egyetlen regisztrációs adatbázis megosztását több csoporttal a szervezeten belül. A regisztrációs adatbázisok több környezeten és csoporton keresztül is megoszthatók. Az Azure Container Registry támogatja a beágyazott névtereket, amelyekkel elkülöníthetők a csoportok.

Vegyük példaként a következő tárolórendszerkép-címkéket. A teljes vállalat által használt rendszerképek (például `aspnetcore`) a gyökérnévtérbe kerülnek, a Termelés és a Marketing csoportok tulajdonában lévő rendszerképek pedig a saját névterüket használják.

```
contoso.azurecr.io/aspnetcore:2.0
contoso.azurecr.io/products/widget/web:1
contoso.azurecr.io/products/bettermousetrap/refundapi:12.3
contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42
```

## <a name="dedicated-resource-group"></a>Dedikált erőforráscsoport

Mivel a tárolóregisztrációs adatbázisok több tárológazdagép által használt erőforrások, a regisztrációs adatbázisoknak tárolniuk kell a saját erőforráscsoportjukat.

Nyugodtan kísérletezhet speciális gazdagéptípusokkal, például az Azure Container Instances-zel, de utána valószínűleg törölni szeretné majd a tárolópéldányt. Előfordulhat azonban, hogy meg szeretné tartani azokat a rendszerképeket, amelyeket átvitt az Azure Container Registry-be. Azzal, hogy a regisztrációs adatbázis a saját erőforráscsoportjába helyezi, csökkentheti annak esélyét, hogy véletlenül törli a rendszerképeket, amikor törli a tárolópéldány erőforráscsoportját.

## <a name="authentication"></a>Authentication

Azure tárolóregisztrációs adatbázissal való hitelesítéskor két fő forgatókönyv fordulhat elő: az egyéni hitelesítés és a szolgáltatásos (vagy „távfelügyelt”) hitelesítés. A következő táblázat röviden bemutatja ezeket a forgatókönyveket és a hozzájuk fűződő ajánlott hitelesítési módokat.

| Típus | Példaforgatókönyv | Javasolt módszer |
|---|---|---|
| Egyéni identitás | Egy fejlesztő rendszerképeket hív le a saját számítógépére, vagy helyez át onnan. | [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) |
| Távfelügyelt/szolgáltatásos identitás | Buildelési és üzembe helyezési folyamatok, amelyekben a felhasználó nem vesz közvetlenül részt. | [Egyszerű szolgáltatás](container-registry-authentication.md#service-principal) |

Az Azure Container Registry-vel kapcsolatos részletes információk: [Hitelesítés Azure tárolóregisztrációs adatbázissal](container-registry-authentication.md).

## <a name="next-steps"></a>Következő lépések

Az Azure Container Registry több szinten, azaz termékváltozatban érhető el, melyek különféle képességeket biztosítanak. Részletek az elérhető termékváltozatokról: [Az Azure Container Registry termékváltozatai](container-registry-skus.md).