---
title: Alkalmazza a frissítéseket az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan importálhatja, és telepítse a Microsoft frissítési csomagokat egy Azure Stackkel integrált rendszereknél.
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
ms.date: 09/07/2018
ms.author: mabrigg
ms.openlocfilehash: 8e4c86a3c9ff40f23a2a758b450d685b81dabc1a
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091900"
---
# <a name="apply-updates-in-azure-stack"></a>Alkalmazza a frissítéseket az Azure Stackben

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek*

Az Azure Stack operátorait szerint alkalmazhatja a Microsoft vagy a frissítés az Azure Stack OEM frissítési csomagokat a felügyeleti portálon csempére. Kell a frissítési csomag letöltéséhez, a csomagfájlok importálása az Azure Stackben és a frissítési csomag telepítése. 

## <a name="download-the-update-package"></a>A frissítési csomag letöltése

Azure Stack egy Microsoft- vagy OEM frissítési csomag nem érhető el, letölti a csomagot olyan helyre, amely az Azure Stack-ból érhetők el, és tekintse át a csomag tartalma. A következő fájlok általában tartalmaz egy frissítési csomagot:

- Egy önkicsomagoló *PackageName*.exe-fájlra. Ez a fájl tartalmazza a frissítés, például a legújabb kumulatív frissítés a Windows Server hasznos.   
- Megfelelő *PackageName*.bin fájlt. Ezeket a fájlokat, adja meg, amelyhez társítva van a tartalom tömörítése a *PackageName*.exe-fájlra. 
- Metadata.xml fájlt. Ez a fájl tartalmazza a frissítés, például a közzétevő, név, előfeltételként szükséges szoftvert, mérete és támogatási elérési út URL-CÍMÉT alapvető adatait.

## <a name="import-and-install-updates"></a>Importálhatja és a frissítések telepítése

Az alábbi eljárás bemutatja, hogyan importálhatja és frissítési csomagok telepítése a felügyeleti portálon.

> [!IMPORTANT]
> Javasoljuk, hogy a karbantartási műveleteket felhasználók értesítése, és hogy úgy ütemezze a normál karbantartási időszakok során munkaidőn kívüli lehető legnagyobb mértékben. Karbantartási műveletek hatással lehet a felhasználó számítási feladatok és a webportálos műveletek.

1. A felügyeleti portálon, válassza ki a **minden szolgáltatás**. Ezt követően a **adatok + tárolás** kategória, jelölje be **tárfiókok**. (Vagy a szűrőmezőbe, kezdje el begépelni **tárfiókok**, és válassza ki azt.)

    ![Bemutatja, hogy hol található a storage-fiókok a portálon](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. Írja be a Szűrő mezőbe **frissítése**, és válassza ki a **updateadminaccount** storage-fiókot.

    ![Bemutatja, hogyan updateadminaccount keresése](media/azure-stack-apply-updates/ApplyUpdates2.png)

3. A Storage-fiók részleteit, a **szolgáltatások**válassza **Blobok**.
 
    ![Bemutatja a blobok a storage-fiók beszerzése](media/azure-stack-apply-updates/ApplyUpdates3.png) 
 
4. A **Blob service**válassza **+ tároló** tároló létrehozásához. Adjon meg egy nevet (például *frissítés – 1709-es*), majd válassza ki **OK**.
 
     ![Bemutatja, hogyan tároló felvétele a storage-fiókban](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. A tároló létrehozása után kattintson a tároló nevére, és kattintson **feltöltése** a csomag fájlok feltöltése a tárolóba.
 
    ![Bemutatja, hogyan tölthet fel a csomagfájlok](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. A **blob feltöltése**, kattintson a mappa ikonra, és keresse meg a frissítési csomag .exe fájlt, majd kattintson a **nyílt** a fájl explorer-ablakban.
  
7. A **blob feltöltése**, kattintson a **feltöltése**. 
  
    ![Látható minden alkalmazáscsomag-fájl feltöltése](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Ismételje meg a 6 és 7 a *PackageName*állapotára vonatkozó .bin és Metadata.xml fájlt. Ha tartalmazza, ne importálja a kiegészítő Notice.txt fájlt.
9. Ha elkészült, az értesítések (Harang ikon a portál jobb felső sarokban) tekintheti meg. Az értesítés jelzi, hogy a feltöltés befejeződött. 
10. Lépjen vissza a frissítés csempét az irányítópulton. A csempe kell azt jelzik, hogy a frissítés érhető el. Kattintson a csempére, és tekintse át az újonnan hozzáadott frissítési csomag.
11. Telepítheti a frissítést, válassza ki a csomagot, amely van megjelölve **készen áll** vagy kattintson a jobb gombbal a csomagot, és kattintson **frissítés most**, vagy kattintson a **frissítés most** tetejénél művelet .
12. Frissítési csomag telepítése gombra kattint, megtekintheti az állapotát a **frissítési menetet részletek** területen. Itt is kattinthat **teljes naplók letöltéséhez** letöltéséhez a rendszernapló fájljaiban.
13. A frissítés befejeződött, a frissítés csempét jelenít meg a frissített Azure Stack-verzió.

Manuálisan törölheti frissítések a storage-fiókból az Azure Stack telepítése után. Az Azure Stack rendszeresen keresi a korábbi frissítési csomagokat, és eltávolítja azokat a storage-ból. Azure Stack is igénybe vehet, távolítsa el a régi csomagot két hét.

## <a name="next-steps"></a>További lépések

- [Kezelheti a frissítéseket az Azure Stack áttekintése](azure-stack-updates.md)
- [Az Azure Stack karbantartási szabályzat](azure-stack-servicing-policy.md)
