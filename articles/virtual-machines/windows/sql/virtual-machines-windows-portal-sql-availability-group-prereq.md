---
title: 'Oktatóanyag: A rendelkezésre állási csoport előfeltételei'
description: Ez az oktatóanyag bemutatja, hogyan konfigurálhatja az SQL Server Mindig rendelkezésre állási csoport létrehozásának előfeltételeit az Azure virtuális gépeken.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: c80a4c07e0649612b4c024cac79833c4b730f55e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060170"
---
# <a name="prerequisites-for-creating-always-on-availability-groups-on-sql-server-on-azure-virtual-machines"></a>Az Always On rendelkezésre állási csoportok létrehozásának előfeltételei az SQL Server en az Azure virtuális gépeken

Ez az oktatóanyag bemutatja, hogyan hajtsa végre az SQL Server Always On availability csoport létrehozásának előfeltételeit [az Azure virtuális gépeken .](virtual-machines-windows-portal-sql-availability-group-tutorial.md) Ha befejezte az előfeltételeket, rendelkezik egy tartományvezérlővel, két SQL Server virtuális gépel és egy tanúsító kiszolgálóval egyetlen erőforráscsoportban.

**Időbecslés:** Az előfeltételek teljesítéséhez eltarthat néhány óráig. Ennek az időnek a nagy részét virtuális gépek létrehozásával tölti.

Az alábbi ábra bemutatja, mit épít az oktatóanyagban.

![Rendelkezésre állási csoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Elérhetőségi csoport dokumentációjának áttekintése

Ez az oktatóanyag feltételezi, hogy alapvető ismereteket szerzett az SQL Server Always On rendelkezésre állási csoportokról. Ha nem ismeri ezt a technológiát, [olvassa el a Mindig rendelkezésre állási csoportok (SQL Server) áttekintése című témakört.](https://msdn.microsoft.com/library/ff877884.aspx)


## <a name="create-an-azure-account"></a>Azure-fiók létrehozása
Rendelkeznie kell Azure-fiókkal. [Megnyithat egy ingyenes Azure-fiókot,](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic&redirectURL=https:%2F%2Fazure.microsoft.com%2Fget-started%2Fwelcome-to-azure%2F&correlationId=24f9d452-1909-40d7-b609-2245aa7351a6&l=en-US) vagy [aktiválhatja a Visual Studio előfizetői előnyeit.](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits)

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Ide **+** kattintva új objektumot hozhat létre a portálon.

   ![Új objektum](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Írja be **az erőforráscsoportot** a **Piactér** keresési ablakába.

   ![Erőforráscsoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Kattintson **az Erőforráscsoport gombra.**
5. Kattintson **a Létrehozás gombra.**
6. Az **Erőforráscsoport neve mezőbe**írja be az erőforráscsoport nevét. Írja be például az **sql-ha-rg**.
7. Ha több Azure-előfizetéssel rendelkezik, ellenőrizze, hogy az előfizetés az az Azure-előfizetés, amelyben létre szeretné hozni a rendelkezésre állási csoportot.
8. Válasszon ki egy helyet. A hely az Azure-régió, ahol létre szeretné hozni a rendelkezésre állási csoportot. Ez a cikk egyetlen Azure-helyen építi fel az összes erőforrást.
9. Ellenőrizze, hogy **az irányítópulton a Rögzítés** jelölőnégyzet be van-e jelölve. Ez a választható beállítás egy parancsikont helyez el az erőforráscsoport számára az Azure Portal irányítópultján.

   ![Erőforráscsoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Az erőforráscsoport létrehozásához kattintson a **Létrehozás** lehetőségre.

Az Azure létrehozza az erőforráscsoportot, és egy parancsikont tűz ki a portálon az erőforráscsoporthoz.

## <a name="create-the-network-and-subnets"></a>A hálózat és az alhálózatok létrehozása
A következő lépés a hálózatok és az alhálózatok létrehozása az Azure erőforráscsoportban.

A megoldás egy virtuális hálózatot használ két alhálózattal. A [virtuális hálózat áttekintése](../../../virtual-network/virtual-networks-overview.md) további információt nyújt az Azure-beli hálózatokról.

A virtuális hálózat létrehozása:

1. Az Azure Portalon az erőforráscsoportban kattintson a **+ Hozzáadás gombra.** 

   ![Új elem](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Keressen **virtuális hálózatot**.

     ![Keresés a virtuális hálózatban](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Kattintson **a Virtuális hálózat gombra.**
4. A **virtuális hálózaton**kattintson az **Erőforrás-kezelő** telepítési modelljére, majd a **Létrehozás gombra.**

    Az alábbi táblázat a virtuális hálózat beállításait mutatja be:

   | **Mező** | Érték |
   | --- | --- |
   | **Név** |autoHAVNET |
   | **Címtér** |10.33.0.0/24 |
   | **Alhálózat neve** |Rendszergazda |
   | **Alhálózati címtartomány** |10.33.0.0/29 |
   | **Előfizetés** |Adja meg a használni kívánt előfizetést. **Az előfizetés** üres, ha csak egy előfizetéssel rendelkezik. |
   | **Erőforráscsoport** |Válassza **a Meglévő használata lehetőséget,** és válassza ki az erőforráscsoport nevét. |
   | **Helyen** |Adja meg az Azure helyét. |

   A címterület és az alhálózati címtartomány eltérhet a táblától. Az előfizetéstől függően a portál egy rendelkezésre álló címterületet és a megfelelő alhálózati címtartományt javasol. Ha nincs elegendő címterület, használjon másik előfizetést.

   A példa a **Rendszergazda**alhálózati nevet használja. Ez az alhálózat a tartományvezérlőkhöz tartozik.

5. Kattintson **a Létrehozás gombra.**

   ![A virtuális hálózat konfigurálása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Az Azure visszatér a portál irányítópultjára, és értesíti, ha az új hálózat jön létre.

### <a name="create-a-second-subnet"></a>Második alhálózat létrehozása
Az új virtuális hálózat egy alhálózattal rendelkezik, **melynek**neve Admin . A tartományvezérlők ezt az alhálózatot használják. Az SQL Server virtuális gépek egy **második, SQL**nevű alhálózatot használnak. Az alhálózat konfigurálása:

1. Az irányítópulton kattintson a létrehozott **SQL-HA-RG**erőforráscsoportra. Keresse meg a hálózatot az erőforráscsoportban az **Erőforrások csoportban.**

    Ha **az SQL-HA-RG** nem látható, keresse meg az **Erőforráscsoportok** elemre kattintva és az erőforráscsoport neve szerinti szűréssel.
2. Kattintson az **automatikusHAVNET** elemre az erőforrások listájában. 
3. Az **autoHAVNET** virtuális hálózaton a **Beállítások** csoportban válassza **az Alhálózatok lehetőséget.**

    Jegyezze fel a már létrehozott alhálózatot.

   ![A virtuális hálózat konfigurálása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Hozzon létre egy második alhálózatot. Kattintson **a + Alhálózat gombra.**
6. Az **Alhálózat hozzáadása**területen konfigurálja az alhálózatot az **sqlsubnet** **név**mezőbe írja be. Az Azure automatikusan érvényes **címtartományt**ad meg. Ellenőrizze, hogy a címtartományban legalább 10 cím van-e. Éles környezetben további címekre lehet szükség.
7. Kattintson az **OK** gombra.

    ![A virtuális hálózat konfigurálása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

Az alábbi táblázat összefoglalja a hálózati konfigurációs beállításokat:

| **Mező** | Érték |
| --- | --- |
| **Név** |**autoHAVNET** |
| **Címtér** |Ez az érték az előfizetésben rendelkezésre álló címterektől függ. Egy tipikus érték 10.0.0.0/16. |
| **Alhálózat neve** |**rendszergazda** |
| **Alhálózati címtartomány** |Ez az érték az előfizetésben rendelkezésre álló címtartományoktól függ. Egy tipikus érték 10.0.0.0/24. |
| **Alhálózat neve** |**sqlsubnet** |
| **Alhálózati címtartomány** |Ez az érték az előfizetésben rendelkezésre álló címtartományoktól függ. Egy tipikus érték 10.0.1.0/24. |
| **Előfizetés** |Adja meg a használni kívánt előfizetést. |
| **Erőforráscsoport** |**SQL-HA-RG** |
| **Helyen** |Adja meg ugyanazt a helyet, amelyet az erőforráscsoporthoz választott. |

## <a name="create-availability-sets"></a>Rendelkezésre állási csoportok létrehozása

Virtuális gépek létrehozása előtt létre kell hoznia a rendelkezésre állási csoportokat. A rendelkezésre állási beállítások csökkentik a tervezett vagy nem tervezett karbantartási események állásidejét. Az Azure rendelkezésre állási csoportja az erőforrások logikai csoportja, amelyet az Azure fizikai tartalék tartományokra és tartományok frissítésére helyez el. A tartalék tartomány biztosítja, hogy a rendelkezésre állási csoport tagjai külön energia- és hálózati erőforrásokkal rendelkezzenek. A frissítési tartomány biztosítja, hogy a rendelkezésre állási csoport tagjai nem hozta le a karbantartási egyidejűleg. További információk: [Virtuális gépek rendelkezésre állásának kezelése](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Két rendelkezésre állási készletre van szüksége. Az egyik a tartományvezérlőkhöz tartozik. A második az SQL Server virtuális gépek.

Rendelkezésre állási csoport létrehozásához nyissa meg az erőforráscsoportot, és kattintson a **Hozzáadás**gombra. Az eredmények szűrése az **elérhetőségi készlet**beírásával. Az eredmények között kattintson az **Elérhetőségbeállítása gombra,** majd a **Létrehozás gombra.**

Konfiguráljon két rendelkezésre állási csoportot az alábbi táblázatban szereplő paraméterek nek megfelelően:

| **Mező** | Tartományvezérlő rendelkezésre állási készlete | SQL Server rendelkezésre állási készlete |
| --- | --- | --- |
| **Név** |adavailabilityset (adavailabilityset) |sqlavailabilityset |
| **Erőforráscsoport** |SQL-HA-RG |SQL-HA-RG |
| **Tartalék tartományok** |3 |3 |
| **Frissítési tartományok** |5 |3 |

Miután létrehozta a rendelkezésre állási csoportok, térjen vissza az erőforráscsoport az Azure Portalon.

## <a name="create-domain-controllers"></a>Tartományvezérlők létrehozása
Miután létrehozta a hálózatot, az alhálózatokat és a rendelkezésre állási csoportokat, készen áll a tartományvezérlők virtuális gépei nek létrehozására.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Virtuális gépek létrehozása a tartományvezérlők számára
A tartományvezérlők létrehozásához és konfigurálásához térjen vissza az **SQL-HA-RG** erőforráscsoportba.

1. Kattintson a **Hozzáadás** gombra. 
2. Írja be a **Windows Server 2016 Datacenter parancsot.**
3. Kattintson **a Windows Server 2016 Datacenter**elemre. A **Windows Server 2016 Datacenter alkalmazásban**ellenőrizze, hogy a telepítési modell **Erőforrás-kezelő,** majd kattintson a **Létrehozás gombra.** 

Ismételje meg az előző lépéseket két virtuális gép létrehozásához. Nevezze el a két virtuális gépet:

* ad-primary-dc
* ad-másodlagos-dc

  > [!NOTE]
  > Az **ad-secondary-dc** virtuális gép nem kötelező, hogy magas rendelkezésre állást biztosítson az Active Directory tartományi szolgáltatások számára.
  >
  >

Az alábbi táblázat a két gép beállításait mutatja be:

| **Mező** | Érték |
| --- | --- |
| **Név** |Első tartományvezérlő: *ad-primary-dc*.</br>Második tartományvezérlő *ad-secondary-dc*. |
| **Virtuális merevlemez típusa** |SSD |
| **Felhasználónév** |Tartománykezelő |
| **Jelszó** |Contoso!0000 |
| **Előfizetés** |*Az Ön előfizetése* |
| **Erőforráscsoport** |SQL-HA-RG |
| **Helyen** |*Az Ön tartózkodási helye* |
| **Méret** |DS1_V2 |
| **Tárterület** | **Felügyelt lemezek** - használata**Igen** |
| **Virtuális hálózat** |autoHAVNET |
| **Alhálózat** |admin |
| **Nyilvános IP-cím** |*Ugyanaz a név, mint a virtuális gép* |
| **Hálózati biztonsági csoport** |*Ugyanaz a név, mint a virtuális gép* |
| **Rendelkezésre állási készlet** |adavailabilityset (adavailabilityset) </br>**Tartalék tartományok:2** </br>**Tartományok frissítése**:2|
| **Diagnosztika** |Engedélyezve |
| **Diagnosztikai tárfiók** |*Automatikus létrehozás* |

   >[!IMPORTANT]
   >A virtuális gép csak akkor helyezhető el egy rendelkezésre állási csoportban, amikor létrehozza. A virtuális gép létrehozása után nem módosíthatja a rendelkezésre állási készletet. Lásd: [A virtuális gépek elérhetőségének kezelése.](../manage-availability.md)

Az Azure létrehozza a virtuális gépeket.

A virtuális gépek létrehozása után konfigurálja a tartományvezérlőt.

### <a name="configure-the-domain-controller"></a>A tartományvezérlő konfigurálása
A következő lépésekben konfigurálja az **ad-primary-dc** gépet corp.contoso.com tartományvezérlőjeként.

1. A portálon nyissa meg az **SQL-HA-RG** erőforráscsoportot, és válassza ki az **ad-primary-dc** gépet. Az **ad-primary-dc**esetén kattintson a **Csatlakozás** gombra az RDP-fájl távoli asztali eléréséhez való megnyitásához.

    ![Csatlakozás virtuális géphez](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Jelentkezzen be a konfigurált rendszergazdai fiókkal (**\DomainAdmin**) és jelszavával (**Contoso!0000**).
3. Alapértelmezés szerint a **Kiszolgálókezelő** irányítópultjának meg kell jelennie.
4. Kattintson a **Szerepkörök és szolgáltatások hozzáadása** hivatkozásra az irányítópulton.

    ![Kiszolgálókezelő – Szerepkörök hozzáadása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Válassza a **Tovább gombot,** amíg el nem jut a **Kiszolgálói szerepkörök** szakaszhoz.
6. Jelölje ki az **Active Directory tartományi szolgáltatások** és a **DNS-kiszolgáló** szerepköreit. Amikor a rendszer kéri, adjon hozzá további funkciókat, amelyek et ezek a szerepkörök igényelnek.

   > [!NOTE]
   > A Windows figyelmezteti, hogy nincs statikus IP-cím. Ha teszteli a konfigurációt, kattintson a **Folytatás**gombra. Éles környezetben állítsa az IP-címet statikusra az Azure Portalon, vagy [a PowerShell segítségével állítsa be a tartományvezérlő-rendszer statikus IP-címét.](../../../virtual-network/virtual-networks-reserved-private-ip.md)
   >
   >

    ![Szerepkörök hozzáadása párbeszédpanel](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Kattintson a **Tovább gombra,** amíg el nem éri a **Megerősítés** szakaszt. Szükség esetén jelölje **be a Célkiszolgáló automatikus újraindítása jelölőnégyzetet.**
8. Kattintson a **Telepítés gombra.**
9. Miután a szolgáltatások telepítése befejeződik, térjen vissza a **Kiszolgálókezelő** irányítópultjára.
10. Válassza az új **AD DS** beállítást a bal oldali ablaktáblán.
11. Kattintson a sárga figyelmeztető sáv **On** More linkre.

    ![Az AD DS párbeszédpanel a DNS-kiszolgáló virtuális gépén](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. A **Minden** **kiszolgálói feladat részletei** párbeszédpanel Művelet oszlopában kattintson **a Kiszolgáló előléptetése tartományvezérlővé gombra**.
13. Az **Active Directory tartományi szolgáltatások konfigurálása varázslóban**használja a következő értékeket:

    | **Oldal** | Beállítás |
    | --- | --- |
    | **Központi telepítés konfigurálása** |**Új erdő hozzáadása**<br/> **Gyökértartomány név** = corp.contoso.com |
    | **Tartományvezérlő beállításai** |**DSRM-jelszó** = Contoso!0000<br/>**Jelszó megerősítése** = Contoso!0000 |
14. A **Tovább** gombra kattintva végighaladhat a varázsló többi lapján. Az **Előfeltételek ellenőrzése** lapon ellenőrizze, hogy a következő üzenet jelenik-e meg: **Minden előfeltétel-ellenőrzés sikeresen megtörtént.** A vonatkozó figyelmeztető üzeneteket áttekintheti, de a telepítés folytatható.
15. Kattintson a **Telepítés gombra.** Az **ad-primary-dc** virtuális gép automatikusan újraindul.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Az elsődleges tartományvezérlő IP-címének megjegyzése

Használja a DNS elsődleges tartományvezérlőjét. Jegyezze fel az elsődleges tartományvezérlő IP-címét.

Az elsődleges tartományvezérlő IP-címének leése az Azure Portalon keresztül érhető el.

1. Az Azure Portalon nyissa meg az erőforráscsoportot.

2. Kattintson az elsődleges tartományvezérlőre.

3. Az elsődleges tartományvezérlőn kattintson a **Hálózati adapterek**elemre.

![Hálózati illesztők](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Jegyezze fel a kiszolgáló privát IP-címét.

### <a name="configure-the-virtual-network-dns"></a>A virtuális hálózati DNS konfigurálása
Miután létrehozta az első tartományvezérlőt, és engedélyezte a DNS-t az első kiszolgálón, konfigurálja úgy a virtuális hálózatot, hogy ezt a kiszolgálót használja a DNS számára.

1. Az Azure Portalon kattintson a virtuális hálózatra.

2. A **Beállítások csoportban**kattintson a **DNS-kiszolgáló gombra.**

3. Kattintson az **Egyéni**gombra, és írja be az elsődleges tartományvezérlő magánhálózati IP-címét.

4. Kattintson a **Mentés** gombra.

### <a name="configure-the-second-domain-controller"></a>A második tartományvezérlő konfigurálása
Az elsődleges tartományvezérlő újraindítása után konfigurálhatja a második tartományvezérlőt. Ez az opcionális lépés a magas rendelkezésre állás. A második tartományvezérlő konfigurálásához kövesse az alábbi lépéseket:

1. A portálon nyissa meg az **SQL-HA-RG** erőforráscsoportot, és válassza ki az **ad-secondary-dc** gépet. Az **ad-secondary-dc**esetén kattintson a **Csatlakozás** gombra az RDP-fájl távoli asztali eléréséhez való megnyitásához.
2. Jelentkezzen be a virtuális gépre a konfigurált rendszergazdai fiók (**BUILTIN\DomainAdmin**) és jelszó (**Contoso!0000**) használatával.
3. Módosítsa az elsődleges DNS-kiszolgáló címét a tartományvezérlő címére.
4. A **Hálózati és megosztási központban**kattintson a hálózati adapterre.
   ![Hálózati illesztő](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Kattintson a **Tulajdonságok** elemre.
6. Válassza az **Ip-protokoll 4-es verzióját (TCP/IPv4),** és kattintson **a Tulajdonságok gombra.**
7. Válassza **a Következő DNS-kiszolgálócímek használata** lehetőséget, és adja meg az elsődleges tartományvezérlő címét az elsődleges **DNS-kiszolgálón**.
8. A módosítások véglegesítéséhez kattintson az **OK**, majd a **Bezárás** gombra. Most már csatlakozhat a virtuális géphez, hogy **corp.contoso.com.**

   >[!IMPORTANT]
   >Ha a DNS-beállítás módosítása után megszakad a kapcsolat a távoli asztallal, nyissa meg az Azure Portalt, és indítsa újra a virtuális gépet.

9. A távoli asztalról a másodlagos tartományvezérlőre nyissa meg a **Server Manager irányítópultját**.
10. Kattintson a **Szerepkörök és szolgáltatások hozzáadása** hivatkozásra az irányítópulton.

    ![Kiszolgálókezelő – Szerepkörök hozzáadása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Válassza a **Tovább gombot,** amíg el nem jut a **Kiszolgálói szerepkörök** szakaszhoz.
12. Jelölje ki az **Active Directory tartományi szolgáltatások** és a **DNS-kiszolgáló** szerepköreit. Amikor a rendszer kéri, adjon hozzá további funkciókat, amelyek et ezek a szerepkörök igényelnek.
13. Miután a szolgáltatások telepítése befejeződik, térjen vissza a **Kiszolgálókezelő** irányítópultjára.
14. Válassza az új **AD DS** beállítást a bal oldali ablaktáblán.
15. Kattintson a sárga figyelmeztető sáv **On** More linkre.
16. A **Minden** **kiszolgálói feladat részletei** párbeszédpanel Művelet oszlopában kattintson **a Kiszolgáló előléptetése tartományvezérlővé gombra**.
17. A **Központi telepítés konfigurálása csoportban**válassza **a Tartományvezérlő hozzáadása meglévő tartományhoz**lehetőséget.
    ![Központi telepítés konfigurációja](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Kattintson a **Kiválasztás** gombra.
19. Csatlakozás a rendszergazdai fiók (**CORP. CONTOSO.COM\domainadmin**) és jelszó (**Contoso!0000**).
20. A **Válasszon ki egy tartományt az erdőből,** kattintson a tartományra, majd az **OK**gombra.
21. A **Tartományvezérlő beállításai párbeszédpanelen**használja az alapértelmezett értékeket, és állítson be dsrm-jelszót.

    >[!NOTE]
    >A **DNS-beállítások** lap figyelmeztetheti, hogy a DNS-kiszolgáló delegálása nem hozható létre. Ezt a figyelmeztetést figyelmen kívül hagyhatja nem éles környezetben.
22. Kattintson a **Tovább gombra,** amíg a párbeszédpanel el nem éri az **Előfeltételek** ellenőrzést. Ezt követően kattintson a **Telepítés** gombra.

Miután a kiszolgáló befejezte a konfigurációs módosításokat, indítsa újra a kiszolgálót.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>A privát IP-cím hozzáadása a második tartományvezérlőhöz a VPN DNS-kiszolgálóhoz

Az Azure Portalon a virtuális hálózat alatt módosítsa a DNS-kiszolgálót a másodlagos tartományvezérlő IP-címének felvételére. Ez a beállítás lehetővé teszi a DNS-szolgáltatás redundanciájának.

### <a name="configure-the-domain-accounts"></a><a name="DomainAccounts"></a>A tartományi fiókok konfigurálása

A következő lépésekben konfigurálja az Active Directory-fiókokat. Az alábbi táblázat a számlákat mutatja be:

| |Telepítési fiók<br/> |sqlserver-0 <br/>SQL Server és SQL Agent Service-fiók |sqlserver-1<br/>SQL Server és SQL Agent Service-fiók
| --- | --- | --- | ---
|**Utónév** |Telepítés |SQLSvc1 | SQLSvc2
|**Felhasználó SamAccountName** |Telepítés |SQLSvc1 | SQLSvc2

Az alábbi lépésekkel hozzon létre minden egyes fiókot.

1. Jelentkezzen be az **ad-primary-dc** gépbe.
2. A **Kiszolgálókezelőben**válassza az **Eszközök**lehetőséget, majd kattintson az **Active Directory felügyeleti központ elemre.**   
3. Válassza ki **a (helyi) vállalatot** a bal oldali ablaktáblából.
4. A jobb **oldali Feladatok** ablaktáblán válassza az **Új**lehetőséget, majd kattintson a **Felhasználó**gombra.
   ![Active Directory felügyeleti központ](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Állítson be egy összetett jelszót minden fiókhoz.<br/> Nem éles környezetben állítsa be a felhasználói fiókot úgy, hogy soha ne járjon le.

5. A felhasználó létrehozásához kattintson az **OK** gombra.
6. Ismételje meg az előző lépéseket mindhárom fiókesetében.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Adja meg a szükséges engedélyeket a telepítési fiókhoz
1. Az **Active Directory felügyeleti központban**válassza a **(helyi) csoport választóját** a bal oldali ablaktáblában. Ezután a jobb oldali **Feladatok** ablaktáblán kattintson a **Tulajdonságok gombra.**

    ![CORP felhasználói tulajdonságok](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Válassza **a Bővítmények**lehetőséget, majd kattintson a **Biztonság** lap **Speciális** gombjára.
3. A **vállalat speciális biztonsági beállításai** párbeszédpanelen kattintson a **Hozzáadás**gombra.
4. Kattintson **a Tag kiválasztása gombra,** keresse meg a **CORP\Install**elemet, majd kattintson **az OK**gombra.
5. Jelölje be az **Összes tulajdonság olvasása** jelölőnégyzetet.

6. Jelölje be a **Számítógép-objektumok létrehozása** jelölőnégyzetet.

     ![Corp felhasználói engedélyek](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Kattintson **az OK**, majd ismét **az OK** gombra. Zárja be a **vállalat** tulajdonságai ablakot.

Most, hogy befejezte az Active Directory és a felhasználói objektumok konfigurálását, hozzon létre két SQL Server virtuális gépet és egy tanúsító kiszolgáló virtuális gépet. Ezután csatlakozzon mindhárom a tartományhoz.

## <a name="create-sql-server-vms"></a>SQL Server virtuális gépek létrehozása

Hozzon létre három további virtuális gépet. A megoldáshoz két SQL Server-példányt igénylő virtuális gépre van szükség. Egy harmadik virtuális gép fog működni, mint egy tanú. A Windows Server 2016 [felhőtanúsítókat](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)is használhat, azonban a korábbi operációs rendszerekkel való konzisztencia érdekében ez a dokumentum egy tanúsító számára virtuális gépet használ.  

A folytatás előtt fontolja meg a következő tervezési döntéseket.

* **Tárolás – Azure felügyelt lemezek**

   A virtuális gép tárolására használja az Azure felügyelt lemezek. A Microsoft felügyelt lemezeket ajánl az SQL Server virtuális gépekhez. A Managed Disks szolgáltatás a háttérben kezeli a tárterületet. Emellett ha ugyanabban a rendelkezésre állási csoportban több, a Managed Diskset használó virtuális gép található, az Azure elosztja a tárolási erőforrásokat, hogy megfelelő redundanciát biztosítson. További információkért lásd az [Azure Managed Disks áttekintését](../managed-disks-overview.md). A rendelkezésre állási csoport felügyelt lemezeivel kapcsolatos részleteket a [Felügyelt lemezek használata a virtuális gépekhez egy rendelkezésre állási csoportban](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)részben található.

* **Hálózat - Privát IP-címek éles környezetben**

   A virtuális gépek, ez az oktatóanyag nyilvános IP-címeket használ. A nyilvános IP-cím lehetővé teszi a távoli kapcsolatot közvetlenül a virtuális géphez az interneten keresztül - megkönnyíti a konfigurációs lépéseket. Éles környezetben a Microsoft csak a privát IP-címeket ajánlja az SQL Server példány virtuálisgép-erőforrás biztonsági résének csökkentése érdekében.

### <a name="create-and-configure-the-sql-server-vms"></a>Az SQL Server virtuális gépek létrehozása és konfigurálása
Ezután hozzon létre három virtuális gépet – két SQL Server virtuális gépet és egy virtuális gépet egy további fürtcsomóponthoz. Az egyes virtuális gépek létrehozásához lépjen vissza az **SQL-HA-RG** erőforráscsoportba, kattintson a **Hozzáadás**gombra, keresse meg a megfelelő gyűjteményelemet, kattintson a **Virtuális gép**elemre, majd a **Katalógusból**parancsra. Az alábbi táblázatban található információk segítségével létrehozhatja a virtuális gépeket:


| Oldal | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| A megfelelő galériaelem kiválasztása |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise windows Server 2016 rendszeren** |**SQL Server 2016 SP1 Enterprise windows Server 2016 rendszeren** |
| A virtuális gépek **konfigurálásának alapjai** |**Név** = cluster-fsw<br/>**Felhasználónév** = DomainAdmin<br/>**Jelszó** = Contoso!0000<br/>**Előfizetés** = Az előfizetés<br/>**Erőforráscsoport** = SQL-HA-RG<br/>**Hely** = Az azure helye |**Név** = sqlserver-0<br/>**Felhasználónév** = DomainAdmin<br/>**Jelszó** = Contoso!0000<br/>**Előfizetés** = Az előfizetés<br/>**Erőforráscsoport** = SQL-HA-RG<br/>**Hely** = Az azure helye |**Név** = sqlserver-1<br/>**Felhasználónév** = DomainAdmin<br/>**Jelszó** = Contoso!0000<br/>**Előfizetés** = Az előfizetés<br/>**Erőforráscsoport** = SQL-HA-RG<br/>**Hely** = Az azure helye |
| Virtuális gép **konfigurációjának mérete** |**MÉRET** = DS1\_V2 (1 vCPU, 3,5 GB) |**MÉRET** = DS2\_V2 (2 vCPU, 7 GB)</br>A méretnek támogatnia kell az SSD-tárolót (Prémium szintű lemeztámogatás. )) |**MÉRET** = DS2\_V2 (2 vCPU, 7 GB) |
| Virtuális gép **konfigurációjának beállításai** |**Tárolás**: Felügyelt lemezek használata.<br/>**Virtuális hálózat** = autoHAVNET<br/>**Alhálózat** = sqlsubnet(10.1.0/24)<br/>**A nyilvános IP-cím** automatikusan generálódik.<br/>**Hálózati biztonsági csoport** = Nincs<br/>**Monitoring Diagnosztika** = Engedélyezve<br/>**Diagnosztikatár-fiók** = Automatikusan létrehozott tárfiók használata<br/>**Rendelkezésre állási készlet** = sqlAvailabilitySet<br/> |**Tárolás**: Felügyelt lemezek használata.<br/>**Virtuális hálózat** = autoHAVNET<br/>**Alhálózat** = sqlsubnet(10.1.0/24)<br/>**A nyilvános IP-cím** automatikusan generálódik.<br/>**Hálózati biztonsági csoport** = Nincs<br/>**Monitoring Diagnosztika** = Engedélyezve<br/>**Diagnosztikatár-fiók** = Automatikusan létrehozott tárfiók használata<br/>**Rendelkezésre állási készlet** = sqlAvailabilitySet<br/> |**Tárolás**: Felügyelt lemezek használata.<br/>**Virtuális hálózat** = autoHAVNET<br/>**Alhálózat** = sqlsubnet(10.1.0/24)<br/>**A nyilvános IP-cím** automatikusan generálódik.<br/>**Hálózati biztonsági csoport** = Nincs<br/>**Monitoring Diagnosztika** = Engedélyezve<br/>**Diagnosztikatár-fiók** = Automatikusan létrehozott tárfiók használata<br/>**Rendelkezésre állási készlet** = sqlAvailabilitySet<br/> |
| Virtuális gép **konfigurációja SQL Server-beállítások** |Nem alkalmazható |**SQL-kapcsolat** = Privát (virtuális hálózaton belül)<br/>**Port** = 1433<br/>**SQL-hitelesítés** = Letiltás<br/>**Tárolási konfiguráció** = Általános<br/>**Automatizált javítás** = vasárnap 2:00-kor<br/>**Automatikus biztonsági mentés** = Letiltva</br>**Azure Key Vault integráció** = Letiltva |**SQL-kapcsolat** = Privát (virtuális hálózaton belül)<br/>**Port** = 1433<br/>**SQL-hitelesítés** = Letiltás<br/>**Tárolási konfiguráció** = Általános<br/>**Automatizált javítás** = vasárnap 2:00-kor<br/>**Automatikus biztonsági mentés** = Letiltva</br>**Azure Key Vault integráció** = Letiltva |

<br/>

> [!NOTE]
> Az itt javasolt gépméretek az Azure virtuális gépek rendelkezésre állási csoportjainak tesztelésére szolgálnak. Az éles számítási feladatok legjobb teljesítményét az SQL Server-gépek méretével és konfigurációjának ajánlott teljesítményével kapcsolatos gyakorlati tanácsok az [Azure virtuális gépeken című témakörben](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ismerteti.
>
>

Miután a három virtuális gép teljesen ki van építve, csatlakoznia kell a **corp.contoso.com** tartományhoz, és meg kell adnia a CORP\Install felügyeleti jogokat a gépekhez.

### <a name="join-the-servers-to-the-domain"></a><a name="joinDomain"></a>Csatlakozzon a kiszolgálókhoz a tartományhoz

Most már csatlakozhat a virtuális gépekhez, hogy **corp.contoso.com.** Az SQL Server virtuális gépek és a fájlmegosztási tanúsító kiszolgáló esetében is végezze el az alábbi lépéseket:

1. A **BUILTIN\DomainAdmin**mappával távolról csatlakozhat a virtuális géphez.
2. A **Kiszolgálókezelőben**kattintson a **Helyi kiszolgáló gombra.**
3. Kattintson a **MUNKACSOPORT** hivatkozásra.
4. A **Számítógépnév** csoportban kattintson a **Módosítás gombra.**
5. Jelölje be a **Domain** jelölőnégyzetet, és írja be **a corp.contoso.com** a szövegmezőbe. Kattintson az **OK** gombra.
6. A **Windows biztonsági** előugró párbeszédpanelen adja meg az alapértelmezett tartományi rendszergazdai fiók (**CORP\DomainAdmin**) hitelesítő adatait és a jelszót (**Contoso!0000**).
7. Amikor megjelenik az "Üdvözli a corp.contoso.com tartomány" üzenet, kattintson az **OK**gombra.
8. Kattintson a **Bezárás**gombra, majd az előugró párbeszédpanel **újraindítása** gombra.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>A Corp\Install felhasználó hozzáadása rendszergazdaként minden fürtvirtuális géphez

Miután minden virtuális gép újraindul a tartomány tagjaként, adja hozzá a **CORP\Install** mappát a helyi rendszergazdák csoportjának tagjaként.

1. Várjon, amíg a virtuális gép újraindul, majd indítsa el újra az RDP-fájlt az elsődleges tartományvezérlőről, hogy a **CORP\DomainAdmin** fiókhasználatával jelentkezzen be az **sqlserver-0-ba.**
   >[!TIP]
   >Győződjön meg arról, hogy a tartományi rendszergazdai fiókkal jelentkezik be. Az előző lépésekben a BEÉPÍTETT RENDSZERGAZDAi fiókot használta. Most, hogy a kiszolgáló a tartományban van, használja a tartományi fiókot. Az RDP-munkamenetben adja meg a *DOMAIN*\\*felhasználónevet.*

2. A **Kiszolgálókezelőben**válassza az **Eszközök**lehetőséget, majd kattintson **a Számítógép-kezelés gombra.**
3. A **Számítógép-kezelés** ablakban bontsa ki a **Helyi felhasználók és csoportok csomópontot,** majd válassza a **Csoportok**lehetőséget.
4. Kattintson duplán a **Rendszergazdák** csoportra.
5. A **Rendszergazdák tulajdonságai** párbeszédpanelen kattintson a **Hozzáadás** gombra.
6. Írja be a **felhasználó corp\install**, majd kattintson **az OK gombra**.
7. A **Rendszergazda tulajdonságai** párbeszédpanel bezárásához kattintson az **OK** gombra.
8. Ismételje meg az előző lépéseket az **sqlserver-1** és **a cluster-fsw rendszeren.**

### <a name="set-the-sql-server-service-accounts"></a><a name="setServiceAccount"></a>Az SQL Server szolgáltatásfiókok beállítása

Minden SQL Server virtuális gépen állítsa be az SQL Server szolgáltatásfiókot. Használja a tartományi fiókok konfigurálásakor létrehozott fiókokat.

1. Nyissa meg az **SQL Server Configuration Manager** eszközt.
2. Kattintson a jobb gombbal az SQL Server szolgáltatásra, majd kattintson **a Tulajdonságok parancsra.**
3. Állítsa be a fiókot és a jelszót.
4. Ismételje meg ezeket a lépéseket a másik SQL Server virtuális gépen.  

Az SQL Server rendelkezésre állási csoportjaihoz minden SQL Server virtuális gépnek tartományi fiókként kell futnia.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Bejelentkezés létrehozása minden SQL Server virtuális gépen a telepítési fiókhoz

A telepítési fiók (CORP\install) segítségével konfigurálja a rendelkezésre állási csoportot. Ennek a fióknak minden SQL Server virtuális gépen a **syadmin** fix kiszolgálói szerepkör tagjának kell lennie. A következő lépések létrehoznak egy bejelentkezést a telepítési fiókhoz:

1. Csatlakozzon a kiszolgálóhoz a Távoli asztali protokollon (RDP) keresztül a * \<MachineName\>\DomainAdmin* fiók használatával.

1. Nyissa meg az SQL Server Management Studio alkalmazást, és csatlakozzon az SQL Server helyi példányához.

1. Az **Objektumkezelőben**kattintson a **Biztonság**gombra.

1. Kattintson a jobb gombbal **a Bejelentkezések**elemre. Kattintson **az Új bejelentkezés gombra.**

1. A **Bejelentkezés - Új mezőben**kattintson a Keresés **gombra.**

1. Kattintson **a Helyek gombra.**

1. Adja meg a tartományi rendszergazda hálózati hitelesítő adatait.

1. Használja a telepítési fiókot.

1. Állítsa be a bejelentkezést úgy, hogy a **sysadmin** rögzített kiszolgálói szerepkör tagja legyen.

1. Kattintson az **OK** gombra.

Ismételje meg az előző lépéseket a másik SQL Server virtuális gép.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Feladatátvevő fürtözési szolgáltatások hozzáadása mindkét SQL Server virtuális géphez

Feladatátvevő fürtözési szolgáltatások hozzáadásához mindkét SQL Server-szolgáltatáson végezze el az alábbi lépéseket:

1. Csatlakozzon az SQL Server virtuális géphez a Távoli asztali protokollon (RDP) keresztül a *CORP\install* fiók használatával. Nyissa meg **a Kiszolgálókezelő irányítópultját**.
2. Kattintson a **Szerepkörök és szolgáltatások hozzáadása** hivatkozásra az irányítópulton.

    ![Kiszolgálókezelő – Szerepkörök hozzáadása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Válassza a **Tovább gombot,** amíg el nem jut a **Kiszolgálószolgáltatások** szakaszhoz.
4. A **Szolgáltatások területen**válassza a **Feladatátvevő fürtözés**lehetőséget.
5. Adja hozzá a további szükséges funkciókat.
6. A szolgáltatások hozzáadásához kattintson a **Telepítés** gombra.

Ismételje meg a lépéseket a másik SQL Server virtuális gép.

  >[!NOTE]
  > Ez a lépés, valamint a ténylegesen csatlakozott az SQL Server virtuális gépek a feladatátvevő fürt, most már automatizálható [az Azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) és [az Azure Quickstart sablonok.](virtual-machines-windows-sql-availability-group-quickstart-template.md)


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall">A tűzfal konfigurálása minden SQL Server virtuális gépen

A megoldás megköveteli, hogy a következő TCP-portok legyenek megnyitva a tűzfalon:

- **SQL Server virtuális gép**:<br/>
   1433-as port az SQL Server egyik alapértelmezett példányához.
- **Azure terheléselosztó mintavétel:**<br/>
   Bármely elérhető port. Példák gyakran használják 59999.
- **Adatbázis-tükrözési végpont:** <br/>
   Bármely elérhető port. Példák gyakran használják 5022.

A tűzfalportoknak mindkét SQL Server-virtuális számítógépen meg kell nyitva lenniük.

A portok megnyitásának módja a használt tűzfalmegoldástól függ. A következő szakasz bemutatja, hogyan lehet megnyitni a portokat a Windows tűzfalban. Nyissa meg a szükséges portokat az SQL Server virtuális gépeken.

### <a name="open-a-tcp-port-in-the-firewall"></a>TCP-port megnyitása a tűzfalon

1. Az SQL Server első **kezdőképernyőjén** indítsa el a **Fokozott biztonságú Windows tűzfal**szolgáltatást.
2. A bal oldali ablaktáblában válassza a **Bejövő szabályok lehetőséget.** A jobb oldali ablaktáblán kattintson az **Új szabály gombra.**
3. A **Szabálytípus mezőben**válassza a **Port**lehetőséget.
4. A porthoz adja meg a **TCP-t,** és írja be a megfelelő portszámokat. Lásd a következő példát:

   ![SQL tűzfal](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Kattintson a **Tovább** gombra.
6. A **Művelet** lapon tartsa a **Kapcsolat engedélyezése** lehetőséget, majd kattintson a **Tovább**gombra.
7. A **Profil** lapon fogadja el az alapértelmezett beállításokat, majd kattintson a **Tovább**gombra.
8. A **Név** lapon adjon meg egy szabálynevet (például **az Azure LB Probe)-t**a **Név** mezőben, majd kattintson a **Befejezés gombra.**

Ismételje meg ezeket a lépéseket a második SQL Server virtuális gépen.

## <a name="configure-system-account-permissions"></a>Rendszerfiók-engedélyek konfigurálása

Ha fiókot szeretne létrehozni a rendszerfiókhoz, és megfelelő engedélyeket szeretne adni, hajtsa végre az alábbi lépéseket az egyes SQL Server-példányokon:

1. Hozzon létre `[NT AUTHORITY\SYSTEM]` egy fiókot minden SQL Server-példányhoz. A következő parancsfájl hozza létre ezt a fiókot:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Adja meg a `[NT AUTHORITY\SYSTEM]` következő engedélyeket minden SQL Server-példányon:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   A következő parancsfájl adja meg ezeket az engedélyeket:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>További lépések

* [SQL Server Always On availability csoport létrehozása azure-beli virtuális gépeken](virtual-machines-windows-portal-sql-availability-group-tutorial.md)
