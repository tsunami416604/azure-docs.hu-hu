---
title: SSH-hozzáférés Linux-tárolókhoz
description: SSH-munkamenetet nyithat meg egy Linux-tárolóhoz az Azure App Service-ben. Az egyéni Linux-tárolók at az egyéni lemezkép néhány módosításával támogatják.
keywords: Azure app szolgáltatás, web app, linux, oss
author: msangapu-msft
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.topic: article
ms.date: 02/25/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: dab13f222b441c7415a8d09d0d91ab3af5aaf836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280182"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>SSH-támogatás a Linuxon futó Azure App Service számára

[A Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) általában a parancssori terminálról távolról történő felügyeleti parancsok végrehajtására szolgál. A Linuxon app service SSH-támogatást nyújt az alkalmazástárolóhoz. 

![Linux app szolgáltatás SSH](./media/app-service-linux-ssh-support/app-service-linux-ssh.png)

A tárolóhoz közvetlenül a helyi fejlesztői gépről is csatlakozhat SSH és SFTP használatával.

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása a böngészőben

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>SSH-támogatás használata egyéni Docker-lemezképekkel

Lásd: [SSH konfigurálása egyéni tárolóban](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>SSH-munkamenet megnyitása távoli rendszerhéjból

> [!NOTE]
> Ez a funkció jelenleg előzetes verzióban érhető el.
>

A TCP-bújtatás használatával hálózati kapcsolatot hozhat létre a fejlesztői gép és a webalkalmazás tárolókhoz egy hitelesített WebSocket-kapcsolaton keresztül. Lehetővé teszi, hogy megnyisson egy SSH-munkamenetet az App Service-ben futó tárolóval az Ön által választott ügyféltől.

A kezdéshez telepítenie kell az [Azure CLI-t.](/cli/azure/install-azure-cli?view=azure-cli-latest) Ha meg szeretné tekinteni, hogyan működik az Azure CLI telepítése nélkül, nyissa meg az [Azure Cloud Shell](../../cloud-shell/overview.md)t. 

Nyisson meg egy távoli kapcsolatot az alkalmazással az [az webapp távoli kapcsolat létrehozása](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) paranccsal. Adja \_ \<meg _ \<az előfizetés-azonosítót>, _ _ \<a csoportnév->_ és az alkalmazásnév->_ az alkalmazáshoz.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&`végén a parancs csak a kényelem, ha a Cloud Shell. A folyamatot a háttérben futtatja, így a következő parancsot ugyanabban a rendszerhéjban futtathatja.

A parancs kimenete megadja az SSH munkamenet megnyitásához szükséges információkat.

```output
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Nyisson meg egy SSH-munkamenetet a tárolóval az Ön által választott ügyféllel a helyi port használatával. A következő példa az alapértelmezett [ssh](https://ss64.com/bash/ssh.html) parancsot használja:

```bash
ssh root@127.0.0.1 -p <port>
```

Amikor a rendszer `yes` kéri, írja be a csatlakozás folytatását. Ezután a rendszer kéri a jelszó megparancsát. Használja `Docker!`a , amely korábban megvolt jelenítve.

```output
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

A hitelesítés után látnia kell a munkamenet üdvözlőképernyőjét.

```output
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  >
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
```

Most csatlakozik az összekötőhöz.  

Próbálja meg futtatni a [felső](https://ss64.com/bash/top.html) parancsot. Az alkalmazás folyamatát látnia kell a folyamatlistában. Az alábbi kimeneti példában ez `PID 263`az, a .

```output
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

Kérdéseket és aggályokat tehet közzé az [Azure-fórumon.](https://docs.microsoft.com/answers/topics/azure-webapps.html)

A Web App for Containers alkalmazásról a következő témakörben talál további információt:

* [A Node.js alkalmazások távoli hibakeresésének bemutatása az Azure App Service szolgáltatásban a VS Code szolgáltatásból](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Egyéni Docker-rendszerkép használata a Web App for Containers szolgáltatásban](quickstart-docker-go.md)
* [A .NET Core használata a Linuxon futó Azure App Service-ben](quickstart-dotnetcore.md)
* [A Ruby használata a Linuxon futó Azure App Service-ben](quickstart-ruby.md)
* [Azure App Service Web App for Containers – gyakori kérdések](app-service-linux-faq.md)
