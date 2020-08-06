---
title: Diagnosztika beállítása
titleSuffix: Azure Digital Twins
description: 'Lásd: a naplózás engedélyezése a diagnosztikai beállításokkal.'
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 402eda92d5cc8e5c2566345d31c9ae625b82695c
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87809398"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Azure Digital Twins hibaelhárítása: diagnosztika naplózása

Az Azure Digital Twins a szolgáltatási példány [metrikáit](troubleshoot-metrics.md) gyűjti, amelyek információkat biztosítanak az erőforrások állapotáról. Ezeket a mérőszámokat felhasználhatja az Azure Digital Twins szolgáltatás általános állapotának felmérésére és a hozzájuk kapcsolódó erőforrásokra. Ezek a felhasználók felé irányuló statisztikák segítenek megtekinteni, hogy mi történik az Azure-beli digitális Ikrekben, és segítsen a problémák kiváltó okának elemzésében anélkül, hogy kapcsolatba kellene lépnie az Azure ügyfélszolgálatával.

Ez a cikk bemutatja, hogyan kapcsolhatja be a **diagnosztikai naplózást** a metrikák adataihoz az Azure Digital Twins-példányból. Ezeket a naplókat használhatja a szolgáltatással kapcsolatos problémák elhárításához.

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Diagnosztikai beállítások bekapcsolása a Azure Portal

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az Azure Digital Twins-példányhoz. A nevét a portál keresési sávjába írja be. 

2. Válassza a menüből a **diagnosztikai beállítások** elemet, majd **adja hozzá a diagnosztikai**beállításokat.

    :::image type="content" source="media/troubleshoot-metrics/diagnostic-settings.png" alt-text="A diagnosztikai beállítások lapot és a hozzáadni kívánt gombot ábrázoló képernyőkép":::

3. Az alábbi lapon adja meg a következő értékeket:
     * **Diagnosztikai beállítás neve**: adjon meg egy nevet a diagnosztikai beállításoknak.
     * **Kategória részletei**: válassza ki a figyelni kívánt műveleteket, majd jelölje be a jelölőnégyzeteket a diagnosztika engedélyezéséhez a műveletekhez. A diagnosztikai beállítások által a következő műveleteket lehet jelenteni:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
     * **Célhely részletei**: válassza ki, hová szeretné elküldeni a naplókat. A három lehetőség bármely kombinációját kiválaszthatja:
        - Küldés a Log Analyticsnek
        - Archiválás tárfiókba
        - Streamelés eseményközpontba

        Előfordulhat, hogy további részleteket is meg kell adnia, ha azok a célhely kiválasztásához szükségesek.  
    
4. Mentse az új beállításokat. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="A diagnosztikai beállítások lapot és a hozzáadni kívánt gombot ábrázoló képernyőkép":::

Az új beállítások körülbelül 10 percen belül lépnek érvénybe. Ezután a naplók megjelennek a konfigurált célhelyen a példány **diagnosztikai beállítások** lapján. 

## <a name="next-steps"></a>További lépések

* A diagnosztika konfigurálásával kapcsolatos további információkért lásd: [*adatok gyűjtése és felhasználása az Azure-erőforrásokból*](../azure-monitor/platform/platform-logs-overview.md).
* További információ az Azure digitális Twins metrikáinak használatáról [*: a metrikák megtekintése Azure Monitorsal*](troubleshoot-metrics.md).
