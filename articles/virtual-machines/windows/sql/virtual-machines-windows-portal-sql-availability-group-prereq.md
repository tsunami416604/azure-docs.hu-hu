---
title: Az SQL Server rendelkezésre állási csoportok – az Azure virtual machines – Előfeltételek |} A Microsoft Docs
description: Ez az oktatóanyag bemutatja egy SQL Server Always On rendelkezésre állási csoport létrehozása Azure virtuális gépekre vonatkozó Előfeltételek konfigurálása.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mikeray
ms.openlocfilehash: d75bb780a17653aaacbc74413fb4240a8052a983
ms.sourcegitcommit: e45b2aa85063d33853560ec4bc867f230c1c18ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43371485"
---
# <a name="complete-the-prerequisites-for-creating-always-on-availability-groups-on-azure-virtual-machines"></a>Teljesítse az előfeltételeket az Always On rendelkezésre állási csoportok létrehozása az Azure-beli virtuális gépeken

Ez az oktatóanyag bemutatja, hogyan végezheti el az Előfeltételek létrehozásához egy [SQL Server Always On rendelkezésre állási csoportot az Azure-beli virtuális gépek (VM)](virtual-machines-windows-portal-sql-availability-group-tutorial.md). Amikor végzett az előfeltételeket, van egy tartományvezérlő, a két SQL Server virtuális gépek és a egy tanúsító kiszolgáló egyetlen erőforráscsoportra.

**Becsült időtartam**: eltarthat néhány óra múlva, hogy teljesítse az előfeltételeket. Ez idő töltött, virtuális gépekre.

Az alábbi ábra az oktatóanyag során létre.

![Rendelkezésre állási csoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Tekintse át a rendelkezésre állási csoport dokumentációja

Ez az oktatóanyag feltételezi, hogy rendelkezik-e az SQL Server Always On rendelkezésre állási csoportok alapvető ismeretekkel. Ha még nem ismeri a technológia segítségével, tekintse meg [mindig a rendelkezésre állási csoportok áttekintése (SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Azure-fiók létrehozása
Rendelkeznie kell Azure-fiókkal. Is [nyissa meg az ingyenes Azure-fiók](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic&redirectURL=https:%2F%2Fazure.microsoft.com%2Fget-started%2Fwelcome-to-azure%2F&correlationId=24f9d452-1909-40d7-b609-2245aa7351a6&l=en-US) vagy [aktiválhatja a Visual Studio előfizetői előnyeit](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Kattintson a **+** hozhat létre új objektumot a portálon.

   ![Új objektum](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Típus **erőforráscsoport** a a **Marketplace** keresési ablakban.

   ![Erőforráscsoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Kattintson a **erőforráscsoport**.
5. Kattintson a **Create** (Létrehozás) gombra.
6. A **erőforráscsoport-név**, írja be az erőforráscsoport nevét. Írja be például **sql-magas rendelkezésre állás-rg**.
7. Ha több Azure-előfizetéssel rendelkezik, ellenőrizze, hogy az előfizetés az Azure-előfizetést, amely a rendelkezésre állási csoport létrehozásához.
8. Válasszon ki egy helyet. A hely, amelyre a rendelkezésre állási csoport létrehozása az Azure-régióban. Ez a cikk egy Azure-helyen található összes erőforrást hoz létre.
9. Ellenőrizze, hogy **rögzítés az irányítópulton** be van jelölve. Ez nem kötelező beállítás az erőforráscsoport parancsikont helyez el az Azure portal irányítópultján.

   ![Erőforráscsoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Kattintson a **létrehozás** az erőforráscsoport létrehozásához.

Az Azure az erőforráscsoportot és a PIN-kódok parancsikont hoz létre az erőforráscsoportot a portálon.

## <a name="create-the-network-and-subnets"></a>A hálózat és alhálózatok létrehozása
A következő lépés, hogy a hálózatok és alhálózatok létrehozása az Azure-erőforráscsoportot.

A megoldás egy virtuális hálózatot két alhálózattal használja. A [virtuális hálózatok áttekintése](../../../virtual-network/virtual-networks-overview.md) Azure-beli hálózatokkal kapcsolatos további információkat tartalmaz.

A virtuális hálózat létrehozása:

1. Az erőforráscsoportban, az Azure Portalon kattintson a **+ Hozzáadás**. 

   ![Új elem](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Keresse meg **virtuális hálózat**.

     ![Virtuális hálózat keresése](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Kattintson a **virtuális hálózati**.
4. Az a **virtuális hálózati**, kattintson a **Resource Manager** üzembe helyezési modell, és kattintson **létrehozás**.

    Az alábbi táblázat a virtuális hálózat beállításait:

   | **Mező** | Érték |
   | --- | --- |
   | **Name (Név)** |autoHAVNET |
   | **Címtér** |10.33.0.0/24 |
   | **Alhálózat neve** |Adminisztratív körzet |
   | **Alhálózati címtartomány** |10.33.0.0/29 |
   | **Előfizetés** |Válassza ki a használni kívánt előfizetést. **Előfizetés** üres, ha csak egy előfizetéssel rendelkezik. |
   | **Erőforráscsoport** |Válasszon **meglévő** , és válassza ki az erőforráscsoport nevét. |
   | **Hely** |Adja meg az Azure-helyen. |

   A cím címteret és az alhálózati címtartomány eltérhet a táblából. Az előfizetés függvényében a portál javasol, egy elérhető címteret és a megfelelő alhálózati címtartományt. Ha nincs elegendő címterület nem érhető el, használjon másik előfizetést.

   A példában az alhálózat neve **rendszergazdai**. Ez az alhálózat van a tartományvezérlőn.

5. Kattintson a **Create** (Létrehozás) gombra.

   ![A virtuális hálózat konfigurálása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Az Azure ad vissza, a portál irányítópultján, és értesíti, ha az új hálózat jön létre.

### <a name="create-a-second-subnet"></a>Hozzon létre egy második alhálózatot
Az új virtuális hálózat nevű alhálózattal rendelkezik **rendszergazdai**. A tartományvezérlők ezt az alhálózatot használja. Az SQL Server virtuális gépek használatát egy második alhálózatot nevű **SQL**. Ez az alhálózat beállítása:

1. Az irányítópulton, kattintson a létrehozott erőforráscsoport **SQL-magas rendelkezésre ÁLLÁS-RG**. A hálózaton található az erőforráscsoport **erőforrások**.

    Ha **SQL-magas rendelkezésre ÁLLÁS-RG** nem látható, megkereshetjük **erőforráscsoportok** és az erőforráscsoport neve alapján történő szűrést.
2. Kattintson a **autoHAVNET** az erőforrások listájában. 
3. Az a **autoHAVNET** virtuális hálózat, a **beállítások** , kattintson a **alhálózatok**.

    Vegye figyelembe a már létrehozott alhálózat.

   ![A virtuális hálózat konfigurálása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Hozzon létre egy második alhálózatot. Kattintson a **+ alhálózat**.
6. A **alhálózat hozzáadása**, írja be az alhálózat konfigurálása **sqlsubnet** alatt **neve**. Az Azure automatikusan megadja egy érvényes **címtartomány**. Győződjön meg arról, hogy a címtartomány legalább 10-címmel rendelkezik, benne. Éles környezetben több címmel lehet szüksége.
7. Kattintson az **OK** gombra.

    ![A virtuális hálózat konfigurálása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

A következő táblázat összefoglalja a hálózati konfigurációs beállításokat:

| **Mező** | Érték |
| --- | --- |
| **Name (Név)** |**autoHAVNET** |
| **Címtér** |Ez az érték attól függ, hogy az elérhető címtereket az előfizetésében. Egy tipikus értéke a 10.0.0.0/16. |
| **Alhálózat neve** |**rendszergazda** |
| **Alhálózati címtartomány** |Ezt az értéket a rendelkezésre álló-címtartományokat az előfizetésében függ. Egy tipikus értéke a 10.0.0.0/24. |
| **Alhálózat neve** |**sqlsubnet** |
| **Alhálózati címtartomány** |Ezt az értéket a rendelkezésre álló-címtartományokat az előfizetésében függ. Egy tipikus értéke 10.0.1.0/24. |
| **Előfizetés** |Válassza ki a használni kívánt előfizetést. |
| **Erőforráscsoport** |**SQL-HA-RG** |
| **Hely** |Adja meg az ugyanazon a helyen, az erőforráscsoport számára is választott. |

## <a name="create-availability-sets"></a>Rendelkezésre állási csoportok létrehozása

Mielőtt virtuális gépeket hoz létre, szüksége rendelkezésre állási csoportok létrehozása. A rendelkezésre állási csoportok csökkentheti az állásidő tervezett vagy nem tervezett karbantartási események esetében. Egy Azure rendelkezésre állási csoport egy olyan logikai csoport, amely a fizikai tartalék tartományokban és frissítési tartományok helyezi az Azure-erőforrások. Tartalék tartomány biztosítja, hogy a rendelkezésre állási csoport tagjai külön power és hálózati erőforrásokat. Frissítési tartomány biztosítja, hogy a rendelkezésre állási csoport tagjai nem hozzák a karbantartási egyszerre. További információkért lásd: [virtuális gépek rendelkezésre állásának kezelése](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Szüksége van két rendelkezésre állási csoportot. Az egyik, a tartományvezérlők. A második pedig az SQL Server rendszerű virtuális gépekhez.

Hozzon létre egy rendelkezésre állási csoport, nyissa meg az erőforráscsoport, és kattintson a **Hozzáadás**. Az eredmények szűréséhez beírásával **rendelkezésre állási csoport**. Kattintson a **rendelkezésre állási csoport** az eredményeket, majd kattintson a **létrehozás**.

A paraméterek alapján két rendelkezésre állási csoportok konfigurálása az alábbi táblázatban:

| **Mező** | Domain controller rendelkezésre állási csoport | Az SQL Server rendelkezésre állási csoport |
| --- | --- | --- |
| **Name (Név)** |adavailabilityset |sqlavailabilityset |
| **Erőforráscsoport** |SQL-HA-RG |SQL-HA-RG |
| **Tartalék tartományok** |3 |3 |
| **Frissítési tartományok** |5 |3 |

Miután létrehozta a rendelkezésre állási csoportok, térjen vissza az erőforráscsoportot az Azure Portalon.

## <a name="create-domain-controllers"></a>Tartományvezérlők létrehozása
Miután létrehozta a hálózat, alhálózat, a rendelkezésre állási csoportok és egy internetkapcsolattal rendelkező terheléselosztó, készen áll a tartományvezérlők a virtuális gépek létrehozásához.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>A tartományvezérlők virtuális gépek létrehozása
Hozzon létre, és a tartományvezérlők konfigurálása, térjen vissza a **SQL-magas rendelkezésre ÁLLÁS-RG** erőforráscsoportot.

1. Kattintson a **Hozzáadás** parancsra. 
2. Típus **Windows Server 2016 Datacenter**.
3. Kattintson a **Windows Server 2016 Datacenter**. A **Windows Server 2016 Datacenter**, győződjön meg arról, hogy az üzembe helyezési modell **Resource Manager**, és kattintson a **létrehozás**. 

Ismételje meg a fenti lépéseket a két virtuális gépet. A két virtuális gép neve:

* ad-primary-dc
* ad-secondary-dc

  > [!NOTE]
  > A **ad-másodlagos-tartományvezérlő** virtuális gép nem kötelező, magas rendelkezésre állás biztosításához az Active Directory Domain Servicesben.
  >
  >

Az alábbi táblázat ezek két gép beállításait:

| **Mező** | Érték |
| --- | --- |
| **Name (Név)** |Első tartományvezérlő: *ad-elsődleges-tartományvezérlő*.</br>Második tartományvezérlő *ad-másodlagos-tartományvezérlő*. |
| **Virtuális merevlemez típusa** |SSD |
| **Felhasználónév** |DomainAdmin |
| **Jelszó** |Contoso!0000 |
| **Előfizetés** |*Az Ön előfizetése* |
| **Erőforráscsoport** |SQL-HA-RG |
| **Hely** |*A hely* |
| **Méret** |DS1_V2 |
| **Storage** | **Felügyelt lemezek használata** - **Igen** |
| **Virtuális hálózat** |autoHAVNET |
| **Alhálózat** |rendszergazda |
| **Nyilvános IP-cím** |*Neve megegyezik a virtuális gép* |
| **Hálózati biztonsági csoport** |*Neve megegyezik a virtuális gép* |
| **A rendelkezésre állási csoport** |adavailabilityset </br>**Tartalék tartományok**: 2</br>**Frissítési tartományok**: 2|
| **Diagnosztika** |Engedélyezve |
| **Storage-fiók diagnosztikája** |*Automatikusan létrehozott* |

   >[!IMPORTANT]
   >Csak egy virtuális Gépet elhelyezheti egy rendelkezésre állási csoport létrehozásakor. A rendelkezésre állási csoportot a virtuális gép létrehozása után nem módosítható. Lásd: [virtuális gépek rendelkezésre állásának kezelése](../manage-availability.md).

Az Azure létrehozza a virtuális gépeket.

Miután a virtuális gépek jönnek létre, konfigurálhatja a tartományvezérlőt.

### <a name="configure-the-domain-controller"></a>A tartományvezérlő konfigurálása
Az alábbi lépésekkel, konfigurálja a **ad-elsődleges-tartományvezérlő** gépi Corp.contoso.com tartományvezérlőként.

1. A portálon nyissa meg a **SQL-magas rendelkezésre ÁLLÁS-RG** erőforrás-csoport, és válassza a **ad-elsődleges-tartományvezérlő** gép. A **ad-elsődleges-tartományvezérlő**, kattintson a **Connect** egy RDP-fájl, amely távoli asztali hozzáférés megnyitásához.

    ![Csatlakozás virtuális géphez](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Jelentkezzen be a beállított rendszergazdai fiókjával (**\DomainAdmin**) és a jelszót (**Contoso! 0000**).
3. Alapértelmezés szerint a **Kiszolgálókezelő** irányítópult üzenetnek kell megjelennie.
4. Kattintson a **szerepkörök és szolgáltatások hozzáadása** hivatkozás az irányítópulton.

    ![Kiszolgálókezelő - Szerepkörök hozzáadása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Válassza ki **tovább** mindaddig, amíg kap a **kiszolgálói szerepkörök** szakaszban.
6. Válassza ki a **Active Directory Domain Services** és **DNS-kiszolgáló** szerepköröket. Amikor a rendszer kéri, ezek a szerepkörök által igényelt további funkciókkal bővíti.

   > [!NOTE]
   > Windows figyelmeztetést jelenít meg, hogy nem statikus IP-cím nem létezik. Ha a tesztelést a konfigurációt, kattintson a **Folytatás**. A termelési forgatókönyvekhez állítsa be az IP-cím statikus az Azure Portalon vagy [PowerShell használatával beállíthatja a statikus IP-címet a domain controller gép](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Adja hozzá szerepkörök párbeszédpanel](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Kattintson a **tovább** amíg el nem éri a **megerősítő** szakaszban. Válassza ki a **a célkiszolgáló automatikus újraindítása, ha szükséges** jelölőnégyzetet.
8. Kattintson az **Install** (Telepítés) gombra.
9. Miután a szolgáltatások telepítése, térjen vissza a **Kiszolgálókezelő** irányítópultot.
10. Válassza ki az új **Active Directory tartományi szolgáltatások** lehetőséget a bal oldali panelen.
11. Kattintson a **további** a sárga figyelmeztető sáv található hivatkozásra.

    ![Az AD DS párbeszédpanel a DNS-kiszolgáló virtuális gépen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. Az a **művelet** oszlopa a **összes kiszolgáló feladat részletei** párbeszédpanelen kattintson a **a számítógép előléptetése tartományvezérlővé**.
13. Az a **Active Directory Domain Services konfigurálási varázslója**, használja a következő értékeket:

    | **Lap** | Beállítás |
    | --- | --- |
    | **Központi telepítés konfigurálása** |**Új erdő hozzáadása**<br/> **Legfelső szintű tartománynév** = corp.contoso.com |
    | **Tartományvezérlő beállításai** |**Címtárszolgáltatás-javító mód jelszavát** = Contoso! 0000<br/>**Jelszó megerősítése** = Contoso! 0000 |
14. Kattintson a **tovább** haladhat végig a varázsló többi lapján. Az a **szükséges előfeltételek ellenőrzése** lapon, győződjön meg arról, hogy a következő üzenetet látja: **az előfeltétel-ellenőrzés sikeresen lefutott**. Áttekintheti a alkalmazni figyelmeztető üzeneteket, de lehetséges, a telepítés folytatásához.
15. Kattintson az **Install** (Telepítés) gombra. A **ad-elsődleges-tartományvezérlő** virtuális gép automatikusan újraindul.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Vegye figyelembe az elsődleges tartományvezérlő IP-címe

Használja az elsődleges tartományvezérlő DNS-hez. Vegye figyelembe a tartomány elsődleges tartományvezérlő IP-címét.

Egy elsődleges tartományt vezérlő IP-címének lekéréséhez módja az Azure Portalon keresztül.

1. Az Azure Portalon nyissa meg az erőforráscsoportot.

2. Kattintson az elsődleges tartományvezérlőt.

3. Az elsődleges tartományvezérlőn kattintson **hálózati adapterek**.

![Hálózati illesztők](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Vegye figyelembe a magánhálózati IP-címet ehhez a kiszolgálóhoz.

### <a name="configure-the-virtual-network-dns"></a>A virtuális hálózat DNS konfigurálása
Miután létrehozta az első tartományvezérlő és DNS engedélyezése az első kiszolgálón, konfigurálja a virtuális hálózat DNS-ehhez a kiszolgálóhoz használandó.

1. Az Azure Portalon kattintson a virtuális hálózaton.

2. A **beállítások**, kattintson a **DNS-kiszolgáló**.

3. Kattintson a **egyéni**, és írja be az elsődleges tartományvezérlőn a magánhálózati IP-címe.

4. Kattintson a **Save** (Mentés) gombra.

### <a name="configure-the-second-domain-controller"></a>A második tartományvezérlő konfigurálása
Miután az elsődleges tartományvezérlő újraindult, konfigurálhatja a második tartományvezérlő. Ez a lépés nem kötelező a magas rendelkezésre állás. Kövesse az alábbi lépéseket a második tartományvezérlő konfigurálása:

1. A portálon nyissa meg a **SQL-magas rendelkezésre ÁLLÁS-RG** erőforrás-csoport, és válassza a **ad-másodlagos-tartományvezérlő** gép. A **ad-másodlagos-tartományvezérlő**, kattintson a **Connect** egy RDP-fájl, amely távoli asztali hozzáférés megnyitásához.
2. A konfigurált rendszergazdai fiók használatával jelentkezzen be a virtuális gép (**BUILTIN\DomainAdmin**) és a jelszót (**Contoso! 0000**).
3. Módosítsa a címet, a tartományvezérlő a DNS-kiszolgáló címét.
4. A **hálózati és megosztási központ**, kattintson a hálózati adaptert.
   ![Hálózati illesztő](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Kattintson a **Tulajdonságok** elemre.
6. Válassza ki **Internet Protocol Version 4 (TCP/IPv4)** kattintson **tulajdonságok**.
7. Válassza ki **használja a következő DNS-kiszolgálócímek** , és adja meg az elsődleges tartományvezérlő a címét **elsődleges DNS-kiszolgáló**.
8. Kattintson a **OK**, majd **Bezárás** gombra a módosítások véglegesítéséhez. Most már csatlakozhat a virtuális gép áll **corp.contoso.com**.

   >[!IMPORTANT]
   >Ha a DNS-beállításainak módosítása után a távoli asztal megszakad a kapcsolat, nyissa meg az Azure Portalon, és indítsa újra a virtuális gépet.

9. Nyissa meg a másodlagos tartományvezérlő a távoli asztalról **Kiszolgálókezelő irányítópultján**.
10. Kattintson a **szerepkörök és szolgáltatások hozzáadása** hivatkozás az irányítópulton.

    ![Kiszolgálókezelő - Szerepkörök hozzáadása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Válassza ki **tovább** mindaddig, amíg kap a **kiszolgálói szerepkörök** szakaszban.
12. Válassza ki a **Active Directory Domain Services** és **DNS-kiszolgáló** szerepköröket. Amikor a rendszer kéri, ezek a szerepkörök által igényelt további funkciókkal bővíti.
13. Miután a szolgáltatások telepítése, térjen vissza a **Kiszolgálókezelő** irányítópultot.
14. Válassza ki az új **Active Directory tartományi szolgáltatások** lehetőséget a bal oldali panelen.
15. Kattintson a **további** a sárga figyelmeztető sáv található hivatkozásra.
16. Az a **művelet** oszlopa a **összes kiszolgáló feladat részletei** párbeszédpanelen kattintson a **a számítógép előléptetése tartományvezérlővé**.
17. A **üzembe helyezési konfiguráció**válassza **tartományvezérlő hozzáadása meglévő tartományhoz**.
   ![Központi telepítés konfigurálása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Kattintson a **Kiválasztás** gombra.
19. Csatlakozás a rendszergazdai fiók használatával (**CORP. CONTOSO.COM\domainadmin**) és a jelszót (**Contoso! 0000**).
20. A **jelöljön ki egy tartományt az erdőben**, a tartományra, és kattintson **OK**.
21. A **tartományvezérlő beállításai**, használja az alapértelmezett értékeket, és állítsa be a DSRM-jelszót.

   >[!NOTE]
   >A **DNS-beállítások** lap előfordulhat, hogy figyelmezteti, hogy a DNS-kiszolgáló delegálást nem hozható létre. Ez a figyelmeztetés nem éles környezetekben figyelmen kívül hagyhatja.
22. Kattintson a **tovább** amíg el nem éri a párbeszédpanelen a **Előfeltételek** ellenőrzése. Ezt követően kattintson a **Telepítés** gombra.

Ha a kiszolgáló befejezi a konfiguráció módosításait, indítsa újra a kiszolgálót.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>A magánhálózati IP-címet ad hozzá a második tartományvezérlő VPN DNS-kiszolgálóra

Az Azure Portalon, a virtuális hálózat módosítsa a DNS-kiszolgáló a másodlagos tartományvezérlő IP-címét tartalmazza. Ez a beállítás lehetővé teszi, hogy a DNS szolgáltatás redundanciájára.

### <a name=DomainAccounts></a> A tartományi fiókok konfigurálása

Az alábbi lépésekkel konfigurálja az Active Directory-fiókokat. Az alábbi táblázat a fiókok:

| |Telepítési fiók<br/> |sqlserver-0 <br/>SQL Server és az SQL Agent szolgáltatás fiókja |sqlserver-1<br/>SQL Server és az SQL Agent szolgáltatás fiókja
| --- | --- | --- | ---
|**Utónév** |Telepítés |SQLSvc1 | SQLSvc2
|**Felhasználó SamAccountName** |Telepítés |SQLSvc1 | SQLSvc2

A következő lépések segítségével hozzon létre minden egyes fiók.

1. Jelentkezzen be a **ad-elsődleges-tartományvezérlő** gép.
2. A **Kiszolgálókezelő**válassza **eszközök**, és kattintson a **Active Directory felügyeleti központ**.   
3. Válassza ki **corp (helyi)** a bal oldali ablaktáblán.
4. A jobb oldali **feladatok** ablaktáblán válassza előbb **új**, és kattintson a **felhasználói**.
   ![Active Directory felügyeleti központ](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Az egyes fiókok számára bonyolult jelszót beállítani.<br/> Nem éles környezetekhez állítsa be a felhasználói fiókot soha ne járjon le.

5. Kattintson a **OK** a felhasználó létrehozásához.
6. Ismételje meg a fenti lépéseket a három fiókot mindegyikében.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Adja meg a szükséges engedélyeket a telepítési fiók
1. Az a **Active Directory felügyeleti központ**válassza **corp (helyi)** a bal oldali panelen. Ezután a jobb oldalon található **feladatok** ablaktáblán kattintson a **tulajdonságok**.

    ![CORP-felhasználói tulajdonságok](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Válassza ki **bővítmények**, majd kattintson a **speciális** gombot a **biztonsági** fülre.
3. Az a **speciális biztonsági beállítások vállalati** párbeszédpanelen kattintson a **Hozzáadás**.
4. Kattintson a **rendszerbiztonsági tag kiválasztása**, keressen **CORP\Install**, és kattintson a **OK**.
5. Válassza ki a **az összes tulajdonság olvasása** jelölőnégyzetet.

6. Válassza ki a **számítógép-objektumok létrehozása** jelölőnégyzetet.

     ![Corp-felhasználói engedélyek](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Kattintson a **OK**, és kattintson a **OK** újra. Zárja be a **corp** tulajdonságai ablakban.

Most, hogy végzett Active Directory és a felhasználói objektumok, hozzon létre két SQL Server virtuális gépek és a egy tanúsító kiszolgáló virtuális Géphez. Ezután csatlakozzon mindhárom a tartományhoz.

## <a name="create-sql-server-vms"></a>SQL Server virtuális gépek létrehozása

Hozzon létre három további virtuális gépeket. A megoldás két virtuális gép az SQL Server-példányokat igényel. A harmadik virtuális gépek egy tanúsító fognak működni. A Windows Server 2016-ban használható egy [felhőbeli tanúsító](http://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness), azonban a korábbi operációs rendszerekhez való Ez a dokumentum egy tanúsító használ egy virtuális gépet.  

Folytatás előtt fontolja meg az alábbi tervezési döntéseket hozhat.

* **Storage – az Azure Managed Disks**

   A virtuálisgép-tároló az Azure Managed Disks használata. A Microsoft SQL Server virtuális gépek Managed Disks használatát javasolja. A Managed Disks szolgáltatás a háttérben kezeli a tárterületet. Emellett ha ugyanabban a rendelkezésre állási csoportban több, a Managed Diskset használó virtuális gép található, az Azure elosztja a tárolási erőforrásokat, hogy megfelelő redundanciát biztosítson. További információkért lásd az [Azure Managed Disks áttekintését](../managed-disks-overview.md). A rendelkezésre állási csoportban a felügyelt lemezekkel kapcsolatos részletekért lásd: [felügyelt lemezek használata a virtuális gépet egy rendelkezésre állási csoportban](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **-Hálózati magánhálózati IP-címek éles környezetben**

   A virtuális gépek ebben az oktatóanyagban használja a nyilvános IP-címeket. Nyilvános IP-cím lehetővé teszi, hogy a távoli kapcsolatot közvetlenül a virtuális gép az interneten keresztül – a konfigurációs lépések egyszerűbbé teszi. Éles környezetben a Microsoft javasolja a magánhálózati IP-címek annak érdekében, hogy csökkenti a biztonsági rés adatmennyiséget az SQL Server-példány Virtuálisgép-erőforrást.

### <a name="create-and-configure-the-sql-server-vms"></a>Hozzon létre és konfigurálja az SQL Server rendszerű virtuális gépekhez
Ezután hozzon létre három virtuális gép – két SQL Server virtuális gépek és a egy virtuális Gépet a egy további fürtcsomópontra. Hozzon létre minden egyes virtuális, lépjen vissza a **SQL-magas rendelkezésre ÁLLÁS-RG** erőforráscsoportot, kattintson a **hozzáadása**, keresse meg a megfelelő gyűjteményelemet, kattintson a **virtuális gép**, majd  **A katalógus**. Az alábbi táblázatban szereplő információk segítségével hozhat létre a virtuális gépek:


| Oldal | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Válassza ki a megfelelő Katalóguselem |**A Windows Server 2016 Datacenter** |**Az SQL Server 2016 SP1 Enterprise Windows Server 2016 rendszeren** |**Az SQL Server 2016 SP1 Enterprise Windows Server 2016 rendszeren** |
| Virtuálisgép-konfiguráció **alapjai** |**Név** fürt-fsw =<br/>**Felhasználónév** tartományi rendszergazdai =<br/>**Jelszó** = Contoso! 0000<br/>**Előfizetés** = az előfizetés<br/>**Erőforráscsoport** SQL-magas rendelkezésre ÁLLÁS-RG =<br/>**Hely** = az Azure-beli hely |**Név** sqlserver – 0 =<br/>**Felhasználónév** tartományi rendszergazdai =<br/>**Jelszó** = Contoso! 0000<br/>**Előfizetés** = az előfizetés<br/>**Erőforráscsoport** SQL-magas rendelkezésre ÁLLÁS-RG =<br/>**Hely** = az Azure-beli hely |**Név** sqlserver-1 =<br/>**Felhasználónév** tartományi rendszergazdai =<br/>**Jelszó** = Contoso! 0000<br/>**Előfizetés** = az előfizetés<br/>**Erőforráscsoport** SQL-magas rendelkezésre ÁLLÁS-RG =<br/>**Hely** = az Azure-beli hely |
| Virtuálisgép-konfiguráció **mérete** |**MÉRET** = DS1\_V2 (1 vCPU, 3,5 GB) |**MÉRET** = DS2\_V2 (2 vcpu-k, 7 GB)</br>Méretének támogatnia kell az SSD-tárolás (prémium szintű lemez támogatása. )) |**MÉRET** = DS2\_V2 (2 vcpu-k, 7 GB) |
| Virtuálisgép-konfiguráció **beállításai** |**Tárolási**: felügyelt lemezek használata.<br/>**Virtuális hálózat** = autoHAVNET<br/>**Alhálózat** = sqlsubnet(10.1.1.0/24)<br/>**Nyilvános IP-cím** automatikusan generált.<br/>**Hálózati biztonsági csoport** = None<br/>**Figyelés, diagnosztika** = engedélyezve<br/>**Diagnosztikai tárfiók** = egy automatikusan létrehozott storage-fiók használata<br/>**A rendelkezésre állási csoport** = sqlAvailabilitySet<br/> |**Tárolási**: felügyelt lemezek használata.<br/>**Virtuális hálózat** = autoHAVNET<br/>**Alhálózat** = sqlsubnet(10.1.1.0/24)<br/>**Nyilvános IP-cím** automatikusan generált.<br/>**Hálózati biztonsági csoport** = None<br/>**Figyelés, diagnosztika** = engedélyezve<br/>**Diagnosztikai tárfiók** = egy automatikusan létrehozott storage-fiók használata<br/>**A rendelkezésre állási csoport** = sqlAvailabilitySet<br/> |**Tárolási**: felügyelt lemezek használata.<br/>**Virtuális hálózat** = autoHAVNET<br/>**Alhálózat** = sqlsubnet(10.1.1.0/24)<br/>**Nyilvános IP-cím** automatikusan generált.<br/>**Hálózati biztonsági csoport** = None<br/>**Figyelés, diagnosztika** = engedélyezve<br/>**Diagnosztikai tárfiók** = egy automatikusan létrehozott storage-fiók használata<br/>**A rendelkezésre állási csoport** = sqlAvailabilitySet<br/> |
| Virtuálisgép-konfiguráció **SQL Server-beállítások** |Nem alkalmazható |**SQL-kapcsolat** privát (virtuális hálózaton belül) =<br/>**Port** 1433-as =<br/>**SQL-hitelesítés** = Disable<br/>**Tárolási konfiguráció** általános =<br/>**Automatikus javítás** vasárnap = 2:00-kor<br/>**Automatikus biztonsági mentés** = letiltva</br>**Az Azure Key Vault-integráció** = letiltva |**SQL-kapcsolat** privát (virtuális hálózaton belül) =<br/>**Port** 1433-as =<br/>**SQL-hitelesítés** = Disable<br/>**Tárolási konfiguráció** általános =<br/>**Automatikus javítás** vasárnap = 2:00-kor<br/>**Automatikus biztonsági mentés** = letiltva</br>**Az Azure Key Vault-integráció** = letiltva |

<br/>

> [!NOTE]
> Az itt ajánlott gépméreteket úgy van kialakítva, az Azure-beli virtuális gépek rendelkezésre állási csoportok teszteléshez. A termelési számítási feladatokhoz a legjobb teljesítmény érdekében tekintse meg a javaslatok az SQL Server gépméretekért és -konfigurációkért [teljesítménnyel kapcsolatos ajánlott eljárások az SQL Server Azure-beli virtuális gépeken](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

A három virtuális gép teljes kiosztása után kell csatlakoztassa azokat a **corp.contoso.com** tartomány és az engedélyezés CORP\Install rendszergazdai jogosultságokkal a gépek.

### <a name="joinDomain"></a>A kiszolgálók csatlakoztatása a tartományhoz

Ön mostantól csatlakozhat a virtuális gépek **corp.contoso.com**. Kövesse az alábbi lépéseket az SQL Server virtuális gépek és a tanúsító fájlmegosztás kiszolgálója:

1. A virtuális géphez a távoli csatlakozás **BUILTIN\DomainAdmin**.
2. A **Kiszolgálókezelő**, kattintson a **helyi kiszolgáló**.
3. Kattintson a **munkacsoport** hivatkozásra.
4. Az a **számítógépnév** területén kattintson **módosítás**.
5. Válassza ki a **tartomány** jelölőnégyzetet, és írja be **corp.contoso.com** a szövegmezőben. Kattintson az **OK** gombra.
6. Az a **Windows biztonsági** előugró párbeszédpanelen adja meg az alapértelmezett tartományi rendszergazdai fiók hitelesítő adatait (**CORP\DomainAdmin**) és a jelszót (**Contoso! 0000**) .
7. Ha a "Üdvözli a corp.contoso.com tartományban az" üzenetet látja, kattintson az **OK**.
8. Kattintson a **Bezárás**, és kattintson a **Újraindítás most** az előugró párbeszédpanelen.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Adja hozzá a Corp\Install felhasználó minden virtuális gép fürtön rendszergazdaként

A tartomány tagjaként minden virtuális gép újraindítása után adja hozzá a **CORP\Install** a helyi Rendszergazdák csoport tagjaként.

1. Várjon, amíg a virtuális gép újraindul, majd indítsa el újra az elsődleges tartományvezérlőn jelentkezzen be az RDP-fájl **sqlserver-0** használatával a **CORP\DomainAdmin** fiókot.
   >[!TIP]
   >Győződjön meg arról, hogy jelentkezzen be a tartomány rendszergazdai fiókkal. Az előző lépésekben használt a a beépített rendszergazdai fiók. Most, hogy a kiszolgáló a tartományban, a tartományi fiók használata. Adja meg az RDP-munkamenet *tartomány*\\*felhasználónév*.

2. A **Kiszolgálókezelő**válassza **eszközök**, és kattintson a **számítógép-kezelés**.
3. Az a **számítógép-kezelés** ablakában bontsa ki a **helyi felhasználók és csoportok**, majd válassza ki **csoportok**.
4. Kattintson duplán a **rendszergazdák** csoport.
5. Az a **rendszergazdák tulajdonságok** párbeszédpanelen kattintson a **Hozzáadás** gombra.
6. Adja meg a felhasználó **CORP\Install**, és kattintson a **OK**.
7. Kattintson a **OK** gombra kattintva zárja be a **rendszergazda tulajdonságainak** párbeszédpanel.
8. Ismételje meg az előző lépéseket **sqlserver-1** és **fürt-fsw**.

### <a name="setServiceAccount"></a>Az SQL Server szolgáltatás fiókok beállítása

Minden egyes SQL Server VM állítsa be az SQL Server szolgáltatásfiókja. A fiókok, amelyet hozott létre, mikor használják, [konfigurálva a tartományi fiókok](#DomainAccounts).

1. Nyissa meg **SQL Server Configuration Manager**.
2. Kattintson a jobb gombbal az SQL Server szolgáltatást, és kattintson a **tulajdonságok**.
3. Állítsa be a fiók és jelszó.
4. Ismételje meg ezeket a lépéseket, a más SQL Server virtuális gépen.  

Az SQL Server rendelkezésre állási csoportok minden egyes SQL Server rendszerű virtuális gép futtatása tartományi fiókként kell.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Hozzon létre egy jelentkezzen be az egyes SQL Server virtuális Gépeken, a telepítési fiók

A telepítési fiók (CORP\install) segítségével konfigurálhatja a rendelkezésre állási csoportot. Ehhez a fiókhoz kell tartoznia a **SysAdmin (rendszergazda)** rögzített kiszolgálói szerepkör az egyes SQL Server virtuális Gépeken. Az alábbi lépések létrehozzák egy bejelentkezési a telepítési fiók:

1. Csatlakozás a kiszolgálóhoz a távoli asztal protokoll (RDP) keresztül használatával a  *\<MachineName\>\DomainAdmin* fiókot.

1. Nyissa meg az SQL Server Management Studiót, és csatlakozzon az SQL Server helyi példányát.

1. A **Object Explorer**, kattintson a **biztonsági**.

1. Kattintson a jobb gombbal **bejelentkezések**. Kattintson a **új bejelentkezés**.

1. A **bejelentkezés – új**, kattintson a **keresési**.

1. Kattintson a **helyek**.

1. Adja meg a tartományi rendszergazda hitelesítő.

1. A telepítési fiókot használja.

1. A bejelentkezés tagja kell beállítani a **SysAdmin (rendszergazda)** rögzített kiszolgálói szerepkör.

1. Kattintson az **OK** gombra.

Ismételje meg a fenti lépéseket a más SQL Server virtuális gépen.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>A Feladatátvételi fürtszolgáltatás szolgáltatások mindkét SQL Server virtuális gépek hozzáadása

Adja hozzá a Feladatátvételi fürtszolgáltatással, tegye mindkét SQL Server virtuális gépeken az alábbi lépéseket:

1. Csatlakozás az SQL Server virtuális géphez a távoli asztal protokoll (RDP) keresztül használatával a *CORP\install* fiókot. Nyissa meg **Kiszolgálókezelő irányítópultján**.
2. Kattintson a **szerepkörök és szolgáltatások hozzáadása** hivatkozás az irányítópulton.

    ![Kiszolgálókezelő - Szerepkörök hozzáadása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Válassza ki **tovább** mindaddig, amíg kap a **kiszolgálói szolgáltatásokat** szakaszban.
4. A **funkciók**válassza **feladatátvételi fürtszolgáltatás**.
5. Adja hozzá a további szükséges szolgáltatásokat.
6. Kattintson a **telepítése** a funkciókat.

Ismételje meg a más SQL Server virtuális gépen.

## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"> A tűzfal konfigurálása az egyes SQL Server virtuális Gépeken

A megoldás a következő TCP-portok nyitva a tűzfalon van szükség:

- **Az SQL Servert futtató virtuális gép**:<br/>
   Az SQL Server alapértelmezett példánya esetében az 1433-as port.
- **Az Azure load balancer mintavételi:**<br/>
   Minden elérhető port. Példák 59999 gyakran használnak.
- **Az adatbázis-tükrözési végpont:** <br/>
   Minden elérhető port. Példák az 5022 gyakran használnak.

A tűzfal portjait kell megnyitni mindkét SQL Server virtuális gépeken.

A portok megnyitása módszer attól függ, hogy a tűzfal-megoldásnak megfelelő. Ez a szakasz azt ismerteti, hogyan a portok Windows tűzfalban való megnyitásáról. Nyissa meg az SQL Server virtuális gépek mindegyike a szükséges portok.

### <a name="open-a-tcp-port-in-the-firewall"></a>Nyissa meg a TCP-port a tűzfalon

1. Az első SQL-kiszolgálón **Start** indítása jobb **fokozott biztonságú Windows tűzfal**.
2. A bal oldali panelen válassza ki a **bejövő szabályok**. Kattintson a jobb oldali **új szabály**.
3. A **szabálytípus**, válassza a **Port**.
4. Adja meg a portot, **TCP** , és írja be a megfelelő portok számát. Tekintse meg a következő példát:

   ![SQL-tűzfal](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Kattintson a **Tovább** gombra.
6. Az a **művelet** lapon, hogy **engedélyezze a kapcsolatot** kiválasztva, és kattintson **tovább**.
7. Az a **profil** lapon fogadja el az alapértelmezett beállításokat, és kattintson a **tovább**.
8. Az a **neve** csoportjában adja meg a szabály nevét (például **Azure LB Probe**) az a **neve** szövegmezőbe, és kattintson **Befejezés**.

Ismételje meg ezeket a lépéseket, a második az SQL Server virtuális gépen.

## <a name="configure-system-account-permissions"></a>A system fiók engedélyeit

Hozzon létre egy fiókot a system fiók és a megfelelő engedélyeket, a következő lépéseket minden egyes SQL Server-példányon:

1. Hozzon létre egy fiókot `[NT AUTHORITY\SYSTEM]` minden egyes SQL Server-példányon. A következő szkriptet az ezt a fiókot hoz létre:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. A következő engedélyeket `[NT AUTHORITY\SYSTEM]` minden egyes SQL Server-példányon:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   A következő szkript engedélyezi ezeket az engedélyeket:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>További lépések

* [Egy SQL Server Always On rendelkezésre állási csoport létrehozása Azure-beli virtuális gépeken](virtual-machines-windows-portal-sql-availability-group-tutorial.md)
