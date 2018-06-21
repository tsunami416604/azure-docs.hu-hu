---
title: Az Azure Lab Services bemutatása | Microsoft Docs
description: Megismerheti, hogyan könnyítheti meg a Lab Services a tesztkörnyezetek létrehozását, felügyeletét és védelmét a fejlesztők, tesztelők, oktatók, diákok és mások által használható virtuális gépekkel.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/21/2018
ms.author: spelluru
ms.openlocfilehash: e9c3cae7c7129cc489ddd38b5b2de18dd6f52e58
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660529"
---
# <a name="introduction-to-classroom-labs"></a>Az osztályterem-tesztkörnyezetek bemutatása
Az Azure Lab Services lehetővé teszi egy osztályterem-tesztkörnyezet gyors beállítását a felhőben. Az oktató létrehoz egy osztályterem-tesztkörnyezetet, Windows vagy Linux rendszerű virtuális gépeket épít ki, telepíti a szükséges szoftvereket és eszközöket a tesztkörnyezetben, és elérhetővé teszi őket a diákok számára. A diákok osztályterem-tesztkörnyezetben található virtuális gépekhez csatlakoznak, és a projektjeikhez, feladataikhoz vagy osztálytermi gyakorlataikhoz használják őket. 

Az osztálytermi tesztkörnyezetek az Azure által felügyelt tesztkörnyezetek. A szolgáltatás maga gondoskodik a felügyelt tesztkörnyezet infrastruktúrájának átfogó kezeléséről, a virtuális gépek indításától a hibák kezelésén keresztül az infrastruktúra skálázásáig. Ön megadhatja, milyen típusú infrastruktúrát igényel, és telepítheti az eszközöket vagy szoftvereket, amelyekre az osztálynak szüksége van. A felügyelt tesztkörnyezetek jelenleg előzetes verzióban érhetők el.  

## <a name="scenarios"></a>Forgatókönyvek
Az alábbiakban az Azure Lab Services által támogatott legfontosabb forgatókönyvet ismerheti meg: 

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Méretezhető számítógépes tesztkörnyezet beállítása a felhőben az osztályterem számára  

- Felügyelt osztályterem-tesztkörnyezet létrehozása. Mindössze közölnie kell a szolgáltatással, hogy pontosan mire van szüksége, a szolgáltatás pedig létrehozza és felügyeli a tesztkörnyezet infrastruktúráját, így az osztály tanítására összpontosíthat, és nem kell a tesztkörnyezet technikai részleteivel foglalkoznia. 
- A pontosan a tanórához szükséges dolgokkal konfigurált virtuális gépekből álló tesztkörnyezet biztosítása a diákok számára. Korlátozott mennyiségű időt biztosíthat minden diák számára a virtuális gépek használatához az osztálytermi munka elvégzéséhez.  
- Az iskola fizikai számítógéptermének áthelyezése a felhőbe. A virtuális gépek számának automatikus méretezése a tesztkörnyezetben beállított maximális használatnak és költséghatárnak megfelelően. 
- A tesztkörnyezet törlése egyetlen kattintással, miután végzett. 

## <a name="user-profiles"></a>Felhasználói profilok
Ez a cikk az Azure Lab Services különböző felhasználói profiljait ismerteti. 

### <a name="lab-account-owner"></a>Tesztkörnyezetfiók tulajdonosa
Általában a vállalat felhőerőforrásainak informatikai rendszergazdája, aki az Azure-előfizetés tulajdonosa, és a tesztkörnyezetfiók tulajdonosaként eljárva az alábbi feladatokat hajthatja végre:   

- Tesztkörnyezetfiókot állít be a vállalat számára.
- Felügyeli és konfigurálja a szabályzatokat minden tesztkörnyezetre vonatkozóan.
- Engedélyeket ad a vállalatban dolgozó személyeknek tesztkörnyezet létrehozására a tesztkörnyezetfiókban.

### <a name="educator"></a>Oktató
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
