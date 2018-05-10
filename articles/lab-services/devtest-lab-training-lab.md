---
title: Azure DevTest Labs használatát képzés |} Microsoft Docs
description: 'Útmutató: Azure DevTest Labs képzési helyzetekben használhatja.'
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 85eddaaf101c3e85eca7514b04660163d23c1c80
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-devtest-labs-for-training"></a>Azure DevTest Labs használatát képzés
Az Azure DevTest Labs segítségével fejlesztési és tesztelési célú túl sok főbb forgatókönyvek megvalósításához. Ezek a forgatókönyvek egyik beállíthat egy tesztkörnyezetet képzési. Az Azure DevTest Labs létrehozhat egy tesztkörnyezetet, ahol megadhatja az egyéni sablonok, amelyek minden tanuló képzési azonos és elkülönített környezetek létrehozása teszi lehetővé. Annak érdekében, hogy képzési környezetekben minden egyes tanuló rendelkezésére csak akkor, ha szüksége van rájuk, és tartalmaznak elég erőforrások – például a virtuális gépek - képzés szükséges házirendeket is alkalmazhat. Végül könnyedén megoszthatja a labor képzésben, amelyek az egy kattintással eléréséhez.

![DevTest Labs használatát képzés](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure DevTest Labs megfelel a következő tartson képzéseket minden virtuális környezetben szükséges: 

* Képzésben más képzésben által létrehozott virtuális gépek nem látható.
* Minden képzési gép azonosnak kell lennie.
* Képzésben gyorsan építhető ki a képzés környezetek
* Biztosítsa, hogy a képzésben nem olvasható be a további virtuális gépek van szükségük a képzés és is virtuális gépek leállítása, ha azok nem használja őket, mint a költségek szabályozásához
* Egyszerűen megoszthatja a képzés labor minden tanuló a
* A képzési labor újra és újra felhasználhatja

Ebből a cikkből megismerheti a korábban ismertetett képzési követelmények és a részletes lépéseket, amelyek követésével beállíthat egy tesztkörnyezetet képzési használható különböző Azure DevTest Labs szolgáltatásairól.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Az Azure DevTest Labs végrehajtási képzési
1. **A labor létrehozása** 
   
    Labs szerepelnek a Azure DevTest Labs szolgáltatásban. Labor létrehozása után feladatokat hajthat végre olyan, laborteszteket, az ad felhasználók (képzésben) házirendek beállítása a kapcsolatos költségek szabályozását, gyorsan hozhat létre Virtuálisgép-lemezképeket, és több megadása.   
   
    Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Labor létrehozása a Azure DevTest Labs szolgáltatásban](devtest-lab-create-lab.md) |Útmutató a labor létrehozása a Azure DevTest Labs szolgáltatásban az Azure portálon. |
2. **Előre elkészített piactéren elérhető rendszerkép és az egyéni lemezképek használatával percek alatt képzési virtuális gépek létrehozása** 
   
    Válasszon az előre elkészített képek széles képek az Azure piactéren, és elérhetővé teszi azokat a laborkörnyezetben képzésben. Ha az előre elkészített lemezképeket nem megfelelnek az elvárásainak, hozzon létre egy virtuális gép egy előre elkészített lemezképet használja az Azure piactérről, amelyekre szüksége van a képzési és a virtuális gép mentése az, amikor egyéni lemezképként az összes szoftver telepítése, amikor egyéni kép is létrehozhat. 
   
    Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Azure piactéren elérhető rendszerkép konfigurálása](devtest-lab-configure-marketplace-images.md) |Megtudhatja, hogyan zajlik engedélyezett Azure piactéren elérhető rendszerkép; érdemes a képzés elérhetővé teszi a kiválasztható elemek csak a képeket. |
   | [Egyéni lemezkép létrehozása](devtest-lab-create-template.md) |Hozzon létre egy egyéni lemezképet előre telepíteni a szoftvert, a képzési, hogy képzésben gyorsan létre tud hozni egy virtuális Gépet, az egyéni lemezkép használata szükséges. |
3. **A képzési gépek újrafelhasználható sablonok létrehozása** 
   
    Az Azure DevTest Labs képlet egy virtuális gép létrehozásához használt alapértelmezett tulajdonság értékek listáját. Képlet létrehozhatja a laborban lemezkép, a Virtuálisgép-méretet (kombinációja Processzor és memória szempontjából) és egy virtuális hálózatot válassza háttérszínnek. Minden tanuló tekintse meg a képlet a laborkörnyezetben, és a virtuális gépek létrehozására használható. 
   
    Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Virtuális gépek létrehozása a DevTest Labs képletek kezelése](devtest-lab-manage-formulas.md) |Ismerje meg, hogyan is létrehozhat egy képletet fel egy lemezképet, a Virtuálisgép-méretet (Processzor és memória szempontjából kombinációja) és a virtuális hálózat. |
4. **Költségek szabályozása**
   
    Az Azure DevTest Labs lehetővé teszi, hogy meg kell adnia egy házirendet a laborban a laborban vizsgában létrehozott virtuális gépek maximális számának megadásához. 
   
    Ha többnapos képzési céljából, és szeretné, hogy a virtuális gépek leállítása a nap adott időpontban, és automatikusan indítsa újra őket a következő napon, egyszerűen hajthatja végre, hogy az automatikus rendszerleállítást beállításával, és automatikusan elinduló házirendek a tesztkörnyezetben. 
   
    Végezetül betanítás törölheti a virtuális gépek egyszerre egy PowerShell-parancsfájl futtatásával. 
   
    Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Laborszabályzatok definiálása](devtest-lab-set-lab-policy.md) |A labor házirendek beállításával kapcsolatos költségek szabályozását. |
   | [Törölje a labor virtuális gépeken, egy PowerShell-parancsfájl használatával](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Egyetlen művelettel összes labs törlése, a képzési befejezésekor. |
5. **A labor megosztása minden tanuló**
   
    Labs közvetlenül elérhetők a képzésben közösen-kapcsolaton keresztül. A képzésben nem is kell rendelkezik Azure-fiókkal, mindaddig, amíg azok rendelkeznek egy [Microsoft-fiók](devtest-lab-faq.md#what-is-a-microsoft-account). Képzésben más képzésben által létrehozott virtuális gépek nem látható.  
   
    Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Vizsgában hozzáadása egy laborhoz a Azure DevTest Labs szolgáltatásban](devtest-lab-add-devtest-user.md) |Az Azure portál segítségével képzésben hozzá a képzés tesztkörnyezet. |
   | [A PowerShell parancsfájl használatával labor képzésben hozzáadása](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |PowerShell használatával automatizálhatja a képzés laborkörnyezetben hozzáadását képzésben. |
   | [Szerezzen be egy hivatkozást a laborkörnyezetben](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Ismerje meg, hogyan labor közvetlenül elérhető hivatkozás segítségével. |
6. **A labor újra és újra felhasználhatja** 
   
    Labor létrehozása, egyéni beállításokat, beleértve a Resource Manager-sablonok létrehozásával, és újra és újra létre azonos labs segítségével automatizálható. 
   
    Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [A Resource Manager sablonnal labor létrehozása](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Hozzon létre labs Azure DevTest Labs Resource Manager-sablonok használatával. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

