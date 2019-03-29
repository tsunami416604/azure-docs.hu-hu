---
title: Az IoT device vizsgálat útmutató előzetes ASC |} A Microsoft Docs
description: Ennek módjáról útmutató ismerteti, hogyan ASC IOT segítségével egy gyanús IoT-eszközzel a Log Analytics.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 032315d1b618b9716e23c8433c6ec1bf64b7e77d
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580538"
---
# <a name="investigate-a-suspicious-iot-device"></a>Vizsgálja meg a gyanús IoT-eszköz

> [!IMPORTANT]
> Az IoT ASC jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió nélkül egy szolgáltatói szerződést, és nem javasolt éles számítási feladatok esetében. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az IoT a szolgáltatás értesítéseinek és bizonyíték ASC egyértelmű jelzések szolgáltatni IoT-eszközök gyanús bevonása a gyanús tevékenységek, vagy ha jelzések léteznek, hogy egy eszköz biztonsága sérül. 

Ebben az útmutatóban használja az határozza meg az esetleges kockázatokat a szervezet számára, annak eldöntése, hogyan javíthatja és hasonló támadások megelőzése, a jövőben miként segítik a vizsgálat javaslatokat.  

> [!div class="checklist"]
> * Az adatok keresése
> * Vizsgálja meg a kql lekérdezésekkel


## <a name="how-can-i-access-my-data"></a>Hogyan érhetem el az adataimat?

Alapértelmezés szerint az IoT ASC tárolja a Log Analytics-munkaterület a biztonsági riasztások és javaslatok. Azt is beállíthatja a nyers biztonsági adatok tárolására.

Keresse meg a Log Analytics-munkaterület, társzolgáltatást:

1. Nyissa meg az IoT hubhoz 
1. Kattintson a **biztonsági**, majd **beállítások**.
1. Módosítsa a Log Analytics-munkaterület konfigurációs adatait. 
1. Kattintson a **Save** (Mentés) gombra. 

A következő konfigurációs a következőket a Log Analytics-munkaterület adataihoz hozzáférést:

1. Válassza ki, és kattintson az ASC az IoT hub IoT-riasztás. 
1. Kattintson a **további vizsgálatra**. 
1. Válassza ki **ezt a riasztást, kattintson ide, és tekintse meg a DeviceId oszlopot, mely eszközök vannak**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Vizsgálati lépések gyanús IoT-eszközök esetén

Insights és az IoT-eszközökre vonatkozó nyers adatok elérésére, nyissa meg a Log Analytics-munkaterület [adatok eléréséhez](#how-can-i-access-my-data).

Ellenőrizze, és vizsgálja meg az eszközön lévő adatokat a következő részleteket és a tevékenységek az alábbi kql lekérdezésekkel.

### <a name="related-alerts"></a>Kapcsolódó riasztások

Ismerje meg, ha más riasztások voltak, aktivált körül az azonos használja a következő kql lekérdezés:

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ~~~

### <a name="users-with-access"></a>Hozzáféréssel rendelkező felhasználók

Ismerje meg, hogy mely felhasználók férhetnek hozzá az eszköz használatát a következő kql lekérdezés: 

  ~~~
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
  ~~~
Ezen adatok segítségével felderítése: 
  1. Mely felhasználók rendelkezzenek hozzáféréssel az eszközhöz?
  2. Rendelkeznek hozzáféréssel rendelkező felhasználók a várt módon jogosultsági szintek? 

### <a name="open-ports"></a>Portok megnyitása

Milyen portokat az eszközön jelenleg használatban van, vagy a használt ki használja az alábbi kql lekérdezés: 

  ~~~
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
  ~~~

    Use this data to discover:
  1. Mely figyelő sockets jelenleg aktívak az eszközön?
  2. A jelenleg aktív figyelési sockets engedélyezett?
  3. Vannak-e az eszközhöz csatlakoztatott minden gyanús távoli cím?

### <a name="user-logins"></a>Felhasználói bejelentkezések

Ismerje meg, hogy a felhasználók, az eszközre való bejelentkezéshez használja a következő kql lekérdezést: 
 
  ~~~
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
  ~~~

    Use the query results to discover:
  1. Az eszköz a bejelentkezett felhasználók?
  2. Jelentkezzen be kell menteniük a bejelentkezett felhasználók?
  3. A bejelentkezett felhasználók várható vagy váratlan IP-címekről fejeződött csatlakozni?
  
### <a name="process-list"></a>Folyamatok listája

Annak megállapítása, hogy a folyamat lista van, mint várt használja a következő kql lekérdezés: 

  ~~~
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
  ~~~

    Use the query results to discover:

  1. Az eszközön futó gyanús folyamatok küldtek?
  2. A megfelelő felhasználók végrehajtódtak folyamatokat?
  3. Tartalmazott bármely parancssori végrehajtás a megfelelő és a várt argumentum?

## <a name="next-steps"></a>További lépések
Miután kivizsgálta egy eszköz, és azonosítsa a kockázatok jobban megértette, érdemes figyelembe venni [egyéni riasztások konfigurálása](quickstart-create-custom-alerts.md) javíthatja biztonsági helyzetét IoT megoldás. Ha egy eszköz ügynök még nem rendelkezik, érdemes lehet [egy biztonsági ügynök üzembe helyezése](how-to-deploy-agent.md) vagy [módosítja a konfigurációt egy meglévő eszközt ügynök](how-to-agent-configuration.md) hatékonyságának növelése érdekében. 