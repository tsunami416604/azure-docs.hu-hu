---
title: Ajánlott eljárások a beállításjegyzék használatához
description: Az ajánlott eljárások követésével megismerkedhet az Azure Container Registry leghatékonyabb használatával.
ms.topic: article
ms.date: 09/27/2018
ms.openlocfilehash: 233d84b8bfa6f3d8c800e76032ef74a643db11ca
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668662"
---
# <a name="best-practices-for-azure-container-registry"></a>Az Azure Container Registry ajánlott eljárásai

Az itt leírt ajánlott eljárások követésével a legjobb teljesítménnyel és költséghatékonysággal használhatja az Azure-ban található privát Docker regisztrációs adatbázist.

Lásd még: [javaslatok a lemezképek címkézésére és verziószámozására](container-registry-image-tag-version.md) a beállításjegyzékben található rendszerképek címkézéséhez és verziószámozásához. 

## <a name="network-close-deployment"></a>Hálózatközeli központi telepítés

A tárolóregisztrációs adatbázist ugyanabban az Azure-régióban hozza létre, amelyben a tárolókat helyezi üzembe. Ha a regisztrációs adatbázist a tároló gazdagéphez hálózatban közeli régióba telepíti, az csökkentheti a késést és a költségeket is.

A hálózatközeli központi telepítés a privát tárolóregisztrációs adatbázis használatának egyik fő oka. A Docker-rendszerképek hatékony [rétegezett felépítésüknek](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) köszönhetően növekményesen is telepíthetők. Az új csomópontoknak azonban egy adott rendszerképhez minden szükséges réteget le kell kérniük. A kezdeti `docker pull` hamar akár több gigabájttá nőheti ki magát. Az üzemelő példányához közeli privát regisztrációs adatbázis csökkenti a hálózati késést.
A nyilvános felhők – köztük az Azure is – hálózati forgalmi díjat számítanak fel. A rendszerképek egyes adatközpontok közötti mozgatása hálózati forgalmi díjjal is jár a nagyobb mértékű késés mellett.

## <a name="geo-replicate-multi-region-deployments"></a>Többrégiós üzemelő példányok georeplikációja

Ha több régióban telepít tárolókat, használja az Azure Container Registry [georeplikációs](container-registry-geo-replication.md) funkcióját. Ha globális ügyfélbázist szolgál ki helyi adatközpontokból, vagy fejlesztői csapatának tagjai különböző helyeken tartózkodnak, a regisztrációs adatbázis georeplikálásával egyszerűsítheti a regisztrációs adatbázis kezelését és minimalizálhatja a késést. A georeplikáció csak [Prémium](container-registry-skus.md) szintű regisztrációs adatbázisok esetén érhető el.

A georeplikáció használatának megismeréséhez tekintse meg háromrészes útmutatónkat: [Georeplikáció az Azure Container Registry-ben](container-registry-tutorial-prepare-registry.md).

## <a name="repository-namespaces"></a>Adattárnévterek

Az adattárnévterek használatával engedélyezheti egyetlen regisztrációs adatbázis megosztását több csoporttal a szervezeten belül. A regisztrációs adatbázisok több környezeten és csoporton keresztül is megoszthatók. Az Azure Container Registry támogatja a beágyazott névtereket, amelyekkel elkülöníthetők a csoportok.

Vegyük példaként a következő tárolórendszerkép-címkéket. A vállalati szintű, például a `aspnetcore`hoz használt rendszerképek a legfelső szintű névtérbe kerülnek, míg a termékek és a marketing csoportok által birtokolt tároló lemezképek saját névtereket használnak.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>Dedikált erőforráscsoport

Mivel a tároló-beállításjegyzékek olyan erőforrások, amelyeket több tároló gazdagépe használ, a beállításjegyzéknek a saját erőforráscsoporthoz kell tartoznia.

Nyugodtan kísérletezhet speciális gazdagéptípusokkal, például az Azure Container Instances-zel, de utána valószínűleg törölni szeretné majd a tárolópéldányt. Előfordulhat azonban, hogy meg szeretné tartani azokat a rendszerképeket, amelyeket átvitt az Azure Container Registry-be. Azzal, hogy a regisztrációs adatbázis a saját erőforráscsoportjába helyezi, csökkentheti annak esélyét, hogy véletlenül törli a rendszerképeket, amikor törli a tárolópéldány erőforráscsoportját.

## <a name="authentication"></a>Hitelesítés

Azure tárolóregisztrációs adatbázissal való hitelesítéskor két fő forgatókönyv fordulhat elő: az egyéni hitelesítés és a szolgáltatásos (vagy „távfelügyelt”) hitelesítés. A következő táblázat röviden bemutatja ezeket a forgatókönyveket és a hozzájuk fűződő ajánlott hitelesítési módokat.

| Típus | Példaforgatókönyv | Javasolt módszer |
|---|---|---|
| Egyéni identitás | Egy fejlesztő rendszerképeket hív le a saját számítógépére, vagy helyez át onnan. | [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) |
| Távfelügyelt/szolgáltatásos identitás | Buildelési és üzembe helyezési folyamatok, amelyekben a felhasználó nem vesz közvetlenül részt. | [Egyszerű szolgáltatás](container-registry-authentication.md#service-principal) |

Az Azure Container Registry-vel kapcsolatos részletes információk: [Hitelesítés Azure tárolóregisztrációs adatbázissal](container-registry-authentication.md).

## <a name="manage-registry-size"></a>Regisztrációs adatbázis méretének kezelése

Az egyes [tárolók beállításjegyzékbeli SKU][container-registry-skus] tárolási korlátozásai egy tipikus forgatókönyvhöz igazodnak: **alapszintű** , első lépések, **standard** az üzemi alkalmazások többsége számára, valamint **prémium** szintű teljesítmény és [földrajzi replikálás][container-registry-geo-replication]. A regisztrációs adatbázis élettartama során érdemes felügyelnie annak méretét a nem használt tartalmak törlésével.

Használja az Azure CLI-parancsot az [ACR show-][az-acr-show-usage] use paranccsal a beállításjegyzék aktuális méretének megjelenítéséhez:

```azurecli
az acr show-usage --resource-group myResourceGroup --name myregistry --output table
```

```output
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

A Azure Portalban található beállításjegyzék **áttekintésében** használt aktuális tárterületet is megtalálhatja:

![Adattár használati adatainak megtekintése az Azure Portalon][registry-overview-quotas]

### <a name="delete-image-data"></a>Rendszerkép-adatok törlése

Azure Container Registry számos módszert támogat a képadatoknak a tároló-beállításjegyzékből való törléséhez. A képeket címkével vagy jegyzékfájl-kivonattal is törölheti, vagy törölheti a teljes tárházat.

A képadatok beállításjegyzékből való törlésével kapcsolatos részletekért, beleértve a címkézett (más néven "lelógó" vagy "árva") lemezképeket, lásd: [tároló lemezképek törlése Azure Container Registry](container-registry-delete.md).

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
