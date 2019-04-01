---
title: Az Azure Security Center konfigurálása az IoT-ügynök előzetes verzió |} A Microsoft Docs
description: Megtudhatja, hogyan használható az ügynökök konfigurálására az Azure Security Center az IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 27b548459bd1fee3c6596cce624b00d052e608fe
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757380"
---
# <a name="tutorial-configure-security-agents"></a>Oktatóanyag: Biztonsági ügynökök konfigurálása

> [!IMPORTANT]
> Az Azure Security Center az IoT jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió nélkül egy szolgáltatói szerződést, és nem javasolt éles számítási feladatok esetében. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk ismerteti az Azure Security Center (ASC) IoT biztonsági ügynök, hogyan módosíthatja őket konfigurálása ASC IoT biztonsági ügynökök esetében.

> [!div class="checklist"]
> * Biztonsági ügynökök konfigurálása
> * Ikereszköz tulajdonságainak szerkesztésével ügynök viselkedésének módosítása
> * Fedezze fel az alapértelmezett konfiguráció

## <a name="agents"></a>Ügynökök

ASC IoT biztonsági ügynökök esetében adatok gyűjtése IoT-eszközökről, és hajtsa végre az észlelt biztonsági rések elhárításához biztonsági műveleteket. Biztonsági ügynök konfigurációs testre szabhatja a modul ikertulajdonságok használatával vezérelhető. Általánosságban elmondható hogy ezek a Tulajdonságok másodlagos frissítések ritkák.  

ASC IoT a biztonsági ügynök ikereszköz konfigurációs objektum számára egy egy .json formátumú objektum. A konfigurációs objektum az ügynök viselkedését vezérlő meghatározó vezérelhető tulajdonságait. 

Ezek a beállítások segítségével testre szabhatja az ügynök az egyes helyzetekhez szükséges. Például automatikusan a kivételével az egyes események vagy energiafogyasztás minimális szinten tartása is ezek a tulajdonságok konfigurálásával.  

Az ASC használja az IoT biztonsági ügynökkonfiguráció [séma](https://aka.ms/iot-security-github-module-schema) módosításokat.  

## <a name="configuration-objects"></a>Konfigurációs objektumok 

Minden egyes IoT biztonsági ügynök ASC kapcsolódó tulajdonság az ügynök konfigurációs objektumot, a kívánt tulajdonságok területen belül található a **azureiotsecurity** modul. 

Módosíthatja a konfigurációt, hozzon létre, és módosítsa az objektumon belüli a **azureiotsecurity** modul ikereszköz identitás. 

Ha az ügynök konfigurációs objektum nem létezik az a **azureiotsecurity** ikermodul, az összes biztonsági ügynök tulajdonság értéke az alapértelmezett. 

```json
"desired": {
  "azureiot*com^securityAgentConfiguration^1*0*0": {
  } 
}
```

Ellenőrizze, hogy a az ügynök konfigurációs módosítások ellen [séma](https://aka.ms/iot-security-github-module-schema).
Az ügynök nem indulnak el, ha a konfigurációs objektum nem egyezik meg a séma.

## <a name="configuration-schema-and-validation"></a>Konfigurációs séma- és érvényesítése 

Ellenőrizze, hogy ezzel szemben az ügynök konfigurációs [séma](https://aka.ms/iot-security-github-module-schema). Az ügynök nem indulnak el, ha a konfigurációs objektum nem egyezik meg a séma.

 
Ha az ügynök futása közben a konfigurációs objektum egy érvénytelen konfigurációt (a konfiguráció nem felel meg a sémának) változik, az ügynök figyelmen kívül hagyja az érvénytelen konfigurációt, és továbbra is a jelenlegi konfiguráció használatával. 

## <a name="editing-a-property"></a>Egy tulajdonság szerkesztése 

Az ügynök konfigurációs objektumban belül minden egyéni tulajdonságokat kell beállítani a **azureiotsecurity** ikermodul.
Tulajdonság alapértelmezett értéke használatához távolítsa el a konfigurációs objektumot a tulajdonságot.

### <a name="setting-a-property"></a>A következő tulajdonság beállítása

1. Az IoT hubhoz keresse meg és válassza ki a módosítani kívánt eszközt.

1. Kattintson az eszközén, majd a **azureiotsecurity** modul.

1. Kattintson a **identitás Ikermodul**.

1. A biztonsági modulhoz kívánt tulajdonságainak szerkesztéséhez.
   
   Ha például magas prioritásúként kapcsolateseményei konfigurálása, és magas prioritású eseményeinek gyűjtése 7 percenként, az alábbi konfigurációt használja.
   
   ```json
    "desired": {
      "azureiot*com^securityAgentConfiguration^1*0*0": {
        "highPriorityMessageFrequency": "PT7M",    
        "eventPriorityConnectionCreate": "High" 
      } 
    }, 
    ```

1. Kattintson a **Save** (Mentés) gombra.

### <a name="using-a-default-value"></a>Alapértelmezett érték használata

Tulajdonság alapértelmezett értéke használatához távolítsa el a konfigurációs objektumot a tulajdonságot.

## <a name="default-properties"></a>Az alapértelmezett tulajdonságokat 

A következő táblázat tartalmazza az ASC vezérelhető tulajdonságainak IoT biztonsági ügynökök esetében.

Alapértelmezett értékek érhetők el a megfelelő sémát [Github](https://aka.ms/iot-security-module-default).

| Name (Név)| status | Érvényes értékek| Alapértelmezett értékek| Leírás |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Kötelező: False (hamis) |Érvényes értékek: ISO 8601 formátumú időtartama |Alapértelmezett érték: PT7M |Maximális idő előtt magas prioritású üzeneteket küldi el.|
|lowPriorityMessageFrequency |Kötelező: False (hamis)|Érvényes értékek: ISO 8601 formátumú időtartama |Alapértelmezett érték: PT5H |Maximális idő, alacsony prioritású üzenetek előtt küldi el.| 
|snapshotFrequency |Szükséges: False (hamis)|Érvényes értékek: időtartam ISO 8601 formátumban |Alapértelmezett érték PT13H |Eszköz állapota pillanatképek létrehozásának időintervallum.| 
|maxLocalCacheSizeInBytes |Kötelező: False (hamis) |Érvényes értékek: |Alapértelmezett érték: nagyobb, mint 8192 2560000 | Maximális tárterület (bájt) engedélyezett az ügynök üzeneteket tartalmazó gyorsítótárát. Terület üzenetek tárolásához az eszközön, mielőtt üzeneteket küld az engedélyezett maximális mérete.| 
|maxMessageSizeInBytes |Kötelező: False (hamis) |Érvényes értékek: Egy pozitív szám, 8192, kevesebb mint 262144-nál nagyobb |Alapértelmezett érték: 204800 |Maximális engedélyezett felhőüzenet ügynökök méretét. Ez a beállítás szabályozza a legnagyobb, az egyes üzenetekben küldött adatok mennyisége. |
|eventPriority$ {EventName} |Kötelező: False (hamis) |Érvényes értékek: Magas, alacsony kikapcsolva |Alapértelmezett értékeit: |Minden ügynök prioritását esemény jön létre. | 

### <a name="supported-security-events"></a>Támogatott biztonsági események

|Esemény neve| a propertyName | Alapértelmezett érték| Snapshot Event| Részletes állapota  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Diagnosztikai esemény|eventPriorityDiagnostic| Ki| False (Hamis)| Az ügynök kapcsolódó diagnosztikai események. Használja ezt az eseményt a részletes naplózás.| 
|Konfigurációs hiba |eventPriorityConfigurationError |Alacsony |False (Hamis) |Az ügynök nem tudta elemezni a konfigurációt. Ellenőrizze a konfigurációt a sémának.| 
|Az eldobott események statisztika |eventPriorityDroppedEventsStatistics |Alacsony |True (Igaz)|Az ügynök eseménystatisztika kapcsolatos. |
|Üzenet statisztika|eventPriorityMessageStatistics |Alacsony |True (Igaz) |Az ügynök kapcsolatos üzenet statisztikákat. |
|Csatlakoztatott hardver|eventPriorityConnectedHardware |Alacsony |True (Igaz) |Az eszközhöz csatlakoztatott minden hardveres pillanatképet.|
|Figyelőportjait|eventPriorityListeningPorts |Magas |True (Igaz) |Az eszközön lévő összes nyitott figyelőportjait pillanatképét.|
|Folyamat létrehozása |eventPriorityProcessCreate |Alacsony |False (Hamis) |Naplózás folyamat-létrehozás az eszközön.|
|Folyamat leállítása|eventPriorityProcessTerminate |Alacsony |False (Hamis) |Naplózás megszüntetése az eszközön feldolgozni.| 
|Rendszerinformáció |eventPrioritySystemInformation |Alacsony |True (Igaz) |Rendszer-információkat pillanatképet (például: Az operációs rendszer vagy a CPU).| 
|A helyi felhasználók| eventPriorityLocalUsers |Magas |True (Igaz)|A regisztrált helyi felhasználók belül a rendszer pillanatképet. |
|Bejelentkezés|  eventPriorityLogin |Magas|False (Hamis)|Az eszközön (helyi és távoli bejelentkezések) a bejelentkezési események naplózása.|
|Kapcsolat létrehozása |eventPriorityConnectionCreate|Alacsony|False (Hamis)|Naplózza a TCP-kapcsolatok létrehozása, és az eszközről. |
|Tűzfal-konfiguráció| eventPriorityFirewallConfiguration|Alacsony|True (Igaz)|Eszköz tűzfal-konfiguráció (tűzfalszabályok) pillanatképét. |
|Operációs rendszer| eventPriorityOSBaseline| Alacsony|True (Igaz)|Ellenőrizze a rendelkezésre álló eszköz OS pillanatkép.|
 

## <a name="next-steps"></a>További lépések

- [ASC megismerheti az IoT-javaslatok](concept-recommendations.md)
- [Fedezze fel az ASC IoT-riasztások](concept-security-alerts.md)
- [Hozzáférés nyers biztonsági adatok](how-to-security-data-access.md)
