---
title: "SQL Server rendelkezésre állási csoportok – az Azure virtual machines - Előfeltételek |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan konfigurálhatja az Azure virtuális gépeken futó SQL Server Always On rendelkezésre állási csoport létrehozására vonatkozó előfeltételek."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/09/2017
ms.author: mikeray
ms.openlocfilehash: 3d508877928e033f24dae62c1042745ea7250033
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="complete-the-prerequisites-for-creating-always-on-availability-groups-on-azure-virtual-machines"></a>Always On rendelkezésre állási csoportok létrehozása az Azure virtuális gépeken előfeltételeit teljesítenie

Ez az oktatóanyag bemutatja, hogyan lehet elvégezni az Előfeltételek létrehozásához egy [SQL Server Always On rendelkezésre állási csoport az Azure virtuális gépek (VM)](virtual-machines-windows-portal-sql-availability-group-tutorial.md). Amikor befejezte az előfeltételeket, vannak egy tartományvezérlő, két SQL Server virtuális gépen és a tanúsító kiszolgálói egyetlen erőforráscsoportként működnek.

**Becsült idő**: eltarthat néhány óra múlva az Előfeltételek befejezéséhez. Ezen idő nagy töltött, virtuális gépek létrehozását.

A következő ábra szemlélteti, hogy az oktatóanyag felépítéséhez.

![Rendelkezésre állási csoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Tekintse át a rendelkezésre állási csoport dokumentáció

Ez az oktatóanyag feltételezi, hogy rendelkezik-e az SQL Server Always On rendelkezésre állási csoportokkal kapcsolatos alapvető ismeretekkel. Ha még nem ismeri ezt a technológiát, lásd: [az mindig a rendelkezésre állási csoportok áttekintése (SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Azure-fiók létrehozása
Rendelkeznie kell Azure-fiókkal. Is [nyisson meg egy ingyenes Azure-fiók](/pricing/free-trial/?WT.mc_id=A261C142F) vagy [aktiválhatja a Visual Studio előfizetői előnyeit](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Kattintson a  **+**  új-objektum létrehozása a portálon.

   ![Új objektum](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Típus **erőforráscsoport** a a **piactér** keresési ablak.

   ![Erőforráscsoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Kattintson a **erőforráscsoport**.
5. Kattintson a **Create** (Létrehozás) gombra.
6. A a **erőforráscsoport** panel alatt **erőforráscsoport-név**, adja meg az erőforráscsoport nevét. Írja be például **sql-magas rendelkezésre állású-rg**.
7. Ha több Azure-előfizetéssel rendelkezik, győződjön meg arról, hogy az előfizetés az Azure-előfizetés, amely a rendelkezésre állási csoport létrehozásához.
8. Válasszon ki egy helyet. A hely az az Azure-régió, ahol szeretné a rendelkezésre állási csoport létrehozására. Ebben az oktatóanyagban az oktatóanyagban módosítjuk hozhat létre egy Azure-beli hely összes erőforrása.
9. Ellenőrizze, hogy **rögzítés az irányítópulton** be van jelölve. A nem kötelező beállítás ahhoz az erőforráscsoporthoz parancsikont helyez el az Azure-portál irányítópultjának.

   ![Erőforráscsoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Kattintson a **létrehozása** az erőforráscsoport létrehozásához.

Azure erőforráscsoport és PIN-kódok parancsikon létrehozása az erőforráscsoportot a portálon.

## <a name="create-the-network-and-subnets"></a>A hálózati és alhálózatok létrehozásával
A következő lépés, ha a hálózatok és alhálózatok Azure erőforráscsoportban.

A megoldás két alhálózat egy virtuális hálózatot használ. A [virtuális hálózat áttekintése](../../../virtual-network/virtual-networks-overview.md) hálózatok az Azure-ban további információt nyújt.

A virtuális hálózat létrehozása:

1. Az Azure portálon, az erőforráscsoportban, kattintson a **+ Hozzáadás**. Az Azure megnyílik a **mindent** panelen.

   ![Új elem](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Keresse meg **virtuális hálózati**.

     ![Keresési virtuális hálózat](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Kattintson a **virtuális hálózati**.
4. Az a **virtuális hálózati** panelen kattintson a **erőforrás-kezelő** üzembe helyezési modellt, és kattintson **létrehozása**.

    Az alábbi táblázat a virtuális hálózati beállításait:

   | **Mező** | Érték |
   | --- | --- |
   | **Name (Név)** |autoHAVNET |
   | **Címtér** |10.33.0.0/24 |
   | **Alhálózat neve** |Rendszergazda |
   | **Alhálózati címtartomány** |10.33.0.0/29 |
   | **Előfizetés** |Adja meg az előfizetést, amelyet lemezképfájlforrásként kíván használni. **Előfizetés** üres, ha csak egy előfizetéssel rendelkezik. |
   | **Erőforráscsoport** |Válasszon **meglévő** , és válassza ki az erőforráscsoport nevét. |
   | **Hely** |Adja meg az Azure-hely. |

   A cím és az alhálózati címtartományt eltérhet a táblából. Attól függően, hogy az előfizetéséhez a portál javasol, az elérhető és a megfelelő alhálózati címtartományt. Ha nincs elegendő címtérrel érhető el, használjon másik előfizetést.

   A példában az alhálózati név **Admin**. Ez az alhálózat van a tartományvezérlőn.

5. Kattintson a **Create** (Létrehozás) gombra.

   ![A virtuális hálózat konfigurálása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Azure ad vissza, a portál Irányítópultjára, és értesíti, amikor az új hálózat létre van hozva.

### <a name="create-a-second-subnet"></a>Hozzon létre egy második alhálózatot
Az új virtuális hálózat rendelkezik egy alhálózatot, nevű **Admin**. A tartományvezérlők ezt használni. A második alhálózat nevű SQL Server virtuális gépek használata **SQL**. Ez az alhálózat konfigurálása:

1. Az irányítópulton, kattintson a létrehozott, az erőforráscsoport **SQL-magas rendelkezésre ÁLLÁSÚ-RG**. Keresse meg a hálózati alatt az erőforráscsoportban **erőforrások**.

    Ha **SQL-magas rendelkezésre ÁLLÁSÚ-RG** nem látható, megkereshetjük **erőforráscsoportok** és az erőforráscsoport neve szerinti szűrését.
2. Kattintson a **autoHAVNET** az erőforrások listájában. Azure megnyílik a hálózati konfigurációs panel.
3. Az a **autoHAVNET** virtuális hálózat panel alatt **beállítások** , kattintson a **alhálózatok**.

    Vegye figyelembe az alhálózatot, amelyet már hozott létre.

   ![A virtuális hálózat konfigurálása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Hozzon létre egy második alhálózatot. Kattintson a **+ alhálózati**.
6. A a **alhálózat hozzáadása** panelen írja be az alhálózat konfigurálása **sqlsubnet** alatt **neve**. Azure automatikusan megadja egy érvényes **-címtartományt**. Ellenőrizze, hogy a címtartomány legalább 10 cím szerepel. Éles környezetben több címet lehet szükség.
7. Kattintson az **OK** gombra.

    ![A virtuális hálózat konfigurálása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

A következő táblázat összefoglalja a hálózati konfigurációs beállításokat:

| **Mező** | Érték |
| --- | --- |
| **Name (Név)** |**autoHAVNET** |
| **Címtér** |Ezt az értéket a rendelkezésre álló címterek az előfizetésében függ. Jellemző értéke 10.0.0.0/16. |
| **Alhálózat neve** |**rendszergazda** |
| **Alhálózati címtartomány** |Ezt az értéket a rendelkezésre álló címtartományokat az előfizetésében függ. Jellemző értéke: 10.0.0.0/24. |
| **Alhálózat neve** |**sqlsubnet** |
| **Alhálózati címtartomány** |Ezt az értéket a rendelkezésre álló címtartományokat az előfizetésében függ. Jellemző értéke 10.0.1.0/24. |
| **Előfizetés** |Adja meg az előfizetést, amelyet lemezképfájlforrásként kíván használni. |
| **Erőforráscsoport** |**SQL-MAGAS RENDELKEZÉSRE ÁLLÁSÚ-RG-N** |
| **Hely** |Adja meg az erőforráscsoport kiválasztott ugyanazon a helyen. |

## <a name="create-availability-sets"></a>Rendelkezésre állási csoportok létrehozása

Virtuális gépek létrehozása előtt rendelkezésre állási csoportok létrehozásához szükséges. Rendelkezésre állási készletek csökkentik a leállásának elvégzett tervezett vagy nem tervezett karbantartási események. Egy Azure rendelkezésre állási csoportok pedig a logikai csoport, amely a fizikai tartalék tartományok és a frissítési tartományok Azure-erőforrások. A tartalék tartomány biztosítja, hogy a rendelkezésre állási csoport tagjai külön energia- és hálózati erőforrásokat. Egy frissítési tartományt biztosítja, hogy a rendelkezésre állási csoport tagjai nem állapotba a karbantartás egyszerre. További információkért lásd: [virtuális gépek rendelkezésre állásának kezelése](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Két rendelkezésre állási csoportokra van szüksége. A tartományvezérlők egyik. A második pedig az SQL Server virtuális gépen.

Rendelkezésre állási csoportok létrehozásához lépjen az erőforráscsoportot, és kattintson **Hozzáadás**. Írja be az eredmények szűréséhez **rendelkezésre állási csoport**. Kattintson a **rendelkezésre állási csoport** az eredményeket, majd **létrehozása**.

Két rendelkezésre állási készletek paramétereknek megfelelően konfigurálja a következő táblázatban:

| **Mező** | Tartomány a tartományvezérlő a rendelkezésre állási csoport | SQL Server rendelkezésre állási csoport |
| --- | --- | --- |
| **Name (Név)** |adavailabilityset |sqlavailabilityset |
| **Erőforráscsoport** |SQL-MAGAS RENDELKEZÉSRE ÁLLÁSÚ-RG-N |SQL-MAGAS RENDELKEZÉSRE ÁLLÁSÚ-RG-N |
| **Tartalék tartományok** |3 |3 |
| **Tartományok frissítése** |5 |3 |

A rendelkezésre állási csoportok létrehozása után térjen vissza az erőforráscsoportot az Azure portálon.

## <a name="create-domain-controllers"></a>Tartományvezérlők létrehozása
Miután létrehozta a hálózaton, alhálózatok, rendelkezésre állási készletek és egy internetre irányuló terheléselosztót, készen áll a tartományvezérlők a virtuális gépek létrehozásához.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>A tartományvezérlők virtuális gépek létrehozása
Hozzon létre, és a tartományvezérlők konfigurálása, térjen vissza a **SQL-magas rendelkezésre ÁLLÁSÚ-RG** erőforráscsoportot.

1. Kattintson az **Add** (Hozzáadás) parancsra. A **mindent** panel nyílik meg.
2. Típus **Windows Server 2016 Datacenter**.
3. Kattintson a **Windows Server 2016 Datacenter**. Az a **Windows Server 2016 Datacenter** panelen ellenőrizze, hogy van-e a telepítési modell **erőforrás-kezelő**, és kattintson a **létrehozása**. Az Azure megnyílik a **hozzon létre virtuális gépet** panelen.

Ismételje meg az előző két virtuális gép létrehozásához. A két virtuális gép neve:

* Active Directory-elsődleges-tartományvezérlők
* Active Directory-másodlagos-tartományvezérlők

  > [!NOTE]
  > A **ad – másodlagos-tartományvezérlő** virtuális gép nem kötelező, magas rendelkezésre állás biztosításához az Active Directory tartományi szolgáltatásokhoz.
  >
  >

Az alábbi táblázat a két gép beállításait:

| **Mező** | Érték |
| --- | --- |
| **Name (Név)** |Első tartományvezérlő: *ad-elsődleges-dc*.</br>Második tartományvezérlő *ad – másodlagos-tartományvezérlő*. |
| **Virtuális merevlemez típusa** |SSD |
| **Felhasználónév** |Tartományi rendszergazdai |
| **Jelszó** |Contoso! 0000 |
| **Előfizetés** |*Az Ön előfizetése* |
| **Erőforráscsoport** |SQL-MAGAS RENDELKEZÉSRE ÁLLÁSÚ-RG-N |
| **Hely** |*A hely* |
| **Méret** |DS1_V2 |
| **Storage** | **Felügyelt lemezeket használó** - **Igen** |
| **Virtuális hálózat** |autoHAVNET |
| **Alhálózat** |Rendszergazda |
| **Nyilvános IP-cím** |*A virtuális gép azonos néven* |
| **Hálózati biztonsági csoport** |*A virtuális gép azonos néven* |
| **A rendelkezésre állási csoport** |adavailabilityset </br>**Tartományok fault**: 2. régiója</br>**Tartományok frissítése**: 2. régiója|
| **Diagnosztika** |Engedélyezve |
| **Diagnosztikai tárfiók** |*Automatikusan létrehozott* |

   >[!IMPORTANT]
   >Csak egy virtuális gép elhelyezheti rendelkezésre állási készlet létrehozásakor. A rendelkezésre állási csoportot a virtuális gép létrehozása után nem módosítható. Lásd: [virtuális gépek rendelkezésre állásának kezelése](../manage-availability.md).

A virtuális gépek az Azure létrehoz.

Miután a virtuális gépek jönnek létre, állítsa be a tartományvezérlőt.

### <a name="configure-the-domain-controller"></a>A tartományvezérlő konfigurálása
Az alábbi lépéseket kell konfigurálni a **ad-elsődleges-dc** gépi Corp.contoso.com tartományvezérlőként működik.

1. A portál, nyissa meg a **SQL-magas rendelkezésre ÁLLÁSÚ-RG** erőforrás csoportot, és válassza a **ad-elsődleges-dc** gép. A a **ad-elsődleges-dc** panelen kattintson a **Connect** megnyitni egy RDP-fájlt a távoli asztal eléréséhez.

    ![Csatlakozzon a virtuális géphez](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Jelentkezzen be a beállított rendszergazdai fiókkal (**\DomainAdmin**) és a jelszót (**Contoso! 0000**).
3. Alapértelmezés szerint a **Kiszolgálókezelő** irányítópult üzenetnek kell megjelennie.
4. Kattintson a **szerepkörök és szolgáltatások hozzáadása** az irányítópulton lévő hivatkozásra.

    ![Kiszolgálókezelő - Szerepkörök hozzáadása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Válassza ki **következő** amíg elér a **kiszolgálói szerepkörök** szakasz.
6. Válassza ki a **Active Directory tartományi szolgáltatások** és **DNS-kiszolgáló** szerepkörök. Amikor a rendszer kéri, adja hozzá ezeket a szerepköröket szükséges további funkciókkal.

   > [!NOTE]
   > A Windows figyelmeztetést jelenít meg, hogy nincs-e statikus IP-cím. Ha a konfigurációs tesztelést, kattintson a **Folytatás**. Éles környezetben, az IP-cím beállítása az Azure portálon, statikus vagy [PowerShell segítségével állítsa be a statikus IP-cím a tartomány a tartományvezérlő gép](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Adja hozzá a szerepkörök párbeszédpanel](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Kattintson a **következő** amíg el nem éri a **megerősítő** szakasz. Válassza ki a **a célkiszolgáló automatikus újraindítása, ha szükséges** jelölőnégyzetet.
8. Kattintson az **Install** (Telepítés) gombra.
9. Miután a szolgáltatások telepítésének befejezéséhez, térjen vissza a **Kiszolgálókezelő** irányítópult.
10. Jelölje be az új **Active Directory tartományi szolgáltatások** lehetőséget a bal oldali panelen.
11. Kattintson a **további** a sárga figyelmeztető sáv hivatkozásra kattintva.

    ![Az Active Directory tartományi szolgáltatások párbeszédpanel a DNS-kiszolgáló virtuális gépen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. Az a **művelet** oszlopa a **összes kiszolgáló feladat részletei** párbeszédpanel, kattintson a **a számítógép előléptetése tartományvezérlővé**.
13. Az a **Active Directory tartományi szolgáltatások konfigurációs varázslója**, a következő értékeket használja:

    | **Lap** | Beállítás |
    | --- | --- |
    | **Központi telepítés konfigurálása** |**Új erdő hozzáadása**<br/> **Gyökértartomány neve** = corp.contoso.com |
    | **Tartományvezérlő beállításai** |**DSRM-jelszót** = Contoso! 0000<br/>**Jelszó megerősítése** = Contoso! 0000 |
14. Kattintson a **következő** haladhat végig a varázsló többi lapján. Az a **szükséges előfeltételek ellenőrzése** lapon, győződjön meg arról, hogy a következő üzenetet látja: **az előfeltétel-ellenőrzés sikeresen lefutott**. Tekintse át a megfelelő figyelmeztető üzeneteket, de lehetséges, a telepítés folytatásához.
15. Kattintson az **Install** (Telepítés) gombra. A **ad-elsődleges-dc** virtuális gép automatikusan újraindul.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Vegye figyelembe az elsődleges tartományvezérlő IP-címe

Az elsődleges tartományvezérlőt használja a DNS. Vegye figyelembe az elsődleges tartományvezérlő IP-címét.

Az elsődleges tartományvezérlő IP-címét egy módja van az Azure portálon keresztül.

1. Nyissa meg az erőforráscsoportot az Azure-portálon.

2. Kattintson az elsődleges tartományvezérlőn történik.

3. Az elsődleges tartományvezérlő paneljén kattintson **hálózati illesztőt**.

![Hálózati illesztők](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Vegye figyelembe a magánhálózati IP-címet ehhez a kiszolgálóhoz.

### <a name="configure-the-virtual-network-dns"></a>A virtuális hálózat DNS konfigurálása
Után az első tartományvezérlő létrehozásához, és a DNS engedélyezése az első kiszolgálón, konfigurálja a virtuális hálózat DNS-ehhez a kiszolgálóhoz használandó.

1. Az Azure portálon kattintson a virtuális hálózaton.

2. A **beállítások**, kattintson a **DNS-kiszolgáló**.

3. Kattintson a **egyéni**, és írja be az elsődleges tartományvezérlő privát IP-címét.

4. Kattintson a **Save** (Mentés) gombra.

### <a name="configure-the-second-domain-controller"></a>A második tartományvezérlő konfigurálása
Miután az elsődleges tartományvezérlő újraindul, a második tartományvezérlő konfigurálhatja. Ez az opcionális lépés van a magas rendelkezésre állás érdekében. Kövesse az alábbi lépéseket a második tartományvezérlő konfigurálása:

1. A portál, nyissa meg a **SQL-magas rendelkezésre ÁLLÁSÚ-RG** erőforrás csoportot, és válassza ki a **ad – másodlagos-tartományvezérlő** gép. A a **ad – másodlagos-tartományvezérlő** panelen kattintson a **Connect** megnyitni egy RDP-fájlt a távoli asztal eléréséhez.
2. A konfigurált rendszergazda fiók használatával jelentkezzen be a virtuális gép (**BUILTIN\DomainAdmin**) és a jelszót (**Contoso! 0000**).
3. Módosítsa a címet, a tartományvezérlő az előnyben részesített DNS-kiszolgáló címét.
4. A **hálózati és megosztási központ**, kattintson a hálózati illesztőt.
   ![Hálózati illesztő](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Kattintson a **Tulajdonságok** elemre.
6. Válassza ki **Internet Protocol Version 4 (TCP/IPv4)** kattintson **tulajdonságok**.
7. Válassza ki **a következő DNS-kiszolgálócímek használata** és adja meg az elsődleges tartományvezérlő **elsődleges DNS-kiszolgáló**.
8. Kattintson a **OK**, majd **Bezárás** a módosítások véglegesítéséhez. Most már áll tudni csatlakozni a virtuális Gépet **corp.contoso.com**.

   >[!IMPORTANT]
   >Ha a DNS-beállítás módosítása után a távoli asztal elveszítik a kapcsolatot, nyissa meg az Azure portálra, és indítsa újra a virtuális gépet.

9. A távoli asztalt a másodlagos tartományvezérlő, nyissa meg a **a Kiszolgálókezelő irányítópultja**.
10. Kattintson a **szerepkörök és szolgáltatások hozzáadása** az irányítópulton lévő hivatkozásra.

    ![Kiszolgálókezelő - Szerepkörök hozzáadása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Válassza ki **következő** amíg elér a **kiszolgálói szerepkörök** szakasz.
12. Válassza ki a **Active Directory tartományi szolgáltatások** és **DNS-kiszolgáló** szerepkörök. Amikor a rendszer kéri, adja hozzá ezeket a szerepköröket szükséges további funkciókkal.
13. Miután a szolgáltatások telepítésének befejezéséhez, térjen vissza a **Kiszolgálókezelő** irányítópult.
14. Jelölje be az új **Active Directory tartományi szolgáltatások** lehetőséget a bal oldali panelen.
15. Kattintson a **további** a sárga figyelmeztető sáv hivatkozásra kattintva.
16. Az a **művelet** oszlopa a **összes kiszolgáló feladat részletei** párbeszédpanel, kattintson a **a számítógép előléptetése tartományvezérlővé**.
17. A **központi telepítés konfigurálása**, jelölje be **tartományvezérlő hozzáadása meglévő tartományhoz**.
   ![Központi telepítés konfigurálása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Kattintson a **Kiválasztás** gombra.
19. Csatlakozás a rendszergazdai fiók használatával (**vállalat esetében. CONTOSO.COM\domainadmin**) és a jelszót (**Contoso! 0000**).
20. A **jelöljön ki egy tartományt az erdőből**, kattintson a tartomány, majd **OK**.
21. A **tartományvezérlő-beállítások**, használja az alapértelmezett értékeket, majd állítsa be a DSRM-jelszót.

   >[!NOTE]
   >A **DNS-beállítások** lap jelezheti, hogy a DNS-kiszolgáló egy delegálás nem hozható létre. Ez a figyelmeztetés nem éles környezetben figyelmen kívül hagyhatja.
22. Kattintson a **következő** amíg el nem éri a párbeszédpanel a **Előfeltételek** ellenőrzése. Ezt követően kattintson a **Telepítés** gombra.

Ha a kiszolgáló befejezi a konfigurációs módosításokat, indítsa újra a kiszolgálót.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>A magánhálózati IP-cím hozzáadása a VPN DNS-kiszolgálót a második tartományvezérlő

Az Azure portálon, a virtuális hálózat módosítsa a DNS-kiszolgáló a másodlagos tartományvezérlő IP-címét tartalmazza. Ez lehetővé teszi a DNS szolgáltatás redundancia csökkentése érdekében.

### <a name=DomainAccounts></a>A tartományi fiókok beállítása

A következő lépésekkel konfigurálja az Active Directory-fiókokat. Az alábbi táblázat a fiókok:

| |Telepítési fiók<br/> |SQL Server-0 <br/>SQL Server és az SQL Agent szolgáltatás fiókja |SQL Server-1<br/>SQL Server és az SQL Agent szolgáltatás fiókja
| --- | --- | --- | ---
|**Utónév** |Telepítés |SQLSvc1 | SQLSvc2
|**Felhasználó SamAccountName** |Telepítés |SQLSvc1 | SQLSvc2

A következő lépésekkel minden fiók létrehozása.

1. Jelentkezzen be a **ad-elsődleges-dc** gép.
2. A **Kiszolgálókezelő**, jelölje be **eszközök**, és kattintson a **Active Directory felügyeleti központ**.   
3. Válassza ki **corp (helyi)** a bal oldali ablaktáblán.
4. A jobb oldali **feladatok** ablaktáblán válassza előbb **új**, és kattintson a **felhasználói**.
   ![Active Directory felügyeleti központ](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Az egyes fiókok számára összetett jelszót állíthat be.<br/> Nem éles környezetben állítsa be a felhasználói fiókot soha ne járjon le.

5. Kattintson a **OK** a felhasználó létrehozásához.
6. Ismételje az előző három-fiókkal.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Adja meg a szükséges engedélyeket a telepítési fiók
1. Az a **Active Directory felügyeleti központ**, jelölje be **corp (helyi)** a bal oldali ablaktáblán. Ezt a jobb oldali **feladatok** ablaktáblán kattintson a **tulajdonságok**.

    ![CORP felhasználó tulajdonságai](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Válassza ki **bővítmények**, majd kattintson a **speciális** gombra a **biztonsági** lapon.
3. Az a **speciális biztonsági beállítások vállalati** párbeszédpanel, kattintson a **Hozzáadás**.
4. Kattintson a **rendszerbiztonsági tag kiválasztása**, keressen **CORP\Install**, és kattintson a **OK**.
5. Válassza ki a **az összes tulajdonság olvasása** jelölőnégyzetet.

6. Válassza ki a **számítógép-objektumok létrehozása** jelölőnégyzetet.

     ![Corp felhasználói engedélyek](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Kattintson a **OK**, és kattintson a **OK** újra. Zárja be a **corp** tulajdonságai ablakban.

Most, hogy befejezte az Active Directory és a felhasználói objektumok, két SQL Server virtuális gépen és a tanúsító kiszolgálói virtuális gép létrehozása. Majd a három tartományhoz csatlakozzon.

## <a name="create-sql-server-vms"></a>SQL Server virtuális gépek létrehozása

Hozzon létre három további virtuális gépeket. A megoldás két virtuális gép az SQL Server-példányokat igényel. A harmadik virtuális gép egy tanúsító fog működni. Windows Server 2016 használhatja egy [tanúsító cloud](http://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness), azonban ez a dokumentum korábbi operációs rendszerekhez való konzisztenciáját a virtuális gép használja a tanúsító.  

Folytatás előtt fontolja meg a következőket deisign.

* **Tároló - Azure által kezelt lemezeken**

   A virtuális gépek tárolására Azure felügyelt lemezek használhatók. A Microsoft azt javasolja, hogy felügyelt lemezek SQL Server virtuális gépekhez. A Managed Disks szolgáltatás a háttérben kezeli a tárterületet. Emellett ha ugyanabban a rendelkezésre állási csoportban több, a Managed Diskset használó virtuális gép található, az Azure elosztja a tárolási erőforrásokat, hogy megfelelő redundanciát biztosítson. További információkért lásd az [Azure Managed Disks áttekintését](../managed-disks-overview.md). A rendelkezésre állási csoportba felügyelt lemezek kapcsolatos részletekért lásd: [használata felügyelt lemezeket a virtuális gépek rendelkezésre állási csoportba](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Hálózati - privát IP-címek éles környezetben**

   A virtuális gépek esetén ez az oktatóanyag nyilvános IP-címet használja. Ez lehetővé teszi a távoli kapcsolat a virtuális gép számára közvetlenül az interneten keresztül, mert a konfigurációs lépéseket egyszerűbbé teszi. Éles környezetben a Microsoft azt javasolja, hogy csak privát IP-címek ahhoz, hogy csökkentse a biztonsági kockázatokat, az SQL Server-példány VM erőforrás.

### <a name="create-and-configure-the-sql-server-vms"></a>Hozza létre és konfigurálja az SQL Server virtuális gépen
Ezután hozzon létre három virtuális gépek – két SQL Server virtuális gépen és a virtuális gépek számára egy további fürtcsomópontra. Minden egyes virtuális gépek létrehozásához lépjen vissza a **SQL-magas rendelkezésre ÁLLÁSÚ-RG** erőforráscsoport, kattintson **Hozzáadás**, keresse meg a megfelelő gyűjteményelemet, majd **virtuális gép**, és kattintson a  **Gyűjteményből**. Olvassa el a következő táblázat a virtuális gépek létrehozásához:


| Lap | VM1 | VM2 VIRTUÁLIS GÉPNEK | VM3 |
| --- | --- | --- | --- |
| Válassza ki a megfelelő gyűjteményelem |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise, Windows Server 2016** |**SQL Server 2016 SP1 Enterprise, Windows Server 2016** |
| Virtuálisgép-konfiguráció **alapjai** |**Név** fürt-fsw =<br/>**Felhasználónév** tartománygazda =<br/>**Jelszó** = Contoso! 0000<br/>**Előfizetés** az előfizetés =<br/>**Erőforráscsoport** SQL-magas rendelkezésre ÁLLÁSÚ-RG =<br/>**Hely** az Azure-beli hely = |**Név** sqlserver – 0 =<br/>**Felhasználónév** tartománygazda =<br/>**Jelszó** = Contoso! 0000<br/>**Előfizetés** az előfizetés =<br/>**Erőforráscsoport** SQL-magas rendelkezésre ÁLLÁSÚ-RG =<br/>**Hely** az Azure-beli hely = |**Név** SQL Server-1 =<br/>**Felhasználónév** tartománygazda =<br/>**Jelszó** = Contoso! 0000<br/>**Előfizetés** az előfizetés =<br/>**Erőforráscsoport** SQL-magas rendelkezésre ÁLLÁSÚ-RG =<br/>**Hely** az Azure-beli hely = |
| Virtuálisgép-konfiguráció **mérete** |**MÉRET** = DS1\_V2 (1 mag, 3.5-ös GB) |**MÉRET** = DS2\_V2 (2 mag, 7 GB)</br>A méret támogatnia kell az SSD-tárhelyre (prémium szintű támogatása. )) |**MÉRET** = DS2\_V2 (2 mag, 7 GB) |
| Virtuálisgép-konfiguráció **beállítások** |**Tárolási**: az kezelt lemezek.<br/>**Virtuális hálózati** = autoHAVNET<br/>**Alhálózati** = sqlsubnet(10.1.1.0/24)<br/>**Nyilvános IP-cím** automatikusan generált.<br/>**Hálózati biztonsági csoport** = None<br/>**Diagnosztikai figyelő** = engedélyezve<br/>**Diagnosztikai tárfiók** = egy automatikusan létrehozott tárfiók használata<br/>**A rendelkezésre állási csoport** = sqlAvailabilitySet<br/> |**Tárolási**: az kezelt lemezek.<br/>**Virtuális hálózati** = autoHAVNET<br/>**Alhálózati** = sqlsubnet(10.1.1.0/24)<br/>**Nyilvános IP-cím** automatikusan generált.<br/>**Hálózati biztonsági csoport** = None<br/>**Diagnosztikai figyelő** = engedélyezve<br/>**Diagnosztikai tárfiók** = egy automatikusan létrehozott tárfiók használata<br/>**A rendelkezésre állási csoport** = sqlAvailabilitySet<br/> |**Tárolási**: az kezelt lemezek.<br/>**Virtuális hálózati** = autoHAVNET<br/>**Alhálózati** = sqlsubnet(10.1.1.0/24)<br/>**Nyilvános IP-cím** automatikusan generált.<br/>**Hálózati biztonsági csoport** = None<br/>**Diagnosztikai figyelő** = engedélyezve<br/>**Diagnosztikai tárfiók** = egy automatikusan létrehozott tárfiók használata<br/>**A rendelkezésre állási csoport** = sqlAvailabilitySet<br/> |
| Virtuálisgép-konfiguráció **SQL Server-beállítások** |Nem alkalmazható |**SQL-kapcsolat** = Private (virtuális hálózaton belül)<br/>**Port** = 1433<br/>**SQL-hitelesítés** = letiltás<br/>**Tárolási konfiguráció** általános =<br/>**Automatikus javítás** vasárnap = 2:00<br/>**Automatikus biztonsági mentés** = letiltva</br>**Az Azure Key Vault-integráció** = letiltva |**SQL-kapcsolat** = Private (virtuális hálózaton belül)<br/>**Port** = 1433<br/>**SQL-hitelesítés** = letiltás<br/>**Tárolási konfiguráció** általános =<br/>**Automatikus javítás** vasárnap = 2:00<br/>**Automatikus biztonsági mentés** = letiltva</br>**Az Azure Key Vault-integráció** = letiltva |

<br/>

> [!NOTE]
> Az itt közölt méreteket úgy van kialakítva, a rendelkezésre állási csoportok tesztelése az Azure virtuális gépeken. A legjobb teljesítmény érdekében a termelési számítási feladatokhoz, javaslatok az SQL Server-gépek méretét és a konfigurációs [teljesítmény a bevált gyakorlat az SQL Server Azure virtuális gépek](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

Miután a három virtuális gépek teljesen kiépített, kell-e csatlakoztassa azokat a **corp.contoso.com** tartomány- és támogatási CORP\Install rendszergazdai jogosultságokkal a gépek.

### <a name="joinDomain"></a>A kiszolgálók csatlakoztatása a tartományhoz

Most már elvégezheti a virtuális gép csatlakoztatás **corp.contoso.com**. Tegye a következőket mind az SQL Server virtuális gépen, és a tanúsító fájlmegosztás kiszolgálója:

1. Távolról csatlakozni a virtuális gép **BUILTIN\DomainAdmin**.
2. A **Kiszolgálókezelő**, kattintson a **helyi kiszolgáló**.
3. Kattintson a **munkacsoport** hivatkozásra.
4. Az a **számítógépnév** kattintson **módosítása**.
5. Válassza ki a **tartomány** jelölőnégyzetet, és írja be **corp.contoso.com** a szövegmezőben. Kattintson az **OK** gombra.
6. Az a **Windows biztonsági** előugró párbeszédpanelen adja meg az alapértelmezett tartományi rendszergazdai fiók hitelesítő adatait (**CORP\DomainAdmin**) és a jelszót (**Contoso! 0000**) .
7. Kattintson a "Üdvözli a corp.contoso.com tartomány" üzenet jelenik meg, amikor **OK**.
8. Kattintson a **Bezárás**, és kattintson a **Újraindítás most** az előugró párbeszédpanelen.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Minden virtuális gép fürtön rendszergazdaként Corp\Install felhasználó hozzáadása

A tartomány tagjaként minden virtuális gép újraindítása után adja hozzá a **CORP\Install** a helyi Rendszergazdák csoport tagjaként.

1. Várjon, amíg a virtuális gép újraindul, majd indítsa el az RDP-fájl újra bejelentkezni az elsődleges tartományvezérlő **SQL Server-0** használatával a **CORP\DomainAdmin** fiók.
   >[!TIP]
   >Győződjön meg arról, hogy jelentkezzen be a tartományi rendszergazdai fiókot. Az előző lépésben a IN a beépített rendszergazdai fiókot használta. Most, hogy a kiszolgáló a tartományban, a tartományi fiókot használni. Az RDP-munkamenetbe, adja meg a *tartomány*\\*felhasználónév*.

2. A **Kiszolgálókezelő**, jelölje be **eszközök**, és kattintson a **számítógép-kezelés**.
3. Az a **számítógép-kezelés** ablakában bontsa ki a **helyi felhasználók és csoportok**, majd válassza ki **csoportok**.
4. Kattintson duplán a **rendszergazdák** csoport.
5. Az a **rendszergazdák tulajdonságok** párbeszédpanel, kattintson a **Hozzáadás** gombra.
6. Adja meg a felhasználó **CORP\Install**, és kattintson a **OK**.
7. Kattintson a **OK** bezárásához a **rendszergazda tulajdonságainak** párbeszédpanel.
8. Ismételje meg az előző lépéseket **SQL Server-1** és **fürt-fsw**.

### <a name="setServiceAccount"></a>Az SQL Server szolgáltatás-fiók beállítása

Minden egyes SQL Server virtuális gépen állítsa be az SQL Server szolgáltatásfiókja. A fiókok létrehozott mikor használják, akkor [konfigurálva a tartományi fiókok](#DomainAccounts).

1. Nyissa meg **SQL Server Konfigurációkezelő**.
2. Kattintson a jobb gombbal az SQL Server szolgáltatást, és kattintson **tulajdonságok**.
3. Állítsa be a fiók és jelszó.
4. Ismételje ezeket a lépéseket, a többi SQL Server virtuális Gépen.  

Az SQL Server rendelkezésre állási csoportok minden egyes SQL Server virtuális gép kell tartományi fiókként futtatni.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>A bejelentkezés minden SQL Server virtuális gépen a telepítési fiók létrehozása

A telepítési fiók (CORP\install) használatával a rendelkezésre állási csoport konfigurálásához. Ennek a fióknak tagjának lennie kell a **sysadmin** rögzített kiszolgálói szerepkör minden egyes SQL Server virtuális gépen. Az alábbi lépéseket a bejelentkezés a telepítési fiók létrehozása:

1. A kiszolgáló a távoli asztal protokoll (RDP) révén használatával kapcsolódnak a  *\<MachineName\>\DomainAdmin* fiók.

1. Nyissa meg az SQL Server Management Studio eszközt, és csatlakozzon az SQL Server helyi példányát.

1. A **Object Explorer**, kattintson a **biztonsági**.

1. Kattintson a jobb gombbal **bejelentkezések**. Kattintson a **új bejelentkezés**.

1. A **- új bejelentkezés**, kattintson a **keresési**.

1. Kattintson a **helyek**.

1. Adja meg a tartományi rendszergazda hálózati hitelesítő adatait.

1. A telepítési fiókot használni.

1. A bejelentkezés tagja kell beállítani a **sysadmin** rögzített kiszolgálói szerepkör.

1. Kattintson az **OK** gombra.

Ismételje meg az előző lépéseket, a többi SQL Server virtuális Gépen.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Adja hozzá a Feladatátvételi fürtszolgáltatással az mindkét SQL Server virtuális gépen

Feladatátvételi fürtszolgáltatással hozzáadásához tegye a következőket a mindkét SQL Server virtuális gépen:

1. Az SQL Server virtuális géphez a távoli asztal protokoll (RDP) révén használatával kapcsolódnak a *CORP\install* fiók. Nyissa meg **a Kiszolgálókezelő irányítópultja**.
2. Kattintson a **szerepkörök és szolgáltatások hozzáadása** az irányítópulton lévő hivatkozásra.

    ![Kiszolgálókezelő - Szerepkörök hozzáadása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Válassza ki **következő** amíg elér a **kiszolgálószolgáltatások** szakasz.
4. A **szolgáltatások**, jelölje be **feladatátvételi fürtszolgáltatás**.
5. A további szükséges szolgáltatások hozzáadását.
6. Kattintson a **telepítése** a szolgáltatások hozzáadását.

Ismételje meg a többi SQL Server virtuális Gépen.

## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall">A tűzfal konfigurálása minden egyes SQL Server virtuális gépen

A megoldás a következő TCP-portok nyitva a tűzfalon igényel:

- **SQL Server rendszerű virtuális Géphez**:<br/>
   Az SQL Server alapértelmezett példányán 1433-as port.
- **Terheléselosztói mintavétel Azure:**<br/>
   Minden elérhető port. Példák 59999 gyakran használnak.
- **Adatbázis-tükrözési végpontját:** <br/>
   Minden elérhető port. Példák 5022 gyakran használnak.

A tűzfalportok nyitva a mindkét SQL Server virtuális gépen kell.

A portok megnyitása módja attól függ, hogy a tűzfal megoldáshoz, amelyet használni. A következő szakasz ismerteti a portok megnyitása a Windows tűzfalon. Nyissa meg a szükséges portok minden egyes az SQL Server virtuális gépen.

### <a name="open-a-tcp-port-in-the-firewall"></a>Nyissa meg a TCP-portot a tűzfalon

1. Az első SQL-kiszolgálón **Start** indítsa el a jobb **fokozott biztonságú Windows tűzfal**.
2. A bal oldali panelen válassza ki a **bejövő szabályok**. Kattintson a jobb oldali ablaktáblában **új szabály**.
3. A **szabálytípus**, válassza a **Port**.
4. Adja meg a portot, **TCP** , és írja be a megfelelő portszámokat. Tekintse meg a következő példát:

   ![SQL-tűzfal](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Kattintson a **Tovább** gombra.
6. Az a **művelet** lapon, tartsa **a kapcsolat engedélyezéséhez** kiválasztva, és kattintson **következő**.
7. Az a **profil** lapon fogadja el az alapértelmezett beállításokat, és kattintson a **következő**.
8. A a **neve** csoportjában adja meg a szabály neve (például **Azure LB mintavételi**) az a **neve** szövegmezőbe, és kattintson **Befejezés**.

Ismételje ezeket a lépéseket, a második SQL Server virtuális Gépen.

## <a name="next-steps"></a>Következő lépések

* [Azure virtuális gépeken futó SQL Server Always On rendelkezésre állási csoport létrehozása](virtual-machines-windows-portal-sql-availability-group-tutorial.md)
