---
title: Architektúra IoT Connected logisztika | Microsoft dokumentumok
description: IoT Connected Logistics alkalmazássablon architektúrája az IoT Central számára
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: 8925b98269b67bfb8a96cb057982ee4e396f17ed
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686229"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>Az IoT Central hoz csatlakoztatott logisztikai alkalmazássablon architektúrája



A partnerek & az ügyfél az alkalmazássablont & alábbi útmutatást használhatja a **végpontok között csatlakoztatott logisztikai megoldások**kifejlesztéséhez.

> [!div class="mx-imgBorder"]
> ![csatlakoztatott logisztikai irányítópult](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. Telemetriai adatokat küldő IoT-címkék készlete átjáróeszközre
2. Telemetriai adatokat és összesített elemzéseket küldő átjáróeszközök az IoT Centralnak
3. Az adatok a kívánt Azure-szolgáltatáshoz irányítva lesznek a manipuláció hoz
4. Az Azure-szolgáltatások, például az ASA vagy az Azure Functions az adatfolyamok újraformázására és a kívánt tárfiókokra való elküldésre használhatók 
5. A különböző üzleti munkafolyamatokat végfelhasználói üzleti alkalmazások táplálhatják

## <a name="details"></a>Részletek
A következő szakasz ismerteti az iotrikátorcímkék & átjárók telemetriai betöltésének egyes részét.

## <a name="iot-tags"></a>IoT-címkék
Az IoT-címkék olyan fizikai, környezeti és környezeti érzékelői képességeket biztosítanak, mint a hőmérséklet, a páratartalom, a sokk, a döntés &a fény. Az IoT-címkék általában a Zigbee-n keresztül csatlakoznak az átjáróeszközhöz (802.15.4). Címkék olcsóbb érzékelők; így egy tipikus logisztikai út végén eldobhatók, hogy elkerüljék a fordított logisztikával való kihívásokat.

## <a name="gateway"></a>Átjáró
Az átjárók IoT-címkékként is működhetnek a környezeti érzékelési képességeikkel. Az átjáró lehetővé teszi a felsőbb szintű Azure IoT felhőalapú kapcsolatot (MQTT) mobil, Wi-Fi csatornák használatával.  A Bluetooth, NFC és 802.15.4 vezeték nélküli érzékelőhálózat (WSN) üzemmódok az IoT-címkékkel való későbbi kommunikációhoz használatosak. Az átjárók végpontok közötti biztonságos felhőkapcsolatot, IoT-címkepárosítást, érzékelőadatok összesítését, adatmegőrzést és riasztási küszöbértékek konfigurálását biztosítják.

## <a name="device-management-with-iot-central"></a>Eszközkezelés az IoT Central segítségével 
Az Azure IoT Central egy megoldásfejlesztési platform, amely leegyszerűsíti az IoT-eszközök kapcsolatát, konfigurációját és felügyeletét. A platform jelentősen csökkenti az IoT-eszközök kezelésének, műveleteinek és a kapcsolódó fejlesztéseknek a terheit és költségeit. Az ügyfelek & partnerek teljes körű vállalati megoldásokat hozhatnak létre a logisztika digitális visszacsatolási hurokjának elérése érdekében.

## <a name="business-insights-and-actions-using-data-egress"></a>Üzleti elemzések és műveletek adatforgalom használatával 
Az IoT Central platform gazdag bővíthetőségi lehetőségeket biztosít a folyamatos adatexportálás (CDE) és api-k révén. Üzleti elemzések alapján telemetriai adatfeldolgozás vagy a nyers telemetriai adatok általában exportált egy előnyben részesített üzletági alkalmazás. Webhook, service bus, event hub vagy blob storage használatával érhető el a gépi tanulási modellek létrehozásához, betanításához és üzembe helyezéséhez, & tovább gazdagíthatja az elemzéseket.

## <a name="next-steps"></a>További lépések
* A csatlakoztatott [logisztikai megoldássablon](./tutorial-iot-central-connected-logistics.md) üzembe helyezése
* További információ az [IoT Central kiskereskedelmi sablonjairól](./overview-iot-central-retail.md)
* Az IoT Centralról további információ az [IoT Central áttekintése című témakörben található.](../core/overview-iot-central.md)
