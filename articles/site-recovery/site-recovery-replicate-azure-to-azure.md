---
title: "Alkalmazások (Azure-az Azure-bA) replikálni |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan állíthat be egy Azure-régió, egy másik régióban az Azure-ban működő virtuális gépek replikációját."
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: asgang
ms.openlocfilehash: dc7dff33aa2c3e844c6a91024fcfc98148416f7e
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Az Azure virtuális gépek replikálása Azure-régió, egy másik



>[!NOTE]
>
> Az Azure virtuális gépek helyreállítási helyreplikálásának jelenleg előzetes verzió.

A cikkből megtudhatja, hogyan állíthat be egy másik Azure-régió, egy Azure-régiót futó virtuális gépek replikációját.

## <a name="prerequisites"></a>Előfeltételek

* A cikk feltételezi, hogy már ismeri a Site Recovery és a Recovery Services-tároló. Egy "Recovery services-tároló" előre létrehozott, rendelkeznie kell.

    >[!NOTE]
    >
    > Javasoljuk, hogy hozzon létre a "Recovery services-tároló" a helyen, ahol azt szeretné, hogy a virtuális gépek replikálásához. Ha a célhely "USA középső RÉGIÓJA", hozzon létre például az "USA középső RÉGIÓJA" tárolóban.

* Ha a hálózati biztonsági csoportok (NSG) szabályok vagy tűzfal proxy segítségével végzi a kimenő internetkapcsolattal az Azure virtuális gépeken való hozzáférést, győződjön meg arról, hogy Ön engedélyezett a szükséges URL-címek vagy IP-címek. Tekintse meg [hálózati útmutató](./site-recovery-azure-to-azure-networking-guidance.md) további részleteket.

* Ha egy expressroute-on vagy VPN-kapcsolat a helyszíni és az Azure-ban a forráshely között, hajtsa végre a [hely kapcsolatos szempontok az Azure-bA helyszíni expressroute / VPN-konfiguráció](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration) dokumentum.

* Egyes rendelkeznie kell Azure felhasználói fiókja [engedélyek](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) egy Azure virtuális gép replikációjának engedélyezéséhez.

* Az Azure-előfizetéshez engedélyezni kell a célként megadott helyen vész-Helyreállítási régió használni kívánt virtuális gépek létrehozásához. Ahhoz, hogy a szükséges kvóta is ügyfélszolgálatához.

## <a name="enable-replication-from-azure-site-recovery-vault"></a>Engedélyezze a replikálást az Azure Site Recovery-tároló
Az ezen az ábrán Kelet-Ázsiában futó virtuális gépeket replikálja a Microsoft Azure-beli hely a "Délkelet-Ázsia" helyre. A lépések a következők:

 Kattintson a **+ replikálás** engedélyezni szeretné a virtuális gépek replikálását a tárolóban lévő állapottal.

1. **Forrás:** származási gépek, amelyek ebben az esetben a pont hivatkozik **Azure**.

2. **A forrás helye:** az Azure-régió, ahol el szeretné-e a virtuális gépek védelmére. Az ezen az ábrán a hely a "Kelet-Ázsia"

3. **Telepítési modell:** forrásgépek Azure-telepítés modelljét hivatkozik. Kiválaszthatja, hogy vagy a klasszikus vagy erőforrás-kezelő és a modell tartozó gépek listája a következő lépésben védelemre.

      >[!NOTE]
      >
      > Csak a klasszikus virtuális gépek replikálása, és végezze el a helyreállítást a klasszikus virtuális gépként. Az erőforrás-kezelő virtuális gépként nem lehet helyreállítani.

4. **Erőforráscsoport:** az erőforráscsoport, amelybe a forrás virtuális gépek tartoznak. A kijelölt erőforráscsoportba tartozó összes virtuális gép megjelenik a védelem a következő lépésben.

    ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

A **virtuális gépek > Válassza ki a virtuális gépek**kattintson, és válassza a minden replikálni kívánt gépek. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Ezután kattintson az OK gombra.
    ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)


Beállítások szakaszban konfigurálhatja a cél hely tulajdonságai

1. **Cél helye:** Ez az a hely, ahol a forrás virtuális gép adatait a rendszer replikálja. Attól függően, a kijelölt gépekhez helyét, a Site Recovery biztosítja azokat a megfelelő cél régiók listáját.

    > [!TIP]
    > Javasoljuk, hogy tartsa célhelye megegyezik a helyreállítási frissítésétől services-tároló.

2. **Cél-erőforráscsoport:** az erőforráscsoport, ahol az összes a replikált virtuális gépek tartozni fog. Alapértelmezés szerint Azure Site Recovery létrehoz egy új erőforráscsoportot a cél régióban "automatikus" utótaggal rendelkező névvel. Erőforráscsoport létrehozásánál Azure Site Recovery által már létezik, a fogja használni. Választhatja azt is, ahogy az alábbi szakasz testre szabhatja.    
3. **Cél virtuális hálózat:** alapértelmezés szerint Azure Site Recovery létrehoz egy új virtuális hálózat a cél régióban "automatikus" utótaggal rendelkező nevű. Ez a forrás hálózati kell hozzárendelni, és minden jövőbeli védelmi használandó.

    > [!NOTE]
    > [Ellenőrizze a hálózati részleteket](site-recovery-network-mapping-azure-to-azure.md) további információkat a hálózat leképezését.

4. **Cél Storage-fiókok:** alapértelmezés szerint az Azure Site Recovery az új cél tárfiók a forrás virtuális gép tárolókonfiguráció mimicking hoz létre. Abban az esetben, ha már Azure Site Recovery által létrehozott tárfiók létezik, a fogja használni.

5. **Storage-fiókok gyorsítótárazása:** Azure Site Recovery gyorsítótárazása nevű adatforrás régióban extra storage-fiók szükséges. Történik a forrás virtuális gépeken végrehajtott módosításokat a nyomon követheti és a gyorsítótár tárfiók előtt replikálni azokat a célhelyre küldött.

6. **A rendelkezésre állási csoporthoz:** alapértelmezés szerint a Azure Site Recovery létrehoz egy új rendelkezésre állási cél régióban "automatikus" utótaggal rendelkező név megadva. Azure Site Recovery már létrehozta a rendelkezésre állási csoport létezik, a fogja használni.

7.  **Replikációs házirend:** azt határozza meg a helyreállítási pont megőrzési előzményeit és az alkalmazás alkalmazáskonzisztens pillanatkép gyakorisága beállításait. Alapértelmezés szerint Azure Site Recovery létrehoz egy új replikációs házirendet a ' 24 órányi a helyreállítási pontok megőrzésének ideje és a "60 percig app alkalmazáskonzisztens pillanatkép gyakorisága alapértelmezett beállításokkal.

    ![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>Tároló erőforrásait testreszabása

Abban az esetben, ha az Azure Site Recovery által használt alapértelmezett módosítani szeretné, hogy megváltoztathatják a beállításokat, a igények alapján.

1. **Testreszabása:** kattintson rá az Azure Site Recovery által használt alapértelmezett módosítása.

2. **Cél-erőforráscsoport:** az erőforráscsoportot kiválaszthatja a meglévő a célhelyen az előfizetésen belüli összes erőforráscsoport listájáról.

3. **Cél virtuális hálózat:** található a listában, a virtuális hálózat a célhelyen.

4. **A rendelkezésre állási csoporthoz:** rendelkezésre állási készletek beállítások csak hozzáadása a rendelkezésre állási forrás régióban részét képező virtuális gépeket.

5. **Cél Storage-fiókok:**

![Engedélyezze a replikálást](./media/site-recovery-replicate-azure-to-azure/customize.PNG) kattintson a **tároló-erőforrás létrehozása** és a replikáció engedélyezése


Amennyiben a védett virtuális gépek a virtuális gépek állapotát állapotának ellenőrizheti **replikált elemek**

>[!NOTE]
>Az időre, amíg a kezdeti replikáció lehetnek egy előfordulhat, hogy az állapot frissítése időt vesz igénybe, és nem jelenik meg a folyamatban van egy kis ideig. A legfrissebb állapotának beolvasása a panel felső részén található frissítés gombra kattinthat.
>

![A replikáció engedélyezése](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)


## <a name="next-steps"></a>Következő lépések
- [További](site-recovery-test-failover-to-azure.md) feladatátvételi teszt futtatása.
- [További](site-recovery-failover.md) kapcsolatos különféle a feladatátvételeket, és futtatásukhoz.
- További információ [helyreállítási tervek segítségével](site-recovery-create-recovery-plans.md) RTO csökkentése érdekében.
- További információ [újbóli védelméhez az Azure virtuális gépek](site-recovery-how-to-reprotect.md) feladatátvételt követően.
