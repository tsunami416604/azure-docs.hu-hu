---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673369"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Az Azure Storage-kapcsolati karakterlánc lekérése

Korábban létrehozott egy Azure Storage-fiókot a függvényalkalmazás általi használatra. A fiók kapcsolati karakterlánca biztonságosan tárolódik az Azure-beli alkalmazásbeállításokban. Ha letölti a beállítást a *local.settings.json* fájlba, akkor a helyi futtatásakor használhatja ezt a kapcsolatírást ugyanazon a fiókban lévő Storage várólistára. 

1. A projekt gyökeréből futtassa `<app_name>` a következő parancsot, és cserélje le a függvényalkalmazás nevét az előző rövid útmutatóból. Ez a parancs felülírja a fájlban lévő értékeket.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Nyissa meg a *local.settings.json webhelyet,* és keresse meg a nevű `AzureWebJobsStorage`értéket, amely a Storage-fiók kapcsolati karakterlánca. A cikk `AzureWebJobsStorage` más szakaszaiban a nevet és a kapcsolati karakterláncot használhatja.

> [!IMPORTANT]
> Mivel *a local.settings.json* az Azure-ból letöltött titkokat tartalmaz, mindig zárja ki ezt a fájlt a forrásellenőrzésből. A helyi függvényprojekttel létrehozott *.gitignore* fájl alapértelmezés szerint kizárja a fájlt.