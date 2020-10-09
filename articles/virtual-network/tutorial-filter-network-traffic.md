---
title: Hálózati forgalom szűrése – oktatóanyag – Azure Portal
titlesuffix: Azure Virtual Network
description: Ebből az oktatóanyagból megtudhatja, hogyan szűrheti az alhálózatra irányuló hálózati forgalmat egy hálózati biztonsági csoporttal a Azure Portal használatával.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.service: virtual-network
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/13/2018
ms.author: kumud
ms.openlocfilehash: b5a136ae05b3cd410ca252b6d5a1df443aff6f7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "75350141"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Oktatóanyag: hálózati forgalom szűrése hálózati biztonsági csoporttal a Azure Portal használatával

A virtuális hálózatok alhálózatainak bejövő vagy kimenő hálózati forgalmát hálózati biztonsági csoport segítségével szűrheti. A hálózati biztonsági csoportok biztonsági szabályokat tartalmaznak, amelyek IP-cím, port és protokoll szerint szűrik a hálózati forgalmat. A biztonsági szabályok az egyes alhálózatokban üzembe helyezett erőforrásokra vonatkoznak. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hálózati biztonsági csoport és biztonsági szabályok létrehozása
> * Virtuális hálózat létrehozása és hálózati biztonsági csoport hozzárendelése egy alhálózathoz
> * Virtuális gépek üzembe helyezése egy alhálózaton
> * Forgalomszűrők tesztelése

Igény szerint az oktatóanyag az [Azure CLI](tutorial-filter-network-traffic-cli.md) vagy a [PowerShell](tutorial-filter-network-traffic-powershell.md) használatával is elvégezhető.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet. 
2. Válassza a **Hálózatkezelés**, majd a **Virtuális hálózat** elemet.
3. Adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Név                    | myVirtualNetwork                                   |
    | Címtér           | 10.0.0.0/16                                        |
    | Előfizetés            | Válassza ki előfizetését.                          |
    | Erőforráscsoport          | Válassza az **Új létrehozása** elemet, és adja meg a *myResourceGroup* nevet. |
    | Hely                | Válassza az **USA keleti régiója** lehetőséget.                                |
    | Alhálózat – név            | mySubnet                                           |
    | Alhálózat – címtartomány  | 10.0.0.0/24                                        |

## <a name="create-application-security-groups"></a>Alkalmazásbiztonsági csoportok létrehozása

Az alkalmazásbiztonsági csoportok lehetővé teszik, hogy egy csoportba rendezze a hasonló funkciójú kiszolgálókat, például a webkiszolgálókat.

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet. 
2. A **Keresés a Marketplace-en** mezőbe írja be a következőt: *Alkalmazásbiztonsági csoport*. Amikor az **Alkalmazásbiztonsági csoport** kifejezés megjelenik a keresési eredmények között, válassza ki ismét az **Alkalmazásbiztonsági csoportot** a **Minden** területen, majd válassza a **Létrehozás** elemet.
3. Adja meg vagy válassza ki a következő adatokat, majd válassza a **Létrehozás** lehetőséget:

    | Beállítás        | Érték                                                         |
    | ---            | ---                                                           |
    | Név           | myAsgWebServers                                               |
    | Előfizetés   | Válassza ki előfizetését.                                     |
    | Erőforráscsoport | Válassza a **Meglévő használata**, majd a **myResourceGroup** lehetőséget. |
    | Hely       | USA keleti régiója                                                       |

4. Végezze el ismét a 3. lépést az alábbi értékeket megadva:

    | Beállítás        | Érték                                                         |
    | ---            | ---                                                           |
    | Név           | myAsgMgmtServers                                              |
    | Előfizetés   | Válassza ki előfizetését.                                     |
    | Erőforráscsoport | Válassza a **Meglévő használata**, majd a **myResourceGroup** lehetőséget. |
    | Hely       | USA keleti régiója                                                       |

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet. 
2. Kattintson a **Hálózatkezelés**, majd a **Hálózati biztonsági csoport** lehetőségre.
3. Adja meg vagy válassza ki a következő adatokat, majd válassza a **Létrehozás** lehetőséget:

    |Beállítás|Érték|
    |---|---|
    |Név|myNsg|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport | Válassza a **meglévő használata** , majd a *myResourceGroup*lehetőséget.|
    |Hely|USA keleti régiója|

## <a name="associate-network-security-group-to-subnet"></a>Hálózati biztonsági csoport társítása alhálózathoz

1. A portál felső részében található *Erőforrások, szolgáltatások és dokumentumok keresése* mezőben kezdje el beírni a *myNsg* nevet. Amikor a **myNsg** elem megjelenik a keresési eredmények között, válassza ki.
2. A **BEÁLLÍTÁSOK** területen válassza az **Alhálózatok**, majd a **+ Társítás** lehetőséget, ahogyan az a következő képen látható:

    ![NSG társítása alhálózathoz](./media/tutorial-filter-network-traffic/associate-nsg-subnet.png)

3. Az **Alhálózat hozzárendelése** területen válassza a **Virtuális hálózat** elemet, majd a **myVirtualNetwork** lehetőséget. Válassza az **Alhálózat**, majd a **mySubnet** lehetőséget, végül kattintson az **OK** gombra.

## <a name="create-security-rules"></a>Biztonsági szabályok létrehozása

1. A **BEÁLLÍTÁSOK** területen válassza a **Bejövő biztonsági szabályok**, majd a **+ Hozzáadás** elemet a következő képen látható módon:

    ![Bejövő biztonsági szabály hozzáadása](./media/tutorial-filter-network-traffic/add-inbound-rule.png)

2. Hozzon létre egy biztonsági szabályt, amely engedélyezi a 80-as és a 443-as portot a **myAsgWebServers** alkalmazásbiztonsági csoport számára. A **Bejövő biztonsági szabály felvétele** területen írja be vagy válassza ki a következő értékeket, fogadja el a fennmaradó alapértelmezett beállításokat, majd válassza a **Hozzáadás** elemet:

    | Beállítás                 | Érték                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Cél             | Válassza az **Alkalmazásbiztonsági csoport** lehetőséget, majd válassza a **myAsgWebServers** elemet az **Alkalmazásbiztonsági csoport** számára.  |
    | Célporttartományok | Írja be a 80,443 értéket.                                                                                                    |
    | Protokoll                | Válassza a TCP lehetőséget.                                                                                                      |
    | Név                    | Allow-Web-All                                                                                                   |

3. Hajtsa végre ismét a 2. lépést az alábbi értékeket használva:

    | Beállítás                 | Érték                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Cél             | Válassza az **Alkalmazásbiztonsági csoport** lehetőséget, majd válassza a **myAsgMgmtServers** elemet az **Alkalmazásbiztonsági csoport** számára. |
    | Célporttartományok | Írja be a 3389 értéket.                                                                                                      |
    | Protokoll                | Válassza a TCP lehetőséget.                                                                                                      |
    | Prioritás                | Írja be a 110 értéket.                                                                                                       |
    | Név                    | Allow-RDP-All                                                                                                   |

    Ebben az oktatóanyagban az RDP (3389-es port) közvetlenül az internetre csatlakozik a *myAsgMgmtServers* alkalmazásbiztonsági csoporthoz rendelt virtuális gépen. Éles környezet esetében a 3389-es port közvetlenül az internetre való csatlakoztatása helyett javasolt VPN vagy privát hálózati kapcsolat használatával csatlakozni a kezelni kívánt Azure-erőforrásokhoz.

Miután végrehajtotta az 1–3. lépést, tekintse át a létrehozott szabályokat. A listának a következő képen látható listához kell hasonlítania:

![Biztonsági szabályok](./media/tutorial-filter-network-traffic/security-rules.png)

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet. 
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet.
3. Adja meg vagy válassza ki a következő információkat, és fogadja el a többi beállítás alapértelmezett beállításait:

    |Beállítás|Érték|
    |---|---|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport| Válassza a **Meglévő használata** lehetőséget, majd a **myResourceGroup** elemet.|
    |Név|myVmWeb|
    |Hely| Válassza az **USA keleti régiója** lehetőséget.|
    |Felhasználónév| Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|

   

4. Válassza ki a virtuális gép méretét, majd kattintson a **Kiválasztás** gombra.
5. A **hálózat**területen válassza ki a következő értékeket, és fogadja el a fennmaradó alapértékeket:

    |Beállítás|Érték|
    |---|---|
    |Virtuális hálózat |Válassza a **myVirtualNetwork**lehetőséget.|
    |NIC hálózati biztonsági csoport |Válassza a **Nincs** lehetőséget.|
  

6. Válassza a **felülvizsgálat + létrehozás** lehetőséget a bal oldali sarokban, majd a **Létrehozás** elemre kattintva indítsa el a virtuális gép üzembe helyezését.

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

Végezze el ismét az 1–6. lépést, de a 3. lépésben a *myVmMgmt* nevet adja a virtuális gépnek. A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a következő lépéssel a virtuális gép üzembe helyezéséig.

## <a name="associate-network-interfaces-to-an-asg"></a>Hálózati adapterek társítása ASG-hez

Amikor a portál létrehozta a virtuális gépeket, létrehozott egy hálózati adaptert mindegyik virtuális géphez, és a hálózati adaptert a virtuális géphez csatolta. Adja hozzá az egyes virtuális gépek hálózati adapterét a korábban létrehozott alkalmazásbiztonsági csoporthoz valamelyikéhez:

1. A portál felső részében található *Erőforrások, szolgáltatások és dokumentumok keresése* mezőben kezdje el beírni a *myVmWeb* nevet. Amikor a **myVmWeb** virtuális gép megjelenik a keresési eredmények között, válassza ki.
2. A **BEÁLLÍTÁSOK** területen válassza a **Hálózatkezelés** elemet.  Válassza az **Alkalmazásbiztonsági csoportok konfigurálása** lehetőséget, válassza a **myAsgWebServers** elemet az **Alkalmazásbiztonsági csoportok** számára, majd kattintson a **Mentés** lehetőségre, ahogyan az a következő képen látható:

    ![Társítás ASG-hez](./media/tutorial-filter-network-traffic/associate-to-asg.png)

3. Végezze el ismét az 1. és a 2. lépést, a **myVmMgmt** virtuális gépre keresve, és a **myAsgMgmtServers** ASG-t kiválasztva.

## <a name="test-traffic-filters"></a>Forgalomszűrők tesztelése

1. Csatlakozzon a *myVmMgmt* virtuális géphez. Írja be a *myVmMgmt* nevet a portál tetején lévő keresőmezőbe. Amikor a **myVmMgmt** elem megjelenik a keresési eredmények között, válassza ki. Kattintson a **Csatlakozás** gombra.
2. Válassza az **RDP-fájl letöltése**lehetőséget.
3. Nyissa meg a letöltött RDP-fájlt, és válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania.
4. Válassza az **OK** lehetőséget.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.

    A kapcsolat sikeresen létrejön, mert a 3389-es porton engedélyezve van az internetről a *myAsgMgmtServers* alkalmazásbiztonsági csoportba (amelyben a *myVmMgmt* virtuális géphez csatlakoztatott hálózati adapter található) bejövő forgalom.

6. Csatlakozzon a *myVmWeb* virtuális géphez a *myVmMgmt* virtuális gépről a következő parancs beírásával egy PowerShell-munkamenetben:

    ``` 
    mstsc /v:myVmWeb
    ```

    A myVmMgmt virtuális gépről azért csatlakozhat a myVmWeb virtuális géphez, mert az ugyanazon a virtuális hálózaton található virtuális gépek alapértelmezés szerint bármely porton keresztül kommunikálhatnak egymással. Nem hozhat létre azonban távoli asztali kapcsolatot a *myVmWeb* virtuális géppel az internetről, mert a *myAsgWebServers* biztonsági szabálya nem engedélyezi az internetről bejövő forgalmat a 3389-es porton keresztül, és a rendszer alapértelmezés szerint elutasítja az erőforrásokra irányuló bejövő forgalmat.

7. A Microsoft IIS telepítéséhez a *myVmWeb* virtuális gépen írja be a következő parancsot egy PowerShell-munkamenetben a *myVmWeb* virtuális gépen:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. Az IIS telepítésének befejezése után csatlakozzon le a *myVmWeb* virtuális gépről. Így csak a *myVmMgmt* virtuális géppel létesített távoli asztali kapcsolat marad.
9. Csatlakozzon le a *myVmMgmt* virtuális gépről.
10. Az Azure Portal felső részében található *Erőforrások, szolgáltatások és dokumentumok keresése* mezőben kezdje el beírni a *myVmWeb* nevet a számítógépen. Amikor a **myVmWeb** elem megjelenik a keresési eredmények között, válassza ki. Jegyezze fel a virtuális gép **Nyilvános IP-cím** értékét. A következő képen látható cím a 137.135.84.74, az Ön címe azonban más:

    ![Nyilvános IP-cím](./media/tutorial-filter-network-traffic/public-ip-address.png)
  
11. Annak ellenőrzéséhez, hogy eléri-e a *myVmWeb* webkiszolgálót az internetről, nyisson meg egy webböngészőt a számítógépen, és lépjen a következő címre: `http://<public-ip-address-from-previous-step>`. Megjelenik az IIS üdvözlőképernyője, mert a 80-as porton engedélyezve van az internetről a *myAsgWebServers* alkalmazásbiztonsági csoportba (amelyben a *myVmWeb* virtuális géphez csatlakoztatott hálózati adapter található) bejövő forgalom.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. Írja be a *myResourceGroup* nevet a portál tetején lévő **keresőmezőbe**. Amikor a **myResourceGroup** megjelenik a keresési eredmények között, válassza ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be a *myResourceGroup* nevet az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy hálózati biztonsági csoportot, és hozzárendelte azt egy virtuális hálózat alhálózatához. A hálózati biztonsági csoportokkal kapcsolatos további információ: [Hálózati biztonsági csoportok áttekintése](security-overview.md) és [Hálózati biztonsági csoportok kezelése](manage-network-security-group.md).

Az Azure alapértelmezés szerint irányítja a forgalmat az alhálózatok között. Ehelyett lehetősége van arra, hogy egy virtuális gépen keresztül irányítsa a forgalmat az alhálózatok között, amely így például tűzfalként is szolgálhat. Ha meg szeretné ismerni, hogyan hozhat létre útválasztási táblázatot, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Útválasztási táblázat létrehozása](./tutorial-create-route-table-portal.md)
