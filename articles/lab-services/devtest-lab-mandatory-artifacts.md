---
title: Kötelező összetevők megadása az Azure DevTest Labs számára | Microsoft dokumentumok
description: Megtudhatja, hogyan adhatja meg a kötelező összetevőket, amelyeket telepíteni kell a felhasználó által kiválasztott összetevők telepítése előtt a tesztkörnyezetben lévő virtuális gépeken.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60562209"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Kötelező összetevők megadása a laborhoz az Azure DevTest Labsben
A tesztkörnyezet tulajdonosaként megadhatja a kötelező összetevőket, amelyek a laborban létrehozott összes gépre vonatkoznak. Képzeljen el egy olyan forgatókönyvet, amelyben azt szeretné, hogy a laborban lévő minden gép csatlakozzon a vállalati hálózathoz. Ebben az esetben minden tesztkörnyezet-felhasználónak hozzá kell adnia egy tartományillesztő-összetevőt a virtuális gép létrehozása során, hogy a gépe csatlakozzon a vállalati tartományhoz. Más szóval, a labor felhasználók lényegében újra létre kell hoznia egy gépet, ha elfelejtik alkalmazni a kötelező összetevők a gépen. A labor tulajdonosaként a tartományillesztési összetevőt kötelező összetevőként a laborban. Ez a lépés gondoskodik arról, hogy minden gép csatlakozik a vállalati hálózathoz, és időt és energiát takarít meg a tesztkörnyezet felhasználói számára.
 
Egyéb kötelező összetevők közé tartozik egy közös eszköz, amely a csapat által használt, vagy egy platformmal kapcsolatos biztonsági csomag, amely minden gép kell alapértelmezés szerint, stb. Röviden, minden olyan közös szoftver, amelya labor minden gépkell válik kötelező műtermék. Ha egy olyan gépről hoz létre egyéni lemezképet, amelykötelező összetevőket alkalmaz rá, majd létrehoz egy új gépet abból a lemezképből, a kötelező összetevők et a rendszer újra alkalmazza a gépen a létrehozás során. Ez a viselkedés azt is jelenti, hogy annak ellenére, hogy az egyéni lemezkép régi, minden alkalommal, amikor létrehoz egy gépet belőle a kötelező összetevők legfrissítettebb verzióját alkalmazza rá a létrehozási folyamat során. 
 
Csak a paramétereket nem tartalmazó összetevők támogatottak kötelezőként. A tesztkörnyezet-felhasználónak nem kell további paramétereket megadnia a tesztkörnyezet létrehozása során, és így a virtuális gép létrehozásának folyamata egyszerű. 

## <a name="specify-mandatory-artifacts"></a>Kötelező összetevők megadása
A Windows és Linux gépekhez külön-külön is kiválaszthat kötelező összetevőket. Ezeket az összetevőket át is rendezheti attól függően, hogy milyen sorrendben szeretné alkalmazni őket. 

1. A tesztkörnyezet kezdőlapján válassza a **Beállítások és házirendek** lehetőséget a **BEÁLLÍTÁSOK csoportban.** 
3. A **KÜLSŐ ERŐFORRÁSOK**csoportban válassza a **Kötelező összetevők** lehetőséget. 
4. Válassza a **Szerkesztés lehetőséget** a **Windows** vagy a **Linux** szakaszban. Ez a példa a **Windows** beállítást használja. 

    ![Kötelező összetevők lap - Szerkesztés gomb](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Jelöljön ki egy műtárgyat. Ez a példa **a 7-Zip** opciót használja. 
5. A **Műtermék hozzáadása** lapon válassza a **Hozzáadás**lehetőséget. 

    ![Kötelező összetevők lap - 7-zip hozzáadása](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Másik műtermék hozzáadásához jelölje ki a cikket, és válassza a **Hozzáadás**lehetőséget. Ez a példa a **Chrome-ot** adja hozzá a második kötelező összetevőként.

    ![Kötelező összetevők lap - Chrome hozzáadása](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. A **Kötelező összetevők** lapon megjelenik egy üzenet, amely megadja a kijelölt összetevők számát. Ha az üzenetre kattint, megjelennek a kijelölt összetevők. A mentéshez válassza a **Mentés** lehetőséget. 

    ![Kötelező összetevők lap – Műtermékek mentése](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Ismételje meg a lépéseket a Linux virtuális gépek kötelező összetevőinek megadásához. 
    
    ![Kötelező összetevők lap – Windows és Linux összetevők](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. Ha **törölni** szeretne egy műtárgyat a listából, válassza **a ... (Ellipszis)** a sor végén, és válassza a **Törlés gombot.** 
10. A listában szereplő összetevők **átrendezéséhez** vigye az egérmutatót a műtermék fölé, és válassza **a ... (Ellipszis)** amely a sor elején jelenik meg, és húzza az elemet az új pozícióba. 
11. Ha a kötelező összetevőket a tesztkörnyezetben szeretné menteni, válassza a **Mentés gombot.** 

    ![Kötelező összetevők lap – Műtermékek mentése a laborban](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Zárja be a **Konfiguráció és házirendek** lapot (válassza az **X** lehetőséget a jobb felső sarokban) a tesztkörnyezet kezdőlapjára való visszavezető gombra.  

## <a name="delete-a-mandatory-artifact"></a>Kötelező műtermék törlése
Kötelező műtermék törlése egy tesztkörnyezetből, tegye a következő műveleteket: 

1. Válassza a **Beállítások és házirendek** lehetőséget a **BEÁLLÍTÁSOK csoportban.** 
2. A **KÜLSŐ ERŐFORRÁSOK**csoportban válassza a **Kötelező összetevők** lehetőséget. 
3. Válassza a **Szerkesztés lehetőséget** a **Windows** vagy a **Linux** szakaszban. Ez a példa a **Windows** beállítást használja. 
4. Válassza ki az üzenetet a kötelező összetevők tetején a kötelező összetevők számával. 

    ![Kötelező összetevők lap – Az üzenet kijelölése](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. A **Kijelölt összetevők** lapon válassza **a ... (Ellipszis)** a műtermék törléséhez, és válassza **az Eltávolítás gombot.** 
    
    ![Kötelező összetevők lap – Műtermék eltávolítása](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. A Kijelölt összetevők lap bezárásához **kattintson** az OK **gombra.** 
7. Válassza a **Mentés** gombot a **Kötelező eltérések** lapon.
8. Ismételje meg a **linuxos** lemezképek lépéseit, ha szükséges. 
9. Válassza a **Mentés** lehetőséget az összes módosítás mentéséhez a tesztkörnyezetbe. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Kötelező összetevők megtekintése virtuális gép létrehozásakor
Most, mint egy tesztkörnyezet-felhasználó megtekintheti a kötelező összetevők listáját, miközben egy virtuális gép létrehozása a laborban. Nem szerkesztheti vagy törölheti a tesztkörnyezet ben beállított kötelező összetevőket a labor tulajdonosa által beállított kötelező összetevőket.

1. A labor kezdőlapján válassza a menü **Áttekintés parancsát.**
2. Ha virtuális gépet szeretne hozzáadni a tesztkörnyezethez, válassza a **+ Hozzáadás lehetőséget.** 
3. Válasszon egy **alapképet**. Ez a példa **a Windows Server 1709-es verzióját**használja.
4. Figyelje meg, hogy **megjelenik** egy üzenet az összetevők számára a kötelező összetevők kijelölt száma. 
5. Válassza **az Eltérések lehetőséget.** 
6. Győződjön meg arról, hogy a tesztkörnyezet konfigurációjában és szabályzataiban megadott **kötelező összetevők** jelennek meg. 

    ![Virtuális gép létrehozása – kötelező összetevők](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [adhat hozzá git-műtermék-tárházat egy tesztkörnyezethez.](devtest-lab-add-artifact-repo.md)

