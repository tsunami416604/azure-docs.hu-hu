---
title: Az Azure Security Center megismerése az IoT alapkonfigurációhoz| Microsoft dokumentumok
description: Ismerje meg az Azure Security Center for IoT alapkonfiguráció koncepcióját.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2019
ms.author: mlottner
ms.openlocfilehash: ce5a0625a16c5a02d03ee74f894c585820414fa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72176615"
---
# <a name="azure-security-center-for-iot-baseline-and-custom-checks"></a>Azure Security Center az IoT alapkonfigurációjának és egyéni ellenőrzésének

Ez a cikk ismerteti az Azure Security Center az IoT alapkonfigurációját, és összegzi az alapszintű egyéni ellenőrzések összes társított tulajdonságait.

## <a name="baseline"></a>Alapkonfiguráció

Az alapterv minden eszköz szabványos viselkedését hozza létre, és megkönnyíti a szokatlan viselkedés vagy a várt normáktól való eltérés megállapítását.  

## <a name="baseline-custom-checks"></a>Alaptervegyéni ellenőrzések

Az alapkonfigurációs egyéni ellenőrzések az eszköz alapkonfigurációjának ellenőrzéseit egyéni listát hoznak létre az eszköz **modulidentitásikerének** használatával. 

## <a name="setting-baseline-properties"></a>Alapvonal-tulajdonságok beállítása

1. Az IoT Hubban keresse meg és válassza ki a módosítani kívánt eszközt.
1. Kattintson az eszközre, majd kattintson az **azureiotsecurity** modulra.
1. Kattintson **a Modul identitás ikergombra.**
1. Töltse fel az **alapkonfiguráció egyéni csekkfájlját** az eszközre.
1. Adja hozzá az alapvonal tulajdonságait a biztonsági modulhoz, és kattintson a **Mentés gombra.**

### <a name="baseline-custom-check-file-example"></a>Példa alapszintű egyéni ellenőrzőfájlra

Alapszintű egyéni ellenőrzések konfigurálása:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>Alapszintű egyéni ellenőrzési tulajdonságok

| Név| status | Érvényes értékek| Alapértelmezett értékek| Leírás |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|baselineCustomChecksEnabled|Kötelező: igaz |Érvényes értékek: **Logikai érték** |Alapértelmezett érték: **false** |A magas prioritású üzenetek elküldése előtti maximális időintervallum.|
|baselineCustomChecksFilePath |Kötelező: igaz|Érvényes értékek: **Karakterlánc**, **null** |Alapértelmezett érték: **null** |A kiindulási XML-konfiguráció teljes elérési útja|
|baselineCustomChecksFileHash |Kötelező: igaz|Érvényes értékek: **Karakterlánc**, **null** |Alapértelmezett érték: **null** |`sha256sum`az XML konfigurációs fájl. További információkért használja a [sha256sum hivatkozást.](https://linux.die.net/man/1/sha256sum) |

További alaptervi példák áttekintéséhez tekintse meg a [-1 egyéni alaptervi példát](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) és [a -2 egyéni alaptervpéldát.](https://ascforiot.blob.core.windows.net/public/oms_audits.xml)

## <a name="next-steps"></a>További lépések

- A [nyers biztonsági adatok](how-to-security-data-access.md) elérése
- [Eszköz vizsgálata](how-to-investigate-device.md)
- A [biztonsági javaslatok](concept-recommendations.md) ismertetése és feltárása
- A [biztonsági riasztások ismertetése](concept-security-alerts.md) és feltárása
