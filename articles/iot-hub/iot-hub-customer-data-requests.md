---
title: Az Azure IoT Hub-eszközökre vonatkozó ügyféladatok-kérelmek
description: Az Azure IoT Hub által felügyelt eszközök többsége nem személyes, néhány pedig. Ez a cikk azt ismerteti, hogy a rendszergazdák hogyan tudják exportálni vagy törölni a személyes adatait egy eszközről.
author: robinsh
ms.author: robinsh
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: a05fbf6e1908f88014cd8da99fafb875de033f45
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499314"
---
# <a name="summary-of-customer-data-request-features"></a>A vásárlói adatkérések funkcióinak összefoglalása

Az Azure IoT Hub a nagyvállalati ügyfeleknek szánt, REST API-alapú felhőalapú szolgáltatás, amely biztonságos, kétirányú kommunikációt tesz lehetővé több millió eszköz és egy particionált Azure-szolgáltatás között.

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

Az egyes eszközökhöz a bérlői rendszergazda rendeli az eszköz azonosítóját (eszköz-AZONOSÍTÓját). Az eszközre vonatkozó adat a hozzárendelt eszköz azonosítója alapján történik. A Microsoft nem tart fenn információt, és nem fér hozzá olyan adatokhoz, amelyek lehetővé tennék az eszköz AZONOSÍTÓjának felhasználói korrelációt.

Az Azure IoT Hubban felügyelt eszközök közül sok nem személyes eszköz, például irodai termosztát vagy gyári robot. Előfordulhat azonban, hogy egyes eszközök személyes azonosításra alkalmasak, és saját belátásuk szerint fenntartják saját eszközeiket vagy leltározási követési módszereiket, amelyek az eszközöket az egyéni felhasználók számára használják. Az Azure IoT Hub kezeli és tárolja az eszközökhöz kapcsolódó összes olyan adatmennyiséget, amely személyes adatként volt.

A bérlői rendszergazdák vagy a Azure Portal vagy a szolgáltatás REST API-jai segítségével teljesítik az adatkéréseket az eszköz-AZONOSÍTÓhoz társított adatok exportálásával vagy törlésével.

Ha az Azure IoT Hub szolgáltatás útválasztási funkciójával továbbítja az üzeneteket más szolgáltatásoknak, akkor az egyes útválasztási végpontok esetén az adatkéréseket a bérlői rendszergazdának kell elvégeznie az adott eszközre vonatkozó teljes kérelem teljesítése érdekében. További részletekért tekintse meg az egyes végpontok hivatkozási dokumentációját. További információ a támogatott végpontokról: [referenciák IoT hub végpontok](iot-hub-devguide-endpoints.md).

Ha az Azure IoT Hub szolgáltatás Azure Event Grid integrációs szolgáltatását használja, akkor az egyes előfizetőknél az adatkéréseket a bérlői rendszergazdának kell elvégrehajtania. További információ: [IoT hub eseményekre való reagálás Event Grid használatával](iot-hub-event-grid.md).

Ha az Azure IoT Hub szolgáltatás Azure Monitor integrációs funkciójával diagnosztikai naplókat hoz létre, akkor az adatkéréseket a bérlői rendszergazdának kell végrehajtania a tárolt naplókon. További információ: [Az Azure IoT hub állapotának monitorozása](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Ügyféladatok törlése

A bérlői rendszergazdák az Azure IoT Hub bővítmény IoT-eszközök paneljén használhatják a Azure Portal egy eszköz törléséhez, amely törli az adott eszközhöz társított adatmennyiséget.

A REST API-kat használó eszközök törlési műveleteit is el lehet végezni. További információ: [szolgáltatás – eszköz törlése](/rest/api/iothub/service/registrymanager/deletedevice).

## <a name="exporting-customer-data"></a>Ügyféladatok exportálása

A bérlői rendszergazdák a Azure Portal Azure IoT Hub bővítményének IoT-eszközök paneljén a másolás és beillesztés funkciót használhatják az eszközhöz társított adatexportáláshoz.

A REST API-kat használó eszközök exportálási műveleteit is el lehet végezni. További információ: [szolgáltatás – eszköz beolvasása](/rest/api/iothub/service/registrymanager/getdevice).

> [!NOTE]
> A Microsoft nagyvállalati szolgáltatásainak használatakor a Microsoft bizonyos adatokat hoz létre a rendszer által létrehozott naplók néven. Néhány Azure-IoT Hub rendszer által generált napló nem érhető el vagy nem exportálható a bérlői rendszergazdák számára. Ezek a naplók a szolgáltatáson belüli, illetve az egyes eszközökhöz kapcsolódó diagnosztikai adatokból származó ténybeli műveleteket alkotnak.

## <a name="links-to-additional-documentation"></a>További dokumentációra mutató hivatkozások

Az Azure IoT Hub Service API-k teljes dokumentációja [IoT hub Service API](https://docs.microsoft.com/rest/api/iothub/service/configuration)-kon található.
