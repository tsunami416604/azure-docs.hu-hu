---
title: Szolgáltatási fogalmak az Azure IoT Hub-eszközkiépítési szolgáltatásban | Microsoft dokumentumok
description: Az eszközkiépítési szolgáltatással (DPS) és az IoT Hubmal rendelkező eszközökre vonatkozó szolgáltatáskiépítési fogalmak ismertetése
author: nberdy
ms.author: nberdy
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f42502ac4db12a060af5906243d3f8e7584c5df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79285213"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>IoT Hub-eszközkiépítési szolgáltatás fogalmai

Az IoT Hub-eszközkiépítési szolgáltatás egy segítő szolgáltatás az IoT Hub, amely segítségével konfigurálja a nulla érintésű eszköz kiépítése egy adott IoT hub. Az eszközkiépítési szolgáltatással [biztonságos](concepts-auto-provisioning.md) és méretezhető módon több millió eszközt biztosíthat automatikusan.

Az eszközkiépítés két részes folyamat. Az első rész az eszköz és az IoT-megoldás közötti kezdeti kapcsolat létrehozása az eszköz *regisztrálásával.* A második rész a megfelelő *konfiguráció* alkalmazása az eszközre a megoldás speciális követelményei alapján. Miután mindkét lépés befejeződött, az eszköz teljesen *ki van építve.* A Device Provisioning Service a két lépés automatizálásával biztosítja az eszközök zökkenőmentes kiépítését.

Ez a cikk áttekintést nyújt a szolgáltatás kezelésére leginkább alkalmazható kiépítési *fogalmakról*. Ez a cikk leginkább a [felhőalapú beállítási lépésben](about-iot-dps.md#cloud-setup-step) részt vevő személyiségek számára fontos, hogy az eszköz készen álljon a telepítésre.

## <a name="service-operations-endpoint"></a>Szolgáltatási műveletek végpontja

A szolgáltatási műveletek végpontja a végpont a szolgáltatásbeállítások kezeléséhez és a regisztrációs lista karbantartásához. Ezt a végpontot csak a szolgáltatás-rendszergazda használja; nem használják az eszközök.

## <a name="device-provisioning-endpoint"></a>Eszközkiépítési végpont

Az eszköz kiépítési végpont jatt s az egyetlen végpont, amelyet az összes eszköz automatikus kiépítéshez használ. Az URL-cím ugyanaz az összes létesítési szolgáltatás példányok, hogy nincs szükség a reflash eszközök új kapcsolat adatait ellátásilánc-forgatókönyvekben. Az azonosító hatókörbiztosítja a bérlők elkülönítését.

## <a name="linked-iot-hubs"></a>Csatolt IoT-központok

Az eszközkiépítési szolgáltatás csak olyan IoT-központokba helyezhet ki eszközöket, amelyek hozzá vannak kapcsolva. Az IoT-központ összekapcsolása az eszközkiépítési szolgáltatás egy példányához olvasási/írási engedélyeket ad a szolgáltatásnak az IoT hub eszközbeállítási beállításjegyzékéhez; a kapcsolat, az eszköz kiépítési szolgáltatás regisztrálhat egy eszközazonosítót, és állítsa be a kezdeti konfigurációt az ikereszközben. A csatolt IoT-központok bármely Azure-régióban lehetnek. Más előfizetésekben lévő hubokat összekapcsolhatja a létesítési szolgáltatással.

## <a name="allocation-policy"></a>Felosztási politika

A szolgáltatásszintű beállítás, amely meghatározza, hogy az Eszközkiépítési szolgáltatás hogyan rendeli hozzá az eszközöket egy IoT hubhoz. Három támogatott kiosztási szabályzat létezik:

* **Egyenletesen súlyozott disztribúció:** összekapcsolt IoT-központok egyaránt valószínű, hogy az eszközök kivannak építve őket. Az alapértelmezett beállítás. Akkor érdemes megtartani, ha csak egy IoT-központban épít ki eszközöket.

* **Legkisebb késés:** az eszközök egy IoT-hubhoz vannak kiépítve, amely nek az eszközhöz a legalacsonyabb a késése. Ha több összekapcsolt IoT-központ ugyanazt a legalacsonyabb késést biztosítaná, a létesítési szolgáltatás kiírja az eszközöket ezeken a központokon keresztül

* **Statikus konfiguráció a regisztrációs listán keresztül:** a regisztrációs listában a kívánt IoT-központ specifikációja elsőbbséget élvez a szolgáltatásszintű foglalási szabályzattal szemben.

## <a name="enrollment"></a>Regisztráció

A regisztráció olyan eszközök vagy eszközcsoportok rekordja, amelyek automatikus kiépítés során regisztrálhatnak. A regisztrációs rekord az eszközre vagy eszközcsoportra vonatkozó információkat tartalmaz, többek között a következőket:
- a készülék által használt [igazolási mechanizmus](concepts-security.md#attestation-mechanism)
- az opcionális kezdeti kívánt konfiguráció
- kívánt IoT hub
- a kívánt eszközazonosító

Az Eszközkiépítési szolgáltatás kétféle regisztrációt támogat:

### <a name="enrollment-group"></a>Beléptetési csoport

A regisztrációs csoport olyan eszközök csoportja, amelyek egy adott tanúsítványmechanizmussal rendelkeznek. A beléptetési csoportok mind az X.509, mind a szimmetrikus csoportokat támogatják. Az X.509 regisztrációs csoport minden olyan eszköze x.509-es tanúsítvánnyal rendelkezik, amelyet ugyanaz a legfelső szintű vagy közbenső hitelesítésszolgáltató (CA) írt alá. A szimmetrikus kulcsregisztrációs csoportminden egyes eszköze a csoportszimmetrikus kulcsból származó SAS-jogkivonatokat mutatja be. A beléptetési csoport nevének és tanúsítványnevének alfanumerikusnak, kisbetűsnek és kötőjeleket tartalmazhat.

> [!TIP]
> Azt javasoljuk, hogy egy regisztrációs csoport nagy számú eszköz, amely a kívánt kezdeti konfigurációt, vagy az eszközök minden megy ugyanahhoz a bérlőhöz.

### <a name="individual-enrollment"></a>Egyéni regisztráció

Az egyéni regisztráció egyetlen eszköz bejegyzése, amely regisztrálhat. Az egyéni beléptetések x.509 levéltanúsítványokat vagy SAS-jogkivonatokat (fizikai vagy virtuális TPM-ből) használhatnak igazolási mechanizmusként. Az egyéni regisztrációregisztrációs azonosító ja, hogy alfanumerikus, kisbetűs, és kötőjeleket tartalmazhat. Előfordulhat, hogy az egyéni regisztrációkhoz meg van határozva a kívánt IoT Hub-eszközazonosító.

> [!TIP]
> Azt javasoljuk, hogy egyéni regisztrációk at eszközök, amelyek egyedi kezdeti konfigurációk, vagy az eszközök, amelyek csak hitelesítési segítségével SAS-jogkivonatok TPM-tanúsítványon keresztül.

## <a name="registration"></a>Regisztráció

A regisztráció az eszköz sikeres regisztrációja/kiépítése az IoT Hub on az eszköz kiépítési szolgáltatáson keresztül. A regisztrációs rekordok automatikusan létrejönnek; törölhetők, de nem frissíthetők.

## <a name="operations"></a>Műveletek

A műveletek az eszközkiépítési szolgáltatás számlázási egységei. Az egyik művelet a szolgáltatás egyik utasításának sikeres befejezése. A műveletek magukban foglalják az eszközregisztrációkat és az újraregisztrációkat; a műveletek közé tartoznak a szolgáltatásoldali módosítások is, például a regisztrációs lista bejegyzéseinek hozzáadása és a regisztrációs lista bejegyzéseinek frissítése.
