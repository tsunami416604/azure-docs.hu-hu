---
title: A Dynamics AX vészhelyreállítása az Azure Site Recovery szolgáltatással
description: Ismerje meg, hogyan állíthatja be a Dynamics AX vészutáni helyreállítását az Azure Site Recovery szolgáltatással
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 0b32f00374aa8ce6c41415e28f319e3e7d5abddb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941587"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>Vészhelyreállítás beállítása többrétegű Dynamics AX-alkalmazáshoz   




 A Dynamics AX az egyik legnépszerűbb ERP-megoldás, amelyet a vállalatok a folyamatok különböző helyek közötti szabványosítására, az erőforrások kezelésére és a megfelelőség egyszerűsítésére használnak. Mivel az alkalmazás kritikus fontosságú egy szervezet számára, katasztrófa esetén az alkalmazásnak minimális idő alatt kell felkeserednie és futnia kell.

Ma a Dynamics AX nem biztosít semmilyen beépített vész-helyreállítási képességeket. A Dynamics AX számos kiszolgálóösszetevőből áll, például a Windows Application Object Serverből, az Azure Active Directoryból, az Azure SQL Databaseből, a SharePoint Serverből és a Reporting Servicesszolgáltatásból. Az egyes összetevők vész-helyreállítási manuális kezelése nem csak költséges, hanem hibalehetőségeket is rejt.

Ez a cikk bemutatja, hogyan hozhat létre vészhelyreállítási megoldást a Dynamics AX-alkalmazáshoz az [Azure Site Recovery](site-recovery-overview.md)használatával. Azt is magában foglalja a tervezett/nem tervezett teszt feladatátvételek egy kattintással helyreállítási terv, támogatott konfigurációk és előfeltételek használatával.



## <a name="prerequisites"></a>Előfeltételek

A Dynamics AX-alkalmazás vész-helyreállítási megvalósításának megvalósítása a Site Recovery használatával a következő előfeltételeket igényli:

• Hozzon létre egy helyszíni Dynamics AX központi telepítést.

• Hozzon létre egy Site Recovery trezort egy Azure-előfizetésben.

• Ha az Azure a helyreállítási hely, futtassa az Azure virtuális gép relevanciád-felmérési eszközét a virtuális gépeken. Kompatibilisnek kell lennie az Azure virtuális gépek és a site recovery szolgáltatások.

## <a name="site-recovery-support"></a>Site Recovery támogatása

A cikk létrehozásához a Windows Server 2012 R2 Enterprise rendszeren a Dynamics AX 2012 R3 rendszerrel rendelkező VMware virtuális gépeket használtuk. Mivel a Site Recovery replikációalkalmazás-független, az itt megadott javaslatok at a következő esetekben kell tárolni.

### <a name="source-and-target"></a>Forrás és cél

**Forgatókönyv** | **Egy másodlagos helyre** | **Az Azure-ba**
--- | --- | ---
**Hyper-V** | Igen | Igen
**Vmware** | Igen | Igen
**Fizikai kiszolgáló** | Igen | Igen

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>A Dynamics AX alkalmazás vészutáni helyreállításának engedélyezése a Site Recovery használatával
### <a name="protect-your-dynamics-ax-application"></a>A Dynamics AX alkalmazás védelme
A teljes alkalmazásreplikáció és -helyreállítás engedélyezéséhez a Dynamics AX minden egyes összetevőjét védeni kell.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Az Active Directory és a DNS replikáció beállítása

Az Active Directory szükséges a vész-helyreállítási helyen a Dynamics AX alkalmazás működéséhez. Az ügyfél helyszíni környezetének összetettsége alapján az alábbi két választási lehetőséget javasoljuk.

**1. lehetőség**

Az ügyfél rendelkezik egy kis számú alkalmazás és egy tartományvezérlő a teljes helyszíni hely, és azt tervezi, hogy feladatátvétela az egész hely együtt. Azt javasoljuk, hogy a Site Recovery replikáció használatával replikálja a tartományvezérlő-gépet egy másodlagos helyre (amely mind a helyek, mind a helyek közötti és az Azure-ba forgatókönyvek esetén alkalmazható).

**2. lehetőség**

Az ügyfél számos alkalmazással rendelkezik, és egy Active Directory-erdőt futtat, és azt tervezi, hogy egyszerre néhány alkalmazást átad. Azt javasoljuk, hogy hozzon létre egy további tartományvezérlőt a vész-helyreállítási helyen (egy másodlagos helyen vagy az Azure-ban).

 További információt a [Tartományvezérlő elérhetővé tévává az vész-helyreállítási helyen című témakörben talál.](site-recovery-active-directory.md) A dokumentum további, feltételezzük, hogy egy tartományvezérlő érhető el a vész-helyreállítási helyen.

### <a name="2-set-up-sql-server-replication"></a>2. Sql Server replikáció beállítása
Az SQL-szint védelmére ajánlott beállítással kapcsolatos technikai útmutatásért olvassa el az [alkalmazások replikálása sql server rel és az Azure Site Recovery szolgáltatással című témakört.](site-recovery-sql.md)

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Védelem engedélyezése a Dynamics AX-ügyfél és az Application Object Server virtuális gépek számára
Végezze el a megfelelő site recovery konfigurációt attól függően, hogy a virtuális gépek telepítve [vannak-e a Hyper-V vagy](site-recovery-hyper-v-site-to-azure.md) a [VMware szolgáltatásban.](site-recovery-vmware-to-azure.md)

> [!TIP]
> Azt javasoljuk, hogy konfigurálja az összeomlás-konzisztens gyakoriság15 perc.
>

A következő pillanatkép a Dynamics-component virtuális gépek védelmi állapotát mutatja be egy VMware-hely–Azure védelmi forgatókönyvben.

![Védett elemek](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Hálózat konfigurálása
**Vm számítási és hálózati beállítások konfigurálása**

A Dynamics AX-ügyfél- és az Application Object Server virtuális gépek esetében konfigurálja a hálózati beállításokat a Site Recovery alkalmazásban úgy, hogy a virtuálisgép-hálózatok a feladatátvétel után a megfelelő vész-helyreállítási hálózathoz kapcsolódjanak. Győződjön meg arról, hogy a vész-helyreállítási hálózat ezek a rétegek irányítható az SQL-szintre.

A virtuális gép kiválasztásával konfigurálhatja a hálózati beállításokat, ahogy az a következő pillanatképben látható:

* Alkalmazásobjektum-kiszolgálók esetén válassza ki a megfelelő rendelkezésre állási készletet.

* Statikus IP-cím használata esetén adja meg azt az IP-címet, amelyet a virtuális gépnek a **cél IP-szövegmezőben** kell használnia.

    ![Hálózati beállítások](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. Helyreállítási terv létrehozása

A feladatátvételi folyamat automatizálásához hozzon létre helyreállítási tervet a Site Recovery-ben. Adjon hozzá egy alkalmazásszintet és egy webes réteget a helyreállítási tervben. Rendelje meg őket különböző csoportokban, hogy az előtér leáll az alkalmazásszint előtt.

1. Válassza ki a Site Recovery tárolót az előfizetésében, és válassza a **Helyreállítási tervek** csempét.

2. Válassza a **+ helyreállítási terv**lehetőséget , és adjon meg egy nevet.

3. Válassza ki a **Forrás** és **a Cél**lehetőséget . A cél lehet az Azure vagy egy másodlagos hely. Ha az Azure-t választja, meg kell adnia a központi telepítési modellt.

    ![Helyreállítási terv létrehozása](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Válassza ki az Application Object Server és az ügyfél virtuális gépek a helyreállítási terv, és válassza ki a ✓.

    ![Elemek kijelölése](./media/site-recovery-dynamics-ax/selectvms.png)

    Például helyreállítási terv:

    ![A helyreállítási terv részletei](./media/site-recovery-dynamics-ax/recoveryplan.png)

A Dynamics AX alkalmazás helyreállítási tervét a következő lépések hozzáadásával testreszabhatja. Az előző pillanatkép az összes lépés hozzáadása után megjeleníti a teljes helyreállítási tervet.


* **SQL Server feladatátvételi lépései**: Az SQL-kiszolgálóra vonatkozó helyreállítási lépésekről az SQL Server és az [Azure Site Recovery replikációs alkalmazások című](site-recovery-sql.md)témakörében talál további információt.

* **1. feladatátvételi csoport:** Feladatátvétel az Application Object Server virtuális gépein.
Győződjön meg arról, hogy a kiválasztott helyreállítási pont a lehető legközelebb van az adatbázis PIT adatbázishoz, de nem előtte.

* **Script**: Terheléselosztó hozzáadása (csak E-A).
Adjon hozzá egy parancsfájlt (az Azure Automationen keresztül) miután az Application Object Server virtuális gépcsoport jön létre, hogy hozzáadjon egy terheléselosztót. A feladat végrehajtásához parancsfájlt használhat. További információ: [Terheléselosztó hozzáadása többrétegű alkalmazás vész-helyreállítási.](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/)

* **2 feladatátvételi csoport:** A Dynamics AX-ügyfél virtuális gépei átvétele. A helyreállítási terv részeként a webes réteg virtuális gépei nem felelnek meg.


### <a name="perform-a-test-failover"></a>Teszt feladatátvétel végrehajtása

Az Active Directoryra vonatkozó további információkért tekintse meg az "Active Directory vész-helyreállítási megoldás" kísérőútmutatóját.

A tesztfeladat-átvétel során az SQL-kiszolgálóra vonatkozó további információkért [lásd: Alkalmazások replikálása sql server rel és az Azure Site Recovery szolgáltatással.](site-recovery-sql.md)

1. Nyissa meg az Azure Portalon, és válassza ki a site recovery-tároló.

2. Válassza ki a Dynamics AX-hez létrehozott helyreállítási tervet.

3. Kattintson a **Feladatátvétel tesztelése** elemre.

4. Válassza ki a virtuális hálózatot a teszt feladatátvételi folyamat elindításához.

5. A másodlagos környezet leállása után elvégezheti az ellenőrzéseket.

6. Az ellenőrzések befejezése után válassza **az Érvényesítések befejeződött,** és a teszt feladatátvételi környezet törlődik.

A tesztfeladat-átvétel végrehajtásáról további információt a [Feladatátvétel tesztelése az Azure-ba a Site Recovery szolgáltatásban című témakörben talál.](site-recovery-test-failover-to-azure.md)

### <a name="perform-a-failover"></a>Feladatátvétel végrehajtása

1. Nyissa meg az Azure Portalon, és válassza ki a site recovery-tároló.

2. Válassza ki a Dynamics AX-hez létrehozott helyreállítási tervet.

3. Válassza **a Feladatátvétel**lehetőséget, majd a **Feladatátvétel**lehetőséget.

4. Válassza ki a célhálózatot, és válassza **a ✓** lehetőséget a feladatátvételi folyamat elindításához.

A feladatátvételről a [Site Recovery feladatátvétel című témakörben](site-recovery-failover.md)talál további információt.

### <a name="perform-a-failback"></a>Feladat-visszavétel végrehajtása

Az SQL Server feladat-visszavétel során kapcsolatos szempontokról az [ALKALMAZÁSOK replikálása az SQL Server és az Azure Site Recovery használatával](site-recovery-sql.md)című témakörben található.

1. Nyissa meg az Azure Portalon, és válassza ki a site recovery-tároló.

2. Válassza ki a Dynamics AX-hez létrehozott helyreállítási tervet.

3. Válassza **a Feladatátvétel**lehetőséget, majd a **Feladatátvétel**lehetőséget.

4. Válassza **az Irányváltás lehetőséget.**

5. Válassza ki a megfelelő beállításokat: adatszinkronizálás és virtuális gép létrehozása.

6. Válassza az **✓** lehetőséget a feladat-visszavételi folyamat elindításához.


A feladat-visszavételről további információt a [Feladat-visszaszolgáltatás virtuális gépei az Azure-ból a helyszíni gépekbe című témakörben talál.](site-recovery-failback-azure-to-vmware.md)

## <a name="summary"></a>Összefoglalás
A Site Recovery használatával teljes körű automatikus vész-helyreállítási tervet hozhat létre a Dynamics AX-alkalmazáshoz. Megszakítás esetén a feladatátvételt másodperceken belül kezdeményezheti bárhonnan, és percek alatt működésbe hozhatja az alkalmazást.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a vállalati munkaterhelések védelméről a Site Recovery segítségével, olvassa el a [Milyen számítási feladatokat védhetek meg?](site-recovery-workload.md)
