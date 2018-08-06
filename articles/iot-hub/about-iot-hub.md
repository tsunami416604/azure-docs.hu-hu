---
title: Az Azure IoT Hub bemutatása | Microsoft Docs
description: Tudnivalók az Azure IoT Hubról. Ez az IoT-szolgáltatás skálázható adatfeldolgozáshoz, eszközkezeléshez és biztonsághoz lett tervezve.
author: nberdy
ms.author: nberdy
ms.date: 07/04/2018
ms.topic: overview
ms.custom: mvc
ms.service: iot-hub
services: iot-hub
manager: briz
ms.openlocfilehash: f4254cd90d8cf3b9f4cd206b729a3d44784b377a
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343627"
---
# <a name="what-is-azure-iot-hub"></a>Mi az Azure IoT Hub?

Az IoT Hub egy olyan felügyelt szolgáltatás, amely a felhőn fut, és amely az IoT-alkalmazás és az általa felügyelt eszközök közötti kétirányú kommunikáció üzenetközpontjaként működik. Az Azure IoT Hub segítségével megbízható és biztonságos kommunikációt használó IoT-megoldásokat készíthet több millió IoT-eszköz és egy felhőalapú háttérmegoldás között. Gyakorlatilag bármilyen eszközt csatlakoztathat az IoT Hubhoz.

Az IoT Hub az eszközről a felhőbe és a felhőről az eszközre irányuló kommunikációt is támogatja. Az IoT Hub több üzenetkezelési mintát támogat, például az eszközről a felhőbe irányuló telemetriát, a fájlfeltöltést az eszközökről, valamint a kérés-válasz módszereket, amelyekkel a felhőről vezérelhetők az eszközök. Az IoT Hub monitorozása az események (például az eszköz létrehozása, az eszköz meghibásodásai és az eszközkapcsolatok) követésével segít fenntartani a megoldás épségét.

Az IoT Hub képességei segítenek a skálázható, teljes körű IoT-megoldások felépítésében, amelyek például a gyártásban használt ipari berendezések kezelésére, értékes egészségügyi eszközök követésére és irodaépületek használatának monitorozására szolgálnak.

## <a name="scale-your-solution"></a>A megoldás méretezése

Az IoT Hub több millió egyszerre csatlakoztatott eszközre és másodpercenként több millió eseményre skálázható az IoT számítási feladatok támogatása érdekében. Az IoT Hub számos szintet nyújt a skálázási igények legjobb kielégítése érdekében. További részletekért tekintse át a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="secure-your-communications"></a>Biztonságos kommunikáció

Az IoT Hub biztonságos kommunikációs csatornát nyújt az eszközöknek az adatküldéshez.

* Az eszközönkénti hitelesítés lehetővé teszi az összes eszköz biztonságos kezelését és az IoT Hubhoz való biztonságos kapcsolódását.

* Teljeskörűen irányíthatja az eszközök hozzáférését, és eszközönként szabályozhatja a kapcsolatokat.

* Az [IoT Hub Device Provisioning szolgáltatás](https://docs.microsoft.com/azure/iot-dps/) automatikusan a megfelelő IoT Hubhoz irányítja az eszközöket, amikor először indulnak el.

* Több hitelesítéstípus különböző eszközképességeket támogat:

  * SAS-jogkivonat alapú hitelesítés az IoT megoldás használatának gyors megkezdéséhez.

  * Egyéni X.509-tanúsítványhitelesítés a biztonságos, szabványokon alapuló hitelesítés érdekében.

  * X.509-hitelesítésszolgáltatói hitelesítés az egyszerű, szabványalapú regisztráció érdekében.

## <a name="route-device-data"></a>Eszközadatok útválasztása

A beépített üzenet-útválasztási funkció rugalmasságot biztosít az automatikus, szabályalapú üzenetelosztás beállításához:

* Az üzenet-átirányítással vezérelheti, hová küldje a központ az eszköz telemetriai adatait.

* Az üzenetek több végpontra való irányítása nem jár többletköltségekkel.

* Az egyéni üzenetátadói kód helyett a rendszer kód nélküli útválasztási szabályokat használ.

## <a name="integrate-with-other-services"></a>Integráció más szolgáltatásokkal

Az IoT Hub integrálható más Azure-szolgáltatásokkal teljes, végpontok közötti megoldások felépítése érdekében. Például használhatja a következőket:

* Az [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) lehetővé teszi, hogy gyorsan, megbízhatóan, biztonságosan és skálázható módon reagáljon a kritikus eseményekre.

* Az [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) az üzleti folyamatok automatizálására használható.

* Az [Azure Machine Learninggel](https://docs.microsoft.com/azure/machine-learning/) gépi tanulást és mesterségesintelligencia-modelleket adhat a megoldáshoz.

* Az [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) használatával valós idejű elemző számításokat futtathat az adatstreameken az eszközökről.

## <a name="configure-and-control-your-devices"></a>Az eszközök konfigurálása és vezérlése

Az IoT Hubhoz csatlakoztatott eszközöket különböző beépített funkciókkal kezelheti.

* Tárolhatja, szinkronizálhatja és lekérdezheti a minden eszköz metaadatait és állapotinformációit.

* Az eszközök állapotát eszközönként vagy az eszközök közös jellemzői alapján állíthatja be.

* Automatikusan válaszolhat az eszközök által jelentett állapotváltozásokra az üzenet-útválasztás integrációjával.

## <a name="make-your-solution-highly-available"></a>Biztosíthatja a megoldás magas rendelkezésre állását

Az [IoT Hubhoz](https://azure.microsoft.com/support/legal/sla/iot-hub/) 99,9%-os szolgáltatói szerződés érhető el. Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/)-ban található.

## <a name="connect-your-devices"></a>Az eszközök csatlakoztatása

Az [Azure IoT eszközoldali SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks)-kódtárakkal olyan alkalmazásokat készíthet, amelyek az eszközökön futnak, és az IoT Hubbal kommunikálnak. A támogatott platformok közé tartozik több Linux-disztribúció, Windows és valós idejű operációs rendszer. A támogatott nyelvek közé tartoznak a következők:

* C#
* C#
* Java
* Python
* Node.js.

Az IoT Hub és az eszközoldali SDK-k a következő protokollokat támogatják az eszközök csatlakoztatásához:

* HTTPS
* AMQP
* AMQP WebSocketen keresztül
* MQTT
* MQTT WebSocketen keresztül

Ha a megoldása nem tudja használni az eszközkódtárakat, az eszközök natív módon csatlakozhatnak a központhoz az MQTT v3.1.1, HTTPS 1.1 vagy AMQP 1.0 protokollokkal.

Ha a megoldás nem tudja használni egyik támogatott protokollt sem, kibővítheti az IoT Hubot az egyéni protokollok támogatásához:

* Az [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) használatával hozzon létre egy helyi átjárót a peremhálózaton végzett protokollfordításhoz.

* Szabja testre az [Azure IoT-protokollátjárót](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md), hogy protokollfordítást hajtson végre a felhőben.

## <a name="quotas-and-limits"></a>Kvóták és korlátok

Minden Azure-előfizetésre alapértelmezett kvótakorlátozások vonatkoznak, amelyek meggátolják a szolgáltatással való visszaélést, és ezek a korlátozások hatással lehetnek az IoT-megoldás hatókörére. A jelenlegi határérték előfizetésenként 10 IoT Hub. A támogatási részlegtől kérheti a kvóta növelését. További információ a kvótakorlátozásokról:

* [Az Azure-előfizetés szolgáltatásokra vonatkozó korlátozásai](../azure-subscription-service-limits.md)

* [Az IoT Hub szabályozása és Ön](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## <a name="next-steps"></a>További lépések

A végpontok közötti IoT-megoldás kipróbálásához tekintse meg az IoT Hub rövid útmutatóit:

* [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-node.md)