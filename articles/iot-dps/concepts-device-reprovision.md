---
title: Azure IoT Hub-eszközkiépítési szolgáltatás – Eszközfogalmak
description: Az Azure IoT Hub-eszközkiépítési szolgáltatás (DPS) eszköz-újrakiépítéséi fogalmainak ismertetése
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 2bf369b784cddf307abc59d2b8766fc8a87e0985
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975346"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>Az IoT Hub-eszköz újralétesítési fogalmai

Az IoT-megoldás életciklusa során gyakori, hogy az eszközöket az IoT-központok között mozgatja. Ennek a lépésnek az okai a következő forgatókönyvek lehetnek:

* **Földrajzi hely / GeoLatency:** Ahogy egy eszköz mozog a helyek között, a hálózati késés javul azáltal, hogy az eszköz áttelepítése egy közelebbi IoT hub.

* **Több-bérlős:** Egy eszköz használható ugyanabban az IoT-megoldásban, és újra hozzárendelve egy új ügyfél, vagy az ügyfél honlapján. Ez az új ügyfél lehet kiszolgálni egy másik IoT hub használatával.

* **Megoldásmódosítása:** Egy eszköz áthelyezhető egy új vagy frissített IoT-megoldásba. Ez az újrahozzárendelés szükség lehet az eszköz kommunikálni egy új IoT hub, amely más háttér-összetevők csatlakozik.

* **Karantén**: A megoldásmódosításhoz hasonlóan. Egy eszköz, amely hibásan működik, sérült, vagy elavult lehet hozzárendelve egy IoT hub, amely csak frissíteni, és kap vissza a megfelelőségi. Miután az eszköz megfelelően működik, ezután visszakerül a fő hub.

Az eszközkiépítési szolgáltatáson belüli újralétesítési támogatás kielégíti ezeket az igényeket. Az eszközök automatikusan újra hozzárendelhetők az új IoT-központokhoz az eszköz regisztrációs bejegyzésében konfigurált újrakiépítési szabályzat alapján.

## <a name="device-state-data"></a>Eszközállapot-adatok

Az eszközállapot-adatok az [ikereszköz és](../iot-hub/iot-hub-devguide-device-twins.md) az eszköz képességeiből állnak. Ezeket az adatokat az eszközkiépítési szolgáltatás példánya és az IoT hub, amelyhez egy eszköz van rendelve tárolja.

![Kiépítés az eszközkiépítési szolgáltatással](./media/concepts-device-reprovisioning/dps-provisioning.png)

Ha egy eszköz először egy eszközkiépítési szolgáltatás példány, a következő lépéseket kell tenni:

1. Az eszköz egy létesítési kérelmet küld egy eszközkiépítési szolgáltatás példány. A szolgáltatáspéldány egy regisztrációs bejegyzés alapján hitelesíti az eszközidentitást, és létrehozza az eszközállapot-adatok kezdeti konfigurációját. A szolgáltatáspéldány hozzárendeli az eszközt egy IoT huba a regisztrációs konfiguráció alapján, és visszaadja, hogy az IoT hub hozzárendelésaz eszközre.

2. A kiépítési szolgáltatáspéldány ad egy másolatot a kezdeti eszköz állapotadatok a hozzárendelt IoT hub. Az eszköz csatlakozik a hozzárendelt IoT hubhoz, és megkezdi a műveleteket.

Idővel az IT hubon lévő eszközállapot-adatok at [az eszközműveletek](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) és a [háttérműveletek frissíthetik.](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations) Az eszközkiépítési szolgáltatás példányában tárolt kezdeti eszközállapot-adatok érintetlenek maradnak. Ez az érintetlen eszközállapot-adat a kezdeti konfiguráció.

![Kiépítés az eszközkiépítési szolgáltatással](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

A forgatókönyvtől függően, ahogy egy eszköz az IoT-központok között mozog, szükség lehet az előző IoT hubon frissített eszközállapot áttelepítésére is az új IoT hubra. Ezt az áttelepítést az eszközkiépítési szolgáltatás házirendjeinek újrakiépítése támogatja.

## <a name="reprovisioning-policies"></a>Házirendek újrakiépítési irányelvei

A forgatókönyvtől függően az eszköz általában küld egy kérelmet egy kiépítési szolgáltatáspéldány újraindításkor. Azt is támogatja a módszert manuálisan kiépítés igény szerint. A regisztrációs bejegyzés újralétesítési szabályzata határozza meg, hogy az eszközkiépítési szolgáltatáspéldány hogyan kezeli ezeket a létesítési kérelmeket. A házirend azt is meghatározza, hogy az eszközállapot-adatokat át kell-e telepíteni az újraépítés során. Ugyanazok a házirendek érhetők el az egyes beléptetési csoportok és beléptetési csoportok esetében:

* **Adatok újbóli kiépítése és áttelepítése**: Ez a házirend az alapértelmezett az új beléptetési bejegyzéseknél. Ez a házirend akkor lép működésbe, ha a regisztrációs bejegyzéshez társított eszközök új kérelmet (1) küldnek. Attól függően, hogy a regisztrációs bejegyzés konfigurációját, az eszköz lehet hozzárendelni egy másik IoT hub. Ha az eszköz módosítja az IoT-központok, az eszköz regisztrációja a kezdeti IoT hub lesz távolítva. A kezdeti IoT hub frissített eszközállapot-adatait a központi adatok áttelepítik az új IoT hubra (2). Az áttelepítés során az eszköz állapota **hozzárendelésként lesz jelentve.**

    ![Kiépítés az eszközkiépítési szolgáltatással](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Újraüzembe helyezés és visszaállítás a kezdeti konfigurációra**: Ez a házirend akkor lép működésbe, amikor a regisztrációs bejegyzéshez társított eszközök új létesítési kérelmet (1) nyújtanak be. Attól függően, hogy a regisztrációs bejegyzés konfigurációját, az eszköz lehet hozzárendelni egy másik IoT hub. Ha az eszköz módosítja az IoT-központok, az eszköz regisztrációja a kezdeti IoT hub lesz távolítva. A kezdeti konfigurációs adatokat, amelyek a létesítési szolgáltatáspéldány kapott, amikor az eszköz kiépítése az új IoT hub (2). Az áttelepítés során az eszköz állapota **hozzárendelésként lesz jelentve.**

    Ezt a szabályzatot gyakran használják a gyári beállítások visszaállítása ioT-központok módosítása nélkül.

    ![Kiépítés az eszközkiépítési szolgáltatással](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Soha ne létesítse újra:** Az eszköz soha nem lesz újrahozzárendelve egy másik hubhoz. Ez a szabályzat a visszamenőleges kompatibilitás kezelésére szolgál.

### <a name="managing-backwards-compatibility"></a>Visszamenőleges kompatibilitás kezelése

2018 szeptembere előtt az IoT-központokhoz való eszköz-hozzárendelések ragadós viselkedést tanúsítottak. Amikor egy eszköz ment vissza a létesítési folyamat, csak akkor lesz hozzárendelve vissza ugyanahhoz az IoT hubhoz.

A viselkedéstől függő megoldások esetében a kiépítési szolgáltatás visszafelé kompatibilitást tartalmaz. Ez a viselkedés jelenleg az alábbi feltételek nek megfelelően marad fenn az eszközökesetében:

1. Az eszközök egy API-verzióhoz csatlakoznak, mielőtt az eszközkiépítési szolgáltatásnatív újralétesítési támogatás rendelkezésre állna. Tekintse meg az alábbi API-táblázatot.

2. Az eszközök regisztrációs bejegyzése nem rendelkezik újrakiépítési házirend-beállítással.

Ez a kompatibilitás biztosítja, hogy a korábban üzembe helyezett eszközök ugyanazt a viselkedést tapasztalják, mint a kezdeti tesztelés során. Az előző viselkedés megőrzése érdekében ne mentse a reprovisioning házirendet ezekre a regisztrációkra. Ha egy újrakiépítési házirend van beállítva, az újrakiépítési házirend elsőbbséget élvez a viselkedéssel szemben. Azáltal, hogy lehetővé teszi, hogy az újraépítési szabályzat elsőbbséget élvezzen, az ügyfelek frissíthetik az eszköz viselkedését anélkül, hogy újra kellene képzniük az eszközt.

A következő folyamatábra segít megjeleníteni, ha a viselkedés jelen van:

![visszamenőleges kompatibilitási folyamatábra](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

Az alábbi táblázat az API-verziókat mutatja be az eszközkiépítési szolgáltatásnatív újraépítési támogatás ának rendelkezésre állása előtt:

| REST API | C SDK | Python SDK |  Node SDK | Java SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 és korábbi](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 és korábbi](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2. és korábbi](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3.](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 vagy korábbi](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 vagy korábbi](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Ezek az értékek és hivatkozások valószínűleg megváltoznak. Ez csak egy helyőrző kísérlet annak meghatározására, hogy az ügyfél hol határozhatja meg a verziókat, és hogy mi lesz a várt verziók.

## <a name="next-steps"></a>További lépések

* [Eszközök újbóli kiépítése](how-to-reprovision.md)
