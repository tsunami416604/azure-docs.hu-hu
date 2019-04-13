---
title: SSH-támogatás az App Service linuxon – Azure |} A Microsoft Docs
description: Ismerje meg az SSH használata Linuxon futó Azure App Service.
keywords: az Azure app service, webalkalmazás, linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 2d84a4dd0b69ce9ca7fc594dffce3238c620c426
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543973"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>SSH-támogatás a Linuxon futó Azure App Service

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) gyakran használatos parancssori terminálból távolról hajtsa végre a felügyeleti parancsokat. A linuxon futó App Service-ben az egyes használt a futtatókörnyezeti verem új web Apps beépített Docker-rendszerképek a SSH-támogatás az alkalmazás tárolóba biztosít. 

![Futtatókörnyezeti vermek](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Az egyéni Docker-rendszerképek, SSH-kiszolgáló konfigurálása az egyéni rendszerkép.

Is csatlakozhat a tároló közvetlenül a helyi fejlesztői gépen SSH és az SFTP használatával.

## <a name="open-ssh-session-in-browser"></a>Nyissa meg böngészőben SSH-munkamenet

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>SSH-támogatás használata egyéni Docker-rendszerképek

Lásd: [konfigurálása SSH egyéni tárolóban](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>Nyissa meg az SSH-munkamenetet a távoli rendszerhéj

> [!NOTE]
> Ez a funkció jelenleg előzetes verzióban érhető el.
>

Bújtatás, a TCP használatával hozhat létre egy hitelesített WebSocket kapcsolaton keresztül a fejlesztői gépen, és a Web App for Containers közötti hálózati kapcsolat. Lehetővé teszi, hogy nyisson meg egy SSH-munkamenet az App Service-ben az ügyfél a választott futtató tárolót.

Első lépésként telepítenie kell [Azure CLI-vel](/cli/azure/install-azure-cli?view=azure-cli-latest). Hogyan működik az Azure parancssori felület telepítése nélkül megtekintéséhez nyissa meg a [Azure Cloud Shell](../../cloud-shell/overview.md). 

Távoli kapcsolatot létesíteni az alkalmazás használatával az [az webapp távoli-kapcsolat létrehozása](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) parancsot. Adja meg  _\<előfizetés-azonosítója >_,  _\<csoport-neve >_ és \_< alkalmazás neve > _ az alkalmazáshoz.

```azurecli-interactive
az webapp remote-connection create --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&` a parancs végén van csak a felhasználók kényelme érdekében a Cloud Shell használata. Ez a folyamat a háttérben fut, hogy az azonos rendszerhéjban a következő parancsot futtathatja.

A parancs kimenete egy SSH-munkamenet megnyitásához szükséges információkat biztosít.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Nyisson meg egy SSH-munkamenet az ügyfél a választott, a helyi portot használ a tárolóhoz. Az alábbi példa az alapértelmezett [ssh](https://ss64.com/bash/ssh.html) parancsot:

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

Amikor a rendszer kéri, írja be `yes` csatlakozás folytatásához. Ezután jelszót kér. Használat `Docker!`, amely volt, a korábban bemutatott.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

Hogy a hitelesítést követően megtekintheti a munkamenet üdvözlő képernyő.

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

Próbálja meg futtatni a [felső](https://ss64.com/bash/top.html) parancsot. Az alkalmazás folyamata a folyamatok listájában látni kell lennie. Az alábbi példa kimenetében a `PID 263`.

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

Ön felteheti kérdéseit és észrevételeit az a [Azure fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

További információ a Web App for containers szolgáltatásban lásd:

* [Távoli hibakeresés a Node.js-alkalmazásokat az Azure App Service-ben a VS Code-ból bemutatása](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Egyéni Docker-rendszerkép használata a Web App for Containers szolgáltatásban](quickstart-docker-go.md)
* [A .NET Core használata a Linuxon futó Azure App Service-ben](quickstart-dotnetcore.md)
* [A Ruby használata a Linuxon futó Azure App Service-ben](quickstart-ruby.md)
* [Azure App Service Web App for Containers – gyakori kérdések](app-service-linux-faq.md)
