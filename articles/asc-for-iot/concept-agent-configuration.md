---
title: Konfigurálja egy IoT-ügynök előzetes ASC |} A Microsoft Docs
description: Megtudhatja, hogyan használható az ügynökök konfigurálására az ASC az IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.service: ascforiot
ms.devlang: na
ms.topic: concept
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 3a3806c73ef254abab91bd28cd8761917371235f
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541917"
---
# <a name="configure-security-agents"></a>Biztonsági ügynökök konfigurálása

> [!IMPORTANT]
> Az IoT ASC jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió nélkül egy szolgáltatói szerződést, és nem javasolt éles számítási feladatok esetében. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk bemutatja, hogyan használható az ügynök konfigurálása az ASC IoT magyarázattal szolgál.

## <a name="agents"></a>Ügynökök

ASC IoT biztonsági ügynökök esetében adatok gyűjtése IoT-eszközökről, és hajtsa végre az észlelt biztonsági rések elhárításához biztonsági műveleteket. Biztonsági ügynök konfigurációs testre szabhatja a modul ikertulajdonságok használatával vezérelhető. Általánosságban elmondható hogy ezek a Tulajdonságok másodlagos frissítések ritkák.  

ASC IoT a biztonsági ügynök ikereszköz konfigurációs objektum számára egy egy .json formátumú objektum. A konfigurációs objektum az ügynök viselkedését vezérlő meghatározó vezérelhető tulajdonságait. 

Ezek a beállítások segítségével testre szabhatja az ügynök az egyes helyzetekhez szükséges. Például automatikusan a kivételével az egyes események vagy energiafogyasztás minimális szinten tartása is ezek a tulajdonságok konfigurálásával.  

Az ASC használja az IoT biztonsági ügynökkonfiguráció [séma](https://github.com/azure/asc-for-iot-schemas/security/module/twin) módosításokat.  

## <a name="configuration-objects"></a>Konfigurációs objektumok 

Minden egyes IoT biztonsági ügynök ASC kapcsolódó tulajdonság az ügynök konfigurációs objektumot, kívánt tulajdonságok szakaszában a azureiotsecurity modul belül található. 

A konfiguráció módosításához hozzon létre, és módosíthatja ezt az objektumot a azureiotsecurity modul ikereszköz identitásának belülre. Ha az ügynök konfigurációs objektum nem létezik a azureiotsecurity ikermodulja, a rendszer az összes biztonsági ügynök tulajdonságértékek alapértelmezettként beállítva. 

```json
"desired": { //azureiotsecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //Agent configuration object 
  … 
} 
}
```

Ellenőrizze, hogy a az ügynök konfigurációs módosítások ellen [séma](https://aka.ms/iot-security-github-module-schema).
Az ügynök nem indulnak el, ha a konfigurációs objektum nem egyezik meg a séma.


## <a name="editing-a-property"></a>Egy tulajdonság szerkesztése 

Az ügynök konfigurációs objektumban azureiotsecurity ikermodulja belül az összes egyéni tulajdonság kell beállítani. 

A következő tulajdonság beállítása felülbírálja az alapértelmezett érték. Tulajdonság beállításához adja hozzá a konfigurációs objektum a kívánt értéket a tulajdonság kulcsot. 

Tulajdonság alapértelmezett értéke használatához távolítsa el a konfigurációs objektumot a tulajdonságot. 

```json
"desired": { //azureiotsecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //ASC for IoT Agent 
      // configuration section  
    "lowPriorityMessageFrequency": "PT1H",     
    "highPriorityMessageFrequency": "PT7M",    
    "eventPriorityFirewallConfiguration": "High",     
    "eventPriorityConnectionCreate": "High" 
  } 
}, 
```

## <a name="default-properties"></a>Az alapértelmezett tulajdonságokat 
Az ASC vezérlő IoT biztonsági ügynökök esetében vezérelhető tulajdonságok készlete.

Alapértelmezett értékek érhetők el a megfelelő sémát [Github](https://aka.ms/iot-security-module-default).

| Name (Név)| status | Érvényes értékek| Alapértelmezett értékek| Leírás |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Kötelező: False (hamis) |Érvényes értékek: ISO 8601 formátumú időtartama |Alapértelmezett érték: PT7M |Maximális idő előtt magas prioritású üzeneteket küldi el.|
|lowPriorityMessageFrequency |Kötelező: False (hamis)|Érvényes értékek: ISO 8601 formátumú időtartama |Alapértelmezett érték: PT5H |Maximális idő, alacsony prioritású üzenetek előtt küldi el.| 
|snapshotFrequency |Szükséges: False (hamis)|Érvényes értékek: időtartam ISO 8601 formátumban |Alapértelmezett érték PT13H |Eszköz állapota pillanatképek létrehozásának időintervallum.| 
|maxLocalCacheSizeInBytes |Kötelező: False (hamis) |Érvényes értékek: |Alapértelmezett érték: nagyobb, mint 8192 2560000 | Maximális tárterület (bájt) engedélyezett az ügynök üzeneteket tartalmazó gyorsítótárát. Terület üzenetek tárolásához az eszközön, mielőtt üzeneteket küld az engedélyezett maximális mérete.| 
|maxMessageSizeInBytes |Kötelező: False (hamis) |Érvényes értékek: Egy pozitív szám, 8192, kevesebb mint 262144-nál nagyobb |Alapértelmezett érték: 204800 |Maximális engedélyezett felhőüzenet ügynökök méretét. Ez a beállítás szabályozza a legnagyobb, az egyes üzenetekben küldött adatok mennyisége. |
|eventPriority$ {EventName} |Kötelező: False (hamis) |Érvényes értékek: Magas, alacsony kikapcsolva |Alapértelmezett értékeit: |Minden ügynök prioritását esemény jön létre. | 

### <a name="events"></a>Események

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
 

## <a name="see-also"></a>Lásd még:

- [ASC megismerheti az IoT-javaslatok](concept-recommendations.md)
- [Fedezze fel az ASC IoT-riasztások](concept-security-alerts.md)
- [Hozzáférés nyers biztonsági adatok](how-to-security-data-access.md)