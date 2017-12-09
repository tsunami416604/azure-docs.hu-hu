---
title: "Telepítse az Azure parancssori felület 1.0 |} Microsoft Docs"
description: "Telepítse az Azure CLI 1.0 Mac, Linux és a Windows Azure-szolgáltatások használatának megkezdéséhez"
editor: 
manager: timlt
documentationcenter: 
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management
ms.assetid: bdb776c8-7a76-4f3a-887c-236b4fffee10
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: command-line-interface
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: rasquill
ms.openlocfilehash: 67aa5bb122b277e998119506bb2a574d6b4fde5e
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2017
---
# <a name="install-the-azure-cli-10"></a>Az Azure parancssori felület 1.0-s telepítése
> [!div class="op_single_selector"]
> * [PowerShell](/powershell/azure/overview)
> * [Azure CLI 1.0](cli-install-nodejs.md)
> * [Azure CLI 2.0](/cli/azure/install-azure-cli)

> [!IMPORTANT]
> Ez a témakör ismerteti, hogyan telepítheti az Azure CLI 1.0. A parancssori felület elavult, és csak a "klasszikus" erőforrások Azure Service Management (ASM) modell által támogatott használható.
> Az Azure Resource Managerrel (ARM) történő telepítések esetén használjon [Azure CLI 2.0](/cli/azure/overview).

Gyorsan telepíti az Azure parancssori felület (Azure CLI 1.0-s)-alapú parancsokat nyílt forráskódú használandó létrehozása és kezelése a Microsoft Azure erőforrásait. Az platformfüggetlen-eszközök telepítése a számítógépre több lehetőség közül választhat:

* **npm csomag** -npm (a Csomagkezelőt a JavaScript) futtassa a Linux-disztribúció vagy az operációs rendszer a legújabb Azure CLI 1.0 csomag telepítéséhez. Szükséges, node.js és npm a számítógépen.
* **Telepítő** -könnyen Mac vagy Windows-telepítést telepítőjének letöltése.
* **Docker-tároló** - Start készen áll a futásra Docker-tároló a legújabb parancssori felület használatával. A számítógépen a Docker gazdagépet igényel.

A további beállítások és a háttérben, tekintse meg a projekt tárház [GitHub](https://github.com/azure/azure-xplat-cli).

Az Azure CLI 1.0-s telepítése után [csatlakoztassa az Azure előfizetéssel rendelkező](/cli/azure/authenticate-azure-cli) , és futtassa a **azure** az Azure-erőforrások kezelésére használható a parancssori felületről (Bash, Terminálszolgáltatások, parancssort, és így tovább) parancsokat.

## <a name="option-1-install-an-npm-package"></a>1. lehetőség: Egy npm csomag telepítése
A parancssori felület telepítése npm-csomagból, ellenőrizze, hogy már letöltötte és telepítette a [legújabb Node.js és npm](https://nodejs.org/en/download/package-manager/). Ezután futtassa **npm telepítése** az azure-cli csomag telepítése:

```bash
npm install -g azure-cli
```

A Linux terjesztéseket, előfordulhat, hogy szüksége **sudo** sikeresen futtatni a **npm** parancs, az alábbiak szerint:

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Ha telepíteni vagy frissíteni a Node.js és a Linux-disztribúció vagy az operációs rendszer npm van szüksége, azt javasoljuk, hogy telepítse a Node.js LTS legfrissebb (4.x). Ha egy régebbi verzióját használja, előfordulhat, hogy hibaüzenet telepítését.

Ha szeretné, töltse le a legfrissebb Linux [bont fájl] [ linux-installer] helyileg az npm-csomag. Majd telepítse az alábbiak szerint a letöltött npm csomagot (a Linux terjesztéseket, előfordulhat, hogy szeretné használni, **sudo**):

```bash
npm install -g <path to downloaded tar file>
```

## <a name="option-2-use-an-installer"></a>2. lehetőség: Egy telepítővel
Ha egy Mac vagy Windows-számítógépet használ, a következő parancssori Felületet telepítőcsomagokat letölthetők:

* [Mac OS X telepítőjét][mac-installer]
* [Windows MSI][windows-installer]

> [!TIP]
> A Windows rendszeren is letöltheti a [Webplatform-telepítő](https://go.microsoft.com/?linkid=9828653) a parancssori felület telepítése. A telepítő lehetővé teszi a parancssori felület telepítése után további Azure SDK-t és parancssori eszközök telepítése lehetőséget.

## <a name="option-3-use-a-docker-container"></a>3. lehetőség: Egy Docker-tároló használata
Ha úgy állította be a számítógépre, a [Docker](https://docs.docker.com/engine/understanding-docker/) állomás, futtathatja az Azure CLI legújabb 1.0 egy Docker-tároló. A következő parancsot (a Linux terjesztéseket, előfordulhat, hogy szeretné használni, **sudo**):

```bash
docker run -it microsoft/azure-cli
```

## <a name="run-azure-cli-10-commands"></a>Azure CLI 1.0-parancsok futtatása
Az Azure CLI 1.0-s telepítése után futtassa a **azure** parancsot a parancssori felületén (Bash, Terminálszolgáltatások, parancssort, és így tovább). A Súgó parancs futtatásához írja be például a következőket:

```azurecli
azure help
```

> [!NOTE]
> Egyes Linux terjesztésekről hasonló hiba jelenhet `/usr/bin/env: ‘node’: No such file or directory`. Ez a hiba a legújabb telepítendő /usr/bin/nodejs, Node.js-telepítések származik. Megjavítani, ez a parancs futtatásával hozzon létre /usr/bin/node a szimbolikus hivatkozást:

```bash
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Az Azure CLI 1.0 telepített verziójának megtekintéséhez írja be a következőt:

```azurecli
azure --version
```

Most már készen áll! A saját erőforrásokat parancssori felület parancsai eléréséhez [csatlakozzon az Azure-előfizetéshez az Azure parancssori felületen](/cli/azure/authenticate-azure-cli).

> [!NOTE]
> Ha először használja az Azure parancssori felület, megjelenik egy üzenet rákérdez, hogy a Microsoft használati adatokat gyűjthet a. Részvétel önkéntes történik. Ha a részvétel mellett dönt, le is bármikor futtatásával `azure telemetry --disable`. Részvétel bármikor engedélyezéséhez futtassa `azure telemetry --enable`.

## <a name="update-the-cli"></a>A parancssori felület frissítése
A Microsoft gyakran ad az Azure parancssori felület frissített verzióit. Telepítse újra a telepítő használatával az operációs rendszer CLI, vagy futtassa a legújabb Docker-tároló. Vagy, ha a legfrissebb Node.js és npm telepítve van, frissítse a következő (a Linux terjesztéseket, előfordulhat, hogy szeretné használni, **sudo**).

```bash
npm update -g azure-cli
```

## <a name="enable-tab-completion"></a>Kiegészítés engedélyezése
A parancssori felület parancsait kiegészítést Mac és Linux esetén támogatott.

Engedélyezze a zsh, futtassa:

```bash
echo '. <(azure --completion)' >> .zshrc
```

Engedélyezze a bash, futtassa:

```bash
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## <a name="next-steps"></a>Következő lépések
* [Csatlakoztassa a CLI Azure-előfizetése](/cli/azure/authenticate-azure-cli) létrehozásához és kezeléséhez az Azure-erőforrások.
* További tudnivalók az Azure parancssori felület, letöltési forráskódját, kapcsolatos problémákat, vagy a projekt hozzájárul, látogasson el a [az Azure parancssori felület GitHub-tárházban](https://github.com/azure/azure-xplat-cli).
* Ha az Azure parancssori felület, vagy az Azure használatával kapcsolatos kérdésekre, keresse fel a [Azure fórumok](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).


[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: /cli/azure/get-started-with-az-cli2
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md
