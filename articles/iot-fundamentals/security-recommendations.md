---
title: Biztonsági javaslatok az Azure IoT-hez | Microsoft dokumentumok
description: Ez a cikk összefoglalja az Azure IoT Hub-megoldás biztonságának biztosításához szükséges további lépéseket.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom:
- security-recommendations
- amqp
- mqtt
ms.openlocfilehash: 5dd09988d37982c41b761688492bd2dc3642b2db
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81728983"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Biztonsági javaslatok az Azure Internet of Things (IoT) telepítéséhez

Ez a cikk az IoT biztonsági javaslatait tartalmazza. Ezeknek az ajánlásoknak a végrehajtása segít a közös felelősségi modellünkben leírt biztonsági kötelezettségek teljesítésében. Ha többet szeretne tudni arról, hogy a Microsoft mit tesz a szolgáltatói feladatok teljesítése érdekében, olvassa el [a Megosztott felelősségi körök a felhőalapú számítástechnikával](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)kapcsolatos című információt.

A cikkben szereplő javaslatok közül néhányat az Azure Security Center automatikusan figyelhet. Az Azure Security Center az első védelmi vonal az erőforrások védelmében az Azure-ban. Rendszeresen elemzi az Azure-erőforrások biztonsági állapotát a potenciális biztonsági rések azonosításához. Ezután ajánlásokat ad arra vonatkozóan, hogyan kell kezelni őket.

- Az Azure Security Center-javaslatokról az [Azure Security Center biztonsági javaslatai](../security-center/security-center-recommendations.md)című témakörben talál további információt.
- Az Azure Security Centerről a [Mi az Azure Security Center?](../security-center/security-center-intro.md)

## <a name="general"></a>Általános kérdések

| Ajánlás | Megjegyzések | Az ASC támogatásával |
|-|----|--|
| Legyen naprakész | A támogatott platformok, programozási nyelvek, protokollok és keretrendszerek legújabb verzióit használhatja. | - |
| A hitelesítési kulcsok biztonságának megőrzése | Az eszközazonosítók és a hitelesítési kulcsok fizikailag biztonságban legyenek a telepítés után. Ezzel elkerülhető, hogy egy rosszindulatú eszköz regisztrált eszköznek álcázza magát. | - |
| Eszköz SDK-k használata, ha lehetséges | Az eszköz SDK-k számos biztonsági funkciót valósítanak meg, például titkosítást, hitelesítést és így tovább, hogy segítsenek egy robusztus és biztonságos eszközalkalmazás fejlesztésében. További [információért olvassa el az Azure IoT Hub SDK-k ismertetése és használata](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) című témakört. | - |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés 

| Ajánlás | Megjegyzések | Az ASC támogatásával |
|-|----|--|
| Hozzáférés-vezérlés definiálása a hubhoz | [Ismerje meg és határozza meg, hogy az](iot-security-deployment.md#securing-the-cloud) egyes összetevők milyen típusú hozzáféréssel rendelkeznek az IoT Hub-megoldásban a funkciók alapján. A megengedett engedélyek a következők: *Rendszerleíró adatbázis olvasása*, *RegistryReadWrite*, *ServiceConnect*és *DeviceConnect*. Az IoT hub alapértelmezett [megosztott hozzáférési szabályzatai](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) is segíthetnek meghatározni az egyes összetevők engedélyeit a szerepköre alapján. | - |
| Hozzáférés-vezérlés definiálása háttérszolgáltatásokhoz | Az IoT Hub-megoldás által bevitt adatokat más Azure-szolgáltatások, például [a Cosmos DB,](https://docs.microsoft.com/azure/cosmos-db/) [a Stream Analytics,](https://docs.microsoft.com/azure/stream-analytics/) [az App Service,](https://docs.microsoft.com/azure/app-service/) [a Logic Apps](https://docs.microsoft.com/azure/logic-apps/)és a [Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)is felhasználhatja. Győződjön meg arról, hogy megértette és engedélyezni a megfelelő hozzáférési engedélyeket dokumentált ezeket a szolgáltatásokat. | - |

## <a name="data-protection"></a>Adatvédelem

| Ajánlás | Megjegyzések | Az ASC támogatásával |
|-|----|--|
| Biztonságos eszközhitelesítés | Biztosítsa a biztonságos kommunikációt az eszközök és az IoT hub között, akár [egy egyedi identitáskulcs vagy biztonsági jogkivonat,](iot-security-deployment.md#iot-hub-security-tokens)vagy [egy eszközön lévő X.509 tanúsítvány](iot-security-deployment.md#x509-certificate-based-device-authentication) használatával minden eszközön. A megfelelő módszerrel [használja a kiválasztott protokollon (MQTT, AMQP vagy HTTPS) alapuló biztonsági jogkivonatokat.](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security) | - |
| Biztonságos eszközkommunikáció | Az IoT Hub biztosítja a kapcsolatot az eszközökkel a Transport Layer Security (TLS) szabvány, az 1.2-es és 1.0-s verziók támogatása használatával. A maximális biztonság érdekében használja a [TLS 1.2-t.](https://tools.ietf.org/html/rfc5246) | - |
| Biztonságos szolgáltatáskommunikáció | Az IoT Hub végpontokat biztosít a háttérszolgáltatásokhoz, például az [Azure Storage-hoz](/azure/storage/) vagy az [Event Hubs-hoz](/azure/event-hubs) csak a TLS protokoll használatával való csatlakozáshoz, és nincs végpont egy titkosítatlan csatornán. Amint ezek az adatok elériezeket a háttérszolgáltatások tárolásvagy elemzés, győződjön meg róla, hogy megfelelő biztonsági és titkosítási módszereket alkalmaznak a szolgáltatáshoz, és a bizalmas adatok védelme a háttérrendszerben. | - |

## <a name="networking"></a>Hálózat

| Ajánlás | Megjegyzések | Az ASC támogatásával |
|-|----|--|
| Az eszközökhöz való hozzáférés védelme | Tartsa a hardverportokat az eszközökön a lehető legkisebbre a nem kívánt hozzáférés elkerülése érdekében. Ezenkívül mechanizmusokat hozhat létre az eszköz fizikai illetéktelen beavatkozásának megelőzésére vagy észlelésére. Olvassa el az [IoT biztonsági gyakorlati tanácsait](iot-security-best-practices.md) a részletekért. | - |
| Biztonságos hardver létrehozása | Az eszközök és az infrastruktúra biztonságosabbá tétele érdekében olyan biztonsági funkciókat is beépíthet, mint a titkosított tárolás vagy a Platformmegbízhatósági modul (TPM). Tartsa az eszköz operációs rendszerét és illesztőprogramjait a legújabb verziókra frissítve, és ha a hely lehetővé teszi, telepítse a víruskereső és kártevőirtó képességeit. Olvassa el [az IoT biztonsági architektúrát,](iot-security-architecture.md) hogy ismerje meg, hogyan segíthet ez számos biztonsági fenyegetés csökkentésében. | - |

## <a name="monitoring"></a>Figyelés

| Ajánlás | Megjegyzések | Az ASC támogatásával |
|-|----|--|
| Az eszközökhöz való jogosulatlan hozzáférés figyelése |  Az eszköz operációs rendszerének naplózási szolgáltatásával figyelheti az eszköz vagy portjai biztonsági megsértését vagy fizikai illetéktelen beavatkozását. | - |
| Az IoT-megoldás figyelése a felhőből | Figyelje az IoT Hub-megoldás általános állapotát az [Azure Monitor metrikák](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics)használatával. | - |
| Diagnosztika beállítása | Szorosan figyelje a műveleteket az események naplózásával a megoldásban, majd küldje el a diagnosztikai naplókat az Azure Monitornak, hogy láthatóvá tegye a teljesítményt. További információért olvassa el [a Figyelő és az IoT-központ problémáinak diagnosztizálása](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) című részt. | - |

## <a name="next-steps"></a>További lépések

Az Azure IoT-t érintő speciális forgatókönyvek esetén előfordulhat, hogy további biztonsági követelményeket kell figyelembe vennie. További útmutatásért tekintse meg az [IoT biztonsági architektúráját.](iot-security-architecture.md)

