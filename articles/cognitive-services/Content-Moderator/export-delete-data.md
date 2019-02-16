---
title: Exportálja, vagy törölje a data - Content Moderator
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan exportálhatja, vagy a Content Moderator-adatok törlése.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 15a59bbdc4c93202f8906689100c24ba713ee487
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311256"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Exportálja, vagy a Content Moderator a felhasználói adatok törlése

Content Moderator gyűjti a felhasználói adatokat a szolgáltatás, de az ügyfél rendelkezik teljes hozzáférés a megtekintése, exportálását és törlését a saját adatok használatával a [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com/) és a [moderálás és a felülvizsgálati API-k](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Hogyan exportálhatja, és törli a felhasználói adatokat a Content Moderator további információkért lásd az alábbi táblázatot.

| Adatok | Az exportálási művelet | Törlési művelet |
| ---- | ---------------- | ---------------- |
| Fiók adatai (előfizetési kulcsok) | – | Törlés az Azure portal (az Azure-előfizetések) használatával. Vagy használja a **törlése csapat** gombra a [felülvizsgálati felhasználói felület](https://contentmoderator.cognitive.microsoft.com/) Team-beállítások lapon. |
| Az egyéni egyező képek | Hívja a [Get kép azonosítók API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Rendszerképek tárolása a szellemi tulajdont képező egyirányú kivonat formátumban történik, és nem lehet kibontani a tényleges lemezképeket. | Hívja a [törli az összes lemezképek API-t](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Vagy törli a Content Moderator az erőforrást az Azure portal használatával. |
| Egyéni egyezési feltételei | CAL a [minden használati API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | Hívja a [összes feltételek API törlése](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Vagy törli a Content Moderator az erőforrást az Azure portal használatával. |
| Címkék | – | Használja a **törlése** ikon minden címke a tekintse át a felhasználói felület címke beállítások oldalon érhető el. Vagy használja a **törlése csapat** gombra a [felülvizsgálati felhasználói felület](https://contentmoderator.cognitive.microsoft.com/) Team-beállítások lapon. |
| Felülvizsgálatok | Hívja a [Get felülvizsgálati API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Használja a **törlése csapat** gombra a [felülvizsgálati felhasználói felület](https://contentmoderator.cognitive.microsoft.com/) Team-beállítások lapon.
| Felhasználók | – | Használja a **törlése** ikonra a minden felhasználó számára elérhető a [felülvizsgálati felhasználói felület](https://contentmoderator.cognitive.microsoft.com/) Team-beállítások lapon. Vagy használja a **törlése csapat** gombra a [felülvizsgálati felhasználói felület](https://contentmoderator.cognitive.microsoft.com/) Team-beállítások lapon. |

