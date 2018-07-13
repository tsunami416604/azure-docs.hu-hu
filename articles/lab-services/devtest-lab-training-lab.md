---
title: Az Azure DevTest Labs használata képzéshez |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure DevTest Labs-képzési forgatókönyveihez.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38666794"
---
# <a name="use-azure-devtest-labs-for-training"></a>Az Azure DevTest Labs használata képzéshez
Az Azure DevTest Labs fejlesztési és tesztelési mellett számos kulcsfontosságú forgatókönyvek megvalósításához használható. Ezek a forgatókönyvek egyik képzési tesztkörnyezetek beállításához. Az Azure DevTest Labs lehetővé teszi, hogy egy tesztkörnyezet, ahol megadhatja, hogy minden tanuló segítségével képzéshez azonosak, és elkülönített környezeteket hozhat létre egyéni sablonok létrehozása. Annak érdekében, hogy képzési környezetek elérhető minden tanuló, csak akkor, amikor szükség van rájuk, és tartalmaz elég erőforrások – például a virtuális gépek – szükséges a képzés-szabályzatok alkalmazása. Végül könnyedén megoszthatja a labor résztvevők, amely egyetlen kattintással eléréséhez.

![DevTest Labs használata képzéshez](./media/devtest-lab-training-lab/devtest-lab-training.png)

Az Azure DevTest Labs megfelel a következő képzést bármely virtuális környezetben szükséges: 

* Résztvevők nem látható egyéb résztvevők által létrehozott virtuális gépek
* Minden Azure-képzési gép azonosnak kell lenniük.
* Résztvevők gyorsan üzembe helyezhet a betanítási környezetek
* Költség szabályozhatja, hogy a résztvevők nem olvasható be a szükséges a képzési, valamint a virtuális gépek leállítása, ha azok nem használja őket több virtuális gépet biztosítva
* A képzési labor könnyedén megoszthatja minden tanuló
* A képzési labor újra és újra felhasználhatja

Ez a cikk bemutatja, amely megfelel a dátumtáblázatok fenti képzési követelményekkel és részletes lépéseket követve állítsa be a képzési tesztkörnyezetek segítségével különböző Azure DevTest Labs funkciókról.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Az Azure DevTest Labs végrehajtási képzés
1. **A tesztkörnyezet létrehozása** 
   
    Laborok szerepelnek az Azure DevTest Labs szolgáltatásban. Miután létrehozta a labor, feladatokat hajthat végre ilyen, a felhasználók (résztvevők) hozzáadása a labor házirendek beállítását a költségek csökkentését, hozhat létre gyorsan Virtuálisgép-rendszerképek és a további megadása.   
   
    Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Labor létrehozása az Azure DevTest Labs szolgáltatásban](devtest-lab-create-lab.md) |Útmutató az Azure DevTest Labs labor létrehozása az Azure Portalon. |
2. **Képzési virtuális gépek létrehozása és előre elkészített marketplace egyéni rendszerképek segítségével percek alatt** 
   
    Válassza ki a képek számos előre elkészített rendszerképek az Azure piactéren, és elérhetővé teheti őket a lab-ben a résztvevők számára. Ha a beépített rendszerképek nem felelnek meg igényeinek, létrehozhat egyéni rendszerkép egy virtuális gép egy beépített rendszerkép használatával Azure piactéren, a képzés és a virtuális gép mentése a labor létrehozása az egyéni rendszerkép szükséges szoftverek telepítése tesztlabor létrehozásával. 
   
    Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Az Azure piactér képeinek konfigurálása](devtest-lab-configure-marketplace-images.md) |Ismerje meg, hogyan is engedélyezett az Azure Marketplace-rendszerképek; kijelölés elérhetővé tétele csak a lemezképeket szeretne a képzést. |
   | [Egyéni rendszerkép létrehozása](devtest-lab-create-template.md) |Hozzon létre egy egyéni rendszerkép előre telepíti a szoftvert, szüksége lesz a tanfolyamot úgy, hogy a résztvevők gyorsan létre tud hozni egy virtuális Gépet az egyéni rendszerkép használatával. |
3. **Képzési gépek újrafelhasználható sablonok létrehozása** 
   
    Az Azure DevTest Labs szolgáltatásban képletet az virtuális gép létrehozásához használt alapértelmezett tulajdonságértékeket listája. A lab-ben egy képlet kiválasztásával a képet, a virtuális gép méretét (kombinációjával Processzor és memória) és egy virtuális hálózatot hozhat létre. Minden tanuló is tekintse meg a képlet a tesztkörnyezetben, és ezzel hozzon létre egy virtuális Gépet. 
   
    Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Virtuális gépek létrehozásához a DevTest Labs képletek kezelése](devtest-lab-manage-formulas.md) |Ismerje meg, hogyan hozhat létre egy képlet szerint vesz fel a képet, a virtuális gép mérete (Processzor és memória kombinációjával) és a egy virtuális hálózatot. |
4. **Költségek korlátozására**
   
    Az Azure DevTest Labs lehetővé teszi egy szabályzat beállítása a tesztkörnyezetben, adja meg a tanuló a lab-ben létrehozott virtuális gépek maximális számát. 
   
    Ha vannak folytatott többnapos képzés, és állítsa le a virtuális gépek a nap egy adott időpontban, és automatikusan indítsa újra őket a következő napon, egyszerűen hajthatja végre, hogy az automatikus leállítási beállításával és automatikusan elinduló házirendek a tesztkörnyezetben. 
   
    Végül képzési befejezésekor törölheti a virtuális gépek egyszerre egy egyetlen PowerShell-parancsprogram futtatásával. 
   
    Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Laborszabályzatok definiálása](devtest-lab-set-lab-policy.md) |Költségek szabályozása szabályzatok beállítása a tesztkörnyezetben. |
   | [A labor virtuális gépeken a PowerShell-szkripttel törlése](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |A képzési befejezésekor, törölje a egyetlen művelettel Labs szolgáltatásban. |
5. **A labor megosztása minden tanuló**
   
    Laborok közvetlenül elérhetők a résztvevők megoszt egy hivatkozással. A résztvevők nem is kell rendelkeznie egy Azure-fiókkal is rendelkeznek egy [Microsoft-fiók](devtest-lab-faq.md#what-is-a-microsoft-account). Résztvevők más résztvevők által létrehozott virtuális gépek nem látható.  
   
    Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [A tanuló hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet](devtest-lab-add-devtest-user.md) |Az Azure portal használatával résztvevők ad hozzá a betanítási labor. |
   | [A labor létrehozása a PowerShell-szkripttel résztvevők hozzáadása](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |A képzési laborhoz hozzáadásának résztvevők automatizálása a PowerShell használatával. |
   | [A labor létrehozása hivatkozás beszerzése](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Ismerje meg, hogyan labor közvetlenül elérhető hivatkozás segítségével. |
6. **Újra és újra felhasználhatja a labor létrehozása** 
   
    Labor létrehozása, egyéni beállításokat, beleértve egy Resource Manager-sablon létrehozásával, és újra és újra létre azonos labs segítségével automatizálható. 
   
    Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Resource Manager-sablonnal labor létrehozása](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Tesztkörnyezetek létrehozása az Azure DevTest Labs szolgáltatásban létrehozott Resource Manager-sablonok használatával. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

