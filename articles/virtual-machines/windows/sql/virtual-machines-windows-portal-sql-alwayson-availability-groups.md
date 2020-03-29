---
title: Magas rendelkezésre állás beállítása az Azure Resource Manager virtuális gépeiszámára | Microsoft dokumentumok
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre mindig rendelkezésre állási csoportot az Azure Resource Manager módban az Azure Resource Manager módban.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75374255"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>A Mindig rendelkezésre állási csoportok konfigurálása az Azure virtuális gépein automatikusan: Erőforrás-kezelő

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy SQL Server-rendelkezésre állási csoportot, amely az Azure Resource Manager virtuális gépeket használja. Az oktatóanyag az Azure blades használatával konfigurálja a sablont. Áttekintheti az alapértelmezett beállításokat, írja be a szükséges beállításokat, és frissítse a paneleket a portálon, ahogy végigvezeti ezt az oktatóanyagot.

A teljes oktatóanyag létrehoz egy SQL Server-elérhetőségi csoportot az Azure virtuális gépeken, amelyek a következő elemeket tartalmazzák:

* Több alhálózattal rendelkező virtuális hálózat, beleértve egy előtér- és egy háttér-alhálózatot
* Active Directory-tartománnyal működő tartományvezérlők
* Két virtuális gép, amelyek az SQL Server t futtatják, és a háttérkiszolgálóalhálózatra vannak telepítve, és csatlakoznak az Active Directory-tartományhoz
* Háromcsomópontos feladatátvevő fürt a Csomópont többségi kvórummodellel
* Egy rendelkezésre állási csoport, amely két szinkron véglegesítési replikák egy rendelkezésre állási adatbázis

A következő ábra a teljes megoldást mutatja.

![Tesztlabor architektúra az Azure-beli rendelkezésre állási csoportokhoz](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

A megoldásban szereplő összes erőforrás egyetlen erőforráscsoporthoz tartozik.

Az oktatóanyag megkezdése előtt erősítse meg a következőket:

* Már rendelkezik Azure-fiókkal. Ha még nem rendelkezik ilyensel, [regisztráljon egy próbafiókot.](https://azure.microsoft.com/pricing/free-trial/)
* Már tudja, hogyan használhatja a grafikus felhasználói felületet egy SQL Server virtuális gép üzembe létesítéséhez a virtuálisgép-gyűjteményből. További információ: [Kiépítés egy SQL Server virtuális gép az Azure-ban.](virtual-machines-windows-portal-sql-server-provision.md)
* Már rendelkezik a rendelkezésre állási csoportok alapos ismerete. További információ: [Mindig a rendelkezésre állási csoportok (SQL Server)](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).

> [!NOTE]
> Ha szeretné használni a sharepoint-csoportokkal a rendelkezésre álláscsoportokat, olvassa [el az SQL Server 2012 Mindig rendelkezésre állási csoportjainak konfigurálása a SharePoint 2013-hoz](/SharePoint/administration/configure-an-alwayson-availability-group)című témakört is.
>
>

Ebben az oktatóanyagban használja az Azure Portalt a következőkhöz:

* Válassza a Portál mindig bekapcsolva sablonját.
* Tekintse át a sablon beállításokat, és frissítsen néhány konfigurációs beállítást a környezetben.
* Az Azure figyelése, mert létrehozza a teljes környezetet.
* Csatlakozzon egy tartományvezérlőhöz, majd egy SQL Server t futtató kiszolgálóhoz.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>A fürt kiépítése a gyűjteményből
Az Azure egy galériaképet biztosít a teljes megoldáshoz. A sablon megkeresése:

1. Jelentkezzen be az Azure Portalra a fiókjával.
2. Az Azure Portalon kattintson az **Erőforrás létrehozása** elemre az **Új** ablaktábla megnyitásához.
3. Az **Új** ablaktáblán keresse meg az **AlwaysOn (AlwaysOn)** elemet.
   ![AlwaysOn sablon keresése](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. A keresési eredmények között keresse meg az **SQL Server AlwaysOn Cluster .**
   ![AlwaysOn sablon](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. A **Telepítési modell kiválasztása**csoportban válassza az **Erőforrás-kezelő**lehetőséget.

### <a name="basics"></a>Alapvető beállítások
Kattintson **az Alapok gombra,** és adja meg a következő beállításokat:

* **A rendszergazdai felhasználónév** olyan felhasználói fiók, amely tartományi rendszergazdai engedélyekkel rendelkezik, és az SQL Server mindkét példányán tagja az SQL Server mindkét példányának. Ehhez az oktatóanyaghoz használja a **DomainAdmin**lehetőséget.
* **A jelszó** a tartományi rendszergazdai fiók jelszava. Használjon összetett jelszót. Erősítse meg a jelszót.
* **Az előfizetés** az az előfizetés, amelyet az Azure számláz a rendelkezésre állási csoport összes üzembe helyezett erőforrásának futtatásához. Ha fiókja több előfizetéssel rendelkezik, megadhat egy másik előfizetést.
* **Erőforráscsoport** annak a csoportnak a neve, amelyhez a sablon által létrehozott összes Azure-erőforrás tartozik. Ebben az oktatóanyagban használja az SQL-HA-RG .For this tutorial, use **SQL-HA-RG**. További információk: [Azure Resource Manager overview](../../../azure-resource-manager/management/overview.md#resource-groups) (Az Azure Resource Manager áttekintése).
* **A hely** az Azure-régió, ahol az oktató i. az oktatóanyag létrehozza az erőforrásokat. Válasszon egy Azure-régiót.

A következő képernyőkép egy elkészült **Basics** panel:

![Alapvető beállítások](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Kattintson az **OK** gombra.

### <a name="domain-and-network-settings"></a>Tartomány- és hálózati beállítások
Ez az Azure-katalógussablon létrehoz egy tartományt és tartományvezérlőket. Emellett létrehoz egy hálózatot és két alhálózatot. A sablon nem tud kiszolgálókat létrehozni meglévő tartományban vagy virtuális hálózatban. A következő lépés a tartomány- és hálózati beállításokat adja meg.

A **Tartomány- és hálózati beállítások** panelen tekintse át a tartomány- és hálózati beállítások előre beállított értékeit:

* **Az erdő gyökértartományának neve** a fürtöt tartalmazó Active Directory-tartomány tartományneve. A bemutató, használja **contoso.com**.
* **A virtuális hálózat neve** az Azure virtuális hálózat hálózati neve. Az oktatóanyaghoz használja az **autohaVNET -**
* **A tartományvezérlő alhálózati neve** a tartományvezérlőt tartalmazó virtuális hálózat egy részének neve. Használja **az alhálózatot-1.** Ez az alhálózat a **10.0.0.0/24**címelőtagot használja.
* **Az SQL Server alhálózati név** a virtuális hálózat azon részének a neve, amely az SQL Server rendszert futtató kiszolgálókat és a tanúsító fájlmegosztást üzemelteti. Használja **az alhálózatot-2.** Ez az alhálózat a **10.0.1.0/26**címelőtagot használja.

Ha többet szeretne megtudni az Azure virtuális hálózatairól, olvassa el a Virtuális hálózatok áttekintése című [témakört.](../../../virtual-network/virtual-networks-overview.md)  

A **Domain és a hálózati beállításoknak** a következő képernyőképen kell kinézniük:

![Tartomány- és hálózati beállítások](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Szükség esetén módosíthatja ezeket az értékeket. Ebben az oktatóanyagban használja az előre beállított értékeket.

Tekintse át a beállításokat, majd kattintson az **OK gombra.**

### <a name="availability-group-settings"></a>Rendelkezésre állási csoport beállításai
A **rendelkezésre állási csoport beállításait**tekintse át az előre beállított értékeket a rendelkezésre állási csoport és a figyelő.

* **Az elérhetőségi csoport neve** az elérhetőségi csoport fürtözött erőforrásneve. Ebben az oktatóanyagban használja **a Contoso-ag .**
* A fürt és a belső terheléselosztó a **rendelkezésre állási csoport figyelőjének nevét** használja. Az SQL Server kiszolgálóhoz csatlakozó ügyfelek ezzel a névvel csatlakozhatnak az adatbázis megfelelő kópiájához. Ebben az oktatóanyagban használja **a Contoso-figyelőt.**
* **A rendelkezésre állási csoport figyelőportja** az SQL Server-figyelő TCP-portját adja meg. Ebben az oktatóanyagban használja az alapértelmezett **portot, az 1433-as portot.**

Szükség esetén módosíthatja ezeket az értékeket. Ebben az oktatóanyagban használja az előre beállított értékeket.  

![rendelkezésre állási csoport beállításai](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Kattintson az **OK** gombra.

### <a name="virtual-machine-size-storage-settings"></a>Virtuális gép mérete, tárolási beállítások
A **virtuális gép mérete, tárolási beállítások**, válasszon egy SQL Server virtuális gép méretét, és tekintse át a többi beállítást.

* **Az SQL Server virtuális gép mérete** az SQL Server t futtató mindkét virtuális gép mérete. Válassza ki a számítási feladatoknak megfelelő virtuálisgép-méretet. Ha ezt a környezetet az oktatóanyaghoz építi, használja a **DS2-t.** Éles számítási feladatokhoz válasszon egy virtuális gép mérete, amely támogatja a számítási feladatokat. Számos éles számítási feladatok **ds4-es** vagy nagyobb. A sablon két ilyen méretű virtuális gépet hoz létre, és mindegyikre telepíti az SQL Server t. További információ: [Méretek a virtuális gépekhez.](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

> [!NOTE]
> Az Azure telepíti az SQL Server Enterprise Edition kiadását. A költség a kiadástól és a virtuális gép méretétől függ. Az aktuális költségekről a [virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)című témakörben talál részletes információt.
>
>

* **A tartományvezérlő virtuálisgép-mérete** a tartományvezérlők virtuális gépmérete. Ehhez a bemutató használata **D2**.
* **A Fájlmegosztás tanúsító virtuálisgép-mérete** a tanúsító fájlmegosztási tanúsító virtuálisgép-mérete. Ehhez az oktatóanyaghoz használja az **A1 -t.**
* **Az SQL Storage-fiók** az SQL Server-adatokat és az operációs rendszer lemezeit tároló tárfiók neve. Ebben a bemutatóban használja **alwaysonsql01**.
* **A tartományvezérlő-tárolófiók** a tartományvezérlők tárfiókjának neve. Ebben az oktatóanyagban használja **az alwaysondc01 programot.**
* **Az SQL Server adatlemezének mérete** TB-ben az SQL Server adatlemezének mérete TB-ben. Adjon meg egy 1 és 4-es számot. Ehhez az oktatóanyaghoz használja az **1-et.**
* **A tárolási optimalizálás** a számítási feladatok típusa alapján határozza meg az SQL Server virtuális gépek adott tárolási konfigurációs beállításait. Ebben a forgatókönyvben az összes SQL Server virtuális gép prémium szintű tárhelyet használ az Azure lemezgazda-gyorsítótárírási beállításával. Ezenkívül az SQL Server beállításait a munkaterheléshez a következő három beállítás egyikével optimalizálhatja:

  * **Az általános munkaterhelés** nem állít be konkrét konfigurációs beállításokat.
  * **Tranzakciós feldolgozási** készletek nyomkövetési jelző 1117 és 1118.
  * **Adattárház** készletek nyomkövetési jelző 1117 és 610.

Ebben az oktatóanyagban használja az **Általános munkaterhelés**t.

![Virtuális gép méretének tárolási beállításai](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Tekintse át a beállításokat, majd kattintson az **OK gombra.**

#### <a name="a-note-about-storage"></a>Megjegyzés a tárolásról
A további optimalizálások az SQL Server adatlemezek méretétől függenek. Az Azure minden egyes terabájtadatlemezhez további 1 TB-os prémium szintű tárhelyet ad hozzá. Ha egy kiszolgálónak legalább 2 TB-ot kell igényelnie, a sablon minden SQL Server virtuális gépen létrehoz egy tárolókészletet. A tárolókészlet a tárolási virtualizáció egy formája, ahol több lemez van beállítva, hogy nagyobb kapacitást, rugalmasságot és teljesítményt biztosítsanak.  A sablon ezután létrehoz egy tárhelyet a tárolókészleten, és egyetlen adatlemezt jelenít meg az operációs rendszernek. A sablon ezt a lemezt jelöli meg az SQL Server adatlemezeként. A sablon az SQL Server tárolókészletét a következő beállításokkal hangolja be:

* A csíkméret a virtuális lemez átkapcsolási beállítása. A tranzakciós számítási feladatok 64 KB-ot használnak. Az adattárház-munkaterhelések 256 KB-ot használnak.
* A rugalmasság egyszerű (nincs rugalmasság).

> [!NOTE]
> Az Azure prémium szintű storage helyileg redundáns, és az adatok három példányát egy régióban tartja, így nincs szükség további rugalmasságra a tárolókészletben.
>
>

* Az oszlopok száma megegyezik a tárolókészletben lévő lemezek számával.

A tárhelyről és a tárolókészletekről további információt a következő témakörökben talál:

* [Tárolóhelyek – áttekintés](https://technet.microsoft.com/library/hh831739.aspx)
* [Windows Server biztonsági másolat és tárolókészletek](https://technet.microsoft.com/library/dn390929.aspx)

Az SQL Server konfigurációs gyakorlati tanácsairól az [Azure virtuális gépeken az SQL Server teljesítményre vonatkozó gyakorlati tanácsai](virtual-machines-windows-sql-performance.md)című témakörben talál további információt.

### <a name="sql-server-settings"></a>SQL Server beállításai
Az **SQL Server beállításainál**tekintse át és módosítsa az SQL Server virtuális gép névelőtagot, az SQL Server verzióját, az SQL Server szolgáltatásfiókot és -jelszót, valamint az SQL automatikus javítási karbantartási ütemezését.

* **Az SQL Server névelőtag** minden SQL Server virtuális gép nevének létrehozására szolgál. Ebben az oktatóanyagban használja az **sqlserver t.** A sablon az SQL Server *sqlserver-0* és *sqlserver-1*virtuális gépeket nevezi meg.
* **Az SQL Server verziója** az SQL Server verziója. Ehhez az oktatóanyaghoz használja az **SQL Server 2014-et.** Választhataz **SQL Server 2012** vagy az **SQL Server 2016 is.**
* **Az SQL Server szolgáltatásfiók felhasználói neve** az SQL Server szolgáltatás tartományfiók-neve. Ebben az oktatóanyagban használja az **sqlservice szolgáltatást.**
* **A jelszó** az SQL Server szolgáltatásfiók jelszava.  Használjon összetett jelszót. Erősítse meg a jelszót.
* **Az SQL automatikus javítás karbantartási ütemezése** azonosítja a hét azon napját, amikor az Azure automatikusan kijavítja az SQL-kiszolgálókat. Ehhez az oktatóanyaghoz írja be a **Vasárnap**típust.
* **Az SQL automatikus javítás karbantartási kezdési órája** az Azure-régió azon ideje, amikor megkezdődik az automatikus javítás.

> [!NOTE]
> Az egyes virtuális gépek javítási ablaka egy órával van elszórón. Egyszerre csak egy virtuális gép van javítva a szolgáltatások megszakadásának megelőzése érdekében.
>
>

![SQL Server beállításai](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Tekintse át a beállításokat, majd kattintson az **OK gombra.**

### <a name="summary"></a>Összefoglalás
Az összefoglaló lapon az Azure érvényesíti a beállításokat. A sablont is letöltheti. Tekintse át az összegzést. Kattintson az **OK** gombra.

### <a name="buy"></a>Vásárlás
Ez a végső panel **tartalmazza a használati feltételeket**, és **adatvédelmi politika**. Tekintse át ezt az információt. Ha készen áll arra, hogy az Azure megkezdje a virtuális gépek és a rendelkezésre állási csoporthoz szükséges összes többi erőforrás létrehozását, kattintson a **Létrehozás gombra.**

Az Azure Portal létrehozza az erőforráscsoportot és az összes erőforrást.

## <a name="monitor-deployment"></a>Az üzembe helyezés figyelése
Figyelje a központi telepítés folyamatát az Azure Portalon. A központi telepítést jelző ikon automatikusan rögzítésre kerül az Azure Portal irányítópultján.

![Azure irányítópult](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Csatlakozás az SQL Serverhez
Az SQL Server új példányai internetkapcsolattal rendelkező IP-címekkel rendelkező virtuális gépeken futnak. Az RDP-t közvetlenül az egyes SQL Server virtuális gépekre is átviheti.

Az RDP-t SQL Server kiszolgálóra kell végrehajtani, kövesse az alábbi lépéseket:

1. Az Azure Portal irányítópultján ellenőrizze, hogy a központi telepítés sikeres volt-e.
2. Kattintson **az Erőforrások gombra.**
3. Az **Erőforrások** panelen kattintson az **sqlserver-0**elemre, amely az SQL Server t futtató virtuális gépek egyikének számítógépneve.
4. Az **sqlserver-0**paneljén kattintson a **Csatlakozás gombra.** A böngésző megkérdezi, hogy meg szeretné-e nyitni vagy menteni a távoli kapcsolatobjektumot. Kattintson az **Open** (Megnyitás) elemre.
5. **A távoli asztali kapcsolat** figyelmeztetheti, hogy a távoli kapcsolat közzétevője nem azonosítható. Kattintson a **Csatlakozás** gombra.
6. A Windows biztonsági szolgálat a hitelesítő adatok megadását kéri az elsődleges tartományvezérlő IP-címéhez való csatlakozáshoz. Kattintson **a Másik fiók használata gombra.** A **Felhasználónév mezőbe**írja be a **contoso\DomainAdmin parancsot.** Ezt a fiókot akkor konfigurálta, amikor beállította a rendszergazda felhasználónevét a sablonban. A sablon konfigurálásakor kiválasztott összetett jelszót használja.
7. **A távoli asztal** figyelmeztetheti, hogy a távoli számítógép hitelesítése nem hitelesíthető a biztonsági tanúsítványával kapcsolatos problémák miatt. A biztonsági tanúsítvány nevét mutatja. Ha követte az oktatóanyagot, a név **sqlserver-0.contoso.com**. Kattintson **az Igen gombra.**

Most már kapcsolódik az RDP-hez az SQL Server virtuális géphez. Megnyithatja az SQL Server Management Studio alkalmazást, csatlakozhat az SQL Server alapértelmezett példányához, és ellenőrizheti, hogy az elérhetőségi csoport be van-e állítva.
