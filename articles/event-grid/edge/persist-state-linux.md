---
title: Megmaradó állapot Linuxon – Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Metaadatok megőrzése Linux alatt
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 12655d2ceb4a1124376d9bddf82194472c98ebb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086648"
---
# <a name="persist-state-in-linux"></a>Állapot megőrzése linuxos állapotban

Az Event Grid modulban létrehozott témakörök és előfizetések alapértelmezés szerint a tárolófájlrendszerben tárolófájlrendszerben tárolóba kerülnek. Megőrzés nélkül, ha a modul újratelepítése, az összes létrehozott metaadatok elveszne. Az adatok megőrzése a központi telepítések és újraindítások között, meg kell őriznie az adatokat a tároló fájlrendszeren kívül.

Alapértelmezés szerint csak a metaadatok maradnak meg, és az események továbbra is a memóriában tárolódnak a jobb teljesítmény érdekében. Kövesse az események megőrzése szakaszt az eseménymegőrzés engedélyezéséhez is.

Ez a cikk az Event Grid modul linuxos központi telepítéseiben való megőrzési alkalmazással történő üzembe helyezésének lépéseit tartalmazza.

> [!NOTE]
>Az Event Grid modul alacsony jogosultsági szintű felhasználóként fut UID `2000` azonosítóval és névvel. `eventgriduser`

## <a name="persistence-via-volume-mount"></a>Perzisztencia a kötetcsatlakoztatással

 [Docker-kötetek](https://docs.docker.com/storage/volumes/) az adatok megőrzésére a központi telepítések között. A docker automatikusan létrehozhat egy elnevezett kötetet az Event Grid modul üzembe helyezése részeként. Ez az opció a legegyszerűbb lehetőség. A **kötések** szakaszban a következőképpen adhatja meg a létrehozandó kötet nevét:

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
>Ne módosítsa a kötési érték második részét. A modulon belül egy adott helyre mutat. A Linuxon lévő Event Grid modulhoz **a /app/metadataDb**-nek kell lennie.

Például a következő konfiguráció eredményeképpen létre kell venni az **egmetadataDbVol** kötetet, ahol a metaadatok megmaradnak.

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb",
      "egdataDbVol:/app/eventsDb"
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

Kötet csatlakoztatása helyett létrehozhat egy könyvtárat a gazdarendszeren, és csatlakoztathatja a könyvtárat.

## <a name="persistence-via-host-directory-mount"></a>Perzisztencia a gazdatár-csatlakoztatással

Docker-kötet helyett is lehetősége van egy gazdamappa csatlakoztatására.

1. Először hozzon létre egy **felhasználót eventgriduser** névvel és **2000** azonosítójú felhasználóval a gazdagépen a következő parancs futtatásával:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Hozzon létre egy könyvtárat a gazdafájlrendszeren a következő parancs futtatásával.

   ```sh
   md <your-directory-name-here>
   ```

    A következő parancs futtatása például létrehoz egy **myhostdir**nevű könyvtárat.

    ```sh
    md /myhostdir
    ```
1. Ezután tegye **az eventgriduser** tulajdonosát a mappához a következő parancs futtatásával.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Például:

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. **A Binds** használatával csatlakoztatja a címtárat, és újratelepítheti az Event Grid modult az Azure Portalról.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb",
                "<your-directory-name-here>:/app/eventsDb",
             ]
         }
    }
    ```

    Például:

    ```json
    {
          "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb",
                  "/myhostdir2:/app/eventsDb"
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
    >Ne módosítsa a kötési érték második részét. A modulon belül egy adott helyre mutat. A linuxos Event Grid modulhoz **a /app/metadataDb** és **a /app/eventsDb** kapcsolónak kell lennie.


## <a name="persist-events"></a>Események megőrzése

Az eseménymegőrzés engedélyezéséhez először engedélyeznie kell a metaadat-megőrzést a kötetcsatlakoztatás vagy a gazdakönyvtár csatlakoztatása segítségével a fenti szakaszok használatával.

Fontos tudnivalók a tartós eseményekről:

* A tartós események esemény-előfizetésenként engedélyezve vannak, és a kötet vagy könyvtár csatlakoztatása után engedélyezve vannak.
* Az eseménymegőrzés a létrehozás időpontjában egy esemény-előfizetésen van konfigurálva, és az esemény-előfizetés létrehozása után nem módosítható. Az eseménymegőrzésváltáshoz törölnie kell, majd újra létre kell hoznia az Esemény-előfizetést.
* A tartós események szinte mindig lassabbak, mint a memóriaműveletekben, azonban a sebességkülönbség nagymértékben függ a meghajtó jellemzőitől. A sebesség és a megbízhatóság közötti kompromisszum minden üzenetküldő rendszer velejárója, de általában csak nagy léptékben válik észrevehetővé.

Az esemény-előfizetés eseménymegőrzésének engedélyezéséhez `true`állítsa a következőt: `persistencePolicy`

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```
