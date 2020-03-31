---
title: Az Azure Security Center konfigurálása IoT-ügynökhöz| Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhatja az Azure Security Center for IoT biztonsági ügynökök et az Azure Security Center for IoT biztonsági szolgáltatáshoz való használatra.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: 70396cdcaf8b6e2ac66619290eea35a7b260cd9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461249"
---
# <a name="tutorial-configure-security-agents"></a>Oktatóanyag: Biztonsági ügynökök konfigurálása

Ez a cikk ismerteti az Azure Security Center for IoT biztonsági ügynökök, és részletesen, hogyan módosíthatja és konfigurálhatja őket. 

> [!div class="checklist"]
> * Biztonsági ügynökök konfigurálása
> * Az ügynök viselkedésének módosítása ikertulajdonságok szerkesztésével
> * Alapértelmezett konfiguráció felderítése

## <a name="agents"></a>Ügynökök

Az IoT biztonsági ügynökeinek Azure Security Center adatokat gyűjt IoT-eszközökről, és biztonsági műveleteket hajt végre az észlelt biztonsági rések csökkentése érdekében. A biztonsági ügynök konfigurációja a testre szabható ikermodul-tulajdonságok segítségével vezérelhető. Általában ezeka tulajdonságok másodlagos frissítései ritkán fordulnak elő.  

Az Azure Security Center for IoT biztonsági ügynök ikerkonfigurációs objektum egy JSON formátumú objektum. A konfigurációs objektum olyan szabályozható tulajdonságok készlete, amelyek az ügynök viselkedésének szabályozására határozhatók meg. 

Ezek a konfigurációk segítségével testre szabhatja az ügynököt az egyes szükséges forgatókönyvekhez. Például bizonyos események automatikus kizárása, vagy az energiafogyasztás minimális szinten tartása a tulajdonságok konfigurálásával lehetséges.  

Használja az Azure Security Center for IoT biztonsági ügynök [konfigurációs séma](https://aka.ms/iot-security-github-module-schema) a módosításokat.  

## <a name="configuration-objects"></a>Konfigurációs objektumok 

Az Azure Security Center for IoT biztonsági ügynökhöz kapcsolódó tulajdonságok az **azureiotsecurity** modul kívánt tulajdonságszakaszában található ügynök konfigurációs objektumban találhatók. 

A konfiguráció módosításához hozza létre és módosítsa ezt az objektumot az **azureiotsecurity** modul ikeridentitásában. 

Ha az ügynök konfigurációs objektum nem létezik az **azureiotsecurity** modul iker, minden biztonsági ügynök tulajdonság értékei alapértelmezettre vannak állítva. 

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  } 
}
```

## <a name="configuration-schema-and-validation"></a>Konfigurációs séma és érvényesítés 

Győződjön meg arról, hogy ellenőrizze az ügynök konfigurációját ezzel a [sémával.](https://aka.ms/iot-security-github-module-schema) Az ügynök nem indul el, ha a konfigurációs objektum nem felel meg a sémának.

 
Ha az ügynök futása közben a konfigurációs objektum érvénytelen konfigurációra változik (a konfiguráció nem felel meg a sémának), az ügynök figyelmen kívül hagyja az érvénytelen konfigurációt, és folytatja az aktuális konfiguráció t. 

### <a name="configuration-validation"></a>Konfiguráció ellenőrzése

Az Azure Security Center for IoT biztonsági ügynök jelenti az aktuális konfiguráció belül a jelentett tulajdonságok részben az **azureiotsecurity** modul ikeridentitás.
Az ügynök jelenti az összes elérhető tulajdonságot, ha a felhasználó nem állított be tulajdonságot, az ügynök jelenti az alapértelmezett konfigurációt.

A konfiguráció ellenőrzése érdekében hasonlítsa össze a kívánt szakaszban beállított értékeket a jelentett szakaszban jelentett értékekkel.

Ha eltérés van a kívánt és a jelentett tulajdonságok között, akkor az ügynök nem tudta elemezni a konfigurációt.

Érvényesítse a kívánt tulajdonságokat a [sémával](https://aka.ms/iot-security-github-module-schema)szemben, javítsa ki a hibákat, és állítsa be újra a kívánt tulajdonságokat!

> [!NOTE]
> Az ügynök konfigurációs hibariasztást küld az ügynöktől arra az esetre, ha az ügynök nem tudta elemezni a kívánt konfigurációt.
> A jelentett és a kívánt szakasz összehasonlítása annak megértéséhez, hogy a riasztás továbbra is érvényben van-e

## <a name="editing-a-property"></a>Tulajdonság szerkesztése 

Minden egyéni tulajdonságokat be kell állítani az ügynök konfigurációs objektum az **azureiotsecurity** modul iker.
Ha alapértelmezett tulajdonságértéket szeretne használni, távolítsa el a tulajdonságot a konfigurációs objektumból.

### <a name="setting-a-property"></a>Tulajdonság beállítása

1. Az IoT Hubban keresse meg és válassza ki a módosítani kívánt eszközt.

1. Kattintson az eszközre, majd az **azureiotsecurity** modulra.

1. Kattintson a **modul Identity Twin**.

1. A biztonsági modulban módosítani kívánt tulajdonságok szerkesztése.
   
   Ha például a kapcsolati eseményeket magas prioritásúként szeretné konfigurálni, és 7 percenként magas prioritású eseményeket szeretne gyűjteni, használja a következő konfigurációt.
   
    ```json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
            "highPriorityMessageFrequency": {
                "value": "PT7M"
            },
            "eventPriorityConnectionCreate": {
                "value": "High"
            }
        }
    }
    ```

1. Kattintson a **Mentés** gombra.

### <a name="using-a-default-value"></a>Alapértelmezett érték használata

Ha alapértelmezett tulajdonságértéket szeretne használni, távolítsa el a tulajdonságot a konfigurációs objektumból.

## <a name="default-properties"></a>Alapértelmezett tulajdonságok 

Az alábbi táblázat az Azure Security Center for IoT biztonsági ügynökök szabályozható tulajdonságait tartalmazza.

Az alapértelmezett értékek a [GitHub](https\://aka.ms/iot-security-module-default)megfelelő sémájában érhetők el.

| Név| status | Érvényes értékek| Alapértelmezett értékek| Leírás |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Kötelező: hamis |Érvényes értékek: Időtartam ISO 8601 formátumban |Alapértelmezett érték: PT7M |A magas prioritású üzenetek elküldése előtti maximális időintervallum.|
|lowPriorityMessageFrequency |Kötelező: hamis|Érvényes értékek: Időtartam ISO 8601 formátumban |Alapértelmezett érték: PT5H |Maximális idő az alacsony prioritású üzenetek küldése előtt.| 
|pillanatképgyakoriság |Megkövetelése: false|Érvényes értékek: Időtartam ISO 8601 formátumban |Alapértelmezett érték PT13H |Az eszköz állapotpillanatképei létrehozásának időintervalluma.| 
|maxLocalCacheSizeInBytes |Kötelező: hamis |Érvényes értékek: |Alapértelmezett érték: 2560000, nagyobb, mint 8192 | Az ügynök üzenetgyorsítótárának maximális tárolása (bájtban). Az üzenetek küldését megelőzően az üzenetek tárolására engedélyezett maximális terület.| 
|maxMessageSizeInBájt |Kötelező: hamis |Érvényes értékek: 8192-nél nagyobb pozitív szám, kevesebb, mint 262144 |Alapértelmezett érték: 204800 |Az ügynök maximálisan engedélyezett mérete a felhőalapú üzenetekhez. Ez a beállítás határozza meg az egyes üzenetekben küldött maximális adatmennyiséget. |
|eventPriority${EventName} |Kötelező: hamis |Érvényes értékek: Magas, Alacsony, Ki |Alapértelmezett értékek: |Minden ügynök által létrehozott esemény prioritása | 

### <a name="supported-security-events"></a>Támogatott biztonsági események

|Esemény neve| PropertyName | Alapértelmezett érték| Pillanatkép-esemény| Részletek állapota  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Diagnosztikai esemény|eventPriorityDiagnostic| Ki| False (Hamis)| Ügynökkel kapcsolatos diagnosztikai események. Ezt az eseményt részletes naplózáshoz használja.| 
|Konfigurációs hiba |eventPriorityConfigurationError hiba |Alacsony |False (Hamis) |Az ügynök nem tudta elemezni a konfigurációt. Ellenőrizze a konfigurációt a sémával szemben.| 
|Eldobott események statisztikái |eventPriorityDroppedEventsStatisztika |Alacsony |True (Igaz)|Ügynökkel kapcsolatos eseménystatisztika. |
|Csatlakoztatott hardver|eventPriorityConnectedHardver |Alacsony |True (Igaz) |Pillanatkép az eszközhöz csatlakoztatott összes hardverről.|
|Figyelő portok|eventPriorityListeningPorts |Magasság |True (Igaz) |Pillanatkép az eszköz összes megnyitott lehallgató portjáról.|
|Folyamat létrehozása |eventPriorityProcessCreate |Alacsony |False (Hamis) |Auditok folyamat létrehozása az eszközön.|
|Folyamat leállítása|eventPriorityProcessTerminate |Alacsony |False (Hamis) |A naplózás folyamatvégződést az eszközön.| 
|Rendszerinformáció |eventPrioritySystemInformation |Alacsony |True (Igaz) |Pillanatkép a rendszeradatokról (például: operációs rendszer vagy processzor).| 
|Helyi felhasználók| eventPriorityLocalUsers |Magasság |True (Igaz)|A rendszer regisztrált helyi felhasználóinak pillanatképe. |
|Bejelentkezés|  eventPriorityLogin |Magasság|False (Hamis)|Naplózza a bejelentkezési eseményeket az eszközön (helyi és távoli bejelentkezések).|
|Kapcsolat létrehozása |eventPriorityConnectionCreate|Alacsony|False (Hamis)|Az eszközre és az eszközről létrehozott TCP-kapcsolatok naplózása. |
|Tűzfal-konfiguráció| eventPriorityFirewallConfiguration|Alacsony|True (Igaz)|Pillanatkép az eszköztűzfal konfigurációjáról (tűzfalszabályok). |
|Operációs rendszer alapértéke| eventPriorityOSBaseline| Alacsony|True (Igaz)|Pillanatkép az eszköz operációs rendszerének alapkonfigurációjának ellenőrzéséről.|
|
 

## <a name="next-steps"></a>További lépések

- [Az Azure Security Center ismertetése az IoT-javaslatokhoz](concept-recommendations.md)
- [Fedezze fel az Azure Security Center for IoT-riasztásokat](concept-security-alerts.md)
- [Hozzáférés a nyers biztonsági adatokhoz](how-to-security-data-access.md)
