---
title: SSH-támogatás az App Service Linux rendszeren – Azure | Microsoft Docs
description: Ismerkedjen meg az SSH és a Linux Azure App Service használatával.
keywords: Azure app Service, Web App, Linux, OSS
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: fef8a17de4539a1427c269cdc512063d07df195c
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066870"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>SSH-támogatás a Azure App Service Linux rendszeren

A [Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) általában a felügyeleti parancsok parancssori terminálról történő távoli végrehajtásához használatos. A Linux rendszeren futó App Service SSH-támogatást biztosít az alkalmazás-tárolóba az új webalkalmazások futásidejű készletéhez használt beépített Docker-rendszerképekkel. 

![Futásidejű stackek](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Egyéni Docker-lemezképek esetén az SSH-kiszolgáló konfigurálásával az egyéni rendszerképben.

A tárolóhoz közvetlenül a helyi fejlesztési gépről is csatlakozhat SSH és SFTP használatával.

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása böngészőben

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>SSH-támogatás használata egyéni Docker-rendszerképekkel

Lásd: [az SSH konfigurálása egyéni tárolóban](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>SSH-munkamenet megnyitása a távoli rendszerhéjból

> [!NOTE]
> Ez a funkció jelenleg előzetes verzióban érhető el.
>

A TCP-bújtatás használatával hálózati kapcsolatot hozhat létre a fejlesztői gép és Web App for Containers között egy hitelesített WebSocket-kapcsolaton keresztül. Lehetővé teszi, hogy egy SSH-munkamenetet az Ön által választott ügyféltől App Service futtatott tárolóval nyisson meg.

Első lépésként telepítenie kell az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)-t. Ha szeretné megtekinteni, hogyan működik az Azure CLI telepítése nélkül, nyissa meg [Azure Cloud Shell](../../cloud-shell/overview.md). 

Nyisson meg egy távoli kapcsolódást az alkalmazáshoz az az [WebApp Remote-Network-kapcsolatkezelő Create](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) paranccsal. \_Adja meg \<  _\<_  _\<az alkalmazáshoz az előfizetés-azonosító >_ , a csoport neve > és az alkalmazás neve > _.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&`a parancs végén a Cloud Shell használata esetén csak kényelmi célokat szolgál. A háttérben futtatja a folyamatot, így a következő parancsot ugyanazon a rendszerhéjon futtathatja.

A parancs kimenete megadja az SSH-munkamenet megnyitásához szükséges információkat.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Nyisson meg egy SSH-munkamenetet az Ön által választott ügyféllel a helyi port használatával. Az alábbi példa az alapértelmezett [SSH](https://ss64.com/bash/ssh.html) -parancsot használja:

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

Ha a rendszer kéri, `yes` írja be a következőt: a csatlakozás folytatásához. Ezt követően a rendszer kéri a jelszót. Használja `Docker!`, amely korábban volt látható.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

A hitelesítés után látnia kell a munkamenet-üdvözlőképernyő képernyőt.

```
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  >
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
```

Ezzel csatlakozott az összekötőhöz.  

Próbálja meg futtatni a [felső](https://ss64.com/bash/top.html) parancsot. Az alkalmazás folyamatát meg kell tudnia tekinteni a folyamat listában. Az alábbi példában szereplő kimenetben ez a `PID 263`következő:.

```
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
```

## <a name="next-steps"></a>További lépések

Kérdéseket és problémákat tehet közzé az [Azure fórumán](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

További információ a Web App for Containersről:

* [A Node. js-alkalmazások távoli hibakeresésének bemutatása Azure App Service a VS Code-ból](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Egyéni Docker-rendszerkép használata a Web App for Containers szolgáltatásban](quickstart-docker-go.md)
* [A .NET Core használata a Linuxon futó Azure App Service-ben](quickstart-dotnetcore.md)
* [A Ruby használata a Linuxon futó Azure App Service-ben](quickstart-ruby.md)
* [Azure App Service Web App for Containers – gyakori kérdések](app-service-linux-faq.md)
