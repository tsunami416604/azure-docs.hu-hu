---
title: Vásárlói adatok funkciók
description: Vásárlói adatok funkciók összefoglalása
author: dominicbetts
ms.author: dobett
manager: timlt
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: c0197b8f0cceab575aa5e830d094ddd2fb6b6b01
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436295"
---
# <a name="summary-of-customer-data-request-features"></a>Vásárlói adatok funkciók összefoglalása

Az Azure IoT Hub REST API-alapú felhőszolgáltatás vállalati ügyfelek célzott, amely lehetővé teszi több millió eszköz és a egy particionált Azure-szolgáltatás közötti biztonságos, kétirányú kommunikációra.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Egyes eszközök egy Bérlői rendszergazda által kiosztott egy eszközazonosítót (Eszközazonosító). Eszközadatok alapul a hozzárendelt eszközazonosítót. A Microsoft tart fenn semmilyen adatot, és nem fér hozzá, amelyek lehetővé teszik, hogy a felhasználó megfelelési Eszközazonosító adatokhoz.

Számos az Azure IoT Hub által felügyelt eszközök, amelyek nem személyes eszközök, például az office thermostat vagy a gyári robot. Az ügyfelek is, azonban figyelembe kell venni néhány eszközt személyes azonosításra alkalmas lehet, és saját belátása szerint a saját eszköz vagy a leltár nyomon követése módszerek elősegítsék eszközök egyéni felhasználók számára fenntarthatják. Az Azure IoT Hub kezeli, és, mintha a személyes adatok eszközökhöz társított minden adatot tárol.

A bérlői rendszergazdák segítségével az Azure Portalon vagy a szolgáltatás REST API-k információk kérések teljesítését által exportálása, vagy törölje a társított eszköz-azonosítóval.

Ha a útválasztási szolgáltatását, az Azure IoT Hub szolgáltatás használatával más szolgáltatások eszköz üzenetek továbbítására, majd kérelmek kell elvégezni útválasztási végpontok a bérlői rendszergazda által egy adott eszközhöz egy teljes kérelem végrehajtásához. Minden végpont dokumentációja további részletekért lásd:. További információ a támogatott végpontok: [referencia – IoT Hub-végpontok](iot-hub-devguide-endpoints.md).

Ha az Azure IoT Hub szolgáltatás az Azure Event Grid integráció funkcióját használja, majd kérelmek kell elvégezni a bérlői rendszergazda által ezek az események minden előfizető. További információkért lásd: [reagálhat rájuk az IoT Hub Event Grid használatával](iot-hub-event-grid.md).

Ha az Azure Monitor integrációja az Azure IoT Hub szolgáltatás használatával a diagnosztikai naplók létrehozása, majd kérelmek kell elvégezni ellen a tárolt naplókat a bérlői rendszergazda által. További információkért lásd: [Azure IoT Hub állapotának figyelésére](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Vásárlói adatok törlése

A bérlői rendszergazdák törli egy eszköz, amely törli az adott eszközhöz társított adatokat használhatja az Azure Portalon az Azure IoT Hub-bővítmény az IoT-Eszközök panelen.

Akkor is REST API-kat használó eszközök esetében a törlési műveletek végrehajtásához. További információkért lásd: [szolgáltatás - eszköz törlése](https://docs.microsoft.com/rest/api/iothub/service/deletedevice).

## <a name="exporting-customer-data"></a>Vásárlói adatok exportálása

A bérlői rendszergazdák is használni a másolási, és illessze be az IoT-Eszközök panel az Azure IoT Hub-bővítmény, az eszközök adatainak exportálása az Azure Portalon belül.

Akkor is REST API-kat használó eszközök esetében exportálási műveletek végrehajtásához. További információkért lásd: [Service – Get eszköz](https://docs.microsoft.com/rest/api/iothub/service/getdevice).

> [!NOTE]
> A Microsoft enterprise szolgáltatást használ, a Microsoft néhány információt, a rendszer által létrehozott naplók néven hoz létre. Egyes Azure IoT Hub a rendszer által létrehozott naplókban nincsenek elérhető vagy nem exportálható a bérlői rendszergazdák. Ezek a naplók a műveletek előzményrekordjait a szolgáltatás és az egyes eszközökkel kapcsolatos diagnosztikai adatok képezik.

## <a name="links-to-additional-documentation"></a>További dokumentációkra mutató hivatkozások

Az Azure IoT Hub szolgáltatás API-k teljes dokumentációt a következő helyen található [ https://docs.microsoft.com/rest/api/iothub/service ](https://docs.microsoft.com/rest/api/iothub/service).