---
title: Állapot megőrzése Linux rendszeren – Azure Event Grid IoT Edge | Microsoft Docs
description: Metaadatok megőrzése Linuxon
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 6639b8f4d947720db38a6b366c47700a56154af3
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992274"
---
# <a name="persist-state-in-linux"></a>Állapot megőrzése Linuxon

A Event Grid modulban létrehozott témaköröket és előfizetéseket alapértelmezés szerint a tároló fájlrendszerében tárolja a rendszer. Az adatmegőrzés nélkül, ha a modul újratelepítése megtörténik, az összes létrehozott metaadat elvész. Jelenleg csak a metaadatok maradnak meg. Az eseményeket a memóriában tárolja a rendszer. Ha Event Grid modul újratelepítése vagy újraindítása megtörtént, akkor a rendszer elveszi a nem kézbesítési eseményeket.

Ez a cikk azokat a lépéseket ismerteti, amelyekkel üzembe helyezheti a Event Grid modult a Linux rendszerű üzemelő példányokban.

> [!NOTE]
>Az Event Grid modul alacsony jogosultsági szintű felhasználóként fut, és az UID `2000` és a name `eventgriduser`.

## <a name="persistence-via-volume-mount"></a>Adatmegőrzés a Volume Mount használatával

 A [Docker-kötetek](https://docs.docker.com/storage/volumes/) a központi telepítések közötti adatmegőrzésre szolgálnak. Lehetővé teheti, hogy a Docker automatikusan hozzon létre egy nevesített kötetet a Event Grid modul üzembe helyezésének részeként. Ez a legegyszerűbb lehetőség. A **kötések** szakaszban megadhatja a kötet nevét, amelyet a következőképpen hozhat létre:

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>Ne módosítsa a kötési érték második részét. Egy adott helyre mutat a modulon belül. A Linux Event Grid moduljának **/app/metadata**kell lennie.

A következő konfiguráció például azt eredményezi, hogy a kötet **egmetadataDbVol** hozza létre, ahol a metaadatok megmaradnak.

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
      "egmetadataDbVol:/app/metadataDb"
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

Azt is megteheti, hogy létrehoz egy Docker-kötetet a Docker-ügyfél parancsaival. 

## <a name="persistence-via-host-directory-mount"></a>Adatmegőrzés a gazdagép könyvtárának csatlakoztatása révén

A Docker-kötetek helyett lehetősége van a gazdagép-mappák csatlakoztatására is.

1. Először hozzon létre egy **eventgriduser** és ID **2000** nevű felhasználót a gazdagépen a következő parancs futtatásával:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. A következő parancs futtatásával hozzon létre egy könyvtárat a gazdagép fájlrendszerén.

   ```sh
   md <your-directory-name-here>
   ```

    A következő parancs futtatása például létrehoz egy **myhostdir**nevű könyvtárat.

    ```sh
    md /myhostdir
    ```
1. Ezután a következő parancs futtatásával tegye **eventgriduser** a mappa tulajdonosát.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Például:

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. A **kötések** használatával csatlakoztassa a könyvtárat, és telepítse újra a Event Grid modult Azure Portal.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb"
             ]
         }
    }
    ```

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
                  "/myhostdir:/app/metadataDb"
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
    >Ne módosítsa a kötési érték második részét. Egy adott helyre mutat a modulon belül. A Linux Event Grid moduljának **/app/metadata**kell lennie.
