---
title: Helyreállítási tervek létrehozása/testreszabása Azure Site Recovery
description: Megtudhatja, hogyan hozhat létre és szabhat testre helyreállítási terveket a vész-helyreállítási tervekhez a Azure Site Recovery szolgáltatás használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 9bb5a1a3aa0c2a4681ddecb5e20df41d481755ec
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084507"
---
# <a name="create-and-customize-recovery-plans"></a>Helyreállítási tervek létrehozása és testreszabása

Ez a cikk azt ismerteti, hogyan hozhat létre és szabhat testre egy helyreállítási tervet a [Azure site Recoveryban](site-recovery-overview.md). Mielőtt elkezdené, [további](recovery-plan-overview.md) információt a helyreállítási tervekről a következő témakörben olvashat:.

## <a name="create-a-recovery-plan"></a>Helyreállítási terv létrehozása

1. A Recovery Services-tárolóban válassza a **helyreállítási tervek (site Recovery)**  >  **+ helyreállítási terv**elemet.
2. A **helyreállítási terv létrehozása**lapon adja meg a csomag nevét.
3. Válasszon forrást és célt a csomag gépei alapján, majd válassza a **Resource Manager** lehetőséget a telepítési modellhez. A forrás helyének rendelkeznie kell a feladatátvételhez és a helyreállításhoz engedélyezett gépekkel. 

   **Feladatátvétel** | **Forrás** | **Cél** 
   --- | --- | ---
   Azure – Azure | Azure-régió |Azure-régió
   VMware – Azure | Konfigurációs kiszolgáló | Azure
   Fizikai gépek az Azure-ba | Konfigurációs kiszolgáló | Azure   
   A VMM által az Azure-ba felügyelt Hyper-V  | VMM megjelenítendő neve | Azure
   Hyper-V VMM nélkül az Azure-ba | Hyper-V hely neve | Azure
   VMM – VMM |VMM rövid neve | VMM megjelenítendő neve 

   > [!NOTE]
   > A helyreállítási terv ugyanazzal a forrással és céllal rendelkező gépeket tartalmazhat. A VMM által felügyelt VMware-és Hyper-V virtuális gépek nem lehetnek ugyanabban a csomagban. A VMware virtuális gépek és a fizikai kiszolgálók ugyanabban a csomagban lehetnek, ahol a forrás egy konfigurációs kiszolgáló.

2. Az **elemek kiválasztása virtuális gépek**területen válassza ki a tervhez hozzáadni kívánt gépeket (vagy replikációs csoportot). Ezután kattintson az **OK** gombra.
    - A rendszer hozzáadja a gépek alapértelmezett csoportját (1. csoport) a csomaghoz. A feladatátvételt követően az ebben a csoportban lévő összes gép egy időben indul el.
    - Csak a megadott forrás-és célhelyek közül választhat ki gépeket. 
1. A terv létrehozásához kattintson **az OK** gombra.

## <a name="add-a-group-to-a-plan"></a>Csoport hozzáadása egy csomaghoz

További csoportokat hoz létre, és különböző csoportokba helyezheti el a gépeket, így különböző viselkedést adhat meg egy csoport – csoport alapján. Megadhatja például, hogy a csoportba tartozó gépek mikor induljon el a feladatátvétel után, vagy csoportosan testreszabott műveleteket is megadhat.

1. A **helyreállítási tervek**menüben kattintson a jobb gombbal a csomagra, > a **Testreszabás**lehetőségre. Alapértelmezés szerint a csomag létrehozása után az összes hozzáadott gép az alapértelmezett 1. csoportban található.
2. Kattintson a **+ csoport**elemre. Alapértelmezés szerint az új csoportok számozása a hozzáadás sorrendjében történik. Legfeljebb hét csoportot tartalmazhat.
3. Válassza ki azt a gépet, amelyet át szeretne helyezni az új csoportba, kattintson a **csoport módosítása**elemre, majd válassza ki az új csoportot. Másik lehetőségként kattintson a jobb gombbal a csoport nevére > a **védett elemre**, és vegyen fel gépeket a csoportba. Egy számítógép vagy replikációs csoport csak egy helyreállítási tervben szereplő csoporthoz tartozhat.


## <a name="add-a-script-or-manual-action"></a>Parancsfájl vagy manuális művelet hozzáadása

A helyreállítási tervet parancsfájl vagy manuális művelet hozzáadásával is testreszabhatja. Vegye figyelembe:

- Ha az Azure-ba replikál, integrálhatja az Azure Automation-runbookok a helyreállítási tervbe. [További információ](site-recovery-runbook-automation.md).
- Ha a System Center VMM által felügyelt Hyper-V virtuális gépeket replikál, létrehozhat egy parancsfájlt a helyszíni VMM-kiszolgálón, és belefoglalhatja azt a helyreállítási tervbe.
- Parancsfájl hozzáadásakor a rendszer hozzáad egy új műveletet a csoporthoz. Például az 1. csoport előzetes lépéseinek egy készlete jön létre az 1. nevű *csoporttal: előzetes lépések*. A készleten belül minden előzetes lépés megjelenik. Az elsődleges helyen csak akkor adhat hozzá parancsfájlt, ha a VMM-kiszolgáló telepítve van.
- Ha manuális műveletet ad hozzá, a helyreállítási terv futtatásakor a manuális művelet beszúrt pontján leáll. Megjelenik egy párbeszédpanel, amelyen megadhatja, hogy a manuális művelet befejeződött-e.
- Ha parancsfájlt szeretne létrehozni a VMM-kiszolgálón, kövesse a [cikk](hyper-v-vmm-recovery-script.md)utasításait.
- A parancsfájlok a másodlagos helyre történő feladatátvétel során, a másodlagos helyről az elsődleges helyre történő feladat-visszavétel során is alkalmazhatók. A támogatás a replikációs forgatókönyvtől függ:
    
    **Forgatókönyv** | **Feladatátvétel** | **Feladat-visszavétel**
    --- | --- | --- 
    Azure – Azure  | Forgatókönyv | Forgatókönyv
    VMware – Azure | Forgatókönyv | NA 
    Hyper-V a VMM-ből az Azure-ba | Forgatókönyv | Szkript
    Replikálás Hyper-V-helyről az Azure-ba | Forgatókönyv | NA
    VMM másodlagos VMM | Szkript | Szkript

1. A helyreállítási tervben kattintson arra a lépésre, amelyre a műveletet hozzá szeretné adni, majd adja meg, hogy mikor történjen a művelet:
    1. Ha azt szeretné, hogy a művelet a csoportba tartozó gépek feladatátvétel után induljon el, válassza az **előzetes művelet hozzáadása**lehetőséget.
    1. Ha azt szeretné, hogy a művelet a csoport gépei után induljon el a feladatátvétel után, válassza a **post művelet hozzáadása**lehetőséget. A művelet pozíciójának áthelyezéséhez válassza a **feljebb vagy** **lejjebb** gombokat.
2. A **beszúrási művelet**területen válassza a **parancsfájl** vagy **manuális művelet**lehetőséget.
3. Ha manuális műveletet szeretne hozzáadni, tegye a következőket:
    1. Írja be a művelet nevét, és írja be a művelet utasításait. A feladatátvételt futtató személy ezeket az utasításokat fogja látni.
    1. Adja meg, hogy szeretné-e hozzáadni a manuális műveletet a feladatátvétel összes típusához (teszt, feladatátvétel, tervezett feladatátvétel (ha szükséges)). Ezután kattintson az **OK** gombra.
4. Ha parancsfájlt szeretne felvenni, tegye a következőket:
    1. Ha VMM-parancsfájlt ad hozzá, válassza a **FELADATÁTVÉTEL VMM szkripthez**lehetőséget, és a **parancsfájl elérési útja** mezőbe írja be a megosztás relatív elérési útját. Ha például a megosztás helye \\\<VMMServerName > \MSSCVMMLibrary\RPScripts, adja meg a következő elérési utat: \RPScripts\RPScript.PS1.
    1. Ha Azure Automation-futtatási könyvet ad hozzá, adja meg azt a **Azure Automation fiókot** , amelyben a runbook található, és válassza ki a megfelelő **Azure runbook-szkriptet**.
5. Futtasson feladatátvételi tesztet a helyreállítási tervből annak ellenőrzéséhez, hogy a parancsfájl a várt módon működik-e.

## <a name="watch-a-video"></a>Videó megtekintése

Tekintsen meg egy videót, amely bemutatja, hogyan hozhat létre helyreállítási tervet.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Következő lépések

További információ a [feladatátvételek futtatásáról](site-recovery-failover.md).  

    
