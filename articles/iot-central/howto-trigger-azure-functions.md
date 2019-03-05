---
title: Az Azure IoT Central webhookok használata az Azure Functions aktiválása
description: Hozzon létre egy függvényalkalmazást, amely minden alkalommal, amikor egy szabály akkor lesz kiváltva futtatja az Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 512956d2de0f9a838cc6378345a334e489d1d120
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57306867"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Az Azure IoT Central webhookok használata az Azure Functions aktiválása

*Ez a témakör létrehozói és a rendszergazdák vonatkozik.*

Azure Functions használatával kiszolgáló nélküli programkód webhook kimenetén az IoT-központ szabályok futtatása. Virtuális gép létrehozása, vagy használhatja az Azure Functions egy webalkalmazás közzététele nem szükséges, de Ehelyett futtathatja a kódot serverlessly. Az Azure Functions segítségével átalakíthatja a webhook hasznos adatai, például egy SQL database vagy az Event Grid a végső rendeltetési elküldése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="how-to-connect-azure-functions"></a>Csatlakozás az Azure Functions

1. [Új függvényalkalmazás létrehozása az Azure Portalon](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Új függvényalkalmazás létrehozása az Azure Portalon](media/howto-trigger-azure-functions/createfunction.png)

2. Bontsa ki a függvényalkalmazást, és válassza ki a **+ gomb** funkciók mellett. Ha ez az első függvény a függvényalkalmazásban, jelölje ki az **Egyéni függvény** lehetőséget. Ez megjeleníti a függvénysablonok teljes készletét.

    ![Válasszon függvényalkalmazást egyéni függvény](media/howto-trigger-azure-functions/customfunction.png)

3. A keresőmezőbe írja be a **"általános"** és válassza ki a kívánt nyelvet az általános webhook-eseményindító sablonjához. Ez a témakör C#-függvényt használ. 

    ![Általános webhook-eseményindító kiválasztása](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. Válassza ki az új függvényben **<> / Get függvény URL-Címének**, majd másolja és mentse az értéket. Erre az értékre a webhook konfigurálásához lesz szükség.

    ![A függvény URL-címére](media/howto-trigger-azure-functions/getfunctionurl.png)

4. IoT-központ keresse meg a szabály, amely a függvényalkalmazás csatlakozni szeretne.

5. Adjon hozzá egy webhook művelettel. Adjon meg egy **megjelenítendő név** és illessze be a függvény URL-címet korábban vágólapra másolt be **visszahívási URL-Címének**.

    ![A visszahívási URL-cím mezőben adja meg a függvény URL-címe](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. A szabály mentéséhez. Most már a szabály akkor lesz kiváltva, ha a webhook hív meg, a függvényalkalmazás futtatásához. A függvényalkalmazásban, kiválaszthatja a **figyelő** a függvény meghívási előzményeinek megtekintéséhez. Az App Insights vagy a klasszikus nézet segítségével tekintse meg az előzményeket.

    ![A függvény meghívási előzményeinek figyelése](media/howto-trigger-azure-functions/monitorfunction.PNG)

További információért látogasson el az Azure Functions cikk kapcsolatos [általános webhook által aktivált függvény létrehozása](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function).

## <a name="next-steps"></a>További lépések
Most, hogy, hogyan állíthatja be, és webhookok használata, a javasolt következő lépésre megismeréséhez [munkafolyamatokat a Microsoft Flow-hoz](howto-add-microsoft-flow.md).
