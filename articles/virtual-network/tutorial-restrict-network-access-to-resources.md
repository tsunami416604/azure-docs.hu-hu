---
title: A PaaS-erőforrásokhoz való hálózati hozzáférés korlátozása – oktatóanyag – Azure Portal | Microsoft Docs
description: Ebben az oktatóanyagban megtanulhatja, hogyan korlátozható az Azure-erőforrásokhoz – például az Azure Storage-hoz és az Azure SQL Database-hez – való hálózati hozzáférés virtuális hálózati szolgáltatásvégpontokkal az Azure Portal használatával.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/14/2018
ms.author: jdial
ms.openlocfilehash: 2efbd6e0fc3f90909553bc839a8b61ff3ed681ad
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267390"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Oktatóanyag: PaaS-erőforrásokhoz való hálózati hozzáférés korlátozása virtuális hálózati szolgáltatásvégpontokkal az Azure Portal használatával

Virtuális hálózati szolgáltatásvégpontokkal egy adott virtuális hálózati alhálózatra korlátozható az egyes Azure-szolgáltatási erőforrásokhoz való hálózati hozzáférés. Emellett teljesen le is tiltható az internetes hozzáférés az erőforrásokhoz. A szolgáltatásvégpontok közvetlen csatlakozást biztosítanak a virtuális hálózat és a támogatott Azure-szolgáltatások között, így lehetővé teszik a virtuális hálózat magáncímterének használatát az Azure-szolgáltatások eléréséhez. A szolgáltatásvégpontokon keresztül az Azure-erőforrások felé irányuló forgalom mindig a Microsoft Azure gerinchálózatán marad. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális hálózat létrehozása egyetlen alhálózattal
> * Alhálózat hozzáadása és szolgáltatásvégpont engedélyezése
> * Azure-erőforrás létrehozása és hálózati hozzáférés engedélyezése az erőforráshoz egyetlen alhálózatról
> * Virtuális gép (VM) üzembe helyezése az egyes alhálózatokon
> * Erőforráshoz való alhálózati hozzáférés ellenőrzése
> * Erőforráshoz való alhálózati és internetes hozzáférés letiltásának ellenőrzése

Igény szerint az oktatóanyagot az [Azure CLI](tutorial-restrict-network-access-to-resources-cli.md) vagy az [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md) használatával is elvégezheti.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a http://portal.azure.com címen.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Válassza az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** lehetőséget.
2. Válassza a **Hálózatkezelés**, majd a **Virtuális hálózat** elemet.
3. Adja meg vagy válassza ki a következő adatokat, majd válassza a **Létrehozás** lehetőséget:

    |Beállítás|Érték|
    |----|----|
    |Name (Név)| myVirtualNetwork |
    |Címtér| 10.0.0.0/16|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport | Válassza az **Új létrehozása** elemet, és adja meg a *myResourceGroup* nevet.|
    |Hely| Válassza az **USA keleti régiója** lehetőséget. |
    |Alhálózat neve| Nyilvános|
    |Alhálózat címtartománya| 10.0.0.0/24|
    |Szolgáltatásvégpontok| Letiltva|

    ![Írja be a virtuális hálózat alapvető adatait](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)


## <a name="enable-a-service-endpoint"></a>Szolgáltatásvégpont engedélyezése

A szolgáltatásvégpontok szolgáltatásonként és alhálózatonként engedélyezhetők. Hozzon létre egy alhálózatot, és engedélyezzen egy szolgáltatásvégpontot az alhálózat számára.

1. Írja be a *myVirtualNetwork* kifejezést portál tetején található **Erőforrások, szolgáltatások és dokumentumok keresése** mezőbe. Amikor a **myVirtualNetwork** megjelenik a keresési eredmények között, válassza ki.
2. Adjon hozzá egy alhálózatot a virtuális hálózathoz. A **BEÁLLÍTÁSOK** területen válassza az **Alhálózatok**, majd az **+ Alhálózat** lehetőséget, ahogyan az a következő képen látható:

    ![Alhálózat hozzáadása](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. Az **Alhálózat hozzáadása** alatt válassza ki vagy adja meg a következő adatokat, és kattintson az **OK** gombra:

    |Beállítás|Érték|
    |----|----|
    |Name (Név)| Privát |
    |Címtartomány| 10.0.1.0/24|
    |Szolgáltatásvégpontok| A **Szolgáltatások** területen válassza a **Microsoft.Storage** elemet.|

> [!CAUTION]
> Mielőtt engedélyezne egy szolgáltatásvégpontot egy erőforrásokkal rendelkező meglévő alhálózaton, tekintse meg az [alhálózat beállításainak módosítását](virtual-network-manage-subnet.md#change-subnet-settings) ismertető szakaszt.

## <a name="restrict-network-access-for-a-subnet"></a>Alhálózat hálózati hozzáférésének korlátozása

Alapértelmezés szerint egy adott alhálózaton belül minden virtuális gép minden erőforrással kommunikálhat. Az alhálózaton belüli összes erőforrás kimenő és bejövő forgalmát egy hálózati biztonsági csoport létrehozásával, és annak az alhálózathoz való társításával korlátozhatja.

1. Válassza az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** lehetőséget.
2. Kattintson a **Hálózatkezelés**, majd a **Hálózati biztonsági csoport** lehetőségre.
3. A **Hálózati biztonsági csoport létrehozása** lehetőség alatt adja meg vagy válassza ki a következő adatokat, majd válassza a **Létrehozás** elemet:

    |Beállítás|Érték|
    |----|----|
    |Name (Név)| myNsgPrivate |
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport | Válassza a **Meglévő használata** lehetőséget, majd a *myResourceGroup* elemet.|
    |Hely| Válassza az **USA keleti régiója** lehetőséget. |

4. Miután a hálózati biztonsági csoport létrejött, írja be a *myNsgPrivate* kifejezést a portál tetején található **Erőforrások, szolgáltatások és dokumentumok keresése** mezőbe. Amikor a **myNsgPrivate** megjelenik a keresési eredmények között, válassza ki.
5. A **BEÁLLÍTÁSOK** területen válassza a **Kimenő biztonsági szabályok** elemet.
6. Válassza a **+ Hozzáadás** lehetőséget.
7. Hozzon létre egy szabályt, amely engedélyezi a kimenő kommunikációt az Azure Storage szolgáltatás felé. Adja meg vagy válassza ki a következő adatokat, majd kattintson az **OK** gombra:

    |Beállítás|Érték|
    |----|----|
    |Forrás| Válassza a **VirtualNetwork** lehetőséget. |
    |Forrásporttartományok| * |
    |Cél | Válassza a **Service Tag** lehetőséget.|
    |Cél szolgáltatáscímkéje | Válassza a **Storage** lehetőséget.|
    |Célporttartományok| * |
    |Protokoll|Bármelyik|
    |Műveletek|Engedélyezés|
    |Prioritás|100|
    |Name (Név)|Allow-Storage-All|
    
8. Hozzon létre egy szabályt, amely tiltja a kommunikációt az internet irányában. Ez a szabály felülírja az összes hálózati biztonsági csoportra érvényes alapértelmezett szabályt, amely engedélyezi a kimenő internetes kommunikációt. Végezze el ismét a 6. és a 7. lépést a következő értékek használatával:

    |Beállítás|Érték|
    |----|----|
    |Forrás| Válassza a **VirtualNetwork** lehetőséget. |
    |Forrásporttartományok| * |
    |Cél | Válassza a **Service Tag** lehetőséget.|
    |Cél szolgáltatáscímkéje| Válassza az **Internet** lehetőséget.|
    |Célporttartományok| * |
    |Protokoll|Bármelyik|
    |Műveletek|Megtagadás|
    |Prioritás|110|
    |Name (Név)|Deny-Internet-All|

9. A **BEÁLLÍTÁSOK** területen válassza a **Bejövő biztonsági szabályok** elemet.
10. Válassza a **+ Hozzáadás** lehetőséget.
11. Hozzon létre egy szabályt, amely engedélyezi az alhálózatra bejövő RDP-forgalmat bárhonnan. Ez a szabály felülbírálja azon alapértelmezett biztonsági szabályokat, amelyek elutasítanak minden bejövő forgalmat az internetről. A távoli asztali kapcsolatok engedélyezettek az alhálózathoz, hogy a kapcsolat egy későbbi lépésben tesztelhető legyen. Végezze el ismét a 6. és a 7. lépést a következő értékek használatával:

    |Beállítás|Érték|
    |----|----|
    |Forrás| Bármelyik |
    |Forrásporttartományok| * |
    |Cél | Válassza a **Service Tag** lehetőséget.|
    |Cél szolgáltatáscímkéje| Válassza a **VirtualNetwork** lehetőséget.|
    |Célporttartományok| 3389 |
    |Protokoll|Bármelyik|
    |Műveletek|Engedélyezés|
    |Prioritás|120|
    |Name (Név)|Allow-RDP-All|

12. A **BEÁLLÍTÁSOK** területen válassza az **Alhálózatok** elemet.
13. Válassza a **+ Társítás** lehetőséget.
14. Az **Alhálózat hozzárendelése** területen válassza a **Virtuális hálózat** elemet, majd a **myVirtualNetwork** lehetőséget a **Virtuális hálózat kiválasztása** lehetőség alatt.
15. Az **Alhálózat választása** alatt válassza a **Magánjellegű** elemet, és kattintson az **OK** gombra.

## <a name="restrict-network-access-to-a-resource"></a>Erőforráshoz való hálózati hozzáférés korlátozása

A szolgáltatásvégpontok használatára képes Azure-szolgáltatásokkal létrehozott erőforrásokhoz való hálózati hozzáférés korlátozásának lépései szolgáltatásonként eltérőek. Az egyes szolgáltatásokhoz szükséges lépéseket az adott szolgáltatások dokumentációja tartalmazza. Az oktatóanyag a továbbiakban egy Azure Storage-tárfiók hálózati hozzáférésének korlátozásához szükséges lépéseket ismerteti példaként.

### <a name="create-a-storage-account"></a>Create a storage account

1. Válassza az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** lehetőséget.
2. Válassza a **Storage** elemet, majd a **Tárfiók – blob, fájl, tábla, üzenetsor** lehetőséget.
3. Adja meg vagy válassza ki a következő adatokat, fogadja el a fennmaradó alapértelmezett adatokat, majd válassza a **Létrehozás** elemet:

    |Beállítás|Érték|
    |----|----|
    |Name (Név)| Olyan nevet adjon meg, amely az összes Azure-helyen egyedi, 3–24 karakter hosszú, és csak számokat és kisbetűket tartalmaz.|
    |Fióktípus|StorageV2 (általános célú v2)|
    |Replikáció| Helyileg redundáns tárolás (LRS)|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport | Válassza a **Meglévő használata** lehetőséget, majd a *myResourceGroup* elemet.|
    |Hely| Válassza az **USA keleti régiója** lehetőséget. |

### <a name="create-a-file-share-in-the-storage-account"></a>Fájlmegosztás létrehozása a tárfiókban

1. Miután a tárfiók létrejött, írja be a tárfiók nevét a portál tetején található **Erőforrások, szolgáltatások és dokumentumok keresése** mezőbe. Amikor a tárfiók neve megjelenik a keresési eredmények között, válassza ki.
2. Válassza a **Files** lehetőséget, ahogyan az a következő képen látható:

    ![Tárfiók](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 
3. A **Fájlszolgáltatás** területen kattintson a **+ Fájlmegosztás** lehetőségre.
4. Adja meg a *my-file-share* nevet a **Név** területen, majd kattintson az **OK** gombra.
5. Zárja be a **Fájlszolgáltatás** ablakot.

### <a name="restrict-network-access-to-a-subnet"></a>Alhálózathoz való hálózati hozzáférés korlátozása

Alapértelmezés szerint a tárfiókok bármilyen hálózatban található ügyféltől érkező hálózati kapcsolatot elfogadnak, beleértve az internetet is. Tiltsa le a hálózat az internetről és az összes virtuális hálózat összes alhálózatáról való elérését, a *myVirtualNetwork* virtuális hálózat *Magánjellegű* alhálózatát kivéve.

1. A tárfiókhoz tartozó **BEÁLLÍTÁSOK** menüben válassza a **Tűzfalak és virtuális hálózatok** lehetőséget.
2. A **Virtuális hálózatok** területen válassza a **Kiválasztott hálózatok** lehetőséget.
3. Válassza a **Meglévő virtuális hálózat hozzáadása** elemet.
4. A **Hálózatok hozzáadása** területen válassza ki a következő értékeket, majd kattintson a **Hozzáadás** gombra:

    |Beállítás|Érték|
    |----|----|
    |Előfizetés| Válassza ki előfizetését.|
    |Virtuális hálózatok|A **Virtuális hálózatok** területen válassza a **myVirtualNetwork** lehetőséget.|
    |Alhálózatok| Az **Alhálózatok** területen válassza a **Magánjellegű** lehetőséget.|

    ![Tűzfalak és virtuális hálózatok](./media/tutorial-restrict-network-access-to-resources/storage-firewalls-and-virtual-networks.png) 

5. Kattintson a **Mentés** gombra.
6. Zárja be a **Tűzfalak és virtuális hálózatok** ablakot.
7. A tárfiókhoz tartozó **BEÁLLÍTÁSOK** területen válassza a **Hozzáférési kulcsok** elemet, ahogyan az a következő képen látható:

      ![Tűzfalak és virtuális hálózatok](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

8. Jegyezze meg a **kulcs** értékét, mivel ezt manuálisan meg kell adnia egy későbbi lépésben, amikor leképezi a fájlmegosztást egy meghajtó betűjelére egy virtuális gépen.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Tárfiókhoz való hálózati hozzáférés teszteléséhez helyezzen üzembe egy virtuális gépet minden alhálózaton.

### <a name="create-the-first-virtual-machine"></a>Az első virtuális gép létrehozása

1. Az Azure Portal bal felső sarkában kattintson az **+ Erőforrás létrehozása** gombra.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet.
3. Adja meg vagy válassza ki a következő adatokat, majd kattintson az **OK** gombra:

    |Beállítás|Érték|
    |----|----|
    |Name (Név)| myVmPublic|
    |Felhasználónév|Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport| Válassza a **Meglévő használata** lehetőséget, majd a **myResourceGroup** elemet.|
    |Hely| Válassza az **USA keleti régiója** lehetőséget.|

    ![Virtuális gép alapvető információinak megadása](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)
4. Válassza ki a virtuális gép méretét, majd kattintson a **Kiválasztás** gombra.
5. A **Beállítások** területen válassza a **Hálózat**, majd a **myVirtualNetwork** lehetőséget. Ezután válassza az **Alhálózat**, majd a **Nyilvános** lehetőséget, ahogyan az a következő képen látható:

    ![Virtuális hálózat kiválasztása](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)
6. Az **Összefoglalás** lapon válassza a **Létrehozás** lehetőséget a virtuális gép üzembe helyezésének megkezdéséhez. A virtuális gép üzembe helyezése néhány percig eltart, de közben folytathatja a következő lépéssel.

### <a name="create-the-second-virtual-machine"></a>A második virtuális gép létrehozása

Végezze el ismét az 1–6. lépést, azonban a 3 lépésben adja a virtuális gépnek a *myVmPrivate* nevet, az 5. lépésben pedig válassza a **Magánjellegű** alhálózatot.

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a következő lépéssel, amíg a rendszer létre nem hozza ezt a virtuális gépet, és a beállításai meg nem nyílnak a portálon.

## <a name="confirm-access-to-storage-account"></a>Tárfiókhoz való hozzáférés ellenőrzése

1. Miután létrejött a *myVmPrivate* virtuális gép, az Azure megnyitja az ahhoz tartozó beállításokat. Csatlakozzon a virtuális géphez a **Csatlakozás** gombra kattintva, ahogyan az a következő képen látható:

    ![Csatlakozás virtuális géphez](./media/tutorial-restrict-network-access-to-resources/connect-to-virtual-machine.png)

2. A **Csatlakozás** gomb kiválasztása után a rendszer létrehoz és letölt a számítógépre egy Remote Desktop Protocol (.rdp) fájlt.  
3. Nyissa meg a letöltött rdp-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania. 
4. Kattintson az **OK** gombra.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.
6. A *myVmPrivate* virtuális gépen a PowerShell-lel képezze le az Azure-fájlmegosztást a Z meghajtóra. A következő parancsok futtatása előtt cserélje le a `<storage-account-key>` és a `<storage-account-name>` elemet azokra az értékekre, amelyeket a [tárfiók létrehozása](#create-a-storage-account) során megadott.

    ```powershell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
    ```
    
    A PowerShell a következő példához hasonló kimenetet ad vissza:

    ```powershell
    Name           Used (GB)     Free (GB) Provider      Root
    ----           ---------     --------- --------      ----
    Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
    ```

    Az Azure-fájlmegosztás sikeresen le lett képezve a Z meghajtóra.

7. Egy parancssorból ellenőrizze, hogy a virtuális gépnek nincs-e kimenő csatlakozása az internethez:

    ```
    ping bing.com
    ```
    
    Nem kap választ, mert a *Magánjellegű* alhálózathoz rendelt hálózati biztonsági csoport nem engedélyezi a kimenő hozzáférést az internethez.

8. Zárja be a *myVmPrivate* virtuális gépre irányuló távoli asztali munkamenetet.

## <a name="confirm-access-is-denied-to-storage-account"></a>Tárfiókhoz való hozzáférés letiltásának ellenőrzése

1. Írja be a *myVmPublic* kifejezést a portál tetején található **Erőforrások, szolgáltatások és dokumentumok keresése** mezőbe.
2. Amikor a **myVmPublic** megjelenik a keresési eredmények között, válassza ki.
3. Végezze el a [Tárfiókhoz való hozzáférés ellenőrzése](#confirm-access-to-storage-account) rész 1–6. lépését a *myVmPublic* virtuális gép esetében.

    A hozzáférést a rendszer megtagadja, és a `New-PSDrive : Access is denied` hibaüzenetet adja vissza. A hozzáférést a rendszer megtagadja, mert a *myVmPublic* virtuális gép a *Nyilvános* alhálózaton van üzembe helyezve. A *Nyilvános* alhálózat nem rendelkezik az Azure Storage-hoz engedélyezett szolgáltatásvégponttal. A tárfiók kizárólag a *Magánjellegű* alhálózatról engedélyezi a hozzáférést, a *Nyilvános* alhálózatról nem.

4. Zárja be a távoli asztali munkamenetet a *myVmPublic* virtuális géppel.

5. Lépjen az [Azure Portalra](https://portal.azure.com).
6. Írja be a létrehozott tárfiók nevét a portál tetején található **Erőforrások, szolgáltatások és dokumentumok keresése** mezőbe. Amikor a tárfiók neve megjelenik a keresési eredmények között, válassza ki.
7. Válassza a **Files** (Fájlok) lehetőséget.
8. Ekkor a következő képen látható hiba jelenik meg:

    ![A hozzáférés megtagadva hiba](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)

    A hozzáférést a rendszer megtagadja, mert a számítógép nem a *MyVirtualNetwork* virtuális hálózat *Magánjellegű* alhálózatában található.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. Írja be a *myResourceGroup* nevet a portál tetején lévő **keresőmezőbe**. Amikor a **myResourceGroup** megjelenik a keresési eredmények között, válassza ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be a *myResourceGroup* nevet az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban engedélyezett egy szolgáltatásvégpontot egy virtuális hálózat alhálózatához. Megtudta, hogy a több Azure-szolgáltatásból üzembe helyezett erőforrások számára szolgáltatásvégpontokat engedélyezhet. Létrehozott egy Azure Storage-fiókot, és egy adott virtuális hálózati alhálózat erőforrásaira korlátozta a tárfiók felé irányuló hálózati hozzáférést. További információkat a szolgáltatásvégpontokról a [szolgáltatásvégpontok áttekintését](virtual-network-service-endpoints-overview.md) és az [alhálózatok kezelését](virtual-network-manage-subnet.md) ismertető cikkekben olvashat.

Ha több virtuális hálózat található a fiókjában, érdemes lehet összekapcsolni két virtuális hálózatot, hogy az egyes virtuális hálózatokban található erőforrások kommunikálhassanak egymással. Annak megismeréséhez, hogyan kapcsolhatók össze virtuális hálózatok, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Virtuális hálózatok csatlakoztatása](./tutorial-connect-virtual-networks-portal.md)
