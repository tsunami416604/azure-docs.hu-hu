---
title: Szolgáltatás az Azure IoT Hub Device Provisioning Service fogalmak |} A Microsoft Docs
description: Szolgáltatás üzembe helyezési kapcsolatos fogalmakat ismerteti a Device Provisioning Service és az IoT Hub-eszközökre vonatkozó
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 4a4f53f991355e634e8139f9e90bec6c508a527d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792747"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>IoT Hub Device Provisioning Service-fogalmak

IoT Hub Device Provisioning Service, amellyel egy adott IoT hub használatával beavatkozás nélküli eszközök konfigurálása IoT hub segítő szolgáltatása. A Device Provisioning Service szolgáltatással is [automatikus üzembe](concepts-auto-provisioning.md) eszközök, biztonságos és méretezhető módon.

Eszköz kiépítése két részből álló folyamat. Az első rész az eszköz és az IoT-megoldások között a kezdeti kapcsolatot létesít *regisztrálása* az eszközön. A második rész alkalmazza a megfelelő *konfigurációs* az eszközön, a megoldás egyéni követelményei alapján. Ha mindkét lépés befejeződött, az eszköz teljes mértékben van *kiépített*. A Device Provisioning Service a két lépés automatizálásával biztosítja az eszközök zökkenőmentes kiépítését.

Ez a cikk áttekintést nyújt a legjobban megfelelő kezelése kiépítési fogalmakat a *szolgáltatás*. Ez a cikk a megfelelő személyeknek, résztvevő a [felhőbeli telepítési lépés](about-iot-dps.md#cloud-setup-step) szerezhető egy eszköz telepítésre készen áll.

## <a name="service-operations-endpoint"></a>Szolgáltatásvégpont-műveletek

A szolgáltatásvégpont műveletek az a végpont a szolgáltatás beállításainak kezelése és fenntartása a regisztrációs listán. Ez a végpont csak használják; a szolgáltatás-rendszergazda az eszközök nem használatos.

## <a name="device-provisioning-endpoint"></a>Üzembe helyezési eszközvégpont

Az eszköz regisztrációs végpont az az egyetlen végpont az Automatikus kiépítés használata minden eszköz. Az URL-cím megegyezik az összes kiépítési szolgáltatás példány, nem kell az új kapcsolati adatokkal eszközök reflash ellátási lánc forgatókönyvekben. Az azonosító hatókörének biztosítja a bérlők elkülönítését.

## <a name="linked-iot-hubs"></a>Összekapcsolt IoT-központok

A Device Provisioning Service-eszközöket az ahhoz kapcsolt IoT hubra csak helyezhet üzembe. Az IoT hub csatolása a Device Provisioning service egy példányát lehetővé teszi az IoT hub eszközjegyzékében; a szolgáltatás olvasási/írási engedélyekkel a hivatkozást tartalmazó a Device Provisioning service regisztrálni egy Eszközazonosítót, és állítsa be a kezdeti konfigurációt az ikereszköz. Összekapcsolt IoT-központok lehet bármely Azure-régióban. Előfordulhat, hogy más előfizetésekben hubs összekapcsolása a kiépítési szolgáltatáshoz.

## <a name="allocation-policy"></a>Foglalási szabályzat

A szolgáltatásiszint-beállítás, amely meghatározza, hogy hogyan Device Provisioning Service rendeli hozzá eszközöket egy IoT hubra. Három támogatott kiosztási szabályzat létezik:

* **Egyenletesen súlyozott elosztás**: összekapcsolt IoT-központok valószínűleg egyaránt neki biztosított eszközök. Az alapértelmezett beállítás. Akkor érdemes megtartani, ha csak egy IoT-központban épít ki eszközöket.

* **Legkisebb késés**: eszközök kiosztása egy IoT hubra az a legkisebb mértékű késleltetéssel rendelkezik az eszközön. Ha több összekapcsolt IoT-központok ugyanolyan legkisebb késéssel biztosítja, a kiépítési szolgáltatás eszközök kivonatolja ezeket hubs között

* **A regisztrációs listán keresztül statikus konfiguráció**: a regisztrációs listában meghatározott IoT-központ specifikace keresztül a szolgáltatás-szintű kiosztási szabályzat élvez elsőbbséget.

## <a name="enrollment"></a>Regisztráció

Regisztrációnak az Automatikus kiépítés keresztül regisztrálható eszközök csoportját a rekordját. A regisztrációs rekord az eszközről vagy eszközcsoportról, köztük kapcsolatos információkat tartalmazza:
- a [igazolási mechanizmus](concepts-security.md#attestation-mechanism) az eszközök
- a választható kívánt kezdeti konfiguráció
- desired IoT hub
- a kívánt eszköz azonosítója

Kétféle regisztrációtípust támogat: a Device Provisioning Service-hiba:

### <a name="enrollment-group"></a>Regisztrációs csoportot

Egy regisztrációs csoportnak egy igazolási mechanizmussal rendelkező eszközök egy csoportját. A regisztrációs csoport minden eszköze megegyező legfelső szinttel által aláírt X.509-tanúsítványokat vagy köztes hitelesítésszolgáltató (CA) található. Regisztrációs csoportok X.509 igazolási eljárás csak használható. A regisztrációs csoport nevének és a tanúsítvány neve alfanumerikus karakterek, kisbetűs kell lennie, és kötőjeleket tartalmazhat.

> [!TIP]
> Ajánlott regisztrációs csoportot használni kívánt kezdeti konfigurációval rendelkező eszközök nagy számú, vagy eszközök célzó ugyanazt bérlőhöz.

### <a name="individual-enrollment"></a>Egyéni regisztráció

Egyéni regisztrációt regisztrálható egyetlen eszközhöz tartozó bejegyzés. Egyéni regisztrációk X.509 levél-tanúsítványokat vagy SAS-tokeneket (a fizikai vagy virtuális TPM) felhasználhatja igazolási mechanizmusként. A regisztrációs azonosító egyedi regisztrációs alfanumerikus karakterek, kisbetűs, és kötőjeleket tartalmazhat. Előfordulhat, hogy az egyéni regisztrációkhoz meg van határozva a kívánt IoT Hub-eszközazonosító.

> [!TIP]
> Azt javasoljuk, hogy az egyedi kezdeti konfigurációt igénylő eszközök részére, vagy hitelesítheti, hogy csak a TPM-igazolást keresztül SAS-tokeneket használó eszközökhöz egyéni regisztrációk használatát.

## <a name="registration"></a>Regisztráció

A regisztráció az a rekord az eszköz sikeres regisztrációja/való üzembe helyezést az IoT Hub Device Provisioning Service használatával. Regisztrációs rekordok jönnek létre automatikusan; ezek törölhetők, de nem lehet frissíteni.

## <a name="operations"></a>Műveletek

Műveletek a következők a Device Provisioning Service elszámolási egysége. Egy művelet egy utasítást a szolgáltatás sikeres befejezését. Művelet lehet az eszköz regisztrálása és újraregisztrálása, vagy a szolgáltatásoldali változások, például tételek hozzáadása a regisztrálási listákhoz vagy a tételek frissítése a listákban.
