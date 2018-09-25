---
title: Az Azure IoT Hub Device Provisioning Service reprovisioning eszköz fogalmai |} A Microsoft Docs
description: Ismerteti az Azure IoT Hub Device Provisioning Service fogalmak reprovisioning eszköz
author: wesmc7777
ms.author: wesmc
ms.date: 08/15/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 391131df7726131865ab9d875e8fcde185b3d0a5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965572"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>IoT Hub Device reprovisioning fogalmak


IoT-megoldás élettartama során szokás az eszközök IoT-központok között. Az erről az áthelyezésről okok a következők lehetnek a következő esetekben:

* **Földrajzi hely meghatározásának / GeoLatency**: egy eszköz között helyezi át, mert az eszköz által növelése a hálózati késés közelebb IoT hubra át.

* **Több-bérlős**: Előfordulhat, hogy belül az IoT-megoldásban használt és egy új ügyfél vagy ügyfél telephelyén van rendelve egy eszközt. Az új ügyféllel előfordulhat, hogy szolgálja ki a különböző IoT hub használatával.

* **Megoldás módosítása**: eszköz sikerült áthelyezni egy új vagy frissített IoT-megoldás. Az újbóli hozzárendelést szükség lehet az eszköz kommunikáljon az egy új IoT hubot, amelyhez csatlakozik a többi háttérkomponensnek. 

* **Karantén**: hasonló, hogy a megoldás módosítása. Egy IoT hubra, ahol teheti meg, csak frissítse, majd az megfelelőségi hibás, sérült vagy elavult eszköz rendelésekor lehet. Miután az eszköz megfelelően működik-e, majd át térjen vissza a fő hub.

Belül a Device Provisioning Service-címeket támogatja ezen igények reprovisioning. Eszközök automatikusan máshoz lehessen az új IoT hubra a reprovisioning házirend, amely konfigurálva van az eszköz regisztrációs bejegyzés alapján. 

## <a name="device-state-data"></a>Eszközadatok állapota

Áll állapotadatok eszköz, a [ikereszköz](../iot-hub/iot-hub-devguide-device-twins.md) és az eszköz képességeit. A Device Provisioning Service-példány és az IoT hub eszköz rendelt tárolja ezeket az adatokat. 

![A Device Provisioning Service-mel](./media/concepts-device-reprovisioning/dps-provisioning.png)

Ha egy eszköz kezdetben ki van építve a Device Provisioning Service-példányt, a következő lépéseket kell végrehajtani:

1. Az eszköz egy kiépítési kérést küld a Device Provisioning Service-példányra. A szolgáltatáspéldány az eszközidentitást regisztrációs bejegyzés alapján hitelesíti, és létrehozza a kezdeti konfigurációt az eszköz állapota adatok. A szolgáltatáspéldány hozzárendeli az eszköz egy IoT hubra, a beléptetési konfigurációhoz alapján, és az eszköz adott IoT hub-hozzárendelést visszaadja.

2. A kiépítési szolgáltatás-példánya a hozzárendelt IoT-központ bármely kezdeti állapot adatokat biztosít. Az eszköz csatlakozik a hozzárendelt IoT hubhoz, és operations kezdődik.


Az idő múlásával a eszköz állapot adatait az IoT hub előfordulhat, hogy frissítette [eszközművelet](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) és [háttérbeli műveletek](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations). A kezdeti Eszközállapot-adatokat a Device Provisioning Service-példányban tárolt érintetlenek maradnak. Ez változatlanul eszköz állapotadatok a kezdeti konfigurációs.

![A Device Provisioning Service-mel](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

A forgatókönyvtől függően szerint az eszköz IoT-központok között helyezi át is lehet áttelepíteni az eszköz állapotát, az előző IoT hubon keresztül, az új IoT hub frissítése szükséges. Ez támogatott reprovisioning házirendek a Device Provisioning Service-ben. 


## <a name="reprovisioning-policies"></a>Reprovisioning házirendek

A forgatókönyvtől függően egy eszköz fog általában újraindítás a kiépítési szolgáltatás példánya egy kiépítési kérést küldhet, és manuálisan indítható a igény szerinti kiépítés metódus támogatja. A regisztrációs bejegyzés reprovisioning házirend határozza meg, hogyan kezeli az eszközt a kiépítési szolgáltatáspéldány a regisztrációs kérések, és ha eszközadatok állapot reprovisioning során lesz áttelepítve. Az ugyanazon házirend érhető el az egyéni regisztrációk és regisztrációs csoportok:

* **Újbóli üzembe helyezése és az adatok áttelepítése**: Ez a házirend az alapértelmezett az új regisztrációs bejegyzés. Ez a szabályzat hajt végre műveletet, amikor regisztrációs bejegyzés társított eszközök elküld egy új kiépítési kérést (1). A regisztrációs bejegyzés konfigurációjától függően az eszköz lehet hozzárendelni egy másik IoT hubra. Ha az eszköz IoT-központok változnak, az eszköz regisztrációját, a kezdeti az IoT hub távolítja el. A frissített Eszközállapot-adatokat a kezdeti IoT hubról fog migráljuk az új IoT hub (2). Az áttelepítés során az eszköz állapota nem szerepel, **hozzárendelése**.

    ![A Device Provisioning Service-mel](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)


* **Újbóli üzembe helyezése és a kezdeti konfiguráció alaphelyzetbe**: Ez a szabályzat művelet vesz igénybe, amikor regisztrációs bejegyzés társított eszközök elküld egy új kiépítési kérést (1). A regisztrációs bejegyzés konfigurációjától függően az eszköz lehet hozzárendelni egy másik IoT hubra. Ha az eszköz IoT-központok változnak, az eszköz regisztrációját, a kezdeti az IoT hub távolítja el. A kezdeti konfigurációs adatokat (2) az új IoT hub által biztosított a kiépítési szolgáltatás példány kapta, amikor az eszköz lett üzembe helyezve. Az áttelepítés során az eszköz állapota nem szerepel, **hozzárendelése**.

    Ez a szabályzat gyakran használják a gyári beállításokat az IoT-központok módosítása nélkül. 

    ![A Device Provisioning Service-mel](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)


* **Soha ne újbóli létesítéséhez**: az eszköz soha nem rendelve egy másik központ. Ez a házirend kezeléséhez a visszamenőleges kompatibilitás biztosítunk.

#### <a name="managing-backwards-compatibility"></a>Visszamenőleges kompatibilitási kezelése

2018. szeptember eszköz-hozzárendelések az IoT hubra kellett egy Beragadó működés. Amikor egy eszköz végighaladt-e vissza a kiépítési folyamat, azt csak hozzájuk vissza a azonos IoT hub használatával. 

Függőség elvégezte ezt a viselkedést a megoldások a kiépítési szolgáltatás tartalmaz visszamenőleges kompatibilitás. Ez a viselkedés jelenleg változatlan marad a következő feltételek szerint eszközök:

1. Az eszközök csatlakoznak az API-verziót, az a Device Provisioning Service reprovisioning natív támogatását a rendelkezésre állás előtt. Tekintse meg az API-t az alábbi táblázat.

2. Az eszköz beléptetési bejegyzésében nincs rajtuk beállított reprovisioning házirend. 

A kompatibilitás biztosítja a már telepített eszközök ugyanez a viselkedés, amely a kezdeti tesztelés során található volt tapasztalható. Meg szeretné őrizni a korábbi működése, ne mentse reprovisioning házirend ezek regisztrációk. Reprovisioning szabályzat be van állítva, ha a reprovisioning szabályzat élvez elsőbbséget a korábbinál. Azáltal, hogy a reprovisioning szabályzat elsőbbséget, ügyfelek alaphelyzetbe állítja az eszközt anélkül frissítheti eszközök viselkedését.

Az alábbi folyamatábra segít összefoglalva viselkedésének megléte esetén:

![visszamenőleges kompatibilitási folyamatábra](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

Az alábbi táblázat az API-verziók a reprovisioning natív módon támogatja a rendelkezésre állás előtt a Device Provisioning Service-ben:


| REST API | C SDK-T | Python SDK |  Node SDK | Java SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01-es vagy korábbi kiadásai](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/createorupdate#uri-parameters) | [1.2.8 és korábbi verziók](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 és korábbi verziók](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 vagy régebbi](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 vagy régebbi](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0-ás vagy korábbi](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Ezek az értékek és hivatkozások valószínűleg módosítani. Ez a csak egy helyőrző kísérlet meghatározhatja a verziók hol lehet meghatározni egy ügyfél és a várt verzió lesz.




## <a name="next-steps"></a>További lépések

- [Ismételt kiépítése az eszközökön](how-to-reprovision.md)







