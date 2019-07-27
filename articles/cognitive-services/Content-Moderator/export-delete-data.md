---
title: Felhasználói adatContent Moderator exportálása vagy törlése –
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan exportálhatja vagy törölheti az adatait Content Moderatorban.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: b18bc2907c00ed1424db440f09c914faff6a9863
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565570"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Felhasználói adatexportálás vagy-törlés Content Moderator

A Content Moderator felhasználói adatokat gyűjt a szolgáltatás üzemeltetéséhez, de az ügyfelek teljes hozzáféréssel rendelkeznek az adatok megtekintésére, exportálására és törlésére a [felülvizsgálati eszközzel](https://contentmoderator.cognitive.microsoft.com/) , valamint a [moderálási és felülvizsgálati API](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference)-k használatával.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

A Content Moderator felhasználói adatainak exportálásával és törlésével kapcsolatos további információkért tekintse meg a következő táblázatot.

| Data | Exportálási művelet | Törlési művelet |
| ---- | ---------------- | ---------------- |
| Fiókadatok (előfizetési kulcsok) | – | Törlés a Azure Portal használatával (Azure-előfizetések). Vagy használja a **csapat törlése** gombot a [felhasználói felületi](https://contentmoderator.cognitive.microsoft.com/) csoport beállításainak áttekintése lapon. |
| Egyéni egyeztetésre szolgáló lemezképek | Hívja meg a képazonosítók beolvasása [API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676)-t. A lemezképek egyirányú tulajdonosi kivonatoló formátumban tárolódnak, és nem lehet kinyerni a tényleges képeket. | Hívja meg az [összes rendszerkép törlése API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686)-t. Vagy törölje a Content Moderator erőforrást a Azure Portal használatával. |
| Az egyéni egyeztetés feltételei | Cal a [minden feltétel beolvasása API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | Hívja meg az [összes feltétel törlése API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d)-t. Vagy törölje a Content Moderator erőforrást a Azure Portal használatával. |
| Tags | – | Használja az egyes címkékhez elérhető **delete** ikont a felhasználói felület áttekintése címke beállításai lapon. Vagy használja a **csapat törlése** gombot a [felhasználói felületi](https://contentmoderator.cognitive.microsoft.com/) csoport beállításainak áttekintése lapon. |
| Értékelések | A [Get felülvizsgálati API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) meghívása | Használja a **csapat törlése** gombot a [felhasználói felületi](https://contentmoderator.cognitive.microsoft.com/) csoport beállításainak áttekintése lapon.
| Felhasználók | – | Használja az egyes felhasználók számára elérhető **delete** ikont a [felhasználói felületi](https://contentmoderator.cognitive.microsoft.com/) csoport beállításainak áttekintése lapon. Vagy használja a **csapat törlése** gombot a [felhasználói felületi](https://contentmoderator.cognitive.microsoft.com/) csoport beállításainak áttekintése lapon. |

