---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673369"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Az Azure Storage-beli kapcsolatok karakterláncának beolvasása

Korábban létrehozott egy Azure Storage-fiókot, amelyet a Function alkalmazás használ. A fiókhoz tartozó kapcsolatok karakterlánca biztonságosan tárolódik az Azure-beli alkalmazás beállításaiban. Ha a beállítást a *Local. Settings. JSON* fájlba tölti le, akkor a függvény helyileg történő futtatásakor ugyanazzal a fiókkal írja be a kapcsolódást a tárolási várólistába. 

1. A projekt gyökeréből futtassa a következő parancsot, és cserélje `<app_name>` le a függvényt az előző rövid útmutatóból származó Function alkalmazás nevére. Ez a parancs felülírja a fájlban lévő összes meglévő értéket.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Nyissa meg a *Local. Settings. JSON* fájlt `AzureWebJobsStorage`, és keresse meg a nevű értéket, amely a Storage-fiókhoz tartozó kapcsolatok karakterlánca. A jelen cikk más `AzureWebJobsStorage` részeiben a nevet és a kapcsolatok karakterláncát kell használnia.

> [!IMPORTANT]
> Mivel a *Local. Settings. JSON* az Azure-ból letöltött titkos kulcsokat tartalmaz, mindig zárja ki ezt a fájlt a forrás vezérlőelemből. A helyi functions projekttel létrehozott *. gitignore* fájl alapértelmezés szerint kizárja a fájlt.