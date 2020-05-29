---
title: Azure EA portál adminisztrációja a partnerek számára
description: A partnerekkel kapcsolatos portáladminisztrációs témaköröket ismerteti.
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: banders
ms.openlocfilehash: 9751b64064b1f4e5f3c55de829f107e096cfb7d9
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648625"
---
# <a name="azure-ea-portal-administration-for-partners"></a>Azure EA portál adminisztrációja a partnerek számára

Ez a cikk a partner EA-rendszergazdák Azure EA Portalon (https://ea.azure.com) ) elvégzendő gyakori feladatait ismerteti.

## <a name="manage-partner-administrators"></a>Partneradminisztrátorok kezelése

Az Azure EA Portalon mindegyik partneradminisztrátor jogosult felvenni vagy eltávolítani más partneradminisztrátorokat. A partneradminisztrátorok a közvetett regisztrációk partnerszervezeteihez vannak társítva, és nincsenek közvetlenül társítva a regisztrációkhoz.

### <a name="add-a-partner-administrator"></a>Partneradminisztrátorok hozzáadása

Ha meg szeretné tekinteni az összes olyan regisztrációt, amely ugyanahhoz a partnerszervezethez tartozik, mint az aktuális felhasználó, válassza a **Regisztráció** lapot, és jelölje be a kívánt regisztráció jelölőnégyzetét.

1. Jelentkezzen be partneradminisztrátorként.
1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
1. Válassza a **Partner** lapot.
1. Válassza az **+ Adminisztrátor hozzáadása** lehetőséget, és adja meg az e-mail-címét, az értesítési kapcsolattartóját és az értesítési adatait.
1. Válassza a **Hozzáadás** lehetőséget.

### <a name="remove-a-partner-administrator"></a>Partneradminisztrátorok eltávolítása

Ha meg szeretné tekinteni az összes olyan regisztrációt, amely ugyanahhoz a partnerszervezethez tartozik, mint az aktuális felhasználó, válassza a **Regisztráció** lapot, és jelölje be a kívánt regisztráció jelölőnégyzetét.

1. Jelentkezzen be partneradminisztrátorként.
1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
1. Válassza a **Partner** lapot.
1. A Rendszergazda területen válassza ki az eltávolítani kívánt rendszergazdához tartozó sort.
1. Válassza a jobb oldalon található X szimbólumot.
1. Erősítse meg a törlési szándékát.

## <a name="manage-partner-notifications"></a>A partnerértesítések kezelése

A partnerrendszergazdák beállíthatják, hogy milyen gyakran kapjanak a regisztrációikkal kapcsolatos használati értesítéseket. A rendszer minden héten automatikusan értesítéseket küld nekik a számlázatlan egyenlegükről. Módosíthatják az értesítések gyakoriságát havonta, hetente, vagy naponta értékre, vagy teljesen ki is kapcsolhatják őket.

Ha a felhasználó nem kap értesítést, a következő lépésekkel ellenőrizze, hogy helyesek-e a felhasználó értesítési beállításai.

1. Jelentkezzen be az Azure EA Portalra partnerrendszergazdaként.
2. Válassza a **Kezelés** lehetőséget, majd a **Partner** lapot.
3. Tekintse meg a rendszergazdák listáját a Rendszergazda területen.
4. Az értesítési beállítások szerkesztéséhez vigye a mutatót a megfelelő rendszergazda fölé, és válassza a ceruza szimbólumot.
5. Növelje igény szerint az értesítési gyakoriságot és az életciklus-értesítéseket.
6. Szükség esetén adjon hozzá egy kapcsolattartót, és válassza a **Hozzáadás** lehetőséget.
7. Kattintson a **Mentés** gombra.

![A Kapcsolattartó hozzáadása panelt mutató példa ](./media/ea-partner-portal-administration/create-ea-manage-partner-notification.png)

## <a name="view-enrollments-for-partner-administrators"></a>Partneradminisztrátorok regisztrációinak megtekintése

A partneradminisztrátorok megtekinthetik az Azure EA Portalon elérhető közvetlen és közvetett regisztrációik listáját. A regisztrációk áttekintését tartalmazó mezők a regisztrációs számmal, a regisztráció nevével, egyenleggel és a kerettúllépés mennyiségével jelennek meg.

### <a name="view-a-list-of-enrollments"></a>A regisztrációk listájának megtekintése

1. Jelentkezzen be partneradminisztrátorként.
1. Válassza a **Kezelés** lehetőséget az oldal bal oldalán található navigációs sávon.
1. Válassza a **Regisztráció** fület.
1. Jelölje be a regisztrációhoz tartozó jelölőnégyzetet.

Az összes regisztráció nézete az oldal tetején továbbra is megtekinthető marad az egyes regisztrációkhoz tartozó jelölőnégyzetekkel. Emellett a regisztrációk között a lap bal oldalán lévő navigációs sávon az aktuális regisztrációs számot kiválasztva is válthat. Ekkor megjelenik egy felugró ablak, amely lehetővé teszi a regisztrációk keresését, vagy egy másik regisztráció kiválasztását a megfelelő jelölőnégyzet kiválasztásával.

## <a name="next-steps"></a>További lépések

- Az Azure EA Portallal kapcsolatos bevezető információkért lásd: [Ismerkedés az Azure EA Portallal](ea-portal-get-started.md).
- Ha segítségre van szüksége az Azure EA Portallal kapcsolatos hibák elhárításához, olvassa el a következő részt: [A nagyvállalati szerződéses Azure Portal elérésével kapcsolatos hibák elhárítása](ea-portal-troubleshoot.md).