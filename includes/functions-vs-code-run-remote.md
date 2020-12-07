---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 55a75651b724a4fe975f655958e36fbd40e35db7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2020
ms.locfileid: "96748226"
---
## <a name="run-the-function-in-azure"></a>A függvény futtatása az Azure-ban

1. Vissza az **Azure: functions** területen az oldal sávján bontsa ki az új Function alkalmazást az előfizetés alatt. Bontsa ki a **függvények** csomópontot, kattintson a jobb gombbal (Windows) vagy a <kbd>CTRL</kbd> (MacOS) elemre a **HttpExample**, majd válassza a **függvény URL-címének másolása** lehetőséget.

    ![A függvény URL-címének másolása az új HTTP-triggerhez](./media/functions-vs-code-run-remote/function-copy-endpoint-url.png)

1. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába, adja hozzá a `name` lekérdezési karakterláncot `?name=Functions` az URL-cím végéhez, majd hajtsa végre a kérelmet. A HTTP-eseményindítót használó függvényt meghívó URL-címnek az alábbi formátumban kell lennie:

    ```http
    http://<FUNCTION_APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions
    ```

    Az alábbi példa a böngészőben a függvény által visszaadott távoli GET kérelemre adott választ mutatja be:

    ![A függvény által visszaadott válasz a böngészőben](./media/functions-vs-code-run-remote/functions-test-remote-browser.png)
