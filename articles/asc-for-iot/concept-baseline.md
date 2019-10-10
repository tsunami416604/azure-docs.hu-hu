---
title: A IoT alapkonfigurációjának Azure Security Center ismertetése | Microsoft Docs
description: Ismerje meg a IoT alapkonfigurációjának Azure Security Center koncepcióját.
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
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176615"
---
# <a name="azure-security-center-for-iot-baseline-and-custom-checks"></a>A IoT alapkonfigurációjának és egyéni ellenőrzésének Azure Security Center

Ez a cikk a IoT alapkonfigurációjának Azure Security Center ismerteti, és összefoglalja az alapkonfiguráció egyéni ellenőrzésének összes társított tulajdonságát.

## <a name="baseline"></a>Alapterv

Az alapkonfiguráció minden eszköz esetében szabványos viselkedést hoz létre, és megkönnyíti a szokatlan viselkedést vagy a várt normáktól való eltérést.  

## <a name="baseline-custom-checks"></a>Alapkonfiguráció egyéni ellenőrzése

Az alapkonfiguráció egyéni ellenőrzése az eszközhöz tartozó **Identity Twin** használatával egyéni ellenőrzési listát hoz létre az egyes eszközök alapértékei számára. 

## <a name="setting-baseline-properties"></a>Alapterv tulajdonságainak beállítása

1. A IoT Hub keresse meg és válassza ki a módosítani kívánt eszközt.
1. Kattintson az eszközre, majd kattintson a **azureiotsecurity** modulra.
1. Kattintson a **modul Identity Twin**elemre.
1. Töltse fel az alapkonfiguráció **Egyéni ellenőrzési** fájlját az eszközre.
1. Adja hozzá az alapkonfiguráció tulajdonságait a biztonsági modulhoz, és kattintson a **Mentés**gombra.

### <a name="baseline-custom-check-file-example"></a>Példa alapterv egyéni ellenőrzési fájlra

Az alapkonfiguráció egyéni ellenőrzésének konfigurálása:

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

## <a name="baseline-custom-check-properties"></a>Alapterv egyéni ellenőrzési tulajdonságai

| Név| Állapot | Érvényes értékek| Alapértelmezett értékek| Leírás |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|baselineCustomChecksEnabled|Kötelező: igaz |Érvényes értékek: **Boolean** |Alapértelmezett érték: **false** |A magas prioritású üzenetek küldése előtti maximális időtartam.|
|baselineCustomChecksFilePath |Kötelező: igaz|Érvényes értékek: **karakterlánc**, **Null** |Alapértelmezett érték: **Null** |Az eredeti XML-konfiguráció teljes elérési útja|
|baselineCustomChecksFileHash |Kötelező: igaz|Érvényes értékek: **karakterlánc**, **Null** |Alapértelmezett érték: **Null** |@no__t – 0 az XML-konfigurációs fájlból. További információkért használja az [sha256sum-referenciát](https://linux.die.net/man/1/sha256sum) . |

További alapkonfigurációk áttekintéséhez tekintse meg az [Egyéni Alapterv – 1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) . és az [Egyéni alapterv – 2. példát](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Következő lépések

- A [nyers biztonsági adataihoz](how-to-security-data-access.md) való hozzáférés
- [Eszköz vizsgálata](how-to-investigate-device.md)
- A [biztonsági javaslatok](concept-recommendations.md) megismerése és megismerése
- A [biztonsági riasztások](concept-security-alerts.md) megismerése és megismerése
