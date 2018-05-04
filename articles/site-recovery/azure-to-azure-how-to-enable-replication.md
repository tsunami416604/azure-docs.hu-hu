---
title: Azure virtuális gépek replikálása az Azure Site Recovery konfigurálása |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan konfigurálni a replikálást az Azure virtuális gépeken, a másik a Site Recovery segítségével egy Azure-régiót.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/09/2018
ms.author: asgang
ms.openlocfilehash: 4479f7bbe657908d4c1ed94f5eaa00401efcb87e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Az Azure virtuális gépek replikálása Azure-régió, egy másik


>[!NOTE]
>
> Az Azure virtuális gépek helyreállítási helyreplikálásának jelenleg előzetes verzió.

Ez a cikk ismerteti, hogyan replikáláshoz Azure virtuális gépek, a másik egy Azure-régiót.

## <a name="prerequisites"></a>Előfeltételek

A jelen cikk feltételezi, hogy már beállította a Site Recovery ebben a forgatókönyvben leírtak szerint a [Azure az Azure-bA oktatóanyag](azure-to-azure-tutorial-enable-replication.md). Győződjön meg arról, hogy korábban előkészítve az előfeltételeket, és a Recovery Services-tároló létrehozása.



## <a name="enable-replication"></a>A replikáció engedélyezése

Engedélyezheti a replikálást. Ez az eljárás azt feltételezi, hogy az elsődleges Azure-régió, Kelet-Ázsia, és a másodlagos régióba Délkelet-Ázsia.

1. Kattintson a tárolóban lévő **+ replikálás**.
2. Vegye figyelembe a következő mezőket:
    - **Forrás**: származási virtuális gépeken, amelyek ebben az esetben a pont **Azure**.
    - **Adatforrásról**: ahonnan szeretné a virtuális gépek védelméhez az Azure-régió. Az ezen az ábrán a hely a "Kelet-Ázsia"
    - **Telepítési modell**: Azure telepítési modell forrásgépek közül.
    - **Erőforráscsoport**: az erőforráscsoport, amelybe a forrás virtuális gépek tartoznak. A kijelölt erőforráscsoportba tartozó összes virtuális gép védelmét a következő lépésben fel vannak sorolva.

    ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. A **virtuális gépek > Válassza ki a virtuális gépek**kattintson, és válassza a minden replikálni kívánt virtuális Gépnek. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Kattintson a **OK**.
    ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. A **beállítások**, cél hely beállításait konfigurálhatja:

    - **Célhelye**: A hely, ahol a forrás virtuális gép adatait a rendszer replikálja. Attól függően, a kijelölt gépekhez helyét, a Site Recovery biztosítja azokat a megfelelő cél régiók listáját. Azt javasoljuk, hogy őrizze meg a célhely ugyanazt a Recovery Services-tároló helyként.
    - **Célként megadott erőforráscsoportja**: az erőforráscsoport, ahol az összes a replikált virtuális gépek tartoznak. Alapértelmezés szerint Azure Site Recovery hoz létre egy új erőforráscsoportot a cél régióban "automatikus" utótaggal rendelkező nevét. Erőforráscsoport létrehozásánál Azure Site Recovery által már létezik, a rendszer újra. Választhatja azt is, ahogy az alábbi szakasz testre szabhatja. A célként megadott erőforráscsoportja helyét bármely Azure-régió, kivéve a régiót, amelyben a forrás virtuális gépek üzemeltetett lehet.
    - **Virtuális hálózati cél**: alapértelmezés szerint a Site Recovery hoz létre egy új virtuális hálózat a cél régióban "automatikus" utótaggal rendelkező neve. Ez a forrás hálózati leképezve, és minden jövőbeli védelmi használt. [További](site-recovery-network-mapping-azure-to-azure.md) kapcsolatos hálózatra való leképezés.
    - **Cél Storage-fiókok (Ha a forrás virtuális gép nem használ által kezelt lemezeken)**: alapértelmezés szerint a Site Recovery hoz létre egy új cél tárfiók mimicking a forrás virtuális gép tárolási konfigurációt. A tárfiók már létezik, a rendszer újra.
    - **A replika (Ha a forrás virtuális gép használja a felügyelt lemezek) által kezelt lemezeken**: a Site Recovery hoz létre új kezelt lemezek mappába történő tükrözésének felügyelt lemezeket a forrás virtuális gép (Standard vagy prémium) azonos tárolási módot, a forrás virtuális gép kezeli a lemez a cél régióban.
    - **Storage-fiókok gyorsítótár**: a Site Recovery gyorsítótárazása nevű adatforrás régióban extra storage-fiók szükséges. Történik a forrás virtuális gépeken végrehajtott módosításokat a nyomon követheti és a gyorsítótár tárfiók előtt replikálni azokat a célhelyre küldött.
    - **A rendelkezésre állási csoport**: alapértelmezés szerint az Azure Site Recovery létrehoz egy új rendelkezésre állási cél régióban "automatikus" utótaggal rendelkező név megadva. Azure Site Recovery már létrehozta a rendelkezésre állási csoport létezik, a rendszer újra.
    - **Replikációs szabályzat**: azt határozza meg a helyreállítási pont megőrzési előzményeit és az alkalmazás alkalmazáskonzisztens pillanatkép gyakorisága beállításait. Alapértelmezés szerint az Azure Site Recovery egy új replikációs házirendet létesít 24 órányi a helyreállítási pontok megőrzésének ideje és a "60 percig app alkalmazáskonzisztens pillanatkép gyakorisága alapértelmezett beállításai.

    ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>Tároló erőforrásait testreszabása

A Site Recovery által használt alapértelmezett tárolóbeállítások módosíthatja.

1. Kattintson a **testreszabás:** alapértelmezett beállításainak módosítása:
    - A **célként megadott erőforráscsoportja**, válassza ki az erőforráscsoportot a célként megadott helyen az előfizetés az erőforrás-csoportok a listából.
    - A **cél virtuális hálózati**, válassza ki a hálózatot a virtuális hálózat a célhelyen listájából.
    - A **rendelkezésre állási csoport**, adhat hozzá rendelkezésre állási csoport beállításait a virtuális gépre, ha azok rendelkezésre állási készlet forrás régióban részei.
    - A **cél tárfiókok**, válassza ki a használni kívánt fiókot.

        ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. Kattintson a **tároló-erőforrás létrehozása** > **Replikálásengedélyezési**.
3. Után a virtuális gépek vannak engedélyezve van a replikáció, nézze meg a virtuális gép állapotának állapotának **replikált elemek**

>[!NOTE]
>Kezdeti replikálás során a állapotát eltarthat egy ideig, frissítsen, anélkül, hogy folyamatban van. Kattintson a **frissítése** gombra, a legutóbbi állapot lekérdezése céljából.
>

# <a name="next-steps"></a>További lépések

[További](site-recovery-test-failover-to-azure.md) feladatátvételi teszt futtatása.
