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
ms.openlocfilehash: 03384db96338bf78ed89c1cd0fb8780489ee0e13
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807388"
---
# <a name="introduction-to-classroom-labs"></a>Az osztályterem-tesztkörnyezetek bemutatása
Az Azure Lab Services lehetővé teszi egy osztályterem-tesztkörnyezet gyors beállítását a felhőben. Az oktató létrehoz egy osztályterem-tesztkörnyezetet, Windows vagy Linux rendszerű virtuális gépeket épít ki, telepíti a szükséges szoftvereket és eszközöket a tesztkörnyezetben, és elérhetővé teszi őket a diákok számára. A diákok osztályterem-tesztkörnyezetben található virtuális gépekhez csatlakoznak, és a projektjeikhez, feladataikhoz vagy osztálytermi gyakorlataikhoz használják őket. 

Az osztályterem-tesztkörnyezetek az Azure által felügyelt felügyelt tesztkörnyezet-típusok. A szolgáltatás maga gondoskodik a felügyelt tesztkörnyezet infrastruktúrájának átfogó kezeléséről, a virtuális gépek indításától a hibák kezelésén keresztül az infrastruktúra skálázásáig. Ön megadhatja, milyen típusú infrastruktúrát igényel, és telepítheti az eszközöket vagy szoftvereket, amelyekre az osztálynak szüksége van. A felügyelt tesztkörnyezetek jelenleg előzetes verzióban érhetők el.   

## <a name="automatic-management-of-azure-infrastructure-and-scale"></a>Azure-infrastruktúra és a méretezési csoport automatikus felügyeletét 
Az Azure Lab Services egy felügyelt szolgáltatás, ami azt jelenti, hogy a kiépítési és felügyeleti egy tesztkörnyezet egy alapul szolgáló infrastruktúra a szolgáltatás automatikusan kezeli. Csak összpontosíthat a felhasználók számára a megfelelő labor előkészítése. Lehetővé teszik a szolgáltatást, a többit bízza, és a labor virtuális gépek bevezetése a célközönségnek. Több száz virtuális gép egyetlen kattintással a labor skálázása.

## <a name="simple-experience-for-your-lab-users"></a>A lab felhasználói élményét, egyszerű 
A tesztkörnyezethez azok a felhasználók azonnali az erőforrások eléréséről a gyakorlatot belül biztosíthat számukra. Csak akkor kell azok teljes hozzáféréssel rendelkezik az több labs virtuális gépek teljes listájának megtekintéséhez jelentkezzen be. A csatlakozni a virtuális géphez, és megkezdheti a munkát egy egyetlen gombra kattintva törölheti. A felhasználóknak nem kell a szolgáltatás használatához Azure-előfizetések. 

## <a name="cost-optimization-and-tracking"></a>Költségek optimalizálása és nyomon követése  
Kordában tarthatja ellenőrzés pontosan hány órát a tesztlabor a felhasználók használhatják a virtuális gépek szabályozásával. Állítsa be a laborban ütemezéseket, hogy a felhasználók a virtuális gépek csak a kijelölt időszeletek során használ, vagy ütemezhet automatikus leállítás beállítása és kezdési idejét. Nyomon követheti az egyes felhasználók használati és korlátokat.

## <a name="next-steps"></a>További lépések
Első lépésként az Azure Lab Services használatával hozzon létre egy tesztkörnyezetfiókot, amely az osztálytermi tesztkörnyezetek létrehozásához szükséges:

- [Tesztkörnyezetfiók beállítása](tutorial-setup-lab-account.md)
