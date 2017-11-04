---
title: "SSH-támogatás az Azure App Service-Linux |} Microsoft Docs"
description: "További tudnivalók az Azure App Service Linux SSH használatával."
keywords: "az Azure app service, webalkalmazás, linux, oss"
services: app-service
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.openlocfilehash: 7e6bb974565810ebb8d8e21d1c274d42d6d39e55
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Az Azure App Service-Linux SSH-támogatás

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) kriptográfiai hálózati protokoll a biztonságos hálózati szolgáltatások használatával. Leggyakrabban jelentkezzen be a rendszer távolról biztonságosan parancsot a parancssorból, és távolról felügyeleti parancsok végrehajtására szolgál.

App Service Linux támogatja az SSH az alkalmazás tárolóba egyes a beépített Docker lemezképeket az új webes alkalmazások futásidejű verem használható.

![Futásidejű verem](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Többek között az SSH-kiszolgálót a lemezkép részeként, és ebben a témakörben leírtak szerint konfigurálja úgy az egyéni Docker-lemezképekkel is használható SSH.

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

Ezen lépések végrehajtása az Azure App Service-tárházban, mint látható [például](https://github.com/Azure-App-Service/node/blob/master/6.9.3/).

1. Tartalmazza a `openssh-server` telepítés [ `RUN` utasítás](https://docs.docker.com/engine/reference/builder/#run) a lemezképet, és a beállított fiók jelszavát a legfelső szintű Dockerfile a `"Docker!"`.

    > [!NOTE]
    > Ez a konfiguráció nem teszi lehetővé a tároló külső kapcsolatot. SSH csak a Kudu keresztül érhető el vagy SCM helyhez, ami hitelesített közzétételi hitelesítő adatok használatával.

    ```docker
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

1. Adja hozzá egy [ `COPY` utasítás](https://docs.docker.com/engine/reference/builder/#copy) történő másolásához Dockerfile egy [sshd_config](http://man.openbsd.org/sshd_config) fájlt a */stb/ssh/* könyvtár. A konfigurációs fájl alapján a sshd_config fájlt az Azure-alkalmazásszolgáltatási GitHub-tárházban [Itt](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config).

    > [!NOTE]
    > A *sshd_config* tartalmaznia kell a következő, vagy a kapcsolat hibája esetén: 
    > * `Ciphers`tartalmaznia kell legalább a következők egyikét: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs`tartalmaznia kell legalább a következők egyikét: `hmac-sha1,hmac-sha1-96`.

    ```docker
    COPY sshd_config /etc/ssh/
    ```

1. Port 2222 közé tartozik a [ `EXPOSE` utasítás](https://docs.docker.com/engine/reference/builder/#expose) a Dockerfile számára. Bár a gyökér szintű jelszó is ismert, port 2222 nem érhető el az internetről. Egy belső egyetlen port elérhető csak által a virtuális magánhálózat híd hálózati tárolókra is.

    ```docker
    EXPOSE 2222 80
    ```

1. Ügyeljen arra, hogy [indítsa el az ssh szolgáltatás](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh) rendszerhéj-parancsfájl használatával */bin* könyvtár.

    ```bash
    #!/bin/bash
    service ssh start
    ```

A Dockerfile használja a [ `CMD` utasítás](https://docs.docker.com/engine/reference/builder/#cmd) a parancsfájl futtatásához.

    ```docker
    COPY init_container.sh /bin/
    ...
    RUN chmod 755 /bin/init_container.sh
    ...
    CMD ["/bin/init_container.sh"]
    ```

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő hivatkozásokat az tárolókat webalkalmazás vonatkozó további információért. Kérdések és problémákat is közzétesz a [fórumban](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [A tárolók webalkalmazás egyéni Docker-lemezkép használata](quickstart-custom-docker-image.md)
* [Használatával a .NET Core Linux Azure App Service-ben](quickstart-dotnetcore.md)
* [Ruby használata Linux Azure App Service-ben](quickstart-ruby.md)
* [Az Azure App Service webalkalmazás tárolók – gyakori kérdések](app-service-linux-faq.md)