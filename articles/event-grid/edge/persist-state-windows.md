---
title: Állapot megőrzése a Windowsban – Azure Event Grid IoT Edge | Microsoft Docs
description: Állapot megőrzése a Windowsban
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 485c6d4a92539a2ba67aece319c68d31649e8045
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992261"
---
# <a name="persist-state-in-windows"></a>Állapot megőrzése a Windowsban

A Event Grid modulban létrehozott témaköröket és előfizetéseket alapértelmezés szerint a tároló fájlrendszerében tárolja a rendszer. Az adatmegőrzés nélkül, ha a modul újratelepítése megtörténik, az összes létrehozott metaadat elvész. Az adatüzemelő példányok közötti megőrzéshez a tároló fájlrendszerén kívül kell megőriznie az adattárolást. Jelenleg csak a metaadatok maradnak meg. Az eseményeket a memóriában tárolja a rendszer. Ha Event Grid modul újratelepítése vagy újraindítása megtörtént, akkor a rendszer elveszi a nem kézbesítési eseményeket.

Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek a Windows rendszerű központi telepítések Event Grid moduljának telepítéséhez.

> [!NOTE]
>A Event Grid modul alacsony jogosultsági szintű felhasználói **ContainerUser** fut a Windows rendszerben.

## <a name="persistence-via-volume-mount"></a>Adatmegőrzés a Volume Mount használatával

A [Docker-kötetek](https://docs.docker.com/storage/volumes/) használatával megőrizhető az egyes központi telepítések. Kötet csatlakoztatásához a Docker-parancsokkal létre kell hoznia a tárolót, engedélyeket kell adnia, hogy a tároló képes legyen olvasni, írni, majd üzembe helyezni a modult. Nincs lehetőség arra, hogy automatikusan hozzon létre egy kötetet a Windows rendszeren szükséges engedélyekkel. A telepítés előtt létre kell hozni.

1. Hozzon létre egy kötetet a következő parancs futtatásával:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Például:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Az alábbi parancs futtatásával szerezze be a kötethez hozzárendelni kívánt gazdagépi könyvtárat:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Például:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Minta kimenete:-

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. Adja hozzá a **felhasználók** csoportot a **csatlakoztatási pont** által mutatott értékhez a következőképpen:
    1. Indítsa el a fájlkezelőt.
    1. Navigáljon a **csatlakoztatási pont**által mutatott mappára.
    1. Kattintson a jobb gombbal, majd válassza a **Tulajdonságok**lehetőséget.
    1. Válassza a **Biztonság**elemet.
    1. A * csoport vagy felhasználó neve területen válassza a **Szerkesztés**lehetőséget.
    1. Válassza a **Hozzáadás**lehetőséget, írja be `Users` **, válassza a Névellenőrzés lehetőséget**, majd kattintson **az OK gombra**.
    1. A *Felhasználók engedélyei*területen válassza a **módosítás**lehetőséget, majd kattintson **az OK gombra**.
1. **Kötések** használata a kötet csatlakoztatásához és Event Grid modul újbóli üzembe helyezéséhez Azure Portal

   Például:

    ```json
        {
              "Env": [
                "inbound:serverAuth:tlsPolicy=strict",
                "inbound:serverAuth:serverCert:source=IoTEdge",
                "inbound:clientAuth:sasKeys:enabled=false",
                "inbound:clientAuth:clientCert:enabled=true",
                "inbound:clientAuth:clientCert:source=IoTEdge",
                "inbound:clientAuth:clientCert:allowUnknownCA=true",
                "outbound:clientAuth:clientCert:enabled=true",
                "outbound:clientAuth:clientCert:source=IoTEdge",
                "outbound:webhook:httpsOnly=true",
                "outbound:webhook:skipServerCertValidation=false",
                "outbound:webhook:allowUnknownCA=true"
              ],
              "HostConfig": {
                "Binds": [
                  "<your-volume-name-here>:C:\\app\\metadataDb"
                ],
                "PortBindings": {
                  "4438/tcp": [
                     {
                        "HostPort": "4438"
                     }
                  ]
                }
              }
        }
    ```

   >[!IMPORTANT]
   >Ne módosítsa a kötési érték második részét. A modul egy adott helyére mutat. A Windows Event Grid moduljában a következőnek kell lennie **: C:\\app\\metadataDb**.


    Például:

    ```json
    {
        "Env": [
            "inbound:serverAuth:tlsPolicy=strict",
            "inbound:serverAuth:serverCert:source=IoTEdge",
            "inbound:clientAuth:sasKeys:enabled=false",
            "inbound:clientAuth:clientCert:enabled=true",
            "inbound:clientAuth:clientCert:source=IoTEdge",
            "inbound:clientAuth:clientCert:allowUnknownCA=true",
            "outbound:clientAuth:clientCert:enabled=true",
            "outbound:clientAuth:clientCert:source=IoTEdge",
            "outbound:webhook:httpsOnly=true",
            "outbound:webhook:skipServerCertValidation=false",
            "outbound:webhook:allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "myeventgridvol:C:\\app\\metadataDb"
             ],
             "PortBindings": {
                    "4438/tcp": [
                         {
                            "HostPort": "4438"
                          }
                    ]
              }
         }
    }
    ```

## <a name="persistence-via-host-directory-mount"></a>Adatmegőrzés a gazdagép könyvtárának csatlakoztatása révén

Azt is megteheti, hogy létrehoz egy könyvtárat a gazda rendszeren, és csatlakoztatja azt a címtárhoz.

1. Hozzon létre egy könyvtárat a gazdagép fájlrendszerén a következő parancs futtatásával.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Például:

   ```sh
   mkdir C:\myhostdir
   ```
1. A **kötések** használatával csatlakoztassa a könyvtárat, és telepítse újra a Event Grid modult Azure Portal.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >Ne módosítsa a kötési érték második részét. A modul egy adott helyére mutat. A Windows Event Grid moduljának a következőnek kell lennie **: C:\\app\\metadataDb**.

    Például:

    ```json
    {
        "Env": [
            "inbound:serverAuth:tlsPolicy=strict",
            "inbound:serverAuth:serverCert:source=IoTEdge",
            "inbound:clientAuth:sasKeys:enabled=false",
            "inbound:clientAuth:clientCert:enabled=true",
            "inbound:clientAuth:clientCert:source=IoTEdge",
            "inbound:clientAuth:clientCert:allowUnknownCA=true",
            "outbound:clientAuth:clientCert:enabled=true",
            "outbound:clientAuth:clientCert:source=IoTEdge",
            "outbound:webhook:httpsOnly=true",
            "outbound:webhook:skipServerCertValidation=false",
            "outbound:webhook:allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "C:\\myhostdir:C:\\app\\metadataDb"
             ],
             "PortBindings": {
                    "4438/tcp": [
                         {
                            "HostPort": "4438"
                          }
                    ]
              }
         }
    }
    ```
