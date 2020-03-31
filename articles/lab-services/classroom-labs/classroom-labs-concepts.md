---
title: Tantermi laborok fogalmai - Azure Lab Services | Microsoft dokumentumok
description: Ismerje meg a Lab Services alapfogalmait, és hogyan teheti lehetővé a laborok létrehozását és kezelését.
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
ms.openlocfilehash: 15fd3c18d059466c2b2bd5e2431013f393092b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526229"
---
# <a name="classroom-labs-concepts"></a>Osztályterem-tesztkörnyezetek alapelvei

Az alábbi lista a Lab Services legfontosabb fogalmait és definícióit tartalmazza:

## <a name="quota"></a>Kvóta

A kvóta az az időkorlát (órában), amelyet a tanár beállíthat egy tanuló számára a tesztkörnyezet virtuális gépének használatára. Beállítható 0-ra vagy meghatározott számú órára. Ha a kvóta 0-ra van állítva, a tanuló csak akkor használhatja a virtuális gépet, ha az ütemezés fut, vagy ha egy tanár manuálisan kapcsolja be a virtuális gépet a tanuló számára.  

A kvótaórák akkor számítanak, amikor a tanuló saját maga indítja el a labor virtuális gépét.  Ha egy tanár manuálisan indítja el egy tanuló tesztlaborvirtuálisgépét, a kvótaórák at nem használja a rendszer az adott diákhoz.

## <a name="schedules"></a>Ütemezések

Az ütemezések azok az időrések, amelyeket a tanár létrehozhat az osztályhoz, így a tanuló virtuális gépek elérhetők az órai időhez.  Az ütemezések lehetnek egyszeriek vagy ismétlődőek.  A kvótaórák nem használatosak ütemezés futtatásakor.

Az ütemezéseknek három típusa van: Normál, Csak indítás és Csak a Leállítás.

- **Standard .**  Ez az ütemezés elindítja az összes tanuló virtuális gép a megadott kezdési időpontban, és leállítja az összes diák virtuális gépek a megadott leállítási időpontban.
- **Csak a indítás**.   Ez az ütemezés a megadott időpontban indítja el az összes tanuló virtuális gépet.  A tanulói virtuális gépek nem állnak le, amíg egy diák le nem állítja a virtuális gépet az Azure Lab Services portálon keresztül, vagy csak egy stop ütemezés történik.
- **Csak megállítás**.  Ez az ütemezés leállítja az összes tanuló virtuális gép a megadott időpontban.  

## <a name="template-virtual-machine"></a>Sablon virtuális gép

A sablon virtuális gép egy tesztkörnyezetben egy alap virtuálisgép-lemezkép, amelyből minden felhasználó virtuális gép jön létre. Oktatók / labor alkotók létre a sablon virtuális gép, és konfigurálja azt a szoftvert, hogy akarnak nyújtani a képzés résztvevői csinálni labs. Amikor közzétesz egy sablon virtuális gépet, az Azure Lab Services létrehozza vagy frissíti a lab virtuális gépeket a sablon virtuális gép alapján.

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
