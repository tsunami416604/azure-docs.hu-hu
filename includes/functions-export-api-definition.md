---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 49ac1a7585ddf2a6500c7e9382880109c3f7f431
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134159"
---
## <a name="export-an-api-definition"></a>API-definíció exportálása
OpenAPI-definíció rendelkezik a függvény [függvény OpenAPI definíció létrehozása](../articles/azure-functions/functions-openapi-definition.md). Ez a folyamat következő lépése, hogy az API-definíció exportálása, hogy a PowerApps és a Microsoft Flow is használhassák az egyéni API-t.

> [!IMPORTANT]
> Ne feledje, hogy Ön be kell jelentkeznie az Azure-ba, használhatja a powerapps és Microsoft Flow-bérlők azonos hitelesítő adatokkal. Ez lehetővé teszi az Azure-ban az egyéni API létrehozása és a PowerApps és a Microsoft Flow elérhetővé tenni.

1. Az a [az Azure portal](https://portal.azure.com), kattintson a függvényalkalmazás nevére (például **function-demo-energy**) > **platformfunkciók** > **API-definíció** .

    ![API-definíció](media/functions-export-api-definition/api-definition.png)

1. Kattintson a **exportálás a Powerappsba és a Flow**.

    ![API-definíció forrása](media/functions-export-api-definition/export-api-1.png)

1. A jobb oldali ablaktáblán használja a táblázatban megadott beállításokat.

    |Beállítás|Leírás|
    |--------|------------|
    |**Export mód**|Válassza ki **Express** automatikusan létrehozni az egyéni API-t. Kiválasztásával **manuális** exportálások az API-definíció, de majd importálnia kell azt a PowerApps és a Microsoft Flow manuálisan. További információkért lásd: [exportálás a Powerappsbe és a Microsoft Flow](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Környezet**|Válassza ki a környezetet, amely az egyéni API-t menteni kell. További információkért lásd: [környezetek áttekintése (PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) vagy [környezetek áttekintése (Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Egyéni API neve**|Adjon meg egy nevet, például `Turbine Repair`.|
    |**API-kulcs neve**|Az egyéni API-t felhasználói felületén adja meg a nevét, amely alkalmazások és folyamatok sikerei kell megjelennie. Vegye figyelembe, hogy a példa a hasznos információkat tartalmazza.|
 
    ![Exportálás a PowerAppsbe és a Microsoft Flow-ba](media/functions-export-api-definition/export-api-2.png)

1. Kattintson az **OK** gombra. Az egyéni API-t most készített, és felveszi a környezetbe a megadott.