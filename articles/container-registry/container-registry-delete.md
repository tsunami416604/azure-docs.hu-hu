---
title: Képforrások törlése
description: Részletek arról, hogyan hatékonyan kezelheti a beállításjegyzék méretét a tárolórendszerkép-adatok Azure CLI-parancsok használatával történő törlésével.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 449a1c09bf88e3e0e0aeca4d3b687371d2a6b91a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78403337"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Tárolórendszerképek törlése az Azure Container Registry szolgáltatásban az Azure CLI használatával

Az Azure-tároló beállításjegyzékméretének megőrzése érdekében rendszeresen törölnie kell az elavult lemezképadatokat. Míg egyes tárolórendszerképek éles környezetben üzembe helyezett hosszabb távú tárolást igényelhet, mások általában gyorsabban törölhetők. Egy automatikus build elkészült és tesztelési forgatókönyv esetén például a beállításjegyzék gyorsan kitölthetolyan lemezképeket, amelyek soha nem lesznek telepítve, és röviddel a build- és tesztbérlet befejezése után törölhetők.

Mivel a képadatokat többféleképpen is törölheti, fontos megérteni, hogy az egyes törlési műveletek hogyan befolyásolják a tárhelyhasználatot. Ez a cikk a képadatok törlésének számos módszerét ismerteti:

* [Tárház](#delete-repository)törlése : Törli az összes képet és az összes egyedi réteget a tárházban.
* Törlés [címke](#delete-by-tag)szerint: Törli a képet, a címkét, a kép által hivatkozott összes egyedi réteget és a képhez társított összes többi címkét.
* Törlés [jegyzékfájl kivonatolószerint:](#delete-by-manifest-digest)Törli a képet, a kép által hivatkozott összes egyedi réteget és a képhez társított összes címkét.

A törlési műveletek automatizálásához mintaparancsfájlok állnak rendelkezésre.

Ezeknek a fogalmaknak a bemutatása: [A nyilvántartások, adattárak és képek](container-registry-concepts.md)– itt található.

## <a name="delete-repository"></a>Adattár törlése

A tárház törlése törli a tárházban lévő összes képet, beleértve az összes címkét, egyedi réteget és jegyzékfájlt. A tárház törlésekor helyreállítja a tárházban lévő egyedi rétegekre hivatkozó lemezképek által használt tárhelyet.

A következő Azure CLI parancs törli az "acr-helloworld" tárházat, és az összes címkét és jegyzéket a tárházban. Ha a törölt jegyzékek által hivatkozott rétegekre a beállításjegyzékmás lemezképei nem hivatkoznak, a rétegadatok is törlődnek, helyreállítva a tárhelyet.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Törlés címke szerint

Az egyes képeket törölheti a tárházból a tárház nevének és címkéjének megadásával a törlési műveletben. Címke szerinti törléskor helyreállítja a kép bármely egyedi rétege által használt tárhelyet (a rendszerleíró adatbázis más lemezképei által nem megosztott rétegek).

Címke szerint törölni, használja [az acr repository törölni,][az-acr-repository-delete] és adja meg a kép nevét a `--image` paraméterben. A képre vonatkozó összes réteg és a képhez társított egyéb címkék törlődnek.

Például az "acr-helloworld:latest" lemezkép törlése a rendszerleíró adatbázisból "myregistry":

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> A *címke törlése* nem tévesztendő össze a címke törlésével (kitagolás nélküliítéssel). Törölheti a címkét az Azure CLI parancs [az acr repository untag][az-acr-repository-untag]. A lemezkép címkézésének kioldásakor a rendszer nem szabadít fel szabad területet, mert a [jegyzékfájl és](container-registry-concepts.md#manifest) a rétegadatok a beállításjegyzékben maradnak. Csak maga a címkehivatkozás törlődik.

## <a name="delete-by-manifest-digest"></a>Törlés jegyzékfájl kivonatolója szerint

A [jegyzékfájl kivonatolója](container-registry-concepts.md#manifest-digest) egy, egyikhez vagy több címkéhez is társítható. Ha kivonatoló ként töröl, a jegyzékfájl által hivatkozott összes címke törlődik, csakúgy, mint a képre egyedi rétegek rétegadatai. A megosztott rétegadatok nem törlődnek.

A törléshez először sorolja fel a törölni kívánt képeket tartalmazó tárház jegyzékfájlkivonatait. Példa:

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
  }
]
```

Ezután adja meg a törölni kívánt kivonatot az [az acr repository delete][az-acr-repository-delete] parancsban. A parancs formátuma:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Például az előző kimenetben felsorolt utolsó jegyzékfájl törléséhez (a "v2" címkével):

```azurecli
az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
```

```output
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): 
```

A `acr-helloworld:v2` rendszerkép törlődik a rendszerleíró adatbázisból, csakúgy, mint az adott lemezképre vonatkozó bármely rétegadat. Ha egy jegyzékfájl több címkéhez van társítva, az összes társított címke is törlődik.

## <a name="delete-digests-by-timestamp"></a>Kivonattörlése időbélyeggel

A tárház vagy beállításjegyzék méretének fenntartásához előfordulhat, hogy rendszeresen törölnie kell a jegyzékfájl kivonatolóit, amelyek egy bizonyos dátumnál régebbiek.

A következő Azure CLI parancs felsorolja az összes jegyzékfájl kivonategy tárházban régebbi, mint egy megadott időbélyeg, növekvő sorrendben. Cserélje `<acrName>` `<repositoryName>` le és az adott környezetnek megfelelő értékekre cserélje ki. Az időbélyeg lehet egy teljes dátum-idő kifejezés vagy egy dátum, mint ebben a példában.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Az elavult jegyzékfájl kivonatának azonosítása után futtathatja a következő Bash parancsfájlt a megadott időbélyegnél régebbi jegyzékkivonatok törléséhez. Ehhez az Azure CLI és **xargs**. Alapértelmezés szerint a parancsfájl nem hajt végre törlést. A `ENABLE_DELETE` kép `true` törlésének engedélyezéséhez módosítsa az értéket.

> [!WARNING]
> Használja a következő minta parancsfájl óvatosan-törölt kép adatok nem helyreállítható. Ha olyan rendszerekkel rendelkezik, amelyek a listákat tartalmazó kivonattal (a lemezkép nevével ellentétben) lekérik a képeket, ne futtassa ezeket a parancsfájlokat. A jegyzékfájl kivonatainak törlése megakadályozza, hogy ezek a rendszerek lehúzzák a képeket a rendszerleíró adatbázisból. Ahelyett, hogy a manifesztet húzná, fontolja meg egy *egyedi címkézési* rendszer elfogadását, amely [ajánlott ajánlott eljárás.](container-registry-image-tag-version.md) 

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

## <a name="delete-untagged-images"></a>Címkézetlen képek törlése

Ahogy azt a [Manifest kivonatolási](container-registry-concepts.md#manifest-digest) szakaszban is említette, egy módosított kép leküldése egy meglévő címkével a korábban lenyomott kép **kéjezése,** ami egy árva (vagy "lógó") képet eredményez. A korábban leadott lemezkép jegyzékfájlja – és a rétegadatai – a beállításjegyzékben marad. Vegye figyelembe a következő eseménysorozatot:

1. Push kép *acr-helloworld* tag **legújabb:**`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Ellenőrizze a jegyzékek tárház *acr-helloworld:*

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   
   ```
   
   ```output
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

1. *Acr-helloworld* Docker-fájl módosítása
1. Push kép *acr-helloworld* tag **legújabb:**`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Ellenőrizze a jegyzékek tárház *acr-helloworld:*

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   ```
   
   ```output
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

Amint az a sorozat utolsó lépésének kimenetében látható, most egy `"tags"` árva jegyzék van, amelynek tulajdonsága egy üres lista. Ez a jegyzékfájl továbbra is létezik a beállításjegyzékben, az általa hivatkozott egyedi rétegadatokkal együtt. **Az ilyen árva képek és rétegadataik törléséhez a jegyzékfájl kivonatolója szerint törölnie kell**a fájlt.

## <a name="delete-all-untagged-images"></a>Az összes címkézetlen kép törlése

Az összes címkézetlen lemezképek a tárházban a következő Azure CLI parancs használatával. Cserélje `<acrName>` `<repositoryName>` le és az adott környezetnek megfelelő értékekre cserélje ki.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Ezzel a parancsfájlban ezzel a paranccsal törölheti az összes címkézetlen képet a tárházban.

> [!WARNING]
> Használja a következő mintaparancsfájlokat óvatosan – a törölt képadatok helyreállíthatatlanok. Ha olyan rendszerekkel rendelkezik, amelyek a listákat tartalmazó kivonattal (a lemezkép nevével ellentétben) lekérik a képeket, ne futtassa ezeket a parancsfájlokat. A címkézetlen képek törlése megakadályozza, hogy ezek a rendszerek lehúzzák a képeket a rendszerleíró adatbázisból. Ahelyett, hogy a manifesztet húzná, fontolja meg egy *egyedi címkézési* rendszer elfogadását, amely [ajánlott ajánlott eljárás.](container-registry-image-tag-version.md)

**Azure CLI a Bash-ben**

A következő Bash parancsfájl törli az összes címkézetlen lemezképet egy tárházból. Ehhez az Azure CLI és **xargs**. Alapértelmezés szerint a parancsfájl nem hajt végre törlést. A `ENABLE_DELETE` kép `true` törlésének engedélyezéséhez módosítsa az értéket.

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
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**Azure CLI a PowerShellben**

A következő PowerShell-parancsfájl törli az összes címkézetlen lemezképet egy tárházból. Ehhez PowerShell és az Azure CLI szükséges. Alapértelmezés szerint a parancsfájl nem hajt végre törlést. A `$enableDelete` kép `$TRUE` törlésének engedélyezéséhez módosítsa az értéket.

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


## <a name="automatically-purge-tags-and-manifests-preview"></a>Címkék és jegyzékfájlok automatikus végleges törlése (előzetes verzió)

Az Azure CLI-parancsok parancsfájl-alapú futtatásának alternatívájaként futtasson egy igény szerinti vagy ütemezett ACR-feladatot, hogy törölje az összes olyan címkét, amely egy bizonyos időtartamnál régebbi, vagy egy adott névszűrőnek felel meg. További információ: [A lemezképek automatikus törlése az Azure-tároló beállításjegyzékéből](container-registry-auto-purge.md)című témakörben talál.

Szükség esetén minden beállításjegyzékhez be állíthat egy [adatmegőrzési szabályt](container-registry-retention-policy.md) a címkézetlen jegyzékek kezeléséhez. Ha engedélyezi az adatmegőrzési házirendet, a rendszerleíró adatbázisban olyan képjegyzékek jelennek meg, amelyek nem rendelkeznek társított címkékkel, és az alapul szolgáló rétegadatok automatikusan törlődnek egy meghatározott időszak után.

## <a name="next-steps"></a>További lépések

Az Azure Container Registry rendszerkép-tárolásával kapcsolatos további információkért tekintse [meg a Tárolórendszerkép-tárolás t az Azure Container Registry szolgáltatásban.](container-registry-storage.md)

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
