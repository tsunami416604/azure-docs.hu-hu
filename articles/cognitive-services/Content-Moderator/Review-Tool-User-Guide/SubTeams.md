---
title: Csoportok és alcsoportok a Content Moderator API - Content Moderator a kezelése
titlesuffix: Azure Cognitive Services
description: Ismerje meg, csoportok és alcsoportok a Content Moderator API használata a Cognitive Services számára.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: a36da1787d5abd5f7dc1455823be55f1880c7ba5
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227131"
---
# <a name="manage-review-teams-and-subteams"></a>Felülvizsgáló csoportok és alcsoportok kezelése

A Content Moderator használata előtt létre kell hoznia egy csapatot. Bejelentkezés beállítása és a csoport neve, a csapat lesz az alapértelmezett csoport. Legfeljebb egy csapat a felülvizsgálati eszközben. Több alcsoportok is létrehozhat. Alcsoportok hasznosak eszkalációs csapatok vagy a megjelölt kategóriákat a tartalom megtekintésével számára kijelölt csoportok létrehozása. Például érdemes lehet küldje el a felnőtt tartalmat egy másik csapat további véleményezésre.

Ez a témakör bemutatja, hogyan alcsoportok létrehozása, és gyorsan hozzárendelése az értékelések menet közben. Használhatja azonban [munkafolyamatok](workflows.md) hozzárendelni a megadott feltételek alapján felülvizsgálatok.

## <a name="go-to-the-teams-setting"></a>Nyissa meg a csapatok beállítás

Ismerkedés a csapaton létrehozásával, válassza ki a **csapatok** a beállítások lehetőséget.

![Team-beállítások](images/0-teams-1.png)

## <a name="create-subteams"></a>Alcsoportok létrehozása

Az alapértelmezett csoport tartalmazza az összes lehetséges véleményező; alcsoportok az alapértelmezett csapat részhalmaza. Nem rendelhet valaki a csapaton, ha azok még nem az alapértelmezett csapat, így most már bármilyen felülvizsgálók hozzáadása az alapértelmezett csapat kell. A meghívás gombra a csapatoldalon.

![Felhasználó meghívása](images/invite-users.png)

### <a name="create-a-subteam"></a>Hozzon létre egy alcsoportok
Görgessen le a csapatoldalon a csapaton szakaszra. Alcsoportok hozzáadása gombra. 

![Add Subteam](images/1-teams-1.png)

### <a name="name-your-subteam"></a>Nevezze el a csapaton
A párbeszédpanelen adja meg a csapaton nevét, majd kattintson a Mentés gombra.

![Alcsoportok neve](images/1-Teams-2.PNG)

### <a name="assign-members-from-your-default-team"></a>Az alapértelmezett csoport tagjai hozzárendelését.
A tag hozzáadása gombra az alapértelmezett csoport tagok hozzárendelése egy vagy több alcsoportok. A meglévő felhasználók csak a csapaton adhat hozzá. Új felhasználók, akik nem tagjai a felülvizsgálati eszköz hozzáadásához, felkínáljuk számukra a Team-beállítások lapon a "Meghívás" gomb használatával.

![Alcsoportok tagok hozzárendelése](images/1-Teams-3.PNG)

## <a name="assign-reviews-to-your-subteams"></a>Értékelések hozzárendelése az alcsoportok

A létrehozott alcsoportok után és hozzárendelt csoport tagjainak lemezkép hozzárendelése indítható, és ezek alcsoportok felülvizsgálat szöveg. Ez történik, a felülvizsgálat ablakból.
Ha azt szeretné, egyéni lemezkép hozzárendelése a csapaton, a kép jobb felső sarkában a három pontot ábrázoló gombra, válassza ki a lépés, és válassza ki a csapaton.

![Kép felülvizsgálat subteam hozzárendelése](images/3-review-image-subteam-1.png)

## <a name="switch-between-subteams-to-review-assigned-content"></a>Váltás a alcsoportok hozzárendelt tartalom áttekintése

Ha Ön egy vagy több alcsoportok tagja, ezek a felülvizsgálati eszköz irányítópultján alcsoportok között válthat. Az összes függőben lévő értékelések a csapaton tartozó aktuális megtekintéséhez válassza a lemezkép lapon válassza a csapaton.

![Alcsoportok közötti váltás](images/3-review-image-subteam-2.png)
