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
ms.openlocfilehash: cf27e852f5ec9b7e12b0c678e9940596bc57b385
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Az Azure App Service-Linux SSH-támogatás

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) kriptográfiai hálózati protokoll a biztonságos hálózati szolgáltatások használatával. Leggyakrabban jelentkezzen be a rendszer távolról biztonságosan parancsot a parancssorból, és távolról felügyeleti parancsok végrehajtására szolgál.

App Service Linux támogatja az SSH az alkalmazás tárolóba egyes a beépített Docker lemezképeket az új webes alkalmazások futásidejű verem használható.

![Futásidejű verem](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Is használhatja SSH az egyéni Docker-lemezképekkel többek között az SSH-kiszolgálót a lemezkép részeként, és konfigurálja úgy a cikkben leírtak szerint.

> [!NOTE] 
> Keresztül is csatlakozhat a alkalmazástárolóval közvetlenül gépről a helyi fejlesztési segítségével SSH, SFTP vagy Visual Studio Code (az élő hibakeresési Node.js alkalmazások). További információkért lásd: [távoli hibakereséssel és az App Service Linux SSH](https://aka.ms/linux-debug).
>

## <a name="making-a-client-connection"></a>Egy ügyfél-kapcsolatot

Ahhoz, hogy az SSH-ügyfél kapcsolat, a fő helye el kell indítani.

A webalkalmazás a forrás vezérlő Management (SCM) végpont illessze be a böngészőt az alábbi paranccsal:

```bash
https://<your sitename>.scm.azurewebsites.net/webssh/host
```

Ha Ön nem már hitelesítve, hitelesítik magukat az Azure-előfizetéshez való csatlakozáshoz szükségesek.

![SSH-kapcsolat](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="ssh-support-with-custom-docker-images"></a>Egyéni Docker-lemezképekkel SSH-támogatás

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

## <a name="next-steps"></a>További lépések

Kérdések és problémákat is közzétesz a a [Azure fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

A webalkalmazás az tárolókat további információkért lásd:

* [Egyéni Docker-rendszerkép használata a Web App for Containers szolgáltatásban](quickstart-docker-go.md)
* [A .NET Core használata a Linuxon futó Azure App Service-ben](quickstart-dotnetcore.md)
* [A Ruby használata a Linuxon futó Azure App Service-ben](quickstart-ruby.md)
* [Azure App Service Web App for Containers – gyakori kérdések](app-service-linux-faq.md)