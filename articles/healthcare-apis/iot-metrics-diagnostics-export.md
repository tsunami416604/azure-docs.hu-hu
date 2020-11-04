---
title: Az Azure IoT-összekötő exportálása a FHIR (előzetes verzió) mérőszámai számára a diagnosztikai beállítások segítségével
description: Ez a cikk bemutatja, hogyan exportálhatja az Azure IoT Connectort a FHIR (előzetes verzió) Mérőszámokhoz a diagnosztikai beállítások segítségével
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/30/2020
ms.author: jasteppe
ms.openlocfilehash: 2c10dc05775739a55267dd3766e533b73e12b3a1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322356"
---
# <a name="export-azure-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>Az Azure IoT-összekötő exportálása a FHIR (előzetes verzió) mérőszámai számára a diagnosztikai beállítások segítségével

Ebből a cikkből megtudhatja, hogyan exportálhatja az Azure IoT Connectort az FHIR * mérőszámok naplóiba. A metrikák naplózását lehetővé tevő szolgáltatás a Azure Portal [**diagnosztikai beállításai**](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) . 

> [!TIP]
> A naplózási naplózás beállításához kövesse az [Azure API diagnosztikai naplózásának engedélyezése az FHIR és az Azure IoT connectorhoz](enable-diagnostic-logging.md#enable-diagnostic-logging-in-azure-api-for-fhir) című témakör útmutatását.

## <a name="enable-metrics-logging-for-the-azure-iot-connector-for-fhir-preview"></a>Metrikák naplózásának engedélyezése a FHIR készült Azure IoT-összekötőhöz (előzetes verzió)
1. A FHIR Azure IoT-összekötő metrikáinak naplózásának engedélyezéséhez válassza ki az Azure API-t a FHIR szolgáltatáshoz a Azure Portal 

2. Navigáljon a **diagnosztikai beállításokhoz** 

3. Válassza a **+ diagnosztikai beállítás hozzáadása** lehetőséget

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. Adjon meg egy nevet a **diagnosztikai beállítások neve** párbeszédpanelen.

5. Válassza ki a diagnosztikai naplók eléréséhez használni kívánt módszert:

    1. Archiválás vagy manuális ellenőrzés a **Storage-fiókba** . A használni kívánt Storage-fióknak már létre kell hoznia.
    2. **Adatfolyam küldése az Event hub** -nak egy harmadik féltől származó szolgáltatás vagy egyéni analitikai megoldás betöltéséhez. Ennek a lépésnek a konfigurálásához létre kell hoznia egy Event hub-névteret és egy Event hub-házirendet.
    3. **Stream a Azure Monitor log Analytics** munkaterületére. Ennek a lehetőségnek a kiválasztásához létre kell hoznia a naplók Analytics-munkaterületet.

6. A FHIR Azure IoT-összekötőhöz tartozó **hibák, forgalom és késés** kiválasztása.  Válassza ki a FHIR Azure API-hoz rögzíteni kívánt további metrikai kategóriákat.

7. Válassza a **Mentés** lehetőséget.

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT Connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> Akár 15 percet is igénybe vehet, hogy az első mérőszámok naplói megjelenjenek az Ön által választott adattárban.  
 
A diagnosztikai naplók használatával kapcsolatos további információkért tekintse meg az [Azure Resource log dokumentációját](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-overview) .

## <a name="conclusion"></a>Összegzés 
A metrikák naplóihoz való hozzáférés elengedhetetlen a figyeléshez és a hibaelhárításhoz.  A FHIR készült Azure IoT Connector lehetővé teszi ezeket a műveleteket a metrikák naplóin keresztül. 

## <a name="next-steps"></a>Következő lépések

Tekintse meg a FHIR készült Azure IoT-összekötővel kapcsolatos gyakori kérdéseket.

>[!div class="nextstepaction"]
>[Azure IoT-összekötő FHIR – gyakori kérdések](fhir-faq.md)

* A Azure Portal a FHIR készült Azure IoT-összekötő a IoT-összekötő (előzetes verzió) néven ismert.

Az FHIR a HL7 bejegyzett védjegye, amelynek felhasználását a HL7 engedélyezte.
