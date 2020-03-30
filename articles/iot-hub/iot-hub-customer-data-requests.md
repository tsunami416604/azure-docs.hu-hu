---
title: Ügyféladat-kérések Az Azure IoT Hub-eszközökhöz
description: Az Azure IoT Hubban kezelt eszközök többsége nem személyes, de néhány. Ez a cikk arról szól, hogy a rendszergazdák exportálhatnak vagy törölhetnek személyes adatokat egy eszközről.
author: robinsh
ms.author: robinsh
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: a05fbf6e1908f88014cd8da99fafb875de033f45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499314"
---
# <a name="summary-of-customer-data-request-features"></a>Az ügyféladat-igénylési funkciók összefoglalása

Az Azure IoT Hub egy REST API-alapú felhőszolgáltatás, amely a vállalati ügyfeleket célozza meg, és lehetővé teszi a biztonságos, kétirányú kommunikációt több millió eszköz és egy particionált Azure-szolgáltatás között.

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

Az egyes eszközökhöz egy bérlői rendszergazda rendel hozzá egy eszközazonosítót (eszközazonosítót). Az eszközadatok a hozzárendelt eszközazonosítón alapulnak. A Microsoft nem tart fenn adatokat, és nem fér hozzá olyan adatokhoz, amelyek lehetővé tennék az eszközazonosító t a felhasználói korrelációhoz.

Az Azure IoT Hubban kezelt eszközök közül sok nem személyes eszköz, például egy irodai termosztát vagy gyári robot. Az ügyfelek azonban úgy tekinthetik, hogy egyes eszközök személyazonosításra alkalmasak, és saját belátásuk szerint fenntarthatják saját eszköz- vagy készletkövetési módszereiket, amelyek az eszközöket egyénekhez kötik. Az Azure IoT Hub úgy kezeli és tárolja az eszközökhöz társított összes adatot, mintha azok személyes adatok lennének.

A bérlői rendszergazdák az Azure Portalon vagy a szolgáltatás REST API-iban is teljesíthetik az információkéréseket az eszközazonosítóhoz társított adatok exportálásával vagy törlésével.

Ha az Azure IoT Hub szolgáltatás útválasztási szolgáltatását használja az eszközüzenetek más szolgáltatásoknak való továbbításához, akkor az adatkéréseket a bérlői rendszergazdának kell végrehajtania minden útválasztási végponthoz, hogy egy adott eszköz teljes kérelmet teljesíthesse. További részletekért tekintse meg az egyes végpontok referenciadokumentációját. A támogatott végpontokról a [Referencia - IoT Hub végpontok](iot-hub-devguide-endpoints.md)című témakörben talál további információt.

Ha az Azure IoT Hub szolgáltatás Azure Event Grid-integrációs funkcióját használja, akkor az adatkéréseket a bérlői rendszergazdának kell végrehajtania az események minden egyes előfizetője számára. További információ: [Reagálva az IoT Hub-események az Event Grid használatával című témakörben.](iot-hub-event-grid.md)

Ha az Azure IoT Hub szolgáltatás Azure Monitor integrációs szolgáltatását használja diagnosztikai naplók létrehozásához, akkor az adatkéréseket a bérlői rendszergazdának kell végrehajtania a tárolt naplók ellen. További információ: [Az Azure IoT Hub állapotának figyelése.](iot-hub-monitor-resource-health.md)

## <a name="deleting-customer-data"></a>Ügyféladatok törlése

A bérlői rendszergazdák az Azure IoT Hub-bővítmény IoT-eszközök paneljét használhatják az Azure Portalon egy eszköz törléséhez, amely törli az adott eszközhöz társított adatokat.

A REST API-kat használó eszközök törlési műveletei is végrehajthatók. További információ: [Service - Delete Device](/rest/api/iothub/service/registrymanager/deletedevice).

## <a name="exporting-customer-data"></a>Vevői adatok exportálása

A bérlői rendszergazdák az Azure IoT Hub-bővítmény IoT-eszközök ablaktábláján belül másolhatnak és illeszthetnek be egy eszközhöz társított adatokat.

A REST API-kat használó eszközök exportálási műveletei is végrehajthatók. További információ: [Service - Get Device](/rest/api/iothub/service/registrymanager/getdevice).

> [!NOTE]
> A Microsoft vállalati szolgáltatásainak használatakor a Microsoft létrehoz néhány információt, más néven a rendszer által létrehozott naplókat. Egyes Azure IoT Hub rendszer által létrehozott naplók nem érhetők el, vagy exportálható a bérlői rendszergazdák. Ezek a naplók a szolgáltatáson belül végzett tényszerű műveleteket és az egyes eszközökkel kapcsolatos diagnosztikai adatokat jelentik.

## <a name="links-to-additional-documentation"></a>További dokumentációra mutató hivatkozások

Az Azure IoT Hub Service API-k teljes dokumentációja az [IoT Hub szolgáltatás API-jainál](https://docs.microsoft.com/rest/api/iothub/service/configuration)található.
