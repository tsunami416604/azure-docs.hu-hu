---
title: Az Azure IoT Hub Device Provisioning Service szolgáltatással kapcsolatos fogalmak | Microsoft Docs
description: Az eszközök kiépítési szolgáltatásával (DPS) és IoT Hubekkel kapcsolatos szolgáltatások kiépítési fogalmait ismerteti.
author: nberdy
ms.author: nberdy
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f42502ac4db12a060af5906243d3f8e7584c5df3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378898"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>IoT Hub Device Provisioning Service fogalmak

A IoT Hub Device Provisioning Service egy olyan IoT Hub segítő szolgáltatás, amellyel a nulla érintéses eszköz kiépíthető a megadott IoT hubhoz. A Device kiépítési szolgáltatással akár több millió eszközt is biztonságosan és méretezhető módon lehet [automatikusan kiépíteni](concepts-auto-provisioning.md) .

Az eszközök kiépítési folyamata két részből áll. Az első rész a kezdeti kapcsolatot hozza létre az eszköz és a IoT megoldás között az eszköz *regisztrálásával* . A második rész a megoldás konkrét követelményei alapján alkalmazza a megfelelő *konfigurációt* az eszközre. Ha mindkét lépést végrehajtotta, az eszköz teljes mértékben *kiépítve*lett. A Device Provisioning Service a két lépés automatizálásával biztosítja az eszközök zökkenőmentes kiépítését.

Ez a cikk áttekintést nyújt a *szolgáltatás*kezeléséhez leginkább alkalmazandó kiépítési fogalmakról. Ez a cikk a [felhő telepítési lépésében](about-iot-dps.md#cloud-setup-step) érintett personák szempontjából a legfontosabb, hogy az eszköz készen álljon az üzembe helyezésre.

## <a name="service-operations-endpoint"></a>Szolgáltatási műveletek végpontja

A szolgáltatási műveletek végpontja a szolgáltatás beállításainak kezeléséhez és a beléptetési lista karbantartásához használt végpont. Ezt a végpontot csak a szolgáltatás rendszergazdája használja; az eszközök nem használják.

## <a name="device-provisioning-endpoint"></a>Eszköz kiépítési végpontja

Az eszköz kiépítési végpontja az egyetlen végpont, amelyet minden eszköz használ az automatikus kiépítés során. Az URL-cím megegyezik az összes kiépítési szolgáltatási példány esetében, így nincs szükség az eszközök új kapcsolódási információkkal való újratöltésére az ellátási lánc forgatókönyvei között. Az azonosító hatóköre biztosítja a bérlő elkülönítését.

## <a name="linked-iot-hubs"></a>Csatolt IoT hubok

A Device kiépítési szolgáltatás csak olyan IoT-hubok számára tud eszközöket kiépíteni, amelyek hozzá lettek kapcsolva. Az IoT hub és az eszköz kiépítési szolgáltatásának egy példányához való csatolásával a szolgáltatás olvasási/írási jogosultságot biztosít az IoT hub eszközének beállításjegyzékéhez; a hivatkozással egy eszköz kiépítési szolgáltatása regisztrálhat egy eszköz-azonosítót, és beállíthatja a kezdeti konfigurációt az eszköz Twin-ben. A csatolt IoT hubok bármelyik Azure-régióban lehetnek. A kiépítési szolgáltatáshoz más előfizetésekben lévő hubokat is csatolhat.

## <a name="allocation-policy"></a>Kiosztási szabályzat

A szolgáltatás szintjének beállítása, amely meghatározza, hogy az eszköz kiépítési szolgáltatása hogyan rendeljen eszközöket az IoT hubhoz. Három támogatott kiosztási szabályzat létezik:

* **Egyenletesen súlyozott eloszlás**: a csatolt IoT hubok ugyanilyen valószínűséggel rendelkeznek a számukra kiépített eszközökkel. Az alapértelmezett beállítás. Akkor érdemes megtartani, ha csak egy IoT-központban épít ki eszközöket.

* **Legalacsonyabb késés**: az eszközök egy IoT hubhoz vannak kiépítve, az eszköz legalacsonyabb késéssel. Ha több összekapcsolt IoT-hubok ugyanazt a legalacsonyabb késést nyújtják, a kiépítési szolgáltatás az adott hubokban lévő eszközöket

* **Statikus konfiguráció a beléptetési listán**: a beléptetési listán a kívánt IoT hub specifikációja elsőbbséget élvez a szolgáltatási szintű kiosztási szabályzattal szemben.

## <a name="enrollment"></a>Regisztráció

A regisztráció az automatikus kiépítés útján regisztrálhatók eszközeinek vagy csoportjainak a nyilvántartása. A beléptetési rekord információt tartalmaz az eszközről vagy az eszközök csoportjából, beleértve a következőket:
- az eszköz által használt [igazolási mechanizmus](concepts-security.md#attestation-mechanism)
- a választható kezdeti kívánt konfiguráció
- desired IoT hub
- a kívánt eszköz azonosítója

A Device kiépítési szolgáltatás két típusú regisztrációt támogat:

### <a name="enrollment-group"></a>Beléptetési csoport

A regisztrációs csoport olyan eszközök csoportja, amelyek osztoznak egy adott igazolási mechanizmuson. A regisztrációs csoportok az X. 509 és a szimmetrikus is támogatást támogatják. Az X. 509 beléptetési csoportban lévő összes eszköz X. 509 tanúsítványokat tartalmaz, amelyeket ugyanahhoz a legfelső szintű vagy köztes hitelesítésszolgáltató (CA) írta alá. A szimmetrikus kulcs beléptetése csoportban lévő minden eszköz a csoport szimmetrikus kulcsból származtatott SAS-jogkivonatokat mutat be. A regisztrációs csoport neve és a tanúsítvány neve csak alfanumerikus, kisbetűs és kötőjeleket tartalmazhat.

> [!TIP]
> Azt javasoljuk, hogy nagy számú, a kívánt kezdeti konfigurációt megosztó eszközhöz használjon beléptetési csoportot, vagy ha az összes eszköz ugyanahhoz a bérlőhöz fog csatlakozni.

### <a name="individual-enrollment"></a>Egyéni regisztráció

Az egyéni regisztráció egy olyan bejegyzés, amely regisztrálható egyetlen eszközön. Az egyéni regisztrációk X. 509 levél-tanúsítványokat vagy SAS-jogkivonatokat (fizikai vagy virtuális TPM-ből) is használhatnak igazolási mechanizmusként. Az egyéni regisztrációban szereplő regisztrációs azonosító alfanumerikus, kisbetűs, és tartalmazhat kötőjeleket. Előfordulhat, hogy az egyéni regisztrációkhoz meg van határozva a kívánt IoT Hub-eszközazonosító.

> [!TIP]
> Ajánlott egyéni regisztrációkat használni olyan eszközökhöz, amelyek egyedi kezdeti konfigurációt igényelnek, vagy olyan eszközökön, amelyek csak SAS-tokenekkel tudnak hitelesíteni a TPM-igazolás segítségével.

## <a name="registration"></a>Regisztráció

A regisztráció egy eszköz rekordja, amely sikeresen regisztrálja/kiépít egy IoT Hub az eszköz kiépítési szolgáltatásán keresztül. A regisztrációs rekordok automatikusan létrejönnek; törölhetik őket, de nem frissíthetők.

## <a name="operations"></a>Műveletek

A műveletek az eszköz kiépítési szolgáltatásának számlázási egységei. Egy művelet egy utasítás sikeres befejezése a szolgáltatás számára. A műveletek közé tartozik az eszközök regisztrációja és az újbóli regisztráció; a műveletek olyan szolgáltatás-oldali módosításokat is tartalmaznak, mint például a regisztrációs lista bejegyzéseinek hozzáadása és a regisztrációs lista bejegyzéseinek frissítése.
