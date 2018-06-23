---
title: Exportálja, vagy törölje a tartalom moderátor - Azure kognitív szolgáltatások adatai |} Microsoft Docs
description: Megtudhatja, hogyan exportálja, vagy törölje a tartalom moderátor adatai.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: 1dbb645a033c6db5ffa9003a53f30fd927131298
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349534"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Exportálja, vagy a tartalom moderátor felhasználói adatok törlése

Tartalom moderátor a szolgáltatás működtetéséhez felhasználói adatokat gyűjt, de az ügyfelek teljes hozzáféréssel rendelkeznek megtekintése, exportálásához és az adatok törléséhez használja a [felülvizsgálati felhasználói felület](http://contentmoderator.cognitive.microsoft.com/) és a [API-k](https://docs.microsoft.com/en-us/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Törölje a tartalom moderátor felhasználói adatok és exportálása a további információkért lásd az alábbi táblázatot.

| Adatok | Az exportálási művelet | Törlési művelet |
| ---- | ---------------- | ---------------- |
| Fiók adatainak (előfizetés kulcsok) | – | Az Azure portálon (Azure-előfizetések) törlése. Vagy használja a **csoport törlése** gombra a [felülvizsgálati felhasználói felület](http://contentmoderator.cognitive.microsoft.com/) Team beállítások lapon. |
| Az egyéni egyező lemezképek | [Lemezkép-azonosítók beolvasása](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Képek a szellemi tulajdont képező egyirányú kivonat formában tároljuk, és nincs mód a tényleges képek kibontásához. | [Törölje az összes lemezképet](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Vagy törölje a tartalom moderátor erőforrást az Azure portál használatával. |
| Az egyéni egyező kifejezések | [Minden feltételek beolvasása](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | [Törölje az összes kifejezést](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Vagy törölje a tartalom moderátor erőforrást az Azure portál használatával. |
| Címkék | – | Használja a **törlése** ikon egyes címkék a felülvizsgálati felhasználói felület címke beállítási oldalon érhető el. Vagy használja a **csoport törlése** gombra a [felülvizsgálati felhasználói felület](http://contentmoderator.cognitive.microsoft.com/) Team beállítások lapon. |
| Felülvizsgálatok | [Felülvizsgálati beolvasása](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Használja a **csoport törlése** gombra a [felülvizsgálati felhasználói felület](http://contentmoderator.cognitive.microsoft.com/) Team beállítások lapon.
| Felhasználók | – | Használja a **törlése** ikon a minden felhasználó számára elérhető a [felülvizsgálati felhasználói felület](http://contentmoderator.cognitive.microsoft.com/) Team beállítások lapon. Vagy használja a **csoport törlése** gombra a [felülvizsgálati felhasználói felület](http://contentmoderator.cognitive.microsoft.com/) Team beállítások lapon. |

