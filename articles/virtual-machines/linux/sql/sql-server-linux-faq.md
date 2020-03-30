---
title: SQL Server Linux linuxos Azure virtuális gépeken – gyakori kérdések | Microsoft dokumentumok
description: Ez a cikk választ ad az SQL Server Linux Azure virtuális gépeken való futtatásával kapcsolatos gyakori kérdésekre.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70082037"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Gyakori kérdések az SQL Server ről Linux Azure virtuális gépeken

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Ez a cikk az [SQL Server Linux Azure virtuális gépeken](sql-server-linux-virtual-machines-overview.md)való futtatásával kapcsolatos leggyakoribb kérdésekre ad választ.

> [!NOTE]
> Ez a cikk a Linux os virtuális gépeken lévő SQL Server-re vonatkozó problémákra összpontosít. Ha az SQL Server rendszert Windows virtuális gépeken futtatja, olvassa el a [Windows gyakori kérdések című témakört.](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>Képek

1. **Milyen SQL Server virtuálisgép-galériaképek érhetők el?**

   Az Azure az SQL Server összes fő kiadásának virtuálisgép-lemezképeit a Linux és a Windows összes kiadásában karbantartja. További részletekért tekintse meg a [Linux virtuálisgép-lemezképek](sql-server-linux-virtual-machines-overview.md#create) és a [Windows virtuálisgép-lemezképek](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)teljes listáját.

1. **Frissülnek a meglévő SQL Server virtuálisgép-gyűjtemény képei?**

   Kéthavonta az SQL Server-lemezképek a virtuálisgép-gyűjteményben frissülnek a legújabb Linux- és Windows-frissítésekkel. Linux os rendszerképek esetén ez tartalmazza a legújabb rendszerfrissítéseket. Windows lemezképek esetén ez magában foglalja a Windows Update szolgáltatásban fontosnak jelöléssel ellátott frissítéseket, beleértve az SQL Server fontos biztonsági frissítéseit és szervizcsomagjait is. Az SQL Server összesítő frissítéseit a Linux és a Windows esetében eltérően kezeli a rendszer. Linux esetén az SQL Server összesítő frissítései is szerepelnek a frissítésben. Jelenleg azonban a Windows virtuális gépek nem frissülnek az SQL Server vagy a Windows Server összesítő frissítéseivel.

1. **Milyen kapcsolódó SQL Server csomagok is telepítve vannak?**

   Az SQL Server Linux virtuális gépeken alapértelmezés szerint telepített SQL Server-csomagok megtekintéséről a Telepített csomagok című témakörben [olvashat.](sql-server-linux-virtual-machines-overview.md#packages)

1. **Eltávolíthatók az SQL Server virtuálisgép-lemezképei a gyűjteményből?**

   Igen. Az Azure főverziónként és kiadásonként csak egy lemezképet tart fenn. Például egy új SQL Server szervizcsomag megjelenésekor az Azure új lemezképet ad a katalógushoz az adott szervizcsomaghoz. Az előző szervizcsomag SQL Server-lemezképét azonnal eltávolítja az Azure Portalról. Azonban továbbra is elérhető a PowerShell-ből való kiépítés a következő három hónapban. Három hónap elteltére az előző szervizcsomag-lemezkép már nem érhető el. Ez az eltávolítási házirend akkor is érvényes, ha az SQL Server-verzió nem támogatott, amikor eléri az életciklusa végét.

## <a name="creation"></a>Létrehozás

1. **Hogyan hozhatok létre Linux Azure virtuális gépet az SQL Server rel?**

   A legegyszerűbb megoldás egy Linux virtuális gép létrehozása, amely tartalmazza az SQL Server. Az Azure-ra való regisztrációról és az SQL virtuális gép portálról való létrehozásáról az [Azure Portalon](provision-sql-server-linux-virtual-machine.md)egy Linux SQL Server virtuális gép kiépítése című témakörben található. Lehetősége van manuálisan telepíteni az SQL Servert egy virtuális gépre egy szabadon licencelt kiadással (Developer vagy Express) vagy egy helyszíni licenc újbóli felhasználásával. Ha saját licencet hoz magával, az [Azure Frissítési Garanciáján keresztül licenchordozhatósa.](https://azure.microsoft.com/pricing/license-mobility)

1. **Miért nem tudok rhel vagy SLES SQL Server virtuális gép kiépítése egy Azure-előfizetés, amely rendelkezik a költési limit?**

   Az RHEL és Az SLES virtuális gépek hez az előfizetéshez nincs költési limit és az előfizetéshez társított ellenőrzött fizetési mód (általában hitelkártya) szükséges. Ha egy RHEL vagy SLES virtuális gépet létesít a költségkeret eltávolítása nélkül, az előfizetés le lesz tiltva, és az összes virtuális gép/szolgáltatás leáll. Ha nem fut be ebben az állapotban, hogy újra engedélyezze az előfizetés [távolítsa el a költési korlátot.](https://account.windowsazure.com/subscriptions) A fennmaradó kreditek visszaállnak az aktuális számlázási ciklusban, de egy RHEL vagy SLES virtuális gép lemezkép felár ellenkezik a hitelkártyával, ha úgy dönt, hogy újraindítja és folytatja a futtatását.

## <a name="licensing"></a>Licencek

1. **Hogyan telepíthetem az SQL Server egy licencelt példányát egy Azure-beli virtuális gépen?**

   Először hozzon létre egy csak Linux operációs rendszerrel csak virtuális gépet. Ezután futtassa az [SQL Server telepítési lépéseit](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) a Linux-disztribúcióhoz. Ha nem telepíti az SQL Server egyik szabadon licencelt kiadását, az Azure Frissítési Garanciáján keresztül is rendelkeznie kell SQL Server licenccel és [licenchordozhatósággal.](https://azure.microsoft.com/pricing/license-mobility/)

1. **Vannak Bring-Your-Own-License (BYOL) Linux virtuálisgép-lemezképek az SQL Server számára?**

   Jelenleg nincsenek BYOL Linux virtuálisgép-lemezképek az SQL Server hez. Az SQL Server t manuálisan azonban telepítheti egy csak Linux os virtuális gépre, ahogy azt az előző kérdések ben tárgyaltuk.

1. **Módosíthatok egy virtuális gépet, hogy a saját SQL Server-licencemet használja, ha az a használatalapú fizetéses katalógus egyik rendszerképéből lett létrehozva?**

   Nem. Nem válthat a másodpercenkénti fizetésről a saját licenc használatára. Létre kell hoznia egy új Linux virtuális gép, telepítse az SQL Server, és telepítse az adatokat. Az előző kérdésben további részleteket talál a saját licencének megvalósításáról.

## <a name="administration"></a>Adminisztráció

1. **Kezelhetek linuxos SQL Server virtuális gépet az SQL Server Management Studio (SSMS) segítségével?**

   Igen, de az SSMS jelenleg csak Windows rendszerű eszköz. Az SSMS Linux SQL Server virtuális gépekkel való használatához távolról kell csatlakoznia egy Windows-gépről. Helyileg Linuxon az új [mssql-conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) eszköz számos felügyeleti feladatot eltud végezni. A platformok közötti adatbázis-kezelő eszközről az [Azure Data Studio (Azure Data Studio) témakörben található.](https://docs.microsoft.com/sql/azure-data-studio/what-is)

1. **Eltávolíthatom teljesen az SQL Servert egy SQL-alapú virtuális gépről?**

   Igen, de továbbra is fizetnie kell az SQL virtuális gépért az [SQL Server Azure-beli virtuális gépek díjszabási útmutatójában leírtak szerint.](../../windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json) Ha már nincs szüksége az SQL Serverre, üzembe helyezhet egy új virtuális gépet, majd oda migrálhatja az adatokat és az alkalmazásokat. Ezt követően már eltávolíthatja az SQL Servert futtató virtuális gépet.

## <a name="updating-and-patching"></a>Frissítés és javítás

1. **Hogyan frissíthetek az SQL Server új verziójára/kiadására egy Azure-beli virtuális gépben?**

   Jelenleg nincs helyszíni frissítés az Azure-beli virtuális gépeken futtatott SQL Serverhez. Hozzon létre egy új Azure-beli virtuális gépet a kívánt SQL Server-verzióval vagy -kiadással, majd migrálja az adatbázisait az új kiszolgálóra [a szokásos adatmigrálási eljárásokkal](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Általános kérdések

1. **Az SQL Server magas rendelkezésre állású megoldásai támogatottak az Azure virtuális gépein?**

   Jelenleg nem. Mindig a rendelkezésre állási csoportok és a feladatátvevő fürtözés egyaránt igényel fürtözési megoldást a Linux, például a Pacemaker. Az SQL Server támogatott Linux-disztribúciói nem támogatják a felhőben rendelkezésre álló magas rendelkezésre állású bővítményeket.

## <a name="resources"></a>Források

**Linux virtuális gépek**:

* [Az SQL Server áttekintése Linux os virtuális gépen](sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux virtuális gép kiépítése](provision-sql-server-linux-virtual-machine.md)
* [SQL Server Linux on dokumentáció](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Windows virtuális gépek**:

* [Az SQL Server áttekintése Windows virtuális gépen](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows VM kiépítése](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Gyakori kérdések (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)