---
title: "Azure Resource Manager virtuális gépek magas rendelkezésre állású beállítása |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan Always On rendelkezésre állási csoport létrehozása az Azure Resource Manager módra az Azure virtuális gépekkel."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: d430febee23081b26eee0a68d4beb43228549f52
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Always On rendelkezésre állási csoportok Azure virtuális gépek automatikus konfigurálásához: erőforrás-kezelő

Ez az oktatóanyag bemutatja, hogyan Azure Resource Manager virtuális gépek használó SQL Server rendelkezésre állási csoport létrehozásához. Az oktatóprogram Azure paneleken állítson be egy sablont. Tekintse át az alapértelmezett beállításokat, írja be a kívánt beállításokat, és frissíti a paneleken a portálon, az oktatóanyag ismerteti.

A teljes útmutató hoz létre egy SQL Server rendelkezésre állási csoport Azure virtuális gépeken, amelyek az alábbi elemeket tartalmazzák:

* Virtuális hálózat már több alhálózat működik, beleértve az olyan előtér- és a backend alhálózathoz
* Két tartományvezérlők, amelyeken az Active Directory-tartomány
* Két virtuális gép futtatása az SQL Server és a telepített a backend alhálózathoz és Active Directory-tartományhoz csatlakozik
* Egy három csomópontos feladatátvevő fürt csomóponttöbbség kvórum modell
* Két szinkron véglegesítésű másodpéldányt a rendelkezésre állási adatbázis egy rendelkezésre állási csoport

A következő ábra azt a teljes megoldás.

![Tesztkörnyezet felépítése az Azure-ban rendelkezésre állási csoportokkal](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Ez a megoldás összes erőforrása tartozik egyetlen erőforráscsoportként működnek.

Ez az oktatóanyag megkezdése előtt ellenőrizze a következőket:

* Már rendelkezik Azure-fiókkal. Ha még nincs fiókja, [regisztrálhat a próbafiókra](http://azure.microsoft.com/pricing/free-trial/).
* Már ismeri a grafikus felhasználói felület segítségével egy SQL Server rendszerű virtuális gép a virtuális gép gyűjteményből. További információkért lásd: [SQL Server Azure virtuális gépek kiépítése](virtual-machines-windows-portal-sql-server-provision.md).
* Már van egy rendelkezésre állási csoportok alapos ismerete. További információkért lásd: [Always On rendelkezésre állási csoportok (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Ha érdekli a rendelkezésre állási csoportokat használ a SharePoint, [konfigurálja az SQL Server 2012 Always On rendelkezésre állási csoportok a SharePoint 2013 rendszerhez](http://technet.microsoft.com/library/jj715261.aspx).
>
>

Ebben az oktatóanyagban az Azure-portál használata:

* Válassza ki a mindig bekapcsolva sablon a portálon.
* Tekintse át a sablon beállításokat, és a környezet néhány konfigurációs beállításainak frissítése.
* Mivel az hozza létre a teljes környezet Azure figyelése
* Csatlakoztassa egy olyan tartományvezérlőre, majd egy SQL Server rendszert futtató kiszolgálóra.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Rendelkezés a fürt a gyűjteményből
Azure biztosít a teljes megoldás egy gyűjtemény lemezképet. A sablon kereséséhez:

1. A fiók használatával jelentkezzen be az Azure-portálon.
2. Az Azure portálon kattintson **+ új** megnyitásához a **új** panelen.
3. Az a **új** panelen, a Keresés **AlwaysOn**.
   ![AlwaysOn sablon keresése](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Keresse meg a keresési eredmények **SQL Server AlwaysOn fürt**.
   ![AlwaysOn sablon](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. A **telepítési modell kiválasztása**, válassza a **erőforrás-kezelő**.

### <a name="basics"></a>Alapvető beállítások
Kattintson a **alapjai** és a következő beállításokat:

* **Rendszergazdai felhasználónév** és tartományi rendszergazdai jogosultságokkal rendelkező felhasználói fiók tagja az SQL Server SysAdmin (rendszergazda) rögzített kiszolgálói szerepkör, mind az SQL Server-példányokon. A jelen oktatóanyag esetében használja **tartománygazda**.
* **Jelszó** a tartományi rendszergazda fiók jelszava. Használjon erős jelszót hozzon létre. Erősítse meg a jelszót.
* **Előfizetés** van az előfizetés, hogy az Azure számlák a rendelkezésre állási csoporthoz tartozó összes üzembe helyezett erőforrás futtatásához. Ha a fiókja több előfizetéssel rendelkezik, megadhat egy másik előfizetést.
* **Erőforráscsoport** a neve, a csoport, amelybe ez a sablon által létrehozott összes Azure erőforrások tartoznak. A jelen oktatóanyag esetében használja **SQL-magas rendelkezésre ÁLLÁSÚ-RG**. További információk: [Azure Resource Manager overview](../../../azure-resource-manager/resource-group-overview.md#resource-groups) (Az Azure Resource Manager áttekintése).
* **Hely** van az Azure-régió, ahol az oktatóanyag hoz létre az erőforrásokhoz. Válassza ki a kívánt Azure-régiót.

Az alábbi képernyőfelvételen a kész **alapjai** panel:

![Alapvető beállítások](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Kattintson az **OK** gombra.

### <a name="domain-and-network-settings"></a>Tartomány és a hálózati beállítások
Az Azure katalógusában sablont hoz létre a tartomány és a tartományvezérlők. A hálózat és két alhálózat is létrehoz. A sablon nem hozható létre kiszolgálók egy meglévő tartományhoz vagy a virtuális hálózat. A következő lépésben a tartományi és hálózati beállítások konfigurálása.

Az a **tartomány és a hálózati beállítások** panelt, tekintse át az előre definiált értékek a tartományhoz, és a hálózati beállítások:

* **Erdő gyökértartományának a neve** a tartománynév, az Active Directory-tartomány, amelyen a fürt. Az oktatóanyag használja **contoso.com**.
* **Virtuális hálózat neve** az Azure virtuális hálózat hálózati neve. Az oktatóanyag használja **autohaVNET**.
* **Tartományvezérlő alhálózat neve** neve, a tartományvezérlőt üzemeltető virtuális hálózat része. Használjon **alhálózat-1**. Ez az alhálózat címét előtag- **10.0.0.0/24**.
* **SQL Server alhálózati név** egy része a virtuális hálózat, amelyen a kiszolgálókat, hogy futtassa az SQL Server- és a tanúsító fájlmegosztás neve. Használjon **alhálózat-2**. Ez az alhálózat címét előtag- **10.0.1.0/26**.

Azure virtuális hálózatairól kapcsolatos további információkért lásd: [virtuális hálózat áttekintése](../../../virtual-network/virtual-networks-overview.md).  

A **tartomány és a hálózati beállítások** az alábbi képernyőfelvételen hasonlóan kell kinéznie:

![Tartomány és a hálózati beállítások](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Ha szükséges, módosíthatja ezeket az értékeket. Ebben az oktatóanyagban az előre definiált értékeket használja.

Tekintse át a beállításokat, és kattintson **OK**.

### <a name="availability-group-settings"></a>Rendelkezésre állási csoport beállítások
A **rendelkezésre állási csoport beállítások**, tekintse át a rendelkezésre állási csoport és a figyelő az előre beállított értéket.

* **Rendelkezésre állási csoport nevének** a rendelkezésre állási csoport fürtözött erőforrás neve. A jelen oktatóanyag esetében használja **Contoso-ag**.
* **Rendelkezésre állási csoport figyelőjének nevével** a fürt és a belső terheléselosztó által használt. Az SQL-kiszolgálóhoz csatlakozó ügyfelek kapcsolódni az adatbázis megfelelő replika használhatja ezt a nevet. A jelen oktatóanyag esetében használja **Contoso-figyelő**.
* **Rendelkezésre állási csoport figyelőjének portszámára** határozza meg a TCP-portot az SQL Server-figyelő. A jelen oktatóanyag esetében használja az alapértelmezett port **1433**.

Ha szükséges, módosíthatja ezeket az értékeket. Ebben az oktatóanyagban az előre definiált értékeket használja.  

![Rendelkezésre állási csoport beállítások](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Kattintson az **OK** gombra.

### <a name="virtual-machine-size-storage-settings"></a>Virtuális gép méretét, a tárolási beállítások
A **Virtuálisgép-méretet, a tárolási beállítások**, egy SQL Server virtuális gép méretének kiválasztása, és tekintse át az egyéb beállításokat.

* **SQL Server virtuálisgép-méret** mindkét SQL Server rendszerű virtuális gépek méretét. Válasszon egy megfelelő virtuális gép mérete, a munkaterhelés számára. Ha ebben a környezetben az oktatóanyaghoz, használjon **DS2**. A termelési számítási feladatokhoz válassza ki a virtuális gép méretét, amely képes támogatni a munkaterhelés. Sok termelési számítási feladatokhoz szükséges **DS4** vagy nagyobb. A sablon két virtuális gép ekkora alapszik, és az egyes SQL Server telepítése. További információkért lásd: [virtuális gépek méretei](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Azure telepíti a vállalati az SQL Server kiadása. A költségeket a edition és a virtuálisgép-méret függ. Aktuális költségekre vonatkozó részletes információkért lásd: [virtuális gépek díjszabása](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Tartomány a tartományvezérlő virtuálisgép-méret** a tartományvezérlők virtuális gép méretét. Az oktatóanyag használatra **D2**.
* **A fájl tanúsító fájlmegosztás virtuális gép mérete** a virtuális gép mérete a tanúsító fájlmegosztás. A jelen oktatóanyag esetében használja **A1**.
* **SQL-Storage-fiók** , amely tárolja a SQL Server-adatok és az operációs rendszer lemezeket a tárfiók neve. A jelen oktatóanyag esetében használja **alwaysonsql01**.
* **DC tárfiók** a tartományvezérlők a tárfiók neve. A jelen oktatóanyag esetében használja **alwaysondc01**.
* **SQL Server-adatok lemezméret** TB van az SQL Server adatlemez TB a méretét. Adjon meg egy 1 és 4 közötti számot. A jelen oktatóanyag esetében használja **1**.
* **Tárolási optimalizálása** bizonyos tárolási konfigurációs beállításait adja meg az SQL Server virtuális gépek, a munkaterhelés típusától függően. Ebben a forgatókönyvben szereplő összes SQL Server virtuális gép prémium szintű storage használata az Azure gazdagép lemezgyorsítótár csak olvasható. Emellett a munkaterheléshez tartozó SQL Server-beállítások is optimalizálhatja, ezek a beállítások egyikének kiválasztásával:

  * **Általános munkaterhelés** nincs konfigurációs beállításait.
  * **Tranzakciós feldolgozást** beállítása nyomkövetési jelző 1117 és 1118.
  * **Az adatraktározás terén** beállítása nyomkövetési jelző 1117 és 610.

A jelen oktatóanyag esetében használja **általános munkaterhelés**.

![Virtuális gép mérete tárolási beállításai](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Tekintse át a beállításokat, és kattintson **OK**.

#### <a name="a-note-about-storage"></a>Tárolási Megjegyzés
További optimalizálást az SQL Server adatlemezek méretétől függ. Minden egyes adatlemez terabájt Azure hozzáadja egy további 1 TB prémium szintű storage. Ha egy kiszolgáló 2 TB, vagy több, a sablon egy tárolókészletet hoz létre minden egyes SQL Server virtuális gépen. A tárolókészlet a tárhely-virtualizálás egy formája, ahol több lemezből megadására vannak konfigurálva nagyobb kapacitás, a rugalmasság és a teljesítmény.  A sablon majd hoz létre a tárolóhelyek a tárolókészletben, és az operációs rendszer egyetlen adatlemezt mutatja be. A sablon jelöl ki, az SQL Server ezt a lemezt az adatok tárolására. A sablon az SQL Server a tárolókészlet beállítja a következő beállításokkal:

* Paritásos a szektorszórás beállítása a virtuális lemez mérete. Tranzakciós munkaterhelések 64 KB-os használják. Adatraktározás számítási feladatainál 256 KB-os használja.
* Rugalmasságra egyszerű (rugalmasság nélküli).

> [!NOTE]
> Prémium szintű Azure storage helyileg redundáns, és az adatok három példányban tartja egyetlen régión belül, így nincs szükség további rugalmasságot, a tárolókészletben.
>
>

* Az oszlopok száma egyenlő a tárolókészletben lévő lemezek számát.

Tárolóhely és a tárolókészletek kapcsolatos további információkért lásd:

* [Tárolóhelyek – áttekintés](http://technet.microsoft.com/library/hh831739.aspx)
* [Windows Server biztonsági másolat és Tárolókészletek](http://technet.microsoft.com/library/dn390929.aspx)

SQL Server technológiának az ajánlott eljárásokkal kapcsolatos további információkért lásd: [teljesítmény a bevált gyakorlat az SQL Server Azure virtuális gépek](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>SQL Server beállításai
A **SQL Server-beállítások**, és módosítsa az SQL Server virtuális gép nevének előtagja, SQL Server-verzió, SQL Server-szolgáltatásfiók és a jelszót és az SQL automatikus javítás karbantartási ütemezését.

* **SQL Server neve előtag** hozhatók létre minden egyes SQL Server virtuális gép nevét. A jelen oktatóanyag esetében használja **sqlserver**. A sablon nevét az SQL Server virtuális gépek *SQL Server-0* és *SQL Server-1*.
* **SQL Server-verzió** az SQL Server verziója. Az oktatóanyag használatra **SQL Server 2014**. Másik lehetőségként **SQL Server 2012** vagy **SQL Server 2016**.
* **SQL Server szolgáltatásfiókjának felhasználóneve** a tartományi fiók neve, az SQL Server szolgáltatáshoz. A jelen oktatóanyag esetében használja **sqlservice**.
* **Jelszó** az SQL Server szolgáltatás fiókjának jelszava.  Használjon erős jelszót hozzon létre. Erősítse meg a jelszót.
* **SQL automatikus javítás karbantartási ütemterv** azonosítja, hogy az Azure automatikusan összekapcsolja az SQL Server a hét napját. Ebben az oktatóanyagban írja be a következőt **vasárnap**.
* **SQL automatikus javítás karbantartási start óra** van a időpontot az Azure-régió az automatikus javítás megkezdésekor.

> [!NOTE]
> A javítási időszakot minden egyes virtuális géphez van egy órával lépcsőzetes elrendezésben. A szolgáltatás megszűnésének megelőzése érdekében egyszerre csak egy virtuális gép telepítve.
>
>

![SQL Server beállításai](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Tekintse át a beállításokat, és kattintson **OK**.

### <a name="summary"></a>Összefoglalás
Az összefoglalás lapon Azure ellenőrzi a beállításokat. A sablon is letöltheti. Tekintse át az összegzést. Kattintson az **OK** gombra.

### <a name="buy"></a>Vásárlás
A végső panel tartalmaz **használati feltételek**, és **adatvédelmi**. Tekintse át ezt az információt. Amikor készen áll az Azure-indítsa el a virtuális gépek és az összes többi létrehozásához szükséges erőforrások a rendelkezésre állási csoporthoz, kattintson a **létrehozása**.

Az Azure-portálon létrehoz az erőforráscsoportot és az összes erőforrás.

## <a name="monitor-deployment"></a>A figyelő telepítése
Azure-portálról telepítési előrehaladásának figyeléséhez. A központi telepítés jelölő ikon automatikusan az Azure portál Irányítópultjára van rögzítve.

![Az Azure irányítópult](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Csatlakozás SQL-kiszolgálóhoz
Az SQL Server új példányát internetkapcsolattal rendelkező IP-címmel rendelkező virtuális gépeken futnak. A távoli asztal (RDP) közvetlenül az SQL Server virtuális gépeken is.

Távoli asztali SQL Serverre kövesse az alábbi lépéseket:

1. Az Azure portál irányítópultján győződjön meg arról, hogy a telepítés sikerült.
2. Kattintson a **erőforrások**.
3. Az a **erőforrások** panelen kattintson a **SQL Server-0**, ez az SQL Servert futtató virtuális gépek egyik számítógép nevét.
4. A panel **SQL Server-0**, kattintson a **Connect**. A böngésző megkérdezi, hogy szeretné-e meg és a távoli kapcsolati objektum. Kattintson a **nyitott**.
5. **Távoli asztali kapcsolat** jelezheti, hogy a távoli kapcsolat közzétevője nem azonosítható. Kattintson a **Connect** (Csatlakozás) gombra.
6. Windows biztonsági eléréséhez az elsődleges tartományvezérlő IP-címe a megadott hitelesítő adatok megadását kéri. Kattintson a **másik fiók használata**. A **felhasználónév**, típus **contoso\DomainAdmin**. Ha úgy állítja be a rendszergazdai felhasználónevet a sablonban konfigurálta ezt a fiókot. Használja az összetett jelszót, hogy úgy döntött, hogy a sablon konfigurálásakor.
7. **A távoli asztal** jelezheti, hogy a távoli számítógép a biztonsági tanúsítvánnyal kapcsolatos problémák miatt nem lehet hitelesíteni. Azt mutatja meg a biztonsági tanúsítvány neve. Ha követte az oktatóanyag, van-e a név **SQL Server-0.contoso.com**. Kattintson a **Yes** (Igen) gombra.

Most már csatlakoztatott RDP az SQL Server virtuális géphez. Nyissa meg az SQL Server Management Studio eszközt, az SQL Server alapértelmezett példányát kapcsolódni, és győződjön meg arról, hogy van-e konfigurálva a rendelkezésre állási csoport.
