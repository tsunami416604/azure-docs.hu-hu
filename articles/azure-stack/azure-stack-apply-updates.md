---
title: Azure-készletben frissítések alkalmazása |} Microsoft Docs
description: Megtudhatja, hogyan importálja, és telepítse a Microsoft frissítési csomagokat egy integrált Azure verem rendszer.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: mabrigg
ms.openlocfilehash: 0f23216c6aced60dc651a0f10179281bc9a29c2c
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
ms.locfileid: "29802651"
---
# <a name="apply-updates-in-azure-stack"></a>Azure-készletben frissítések alkalmazása

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Egy Azure verem operátorként a felügyeleti portálon csempe Azure verem csomagok a frissítés használatával a Microsoft update is alkalmazhat. Kell a Microsoft update-csomag, a fájlok importálása a csomag Azure verem és majd a frissítés telepítéséhez. 

## <a name="download-the-update-package"></a>A frissítési csomag

A Microsoft Azure verem csomag nem érhető el, töltse le a csomag egy helyre, amely elérhető Azure-vermet, és tekintse át a csomag tartalma. Frissítések jellemzően a következő fájlok áll:

- Egy önkicsomagoló *csomagnév*.exe fájlt. Ez a fájl tartalmazza a frissítés, például a legújabb összesítő frissítéssel a Windows Server hasznos.   
- Megfelelő *csomagnév*.bin fájlt. Ezeket a fájlokat, adja meg, amelyhez társítva van a tartalom tömörítése a *csomagnév*.exe fájlt. 
- Metadata.xml fájlt. Ez a fájl tartalmazza a frissítés, például a közzétevő, név, előfeltétel, mérete és támogatási elérési URL-címe alapvető adatait.

## <a name="import-and-install-updates"></a>Importálja és a frissítések telepítése

Az alábbi eljárás bemutatja, hogyan importálását és frissítési csomagokat telepítheti a felügyeleti portálon.

> [!IMPORTANT]
> Határozottan javasoljuk, hogy a karbantartási műveleteket sem felhasználók értesítése, és a normál karbantartási időszak alatt munkaidőn kívüli lehetőség szerint úgy ütemezni. A karbantartási műveleteket hatással lehet a felhasználó munkaterhelések és a portál műveletek.

1. Válassza a felügyeleti portál **további szolgáltatások**. Ekkor a a **adatok + tárolás** kategória, jelölje be **tárfiókok**. (Vagy a Szűrő mezőbe kezdje el begépelni **tárfiókok**, és jelölje ki.)

    ![Bemutatja, hol található a tárfiók a portálon](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. A Szűrő mezőbe írja be **frissítése**, és válassza ki a **updateadminaccount** tárfiók.

    ![Bemutatja, hogyan updateadminaccount keresése](media/azure-stack-apply-updates/ApplyUpdates2.png)

3. A tárolóban lévő fiók részleteit, a **szolgáltatások**, jelölje be **Blobok**.
 
    ![Bemutatja, hogyan a tárfiók a BLOB beolvasása](media/azure-stack-apply-updates/ApplyUpdates3.png) 
 
4. A **Blob szolgáltatás**, jelölje be **+ tároló** hozhat létre tárolót. Adja meg a nevét (például *frissítés-1709*), majd válassza ki **OK**.
 
     ![Bemutatja, hogyan adjon hozzá egy tárolót a tárfiókban lévő](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. A tároló létrehozása után kattintson a tároló neve, majd **feltöltése** a csomagfájlok feltölteni a tárolóba.
 
    ![A fájlok feltöltéséről](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. A **feltöltése a blob**, a mappa ikonra, és keresse meg a frissítési csomag .exe fájlt, majd kattintson a **nyitott** a fájl explorer-ablakban.
  
7. A **feltöltése a blob**, kattintson a **feltöltése**. 
 
    ![Azt minden fájl feltöltése](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Ismételje meg a 6 és 7 a *csomagnév*.bin és Metadata.xml fájlt. A kiegészítő Notice.txt fájl nem importálható, ha tartalmazza.
9. Ha befejezte, az értesítések (a portál jobb felső sarkában található harang ikonra) tekintheti meg. Az értesítések kell jeleznie, hogy befejeződött a feltöltés. 
10. Lépjen vissza a frissítés csempe az irányítópulton. A csempe kell jeleznie, hogy egy frissítés érhető el. Kattintson a csempére kattintva tekintse át az újonnan hozzáadott frissítési csomag.
11. A frissítés telepítéséhez válassza ki a csomagot, amely van megjelölve, **készen** és, vagy kattintson a jobb gombbal a csomagot, és válassza ki **frissítés most**, vagy kattintson a **frissítés most** művelet felső részén .
12. Frissítési csomag telepítése gombra kattintva megtekintheti az állapotát a **frissítési menetnek részletek** területen. Itt is kattinthat **teljes naplók letöltéséhez** letölteni a rendszernapló fájljaiban.
13. A frissítés befejezése után a frissítés csempe a frissített Azure verem verzióját jeleníti meg.

## <a name="next-steps"></a>További lépések

- [Kezelheti a frissítéseket a Azure verem – áttekintés](azure-stack-updates.md)
- [Az Azure verem karbantartása házirend](azure-stack-servicing-policy.md)
