---
title: Azure IoT-összekötő a FHIR (IoT-összekötőhöz) – hibaelhárítási útmutató és útmutató
description: A IoT-összekötővel kapcsolatos gyakori hibaüzenetek és feltételek és a leképezési fájlok másolása
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jasteppe
ms.openlocfilehash: e74e3475256858955a5ab0dc99e7b858548e2485
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099394"
---
# <a name="iot-connector-preview-troubleshooting-guide-and-how-to"></a>IoT-összekötő (előzetes verzió) – hibaelhárítási útmutató és útmutató

Ebből a cikkből megtudhatja, hogyan lehet elhárítani a gyakori IoT-összekötő (előzetes verzió) hibaüzeneteit és feltételeit.

Azt is megtudhatja, hogyan hozhatja létre a IoT-összekötő (előzetes verzió) JSON-leképezésének másolatait.

## <a name="error-messages-and-fixes"></a>Hibaüzenetek és javítások

|Üzenet   |Feltétel  |Javítás         |
|----------|-----------|------------|
|Érvénytelen leképezési név, a hozzárendelés nevének eszköz-vagy FHIR kell lennie.|A megadott leképezési típus nem eszköz-vagy FHIR|Használja a két támogatott leképezési típus valamelyikét (például: eszköz vagy FHIR)|
|Nincs definiálva a kulcs paramétereinek újrakészítése|Kulcs kérésének újrakészítése|Paraméterek belefoglalása a Regeneration Key kérelembe|
|Elérte az előfizetésben üzembe helyezhető IoT-összekötők példányainak maximális számát|Elérte az IoT-összekötő előfizetési kvótáját (az alapértelmezett érték 2/előfizetés)|Törölje az egyik meglévő összekötőt, használjon egy másik előfizetést, amely nem érte el az előfizetési kvótához tartozó (2) összekötőt, vagy az előfizetési kvóta növelését kéri|
|Az erőforrás áthelyezése nem támogatott az IoT-összekötő Azure API FHIR-erőforráshoz való engedélyezése esetén|Egy vagy több IoT-összekötővel rendelkező FHIR-erőforrás áthelyezési műveletének kísérlete egy Azure API-val|Meglévő összekötők törlése az áthelyezési művelet (ek) végrehajtásához/befejezéséhez|
|A FHIR-erőforráshoz készült Azure API-nak engedélyezve van a saját hivatkozása.  A privát hivatkozás nem támogatott az IoT-összekötővel|Kísérlet egy IoT-összekötő Azure API-hoz való hozzáadására olyan FHIR-erőforráshoz, amelyhez engedélyezve van a privát kapcsolat|Válasszon ki vagy hozzon létre egy Azure API-t a FHIR-erőforráshoz (R4-es verzió), amelyhez nincs engedélyezve a privát hivatkozás|
|Az IoT-összekötő nincs kiépítve|Az alárendelt szolgáltatások (kapcsolatok & leképezések) használatának megkísérlése, ha a szülő (IoT-összekötő) nincs kiépítve|IoT-összekötő kiépítése|
|A kérelem nem támogatott|Az adott API-kérelem nem támogatott|A megfelelő API-kérelem használata|
|A fiók nem létezik|Nem létezik IoT-összekötő és az Azure API for FHIR-erőforrás hozzáadása|Hozza létre az Azure API-t a FHIR-erőforráshoz, majd próbálja megismételni a műveletet.|
|Az Azure API for FHIR erőforrás-FHIR verziója nem támogatott a IoT-összekötő esetében|Kísérlet egy IoT-összekötő használatára az Azure API inkompatibilis verziójával a FHIR-erőforráshoz|Hozzon létre egy új Azure API-t a FHIR-erőforráshoz (R4-es verzió), vagy használjon egy meglévő Azure API-t a FHIR-erőforráshoz

## <a name="creating-copies-of-the-iot-connector-preview-mapping-files"></a>A IoT-összekötő (előzetes verzió) leképezési fájljainak másolatainak létrehozása
> [!NOTE]
> A JSON az eszköz-és FHIR-hozzárendelési fájlok egyetlen támogatott formátuma.

> [!TIP]
> Az IoT-összekötők leképezési fájljainak másolása hasznos lehet az Azure Portal webhelyen kívüli szerkesztéshez és archiváláshoz, illetve a támogatási jegy megnyitásakor az Azure technikai támogatásának biztosításához.
> 
> További információ az IoT-összekötő [eszközről és a FHIR-leképezés JSON-fájljairól](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Válassza az **"IoT-összekötő (előzetes verzió)"** lehetőséget az Azure API FHIR erőforrás-irányítópultjának bal alsó részén a **"beépülő modulok"** szakaszban.

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT-összekötő" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Válassza ki azt az **"összekötőt"** , amelyről a leképezési fájlokat másolni kívánja.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT-összekötő" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

3. Válassza az **"eszköz-hozzárendelés konfigurálása"** lehetőséget.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT-összekötő" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

> [!NOTE]
> Ez a folyamat a **"FHIR-leképezés konfigurálása"** JSON-fájl tartalmának másolására és mentésére is használható.

4. Válassza ki a JSON tartalmát, és hajtson végre egy másolási műveletet (például: válassza a CTRL + c billentyűkombinációt). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT-összekötő" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Hajtson végre egy beillesztési műveletet (például a CTRL + v billentyűkombinációt) egy szerkesztőben egy új fájlba (például: Visual Studio Code, notepad), és mentse a fájlt egy *. JSON kiterjesztéssel.

> [!TIP]
> Ha a IoT-összekötőhöz [Azure technikai támogatási](https://azure.microsoft.com/support/create-ticket/) jegyet nyit meg, ügyeljen arra, hogy a leképezési fájlok másolatait tartalmazza a hibaelhárítási folyamat segítése érdekében.

## <a name="next-steps"></a>További lépések

Gyakori kérdések az IoT-összekötőről

>[!div class="nextstepaction"]
>[IoT-összekötő – gyakori kérdések](fhir-faq.md#iot-connector-preview)


Az FHIR a HL7 bejegyzett védjegye, amelynek felhasználását a HL7 engedélyezte.