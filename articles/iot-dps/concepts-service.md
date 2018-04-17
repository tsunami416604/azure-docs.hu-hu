---
title: Szolgáltatás az Azure IoT Hub eszköz kiépítése szolgáltatáshoz fogalmak |} Microsoft Docs
description: Jellemző a terjesztési pontok és az IoT-központ szolgáltatás létesítési fogalmakat ismerteti
services: iot-dps
keywords: ''
author: nberdy
ms.author: nberdy
ms.date: 03/30/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: d2bc58514ea716954ec3ac96151549168fedc2ed
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Az IoT Hub eszköz kiépítése szolgáltatással kapcsolatos fogalmak

IoT Hub eszköz kiépítése szolgáltatáshoz olyan IoT hub, amely egy megadott IoT-központ nulla érintéssel eszközkiépítési konfigurálására használt segítő szolgáltatás. Az eszköz kiépítése szolgáltatáshoz is [automatikus-kiépítési](concepts-auto-provisioning.md) biztonságos és skálázható módon eszközök millióira.

Eszköz kiépítése két lépésben. Az első rész hozza létre azt a kezdeti kapcsolatot az eszköz és az IoT-megoldás által *regisztrálása* az eszközt. A második rész alkalmazza a megfelelő *konfigurációs* az eszközre, a megoldás a meghatározott követelmények alapján. Mindkét lépések elvégzése után, az eszköz teljes mértékben lett *kiépített*. A Device Provisioning Service a két lépés automatizálásával biztosítja az eszközök zökkenőmentes kiépítését.

Ez a cikk áttekintést nyújt a kiépítési fogalmakat kezelésének alkalmazható a *szolgáltatás*. Ez a cikk az érintett személyeknek igényeinek jobban megfelelő a [felhő telepítő lépés](about-iot-dps.md#cloud-setup-step) az eszköz telepítési felkészülés.

## <a name="service-operations-endpoint"></a>Szolgáltatásvégpont műveletek

A szolgáltatásvégpont műveletek a szolgáltatás beállításainak kezelése és a beléptetési lista végpontja. Ez a végpont csak használja a szolgáltatás-rendszergazda; eszközök nem használják.

## <a name="device-provisioning-endpoint"></a>Eszköz üzembe helyezési végpont

Az eszköz üzembe helyezési végpont esetében minden eszköz automatikus-történő üzembe helyezéséhez használjon a végpontot. Az URL-cím megegyezik a összes üzembe helyezési szolgáltatáspéldányok, nincs szükség az új kapcsolat információkkal eszközök reflash ellátási lánc forgatókönyvekben. A [azonosító hatókör](#id-scope) biztosítja a bérlők elkülönítését.

## <a name="linked-iot-hubs"></a>Összekapcsolt IoT-központok

Eszköz kiépítése szolgáltatáshoz csak oszthat meg a hozzá csatolt IoT-központok eszközök. Az IoT-központ Linking eszköz kiépítése szolgáltatáshoz biztosít a service olvasási/írási engedéllyel az IoT hub eszköz beállításjegyzékébe; a hivatkozás eszköz kiépítése szolgáltatáshoz regisztrálja az Eszközazonosítót és az eszköz iker a kezdeti beállítása. Csatolt IoT-központok bármely Azure régióban lehet. A létesítési szolgáltatás előfordulhat, hogy más előfizetések központok kapcsolódik.

## <a name="allocation-policy"></a>Elosztási házirend

A szolgáltatási szint beállítása, amely meghatározza, hogy hogyan eszköz kiépítése szolgáltatáshoz az IoT-központ eszközökhöz rendeli. Három támogatott kiosztási szabályzat létezik:
* **Terjesztési egyenletesen súlyozott**: csatolt IoT-központok valószínűleg egyaránt számukra kiosztott eszközök. Az alapértelmezett beállítás. Akkor érdemes megtartani, ha csak egy IoT-központban épít ki eszközöket.
* **Legkisebb mértékű késleltetést**: eszközök az eszközre a legkisebb mértékű késleltetést törlődnek az IoT-központ számára. Ha több csatolt IoT-központok volna a azonos legkisebb mértékű késleltetést biztosít, a létesítési szolgáltatás eszközök csak ezek hubok között
* **A beléptetési listán keresztül statikus konfigurációs**: a regisztráció listán a kívánt IoT-központ megadását prioritást élvez a szolgáltatásiszint-elosztási házirend.

## <a name="enrollment"></a>Igénylés

A beléptetési eszközöket vagy eszközöket, amelyeket automatikus kiépítés keresztül lehet regisztrálni a bejegyzését. A beléptetési rekord az eszközt vagy eszközöket, beleértve a vonatkozó információkat tartalmazza:
- a [állapotigazolási mechanizmus](concepts-security.md#attestation-mechanism) a eszköz által használt
- a nem kötelező kezdeti szükségeskonfiguráció
- kívánt IoT-központ
- a kívánt eszköz azonosítója

Az eszköz kiépítése szolgáltatáshoz által támogatott regisztrációkat két típusa van:

### <a name="enrollment-group"></a>Beléptetési csoport

Egy regisztrációs csoport olyan eszközök, amelyek egy adott igazolási módszert. A beléptetési csoport minden eszköze jelent-e a legfelső szintű vagy köztes hitelesítésszolgáltató által aláírt X.509-tanúsítványokat. Beléptetési csoportok csak használhatja az X.509 tanúsítvány mechanizmus. A beléptetési csoport nevét és a tanúsítvány neve alfanumerikus, kisbetűnek kell lennie, és kötőjeleket tartalmazhat.

> [!TIP]
> Azt javasoljuk, egy beléptetési csoport az eszközök, amelyek egy kezdeti szükségeskonfiguráció számos vagy eszközök valamennyi amelyet ugyanannak a bérlőnek.

### <a name="individual-enrollment"></a>Az egyes beléptetési

Az egyes regisztrációs bejegyzés regisztrálni lehet, hogy egyetlen eszközökhöz. Egyes regisztrációkat használhatja X.509 levél tanúsítványok vagy (a fizikai vagy virtuális TPM) SAS-tokenje igazolás mechanizmusokat. Az egyes tagság található regisztrációs Azonosítót alfanumerikus, nagybetűk, és kötőjeleket tartalmazhat. Előfordulhat, hogy az egyéni regisztrációkhoz meg van határozva a kívánt IoT Hub-eszközazonosító.

> [!TIP]
> Azt javasoljuk, egyes regisztrációkat külön kezdeti konfigurációt igénylő eszközök, vagy hitelesítheti, hogy csak a SAS-tokenje TPM igazolás keresztül használó eszközök esetében.

## <a name="registration"></a>Regisztráció

A regisztráció az a rekord a eszközkiépítési sikeresen regisztrálása/eszköz kiépítése szolgáltatáson keresztül az IoT hubhoz. Regisztrációs jönnek létre, amelyek automatikusan; Törölje őket, de nem lehet frissíteni.

## <a name="operations"></a>Műveletek

Műveletek esetében a számlázási egysége eszköz kiépítése szolgáltatáshoz. Egy művelet. a szolgáltatás egy utasítás sikeres befejezését. Művelet lehet az eszköz regisztrálása és újraregisztrálása, vagy a szolgáltatásoldali változások, például tételek hozzáadása a regisztrálási listákhoz vagy a tételek frissítése a listákban.
