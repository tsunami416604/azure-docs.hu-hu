---
title: Az Azure-beli virtuális gépek replikációja konfigurálása az Azure Site Recovery |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure virtuális gépek, egy Azure-régióból a másikba Site recoveryvel a replikáció konfigurálása.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 79ad4b3598c227ad2c2e3b76562cf95a30e82ad3
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54101547"
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Az Azure virtuális gépek replikálása másik Azure-régióba



Ez a cikk ismerteti, hogyan lehet engedélyezni a replikációt az Azure virtuális gépek Azure-régióból egy másikba.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már beállította a Site Recovery ebben a forgatókönyvben leírtak szerint a [Azure-bA oktatóanyag](azure-to-azure-tutorial-enable-replication.md). Győződjön meg arról, hogy már előkészítve az előfeltételeket, és a Recovery Services-tároló létrehozása.



## <a name="enable-replication"></a>A replikáció engedélyezése

Engedélyezze a replikációt. Ez az eljárás feltételezi, hogy az elsődleges Azure-régió Kelet-Ázsia, pedig a másodlagos régióba Délkelet-Ázsia.

1. Kattintson a tárolóban **+ replikálás**.
2. Vegye figyelembe a következő mezőket:
    - **forrás**: A pont, a forrás virtuális gépet, amely ebben az esetben **Azure**.
    - **Forrás helye**: Az Azure-régió, ahonnan csak szeretné a virtuális gép védelméhez. Ezen az ábrán a forráshely Kelet-Ázsia
    - **Üzemi modell**: Az Azure üzembe helyezési modell forrásgépek közül.
    - **Forrás-előfizetés**: Az előfizetés, a forrás virtuális gépeket, amelyekhez tartoznak. Ez bármelyik előfizetés lehet azon Azure Active Directory-bérlőn belül, ahol a már meglévő Recovery Services-tárolója van.
    - **Erőforráscsoport**: Az erőforráscsoport, a forrás virtuális gépeket, amelyekhez tartoznak. A kiválasztott erőforráscsoportba tartozó összes virtuális gép jelennek meg a következő lépésben védelemre.

    ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. A **virtuális gépek > Válassza ki a virtuális gépek**kattintson, és válassza a minden replikálni kívánt virtuális Géphez. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Kattintson a **OK**.
    ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. A **beállítások**, igény szerint konfigurálható cél webhely beállításai:

    - **Célhely**: A hely, ahol a forrás virtuális gép fognak replikálódni. Függően a kijelölt gépekhez helyét, a Site Recovery biztosít Önnek a megfelelő régiók listáját. Azt javasoljuk, hogy őrizze meg a célhelyen ugyanaz, mint a Recovery Services-tár helye.
    - **Érintett előfizetés**: A használt vész-helyreállítási célként megadott előfizetés. Alapértelmezés szerint a célelőfizetés megegyezik a forrás-előfizetéssel.
    - **Céloldali erőforráscsoport**: Az erőforráscsoport, ahol az összes a replikált virtuális gépek tartoznak. Alapértelmezés szerint az Azure Site Recovery hoz létre egy új erőforráscsoportot a célrégióban az "asr" utótaggal rendelkező neve. Erőforráscsoport létrehozása az Azure Site Recovery már létezik, a rendszer újra. Azt is beállíthatja az alábbi szakaszban látható módon szabhatja testre. A céloldali erőforráscsoport helye lehet bármelyik Azure-régióban, amelyben a forrás virtuális gépeket üzemeltetett régiót kivéve.
    - **Cél virtuális hálózat**: Alapértelmezés szerint a Site Recovery létrehoz egy új virtuális hálózat a célrégióban "asr" utótaggal rendelkező nevű. Ez a Forráshálózat leképezve, és minden jövőbeli védelemhez használt. [További](site-recovery-network-mapping-azure-to-azure.md) információk a hálózatleképezésről.
    - **Cél tárfiókok (Ha a forrás virtuális gép nem használ felügyelt lemezeket)**: Alapértelmezés szerint a Site Recovery létrehoz egy új céltárfiók, a forrás virtuális gép tárkonfigurációt mimicking. Storage-fiók már létezik, a rendszer újra.
    - **Replikált felügyelt lemezek (Ha a forrásoldali virtuális gép felügyelt lemezeket használ)**: A Site Recovery új replika felügyelt lemezeket hoz létre a célrégióban a forrás virtuális gép felügyelt lemezeinek azokkal az azonos tártípusban (Standard vagy prémium) tükrözik, mert a forrásoldali virtuális gép felügyelt lemez.
    - **Gyorsítótár tárfiókjai**: Site recoverynek extra storage-fiókkal, amelynek neve a gyorsítótárban a forrásrégióban. Történik a forrásoldali virtuális gép az összes módosítást a nyomon követett és a gyorsítótárfiókba azokat a célhelyre történő replikálása előtt küldött.
    - **Cél rendelkezésre állási csoportok**: Alapértelmezés szerint az Azure Site Recovery létrehoz egy új rendelkezésre állási csoportot a célrégióban a virtuális gépek része egy rendelkezésre állási csoportot a forrásrégióban az "asr" utótaggal rendelkező nevű. Rendelkezésre állási csoport létrehozása az Azure Site Recovery már létezik, a rendszer újra.
    - **Cél rendelkezésre állási zónák**: Alapértelmezés szerint a Site Recovery zóna száma azonos a célrégióban a forrás régiójára rendeli, ha a célrégióban támogatja a rendelkezésre állási zónák.

    A célrégió nem támogatja a rendelkezésre állási zónák használatát, ha a cél virtuális gépek alapértelmezés szerint egyetlen példány van konfigurálva. Ha szükséges, az ilyen virtuális gépek rendelkezésre állási csoportot a célrégióban kell "Testreszabás" gombra kattintva konfigurálhatja.

    >[!NOTE]
    >A rendelkezésre állási típusa – egyetlen példány, a rendelkezésre állási csoport vagy a rendelkezésre állási zónában, a replikáció engedélyezése után nem módosítható. Tiltsa le majd engedélyezze a rendelkezésre állási típusának módosításához kell.
    >
    
    - **Replikációs házirend**: Azt határozza meg a helyreállítási pont megőrzési előzményekkel és alkalmazáskonzisztens pillanatkép készítésének gyakorisága beállításait. Alapértelmezés szerint az Azure Site Recovery egy új replikációs házirendet hoz beállítás alapértelmezett 24 órányi a helyreállítási pont megőrzése és az "60 percben az alkalmazáskonzisztens pillanatkép gyakorisága.

    ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)
  
## <a name="customize-target-resources"></a>Célerőforrások testreszabása

A Site Recovery által használt alapértelmezett célbeállítások módosíthatja.

1. Kattintson a **testreszabása:** melletti "Célként megadott előfizetés" az alapértelmezett célként megadott előfizetés módosításához. Válassza ki az előfizetést az Azure Active Directory (AAD) ugyanabban a bérlőben elérhető előfizetések listájából.

2. Kattintson a **testreszabása:** alapértelmezett beállításainak módosítása:
    - A **céloldali erőforráscsoport**, válassza ki az erőforráscsoportot a listából a célként megadott helyen az előfizetés összes erőforráscsoportot.
    - A **cél virtuális hálózattal**, válassza ki a hálózatot a virtuális hálózat a célhelyen egy listából.
    - A **rendelkezésre állási csoport**, adhat hozzá rendelkezésre állási csoport beállításait a virtuális géphez, ha egy rendelkezésre állási csoportot a forrásrégióban részei.
    - A **cél tárfiókok**, válassza ki a használni kívánt fiókot.

        ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
1. Kattintson a **testreszabása:** replikációs beállításainak módosítására.
   - A **több virtuális gépre kiterjedő konzisztencia**, válassza ki együtt a replikálni kívánt virtuális gépek 
   - A feladatátvételkor a replikációs csoportba tartozó összes gép megosztott összeomlás-konzisztens és alkalmazáskonzisztens helyreállítási pontokkal rendelkezik majd. Több virtuális gépre kiterjedő konzisztencia engedélyezése hatással lehet a számítási feladatok teljesítményére (a CPU-igényes szó), és csak akkor, ha a gépek ugyanazt a számítási feladatot futtat, és több gép közötti konzisztenciára van szükség. Például ha egy alkalmazásnak 2 sql virtuális gépek és a 2-webkiszolgálók, hozzá kell adnia csak az sql virtuális gépek egy replikációs csoport részeként.
   - Kiválaszthatja, hogy legfeljebb 16 virtuális gépek egy replikációs csoportban.
   - Ha engedélyezte a több virtuális gépre kiterjedő konzisztenciát, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással. Ellenőrizze, hogy nem blokkolja-e tűzfal a virtuális gépek között a 20004-es porton keresztül zajló belső kommunikációt. Ha Linux rendszerű virtuális gépeket szeretne egy replikációs csoport részévé tenni, győződjön meg arról, hogy a 20004-es port esetében a kimenő adatforgalom manuális megnyitása megtörtént-e az adott Linux-verzió útmutatásainak megfelelően.
![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)
    
2. Kattintson a **célként megadott erőforrás létrehozása** > **replikáció engedélyezése**.
3. Után a replikáció engedélyezve vannak a virtuális gépek, virtuális gép állapota alapján állapotát ellenőrizheti **replikált elemek**

>[!NOTE]
>Kezdeti replikálás során a állapota eltarthat egy ideig, frissítése, anélkül, hogy folyamatban van. Kattintson a **frissítése** gombra, a legújabb állapotának beolvasása.
>

# <a name="next-steps"></a>További lépések

[További](site-recovery-test-failover-to-azure.md) feladatátvételi teszt futtatásáról.
