---
title: Folyamatos fekvőbeteg-figyelési architektúra az Azure IoT Centralban | Microsoft Docs
description: Oktatóanyag – Ismerje meg a folyamatos beteg monitorozási megoldás architektúráját.
author: philmea
ms.author: philmea
ms.date: 09/14/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: ffecd09d1084188195da83568ab3fe32ef2cdaac
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90972223"
---
# <a name="continuous-patient-monitoring-architecture"></a>A folyamatos betegfigyelés architektúrája

Ez a cikk a **folyamatos beteg monitorozási** alkalmazás sablonjában létrehozott megoldások architektúráját ismerteti:

A folyamatos fekvőbeteg-figyelési megoldások a megadott alkalmazás-sablonnal, valamint az alább ismertetett architektúra használatával használhatók.

:::image type="content" source="media/cpm-architecture.png" alt-text="A folyamatos betegfigyelés architektúrája":::

## <a name="details"></a>Részletek

Ez a szakasz részletesebben ismerteti az architektúra-diagram egyes részeit:

### <a name="bluetooth-low-energy-ble-medical-devices"></a>Bluetooth alacsony energiafogyasztású (egypontos) orvostechnikai eszközök

Az egészségügyi IoT-megoldásokban használt számos orvosi eszköz a készülék. Ezek az eszközök nem tudnak közvetlenül kommunikálni a felhővel, és átjáróval kell használniuk az adatcserét a felhőalapú megoldással. Ez az architektúra egy mobiltelefon-alkalmazást használ átjáróként.

### <a name="mobile-phone-gateway"></a>Mobile Phone-átjáró

A mobiltelefonos alkalmazás elsődleges funkciója az, hogy az orvostechnikai eszközökből származó adatokat gyűjtsön, és IoT Central kommunikáljon. Az alkalmazás a betegeket az eszköz beállításán keresztül is irányítja, és lehetővé teszi a személyes állapotadatok megtekintését. Más megoldások is használhatnak táblaszámítógép-átjárót vagy statikus átjárót egy kórházi helyiségben. Az Android és az iOS rendszerhez készült nyílt forráskódú minta Mobile-alkalmazás kiindulási pontként használható az alkalmazások fejlesztéséhez. További információért lásd a [IoT Central folyamatos beteg monitorozása Mobile alkalmazást](https://docs.microsoft.com/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/).

### <a name="export-to-azure-api-for-fhirreg"></a>Exportálás az Azure API-ba FHIR&reg;

Az Azure IoT Central HIPAA-kompatibilis és HITRUST &reg; tanúsítvánnyal rendelkezik. A [FHIR készült Azure API](../../healthcare-apis/overview.md)használatával más szolgáltatásokhoz is küldhet beteg-egészségügyi adataikat. A FHIR készült Azure API egy szabványon alapuló API a klinikai egészségügyi adatkezeléshez. A [FHIR készült Azure IoT-összekötő](https://docs.microsoft.com/azure/healthcare-apis/iot-fhir-portal-quickstart) lehetővé teszi, hogy a FHIR Azure API-ját folyamatos adatexportálási célként használja IoT Central.

### <a name="machine-learning"></a>Gépi tanulás

A gépi tanulási modellek segítségével FHIR-adataival elemzési és támogatási döntéseket hozhat a Care csapata számára. További információt az [Azure Machine learning dokumentációjában](../../machine-learning/index.yml)talál.

### <a name="provider-dashboard"></a>Szolgáltatói irányítópult

Az Azure API-val FHIR-adatokkal egy beteg-elemzési irányítópultot hozhat létre, vagy integrálhatja azt közvetlenül egy Care Teams által használt elektronikus orvosi rekordba. A Care Teams az irányítópultot használva segíti a betegeket, és azonosíthatja a korai előrejelző jeleinek romlását. További tudnivalókat a Power BI- [szolgáltatói irányítópult létrehozása](howto-health-data-triage.md) című oktatóanyagban talál.

## <a name="next-steps"></a>Következő lépések

A következő lépés az, hogy [megtudja, hogyan helyezhet üzembe egy folyamatos beteg monitorozási alkalmazás sablonját](tutorial-continuous-patient-monitoring.md).
