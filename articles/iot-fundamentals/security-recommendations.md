---
title: Biztonsági javaslatok az Azure IoT |} A Microsoft Docs
description: Ez a cikk az Azure IoT Hub-megoldásban biztonsága érdekében további lépéseket foglalja össze.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dkshir
ms.openlocfilehash: d079e082fb8ac90f398e46f283bd1e33e2b4ab40
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67463083"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Biztonsági javaslatok az Azure Internet of Things (IoT) központi telepítés

Ez a cikk az Azure IoT-szolgáltatások biztonsági javaslatokat tartalmaz. Ezek az ajánlások megvalósítása súgó Microsoft Azure IoT, és a rendszer a biztonsági kötelezettségeit, teljesítése javítja az IoT-megoldások általános biztonságát. Az Azure IoT által biztosított belső biztonsági funkcióiról további információért olvassa el [IoT-biztonság létrehozása az alapoktól](iot-security-ground-up.md).

## <a name="general"></a>Általános kérdések

| Ajánlás | Megjegyzések |
|-|-|
| Naprakész | A támogatott platformok, programnyelvek, protokollok és keretrendszereket legfrissebb verzióit használja. |
| Hitelesítési kulcsok biztonsága | Biztonságban az eszköz azonosítóját és a hitelesítési kulcsát ténylegesen üzembe helyezés után. Így elkerülhető, egy rosszindulatú eszköz helyettesítő regisztrált eszközként. |
| Ha lehetséges eszközoldali SDK-k használata | Eszközoldali SDK-k megvalósítása a különféle biztonsági funkciókat, például titkosítással, hitelesítéssel és így tovább, segítségére lehetnek a robusztus és biztonságos eszköz alkalmazás fejlesztése. Lásd: [megismerése és használata az Azure IoT Hub SDK-k](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) további információt. |


## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Ajánlás | Megjegyzések |
|-|-|
| A hub hozzáférés-vezérlés meghatározása | [Megismerheti, és adja meg a hozzáférési](iot-security-deployment.md#securing-the-cloud) az egyes összetevők el fogja tudni az IoT Hub-megoldás a funkcionalitás alapján. Az engedélyezett engedélyek *beállításjegyzék olvasási*, *RegistryReadWrite*, *ServiceConnect*, és *DeviceConnect*. Alapértelmezett [megosztott hozzáférési házirendek az IoT hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) is segít a szerepkör alapján egyes összetevők engedélyeinek megadásához. |
| Hozzáférés-vezérlés a háttérszolgáltatások definiálása | Az IoT Hub-megoldás által betöltött adatok által felhasználható más Azure-szolgáltatások például [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), [App Service-ben](https://docs.microsoft.com/azure/app-service/), [Logic Apps](https://docs.microsoft.com/azure/logic-apps/), és [a Blob storage-](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Ügyeljen arra, hogy ismertetése, és lehetővé teszik a megfelelő hozzáférési engedélyekkel ezen szolgáltatások leírtak szerint. |


## <a name="data-protection"></a>Adatvédelem

| Ajánlás | Megjegyzések |
|-|-|
| Biztonságos eszközhitelesítés | Az eszközök és az IoT hub közötti biztonságos kommunikáció biztosítása a [egy egyedi kulcs, vagy a biztonsági jogkivonat](iot-security-deployment.md#iot-hub-security-tokens), vagy [egy eszközre irányuló X.509 tanúsítvány](iot-security-deployment.md#x509-certificate-based-device-authentication) minden egyes eszközhöz. Használja a megfelelő módszert a [biztonsági jogkivonatokat a kiválasztott protokoll (MQTT, AMQP vagy HTTPS) használata](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). |
| Biztonságos kommunikáció | Az IoT Hub a kapcsolat biztonságossá tételére az eszközökön, használja a Transport Layer Security (TLS) standard, 1.0-s, 1.2-es és 1.2-es verziók támogatása. Használat [a TLS 1.2](https://tools.ietf.org/html/rfc5246) maximális biztonságának megteremtéséhez. |
| A szolgáltatások közötti kommunikáció biztonságossá tétele | IoT Hub által biztosított, például a csatlakozás a Háttérszolgáltatásokhoz végpontok [Azure Storage](/azure/storage/) vagy [az Event Hubs](/azure/event-hubs) titkosítatlan csatornán csak a TLS protokoll és a nem figyelt olyan végpont használatával van közzétéve. Ha ezek az adatok ezeket a háttérszolgáltatásokat, tárolási és analitikai elérte, ügyeljen arra, hogy a alkalmazni, hogy a szolgáltatás megfelelő biztonsági és titkosítási módszereket, és a háttérrendszer a bizalmas adatok védelme. |


## <a name="networking"></a>Hálózat

| Ajánlás | Megjegyzések |
|-|-|
| Az eszközök való hozzáférés védelme | Ne hardver portok az eszközök a nem kívánt hozzáférésnek elkerülése érdekében legalább. Emellett hozhat létre megakadályozza vagy fizikai illetéktelen módosítását az eszköz észlelése mechanizmusokat. Olvasási [IoT ajánlott biztonsági eljárások](iot-security-best-practices.md) részleteiről. |
| Hozhat létre biztonságos hardver | Titkosított tárolást, vagy a platformmegbízhatósági modul (TPM), eszközök és infrastruktúra nagyobb biztonságban, biztonsági funkciókat építhetnek be. Az eszköz operációs rendszere és illesztőprogramok frissítése a legújabb verzióra, és lehetővé teszi a helyet, telepítse a víruskereső és kártevőirtó funkciókat. Olvasási [IoT biztonsági architektúra](iot-security-architecture.md) megérteni, hogyan Ez segíthet elhárítani a számos biztonsági fenyegetéseket. |


## <a name="monitoring"></a>Figyelés

| Ajánlás | Megjegyzések |
|-|-|
| Az eszközök jogosulatlan hozzáférés figyelése |  Az eszköz operációs rendszerének a naplózási szolgáltatás használatával bármilyen biztonsági problémák vagy a fizikai illetéktelen módosítását az eszköz vagy a portok figyeli. |
| A felhőben az IoT-megoldások monitorozása | Az IoT Hub megoldás használatával általános állapotának figyelése a [metrikák az Azure monitorban](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). |
| Diagnosztika beállítása | Szorosan tekintse meg a műveletek a megoldás az események naplózását, és elküldi a diagnosztikai naplók az Azure Monitor betekintést nyerhet a teljesítmény. Olvasási [figyelése és problémák diagnosztizálása az IoT hub a](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) további információt. |

## <a name="next-steps"></a>További lépések

Használata esetén az Azure IoT speciális forgatókönyvek esetén szükség lehet érdemes lehet további biztonsági követelményeket. Lásd: [IoT biztonsági architektúra](iot-security-architecture.md) további útmutatást.

