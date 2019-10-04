---
title: Azure Security Center konfigurálása a IoT-ügynökhöz | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az ügynököket a IoT Azure Security Center való használatra.
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
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 095c8fa080d96c9dc6d40261ee5afc559e9ca06b
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933884"
---
# <a name="tutorial-configure-security-agents"></a>Oktatóanyag: Biztonsági ügynökök konfigurálása

Ez a cikk ismerteti a IoT biztonsági ügynökök Azure Security Centerét, valamint az azok módosításának és konfigurálásának részleteit. 

> [!div class="checklist"]
> * Biztonsági ügynökök konfigurálása
> * Az ügynök viselkedésének módosítása a Twin tulajdonságok szerkesztésével
> * Alapértelmezett konfiguráció felderítése

## <a name="agents"></a>Ügynökök

A IoT biztonsági ügynökök Azure Security Center adatokat gyűjtenek a IoT-eszközökről, és biztonsági műveleteket hajtanak végre az észlelt biztonsági rések enyhítése érdekében. A biztonsági ügynök konfigurációja a testre szabható modulok különálló tulajdonságainak együttes használatával vezérelhető. Általánosságban elmondható, hogy a tulajdonságok másodlagos frissítései nem ritkák.  

Azure Security Center a IoT biztonsági ügynökének Twin konfigurációs objektuma egy JSON formátumú objektum. A konfigurációs objektum olyan ellenőrizhető tulajdonságok halmaza, amelyek segítségével szabályozhatja az ügynök viselkedését. 

Ezek a konfigurációk segítenek testreszabni az ügynököt minden szükséges forgatókönyvhöz. Például automatikusan kizárhat néhány eseményt, vagy megtarthatja az energiafogyasztást egy minimális szintre. Ehhez konfigurálja ezeket a tulajdonságokat.  

A módosítások végrehajtásához használja a IoT biztonsági ügynök konfigurációs [sémájának](https://aka.ms/iot-security-github-module-schema) Azure Security Center.  

## <a name="configuration-objects"></a>Konfigurációs objektumok 

A IoT biztonsági ügynök minden Azure Security Centeréhez kapcsolódó tulajdonságok az ügynök konfigurációs objektumában, a kívánt tulajdonságok szakaszban találhatók, az **azureiotsecurity** modulban. 

A konfiguráció módosításához hozza létre és módosítsa ezt az objektumot a **azureiotsecurity** modul kettős identitásán belül. 

Ha az ügynök konfigurációs objektuma nem létezik a **azureiotsecurity** modulban, az összes biztonsági ügynök tulajdonság értéke alapértelmezett értékre van állítva. 

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  } 
}
```

## <a name="configuration-schema-and-validation"></a>Konfigurációs séma és érvényesítés 

Győződjön meg arról, hogy a [séma](https://aka.ms/iot-security-github-module-schema)alapján érvényesíti az ügynök konfigurációját. Az ügynök nem indul el, ha a konfigurációs objektum nem felel meg a sémának.

 
Ha az ügynök futása közben a konfigurációs objektum nem érvényes konfigurációra változik (a konfiguráció nem felel meg a sémának), az ügynök figyelmen kívül hagyja az érvénytelen konfigurációt, és továbbra is az aktuális konfigurációt fogja használni. 

### <a name="configuration-validation"></a>Konfiguráció ellenőrzése

A IoT biztonsági ügynök Azure Security Center a **azureiotsecurity** modul kettős identitásának jelentett tulajdonságok szakaszának aktuális konfigurációját jelenti.
Az ügynök jelentést készít az összes elérhető tulajdonságról, ha egy tulajdonságot nem a felhasználó állított be, az ügynök az alapértelmezett konfigurációt jelenti.

A konfiguráció érvényesítéséhez hasonlítsa össze a kívánt szakaszban megadott értékeket a jelentett szakaszban bemutatott értékekkel.

Ha a kívánt és a jelentett tulajdonságok között eltérés van, akkor az ügynök nem tudta elemezni a konfigurációt.

Ellenőrizze a kívánt tulajdonságokat a [sémán](https://aka.ms/iot-security-github-module-schema), javítsa ki a hibákat, és állítsa be újra a kívánt tulajdonságokat.

> [!NOTE]
> Egy konfigurációs hiba riasztása az ügynöktől származik, ha az ügynök nem tudta elemezni a kívánt konfigurációt.
> A jelentett és a kívánt szakasz összevetése annak megismeréséhez, hogy a riasztás továbbra is érvényes-e

## <a name="editing-a-property"></a>Tulajdonság szerkesztése 

Az összes egyéni tulajdonságot be kell állítani az ügynök konfigurációs objektumán belül az **azureiotsecurity** modul Twin-ben.
Az alapértelmezett tulajdonság értékének használatához távolítsa el a tulajdonságot a konfigurációs objektumból.

### <a name="setting-a-property"></a>Tulajdonság beállítása

1. A IoT Hub keresse meg és válassza ki a módosítani kívánt eszközt.

1. Kattintson az eszközre, majd a **azureiotsecurity** modulban.

1. Kattintson a **modul Identity Twin**elemre.

1. Szerkessze a biztonsági modulban módosítani kívánt tulajdonságokat.
   
   Ha például a kapcsolódási eseményeket magas prioritással szeretné konfigurálni, és 7 percenként magas prioritású eseményeket szeretne gyűjteni, használja a következő konfigurációt.
   
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

1. Kattintson a **Save** (Mentés) gombra.

### <a name="using-a-default-value"></a>Alapértelmezett érték használata

Az alapértelmezett tulajdonság értékének használatához távolítsa el a tulajdonságot a konfigurációs objektumból.

## <a name="default-properties"></a>Alapértelmezett tulajdonságok 

A következő táblázat a IoT biztonsági ügynökök Azure Security Centerének ellenőrizhető tulajdonságait tartalmazza.

Az alapértelmezett értékek a [GitHub](https\://aka.ms/iot-security-module-default)megfelelő sémájában érhetők el.

| Name (Név)| State | Érvényes értékek| Alapértelmezett értékek| Leírás |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Kötelező: hamis |Érvényes értékek: Időtartam ISO 8601 formátumban |Alapértelmezett érték: PT7M |A magas prioritású üzenetek küldése előtti maximális időtartam.|
|lowPriorityMessageFrequency |Kötelező: hamis|Érvényes értékek: Időtartam ISO 8601 formátumban |Alapértelmezett érték: PT5H |Az alacsony prioritású üzenetek küldésének maximális ideje.| 
|snapshotFrequency |Kötelező: false|Érvényes értékek: Időtartam ISO 8601 formátumban |Alapértelmezett érték PT13H |Az Eszközállapot-Pillanatképek létrehozásának időintervalluma.| 
|maxLocalCacheSizeInBytes |Kötelező: hamis |Érvényes értékek: |Alapértelmezett érték: 2560000, nagyobb, mint 8192 | Az ügynök üzenet-gyorsítótárához engedélyezett maximális tárterület (bájtban). Az üzenetek elküldése előtt az eszközön tárolt üzenetek maximális mérete engedélyezett.| 
|maxMessageSizeInBytes |Kötelező: hamis |Érvényes értékek: 8192-nál nagyobb pozitív szám, amely kisebb, mint 262144 |Alapértelmezett érték: 204800 |Az ügynök maximálisan megengedett mérete a Felhőbeli üzenetben. Ezzel a beállítással szabályozható az egyes üzenetekben elküldött maximális adatmennyiség. |
|eventPriority $ {EventName} |Kötelező: hamis |Érvényes értékek: Magas, alacsony, kikapcsolt |Alapértelmezett értékek: |Minden ügynök által generált esemény prioritása | 

### <a name="supported-security-events"></a>Támogatott biztonsági események

|Esemény neve| a propertyName | Alapértelmezett érték| Pillanatkép-esemény| Részletek állapota  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Diagnosztikai esemény|eventPriorityDiagnostic| Ki| False (Hamis)| Ügynökkel kapcsolatos diagnosztikai események. Ezt az eseményt részletes naplózásra használhatja.| 
|Konfigurációs hiba |eventPriorityConfigurationError |Alacsony |False (Hamis) |Az ügynök nem tudta elemezni a konfigurációt. Ellenőrizze a konfigurációt a sémán.| 
|Eldobott események statisztikája |eventPriorityDroppedEventsStatistics |Alacsony |True|Ügynökkel kapcsolatos események statisztikája. |
|Üzenet statisztikája|eventPriorityMessageStatistics |Alacsony |True |Ügynökkel kapcsolatos üzenet statisztikája. |
|Csatlakoztatott hardver|eventPriorityConnectedHardware |Alacsony |True |Az eszközhöz csatlakoztatott összes hardver pillanatképe.|
|Portok figyelése|eventPriorityListeningPorts |Magas |True |Az eszközön futó összes nyitott figyelő port pillanatképe.|
|Folyamat létrehozása |eventPriorityProcessCreate |Alacsony |False (Hamis) |Naplózza a folyamat létrehozását az eszközön.|
|Folyamat leállítása|eventPriorityProcessTerminate |Alacsony |False (Hamis) |Naplózza a folyamat befejezését az eszközön.| 
|Rendszerinformáció |eventPrioritySystemInformation |Alacsony |True |A rendszerinformációk pillanatképe (például: Operációs rendszer vagy CPU).| 
|Helyi felhasználók| eventPriorityLocalUsers |Magas |True|A rendszeren belüli regisztrált helyi felhasználók pillanatképe. |
|Bejelentkezés|  eventPriorityLogin |Magas|False (Hamis)|Naplózza a bejelentkezési eseményeket az eszközre (helyi és távoli bejelentkezések).|
|Kapcsolatok létrehozása |eventPriorityConnectionCreate|Alacsony|False (Hamis)|Az eszközre és az eszközről létrehozott TCP-kapcsolatok naplózása. |
|Tűzfal konfigurációja| eventPriorityFirewallConfiguration|Alacsony|True|Az eszköz tűzfal-konfigurációjának pillanatképe (tűzfalszabályok). |
|OPERÁCIÓSRENDSZER-alapterv| eventPriorityOSBaseline| Alacsony|True|Az eszköz operációs rendszer alapkonfigurációjának vizsgálatának pillanatképe.|
|
 

## <a name="next-steps"></a>További lépések

- [A IoT-javaslatok Azure Security Center megismerése](concept-recommendations.md)
- [A IoT-riasztások Azure Security Center megismerése](concept-security-alerts.md)
- [A nyers biztonsági adatértékek elérése](how-to-security-data-access.md)
