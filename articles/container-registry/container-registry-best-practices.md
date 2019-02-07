---
title: Az Azure Container Registry ajánlott eljárásai
description: Az ajánlott eljárások követésével megismerkedhet az Azure Container Registry leghatékonyabb használatával.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 09/27/2018
ms.author: danlep
ms.openlocfilehash: 2cf64c7c4f99a57c4a4a6cf03e68e8af803ceca9
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55810762"
---
# <a name="best-practices-for-azure-container-registry"></a>Az Azure Container Registry ajánlott eljárásai

Az itt leírt ajánlott eljárások követésével a legjobb teljesítménnyel és költséghatékonysággal használhatja az Azure-ban található privát Docker regisztrációs adatbázist.

## <a name="network-close-deployment"></a>Hálózatközeli központi telepítés

A tárolóregisztrációs adatbázist ugyanabban az Azure-régióban hozza létre, amelyben a tárolókat helyezi üzembe. Ha a regisztrációs adatbázist a tároló gazdagéphez hálózatban közeli régióba telepíti, az csökkentheti a késést és a költségeket is.

A hálózatközeli központi telepítés a privát tárolóregisztrációs adatbázis használatának egyik fő oka. A Docker-rendszerképek hatékony [rétegezett felépítésüknek](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) köszönhetően növekményesen is telepíthetők. Az új csomópontoknak azonban egy adott rendszerképhez minden szükséges réteget le kell kérniük. A kezdeti `docker pull` hamar akár több gigabájttá nőheti ki magát. Az üzemelő példányához közeli privát regisztrációs adatbázis csökkenti a hálózati késést.
A nyilvános felhők – köztük az Azure is – hálózati forgalmi díjat számítanak fel. A rendszerképek egyes adatközpontok közötti mozgatása hálózati forgalmi díjjal is jár a nagyobb mértékű késés mellett.

## <a name="geo-replicate-multi-region-deployments"></a>Többrégiós üzemelő példányok georeplikációja

Ha több régióban telepít tárolókat, használja az Azure Container Registry [georeplikációs](container-registry-geo-replication.md) funkcióját. Ha globális ügyfélbázist szolgál ki helyi adatközpontokból, vagy fejlesztői csapatának tagjai különböző helyeken tartózkodnak, a regisztrációs adatbázis georeplikálásával egyszerűsítheti a regisztrációs adatbázis kezelését és minimalizálhatja a késést. A georeplikáció csak [Prémium](container-registry-skus.md) szintű regisztrációs adatbázisok esetén érhető el.

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

Mivel a tároló-beállításjegyzékek több tárológazdagép használt erőforrások, a saját erőforráscsoport beállításjegyzék kell lennie.

Nyugodtan kísérletezhet speciális gazdagéptípusokkal, például az Azure Container Instances-zel, de utána valószínűleg törölni szeretné majd a tárolópéldányt. Előfordulhat azonban, hogy meg szeretné tartani azokat a rendszerképeket, amelyeket átvitt az Azure Container Registry-be. Azzal, hogy a regisztrációs adatbázis a saját erőforráscsoportjába helyezi, csökkentheti annak esélyét, hogy véletlenül törli a rendszerképeket, amikor törli a tárolópéldány erőforráscsoportját.

## <a name="authentication"></a>Authentication

Azure tárolóregisztrációs adatbázissal való hitelesítéskor két fő forgatókönyv fordulhat elő: az egyéni hitelesítés és a szolgáltatásos (vagy „távfelügyelt”) hitelesítés. A következő táblázat röviden bemutatja ezeket a forgatókönyveket és a hozzájuk fűződő ajánlott hitelesítési módokat.

| Typo | Példaforgatókönyv | Javasolt módszer |
|---|---|---|
| Egyéni identitás | Egy fejlesztő rendszerképeket hív le a saját számítógépére, vagy helyez át onnan. | [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) |
| Távfelügyelt/szolgáltatásos identitás | Buildelési és üzembe helyezési folyamatok, amelyekben a felhasználó nem vesz közvetlenül részt. | [Egyszerű szolgáltatás](container-registry-authentication.md#service-principal) |

Az Azure Container Registry-vel kapcsolatos részletes információk: [Hitelesítés Azure tárolóregisztrációs adatbázissal](container-registry-authentication.md).

## <a name="manage-registry-size"></a>Regisztrációs adatbázis méretének kezelése

A storage korlátozásokat az egyes [container registry Termékváltozata] [ container-registry-skus] célja, hogy összhangba kerüljenek szokás: **Alapszintű** az első lépésekhez, **Standard** a legtöbb éles üzemi alkalmazások pedig, és **prémium** kapacitású teljesítmény és [georeplikációs][container-registry-geo-replication]. A regisztrációs adatbázis élettartama során érdemes felügyelnie annak méretét a nem használt tartalmak törlésével.

Használja az Azure CLI-paranccsal [az acr show-usage] [ az-acr-show-usage] a regisztrációs adatbázis aktuális méretét megjelenítéséhez:

```console
$ az acr show-usage --resource-group myResourceGroup --name myregistry --output table
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

Használja az aktuális storage is megtalálhatja a **áttekintése** a tárolójegyzék az Azure Portalon:

![Adattár használati adatainak megtekintése az Azure Portalon][registry-overview-quotas]

### <a name="delete-image-data"></a>Rendszerkép-adatok törlése

Az Azure Container Registry a rendszerképet a tárolóregisztrációs adatbázis adatainak törléséről több módszert is támogatja. A képek törlése a címke szerinti vagy kivonatoló manifest, vagy teljes adattár törlése.

További részleteket a rendszerkép-adatok törlése a beállításjegyzékből, beleértve a címkézetlen (más néven "értékhiányos" vagy "árva") lemezképek, lásd: [törlése az Azure Container Registry a tárolólemezképek](container-registry-delete.md).

## <a name="next-steps"></a>További lépések

Az Azure Container Registry több szinten, azaz termékváltozatban érhető el, melyek különféle képességeket biztosítanak. Részletek az elérhető termékváltozatokról: [Az Azure Container Registry termékváltozatai](container-registry-skus.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-show-usage]: /cli/azure/acr#az-acr-show-usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md
