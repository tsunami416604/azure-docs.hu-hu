---
title: E-mail, ha a titkos kulcstartó állapota megváltozik
description: Útmutató a Logic Apps használatához a Key Vault titkos kulcsainak változásaira való válaszadáshoz
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 53e8586486d9a9ebf870de350d5607f58977c0f5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423276"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>A Logic Apps használatával e-maileket kaphat a kulcstartó titkos kulcsainak állapotváltozásairól

Ebben az útmutatóban megtudhatja, hogyan reagálhat az Azure Key Vault-eseményekre, amelyek az [Azure Event Griden](../../event-grid/index.yml) keresztül érkeznek az [Azure Logic Apps](../../logic-apps/index.yml)használatával. A végén lesz egy Azure-logikai alkalmazás beállítása, hogy küldjön egy értesítő e-mailt minden alkalommal, amikor egy titkos kulcsot hoz létre az Azure Key Vault.

Az Azure Key Vault/ Azure Event Grid-integráció áttekintését a [Key Vault figyelése az Azure Event Griddel című témakörben találja (előzetes verzió).](event-grid-overview.md)

## <a name="prerequisites"></a>Előfeltételek

- E-mail fiók bármely olyan e-mail szolgáltatótól, amelyet az Azure Logic Apps támogat (például az Office 365 Outlook). Ez az e-mail-fiók küldi majd az eseményértesítéseket. A támogatott Logic Apps-összekötők teljes listáját az [összekötők áttekintésében](/connectors) találja.
- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.
- Az Azure-előfizetés egyik kulcstartója. Gyorsan létrehozhat egy új kulcstartót a Készlet és [az Azure Key Vault titkos adatainak az Azure CLI használatával történő lekérésével.](../secrets/quick-create-cli.md)

## <a name="create-a-logic-app-via-event-grid"></a>Logikai alkalmazás létrehozása az Eseményrácson keresztül

Először hozzon létre logikai alkalmazást eseményrács-kezelővel, és iratkozzon fel az Azure Key Vault "SecretNewVersionCreated" eseményeire.

Azure Event Grid-előfizetés létrehozásához kövesse az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a kulcstreozót, válassza **az Események > első lépések** lehetőséget, és kattintson a **Logikai alkalmazások elemre.**

    
    ![Key Vault - események lap](../media/eventgrid-logicapps-kvsubs.png)

1. A **Logic Apps Designer** ellenőrzi a kapcsolatot, és kattintson a **Continue** 
 
    ![Logic App Designer - kapcsolat](../media/eventgrid-logicappdesigner1.png)

1. Az **Erőforrás-esemény bekövetkeztekor** képernyőn tegye a következőket:
    - Hagyja **az Előfizetés** és az erőforrás **nevét** alapértelmezettként.
    - Válassza ki a **Microsoft.KeyVault.vaults** elemet az **Erőforrástípushoz.**
    - Válassza a **Microsoft.KeyVault.SecretNewVersionCreated** for **Event Type Item - 1**lehetőséget.

    ![Logic App Designer – eseménykezelő](../media/eventgrid-logicappdesigner2.png)

1. + **Új lépés:** Megnyit egy ablakot a Művelet kiválasztásához.
1. Keresse meg az **E-mail** elemet. Az e-mail-szolgáltató alapján keresse meg és válassza ki a megfelelő összekötőt. Ez az oktatóanyag az **Office 365 Outlook**programot használja. Az egyéb e-mail-szolgáltatókra vonatkozó lépések is hasonlók.
1. Válassza az **E-mail küldése (V2)** műveletet.

   ![Logic App Designer - e-mail hozzáadása](../media/eventgrid-logicappdesigner3.png)

1. Készítse el e-mail sablonját:
    - **A következőhöz:** Adja meg az e-mail címet az értesítő e-mailek fogadásához. Ehhez az oktatóanyaghoz használjon egy olyan e-mail-fiókot, amelyet a tesztelés során el tud majd érni.
    - **Tárgy** és **törzs**: Írja be az e-mail szövegét. A választóeszközben a megfelelő JSON-tulajdonságok kiválasztásával az eseményadatokon alapuló dinamikus tartalmakat szúrhat be. Az esemény adatait a használatával `@{triggerBody()?['Data']}`is bekérheti.

    Az e-mail sablon így nézhet ki.

    ![Logic App Designer - e-mail hozzáadása](../media/eventgrid-logicappdesigner4.png)

8. Kattintson **a Mentés másként gombra.**
9. Adja meg az új logikai alkalmazás **nevét,** és kattintson a **Létrehozás gombra.**
    
    ![Logic App Designer - e-mail hozzáadása](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Tesztelés és ellenőrzés

1.  Nyissa meg a kulcstartót az Azure Portalon, és válassza **az Események > esemény-előfizetések**lehetőséget.  Annak ellenőrzése, hogy új előfizetés jött-e létre
    
    ![Logic App Designer - e-mail hozzáadása](../media/eventgrid-logicapps-kvnewsubs.png)

1.  Nyissa meg a kulcstartót, válassza a **Titkok**lehetőséget, és válassza **a + Létrehozás/importálás**lehetőséget. Hozzon létre egy új titkos kulcsot tesztelési célokra, nevezze el a kulcsot, és tartsa meg a fennmaradó paramétereket az alapértelmezett beállításokban.

    ![Key Vault - Titkos létrehozása](../media/eventgrid-logicapps-kv-create-secret.png)

1. A **Titkos név létrehozása** képernyőn adjon meg bármilyen nevet, értéket, és válassza a **Létrehozás gombot.**

A titkos titok létrehozásakor egy e-mail érkezik a beállított címekre.

## <a name="next-steps"></a>További lépések

- Áttekintés: [Key Vault figyelése az Azure Event Griddel (előzetes verzió)](event-grid-overview.md)
- Útmutató: [A kulcstartóértesítéseinek irányítása az Azure Automationbe.](event-grid-tutorial.md)
- [Azure Event Grid eseménysémája az Azure Key Vaulthoz (előzetes verzió)](../../event-grid/event-schema-key-vault.md)
- További tudnivalók az [Azure Event Grid](../../event-grid/index.yml) szolgáltatásról.
- További tudnivalók az [Azure App Service szolgáltatás Logic Apps funkciójáról](../../logic-apps/index.yml).
