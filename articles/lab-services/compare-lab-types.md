---
title: Az Azure Lab Services különböző típusú tesztkörnyezeteinek összehasonlítása | Microsoft Docs
description: A jelen cikk az Azure Lab Services szolgáltatással létrehozható különböző típusú tesztkörnyezeteket ismerteti és hasonlítja össze.
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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 7e86ba2b7fc729bd4663503282a936a5eaddf3ca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637873"
---
# <a name="compare-managed-labs-in-azure-lab-services-and-devtest-labs"></a>Az Azure Lab Services és DevTest Labs felügyelt tesztkörnyezeteinek összehasonlítása
Kétféle tesztkörnyezetet hozhat létre, **felügyelt tesztkörnyezetet** az Azure Lab Services szolgáltatással, és **egyéni tesztkörnyezetet** az Azure DevTest Labs szolgáltatással. Ha csak azt szeretné megadni, hogy mire van szüksége a tesztkörnyezetben, és a szolgáltatás fogja beállítani és felügyelni a tesztkörnyezethez szükséges infrastruktúrát, válassza a **felügyelt tesztkörnyezetek** egyikét. Jelenleg az **osztályterem-tesztkörnyezet** a felügyelt tesztkörnyezetek egyetlen olyan típusa, amelyet az Azure Lab Services szolgáltatással létrehozhat. Ha saját infrastruktúrát szeretne felügyelni, hozzon létre egy tesztkörnyezetet az Azure DevTest Labs szolgáltatással.

Az alábbi szakaszokban részletesen is tárgyaljuk ezeket a tesztkörnyezeteket. 

## <a name="managed-labs"></a>Felügyelt tesztkörnyezetek
A felügyelt tesztkörnyezetek az adott igényeknek megfelelő különböző típusú tesztkörnyezeteket nyújtanak. Az Azure Lab Services jelenleg csak az **osztályterem-tesztkörnyezetet** támogatja felügyelt tesztkörnyezetként. A felügyelt tesztkörnyezetekkel minimális beállítást követően azonnal elkezdheti a munkát. A szolgáltatás maga gondoskodik a tesztkörnyezet infrastruktúrájának átfogó kezeléséről, a virtuális gépek indításától a hibák kezelésén keresztül az infrastruktúra méretezéséig. Felügyelt tesztkörnyezet létrehozásához először létre kell hoznia egy tesztkörnyezetfiókot a vállalat számára. A tesztkörnyezetfiók központi fiókként szolgál a vállalat összes tesztkörnyezetének a felügyeletéhez. 

Amikor Azure-erőforrásokat hoz létre és használ ezekben a felügyelt tesztkörnyezetekben, a szolgáltatás belső Microsoft-előfizetésekben hozza létre és felügyeli az erőforrásokat, nem pedig az Ön Azure-előfizetésében. A szolgáltatás belső Microsoft-előfizetésekben követi nyomon ezen erőforrások használatát. A használat számlázása a tesztkörnyezetfiókot tartalmazó Azure-előfizetésben történik.   

A **felügyelt tesztkörnyezetek néhány alkalmazási helyzete**: 

- A pontosan a tanórához szükséges dolgokkal konfigurált virtuális gépekből álló tesztkörnyezet biztosítása a diákok számára. Korlátozott mennyiségű idő biztosítása minden diák számára a virtuális gépek használatához a házi feladatok vagy személyes projektek elvégzéséhez.
- Nagy teljesítményű számítások végrehajtására képes virtuális gépek készletének beállítása nagy számítási vagy grafikai igényű kutatáshoz. A virtuális gépek futtatása igény szerint, majd a gépek törlése, miután végzett. 
- Az iskola fizikai számítógéptermének áthelyezése a felhőbe. A virtuális gépek számának automatikus méretezése a tesztkörnyezetben beállított maximális használatnak és költséghatárnak megfelelően.  
- Virtuális gépekből álló tesztkörnyezet gyors kiépítése Hackathon-verseny megtartásához. A tesztkörnyezet törlése egyetlen kattintással, miután végzett. 


## <a name="devtest-labs"></a>DevTest Labs
Felmerülhetnek olyan forgatókönyvek, amikor a saját előfizetésén belül szeretne felügyelni minden infrastruktúrát és konfigurációt. Ehhez létrehozhat egy tesztkörnyezetet az Azure DevTest Labs használatával az Azure Portalon. Ezekhez a tesztkörnyezetekhez nem kell tesztkörnyezetfiókot létrehoznia. Ezek a tesztkörnyezetek nem jelennek meg a tesztkörnyezetfiókban (amelyet a felügyelt tesztkörnyezetekhez kell létrehozni).  

A **DevTest Labs tesztkörnyezetek néhány alkalmazási helyzete**: 

- Virtuális gépekből álló tesztkörnyezet gyors kiépítése Hackathon-verseny megtartásához, vagy egy konferencia gyakorlati foglalkozásához. A tesztkörnyezet törlése egyetlen kattintással, miután végzett. 
- Az alkalmazással konfigurált virtuális gépek készletének létrehozása, és annak lehetővé tétele, hogy a csapat könnyedén használhasson virtuális gépeket programhiba-vizsgálathoz.  
- A fejlesztők számára szükséges eszközök mindegyikével konfigurált virtuális gépek biztosítása. Automatikus indítás és leállítás ütemezése a költségek minimalizálása érdekében. 
- A tesztgépekből álló tesztkörnyezet ismételt létrehozása az üzemi környezet részeként. A legújabb összetevők tesztelése, majd a tesztgépek törlése, miután végzett. 
- Különböző módon konfigurált virtuális gépek és több tesztügynök beállítása a méretezés és a teljesítmény teszteléséhez. 
- Képzési foglalkozások biztosítása az ügyfeleknek a termék legújabb verziójával konfigurált tesztkörnyezettel. Korlátozott mennyiségű idő biztosítása minden ügyfél számára a tesztkörnyezet használatához. 


## <a name="managed-labs-vs-devtest-labs"></a>Felügyelt tesztkörnyezetek és DevTest Labs
Az alábbi táblázat az Azure Lab Services által támogatott tesztkörnyezetek két típusát hasonlítja össze: 

| Szolgáltatások | Felügyelt tesztkörnyezetek | DevTest Labs |
| -------- | ----------------  | ---------- |
| Az Azure-infrastruktúra felügyelete a tesztkörnyezetben. |  A szolgáltatás automatikusan felügyeli. | A felhasználó saját maga felügyeli.  |
| Beépített rugalmasság az infrastruktúrával kapcsolatos problémákkal szemben | A szolgáltatás automatikusan kezeli. | A felhasználó saját maga felügyeli.  |
| Előfizetés-kezelés | A szolgáltatás kezeli az erőforrások kiosztását a szolgáltatást támogató Microsoft-előfizetéseken belül. A szolgáltatás automatikusan kezeli a méretezést. | A felhasználó saját maga felügyeli a saját Azure-előfizetésében. Az előfizetések automatikusan méretezését a szolgáltatás nem biztosítja. |
| Azure Resource Manager-alapú üzemi környezet a tesztkörnyezetben | Nem érhető el | Elérhető |

## <a name="next-steps"></a>További lépések
Kezdje meg egy tesztkörnyezet létrehozását az Azure Lab Services használatával:

- [Osztályterem-tesztkörnyezet beállítása](classroom-labs/tutorial-setup-classroom-lab.md)
- [Tesztkörnyezet beállítása](tutorial-create-custom-lab.md)
