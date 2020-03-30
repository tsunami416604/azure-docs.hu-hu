---
title: SQL Server windowsos virtuális gépeken az Azure-ban – gyakori kérdések | Microsoft dokumentumok
description: Ez a cikk választ ad az SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos gyakori kérdésekre.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 3b73c329c3db54ba78db15ced8e919af4d4a45d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249736"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Windowsos, Azure-beli virtuális gépeken futó SQL Serverrel kapcsolatos gyakori kérdések

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Ez a cikk az [SQL Server Azure-beli Windows virtuális gépeken](https://azure.microsoft.com/services/virtual-machines/sql-server/)való futtatásával kapcsolatos leggyakoribb kérdésekre ad választ.

> [!NOTE]
> Ez a cikk a Windows virtuális gépeken lévő SQL Server rel kapcsolatos problémákra összpontosít. Ha az SQL Server t Linux os virtuális gépeken futtatja, olvassa el a Linux gyIK című [témakört.](../../linux/sql/sql-server-linux-faq.md)

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>Képek

1. **Milyen SQL Server virtuálisgép-galériaképek érhetők el?** 

   Az Azure az SQL Server összes kiadásában üzemelteti a virtuálisgép-lemezképeket a Windows és a Linux összes kiadásában. További információt a [Windows vm-lemezképek](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) és a [Linux virtuálisgép-lemezképek](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create)teljes listájában talál.

1. **Frissülnek a meglévő SQL Server virtuálisgép-gyűjtemény képei?**

   Kéthavonta az SQL Server-lemezképek a virtuálisgép-gyűjteményben frissülnek a legújabb Windows- és Linux-frissítésekkel. Windows lemezképek esetén ez magában foglalja a Windows Update szolgáltatásban fontosnak jelöléssel ellátott frissítéseket, beleértve az SQL Server fontos biztonsági frissítéseit és szervizcsomagjait is. Linux os rendszerképek esetén ez tartalmazza a legújabb rendszerfrissítéseket. Az SQL Server összesítő frissítéseit a Linux és a Windows esetében eltérően kezeli a rendszer. Linux esetén az SQL Server összesítő frissítései is szerepelnek a frissítésben. Jelenleg azonban a Windows virtuális gépek nem frissülnek az SQL Server vagy a Windows Server összesítő frissítéseivel.

1. **Eltávolíthatók az SQL Server virtuálisgép-lemezképei a gyűjteményből?**

   Igen. Az Azure főverziónként és kiadásonként csak egy lemezképet tart fenn. Például egy új SQL Server szervizcsomag megjelenésekor az Azure új lemezképet ad a katalógushoz az adott szervizcsomaghoz. Az előző szervizcsomag SQL Server-lemezképét azonnal eltávolítja az Azure Portalról. Azonban továbbra is elérhető a PowerShell-ből való kiépítés a következő három hónapban. Három hónap elteltére az előző szervizcsomag-lemezkép már nem érhető el. Ez az eltávolítási házirend akkor is érvényes, ha az SQL Server-verzió nem támogatott, amikor eléri az életciklusa végét.


1. **Telepíthető-e az SQL Server régebbi lemezképe, amely nem látható az Azure Portalon?**

   Igen, a PowerShell használatával. Az SQL Server virtuális gépek PowerShell használatával történő központi telepítéséről az SQL Server virtuális gépek kiépítése az [Azure PowerShell használatával című témakörben talál](virtual-machines-windows-ps-sql-create.md)további információt.

1. **Létrehozhatok egy általános Azure SQL Server Piactér-lemezképet az SQL Server virtuális gépemről, és felhasználhatom a virtuális gépek üzembe helyezésére?**

   Igen, de ezután regisztrálnia kell [minden SQL Server virtuális gépet az SQL Server virtuálisgép erőforrás-szolgáltatójával](virtual-machines-windows-sql-register-with-resource-provider.md) az SQL Server virtuális gép kezeléséhez a portálon, valamint olyan funkciókat kell használnia, mint az automatikus javítás és az automatikus biztonsági mentések. Az erőforrás-szolgáltatónál történő regisztrációsorán meg kell adnia az egyes SQL Server virtuális gépek licenctípusát is. 

1. **Használhatom a saját virtuális merevlemezemet az SQL Server virtuális gép telepítéséhez?**

   Igen, de ezután regisztrálnia kell [minden SQL Server virtuális gépet az SQL Server virtuálisgép erőforrás-szolgáltatójával](virtual-machines-windows-sql-register-with-resource-provider.md) az SQL Server virtuális gép kezeléséhez a portálon, valamint olyan funkciókat kell használnia, mint az automatikus javítás és az automatikus biztonsági mentések.

1. **Be lehet-e állítani olyan konfigurációkat, amelyek nem jelennek meg a virtuális gép gyűjteményében (Például Windows 2008 R2 + SQL Server 2012)?**

   Nem. Az SQL Servert tartalmazó virtuálisgép-katalógusok esetén ki kell választania a megadott képek egyikét az Azure Portalon vagy a [PowerShellen](virtual-machines-windows-ps-sql-create.md)keresztül. Azonban lehetősége van windowsos virtuális gép telepítésére és saját telepítésére az SQL Server. Ezután [regisztrálnia kell az SQL Server virtuálisgép-szolgáltatást az SQL Server virtuálisgép-szolgáltatónál](virtual-machines-windows-sql-register-with-resource-provider.md) az SQL Server virtuális gép portálon történő kezeléséhez, valamint olyan funkciók at kell használnia, mint az automatikus javítás és az automatikus biztonsági mentések. 


## <a name="creation"></a>Létrehozás

1. **Hogyan hozhatok létre egy Azure virtuális gépet az SQL Server rel?**

   A legegyszerűbb módszer az SQL Servert tartalmazó virtuális gép létrehozása. Az Azure-ra való regisztrációról és az SQL Server virtuális gép portálról való létrehozásáról az [SQL Server virtuális gép kiépítése az Azure Portalon](virtual-machines-windows-portal-sql-server-provision.md)című témakörben található. Kiválaszthat egy virtuálisgép-lemezképet, amely másodpercenkénti SQL Server-licencelést használ, vagy használhat olyan lemezképet, amely lehetővé teszi saját SQL Server-licenc ének használatát. Lehetősége van manuálisan telepíteni az SQL Servert egy virtuális gépre egy szabadon licencelt kiadással (Developer vagy Express) vagy egy helyszíni licenc újbóli felhasználásával. Győződjön meg arról, hogy [regisztrálja az SQL Server virtuálisgép-szolgáltató az SQL Server virtuális](virtual-machines-windows-sql-register-with-resource-provider.md) gép a portálon, valamint olyan funkciók, mint például az automatikus javítás és az automatikus biztonsági mentések. Ha saját licencet hoz magával, az [Azure Frissítési Garanciáján keresztül licenchordozhatósa.](https://azure.microsoft.com/pricing/license-mobility/) További információkért tekintse meg [az SQL Server Azure virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Hogyan telepíthetem át a helyszíni SQL Server-adatbázisomat a felhőbe?**

   Először hozzon létre egy Azure-beli virtuális gépet egy SQL Server-példánysal. Ezután telepítse át a helyszíni adatbázisokat az adott példányra. Az adatáttelepítési stratégiákról az [SQL Server-adatbázis áttelepítése Az SQL Server kiszolgálóra egy Azure virtuális gépben](virtual-machines-windows-migrate-sql.md)című témakörben található.

## <a name="licensing"></a>Licencek

1. **Hogyan telepíthetem az SQL Server egy licencelt példányát egy Azure-beli virtuális gépen?**

   Ezt háromféleképpen lehet megtenni. Ha Ön nagyvállalati szerződéssel (EA) rendelkező ügyfél, kiépítheti a [licenceket támogató virtuálisgép-lemezképek](virtual-machines-windows-sql-server-iaas-overview.md#BYOL)egyikét, amely et bring-your-own-license (BYOL) néven is ismer. Ha [rendelkezik szoftverbiztosítás,](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)engedélyezheti az [Azure Hybrid Benefit](virtual-machines-windows-sql-ahb.md) egy meglévő pay-as-you-go (PAYG) rendszerkép. Vagy másolhatja az SQL Server telepítési adathordozóját egy Windows Server virtuális gépre, majd telepítheti az SQL Server t a virtuális gépre. Győződjön meg arról, hogy regisztrálja az SQL Server virtuális gép az [erőforrás-szolgáltató](virtual-machines-windows-sql-register-with-resource-provider.md) olyan funkciók, mint a portál kezelése, automatikus biztonsági mentés és automatikus javítás. 

1. **Módosíthatok egy virtuális gépet, hogy a saját SQL Server-licencemet használja, ha az a használatalapú fizetéses katalógus egyik rendszerképéből lett létrehozva?**

   Igen. Az [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)engedélyezésével egyszerűen válthat a felosztó-kiosztó (PAYG) galérialemez-lemezkép között, hogy saját licencét (BYOL) hozza magához.  További információ: [Az SQL Server virtuális gépek licencelési modelljének módosítása.](virtual-machines-windows-sql-ahb.md) Jelenleg ez a lehetőség csak a nyilvános felhőbeli ügyfelek számára érhető el.

1. **A licencelési modell módosítása során az SQL Server le fog állni?**

   Nem. [A licencelési modell módosítása](virtual-machines-windows-sql-ahb.md) nem igényel leállást az SQL Server számára, mivel a módosítás azonnal hatályba lép, és nem igényli a virtuális gép újraindítását. Az SQL Server virtuális gép SQL Server virtuálisgép-erőforrás-szolgáltatóval való regisztrálásához azonban az [SQL IaaS-bővítmény](virtual-machines-windows-sql-server-agent-extension.md) előfeltétele, és az SQL IaaS-bővítmény _teljes_ módban történő telepítése újraindítja az SQL Server szolgáltatást. Mint ilyen, ha az SQL IaaS bővítményt telepíteni kell, telepítse _azt könnyű_ üzemmódban a korlátozott funkcionalitás érdekében, vagy telepítse _teljes_ módban karbantartási időszak alatt. A _könnyű_ módban telepített SQL IaaS-bővítmény bármikor _teljes_ módba frissíthető, de az SQL Server szolgáltatás újraindítását igényli. 
   
1. **Lehet-e váltani a licencelési modell egy KLASSZIKUS modell használatával telepített SQL Server virtuális gépen?**

   Nem. A licencelési modell módosítása nem támogatott egy klasszikus virtuális gép. Áttelepítheti a virtuális gépet az Azure Resource Manager modellbe, és regisztrálhat az SQL Server virtuálisgép-erőforrás-szolgáltatóval. Miután a virtuális gép regisztrálva van az SQL Server virtuálisgép-erőforrás-szolgáltatónál, a licencelési modell módosításai elérhetők lesznek a virtuális gépen.

1. **Használhatom az Azure Portalon több példány kezelésére ugyanazon a virtuális gépen?**

   Nem. A portálkezelés az SQL Server virtuálisgép-erőforrás-szolgáltató által biztosított szolgáltatás, amely az SQL Server IaaS Agent bővítményre támaszkodik. Mint ilyen, ugyanazok a korlátozások vonatkoznak az erőforrás-szolgáltatóra, mint a bővítményre. A portál vagy csak egy alapértelmezett példányt vagy egy elnevezett példányt kezelhet, feltéve, hogy megfelelően van konfigurálva. Ezekről a korlátozásokról további információt az [SQL Server IaaS-ügynökbővítmény című](virtual-machines-windows-sql-server-agent-extension.md)témakörben talál. 

1. **A CSP-előfizetések aktiválhatják az Azure hybrid benefitet?**

   Igen, az Azure Hybrid Benefit a CSP-előfizetésekhez érhető el. A csp-ügyfeleknek először üzembe kell helyezniük egy felosztó-kiosztó lemezképet, majd [módosítaniuk kell a licencelési modellt](virtual-machines-windows-sql-ahb.md) úgy, hogy saját licenccel járjanak.
   
 
1. **Fizetnem kell az SQL Server licenceléséért egy Azure-beli virtuális gépen, ha csak készenlétben tartom vagy feladatátvételre használom?**

   Ha egy készenléti másodlagos rendelkezésre állási csoporthoz vagy feladatátvételi fürtözött példányhoz ingyenes passzív licenccel szeretne rendelkezni, meg kell felelnie a [terméklicencelési feltételekben](https://www.microsoft.com/licensing/product-licensing/products)meghatározott alábbi feltételek mindegyikének:

   1. A [Frissítési Garancia](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)szolgáltatáson keresztül [licenchordozhatósa](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) magát. 
   1. A passzív SQL Server-példány nem szolgál ki SQL Server-adatokat az ügyfeleknek, és nem futtat aktív SQL Server-munkaterheléseket. Csak az elsődleges kiszolgálóval való szinkronizálásra és a passzív adatbázis meleg készenléti állapotban tartására szolgál. Ha adatokat szolgál ki, például jelentéseket az aktív SQL Server-munkaterheléseket futtató ügyfeleknek, vagy a termékfeltételekben meghatározottaktól eltérő munkát végez, akkor fizetős SQL Server-példánynak kell lennie. A másodlagos példányon a következő tevékenység engedélyezett: adatbázis-konzisztencia-ellenőrzések vagy CheckDB, teljes biztonsági mentések, tranzakciónapló-biztonsági mentések és erőforrás-használati adatok figyelése. Az elsődleges és a megfelelő vész-helyreállítási példányt is futtathatja egyidejűleg a vész-helyreállítási tesztelés rövid időszakaiban 90 naponta. 
   1. Az aktív SQL Server-licencre a Frissítési Garancia vonatkozik, és **lehetővé** teszi egy passzív másodlagos SQL Server-példány t, amely legfeljebb ugyanannyi számítási, mint a licencelt aktív kiszolgáló, csak. 
   1. A másodlagos SQL Server virtuális gép az Azure Portalon a [vész-helyreállítási](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) licencet használja.
   
1. **Mi tekinthető passzív példánynak?**

   A passzív SQL Server-példány nem szolgál ki SQL Server-adatokat az ügyfeleknek, és nem futtat aktív SQL Server-munkaterheléseket. Csak az elsődleges kiszolgálóval való szinkronizálásra és a passzív adatbázis meleg készenléti állapotban tartására szolgál. Ha adatokat szolgál ki, például jelentéseket az aktív SQL Server-munkaterheléseket futtató ügyfeleknek, vagy a termékfeltételekben meghatározottaktól eltérő munkát végez, akkor fizetős SQL Server-példánynak kell lennie. A másodlagos példányon a következő tevékenység engedélyezett: adatbázis-konzisztencia-ellenőrzések vagy CheckDB, teljes biztonsági mentések, tranzakciónapló-biztonsági mentések és erőforrás-használati adatok figyelése. Az elsődleges és a megfelelő vész-helyreállítási példányt is futtathatja egyidejűleg a vész-helyreállítási tesztelés rövid időszakaiban 90 naponta.
   

1. **Milyen forgatókönyvek használhatják a Distaster Recovery (DR) előnyeit?**

   A [licencelési útmutató](https://aka.ms/sql2019licenseguide) olyan forgatókönyveket tartalmaz, amelyekben a vész-helyreállítási előnyök et lehet használni. További információért olvassa el a termékhasználati feltételeket, és további információért forduljon licencelési kapcsolattartóihoz vagy fiókkezelőjéhez.

1. **Mely előfizetések támogatják a vész-helyreállítási (DR) előnyt?**

   A Frissítési Garancia egyenértékű előfizetési jogokat fix juttatásként kínáló átfogó programok támogatják a DR-kedvezményt. Ez magában foglalja. de nem korlátozódik a nyílt értékre (OV), az Open Value Subscription (OVS), a Nagyvállalati szerződésre (EA), a nagyvállalati előfizetési szerződésre (EAS) és a kiszolgálói és felhőalapú regisztrációra (SCE). További információért olvassa el a [termékfeltételeket,](https://www.microsoft.com/licensing/product-licensing/products) és további információért forduljon licencelési kapcsolattartóihoz vagy acocunt menedzseréhez. 

   
 ## <a name="resource-provider"></a>Erőforrás-szolgáltató

1. **A virtuális gép regisztrálása az új SQL Server virtuálisgép-erőforrás-szolgáltatóval további költségekkel jár?**

   Nem. Az SQL Server virtuálisgép-erőforrás-szolgáltató csak további kezelhetőséget tesz lehetővé az SQL Server számára az Azure VM-en további költségek nélkül. 

1. **Az SQL Server virtuálisgép-erőforrás-szolgáltató minden ügyfél számára elérhető?**
 
   Igen, mindaddig, amíg az SQL Server virtuális gép telepítve van a nyilvános felhőben az Erőforrás-kezelő modell használatával, és nem a klasszikus modell. Az összes többi ügyfél regisztrálhat az új SQL Server virtuálisgép-erőforrás-szolgáltatóval. Azonban csak a [Frissítési Garancia](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) kedvezménysel rendelkező ügyfelek használhatják saját licencüket az [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) SQL Server virtuális gépeken történő aktiválásával. 

1. **Mi történik az erőforrás-szolgáltatóval (_Microsoft.SqlVirtualMachine)_ erőforrással, ha a virtuális gép erőforrását áthelyezik vagy eldobják?** 

   Amikor a Microsoft.Compute/VirtualMachine erőforrást eldobják vagy áthelyezik, a társított Microsoft.SqlVirtualMachine erőforrás értesítést kap a művelet aszinkron replikálására.

1. **Mi történik a virtuális géppel, ha az erőforrás-szolgáltató (_Microsoft.SqlVirtualMachine_) erőforrás megszakad?**

    A Microsoft.Compute/VirtualMachine erőforrást ez nem érinti, ha a Microsoft.SqlVirtualMachine erőforrást eldobják. A licencelési módosítások azonban alapértelmezés szerint az eredeti képforráshoz fognak visszatérni. 

1. **Regisztrálhat öntelepített SQL Server virtuális gépeket az SQL Server virtuálisgép-erőforrás-szolgáltatónál?**

    Igen. Ha az SQL Servert saját adathordozóról telepítette, és telepítette az SQL IaaS-bővítményt, regisztrálhatja az SQL Server virtuális gépét az erőforrás-szolgáltatónál az SQL IaaS-bővítmény által biztosított felügyeleti előnyök lekérdezéséhez. Azonban nem tudja konvertálni az öntelepített SQL Server virtuális gép pay-as-you-go.


   


## <a name="administration"></a>Adminisztráció

1. **Telepíthetem az SQL Server második példányát ugyanarra a virtuális gépre? Módosíthatom az alapértelmezett példány telepített szolgáltatásait?**

   Igen. Az SQL Server telepítési adathordozója a **C** meghajtó egyik mappájában található. A **telepítő.exe futtatása** ezen a helyen új SQL Server-példányok hozzáadásához vagy az SQL Server egyéb telepített szolgáltatásainak módosításához. Vegye figyelembe, hogy egyes funkciók, például az automatikus biztonsági mentés, az automatikus javítás és az Azure Key Vault-integráció, csak az alapértelmezett példány vagy egy megfelelően konfigurált elnevezett példány ellen működik (lásd: 3. kérdés). 

1. **Eltávolíthatom az SQL Server alapértelmezett példányát?**

   Igen, de bizonyos szempontokat figyelembe kell venni. Először is, az SQL Server hez társított számlázás továbbra is előfordulhat a virtuális gép licencmodelljétől függően. Másodszor, amint az az előző válaszban is szerepel, vannak olyan szolgáltatások, amelyek az [SQL Server IaaS Agent Extension-re támaszkodnak.](virtual-machines-windows-sql-server-agent-extension.md) Ha az alapértelmezett példányt az IaaS-bővítmény eltávolítása nélkül is eltávolítja, a bővítmény továbbra is keresi az alapértelmezett példányt, és eseménynapló-hibákat okozhat. Ezek a hibák a következő két forrásból származnak: **Microsoft SQL Server Credential Management** és Microsoft SQL Server **IaaS Agent**. Az egyik hiba a következőhöz hasonló lehet:

      Hálózattal kapcsolatos vagy példányspecifikus hiba történt az SQL Serverhez való kapcsolódás során. A kiszolgáló nem található vagy nem érhető el.

   Ha úgy dönt, hogy eltávolítja az alapértelmezett példányt, távolítsa el az [SQL Server IaaS Ügynökbővítményt](virtual-machines-windows-sql-server-agent-extension.md) is. 

1. **Használhatom az SQL Server elnevezett példányát az IaaS-kiterjesztéssel?**
   
   Igen, ha az elnevezett példány az egyetlen példány az SQL Server kiszolgálón, és ha az eredeti alapértelmezett [példányt megfelelően távolítják el.](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance) Ha nincs alapértelmezett példány, és egyetlen SQL Server virtuális gépen több elnevezett példány van, az SQL Server IaaS-ügynökbővítmény telepítése sikertelen lesz. 

1. **Teljesen eltávolítható az SQL Server az SQL Server virtuális gépről?**

   Igen, de továbbra is fizetnie kell az SQL Server virtuális gépért az [SQL Server Azure virtuális gépek díjszabási útmutatójában leírtak](virtual-machines-windows-sql-server-pricing-guidance.md)szerint. Ha már nincs szüksége az SQL Serverre, üzembe helyezhet egy új virtuális gépet, majd oda migrálhatja az adatokat és az alkalmazásokat. Ezt követően már eltávolíthatja az SQL Servert futtató virtuális gépet.
   
## <a name="updating-and-patching"></a>Frissítés és javítás

1. **Hogyan válthatok másik SQL Server-verzióra/-kiadásra egy Azure-beli virtuális gépen?**

   Az ügyfelek az SQL Server kívánt verzióját vagy kiadását tartalmazó telepítési adathordozóval módosíthatják az SQL Server verzióját/kiadását. A kiadás módosítása után az Azure Portalon módosítsa a virtuális gép kiadási tulajdonságát, hogy a virtuális gép számlázása a pontos adatokkal történjen. További információt az [SQL Server virtuális gép módosítási kiadásában talál.](virtual-machines-windows-sql-change-edition.md) Az SQL Server különböző verzióiban nincs számlázási különbség, így az SQL Server verziójának módosítása után nincs szükség további műveletre.

1. **Hol kaphatom meg a telepítő adathordozót az SQL Server kiadásának vagy verziójának módosításához?**

   A [szoftverbiztosítást biztosító](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) ügyfelek a [mennyiségi licencelési központtól](https://www.microsoft.com/Licensing/servicecenter/default.aspx)szerezhetik be telepítési adathordozójukat. A szoftvergaranciával nem rendelkező ügyfelek használhatják a kívánt kiadással rendelkező Marketplace SQL Server virtuálisgép-lemezkép telepítőadathordozóját.
   
1. **Hogyan történik a frissítések és a szervizcsomagok alkalmazása az SQL Server virtuális gépeken?**

   A virtuális gépek lehetővé teszik a gazdagép vezérlését, például a frissítések alkalmazási időpontjának és módjának vezérlését. Az operációs rendszer esetében manuálisan alkalmazhatwindows-frissítéseket, vagy engedélyezheti az [Automatikus javítás](virtual-machines-windows-sql-automated-patching.md)nevű ütemezési szolgáltatást. Az Automatikus javítás minden fontosként megjelölt frissítést telepít, így az e kategóriába eső SQL Server-frissítéseket is. Az SQL Server egyéb nem kötelező frissítéseit manuálisan kell telepíteni.

1. **Frissíthetem az SQL Server 2008 / 2008 R2 példányomat, miután regisztráltam az SQL Server virtuálisgép-erőforrás-szolgáltatónál?**

   Igen. Bármely telepítési adathordozó segítségével frissítheti az SQL Server verzióját és kiadását, majd frissítheti az [SQL IaaS bővítmény módot)](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) _ügynökről_ _teljesre._ Ezzel hozzáférést biztosít az SQL IaaS-bővítmény minden előnyéhez, például a portál kezelhetőségéhez, az automatikus biztonsági mentésekhez és az automatikus javításhoz. 

1. **Hogyan kaphatok ingyenes bővített biztonsági frissítéseket az SQL Server 2008 és az SQL Server 2008 R2 példányaihoz?**

   [Ingyenes, kiterjesztett biztonsági frissítéseket](virtual-machines-windows-sql-server-2008-eos-extend-support.md) kaphat, ha az SQL Servert úgy helyezi át, ahogy van egy Azure SQL virtuális gépre. További információt a Támogatási lehetőségek vége című témakörben [talál.](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) 
  
   

## <a name="general"></a>Általános kérdések

1. **Az SQL Server feladatátvételi fürtpéldányai (FCI) támogatottak az Azure virtuális gépeken?**

   Igen. A feladatátvevő fürtpéldányt [prémium szintű fájlmegosztások (PFS)](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) vagy [közvetlen (S2D) tárolóhelyekkel](virtual-machines-windows-portal-sql-create-failover-cluster.md) telepítheti a tárolóalrendszerhez. A prémium szintű fájlmegosztások iOPS-t és átviteli kapacitást biztosítanak, amelyek számos számítási feladat igényeit kielégítik. Az I/O-igényes számítási feladatokhoz fontolja meg a közvetlen, manged prémium vagy ultralemezek en alapuló tárolóhelyek használatát. Azt is megteheti, hogy külső fürtözési vagy tárolási megoldásokat használ az [Azure virtuális gépek SQL Server magas rendelkezésre állású és vész-helyreállítási szolgáltatásában leírtak szerint.](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)

   > [!IMPORTANT]
   > Jelenleg a _teljes_ [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md) nem támogatott az SQL Server FCI az Azure-ban. Azt javasoljuk, hogy távolítsa el a _teljes_ bővítményt az FCI-ben részt vevő virtuális gépekről, és telepítse a bővítményt _könnyű_ módban. Ez a bővítmény olyan szolgáltatásokat támogat, mint például az automatikus biztonsági mentés és javítás, valamint az SQL Server néhány portálszolgáltatása. Ezek a szolgáltatások a _teljes_ ügynök eltávolítása után nem működnek az SQL Server virtuális gépeken.

1. **Mi a különbség az SQL Server virtuális gépek és az SQL Database szolgáltatás között?**

   Fogalmilag az SQL Server azure-beli virtuális gépen való futtatása nem különbözik az SQL Server távoli adatközpontban történő futtatásától. Ezzel szemben az [SQL Database](../../../sql-database/sql-database-technical-overview.md) szolgáltatásként kínál adatbázist. Az SQL Database segítségével nem fér hozzá az adatbázisokat tároló gépekhez. A teljes összehasonlításért olvassa el [a Felhőalapú SQL Server kiválasztása beállítást: Azure SQL (PaaS) Adatbázis vagy SQL Server Azure virtuális gépeken (IaaS).](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

1. **Hogyan telepíthetem az SQL Data-eszközöket az Azure-beli virtuális gépemre?**

    Töltse le és telepítse az SQL Data eszközöket a [Microsoft SQL Server Data Tools – Business Intelligence for Visual Studio 2013 rendszerből.](https://www.microsoft.com/download/details.aspx?id=42313)

1. **Az MSDTC-vel rendelkező elosztott tranzakciók támogatottak az SQL Server virtuális gépeken?**
   
    Igen. A helyi DTC az SQL Server 2016 SP2 és nagyobb rendszerhez támogatott. Az alkalmazásokat azonban tesztelni kell a Mindig a rendelkezésre állási csoportok alkalmazásakor, mivel a feladatátvétel során a repülés közbeni tranzakciók sikertelenek lesznek, és újra meg kell próbálni őket. A fürtözött DTC a Windows Server 2019-től kezdve érhető el. 

## <a name="resources"></a>Források

**Windows virtuális gépek**:

* [Az SQL Server áttekintése Windows virtuális gépeken](virtual-machines-windows-sql-server-iaas-overview.md).
* [SQL Server Windows VM kiépítése](virtual-machines-windows-portal-sql-server-provision.md)
* [Adatbázis áttelepítése az SQL Serverkiszolgálóra Egy Azure virtuális gépen](virtual-machines-windows-migrate-sql.md)
* [Magas rendelkezésre állás és vészhelyreállítás az SQL Server számára az Azure virtuális gépeken](virtual-machines-windows-sql-high-availability-dr.md)
* [Ajánlott eljárások az SQL Server teljesítményének Azure Virtual Machines szolgáltatásbeli növeléséhez](virtual-machines-windows-sql-performance.md)
* [Azure-beli virtuális gépeken futó SQL Server – alkalmazásminták és fejlesztési stratégiák](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux virtuális gépek**:

* [Az SQL Server áttekintése Linux os virtuális gépen](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux virtuális gép kiépítése](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Gyakori kérdések (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server Linux on dokumentáció](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
