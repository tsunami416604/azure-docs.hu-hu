---
title: Felhasználói adatok kérelem szolgáltatások
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
ms.openlocfilehash: 3af76fe22e93b6c5d502733196994bda61b9a93e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="summary-of-customer-data-request-features"></a>Felhasználói adatok kérelem szolgáltatásainak összefoglalása

Az Azure IoT Hub REST API-alapú felhőszolgáltatás vállalati ügyfelek is, amely lehetővé teszi az eszközök millióira és egy particionált Azure szolgáltatás közötti biztonságos, kétirányú kommunikáció.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Egyes eszközök a bérlői rendszergazda rendeli hozzá a eszközazonosító (Eszközazonosító). Eszköz alapul a hozzárendelt eszközazonosító. A Microsoft kezeli az adatokat, és nem fér hozzá, amely lehetővé tenné, hogy a felhasználó korrelációs azonosító adatok.

Az eszközök felügyelete az Azure IoT Hub közül sok nem személyes eszközök, például az office termosztát vagy a gyári robot. Az ügyfelek is, azonban vegye figyelembe a személyes azonosításra alkalmas lehet néhány eszközt, és saját belátása szerint a saját eszköz vagy a készlet nyomon követése a módszereket, amelyek egyéni eszközök összekötését fenntarthatja. Azure IoT Hub kezeli, és tárolja a személyes adatok, mintha eszközök kapcsolódó összes adat.

A bérlői rendszergazdák használatával vagy az Azure-portálon, vagy a szolgáltatás REST API-k adatokat kérelmeinek teljesítéséhez exportálással vagy egy eszköz. társított adatok törlése

Az útválasztási szolgáltatás a Azure IoT-központ szolgáltatás más szolgáltatások eszközre küldött üzenetek továbbítására használatakor, majd kérelmek kell elvégezni a bérlői rendszergazda útválasztási végpontok ahhoz, hogy az adott eszköz teljes kérelem végrehajtása. További részletek minden egyes végpont hivatkozás dokumentációjában talál. További információ a támogatott végpontok: [referencia - IoT-központok végpontjai](iot-hub-devguide-endpoints.md).

Ha az Azure Event rács integrációs szolgáltatás számára az Azure IoT-központ szolgáltatás használja, majd kérelmek kell elvégezni a bérlői rendszergazda által mindegyik előfizető ezek az események. További információkért lásd: [reagálni az IoT-központ események esemény rács használatával](iot-hub-event-grid.md).

Az Azure-figyelő integrációs szolgáltatás számára az Azure IoT-központ szolgáltatás diagnosztikai naplók létrehozására használhatja, ha majd kérelmek kell végrehajtani a bérlői rendszergazda a tárolt naplók ellen. További információkért lásd: [Azure IoT Hub állapotának figyelésére](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Felhasználói adatok törlése

A bérlői rendszergazdák törli az adatokat az adott eszközhöz hozzárendelt eszköz törlése használhatja az Azure-portálon az IoT-Eszközök panel az Azure IoT Hub-bővítmény.

Akkor is REST API-k használatával eszközök delete művelet végrehajtásához. További információkért lásd: [eszköz Api - eszköz törlése](https://docs.microsoft.com/rest/api/iothub/deviceapi/deletedevice).

## <a name="exporting-customer-data"></a>Felhasználói adatok exportálása

A bérlői rendszergazdák másolási használják, és illessze be az IoT-Eszközök panel az Azure IoT Hub-bővítmény, az Azure portálon az eszközök adatainak exportálása belül.

Akkor is REST API-k használatával exportálási műveleteinek elvégzéséhez. További információkért lásd: [eszköz Api - eszköz beolvasása](https://docs.microsoft.com/rest/api/iothub/deviceapi/getdevice).

> [!NOTE]
> A Microsoft vállalati szolgáltatást használ, a Microsoft néhány információt, a rendszer naplókat néven hoz létre. Bizonyos Azure IoT Hub rendszer által létrehozott naplók nem elérhetők, és a bérlői rendszergazdák által exportálható. Ezek a naplók a szolgáltatás és az egyes eszközökkel kapcsolatos diagnosztikai adatok végzett tényleges műveletek alkotják.

## <a name="links-to-additional-documentation"></a>További dokumentációkra mutató hivatkozások

Azure IoT Hub eszköz API-k teljes dokumentációját itt található: [ https://docs.microsoft.com/rest/api/iothub/deviceapi ](https://docs.microsoft.com/rest/api/iothub/deviceapi).