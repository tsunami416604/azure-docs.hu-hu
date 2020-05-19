---
title: Összetett entitás frissítése – LUIS
description: Frissítse az összetett entitást a gépi megtanult entitásra a verziófrissítési folyamattal a LUIS portálon.
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 9bbb03a2009bdcb6e2fa5d20aefadd4c7c99f025
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599424"
---
# <a name="upgrade-composite-entity-to-machine-learned-entity"></a>Összetett entitás frissítése gépi megtanult entitásra

Összetett entitás frissítése a géppel megtanult entitásra olyan entitás kiépítéséhez, amely több teljes előrejelzést kap az entitás hibakereséséhez.

## <a name="current-version-model-restrictions"></a>A modell jelenlegi korlátozásai

A frissítési folyamat géppel megtanult entitásokat hoz létre az alkalmazásában található meglévő összetett entitások alapján az alkalmazás egy új verziójába. Ilyenek például az összetett entitások gyermekei és szerepkörei. A folyamat a címkéket például hosszúságú kimondott szöveg is átváltja az új entitás használatára.

## <a name="upgrade-process"></a>Frissítési folyamat

A frissítési folyamat:
* Új, géppel megtanult entitást hoz létre minden összetett entitáshoz.
* Alárendelt entitások:
    * Ha a gyermek entitás csak az összetett entitásban használatos, akkor csak a gép által megtanult entitáshoz lesz hozzáadva.
    * Ha a gyermek entitás összetett, _és_ különálló entitásként (például hosszúságú kimondott szöveg) van használatban, akkor a rendszer a verziót entitásként és alentitásként adja hozzá az új, géppel megtanult entitáshoz.
    * Ha az alárendelt entitás egy szerepkört használ, a rendszer minden egyes szerepkört egy azonos nevű alentitásba konvertál.
    * Ha az alárendelt entitás egy nem géppel megtanult entitás (reguláris kifejezés, lista entitás vagy előre összeállított entitás), akkor egy új alentitás jön létre ugyanazzal a névvel, és az új alentitás rendelkezik egy funkcióval, amely a nem gépi megtanult entitást használja a szükséges szolgáltatás hozzáadásával.
* A nevek megmaradnak, de az alentitás/testvér szintjén egyedinek kell lenniük. Tekintse meg az [egyedi elnevezési korlátokat](luis-boundaries.md#name-uniqueness).
* A hosszúságú kimondott szöveg például az új, géppel megtanult entitásra váltanak, amelyek alentitásokkal rendelkeznek.

A modell változásának megismeréséhez használja a következő diagramot:

|Régi objektum|Új objektum|Megjegyzések|
|--|--|--|
|Összetett entitás|Gépi megtanult entitás struktúrával|Mindkét objektum szülő objektum.|
|Az összetett gyermek entitás **egyszerű entitás**|alentitás|Mindkét objektum alárendelt objektum.|
|Az összetett gyermek entitás **előre összeépített entitás** , például szám|az előre összeállított entitások, például a szám és az alentitás neve az előre felépített számú entitás _funkciójának_ értéke _true_értékre van állítva.|az alentitás az alentitások szintjén megkötéssel rendelkező funkciót tartalmaz.|
|Az összetett gyermek entitás előre összeállított **entitás** , például szám, és az előre elkészített entitás **szerepköre**|a szerepkör nevű alentitás és az alentitás az előre elkészített Number entitás funkcióját a korlátozási beállítás értéke TRUE (igaz) értékre van állítva.|az alentitás az alentitások szintjén megkötéssel rendelkező funkciót tartalmaz.|
|Szerepkör|alentitás|A szerepkör neve az alentitás neve lesz. Az alentitás a gép által megtanult entitás közvetlen leszármazottja.|

## <a name="begin-upgrade-process"></a>Frissítési folyamat megkezdése

A frissítés előtt ügyeljen a következőre:

* Verziók módosítása, ha nem a megfelelő verziót frissíti


1. A frissítési folyamat megkezdése az értesítésből, vagy megvárhatja a következő ütemezett kérést.

    > [!div class="mx-imgBorder"]
    > ![Frissítés megkezdése értesítésekről](./media/update-composite-entity/notification-begin-update.png)

1. Az előugró ablakban válassza a **Frissítés most**lehetőséget.

1. Tekintse át **, hogy mi történik az adatok frissítésekor,** majd válassza a **Folytatás**lehetőséget.

1. Válassza ki az összetett entitásokat a listából a frissítéshez, majd válassza a **Folytatás**lehetőséget.

1. A nem betanított módosításokat a jelenlegi verzióról a frissített verzióra helyezheti át a jelölőnégyzet bejelölésével.

1. A frissítési folyamat elindításához válassza a **Folytatás** lehetőséget.

1. A folyamatjelző sáv a frissítési folyamat állapotát jelzi.

1. A folyamat elvégzése után új, betanított verziót használhat az új, géppel megtanult entitásokkal. Válassza az új entitások **kipróbálása** lehetőséget az új entitás megjelenítéséhez.

    Ha az új entitás frissítése vagy betanítása meghiúsult, az értesítés további információkat tartalmaz.

1. Az entitások listája lapon az új entitások a típus neve mellett **új** jelöléssel jelennek meg.

## <a name="next-steps"></a>További lépések

* [Szerzők és közreműködők](luis-how-to-collaborate.md)