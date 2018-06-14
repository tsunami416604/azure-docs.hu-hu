---
title: A többrétegű Dynamics AX-telepítés replikálása Azure Site Recovery segítségével |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan replikálja, és a Dynamics AX védelméhez az Azure Site Recovery használatával
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
ms.date: 03/09/2018
ms.author: asgang
ms.openlocfilehash: b390f6c62a6ddf8c800f79b42a36dac2c4f4c908
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
ms.locfileid: "29876956"
---
# <a name="replicate-a-multitier-dynamics-ax-application-by-using-azure-site-recovery"></a>A többrétegű Dynamics AX alkalmazás replikálása az Azure Site Recovery segítségével

## <a name="overview"></a>Áttekintés


 Dynamics AX egyike a legnépszerűbb ERP megoldásokat helyszínen folyamatok szabványosítására, kezelheti az erőforrásokat és való megfelelés leegyszerűsítését vállalatok által használt. Az alkalmazás fontos, hogy egy szervezet legyen katasztrófahelyzet esetén, mert az alkalmazás kell működik, és minimális időtartam.

Ma Dynamics AX nem biztosít semmilyen out-of-az-box vész helyreállítási funkciók. Dynamics AX sok kiszolgáló-összetevők, például Windows objektum alkalmazáskiszolgáló, Azure Active Directory, az Azure SQL Database, a SharePoint Server és a Reporting Services áll. Kezelheti a vész helyreállítási az egyes összetevők manuálisan nincs csak költséges, de is nagyon eséllyel fordulnak elő hiba.

Ez a cikk azt ismerteti, hogyan készíthető egy vész-helyreállítási megoldást a Dynamics AX-alkalmazás használatával [Azure Site Recovery](site-recovery-overview.md). Egy kattintással helyreállítási tervet, a támogatott konfigurációk és az Előfeltételek segítségével tervezett/nem tervezett feladatátvételi teszteket is ismerteti.



## <a name="prerequisites"></a>Előfeltételek

Dynamics AX alkalmazás vész-helyreállítási végrehajtási a Site Recovery segítségével kell rendelkeznie a következő előfeltételek teljesülését:

• Egy helyszíni Dynamics AX üzembe helyezés beállítása.

• A Site Recovery-tárolóban az Azure-előfizetés létrehozása.

• Ha az Azure a helyreállítási hely a virtuális gépek az Azure virtuális gép Readiness Assessment eszközt futtatja. Az Azure virtuális gépek és a Site Recovery services kompatibilisnek kell lennie.

## <a name="site-recovery-support"></a>Webhely-helyreállítási támogatás

Történő létrehozásának ebben a cikkben, a VMware virtuális gépeket a Dynamics AX 2012 R3 verzióját a Windows Server 2012 R2 Enterprise használtuk. Mivel a hely helyreállítási replikációs alkalmazás független, várhatóan az ajánlás itt ahhoz, hogy a következő forgatókönyvek esetén.

### <a name="source-and-target"></a>Forrása és célja

**Scenario** | **Egy másodlagos helyre** | **Az Azure-ba**
--- | --- | ---
**Hyper-V** | Igen | Igen
**VMware** | Igen | Igen
**Fizikai kiszolgáló** | Igen | Igen

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>A Dynamics AX alkalmazás vész-helyreállítási engedélyezése a Site Recovery segítségével
### <a name="protect-your-dynamics-ax-application"></a>A Dynamics AX alkalmazás védelme
A teljes alkalmazás replikáció és a helyreállítás engedélyezéséhez a Dynamics AX minden összetevő kell védeni.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Az Active Directory és a DNS-replikáció beállítása

Az Active Directory a vész-helyreállítási helyen a Dynamics AX alkalmazás működéséhez szükséges. Azt javasoljuk, hogy a következő két választási lehetőség, az ügyfél helyszíni környezetben összetettsége alapján.

**1. lehetőséget**

Az ügyfél rendelkezik egy kis számú alkalmazást, és a teljes egyetlen tartományvezérlővel rendelkezik helyszíni hely és tervezi a feladataikat együtt átadó a teljes helyre. Azt javasoljuk, hogy a Site Recovery replikáció segítségével a tartomány a tartományvezérlő gép replikálása egy másodlagos helyre (érvényes pont-pont és a hely Azure forgatókönyvek esetén).

**2. lehetőséget**

A felhasználói kérelmek nagy számú, és futtatja az Active Directory-erdőt és tervek néhány alkalmazások feladatátvételt egyszerre. Azt javasoljuk, hogy beállította a vész-helyreállítási helyen egy további tartományvezérlőt (a másodlagos helyek vagy az Azure-ban).

 További információkért lásd: [tartományvezérlő egy vész-helyreállítási helyen elérhetővé](site-recovery-active-directory.md). Ez a dokumentum további része feltételezzük, hogy a tartományvezérlő érhető el a vész-helyreállítási helyen.

### <a name="2-set-up-sql-server-replication"></a>2. SQL Server a replikáció beállítása
Az ajánlott az SQL-réteg védelmére a műszaki útmutatót lásd: [replikálja az SQL Server és az Azure Site Recovery alkalmazások](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. A Dynamics AX-ügyfél és az Application objektum kiszolgáló virtuális gépek védelmének engedélyezése
Hajtsa végre a megfelelő Site Recovery konfigurációs alapján, hogy a virtuális gépek vannak telepítve [Hyper-V](site-recovery-hyper-v-site-to-azure.md) vagy [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Azt javasoljuk, hogy konfigurálja az összeomlás-konzisztens gyakoriságát 15 perc.
>

A következő pillanatkép a VMware-hely Azure-védelmi forgatókönyvek Dynamics-összetevő a virtuális gép védelmi állapotát jeleníti meg.

![Védett elemek](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Hálózatkezelés konfigurálása
**Konfigurálja a virtuális gép számítási és hálózati beállításai**

A Dynamics AX-ügyfél és az alkalmazás objektum kiszolgáló virtuális gépek, a hálózati beállítások konfigurálása a Site Recovery, hogy a Virtuálisgép-hálózatok beolvasása csatolva a jobb oldali vész-helyreállítási hálózati feladatátvételt követően. Győződjön meg arról, hogy az SQL-réteghez irányítható, hogy ezek a Rétegek a katasztrófa utáni helyreállítás hálózati.

A virtuális gép kiválaszthatja a replikált elemeket a hálózati beállítások konfigurálása a következő pillanatkép látható módon:

* Objektum alkalmazáskiszolgáló-kiszolgálók válassza ki a megfelelő rendelkezésre állási csoportot.

* Ha egy statikus IP-cím használata esetén adja meg az IP-cím azt szeretné, hogy a virtuális Gépet a hálózatról a **cél IP-címet** szövegmezőben.

    ![Hálózati beállítások ](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png).


### <a name="5-create-a-recovery-plan"></a>5. Helyreállítási terv létrehozása

A Site Recovery szolgáltatásban, a feladatátvételi folyamat automatizálása helyreállítási tervet is létrehozhat. Egy app réteget és egy webes réteghez adja hozzá a helyreállítási tervben. Rendezze őket a különböző csoporthoz, hogy az előtér-leállása előtt a app réteget.

1. Jelölje ki a Site Recovery-tárolóban az előfizetés, és válassza ki a **helyreállítási tervek** csempére.

2. Válassza ki **+ a helyreállítási terv**, és adjon meg egy nevet.

3. Válassza ki a **forrás** és **cél**. A cél Azure vagy egy másodlagos hely lehet. Ha úgy dönt, hogy Azure, meg kell adnia az üzembe helyezési modellben.

    ![Helyreállítási terv létrehozása](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Válassza ki az alkalmazáskiszolgáló objektum és az ügyfél virtuális gépeket a helyreállítási terv, és válassza ki a ✓.

    ![Jelölje ki az elemet](./media/site-recovery-dynamics-ax/selectvms.png)

    Helyreállítási terv. példa:

    ![Helyreállítási terv részletei](./media/site-recovery-dynamics-ax/recoveryplan.png)

Testre szabhatja, hogy a helyreállítási terv a Dynamics AX alkalmazásához adja hozzá az alábbi lépéseket. Az előző pillanatképet jeleníti meg a teljes helyreállítási tervet, miután hozzáadta a lépéseket.


* **SQL Server feladatátvevő lépéseket**: helyreállítási lépések az adott SQL server kapcsolatos információkért lásd: [replikációs alkalmazások az SQL Server és az Azure Site Recovery](site-recovery-sql.md).

* **Feladatátvételi csoport 1**: az alkalmazás objektum kiszolgáló virtuális gépek a feladatátvétel.
Győződjön meg arról, hogy a kiválasztott helyreállítási pont, a lehető legközelebb az adatbázisba mag, de nem előre azt.

* **Parancsfájl**: hozzáadása belső terheléselosztót (csak az E-A).
Adja hozzá a parancsfájlok az (Azure Automation) az Application objektum kiszolgálói virtuális gép csoport után megjelenik a terheléselosztó hozzáadása. Egy parancsfájl segítségével a feladat végrehajtásához. További információkért lásd: [többrétegű alkalmazási vész-helyreállítási terheléselosztó hozzáadása](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **Feladatátvételi csoport 2**: a Dynamics AX ügyfél virtuális gépek a feladatátvétel. Feladatok átadása a webes réteg virtuális gépeket a helyreállítási terv részeként.


### <a name="perform-a-test-failover"></a>Feladatátvételi teszt végrehajtása

További információ adott Active Directory feladatátvételi tesztje közben: az "Active Directory vész-helyreállítási megoldást" útmutatója.

További információ az SQL server adott feladatátvételi tesztje közben, lásd: [replikálja az SQL Server és az Azure Site Recovery alkalmazások](site-recovery-sql.md).

1. Nyissa meg az Azure portálra, és válassza ki a Site Recovery-tárolóban.

2. Válassza ki a helyreállítási tervben készült Dynamics AX.

3. Válassza ki **feladatátvételi teszt**.

4. Válassza ki a virtuális hálózatot a teszt feladatátvételi megkezdéséhez.

5. A másodlagos környezet végeztével végezheti el az érvényesítést.

6. A ellenőrzések végrehajtását követően, válassza ki a **érvényesítést végrehajtani** és karbantartása a teszt feladatátvételi környezet.

A feladatátvételi teszt végrehajtása további információkért lásd: [tesztelése az Azure Site Recovery a](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Végezzen el egy feladatátvételt

1. Nyissa meg az Azure portálra, és válassza ki a Site Recovery-tárolóban.

2. Válassza ki a helyreállítási tervben készült Dynamics AX.

3. Válassza ki **feladatátvételi**, és válassza ki **feladatátvételi**.

4. A célhálózat válassza ki és **✓** a feladatátvételi folyamat elindításához.

A feladatátvétel további információkért lásd: [a Site Recovery feladatátvételi](site-recovery-failover.md).

### <a name="perform-a-failback"></a>A feladat-visszavételt végrehajtani

Szempontok adott SQL Server feladat-visszavétel során, lásd: [replikálja az SQL Server és az Azure Site Recovery alkalmazások](site-recovery-sql.md).

1. Nyissa meg az Azure portálra, és válassza ki a Site Recovery-tárolóban.

2. Válassza ki a helyreállítási tervben készült Dynamics AX.

3. Válassza ki **feladatátvételi**, és válassza ki **feladatátvételi**.

4. Válassza ki **irányának módosítása**.

5. Válassza ki a megfelelő beállítást: adatok szinkronizálása és a virtuális gép létrehozása.

6. Válassza ki **✓** a feladat-visszavétel megkezdéséhez.


A feladat-visszavétel módjáról további információkért lásd: [feladat-visszavétel VMware virtuális gépek az Azure-ból a helyszíni](site-recovery-failback-azure-to-vmware.md).

## <a name="summary"></a>Összegzés
A Site Recovery segítségével a Dynamics AX-alkalmazás egy teljes automatizált vészhelyreállítási tervet hozhat létre. Egy becsukódjon kezdeményezze a feladatátvételt bárhonnan másodpercen belül, és az alkalmazás, amelyekből megismerheti perc múlva.

## <a name="next-steps"></a>További lépések
Vállalati munkaterhelések védelme a Site Recovery kapcsolatos további információkért lásd: [milyen számítási feladatokat tud védeni?](site-recovery-workload.md).
