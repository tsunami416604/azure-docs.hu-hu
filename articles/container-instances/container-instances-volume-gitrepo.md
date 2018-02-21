---
title: "Azure-tároló példányok gitRepo kötet csatlakoztatása"
description: "Útmutató: a Git-tárház klónozása be a tároló példányok gitRepo kötet csatlakoztatása"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 9acde9259fcb392458e7b2fa7d3369776978285e
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2018
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

## <a name="mount-a-gitrepo-volume"></a>GitRepo kötet csatlakoztatása

Csatlakoztassa a *gitRepo* kötet tároló példányt, ha már telepítette használatával egy [Azure Resource Manager sablon](/azure/templates/microsoft.containerinstance/containergroups).

Először feltöltése a `volumes` a tároló csoport tömb `properties` a sablon szakasza. Minden egyes tároló, amelyben szeretné csatlakoztatni a tárolócsoport a következő a *gitRepo* kötet, feltölti a `volumeMounts` tömb a a `properties` szakasz tároló-definícióban.

Például a következő Resource Manager-sablon csoportot hoz létre tárolót álló egyetlen tárolót. A tároló által megadott két GitHub-adattárak klónokat a *gitRepo* kötet blokkokat. A második kötet tartalmaz további megadásával való klónozásához egy könyvtárat, és egy adott változat klónozását véglegesítési kivonatát.

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
