---
title: Helyreállítási csomagok létrehozása/testreszabása az Azure Site Recovery szolgáltatásban
description: Ismerje meg, hogyan hozhat létre és szabhat testre helyreállítási terveket az Azure Site Recovery szolgáltatás használatával.
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: 6540317324a9f0d9bccc046ecf95824d4128bd09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705836"
---
# <a name="create-and-customize-recovery-plans"></a>Helyreállítási tervek létrehozása és testreszabása

Ez a cikk azt ismerteti, hogy miként hozhat létre és szabhat testre helyreállítási tervet az [Azure Site Recovery feladatátvételéhez.](site-recovery-overview.md) Mielőtt elkezdené, [további információ](recovery-plan-overview.md) a helyreállítási tervekről.

## <a name="create-a-recovery-plan"></a>Helyreállítási terv létrehozása

1. A Helyreállítási szolgáltatások tárolójában válassza a **Helyreállítási tervek (Site Recovery)** > **+Helyreállítási terv lehetőséget.**
2. A **Helyreállítási terv létrehozása**csoportban adja meg a terv nevét.
3. Válasszon ki egy forrást és egy célt a tervben lévő gépek alapján, és válassza az **Erőforrás-kezelő** lehetőséget a központi telepítési modellhez. A forráshelynek olyan gépekkel kell rendelkeznie, amelyek engedélyezve vannak a feladatátvételhez és a helyreállításhoz. 

    **Feladatátvétel** | **Forrás** | **Cél** 
   --- | --- | ---
   Azure – Azure | Válassza ki az Azure-régiót | Válassza ki az Azure-régiót
   A VMware az Azure-ba | A konfigurációs kiszolgáló kiválasztása | Válassza az Azure-t
   Fizikai gépek az Azure-ba | A konfigurációs kiszolgáló kiválasztása | Válassza az Azure-t   
   Hyper-V – Azure | A Hyper-V webhely nevének kijelölése | Válassza az Azure-t
   Hyper-V (VMM által kezelt) az Azure-ba  | A VMM-kiszolgáló kijelölése | Válassza az Azure-t
  
    Vegye figyelembe a következőket:
    -  Csak a helyreállítási terv használható feladatátvétela a forrás helyről az Azure-ba. Nem használhatja a helyreállítási terv feladat-visszavétel az Azure-ból.
    - A forráshelynek olyan gépekkel kell rendelkeznie, amelyek engedélyezve vannak a feladatátvételhez és a helyreállításhoz. 
    - A helyreállítási terv azonos forrású és célgépekkel rendelkező gépeket tartalmazhat. 
    - A VMware virtuális gépek és a VMM által kezelt Hyper-V VM-ek is szerepelhetnek ugyanabban a csomagban.
    - VMware virtuális gépek és a fizikai kiszolgálók lehet ugyanabban a tervben.

4. Az **Elemek kiválasztása virtuális gépek**csoportban jelölje ki a tervhez hozzáadni kívánt gépeket (vagy replikációs csoportot). Ezt követően kattintson az **OK** gombra.
    - A gépek hozzáadódnak az alapértelmezett csoporthoz (1. csoport) a tervbe. A feladatátvétel után a csoport összes gépe egyszerre indul.
    - Csak a megadott forrás- és célhelyeken lévő gépek közül választhat. 
5. A terv létrehozásához kattintson az **OK** gombra.

## <a name="add-a-group-to-a-plan"></a>Csoport hozzáadása tervhez

További csoportokat hozhat létre, és gépeket adhat hozzá különböző csoportokhoz, így csoportonként különböző viselkedést adhat meg. Megadhatja például, hogy egy csoport ban lévő gépek mikor induljon el a feladatátvétel után, vagy megadhatja a csoportonkénti testreszabott műveleteket.

1. A **helyreállítási tervekben**kattintson a jobb gombbal a Testreszabás > **tervre.** Alapértelmezés szerint a terv létrehozása után az összes hozzáadott gép az 1.
2. Kattintson **a +Csoport gombra.** Alapértelmezés szerint az új csoport számozása abban a sorrendben történik, ahogyan a program hozzáadja. Legfeljebb hét csoport lehet.
3. Jelölje ki az új csoportba áthelyezni kívánt gépet, kattintson a **Csoport módosítása**gombra, majd jelölje ki az új csoportot. Másik lehetőségként kattintson a jobb gombbal a csoport nevére > **Védett elemelemre,** és vegyen fel gépeket a csoportba. Egy gép vagy replikációs csoport csak egy csoporthoz tartozhat a helyreállítási tervben.


## <a name="add-a-script-or-manual-action"></a>Parancsfájl vagy manuális művelet hozzáadása

A helyreállítási tervet parancsfájl vagy manuális művelet hozzáadásával testreszabhatja. Vegye figyelembe:

- Ha replikálja az Azure-ba, integrálhatja az Azure-automatizálási runbookok a helyreállítási tervbe. [További információ](site-recovery-runbook-automation.md).
- Ha a System Center VMM által kezelt Hyper-V virtuális gépekreplikálásával replikálja a parancsfájlt a helyszíni VMM-kiszolgálón, és belefoglalhatja a helyreállítási tervbe.
- Parancsfájl hozzáadásakor új műveletkészletet ad hozzá a csoporthoz. Például az 1. *Group 1: pre-steps* Az összes előre lépés ebben a készletben található. Parancsfájlt csak akkor adhat hozzá az elsődleges helyen, ha telepítve van egy VMM-kiszolgáló.
- Ha kézi műveletet ad hozzá, amikor a helyreállítási terv fut, az azon a ponton áll meg, ahol beszúrta a kézi műveletet. Egy párbeszédpanelen megadhatja, hogy a kézi művelet befejeződött-e.
- Ha parancsfájlt szeretne létrehozni a VMM-kiszolgálón, kövesse a [cikkben](hyper-v-vmm-recovery-script.md)található utasításokat.
- Parancsfájlok lehet alkalmazni a feladatátvétel során a másodlagos hely, és a feladat-visszavétel során a másodlagos helyről az elsődleges. A támogatás a replikációs forgatókönyvtől függ:
    
    **Forgatókönyv** | **Feladatátvétel** | **Feladat-visszavétel**
    --- | --- | --- 
    Azure – Azure  | Forgatókönyv | Forgatókönyv
    VMware – Azure | Forgatókönyv | NA 
    Hyper-V a VMM-mel az Azure-ba | Forgatókönyv | Parancsfájl
    Replikálás Hyper-V-helyről az Azure-ba | Forgatókönyv | NA
    VMM-ről másodlagos VMM-re | Parancsfájl | Parancsfájl

1. A helyreállítási tervben kattintson arra a lépésre, amelyhez a műveletet hozzá kell adni, és adja meg, hogy mikor történjen meg a művelet:
    1. Ha azt szeretné, hogy a művelet a csoportban lévő gépek feladatátvétel után történő megkezdése előtt történjen, válassza **a Művelet előtti hozzáadása**lehetőséget.
    1. Ha azt szeretné, hogy a művelet a csoportban lévő gépek feladatátvétel után történő indítása után történjen, válassza a **Bejegyzés hozzáadása művelet lehetőséget.** A művelet pozíciójának áthelyezéséhez jelölje be a **Fel** vagy a **Le** gombot.
2. A **Beszúrás műveletben**válassza a **Parancsfájl** vagy a Kézi **művelet lehetőséget.**
3. Ha manuális műveletet szeretne hozzáadni, tegye a következőket:
    1. Írja be a művelet nevét, és írja be a műveletutasításokat. A feladatátvételt futtató személy látni fogja ezeket az utasításokat.
    1. Adja meg, hogy hozzá kívánja-e adni a manuális műveletet az összes feladatátvételi típushoz (teszt, feladatátvétel, tervezett feladatátvétel (ha szükséges)). Ezt követően kattintson az **OK** gombra.
4. Ha parancsfájlt szeretne hozzáadni, tegye a következőket:
    1. VMM-parancsfájl hozzáadása esetén válassza **a Feladatátvétel VMM-parancsfájlba**lehetőséget, és a **Parancsfájl elérési útja** mezőbe írja be a megosztás relatív elérési útját. Ha például a megosztás \\ \<a VMMServerName>\MSSCVMMLibrary\RPScripts mappában található, adja meg a következő elérési utat: \RPScripts\RPScript.PS1.
    1. Ha egy Azure-automatizálási futtatási könyvet ad hozzá, adja meg azt az **Azure Automation-fiókot,** amelyben a runbook található, és válassza ki a megfelelő **Azure Runbook-parancsfájlt.**
5. Futtasson egy teszt feladatátvételt a helyreállítási tervről annak érdekében, hogy a parancsfájl a várt módon működjön.

## <a name="watch-a-video"></a>Videó megtekintése

Nézzen meg egy videót, amely bemutatja, hogyan készíthet helyreállítási tervet.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>További lépések

További információ a [feladatátvételek futtatásáról.](site-recovery-failover.md)  

    
