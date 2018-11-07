---
title: Az Azure Resource Manager virtuális gépek magas rendelkezésre állásának beállítása |} A Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan hozhat létre egy Always On rendelkezésre állási csoport Azure-beli virtuális gépek az Azure Resource Manager módban.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: bddc83d55c8909412f7f935a4324a6f316a82cd7
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238165"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Always On rendelkezésre állási csoportok konfigurálása Azure Virtual Machines szolgáltatásban automatikusan: Resource Manager

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy SQL Server rendelkezésre állási csoport, amely használja az Azure Resource Manager virtuális gépeken. Az oktatóanyagban paneleket az Azure-sablon konfigurálásához. Tekintse át az alapértelmezett beállításokat, írja be a szükséges beállításokat, és frissíteni a paneleket a portálon, mert ebben az oktatóanyagban vezeti végig.

A részletes útmutató létrehoz egy SQL Server rendelkezésre állási csoportot az Azure Virtual machines szolgáltatásban, amely a következő elemeket tartalmazza:

* Egy virtuális hálózatot, amely már több alhálózat működik, beleértve egy előtér- és a egy háttérbeli alhálózatot
* Két tartományvezérlőt, amelyek az Active Directory-tartomány
* Két, futtassa az SQL Server és az üzembe helyezett a backend alhálózathoz és Active Directory-tartományhoz csatlakoztatott virtuális gépek
* Egy három csomópontos feladatátvevő fürt csomóponttöbbség kvórum modell
* Egy rendelkezésre állási adatbázis két szinkron véglegesítésű másodpéldányt tartalmazó rendelkezésre állási csoport

Az alábbi ábrán a kész megoldás jelöli.

![Tesztkörnyezet felépítése az Azure-beli rendelkezésre állási csoportok számára](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Ez a megoldás összes erőforrása egyetlen erőforráscsoportot tartozik.

Ebben az oktatóanyagban a Kezdés előtt ellenőrizze az alábbiakat:

* Már rendelkezik Azure-fiókkal. Ha nem rendelkezik ilyennel, [regisztrálhat a próbafiókra](https://azure.microsoft.com/pricing/free-trial/).
* Már ismeri a grafikus felhasználói felület használatával a virtuálisgép-katalógus az SQL Server virtuális gép kiépítése. További információkért lásd: [az Azure-ban az SQL Server virtuális gép üzembe helyezésének](virtual-machines-windows-portal-sql-server-provision.md).
* Már van egy rendelkezésre állási csoportjainak alapos ismerete. További információkért lásd: [Always On rendelkezésre állási csoportok (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Ha érdekli a rendelkezésre állási csoportok használata a SharePoint, [konfigurálása az SQL Server 2012 AlwaysOn rendelkezésre állási csoportok a SharePoint 2013-hoz készült](https://technet.microsoft.com/library/jj715261.aspx).
>
>

Ebben az oktatóanyagban használja az Azure Portalon:

* Válassza az Always On sablont a portálról.
* Tekintse át a sablon beállításokat, és a környezet néhány konfigurációs beállításainak frissítése.
* Az Azure figyelése, mert a teljes környezetet hoz létre.
* Csatlakozzon egy olyan tartományvezérlőre, és a egy SQL Servert futtató kiszolgáló.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>A galériából a fürt üzembe helyezése
Az Azure biztosít a katalógus-lemezkép a teljes megoldás. Keresse meg a sablon:

1. Jelentkezzen be a fiókjával az Azure Portalon.
2. Az Azure Portalon kattintson a **erőforrás létrehozása** megnyitásához a **új** ablaktáblán.
3. Az a **új** ablaktáblán, és keressen **AlwaysOn**.
   ![AlwaysOn sablon keresése](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Keresse meg a keresési eredmények **SQL Server AlwaysOn fürt**.
   ![AlwaysOn-sablon](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. A **telepítési modell kiválasztása**, válassza a **Resource Manager**.

### <a name="basics"></a>Alapvető beállítások
Kattintson a **alapjai** és a következő beállításokat:

* **Rendszergazdai felhasználónév** egy tartományi rendszergazdai engedélyekkel rendelkező felhasználói fiók és az SQL Server SysAdmin (rendszergazda) rögzített kiszolgálói szerepkör az SQL Server-példány is tagja. A jelen oktatóanyag esetében használja **tartományi rendszergazdai**.
* **Jelszó** tartományi rendszergazdai fiók jelszava. Egy összetett jelszót használja. Erősítse meg a jelszót.
* **Előfizetés** az előfizetésre van szükség, hogy a rendelkezésre állási csoport összes üzembe helyezett erőforrások futtatásához az Azure számlák. Ha a fiók több előfizetéssel rendelkezik, megadhat egy másik előfizetést.
* **Erőforráscsoport** a neve, a csoport, amely az összes Azure-sablon által létrehozott erőforrások tartoznak. A jelen oktatóanyag esetében használja **SQL-magas rendelkezésre ÁLLÁS-RG**. További információk: [Azure Resource Manager overview](../../../azure-resource-manager/resource-group-overview.md#resource-groups) (Az Azure Resource Manager áttekintése).
* **Hely** , az oktatóanyag az erőforrásokat hozza létre az Azure-régióban van. Válasszon egy Azure-régióban.

Az alábbi képernyőképen egy befejezett **alapjai** panelen:

![Alapvető beállítások](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Kattintson az **OK** gombra.

### <a name="domain-and-network-settings"></a>Tartomány és a hálózati beállítások
Az Azure-katalógus sablont hoz létre, a tartomány és a tartományvezérlők. A hálózat és két alhálózatot is létrehoz. A sablon nem hozható létre a kiszolgálók egy meglévő tartomány vagy a virtuális hálózat. A következő lépésben a tartományi és hálózati beállítások konfigurálása.

Az a **tartományi és hálózati beállításainál** panelen tekintse át az előre definiált értékek a tartományhoz, és a hálózati beállítások:

* **Erdő gyökértartományának neve** a tartománynév az Active Directory-tartományban, amelyen a fürt. Az oktatóanyaghoz használja **contoso.com**.
* **Virtuális hálózat neve** az Azure virtuális hálózat hálózati neve. Az oktatóanyaghoz használja **autohaVNET**.
* **Tartományvezérlő alhálózat neve** egy részét a tartományvezérlőt üzemeltető virtuális hálózat neve. Használat **subnet-1**. Ez az alhálózat használja címelőtag **10.0.0.0/24**.
* **Az SQL Server-alhálózat neve** egy része a virtuális hálózat, amelyen a kiszolgálókat, hogy futtassa az SQL Server- és a tanúsító fájlmegosztás neve. Használat **alhálózat-2**. Ez az alhálózat használja címelőtag **10.0.1.0/26**.

Azure-beli virtuális hálózatokkal kapcsolatos további információkért lásd: [virtuális hálózatok áttekintése](../../../virtual-network/virtual-networks-overview.md).  

A **tartományi és hálózati beállításainál** a következő képernyőképhez hasonlóan kell kinéznie:

![Tartomány és a hálózati beállítások](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Szükség esetén módosíthatja ezeket az értékeket. A jelen oktatóanyag esetében használja az előre megadott értékeket.

Tekintse át a beállításokat, és kattintson **OK**.

### <a name="availability-group-settings"></a>Rendelkezésre állási csoport beállításai
A **rendelkezésre állási csoport beállításait**, tekintse át a rendelkezésre állási csoport és a figyelő az előre beállított értékeket.

* **Rendelkezésre állási csoport nevének** a rendelkezésre állási csoporthoz tartozó fürtözött erőforrás neve. A jelen oktatóanyag esetében használja **Contoso-ag**.
* **Rendelkezésre állási csoport figyelőjének nevét** a fürt és a belső terheléselosztó által használt. Csatlakozás az SQL Serverhez ügyfelek használhatják ezt a nevet a megfelelő replika adatbázis csatlakozni. A jelen oktatóanyag esetében használja **Contoso-figyelő**.
* **Rendelkezésre állási csoport figyelőjének portszámára** adja meg a TCP-portot az SQL Server-figyelő. A jelen oktatóanyag esetében használja az alapértelmezett port **1433-as**.

Szükség esetén módosíthatja ezeket az értékeket. A jelen oktatóanyag esetében használja az előre megadott értékeket.  

![rendelkezésre állási csoport beállításai](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Kattintson az **OK** gombra.

### <a name="virtual-machine-size-storage-settings"></a>Virtuális gép méretét, a tárolási beállítások
A **Virtuálisgép-méretet, a tárolási beállítások**SQL Server virtuálisgép-méretet válassza, majd tekintse át a többi beállítást.

* **Az SQL Server virtuális gép mérete** mindkét SQL Server rendszerű virtuális gépek mérete. Válasszon egy megfelelő virtuálisgép-méretet a számítási feladatok számára. Ebben a környezetben az oktatóanyagban hoz létre, ha **DS2**. Az éles számítási feladatokhoz válassza ki a virtuális gép méretét a számítási feladatok támogatására képes. Számos éles számítási feladathoz szükséges **DS4** vagy nagyobb. A sablon ekkora két virtuális gépet hoz létre, és telepíti az SQL Servert is. További információkért lásd: [virtuális gépek méretei](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Az Azure Enterprise Edition, SQL Server telepítése. A költségek az edition és a virtuális gép mérete függ. Az aktuális költségekkel kapcsolatos részletes információkért lásd: [virtual machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Domain controller virtuálisgép-méret** a tartományvezérlők virtuális gép méretét. Ez az oktatóanyag használatra **D2**.
* **A fájl tanúsító fájlmegosztást a virtuális gép mérete** van a virtuális gép mérete a tanúsító fájlmegosztás. A jelen oktatóanyag esetében használja **A1**.
* **SQL-tárfiók** neve, a storage-fiók, amely az SQL Server-adatokat és operációsrendszer-lemezek tárol. A jelen oktatóanyag esetében használja **alwaysonsql01**.
* **Tartományvezérlő tárfiók** a tartományvezérlők a tárfiók neve. A jelen oktatóanyag esetében használja **alwaysondc01**.
* **SQL Server-adatok lemezméret** TB-os mérete (TB) az SQL Server adatlemez van. Adjon meg egy 1 és 4 közötti szám. A jelen oktatóanyag esetében használja **1**.
* **Tárolási optimalizálása** megadja a meghatározott tároló konfigurációs beállításokat az SQL Server virtuális gépek a számítási feladat típusától függően. Ebben a forgatókönyvben minden SQL Server virtuális gépek premium storage használata az Azure-lemez gazdagép cache csak olvasható. Ezenkívül optimalizálhatja a számítási feladathoz tartozó SQL Server-beállítások ezen három beállítás közül választva:

  * **Általános számítási feladatok** megadja a nem meghatározott konfigurációs beállításokat.
  * **Tranzakciós feldolgozást** csoportok nyomkövetési jelző 1117 és 1118.
  * **Az adattárházak** csoportok nyomkövetési jelző 1117 és 610.

A jelen oktatóanyag esetében használja **általános számítási feladatok**.

![Virtuális gép mérete tárolási beállítások](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Tekintse át a beállításokat, és kattintson **OK**.

#### <a name="a-note-about-storage"></a>A storage szolgáltatással kapcsolatos megjegyzés
További optimalizálást az SQL Server adatlemezek méretétől függ. Minden adatlemez terabájt az Azure hozzáad egy további 1 TB prémium szintű storage. Ha egy kiszolgáló 2 TB-os vagy több, a sablon létrehoz egy tárolókészletet minden egyes SQL Server virtuális gépen. A tárolókészlet olyan tárhely-virtualizálás egy formája, ahol több lemezek vannak konfigurálva, magasabb szintű kapacitás, rugalmasságot és teljesítményt biztosít.  Majd a sablon létrehoz egy tárolóhelyet a tárolókészletben, és megadja az operációs rendszer egyetlen adatlemez. A sablon jelöli meg ezt a lemezt az adatlemez SQL Serverhez. A sablon a tárolókészlet hangolja az SQL Server a következő beállításokkal:

* STRIPE a szektorszórás beállítását a virtuális lemez mérete. Tranzakciós munkaterhelések kezelésére használja a 64 KB-os. Adatraktározás számítási 256 KB-os használja.
* Rugalmasság az egyszerű (rugalmasság nélküli).

> [!NOTE]
> Az Azure premium storage a helyileg redundáns, és megőrzi az adatok három másolatát egy adott régión belül, így nem szükséges további rugalmasságot, a tárolókészletben.
>
>

* Oszlopok száma egyenlő a tárolókészletben lévő lemezek számát.

Tárterület és a tárolókészletek kapcsolatos további információkért lásd:

* [Tárolóhelyek – áttekintés](https://technet.microsoft.com/library/hh831739.aspx)
* [A Windows Server biztonsági másolat és Tárolókészletek](https://technet.microsoft.com/library/dn390929.aspx)

Az SQL Server configuration ajánlott eljárásokkal kapcsolatos további információkért lásd: [teljesítménnyel kapcsolatos ajánlott eljárások az SQL Server Azure-beli virtuális gépeken](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>SQL Server beállításai
A **SQL Server-beállítások**, tekintse át, és az SQL Server virtuális gépnév előtagja, SQL Server-verzió, az SQL Server-szolgáltatásfiók és a jelszót és az SQL automatikus javítás karbantartási ütemezés módosítása.

* **Az SQL Server neve előtag** hozhat létre minden egyes SQL Server virtuális gép nevét használja. A jelen oktatóanyag esetében használja **sqlserver**. A sablon-neveket az SQL Server-virtuálisgépek *sqlserver-0* és *sqlserver-1*.
* **SQL Server-verzió** az SQL Server verziója. Ez az oktatóanyag használatra **SQL Server 2014**. Azt is beállíthatja **SQL Server 2012** vagy **SQL Server 2016**.
* **Az SQL Server szolgáltatásfiókjának felhasználóneve** az SQL Server szolgáltatáshoz tartozó tartományi fióknév. A jelen oktatóanyag esetében használja **sqlservice**.
* **Jelszó** az SQL Server-szolgáltatásfiók jelszava.  Egy összetett jelszót használja. Erősítse meg a jelszót.
* **SQL automatikus javításával karbantartási ütemezés** azonosítja, hogy az Azure automatikusan összekapcsolja az SQL Server-kiszolgálók a hét napját. A jelen oktatóanyag esetében írja be a **vasárnap**.
* **SQL automatikus javításával karbantartási indítása óra** van a az Azure-régióban a napi időpontot, amikor megkezdődik az automatikus javítással.

> [!NOTE]
> A javítási időszakot minden egyes virtuális géphez van egyenletesen elosztani a egy óra. A szolgáltatás megszakadásának elkerülése érdekében egyszerre csak egy virtuális gép telepítve.
>
>

![SQL Server beállításai](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Tekintse át a beállításokat, és kattintson **OK**.

### <a name="summary"></a>Összegzés
Az összefoglalás lapon az Azure ellenőrzi a beállításokat. A sablon is letöltheti. Tekintse át az összegzést. Kattintson az **OK** gombra.

### <a name="buy"></a>Vásárlás
Ez a végső panel tartalmazza **használati feltételeket tartalmazó fájl**, és **adatvédelmi szabályzatát**. Tekintse át ezt az információt. Amikor készen áll az Azure-ban hozhat létre a virtuális gépek és a többi elindításához szükséges erőforrásokat a rendelkezésre állási csoport, kattintson a **létrehozás**.

Az Azure Portalon hoz létre az erőforráscsoportot és az összes erőforrást.

## <a name="monitor-deployment"></a>A figyelő üzembe helyezés
Figyelje meg az üzembehelyezési folyamatot az Azure Portalról. Az üzembe helyezés jelölő ikon automatikusan az Azure portal irányítópultján van rögzítve.

![Azure Dashboard](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Csatlakozás az SQL Serverhez
Az új SQL Server-példányok IP-címek internetkapcsolattal rendelkező virtuális gépek futnak. Távoli asztali (RDP) közvetlenül az SQL Server virtuális gépeken is.

Az RDP-vel egy SQL Server kövesse az alábbi lépéseket:

1. Az Azure portal irányítópultján győződjön meg arról, hogy az üzembe helyezés sikeres volt-e.
2. Kattintson a **erőforrások**.
3. A a **erőforrások** panelen kattintson a **sqlserver-0**, azaz a számítógép neve egy SQL Servert futtató virtuális gépet.
4. Paneljén **sqlserver-0**, kattintson a **Connect**. A böngésző kéri, ha szeretné-e megnyitni vagy menteni a távoli kapcsolat objektumot. Kattintson az **Open** (Megnyitás) elemre.
5. **A távoli asztali kapcsolat** előfordulhat, hogy figyelmezteti, hogy a távoli kapcsolat közzétevője nem azonosítható. Kattintson a **Connect** (Csatlakozás) gombra.
6. Windows biztonsági csatlakozni az elsődleges tartományvezérlő IP-címét a hitelesítő adatok megadását kéri. Kattintson a **másik fiók használata**. A **felhasználónév**, típus **contoso\DomainAdmin**. Ez a fiók beállításakor a rendszergazda felhasználó nevét a sablonban konfigurált. Ha konfigurálta a sablont választott összetett jelszót használja.
7. **A távoli asztal** előfordulhat, hogy figyelmezteti, hogy a távoli számítógép nem hitelesíthető a biztonsági tanúsítványával kapcsolatos problémák miatt. Megjeleníti a biztonsági tanúsítvány neve. Ha követte az oktatóanyag, a név **sqlserver-0.contoso.com**. Kattintson a **Yes** (Igen) gombra.

Ezzel csatlakozott RDP-vel az SQL Server rendszerű virtuális gépekhez. Nyissa meg az SQL Server Management Studio, csatlakozhat az SQL Server alapértelmezett példányát, és győződjön meg arról, hogy a rendelkezésre állási csoport konfigurálása.
