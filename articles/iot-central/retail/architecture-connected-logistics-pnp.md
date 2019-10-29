---
title: Architektúra IoT kapcsolódó logisztika | Microsoft Docs
description: A IoT Central IoT csatlakoztatott logisztikai alkalmazási sablonjának architektúrája
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: e462d879ea1c7756021cd3a241920fb0028a2e4f
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026401"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>IoT Central csatlakoztatott logisztikai alkalmazásspecifikus sablon architektúrája

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

A partnerek & ügyfél használhatja az alkalmazás sablonját & az alábbi útmutatást követve kifejlesztheti a végpontok közötti **csatlakoztatott logisztikai megoldásokat**.

> [!div class="mx-imgBorder"]
> ![csatlakoztatott logisztikai irányítópult](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. IoT-címkék készlete, amely telemetria adatokat küld egy átjáró eszközének
2. Telemetria küldő és összesített bepillantást IoT Central
3. Az adattovábbítás a kívánt Azure-szolgáltatásba történik a manipulációhoz
4. Az olyan Azure-szolgáltatások, mint például az ASA vagy a Azure Functions kihasználhatják az adatfolyamok újraformázását és a kívánt Storage-fiókokba való elküldését 
5. A különböző üzleti munkafolyamatok a végfelhasználói üzleti alkalmazások által is megadhatók

## <a name="details"></a>Részletek
A következő szakasz ismerteti a koncepcionális architektúra telemetria betöltésének egyes részeit a IoT-címkékből &-átjárók

## <a name="iot-tags"></a>IoT Címkék
A IoT-címkék fizikai, környezeti és környezeti érzékelő képességeket biztosítanak, például a hőmérsékletet, a Páratartalomot, a sokkot, a dőlésszög & a fényt. A IoT-címkék általában az ZigBee (802.15.4) keresztül csatlakoznak az átjáró eszközéhez. A címkék olcsó érzékelők; ezért egy tipikus logisztikai út végén el lehet őket dobni, hogy elkerülje a fordított logisztikai kihívásokat.

## <a name="gateway"></a>Átjáró
Az átjárók IoT címkével is működhetnek a környezeti érzékelési képességekkel. Az átjáró lehetővé teszi, hogy az Azure IoT Cloud connectivity (MQTT) mobil-és Wi-Fi-csatornákon keresztül is elérhető legyen.  A Bluetooth, az NFC és a 802.15.4 Wireless Sensor Network (WSN) mód a IoT-címkékkel folytatott alsóbb szintű kommunikációhoz használatos. Az átjárók biztosítják a végpontok közötti biztonságos felhőalapú kapcsolatot, a IoT címkézését, az érzékelők adatösszesítését, az adatmegőrzést és a riasztási küszöbértékek konfigurálásának lehetőségét.

## <a name="device-management-with-iot-central"></a>Eszközkezelés IoT Central 
Az Azure IoT Central egy megoldás-fejlesztői platform, amely leegyszerűsíti a IoT, a konfigurációt és a felügyeletet. A platform jelentősen csökkenti az IoT, a műveletek és a kapcsolódó fejlesztések terheit és költségeit. Az ügyfelek & partnerekkel teljes körű vállalati megoldásokat hozhatnak létre, amelyekkel digitális visszajelzési hurok valósítható meg a logisztikában.

## <a name="business-insights--actions-via-data-egress"></a>Üzleti elemzések & műveletek a kimenő adatforgalomon keresztül 
A IoT Central platform a folyamatos adatexportálás (CDE) és az API-k révén gazdag bővíthetőségi lehetőségeket biztosít. A telemetria adatfeldolgozási vagy nyers telemetria alapuló üzleti elemzések általában egy előnyben részesített üzletági alkalmazásba exportálhatók. Ez a webhook, a Service Bus, az Event hub vagy a blob Storage szolgáltatáson keresztül érhető el, amely a gépi tanulási modellek fejlesztéséhez, betanításához és üzembe helyezéséhez & további ismereteket biztosít.

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan telepítheti a [csatlakoztatott logisztikai megoldás sablonját](./tutorial-iot-central-connected-logistics-pnp.md)
* További információ a [IoT Central kiskereskedelmi sablonokról](./overview-iot-central-retail-pnp.md)
* További információ a IoT Centralról [IoT Central áttekintés](../core/overview-iot-central-pnp.md)
