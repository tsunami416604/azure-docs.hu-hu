---
title: Adattárak & képek –
description: Az Azure-tároló-jegyzékek, adattárak és tárolórendszerképek fő fogalmai.
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: ea6e2577d3eee91626dd613617a0b79e4ff3d6a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247058"
---
# <a name="about-registries-repositories-and-images"></a>A nyilvántartások, adattárak és képek –

Ez a cikk bemutatja a tárolójegyzékek, adattárak és tárolórendszerképek és a kapcsolódó összetevők legfontosabb fogalmait. 

## <a name="registry"></a>Registry

A *tárolóbeállításjegyzék* olyan szolgáltatás, amely tárolja és terjeszti a tárolórendszerképeket. A Docker Hub egy nyilvános tároló-beállításjegyzék, amely támogatja a nyílt forráskódú közösséget, és a lemezképek általános katalógusaként szolgál. Az Azure Container Registry a felhasználók számára közvetlen irányítást biztosít a lemezképek felett, integrált hitelesítéssel, a globális terjesztést és a hálózati telepítéseket, [a virtuális hálózat és a tűzfal konfigurációjának,](container-registry-vnet.md)a [címkezárolásnak](container-registry-image-lock.md)és sok más továbbfejlesztett szolgáltatásnak a megbízhatóságát támogató [georeplikációval.](container-registry-geo-replication.md) 

A Docker-tárolórendszerképek mellett az Azure Container Registry támogatja a kapcsolódó [tartalomösszetevőket,](container-registry-image-formats.md) beleértve az Open Container Initiative (OCI) rendszerképeket is.

## <a name="content-addressable-elements-of-an-artifact"></a>A műtermék tartalomcímezhető elemei

Egy összetevő címe egy Azure-tároló beállításjegyzékben a következő elemeket tartalmazza. 

`[loginUrl]/[namespace]/[artifact:][tag]`

* **loginUrl** - A rendszerleíró állomás teljesen minősített neve. Az Azure-tároló beállításjegyzékében lévő beállításjegyzék-állomás *a myregistry*.azurecr.io (mind kisbetűs) formátumú. Meg kell adnia a loginUrl használatakor Docker vagy más ügyféleszközök lekérése vagy leküldéses összetevők egy Azure-tároló beállításjegyzékbe. 
* **névtér** – Kapcsolódó képek vagy műtermékek perjel-körültagolt logikai csoportosítása – például munkacsoport vagy alkalmazás esetében
* **må±** egy adott kép vagy műtermék tárházának neve
* **címke** - Egy adattárban tárolt kép vagy műtermék adott verziója


Például egy azure-tároló beállításjegyzékében egy lemezkép teljes neve így nézhet ki:

*myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2*

Ezekről az elemekről a következő szakaszokban talál részleteket.

## <a name="repository-name"></a>Tárház neve

A tárolónyilvántartások *kezelik az adattárakat,* a tárolórendszerképek gyűjteményét vagy más, azonos nevű, de eltérő címkéket tartalmazó összetevőket. Például a következő három kép található az "acr-helloworld" tárházban:


- *acr-helloworld:legújabb*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

A tárháznevek [névtereket is tartalmazhatnak.](container-registry-best-practices.md#repository-namespaces) A névterek lehetővé teszik a képek csoportosítását perjellel tagolt tárháznevek használatával, például:

- *marketing/campaign10-18/web:v2*
- *marketing/campaign10-18/api:v3*
- *marketing/campaign10-18/email-sender:v2*
- *termék-visszaküldések/webes benyújtás:20180604*
- *termék-vissza-vissza/örökölt-integrátor:20180715*

## <a name="image"></a>Kép

A rendszerleíró adatbázison belüli tárolórendszerkép vagy más műtermék egy vagy több címkével van társítva, egy vagy több réteggel rendelkezik, és egy jegyzék azonosítja. Ha tisztában van azzal, hogy ezek az összetevők hogyan kapcsolódnak egymáshoz, akkor hatékonyan kezelheti a rendszerleíró adatbázist.

### <a name="tag"></a>Címke

Egy lemezkép vagy más műtermék *címkéje* határozza meg annak verzióját. A tárházban egyetlen összetevőhöz egy vagy több címke rendelhető, és "címkézetlen" is lehet. Ez azt illeti, törölheti az összes címkét egy képből, míg a kép adatai (rétegei) a rendszerleíró adatbázisban maradnak.

A tárház (vagy tárház és névtér), valamint egy címke határozza meg a kép nevét. A leküldéses vagy lekéréses műveletben megadhatja a kép nevét.

A tárolórendszerképek címkézésének módját a forgatókönyvek vezérlik a fejlesztésükhöz vagy üzembe helyezésükhöz. Például stabil címkék ajánlott az alaplemezképek karbantartásához, és egyedi címkék a lemezképek üzembe helyezéséhez. További információ: [Javaslatok a tárolórendszerképek címkézéséhez és verziószámozásához.](container-registry-image-tag-version.md)

### <a name="layer"></a>Réteg

A tárolórendszerképek egy vagy több *rétegből*állnak, amelyek mindegyike a lemezképet meghatározó Docker-fájl ban lévő egy sornak felel meg. A beállításjegyzékben lévő képek közös rétegeken osztoznak, ami növeli a tárolási hatékonyságot. Például több különböző adattárakban több lemezképek is megoszthatja ugyanazt az Alpine Linux alapréteg, de csak egy példányát, hogy a réteg tárolja a rendszerleíró adatbázisban.

A rétegmegosztás a rétegeloszlást olyan csomópontokra is optimalizálja, ahol több kép osztozik a közös rétegeken. Ha például egy csomóponton már egy lemezkép alapként az Alpesi Linux-réteget tartalmazza, az ugyanazon rétegre hivatkozó másik lemezkép későbbi lekérése nem viszi át a réteget a csomópontra. Ehelyett a csomóponton már meglévő rétegre hivatkozik.

A rétegek biztonságos elkülönítése és védelme érdekében a rétegek nincsenek megosztva a kibocsátásijegyzékek között.

### <a name="manifest"></a>Jegyzék

Minden tárolórendszerkép vagy műtermék egy tároló *manifest*beállításjegyzékbe van társítva egy jegyzékfájlhoz. A jegyzék, amelyet a beállításjegyzék a rendszerkép leküldésekor hoz létre, egyedileg azonosítja a lemezképet, és megadja annak rétegeit. Az Azure CLI parancs [az acr tárház show-manifest-ekkel][az-acr-repository-show-manifests]sorolása:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Például sorolja fel az "acr-helloworld" tárház jegyzékfájljait:

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Nyilvánvaló kivonat

A manifeszteket egyedi SHA-256 kivonat vagy *nyilvánvaló kivonat azonosítja.* Minden képet vagy műtermék - akár címkézett, akár nem - a kivonata azonosítja. A kivonatoló érték akkor is egyedi, ha a kép rétegadatai megegyeznek egy másik kép ével. Ez a mechanizmus az, ami lehetővé teszi, hogy ismételten leküldéseazonos címkézett képeket a rendszerleíró adatbázisba. Például többször is leküldéses `myimage:latest` a rendszerleíró adatbázisba hiba nélkül, mert minden lemezképet az egyedi kivonatoló azonosít.

A lemezképet a beállításjegyzékből úgy húzhatja le, hogy megadja annak kivonatolását a lekéréses műveletben. Egyes rendszerek úgy konfigurálhatók, hogy kivonatolással lekérik, mert garantálja a lehúzandó lemezkép-verziót, még akkor is, ha egy azonos címkével ellátott lemezképet később a rendszerleíró adatbázisba tol.

Például lekérni egy képet az "acr-helloworld" tárházból a manifest digest szerint:

`docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`

> [!IMPORTANT]
> Ha ismételten lenyomja a módosított képeket azonos címkékkel, előfordulhat, hogy árva képeket hoz létre – olyan képeket, amelyek nincsenek címkézve, de továbbra is helyet foglalnak a beállításjegyzékben. Címkézetlen képek nem jelennek meg az Azure CLI vagy az Azure Portalon, ha címke szerint listázvagy tekint képeket. A rétegek azonban továbbra is léteznek, és helyet foglalnak el a beállításjegyzékben. A címkézetlen lemezkép törlése helyet szabadít fel a rendszerleíró adatbázisszámára, ha a jegyzékfájl az egyetlen, vagy az utolsó, amely egy adott rétegre mutat. A címkézetlen lemezképek által használt terület felszabadításáról a [Tárolórendszerképek törlése az Azure Container Registry alkalmazásban című témakörben](container-registry-delete.md)talál további információt.

## <a name="next-steps"></a>További lépések

További információ a [lemezképek tárolásáról](container-registry-storage.md) és [a támogatott tartalomformátumokról](container-registry-image-formats.md) az Azure Container Registry ben.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


