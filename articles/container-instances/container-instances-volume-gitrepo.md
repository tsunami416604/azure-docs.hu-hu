---
title: GitRepo-kötet csatlakoztatása tárolócsoportra
description: Ismerje meg, hogyan csatlakoztathat gitRepo-kötetet egy Git-tárház tárolópéldányokba való klónozásához
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 405cacd7a1649f95640a8dabf476729e101d03f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252090"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>GitRepo-kötet csatlakoztatása az Azure Container-példányokban

Ismerje meg, hogyan csatlakoztathat egy *gitRepo-kötetet* egy Git-tárház tárolópéldányokklónozásához.

> [!NOTE]
> A *gitRepo-kötet* csatlakoztatása jelenleg Linux-tárolókra korlátozódik. Miközben azon dolgozunk, hogy az összes funkciót a Windows tárolók, megtalálja a jelenlegi platform különbségek az [áttekintést](container-instances-overview.md#linux-and-windows-containers).

## <a name="gitrepo-volume"></a>gitRepo kötet

A *gitRepo* kötet csatlakoztat egy könyvtárat, és klónozza a megadott Git-tárházat a tároló indításakor. A *gitRepo-kötet* használatával a tároló példányai, elkerülheti a kód hozzáadása az alkalmazásokban.

*GitRepo* kötet csatlakoztatásakor három tulajdonságot állíthat be a kötet konfigurálásához:

| Tulajdonság | Kötelező | Leírás |
| -------- | -------- | ----------- |
| `repository` | Igen | A klónozandó `http://` `https://`Git-tárház teljes URL-címe, beleértve vagy , .|
| `directory` | Nem | Az a könyvtár, amelybe a tárolót klónozni kell. Az elérési út nem tartalmazhat"`..`" "-ot.  Ha a`.`" " értéket adja meg, a tárház a kötet könyvtárába klónozva lesz. Ellenkező esetben a Git-tárház a kötetkönyvtárban lévő megadott név alkönyvtárába klónozva van. |
| `revision` | Nem | A klónozandó módosítás véglegesítési kivonata. Ha nincs megadva, a `HEAD` verzió klónozásra kerül. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Mount gitRepo kötet: Azure CLI

GitRepo-kötet csatlakoztatása az [Azure CLI-vel](/cli/azure)tárolópéldányok üzembe helyezésekor, adja meg az `--gitrepo-url` az container create parancs és `--gitrepo-mount-path` az [az-tároló létrehozása][az-container-create] parancs paramétereit. Megadhatja a klónozandó változat ( )`--gitrepo-dir`véglegesítési kivonatát a köteten belül`--gitrepo-revision`a klónozni kívánt köteten belül.

Ez a példa parancs klónozza a Microsoft `/mnt/aci-helloworld` [aci-helloworld][aci-helloworld] mintaalkalmazást a tárolópéldányban:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

A gitRepo kötet csatlakoztatásának ellenőrzéséhez indítson el egy héjat az [az container exec][az-container-exec] tárolóval, és sorolja fel a könyvtárat:

```azurecli
az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
```

```output
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Mount gitRepo kötet: Erőforrás-kezelő

GitRepo-kötet csatlakoztatása, amikor egy [Azure Resource Manager-sablonnal](/azure/templates/microsoft.containerinstance/containergroups)tárolópéldányokat telepít, először feltöltse a `volumes` tömböt a sablon tárolócsoport-szakaszában. `properties` Ezután a tárolócsoport minden olyan tárolójára, amelyben a *gitRepo* kötetet `volumeMounts` csatlakoztatni szeretné, a tárolódefiníció `properties` szakaszában feltölti a tömböt.

A következő Erőforrás-kezelő sablon például létrehoz egy tárolócsoportot, amely egyetlen tárolóból áll. A tároló klónoz két GitHub-tárházat, amelyeket a *gitRepo* kötetblokkok határoznak meg. A második kötet további tulajdonságokat tartalmaz, amelyek egy könyvtárat adnak meg, és egy adott verzió véglegesítési kivonatát klónozni.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Az előző sablonban definiált két klónozott tártár gyűjteményszerkezete a következő:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Ha példát szeretne látni egy Azure Resource Manager-sablonnal való tárolópéldány-üzembe helyezésre, [olvassa el a Többtárolós csoportok üzembe helyezése az Azure Container Instances szolgáltatásban című témakört.](container-instances-multi-container-group.md)

## <a name="private-git-repo-authentication"></a>Privát Git-tárterület-hitelesítés

GitRepo-kötet csatlakoztatása egy privát Git-tárházhoz, adja meg a hitelesítő adatokat a tárház URL-címében. A hitelesítő adatok általában egy felhasználónév és egy személyes hozzáférési jogkivonat (PAT) formájában jelennek meg, amely hatókörrel rendelkező hozzáférést biztosít a tárházhoz.

Például egy privát `--gitrepo-url` GitHub-tárház Azure CLI-paramétere a következőhöz hasonlónak tűnik (ahol a "gituser" a GitHub felhasználói neve, és az "abcdef1234fdsa4321abcdef" a felhasználó személyes hozzáférési jogkivonata):

```console
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Egy Azure Repos Git-tárház esetén adjon meg bármilyen felhasználónevet (használhatja az "azurereposuser" mint a következő példában) egy érvényes PAT-tal együtt:

```console
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

A GitHub és az Azure Repos személyes hozzáférési jogkivonatairól az alábbi témakörökben talál további információt:

GitHub: [Személyes hozzáférési jogkivonat létrehozása a parancssorhoz][pat-github]

Azure-adatpos: [Személyes hozzáférési jogkivonatok létrehozása a hozzáférés hitelesítéséhez][pat-repos]

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan csatlakoztathat más kötettípusokat az Azure Container-példányokban:

* [Azure-fájlmegosztás csatlakoztatása az Azure Container Instancesben](container-instances-volume-azure-files.md)
* [EmptyDir kötet csatlakoztatása az Azure Container-példányokban](container-instances-volume-emptydir.md)
* [Titkos kötet csatlakoztatása az Azure Container-példányokban](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
