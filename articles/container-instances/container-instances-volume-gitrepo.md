---
title: Azure Container Instances gitRepo kötet csatlakoztatása
description: Ismerje meg egy Git-tárház klónozásához a container Instances szolgáltatásban való gitRepo kötet csatlakoztatása
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/15/2018
ms.author: marsma
ms.openlocfilehash: 34036c5ec9ccd8c502104ce862e4749c59be62b9
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43112972"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Az Azure Container Instances szolgáltatásban gitRepo kötet csatlakoztatása

Ismerje meg, hogyan csatlakoztathat egy *gitRepo* kötet egy Git-tárház klónozásához a tárolópéldányok be.

> [!NOTE]
> Csatlakoztatási egy *gitRepo* kötet Linux-tárolók jelenleg korlátozva. Arra törekszünk, hogy idővel az összes funkció elérhető legyen a Windows-tárolókon is. Az egyes platformok közötti aktuális eltérésekről a [Azure Container Instances-kvóták és -régiók rendelkezésre állása](container-instances-quotas.md) részben tájékozódhat.

## <a name="gitrepo-volume"></a>a gitRepo kötet

A *gitRepo* kötetet csatlakoztatja egy könyvtárat, és klónozza a megadott Git-tárház bele a tároló indításakor. Használatával egy *gitRepo* a tárolópéldányok kötethez, elkerülése érdekében adja hozzá a kódot végrehajtására az alkalmazásokban.

Ha csatlakoztatja egy *gitRepo* kötet, megadhatja a kötet konfigurálása három tulajdonságait:

| Tulajdonság | Szükséges | Leírás |
| -------- | -------- | ----------- |
| `repository` | Igen | A teljes URL-címet, beleértve a `http://` vagy `https://`, a Git-tárház klónozásához.|
| `directory` | Nem | A könyvtárat, amelybe a tárház klónozása. Az elérési utat kell tartalmaznia, vagy nem kezdődhet "`..`".  Ha megad "`.`", a tárház klónozták a kötet könyvtárba. Ellenkező esetben a Git-tárház be a megadott névvel, a kötet könyvtárban lévő alkönyvtára klónozták. |
| `revision` | Nem | A változat klónozásának véglegesítési kivonatát. Ha nincs megadva, a `HEAD` változat klónozták. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Kötet csatlakoztatási gitRepo: az Azure CLI

A gitRepo kötet csatlakoztatása, container Instances szolgáltatásban való központi telepítésekor a [Azure CLI-vel](/cli/azure), szállítási a `--gitrepo-url` és `--gitrepo-mount-path` paramétereket a [az tároló létrehozása] [ az-container-create] parancsot. Megadhatja a klónozza a köteten lévő könyvtár (`--gitrepo-dir`) és a változat klónozásának véglegesítési kivonatát (`--gitrepo-revision`).

A példában a parancs klónozza a [aci-helloworld] [ aci-helloworld] mintaalkalmazás be `/mnt/aci-helloworld` a tároló-példányban:

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

Annak ellenőrzéséhez, hogy a gitRepo kötet csatlakoztatása, indítsa el a tárolóban található rendszerhéj [az tároló exec] [ az-container-exec] és a könyvtár listázása:

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Kötet csatlakoztatási gitRepo: Resource Manager

A gitRepo kötet csatlakoztatása, a tároló-példányok üzembe helyezésekor egy [Azure Resource Manager-sablon](/azure/templates/microsoft.containerinstance/containergroups), először töltse fel a `volumes` tömb tárolócsoportban `properties` szakasz a sablon. Ezt követően, amelyben szeretné csatlakoztatni a tárolócsoport minden tároló esetén a *gitRepo* kötet, töltse fel a `volumeMounts` Pole a `properties` a tároló-definíció szakasza.

Például a következő Resource Manager-sablont hoz létre egy tárolócsoportot, amely egyetlen tároló. A tároló klónozza a GitHub-adattárak két által meghatározott a *gitRepo* kötet blokkokat. A második kötet klónozása a könyvtárat adja meg a további tulajdonságok és a egy adott változat klónozásához véglegesítési kivonatát tartalmaz.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json --> [!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Az eredményül kapott könyvtárstruktúrája a két klónozott adattárakkal, a fenti sablonban definiálva van:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Tárolópéldány üzembe helyezésének Azure Resource Manager-sablonnal egy példát, olvassa el [többtárolós csoportok az Azure Container Instancesben üzembe helyezése](container-instances-multi-container-group.md).

## <a name="private-git-repo-authentication"></a>Privát Git-tárház hitelesítés

Privát Git-tárházhoz gitRepo kötet csatlakoztatása, adja meg hitelesítő adatait az adattár URL-címe. Általában hitelesítő adatokat az űrlap egy felhasználónév és egy személyes hozzáférési jogkivonat (PAT), amely engedélyezi a hozzáférést a tárház hatókörrel rendelkező.

Ha például az Azure CLI `--gitrepo-url` paramétere egy privát GitHub-tárházat jelent (ahol a "gituser" a GitHub-felhasználó nevét, a "abcdef1234fdsa4321abcdef" pedig a felhasználó személyes hozzáférési jogkivonat) a következőhöz hasonló:

```azurecli
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

VSTS Git-tárházhoz adja meg egy érvényes PAT együtt bármely felhasználó neve (használhatja "vstsuser" a következő példához hasonlóan):

```azurecli
--gitrepo-url https://vstsuser:abcdef1234fdsa4321abcdef@vstsaccountname.visualstudio.com/_git/some-private-repository
```

GitHub-és VSTS személyes hozzáférési jogkivonatok kapcsolatos további információkért tekintse meg a következőket:

GitHub: [a parancssor a személyes hozzáférési jogkivonat létrehozása][pat-github]

VSTS: [személyes hozzáférési jogkivonat hitelesíti a hozzáférést készítése][pat-vsts]

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan csatlakoztathat egyéb kötettípusok az Azure Container Instances szolgáltatásban:

* [Azure-tároló példányát az Azure fájlmegosztások csatlakoztatása](container-instances-volume-azure-files.md)
* [Az Azure-tároló példányát emptyDir kötet csatlakoztatása](container-instances-volume-emptydir.md)
* [Az Azure Container Instances szolgáltatásban titkos kötet csatlakoztatása](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-vsts]: https://docs.microsoft.com/vsts/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
