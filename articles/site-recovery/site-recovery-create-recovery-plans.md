---
title: Létrehozása és testreszabása az Azure Site Recovery használata vész-helyreállítási helyreállítási tervek |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre, és az Azure Site Recovery szolgáltatással vész-helyreállítási helyreállítási tervek testreszabása.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: d52aa3b39a17c42c0f0e0cb669c69d336b41ba48
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53973210"
---
# <a name="create-and-customize-recovery-plans"></a>Hozzon létre, és a helyreállítási tervek testreszabása

Ez a cikk azt ismerteti, hogyan hozhat létre, és a helyreállítási terv testreszabása [Azure Site Recovery](site-recovery-overview.md). Mielőtt elkezdené, [további](recovery-plan-overview.md) helyreállítási tervek ismertetése.

## <a name="create-a-recovery-plan"></a>Helyreállítási terv létrehozása

1. Válassza ki a Recovery Services-tárolóban **helyreállítási tervek (Site Recovery)** > **+ a helyreállítási terv**.
2. A **helyreállítási terv létrehozása**, adja meg a csomag nevét.
3. Válassza ki a forrás- és a számítógépek alapján. a csomagban, és válassza ki **Resource Manager** az üzembe helyezési modelljének. A gépek, amelyek engedélyezve vannak feladatátvételének és helyreállításának kell rendelkeznie. 

   **Feladatátvétel** | **Forrás** | **Target** 
   --- | --- | ---
   Azure – Azure | Azure-régió |Azure-régió
   VMware – Azure | Konfigurációs kiszolgáló | Azure
   Fizikai gépek Azure-bA | Konfigurációs kiszolgáló | Azure   
   Az Azure-ban a VMM által felügyelt Hyper-V  | VMM-ben megjelenített neve | Azure
   A Hyper-V VMM nélkül az Azure-bA | A Hyper-V hely neve | Azure
   A VMM a VMM-hez |A VMM rövid neve | VMM-ben megjelenített neve 

   > [!NOTE]
   > A helyreállítási terv azonos a forrás és cél gépek is tartalmazhat. A VMware és VMM által felügyelt Hyper-V virtuális gépeket nem lehet ugyanabban a csomagban. Az azonos csomaggal, ahol a forrás, a konfigurációs kiszolgáló VMware virtuális gépek és fizikai kiszolgálókat lehet.

2. A **válassza ki a virtuális gépek elemek**, válassza ki a a csomaghoz hozzáadni kívánt gépeket (vagy replikációs csoport). Ezután kattintson az **OK** gombra.
    - Alapértelmezett csoport (1. csoport) a csomag gépet hozzáadja. A csoportban lévő összes gépek a feladatátvételt követően egy időben indítsa el.
    - Bejelölheti a gépeket a megadott forrás- és helyeken vannak. 
1. Kattintson a **OK** a terv létrehozásához.

## <a name="add-a-group-to-a-plan"></a>Csoport hozzáadása egy csomaghoz

Hozzon létre további csoportokat, és gépeket ad hozzá különböző csoportokhoz, hogy megadhat eltérő viselkedést csoportja által történik. Megadhatja például, ha a csoportban lévő gépek kell elindítani a feladatátvételt követően, vagy adja meg a csoportok szerinti egyéni műveletek.

1. A **helyreállítási tervek**, kattintson a jobb gombbal a terv > **Testreszabás**. Alapértelmezés szerint a terv létrehozását követően a gépek felvett összes található az alapértelmezett 1 csoport.
2. Kattintson a **+ csoport**. Alapértelmezés szerint egy új csoportot a sorrendben, ahol megjelenik a számozott. Legfeljebb hét csoportokkal is rendelkezik.
3. Válassza ki az áthelyezni az új csoporthoz, kattintson a kívánt gépet **csoport módosítása**, majd válassza ki az új csoport. Másik lehetőségként kattintson a jobb gombbal a csoport neve > **védett elem**, és a gépek felvétele a csoportba. Egy gép vagy a replikációs csoport is csak egy csoportba tartoznak a helyreállítási terv.


## <a name="add-a-script-or-manual-action"></a>Egy parancsfájl vagy manuális művelet hozzáadása

Olyan parancsfájl vagy manuális művelet hozzáadásával testre szabhatja a helyreállítási terv. Vegye figyelembe:

- Az Azure-ba történő replikálás az Azure automation-runbookok integrálható a helyreállítási tervbe. [További információk](site-recovery-runbook-automation.md).
- Ha a System Center VMM által felügyelt Hyper-V virtuális gépeket replikál, parancsfájl létrehozása a helyszíni VMM-kiszolgálón, és adja hozzá a helyreállítási terv.
- Amikor hozzáad egy parancsfájlt, hozzáadja a csoporthoz tartozó műveletek új készletét. Például egy csoport 1. előtti lépések létrejön nevű *1. csoport: előzetes lépések*. Minden előzetes lépések ezen belül jelennek meg. Hozzáadhat egy szkriptet az elsődleges helyen, csak akkor, ha a VMM-kiszolgáló telepítve van.
- Ha manuális műveletet, amikor futtatja a helyreállítási terv, leállítja a pontnál, amelyen a manuális műveletet beszúrva. Egy párbeszédpanelen kéri, hogy adja meg, hogy a manuális művelet lett végrehajtva.
- Parancsfájl létrehozása a VMM-kiszolgálón, kövesse a [Ez a cikk](hyper-v-vmm-recovery-script.md).
- Parancsfájlok alkalmazhatók feladatátvételt a másodlagos helynek, és a feladat-visszavétel során a másodlagos helyről az elsődleges. Támogatja a replikációs forgatókönyvtől függ:
    
    **Forgatókönyv** | **Feladatátvétel** | **Feladat-visszavétel**
    --- | --- | --- 
    Azure – Azure  | Forgatókönyv | Forgatókönyv
    VMware – Azure | Forgatókönyv | NA 
    A Hyper-V a VMM-ben az Azure-bA | Forgatókönyv | Szkript
    Replikálás Hyper-V-helyről az Azure-ba | Forgatókönyv | NA
    VMM, másodlagos VMM-hez | Szkript | Szkript

1. A helyreállítási tervben szereplő kattintson a lépésre, amelyhez hozzá kell adni, valamint adja meg, mikor történjen, a művelet: egy. Ha azt szeretné, hogy a műveletet úgy, hogy a csoportban található gépek elindulnak, válassza ki a feladatátvételt követően még azelőtt **előzetes művelet hozzáadása**.
    b. Ha azt szeretné, hogy a feladatátvételt követően a gépek csoport elindítása után végrehajtandó művelet, válassza ki a **utólagos művelet felvétele**. A művelet pozícióját áthelyezéséhez jelölje ki a **feljebb** vagy **lejjebb** gombokat.
2. A **művelet beszúrása**válassza **parancsfájl** vagy **manuális művelet**.
3. Ha szeretne egy kézi művelet felvétele, tegye a következőket"egy. Írja be a művelet nevét, és írja be a művelet utasításai. A feladatátvétel futtatása a személy jelenik meg ezeket az utasításokat.
    b. Adja meg, hogy szeretné-e a manuális műveletet (teszt feladatátvétel esetén a tervezett feladatátvétel (Ha releváns)) feladatátvételi minden alkalmazástípus esetében hozzá. Ezután kattintson az **OK** gombra.
4. Ha szeretne hozzáadni egy parancsfájl, tegye a következőket: egy. Hozzáadásakor a VMM-szkript, válassza ki a **a VMM-szkript feladatátvételi**, és a **parancsfájl elérési útján** relatív elérési útját a megosztást. Például, ha a megosztáshoz a következő helyen található \\ <VMMServerName>\MSSCVMMLibrary\RPScripts, adja meg az elérési út: \RPScripts\RPScript.PS1.
    b. Ha egy könyv futtatása az Azure automation, adja meg a **Azure Automation-fiók** , amelyben a runbookban található, majd válassza ki a megfelelő **Azure-forgatókönyv Parancsprogramja**.
5. Annak érdekében, hogy a parancsfájl a várt módon működik-e a helyreállítási terv feladatátvételi teszt futtatása.

## <a name="watch-a-video"></a>Videó megtekintése

A videó azt mutatja be, hogyan hozhat létre egy helyreállítási tervbe.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>További lépések

Tudjon meg többet [futó feladatátvételi teszteket](site-recovery-failover.md).  

    
