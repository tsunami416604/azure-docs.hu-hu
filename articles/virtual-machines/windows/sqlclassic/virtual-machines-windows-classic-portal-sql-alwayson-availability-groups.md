---
title: "Always On rendelkezésre állási csoport konfigurálása az Azure virtuális gépek (klasszikus) |} Microsoft Docs"
description: "Always On rendelkezésre állási csoport létrehozása az Azure virtuális gépek. Ez az oktatóanyag elsősorban akkor használja, a felhasználói felület és az eszközök ahelyett, hogy a parancsfájl-kezelési."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 8d48a3d2-79f8-4ab0-9327-2f30ee0b2ff1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: 275c0fdfecac558e4f10d36eee71d38528f34679
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="configure-always-on-availability-group-in-azure-virtual-machines-classic"></a>Always On rendelkezésre állási csoport konfigurálása az Azure virtuális gépek (klasszikus)
> [!div class="op_single_selector"]
> * [Klasszikus: felhasználói felület](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klasszikus: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Mielőtt elkezdené, vegye figyelembe, hogy most hajthatja végre ezt a feladatot az Azure Resource Manager modellt. Azt javasoljuk, hogy az új központi telepítéseknél Azure Resource Manager modellt. Lásd: [SQL Server Always On rendelkezésre állási csoportok az Azure virtuális gépeken](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT] 
> A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Azure létrehozásához, és a erőforrásokat két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti, hogyan a klasszikus üzembe helyezési modellben. 

Az Azure Resource Manager modellt a feladat végrehajtásához tekintse meg a [SQL Server Always On rendelkezésre állási csoportok az Azure virtuális gépeken](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

A végpont oktatóanyag bemutatja, hogyan rendelkezésre állási csoportokat az SQL Server Always On Azure virtuális gépeken futó segítségével megvalósítható.

Az oktatóanyag végén az SQL Server Always On megoldás az Azure-ban a következő elemekből áll:

* Egy virtuális hálózatot, amely tartalmazza a több alhálózattal, és magában foglalja egy előtér- és a backend alhálózathoz
* Az Active Directory (Azure AD) tartományhoz rendelkező tartományvezérlő
* Két olyan virtuális gépet, amely SQL Server fut és központi telepítése a backend alhálózathoz, és az Azure AD-tartományhoz csatlakozik
* Egy három csomópontos feladatátvevő fürt csomóponttöbbség kvórum modell
* Két szinkron véglegesítésű másodpéldányt a rendelkezésre állási adatbázis egy rendelkezésre állási csoport

Az alábbi ábra a megoldás grafikus ábrázolása.

![Tesztkörnyezet felépítése az Azure-ban rendelkezésre állási csoportokkal](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC791912.png)

Vegye figyelembe, hogy ez a konfiguráció egy lehetséges. Például minimalizálhatja a két-replika rendelkezésre állási csoport a virtuális gépek száma. Ez a konfiguráció mentése az Azure számítási órák a tartományvezérlő a kvórum tanúsító fájlmegosztást egy két csomópontos fürt segítségével. Ez a módszer a virtuális gépek száma csökkenti a illusztrált konfigurációs közül.

Ez az oktatóanyag azt feltételezi, hogy a következő:

* Már rendelkezik Azure-fiókkal.
* Már ismeri a grafikus felhasználói felület használata a virtuális gép gyűjteményben hagyományos SQL Server rendszert futtató virtuális gép kiépítéséhez.
* Már van egy Always On rendelkezésre állási csoportok alapos ismerete. További információkért lásd: [Always On rendelkezésre állási csoportok (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Ha érdekli Always On rendelkezésre állási csoportok használata a SharePoint, [konfigurálása SQL Server 2012 Always On rendelkezésre állási csoportok a SharePoint 2013 rendszerhez](https://technet.microsoft.com/library/jj715261.aspx).
> 
> 

## <a name="create-the-virtual-network-and-domain-controller-server"></a>A virtuális hálózata és tartománya controller-kiszolgáló létrehozása
Új Azure próbafiókkal megkezdése. A fiók beállítása után meg kell lennie a kezdőképernyőn az Azure portálról.

1. Kattintson a **új** a lap alján a bal oldali sarokban lévő gombra kattint, az alábbi képernyőfelvételen látható módon.
   
    ![Kattintson az új a portálon](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665511.gif)
2. Kattintson a **hálózati szolgáltatások** > **virtuális hálózati** > **egyéni létrehozás**, az alábbi képernyőfelvételen látható módon.
   
    ![Virtuális hálózat létrehozása](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665512.gif)
3. Az a **virtuális hálózat létrehozása** párbeszédpanelen hozzon létre egy új virtuális hálózat lépéseit végrehajtani a lapok és a beállítások használatával a következő táblázatban. 
   
   | Oldal | Beállítások |
   | --- | --- |
   | Virtuális hálózat adatai |**NAME = ContosoNET**<br/>**A régióban = USA nyugati régiója** |
   | DNS-kiszolgálók és a VPN-kapcsolat |None |
   | Virtuális hálózat |Az alábbi képernyőfelvételen beállítások láthatók: ![Virtuális hálózat létrehozása](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784620.png) |
4. Hozzon létre a virtuális gépet, akkor a tartományvezérlő (DC) fog használni. Kattintson a **új** > **számítási** > **virtuális gép** > **a gyűjtemény**, ahogy a következő képernyőkép.
   
    ![Virtuális gép létrehozása](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784621.png)
5. Az a **A virtuális gép létrehozása** párbeszédpanelen adja meg egy új virtuális gép által a lapok lépésenkénti végrehajtás, és a beállítások használata az alábbi táblázatban. 
   
   | Oldal | Beállítások |
   | --- | --- |
   | Válassza ki a virtuális gép operációs rendszer |Windows Server 2012 R2 Datacenter |
   | Virtuálisgép-konfiguráció |**VERZIÓ Kiadás dátuma** = (legújabb)<br/>**VIRTUÁLIS gép neve** = ContosoDC<br/>**RÉTEG** = STANDARD<br/>**MÉRET** = A2 (2 mag)<br/>**ÚJ FELHASZNÁLÓNEVET** = AzureAdmin<br/>**Új jelszó** = Contoso! 000<br/>**Erősítse meg** = Contoso! 000 |
   | Virtuálisgép-konfiguráció |**A FELHŐALAPÚ szolgáltatás** = új felhőalapú szolgáltatás létrehozása<br/>**FELHŐALAPÚ szolgáltatás DNS-név** egy egyedi felhőszolgáltatás neve =<br/>**DNS-név** = egyedi nevét (pl.: ContosoDC123)<br/>**RÉGIÓ/AFFINITÁSCSOPORT/virtuális hálózati** = ContosoNET<br/>**VIRTUÁLIS hálózati ALHÁLÓZAT** = Back(10.10.2.0/24)<br/>**A TÁRFIÓK** = egy automatikusan létrehozott tárfiók használata<br/>**A rendelkezésre ÁLLÁSI csoport** = (nincs) |
   | Virtuális gép beállításai |Alapértelmezések használata |

Miután az új virtuális gépet úgy konfigurál, várja meg a virtuális gép nem provsioned. Ez a folyamat befejezése némi időt vesz igénybe. Ha a **virtuális gép** lapon az Azure-portálon a ContosoDC ciklus állapotok a látható **indítása (kiépítés)** való **leállítva**,  **Kezdési**, **fut (kiépítés)**, és végül **futtató**.

A tartományvezérlő kiszolgálóhoz sikeresen most lett kiépítve. A következő, úgy konfigurálja az Active Directory-tartomány a tartományvezérlő kiszolgálóhoz.

## <a name="configure-the-domain-controller"></a>A tartományvezérlő konfigurálása
Az alábbi lépéseket konfigurálja a ContosoDC gépen Corp.contoso.com tartományvezérlőként működik.

1. A portálon, válassza ki a **ContosoDC** gép. Az a **irányítópult** lapra, majd **Connect** kattintva megnyithatja a távoli asztal (RDP) a távoli asztal eléréséhez.
   
    ![Csatlakozás Vritual géphez](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784622.png)
2. Jelentkezzen be a beállított rendszergazdai fiókkal (**\AzureAdmin**) és a jelszót (**Contoso! 000**).
3. Alapértelmezés szerint a **Kiszolgálókezelő** irányítópult üzenetnek kell megjelennie.
4. Kattintson a **szerepkörök és szolgáltatások hozzáadása** az irányítópulton lévő hivatkozásra.
   
    ![Server Explorer szerepkörök hozzáadása](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784623.png)
5. Kattintson a **következő** amíg elér a **kiszolgálói szerepkörök** szakasz.
6. Válassza ki a **Active Directory tartományi szolgáltatások** és **DNS-kiszolgáló** szerepkörök. Amikor a rendszer kéri, adja hozzá ezeket a szerepköröket igénylő szolgáltatásokat.
   
   > [!NOTE]
   > Egy érvényesítési figyelmeztetés, hogy nincs-e statikus IP-cím fog megjelenni. Ha teszteli a konfigurációt, kattintson a **Folytatás**. Éles környezetben [PowerShell segítségével állítsa be a statikus IP-cím a tartomány a tartományvezérlő gép](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   > 
   > 
   
    ![Adja hozzá a szerepkörök párbeszédpanel](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784624.png)
7. Kattintson a **következő** amíg el nem éri a **megerősítő** szakasz. Válassza ki a **a célkiszolgáló automatikus újraindítása, ha szükséges** jelölőnégyzetet.
8. Kattintson az **Install** (Telepítés) gombra.
9. A szolgáltatások telepítése után térjen vissza a **Kiszolgálókezelő** irányítópult.
10. Jelölje be az új **Active Directory tartományi szolgáltatások** lehetőséget a bal oldali ablaktáblán.
11. Kattintson a **további** a sárga figyelmeztető sáv hivatkozásra kattintva.
    
     ![Az Active Directory tartományi szolgáltatások párbeszédpanel DNS-kiszolgáló virtuális gépen](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784625.png)
12. Az a **művelet** oszlopa a **összes kiszolgáló feladat részletei** párbeszédpanel, kattintson a **a számítógép előléptetése tartományvezérlővé**.
13. Az a **Active Directory tartományi szolgáltatások konfigurációs varázslója**, a következő értékeket használja:
    
    | Oldal | Beállítás |
    | --- | --- |
    | Központi telepítés konfigurálása |**Új erdő hozzáadása** kijelölt =<br/>**Gyökértartomány neve** = corp.contoso.com |
    | Tartományvezérlő beállításai |**Jelszó** = Contoso! 000<br/>**Jelszó megerősítése** = Contoso! 000 |
14. Kattintson a **következő** haladhat végig a varázsló többi lapján. Az a **szükséges előfeltételek ellenőrzése** lapon, győződjön meg arról, hogy a következő üzenetet látja: **az előfeltétel-ellenőrzés sikeresen lefutott**. Vegye figyelembe, hogy tekintse át az összes alkalmazható figyelmeztető üzenetek, de lehetséges, a telepítés folytatásához.
15. Kattintson az **Install** (Telepítés) gombra. A **ContosoDC** virtuális gép automatikusan újraindul.

## <a name="configure-domain-accounts"></a>Tartományi fiókok konfigurálása
A következő lépéseket konfigurálja az Active Directory-fiókok későbbi használatra.

1. Jelentkezzen be ismét a a **ContosoDC** gép.
2. A **Kiszolgálókezelő**, kattintson a **eszközök** > **Active Directory felügyeleti központ**.
   
    ![Active Directory felügyeleti központ](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784626.png)
3. Az a **Active Directory felügyeleti központ**, jelölje be **corp (helyi)** a bal oldali ablaktáblán.
4. A jobb oldali **feladatok** ablaktáblán kattintson a **új** > **felhasználói**. A következő beállításokkal:
   
   | Beállítás | Érték |
   | --- | --- |
   | **Utónév** |Telepítés |
   | **Felhasználó SamAccountName** |Telepítés |
   | **Jelszó** |Contoso! 000 |
   | **Jelszó megerősítése** |Contoso! 000 |
   | **Egyéb jelszóbeállítások** |Kiválasztva |
   | **Jelszó sohasem jár le** |Bejelölve |
5. Kattintson a **OK** létrehozásához a **telepítése** felhasználó. Ez a fiók használható a feladatátvevő fürt és a rendelkezésre állási csoport konfigurálása.
6. Hozzon létre két további felhasználó **CORP\SQLSvc1** és **CORP\SQLSvc2**, a ugyanazokat a lépéseket. Ezek a fiókok fog történni az SQL Server-példányokat. Következő lépésként hozzá kell rendelnie **CORP\Install** jogosult arra, hogy konfigurálja a Windows feladatátvételi fürtszolgáltatás.
7. Az a **Active Directory felügyeleti központ**, kattintson a **corp (helyi)** a bal oldali ablaktáblán. Az a **feladatok** ablaktáblán kattintson a **tulajdonságok**.
   
    ![CORP felhasználó tulajdonságai](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784627.png)
8. Válassza ki **bővítmények**, majd kattintson a **speciális** gombra a **biztonsági** lapon.
9. Az a **speciális biztonsági beállítások vállalati** párbeszédpanel, kattintson a **Hozzáadás**.
10. Kattintson a **rendszerbiztonsági tag kiválasztása**, keressen **CORP\Install**, és kattintson a **OK**.
11. Válassza ki a **az összes tulajdonság olvasása** és **számítógép-objektumok létrehozása** engedélyek.
    
     ![Corp felhasználói engedélyek](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784628.png)
12. Kattintson a **OK**, és kattintson a **OK** újra. A vállalat tulajdonságok ablak bezárásához.

Most, hogy konfigurálta az Active Directory és a felhasználói objektumok, hozzon létre három SQL Server virtuális gépeket, és csatlakoztassa azokat a tartományhoz.

## <a name="create-the-sql-server-virtual-machines"></a>Az SQL Server virtuális gépek létrehozása
Hozzon létre három virtuális gépet. Még a fürt összes csomópontjára vonatkozóan, és az SQL Server kettő. Mindegyik virtuális gép létrehozásához lépjen vissza a klasszikus Azure portálon, kattintson a **új** > **számítási** > **virtuális gép**  >  **Gyűjteményből**. Ezt követően az alábbi táblázatban a sablonok használatával alakítsa ki a virtuális gépek létrehozása.

| Oldal | VM1 | VM2 VIRTUÁLIS GÉPNEK | VM3 |
| --- | --- | --- | --- |
| Válassza ki a virtuális gép operációs rendszer |**Windows Server 2012 R2 Datacenter** |**SQL Server 2014 RTM Enterprise** |**SQL Server 2014 RTM Enterprise** |
| Virtuálisgép-konfiguráció |**VERZIÓ Kiadás dátuma** = (legújabb)<br/>**VIRTUÁLIS gép neve** = ContosoWSFCNode<br/>**RÉTEG** = STANDARD<br/>**MÉRET** = A2 (2 mag)<br/>**ÚJ FELHASZNÁLÓNEVET** = AzureAdmin<br/>**Új jelszó** = Contoso! 000<br/>**Erősítse meg** = Contoso! 000 |**VERZIÓ Kiadás dátuma** = (legújabb)<br/>**VIRTUÁLIS gép neve** = ContosoSQL1<br/>**RÉTEG** = STANDARD<br/>**MÉRET** = A3 (4 mag)<br/>**ÚJ FELHASZNÁLÓNEVET** = AzureAdmin<br/>**Új jelszó** = Contoso! 000<br/>**Erősítse meg** = Contoso! 000 |**VERZIÓ Kiadás dátuma** = (legújabb)<br/>**VIRTUÁLIS gép neve** = ContosoSQL2<br/>**RÉTEG** = STANDARD<br/>**MÉRET** = A3 (4 mag)<br/>**ÚJ FELHASZNÁLÓNEVET** = AzureAdmin<br/>**Új jelszó** = Contoso! 000<br/>**Erősítse meg** = Contoso! 000 |
| Virtuálisgép-konfiguráció |**A FELHŐALAPÚ szolgáltatás** = a korábban létrehozott egyedi felhőalapú szolgáltatás DNS-név (pl.: ContosoDC123)<br/>**RÉGIÓ/AFFINITÁSCSOPORT/virtuális hálózati** = ContosoNET<br/>**VIRTUÁLIS hálózati ALHÁLÓZAT** = Back(10.10.2.0/24)<br/>**A TÁRFIÓK** = egy automatikusan létrehozott tárfiók használata<br/>**Rendelkezésre ÁLLÁSI csoport** = létrehozása rendelkezésre állási beállítása<br/>**RENDELKEZÉSRE ÁLLÁSI KÉSZLET NEVE** = SQLHADR |**A FELHŐALAPÚ szolgáltatás** = a korábban létrehozott egyedi felhőalapú szolgáltatás DNS-név (pl.: ContosoDC123)<br/>**RÉGIÓ/AFFINITÁSCSOPORT/virtuális hálózati** = ContosoNET<br/>**VIRTUÁLIS hálózati ALHÁLÓZAT** = Back(10.10.2.0/24)<br/>**A TÁRFIÓK** = egy automatikusan létrehozott tárfiók használata<br/>**Rendelkezésre ÁLLÁSI csoport** = SQLHADR (beállíthatja úgy is a rendelkezésre állási csoportot, a gép létrehozása után. Mind a három gépet rendelhető hozzá a SQLHADR rendelkezésre állási csoportot.) |**A FELHŐALAPÚ szolgáltatás** = a korábban létrehozott egyedi felhőalapú szolgáltatás DNS-név (pl.: ContosoDC123)<br/>**RÉGIÓ/AFFINITÁSCSOPORT/virtuális hálózati** = ContosoNET<br/>**VIRTUÁLIS hálózati ALHÁLÓZAT** = Back(10.10.2.0/24)<br/>**A TÁRFIÓK** = egy automatikusan létrehozott tárfiók használata<br/>**Rendelkezésre ÁLLÁSI csoport** = SQLHADR (beállíthatja úgy is a rendelkezésre állási csoportot, a gép létrehozása után. Mind a három gépet rendelhető hozzá a SQLHADR rendelkezésre állási csoportot.) |
| Virtuális gép beállításai |Alapértelmezések használata |Alapértelmezések használata |Alapértelmezések használata |

<br/>

> [!NOTE]
> A korábbi konfigurációt szabványos réteg virtuális gépein, javasol, mert az ALAPSZINTŰ csomag gépek nem támogatják az elosztott terhelésű végpont. Elosztott terhelésű végpont később egy rendelkezésre állási csoport figyelőjének létrehozásához szükséges. Emellett a méreteket, az itt közölt célja, Azure virtuális gépek rendelkezésre állási csoportok teszteléshez. A legjobb teljesítmény érdekében a termelési számítási feladatokhoz, javaslatok az SQL Server-gépek méretét és a konfigurációs [teljesítmény ajánlott eljárások az SQL Server Azure virtuális gépek](../sql/virtual-machines-windows-sql-performance.md).
> 
> 

Miután a három virtuális gépek teljesen kiépített, kell-e csatlakoztassa azokat a **corp.contoso.com** tartomány- és támogatási CORP\Install rendszergazdai jogosultságokkal a gépek. Ehhez a következő lépésekkel minden három virtuális gépet.

1. Először módosítsa az elsődleges DNS-kiszolgáló címét. Minden virtuális gép RDP-fájl letöltése a helyi könyvtárba jelölje ki a virtuális gép a listában, kattintson a **Connect** gombra. Válasszon ki egy virtuális gépet, kattintson a bárhol az első cellájára, de az alábbi képernyőfelvételen látható módon.
   
    ![Az RDP-fájl letöltése](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664953.jpg)
2. Nyissa meg a letöltött, és a beállított rendszergazda fiók használatával jelentkezzen be a virtuális gép RDP-fájlt (**BUILTIN\AzureAdmin**) és a jelszót (**Contoso! 000**).
3. Miután bejelentkezik, láthatja a **Kiszolgálókezelő** irányítópult. Kattintson a **helyi kiszolgáló** a bal oldali ablaktáblán.
4. Kattintson a **IPv4-cím IPv6 engedélyezett DHCP által hozzárendelt** hivatkozásra.
5. Az a **hálózati kapcsolatok** párbeszédpanelen kattintson a hálózat ikonra.
   
    ![Módosítsa a virtuális gép előnyben részesített DNS-kiszolgáló](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784629.png)
6. A parancssávon kattintson **módosíthatja a beállításokat a kapcsolat**. (Attól függően, hogy az ablak mérete, lehetséges, hogy a dupla jobbra mutató nyílra kattintva tekintse meg ezt a parancsot a).
7. Válassza ki **Internet Protocol Version 4 (TCP/IPv4)**, és kattintson a **tulajdonságok**.
8. Válassza ki **a következő DNS-kiszolgálócímek használata** , majd adja meg **10.10.2.4** a **elsődleges DNS-kiszolgáló**.
9. A **10.10.2.4** a cím, amely a 10.10.2.0/24 alhálózat az Azure virtuális hálózat egy virtuális géphez van rendelve. A virtuális gép **ContosoDC**. Győződjön meg arról, hogy **ContosoDC**tartozó IP-cím használata **nslookup contosodc** a parancssori ablakban, a következő képernyőfelvételen látható módon.
   
    ![A tartományvezérlő IP-címének NSLOOKUP segítségével](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664954.jpg)
10. Kattintson a **OK** > **Bezárás** a módosítások véglegesítéséhez. Most csatlakozhat a virtuális gép **corp.contoso.com**.
11. Vissza a **helyi kiszolgáló** ablak, kattintson a **munkacsoport** hivatkozásra.
12. Az a **számítógépnév** kattintson **módosítása**.
13. Válassza ki a **tartomány** jelölőnégyzetet, írja be **corp.contoso.com** a szövegmezőbe, majd **OK**.
14. Az a **Windows biztonsági** párbeszédpanelen adja meg az alapértelmezett tartományi rendszergazdai fiók hitelesítő adatait (**CORP\AzureAdmin**) és a jelszót (**Contoso! 000**).
15. Kattintson a "Üdvözli a corp.contoso.com tartomány" üzenet jelenik meg, amikor **OK**.
16. Kattintson a **Bezárás** > **Újraindítás most** a párbeszédpanelen.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-virtual-machine"></a>A Corp\Install felhasználó hozzáadása rendszergazdaként minden egyes virtuális gépen
1. Várjon, amíg a virtuális gép újraindul, és ezután nyissa meg az RDP fájlt ismét jelentkezzen be a virtuális gép használata a **BUILTIN\AzureAdmin** fiók.
2. A **Kiszolgálókezelő** kattintson **eszközök** > **számítógép-kezelés**.
   
    ![Számítógép-kezelés](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784630.png)
3. Az a **számítógép-kezelés** párbeszédpanelen bontsa ki **helyi felhasználók és csoportok**, és kattintson a **csoportok**.
4. Kattintson duplán a **rendszergazdák** csoport.
5. Az a **rendszergazdák tulajdonságok** párbeszédpanel, kattintson a **Hozzáadás** gombra.
6. Adja meg a felhasználó **CORP\Install**, és kattintson a **OK**. Ha a rendszer kéri a hitelesítő adatokat, a **AzureAdmin** rendelkező fiók a **Contoso! 000** jelszót.
7. Kattintson a **OK** bezárásához a **rendszergazda tulajdonságainak** párbeszédpanel megnyitásához.

### <a name="add-the-failover-clustering-feature-to-each-virtual-machine"></a>Vegye fel a Feladatátvételi fürtszolgáltatást minden egyes virtuális géphez
1. Az a **Kiszolgálókezelő** irányítópultján kattintson **szerepkörök és szolgáltatások hozzáadása**.
2. Az a **hozzáadása szerepkörök és szolgáltatások varázsló**, kattintson a **következő** amíg elér a **szolgáltatások** lap.
3. Válassza ki **feladatátvételi fürtszolgáltatás**. Amikor a rendszer kéri, adja hozzá az egyéb szolgáltatások.
   
    ![Adja hozzá a Feladatátvételi fürtszolgáltatást a virtuális gép](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784631.png)
4. Kattintson a **következő**, és kattintson a **telepítése** a a **megerősítő** lap.
5. Ha a **feladatátvételi fürtszolgáltatás** szolgáltatás telepítése befejeződött, kattintson a **Bezárás**.
6. Jelentkezzen ki a virtuális gép.
7. Ismételje meg a jelen szakaszban szereplő lépéseket az összes három: **ContosoWSFCNode**, **ContosoSQL1**, és **ContosoSQL2**.

A virtuális gépek most már kiépített SQL Server és fut, de egyes rendelkezik az SQL Server az alapértelmezett beállításokat.

## <a name="create-the-failover-cluster"></a>A feladatátvevő fürt létrehozása
Ebben a szakaszban hoz létre a feladatátvevő fürt, amely a rendelkezésre állási csoportot, amely később létrehozhat tárolni fogja. Mostanra elvégezte kell a következő három virtuális gépen a feladatátvevő fürtben használni kívánt minden egyes:

* Teljesen kiépítve a virtuális gép az Azure-ban
* A virtuális gép csatlakozik a tartományhoz
* Hozzáadott **CORP\Install** a helyi rendszergazdák csoporthoz
* A Feladatátvételi fürtszolgáltatás hozzáadva

Minden olyan Előfeltételek minden egyes virtuális gépen a feladatátvevő fürt csatlakoztatása előtt.

Vegye figyelembe azt is, hogy az Azure virtuális hálózat nem működik ugyanúgy, mint egy helyszíni hálózat. A fürt létrehozása a következő sorrendben kell:

1. Hozzon létre egy egycsomópontos fürt egyik csomópontján (**ContosoSQL1**).
2. A fürt IP-címet nem használt IP-címének módosítása (**10.10.2.101**).
3. Kapcsolja a hálózatra a fürt nevét.
4. A többi csomópont hozzáadása (**ContosoSQL2** és **ContosoWSFCNode**).

A feladatokat, amelyek a fürt teljes konfigurálásához tegye a következőket.

1. Nyissa meg az RDP-fájljának **ContosoSQL1**, és jelentkezzen be a tartományi fiók használatával **CORP\Install**.
2. Az a **Kiszolgálókezelő** irányítópultján kattintson **eszközök** > **Feladatátvevőfürt-kezelő**.
3. A bal oldali ablaktáblán kattintson jobb gombbal **Feladatátvevőfürt-kezelő**, és kattintson a **hozzon létre egy fürtöt**, az alábbi képernyőfelvételen látható módon.
   
    ![Fürt létrehozása](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784632.png)
4. A fürt létrehozása varázsló hozzon létre egy egy csomópontos fürtre lépéseit végrehajtani a lapok és a beállítások használatával a következő táblázatban:
   
   | Oldal | Beállítások |
   | --- | --- |
   | Előkészületek |Alapértelmezések használata |
   | Kiszolgálók kiválasztása |Típus **ContosoSQL1** a **kiszolgáló nevének megadása** kattintson **hozzáadása** |
   | Érvényesítési figyelmeztetés |Válassza ki **szám I nem igényel Microsoft-támogatást ehhez a fürthöz, és ezért nem szeretné, hogy az érvényesítési tesztek futtatásához. A Tovább gombra kattintva, továbbra is a fürt létrehozása**. |
   | Hozzáférési pont a fürt felügyeletéhez |Típus **Cluster1** a **a fürt neve** |
   | Megerősítés |Használhatja az alapértelmezett értékeket, kivéve, ha a tárolóhelyek használata. Tekintse meg a táblázat utáni figyelmeztetést. |
   
   > [!WARNING]
   > Használata [tárolóhelyek](https://technet.microsoft.com/library/hh831739), amely több lemezre csoportok tárolókészletekbe, törölje a jelölést a **minden megfelelő tároló felvétele a fürt** jelölőnégyzetet a **megerősítő**lap. Ne törölje ezt a beállítást, ha a virtuális lemezek a csoportosítási eljárás során választható le. Ennek eredményeképpen ezek fog is csak akkor jelennek meg lemezkezelő vagy Explorer a tárolóhelyek eltávolították a fürtből, és a PowerShell használatával objektumkörnyezetben.
   > 
   > 
5. A bal oldali ablaktáblán bontsa ki a **Feladatátvevőfürt-kezelő**, és kattintson a **Cluster1.corp.contoso.com**.
6. A középső ablaktáblán, görgessen le a **fürt alapvető erőforrásai** szakaszt, és bontsa ki a **Name: Clutser1** részletei. Mindkét kell megjelennie a **neve** és a **IP-cím** erőforrásokat a **sikertelen** állapotát. Az IP-cím erőforrás nem hozható online állapotba, mert a fürt hozzá van rendelve a azonos IP-cím a gép, mint ez az ismétlődő címet.
7. Kattintson a jobb gombbal az a sikertelen **IP-cím** erőforrás, és kattintson **tulajdonságok**.
   
    ![Fürt tulajdonságai](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784633.png)
8. Válassza ki **statikus IP-cím**, adja meg **10.10.2.101** a a **cím** szövegmezőbe, és kattintson **OK**.
9. Az a **fürt alapvető erőforrásai** területen kattintson a jobb gombbal **Name: Cluster1**, és kattintson a **online állapotba hozás**. Várjon, amíg mindkét erőforrás online. A fürt hálózatnév-erőforrás online állapotba kerül, ha a tartományvezérlő kiszolgálóhoz egy új Active Directory-számítógépfiókja frissül. Az Active Directory-fiók használható a rendelkezésre állási csoport fürtözött szolgáltatás később futtatni.
10. Adja hozzá a többi csomópontot a fürthöz. A böngésző konzolfáján kattintson a jobb gombbal **Cluster1.corp.contoso.com**, és kattintson a **csomópont hozzáadása**, az alábbi képernyőfelvételen látható módon.
    
     ![Csomópont hozzáadása a fürthöz](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784634.png)
11. Az a **csomópont hozzáadása varázsló**, kattintson a **következő** a a **kiszolgálók kiválasztása** lapon **ContosoSQL2** és **ContosoWSFCNode**  a listára, írja be a kiszolgáló nevére a **kiszolgáló nevének megadása** majd **Hozzáadás**. Amikor elkészült, kattintson a **következő**.
12. Az a **érvényesítési figyelmeztetés** kattintson **nem**, bár a egy éles telepítési forgatókönyvhöz, végre kell hajtania az ellenőrző teszteket. Ezután kattintson a **Tovább** gombra.
13. Az a **megerősítő** kattintson **tovább** a csomópontokat.
    
    > [!WARNING]
    > Ha használ [tárolóhelyek](https://technet.microsoft.com/library/hh831739), amely több lemezre csoportok tárolókészletekbe, törölje a jelölést a **minden megfelelő tároló felvétele a fürt** jelölőnégyzetet. Ne törölje ezt a beállítást, ha a virtuális lemezek a csoportosítási eljárás során választható le. Ennek eredményeképpen is nem fog megjelenni lemezkezelő vagy Explorer mindaddig, amíg a tárolóhelyek fürt el lesznek távolítva, és objektumkörnyezetben a PowerShell használatával.
    > 
    > 
14. Miután a csomópontokat ad hozzá a fürtöt, kattintson **Befejezés**. Feladatátvevőfürt-kezelő most meg kell jelennie, hogy a fürt három csomópontja rendelkezik-e, és rendezze őket a a **csomópontok** tároló.
15. Jelentkezzen ki a távoli asztali munkamenetben.

## <a name="prepare-the-sql-server-instances-for-availability-groups"></a>Az SQL Server-példányok előkészítése rendelkezésre állási csoportok
Ebben a szakaszban a következő mind ennek **ContosoSQL1** és **contosoSQL2**:

* Adja hozzá a bejelentkezési adatait a következő **NT AUTHORITY\System** az alapértelmezett SQL Server-példány beállítása szükséges engedélyekkel.
* Adja hozzá **CORP\Install** SysAdmin (rendszergazda) szerepkör alapértelmezett SQL Server-példányra.
* Nyissa meg a tűzfalon az SQL Server távoli hozzáféréshez.
* Az Always On rendelkezésre állási csoportok szolgáltatás engedélyezése.
* Az SQL Server-szolgáltatásfiók módosítása **CORP\SQLSvc1** és **CORP\SQLSvc2**, illetve.

Bármilyen sorrendben ezeket a műveleteket végezheti el. Mindazonáltal a következő lépésekkel haladhat révén azokat sorrendben. Kövesse a lépéseket mindkét **ContosoSQL1** és **ContosoSQL2**:

1. Ha a virtuális géphez a távoli asztali munkamenetben nem kijelentkezett, tegye meg.
2. Nyissa meg az RDP-fájlok **ContosoSQL1** és **ContosoSQL2**, és jelentkezzen be a **BUILTIN\AzureAdmin**.
3. Adja hozzá **NT AUTHORITY\System** az SQL Server bejelentkezések szükséges engedélyekkel. Nyissa meg az **SQL Server Management Studiót**.
4. Kattintson a **Connect** alapértelmezett SQL Server-példányhoz való csatlakozáshoz.
5. A **Object Explorer**, bontsa ki a **biztonsági**, majd bontsa ki a **bejelentkezések**.
6. Kattintson a jobb gombbal a **NT AUTHORITY\System** bejelentkezési, és kattintson **tulajdonságok**.
7. Az a **Securables** lap, a helyi kiszolgálón, akkor jelölje be a **Grant** a következő engedélyeket, és kattintson a **OK**.
   
   * Az ALTER bármely rendelkezésre állási csoport
   * Csatlakozás SQL
   * Kiszolgáló állapotának megtekintése
8. Adja hozzá **CORP\Install** , egy **sysadmin** szerepkör az alapértelmezett SQL Server-példányhoz. A **Object Explorer**, kattintson a jobb gombbal **bejelentkezések**, és kattintson a **új bejelentkezés**.
9. Típus **CORP\Install** a **bejelentkezési név**.
10. Az a **kiszolgálói szerepkörök** lapon jelölje be **sysadmin**, és kattintson a **OK**. Miután létrehozta a bejelentkezés, tekintheti meg kibontásával **bejelentkezések** a **Object Explorer**.
11. Egy tűzfalszabály létrehozására az SQL Server, a a **Start** nyissa meg **fokozott biztonságú Windows tűzfal**.
12. A bal oldali panelen válassza ki a **bejövő szabályok**. Kattintson a jobb oldali ablaktáblában **új szabály**.
13. Az a **szabálytípus** kattintson **Program** > **következő**.
14. Az a **Program** lapon jelölje be **Ez a program elérési útja**, típus **%ProgramFiles%\Microsoft SQL Server\MSSQL12. MSSQLSERVER\MSSQL\Binn\sqlservr.exe** a szövegmezőbe, majd **következő**. Ha ezt az útmutatót követve, de a SQL Server 2012, SQL Server van **MSSQL11. MSSQLSERVER**.
15. Az a **művelet** lapon, tartsa **a kapcsolat engedélyezéséhez** kiválasztva, és kattintson **következő**.
16. Az a **profil** lapon fogadja el az alapértelmezett beállításokat, és kattintson a **következő**.
17. A a **neve** lapján adja meg a szabály nevét, például a **SQL Server (Program szabály)**, a a **neve** szöveg mezőbe, majd kattintson az **Befejezés**.
18. Ahhoz, hogy a **Always On rendelkezésre állási csoportok** funkció, a a **Start** nyissa meg **SQL Server Configuration Manager**.
19. A böngésző konzolfáján kattintson **SQL Server Services**, kattintson a jobb gombbal a **SQL Server (MSSQLSERVER)** szolgáltatásra, és kattintson a **tulajdonságok**.
20. Kattintson a **magas rendelkezésre állású mindig a** lapon jelölje be **engedélyezze az Always On rendelkezésre állási csoportok**, ahogy az alábbi képernyőfelvételen, majd **alkalmaz**. Kattintson a **OK** az párbeszédpanel, és zárja be a a **tulajdonságok** még a párbeszédpanel bezárásához. Az SQL Server szolgáltatás újraindul a szolgáltatás fiók módosítása után.
    
     ![A rendelkezésre állási csoportok mindig engedélyezése](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665520.gif)
21. Az SQL Server szolgáltatásfiókja módosításához kattintson a **bejelentkezés** fülre, írja be **CORP\SQLSvc1** (a **ContosoSQL1**) vagy **CORP\SQLSvc2** (a **ContosoSQL2**) a **fióknév**, adja meg és erősítse meg a jelszót, majd **OK**.
22. A megnyíló párbeszédpanelen kattintson a **Igen** az SQL Server szolgáltatás újraindításához. Az SQL Server szolgáltatás újraindítása után módosítja, amikor végzett a **tulajdonságok** párbeszédpanel érvényben.
23. Jelentkezzen ki a virtuális gépek.

## <a name="create-the-availability-group"></a>A rendelkezésre állási csoport létrehozása
Most már készen áll a rendelkezésre állási csoport konfigurálásához. Alább mit fog röviden van:

* Hozzon létre egy új adatbázist (**MyDB1**) a **ContosoSQL1**.
* Teljes biztonsági mentés és a tranzakciós napló biztonsági mentését az adatbázis is igénybe vehet.
* Állítsa vissza a teljes és a biztonsági mentés jelentkezzen **ContosoSQL2** rendelkező a **NORECOVERY** lehetőséget.
* A rendelkezésre állási csoport létrehozása (**AG1**) szinkron véglegesítésre, automatikus feladatátvétel és olvasható másodlagos másodpéldányokra.

### <a name="create-the-mydb1-database-on-contososql1"></a>ContosoSQL1 a MyDB1 adatbázis létrehozásához.
1. Ha nem már kijelentkezett a távoli asztali munkamenetek **ContosoSQL1** és **ContosoSQL2**, most tegye meg.
2. Nyissa meg az RDP-fájljának **ContosoSQL1**, és jelentkezzen be a **CORP\Install**.
3. A **Fájlkezelőben**a **C:\\**, hozzon létre egy könyvtárat nevű **biztonsági mentési**. Ez a könyvtár használandó biztonsági mentése és visszaállítása az adatbázis.
4. Kattintson jobb gombbal az új könyvtár **megosztása**, és kattintson a **adott személyek**, az alábbi képernyőfelvételen látható módon.
   
    ![Hozzon létre egy biztonsági mentési mappát](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665521.gif)
5. Adja hozzá **CORP\SQLSvc1**, és adjon neki a **olvasási/írási** engedéllyel. Adja hozzá **CORP\SQLSvc2**, és adjon neki a **olvasási** engedéllyel, akkor az alábbi képernyőfelvételen, és kattintson az **megosztás**. A fájlmegosztási folyamat befejezése után kattintson **végzett**.
   
    ![Engedélyeket a biztonsági mentési mappája](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665522.gif)
6. Létrehozza az adatbázist, a a **Start** menü megnyitása **SQL Server Management Studio**, és kattintson a **Connect** csatlakozni az alapértelmezett SQL Server-példány.
7. A **Object Explorer**, kattintson a jobb gombbal **adatbázisok**, és kattintson a **új adatbázis**.
8. A **adatbázisnév**, típus **MyDB1**, és kattintson a **OK**.

### <a name="make-a-full-backup-of-mydb1-and-restore-it-on-contososql2"></a>Készítsen teljes biztonsági másolatot az MyDB1, majd állítsa vissza azt a ContosoSQL2
1. Az adatbázis teljes biztonsági másolatot készíteni a **Object Explorer**, bontsa ki a **adatbázisok**, kattintson a jobb gombbal **MyDB1**, mutasson a **feladatok**, majd Kattintson a **készítsen biztonsági másolatot**.
2. Az a **forrás** területen tartsa **biztonsági másolat típusa** beállítása **teljes**. Az a **cél** kattintson **eltávolítása** eltávolítása az alapértelmezett elérési utat a biztonságimásolat-fájl.
3. Az a **cél** kattintson **Hozzáadás**.
4. Az a **Fájlnév** szövegmezőben  **\\ContosoSQL1\backup\MyDB1.bak**, kattintson a **OK**, és kattintson a **OK** újra a biztonsági másolatot az adatbázisról. A biztonsági mentési művelet befejezése után kattintson **OK** a párbeszédpanel bezárásához.
5. A tranzakciós napló biztonsági mentését az adatbázis, hogy a **Object Explorer**, bontsa ki a **adatbázisok**, kattintson a jobb gombbal **MyDB1**, mutasson a **feladatok**, majd **készítsen biztonsági másolatot**.
6. A **biztonsági másolat típusa**, jelölje be **tranzakciónapló**. Tartsa a **cél** korábban megadott elérési út set fájlt, és kattintson a **OK**. A biztonsági mentési művelet befejezése után kattintson **OK** újra.
7. Állítsa vissza a teljes telepítési és a tranzakció bejelentkeznie biztonsági mentések **ContosoSQL2**, nyissa meg az RDP-fájljának **ContosoSQL2**, és jelentkezzen be a **CORP\Install**. A távoli asztali munkamenet hagyja **ContosoSQL1** megnyitásához.
8. A a **Start** menü megnyitása **SQL Server Management Studio**, és kattintson a **Connect** alapértelmezett SQL Server-példányhoz való csatlakozáshoz.
9. A **Object Explorer**, kattintson a jobb gombbal **adatbázisok**, és kattintson a **Restore Database**.
10. Az a **forrás** szakaszban jelölje be **eszköz**, majd kattintson a három ponttal **...** gombra.
11. A **válassza ki a biztonsági mentési eszközöket**, kattintson a **Hozzáadás**.
12. A **biztonsági másolat fájl helye**, típus  **\\ContosoSQL1\backup**, kattintson a **frissítése**, jelölje be **MyDB1.bak**, kattintson **OK**, és kattintson a **OK** újra. Meg kell jelennie a teljes biztonsági mentés és a Naplók biztonsági mentése az a **biztonságimásolat-készletet visszaállítása** ablaktáblán.
13. Lépjen a **beállítások** lapon jelölje be **RESTORE WITH NORECOVERY** a **helyreállítási állapota**, és kattintson a **OK** állítsa vissza az adatbázist. A visszaállítási művelet befejezése után kattintson **OK**.

### <a name="create-the-availability-group"></a>A rendelkezésre állási csoport létrehozása
1. Lépjen vissza a távoli asztali munkamenet **ContosoSQL1**. A **Object Explorer** az SQL Server Management Studio, kattintson a jobb gombbal **magas rendelkezésre állású mindig a**, és kattintson a **új rendelkezésre állási csoport varázsló**, ahogy a következő képernyőkép.
   
    ![Új rendelkezésre állási csoport létrehozása varázsló elindítása](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665523.gif)
2. Az a **bemutatása** kattintson **következő**. A a **adja meg a rendelkezésre állási csoport nevének** írja be **AG1** a **rendelkezésre állási csoport nevének**, majd kattintson a **következő** újra.
   
    ![Új rendelkezésre állási csoport varázsló, a rendelkezésre állási csoport nevének megadása](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665524.gif)
3. A a **adatbázisok kiválasztása** lapon, hogy melyik **MyDB1**, és kattintson a **tovább**. Az adatbázis egy rendelkezésre állási csoport előfeltételeit megfelel, mivel legalább egy teljes biztonsági mentés elvégezte a kívánt elsődleges replikán.
   
    ![Új Availabilty csoport varázsló, jelölje be adatbázisok](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665525.gif)
4. a a **meg replikák** kattintson **adja hozzá a replika**.
   
    ![Új Availabilty csoport varázsló, adjon meg replikák](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665526.gif)
5. Az a **kapcsolódás a kiszolgálóhoz** párbeszédpanelen írja be **ContosoSQL2** a **kiszolgálónév**, és kattintson a **Connect**.
   
    ![Új Availabilty csoport varázsló, a Kapcsolódás a kiszolgálóhoz](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665527.gif)
6. Újra be a **meg replikák** lapon meg kell jelennie **ContosoSQL2** felsorolt **elérhető replikák**. Konfigurálja a replikákat az alábbi képernyőfelvételen látható módon. Ha elkészült, kattintson a **következő**.
   
    ![Új Availabilty csoport varázsló, adjon meg replikák (kész)](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665528.gif)
7. Az a **kezdeti adatszinkronizálás kiválasztása** lapon, hogy melyik **csak összekapcsolás**, és kattintson a **következő**. Ön már végrehajtotta a adatszinkronizálás manuálisan történő a teljes telepítési és a tranzakciós biztonsági mentések **ContosoSQL1** és vissza őket **ContosoSQL2**. Ha szeretné, nem a biztonsági másolat és visszaállítási műveleteket az adatbázishoz, és ehelyett válassza **teljes** ahhoz, hogy az új rendelkezésre állási csoport varázsló adatszinkronizálás végezheti el. Azonban nem ajánlott ezt a beállítást, az egyes vállalatoknál nagyon nagy méretű adatbázisokhoz.
   
    ![Új Availabilty csoport varázsló, a kezdeti adatszinkronizálás kiválasztása](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665529.gif)
8. Az a **érvényesítési** kattintson **következő**. Ezen a lapon az alábbi képernyőfelvételhez hasonlóan kell kinéznie. A figyelő konfigurációjába vonatkozó figyelmeztetés van, mert nem állított be egy rendelkezésre állási csoport figyelőjét. Figyelmen kívül hagyhatja ezt a figyelmeztetést, mert ez az oktatóanyag nem konfigurálja a figyelőt. Ez az oktatóanyag befejezése után a figyelő konfigurálásához lásd: [egy ILB figyelőt az Always On rendelkezésre állási csoportok konfigurálása az Azure-](../classic/ps-sql-int-listener.md).
   
    ![Új Availabilty csoport varázsló érvényesítése](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665530.gif)
9. A a **összegzés** kattintson **Befejezés**, és várjon, amíg a varázsló konfigurálja az új rendelkezésre állási csoport. Az a **folyamatban** lap, kattintson **további részleteket** részletes előrehaladásának megtekintéséhez. A varázsló befejezése után vizsgálja meg a **eredmények** győződjön meg arról, hogy a rendelkezésre állási csoport sikeresen létrejött, az alábbi képernyőfelvételen látható módon a lapon, majd **Bezárás** a varázslóból való kilépéshez.
   
    ![Új Availabilty csoport varázsló, eredmények](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665531.gif)
10. A **Object Explorer**, bontsa ki a **magas rendelkezésre állású mindig a**, majd bontsa ki a **rendelkezésre állási csoportok**. Meg kell jelennie az új rendelkezésre állási csoport ebben a tárolóban. Kattintson a jobb gombbal **AG1 (elsődleges)**, és kattintson a **irányítópult megjelenítése**.
    
     ![Rendelkezésre állási csoport megjelenítése irányítópult](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665532.gif)
11. A **mindig irányítópult** egy, az alábbi képernyőfelvételen a hasonlóan kell kinéznie. A replikákat, a feladatátvételi módot minden egyes replikához, és a szinkronizálás állapotát tekintheti meg.
    
     ![Rendelkezésre állási csoport Irányítópult](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665533.gif)
12. Lépjen vissza **Kiszolgálókezelő**, kattintson a **eszközök**, majd nyissa meg **Feladatátvevőfürt-kezelő**.
13. Bontsa ki a **Cluster1.corp.contoso.com**, majd bontsa ki a **szolgáltatások és alkalmazások**. Válassza ki **szerepkörök** és vegye figyelembe, hogy a **AG1** rendelkezésre állási csoport szerepkör létrejött. Ne feledje, hogy AG1 nem egy IP-címet, mely adatbázis ügyfelek csatlakozhatnak a rendelkezésre állási csoportot, mert nincs konfigurálva egy figyelő. Csatlakozhat közvetlenül az elsődleges csomópont olvasási és írási műveletek és a másodlagos csomópont csak olvasható lekérdezések.
    
     ![A Feladatátvevőfürt-kezelő AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665534.gif)

> [!WARNING]
> Ne próbálja meg a rendelkezésre állási csoport a Feladatátvevőfürt-kezelőből feladatátvételt. Az összes feladatátvételi műveleteket kell elvégezni belül **mindig irányítópult** az SQL Server Management Studio. További információkért lásd: [korlátozások használja a Feladatátvevőfürt-kezelőjén rendelkezésre állási csoportokkal](https://msdn.microsoft.com/library/ff929171.aspx).
> 
> 

## <a name="next-steps"></a>Következő lépések
Most már sikeresen megvalósítását SQL Server Always On rendelkezésre állási csoport létrehozása az Azure-ban. A figyelő a rendelkezésre állási csoport konfigurálásához lásd: [egy ILB figyelőt az Always On rendelkezésre állási csoportok konfigurálása az Azure-](../classic/ps-sql-int-listener.md).

Egyéb Azure-ban az SQL Server használatával kapcsolatos információkért lásd: [SQL Server Azure virtuális gépeken](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

