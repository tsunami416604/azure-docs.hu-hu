---
title: Az Azure IoT Central webhookok használata az Azure Functions aktiválása
description: Hozzon létre egy függvényalkalmazást, amely minden alkalommal, amikor egy szabály akkor lesz kiváltva futtatja az Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 09/06/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7b14dc4eeec1ab543c407b1bb1cf8a5ce46f3ecb
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356622"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Az Azure IoT Central webhookok használata az Azure Functions aktiválása

Azure Functions használatával kiszolgáló nélküli programkód webhook kimenetén az IoT-központ szabályok futtatása. Virtuális gép létrehozása, vagy használhatja az Azure Functions egy webalkalmazás közzététele nem szükséges, de Ehelyett futtathatja a kódot serverlessly. Az Azure Functions segítségével átalakíthatja a webhook hasznos adatai, például egy SQL database vagy az Event Grid a végső rendeltetési elküldése előtt. 

## <a name="prerequisites"></a>Előfeltételek
+ Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="how-to-connect-azure-functions"></a>Csatlakozás az Azure Functions

1. [Új függvényalkalmazás létrehozása az Azure Portalon. ](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).
2. Bontsa ki a függvényalkalmazást, és kattintson a + gombra funkciók mellett. Ha ez a funkció csak az elsőt a függvényalkalmazásban, jelölje be az egyéni függvény. Ez megjeleníti a függvénysablonok teljes készletét.
3. A keresőmezőbe írja be az általános, és válassza ki a kívánt nyelvet az általános webhook-eseményindító sablonjához. Ez a témakör C#-függvényt használ. 
4. Az új függvényben kattintson a **</> Függvény URL-címének lekérése** elemre, majd másolja és mentse az értéket. Erre az értékre a webhook konfigurálásához lesz szükség. 
4. IoT-központ keresse meg a szabály, amely a függvényalkalmazás csatlakozni szeretne. 
5. Adjon hozzá egy webhook művelettel. Adjon meg egy **megjelenítendő név** és illessze be a korábban kimásolt függvény URL-CÍMÉT.
6. A szabály mentéséhez. Most már a szabály akkor lesz kiváltva, ha a webhook meghívja a függvényalkalmazás futtatásához. A függvényalkalmazásban figyelheti, hogy a naplók és minden alkalommal, amikor a függvény akkor aktiválódik, lásd:.

További információért látogasson el az Azure Functions cikk kapcsolatos [általános webhook által aktivált függvény létrehozása](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function). 

## <a name="next-steps"></a>További lépések
Most, hogy, hogyan állíthatja be, és webhookok használata, a javasolt következő lépésre megismeréséhez [munkafolyamatokat a Microsoft Flow-hoz](howto-add-microsoft-flow.md).
