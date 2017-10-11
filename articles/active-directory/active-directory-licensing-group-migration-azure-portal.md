---
title: "Hogyan telepítheti át a egyedi licenccel rendelkező felhasználók az Azure Active Directory csoporthoz |} Microsoft Docs"
description: "Váltás az egyes felhasználói licencek Csoportalapú licencelésre az Azure Active Directoryval"
services: active-directory
keywords: "Az Azure AD-licencelés"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6b77dd4e9a6d361a05382397e89b575896fdad84
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-add-licensed-users-to-a-group-for-licensing-in-azure-active-directory"></a>A csoport az Azure Active Directory licencelésének licenccel rendelkező felhasználók felvétele

Előfordulhat, hogy telepíti a felhasználók számára a "közvetlen hozzárendelés"; keresztül a szervezetek meglévő licencek Ez azt jelenti, hogy használatával PowerShell-parancsfájlok vagy más eszközök egyéni felhasználói licencek hozzárendelése. Ha azt szeretné, indíthatja Csoportalapú licencelési a szervezet-licencek kezelése, szüksége lesz egy áttelepítési terv zökkenőmentesen lecseréli a meglévő megoldásokat Csoportalapú licencelési.

A legtöbb fontos szem előtt tartani, hogy kerülje az olyan helyzet, ahol történő Csoportalapú licencelési hatására a felhasználók ideiglenesen elvesztése aktuálisan hozzárendelt licencét. Távolítsa el a felhasználók, szolgáltatások és az adataik elveszítenék kockázatát kerülni kell a bármely folyamat, amelynek hatására a licencek eltávolítását.

## <a name="recommended-migration-process"></a>Ajánlott áttelepítési folyamat

1. A licenc-hozzárendelést és a felhasználók eltávolításának kezelése automatizálási (például PowerShell) rendelkezik. Fut, hagyja.

2. Hozzon létre egy új licencelési csoportot (vagy döntse el, melyik meglévő csoportokat kívánja használni), és győződjön meg arról, hogy minden felhasználót adnak hozzá tagként szükséges.

3. A szükséges licencek kiosztása ezekhez a csoportokhoz; a cél megfelelően a meglévő automatizációk (például PowerShell) azoknak a felhasználóknak érvényes olyan licencelési állapotban kell lennie.

4. Győződjön meg arról, hogy ezeket a csoportokat az összes felhasználó licencek alkalmaztak. Ehhez a feldolgozási állapotát az egyes csoportok ellenőrzésével, és ehhez ellenőrizze a naplókat.

  - A licenc részletei megtekintésével helyszíni ellenőrzés egyéni felhasználók számára is. Látni fogja, hogy rendelkeznek azonos licenccel "közvetlenül" és "örökölt" csoportból.

  - PowerShell parancsfájl futtatása [győződjön meg arról, hogyan licenceket a felhasználókhoz rendelt](active-directory-licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

  - A azonos termék rendel hozzá licencet a felhasználóhoz, mindkét közvetlenül és a csoport révén, amikor a felhasználó által felhasznált csak egy-egy licencet. További licencek ezért az áttelepítéshez szükségesek.

5. Győződjön meg arról, hogy nem a licenc-hozzárendeléseivel minden csoport hibás állapotú felhasználók ellenőrzésével sikertelen. További információkért lásd: [azonosítása és licenc problémák megoldásához a csoport](active-directory-licensing-group-problem-resolution-azure-portal.md).

6. Fontolja meg az eredeti közvetlen hozzárendelések; eltávolítását érdemes lehet fokozatosan, ne a "hullámok", a felhasználók egy részhalmazát eredménye először figyelése.

  Felhasználók sikerült meghagyása az eredeti közvetlen hozzárendelések, de amikor a felhasználók elhagyják a licenccsoportok azok továbbra is megőrzik az eredeti licenc, amely valószínűleg nem szeretné, hogy szeretné.

## <a name="an-example"></a>Példa

Tudunk 1000 felhasználóval rendelkező szervezeteknél. Minden felhasználó Enterprise Mobility + Security (EMS) licencet igényelnek. 200 felhasználó a pénzügyi részleg és Office 365 nagyvállalati E3 csomag licencek igényel. Tudunk futó helyi hozzáadása és eltávolítása a felhasználók licencek, származnak, és nyissa meg egy PowerShell-parancsfájlt. Cserélje le a parancsfájlt, licencek automatikusan kezeli az Azure AD Csoportalapú licencelési szeretnénk.

Íme néhány nézhet ki az áttelepítési folyamat:

1. Az EMS-licencet az Azure portál használatával rendelje hozzá a **minden felhasználó** az Azure AD-csoporthoz. A E3 licenc hozzárendelése a **pénzügyi részleg** csoportot, amely tartalmazza a szükséges felhasználók.

2. Az egyes csoportokban ellenőrizze, hogy a licenc-hozzárendelést az összes felhasználó számára befejeződött. Keresse fel az egyes csoportokhoz, jelölje be a panelt **licencek**, és a feldolgozási állapotának tetején a **licencek** panelen.

  - Keressen a "Legújabb licenc módosításai frissültek az összes felhasználóra" megerősítéséhez feldolgozása befejeződött.

  - Keresse meg felül, amely licencek előfordulhat, hogy nem sikerült hozzárendelt felhasználók kapcsolatos értesítést. Nem jelenleg egyes felhasználók licenceinek kívül futtatni? Egyes felhasználók rendelkeznek ütköző licenc, amely megakadályozza, hogy azok csoport licencek öröklődés termékváltozatok?

3. Helyszíni egyes felhasználókat győződjön meg arról, hogy rendelkezik-e mind a közvetlen és csoportosítási licencek alkalmazott ellenőrzése. Nyissa meg a felhasználó, jelölje be a panelt **licencek**, és vizsgálja meg a licenc állapotát.

  - Ez az a várt felhasználói állapot áttelepítése során:

      ![várt érték user állapota](media/active-directory-licensing-group-migration-azure-portal/expected-user-state.png)

  Ez megerősíti, hogy a felhasználó rendelkezik-e közvetlen és az örökölt licenceket. Látható, amely mindkét **EMS** és **E3** vannak hozzárendelve.

  - Jelölje ki minden egyes licencet az engedélyezett szolgáltatások részleteit. Ennek segítségével ellenőrizze, hogy ha a közvetlen és csoportosítási licencek engedélyezése pontosan a azonos service-csomagokról a felhasználó számára.

      ![Ellenőrizze a service-csomagokról](media/active-directory-licensing-group-migration-azure-portal/check-service-plans.png)

4. Miután meggyőződött arról, hogy közvetlen és a csoport licencek egyenértékűek, megkezdheti a felhasználók közvetlen licencek eltávolítását. Ehhez távolítsa el az egyes felhasználók számára a portálon tesztelést, és futtassa az automatizálási parancsfájlokat azok egyszerre eltávolítani. Íme egy példa a portálon keresztül a közvetlen licencek ugyanazon felhasználónál. Figyelje meg, hogy a licenc állapota változatlan marad, de már nem látható közvetlen hozzárendeléseket.

  ![közvetlen licencek eltávolítása](media/active-directory-licensing-group-migration-azure-portal/direct-licenses-removed.png)


## <a name="next-steps"></a>Következő lépések

Más esetekben a Licenckezelés csoportokon keresztül kapcsolatos további tudnivalókért olvassa el

* [Licencek hozzárendelése az Azure Active Directory csoport](active-directory-licensing-group-assignment-azure-portal.md)
* [Mi az a csoport-alapú licencelése az Azure Active Directoryban?](active-directory-licensing-whatis-azure-portal.md)
* [Majd azonosítani és megoldani az Azure Active Directory csoport licenc problémák](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Az Azure Active Directory biztonságicsoport-alapú licencelési további helyzeteket is](active-directory-licensing-group-advanced.md)
