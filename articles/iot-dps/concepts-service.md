---
title: "Szolgáltatás az Azure IoT Hub eszköz kiépítése szolgáltatáshoz fogalmak |} Microsoft Docs"
description: "Jellemző a terjesztési pontok és az IoT-központ szolgáltatás létesítési fogalmakat ismerteti"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 10/03/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 96c63e5d0379150ea619dbbe912a21e373f808af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Az IoT Hub eszköz kiépítése szolgáltatással kapcsolatos fogalmak

IoT Hub eszköz kiépítése szolgáltatáshoz olyan IoT hub, amely egy megadott IoT-központ nulla érintéssel eszközkiépítési konfigurálására használt segítő szolgáltatás. Az eszköz kiépítése szolgáltatáshoz megadhat eszközök millióira biztonságos és skálázható módon.

Eszköz kiépítése két lépésben. Az első rész hozza létre azt a kezdeti kapcsolatot az eszköz és az IoT-megoldás által *regisztrálása* az eszközt. A második rész alkalmazza a megfelelő *konfigurációs* az eszközre, a megoldás a meghatározott követelmények alapján. Mindkét lépések elvégzése után, azt is tegyük fel például, hogy az eszköz teljes mértékben lett *kiépített*. Eszköz kiépítése szolgáltatáshoz automatizálja az üzembe helyezési zökkenőmentes élményt biztosít az eszköz két lépést.

Ez a cikk áttekintést nyújt a kiépítési fogalmakat kezelésének alkalmazható a *szolgáltatás*. Ez a cikk az érintett személyeknek igényeinek jobban megfelelő a [felhő telepítő lépés](about-iot-dps.md#cloud-setup-step) az eszköz telepítési felkészülés.

## <a name="service-operations-endpoint"></a>Szolgáltatásvégpont műveletek

A szolgáltatásvégpont műveletek a szolgáltatás beállításainak kezelése és a beléptetési lista végpontja. Ez a végpont csak használja a szolgáltatás-rendszergazda; eszközök nem használják.

## <a name="device-provisioning-endpoint"></a>Eszköz üzembe helyezési végpont

Az eszköz-üzembehelyezési végpont a központi végpont kialakítási konzultáljon minden eszköz. Az URL-cím megegyezik létesítési szolgáltatások nincs szükség az új kapcsolat információkkal eszközök reflash ellátási lánc forgatókönyvekben. A [azonosító hatókör](#id-scope) biztosítja a bérlők elkülönítését.

## <a name="linked-iot-hubs"></a>Csatolt IoT-központok

Eszköz kiépítése szolgáltatáshoz csak oszthat meg a hozzá csatolt IoT-központok eszközök. Az IoT-központ Linking eszköz kiépítése szolgáltatáshoz biztosít a service olvasási/írási engedéllyel az IoT hub eszköz beállításjegyzékébe; a hivatkozás eszköz kiépítése szolgáltatáshoz regisztrálja az Eszközazonosítót és az eszköz iker a kezdeti beállítása. Csatolt IoT-központok bármely Azure régióban lehet. A létesítési szolgáltatás előfordulhat, hogy más előfizetések központok kapcsolódik.

## <a name="allocation-policy"></a>Elosztási házirend

A szolgáltatási szint beállítása, amely meghatározza, hogy hogyan eszköz kiépítése szolgáltatáshoz az IoT-központ eszközökhöz rendeli. Számos három támogatott elosztási házirendeket.
* **Terjesztési egyenletesen súlyozott**: csatolt IoT-központok valószínűleg egyaránt számukra kiosztott eszközök. Az alapértelmezett beállítás. Ha csak egy IoT-központ eszközök kiépíteni, ezzel a beállítással tárolhatja.
* **Legkisebb mértékű késleltetést**: eszközök az eszközre a legkisebb mértékű késleltetést törlődnek az IoT-központ számára. Ha több csatolt IoT-központok volna a azonos legkisebb mértékű késleltetést biztosít, a létesítési szolgáltatás eszközök csak ezek hubok között
* **A beléptetési listán keresztül statikus konfigurációs**: a regisztráció listán a kívánt IoT-központ megadását prioritást élvez a szolgáltatásiszint-elosztási házirend.

## <a name="enrollment"></a>Regisztráció

A beléptetési azon eszközök vagy az eszközök, amelyek lehet, hogy néhány regisztrációs rekordot. A beléptetési rekord az eszköz vagy a csoport az eszközök adatait tartalmazza, beleértve a igazolási módszert a őket, és opcionálisan kezdeti szükségeskonfiguráció szükséges IoT hub eszköz- és kívánt azonosító. Az eszköz kiépítése szolgáltatáshoz által támogatott regisztrációkat két típusa van.

### <a name="enrollment-group"></a>Beléptetési csoport

Egy regisztrációs csoport olyan eszközök, amelyek egy adott igazolási módszert. A beléptetési csoport minden eszköze X.509-tanúsítvány legfelső szintű hitelesítésszolgáltató által aláírt jelenthet. Beléptetési csoportok csak használhatja az X.509 tanúsítvány mechanizmus. A beléptetési csoport nevét és a tanúsítvány neve alfanumerikus, kisbetűnek kell lennie, és kötőjeleket tartalmazhat.

> [!TIP]
> Azt javasoljuk, egy beléptetési csoport az eszközök, amelyek egy kezdeti szükségeskonfiguráció számos vagy eszközök valamennyi amelyet ugyanannak a bérlőnek.

### <a name="individual-enrollment"></a>Az egyes beléptetési

Az egyes regisztrációs bejegyzés regisztrálni lehet, hogy egyetlen eszközökhöz. Egyes regisztrációkat használhatja X.509-tanúsítványokat vagy a SAS-tokenje (a valós vagy virtuális TPM) igazolás mechanizmusokat. Az egyes tagság található regisztrációs Azonosítót alfanumerikus, nagybetűk, és kötőjeleket tartalmazhat. Előfordulhat, hogy az egyes regisztrációkat a kívánt IoT hub eszköz-azonosító van megadva.

> [!TIP]
> Azt javasoljuk, egyes regisztrációkat külön kezdeti konfigurációt igénylő eszközök, vagy használó eszközöket is csak TPM vagy virtuális TPM SAS-tokenje igazoló mechanizmusként.

## <a name="registration"></a>Regisztráció

A regisztráció az a rekord a eszközkiépítési sikeresen regisztrálása/eszköz kiépítése szolgáltatáson keresztül az IoT hubhoz. Regisztrációs jönnek létre, amelyek automatikusan; Törölje őket, de nem lehet frissíteni.

## <a name="operations"></a>Műveletek

Műveletek esetében a számlázási egysége eszköz kiépítése szolgáltatáshoz. Egy művelet. a szolgáltatás egy utasítás sikeres befejezését. Művelet lehet az eszköz regisztrálása és újraregisztrálása, vagy a szolgáltatásoldali változások, például tételek hozzáadása a regisztrálási listákhoz vagy a tételek frissítése a listákban.
