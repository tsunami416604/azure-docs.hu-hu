---
title: SSH-támogatás a Linuxon futó Azure App Service |} A Microsoft Docs
description: Ismerje meg az SSH használata Linuxon futó Azure App Service.
keywords: az Azure app service, webalkalmazás, linux, oss
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.openlocfilehash: 049ae78efee0b5ebdf6bfbe207f8c85d94e9238f
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44050068"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>SSH-támogatás a Linuxon futó Azure App Service

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) gyakran használatos parancssori terminálból távolról hajtsa végre a felügyeleti parancsokat. A linuxon futó App Service-ben az egyes használt a futtatókörnyezeti verem új web Apps beépített Docker-rendszerképek a SSH-támogatás az alkalmazás tárolóba biztosít. 

![Futtatókörnyezeti vermek](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Az egyéni Docker-rendszerképek, SSH-kiszolgáló konfigurálása az egyéni rendszerkép.

Is csatlakozhat a tároló közvetlenül a helyi fejlesztői gépen SSH és az SFTP használatával.

## <a name="open-ssh-session-in-browser"></a>Nyissa meg böngészőben SSH-munkamenet

Ahhoz, hogy a tároló SSH-ügyfél kapcsolatot, az alkalmazás kell futtatnia.

Illessze be a következő URL-címet a böngészőben, és cserélje le \<app_name > az alkalmazás nevére:

```
https://<app_name>.scm.azurewebsites.net/webssh/host
```

Ha Ön nem már hitelesített, hitelesítéséhez az Azure-előfizetésében való csatlakozáshoz szükségesek. A hitelesítést követően látni egy böngészőben rendszerhéj, ahol futtathat parancsokat a tárolóban.

![SSH-kapcsolatot](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="use-ssh-support-with-custom-docker-images"></a>SSH-támogatás használata egyéni Docker-rendszerképek

Ahhoz, hogy egy egyéni Docker-rendszerkép SSH-kommunikációt a tároló és az ügyfél közötti támogatása az Azure Portalon hajtsa végre az alábbi lépéseket a Docker-rendszerképet a.

Ezeket a lépéseket jelennek meg az Azure App Service-tárházban, [például](https://github.com/Azure-App-Service/node/blob/master/6.9.3/).

1. Tartalmazza a `openssh-server` telepítés [ `RUN` utasítás](https://docs.docker.com/engine/reference/builder/#run) a docker-fájlban, a lemezkép és a fiók jelszavát a legfelső szintű set `"Docker!"`.

    > [!NOTE]
    > Ez a konfiguráció nem tesz elérhetővé külső kapcsolatokat a tárolóhoz. Az SSH csak a Kudu-n keresztül érhetők el / SCM webhely, amely a közzétételi hitelesítő adatokkal van hitelesítve.

    ```docker
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

1. Adjon hozzá egy [ `COPY` utasítás](https://docs.docker.com/engine/reference/builder/#copy) , a docker-fájl másolása egy [sshd_config](http://man.openbsd.org/sshd_config) fájlt a */etc/ssh/* könyvtár. A konfigurációs fájlban kell alapozni az sshd_config fájlban, az Azure-App-Service GitHub-adattárában [Itt](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config).

    > [!NOTE]
    > A *sshd_config* fájlnak tartalmaznia kell a következő vagy a kapcsolat hibája esetén: 
    > * `Ciphers` tartalmaznia kell legalább a következők egyikét: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` tartalmaznia kell legalább a következők egyikét: `hmac-sha1,hmac-sha1-96`.

    ```docker
    COPY sshd_config /etc/ssh/
    ```

1. Tartalmazza a 2222-es port a [ `EXPOSE` utasítás](https://docs.docker.com/engine/reference/builder/#expose) a docker-fájl számára. Bár a rendszergazdai szintű jelszó ismert, a 2222-es port nem érhető el az internet irányából. Egy belső egyetlen port elérhető csak egy privát virtuális hálózat hálózati hidas kapcsolatán belüli tárolók által.

    ```docker
    EXPOSE 2222 80
    ```

1. Ügyeljen arra, hogy indítsa el a héjparancsfájl használatával SSH szolgáltatást (Példa: [init_container.sh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)).

    ```bash
    #!/bin/bash
    service ssh start
    ```

A docker-fájlt használ a [ `ENTRYPOINT` utasítás](https://docs.docker.com/engine/reference/builder/#entrypoint) a parancsfájl futtatásához.

    ```docker
    COPY init_container.sh /opt/startup
    ...
    RUN chmod 755 /opt/startup/init_container.sh
    ...
    ENTRYPOINT ["/opt/startup/init_container.sh"]
    ```

## <a name="open-ssh-session-from-remote-shell"></a>Nyissa meg az SSH-munkamenetet a távoli rendszerhéj

> [!NOTE]
> Ez a funkció jelenleg előzetes verzióban érhető el.
>

Bújtatás, a TCP használatával hozhat létre egy hitelesített WebSocket kapcsolaton keresztül a fejlesztői gépen, és a Web App for Containers közötti hálózati kapcsolat. Lehetővé teszi, hogy nyisson meg egy SSH-munkamenet az App Service-ben az ügyfél a választott futtató tárolót.

Első lépésként telepítenie kell [Azure CLI-vel](/cli/azure/install-azure-cli?view=azure-cli-latest). Hogyan működik az Azure parancssori felület telepítése nélkül megtekintéséhez nyissa meg a [Azure Cloud Shell](../../cloud-shell/overview.md). 

A legújabb App Service-bővítmény hozzáadása futtatásával [az bővítmény hozzáadása](/cli/azure/extension?view=azure-cli-latest#az-extension-add):

```azurecli-interactive
az extension add --name webapp
```

Ha már futtatta `az extension add` előtt, futtassa [az bővítmény frissítése](/cli/azure/extension?view=azure-cli-latest#az-extension-update) helyette:

```azurecli-interactive
az extension update --name webapp
```

Távoli kapcsolatot létesíteni az alkalmazás használatával az [az webapp távoli-kapcsolat létrehozása](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) parancsot. Adja meg  _\<előfizetés\_azonosítója >_,  _\<csoport\_neve >_ és \_< alkalmazás\_neve > _-alkalmazások esetén és cserélje le \<port > egy helyi port számát.

```azurecli-interactive
az webapp remote-connection create --subscription <subscription_id> --resource-group <group_name> -n <app_name> -p <port> &
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
