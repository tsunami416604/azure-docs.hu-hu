---
title: Egy többrétegű Dynamics AX-telepítés replikálása az Azure Site Recovery használatával |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan replikálja, és a Dynamics AX védelme az Azure Site Recovery használatával
services: site-recovery
documentationcenter: ''
author: asgang
manager: rochakm
editor: ''
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: asgang
ms.openlocfilehash: eb6f7d9b34e00ce1efd8c871439c2504e5f550d5
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669442"
---
# <a name="replicate-a-multitier-dynamics-ax-application-by-using-azure-site-recovery"></a>Egy többrétegű Dynamics AX-alkalmazás replikálása az Azure Site Recovery használatával

## <a name="overview"></a>Áttekintés


 A Dynamics AX egyike a vállalat használja a helyek közötti szabványosíthatják a folyamatokat, kezelheti az erőforrásokat és leegyszerűsítse a megfelelőség a legnépszerűbb ERP-megoldásokat. Mivel az alkalmazás kritikus nyújtva a cég katasztrófa esetén az alkalmazás minimális idő kell lennie a helyezheti üzembe.

Még ma Dynamics AX nem biztosít semmilyen-a-beépített vészhelyreállítási képességeket. A Dynamics AX számos kiszolgáló-összetevők, például a Windows alkalmazáskiszolgáló-objektum, Azure Active Directory, Azure SQL Database, a SharePoint-kiszolgáló és Reporting Services áll. Kezelheti a vész helyreállítási az egyes összetevők manuálisan nem áll csak költséges, de is hibalehetőségeket rejt magában.

Ez a cikk bemutatja, hogyan készíthető egy vész-helyreállítási megoldást a Dynamics AX-alkalmazás használatával [Azure Site Recovery](site-recovery-overview.md). Tervezett/nem tervezett feladatátvételi tesztek használatával, egy kattintással helyreállítási tervet, a támogatott konfigurációk és az Előfeltételek emellett ismerteti.



## <a name="prerequisites"></a>Előfeltételek

Végrehajtási Dynamics AX-alkalmazások vészhelyreállítása a Site Recovery használatával van szükség a következő előfeltételek vonatkoznak:

• Egy helyszíni Dynamics AX üzemelő beállítása.

• A Site Recovery-tároló létrehozása az Azure-előfizetéssel.

• Ha az Azure helyreállítási webhelyként, az Azure Virtual Machine Readiness Assessment eszköz virtuális gépeken futnak. Az Azure Virtual Machines és a Site Recovery services-kompatibilis kell lennie.

## <a name="site-recovery-support"></a>Site Recovery támogatási

Ez a cikk létrehozása, céljából a VMware virtuális gépek a Dynamics AX 2012 R3 verzióját a Windows Server 2012 R2 Enterprise használtuk. Mivel a site recovery-replikációja alkalmazás független, várhatóan a javaslatok, az itt elérhető, amely tárolja a következő célokra.

### <a name="source-and-target"></a>Forrás és cél

**Forgatókönyv** | **Egy másodlagos helyre** | **Az Azure-ba**
--- | --- | ---
**Hyper-V** | Igen | Igen
**VMware** | Igen | Igen
**Fizikai kiszolgáló** | Igen | Igen

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>A Dynamics AX-alkalmazások vész-helyreállítási engedélyezése a Site Recovery használatával
### <a name="protect-your-dynamics-ax-application"></a>A Dynamics AX-alkalmazások védelme
A teljes alkalmazás replikálásának és helyreállításának engedélyezéséhez minden egyes összetevő, a Dynamics AX védelme biztosítható.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Az Active Directory és DNS-replikálás beállítása

Az Active Directory a vész-helyreállítási webhelyként, a Dynamics AX alkalmazás működéséhez szükséges. Azt javasoljuk, hogy a következő két választási lehetőség, az ügyfél a helyszíni környezet összetettsége alapján.

**1. lehetőséget**

Az ügyfél rendelkezik egy kis számú alkalmazást, és a teljes egyetlen tartományvezérlő a helyszíni hely és a tervek a feladatátvételt együtt a teljes helyre. Azt javasoljuk, hogy a Site Recovery replikációs a domain controller gép replikálása egy másodlagos helyre (site-to-site és a hely – Azure forgatókönyvek esetén alkalmazandó) használja.

**2. lehetőséget**

Az ügyfél nagy számú az alkalmazások, és az Active Directory-erdő és a tervek szerint néhány alkalmazások feladatátvételt egyszerre fut. Javasoljuk, hogy állítsa be a vész-helyreállítási helyre további tartományvezérlő (egy másodlagos helyre vagy az Azure-ban).

 További információkért lásd: [elérhetővé tenni a tartományvezérlő egy vész-helyreállítási webhelyként](site-recovery-active-directory.md). Ez a dokumentum további része feltételezzük, hogy a tartományvezérlő érhető el a vész-helyreállítási helyre.

### <a name="2-set-up-sql-server-replication"></a>2. SQL Server-replikáció beállítása
Technikai útmutató a javasolt megoldás védelme érdekében az SQL-szinten, lásd: [alkalmazásait az SQL Server és az Azure Site Recovery replikálja](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. A Dynamics AX-ügyfél és az alkalmazás objektum Serveres virtuális gépek védelmének engedélyezése
Hajtsa végre a megfelelő alapján, hogy a virtuális gépek vannak telepítve a Site Recovery-konfigurációs [Hyper-V](site-recovery-hyper-v-site-to-azure.md) vagy [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Azt javasoljuk, hogy konfigurálja-e az összeomlás-konzisztens gyakoriság 15 perc.
>

A következő pillanatkép VMware-hely – Azure protection helyzetekben Dynamics-összetevő a virtuális gép védelmi állapotát jeleníti meg.

![Védett elemek](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Hálózatkezelés konfigurálása
**Konfigurálja a virtuális gép számítási és hálózati beállítások**

A Dynamics AX-ügyfél és az alkalmazás objektum Serveres virtuális gépek, a hálózati beállítások konfigurálása a Site Recovery úgy, hogy a Virtuálisgép-hálózatok a megfelelő vész-helyreállítási hálózat első csatolt a feladatátvételt követően. Győződjön meg arról, hogy a vész-helyreállítási hálózatot, ezek a rétegek irányítható az SQL-réteghez.

A virtuális gép kiválaszthatja a replikált elemek, a hálózati beállítások konfigurálása a következő pillanatkép látható módon:

* Objektum-alkalmazáskiszolgálói kiszolgálók válassza ki a megfelelő rendelkezésre állási csoport.

* Ha statikus IP-címet használ, adja meg az IP-Címmel lépése a virtuális gépet a **cél IP-címet** szövegmezőben.

    ![Hálózati beállítások ](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. Helyreállítási terv létrehozása

A helyreállítási terv a Site Recovery segítségével automatizálja a feladatátvételi folyamatot hozhat létre. Adjon hozzá egy alkalmazást és egy webes réteget a helyreállítási tervben. Rendezze őket a különböző csoportok úgy, hogy az előtér-leállítja az app-réteget előtt.

1. Válassza ki a Site Recovery-tároló az előfizetésében, és válassza ki a **helyreállítási tervek** csempére.

2. Válassza ki **+ a helyreállítási terv**, és adjon meg egy nevet.

3. Válassza ki a **forrás** és **cél**. A cél Azure-ban vagy egy másodlagos hely lehet. Ha úgy dönt, Azure, a központi telepítési modellt kell megadnia.

    ![Helyreállítási terv létrehozása](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Válassza ki az alkalmazáskiszolgáló objektum és az ügyfél virtuális gépeket a helyreállítási terv, és válassza ki a ✓.

    ![Elemek kiválasztása](./media/site-recovery-dynamics-ax/selectvms.png)

    Helyreállítási terv. példa:

    ![Helyreállítási terv részletei](./media/site-recovery-dynamics-ax/recoveryplan.png)

A következő lépések hozzáadásával testre szabhatja a helyreállítási terv a Dynamics AX-alkalmazáshoz. Az előző pillanatképet mutatja a teljes helyreállítási tervet, minden lépés hozzáadása után.


* **Az SQL Server feladatátvevő lépéseket**: SQL Server adott helyreállítási feladatokra vonatkozó információkért lásd: [az SQL Server és az Azure Site Recovery replikációs alkalmazások](site-recovery-sql.md).

* **Feladatátvételi csoport 1**: az alkalmazás objektum Serveres virtuális gépek feladatátvételét.
Győződjön meg arról, hogy a kiválasztott helyreállítási pont, de nem zárja be az adatbázis PILLANATKÉPÉNEK, a lehető előre azt.

* **Parancsfájl**: hozzáadása terheléselosztó (csak E-A).
Adjon hozzá egy terheléselosztó hozzáadása betölt egy parancsfájl (keresztül az Azure Automation) után az alkalmazás objektum Server VM-csoportazonosítóval. Egy parancsfájl segítségével a feladat végrehajtásához. További információkért lásd: [többrétegű alkalmazás vész-helyreállítási terheléselosztó hozzáadása](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **Feladatátvételi csoport 2**: a Dynamics AX-ügyfél virtuális gépek feladatátvételét. A helyreállítási terv részeként a webes szintű virtuális gépek feladatátvételét.


### <a name="perform-a-test-failover"></a>Feladatátvételi teszt végrehajtása

További információ az Active Directory adott feladatátvételi teszt során az "Active Directory vész-helyreállítási megoldást" kiegészítő útmutatójában talál.

További információ az SQL server adott feladatátvételi teszt során, tekintse meg a [alkalmazásait az SQL Server és az Azure Site Recovery replikálja](site-recovery-sql.md).

1. Nyissa meg az Azure Portalon, és válassza ki a Site Recovery-tárolót.

2. Válassza ki a helyreállítási terv létrehozása a Dynamics AX.

3. Válassza ki **feladatátvételi teszt**.

4. Válassza ki a virtuális hálózat, a teszt feladatátvételi folyamat elindításához.

5. A másodlagos környezet után az ellenőrzések hajthat végre.

6. Az ellenőrzés befejezése után jelölje ki a **ellenőrzések elvégzéséhez** és a teszt feladatátvételi környezetet karbantartása.

További információ a feladatátvételi teszt végrehajtásához: [tesztelheti a feladatátvételeket, az Azure-bA a Site Recoveryben](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Feladatátvétel végrehajtása

1. Nyissa meg az Azure Portalon, és válassza ki a Site Recovery-tárolót.

2. Válassza ki a helyreállítási terv létrehozása a Dynamics AX.

3. Válassza ki **feladatátvételi**, és válassza ki **feladatátvételi**.

4. Válassza ki a cél-hálózatot, és válassza ki **✓** a feladatátvételi folyamat elindításához.

A feladatátvétel végrehajtása további információkért lásd: [feladatátvétel a Site Recoveryben](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Feladat-visszavétel végrehajtása

További szempontokért adott SQL Server feladat-visszavétel során: [alkalmazásait az SQL Server és az Azure Site Recovery replikálja](site-recovery-sql.md).

1. Nyissa meg az Azure Portalon, és válassza ki a Site Recovery-tárolót.

2. Válassza ki a helyreállítási terv létrehozása a Dynamics AX.

3. Válassza ki **feladatátvételi**, és válassza ki **feladatátvételi**.

4. Válassza ki **irányának módosítása**.

5. Válassza ki a megfelelő beállításokat: adatok szinkronizálása és a virtuális gép létrehozása.

6. Válassza ki **✓** a feladat-visszavételi folyamat elindításához.


Ez a feladat-visszavételhez további információkért lásd: [feladat-visszavétel VMware virtuális gépek az Azure-ból a helyszíni](site-recovery-failback-azure-to-vmware.md).

## <a name="summary"></a>Összegzés
Site Recovery használatával egy teljes automatizált vészhelyreállítási tervet hozhat létre a Dynamics AX-alkalmazáshoz. Egy bekövetkező szolgáltatáskimaradás esetén kezdeményezze a feladatátvételt bárhonnan másodpercen belül, és megkezdheti az alkalmazások percek alatt.

## <a name="next-steps"></a>További lépések
A Site Recovery a vállalati számítási feladatok védelmét kapcsolatos további információkért lásd: [milyen számítási feladatokat tud védeni?](site-recovery-workload.md).
