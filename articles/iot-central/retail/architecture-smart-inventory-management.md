---
title: Architektúra IoT Smart Inventory Management | Microsoft dokumentumok
description: Az IoT Smart Inventory Management sablon architektúrája az IoT Central számára
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 9d52b2fa9289da709449a5f1edc527239800dfa1
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000643"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>Az IoT Central intelligens készletkezelési alkalmazássablonarchitektúrája

A partnerek és az ügyfelek használhatják az alkalmazássablont, és útmutatást kaphatnak az **intelligens készletkezelési** megoldások kifejlesztéséhez.

> [!div class="mx-imgBorder"]
> ![intelligens készletkezelés](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png)

1. Telemetriai adatokat küldő IoT-érzékelők készlete egy átjáróeszközre
2. Telemetriai adatokat és összesített elemzéseket küldő átjáróeszközök az IoT Centralnak
3. Az adatok a kívánt Azure-szolgáltatáshoz irányítva lesznek a manipuláció hoz
4. Az Azure-szolgáltatások, például az ASA vagy az Azure Functions az adatfolyamok újraformázására és a kívánt tárfiókokra való elküldésre használhatók 
5. A feldolgozott adatokat a közel valós idejű műveletek vagy a hűtőtárolás érdekében a rendszer gyorstárolással tárolja, és további, a gép- vagy kötegelemzésen alapuló betekintési fejlesztéseket biztosít. 
6. A Logic Apps különböző üzleti munkafolyamatok működtetésére használható végfelhasználói üzleti alkalmazásokban

## <a name="details"></a>Részletek
A következő szakasz a fogalmi architektúra minden részét ismerteti Telemetriai betöltés a rádiófrekvenciás azonosítás (RFID), a Bluetooth alacsony energiafelhasználású (BLE) címkékből

## <a name="rfid-tags"></a>RFID-címkék
Az RFID-címkék rádióhullámokon keresztül továbbítják egy elem adatait. Az RFID-címkék általában csak akkor rendelkeznek akkumulátorral, ha meg van adva. A címkék energiát kapnak az olvasó által generált rádióhullámokból, és jelet továbbítanak az RFID-olvasó felé.

## <a name="ble-tags"></a>BLE címkék
Az energiabeacon rendszeres időközönként adatcsomagokat sugároz. A sugáradatot a BLE-olvasók vagy a telepített szolgáltatások okostelefonon észlelik, majd továbbítják a felhőbe.

## <a name="rfid--ble-readers"></a>RFID & BLE olvasók
Az RFID-olvasó a rádióhullámokat egy használhatóbb adathalmazsá alakítja. A címkékből gyűjtött adatokezután a helyi peremhálózati kiszolgálón tárolódnak, vagy a JSON-RPC 2.0-s mazmon keresztüli felhőbe kerül.
A BLE-olvasó, más néven hozzáférési pontok (AP) hasonlóak az RFID-olvasóhoz. Arra szolgál, hogy észlelje a közeli Bluetooth-jeleket, és továbbítsa az üzenetet a helyi Azure IoT Edge vagy felhő json-RPC 2.0 mqtt-n keresztül.
Sok olvasó képes az RFID és a jeladó jelek olvasására, valamint a hőmérséklethez, a páratartalomhoz, a gyorsulásmérőhöz és a giroszkóphoz kapcsolódó további érzékelőképesség biztosítására.

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge átjáró
Az Azure IoT Edge-kiszolgáló egy helyet biztosít az adatok helyi előzetes feldolgozásához, mielőtt a felhőbe küldené őket. Felhőalapú számítási feladatok at is üzembe helyezhetünk mesterséges intelligenciával, Az Azure-ral és harmadik féltől származó szolgáltatásokkal, üzleti logikával szabványos tárolók használatával.

## <a name="device-management-with-iot-central"></a>Eszközkezelés az IoT Central segítségével 
Az Azure IoT Central egy megoldásfejlesztési platform, amely leegyszerűsíti az IoT-eszközök kapcsolatát, konfigurációját és felügyeletét. A platform jelentősen csökkenti az IoT-eszközök kezelésének, műveleteinek és a kapcsolódó fejlesztéseknek a terheit és költségeit. Az ügyfelek & partnerek teljes körű vállalati megoldásokat hozhatnak létre a digitális visszacsatolási hurok eléréséhez a készletkezelésben.

## <a name="business-insights--actions-using-data-egress"></a>Üzleti elemzések & műveletek adatforgalom használatával 
Az IoT Central platform gazdag bővíthetőségi lehetőségeket biztosít a folyamatos adatexportálás (CDE) és api-k révén. Üzleti elemzések alapján telemetriai adatfeldolgozás vagy a nyers telemetriai adatok általában exportált egy előnyben részesített üzletági alkalmazás. Webhook, service bus, event hub vagy blob storage használatával érhető el a gépi tanulási modellek létrehozásához, betanításához és üzembe helyezéséhez, & tovább gazdagíthatja az elemzéseket.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan helyezhető üzembe [intelligens készletkezelési sablon](./tutorial-iot-central-smart-inventory-management.md)
* További információ az [IoT Central kiskereskedelmi sablonjairól](./overview-iot-central-retail.md)
* Az IoT Centralról további információ az [IoT Central áttekintése című témakörben található.](../core/overview-iot-central.md)
