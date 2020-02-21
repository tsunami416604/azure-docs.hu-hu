---
title: Tantermi Labs-fogalmak – Azure Lab Services | Microsoft Docs
description: Megismerheti a labor Services alapfogalmait, és azt, hogy miként hozhat létre és kezelhet laborokat.
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
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526229"
---
# <a name="classroom-labs-concepts"></a>Osztályterem-tesztkörnyezetek alapelvei

A következő lista a Key Lab Services alapfogalmait és definícióit tartalmazza:

## <a name="quota"></a>Kvóta

A kvóta az az időkorlátja (órában), ameddig a tanár beállíthatja, hogy a tanulók laboratóriumi virtuális gépet használjanak. A beállítás értéke 0 vagy adott számú óra lehet. Ha a kvóta 0 értékű, a tanulók csak akkor használhatják a virtuális gépet, ha egy ütemterv fut, vagy amikor egy tanár manuálisan bekapcsolja a tanuló virtuális gépét.  

A kvóta órája akkor számít, ha a tanuló saját maga indítja el a laboratóriumi virtuális gépet.  Ha egy tanár manuálisan indítja el a labor virtuális gépet egy tanuló számára, a rendszer nem használja az adott diákra vonatkozó kvóta-időt.

## <a name="schedules"></a>Ütemezések

Az ütemtervek azt az időpontot jelentik, amelyet a tanár létrehozhat az osztály számára, így a tanuló virtuális gépek az osztály idejére elérhetők.  Az ütemtervek lehetnek egyszeri vagy ismétlődőek.  A kvóta órája nem használatos az ütemterv futtatásakor.

Az ütemtervek három típusa létezik: standard, csak indítás és leállítás.

- **Standard**.  Ez az ütemterv elindítja az összes tanuló virtuális gépet a megadott kezdési időpontban, és leállítja az összes tanuló virtuális gépet a megadott leállítás időpontjában.
- **Csak indítás**.   Ez az ütemterv az összes tanuló virtuális gépet a megadott időpontban indítja el.  A tanuló virtuális gépek nem állnak le, amíg egy tanuló leállítja a virtuális gépet a Azure Lab Services-portálon, vagy csak leállítási ütemezés történik.
- **Csak leállítás**.  Ez az ütemterv az összes tanuló virtuális gépet leállítja a megadott időpontban.  

## <a name="template-virtual-machine"></a>Sablon virtuális gépe

A laborban található sablon virtuális gépe egy alapszintű virtuálisgép-rendszerkép, amelyből minden felhasználó virtuális gépe létrejött. A trénerek/labor-készítők beállítják a sablon virtuális gépet, és azt a szoftverrel konfigurálja, amelyet a résztvevők számára a laborok betanításához szeretne biztosítani. Amikor közzétesz egy sablon virtuális gépet, Azure Lab Services létrehozza vagy frissíti a laboratóriumi virtuális gépeket a sablon VM-je alapján.

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

## <a name="next-steps"></a>Következő lépések

Első lépésként az Azure Lab Services használatával hozzon létre egy tesztkörnyezetfiókot, amely az osztálytermi tesztkörnyezetek létrehozásához szükséges:

- [Tesztkörnyezetfiók beállítása](tutorial-setup-lab-account.md)
