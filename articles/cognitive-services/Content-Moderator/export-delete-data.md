---
title: Felhasználói adatok exportálása vagy törlése – Tartalommoderátor
titleSuffix: Azure Cognitive Services
description: Teljes mértékben ön kézben tarthatja az adatokat. További információ az adatok tartalommoderátorban való megtekintéséről, exportálásáról és törléséről.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 81713bf8d424b9f272f6b1bccf3657810160d4cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73744786"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Felhasználói adatok exportálása vagy törlése a Tartalommoderátorban

A tartalommoderátor felhasználói adatokat gyűjt a szolgáltatás működtetéséhez, de az ügyfelek teljes hozzáféréssel rendelkeznek adataik megtekintéséhez, exportálásához és törléséhez a [Véleményezés eszköz](https://contentmoderator.cognitive.microsoft.com/) és a [Moderálás és véleményezés API-k](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference)használatával.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

A felhasználói adatok tartalommoderátorban történő exportálásáról és törléséről az alábbi táblázatban talál további információt.

| Adatok | Exportálási művelet | Törlési művelet |
| ---- | ---------------- | ---------------- |
| Fiókadatok (előfizetési kulcsok) | N/A | Törlés az Azure Portalon (Azure-előfizetések). Vagy használja a **Csapat törlése** gombot a Felhasználói felület csoport beállításainak [áttekintése](https://contentmoderator.cognitive.microsoft.com/) lapon. |
| Képek egyéni egyeztetéshez | Hívja meg a [Rendszerkép-azonosítók beszedése API-t.](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676) A képek egyirányú saját kivonatformátumban tárolódnak, és a tényleges képeket nem lehet kivonni. | Hívja meg az [Összes kép törlése API-t](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Vagy törölje a tartalommoderátor erőforrást az Azure Portalon. |
| Az egyéni egyeztetés feltételei | Cal a [Get all terms API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | Hívja meg az [Összes kifejezés törlése API-t.](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d) Vagy törölje a tartalommoderátor erőforrást az Azure Portalon. |
| Címkék | N/A | Használja a **Törlés** ikont, amely a Felhasználói felület címke beállításainak áttekintése lapon minden címkéhez elérhető. Vagy használja a **Csapat törlése** gombot a Felhasználói felület csoport beállításainak [áttekintése](https://contentmoderator.cognitive.microsoft.com/) lapon. |
| Felülvizsgálatok | Hívja fel az [Értékelés behívása API-t](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Használja a **Csapat törlése** gombot a Felhasználói felület csoport beállításainak [áttekintése](https://contentmoderator.cognitive.microsoft.com/) lapon.
| Felhasználók | N/A | Használja a **Törlés** ikont, amely minden felhasználó számára elérhető a Felhasználói felület csoport beállításainak [áttekintése](https://contentmoderator.cognitive.microsoft.com/) lapon. Vagy használja a **Csapat törlése** gombot a Felhasználói felület csoport beállításainak [áttekintése](https://contentmoderator.cognitive.microsoft.com/) lapon. |

