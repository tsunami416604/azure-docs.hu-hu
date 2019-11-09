---
title: Architektúra IoT – intelligens leltár kezelése | Microsoft Docs
description: A IoT intelligens leltározási felügyeleti sablonjának architektúrája IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 6450169ae2b2d74006eedc66f35338494257594a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889085"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>A IoT Central Smart Inventory Management alkalmazás-sablon architektúrája

A partnerek & ügyfél használhatja az alkalmazás sablonját, & az alábbi útmutatást követve fejlesztheti a végpontok közötti **intelligens leltár-kezelési** megoldásokat.

> [!div class="mx-imgBorder"]
> intelligens leltár-felügyeleti](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png) ![

1. IoT-érzékelők készlete telemetria-adatokat küld egy átjáró-eszközre
2. Telemetria küldő és összesített bepillantást IoT Central
3. Az adattovábbítás a kívánt Azure-szolgáltatásba történik a manipulációhoz
4. Az olyan Azure-szolgáltatások, mint például az ASA vagy a Azure Functions kihasználhatják az adatfolyamok újraformázását és a kívánt Storage-fiókokba való elküldését 
5. A feldolgozott adatok tárolása a közel valós idejű műveletekhez vagy a hideg tároláshoz szükséges, és a további elemzési fejlesztések a ML-vagy a Batch-elemzés alapján történik. 
6. A Logic Apps a végfelhasználói üzleti alkalmazások különböző üzleti munkafolyamatainak kikapcsolására is használható

## <a name="details"></a>Részletek
A következő szakasz ismerteti a koncepcionális architektúra telemetria betöltését a rádiófrekvenciás azonosítás (RFID), a Bluetooth alacsony energiafogyasztású Címkék

## <a name="rfid-tags"></a>RFID-Címkék
Az RFID-címkék rádióhullámokon keresztül továbbítják az adott elemmel kapcsolatos adatokat. Az RFID-címkék általában nem rendelkeznek akkumulátorral, kivéve, ha meg van adva. A címkék energiát kapnak az olvasó által generált rádióhullámokból, és visszaküldik a jeleket az RFID-olvasó felé.

## <a name="ble-tags"></a>A Címkék
Az Energy Beacon rendszeres időközönként közvetíti az adatcsomagokat. A Beacon-alapú adattárakat az intelligens olvasók vagy a telepített szolgáltatások észlelik, majd továbbítják a felhőbe.

## <a name="rfid--ble-readers"></a>RFID-& rendszerolvasók
Az RFID-olvasó a rádióhullámokat egy jobban használható adatformátumba alakítja át. A címkékből gyűjtött információk ezután a helyi peremhálózati kiszolgálón tárolódnak, vagy a MQTT-n keresztül JSON-RPC 2,0-en keresztül érkeznek a felhőbe.
Az egyhelyes olvasó más néven hozzáférési pontok (AP) hasonlóak az RFID-olvasóhoz. Ezek a közeli Bluetooth-jelek észlelésére és az üzenet helyi Azure IoT Edge vagy felhőbe való továbbítására szolgálnak JSON-RPC 2,0-en keresztül, MQTT keresztül.
Számos olvasó képes az RFID & Beacon-jelek olvasására, valamint a mérsékelt, páratartalom, gyorsulásmérő & Giroszkóphoz kapcsolódó további érzékelő képesség biztosítására.

## <a name="azure-iot-edge-gateway"></a>Átjáró Azure IoT Edge
Azure IoT Edge kiszolgáló lehetővé teszi az adatok helyi előfeldolgozását, mielőtt elküldené a felhőbe. A Felhőbeli számítási feladatokat mesterséges intelligencia, az Azure és a külső szolgáltatások, az üzleti logika standard szintű tárolón keresztül is üzembe helyezheti.

## <a name="device-management-with-iot-central"></a>Eszközkezelés IoT Central 
Az Azure IoT Central egy megoldás-fejlesztői platform, amely leegyszerűsíti a IoT, a konfigurációt és a felügyeletet. A platform jelentősen csökkenti az IoT, a műveletek és a kapcsolódó fejlesztések terheit és költségeit. Az ügyfelek & partnerekkel teljes körű vállalati megoldásokat hozhatnak létre, amelyekkel digitális visszajelzési hurkot érhet el a készletkezelésben.

## <a name="business-insights--actions-via-data-egress"></a>Üzleti elemzések & műveletek a kimenő adatforgalomon keresztül 
A IoT Central platform a folyamatos adatexportálás (CDE) és az API-k révén gazdag bővíthetőségi lehetőségeket biztosít. A telemetria adatfeldolgozási vagy nyers telemetria alapuló üzleti elemzések általában egy előnyben részesített üzletági alkalmazásba exportálhatók. Ez a webhook, a Service Bus, az Event hub vagy a blob Storage szolgáltatáson keresztül érhető el, amely a gépi tanulási modellek fejlesztéséhez, betanításához és üzembe helyezéséhez & további ismereteket biztosít.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan telepítheti az [intelligens leltár felügyeleti sablonját](./tutorial-iot-central-smart-inventory-management-pnp.md)
* További információ a [IoT Central kiskereskedelmi sablonokról](./overview-iot-central-retail-pnp.md)
* További információ a IoT Centralról [IoT Central áttekintés](../preview/overview-iot-central.md)
