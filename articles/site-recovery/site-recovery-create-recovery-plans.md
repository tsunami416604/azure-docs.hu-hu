---
title: Hozzon létre, és szabja testre a helyreállítási terv feladatátvételi és az Azure Site Recovery helyreállítási |} Microsoft Docs
description: Ismerje meg, hogyan hozhat létre és testreszabása az Azure Site Recovery helyreállítási tervek. Ez a cikk ismerteti a feladatátvétel és a virtuális gépek és fizikai kiszolgálók helyreállításához.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/21/2018
ms.author: raynew
ms.openlocfilehash: e02672ea76eada2d660b20f91c4417019d4efc97
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30189834"
---
# <a name="create-and-customize-recovery-plans"></a>Hozzon létre, és a helyreállítási terv testreszabása

Ez a cikk ismerteti, hogyan hozhat létre, és testre szabhatja a helyreállítási terv [Azure Site Recovery](site-recovery-overview.md). Mielőtt elkezdené, [további](recovery-plan-overview.md) helyreállítási tervek.

## <a name="create-a-recovery-plan"></a>Helyreállítási terv létrehozása

1. Válassza ki a Recovery Services-tároló **helyreállítási tervek (helyreállítás)** > **+ a helyreállítási terv**.
2. A **helyreállítási terv létrehozása**, adja meg a csomag nevét.
3. Válassza a forrás és cél a tervben a számítógépek alapján, majd **erőforrás-kezelő** telepítési modell. A forráshely kell rendelkeznie a gépek, amelyek engedélyezve vannak feladatátvételét és helyreállítását. 

   **Feladatátvétel** | **Source** | **Target** 
   --- | --- | ---
   Azure – Azure | Azure region |Azure region
   VMware az Azure-bA | Configuration server | Azure
   Fizikai gépek az Azure-bA | Configuration server | Azure   
   VMM Azure által felügyelt Hyper-V  | A VMM megjelenített neve | Azure
   A Hyper-V nélkül VMM, Azure-bA | Hyper-V hely neve | Azure
   A VMM VMM |A VMM rövid neve | A VMM megjelenített neve 

   > [!NOTE]
   > A helyreállítási terv azonos a forrás és cél gépeket tartalmazhat. VMware és a Hyper-V virtuális gépek a VMM kezeli-e a csomagot nem lehet. VMware virtuális gépek és fizikai kiszolgálók a csomagot, ahol a forrás, a konfigurációs kiszolgáló lehet.

2. A **válassza ki a virtuális gépek elemek**, válassza ki a a terv hozzáadni kívánt gépek (vagy replikációs csoport). Ezután kattintson az **OK** gombra.
    - Alapértelmezett csoport (1. csoport) a tervben szereplő gépeket adnak. Feladatátvétel után a csoport összes gép egyszerre elindításához.
    - Jelölhet ki gépeket a megadott forrás- és célparaméterei helyeken vannak. 
1. Kattintson a **OK** a terv létrehozásához.

## <a name="add-a-group-to-a-plan"></a>Csoport hozzáadása egy csomaghoz

További csoportok létrehozása, és vegye fel a gépek különböző csoportokat, hogy a csoport által CSO alapon is megadhat a másik viselkedését. Megadhatja például, amikor a gépek csoportban kell elindítani a feladatátvételt követően, vagy adja meg a testreszabott műveletek csoportonként.

1. A **helyreállítási tervek**, kattintson a jobb gombbal a terv > **Testreszabás**. Alapértelmezés szerint terv létrehozása után a gépek felvette az összes található az alapértelmezett 1 csoportot.
2. Kattintson a **+ csoport**. Alapértelmezés szerint egy új csoportot számozása a sorrendet, amelyben kerül. Legfeljebb hét csoportokat is.
3. Válassza ki az áthelyezni az új csoporthoz, kattintson a kívánt gépet **csoport módosítása**, majd jelölje ki az új csoportot. Másik lehetőségként kattintson a jobb gombbal a csoport neve > **védett elemhez**, és a gépek felvétele a csoportba. Egy számítógép vagy a replikációs csoport is csak egy csoportjához tartozó helyreállítási tervet.


## <a name="add-a-script-or-manual-action"></a>Egy parancsfájl vagy manuális művelet hozzáadása

Testre szabhatja a helyreállítási terv hozzáadása egy parancsfájl vagy a manuális műveletet. Vegye figyelembe:

- Ha az Azure-bA replikál az Azure automation-runbook integrálhatja a helyreállítási tervbe. [További információk](site-recovery-runbook-automation.md).
- Ha a System Center VMM által felügyelt Hyper-V virtuális gépeket replikál, hozzon létre egy parancsfájlt a helyszíni VMM-kiszolgálón, és adja hozzá a helyreállítási tervben.
- Amikor egy új, hozzáadja a csoport műveletek új készletét. Például egy csoport 1 előtti lépések hozta neve *csoport 1: előtti lépések*. Ebben a készletben található összes előtti lépések jelennek meg. Hozzáadhat egy parancsfájlt az elsődleges helyen, csak akkor, ha a VMM-kiszolgáló telepítve van.
- Ha a manuális műveletet, a helyreállítási terv futtatásakor, a pont, ahol a manuális műveletet beszúrni megáll. Egy párbeszédpanelen kéri, hogy adja meg, hogy befejeződött-e a manuális műveletet.
- Parancsfájl létrehozása a VMM-kiszolgálón, kövesse az utasításokat a [Ez a cikk](hyper-v-vmm-recovery-script.md).
- Parancsfájlok alkalmazhatja a másodlagos helyre a feladatátvétel során, és a feladat-visszavétel során a másodlagos hely az elsődleges. Támogatás a replikációs forgatókönyv függ:
    
    **Scenario** | **Feladatátvétel** | **Feladat-visszavétel**
    --- | --- | --- 
    Azure – Azure  | Forgatókönyv | Forgatókönyv
    VMware – Azure | Forgatókönyv | NA 
    A Hyper-V és a VMM az Azure-bA | Forgatókönyv | Szkript
    Replikálás Hyper-V-helyről az Azure-ba | Forgatókönyv | NA
    VMM másodlagos VMM | Szkript | Szkript

1. Kattintson a helyreállítási terv, amelyhez a művelet hozzá kell adni, és adja meg, mikor kell végrehajtani a műveletet. a lépés: egy. Ha azt szeretné, hogy milyen műveletet kell történnie ahhoz a feladatátvétel után válassza ki a gépeket, a csoport indított **előtti művelet hozzáadása**.
    b. Ha a feladatátvételt követően a gépek csoport elindítása után végrehajtandó művelet, jelölje be **post művelet hozzáadása**. A művelet pozíciója áthelyezéséhez jelölje ki a **fel** vagy **le** gombok.
2. A **műveletet**, jelölje be **parancsfájl** vagy **manuális műveletet**.
3. Ha hozzá szeretne adni egy kézi művelet, tegye a következőket"egy. Adjon meg egy nevet, a művelet, és írja be a művelet utasításokat. A feladatátvételi futtató személy jelenik meg ezeket az utasításokat.
    b. Adja meg, hogy szeretné-e hozzáadni a manuális műveletet az összes feladatátvételi (Test, feladatátvétel esetén a tervezett feladatátvétel (ha szükséges)). Ezután kattintson az **OK** gombra.
4. Ha hozzá szeretne adni egy parancsfájlt, tegye a következőket: a. A VMM-parancsfájl hozzáadása, válassza ki **VMM parancsfájl feladatátvétel**, és a a **parancsprogram elérési útját** relatív elérési útját a megosztást. Például, ha a megosztás helye \\ <VMMServerName>\MSSCVMMLibrary\RPScripts, adja meg az elérési út: \RPScripts\RPScript.PS1.
    b. Ha egy Azure Automation szolgáltatásbeli könyv futtassa, adja meg a **Azure Automation-fiók** , amelyben a runbookban található, és válassza ki a megfelelő **Azure Runbook parancsfálj**.
5. Győződjön meg arról, hogy a parancsfájl megfelelően működik-e a helyreállítási terv feladatátvételi teszt futtatása.

## <a name="watch-a-video"></a>Bemutató videó

Bemutató videó bemutatja, hogyan hozhat létre egy helyreállítási terv.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>További lépések

További információ [feladatátvételek futtató](site-recovery-failover.md).  

    
