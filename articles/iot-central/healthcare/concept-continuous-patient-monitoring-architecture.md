---
title: Folyamatos betegfigyelési architektúra az Azure IoT Centralban | Microsoft dokumentumok
description: Ismerje meg a folyamatos betegfigyelési megoldásarchitektúrát.
author: philmea
ms.author: philmea
ms.date: 10/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 92eb4157abb55b7056952d1fb064c7c7d7500335
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77021696"
---
# <a name="continuous-patient-monitoring-architecture"></a>A folyamatos betegfigyelés architektúrája



Folyamatos betegfigyelési megoldások a rendelkezésre álló alkalmazássablon használatával és az alábbiakban ismertetett architektúrával is létrehozható útmutatásként.

>[!div class="mx-imgBorder"] 
>![CPM architektúra](media/cpm-architecture.png)

1. Alacsony energiaigényű (BLE) Bluetooth-energiát használó orvostechnikai eszközök
1. Ble-adatokat fogadó és az IoT Central ba küldött mobiltelefon-átjáró
1. A betegek állapotának folyamatos exportálása az FHIR Azure API-ba&reg;
1. Interoperábilis adatokon alapuló gépi tanulás
1. Care csapat műszerfal épül FHIR adatok

## <a name="details"></a>Részletek
Ez a szakasz részletesebben ismerteti az architektúradiagram egyes részeinek.

### <a name="ble-medical-devices"></a>BLE orvostechnikai eszközök
Az egészségügyi IoT-térben használt számos orvosi viselhető eszköz Bluetooth Low Energy eszköz. Nem tudnak közvetlenül beszélni a felhővel, és át kell haladniuk egy átjárón. Ez az architektúra azt javasolja, hogy egy mobiltelefon-alkalmazást használjanak átjáróként.

### <a name="mobile-phone-gateway"></a>Mobiltelefon átjáró
A mobiltelefon-alkalmazás elsődleges funkciója a BLE-adatok betöltése az orvostechnikai eszközökről, és közli azokat az Azure IoT Centralszolgáltatással. Emellett az alkalmazás segíthet a betegeknek az eszköz beállítási és kiépítési folyamatán keresztül, és segít nekik a személyes egészségügyi adataik megtekintésében. Más megoldások táblagép-átjárót vagy statikus átjárót is használhatnak, ha egy kórházi helyiségben ugyanazt a kommunikációs folyamatot érik el.

### <a name="export-to-azure-api-for-fhirreg"></a>Exportálás az FHIR-hez az Azure API-ba&reg;
Az Azure IoT Central HIPAA-kompatibilis és HITRUST&reg; tanúsítvánnyal rendelkezik, de előfordulhat, hogy a betegek állapotával kapcsolatos adatokat is el szeretne küldeni az FHIR Azure API-jának. [Az Azure API for FHIR](../../healthcare-apis/overview.md) egy teljes körűen felügyelt, szabványokon alapuló, megfelelő API a klinikai egészségügyi adatokhoz, amely lehetővé teszi, hogy új, az egészségügyi adatokkal való elköteleződési rendszereket hozzon létre. Lehetővé teszi az adatok gyors cseréjét az FHIR API-kon keresztül, amelyet egy felügyelt Platform-as-a-Service (PaaS) kínál a felhőben. Az IoT Central folyamatos adatexportálási funkciójával adatokat küldhet az FHIR Azure API-jának.

### <a name="machine-learning"></a>Gépi tanulás
Az adatok összesítése és FHIR formátumba fordítása után gépi tanulási modelleket hozhat létre, amelyek gazdagíthatják az elemzéseket, és intelligensebb döntéshozatalt tesznek lehetővé az ápolási csapat számára. Vannak különböző típusú szolgáltatások, amelyek gépi tanulási modellek létrehozásához, betanításához és üzembe helyezéséhez használható. Az Azure gépi tanulási ajánlatai használatáról a [gépi tanulási dokumentációban](../../machine-learning/index.yml)olvashat bővebben.

### <a name="provider-dashboard"></a>Szolgáltató irányítópultja
Az Azure API-fhir-hez található adatok felhasználhatók a betegek elemzési irányítópultjának létrehozásához, vagy közvetlenül integrálhatók az EMR-be, hogy segítsenek a betegállapot megjelenítésében. Az ápolási csapatok ezt a műszerfalat használhatják a segítségre szoruló betegek gondozására, és észlelhetik a romlás korai figyelmeztető jeleit. Ha meg szeretné tudni, hogyan hozhat létre valós idejű Power BI-szolgáltatói irányítópultot, olvassa el [útmutatónkat.](howto-health-data-triage.md)

## <a name="next-steps"></a>További lépések
* [Ismerje meg, hogyan helyezhet üzembe folyamatos betegfigyelési alkalmazássablont](tutorial-continuous-patient-monitoring.md)