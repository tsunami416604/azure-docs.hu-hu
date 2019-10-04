---
title: SQL Server on Linux Azure Virtual Machines GYIK | Microsoft Docs
description: Ez a cikk a SQL Server on Linux Azure-beli virtuális gépek futtatásával kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1e729c608a2cad28c810f8d5236360c909a496b0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082037"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Gyakori kérdések a SQL Server on Linux Azure-Virtual Machines

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Ez a cikk a [SQL Server on Linux Azure Virtual Machines](sql-server-linux-virtual-machines-overview.md)futtatásával kapcsolatos leggyakoribb kérdésekre ad választ.

> [!NOTE]
> Ez a cikk SQL Server on Linux virtuális gépekre vonatkozó problémákra koncentrál. Ha Windows rendszerű virtuális gépeken futtat SQL Server, tekintse meg a [Windows gyakori kérdések](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)című témakört.

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a>Képek

1. **Milyen SQL Server virtuálisgép-katalógus lemezképei érhetők el?**

   Az Azure a Linux és a Windows összes kiadásában a SQL Server összes támogatott főbb kiadásához biztosít virtuálisgép-lemezképeket. További részletekért tekintse meg a Linux rendszerű [virtuális](sql-server-linux-virtual-machines-overview.md#create) gépek rendszerképeinek és a Windowsos virtuálisgép-rendszerképeknek a teljes listáját. [](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)

1. **Frissültek a meglévő SQL Server virtuálisgép-katalógus rendszerképei?**

   A virtuális gép galériájában SQL Server rendszerképek a legújabb Linux-és Windows-frissítésekkel frissülnek. Linux-rendszerképek esetén ez tartalmazza a legújabb rendszerfrissítéseket. Windows-rendszerképek esetén ide tartoznak a Windows Update fontosként megjelölt frissítések, beleértve a fontos SQL Server biztonsági frissítéseket és szervizcsomagokat is. SQL Server kumulatív frissítések kezelése a Linux és a Windows rendszerben eltérően történik. A Linux esetében SQL Server kumulatív frissítések is szerepelnek a frissítésben. Ebben az esetben azonban a Windows rendszerű virtuális gépek nem frissülnek SQL Server vagy a Windows Server összesítő frissítéseivel.

1. **Milyen kapcsolódó SQL Server csomagokat is telepíteni kell?**

   A SQL Server Linux rendszerű virtuális gépeken alapértelmezés szerint telepített SQL Server csomagok megtekintéséhez lásd: [telepített csomagok](sql-server-linux-virtual-machines-overview.md#packages).

1. **El SQL Server tudja távolítani a virtuális gépek lemezképeit a katalógusból?**

   Igen. Az Azure csak egy rendszerképet tart fenn főverzióként és kiadásként. Ha például új SQL Server szervizcsomagot szabadít fel, az Azure új rendszerképet hoz létre az adott szervizcsomaghoz tartozó galériához. Az előző szervizcsomag SQL Server rendszerképét a rendszer azonnal eltávolítja a Azure Portalból. A következő három hónapban azonban továbbra is elérhető a PowerShellből való kiépítés. Három hónap elteltével az előző szervizcsomag-rendszerkép már nem érhető el. Ez az eltávolítási szabályzat akkor is érvényes, ha a SQL Server verziója nem támogatott, amikor eléri az életciklusának végét.

## <a name="creation"></a>Létrehozása

1. **Hogyan linuxos Azure-beli virtuális gépet SQL Server-vel?**

   A legegyszerűbb megoldás egy olyan linuxos virtuális gép létrehozása, amely SQL Server tartalmaz. Az Azure-regisztrációra és az SQL virtuális gép portálról való létrehozására vonatkozó oktatóanyagért lásd: [Linux SQL Server virtuális gép létesítése a Azure Portal](provision-sql-server-linux-virtual-machine.md). Lehetősége van arra is, hogy manuálisan telepítse a SQL Servert egy olyan virtuális gépre, amely vagy egy ingyenes licenccel rendelkező kiadás (Developer vagy Express), vagy egy helyszíni licenc újrafelhasználásával. Ha saját licencet használ, rendelkeznie kell [licenchordozhatóság frissítési garancia keretében az Azure](https://azure.microsoft.com/pricing/license-mobility)-ban.

1. **Miért nem tudok kiépíteni egy RHEL vagy SLES SQL Server VM egy költségkerettel rendelkező Azure-előfizetéssel?**

   A RHEL és a SLES virtuális gépekhez a költségkeret nélküli előfizetés, valamint az előfizetéshez társított ellenőrzött fizetési mód (általában bankkártyás) szükséges. Ha kiépít egy RHEL vagy SLES virtuális gépet a költségkeret eltávolítása nélkül, az előfizetés le lesz tiltva, és az összes virtuális gép/szolgáltatás leállt. Ha ezt az állapotot futtatja, az előfizetés újbóli engedélyezésével [távolítsa el a](https://account.windowsazure.com/subscriptions)költségkeretet. A fennmaradó kreditek vissza lesznek állítva az aktuális számlázási időszakra, de a RHEL vagy SLES virtuálisgép-lemezkép felára a hitelkártyára kerül, ha úgy dönt, hogy újraindul, és folytatja a futtatását.

## <a name="licensing"></a>Licencelés

1. **Hogyan telepíthetem az SQL Server egy licencelt példányát egy Azure-beli virtuális gépen?**

   Először hozzon létre egy csak Linux RENDSZERű virtuális gépet. Ezután futtassa a [SQL Server telepítési lépéseket](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) a Linux-disztribúcióhoz. Ha nem a SQL Server ingyenes licenccel rendelkező kiadását telepíti, akkor az [Azure](https://azure.microsoft.com/pricing/license-mobility/)-ban SQL Server licenccel és licenchordozhatóság frissítési garancia keretébental is rendelkeznie kell.

1. **Van-e a SQL Server saját licencű (BYOL) linuxos virtuálisgép-lemezképei?**

   Jelenleg nincsenek BYOL linuxos virtuálisgép-lemezképek SQL Server számára. Azonban manuálisan is telepítheti a SQL Servert egy linuxos virtuális gépre az előző kérdésekben tárgyalt módon.

1. **Módosíthatok egy virtuális gépet, hogy a saját SQL Server-licencemet használja, ha az a használatalapú fizetéses katalógus egyik rendszerképéből lett létrehozva?**

   Nem. A fizetések másodpercenkénti licencelését nem lehet a saját licenc használatára váltani. Létre kell hoznia egy új linuxos virtuális gépet, telepítenie SQL Servert, és át kell telepítenie az adatait. A saját licencek létrehozásával kapcsolatos további részletekért tekintse meg az előző kérdést.

## <a name="administration"></a>Felügyelet

1. **Kezelhetem a Linux SQL Server virtuális gépet SQL Server Management Studio (SSMS) használatával?**

   Igen, de a SSMS jelenleg csak Windows-eszköz. A SSMS Linux SQL Server virtuális gépekkel való használatához távolról kell csatlakoznia egy Windows rendszerű gépről. A Linuxon helyileg az új [MSSQL-conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) eszköz számos felügyeleti feladatot képes végrehajtani. Platformfüggetlen adatbázis-kezelő eszköz esetén tekintse meg a következőt: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is).

1. **Eltávolíthatom teljesen az SQL Servert egy SQL-alapú virtuális gépről?**

   Igen, de továbbra is díjat kell fizetnie az SQL-alapú virtuális gép számára a [SQL Server Azure-beli virtuális gépek díjszabási útmutatójában](../../windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json)leírtak szerint. Ha már nincs szüksége az SQL Serverre, üzembe helyezhet egy új virtuális gépet, majd oda migrálhatja az adatokat és az alkalmazásokat. Ezt követően már eltávolíthatja az SQL Servert futtató virtuális gépet.

## <a name="updating-and-patching"></a>Frissítés és javítás

1. **Hogyan frissíteni egy Azure-beli virtuális gépen lévő SQL Server új verziójára/kiadására?**

   Jelenleg nincs helyszíni frissítés az Azure-beli virtuális gépeken futtatott SQL Serverhez. Hozzon létre egy új Azure-beli virtuális gépet a kívánt SQL Server-verzióval vagy -kiadással, majd migrálja az adatbázisait az új kiszolgálóra [a szokásos adatmigrálási eljárásokkal](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Általános

1. **Az Azure-beli virtuális gépeken támogatott magas rendelkezésre állású megoldások SQL Server?**

   Jelenleg nem. Az Always On rendelkezésre állási csoportok és a feladatátvételi fürtszolgáltatás is szükség van egy Linux-alapú fürtözési megoldásra, például a pacemakerre. A SQL Server támogatott Linux-disztribúciói nem támogatják a magas rendelkezésre állású bővítményeket a felhőben.

## <a name="resources"></a>További források

**Linux rendszerű virtuális gépek**:

* [Linux rendszerű virtuális gépen lévő SQL Server áttekintése](sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux rendszerű virtuális gép kiépítése](provision-sql-server-linux-virtual-machine.md)
* [A Linux rendszeren futó SQL Server dokumentációja](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Windows rendszerű virtuális gépek**:

* [Windows rendszerű virtuális gépek SQL Server áttekintése](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows rendszerű virtuális gép kiépítése](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Gyakori kérdések (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)