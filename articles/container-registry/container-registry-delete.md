---
title: Az Azure Container Registry képerőforrások törlése
description: Megtudhatja, hogyan hatékonyan kezelheti a regisztrációs adatbázis méretének tároló rendszerkép-adatok törlésével.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/17/2019
ms.author: danlep
ms.openlocfilehash: c603afa61499a615a0882cef06f14fd3d080a9ef
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797770"
---
# <a name="delete-container-images-in-azure-container-registry"></a>Az Azure Container Registry a tárolólemezképek törlése

Az Azure container registry méretének karbantartását, rendszeres időközönként törölni kell a régi kép adatait. Bár egyes éles környezetben üzembe helyezett tárolórendszerképek hosszabb távú tárhelyet igényelhetnek, gyorsabban általában mások lehet törölni. Például egy automatizált összeállítási és a tesztkörnyezet, a beállításjegyzék gyorsan kitöltheti képekkel, előfordulhat, hogy soha nem telepíthető, és hamarosan a létrehozási és tesztelési fázis befejezése után is kiüríthetők.

Törölheti a kép adatait a különféle módokon, mert fontos tudni, hogyan befolyásolja az egyes törlési műveletet a tárhelyhasználat. Ez a cikk ismerteti a rendszerkép-adatok törlése többféle módszer:

* Törölje a [tárház](#delete-repository): Az összes rendszerkép és rendszerképréteg minden egyedi réteg törlése.
* Törölje [címke](#delete-by-tag): Törli a képet, a címke, a lemezkép által hivatkozott összes egyedi réteget és az összes többi címke a lemezképhez hozzárendelt.
* Törölje [jegyzékfájl kivonatoló](#delete-by-manifest-digest): Kép, a lemezkép által hivatkozott összes egyedi réteg és a lemezképhez hozzárendelt összes címke törlése.

A mintaparancsfájlokat törlési műveletek automatizálására.

Ezeket a fogalmakat szeretné megismerni, lásd: [beállításjegyzékek, adattárak és rendszerképek](container-registry-concepts.md).

## <a name="delete-repository"></a>Adattár törlése

Egy adattár törlése törli az összes a tárházban, beleértve az összes címkék, egyedi rétegek és jegyzékek rendszerképet. Ha töröl egy adattár, állítsa helyre a lemezképeket, hogy az adott tárházba egyedi rétegek által felhasznált lemezterület.

A következő Azure CLI-vel a parancs törli a "acr-helloworld" tárházat és az összes címkék és rendszerképréteg jegyzékfájlok. A törölt jegyzékek által hivatkozott rétegek nem hivatkozik a beállításjegyzékben található többi rendszerkép, ha azok rendszerképréteg-adatot is törlődik, helyreállítása a ténylegesen felhasznált tárterület.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Címke törlése

Egyéni rendszerképek adattárból megadásával az adattár nevét és címkéjét a törlési művelettel törölheti. Címke szerinti törlésekor a képen (a rétegek nem osztja meg a beállításjegyzékben található többi rendszerkép a) bármilyen egyedi rétegek által felhasznált lemezterület állítható helyre.

Címke szerinti törléséhez használja [az acr-adattár törlése][az-acr-repository-delete] adja meg a rendszerkép nevét és a `--image` paraméter. A kép egyedi rétegeket, és minden egyéb címkét a lemezképhez hozzárendelt törlődnek.

Ha például töröl a "acr-helloworld:latest" lemezkép "myregistry" beállításjegyzékből:

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> Törlés *címke szerint* nem tévesztendő össze egy (címkék törlése) címke törlése. A címke az Azure CLI-paranccsal törölheti [az acr-tárház untag][az-acr-repository-untag]. Nincs lemezterület nem szabadul, amikor a lemezkép untag, mert annak [manifest](container-registry-concepts.md#manifest) és rendszerképréteg-adatot a beállításjegyzékben. Csak maga a címke hivatkozás törlődik.

## <a name="delete-by-manifest-digest"></a>Jegyzékfájl kivonatos törlése

A [jegyzékfájl kivonatoló](container-registry-concepts.md#manifest-digest) társítható egy, nEz egy vagy több címke. Kivonatos törlésekor összes címkét a jegyzékfájlban által hivatkozott vannak törölni, mert a réteg adatokat bármely, a kép egyedi rétegekhez. Megosztott réteg adatok nem törlődnek.

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

Ezután adja meg a törölni kívánt digest a [az acr-adattár törlése][az-acr-repository-delete] parancsot. A parancs formátuma:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Például a legutóbbi jegyzék törlése a fenti kimenetben szereplő (a "v2" címkével):

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

A `acr-helloworld:v2` lemezkép törlődik a beállításjegyzékből, mivel minden egyedi rendszerkép rendszerképréteg-adatot. Ha egy jegyzéket több címkét társítva, az összes társított címkék is törlődik.

## <a name="delete-digests-by-timestamp"></a>Tárhelyek időbélyegző szerint emésztett törlése

A tárház vagy a beállításjegyzék méretének karbantartását, szüksége lehet régebbi, mint egy adott dátumon jegyzékfájl emésztett rendszeres időközönként törlése.

Az Azure CLI-parancsot egy régebbi, mint a megadott időbélyeg, növekvő sorrendben tárházban az összes jegyzékfájl kivonatoló sorolja fel. Cserélje le `<acrName>` és `<repositoryName>` válasszon a környezetének megfelelő értékekkel. Az időbélyeg vagy dátumot, mint ebben a példában a teljes dátum-idő kifejezés lehet.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Elavult jegyzékfájl emésztett azonosítása, után futtathatja az alábbi Bash-szkript régebbi, mint a megadott időbélyeg jegyzékfájl emésztett törlése. Az Azure CLI szükséges és **xargs**. Alapértelmezés szerint a parancsfájl törlése nem hajtja végre. Módosítsa a `ENABLE_DELETE` értéket a következőre `true` engedélyezéséhez a lemezkép törlése.

> [!WARNING]
> Az alábbi parancsprogram körültekintően – törölt rendszerkép-adatok használata UNRECOVERABLE. Ha rendszereket, kérje le a rendszerképeket jegyzékfájl kivonatos (ellentétben a rendszerkép neve), ezek a parancsfájlok nem futhat. A jegyzékfájl emésztett törlése megakadályozza az ezekhez a rendszerekhez a rendszerképek lekérése a beállításjegyzékből. Lehetőség szerint jegyzékfájl, helyett fontolja meg a bevezetése egy *egyedi címkézés* sémát egy [ajánlott bevált gyakorlat][tagging-best-practices]. 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>A képek címkézetlen törlése

Említetteknek megfelelően az [jegyzékfájl kivonatoló](container-registry-concepts.md#manifest-digest) szakaszban, hogyan lehet továbbítani rá egy meglévő címke használata egy módosított lemezképet **untags** a korábban leküldött rendszerkép, egy árva (vagy "értékhiányos") képet eredményez. A korábban leküldött rendszerkép manifest – és a réteg adatok--maradnak a beállításjegyzékben. Vegye figyelembe a következő lépéseket az események:

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
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

Amint láthatja, hogy a feladatütemezés utolsó lépése kimenetében, nincs-e most egy árva manifest azon `"tags"` tulajdonság üres lista lesz. A jegyzékfájl továbbra is megtalálható a beállításjegyzék minden általa hivatkozott egyedi réteg adatokkal együtt. **Árva törölni, például a képek és a réteg adataikat, törölnie kell a jegyzékfájl kivonatoló**.

## <a name="delete-all-untagged-images"></a>Törölje az összes címkézetlen lemezképet

A tárházban, a következő Azure CLI-paranccsal listázhatja az összes címkézetlen rendszerkép. Cserélje le `<acrName>` és `<repositoryName>` válasszon a környezetének megfelelő értékekkel.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Ezzel a paranccsal egy parancsfájlban, törölheti a tárházban az összes címkézetlen rendszerkép.

> [!WARNING]
> Használja az alábbi minta parancsfájlok körültekintően – törölt képadatok UNRECOVERABLE. Ha rendszereket, kérje le a rendszerképeket jegyzékfájl kivonatos (ellentétben a rendszerkép neve), ezek a parancsfájlok nem futhat. Címkézetlen lemezképek törlése megakadályozza az ezekhez a rendszerekhez a rendszerképek lekérése a beállításjegyzékből. Lehetőség szerint jegyzékfájl, helyett fontolja meg a bevezetése egy *egyedi címkézés* sémát egy [ajánlott bevált gyakorlat][tagging-best-practices].

**A Bash Azure CLI**

Az alábbi Bash-szkript törli az összes címkézetlen rendszerkép-adattárból. Az Azure CLI szükséges és **xargs**. Alapértelmezés szerint a parancsfájl törlése nem hajtja végre. Módosítsa a `ENABLE_DELETE` értéket a következőre `true` engedélyezéséhez a lemezkép törlése.

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
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**A PowerShell az Azure CLI**

A következő PowerShell-parancsfájl törli az összes címkézetlen rendszerkép-adattárból. PowerShell és az Azure CLI van szükség. Alapértelmezés szerint a parancsfájl törlése nem hajtja végre. Módosítsa a `$enableDelete` értéket a következőre `$TRUE` engedélyezéséhez a lemezkép törlése.

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
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```

## <a name="next-steps"></a>További lépések

További információ az Azure Container Registry képtárolás: [képtárolás tárolót az Azure Container Registry](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com
[tagging-best-practices]: https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
