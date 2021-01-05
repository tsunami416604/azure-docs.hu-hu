---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 3f4272f5445c8530e97726bd4fdca6bf662719c2
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807807"
---
Az egyéni modell betanítási adataihoz tartozó SAS URL-cím lekéréséhez lépjen a Azure Portal Storage-erőforrására, és válassza a **Storage Explorer** lapot. Navigáljon a tárolóhoz, kattintson a jobb gombbal, majd válassza a **közös hozzáférési aláírás beolvasása** elemet. Fontos, hogy az SAS-t lekérje a tárolóhoz, nem pedig magához a Storage-fiókhoz. Győződjön meg arról, hogy az **olvasási** és a **listázási** engedély be van jelölve, majd kattintson a **Létrehozás** gombra. Ezután másolja az **URL-cím** szakaszban lévő értéket egy ideiglenes helyre. A következő formátumban kell lennie: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.