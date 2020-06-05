---
title: Az Azure IoT Central eszközének fejlesztése | Microsoft Docs
description: Az Azure IoT Central egy IoT-alkalmazásplatform, amely megkönnyíti IoT-megoldások létrehozását. Ez a cikk áttekintést nyújt a IoT Central-alkalmazáshoz való kapcsolódáshoz szükséges eszközök fejlesztéséről.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: aa442e15dbc95709ecf3c818f69301d2f02e9b5b
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417021"
---
# <a name="iot-central-device-development-overview"></a>IoT Central az eszközök fejlesztésének áttekintése

*Ez a cikk az eszközök fejlesztőire vonatkozik.*

Egy IoT Central alkalmazás lehetővé teszi több millió eszköz figyelését és kezelését életciklusuk során. Ez az Áttekintés olyan eszközök fejlesztői számára készült, akik a IoT Centralhoz csatlakozó eszközökön futtatnak programkódot.

Az eszközök az alábbi primitívek használatával kommunikálnak IoT Central alkalmazásokkal:

- A _telemetria_ olyan adatokat mutatnak be, amelyeket az eszköz a IoT Centralnek küld. Például egy fedélzeti érzékelőből származó hőmérsékleti értékek streamje.
- A _Tulajdonságok_ olyan állapotinformációkat jelentenek, amelyeket az eszköz IoT Central. Például az eszköz aktuális belső vezérlőprogram-verziója. Használhat olyan írható tulajdonságokat is, amelyeket IoT Central frissíthet az eszközön.
- Az eszközök viselkedésének szabályozására a rendszer a IoT Central _parancsokat_ hívja meg. Előfordulhat például, hogy a IoT Central alkalmazás meghív egy parancsot egy eszköz újraindítására.

A megoldás-szerkesztő feladata az irányítópultok és nézetek konfigurálása a IoT Central webes felületen a telemetria, a tulajdonságok kezelése és a hívási parancsok megjelenítéséhez.

## <a name="types-of-device"></a>Az eszköz típusai

A következő szakaszok ismertetik a IoT Central alkalmazáshoz csatlakoztatható eszközök fő típusait:

### <a name="standalone-device"></a>Önálló eszköz

Egy önálló eszköz közvetlenül csatlakozik IoT Centralhoz. Az önálló eszközök általában telemetria küldenek a bevezető vagy csatlakoztatott érzékelőkről a IoT Central alkalmazásba. Az önálló eszközök is jelenthetik a tulajdonságértékek jelentését, az írható tulajdonságértékek fogadását és a parancsok megválaszolását.

### <a name="gateway-device"></a>Átjáró eszköz

Az átjáró-eszköz egy vagy több alsóbb rétegbeli eszközt kezel, amely a IoT Central alkalmazáshoz csatlakozik. A IoT Central az alárendelt eszközök és az átjáró-eszköz közötti kapcsolatok konfigurálására használható. További információért lásd: [új IoT-átjáró eszköz típusának meghatározása az Azure IoT Central alkalmazásban](./tutorial-define-gateway-device-type.md).

### <a name="edge-device"></a>Peremhálózati eszköz

Az Edge-eszköz közvetlenül csatlakozik IoT Centralhoz, de közvetítőként működik más, _levélként_ismert eszközökhöz. Az Edge-eszközök általában közel vannak azon Leaf-eszközökhöz, amelyek közvetítőként működnek. A peremhálózati eszközöket használó forgatókönyvek a következők:

- Olyan eszközök engedélyezése, amelyek nem tudnak közvetlenül kapcsolódni IoT Centralhoz a peremhálózati eszközön való kapcsolódáshoz. Előfordulhat például, hogy egy levél eszköz Bluetooth-kapcsolattal csatlakozik a peremhálózati eszközhöz, majd az interneten keresztül csatlakozik a IoT Centralhoz.
- A IoT Centralbe való elküldése előtt összesített telemetria. Ez a megközelítés segít csökkenteni az adatok IoT Centralba való küldésének költségeit.
- A Leaf-eszközök helyi vezérlése a IoT Central az interneten keresztüli csatlakozáshoz kapcsolódó késés elkerüléséhez.

Az Edge-eszközök a saját telemetria is elküldhetik, bejelenthetik a tulajdonságokat, és reagálnak az írható tulajdonságok frissítéseire és parancsaira.

IoT Central csak a peremhálózati eszközt látja, nem pedig a peremhálózati eszközhöz csatlakozó Leaf-eszközöket.

További információ: Azure IoT Edge- [eszköz hozzáadása az Azure IoT Central alkalmazáshoz](./tutorial-add-edge-as-leaf-device.md).

## <a name="connect-a-device"></a>Eszköz csatlakoztatása

Az Azure IoT Central az [azure IoT hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) használatával kezeli az összes eszköz regisztrációját és kapcsolódását.

A DPS használata lehetővé teszi a következőket:

- IoT Central az eszközök nagy léptékű bevezetésének és csatlakoztatásának támogatásához.
- Az eszköz hitelesítő adatainak előállításához és az eszközök offline konfigurálásához az eszközök IoT Central felhasználói felületen keresztül történő regisztrálása nélkül.
- A saját eszközök azonosítóinak használatával regisztrálhatja az eszközöket a IoT Centralban. A saját eszköz-azonosítók használatával egyszerűbbé válik a meglévő Back-Office rendszerekkel való integráció.
- Egyetlen, egységes módszer az eszközök IoT Centralhoz való csatlakoztatására.

További információért lásd: [Csatlakozás az Azure IoT Centralhoz](./concepts-get-connected.md).

### <a name="security"></a>Biztonság

Az eszköz és a IoT Central alkalmazás közötti kapcsolat a [megosztott hozzáférési aláírások](./concepts-get-connected.md#connect-devices-at-scale-using-sas) vagy az iparági szabványnak megfelelő [X. 509 tanúsítványok](./concepts-get-connected.md#connect-devices-using-x509-certificates)használatával biztosítható.

### <a name="communication-protocols"></a>Kommunikációs protokollok

Azok a kommunikációs protokollok, amelyeket az eszköz használhat a IoT Centralhoz való kapcsolódáshoz, beleértve a MQTT, a AMQP és a HTTPS-t. Belsőleg a IoT Central egy IoT hubot használ az eszközök kapcsolatának engedélyezéséhez. A IoT Hub által támogatott kommunikációs protokollokról további információt a [kommunikációs protokoll kiválasztása](../../iot-hub/iot-hub-devguide-protocols.md)című témakörben talál.

## <a name="implement-the-device"></a>Az eszköz implementálása

Az eszköz működésének megvalósításához használja az [Azure IoT Device SDK](#languages-and-sdks) -k egyikét. A kódnak a következőket kell tennie:

- Regisztrálja az eszközt a DPS-vel, és használja a DPS-ből származó információkat a IoT Central alkalmazás belső IoT hubhoz való kapcsolódáshoz.
- Küldje el a telemetria a IoT Central az eszköz sablonjának formátumában. A IoT Central az eszköz sablonjának használatával határozza meg, hogyan használja a telemetria a vizualizációk és az elemzések számára.
- Az eszköz és a IoT Central közötti tulajdonságértékek szinkronizálása. Az eszköz sablonja megadja a tulajdonságokat és az adattípusokat, így a IoT Central megjelenítheti az információkat.
- A parancsokhoz tartozó parancssori kezelők implementálása az eszköz sablonjában adható meg. Az eszköz sablonja megadja az eszköz által használandó parancsokat és paramétereket.

További információ az eszközök sablonjainak szerepköréről: Mik az [eszközök sablonjai?](./concepts-device-templates.md).

Néhány mintakód esetében lásd: [Node. js-ügyfélalkalmazás létrehozása és összekötése](./tutorial-connect-device-nodejs.md) , illetve [Python-ügyfélalkalmazás létrehozása és összekapcsolása](./tutorial-connect-device-python.md).

### <a name="languages-and-sdks"></a>Nyelvek és SDK-k

További információ a támogatott nyelvekről és SDK-k használatáról: az [Azure IoT hub Device SDK-k megismerése és használata](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks).

## <a name="next-steps"></a>Következő lépések

Ha Ön egy eszköz fejlesztője, és szeretne belemerülni egy kódra, a javasolt következő lépés egy [ügyfélalkalmazás létrehozása és csatlakoztatása az Azure IoT Central-alkalmazáshoz](./tutorial-connect-device-nodejs.md).

Ha többet szeretne megtudni a IoT Central használatáról, a javasolt következő lépések az [Azure IoT Central-alkalmazás létrehozása](./quick-deploy-iot-central.md)című rövid útmutatók kipróbálása.
