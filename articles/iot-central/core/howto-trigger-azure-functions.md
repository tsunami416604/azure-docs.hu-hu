---
title: Trigger Azure Functions az Azure-beli webhookok használatával IoT Central
description: Hozzon létre egy olyan Function alkalmazást, amely minden alkalommal fut, amikor egy szabály aktiválódik az Azure IoT Centralban.
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a9590e5554956418859a07b43fb57724783343fe
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952848"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Trigger Azure Functions az Azure-beli webhookok használatával IoT Central

*Ez a témakör az építők és a rendszergazdákra vonatkozik.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

A Azure Functions használatával kiszolgáló nélküli kódokat futtathat IoT Central szabályok webhook kimenetén. Nem kell létrehoznia egy virtuális gépet, vagy közzé kell tennie egy webalkalmazást a Azure Functions használatához, de ehelyett futtathatja ezt a kódot kiszolgáló nélkül. Az Azure Functions használatával alakítsa át a webhook hasznos adatait, mielőtt elküldi azt a végső célhelyre, például egy SQL-adatbázisra vagy Event Gridra.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="how-to-connect-azure-functions"></a>A Azure Functions kapcsolódása

1. [Hozzon létre egy új Function-alkalmazást a Azure Portal](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Új Function-alkalmazás létrehozása a Azure Portal](media/howto-trigger-azure-functions/createfunction.png)

2. Bontsa ki a Function alkalmazást, és kattintson a függvények elem melletti **+ gombra** . Ha ez a függvény az első a Function alkalmazásban, válassza **a portál** fejlesztési környezetként lehetőséget, és válassza a **Folytatás**lehetőséget.

    ![Egyéni függvény választása a Function alkalmazásban](media/howto-trigger-azure-functions/customfunction.png)

3. Válassza a **webhook + API** -sablon elemet, majd válassza a **Létrehozás**lehetőséget. Ez a témakör a .NET-alapú Azure-függvényt használja.

    ![Általános webhook-trigger kiválasztása](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. Az új függvényben válassza a **</> függvény URL-címének beolvasása**lehetőséget, majd másolja és mentse az értéket. Erre az értékre a webhook konfigurálásához lesz szükség.

    ![A függvény URL-címének beolvasása](media/howto-trigger-azure-functions/getfunctionurl.png)

4. A IoT Centralban navigáljon ahhoz a szabályhoz, amelyhez csatlakozni szeretne a Function alkalmazáshoz.

5. Webhook-művelet hozzáadása. Adja meg a **megjelenített nevet** , és illessze be a korábban a **VISSZAhívási URL**-címre másolt függvény URL-címét.

    ![Adja meg a függvény URL-címét a visszahívási URL-cím mezőben](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Mentse a szabályt. Most, hogy a szabály aktiválódik, a webhook elindítja a Function alkalmazást a futtatáshoz. A Function alkalmazásban a **figyelő** lehetőség kiválasztásával megtekintheti a függvény Meghívási előzményeit. Az előzmények megjelenítéséhez használhatja az App bepillantást vagy a klasszikus nézetet is.

    ![A függvény Meghívási előzményeinek figyelése](media/howto-trigger-azure-functions/monitorfunction.PNG)

További információért látogasson el az [általános webhook által aktivált függvény létrehozásáról](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function)szóló Azure functions cikkre.

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte, hogyan állíthat be és használhat webhookokat, a javasolt következő lépés a [munkafolyamatok kiépítése a Microsoft flow](howto-add-microsoft-flow.md).
