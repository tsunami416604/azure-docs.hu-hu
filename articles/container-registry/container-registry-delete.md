---
title: Az Azure Container Registry képerőforrások törlése
description: Megtudhatja, hogyan hatékonyan kezelheti a regisztrációs adatbázis méretének tároló rendszerkép-adatok törlésével.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 07/27/2018
ms.author: marsma
ms.openlocfilehash: 6ab667a01eddd84d1145868a3ae499e7497035c9
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39265657"
---
# <a name="delete-container-images-in-azure-container-registry"></a>Az Azure Container Registry a tárolólemezképek törlése

Az Azure container registry méretének karbantartását, rendszeres időközönként törölni kell a régi kép adatait. Bár egyes éles környezetben üzembe helyezett tárolórendszerképek hosszabb távú tárhelyet igényelhetnek, gyorsabban általában mások lehet törölni. Például egy automatizált összeállítási és a tesztkörnyezet, a beállításjegyzék gyorsan kitöltheti képekkel, előfordulhat, hogy soha nem telepíthető, és hamarosan a létrehozási és tesztelési fázis befejezése után is kiüríthetők.

Törölheti a kép adatait a különféle módokon, mert fontos tudni, hogyan befolyásolja az egyes törlési műveletet a tárhelyhasználat. Ez a cikk először egy Docker-jegyzék és tároló lemezképek azon összetevőket mutatja be, majd törlése a rendszerkép-adatok több módszerét ismerteti.

## <a name="registry"></a>Beállításjegyzék

Egy tároló *beállításjegyzék* egy szolgáltatás, amely tárolja, és elosztja a tárolórendszerképeket. A docker Hub egy nyilvános Docker-tárolójegyzék, amíg az Azure Container Registry biztosítja magán Docker tároló-beállításjegyzékek az Azure-ban.

## <a name="repository"></a>Tárház

Tároló-beállításjegyzékek kezelése *tárházak*, ugyanazzal a névvel, de eltérő címkéket a tárolólemezképeket gyűjteményei. Az alábbi három lemezképek például a "acr-helloworld" tárház szerepelnek:

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

Adattár neve is tartalmazhatnak [névterek](container-registry-best-practices.md#repository-namespaces). Névterek lehetővé teszi, hogy csoportokba rendezheti a képeket nevekkel előre perjellel elválasztva adattár, például:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="components-of-an-image"></a>Kép összetevői

Egy tárolórendszerképet a beállításjegyzék belül társított egy vagy több címkét, egy vagy több réteget tartalmaz, és a jegyzék azonosítja. Megértéséhez, hogy ezek az összetevők egymáshoz segít meghatározni a legjobb módszer a lemezhely a beállításjegyzékben.

### <a name="tag"></a>Címke

A képfájl *címke* Megadja azt a verziót. Egy adattár belül egyetlen rendszerképből egy vagy több címke lehet hozzárendelni, és is lehet "címkézetlen." Azt jelenti törölheti az összes címke-lemezképről, a rendszerkép-adatok (réteg) továbbra is a beállításjegyzékben.

A tárház (vagy a tárházat és a névtér) és a egy címkét a rendszerkép nevének határozza meg. Leküldéses és a egy rendszerkép lekérése a leküldéses és lekéréses műveletet a neve megadásával.

Például az Azure Container Registry olyan privát beállításjegyzékbe a rendszerkép neve is tartalmaz a teljes nevet, a beállításjegyzék-gazdagép. A rendszerképeket az ACR beállításjegyzék-gazdagépen a következő formátumban kell *acrname.azurecr.io*. Például "marketing" névtér az előző szakaszban látható első ábrával teljes nevét a következő lesz:

```
myregistry.azurecr.io/marketing/campaign10-18/web:v2
```

Ajánlott eljárások címkézés rendszerképen, lásd: a [Docker címkézés: ajánlott eljárások a docker-rendszerképek címkézése és verziókezelését] [ tagging-best-practices] blogbejegyzés az MSDN Webhelyén.

### <a name="layer"></a>Réteg

Képek épülnek fel, egy vagy több *rétegek*, minden egyes egy sort a docker-fájlban, amely meghatározza a kép megfelelő. A regisztrációs adatbázisban található rendszerképek ossza meg közös rétegek, a tárolási hatékonyság növelése mellett. Például számos olyan rendszerkép található különböző tárházakban megoszthatják az Alpine Linux azonos rétegben, de a réteg csak egy példányát tárolja a beállításjegyzékben.

Közös rétegek megosztása több képekkel is réteg megosztása optimalizálja a réteg terjesztési csomópontokra. Ha már a csomóponton lévő kép alapja az Alpine Linux réteget tartalmaz, ugyanazt a rétegre hivatkozó egy másik képet, a későbbi lekéréses nem vigye át például a réteg a csomópontra. Ehelyett azt a réteget, a csomóponton már meglévő hivatkozik.

### <a name="manifest"></a>Jegyzék

Minden tárolórendszerképet fel leküldéssel továbbíthat egy tárolóregisztrációs adatbázisba társítva van egy *manifest*. A jegyzékfájlban, a beállításjegyzék által generált, amikor a lemezkép leküldéssel, egyedileg azonosítja a kép, és megadja a réteg. A jegyzékek tárházhoz az Azure CLI-paranccsal listázhatja [az acr tárház show-jegyzékfájlokká][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Ha például a jegyzékfájl ajánlati digests "acr-helloworld" adattár:

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

A jegyzékfájl itt tárgyalt eltér a megtekintheti az Azure Portalon vagy a lemezkép jegyzékfájljára [vizsgálja meg a docker-jegyzék][docker-manifest-inspect]. A leküldéses művelet által generált nem digest "manifest kivonatoló" hivatkozik a következő szakaszban a *config.digest* a lemezkép-jegyzékfájlban. Kérje le, és törölje a lemezképet **jegyzékfájl kivonatoló**, nem config.digest. Az alábbi ábrán a két típusú emésztett.

![A kivonatoló és az Azure Portalon config.digest manifest][manifest-digest]

### <a name="manifest-digest"></a>A kivonatoló manifest

Jegyzékek azonosítja egy egyedi SHA-256 kivonatoló vagy *jegyzékfájl kivonatoló*. Minden egyes képe – e vagy sem – címkézett azonosítja a kivonatoló. Kivonat értéke egyedi akkor is, ha a kép rendszerképréteg-adatot megegyezik-e egy másik lemezképet. Ez a mechanizmus nem teszi lehetővé a ismételten azonos módon címkézett rendszerképek leküldése a beállításjegyzék. Például ismételten küldhet `myimage:latest` hiba nélkül a regisztrációs adatbázisba, mert az egyedi kivonatoló egyes rendszerképek azonosítása.

A kivonatoló megadásával a pull művelet is hívhatók elő onnan a beállításjegyzékben. Egyes rendszerek kivonatos lekérni, mivel ez garantálja a rendszerkép verziószámát lekért folyamatban van, még akkor is, ha azonos módon megcímkézett képet ezt követően a rendszer továbbítja a beállításjegyzék konfigurálhatók.

Például lehetőség egy képet a "acr-helloworld" adattárból jegyzékfájl kivonatos:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Ha ismételten leküldéses azonos címkékkel módosított képeket, létrehozhat árva lemezképek – címkézetlen, de továbbra is használnak a terület a tárolójegyzékben található rendszerképek. Címkézetlen képek nem jelennek az Azure CLI-ben vagy az Azure Portalon, ha a listában, vagy képeket tekinthetnek címke alapján. A rétegek azonban továbbra is létezik, és a regisztrációs adatbázis terület felhasználását. A [címkézetlen lemezképeket törölni](#delete-untagged-images) Ez a cikk jelen címkézetlen lemezképek által használt felszabadítás területet.

## <a name="delete-image-data"></a>Rendszerkép-adatok törlése

Rendszerkép-adatok többféle módon a tárolóregisztrációs adatbázisból törölheti:

* Törölje a [tárház](#delete-repository): törli az összes rendszerkép és rendszerképréteg minden egyedi réteg.
* Törölje [címke](#delete-by-tag): egy képet, a címke, a lemezkép által hivatkozott összes egyedi réteget és az összes többi címke a lemezképhez hozzárendelt törli.
* Törölje [jegyzékfájl kivonatoló](#delete-by-manifest-digest): egy képet, a lemezkép által hivatkozott összes egyedi réteg és a lemezképhez hozzárendelt összes címke törlése.

## <a name="delete-repository"></a>Adattár törlése

Egy adattár törlése törli az összes a tárházban, beleértve az összes címkék, egyedi rétegek és jegyzékek rendszerképet. Ha töröl egy adattár, állítsa helyre az adott tárházba a lemezképeket által felhasznált lemezterület.

A következő Azure CLI-vel a parancs törli a "acr-helloworld" tárházat és az összes címkék és rendszerképréteg jegyzékfájlok. A törölt jegyzékek által hivatkozott rétegek nem hivatkozik a beállításjegyzékben található többi rendszerkép, ha azok rendszerképréteg-adatot is törlődik.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Címke törlése

Egyéni rendszerképek adattárból megadásával az adattár nevét és címkéjét a törlési művelettel törölheti. Címke szerinti törlésekor a képen (a rétegek nem osztja meg a beállításjegyzékben található többi rendszerkép a) bármilyen egyedi rétegek által felhasznált lemezterület állítható helyre.

Címke szerinti törléséhez használja [az acr-adattár törlése] [ az-acr-repository-delete] adja meg a rendszerkép nevét és a `--image` paraméter. A kép egyedi rétegeket, és minden egyéb címkét a lemezképhez hozzárendelt törlődnek.

Ha például töröl a "acr-helloworld:latest" lemezkép "myregistry" beállításjegyzékből:

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> Törlés *címke szerint* nem tévesztendő össze egy (címkék törlése) címke törlése. A címke az Azure CLI-paranccsal törölheti [az acr-tárház untag][az-acr-repository-untag]. Nincs lemezterület nem szabadul, amikor a lemezkép untag, mert annak [manifest](#manifest) és réteg adatok marad a beállításjegyzékben. Csak maga a címke hivatkozás törlődik.

## <a name="delete-by-manifest-digest"></a>Jegyzékfájl kivonatos törlése

A [jegyzékfájl kivonatoló](#manifest-digest) társítható egy, nEz egy vagy több címke. Kivonatos törlésekor összes címkét a jegyzékfájlban által hivatkozott vannak törölni, mert a réteg adatokat bármely, a kép egyedi rétegekhez. Megosztott réteg adatok nem törlődnek.

Kivonatoló törléséhez az első listában a jegyzékfájlt a törölni kívánt képeket tartalmazó adattár digests. Példa:

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
  }
]
```

Ezután adja meg a törölni kívánt digest a [az acr-adattár törlése] [ az-acr-repository-delete] parancsot. A parancs formátuma:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Például a legutóbbi jegyzék törlése a fenti kimenetben szereplő (a "v2" címkével):

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

A "acr-helloworld:v2" lemezkép törlődik a beállításjegyzékből, mivel minden egyedi rendszerkép rendszerképréteg-adatot. Ha egy jegyzéket több címkét társítva, az összes társított címkék is törlődik.

## <a name="delete-untagged-images"></a>A képek címkézetlen törlése

Említetteknek megfelelően az [jegyzékfájl kivonatoló](#manifest-digest) szakaszban, hogyan lehet továbbítani rá egy meglévő címke használata egy módosított lemezképet **untags** a korábban leküldött rendszerkép, egy árva (vagy "értékhiányos") képet eredményez. A korábban leküldött rendszerkép manifest – és a réteg adatok--maradnak a beállításjegyzékben. Vegye figyelembe a következő lépéseket az események:

1. Rendszerkép leküldése *acr-helloworld* címkével ellátott **legújabb**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Ellenőrizze a tárház jegyzékek *acr-helloworld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. Módosítsa *acr-helloworld* docker-fájlban
1. Rendszerkép leküldése *acr-helloworld* címkével ellátott **legújabb**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Ellenőrizze a tárház jegyzékek *acr-helloworld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": null,
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

Amint láthatja, hogy a feladatütemezés utolsó lépése kimenetében, nincs-e most egy árva manifest azon `"tags"` tulajdonság `null`. A jegyzékfájl továbbra is megtalálható a beállításjegyzék minden általa hivatkozott egyedi réteg adatokkal együtt. **Árva törölni, például a képek és a réteg adataikat, törölnie kell a jegyzékfájl kivonatoló**.

### <a name="list-untagged-images"></a>Címkézetlen lemezképek listázása

A tárházban, a következő Azure CLI-paranccsal listázhatja az összes címkézetlen rendszerkép. Cserélje le `<acrName>` és `<repositoryName>` válasszon a környezetének megfelelő értékekkel.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>  --query "[?tags==null].digest"
```

### <a name="delete-all-untagged-images"></a>Törölje az összes címkézetlen lemezképet

Használja az alábbi minta parancsfájlok körültekintően – törölt képadatok UNRECOVERABLE.

**A Bash Azure CLI**

Az alábbi Bash-szkript törli az összes címkézetlen rendszerkép-adattárból. Az Azure CLI szükséges és **xargs**. Alapértelmezés szerint a parancsfájl törlése nem hajtja végre. Módosítsa a `ENABLE_DELETE` értéket a következőre `true` engedélyezéséhez a lemezkép törlése.

> [!WARNING]
> Ha rendszereket, kérje le a rendszerképeket jegyzékfájl kivonatos (ellentétben a rendszerkép neve), ez a szkript nem futhat. Címkézetlen lemezképek törlése megakadályozza az ezekhez a rendszerekhez a rendszerképek lekérése a beállításjegyzékből. Lehetőség szerint jegyzékfájl, helyett fontolja meg a bevezetése egy *egyedi címkézés* sémát egy [ajánlott bevált gyakorlat][tagging-best-practices].

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted. Set ENABLE_DELETE=true to enable image deletion."
fi
```

**A PowerShell az Azure CLI**

A következő PowerShell-parancsfájl törli az összes címkézetlen rendszerkép-adattárból. PowerShell és az Azure CLI van szükség. Alapértelmezés szerint a parancsfájl törlése nem hajtja végre. Módosítsa a `$enableDelete` értéket a következőre `$TRUE` engedélyezéséhez a lemezkép törlése.

> [!WARNING]
> Ha rendszereket, kérje le a rendszerképeket jegyzékfájl kivonatos (ellentétben a rendszerkép neve), ez a szkript nem futhat. Címkézetlen lemezképek törlése megakadályozza az ezekhez a rendszerekhez a rendszerképek lekérése a beállításjegyzékből. Lehetőség szerint jegyzékfájl, helyett fontolja meg a bevezetése egy *egyedi címkézés* sémát egy [ajánlott bevált gyakorlat][tagging-best-practices].

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted. Set `$enableDelete = `$TRUE to enable image deletion."
}
```

## <a name="next-steps"></a>További lépések

További információ az Azure Container Registry képtárolás: [képtárolás tárolót az Azure Container Registry](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com
[tagging-best-practices]: https://blogs.msdn.microsoft.com/stevelasker/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
