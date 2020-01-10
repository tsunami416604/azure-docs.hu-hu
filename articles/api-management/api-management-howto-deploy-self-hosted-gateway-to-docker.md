---
title: Saját üzemeltetésű Azure API Management-átjáró üzembe helyezése a Docker-ben | Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe egy saját üzemeltetésű Azure API Management-átjárót a Docker-ben
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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768503"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Azure API Management saját üzemeltetésű átjáró üzembe helyezése a Docker-ben

Ez a cikk a saját üzemeltetésű Azure API Management-átjárók Docker-környezetbe való üzembe helyezésének lépéseit ismerteti.

> [!NOTE]
> A saját üzemeltetésű átjáró funkció előzetes verzióban érhető el. Az előzetes verzió ideje alatt a saját üzemeltetésű átjáró csak a fejlesztői és prémium szinteken érhető el, díjmentesen. A fejlesztői réteg egyetlen saját üzemeltetésű átjáróra korlátozódik.

## <a name="prerequisites"></a>Előfeltételek

- Végezze el a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md)
- Hozzon létre egy Docker-környezetet. Az [asztali Docker](https://www.docker.com/products/docker-desktop) jó megoldás fejlesztési és értékelési célokra. A Docker-kiadásokról, azok szolgáltatásairól és a Docker-ről szóló átfogó dokumentációról a [Docker dokumentációjában](https://docs.docker.com) talál további információt.
- [Átjáró-erőforrás kiépítése a API Management-példányban](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> A saját üzemeltetésű átjáró x86-64 Linux-alapú Docker-tárolóként van csomagolva.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>A saját üzemeltetésű átjáró üzembe helyezése a Docker-ben

1. Válassza az **átjárók** lehetőséget a **Beállítások**területen.
2. Válassza ki azt az átjáró-erőforrást, amelyet telepíteni kíván.
3. Válassza a **telepítés**lehetőséget.
4. Vegye figyelembe, hogy a **jogkivonat** szövegmezőben lévő új jogkivonat automatikusan lett létrehozva az alapértelmezett **lejárati** és **titkos kulcs** értékeinek használatával. Ha szükséges, módosítsa a vagy mindkettőt, és válassza a **Létrehozás** lehetőséget egy új jogkivonat létrehozásához.
4. Győződjön meg arról, hogy a **Docker** ki van választva a **telepítési parancsfájlok**területen.
5. A fájl letöltéséhez válassza az **env. conf** fájl hivatkozását a **környezet** mellett.
6. A **Run (Futtatás** ) szövegmező jobb oldalán található **másolási** ikont választva mentse a Docker-parancsot a vágólapra.
7. Illessze be a parancsot a terminál (vagy a parancs) ablakába. Szükség szerint módosítsa a port-hozzárendeléseket és a tároló nevét. Vegye figyelembe, hogy a parancs elvárja, hogy a letöltött környezeti fájl megtalálható legyen az aktuális könyvtárban.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Hajtsa végre a parancsot. A parancs arra utasítja a Docker-környezetet, hogy futtassa a tárolót a saját üzemeltetésű átjáró a Microsoft Container Registry letöltött rendszerképének használatával, valamint a tároló HTTP-(8080-) és HTTPS-(8081-) portjainak a gazdagépen lévő 80-és 443-portokra való leképezéséhez.
9. Futtassa az alábbi parancsot az átjáró Pod futtatásának vizsgálatához:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Lépjen vissza a Azure Portalra, és ellenőrizze, hogy az imént telepített átjáró-csomópont a kifogástalan állapotot jelenti-e.

![átjáró állapota](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> A <code>console docker container logs <gateway-name></code> parancs használatával megtekintheti a saját üzemeltetésű átjáró naplójának pillanatképét.
>
> <code>docker container logs --help</code> parancs használatával tekintheti meg az összes naplózási megtekintési beállítást.

## <a name="next-steps"></a>Következő lépések

* További információ a saját üzemeltetésű átjáróról: az [Azure API Management saját üzemeltetésű átjárójának áttekintése](self-hosted-gateway-overview.md).
* [Konfigurálja az egyéni tartománynevet a saját üzemeltetésű átjáróhoz](api-management-howto-configure-custom-domain-gateway.md).
