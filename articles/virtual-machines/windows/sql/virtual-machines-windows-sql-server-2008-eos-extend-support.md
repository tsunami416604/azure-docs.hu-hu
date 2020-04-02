---
title: Az SQL Server 2008 & a 2008 R2 támogatásának kiterjesztése
description: Az SQL Server 2008 és az SQL Server 2008 R2 támogatásának kiterjesztése az SQL Server-példány Azure-ba való áttelepítésével, vagy a példányok helyszíni megtartásához nyújtott kiterjesztett támogatás megvásárlásával.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: faaff9e9ad5982efac6409f2284158a3d0711331
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548410"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Az SQL Server 2008 és az SQL Server 2008 R2 támogatásának kiterjesztése az Azure-ral

Az SQL Server 2008 és az SQL Server 2008 R2 egyaránt elérte [a támogatási (EOS) életciklusuk végét.](https://www.microsoft.com/sql-server/sql-server-2008) Mivel sok ügyfél továbbra is mindkét verziót használja, számos lehetőséget biztosítunk a támogatás további megszerzéséhez. Áttelepítheti a helyszíni SQL Server-példányokat az Azure virtuális gépekre, áttelepítheti az Azure SQL Database-be, vagy maradhat a helyszínen, és bővített biztonsági frissítéseket vásárolhat.

A felügyelt példányokkal ellentétben az Azure virtuális gépre való áttelepítés nem igényel újrahitelesítést az alkalmazások. És a helyszíni tartózkodással ellentétben ingyenes kiterjesztett biztonsági javításokat kap egy Azure-beli virtuális gépre való áttelepítéssel.

A cikk további részében az SQL Server-példány átáttelepítésével kapcsolatos szempontokat tartalmaz egy Azure-beli virtuális gépre.

A támogatási lehetőségek megszűnéséről a [Támogatás vége című](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)témakörben talál további információt.

## <a name="provisioning"></a>Kiépítés

A Windows Server 2008 R2 rendszeren elérhető a rendszer, ha csak felosztó-ki(ke) **SQL Server 2008 R2** rendszerű rendszer érhető el az Azure Marketplace-en.

Az SQL Server 2008 rendszerű felhasználóknak saját telepítést kell telepíteniük, vagy frissíteniük kell az SQL Server 2008 R2 rendszerre. Hasonlóképpen a Windows Server 2008 felhasználóinak vagy egyéni virtuális merevlemezről kell telepíteniük a virtuális gépüket, vagy frissíteniük kell a Windows Server 2008 R2 rendszerre.

Az Azure Marketplace-en keresztül telepített rendszerképek előre telepített SQL IaaS-bővítményt tartalmazza. Az SQL IaaS-bővítmény a rugalmas licencelés és az automatikus javítás követelménye. Az öntelepítő virtuális gépeket telepítő felhasználóknak manuálisan kell telepíteniük az SQL IaaS bővítményt. Windows Server 2008 rendszeren az SQL IaaS-bővítmény nem támogatott.

> [!NOTE]
> Bár az SQL Server **létrehozása** és **kezelése** panelek együtt fog működni az SQL Server 2008 R2 lemezkép az Azure Portalon, a következő funkciók _nem támogatottak:_ Automatikus biztonsági mentések, Azure Key Vault integráció, R-szolgáltatások és tárolási konfiguráció.

## <a name="licensing"></a>Licencek
Az alkalmazásalapú SQL Server 2008 R2-telepítéseket [azure hybrid benefittá alakíthatják.](https://azure.microsoft.com/pricing/hybrid-benefit/)

A frissítési garancia (SA) alapú licenc it-as fizetésre való konvertálásához az ügyfeleknek regisztrálniuk kell az SQL virtuális gép [erőforrás-szolgáltatójánál.](virtual-machines-windows-sql-register-with-resource-provider.md) A regisztráció után az SQL-licenctípus felcserélhető lesz az Azure Hybrid Benefit és a felosztó-kirovó szolgáltatás között.

Az Azure virtuális gépekön telepített SQL Server 2008 vagy SQL Server 2008 R2 példányok regisztrálhatnak az SQL virtuálisgép-erőforrás-szolgáltatónál, és licenctípusukat felosztó-kirovóvá alakíthatják.

## <a name="migration"></a>Migrálás
Az EOS SQL Server-példányokat manuális biztonsági mentési és visszaállítási módszerekkel telepítheti át egy Azure-beli virtuális gépre. Ez a leggyakoribb áttelepítési módszer a helyszíni azure-beli virtuális gép.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Tömeges áttelepítések esetén javasoljuk az [Azure Site Recovery](/azure/site-recovery/site-recovery-overview) szolgáltatást. Az Azure Site Recovery segítségével az ügyfelek replikálhatják a teljes virtuális gép, beleértve az SQL Server a helyszíni Azure Virtuális gép.

Az SQL Server alkalmazáskonzisztens Azure Site Recovery pillanatképeket igényel a helyreállítás biztosításához. Az Azure Site Recovery legalább 1 órás időközzel támogatja az alkalmazáskonzisztens pillanatképeket. Az Azure Site Recovery-áttelepítéssel rendelkező SQL Server esetében lehetséges minimális helyreállításipont (RPO) 1 óra. A helyreállítási idő célkitűzés (RTO) 2 óra plusz SQL Server helyreállítási idő.

### <a name="database-migration-service"></a>Adatbázis-áttelepítési szolgáltatás

Az [adatbázis-áttelepítési szolgáltatás](/azure/dms/dms-overview) egy lehetőség az ügyfelek számára, ha a helyszíni azure-beli virtuális gépről az SQL Server 2012-es vagy újabb verziójára való frissítésével áttelepülnek.

## <a name="disaster-recovery"></a>Vészhelyreállítás

Az Azure-beli virtuális gépeken az EOS SQL Server vész-helyreállítási megoldásai a következők:

- **SQL Server biztonsági mentések:** Az Azure Backup segítségével megvédheti az EOS SQL Server 2008 és 2008 R2 programot a zsarolóprogramok, a véletlen törlés és a 15 perces RPO és az időponthoz kapcsolódó helyreállítás elleni védelemhez. További részletek ebben [a cikkben olvashatók.](https://docs.microsoft.com/azure/backup/sql-support-matrix#scenario-support)
- **Naplószállítás:** Létrehozhat egy naplószállítási replikát egy másik zónában vagy az Azure-régióban folyamatos visszaállításokkal az RTO csökkentése érdekében. Manuálisan kell konfigurálnia a naplószállítást.
- **Azure Site Recovery:** Replikálhatja a virtuális gép a zónák és a régiók között az Azure Site Recovery replikációs. Az SQL Server alkalmazáskonzisztens pillanatképeket igényel a helyreállítás biztosításához katasztrófa esetén. Az Azure Site Recovery legalább 1 órás RPO-t és 2 órás (plusz SQL Server helyreállítási idő) RTO-t kínál az EOS SQL Server vész-helyreállítási rendszeréhez.

## <a name="security-patching"></a>Biztonsági javítás
Az SQL Server virtuális gépek kiterjesztett biztonsági frissítései a Microsoft Update csatornákon keresztül érhetők el, miután az SQL Server virtuális gép regisztrálva van az SQL VM [erőforrás-szolgáltatónál.](virtual-machines-windows-sql-register-with-resource-provider.md) A javítások manuálisan vagy automatikusan letölthetők.

Az *Automatikus javítás* alapértelmezés szerint engedélyezve van. Az automatizált javítás lehetővé teszi, hogy az Azure automatikus javításokat alkalmazzon az SQL Serveren és az operációs rendszeren. Ha az SQL Server IaaS-bővítmény telepítve van, megadhatja a hét napját, idejét és időtartamát a karbantartási időszakhoz. Az Azure ebben a karbantartási időszakban végzi el a javításokat. A karbantartási időszak ütemezése a virtuális gép területi beállítása szerinti időt használja.  További információ: [Automatikus javítás az SQL Server hez az Azure virtuális gépeken című témakörben.](virtual-machines-windows-sql-automated-patching.md)


## <a name="next-steps"></a>További lépések

Az SQL Server virtuális gép áttelepítése az Azure-ba:

* [SQL Server-adatbázis áttelepítése Azure-beli virtuális gépen futó SQL Serverre](virtual-machines-windows-migrate-sql.md)

Ismerkedés az SQL Server alkalmazással az Azure virtuális gépeken:

* [SQL Servert futtató virtuális gép létrehozása az Azure Portalon](quickstart-sql-vm-create-portal.md)

Válaszok az SQL Server virtuális gépekkel kapcsolatos gyakori kérdésekre:

* [Gyakori kérdések az SQL Server rel kapcsolatban az Azure virtuális gépeken](virtual-machines-windows-sql-server-iaas-faq.md)

További információ a támogatási lehetőségek befejezéséről és a kiterjesztett biztonsági frissítésekről:

* [A támogatás megszűnése](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) & [Kiterjesztett biztonsági frissítések](/sql/sql-server/end-of-support/sql-server-extended-security-updates)