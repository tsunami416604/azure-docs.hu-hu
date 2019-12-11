---
title: További adatok átvitele az eszköz és az Azure Device kiépítési szolgáltatás között
description: Ez a dokumentum azt ismerteti, hogyan lehet további adatátvitelt továbbítani az eszköz és az eszköz kiépítési szolgáltatása (DPS) között.
author: menchi
ms.author: menchi
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e9482f7069616d61efb98f66590ce33cfe3cf350
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974853"
---
# <a name="how-to-transfer-additional-data-between-device-and-dps"></a>További adatok átvitele az eszköz és a DPS között
A DPS-nek esetenként további adatokra van szüksége az eszközről ahhoz, hogy megfelelő módon kiépíthesse azokat a helyes IoT Hubon, az adatokat pedig az eszköznek kell rendelkezésre bocsátania. A DPS azonban visszatérhet az eszközre, hogy az ügyféloldali logikát is megkönnyítse. 

## <a name="when-to-use-it"></a>Használati esetek
Ez a funkció az [Egyéni foglalások](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)fejlesztéséhez használható. Például az eszköz modellje alapján szeretné kiosztani az eszközöket az emberi beavatkozás nélkül. Ebben az esetben [Egyéni foglalást](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)fog használni. Beállíthatja, hogy az eszköz bejelentse a modell adatait az [eszköz regisztrálása hívásának](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice)részeként. A DPS továbbítja az eszköz adatait az egyéni kiosztási webhookba. A függvény eldöntheti, hogy mely IoT Hub az eszköz fog megjelenni, amikor megkapja az eszköz modelljének adatait. Ehhez hasonlóan ha a webhook adatokat küld vissza az eszköznek, akkor ezeket sztringként adja vissza a webhookválaszban.  

## <a name="device-sends-data-to-dps"></a>Az eszköz adatokat küld a DPS-nek
Ha az eszköz [regisztrálja](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) a DPS-t, a regisztrálási hívás javítható a törzs más mezőinek elvégzéséhez. A törzs a következőhöz hasonlóan néz ki: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “interfaces”: “TODO: get how interfaces are reported by devices from PnP folks.”, 
       “data”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>A DPS visszaadja az eszköznek az adatok értékét
Ha az egyéni kiosztási szabályzat webhooka bizonyos adatmennyiséget szeretne visszaadni az eszköznek, akkor a webhook-válaszban karakterláncként adja vissza az adatmennyiséget. A módosítás az alábbi returnData szakaszban található. 
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
        "returnData": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK-támogatás
Ez a funkció C, C#, Java és Node. js [ügyféloldali SDK](https://docs.microsoft.com/azure/iot-dps/)-k esetén érhető el.  

## <a name="next-steps"></a>Következő lépések
* Fejlesztés az Azure IoT Hub és az Azure-hoz készült [Azure IOT SDK]( https://github.com/Azure/azure-iot-sdks) -val IoT hub Device Provisioning Service
