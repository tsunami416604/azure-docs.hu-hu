---
title: Mi a SQL Server IaaS-ügynök bővítmény?
description: Ez a cikk azt ismerteti, hogy a SQL Server IaaS-ügynök bővítménye Hogyan automatizálható az Azure-beli virtuális gépeken futó SQL Server felügyeleti feladatainak automatizálására. Ilyenek például az automatikus biztonsági mentés, az automatikus javítás, az Azure Key Vault integráció, a licencelés kezelése, a tárolási konfiguráció és az összes SQL Server VM példány központi kezelése.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 7ddc13306f4adb1730169c4811b9d2227dedca33
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632766"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>A felügyelet automatizálása a SQL Server IaaS-ügynök bővítménnyel
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Az SQL Server IaaS-ügynök bővítmény (SqlIaasExtension) az Azure Virtual Machines (VM) SQL Server fut a felügyeleti és adminisztrációs feladatok automatizálásához. 

Ez a cikk áttekintést nyújt a bővítményről. A SQL Server IaaS bővítmény Azure-beli virtuális gépeken való SQL Server való telepítéséhez tekintse meg az [automatikus telepítéssel](sql-agent-extension-automatic-registration-all-vms.md), az [önálló virtuális gépekkel](sql-agent-extension-manually-register-single-vm.md)vagy [a nagy méretű virtuális gépekkel](sql-agent-extension-manually-register-vms-bulk.md)kapcsolatos cikkeket. 

## <a name="overview"></a>Áttekintés

A SQL Server IaaS-ügynök bővítmény számos előnyt biztosít az Azure-beli virtuális gépeken futó SQL Server számára: 

- A **szolgáltatás előnyei**: a bővítmény számos Automation-szolgáltatás előnyeit feloldja, például a portál felügyeletét, a licencek rugalmasságát, az automatikus biztonsági mentést, az automatizált javításokat és egyebeket. A részletekért lásd a jelen cikkben a [szolgáltatásokkal kapcsolatos előnyöket](#feature-benefits) . 

- **Megfelelőség**: a bővítmény egyszerűsített módszert biztosít ahhoz, hogy a Microsoft értesíti a felhasználót arról, hogy a Azure Hybrid Benefit a termék feltételeiben megadott módon van engedélyezve. Ez a folyamat cáfolja az egyes erőforrások licencelési regisztrációs űrlapjainak kezelését.  

- **Ingyenes**: a bővítmény mindhárom kezelhetőségi módban teljesen ingyenes. A bővítményhez nem tartozik további díj, illetve nem módosítható felügyeleti mód. 

- **Egyszerűsített licencek kezelése**: a bővítmény leegyszerűsíti SQL Server a licencek kezelését, és lehetővé teszi SQL Server virtuális Azure Hybrid Benefit gépek gyors azonosítását a [Azure Portal](manage-sql-vm-portal.md), a POWERSHELL vagy az Azure CLI használatával: 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```



   ---


> [!IMPORTANT]
> Az SQL IaaS-ügynök bővítmény olyan adatokat gyűjt az expressz célra, amelyek az ügyfelek számára opcionális előnyöket biztosítanak SQL Server Azure-beli Virtual Machines való használatakor. A Microsoft ezeket az adatszolgáltatásokat nem használja fel a licencelési naplózáshoz az ügyfél előzetes belefoglalása nélkül. További információért tekintse meg a [SQL Server adatvédelmi kiegészítését](/sql/sql-server/sql-server-privacy#non-personal-data) .


## <a name="feature-benefits"></a>Funkciók előnyei 

A SQL Server IaaS-ügynök bővítmény feloldja számos funkció előnyeit a SQL Server VM kezeléséhez. 

A következő táblázat részletezi ezeket az előnyöket: 


| Szolgáltatás | Leírás |
| --- | --- |
| **A portál kezelése** | Feloldja [a felügyeletet a portálon](manage-sql-vm-portal.md), így egyetlen helyen tekintheti meg az összes SQL Server virtuális gépet, és így közvetlenül a portálról engedélyezheti és letilthatja az SQL-specifikus szolgáltatásokat. 
| **Automatikus biztonsági mentés** |Automatizálja a biztonsági mentések ütemezését az összes adatbázishoz az alapértelmezett példányhoz, vagy a virtuális gépen a SQL Server [megfelelően telepített](frequently-asked-questions-faq.md#administration) példányát. További információ: [SQL Server automatikus biztonsági mentése az Azure Virtual Machines szolgáltatásban (Resource Manager)](automated-backup-sql-2014.md). |
| **Automatikus javítás** |Beállít egy karbantartási időszakot, amely alatt a virtuális gép fontos Windows-és SQL Server biztonsági frissítéseit elvégezheti, így elkerülhető a frissítések végrehajtása a számítási feladatok csúcsidőben. További információ: [SQL Server automatikus javítása az Azure Virtual Machines szolgáltatásban (Resource Manager)](automated-patching.md). |
| **Azure Key Vault integráció** |Lehetővé teszi Azure Key Vault automatikus telepítését és konfigurálását a SQL Server VM. További információ: [Azure Key Vault integrációjának konfigurálása az Azure-beli SQL Serverhoz Virtual Machines (Resource Manager)](azure-key-vault-integration-configure.md). |
| **Rugalmas licencelés** | Az utólagos elszámolású licencelési modellre való zökkenőmentes áttéréssel, a saját licencek (más néven a Azure Hybrid Benefit) és a back-as-you-go licencelési modelljének [zavartalan átváltásával](licensing-model-azure-hybrid-benefit-ahb-change.md) megtakaríthatja a költségeket. | 
| **Rugalmas verzió/kiadás** | Ha úgy dönt, hogy módosítja a SQL Server [verzióját](change-sql-server-version.md) vagy [kiadását](change-sql-server-edition.md) , akkor a teljes SQL Server VM újbóli üzembe helyezése nélkül frissítheti a Azure Portalon belüli metaadatokat.  | 


## <a name="management-modes"></a>Felügyeleti módok

Az SQL IaaS-bővítményt három felügyeleti módban is regisztrálhatja: 

- A **könnyű** mód a bináris fájlokat a virtuális gépre másolja, de nem telepíti az ügynököt, és nem indítja újra a SQL Server szolgáltatást. A könnyű mód csak a SQL Server licencek típusának és kiadásának módosítását támogatja, és korlátozott portál-felügyeletet biztosít. Ezzel a beállítással SQL Server virtuális gépek több példánnyal, vagy a feladatátvevő fürt példányaiban (...) részt vesznek. A könnyű mód az alapértelmezett felügyeleti mód az [automatikus regisztrációs](sql-agent-extension-automatic-registration-all-vms.md) funkció használatakor, vagy ha nincs megadva felügyeleti típus a manuális regisztráció során. A könnyű mód használata esetén nincs hatással a memóriára vagy a CPU-ra, és nincs kapcsolódó díj. Javasoljuk, hogy először regisztrálja a SQL Server VMt, majd egy ütemezett karbantartási időszak alatt frissítsen a teljes módba. 

- A **teljes** mód telepíti az SQL IaaS-ügynököt a virtuális gépre az összes funkció biztosításához, de a SQL Server szolgáltatás és a rendszergazda engedélyeinek újraindítását igényli. A SQL Server VM egyetlen példánnyal való felügyeletére használható. A teljes mód két Windows-szolgáltatást telepít, amelyek minimális hatással vannak a memóriára és a CPU-ra – ezeket a Feladatkezelő segítségével figyelheti. A teljes kezelhetőségi mód használata nem jár együtt. 

- A nem **ügynök** mód a Windows Server 2008 rendszeren telepített SQL Server 2008 és SQL Server 2008 R2 rendszerre van kijelölve. A nem befolyásolja a memóriát vagy a CPU-t a nem ügynök mód használata esetén. A nem ügynöki kezelhetőségi mód használata esetén a SQL Server nem indul újra, és az ügynök nincs telepítve a virtuális gépre. 

A SQL Server IaaS-ügynök aktuális módja a Azure PowerShell használatával tekinthető meg: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="installation"></a>Telepítés

Regisztrálja a SQL Server VMt a SQL Server IaaS-ügynök bővítménnyel, hogy az előfizetésen belül hozza létre az SQL-alapú **virtuális gép** _erőforrását_ , amely a virtuális gép erőforrásának _külön_ erőforrása. Ha törli a SQL Server VM regisztrációját a bővítményből, a rendszer eltávolítja az SQL-alapú **virtuális gép** _erőforrását_ , de nem dobja el a tényleges virtuális gépet.

SQL Server VM Azure Marketplace-rendszerképnek a Azure Portal használatával történő üzembe helyezése automatikusan regisztrálja a SQL Server VM a kiterjesztéssel. Ha azonban úgy dönt, hogy egy Azure-beli virtuális gépen telepíti a SQL Servert, vagy egyéni virtuális MEREVLEMEZről szeretne üzembe helyezni egy Azure-beli virtuális gépet, akkor a szolgáltatás előnyeinek feloldásához regisztrálnia kell a SQL Server VMt az SQL IaaS bővítménnyel. 

A bővítmény egyszerűsített módban való regisztrálása a bináris fájlokat másolja, de nem telepíti az ügynököt a virtuális gépre. Az ügynököt a rendszer a virtuális gépre telepíti, ha a bővítmény teljes felügyeleti módra van frissítve. 

A bővítmény háromféleképpen regisztrálhat: 
- [Az előfizetésben lévő összes jelenlegi és jövőbeli virtuális gép esetében automatikusan](sql-agent-extension-automatic-registration-all-vms.md)
- [Manuálisan egyetlen virtuális géphez](sql-agent-extension-manually-register-single-vm.md)
- [Több nagy méretű virtuális gép esetén manuálisan](sql-agent-extension-manually-register-vms-bulk.md)

### <a name="named-instance-support"></a>Megnevezett példányok támogatása

A SQL Server IaaS-ügynök bővítmény a SQL Server megnevezett példányával működik, ha a virtuális gépen csak az egyetlen SQL Server példány érhető el. A bővítmény nem telepíthető olyan virtuális gépekre, amelyeken több névvel ellátott SQL Server példány található, ha a virtuális gépen nincs alapértelmezett példány. 

SQL Server elnevezett példányának használatához helyezzen üzembe egy Azure-beli virtuális gépet, telepítsen egy nevű SQL Server példányt, majd regisztrálja az [SQL IaaS bővítménnyel](sql-agent-extension-manually-register-single-vm.md).

Azt is megteheti, hogy egy megnevezett példányt használ egy Azure Marketplace SQL Server-lemezképpel, kövesse az alábbi lépéseket: 

   1. SQL Server VM üzembe helyezése az Azure piactéren. 
   1. Szüntesse meg a SQL Server VM [regisztrációját](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) az SQL IaaS-ügynök bővítménnyel. 
   1. Távolítsa el SQL Server teljesen a SQL Server VMon belül.
   1. Telepítse a SQL Servert egy megnevezett példánnyal a SQL Server VMon belül. 
   1. [Regisztrálja a virtuális gépet az SQL IaaS-ügynök bővítménnyel](sql-agent-extension-manually-register-single-vm.md#register-with-extension). 

## <a name="verify-status-of-extension"></a>A bővítmény állapotának ellenőrzése

A bővítmény állapotának vizsgálatához használja a Azure Portal vagy a Azure PowerShell. 

### <a name="azure-portal"></a>Azure Portal

Ellenőrizze, hogy a bővítmény telepítve van-e a Azure Portal. 

Válassza a **minden beállítás** lehetőséget a virtuális gép ablaktáblán, majd válassza a **bővítmények** lehetőséget. Ekkor meg kell jelennie a felsorolt **SqlIaasExtension** -bővítménynek.

![A SQL Server IaaS-ügynök bővítmény állapota a Azure Portal](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


### <a name="azure-powershell"></a>Azure PowerShell

A **Get-AzVMSqlServerExtension** Azure PowerShell parancsmagot is használhatja:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Az előző parancs megerősíti, hogy az ügynök telepítve van, és általános állapotinformációkat biztosít. Az automatikus biztonsági mentéssel és javítással kapcsolatos információkat az alábbi parancsokkal érheti el:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```


## <a name="limitations"></a>Korlátozások

Az SQL IaaS-ügynök bővítménye csak a következőket támogatja: 

- SQL Server a Azure Resource Manager üzembe helyezett virtuális gépeket. SQL Server a klasszikus modellen keresztül üzembe helyezett virtuális gépek nem támogatottak. 
- SQL Server a nyilvános vagy Azure Government felhőbe telepített virtuális gépeket. A többi magán-vagy kormányzati felhőbe való üzembe helyezés nem támogatott. 


## <a name="frequently-asked-questions"></a>Gyakori kérdések 

**Regisztrálhatom a SQL Server VM az Azure Marketplace-en egy SQL Server rendszerképből kiépítve?**

Nem. A Microsoft automatikusan regisztrálja az Azure Marketplace-en SQL Server rendszerképből kiépített virtuális gépeket. A bővítményhez való regisztráció csak akkor szükséges, ha a virtuális gép *nem* lett kiépítve az Azure Marketplace SQL Server rendszerképeiből, és a SQL Server önálló telepítése történt.

**Elérhető az SQL IaaS-ügynök bővítmény az összes ügyfél számára?** 

Igen. Az ügyfeleknek regisztrálniuk kell a SQL Server virtuális gépeket a kiterjesztéssel, ha nem használnak SQL Server rendszerképet az Azure Marketplace-ről, hanem önállóan telepített SQL Server, vagy ha az egyéni VHD-t hozták. Az összes típusú előfizetés (Direct, Nagyvállalati Szerződés és Cloud Solution Provider) tulajdonában lévő virtuális gépek regisztrálhatnak az SQL IaaS-ügynök bővítménnyel.

**Mi az alapértelmezett felügyeleti mód az SQL IaaS-ügynök bővítménnyel való regisztráláskor?**

Az SQL IaaS-ügynök bővítménnyel való regisztráláskor az alapértelmezett felügyeleti mód a *kis*-és nagyméretű. Ha a SQL Server felügyeleti tulajdonság nincs beállítva a bővítményhez való regisztráláskor, a mód egyszerűként lesz beállítva, és a SQL Server szolgáltatás nem fog újraindulni. Javasoljuk, hogy először az SQL IaaS-ügynök bővítménnyel regisztráljon, és a karbantartási időszak alatt a teljes verzióra frissítsen. Hasonlóképpen, az [automatikus regisztrálási funkció](sql-agent-extension-automatic-registration-all-vms.md)használata esetén az alapértelmezett felügyelet is egyszerű.

**Milyen előfeltételeket kell regisztrálni az SQL IaaS-ügynök bővítménnyel?**

Az SQL IaaS-ügynök bővítmény nem rendelkezik előfeltételekkel, mint a virtuális gépre SQL Server telepítve. Vegye figyelembe, hogy ha az SQL IaaS-ügynök bővítménye teljes módban van telepítve, akkor a SQL Server szolgáltatás újraindul, így a karbantartási időszak során ajánlott.

**Regisztrálja az SQL IaaS-ügynök bővítményét, telepítsen ügynököt a virtuális gépre?**

Igen, az SQL IaaS-ügynök bővítménnyel való regisztrálása teljes kezelhetőségi módban egy ügynököt telepít a virtuális gépre. A Lightweight vagy nem ügynök módban való regisztráció nem. 

Ha az SQL IaaS-ügynök bővítményét egyszerűsített módban regisztrálja, a csak az SQL IaaS-ügynök bővítmény *bináris fájljait* másolja át a virtuális gépre, nem telepíti az ügynököt. Ezeket a bináris fájlokat a rendszer az ügynök telepítésére használja, ha a felügyeleti mód teljes verzióra frissül.


**Regisztrálja az SQL IaaS-ügynök bővítményét a virtuális gépen a SQL Server újraindításával?**

Ez a regisztráció során megadott módtól függ. Ha a Lightweight vagy a nem ügynök mód van megadva, akkor a SQL Server szolgáltatás nem indul újra. Azonban a teljes felügyeleti mód megadásával a SQL Server szolgáltatás újraindul. Az automatikus regisztrálási funkció a SQL Server virtuális gépeket egyszerű módban regisztrálja, kivéve, ha a Windows Server verziója 2008, és ebben az esetben a SQL Server VMt a rendszer ügynöki módban regisztrálja. 

**Mi a különbség a könnyű és a nem ügynök közötti felügyeleti üzemmódok között az SQL IaaS-ügynök bővítménnyel való regisztráláskor?** 

A nem ügynök-felügyeleti mód az egyetlen elérhető felügyeleti mód a SQL Server 2008 és SQL Server 2008 R2 rendszerhez Windows Server 2008 rendszeren. A Windows Server minden újabb verziójában a két elérhető kezelhetőségi mód könnyű és teljes. 

A nem ügynök üzemmódhoz az ügyfélnek be kell állítania SQL Server Version és Edition tulajdonságokat. A könnyű mód lekérdezi a virtuális gépet a SQL Server példány verziójának és kiadásának megkereséséhez.

**Regisztrálhatok az SQL IaaS-ügynök bővítménnyel a SQL Server licenc típusának megadása nélkül?**

Nem. Az SQL Server licenc típusa nem választható tulajdonság az SQL IaaS-ügynök bővítménnyel való regisztráláskor. Ha az SQL IaaS-ügynök bővítményt az összes kezelhetőségi módban (nem ügynök, könnyűsúly és teljes) regisztrálja, akkor a SQL Server Azure Hybrid Benefit licencfeltételeket kell beállítania. Ha a telepített SQL Server ingyenes verzióit (például a fejlesztői vagy a próbaverziós verziót) használja, regisztrálnia kell az utólagos elszámolású licenceléssel. Azure Hybrid Benefit csak SQL Server, például a nagyvállalati és a standard kiadások fizetős verzióihoz érhető el.

**Frissíthetem a SQL Server IaaS bővítményt a nem ügynök módból a teljes módba?**

Nem. A kezelhetőségi mód teljes vagy egyszerű frissítése nem érhető el a nem ügynök módban. Ez a Windows Server 2008 technikai korlátozása. Először a Windows Server 2008 R2 vagy újabb verzióra kell frissítenie az operációs rendszert, majd frissítenie kell a teljes felügyeleti módra. 

**Frissíthetem a SQL Server IaaS-bővítményt az egyszerűsített módból a teljes módba?**

Igen. A kezelhetőségi mód könnyűről teljesre történő frissítése Azure PowerShell vagy a Azure Portal használatával támogatott. Ez elindítja a SQL Server szolgáltatás újraindítását.

**Visszaválthatom a SQL Server IaaS-bővítményt a teljes módból a nem ügynökre vagy az egyszerűsített felügyeleti módra?**

Nem. A SQL Server IaaS-bővítmény kezelhetőségi módjának lefokozása nem támogatott. A kezelhetőségi mód nem állítható vissza a teljes módból a könnyű vagy a nem ügynök módba, és nem lehet leértékelni az egyszerűsített üzemmódból a nem ügynök módba. 

Ha módosítani szeretné a kezelhetőségi módot a teljes kezelhetőséggel, szüntesse meg a SQL Server VM [regisztrációját](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) az SQL IaaS-ügynök bővítményből az SQL-alapú virtuális gép _erőforrásának_ eldobásával, majd a SQL Server VM újbóli regisztrálásával az SQL IaaS-ügynök bővítményével egy másik felügyeleti módban.

**Regisztrálhatok az SQL IaaS-ügynök bővítménnyel a Azure Portal?**

Nem. Az SQL IaaS-ügynök bővítménnyel való regisztráció nem érhető el a Azure Portal. Az SQL IaaS-ügynök bővítménnyel való regisztráció csak az Azure CLI vagy a Azure PowerShell esetén támogatott. 

**Regisztrálhatok egy virtuális gépet az SQL IaaS-ügynök bővítménnyel SQL Server telepítése előtt?**

Nem. A virtuális gépnek rendelkeznie kell legalább egy SQL Server (adatbázismotor) példánnyal, hogy sikeresen regisztrálja az SQL IaaS-ügynök bővítményét. Ha a virtuális gépen nincs SQL Server példány, az új Microsoft. SqlVirtualMachine erőforrás sikertelen állapotba kerül.

**Regisztrálhatok egy virtuális gépet az SQL IaaS-ügynök bővítménnyel, ha több SQL Server példány is van?**

Igen, a virtuális gépen alapértelmezett példány van megadva. Az SQL IaaS-ügynök bővítmény csak egy SQL Server (adatbázismotor) példányt regisztrál. Az SQL IaaS-ügynök bővítmény több példány esetén regisztrálja az alapértelmezett SQL Server példányt.

**Regisztrálhatok egy SQL Server feladatátvevő fürt példányát az SQL IaaS-ügynök bővítménnyel?**

Igen. Az Azure-beli virtuális gépeken SQL Server feladatátvevő fürt példányai az SQL IaaS-ügynök bővítményét egyszerűsített módban lehet regisztrálni. SQL Server feladatátvevő fürt példányai azonban nem frissíthetők teljes kezelhetőségi módba.

**Regisztrálhatom a virtuális gépet az SQL IaaS-ügynök bővítménnyel, ha az Always On rendelkezésre állási csoport konfigurálva van?**

Igen. Ha egy always on rendelkezésre állási csoport konfigurációjában vesz részt, az SQL IaaS-ügynök bővítménnyel nem lehet SQL Server példányt regisztrálni egy Azure-beli virtuális gépen.

**Mennyibe kerül az SQL IaaS-ügynök bővítménnyel való regisztráció, vagy a teljes kezelhetőségi módra való frissítés?**

Nincsenek. Az SQL IaaS-ügynök bővítménnyel való regisztrációhoz és a három kezelhetőségi mód bármelyikéhez nem tartozik díj. A SQL Server VM kezelése a bővítménnyel teljesen ingyenes. 

**Milyen hatással van a teljesítmény a különböző kezelhetőségi módok használatával?**

A nem befolyásolható a nem- *ügynök* és a *könnyű* kezelhetőségi mód használata. Az operációs rendszerre telepített két szolgáltatás *teljes* kezelhetőségi módjának használata minimális hatással van. Ezek a Feladatkezelő segítségével figyelhetők meg, és a beépített Windows-szolgáltatások konzolon láthatók. 

A két szolgáltatás neve:
- `SqlIaaSExtensionQuery` (Megjelenítendő név – `Microsoft SQL Server IaaS Query Service` )
- `SQLIaaSExtension` (Megjelenítendő név – `Microsoft SQL Server IaaS Agent` )

**Hogyan távolítsa el a bővítményt?**

Távolítsa el a bővítményt a SQL Server VM SQL IaaS-ügynök bővítményből való [regisztrációjának](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) törlésével. 

## <a name="next-steps"></a>Következő lépések

A SQL Server IaaS bővítmény Azure-beli virtuális gépeken való SQL Server való telepítéséhez tekintse meg az [automatikus telepítéssel](sql-agent-extension-automatic-registration-all-vms.md), az [önálló virtuális gépekkel](sql-agent-extension-manually-register-single-vm.md)vagy [a nagy méretű virtuális gépekkel](sql-agent-extension-manually-register-vms-bulk.md)kapcsolatos cikkeket.

További információ az Azure Virtual Machines SQL Server futtatásáról: [mi SQL Server az azure Virtual Machines?](sql-server-on-azure-vm-iaas-what-is-overview.md)
