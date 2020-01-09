---
title: Magas rendelkezésre állás beállítása Azure Resource Manager virtuális gépekhez | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre always on rendelkezésre állási csoportot az Azure Virtual Machines szolgáltatással Azure Resource Manager módban.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: d7c88e500886453fbfb53655748ccf7025ab7d3d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75374255"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Always On rendelkezésre állási csoportok konfigurálása az Azure-ban Virtual Machines automatikusan: Resource Manager

Ez az oktatóanyag bemutatja, hogyan hozhat létre olyan SQL Server rendelkezésre állási csoportot, amely Azure Resource Manager virtuális gépeket használ. Az oktatóanyag Azure-lapokat használ a sablonok konfigurálásához. Tekintse át az alapértelmezett beállításokat, írja be a kötelező beállításokat, majd frissítse a paneleket a portálon az oktatóanyag lépésein.

A teljes oktatóanyag egy SQL Server rendelkezésre állási csoportot hoz létre az Azure Virtual Machines, amely a következő elemeket tartalmazza:

* Több alhálózattal rendelkező virtuális hálózat, beleértve a felületet és a háttérbeli alhálózatot is
* Két, Active Directory tartománnyal rendelkező tartományvezérlő
* Két, SQL Server rendszert futtató virtuális gép a háttér-alhálózatra van telepítve, és csatlakoztatva van a Active Directory tartományhoz
* Három csomópontos feladatátvevő fürt a csomópontok többségi Kvórumának modelljével
* Egy rendelkezésre állási adatbázis két szinkron-véglegesítő replikáját tartalmazó rendelkezésre állási csoport

A következő ábra a teljes megoldást mutatja.

![Tesztkörnyezet architektúrája rendelkezésre állási csoportok számára az Azure-ban](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

A megoldás összes erőforrása egyetlen erőforráscsoporthoz tartozik.

Az oktatóanyag elindítása előtt erősítse meg a következőket:

* Már rendelkezik Azure-fiókkal. Ha még nem rendelkezik ilyennel, [regisztráljon egy próbaverziós fiókra](https://azure.microsoft.com/pricing/free-trial/).
* Már tudja, hogyan használhatja a grafikus felhasználói felületet egy SQL Server virtuális gép üzembe helyezéséhez a virtuálisgép-galériából. További információ: [SQL Server virtuális gép üzembe helyezése az Azure](virtual-machines-windows-portal-sql-server-provision.md)-ban.
* Már rendelkezik a rendelkezésre állási csoportok alapos megismerésével. További információ: [Always On rendelkezésre állási csoportok (SQL Server)](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).

> [!NOTE]
> Ha érdekli a rendelkezésre állási csoportok használata a SharePointban, lásd még: [SQL Server 2012 always on rendelkezésre állási csoportok konfigurálása a sharepoint 2013](/SharePoint/administration/configure-an-alwayson-availability-group)-hoz.
>
>

Ebben az oktatóanyagban a Azure Portal a következőt használja:

* Válassza ki az Always On sablont a portálon.
* Tekintse át a sablon beállításait, és frissítse a környezet néhány konfigurációs beállítását.
* Figyelje az Azure-t, mert az a teljes környezetet hozza létre.
* Kapcsolódjon egy tartományvezérlőhöz, majd egy SQL Server rendszert futtató kiszolgálóhoz.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>A fürt kiépítése a gyűjteményből
Az Azure egy katalógus-rendszerképet biztosít a teljes megoldáshoz. A sablon megkeresése:

1. Jelentkezzen be a Azure Portalba a fiók használatával.
2. A Azure Portal kattintson az **erőforrás létrehozása** elemre az **új** ablaktábla megnyitásához.
3. Az **új** ablaktáblán keresse meg a **AlwaysOn**.
   ![AlwaysOn-sablon keresése](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. A keresési eredmények között keresse meg **SQL Server AlwaysOn-fürtöt**.
   ![AlwaysOn-sablon](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. A **telepítési modell kiválasztása**lapon válassza a **Resource Manager**lehetőséget.

### <a name="basics"></a>Alapvető beállítások
Kattintson az **alapismeretek** lehetőségre, és adja meg a következő beállításokat:

* A **rendszergazda felhasználóneve** olyan felhasználói fiók, amely tartományi rendszergazdai engedélyekkel rendelkezik, és a SQL Server mindkét példányán tagja a SQL Server sysadmin rögzített kiszolgálói szerepkörnek. Ebben az oktatóanyagban használja a **rdfe**-t.
* A **jelszó** a tartományi rendszergazdai fiók jelszava. Használjon összetett jelszót. Erősítse meg a jelszót.
* Az **előfizetés** azt az előfizetést adja meg, amelyet az Azure számláz az összes telepített erőforrás futtatásához a rendelkezésre állási csoport számára. Ha a fiókja több előfizetéssel rendelkezik, megadhat egy másik előfizetést.
* Az **erőforráscsoport** annak a csoportnak a neve, amelyhez a sablon által létrehozott összes Azure-erőforrás tartozik. Ebben az oktatóanyagban használja az **SQL-ha-RG-** t. További információk: [Azure Resource Manager overview](../../../azure-resource-manager/management/overview.md#resource-groups) (Az Azure Resource Manager áttekintése).
* A **hely** az az Azure-régió, ahol az oktatóanyag létrehozza az erőforrásokat. Válasszon egy Azure-régiót.

A következő képernyőkép egy befejezett **alapismeretek** panel:

![Alapvető beállítások](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Kattintson az **OK** gombra.

### <a name="domain-and-network-settings"></a>Tartomány-és hálózati beállítások
Ez az Azure Gallery-sablon egy tartomány-és tartományvezérlőt hoz létre. Emellett létrehoz egy hálózatot és két alhálózatot. A sablon nem tud kiszolgálót létrehozni meglévő tartományban vagy virtuális hálózatban. A következő lépés a tartomány-és hálózati beállításokat konfigurálja.

A **tartomány és hálózati beállítások** panelen tekintse át a tartomány és a hálózat beállításainak előre megadott értékeit:

* Az **erdő gyökértartományának neve** a fürtöt futtató Active Directory tartomány tartományneve. Az oktatóanyaghoz használja a **contoso.com**.
* **Virtual Network neve** az Azure-beli virtuális hálózat hálózati neve. Az oktatóanyaghoz használja a **autohaVNET**.
* A tartományvezérlő **alhálózatának neve** a tartományvezérlőt üzemeltető virtuális hálózat egy részének a neve. Használja az **-1 alhálózatot**. Ez az alhálózat a **10.0.0.0/24**előtagot használja.
* **SQL Server alhálózat neve** a virtuális hálózat azon részének a neve, amelyen a SQL Server futtató kiszolgálók és a tanúsító fájlmegosztás található. Használja a **-2 alhálózatot**. Ez az alhálózat a **10.0.1.0/26**előtagot használja.

További információ az Azure-beli virtuális hálózatokról: [Virtual Network Overview (virtuális hálózatok áttekintése](../../../virtual-network/virtual-networks-overview.md)).  

A **tartomány-és hálózati beállításoknak** a következő képernyőképhez hasonlóan kell kinéznie:

![Tartomány-és hálózati beállítások](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Ha szükséges, módosíthatja ezeket az értékeket. Ebben az oktatóanyagban használja az előre beállított értékeket.

Tekintse át a beállításokat, majd kattintson **az OK**gombra.

### <a name="availability-group-settings"></a>Rendelkezésre állási csoport beállításai
A **rendelkezésre állási csoport beállításainál**tekintse át a rendelkezésre állási csoport és a figyelő előre megadott értékeit.

* A rendelkezésre állási **csoport neve** a rendelkezésre állási csoport fürtözött erőforrásának neve. Ebben az oktatóanyagban használja a **contoso-AG-** t.
* A **rendelkezésre állási csoport figyelője nevét** a fürt és a belső terheléselosztó használja. A SQL Serverhoz csatlakozó ügyfelek ezt a nevet használhatják az adatbázis megfelelő replikájának eléréséhez. Ebben az oktatóanyagban használja a **contoso-figyelőt**.
* A **rendelkezésre állási csoport figyelő portja** megadja a SQL Server figyelő TCP-portját. Ebben az oktatóanyagban használja az alapértelmezett **1433**-as portot.

Ha szükséges, módosíthatja ezeket az értékeket. Ebben az oktatóanyagban használja az előre beállított értékeket.  

![rendelkezésre állási csoport beállításai](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Kattintson az **OK** gombra.

### <a name="virtual-machine-size-storage-settings"></a>Virtuális gép mérete, tárolási beállításai
A virtuális gép **mérete, a tárolási beállítások**területen válassza ki a SQL Server virtuálisgép-méretet, és tekintse át a többi beállítást.

* **SQL Server a virtuális gép mérete** a SQL Server futtató virtuális gépek mérete. Válasszon ki egy megfelelő virtuálisgép-méretet a munkaterheléshez. Ha ezt a környezetet készíti elő az oktatóanyaghoz, használja a **DS2**. Éles számítási feladatokhoz válasszon olyan virtuálisgép-méretet, amely képes támogatni a munkaterhelést. Számos éles számítási feladathoz **DS4** vagy nagyobb mennyiség szükséges. A sablon két ilyen méretű virtuális gépet hoz létre, és mindegyikre telepíti a SQL Server. További információ: [virtuális gépek méretei](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Az Azure telepíti SQL Server Enterprise kiadását. A díj a kiadástól és a virtuális gép méretétől függ. Az aktuális költségekkel kapcsolatos részletes információkért lásd a [Virtual Machines díjszabását](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* A tartományvezérlő **virtuális gép mérete** a tartományvezérlők virtuálisgép-mérete. Ebben az oktatóanyagban **D2**-t használunk.
* **Tanúsító fájlmegosztás virtuális gép mérete** a tanúsító fájlmegosztás virtuálisgép-mérete. Ebben az oktatóanyagban használja az **a1**-et.
* Az **SQL Storage-fiók** annak a Storage-fióknak a neve, amely a SQL Server és az operációs rendszer lemezeit tárolja. Ebben az oktatóanyagban használja a **alwaysonsql01**-t.
* A **DC Storage-fiók** a tartományvezérlőkhöz tartozó Storage-fiók neve. Ebben az oktatóanyagban használja a **alwaysondc01**-t.
* A TB **SQL Server adatlemez mérete** a TB-ban SQL Server adatlemez mérete. 1 és 4 közötti számot kell megadni. Ebben az oktatóanyagban használja az **1**.
* A **tárolási optimalizálás** a munkaterhelés típusa alapján beállítja a SQL Server virtuális gépek adott tárolási konfigurációs beállításait. Ebben a forgatókönyvben az összes SQL Server virtuális gép Premium Storage-t használ az Azure Disk Host cache-ben a csak olvasható értékre. Emellett a következő három beállítás egyikének kiválasztásával optimalizálhatja a számítási feladatok SQL Server beállításait:

  * Az **általános munkaterhelések** nem adott konfigurációs beállításokat határoznak meg.
  * A **tranzakciós feldolgozás** beállítja a 1117 és a 1118 nyomkövetési jelzőt.
  * **Adattárház** -készletek nyomkövetési jelzője 1117 és 610.

Ebben az oktatóanyagban használja az **általános számítási feladatokat**.

![VIRTUÁLIS gépek méretének tárolási beállításai](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Tekintse át a beállításokat, majd kattintson **az OK**gombra.

#### <a name="a-note-about-storage"></a>A Storage szolgáltatással kapcsolatos Megjegyzés
A további optimalizációk a SQL Server adatlemezek méretétől függenek. Az Azure minden egyes terabájt adatlemezhez egy további 1 TB-os prémium szintű tárterületet biztosít. Ha egy kiszolgáló 2 TB-ot igényel, a sablon minden SQL Server virtuális gépen létrehoz egy tárolási készletet. A Storage-készlet olyan tárolási virtualizálás, amelyben több lemez van konfigurálva, hogy nagyobb kapacitást, rugalmasságot és teljesítményt biztosítson.  A sablon Ezután létrehoz egy tárolóhelyet a tárolóban, és egyetlen adatlemezt jelenít meg az operációs rendszer számára. A sablon a SQL Server adatlemezként jelöli meg ezt a lemezt. A sablon a következő beállításokkal hangolja le a SQL Server a tárolási készletet:

* A csíkozott méret a virtuális lemez interleave-beállítása. A tranzakciós munkaterhelések 64 KB-ot használnak. Az adattárház-munkaterhelések 256 KB-ot használnak.
* A rugalmasság egyszerű (nincs rugalmasság).

> [!NOTE]
> Az Azure Premium Storage helyileg redundáns, és egy adott régión belül három példányban tárolja az adattárolást, így a tárterület további rugalmassága nem szükséges.
>
>

* Az oszlopok száma megegyezik a tárolási készletben lévő lemezek számával.

A tárolóhelyekkel és a tárolási készletekkel kapcsolatos további információkért lásd:

* [Tárolóhelyek – áttekintés](https://technet.microsoft.com/library/hh831739.aspx)
* [Windows Server biztonsági másolat és Storage-készletek](https://technet.microsoft.com/library/dn390929.aspx)

További információ az SQL Server-konfigurációval kapcsolatos ajánlott eljárásokról: az [Azure Virtual machines SQL Server teljesítményének bevált eljárásai](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>SQL Server beállításai
**SQL Server beállítások**lapon tekintse át és módosítsa a SQL Server virtuálisgép-név előtagját, SQL Server verzióját, SQL Server szolgáltatásfiókot és jelszavát, valamint az SQL automatikus javításának karbantartási ütemtervét.

* **SQL Server név előtagot** használ a SQL Server virtuális gépek nevének létrehozásához. Ebben az oktatóanyagban használja a **SQLServer**-t. A sablon neve a SQL Server Virtual Machines *SQLServer-0* és *SQLServer-1*.
* A **SQL Server verziója** SQL Server verziója. Ehhez az oktatóanyaghoz használja a **SQL Server 2014**-et. **SQL Server 2012** vagy **SQL Server 2016**is választhat.
* **SQL Server a szolgáltatásfiók felhasználóneve** a SQL Server szolgáltatás tartományi fiókjának neve. Ebben az oktatóanyagban használja a **sqlservice**-t.
* A **jelszó** a SQL Server-szolgáltatásfiók jelszava.  Használjon összetett jelszót. Erősítse meg a jelszót.
* Az **SQL automatikus javításának karbantartási ütemterve** meghatározza, hogy az Azure milyen napon automatikusan javítja az SQL-kiszolgálókat. Ehhez az oktatóanyaghoz írja be a következőt: **vasárnap**.
* Az SQL-alapú **Automatikus javítás karbantartási kezdési órája** az az időpont, amikor az Azure-régió az automatikus javítás megkezdésekor elindul.

> [!NOTE]
> Az egyes virtuális gépek javítási ablaka egy órára van felosztva. A szolgáltatások megszakadásának elkerülése érdekében egyszerre csak egy virtuális gép javított.
>
>

![SQL Server beállításai](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Tekintse át a beállításokat, majd kattintson **az OK**gombra.

### <a name="summary"></a>Összefoglalás
Az összefoglalás lapon az Azure ellenőrzi a beállításokat. A sablont letöltheti is. Tekintse át az összegzést. Kattintson az **OK** gombra.

### <a name="buy"></a>Vásárlás
Ez az utolsó panel a **használati feltételeket**és az **adatvédelmi szabályzatot**tartalmazza. Tekintse át ezt az információt. Ha készen áll arra, hogy az Azure létrehozza a virtuális gépeket és az összes szükséges erőforrást a rendelkezésre állási csoport számára, kattintson a **Létrehozás**gombra.

A Azure Portal létrehozza az erőforráscsoportot és az összes erőforrást.

## <a name="monitor-deployment"></a>Az üzembe helyezés figyelése
Figyelje a telepítési folyamatot a Azure Portal. A központi telepítést jelölő ikon automatikusan a Azure Portal irányítópultra van rögzítve.

![Azure-irányítópult](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Csatlakozás az SQL Serverhez
A SQL Server új példányai olyan virtuális gépeken futnak, amelyek internetkapcsolattal rendelkező IP-címmel rendelkeznek. A távoli asztal (RDP) közvetlenül az egyes SQL Server virtuális gépekre is felhasználható.

Ha egy SQL Server RDP-t szeretne, kövesse az alábbi lépéseket:

1. A Azure Portal irányítópulton ellenőrizze, hogy a központi telepítés sikeres volt-e.
2. Kattintson az **erőforrások**elemre.
3. A **Resources (erőforrások** ) panelen kattintson a **SQLServer-0**elemre, amely a SQL Server rendszert futtató virtuális gépek egyikének a számítógépének a neve.
4. A **SQLServer-0**panelen kattintson a **kapcsolat**elemre. A böngésző megkérdezi, hogy szeretné-e megnyitni vagy menteni a távoli kapcsolódási objektumot. Kattintson az **Open** (Megnyitás) elemre.
5. Előfordulhat, hogy a **Távoli asztali kapcsolat** figyelmezteti, hogy a távoli kapcsolat közzétevője nem azonosítható. Kattintson a **Csatlakozás** gombra.
6. A Windows biztonsági szolgáltatás megkéri a hitelesítő adatok megadását az elsődleges tartományvezérlő IP-címéhez való kapcsolódáshoz. Kattintson a **másik fiók használata**elemre. A **Felhasználónév**mezőbe írja be a következőt: **contoso\DomainAdmin**. Ezt a fiókot akkor konfigurálta, amikor a rendszergazda felhasználónevet beállította a sablonban. Használja a sablon beállításakor kiválasztott összetett jelszót.
7. Előfordulhat, hogy a **Távoli asztal** figyelmezteti, hogy a távoli számítógép nem hitelesíthető a biztonsági tanúsítvánnyal kapcsolatos problémák miatt. Megjeleníti a biztonsági tanúsítvány nevét. Ha követte az oktatóanyagot, a név **SQLServer-0.contoso.com**. Kattintson az **Igen** gombra.

Mostantól RDP-kapcsolattal csatlakozik a SQL Server virtuális géphez. Megnyithatja SQL Server Management Studio, csatlakozhat a SQL Server alapértelmezett példányához, és ellenőrizheti, hogy a rendelkezésre állási csoport konfigurálva van-e.
