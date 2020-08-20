---
title: Diagnosztika beállítása
titleSuffix: Azure Digital Twins
description: 'Lásd: a naplózás engedélyezése a diagnosztikai beállításokkal.'
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 5091edbf9138cb8ff03df193dcbeed692aaf13e3
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612401"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Azure Digital Twins hibaelhárítása: diagnosztika naplózása

Az Azure Digital Twins a szolgáltatási példány [metrikáit](troubleshoot-metrics.md) gyűjti, amelyek információkat biztosítanak az erőforrások állapotáról. Ezeket a mérőszámokat felhasználhatja az Azure Digital Twins szolgáltatás általános állapotának felmérésére és a hozzájuk kapcsolódó erőforrásokra. Ezek a felhasználók felé irányuló statisztikák segítenek megtekinteni, hogy mi történik az Azure-beli digitális Ikrekben, és segítsen a problémák kiváltó okának elemzésében anélkül, hogy kapcsolatba kellene lépnie az Azure ügyfélszolgálatával.

Ez a cikk bemutatja, hogyan kapcsolhatja be a **diagnosztikai naplózást** a metrikák adataihoz az Azure Digital Twins-példányból. Ezeket a naplókat használhatja a szolgáltatással kapcsolatos problémák elhárításához, valamint a diagnosztikai beállítások konfigurálásához, hogy az Azure Digital Twins-metrikákat különböző célhelyekre küldje. Ezekről a beállításokról további információt a [*diagnosztikai beállítások létrehozása a platform-naplók és a metrikák különböző célhelyekre való küldéséhez*](../azure-monitor/platform/diagnostic-settings.md)című cikkből kaphat.

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Diagnosztikai beállítások bekapcsolása a Azure Portal

Az alábbi módon engedélyezheti az Azure Digital Twins-példány diagnosztikai beállításait:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az Azure Digital Twins-példányhoz. A nevét a portál keresési sávjába írja be. 

2. Válassza a menüből a **diagnosztikai beállítások** elemet, majd **adja hozzá a diagnosztikai**beállításokat.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="A diagnosztikai beállítások lapot és a hozzáadni kívánt gombot ábrázoló képernyőkép":::

3. Az alábbi lapon adja meg a következő értékeket:
     * **Diagnosztikai beállítás neve**: adjon meg egy nevet a diagnosztikai beállításoknak.
     * **Kategória részletei**: válassza ki a figyelni kívánt műveleteket, majd jelölje be a jelölőnégyzeteket a diagnosztika engedélyezéséhez a műveletekhez. A diagnosztikai beállítások által a következő műveleteket lehet jelenteni:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        További információ ezekről a beállításokról: [*Kategória részletei*](#category-details) szakasz.
     * **Célhely részletei**: válassza ki, hová szeretné elküldeni a naplókat. A három lehetőség bármely kombinációját kiválaszthatja:
        - Küldés a Log Analyticsnek
        - Archiválás tárfiókba
        - Streamelés eseményközpontba

        Előfordulhat, hogy további részleteket is meg kell adnia, ha azok a célhely kiválasztásához szükségesek.  
    
4. Mentse az új beállításokat. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="A diagnosztikai beállítások lapot és a hozzáadni kívánt gombot ábrázoló képernyőkép":::

Az új beállítások körülbelül 10 percen belül lépnek érvénybe. Ezután a naplók megjelennek a konfigurált célhelyen a példány **diagnosztikai beállítások** lapján. 

## <a name="category-details"></a>Kategória részletei

A diagnosztikai beállítások beállításakor a **Kategória részletei** szakaszban kiválaszthatja a naplózási kategóriák részleteit.

| Naplókategória | Leírás |
| --- | --- |
| ADTModelsOperation | A modellekre vonatkozó összes API-hívás naplózása |
| ADTQueryOperation | A lekérdezésekre vonatkozó összes API-hívás naplózása |
| ADTEventRoutesOperation | Naplózza az esemény-útvonalakra vonatkozó összes API-hívást, valamint az Azure Digital Twins-ból érkező eseményeket egy olyan végponti szolgáltatáshoz, mint például a Event Grid, a Event Hubs és a Service Bus |
| ADTDigitalTwinsOperation | Az Azure digitális Twins-hoz tartozó összes API-hívás naplózása |

Minden naplózási kategória írási, olvasási, törlési és művelet-műveletből áll.  Ezek a leképezések a következőképpen REST API hívásokat:

| Eseménytípus | REST API műveletek |
| --- | --- |
| Írás | PUT és PATCH |
| Olvasás | GET |
| Törlés | DELETE |
| Művelet | POST |

Itt látható a műveletek és a megfelelő [Azure digitális Twins REST API](https://docs.microsoft.com/rest/api/azure-digitaltwins/) a különböző kategóriákba bejelentkezett hívások részletes listája. 

>[!NOTE]
> Minden naplózási kategória több művelet/REST API hívást tartalmaz. Az alábbi táblázatban minden naplózási kategória az összes művelet/REST API hívására vonatkozik, amíg meg nem jelenik a következő napló kategóriája. 

| Naplókategória | Művelet | Hívások és egyéb események REST API |
| --- | --- | --- |
| ADTModelsOperation | Microsoft. DigitalTwins/modellek/írás | Digital Twin models frissítési API |
|  | Microsoft. DigitalTwins/modellek/olvasás | Az id és a List API-k által lekérdezett digitális kettős modellek |
|  | Microsoft. DigitalTwins/models/delete | Digital Twin models – API törlése |
|  | Microsoft. DigitalTwins/modellek/művelet | Digitális kettős modellek API hozzáadása |
| ADTQueryOperation | Microsoft. DigitalTwins/lekérdezés/művelet | Az ikrek API lekérdezése |
| ADTEventRoutesOperation | Microsoft. DigitalTwins/eventroutes/Write | Event Routes API hozzáadása |
|  | Microsoft. DigitalTwins/eventroutes/READ | Azonosító és lista API-k által beolvasott esemény-útvonalak |
|  | Microsoft. DigitalTwins/eventroutes/delete | Esemény-útvonalak törlése API |
|  | Microsoft. DigitalTwins/eventroutes/művelet | Hiba történt az események végponti szolgáltatásba való közzétételekor (nem API-hívás) |
| ADTDigitalTwinsOperation | Microsoft. DigitalTwins/DigitalTwins/Write | Digitális ikrek hozzáadása, kapcsolat hozzáadása, frissítés, összetevő frissítése |
|  | Microsoft. DigitalTwins/DigitalTwins/READ | Digitális ikrek által beolvasott azonosító, összetevő beolvasása, kapcsolat beolvasása azonosító alapján, bejövő kapcsolatok listázása, kapcsolatok listázása |
|  | Microsoft. DigitalTwins/DigitalTwins/delete | Digitális ikrek törlése, kapcsolat törlése |
|  | Microsoft. DigitalTwins/DigitalTwins/művelet | Digitális Twins – összetevő telemetria küldése, telemetria küldése |

## <a name="next-steps"></a>Következő lépések

* A diagnosztika konfigurálásával kapcsolatos további információkért lásd: [*adatok gyűjtése és felhasználása az Azure-erőforrásokból*](../azure-monitor/platform/platform-logs-overview.md).
* További információ az Azure digitális Twins metrikáinak használatáról [*: a metrikák megtekintése Azure Monitorsal*](troubleshoot-metrics.md).
* Ha szeretné megtudni, hogyan engedélyezheti a riasztásokat a metrikák számára, tekintse meg a [*Hibaelhárítás: riasztások beállítása*](troubleshoot-alerts.md)című témakört.
