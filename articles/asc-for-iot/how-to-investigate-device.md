---
title: Gyanús eszköz vizsgálata
description: Ez az útmutató bemutatja, hogyan használhatja az Azure Security Center for IoT egy gyanús IoT-eszköz kivizsgálása a Log Analytics használatával.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: f333f28dc0e02e8d010f5521f298d0f0b031dbf2
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311036"
---
# <a name="investigate-a-suspicious-iot-device"></a>Gyanús IoT-eszköz vizsgálata

Az Azure Security Center for IoT szolgáltatás riasztások egyértelmű jelzéseket, ha az IoT-eszközök gyanúja a gyanús tevékenységekben való részvétel, vagy ha azt jelzi, hogy egy eszköz sérül.

Ebben az útmutatóban a rendelkezésre álló vizsgálati javaslatok segítségével meghatározhatja a szervezetre nézve lehetséges kockázatokat, eldöntheti, hogyan orvosolja, és fedezze fel a hasonló támadások jövőbeni megelőzésének legjobb módjait.

> [!div class="checklist"]
> * Az eszközadatok megkeresése
> * Kql-lekérdezések használatának vizsgálata

## <a name="how-can-i-access-my-data"></a>Hogyan érhetem el az adataimat?

Alapértelmezés szerint az Azure Security Center for IoT tárolja a biztonsági riasztásokat és javaslatokat a Log Analytics-munkaterületen. A nyers biztonsági adatok tárolását is választhatja.

A Log Analytics-munkaterület megkeresése az adattároláshoz:

1. Nyissa meg az IoT-központot,
1. A **Biztonság**csoportban kattintson **az Áttekintés gombra,** majd válassza a Beállítások **lehetőséget.**
1. Módosítsa a Log Analytics munkaterület konfigurációs adatait.
1. Kattintson a **Save** (Mentés) gombra.

A következő konfigurációt követően a Log Analytics-munkaterületen tárolt adatok eléréséhez tegye a következőket:

1. Válassza ki, és kattintson egy Azure Security Center for IoT-riasztás az IoT Hub.
1. Kattintson **a További vizsgálat gombra.**
1. Jelölje **be, ha meg szeretné tekinteni, hogy mely eszközökön van ez a riasztás, kattintson ide, és tekintse meg a DeviceId oszlopot.**

## <a name="investigation-steps-for-suspicious-iot-devices"></a>A gyanús IoT-eszközök vizsgálati lépései

Az IoT-eszközökelemzési és nyers adatok megtekintéséhez nyissa meg a Log Analytics-munkaterületét [az adatok eléréséhez.](#how-can-i-access-my-data)

Tekintse meg az alábbi kql-lekérdezéseket, hogy megismerkedjen az eszközön található riasztások és tevékenységek vizsgálatával.

### <a name="related-alerts"></a>Kapcsolódó riasztások

Ha meg szeretné tudni, hogy más riasztások is körülbelül egy időben aktiválódtak-e, használja a következő kql lekérdezést:

  ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ```

### <a name="users-with-access"></a>Hozzáféréssel rendelkező felhasználók

Ha meg szeretné tudni, hogy mely felhasználók férhetnek hozzá ehhez az eszközhöz, használja a következő kql lekérdezést:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
 ```
Ezeket az adatokat a következők felderítéséhez használhatja:

- Mely felhasználók férhetnek hozzá az eszközhöz?
- A hozzáféréssel rendelkező felhasználók rendelkeznek a várt jogosultsági szintekkel?

### <a name="open-ports"></a>Portok megnyitása

Ha meg szeretné tudni, hogy az eszköz mely portjai vannak használatban vagy vannak használatban, használja a következő kql lekérdezést:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
 ```

Ezeket az adatokat a következők felderítéséhez használhatja:

- Mely figyelőszoftver-csatlakozók vannak aktívak az eszközön?
- Engedélyezendők az éppen aktív figyelőszoftvercsatornák?
- Vannak gyanús távoli címek az eszközhöz csatlakoztatva?

### <a name="user-logins"></a>Felhasználói bejelentkezések

Az eszközre bejelentkezett felhasználók megkereséséhez használja a következő kql lekérdezést:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
 ```

A lekérdezés eredményeinek használatával a következőket fedezheti fel:

- Mely felhasználók jelentkeztek be az eszközre?
- A bejelentkezett felhasználóknak be kell jelentkezniük?
- A bejelentkezett felhasználók a várt vagy nem várt IP-címekről csatlakoztak?

### <a name="process-list"></a>Folyamatlista

Ha meg szeretné tudni, hogy a folyamatlista a várt módon van-e, használja a következő kql lekérdezést:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
```

A lekérdezés eredményeinek használatával a következőket fedezheti fel:

- Voltak gyanús folyamatok az eszközön?
- A folyamatokat megfelelő felhasználók hajtották végre?
- Tartalmaztak parancssori végrehajtások a helyes és várt argumentumokat?

## <a name="next-steps"></a>További lépések

Miután megvizsgált egy eszközt, és jobban megismerkedhet a kockázatokkal, érdemes [megfontolnia az egyéni riasztások konfigurálását](quickstart-create-custom-alerts.md) az IoT-megoldás biztonsági állapotának javítása érdekében. Ha még nem rendelkezik eszközügynökkel, fontolja meg [egy biztonsági ügynök üzembe helyezését](how-to-deploy-agent.md) vagy egy meglévő [eszközügynök konfigurációjának módosítását](how-to-agent-configuration.md) az eredmények javítása érdekében.
