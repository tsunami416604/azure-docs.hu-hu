---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 8ad682ccd7feb9c7f089e9cf0f066cb1be8c756c
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2020
ms.locfileid: "97505324"
---
Az egyéni modell betanítási adataihoz tartozó SAS URL-cím lekéréséhez lépjen a Azure Portal Storage-erőforrására, és válassza a **Storage Explorer** lapot. Navigáljon a tárolóhoz, kattintson a jobb gombbal, majd válassza a **közös hozzáférési aláírás beolvasása** elemet. Fontos, hogy az SAS-t lekérje a tárolóhoz, nem pedig magához a Storage-fiókhoz. Győződjön meg arról, hogy az **olvasási** és a **listázási** engedély be van jelölve, majd kattintson a **Létrehozás** gombra. Ezután másolja az **URL-cím** szakaszban lévő értéket egy ideiglenes helyre. A következő formátumban kell lennie: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
   > [!div class="mx-imgBorder"]
   > ![helyettesítő szöveg](../media/quickstarts/get-sas-url.png)