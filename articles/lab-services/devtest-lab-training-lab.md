---
title: Az Azure DevTest Labs használata képzéshez | Microsoft dokumentumok
description: Ez a cikk részletes lépéseket tartalmaz, amelyek végrehajtásához hozzon létre egy labort az Azure DevTest Labs betanításához.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 8f0a930d6e3c04548ade71f6d4e3294114eb60a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759977"
---
# <a name="use-azure-devtest-labs-for-training"></a>Az Azure DevTest Labs használata az oktatáshoz
Az Azure DevTest Labs számos kulcsfontosságú forgatókönyv megvalósításához használható a fejlesztés/tesztelés mellett. Az egyik ilyen forgatókönyv az, hogy hozzon létre egy labort a képzéshez. Az Azure DevTest Labs lehetővé teszi, hogy hozzon létre egy labor, ahol egyéni sablonokat, amelyek minden gyakornok használhatja, hogy azonos és elszigetelt környezetek képzéséhez. Szabályzatokat alkalmazhat annak biztosítására, hogy a képzési környezetek csak akkor legyenek elérhetők minden gyakornok számára, amikor szükségük van rájuk, és elegendő erőforrást – például virtuális gépeket – tartalmaznak a képzéshez. Végül könnyedén megoszthatja a labort a gyakornokokkal, amelyeket egy kattintással elérhetnek.

![A DevTest Labs használata a képzéshez](./media/devtest-lab-training-lab/devtest-lab-training.png)

Az Azure DevTest Labs megfelel az alábbi követelményeknek, amelyek szükségesek a képzés elvégzéséhez bármilyen virtuális környezetben: 

* A gyakornokok nem láthatják a más gyakornokok által létrehozott virtuális gépeket
* Minden edzőgépnek azonosnak kell lennie
* A gyakornokok gyorsan elláthatják képzési környezetüket
* A költségek szabályozása annak biztosításával, hogy a gyakornokok ne kaphassanak több virtuális gépet, mint amennyi a képzéshez szükséges, valamint a virtuális gépek leállítása, amikor nem használják őket
* Egyszerűen megoszthatja a képzési labort minden gyakornokkal
* Használja újra és újra az edzéslabort

Ebben a cikkben megismerheti a különböző Azure DevTest Labs funkciók, amelyek a korábban leírt képzési követelmények és a részletes lépéseket, amelyek segítségével egy labor betanítása.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Oktatás megvalósítása az Azure DevTest Labs segítségével
1. **A labor létrehozása** 
   
    Labs a kiindulási pont az Azure DevTest Labs. Miután létrehozott egy tesztkörnyezetet, olyan feladatokat hajthat végre, mint például felhasználók (gyakornokok) hozzáadása a laborhoz, házirendek beállítása a költségek szabályozásához, a gyorsan létrehozható virtuálisgép-lemezképek definiálása és egyebek.   
   
    További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Labor létrehozása az Azure DevTest Labs szolgáltatásban](devtest-lab-create-lab.md) |Ismerje meg, hogyan hozhat létre labort az Azure DevTest Labs az Azure Portalon. |
2. **Betanítási virtuális gépek létrehozása percek alatt kész piactéri lemezképek és egyéni lemezképek használatával** 
   
    Az Azure Marketplace-en a képek széles választékából választhat kész képeket, és elérhetővé teheti azokat a laborban lévő gyakornokok számára. Ha a kész lemezképek nem felelnek meg a követelményeknek, létrehozhat egy egyéni lemezképet egy tesztkörnyezetvirtuális gép létrehozásával az Azure Marketplace-ről, az összes szoftver telepítése, amely szükséges a betanításhoz, és mentse a virtuális gép egyéni lemezképként a laborban. 
   
    További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Azure Marketplace-rendszerképek konfigurálása](devtest-lab-configure-marketplace-images.md) |Ismerje meg, hogyan listázhatja az Azure Marketplace-lemezképeket; csak a képzéshez kívánt képek et szeretné kiválasztani. |
   | [Egyéni lemezkép létrehozása](devtest-lab-create-template.md) |Hozzon létre egy egyéni lemezképet a képzéshez szükséges szoftver előzetes telepítésével, hogy a gyakornokok gyorsan létrehozhathassanak egy virtuális gépet az egyéni lemezkép használatával. |
3. **Újrafelhasználható sablonok létrehozása az oktatógépekhez** 
   
    Az Azure DevTest Labs képlete a virtuális gép létrehozásához használt alapértelmezett tulajdonságértékek listája. Képletet hozhat létre a laborban egy lemezkép, a virtuális gép méretének (a CPU és a RAM kombinációja) és egy virtuális hálózat kiválasztásával. Minden gyakornok láthatja a képletet a laborban, és használja a virtuális gép létrehozásához. 
   
    További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [DevTest Labs képletek kezelése virtuális gépek létrehozásához](devtest-lab-manage-formulas.md) |Megtudhatja, hogyan hozhat létre képletet egy lemezkép, a virtuális gép méretének (a CPU és a RAM kombinációja) és a virtuális hálózat felvételével. |
4. **Ellenőrzési költségek**
   
    Az Azure DevTest Labs lehetővé teszi, hogy a laborban megadott szabályzatadja meg a tesztkörnyezetben egy gyakornok által létrehozható virtuális gépek maximális számát. 
   
    Ha többnapos betanítást végez, és szeretné leállítani az összes virtuális gépet a nap egy adott időpontjában, majd a következő napon automatikusan újra szeretné indítani őket, ezt egyszerűen elvégezheti az automatikus leállítás és az automatikus indítási házirendek beállításával a laborban. 
   
    Végül, ha a betanítás befejeződött, egyszerre törölheti az összes virtuális gépet egyetlen PowerShell-parancsfájl futtatásával. 
   
    További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Laborszabályzatok definiálása](devtest-lab-set-lab-policy.md) |A környezetkörnyezetházirendek beállításával szabályozhatja a költségeket. |
   | [Az összes tesztkörnyezetbeli virtuális gép törlése PowerShell-parancsfájl használatával](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Törölje az összes laborok egy műveletben, ha a betanítás befejeződött. |
5. **Ossza meg a labort minden gyakornokkal**
   
    A laborok közvetlenül elérhetők egy link segítségével, amelyet megoszt a gyakornokokkal. A gyakornokok nem is kell, hogy egy Azure-fiókot, mindaddig, amíg van egy [Microsoft-fiók.](devtest-lab-faq.md#what-is-a-microsoft-account) A gyakornokok nem láthatják a más gyakornokok által létrehozott virtuális gépeket.  
   
    További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Gyakornok felvétele egy laborba az Azure DevTest Labs-ben](devtest-lab-add-devtest-user.md) |Az Azure Portal segítségével gyakornokokat vehet fel a képzési laborba. |
   | [Gyakornokok hozzáadása a laborhoz PowerShell-parancsfájl használatával](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |A PowerShell segítségével automatizálhatja a gyakornokok hozzáadását a képzési laborba. |
   | [Hivatkozás beszerezni a labort](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Ismerje meg, hogyan érhető el közvetlenül egy tesztkörnyezet hivatkozáson keresztül. |
6. **Újra és újra használja fel a labort** 
   
    A laborok létrehozását , beleértve az egyéni beállításokat is, automatizálhatja egy Erőforrás-kezelő sablon létrehozásával, és azonos laborok újbóli és újra létrehozásához. 
   
    További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Labor létrehozása Erőforrás-kezelő sablon nal](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Laborokat hozhat létre az Azure DevTest Labs ben az Erőforrás-kezelő sablonjainak használatával. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

