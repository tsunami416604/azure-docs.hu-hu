---
title: Az Azure-beli virtuális gépek replikációja konfigurálása az Azure Site Recovery |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure virtuális gépek, egy Azure-régióból a másikba Site recoveryvel a replikáció konfigurálása.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: asgang
ms.openlocfilehash: 86bd41d518006b0601a5c9d18e5429f76d5a4fc5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64926623"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Azure virtuális gépek replikálása másik Azure-régióba


Ez a cikk ismerteti, hogyan lehet engedélyezni a replikációt az Azure virtuális gépek Azure-régióból egy másikba.

## <a name="before-you-start"></a>Előkészületek

Ez a cikk azt feltételezi, hogy a Site Recovery üzembe helyezése, előkészítése után leírtak szerint a [Azure-bA vész-helyreállítási oktatóanyag](azure-to-azure-tutorial-enable-replication.md).

Előfeltételek helyén kell lennie, és érdemes létrehozott egy Recovery Services-tárolót.


## <a name="enable-replication"></a>A replikáció engedélyezése

Engedélyezze a replikációt. Ez az eljárás feltételezi, hogy az elsődleges Azure-régió Kelet-Ázsia, pedig a másodlagos régióba Délkelet-Ázsia.

1. Kattintson a tárolóban **+ replikálás**.
2. Vegye figyelembe a következő mezőket:
   - **forrás**: A pont, a forrás virtuális gépet, amely ebben az esetben **Azure**.
   - **Forrás helye**: Az Azure-régió, ahonnan csak szeretné a virtuális gépek védelme érdekében. Ezen az ábrán a forráshely Kelet-Ázsia
   - **Üzemi modell**: Az Azure üzembe helyezési modell forrásgépek közül.
   - **Forrás-előfizetés**: Az előfizetés, amely a forrás virtuális gépek tartoznak. Ez bármelyik előfizetés lehet azon Azure Active Directory-bérlőn belül, ahol a már meglévő Recovery Services-tárolója van.
   - **Erőforráscsoport**: Az erőforráscsoport, a forrás virtuális gépeket, amelyekhez tartoznak. A kiválasztott erőforráscsoportba tartozó összes virtuális gép jelennek meg a következő lépésben védelemre.

     ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. A **virtuális gépek > Válassza ki a virtuális gépek**kattintson, és válassza a minden replikálni kívánt virtuális Géphez. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Ezután kattintson az **OK** gombra.
    ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. A **beállítások**, igény szerint konfigurálható cél webhely beállításai:

   - **Célhely**: A hely, ahol a forrás virtuális gép fognak replikálódni. Függően a kijelölt gépekhez helyét, a Site Recovery biztosít Önnek a megfelelő régiók listáját. Azt javasoljuk, hogy őrizze meg a célhelyen ugyanaz, mint a Recovery Services-tár helye.
   - **Érintett előfizetés**: A használt vész-helyreállítási célként megadott előfizetés. Alapértelmezés szerint a célelőfizetés megegyezik a forrás-előfizetéssel.
   - **Céloldali erőforráscsoport**: Az erőforráscsoport, ahol az összes a replikált virtuális gépek tartoznak.
       - Alapértelmezés szerint a Site Recovery létrehoz egy új erőforráscsoportot a célrégióban a név egy "asr" utótaggal rendelkező.
       - Ha létezik a Site Recovery által már létrehozott erőforráscsoportot, a rendszer újra.
       - Testre szabhatja az erőforrás-csoport beállításokat.
       - A céloldali erőforráscsoport helye lehet bármelyik Azure-régióban, kivéve a régiót, amelyben a forrás virtuális gépek futnak.
   - **Cél virtuális hálózattal**: Alapértelmezés szerint a Site Recovery a célrégióban a név egy "asr" utótaggal rendelkező új virtuális hálózatot hoz létre. Ez a Forráshálózat leképezve, és minden jövőbeli védelemhez használt. [További](site-recovery-network-mapping-azure-to-azure.md) információk a hálózatleképezésről.
   - **Cél tárfiókok (a forrás virtuális gép nem használ felügyelt lemezeket)** : Alapértelmezés szerint a Site Recovery létrehoz egy új céltárfiók, a forrás virtuális gép tárkonfigurációt mimicking. Storage-fiók már létezik, a rendszer újra.
   - **Replikált felügyelt lemezek (a forrás virtuális gép felügyelt lemezeket használ)** : Site Recovery új replika felügyelt lemezeket hoz létre a célrégióban a forrás virtuális gép felügyelt lemezeinek azokkal az azonos tártípusban (Standard vagy prémium) tükrözik, mert a forrásoldali virtuális gép felügyelt lemez.
   - **Gyorsítótár tárfiókjai**: Site recoverynek extra storage-fiókkal, amelynek neve a gyorsítótárban a forrásrégióban. Történik a forrásoldali virtuális gép az összes módosítást a nyomon követett és a gyorsítótárfiókba azokat a célhelyre történő replikálása előtt küldött.
   - **Cél rendelkezésre állási csoportok**: Alapértelmezés szerint a Site Recovery létrehoz egy új rendelkezésre állási csoportot a célrégióban az "asr" utótaggal a nevében, a virtuális gépek rendelkezésre állási csoport a forrásrégióban részét képező. Ha a Site Recovery által már létrehozott rendelkezésre állási csoport már létezik, a rendszer újra.
   - **Cél rendelkezésre állási zónák**: Alapértelmezés szerint a Site Recovery zóna száma azonos a célrégióban a forrás régiójára rendeli, ha a célrégióban támogatja a rendelkezésre állási zónák.

     A célrégió nem támogatja a rendelkezésre állási zónák használatát, ha a cél virtuális gépek alapértelmezés szerint egyetlen példány van konfigurálva. Ha szükséges, az ilyen virtuális gépek rendelkezésre állási csoportot a célrégióban kell "Testreszabás" gombra kattintva konfigurálhatja.

     >[!NOTE]
     >A rendelkezésre állási típusa – egyetlen példány, a rendelkezésre állási csoport vagy a rendelkezésre állási zónában, a replikáció engedélyezése után nem módosítható. Tiltsa le majd engedélyezze a rendelkezésre állási típusának módosításához kell.
     >
    
   - **Replikációs házirend**: Azt határozza meg a helyreállítási pont megőrzési előzményekkel és alkalmazáskonzisztens pillanatkép készítésének gyakorisága beállításait. Alapértelmezés szerint az Azure Site Recovery egy új replikációs házirendet hoz beállítás alapértelmezett 24 órányi a helyreállítási pont megőrzése és az "60 percben az alkalmazáskonzisztens pillanatkép gyakorisága.

     ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>A hozzáadott lemezek a replikáció engedélyezése

Ha lemezeket ad hozzá egy Azure virtuális Gépen, amely a replikáció engedélyezve van, az alábbiak történnek:
-   A virtuális gép replikációs állapotát jeleníti meg a figyelmeztetést, és megjegyzés tájékoztatja, hogy egy közben, vagy további lemezek érhetők el védelemre.
-   Ha engedélyezte a védelmet a hozzáadott lemezek, a figyelmeztetés fiókérvényesítés után megjelenik a kezdeti replikáció a lemez.
-   Ha nem szeretné a lemez replikációja választja, kiválaszthatja a figyelmeztetés bezárása.

    
    ![Új lemez hozzáadása](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Egy hozzáadott lemezt replikálásának engedélyezéséhez, tegye a következőket:

1.  A tárolóban > **replikált elemek**, kattintson a virtuális Gépre, amelyhez hozzáadta a lemezt.
2.  Kattintson a **lemezek**, majd válassza ki az adatlemezt, amelynek szeretné engedélyezni a replikációt (ezek a lemezek rendelkezik egy **nem védett** állapot).
3.  A **Háttértárakról**, kattintson a **engedélyezze a replikációt**.

    ![Hozzáadott lemezt a replikáció engedélyezése](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

Miután az engedélyezés replikációs feladat fut, és a kezdeti replikáció befejeződik a replikációs állapot figyelmeztetés a lemezhiba törlődik.


  
## <a name="customize-target-resources"></a>Célerőforrások testreszabása

A Site Recovery által használt alapértelmezett célbeállítások módosíthatja.

1. Kattintson a **testreszabása:** melletti "Célként megadott előfizetés" az alapértelmezett célként megadott előfizetés módosításához. Válassza ki az előfizetést az Azure Active Directory (AAD) ugyanabban a bérlőben elérhető előfizetések listájából.

2. Kattintson a **testreszabása:** alapértelmezett beállításainak módosítása:
    - A **céloldali erőforráscsoport**, válassza ki az erőforráscsoportot a listából a célként megadott helyen az előfizetés összes erőforráscsoportot.
    - A **cél virtuális hálózattal**, válassza ki a hálózatot a virtuális hálózat a célhelyen egy listából.
    - A **rendelkezésre állási csoport**, adhat hozzá rendelkezésre állási csoport beállításait a virtuális géphez, ha egy rendelkezésre állási csoportot a forrásrégióban részei.
    - A **cél tárfiókok**, válassza ki a használni kívánt fiókot.

        ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Kattintson a **testreszabása:** replikációs beállításainak módosítására.
4. A **több virtuális gépre kiterjedő konzisztencia**, válassza ki együtt a replikálni kívánt virtuális gépek.
    - A feladatátvételkor a replikációs csoportba tartozó összes gép megosztott összeomlás-konzisztens és alkalmazáskonzisztens helyreállítási pontokkal rendelkezik majd.
    - Több virtuális gépre kiterjedő konzisztencia engedélyezése hatással lehet számítási feladat teljesítményére (a CPU-igényes szó). Azt kell engedélyezve, ha a gépek ugyanazt a számítási feladatot futtat, és több gép közötti konzisztenciára van szükség.
    - Például ha egy alkalmazás 2 SQL Servert futtató virtuális gépek és a két olyan webkiszolgáló, akkor hozzá kell adnia csak az SQL Server virtuális gépek replikációs csoporthoz.
    - Ha szeretné, rendelkezik egy legfeljebb 16 virtuális gépek egy replikációs csoportban.
    - Ha engedélyezte a több virtuális gépre kiterjedő konzisztenciát, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással.
    - Ellenőrizze, hogy nem blokkolja-e a virtuális gépek a 20004-es porton között a belső kommunikáció tűzfalkészülék van.
    - Ha azt szeretné, hogy a Linux rendszerű virtuális gépek egy replikációs csoport részét kell, győződjön meg arról, az adott Linux-verzió útmutatás szerint manuális megnyitása a 20004-es porton a kimenő forgalmat.
![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)
    
5. Kattintson a **célként megadott erőforrás létrehozása** > **replikáció engedélyezése**.
6. Után a replikáció engedélyezve vannak a virtuális gépek, virtuális gép állapota alapján állapotát ellenőrizheti **replikált elemek**

>[!NOTE]
>Kezdeti replikálás során a állapota eltarthat egy ideig, frissítése, anélkül, hogy folyamatban van. Kattintson a **frissítése** gombra, a legújabb állapotának beolvasása.
>

# <a name="next-steps"></a>További lépések

[További](site-recovery-test-failover-to-azure.md) feladatátvételi teszt futtatásáról.
