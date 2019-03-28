---
title: Hogyan utasításokat egy ASC IoT ikermodulja az ASC értéket módosítania az IoT-előzetes verzió |} A Microsoft Docs
description: Ismerje meg, hogyan lehet módosítani egy ASC IoT biztonsági ikermodul ASC az IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 1bc5dc86-0f33-4625-b3d3-f9b6c1a54e14
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 4bb25db7feddea66e75d9dd069aaec7785faf738
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541947"
---
# <a name="modify-an-asc-for-iot-module-twin"></a>Az ASC IoT az ikermodul módosítása

> [!IMPORTANT]
> Az IoT ASC jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk azt ismerteti, hogyan módosíthatja egy meglévő konfigurációjának **AzureIoTSecurity ikermodul** egy meglévő eszköz. 

Lásd: [hozzon létre egy IoT-modul ASC](quickstart-create-security-twin.md) megtudhatja, hogyan javíthatja egy új biztonsági modul egy új eszköz.  

## <a name="modification-considerations"></a>Módosítás kapcsolatos szempontok

> [!IMPORTANT]
> Az ikereszköz-konfigurációban mindegyik konfiguráció felülbírálja az alapértelmezett konfigurációt. Egy adott konfigurációnak már nem található a ikereszköz konfigurációban, ha az alapértelmezett konfigurációt használja. 

## <a name="configuration-schema-and-validation"></a>Konfigurációs séma- és érvényesítése 

Ellenőrizze, hogy ezzel szemben az ügynök konfigurációs [séma](https://github.com/Azure/asc-for-iot/schema/security_module_twin). Az ügynök nem indulnak el, ha a konfigurációs objektum nem egyezik meg a séma.

 
Ha az ügynök futása közben a konfigurációs objektum egy érvénytelen konfigurációt (a konfiguráció nem felel meg a sémának) változik, az ügynök figyelmen kívül hagyja az érvénytelen konfigurációt, és továbbra is a jelenlegi konfiguráció használatával. 

## <a name="edit-a-property"></a>Tulajdonság módosítása  

Állítsa be az összes egyéni tulajdonságokat belül AzureIoTSecurity ikermodulja az ügynök konfigurációs objektumban. 

Tulajdonság beállításához adja hozzá a konfigurációs objektum a kívánt értéket a tulajdonság kulcsot. Tulajdonság alapértelmezett értéke használatához távolítsa el a tulajdonságot a konfigurációs objektum:

```json
"desired": { //AzureIoTSecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //ASC for IoT Agent 
      // configuration section  
    "hubResourceId": "/subscriptions/82392767-31d3-4bd2-883d-9b60596f5f42/resourceGroups/myResourceGroup/providers/Microsoft.Devices/IotHubs/myIotHub",     
    "lowPriorityMessageFrequency": "PT1H",     
    "highPriorityMessageFrequency": "PT7M",    
    "eventPriorityFirewallConfiguration": "High",     
    "eventPriorityConnectionCreate": "Off" 
  } 
}, 
```

## <a name="properties"></a>Tulajdonságok 
Az alábbi táblázat tartalmazza az összes IoT-ügynökök ASC szabályozó tulajdonságok gyűjteményével. 
          

| Name (Név)| status | Érvényes értékek| Alapértelmezett értékek| Leírás |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Kötelező: False (hamis) |Érvényes értékek: ISO 8601 formátumú időtartama |Alapértelmezett érték: PT7M |Maximális idő előtt magas prioritású üzeneteket küldi el.|
|lowPriorityMessageFrequency |Kötelező: False (hamis)|Érvényes értékek: ISO 8601 formátumú időtartama |Alapértelmezett érték: PT5H |Maximális idő, alacsony prioritású üzenetek előtt küldi el.| 
|snapshotFrequency |Szükséges: False (hamis)|Érvényes értékek: időtartam ISO 8601 formátumban |Alapértelmezett érték PT13H |Eszköz állapota pillanatképek létrehozásának időintervallum.| 
|maxLocalCacheSizeInBytes |Kötelező: False (hamis) |Érvényes értékek: |Alapértelmezett érték: nagyobb, mint 8192 2560000 | Maximális tárterület (bájt) engedélyezett az ügynök üzeneteket tartalmazó gyorsítótárát. Terület üzenetek tárolásához az eszközön, mielőtt üzeneteket küld az engedélyezett maximális mérete.| 
|maxMessageSizeInBytes |Kötelező: False (hamis) |Érvényes értékek: Egy pozitív szám, 8192, kevesebb mint 262144-nál nagyobb |Alapértelmezett érték: 204800 |Maximális engedélyezett felhőüzenet ügynökök méretét. Ez a beállítás szabályozza a legnagyobb, az egyes üzenetekben küldött adatok mennyisége. |
|eventPriority$ {EventName} |Kötelező: False (hamis) |Érvényes értékek: Magas, alacsony kikapcsolva |Alapértelmezett értékeit: |Minden ügynök prioritását esemény jön létre. | 
|

### <a name="events"></a>Események

Az alábbi listában szereplő események olyan, az események az ASC IoT-ügynök képes gyűjteni az eszközök. AzureIotSecurity ikermodulja segítségével konfigurálhatja, amely ezeket az eseményeket gyűjt, majd döntse el, a megoldás a prioritásuk. 
 
|Esemény neve| a propertyName | Alapértelmezett érték| Pillanatkép-esemény| Részletes állapota  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Diagnosztikai esemény|eventPriorityDiagnostic| Ki| False (Hamis)| Az ügynök kapcsolódó diagnosztikai események. Használja ezt az eseményt a részletes naplózás.| 
Konfigurációs hiba |eventPriorityConfigurationError |Alacsony |False (Hamis) |Az ügynök nem tudta elemezni a konfigurációt. Ellenőrizze a konfigurációt a sémának.| 
|Az eldobott események statisztika |eventPriorityDroppedEventsStatistics |Alacsony |True (Igaz)|Az ügynök eseménystatisztika kapcsolatos. |
|Üzenet statisztika|eventPriorityMessageStatistics |Alacsony |True (Igaz) |Az ügynök kapcsolatos üzenet statisztikákat. |
|Csatlakoztatott hardver|eventPriorityConnectedHardware |Alacsony |True (Igaz) |Az eszközhöz csatlakoztatott minden hardveres pillanatképet.|
|Figyelőportjait|eventPriorityListeningPorts |Magas |True (Igaz) |Az eszközön lévő összes nyitott figyelőportjait pillanatképét.|
| Folyamat létrehozása |eventPriorityProcessCreate |Alacsony |False (Hamis) |Naplózás folyamat-létrehozás az eszközön.|
| Folyamat leállítása|eventPriorityProcessTerminate |Alacsony |False (Hamis) |Naplózás megszüntetése az eszközön feldolgozni.| 
 Rendszerinformáció |eventPrioritySystemInformation |Alacsony |True (Igaz) |Rendszer-információkat, például az operációs rendszer vagy a Processzor pillanatképet.|
|A helyi felhasználók| eventPriorityLocalUsers |Magas |True (Igaz)|A regisztrált helyi felhasználók belül a rendszer pillanatképet. |
|Bejelentkezés|  eventPriorityLogin |Magas|False (Hamis)|A bejelentkezési eseményeket, az eszközön (helyi és távoli bejelentkezések) naplózza.|
|Kapcsolat létrehozása |eventPriorityConnectionCreate|Alacsony|False (Hamis)|Naplózza a TCP-kapcsolatok létrehozása, és az eszközről. |
|Tűzfal-konfiguráció| eventPriorityFirewallConfiguration|Alacsony|True (Igaz)|Eszköz tűzfal-konfiguráció (tűzfalszabályok) pillanatképét. |
|Operációs rendszer| eventPriorityOSBaseline| Alacsony|True (Igaz)|Ellenőrizze a rendelkezésre álló eszköz OS pillanatkép.| 
|


## <a name="next-steps"></a>További lépések

- Olvassa el az ASC IOT [áttekintése](overview.md)
- További információk az ASC az IoT [architektúra](architecture.md)
- Ismertetés és felfedezés [ASC IoT-riasztások](concept-security-alerts.md)
- Ismerje meg az eléréséhez a [biztonsági adatok](how-to-security-data-access.md)
