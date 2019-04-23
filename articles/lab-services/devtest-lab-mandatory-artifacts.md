---
title: Adja meg a kötelező összetevők számára az Azure DevTest Labs |} A Microsoft Docs
description: Ismerje meg, hogyan adja meg a kötelező összetevők, amelyek a virtuális gépeken (VM) a tesztkörnyezetben bármely felhasználó által kiválasztott összetevők telepítése előtt telepíteni kell.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: spelluru
ms.openlocfilehash: 090236ec3647c7c3e38eb862780a615f854e952b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795406"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Adja meg a kötelező összetevők a tesztkörnyezethez az Azure DevTest Labs szolgáltatásban
A labor tulajdonosként minden olyan gép, a lab-ben létrehozott alkalmazott kötelező összetevők is megadhat. Képzelje el egy forgatókönyvet, ahol szeretné az egyes gépek kell csatlakoztatni a vállalati hálózathoz a lab-ben. Ebben az esetben minden labor-felhasználónak kell egy tartományhoz való csatlakozás összetevő hozzáadása a virtuális gép létrehozásakor, győződjön meg arról, hogy a gép csatlakoztatva van a vállalati tartományhoz. Más szóval lab-felhasználó lényegében kellene hozza létre újból a gép abban az esetben, ha elfelejtették a alkalmazni a kötelező összetevők a számítógépen. Labortulajdonosként ellenőrizze a tartományhoz való csatlakozás összetevő egy kötelező összetevő, a tesztkörnyezetben. Ebben a lépésben gondoskodik arról, hogy az egyes gépek csatlakoztatva van a vállalati hálózathoz, és menti az idő és munka a labor-felhasználók számára.
 
Más kötelező összetevők lehetnek egy ismert eszköz, amely a csapat használ, vagy olyan platformon termékspecifikus biztonsági csomag, amely a stb. alapértelmezés szerint minden gép rendelkeznie kell. Röviden semmilyen közös szoftver, amely a laborkörnyezetben található minden gépnek rendelkeznie kell válik egy kötelező összetevő. Ha egy egyéni lemezkép készítése egy gépet, amely kötelező összetevők alkalmaztak, majd hozzon létre egy új gépet abból a rendszerképből a kötelező összetevők rendszer újra alkalmazza a gép a létrehozása során. Ezt a viselkedést azt is jelenti, hogy annak ellenére, hogy az egyéni rendszerkép régi, minden alkalommal, amikor a legtöbb frissített verzió kötelező összetevők létrehozása egy gép származó beállítva rajta a létrehozási folyamat során. 
 
Csak olyan összetevők, amelyek nem lehetnek paraméterei kötelező is támogatottak. A lab-felhasználó nem kell további paramétereket adja meg a labor létrehozása, és így egyszerű a virtuális gép létrehozásakor a folyamat során. 

## <a name="specify-mandatory-artifacts"></a>Kötelező összetevők megadása
Kötelező összetevők Windows és Linux rendszerű gépek külön-külön is választhat. Ezek az összetevők a sorrendet, amelyben szeretné őket a alkalmazni függően is sorrendjének módosításához. 

1. Válassza ki a labor kezdőlapja, **Konfigurace a zásady** alatt **beállítások**. 
3. Válassza ki **kötelező összetevők** alatt **külső erőforrások**. 
4. Válassza ki **szerkesztése** a a **Windows** szakasz vagy a **Linux** szakaszban. Ez a példa a **Windows** lehetőséget. 

    ![Kötelező összetevők lap – Szerkesztés gomb](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Válassza ki egy összetevőt. Ez a példa **7-Zip** lehetőséget. 
5. Az a **Hozzáadás összetevő** lapon jelölje be **Hozzáadás**. 

    ![Kötelező összetevők oldala – 7-zip hozzáadása](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Adjon hozzá egy másik összetevő, jelölje ki a cikket, és válassza ki **Hozzáadás**. Ebben a példában **Chrome** , a második kötelező összetevő.

    ![Kötelező összetevők lap – Chrome hozzáadása](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. Az a **kötelező összetevők** lapon megjelenik egy üzenet, amely a kijelölt összetevők számát adja meg. Kattintson az üzenetre, megjelenik a kiválasztott összetevőket. Válassza ki **mentése** mentéséhez. 

    ![Kötelező összetevők lap - összetevők mentése](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Ismételje meg a lépéseket adja meg a kötelező összetevők Linux rendszerű virtuális gépekhez. 
    
    ![Kötelező összetevők lap - összetevőket Windows és Linux](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. A **törlése** kiválasztása a listából egy összetevő **... három (pont)**  a sort, és válassza a végén **törlése**. 
10. A **átrendezése** összetevők rámutatáskor egér át az összetevő listájában válassza ki **... három (pont)**  , hogy megjelenik-e a sor elejére, és húzza az új helyre az elemet. 
11. A lab-ben kötelező összetevők mentéséhez válassza **mentése**. 

    ![Kötelező összetevők lap – Mentés összetevők lab-ben](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Zárja be a **Konfigurace a zásady** lap (kiválasztása **X** jobb felső sarokban), hogy a labor kezdőlapján.  

## <a name="delete-a-mandatory-artifact"></a>Egy kötelező összetevő törlése
Egy kötelező összetevő a labor törléséhez tegye a következőket: 

1. Válassza ki **Konfigurace a zásady** alatt **beállítások**. 
2. Válassza ki **kötelező összetevők** alatt **külső erőforrások**. 
3. Válassza ki **szerkesztése** a a **Windows** szakasz vagy a **Linux** szakaszban. Ez a példa a **Windows** lehetőséget. 
4. Válassza ki a kötelező összetevők felső számát az üzenetet. 

    ![Kötelező összetevők lap – válassza ki az üzenet](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. Az a **kiválasztott összetevők** lapon jelölje be **... három (pont)**  törlődik, és válassza ki az összetevőre **eltávolítása**. 
    
    ![Kötelező összetevők lap - összetevő eltávolítása](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. Válassza ki **OK** gombra kattintva zárja be a **kiválasztott összetevők** lapot. 
7. Válassza ki **mentése** a a **kötelező összetevők** lapot.
8. Ismételje meg a **Linux** lemezképek, ha szükséges. 
9. Válassza ki **mentése** módosításokat menti a labor létrehozása. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Nézet kötelező összetevők virtuális gép létrehozásakor
Most labor felhasználóként megtekintheti a kötelező összetevők listáját a laborkörnyezetben található virtuális gép létrehozása során. Nem szerkeszthetők, és a lab-ben a labor tulajdonosa által beállított kötelező összetevők törlése.

1. Válassza ki a labor kezdőlapja, **áttekintése** a menüből.
2. A labor virtuális gép hozzáadásához válassza **+ Hozzáadás**. 
3. Válassza ki a **alaplemezkép**. Ez a példa **a Windows Server 1709-es**.
4. Figyelje meg, hogy az üzenet jelenik meg, hogy **összetevők** a kiválasztott kötelező összetevők számát. 
5. Válassza ki **összetevők**. 
6. Győződjön meg arról, hogy a **kötelező összetevők** a labor konfigurace a zásady megadott. 

    ![A virtuális gép létrehozása – kötelező összetevők](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [egy Git-tárház összetevő hozzáadása egy laborhoz](devtest-lab-add-artifact-repo.md).

