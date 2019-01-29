---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 914c006daf49e22ebec870a549bfdbc63f882647
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55148019"
---
## <a name="test"></a>A függvény tesztelése

Tesztelje a Mac vagy Linux rendszerű számítógépen vagy a Windows Powershell használatával üzembe helyezett függvényt a cURL használatával. Hajtsa végre a következő cURL parancsot úgy, hogy a `<app_name>` helyőrzőt lecseréli a függvényalkalmazás nevére. Fűzze hozzá a `&name=<yourname>` lekérdezési sztringet az URL-hez.

```powershell
Invoke-WebRequest -Uri "https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>"
```

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![A függvény válasza a böngészőben jelenik meg.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Ha nem rendelkezik `cURL`vagy `Invoke-WebRequest` érhető el a parancssorban adja meg az ugyanazon URL-CÍMÉT a webböngésző címsorába. Ezúttal is helyettesítse be a `<app_name>` helyőrző helyére a függvényalkalmazás nevét, az URL-címhez pedig fűzze hozzá a `&name=<yourname>` lekérdezési sztringértéket, majd hajtsa végre a kérést.

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![A függvény válasza a böngészőben jelenik meg.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
