---
title: Megőrzési állapot a Windows rendszerben – Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Állapot megőrzése a Windows rendszerben
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c2bae3bd268dba8efdf23ae314671b17a2c89420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086625"
---
# <a name="persist-state-in-windows"></a>Állapot megőrzése a Windows rendszerben

Az Event Grid modulban létrehozott témakörök és előfizetések alapértelmezés szerint a tárolófájlrendszerben tárolófájlrendszerben tárolóba kerülnek. Megőrzés nélkül, ha a modul újratelepítése, az összes létrehozott metaadatok elveszne. Az adatok megőrzése a központi telepítések és újraindítások között, meg kell őriznie az adatokat a tároló fájlrendszeren kívül. 

Alapértelmezés szerint csak a metaadatok maradnak meg, és az események továbbra is a memóriában tárolódnak a jobb teljesítmény érdekében. Kövesse az események megőrzése szakaszt az eseménymegőrzés engedélyezéséhez is.

Ez a cikk az Event Grid modul windows-központi telepítésekben való megőrzéséhez szükséges lépéseket ismerteti.

> [!NOTE]
>Az Event Grid modul alacsony jogosultságú **felhasználóként** fut a Windows ban.

## <a name="persistence-via-volume-mount"></a>Perzisztencia a kötetcsatlakoztatással

[Docker-kötetek](https://docs.docker.com/storage/volumes/) adatok megőrzése a központi telepítések között. Egy kötet csatlakoztatása, docker-parancsok használatával kell létrehozni, engedélyeket adni, hogy a tároló olvasni, írni, majd telepíteni a modult.

1. Kötet létrehozása a következő parancs futtatásával:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Például:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. A kötet által leképezett gazdakönyvtár beszerezni az alábbi parancs futtatásával

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Például:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Minta kimenet:-

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
1. Adja hozzá a **Felhasználók** csoportot a **MountPoint** által mutatott értékhez az alábbiak szerint:
    1. Indítsa el a Fájlkezelőt.
    1. Keresse meg a **Mountpoint**által mutatott mappát.
    1. Kattintson a jobb gombbal, majd válassza **a Tulajdonságok parancsot.**
    1. Válassza a **Security** (Biztonság) lehetőséget.
    1. A *Csoport- vagy felhasználónevek csoportban válassza a **Szerkesztés**lehetőséget.
    1. Válassza a `Users` **Hozzáadás**, írja be a Jelet, a Nevek **ellenőrzése**lehetőséget, majd az **Ok**gombot.
    1. A *Felhasználók engedélyei*csoportban válassza a **Modify (Módosítás)** lehetőséget, majd az **Ok parancsot.**
1. **A Kötések** használatával csatlakoztatja ezt a kötetet, és újratelepítheti az Event Grid modult az Azure Portalról

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
   >Ne módosítsa a kötési érték második részét. A modul egy adott helyére mutat. Az Event Grid modul windows, meg kell **C:\\app\\metadataDb**.


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
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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

## <a name="persistence-via-host-directory-mount"></a>Perzisztencia a gazdatár-csatlakoztatással

Kötet csatlakoztatása helyett létrehozhat egy könyvtárat a gazdarendszeren, és csatlakoztathatja a könyvtárat.

1. Hozzon létre egy könyvtárat a gazdafájlrendszeren a következő parancs futtatásával.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Például:

   ```sh
   mkdir C:\myhostdir
   ```
1. **A Binds** használatával csatlakoztathatja a címtárat, és újratelepítheti az Event Grid modult az Azure Portalról.

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
    >Ne módosítsa a kötési érték második részét. A modul egy adott helyére mutat. Az Event Grid modul windows, meg kell **C:\\app\\metadataDb**.

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
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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
## <a name="persist-events"></a>Események megőrzése

Az eseménymegőrzés engedélyezéséhez először engedélyeznie kell az események megőrzését a kötetcsatlakoztatás vagy a gazdakönyvtár csatlakoztatása segítségével a fenti szakaszok használatával.

Fontos tudnivalók a tartós eseményekről:

* A tartós események esemény-előfizetésenként engedélyezve vannak, és a kötet vagy könyvtár csatlakoztatása után engedélyezve vannak.
* Az eseménymegőrzés a létrehozás időpontjában egy esemény-előfizetésen van konfigurálva, és az esemény-előfizetés létrehozása után nem módosítható. Az eseménymegőrzésváltáshoz törölnie kell, majd újra létre kell hoznia az Esemény-előfizetést.
* A tartós események szinte mindig lassabbak, mint a memóriaműveletekben, azonban a sebességkülönbség nagymértékben függ a meghajtó jellemzőitől. A sebesség és a megbízhatóság közötti kompromisszum minden üzenetküldő rendszer velejárója, de csak nagy léptékben válik észrevehetővé.

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