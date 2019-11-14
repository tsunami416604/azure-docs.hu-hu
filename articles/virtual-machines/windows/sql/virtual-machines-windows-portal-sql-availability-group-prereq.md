---
title: 'Oktatóanyag: rendelkezésre állási csoport előfeltételei'
description: Ez az oktatóanyag bemutatja, hogyan konfigurálhatja a SQL Server always on rendelkezésre állási csoport létrehozásához szükséges előfeltételeket az Azure-beli virtuális gépeken.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.custom: seo-lt-2019
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mikeray
ms.openlocfilehash: 17b1f58a950f2e0589986e9f1da1295671599341
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037476"
---
# <a name="prerequisites-for-creating-always-on-availability-groups-on-sql-server-on-azure-virtual-machines"></a>Always On rendelkezésre állási csoportok létrehozásának előfeltételei az Azure-beli virtuális gépeken SQL Server

Ez az oktatóanyag bemutatja, hogyan végezheti el az előfeltételeket az [Azure Virtual Machines (VM) SQL Server always on rendelkezésre állási csoport](virtual-machines-windows-portal-sql-availability-group-tutorial.md)létrehozásához. Ha befejezte az előfeltételeket, rendelkezik egy tartományvezérlővel, két SQL Server virtuális géppel és egy tanúsító kiszolgálóval egyetlen erőforráscsoporthoz.

**Becsült idő**: az előfeltételek teljesítéséhez több óráig is eltarthat. Az idő nagy részében a virtuális gépek létrehozása történik.

Az alábbi ábra az oktatóanyagban felépített tudnivalókat mutatja be.

![Rendelkezésre állási csoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>A rendelkezésre állási csoport dokumentációjának áttekintése

Ez az oktatóanyag feltételezi, hogy rendelkezik a SQL Server always on rendelkezésre állási csoportokkal kapcsolatos alapvető ismeretekkel. Ha még nem ismeri ezt a technológiát, tekintse [meg az Always On rendelkezésre állási csoportok áttekintése (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx)című témakört.


## <a name="create-an-azure-account"></a>Azure-fiók létrehozása
Rendelkeznie kell Azure-fiókkal. [Megnyithat egy ingyenes Azure-fiókot](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic&redirectURL=https:%2F%2Fazure.microsoft.com%2Fget-started%2Fwelcome-to-azure%2F&correlationId=24f9d452-1909-40d7-b609-2245aa7351a6&l=en-US) , vagy [aktiválhatja a Visual Studio előfizetői előnyeit](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **+** gombra egy új objektum létrehozásához a portálon.

   ![Új objektum](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Írja be az **erőforráscsoport** kifejezést a **piactér** keresési ablakába.

   ![Erőforráscsoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Kattintson az **erőforráscsoport**elemre.
5. Kattintson a **Létrehozás** gombra.
6. Az **erőforráscsoport neve**alatt adja meg az erőforráscsoport nevét. Írja be például az **SQL-ha-RG**értéket.
7. Ha több Azure-előfizetéssel rendelkezik, ellenőrizze, hogy az előfizetés az Azure-előfizetés, amelyben létre kívánja hozni a rendelkezésre állási csoportot.
8. Válasszon ki egy helyet. A hely az az Azure-régió, ahol létre szeretné hozni a rendelkezésre állási csoportot. Ez a cikk egy Azure-beli hely összes erőforrását felépíti.
9. Ellenőrizze, hogy be van-e jelölve **a rögzítés az irányítópulton** jelölőnégyzet. Ez a választható beállítás a Azure Portal irányítópulton helyezi el az erőforráscsoport parancsikonját.

   ![Erőforráscsoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Az erőforráscsoport létrehozásához kattintson a **Létrehozás** gombra.

Az Azure létrehozza az erőforráscsoportot, és a portálon egy parancsikont helyez el az erőforráscsoporthoz.

## <a name="create-the-network-and-subnets"></a>A hálózat és az alhálózatok létrehozása
A következő lépés a hálózatok és alhálózatok létrehozása az Azure-erőforráscsoporthoz.

A megoldás egy virtuális hálózatot használ két alhálózattal. A [virtuális hálózat áttekintése](../../../virtual-network/virtual-networks-overview.md) további információkat nyújt az Azure-beli hálózatokról.

A virtuális hálózat létrehozása:

1. A Azure Portal az erőforráscsoport területén kattintson a **+ Hozzáadás**elemre. 

   ![Új tétel](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Keressen rá a **virtuális hálózatra**.

     ![Virtuális hálózat keresése](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Kattintson a **virtuális hálózat**elemre.
4. A **virtuális hálózaton**kattintson a **Resource Manager** -alapú üzemi modellre, majd kattintson a **Létrehozás**gombra.

    A következő táblázat a virtuális hálózat beállításait mutatja be:

   | **Mező** | Érték |
   | --- | --- |
   | **Name (Név)** |autoHAVNET |
   | **Címtér** |10.33.0.0/24 |
   | **Alhálózat neve** |rendszergazda |
   | **Alhálózati címtartomány** |10.33.0.0/29 |
   | **Előfizetés** |Itt adhatja meg a használni kívánt előfizetést. Az **előfizetés** üres, ha csak egy előfizetéssel rendelkezik. |
   | **Erőforráscsoport** |Válassza a **meglévő használata** lehetőséget, és válassza ki az erőforráscsoport nevét. |
   | **Hely** |Itt adhatja meg az Azure helyét. |

   A Címterület és az alhálózati címtartomány eltérő lehet a táblából. Az előfizetéstől függően a portálon elérhető címtartomány és a hozzá tartozó alhálózat-címtartomány is megjelenik. Ha nem áll rendelkezésre elegendő címtartomány, használjon másik előfizetést.

   A példa az alhálózati név **rendszergazdáját**használja. Ez az alhálózat a tartományvezérlők számára érhető el.

5. Kattintson a **Létrehozás** gombra.

   ![A virtuális hálózat konfigurálása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Az Azure visszatér a portál irányítópultra, és értesítést küld az új hálózat létrehozásakor.

### <a name="create-a-second-subnet"></a>Második alhálózat létrehozása
Az új virtuális hálózat egy **rendszergazda**nevű alhálózattal rendelkezik. A tartományvezérlők ezt az alhálózatot használják. A SQL Server virtuális gépek egy másik, **SQL**nevű alhálózatot használnak. Az alhálózat konfigurálása:

1. Az irányítópulton kattintson a létrehozott erőforráscsoport, az **SQL-ha-RG**elemre. Keresse meg a hálózatot az **erőforrás csoportban az erőforráscsoport területen.**

    Ha az **SQL-ha-RG** nem látható, a megkereséséhez kattintson az **erőforráscsoportok** elemre, és válassza az erőforráscsoport neve alapján történő szűrést.
2. Kattintson a **autoHAVNET** elemre az erőforrások listájában. 
3. A **autoHAVNET** virtuális hálózat **Beállítások** területén válassza az **alhálózatok**lehetőséget.

    Jegyezze fel a már létrehozott alhálózatot.

   ![A virtuális hálózat konfigurálása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Hozzon létre egy második alhálózatot. Kattintson az **+ alhálózat**elemre.
6. Az **alhálózat hozzáadása**területen konfigurálja az alhálózatot úgy, hogy beírja a **sqlsubnet** **nevet**. Az Azure automatikusan megadja a **címtartomány érvényes tartományát**. Győződjön meg arról, hogy ez a címtartomány legalább 10 címmel rendelkezik. Éles környezetben több címet is igényelhet.
7. Kattintson az **OK** gombra.

    ![A virtuális hálózat konfigurálása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

A következő táblázat összefoglalja a hálózati konfiguráció beállításait:

| **Mező** | Érték |
| --- | --- |
| **Name (Név)** |**autoHAVNET** |
| **Címtér** |Ez az érték az előfizetés elérhető címeitől függ. Egy tipikus érték a 10.0.0.0/16. |
| **Alhálózat neve** |**admin** |
| **Alhálózati címtartomány** |Ez az érték az előfizetés elérhető címeitől függ. Egy tipikus érték a 10.0.0.0/24. |
| **Alhálózat neve** |**sqlsubnet** |
| **Alhálózati címtartomány** |Ez az érték az előfizetés elérhető címeitől függ. Egy tipikus érték a 10.0.1.0/24. |
| **Előfizetés** |Itt adhatja meg a használni kívánt előfizetést. |
| **Erőforráscsoport** |**SQL-HA-RG** |
| **Hely** |Ugyanazt a helyet kell megadnia, amelyet az erőforráscsoport számára választott. |

## <a name="create-availability-sets"></a>Rendelkezésre állási csoportok létrehozása

A virtuális gépek létrehozása előtt létre kell hoznia a rendelkezésre állási csoportokat. A rendelkezésre állási csoportok csökkentik a tervezett vagy nem tervezett karbantartási események leállását. Az Azure-beli rendelkezésre állási csoport az erőforrások olyan logikai csoportja, amelyet az Azure fizikai tartalék tartományokon és frissítési tartományokon helyez el. A tartalék tartomány biztosítja, hogy a rendelkezésre állási csoport tagjai külön energiaellátási és hálózati erőforrásokkal rendelkezzenek. A frissítési tartomány biztosítja, hogy a rendelkezésre állási csoport tagjai ne álljanak le egyszerre karbantartásra. További információ: [a virtuális gépek rendelkezésre állásának kezelése](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Két rendelkezésre állási csoportra van szüksége. Az egyik a tartományvezérlők számára. A második a SQL Server virtuális gépek esetében.

Rendelkezésre állási csoport létrehozásához nyissa meg az erőforráscsoportot, és kattintson a **Hozzáadás**gombra. Az eredmények szűréséhez írja **be a rendelkezésre állási csoport**kifejezést. Az eredmények között kattintson a **rendelkezésre állási csoport** elemre, majd kattintson a **Létrehozás**gombra.

Konfigurálja a két rendelkezésre állási csoportot a következő táblázatban szereplő paramétereknek megfelelően:

| **Mező** | Tartományvezérlő rendelkezésre állási készlete | Rendelkezésre állási csoport SQL Server |
| --- | --- | --- |
| **Name (Név)** |adavailabilityset |sqlavailabilityset |
| **Erőforráscsoport** |SQL-HA-RG |SQL-HA-RG |
| **Tartalék tartományok** |3 |3 |
| **Tartományok frissítése** |5 |3 |

A rendelkezésre állási csoportok létrehozása után térjen vissza az erőforráscsoporthoz a Azure Portal.

## <a name="create-domain-controllers"></a>Tartományvezérlők létrehozása
A hálózat, az alhálózatok, a rendelkezésre állási készletek és az internetkapcsolattal rendelkező terheléselosztó létrehozása után készen áll a tartományvezérlők virtuális gépei létrehozására.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Virtuális gépek létrehozása a tartományvezérlők számára
A tartományvezérlők létrehozásához és konfigurálásához térjen vissza az **SQL-ha-RG** erőforráscsoporthoz.

1. Kattintson az **Hozzáadás** parancsra. 
2. Írja be a **Windows Server 2016 datacentert**.
3. Kattintson a **Windows Server 2016 Datacenter**elemre. A **Windows Server 2016 Datacenter**rendszerben ellenőrizze, hogy a telepítési modell **Resource Manager**-e, majd kattintson a **Létrehozás**gombra. 

Két virtuális gép létrehozásához ismételje meg a fenti lépéseket. Nevezze el a két virtuális gépet:

* ad-primary-dc
* ad-secondary-dc

  > [!NOTE]
  > Az **ad-másodlagos-tartományvezérlő** virtuális gép nem kötelező, hogy magas rendelkezésre állást biztosítson Active Directory tartományi szolgáltatások számára.
  >
  >

A következő táblázat a két gép beállításait mutatja be:

| **Mező** | Érték |
| --- | --- |
| **Name (Név)** |Első tartományvezérlő: *ad-Primary-DC*.</br>Második tartományvezérlő *ad-másodlagos-tartományvezérlő*. |
| **Virtuális merevlemez típusa** |SSD |
| **Felhasználónév** |DomainAdmin |
| **Jelszó** |Contoso!0000 |
| **Előfizetés** |*Az Ön előfizetése* |
| **Erőforráscsoport** |SQL-HA-RG |
| **Hely** |*Tartózkodási hely* |
| **Méret** |DS1_V2 |
| **Tárolás** | **Felügyelt lemezek használata** - **Igen** |
| **Virtuális hálózat** |autoHAVNET |
| **Alhálózat** |felügyeleti |
| **Nyilvános IP-cím** |*A virtuális géppel megegyező név* |
| **Hálózati biztonsági csoport** |*A virtuális géppel megegyező név* |
| **Rendelkezésre állási csoport** |adavailabilityset </br>Tartalék **tartományok**: 2 </br>**Frissítési tartományok**: 2|
| **Diagnosztika** |Engedélyezve |
| **Diagnosztikai Storage-fiók** |*Automatikusan létrehozva* |

   >[!IMPORTANT]
   >A virtuális gépeket csak akkor helyezheti üzembe a rendelkezésre állási csoportba, ha létrehozza azt. A rendelkezésre állási csoport a virtuális gép létrehozása után nem módosítható. Lásd: [virtuális gépek rendelkezésre állásának kezelése](../manage-availability.md).

Az Azure létrehozza a virtuális gépeket.

A virtuális gépek létrehozása után konfigurálja a tartományvezérlőt.

### <a name="configure-the-domain-controller"></a>A tartományvezérlő konfigurálása
A következő lépésekben konfigurálja az **ad-Primary-DC** gépet a Corp.contoso.com tartományvezérlőként.

1. A portálon nyissa meg az **SQL-ha-RG** erőforráscsoportot, és válassza ki az **ad-Primary-DC** gépet. Az **ad-Primary-DC-** ben kattintson a **Kapcsolódás** lehetőségre egy RDP-fájl megnyitásához a távoli asztal eléréséhez.

    ![Csatlakozás virtuális géphez](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Jelentkezzen be a konfigurált rendszergazdai fiókjával ( **\DomainAdmin**) és jelszavával (**contoso! 0000**).
3. Alapértelmezés szerint a **Kiszolgálókezelő** irányítópultjának kell megjelennie.
4. Kattintson a **szerepkörök és szolgáltatások hozzáadása** hivatkozásra az irányítópulton.

    ![Kiszolgálókezelő – Szerepkörök hozzáadása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Kattintson a **tovább** gombra, amíg el nem jut a **kiszolgálói szerepkörök** szakaszhoz.
6. Válassza ki a **Active Directory tartományi szolgáltatások** és a **DNS-kiszolgálói** szerepköröket. Ha a rendszer kéri, adja meg a szerepkörökhöz szükséges további szolgáltatásokat.

   > [!NOTE]
   > A Windows arra figyelmeztet, hogy nincs statikus IP-cím. Ha teszteli a konfigurációt, kattintson a **Continue (folytatás**) gombra. Éles környezetekben állítsa az IP-címet statikusra a Azure Portalban, vagy a [PowerShell használatával állítsa be a tartományvezérlő számítógépének statikus IP-címét](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Szerepkörök hozzáadása párbeszédpanel](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Kattintson a **tovább** gombra, amíg el nem éri a **megerősítő** szakaszt. Jelölje be a **célkiszolgáló automatikus újraindítása, ha szükséges** jelölőnégyzetet.
8. Kattintson az **Install** (Telepítés) gombra.
9. A szolgáltatások telepítésének befejezését követően térjen vissza a **Kiszolgálókezelő** irányítópultra.
10. Válassza az új **AD DS** lehetőséget a bal oldali ablaktáblán.
11. Kattintson a sárga figyelmeztető sáv **további** hivatkozására.

    ![AD DS párbeszédpanel a DNS-kiszolgáló virtuális gépén](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. A **minden kiszolgáló feladat részletei** párbeszédpanel **művelet** oszlopában kattintson a **kiszolgáló előléptetése tartományvezérlővé**elemre.
13. A **Active Directory tartományi szolgáltatások konfigurációs varázslóban**a következő értékeket használja:

    | **Oldalala** | Beállítás |
    | --- | --- |
    | **Központi telepítés konfigurálása** |**Új erdő hozzáadása**<br/> **Gyökértartomány neve** = Corp.contoso.com |
    | **Tartományvezérlő beállításai** |**Címtárszolgáltatások helyreállító módjának jelszava** = contoso! 0000<br/>**Jelszó megerősítése** = contoso! 0000 |
14. Kattintson a **tovább** gombra a varázsló többi lapjának átlépéséhez. Az **Előfeltételek ellenőrzése** lapon győződjön meg arról, hogy a következő üzenet jelenik meg: az **összes előfeltétel-ellenőrzés sikeresen átadva**. Áttekintheti a vonatkozó figyelmeztető üzeneteket, de lehetséges, hogy folytathatja a telepítést.
15. Kattintson az **Install** (Telepítés) gombra. Az **ad-Primary-DC** virtuális gép automatikusan újraindul.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Jegyezze fel az elsődleges tartományvezérlő IP-címét.

Használja a DNS elsődleges tartományvezérlőjét. Jegyezze fel az elsődleges tartományvezérlő IP-címét.

Az elsődleges tartományvezérlő IP-címének lekérésének egyik módja a Azure Portalon keresztül történik.

1. A Azure Portal nyissa meg az erőforráscsoportot.

2. Kattintson az elsődleges tartományvezérlőre.

3. Az elsődleges tartományvezérlőn kattintson a **hálózati adapterek**elemre.

![Hálózati illesztők](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Jegyezze fel a kiszolgáló magánhálózati IP-címét.

### <a name="configure-the-virtual-network-dns"></a>A virtuális hálózat DNS-beli konfigurálása
Miután létrehozta az első tartományvezérlőt, és engedélyezte a DNS-t az első kiszolgálón, konfigurálja a virtuális hálózatot a DNS-kiszolgáló használatára.

1. A Azure Portal kattintson a virtuális hálózatra.

2. A **Beállítások**területen kattintson a **DNS-kiszolgáló**elemre.

3. Kattintson az **Egyéni**elemre, és írja be az elsődleges tartományvezérlő magánhálózati IP-címét.

4. Kattintson a **Save** (Mentés) gombra.

### <a name="configure-the-second-domain-controller"></a>A második tartományvezérlő konfigurálása
Az elsődleges tartományvezérlő újraindítása után beállíthatja a második tartományvezérlőt. Ez a választható lépés a magas rendelkezésre állás. A második tartományvezérlő konfigurálásához kövesse az alábbi lépéseket:

1. A portálon nyissa meg az **SQL-ha-RG** erőforráscsoportot, és válassza ki az **ad-szekunder-DC** gépet. Az **ad-másodlagos-tartományvezérlőn**kattintson a **Kapcsolódás** elemre a távoli asztal eléréséhez szükséges RDP-fájl megnyitásához.
2. Jelentkezzen be a virtuális gépre a beállított rendszergazdai fiók (**BUILTIN\DomainAdmin**) és a jelszó (**contoso! 0000**) használatával.
3. Módosítsa az előnyben részesített DNS-kiszolgáló címeit a tartományvezérlő címeként.
4. A **hálózati és megosztási központban**kattintson a hálózati adapterre.
   ![Hálózati illesztő](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Kattintson a **Tulajdonságok** elemre.
6. Válassza a **Internet Protocol 4-es verzió (TCP/IPv4)** lehetőséget, és kattintson a **Tulajdonságok**elemre.
7. Válassza **a következő DNS-kiszolgáló címek használata** lehetőséget, és adja meg az elsődleges tartományvezérlő címét az **ELŐNYben részesített DNS-kiszolgálón**.
8. Kattintson **az OK gombra**, majd a **Bezárás** gombra a módosítások elvégzéséhez. Most már tud csatlakozni a virtuális géphez a **Corp.contoso.com**.

   >[!IMPORTANT]
   >Ha a DNS-beállítás módosítása után elveszíti a távoli asztal kapcsolatát, lépjen a Azure Portal, és indítsa újra a virtuális gépet.

9. A Távoli asztalról a másodlagos tartományvezérlőre nyissa meg a **Kiszolgálókezelő irányítópultját**.
10. Kattintson a **szerepkörök és szolgáltatások hozzáadása** hivatkozásra az irányítópulton.

    ![Kiszolgálókezelő – Szerepkörök hozzáadása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Kattintson a **tovább** gombra, amíg el nem jut a **kiszolgálói szerepkörök** szakaszhoz.
12. Válassza ki a **Active Directory tartományi szolgáltatások** és a **DNS-kiszolgálói** szerepköröket. Ha a rendszer kéri, adja meg a szerepkörökhöz szükséges további szolgáltatásokat.
13. A szolgáltatások telepítésének befejezését követően térjen vissza a **Kiszolgálókezelő** irányítópultra.
14. Válassza az új **AD DS** lehetőséget a bal oldali ablaktáblán.
15. Kattintson a sárga figyelmeztető sáv **további** hivatkozására.
16. A **minden kiszolgáló feladat részletei** párbeszédpanel **művelet** oszlopában kattintson a **kiszolgáló előléptetése tartományvezérlővé**elemre.
17. A **központi telepítés konfigurálása**területen válassza **a tartományvezérlő hozzáadása meglévő tartományhoz**lehetőséget.
    ![telepítési konfiguráció](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Kattintson a **Kiválasztás** gombra.
19. Kapcsolódjon a rendszergazdai fiók használatával (**Corp. CONTOSO. COM\domainadmin**) és jelszó (**contoso! 0000**).
20. A **tartomány kiválasztása az erdőből**területen kattintson a tartomány elemre, majd az **OK**gombra.
21. A **tartományvezérlő beállításainál**használja az alapértelmezett értékeket, és állítsa be a Címtárszolgáltatások helyreállító módjának jelszavát.

    >[!NOTE]
    >Előfordulhat, hogy a **DNS-beállítások** lap figyelmezteti, hogy nem hozható létre delegálás ehhez a DNS-kiszolgálóhoz. Ezt a figyelmeztetést nem éles környezetben is figyelmen kívül hagyhatja.
22. Kattintson a **tovább** gombra, amíg a párbeszédpanel el nem éri az **Előfeltételek** ellenőrzését. Ezt követően kattintson a **Telepítés** gombra.

Miután a kiszolgáló befejezte a konfigurációs módosításokat, indítsa újra a kiszolgálót.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Adja hozzá a magánhálózati IP-címet a második tartományvezérlőhöz a VPN DNS-kiszolgálóhoz

A Azure Portal virtuális hálózat területén módosítsa a DNS-kiszolgálót úgy, hogy az tartalmazza a másodlagos tartományvezérlő IP-címét. Ezzel a beállítással engedélyezhető a DNS-szolgáltatás redundancia.

### <a name="DomainAccounts"></a>A tartományi fiókok konfigurálása

A következő lépésekben konfigurálja a Active Directory fiókokat. A következő táblázat a fiókokat mutatja be:

| |Telepítési fiók<br/> |sqlserver-0 <br/>SQL Server és SQL Agent-szolgáltatásfiók |sqlserver-1<br/>SQL Server és SQL Agent-szolgáltatásfiók
| --- | --- | --- | ---
|**Keresztnév** |Telepítés |SQLSvc1 | SQLSvc2
|**Felhasználói SamAccountName** |Telepítés |SQLSvc1 | SQLSvc2

Az egyes fiókok létrehozásához kövesse az alábbi lépéseket.

1. Jelentkezzen be az **ad-Primary-DC** gépre.
2. A **Kiszolgálókezelőben**válassza az **eszközök**, majd a **Active Directory felügyeleti központ**lehetőséget.   
3. Válassza a **Corp (helyi)** elemet a bal oldali ablaktáblán.
4. A jobb oldali **feladatok** ablaktáblán válassza az **új**, majd a **felhasználó**lehetőséget.
   ![Active Directory felügyeleti központ](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Minden fiókhoz állítson be egy összetett jelszót.<br/> Nem éles környezetekben állítsa be a felhasználói fiókot, hogy soha ne járjon le.

5. A felhasználó létrehozásához kattintson **az OK** gombra.
6. Ismételje meg az előző lépéseket mindhárom fiók esetében.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>A szükséges engedélyek megadása a telepítési fiók számára
1. A **Active Directory felügyeleti központ**válassza a **Corp (helyi)** elemet a bal oldali ablaktáblán. Ezután a jobb oldali **feladatok** ablaktáblán kattintson a **Tulajdonságok**elemre.

    ![CORP-felhasználó tulajdonságai](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Válassza a **bővítmények**lehetőséget, majd kattintson a **speciális** gombra a **Biztonság** lapon.
3. A **Corp speciális biztonsági beállításai** párbeszédpanelen kattintson a **Hozzáadás**gombra.
4. Kattintson **a rendszerbiztonsági tag kiválasztása**elemre, keresse meg a **CORP\Install**, majd kattintson **az OK**gombra.
5. Jelölje be az **összes tulajdonság olvasása** jelölőnégyzetet.

6. Jelölje be a **számítógép-objektumok létrehozása** jelölőnégyzetet.

     ![Corp felhasználói engedélyek](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Kattintson **az OK**gombra, majd kattintson ismét **az OK** gombra. A **Corp** Properties ablak bezárásához.

Most, hogy befejezte a Active Directory és a felhasználói objektumok konfigurálását, hozzon létre két SQL Server virtuális gépet és egy tanúsító kiszolgáló virtuális gépet. Ezután csatlakozzon mindhárom tartományhoz.

## <a name="create-sql-server-vms"></a>SQL Server virtuális gépek létrehozása

Hozzon létre három további virtuális gépet. A megoldáshoz két virtuális gép szükséges SQL Server példányokkal. Egy harmadik virtuális gép tanúsító fog működni. A Windows Server 2016 képes [Felhőbeli tanúsító](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)használatára, azonban a korábbi operációs rendszerekkel való konzisztencia érdekében ez a dokumentum egy tanúsító virtuális gépet használ.  

Az alábbi tervezési döntések meghozatala előtt tekintse át a következő lépéseket.

* **Storage – Azure Managed Disks**

   A virtuális gép tárolójában használja az Azure Managed Disks. A Microsoft a SQL Server virtuális gépek Managed Disksét javasolja. A Managed Disks szolgáltatás a háttérben kezeli a tárterületet. Emellett ha ugyanabban a rendelkezésre állási csoportban több, a Managed Diskset használó virtuális gép található, az Azure elosztja a tárolási erőforrásokat, hogy megfelelő redundanciát biztosítson. További információkért lásd az [Azure Managed Disks áttekintését](../managed-disks-overview.md). A felügyelt lemezekkel kapcsolatos részletekért tekintse meg a [rendelkezésre állási csoportokban található virtuális gépek Managed Disks használatát](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)ismertető témakört.

* **Hálózat – magánhálózati IP-címek az éles környezetben**

   A virtuális gépek esetében ez az oktatóanyag nyilvános IP-címeket használ. A nyilvános IP-cím lehetővé teszi, hogy a távoli kapcsolat közvetlenül a virtuális géphez legyen elérhető az interneten keresztül – megkönnyíti a konfigurációs lépéseket. Éles környezetekben a Microsoft csak a magánhálózati IP-címeket javasolja, hogy csökkentse a SQL Server példány virtuálisgép-erőforrásának sebezhetőségi lábnyomát.

### <a name="create-and-configure-the-sql-server-vms"></a>A SQL Server virtuális gépek létrehozása és konfigurálása
Ezután hozzon létre három virtuális gépet – két SQL Server virtuális gépet és egy virtuális gépet egy további fürtcsomópont számára. Az egyes virtuális gépek létrehozásához lépjen vissza az **SQL-ha-RG** erőforráscsoporthoz, kattintson a **Hozzáadás**gombra, keresse meg a megfelelő gyűjteményt, és kattintson a **virtuális gép**elemre, majd kattintson **a**katalógusból lehetőségre. A következő táblázatban található információk segítségével hozhatja létre a virtuális gépeket:


| Oldal | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Válassza ki a megfelelő gyűjtemény elemet |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise Windows Server 2016 rendszeren** |**SQL Server 2016 SP1 Enterprise Windows Server 2016 rendszeren** |
| A virtuális gép konfigurációjának **alapjai** |**Név** = fürt – FSW<br/>**Felhasználónév** = rdfe<br/>**Password** = contoso! 0000<br/>**Előfizetés** = az előfizetése<br/>**Erőforráscsoport** = SQL-ha-RG<br/>**Location** = az Azure-beli helye |**Név** = SQLServer-0<br/>**Felhasználónév** = rdfe<br/>**Password** = contoso! 0000<br/>**Előfizetés** = az előfizetése<br/>**Erőforráscsoport** = SQL-ha-RG<br/>**Location** = az Azure-beli helye |**Név** = SQLServer-1<br/>**Felhasználónév** = rdfe<br/>**Password** = contoso! 0000<br/>**Előfizetés** = az előfizetése<br/>**Erőforráscsoport** = SQL-ha-RG<br/>**Location** = az Azure-beli helye |
| Virtuális gép konfigurációjának **mérete** |**Size** = DS1\_v2 (1 vCPU, 3,5 GB) |**Size** = DS2\_v2 (2 vCPU, 7 GB)</br>A méretnek támogatnia kell az SSD-tárolót (prémium szintű lemezes támogatás). )) |**Size** = DS2\_v2 (2 vCPU, 7 GB) |
| Virtuális gép konfigurációs **beállításai** |**Storage**: felügyelt lemezek használata.<br/>**Virtuális hálózat** = autoHAVNET<br/>**Alhálózat** = sqlsubnet (10.1.1.0/24)<br/>A **nyilvános IP-cím** automatikusan létrejön.<br/>**Hálózati biztonsági csoport** = nincs<br/>**Figyelési diagnosztika** = engedélyezve<br/>**Diagnosztikai Storage-fiók** = automatikusan létrehozott Storage-fiók használata<br/>**Rendelkezésre állási csoport** = sqlAvailabilitySet<br/> |**Storage**: felügyelt lemezek használata.<br/>**Virtuális hálózat** = autoHAVNET<br/>**Alhálózat** = sqlsubnet (10.1.1.0/24)<br/>A **nyilvános IP-cím** automatikusan létrejön.<br/>**Hálózati biztonsági csoport** = nincs<br/>**Figyelési diagnosztika** = engedélyezve<br/>**Diagnosztikai Storage-fiók** = automatikusan létrehozott Storage-fiók használata<br/>**Rendelkezésre állási csoport** = sqlAvailabilitySet<br/> |**Storage**: felügyelt lemezek használata.<br/>**Virtuális hálózat** = autoHAVNET<br/>**Alhálózat** = sqlsubnet (10.1.1.0/24)<br/>A **nyilvános IP-cím** automatikusan létrejön.<br/>**Hálózati biztonsági csoport** = nincs<br/>**Figyelési diagnosztika** = engedélyezve<br/>**Diagnosztikai Storage-fiók** = automatikusan létrehozott Storage-fiók használata<br/>**Rendelkezésre állási csoport** = sqlAvailabilitySet<br/> |
| A virtuális gép konfigurációjának **SQL Server beállításai** |Nem alkalmazható |**SQL-kapcsolat** = Private (Virtual Networkon belül)<br/>**Port** = 1433<br/>**SQL-hitelesítés** = letiltás<br/>**Tárolási konfiguráció** = általános<br/>**Automatikus javítás** = vasárnap 2:00-kor<br/>**Automatikus biztonsági mentés** = letiltva</br>**Azure Key Vault integráció** = letiltva |**SQL-kapcsolat** = Private (Virtual Networkon belül)<br/>**Port** = 1433<br/>**SQL-hitelesítés** = letiltás<br/>**Tárolási konfiguráció** = általános<br/>**Automatikus javítás** = vasárnap 2:00-kor<br/>**Automatikus biztonsági mentés** = letiltva</br>**Azure Key Vault integráció** = letiltva |

<br/>

> [!NOTE]
> Az itt javasolt gépi méretek a rendelkezésre állási csoportok tesztelésére szolgálnak az Azure-beli virtuális gépeken. Az éles számítási feladatokhoz szükséges legjobb teljesítmény érdekében tekintse meg a SQL Server gépek méretének és konfigurálásának javaslatait az [Azure-beli virtuális gépeken történő SQL Server teljesítményének bevált eljárásaiban](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

A három virtuális gép teljes kiépítés után csatlakoztatnia kell őket a **Corp.contoso.com** tartományhoz, és a CORP\Install rendszergazdai jogosultságokat kell biztosítania a gépekhez.

### <a name="joinDomain"></a>A kiszolgálók csatlakoztatása a tartományhoz

Most már csatlakoztathatja a virtuális gépeket a **Corp.contoso.com**-hez. Hajtsa végre a következő lépéseket a SQL Server virtuális gépek és a tanúsító fájlmegosztás kiszolgálója között:

1. Távolról csatlakozhat a virtuális géphez a **BUILTIN\DomainAdmin**használatával.
2. A **Kiszolgálókezelőben**kattintson a **helyi kiszolgáló**elemre.
3. Kattintson a **munkacsoport** hivatkozásra.
4. A **számítógép neve** szakaszban kattintson a **módosítás**gombra.
5. Jelölje be a **tartomány** jelölőnégyzetet, és írja be a **Corp.contoso.com** szöveget a szövegmezőbe. Kattintson az **OK** gombra.
6. A **Windows biztonsági** előugró ablakban határozza meg az alapértelmezett tartományi rendszergazdai fiók (**CORP\DomainAdmin**) és a jelszó (**contoso! 0000**) hitelesítő adatait.
7. Amikor megjelenik az "üdvözli a corp.contoso.com tartomány" üzenet, kattintson az **OK**gombra.
8. Kattintson a **Bezárás**gombra, majd kattintson az **Újraindítás most** lehetőségre az előugró ablakban.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>A Corp\Install-felhasználó hozzáadása rendszergazdaként az egyes fürtökön futó virtuális gépeken

Miután minden virtuális gép újraindult a tartomány tagjaként, adja hozzá a **CORP\Install** -t a helyi Rendszergazdák csoport tagjaként.

1. Várjon, amíg a virtuális gép újraindul, majd indítsa el újra az RDP-fájlt az elsődleges tartományvezérlőről, hogy bejelentkezzen a **SQLServer-0** értékre a **CORP\DomainAdmin** -fiók használatával.
   >[!TIP]
   >Győződjön meg arról, hogy a tartományi rendszergazda fiókkal jelentkezik be. Az előző lépésekben a beépített rendszergazdai fiókot használta. Most, hogy a kiszolgáló a tartományban van, használja a tartományi fiókot. Az RDP-munkamenetben adja meg a *tartomány*\\*felhasználónevet*.

2. A **Kiszolgálókezelőben**válassza az **eszközök**, majd a számítógép- **kezelés**elemet.
3. A **Számítógép-kezelés** ablakban bontsa ki a **helyi felhasználók és csoportok**csomópontot, majd válassza a **csoportok**lehetőséget.
4. Kattintson duplán a **rendszergazdák** csoportra.
5. A **rendszergazdák tulajdonságai** párbeszédpanelen kattintson a **Hozzáadás** gombra.
6. Adja meg a felhasználó **CORP\Install**, majd kattintson **az OK**gombra.
7. A **rendszergazda tulajdonságok** párbeszédpanel bezárásához kattintson **az OK** gombra.
8. Ismételje meg az előző lépéseket a **SQLServer-1** és a **cluster-FSW**.

### <a name="setServiceAccount"></a>A SQL Server-szolgáltatásfiókok beállítása

Az egyes SQL Server VMeken állítsa be a SQL Server szolgáltatásfiókot. A tartományi fiókok konfigurálásakor létrehozott fiókokat használja.

1. Nyissa meg **SQL Server konfigurációkezelő**.
2. Kattintson a jobb gombbal a SQL Server szolgáltatásra, majd kattintson a **Tulajdonságok**elemre.
3. Állítsa be a fiókot és a jelszót.
4. Ismételje meg ezeket a lépéseket a többi SQL Server VM.  

SQL Server rendelkezésre állási csoportok esetében minden SQL Server VM tartományi fiókként kell futnia.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Bejelentkezés létrehozása minden SQL Server VM a telepítési fiókhoz

A rendelkezésre állási csoport konfigurálásához használja a telepítési fiókot (CORP\install). Ennek a fióknak a **sysadmin (rendszergazda** ) rögzített kiszolgálói szerepkör tagjának kell lennie minden SQL Server VMon. A következő lépésekkel hozzon létre egy bejelentkezési fiókot a telepítési fiókhoz:

1. Kapcsolódjon a kiszolgálóhoz a RDP protokollon (RDP) keresztül a *\<számítógépnév\>\DomainAdmin* fiók használatával.

1. Nyissa meg SQL Server Management Studio, és kapcsolódjon a SQL Server helyi példányához.

1. A **Object Explorer**kattintson a **Biztonság**elemre.

1. Kattintson a jobb gombbal a **bejelentkezések**elemre. Kattintson az **új bejelentkezés**lehetőségre.

1. A **Bejelentkezés – új**területen kattintson a **Keresés**gombra.

1. Kattintson a **helyszínek**elemre.

1. Adja meg a tartományi rendszergazda hálózati hitelesítő adatait.

1. Használja a telepítési fiókot.

1. A bejelentkezést úgy állítsa be, hogy a sysadmin ( **rendszergazda** ) rögzített kiszolgálói szerepkör tagja legyen.

1. Kattintson az **OK** gombra.

Ismételje meg a fenti lépéseket a másik SQL Server VM.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Feladatátvételi fürtszolgáltatási funkciók hozzáadása SQL Server virtuális gépekhez

Feladatátvételi fürtszolgáltatási funkciók hozzáadásához hajtsa végre a következő lépéseket mindkét SQL Server virtuális gépen:

1. Kapcsolódjon a SQL Server virtuális géphez a RDP protokoll (RDP) segítségével a *CORP\install* fiók használatával. Nyissa meg a **Kiszolgálókezelő irányítópultját**.
2. Kattintson a **szerepkörök és szolgáltatások hozzáadása** hivatkozásra az irányítópulton.

    ![Kiszolgálókezelő – Szerepkörök hozzáadása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Kattintson a **tovább** gombra, amíg el nem jut a **kiszolgálói funkciók** szakaszhoz.
4. A **szolgáltatások**területen válassza a **feladatátvételi fürtszolgáltatás**lehetőséget.
5. Adja hozzá a további szükséges szolgáltatásokat.
6. A szolgáltatások hozzáadásához kattintson a **telepítés** gombra.

Ismételje meg a lépéseket a többi SQL Server VMon.

  >[!NOTE]
  > Ez a lépés, valamint a SQL Server virtuális gépek a feladatátvevő fürthöz való tényleges csatlakoztatása mostantól automatizálható az [Azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) -vel és az Azure-beli [Gyorsindítás sablonokkal](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"> a tűzfal konfigurálása minden SQL Server VM

A megoldáshoz a következő TCP-portokat kell megnyitni a tűzfalon:

- **SQL Server VM**:<br/>
   Az 1433-es port a SQL Server alapértelmezett példánya.
- **Azure Load Balancer-mintavétel:**<br/>
   Bármely elérhető port. A példák gyakran használják a 59999-ot.
- **Adatbázis-tükrözési végpont:** <br/>
   Bármely elérhető port. A példák gyakran használják a 5022-ot.

A tűzfal portjait mindkét SQL Server virtuális gépen meg kell nyitni.

A portok megnyitásának módszere a használt tűzfal megoldástól függ. A következő szakasz azt ismerteti, hogyan nyithatók meg a portok a Windows tűzfalban. Nyissa meg a szükséges portokat az egyes SQL Server virtuális gépeken.

### <a name="open-a-tcp-port-in-the-firewall"></a>TCP-port megnyitása a tűzfalon

1. Az első SQL Server **kezdőképernyőn** indítsa el a **fokozott biztonságú Windows tűzfalat**.
2. A bal oldali ablaktáblán válassza a **Bejövő szabályok**lehetőséget. A jobb oldali ablaktáblán kattintson az **új szabály**elemre.
3. A **szabálytípus**mezőben válassza a **port**lehetőséget.
4. A port mezőben adja meg a **TCP** értéket, és írja be a megfelelő portszámokat. Lásd a következő példát:

   ![SQL tűzfal](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Kattintson a **Tovább** gombra.
6. A **művelet** lapon tartsa be **a kapcsolat lehetőséget** , majd kattintson a **tovább**gombra.
7. A **profil** lapon fogadja el az alapértelmezett beállításokat, majd kattintson a **tovább**gombra.
8. A **név** lapon adja meg a szabály nevét (például az **Azure LB**-mintavételt) a **név** szövegmezőben, majd kattintson a **Befejezés**gombra.

Ismételje meg ezeket a lépéseket a második SQL Server VM.

## <a name="configure-system-account-permissions"></a>Rendszerfiók engedélyeinek konfigurálása

Hozzon létre egy fiókot a rendszerfiókhoz, és adja meg a megfelelő engedélyeket, hajtsa végre az alábbi lépéseket minden SQL Server példányon:

1. Hozzon létre egy fiókot `[NT AUTHORITY\SYSTEM]`hoz az egyes SQL Server-példányokon. A következő szkript hozza létre ezt a fiókot:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Adja meg a következő engedélyeket az egyes SQL Server példányok `[NT AUTHORITY\SYSTEM]`ához:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   A következő parancsfájl megadja ezeket az engedélyeket:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>Következő lépések

* [SQL Server always on rendelkezésre állási csoport létrehozása az Azure Virtual Machines szolgáltatásban](virtual-machines-windows-portal-sql-availability-group-tutorial.md)
