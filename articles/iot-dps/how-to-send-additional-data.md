---
title: Hasznos adat átvitele az eszköz és az Azure-eszközkiépítési szolgáltatás között
description: Ez a dokumentum bemutatja, hogyan lehet átvinni a hasznos adatokat az eszköz és az eszközkiépítési szolgáltatás (DPS) között
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: de6bb3fe0879a69467283e93a04a355876a02cba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246687"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>Hogyan lehet átvinni a hasznos terhet az eszköz és a DPS között
Előfordulhat, hogy a DPS-nek több adatra van szüksége az eszközökről, hogy megfelelően kiépíthesse azokat a megfelelő IoT Hubra, és hogy az adatokat az eszköznek kell biztosítania. Fordítva, a DPS adatokat adhat vissza az eszköznek az ügyféloldali logika megkönnyítése érdekében. 

## <a name="when-to-use-it"></a>Mikor lehet használni
Ez a funkció [az egyéni foglalás](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)javításaként használható. Például szeretné lefoglalni az eszközöket az eszköz modell emberi beavatkozás nélkül. Ebben az esetben [egyéni foglalást](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)fog használni. Beállíthatja, hogy az eszköz jelentse a modelladatait a [regisztereszköz-hívás](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice)részeként. A DPS átadja az eszköz hasznos adatát az egyéni foglalási webhooknak. És a funkció eldöntheti, hogy az eszköz melyik IoT Hubhoz kerül, amikor megkapja az eszközmodell-információkat. Hasonlóképpen, ha a webhook vissza szeretne adni bizonyos adatokat az eszközre, az adatokat a webhook-válasz karakterláncaként adja vissza.  

## <a name="device-sends-data-payload-to-dps"></a>Az eszköz adathasznos adatküldést küld a DPS-nek
Amikor a készülék [regisztereszköz-hívást](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) küld a DPS-nek, a regiszterhívás továbbfejleszthető, hogy a szervezet más mezőit is igénybe vehesse. A test úgy néz ki, mint a következő: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>A DPS adatokat ad vissza az eszköznek
Ha az egyéni foglalási házirend webhook vissza szeretne adni bizonyos adatokat az eszközre, akkor adja vissza az adatokat egy karakterláncként a webhook válasz. A változás az alábbi hasznos adatrészben található. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK-támogatás
Ez a funkció C, C#, JAVA és Node.js [ügyfél SDK-kban](https://docs.microsoft.com/azure/iot-dps/)érhető el.  

## <a name="next-steps"></a>További lépések
* Fejlesztés az Azure IoT Hubhoz és az Azure IoT Hub eszközkiépítési szolgáltatásához tervezett [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) használatával
