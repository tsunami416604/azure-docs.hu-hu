---
title: SSH-támogatás az Azure App Service-Linux |} Microsoft Docs
description: További tudnivalók az Azure App Service Linux SSH használatával.
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
ms.openlocfilehash: c2beb67a27b667d31402b903f38dbf116e9425d0
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34301075"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Az Azure App Service-Linux SSH-támogatás

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) távolról végre felügyeleti parancsok parancssori terminált gyakran használatos. App Service Linux támogatja az SSH az alkalmazás tárolóba egyes a beépített Docker lemezképeket az új webes alkalmazások futásidejű verem használható. 

![Futásidejű verem](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Egyéni Docker képek, úgy konfigurálja az SSH-kiszolgálót a az egyéni lemezképet.

Akkor is csatlakozhatnak a tároló közvetlenül a helyi fejlesztési számítógépén SSH és az SFTP használatával.

## <a name="open-ssh-session-in-browser"></a>SSH munkamenet megnyitása böngészőben

Ahhoz, hogy az SSH-ügyfél kapcsolat, a tárolóval, az alkalmazás futnia kell.

A következő URL-cím illessze be a böngésző és a név felülírandó \<alkalmazás_neve > Saját alkalmazás nevét:

```
https://<app_name>.scm.azurewebsites.net/webssh/host
```

Ha Ön nem már hitelesítve, hitelesítik magukat az Azure-előfizetéshez való csatlakozáshoz szükségesek. Hitelesítését követően megjelenik egy böngésző rendszerhéj, ahol futtassa a parancsokat a tároló belül.

![SSH-kapcsolat](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="use-ssh-support-with-custom-docker-images"></a>SSH-támogatás használata egyéni Docker-lemezképek

Ahhoz, hogy egy egyéni Docker lemezképeinek SSH-kommunikációhoz a tároló és az ügyfél között az Azure portálon hajtsa végre a következő lépéseket a Docker kép.

Ezeket a lépéseket az Azure App Service-tárházban, mint látható [például](https://github.com/Azure-App-Service/node/blob/master/6.9.3/).

1. Tartalmazza a `openssh-server` telepítés [ `RUN` utasítás](https://docs.docker.com/engine/reference/builder/#run) a lemezképet, és a beállított fiók jelszavát a legfelső szintű Dockerfile a `"Docker!"`.

    > [!NOTE]
    > Ez a konfiguráció nem tesz elérhetővé külső kapcsolatokat a tárolóhoz. SSH csak a Kudu keresztül érhető el vagy SCM helyhez, ami hitelesített közzétételi hitelesítő adatok használatával.

    ```docker
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

1. Adja hozzá egy [ `COPY` utasítás](https://docs.docker.com/engine/reference/builder/#copy) történő másolásához Dockerfile egy [sshd_config](http://man.openbsd.org/sshd_config) fájlt a */stb/ssh/* könyvtár. A konfigurációs fájlban kell alapozni az az Azure-alkalmazásszolgáltatási GitHub-tárházban sshd_config fájlban [Itt](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config).

    > [!NOTE]
    > A *sshd_config* tartalmaznia kell a következő, vagy a kapcsolat hibája esetén: 
    > * `Ciphers` tartalmaznia kell legalább a következők egyikét: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` tartalmaznia kell legalább a következők egyikét: `hmac-sha1,hmac-sha1-96`.

    ```docker
    COPY sshd_config /etc/ssh/
    ```

1. Port 2222 közé tartozik a [ `EXPOSE` utasítás](https://docs.docker.com/engine/reference/builder/#expose) a Dockerfile számára. Bár a rendszergazdai szintű jelszó ismert, a 2222-es port nem érhető el az internet irányából. Egy belső egyetlen port elérhető csak által a virtuális magánhálózat híd hálózati tárolókra is.

    ```docker
    EXPOSE 2222 80
    ```

1. Ügyeljen arra, hogy az SSH szolgáltatás elindításához használja a PowerShell parancsfájlhoz (Példa: [init_container.sh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)).

    ```bash
    #!/bin/bash
    service ssh start
    ```

A Dockerfile használja a [ `ENTRYPOINT` utasítás](https://docs.docker.com/engine/reference/builder/#entrypoint) a parancsfájl futtatásához.

    ```docker
    COPY startup /opt/startup
    ...
    RUN chmod 755 /opt/startup/init_container.sh
    ...
    ENTRYPOINT ["/opt/startup/init_container.sh"]
    ```

## <a name="open-ssh-session-from-remote-shell"></a>Nyissa meg az SSH-munkamenetet a távoli rendszerhéj

> [!NOTE]
> Ez a funkció jelenleg előzetes verzió.
>

Bújtatás, TCP használatával hozhat létre a hálózati kapcsolatot a fejlesztési számítógépén és Web App az tárolókat hitelesített WebSocket-kapcsolaton keresztül. Ez lehetővé teszi, hogy nyissa meg az SSH-munkamenetet a tárolóval, az App Service-ben az ügyfél az Ön által választott futtatja.

A kezdéshez telepítendő [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Hogyan működik az Azure parancssori felület telepítése nélkül megtekintéséhez nyissa meg a [Azure Cloud rendszerhéj](../../cloud-shell/overview.md). 

A legújabb App Service-bővítmény hozzáadása futtatásával [az bővítmény hozzáadása](/cli/azure/extension?view=azure-cli-latest#az-extension-add):

```azurecli-interactive
az extension add -–name webapp
```

Ha már futtatta `az extension add` elindítása előtt, [az bővítményfrissítést](/cli/azure/extension?view=azure-cli-latest#az-extension-update) helyette:

```azurecli-interactive
az extension update -–name webapp
```

Távoli kapcsolatot létesíteni az alkalmazás használata a [az webapp távoli-kapcsolat létrehozása](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) parancsot. Adja meg  _\<csoport\_neve >_ és \_< app\_neve > _ az alkalmazást, és cserélje le \<port > helyi portszámmal.

```azurecli-interactive
az webapp remote-connection create --resource-group <group_name> -n <app_name> -p <port> &
```

> [!TIP]
> `&` a parancs végén a folyamat csak kényelmi felhő rendszerhéj használata. Ez a folyamat a háttérben fut, hogy az azonos rendszerhéjban a következő parancsot futtathatja.

A parancs kimenetét a jelenít meg adatokat meg kell nyitnia az SSH-munkamenetet.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Nyisson meg egy SSH-munkamenetet a tárolót a helyi portot használja az Ön által választott, az ügyfél. Az alábbi példában az alapértelmezett [ssh](https://ss64.com/bash/ssh.html) parancs:

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

Amikor a rendszer kéri, írja be a `yes` csatlakozás a folytatáshoz. Ezután jelszót kér. Használjon `Docker!`, amely volt, a korábban bemutatott.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

Ha most hitelesítése megtörtént, meg kell jelennie a munkamenet üdvözlőképernyőn.

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

Az összekötő már csatlakozik. 

Próbálja meg fut a [felső](https://ss64.com/bash/top.html) parancsot. Az alkalmazás folyamat folyamat listájában látni kell lennie. Az alábbi példa kimenetben, a másikat is `PID 263`.

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

Kérdések és problémákat is közzétesz a a [Azure fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

A webalkalmazás az tárolókat további információkért lásd:

* [Az Azure App Service szolgáltatásban a Visual STUDIO Code Node.js alkalmazások távoli hibakeresés bemutatása](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Egyéni Docker-rendszerkép használata a Web App for Containers szolgáltatásban](quickstart-docker-go.md)
* [A .NET Core használata a Linuxon futó Azure App Service-ben](quickstart-dotnetcore.md)
* [A Ruby használata a Linuxon futó Azure App Service-ben](quickstart-ruby.md)
* [Azure App Service Web App for Containers – gyakori kérdések](app-service-linux-faq.md)