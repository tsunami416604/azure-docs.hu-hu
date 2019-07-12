---
title: Adattárak és rendszerképek az Azure Container Registry
description: Bevezetés az Azure container registryk, adattárak és rendszerképek kapcsolatos főbb fogalmakat.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/01/2019
ms.author: danlep
ms.openlocfilehash: a14f0a2a86c5e4922fcddf3c92d48c6dfb1497a3
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800327"
---
# <a name="about-registries-repositories-and-images"></a>Beállításjegyzékek, adattárak és rendszerképek

Ez a cikk bemutatja a tároló-beállításjegyzékek, az adattárak és a tárolórendszerképek és a kapcsolódó összetevők felhasználásával kapcsolatos főbb fogalmakat. 

## <a name="registry"></a>Beállításjegyzék

Egy tároló *beállításjegyzék* egy szolgáltatás, amely tárolja, és elosztja a tárolórendszerképeket. A docker Hub egy nyilvános tároló-beállításjegyzék, amely támogatja a nyílt forráskódú fejlesztői Közösség és a egy általános katalógus rendszerképek szolgál. Az Azure Container Registry biztosít a közvetlen befolyása alatt képek, beépített hitelesítéssel rendelkező felhasználók [georeplikációs](container-registry-geo-replication.md) hálózatközeli központi telepítése esetén a globális terjesztés és a megbízhatóság támogató [ virtuális hálózat és tűzfal konfigurációját](container-registry-vnet.md), [címke a zárolása](container-registry-image-lock.md), és sok egyéb funkciókat. 

Docker-tárolórendszerképekhez mellett az Azure Container Registry támogatja a kapcsolódó [összetevők tartalom](container-registry-image-formats.md) Open Container kezdeményezés (OCI) képformátum többek között.

## <a name="content-addressable-elements-of-an-artifact"></a>Egy tartalom címezhető elemei

Az Azure container registry-összetevő címét az alábbi elemeket tartalmazza. 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** – a teljes nevet, a beállításjegyzék-gazdagép. A beállításjegyzék-gazdagép az Azure container registry a következő formátumban kell *myregistry*. azurecr.io (csak kisbetűvel). Meg kell adnia a loginUrl, Docker vagy más ügyfél eszközöket lekéréses vagy leküldéses összetevők használata egy Azure container registrybe. 
* **névtér** - perjellel elválasztva a logikai jellegű csoportosítását kapcsolódó képeket és az összetevők – például egy munkacsoportban vagy egy alkalmazás esetében
* **összetevő** – egy adott rendszerképet vagy összetevő egy adattár nevét
* **címke** – képet vagy az adattárakban tárolt összetevő egy adott verzióját


Ha például egy Azure container registry egy kép a teljes név hasonló lehet:

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

Ezek az elemek részleteit a következő szakaszokban talál.

## <a name="repository-name"></a>Adattár neve

Tároló-beállításjegyzékek kezelése *tárházak*, gyűjtemények tárolórendszerképek vagy más összetevők ugyanazzal a névvel, de eltérő címkéket. Az alábbi három lemezképek például a "acr-helloworld" tárház szerepelnek:

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

Adattár neve is tartalmazhatnak [névterek](container-registry-best-practices.md#repository-namespaces). Névterek lehetővé teszi a csoport lemezképek nevekkel előre perjellel elválasztva adattár, például:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="image"></a>Image

Egy tárolórendszerkép- vagy más összetevő belül beállításjegyzék társított egy vagy több címkét, egy vagy több réteget rendelkezik, és a jegyzék azonosítja. Megértéséhez, hogy ezek az összetevők egymáshoz segíthet a registry hatékony kezeléséhez.

### <a name="tag"></a>Címke

A *címke* kép vagy más összetevő határozza meg az a verzió esetében. Egy egyetlen összetevő belül egy tárház egy vagy több címke lehet hozzárendelni, és is lehet "címkézetlen." Azt jelenti törölheti az összes címke-lemezképről, a rendszerkép-adatok (réteg) továbbra is a beállításjegyzékben.

A tárház (vagy a tárházat és a névtér) és a egy címkét a rendszerkép nevének határozza meg. Leküldéses és a egy rendszerkép lekérése a leküldéses és lekéréses műveletet a neve megadásával.

Tárolórendszerképek címkézése hogyan hozhat létre és telepíthet a forgatókönyvek vezérli. Az alaplemezképek és lemezképek telepítése egyedi címkék megőrzése mellett például stabil címkék használata javasolt. További információkért lásd: [tárolórendszerképek címkézése és verziókezelését javaslatok](container-registry-image-tag-version.md).

### <a name="layer"></a>Réteg

Tárolórendszerképek épülnek fel, egy vagy több *rétegek*, minden egyes egy sort a docker-fájlban, amely meghatározza a kép megfelelő. A regisztrációs adatbázisban található rendszerképek ossza meg közös rétegek, a tárolási hatékonyság növelése mellett. Például számos olyan rendszerkép található különböző tárházakban megoszthatják az Alpine Linux azonos rétegben, de a réteg csak egy példányát tárolja a beállításjegyzékben.

Közös rétegek megosztása több képekkel is réteg megosztása optimalizálja a réteg terjesztési csomópontokra. Ha már a csomóponton lévő kép alapja az Alpine Linux réteget tartalmaz, ugyanazt a rétegre hivatkozó egy másik képet, a későbbi lekéréses nem vigye át például a réteg a csomópontra. Ehelyett azt a réteget, a csomóponton már meglévő hivatkozik.

Biztonságos elkülönítésre, és lehetséges réteg adatkezelési védelmet biztosít, a rétegek nem közösek beállításjegyzékek.

### <a name="manifest"></a>Jegyzék

Minden tárolórendszerképet vagy leküldéssel továbbíthat egy tárolóregisztrációs adatbázisba összetevő társítva van egy *manifest*. A jegyzékfájlban, a beállításjegyzék által generált, amikor a lemezkép leküldéssel, egyedileg azonosítja a kép, és megadja a réteg. A jegyzékek tárházhoz az Azure CLI-paranccsal listázhatja [az acr tárház show-jegyzékfájlokká][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Ha például listája a jegyzékfájl digests "acr-helloworld" adattár:

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
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

### <a name="manifest-digest"></a>A kivonatoló manifest

Jegyzékek azonosítja egy egyedi SHA-256 kivonatoló vagy *jegyzékfájl kivonatoló*. Minden rendszerkép vagy összetevő – e vagy sem – címkézett azonosítja a kivonatoló. Kivonat értéke egyedi akkor is, ha a kép rendszerképréteg-adatot megegyezik-e egy másik lemezképet. Ez a mechanizmus nem teszi lehetővé a ismételten azonos módon címkézett rendszerképek leküldése a beállításjegyzék. Például ismételten küldhet `myimage:latest` hiba nélkül a regisztrációs adatbázisba, mert az egyedi kivonatoló egyes rendszerképek azonosítása.

A kivonatoló megadásával a pull művelet is hívhatók elő onnan a beállításjegyzékben. Egyes rendszerek kivonatos lekérni, mivel ez garantálja a rendszerkép verziószámát lekért folyamatban van, még akkor is, ha azonos módon megcímkézett képet ezt követően a rendszer továbbítja a beállításjegyzék konfigurálhatók.

Például egy rendszerkép lekéréshez a "acr-helloworld" tárház jegyzékfájl kivonatos:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Ha ismételten leküldéses azonos címkékkel módosított képeket, létrehozhat árva lemezképek – címkézetlen, de továbbra is használnak a terület a tárolójegyzékben található rendszerképek. Címkézetlen képek nem jelennek az Azure CLI-ben vagy az Azure Portalon, ha a listában, vagy képeket tekinthetnek címke alapján. A rétegek azonban továbbra is létezik, és a regisztrációs adatbázis terület felhasználását. További információ a rendszerképek címkézetlen által használt területet szabadítson fel: [törlése az Azure Container Registry a tárolólemezképek](container-registry-delete.md).


## <a name="next-steps"></a>További lépések

Tudjon meg többet [képtárolás](container-registry-storage.md) és [támogatott tartalom formátumok](container-registry-image-formats.md) az Azure Container Registryben.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


