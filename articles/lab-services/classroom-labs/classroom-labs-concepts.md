---
title: Tanterem Labs-fogalmak – Azure Lab Services |} A Microsoft Docs
description: Ismerje meg, hogyan azt teheti, hogy egyszerűen hozhat létre és laborok kezelése, és a Lab Services alapvető fogalmait.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: spelluru
ms.openlocfilehash: f0f45e50fba4587e604add937ed45fc78228d44f
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960241"
---
# <a name="classroom-labs-concepts"></a>Osztályterem-tesztkörnyezetek alapelvei
Az alábbi lista tartalmazza az alapfogalmakat Lab Services és a definíciók:

## <a name="quota"></a>Kvóta
Kvóta az időkorlát (órában), amely egy oktató állíthatja be a labor virtuális Géphez használandó tanuló. 0 korlátlan, vagy egy bizonyos számú órák állítható. A kvóta 0 értékre van állítva, ha egy diák csak használhat a virtuális gép Ha ütemezés szerint fut, vagy egy oktató manuálisan kikapcsolja a virtuális gépen a diákoknak.
 
##<a name="schedules"></a>Ütemezések
Ütemezések idő tárolóhely (egyszeri vagy ismétlődő), amely egy oktató hozhat létre az osztály. A labor összes virtuális gép automatikusan elindulnak, az ütemezéshez elején és végén található az ütemezés le vannak állítva. Kvóta órák nem használ egy ütemezés futtatásakor.

## <a name="template-virtual-machine"></a>A sablon virtuális gép
Egy tesztkörnyezetben sablon virtuális gép alap virtuálisgép-lemezkép, amelyből a felhasználók összes virtuális gépek jönnek létre. Az oktatók és labor létrehozói állítsa be a sablon virtuális gépet, és konfigurálja azt a szoftvert, a képzés résztvevői labs ehhez adja meg azoknak a. Egy Virtuálisgép-sablont tesz közzé, ha az Azure Lab Services hoz létre, vagy frissíti a labor virtuális gépeken a virtuális gép sablon alapján. 


## <a name="user-profiles"></a>Felhasználói profilok
Ez a cikk az Azure Lab Services különböző felhasználói profiljait ismerteti. 

### <a name="lab-account-owner"></a>Tesztkörnyezetfiók tulajdonosa
Általában a vállalat felhőerőforrásainak informatikai rendszergazdája, aki az Azure-előfizetés tulajdonosa, és a tesztkörnyezetfiók tulajdonosaként eljárva az alábbi feladatokat hajthatja végre:   

- Tesztkörnyezetfiókot állít be a vállalat számára.
- Felügyeli és konfigurálja a szabályzatokat minden tesztkörnyezetre vonatkozóan.
- Engedélyeket ad a vállalatban dolgozó személyeknek tesztkörnyezet létrehozására a tesztkörnyezetfiókban.

### <a name="professor"></a>Professzor
A tesztkörnyezetfiókban lévő osztálytermi tesztkörnyezeteket általában olyan felhasználók hozzák létre, mint a tanárok vagy az online oktatók. Az oktató az alábbi feladatokat hajtja végre: 

- Létrehozza az osztályterem-tesztkörnyezetet.
- Virtuális gépeket hoz létre a tesztkörnyezetben. 
- Telepíti a megfelelő szoftvereket a virtuális gépeken.
- Meghatározza, hogy ki férhet hozzá a tesztkörnyezethez.
- Elküldi a hallgatóknak a tesztkörnyezetre mutató regisztrációs hivatkozást.

### <a name="student"></a>Hallgató
A hallgató az alábbi feladatokat hajtja végre:

- Regisztrál a tesztkörnyezetben a tesztkörnyezet létrehozójától kapott regisztrációs hivatkozással. 
- Csatlakozik a tesztkörnyezetben lévő virtuális géphez, és osztálytermi munkához, feladatokhoz és projektekhez használja. 

## <a name="next-steps"></a>További lépések
Első lépésként az Azure Lab Services használatával hozzon létre egy tesztkörnyezetfiókot, amely az osztálytermi tesztkörnyezetek létrehozásához szükséges:

- [Tesztkörnyezetfiók beállítása](tutorial-setup-lab-account.md)
