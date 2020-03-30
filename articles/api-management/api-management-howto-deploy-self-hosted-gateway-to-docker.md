---
title: Saját üzemeltetésű Azure API Management átjáró üzembe helyezése a Dockerhez | Microsoft dokumentumok
description: Ismerje meg, hogyan helyezhet üzembe saját üzemeltetésű Azure API Management átjárót a Dockerhez
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: b5acda30f7f4a01e1b7b6ac82652452c3c40e3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768503"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Azure API-kezelés saját üzemeltetésű átjáró üzembe helyezése a Dockerben

Ez a cikk az önkiszolgáló Azure API Management átjáró docker-környezetbe való üzembe helyezésének lépéseit ismerteti.

> [!NOTE]
> A saját üzemeltetésű átjárófunkció előzetes verzióban érhető el. Az előzetes verzió során a saját üzemeltetésű átjáró csak a Fejlesztői és prémium szintű csomagokban érhető el további díj nélkül. A fejlesztői szint egyetlen saját üzemeltetésű átjáró-telepítésre korlátozódik.

## <a name="prerequisites"></a>Előfeltételek

- A következő rövid útmutató befejezése: [Hozzon létre egy Azure API Management-példányt](get-started-create-service-instance.md)
- Hozzon létre egy Docker-környezetet. [A Docker for Desktop](https://www.docker.com/products/docker-desktop) egy jó lehetőség fejlesztési és értékelési célokra. A [Docker dokumentációjában](https://docs.docker.com) tájékozódhat az összes Docker-kiadásról, azok funkcióiról és magáról a Dockerről szóló átfogó dokumentációról.
- [Átjáró-erőforrás kiépítése az API Management-példányban](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> A saját üzemeltetésű átjáró x86-64 Linux-alapú Docker-tárolóként van csomagolva.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Az önkiszolgáló átjáró üzembe helyezése a Docker-hez

1. Válassza **az Átjárók lehetőséget** a Beállítások **csoportban.**
2. Válassza ki a telepíteni kívánt átjáró-erőforrást.
3. Válassza a **Központi telepítés**lehetőséget.
4. Vegye figyelembe, hogy egy új jogkivonatot a **Jogkivonat** szövegmezőben automatikusan létrehozták az alapértelmezett **lejárati** és **titkos kulcs** értékek használatával. Szükség esetén módosítsa az egyiket vagy mindkettőt, és válassza a **Létrehozás** lehetőséget új jogkivonat létrehozásához.
4. Győződjön meg arról, hogy a **Docker** ki van jelölve a **Központi telepítési parancsfájlok**csoportban.
5. A fájl letöltéséhez válassza az **env.conf** fájlhivatkozást a **Környezet** mellett.
6. Válassza a **Szövegfuttatás** párbeszédpanel jobb oldalán található Másolás **ikont,** ha a Docker parancsot vágólapra szeretné menteni.
7. Illessze be a parancsot a terminál (vagy parancs) ablakba. Szükség szerint módosítsa a portleképezéseket és a tároló nevét. Ne feledje, hogy a parancs arra számít, hogy a letöltött környezeti fájl megjelenik az aktuális könyvtárban.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Hajtsa végre a parancsot. A parancs arra utasítja a Docker-környezetet, hogy futtassa a tárolót a Microsoft Container Registry rendszeréből letöltött saját üzemeltetésű átjáró rendszerképének használatával, és rendelje le a tároló HTTP -es (8080) és HTTPS (8081) portjait az állomás 80-as és 443-as portjaihoz.
9. Futtassa az alábbi parancsot, és ellenőrizze, hogy fut-e az átjárópod:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Lépjen vissza az Azure Portalra, és ellenőrizze, hogy az imént üzembe helyezett átjárócsomópont kifogástalan állapotot jelent-e.

![átjáró állapota](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> A <code>console docker container logs <gateway-name></code> parancs segítségével megtekintheti a saját üzemeltetésű átjárónapló pillanatképét.
>
> A <code>docker container logs --help</code> parancs segítségével megtekintheti az összes naplómegtekintési beállítást.

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a saját üzemeltetésű átjáróról, olvassa el az [Azure API Management saját üzemeltetésű átjáró – áttekintés című témakört.](self-hosted-gateway-overview.md)
* [Egyéni tartománynév konfigurálása a saját üzemeltetett átjáróhoz.](api-management-howto-configure-custom-domain-gateway.md)
