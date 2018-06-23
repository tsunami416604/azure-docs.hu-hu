---
title: Csoportok és a tartalom moderátor API subteams |} Microsoft Docs
description: Útmutató a csoportok és subteams használni a tartalom moderátor API kognitív szolgáltatásokhoz.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/22/2017
ms.author: sajagtap
ms.openlocfilehash: 161c7cd8bac07d5ffc138297d98a40317a8d88fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346978"
---
# <a name="team-and-subteams"></a>Csoport és Subteams #

Tartalom moderátor használata előtt létre kell hoznia egy csoportnak. Sign up és a csoport neve, a csapat lesz az alapértelmezett csapat. A felülvizsgálati eszköz csak akkor egy csoportot. Több subteams is létrehozhat. Subteams hasznosak eszkalációs csapatok vagy a megjelölt kategóriákat a tartalom megtekintésével dedikált csoportok létrehozása. Például érdemes lehet felnőtt tartalom küldése egy másik csoport további ellenőrzésre.

Ez a témakör azt ismerteti, hogyan subteams létrehozásához, és gyorsan hozzárendelése az értékelést menet közben. Használhat azonban [munkafolyamatok](workflows.md) hozzárendelni a megadott feltételek alapján értékelést.

## <a name="go-to-the-teams-setting"></a>Ugrás a csoportok beállítása ##

Első lépésként a csapaton létrehozásával, válassza ki a **csapatok** a beállítások lehetőséget.

![Team beállításai](images/0-teams-1.png)

## <a name="create-subteams"></a>Subteams létrehozása ##

Az alapértelmezett csoport tartalmazza az összes lehetséges véleményező; subteams az alapértelmezett csapat részhalmaza. Nem rendelhet valaki a csapaton Ha nincsenek alapértelmezett csapat így ellenőrzőket bármely az alapértelmezett csapatának most kell. A meghívott felhasználó gombra a csoport oldalon.

![Meghívott felhasználóknak](images/invite-users.png)

### <a name="1-create-a-subteam"></a>1. Hozzon létre egy alcsoportok.
Görgessen le a csapat lap a csapaton részt. A csapaton hozzáadása gombra. 

![Alcsoportok hozzáadása](images/1-teams-1.png)

### <a name="2-name-your-subteam"></a>2. Nevezze el a csapaton.
Adja meg a csapaton nevét a párbeszédpanelen, majd kattintson a Mentés gombra.

![Alcsoportok neve](images/1-Teams-2.PNG)

### <a name="3-assign-members-from-your-default-team"></a>3. Az alapértelmezett csapat tagok hozzárendelhetők.
A tag hozzáadása gombra kattintva az alapértelmezett csapat tagjai hozzárendelése egy vagy több subteams. A meglévő felhasználók csak egy alcsoportok adhat hozzá. Az új felhasználók, akik nem a felülvizsgálati eszköz hozzáadása, hívhat meg azokat a csapat beállítások lapon a "Meghívása" gombra kattintva.

![Alcsoportok tagjainak hozzárendeléséhez](images/1-Teams-3.PNG)

## <a name="assign-reviews-to-your-subteams"></a>A subteams értékelést hozzárendelése ##

A létrehozott subteams követően és hozzárendelt csoport tagjainak kép hozzárendelése indítható, és a szöveg ellenőrzi, hogy az adott subteams. Ehhez a felülvizsgálati ablakból.
Ha azt szeretné, egyéni lemezkép hozzárendelése egy alcsoportok, kattintson a három pont, a kép jobb felső sarkában, helyezze át, válassza ki és a csapaton.

![Kép felülvizsgálat subteam hozzárendelése](images/3-review-image-subteam-1.png)

## <a name="switch-between-subteams-to-review-assigned-content"></a>Váltás a subteams hozzárendelt tartalom áttekintése ##

Ha egy vagy több subteams tagja, ezek a felülvizsgálati eszközök irányítópultról subteams között válthat. Összes függőben lévő a csapaton tartozó értékelést az aktuális megtekintéséhez válassza a alcsoportok válassza ki a lemezkép lapot.

![Váltás a subteams](images/3-review-image-subteam-2.png)
