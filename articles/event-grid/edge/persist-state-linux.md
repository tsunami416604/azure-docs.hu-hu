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
ms.openlocfilehash: 39b16c6cfd5b94d412827ed88197edbef2da1453
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844632"
---
# <a name="persist-state-in-linux"></a>Állapot megőrzése Linuxon

A Event Grid modulban létrehozott témaköröket és előfizetéseket a rendszer alapértelmezés szerint a tároló fájlrendszerében tárolja. Az adatmegőrzés nélkül, ha a modul újratelepítése megtörténik, az összes létrehozott metaadat elvész. A központi telepítések és újraindítások közötti adatmegőrzéshez a tároló fájlrendszerén kívül kell megőriznie az adattárolást.

Alapértelmezés szerint a rendszer csak a metaadatokat őrzi meg, az eseményeket pedig a memóriában tárolja a jobb teljesítmény érdekében. Az esemény-megőrzés engedélyezéséhez kövesse az események megőrzése szakaszt is.

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
>Ne módosítsa a kötési érték második részét. Egy adott helyre mutat a modulon belül. A Linux Event Grid moduljának **/app/metadataDb**kell lennie.

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

Kötet csatlakoztatása helyett létrehozhat egy könyvtárat a gazdagépen, és csatlakoztathatja a könyvtárat.

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
    >Ne módosítsa a kötési érték második részét. Egy adott helyre mutat a modulon belül. A Linux Event Grid moduljának **/app/metadata**kell lennie.


## <a name="persist-events"></a>Események megőrzése

Az események megőrzésének engedélyezéséhez előbb engedélyeznie kell a metaadatok megőrzését a Volume Mount vagy a Host Directory Mount használatával a fenti fejezetek segítségével.

Fontos tudnivalók a megőrzött eseményekről:

* Az események megtartása esemény-előfizetések alapján engedélyezett, és a kötet vagy a könyvtár csatlakoztatása után is engedélyezve van.
* Az esemény-megőrzés a létrehozáskor egy esemény-előfizetésen van konfigurálva, és az esemény-előfizetés létrehozása után nem módosítható. Az események megőrzésének váltásához törölnie kell, majd újra létre kell hoznia az esemény-előfizetést.
* A megőrzött események szinte mindig lassabbak, mint a memóriabeli műveletekben, azonban a sebességbeli különbség nagymértékben függ a meghajtó jellemzőitől. A gyorsaság és a megbízhatóság közötti kompromisszum minden üzenetkezelő rendszerhez alkalmazkodik, de általában nagy léptékű észrevehető válik.

Ha engedélyezni szeretné az esemény-megőrzést egy esemény-előfizetésben, állítsa a `persistencePolicy` `true`:

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