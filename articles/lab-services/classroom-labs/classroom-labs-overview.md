---
title: Tantermi laborok az Azure Lab Services-ben | Microsoft dokumentumok
description: Ismerje meg, hogyan állíthat be gyorsan egy tantermi laborkörnyezetet a felhőben – konfiguráljon egy tesztkörnyezetet egy sablonos virtuális gépsegítségével az osztályhoz szükséges szoftverrel, és tegye elérhetővé a virtuális gép másolatát az osztály minden tanulója számára.
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
ms.date: 11/26/2019
ms.author: spelluru
ms.openlocfilehash: bbbea0c943758c2682f7b166e461807d2914f790
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74561675"
---
# <a name="introduction-to-classroom-labs"></a>Az osztályterem-tesztkörnyezetek bemutatása
Az Azure Lab Services lehetővé teszi egy osztályterem-tesztkörnyezet gyors beállítását a felhőben. Az oktató létrehoz egy osztályterem-tesztkörnyezetet, Windows vagy Linux rendszerű virtuális gépeket épít ki, telepíti a szükséges szoftvereket és eszközöket a tesztkörnyezetben, és elérhetővé teszi őket a diákok számára. A diákok osztályterem-tesztkörnyezetben található virtuális gépekhez csatlakoznak, és a projektjeikhez, feladataikhoz vagy osztálytermi gyakorlataikhoz használják őket. 

Az osztálytermi laborok felügyelt labortípusok, amelyek et az Azure által felügyelt. A szolgáltatás maga kezeli a felügyelt tesztkörnyezet-típus összes infrastruktúra-felügyeletét, a virtuális gépek (VM-ek) felpörgetésétől a hibák kezeléséig és az infrastruktúra méretezéséig. Ön megadhatja, milyen típusú infrastruktúrát igényel, és telepítheti az eszközöket vagy szoftvereket, amelyekre az osztálynak szüksége van. 

## <a name="automatic-management-of-azure-infrastructure-and-scale"></a>Az Azure-infrastruktúra és -méretezés automatikus kezelése 
Az Azure Lab Services egy felügyelt szolgáltatás, ami azt jelenti, hogy a tesztkörnyezet alapjául szolgáló infrastruktúra kiépítését és kezelését a szolgáltatás automatikusan kezeli. Csak a megfelelő laboratóriumi élmény előkészítésére összpontosíthat a felhasználók számára. Hagyja, hogy a szolgáltatás kezelje a többit, és a labor virtuális gépeit a közönség számára is elkell járatnia. Egyetlen kattintással több száz virtuális gépre méretezheti laborját.

## <a name="simple-experience-for-your-lab-users"></a>Egyszerű élmény a laborfelhasználók számára 
A laborba meghívott felhasználók azonnal hozzáférhetnek a laboron belül adott erőforrásokhoz. Csak be kell jelentkezniük, hogy a virtuális gépek teljes listáját, amelyekhez több laborban férhetnek hozzá. Egyetlen gombra kattintva csatlakozhatnak a virtuális gépekhez, és elkezdhetnek dolgozni. A felhasználóknak nincs szükségük Azure-előfizetések a szolgáltatás használatához. 

## <a name="cost-optimization-and-tracking"></a>Költségoptimalizálás és -követés  
Tartsa ellenőrzés alatt a költségvetést, és pontosan szabályozza, hogy a tesztkörnyezet felhasználói hány órát használhatnak a virtuális gépeken. Állítsa be az ütemezéseket a laborban, hogy a felhasználók csak a kijelölt időtárolóhelyeken használhassák a virtuális gépeket, vagy beállítsák az ismétlődő automatikus leállítási és indítási időpontokat. Nyomon követheti az egyes felhasználók használatát, és korlátokat állíthat be.

## <a name="example-class-types"></a>Példa osztálytípusok
Az Azure Lab Services többféle osztályhoz is beállíthat laborokat. Tekintse meg a [példa osztálytípusok](class-types.md) az Azure Lab Services cikkben néhány példa típusú osztályok, amelyekhez az Azure Lab Services laborok beállításával. 

## <a name="next-steps"></a>További lépések
Első lépésként az Azure Lab Services használatával hozzon létre egy tesztkörnyezetfiókot, amely az osztálytermi tesztkörnyezetek létrehozásához szükséges:

- [Tesztkörnyezetfiók beállítása](tutorial-setup-lab-account.md)
