---
title: További adatok átvitele az eszköz és az Azure Device kiépítési szolgáltatás között
description: Ez a dokumentum azt ismerteti, hogyan vihetők át további adatok az eszköz és a DPS között
author: menchi
ms.author: menchi
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 1ff06afd363745ae465a8f5b625c27a4a9e4a222
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609605"
---
# <a name="how-to-transfer-additional-data-between-device-and-dps"></a>További adatok átvitele az eszköz és a DPS között
A DPS-nek esetenként további adatokra van szüksége az eszközről ahhoz, hogy megfelelő módon kiépíthesse azokat a helyes IoT Hubon, az adatokat pedig az eszköznek kell rendelkezésre bocsátania. A DPS azonban visszatérhet az eszközre, hogy az ügyféloldali logikát is megkönnyítse. 

## <a name="when-to-use-it"></a>Mikor érdemes használni
Ez a funkció az [Egyéni foglalások](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)fejlesztéséhez használható. Például az eszköz modellje alapján szeretné kiosztani az eszközöket az emberi beavatkozás nélkül. Ebben az esetben [Egyéni foglalást](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)fog használni. Beállíthatja, hogy az eszköz bejelentse a modell adatait az [eszköz regisztrálása hívásának](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice)részeként. A DPS továbbítja az eszköz adatait az egyéni kiosztási webhookba. A függvény eldöntheti, hogy mely IoT Hub az eszköz fog megjelenni, amikor megkapja az eszköz modelljének adatait. Ehhez hasonlóan ha a webhook adatokat küld vissza az eszköznek, akkor ezeket sztringként adja vissza a webhookválaszban.  

## <a name="device-sends-data-to-dps"></a>Az eszköz adatokat küld a DPS-nek
Ha az eszköz [regisztrálja az eszközt, hívja](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) meg a DPS-t. A regisztrálási hívás növelhető úgy, hogy a törzs többi mezőjét is felhasználhassa. A törzs a következőhöz hasonlóan néz ki: 
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

## <a name="next-steps"></a>További lépések
* Fejlesztés az Azure IoT Hub és az Azure-hoz készült [Azure IOT SDK]( https://github.com/Azure/azure-iot-sdks) -val IoT hub Device Provisioning Service