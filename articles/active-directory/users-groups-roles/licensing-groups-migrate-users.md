---
title: Közvetlen licenccel rendelkező felhasználók hozzáadása a csoportos licenceléshez - Azure AD | Microsoft dokumentumok
description: Az egyéni felhasználói licencekről a csoportalapú licencelésre való áttelepítés az Azure Active Directory használatával
services: active-directory
keywords: Az Azure AD licencelése
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c06d81f2f3f6cee781889d05ae08a1fd125df52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025681"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Az egyéni licenccel rendelkező felhasználók áttelepítése csoportokba licencelési csoportokba

Előfordulhat, hogy a szervezetek felhasználói számára közvetlen hozzárendelés útján meglévő licenceket telepít; azaz powershell-parancsfájlok vagy más eszközök használata az egyes felhasználói licencek hozzárendeléséhez. Mielőtt elkezdené használni a csoportalapú licencelést a szervezet licenceinek kezelésére, ezzel az áttelepítési tervvel zökkenőmentesen lecserélheti a meglévő megoldásokat a csoportalapú licencelésre.

A legfontosabb dolog, amit szem előtt kell tartani, hogy kerülnikell egy olyan helyzetet, amikor a csoportalapú licencelésre való áttérés azt eredményezi, hogy a felhasználók ideiglenesen elveszítik a jelenleg hozzárendelt licenceiket. Minden olyan folyamatot, amely a licencek eltávolítását eredményezheti, el kell kerülni annak elkerülése érdekében, hogy a felhasználók hozzáférjenek a szolgáltatásokhoz és az adataikhoz.

## <a name="recommended-migration-process"></a>Ajánlott áttelepítési folyamat

1. Meglévő automatizálási (például PowerShell) rendszerezi a licenc-hozzárendelést és a felhasználók eltávolítását. Hagyd úgy, ahogy van.

1. Hozzon létre egy új licencelési csoportot (vagy döntse el, hogy mely meglévő csoportokat használja), és győződjön meg arról, hogy az összes szükséges felhasználó tagként kerül hozzáadásra.

1. Rendelje hozzá a szükséges licenceket ezekhez a csoportokhoz; a cél az, hogy tükrözze ugyanazt a licencelési állapot a meglévő automatizálás (például PowerShell) vonatkozik ezekre a felhasználókra.

1. Ellenőrizze, hogy a licencek a csoportok összes felhasználóját alkalmazták-e. Ez az alkalmazás az egyes csoportok feldolgozási állapotának ellenőrzésével és a naplók ellenőrzésével végezhető el.

   - Az egyes felhasználók at a licenc adatainak megtekintésével ellenőrizheti. Látni fogja, hogy ugyanazokat a licenceket rendelt "közvetlenül" és "örökölt" csoportokból.

   - PowerShell-parancsfájl futtatásával [ellenőrizheti, hogy a licencek hogyan vannak hozzárendelve a felhasználókhoz.](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses)

   - Ha ugyanazt a terméklicencet közvetlenül és egy csoporton keresztül is hozzárendeli a felhasználóhoz, a felhasználó csak egy licencet használ fel. Ezért az áttelepítéshez nincs szükség további licencre.

1. Ellenőrizze, hogy egyetlen licenc-hozzárendelés sem sikerült, ha ellenőrzi az egyes csoportokat a hibaállapotú felhasználók számára. További információt a [Csoport licencproblémáinak azonosítása és megoldása című](licensing-groups-resolve-problems.md)témakörben talál.

Fontolja meg az eredeti közvetlen hozzárendelések eltávolítását. Azt javasoljuk, hogy ezt fokozatosan, és figyelje az eredményt a felhasználók egy részhalmaza először. Ha az eredeti közvetlen hozzárendeléseket a felhasználókra hagyhatja, de amikor a felhasználók elhagyják a licencelt csoportokat, megtartják a közvetlenül hozzárendelt licenceket, ami lehet, hogy nem az, amit szeretne.

## <a name="an-example"></a>Egy példa

Egy szervezetnek 1000 felhasználója van. Minden felhasználónak Szüksége van Office 365 Nagyvállalati E3 licencre. Jelenleg a szervezet rendelkezik egy PowerShell-parancsfájl a helyszínen, hozzáadása és eltávolítása a felhasználók tól, ahogy jönnek és mennek. A szervezet azonban le szeretné cserélni a parancsfájlt csoportalapú licenccel, hogy a licenceket az Azure AD automatikusan kezelhesse.

Így nézhet ki az áttelepítési folyamat:

1. Az Azure Portal használatával rendelje hozzá az Office 365 E3-licencet az Azure AD **Minden felhasználó** csoportjához.

1. Ellenőrizze, hogy a licenc-hozzárendelés befejeződött-e az összes felhasználó számára. Nyissa meg a csoport áttekintő lapját, válassza a **Licencek**lehetőséget, és ellenőrizze a feldolgozási állapotot a **Licencek** panel tetején.

   - Keresse meg a "Legújabb licencmódosítások at alkalmazták az összes felhasználóra" című lehetőséget a feldolgozás befejezésének megerősítéséhez.

   - Keressen egy értesítést a tetején azokról a felhasználókról, akiknek a licenceit esetleg nem sikerült hozzárendelni. Elfogyott a licenc egyes felhasználók számára? Egyes felhasználók ütköző licenctervekkel rendelkeznek, amelyek megakadályozzák, hogy csoportlicenceket örököljenek?

1. A helyszíni ellenőrzés egyes felhasználók, hogy ellenőrizze, hogy mind a közvetlen és a csoport licencek alkalmazva. Nyissa meg egy felhasználó profillapját, válassza a **Licencek**lehetőséget, és vizsgálja meg a licencek állapotát.

   - Ez a várt felhasználói állapot az áttelepítés során:

      ![a várt felhasználói állapot az áttelepítés során](./media/licensing-groups-migrate-users/expected-user-state.png)

     Ez megerősíti, hogy a felhasználó rendelkezik közvetlen és örökölt licencekkel is. Látjuk, hogy az Office 365 E3 hozzá van rendelve.

   - Válassza ki az egyes licenceket, hogy lássa, mely szolgáltatások vannak engedélyezve. Annak ellenőrzéséhez, hogy a közvetlen és a csoportlicencek pontosan ugyanazokat a szolgáltatásokat engedélyezik-e a felhasználó számára, válassza a **Hozzárendelések**lehetőséget.

1. Miután meggyőződött arról, hogy mind a közvetlen, mind a csoportlicencek egyenértékűek, elkezdheti eltávolítani a közvetlen licenceket a felhasználóktól. Ezt úgy tesztelheti, hogy eltávolítja őket az egyes felhasználók számára a portálon, majd futtassa az automatizálási parancsfájlokat, hogy tömegesen eltávolítsák őket. Íme egy példa ugyanannak a felhasználónak, akinek a portálon keresztül eltávolított közvetlen licencei vannak eltávolítva. Figyelje meg, hogy a licenc állapota változatlan marad, de már nem látjuk a közvetlen hozzárendeléseket.

   ![a közvetlen licencek eltávolításának ellenőrzése](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>További lépések

További információ a csoportlicenc-kezelés egyéb forgatókönyveiről:

- [Mi a csoportalapú licencelés az Azure Active Directoryban?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [Licencek hozzárendelése egy csoporthoz az Azure Active Directoryban](licensing-groups-assign.md)
- [A csoportok licencproblémáinak azonosítása és megoldása az Azure Active Directoryban](licensing-groups-resolve-problems.md)
- [Felhasználók áttelepítése a terméklicencek között az Azure Active Directory csoportalapú licencelésével](licensing-groups-change-licenses.md)
- [Az Azure Active Directory csoportalapú licencelésének további forgatókönyvei](licensing-group-advanced.md)
- [Példák a Csoportalapú licenceléshez az Azure Active Directoryban](licensing-ps-examples.md)
