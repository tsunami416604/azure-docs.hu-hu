---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 49ac1a7585ddf2a6500c7e9382880109c3f7f431
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534273"
---
## <a name="export-an-api-definition"></a>API-definíció exportálása
OpenAPI-definíciója van a függvényhez, a [OpenAPI-definíció létrehozása függvényhez](../articles/azure-functions/functions-openapi-definition.md). A folyamat következő lépése az API-definíció exportálása, hogy a PowerApps és a Microsoft Flow egy egyéni API-ban is használható legyen.

> [!IMPORTANT]
> Ne feledje, hogy be kell jelentkeznie az Azure-ba ugyanazzal a hitelesítő adatokkal, amelyeket a PowerApps és Microsoft Flow bérlőhöz használ. Ez lehetővé teszi, hogy az Azure létrehozza az egyéni API-t, és elérhetővé tegye mind a PowerApps, mind a Microsoft Flow számára.

1. A [Azure Portal](https://portal.azure.com)kattintson a Function alkalmazás nevére (például **Function-demó-Energy**) > **platform szolgáltatásai** > **API-definíció**.

    ![API-definíció](media/functions-export-api-definition/api-definition.png)

1. Kattintson **az Exportálás PowerApps + flow**elemre.

    ![API-definíció forrása](media/functions-export-api-definition/export-api-1.png)

1. A jobb oldali ablaktáblában használja a táblázatban megadott beállításokat.

    |Beállítás|Leírás|
    |--------|------------|
    |**Exportálási mód**|Válassza az **expressz** lehetőséget az egyéni API automatikus létrehozásához. Válassza a **manuális** EXPORTÁLÁS az API-definíciót, de ezt követően manuálisan kell importálnia a PowerApps-be és Microsoft flow. További információ: [Exportálás PowerApps és Microsoft Flowba](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Környezet**|Válassza ki azt a környezetet, amelyre az egyéni API-t menteni kívánja. További információ: környezetek [áttekintése (PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) vagy [környezetek áttekintése (Microsoft flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Egyéni API neve**|Írjon be egy nevet, `Turbine Repair`például:.|
    |**API-kulcs neve**|Adja meg azt a nevet, amelyet az alkalmazásnak és a flow-építőknek látniuk kell az egyéni API felhasználói felületén. Vegye figyelembe, hogy a példa hasznos információkat tartalmaz.|
 
    ![Exportálás a PowerAppsbe és a Microsoft Flow-ba](media/functions-export-api-definition/export-api-2.png)

1. Kattintson az **OK** gombra. Az egyéni API most már a megadott környezethez lett létrehozva és hozzá lett adva.