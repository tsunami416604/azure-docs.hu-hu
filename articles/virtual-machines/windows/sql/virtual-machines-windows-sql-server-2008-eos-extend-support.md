---
title: SQL Server 2008 és az SQL Server 2008 R2 with Azure támogatás kiterjesztése
description: Ismerje meg az Azure-ba való migrálás az SQL Server-példányt, vagy kiterjesztett technikai támogatás, hogy a helyszíni példányok megvásárlását által bővítése a SQL Server 2008 és az SQL Server 2008 R2 támogatása.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 398b7a78c9bc13631e15c8725efb8cc0fba3f127
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806476"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>SQL Server 2008 és az SQL Server 2008 R2 with Azure támogatás kiterjesztése

Az SQL Server 2008 és az SQL Server 2008 R2 is hamarosan eléri a [azok (EOS) támogatási életciklusa végét](https://www.microsoft.com/sql-server/sql-server-2008). Mivel számos ügyfelünk továbbra is használja mindkét verziót, adjuk továbbra is megkaphatja a támogatási számos lehetősége. A helyszíni SQL Server-példányok áttelepítése az Azure-beli virtuális gépek (VM), migrálása az Azure SQL Database, vagy maradjon a helyszínen és a kiterjesztett biztonsági frissítések beszerzési.

Ellentétben a felügyelt példány, egy Azure-beli Virtuálisgép-ba való migrálás nem igényel recertifying az alkalmazások. És eltérően, mivel a helyszínen, az ingyenes kiterjesztett biztonsági javítások által az Azure-beli Virtuálisgép-ba való migrálás. 

Ez a cikk többi része az SQL Server-példányhoz egy Azure virtuális Gépen történő áttelepítésre vonatkozó szempontokat ismerteti. 

## <a name="provisioning"></a>Kiépítés folyamatban 

Az utólagos elszámolású csomagokra van `SQL Server 2008 R2 on Windows Server 2008 R2` az Azure piactéren elérhető rendszerképet. 

Ügyfelek, akik a SQL Server 2008 vagy önálló telepítése vagy frissítése SQL Server 2008 R2 kell. Hasonlóképpen,-ügyfeleknek a Windows Server 2008 vagy kell egyéni VHD-fájlból, a virtuális gép üzembe helyezése, vagy frissítsen a Windows Server 2008 R2. 

A piactéren keresztüli üzembe helyezett rendszerképeket kapható előre telepítve van az SQL IaaS-bővítményt. Az SQL IaaS-bővítményt egy rugalmas licencelési és automatizált javításokat. Önálló telepített virtuális gépek üzembe helyezéséhez ügyfeleknek kell manuálisan telepítenie az SQL IaaS-bővítményt. Windows 2008 nem támogatott az SQL IaaS-bővítményt. 

  > [!NOTE]
  > Miközben az SQL Server `Create` és `Manage` paneleket az Azure Portalon az SQL Server 2008R2-lemezképet fog dolgozni, a következő funkciók vannak _nem támogatott_: Automatikus biztonsági mentést, az Azure Key Vault-integráció, R Services és a tárolókonfigurációt.

## <a name="licensing"></a>Licencelés
Használatalapú fizetés SQL Server 2008R2-telepítéseket konvertálhatja [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/).

Utólagos elszámolású előfizetésre átváltani egy frissítési garanciával (SA) alapú licenccel, ügyfelek regisztrálni kell az SQL virtuális gép [erőforrás-szolgáltató](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). Az SQL virtuális gép erőforrás-szolgáltató regisztrálása után az SQL-licenctípus felcserélhetők AHB és az utólagos elszámolású között lesz. 

Azure virtuális Gépen lévő saját telepített SQL Server 2008 vagy SQL Server 2008 R2 példányok SQL erőforrás-szolgáltatóval regisztrálhat és azok licenctípus utólagos elszámolású előfizetésre átváltani.

## <a name="migration"></a>Migrálás
Áttelepítheti EOS SQL Server-példányokat Azure virtuális gépekhez a manuális biztonsági mentés/visszaállítás módszerekkel; Ez a leggyakoribb áttelepítési módszer a helyszíni Azure virtuális gépekhez.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Tömeges áttelepítések, javasoljuk, hogy [Azure Site Recovery](/azure/site-recovery/site-recovery-overview) szolgáltatás. Az Azure Site Recovery ügyfelek replikálhatja a teljes virtuális Gépet, beleértve az SQL Server helyszíni Azure virtuális gépekhez.

Az SQL Server Azure Site Recovery-alkalmazáskonzisztens pillanatképeket rögzít helyreállítási; biztosításához szükséges és az Azure Site Recovery támogatja az alkalmazáskonzisztens pillanatképek legalább 1 órás időközzel. A lehetséges az SQL Server az Azure Site Recovery áttelepítések minimális RPO 1 óra pedig az RTO 2 órán át, plusz az SQL Server helyreállítási idő.

### <a name="database-migration-service"></a>Database Migration Service

A [Database Migration Service](/dms/dms-overview) esetén lehetőség az ügyfelek áttelepítése a helyszínről az Azure-beli virtuális gép által az SQL Server frissítése az SQL Server 2012 és újabb.

## <a name="disaster-recovery"></a>Vészhelyreállítás

A vészhelyreállítási megoldások EOS SQL Server Azure virtuális gépen a következők:

- **SQL Server-biztonságimásolatok**: Az SQL Server biztonsági mentés az SQL Server esetén a regionális vagy zóna hibák használható. EOS SQL Server felügyelt biztonsági mentési szolgáltatás nem támogatott, mivel az ügyfelek kell manuálisan készítsen biztonsági másolatokat.
- **Naplóküldés**: A napló a szállítási címhez tartozó replika hozhat létre egy másik zónában vagy csökkentése érdekében az RTO folyamatos visszaállítása az Azure-régióban. Ügyfelek kell manuálisan konfigurálnia a naplóküldésben.
- **Az Azure Site Recovery**: A virtuális gép replikálása zónákhoz és régiókhoz keresztül az Azure Site Recovery replikációjára. Az SQL Server alkalmazás Alkalmazáskonzisztens pillanatképek egy esetleges vészhelyzet esetén a helyreállítási biztosításához szükséges. Az Azure Site Recovery kínál legalább 1 óra RPO és 2 óra + az SQL Server helyreállítási idő RTO EOS SQL Server Vészhelyreállítási.

## <a name="security-patching"></a>Biztonsági javítás
Az SQL Server virtuális gépek kibővített biztonsági frissítések kézbesíti a rendszer a Microsoft Update-csatornákon keresztül után az SQL Server rendszerű virtuális gép regisztrálva van az SQL-lel [erőforrás-szolgáltató](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). Javítások vagy manuálisan vagy automatikusan lehet letölteni. 

Az **Automatikus javítás** alapértelmezés szerint engedélyezve van. Az automatizált javítás lehetővé teszi, hogy az Azure automatikus javításokat alkalmazzon az SQL Serveren és az operációs rendszeren. Egy napja a héten, idő és a karbantartási időszak időtartama is megadhat, ha telepítve van az SQL IaaS-bővítményt. Az Azure ebben a karbantartási időszakban végzi el a javításokat. A karbantartási időszak ütemezése a virtuális gép területi beállítása szerinti időt használja.  További információk: [Automated Patching for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md) (Az SQL Server automatikus javítása Azure virtuális gépeken).


## <a name="next-steps"></a>További lépések

Az SQL Server virtuális gép migrálása az Azure-bA

* [SQL Server-adatbázis áttelepítése Azure-beli virtuális gépen futó SQL Serverre](virtual-machines-windows-migrate-sql.md)

Az SQL Server használatának első lépései az Azure virtuális gépeken:

* [SQL Servert futtató virtuális gép létrehozása az Azure Portalon](quickstart-sql-vm-create-portal.md)

Választ találhat az SQL virtuális gépek használatával kapcsolatos gyakori kérdésekre:

* [Az SQL Server használata az Azure Virtual Machinesben – gyakori kérdések](virtual-machines-windows-sql-server-iaas-faq.md)
