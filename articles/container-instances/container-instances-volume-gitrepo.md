---
title: Azure-tároló példányok gitRepo kötet csatlakoztatása
description: 'Útmutató: a Git-tárház klónozása be a tároló példányok gitRepo kötet csatlakoztatása'
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/16/2018
ms.author: marsma
ms.openlocfilehash: e40d841c07534c9c0074c038d1e3c6e435265564
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Azure-tároló példányát gitRepo kötet csatlakoztatása

Megtudhatja, hogyan csatlakoztatása egy *gitRepo* kötet egy Git-tárház klónozása be a tároló példányok.

> [!NOTE]
> Csatlakoztassa a *gitRepo* kötet korlátozódik jelenleg Linux tárolók. Arra törekszünk, hogy idővel az összes funkció elérhető legyen a Windows-tárolókon is. Az egyes platformok közötti aktuális eltérésekről a [Azure Container Instances-kvóták és -régiók rendelkezésre állása](container-instances-quotas.md) részben tájékozódhat.

## <a name="gitrepo-volume"></a>gitRepo kötet

A *gitRepo* kötet csatlakoztatja egy könyvtárat, majd a megadott Git-tárház klónokat bele a tároló indításakor. Használatával egy *gitRepo* kötet a tároló példányát, elkerülheti a kódját, így az alkalmazások hozzáadását.

Csatlakoztatásakor a *gitRepo* kötet, megadhatja a kötet konfigurálása három tulajdonságait:

| Tulajdonság | Szükséges | Leírás |
| -------- | -------- | ----------- |
| `repository` | Igen | A teljes URL-címet, beleértve a `http://` vagy `https://`, a Git-tárház klónozásának.|
| `directory` | Nem | Könyvtár, amelyben a tárház klónozása kell. Az elérési út kell tartalmaznia, vagy nem kezdődhet "`..`".  Ha megad "`.`", a tárház klónozták a kötet könyvtárba. Ellenkező esetben a Git-tárház klónozták be a megadott néven, a kötet könyvtárban lévő egyik alkönyvtár. |
| `revision` | Nem | A-verziójának klónozható véglegesítési kivonatát. Ha nincs megadva, a `HEAD` változat klónozták. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Kötet csatlakoztatási gitRepo: Azure parancssori felület

GitRepo kötet csatlakoztatása, tároló példányok központi telepítésekor a [Azure CLI](/cli/azure), szállítási a `--gitrepo-url` és `--gitrepo-mount-path` paramétereit, és a [az tároló létrehozása] [ az-container-create] parancsot. Opcionálisan megadhat a kötetek klónozása a könyvtár (`--gitrepo-dir`) és a-verziójának klónozható véglegesítési kivonatát (`--gitrepo-revision`).

A példában a parancs klónokat a [aci-helloworld] [ aci-helloworld] a mintaalkalmazás `/mnt/aci-helloworld` tároló-példány:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image microsoft/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

Annak ellenőrzéséhez, hogy a gitRepo kötet csatlakoztatása, indítsa el a rendszerhéj-tárolóban levő [az tároló exec] [ az-container-exec] és a directory listája:

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Kötet csatlakoztatási gitRepo: erőforrás-kezelő

GitRepo kötet csatlakoztatása, tároló példányok telepítésekor egy [Azure Resource Manager sablon](/azure/templates/microsoft.containerinstance/containergroups), először feltöltése a `volumes` a tároló csoport tömb `properties` a sablon szakasza. Ezt követően a, amelyben szeretné csatlakoztatni a tárolócsoport minden egyes tároló a *gitRepo* kötet, feltölti a `volumeMounts` tömb a a `properties` szakasz tároló-definícióban.

Például a következő Resource Manager-sablon csoportot hoz létre tárolót álló egyetlen tárolót. A tároló által megadott két GitHub-adattárak klónokat a *gitRepo* kötet blokkokat. A második kötet tartalmaz további megadásával való klónozásához egy könyvtárat, és egy adott változat klónozását véglegesítési kivonatát.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Az eredményül kapott könyvtárstruktúrát a két klónozott repók az előző sablonban meghatározott van:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Az Azure Resource Manager sablonnal tároló példány szemléltet, olvassa el [központi telepítése az Azure-tároló esetekben több tárolócsoportok](container-instances-multi-container-group.md).

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan csatlakoztatása egyéb Azure tároló példányok kötet típusa:

* [Azure-tároló példányát az Azure fájlmegosztások csatlakoztatása](container-instances-volume-azure-files.md)
* [Az Azure-tároló példányát emptyDir kötet csatlakoztatása](container-instances-volume-emptydir.md)
* [Azure-tároló példányát titkos kötet csatlakoztatása](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec