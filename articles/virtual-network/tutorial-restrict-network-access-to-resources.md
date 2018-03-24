---
title: Hálózati hozzáférés korlátozása PaaS erőforrások – az Azure portálon |} Microsoft Docs
description: Megtudhatja, hogyan korlátjának növelését, és korlátozhatja a hálózati hozzáférést az Azure-erőforrások, például az Azure Storage és az Azure SQL Database, a virtuális hálózati szolgáltatás végpontokat az Azure portál használatával.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 9a64a5c1f63dc05cba6fdfa310b694e34bdba7d1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Hálózati hozzáférés korlátozása PaaS erőforrások virtuális hálózati szolgáltatás végpontokon az Azure portál használatával

Virtuális hálózati szolgáltatási végpont lehetővé teszik a virtuális hálózati alhálózat az Azure szolgáltatás erőforrásokhoz való hálózati hozzáférés korlátozásához. Eltávolíthatja az internet-hozzáférés az erőforrásokhoz is. Végpontok adja meg a közvetlen kapcsolat a virtuális hálózat és a támogatott Azure-szolgáltatásokat, hogy lehetővé teszi a virtuális hálózat titkos címterület használata az Azure-szolgáltatások eléréséhez. Azure-erőforrások Szolgáltatásvégpontok keresztül mindig irányuló forgalom a Microsoft Azure hálózat marad. Ebből a cikkből megismerheti, hogyan:

> [!div class="checklist"]
> * Hozzon létre egy virtuális hálózatot egyetlen alhálózattal
> * Adjon hozzá egy alhálózatot és egy végpontot engedélyezése
> * Hozzon létre egy Azure-erőforrás és a hálózati hozzáférés engedélyezése, hogy csak egy alhálózatból
> * Virtuális gép (VM) telepítése minden egyes alhálózathoz
> * Erősítse meg a hozzáférést egy erőforráshoz alhálózatból származó
> * Győződjön meg arról, a hozzáférés megtagadva erőforrás alhálózat és az internetről

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure portálon, a http://portal.azure.com.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Válassza ki **+ hozzon létre egy erőforrást** a felső, bal oldali sarkában az Azure-portálon.
2. Válassza ki **hálózati**, majd válassza ki **virtuális hálózati**.
3. Adja meg, vagy válassza ki, a következő adatokat, majd **létrehozása**:

    |Beállítás|Érték|
    |----|----|
    |Name (Név)| myVirtualNetwork |
    |Címtér| 10.0.0.0/16|
    |Előfizetés| Jelölje ki az előfizetését|
    |Erőforráscsoport | Válassza ki **hozzon létre új** , és írja be *myResourceGroup*.|
    |Hely| Válassza ki **USA keleti régiója** |
    |Alhálózat neve| Nyilvános|
    |Alhálózati címtartományt| 10.0.0.0/24|
    |Szolgáltatás-végpontok| Letiltva|

    ![A virtuális hálózat alapszintű adatainak megadása](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)


## <a name="enable-a-service-endpoint"></a>A szolgáltatásvégpont engedélyezése

1. Az a **keresést az erőforrások, a szolgáltatások és a dokumentumok** a portál felső mezőbe írja be *myVirtualNetwork.* Ha **myVirtualNetwork** megjelenik a keresési eredmények között, jelölje be.
2. Adjon hozzá egy alhálózatot a virtuális hálózat. A **beállítások**, jelölje be **alhálózatok**, majd válassza ki **+ alhálózati**, az alábbi ábrán látható módon:

    ![Alhálózat hozzáadása](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. A **alhálózat hozzáadása**, vagy adja meg a következő adatokat, majd válassza ki és **OK**:

    |Beállítás|Érték|
    |----|----|
    |Name (Név)| Saját |
    |Címtartomány| 10.0.1.0/24|
    |Szolgáltatás-végpontok| Válassza ki **Microsoft.Storage** alatt **szolgáltatások**|

## <a name="restrict-network-access-to-and-from-a-subnet"></a>Korlátozhatja a hálózati hozzáférést, és az alhálózatot

1. Válassza ki **+ hozzon létre egy erőforrást** a felső, bal oldali sarkában az Azure-portálon.
2. Válassza ki **hálózati**, majd válassza ki **hálózati biztonsági csoport**.
A **hálózati biztonsági csoport létrehozása**, adja meg, vagy válassza ki, a következő adatokat, majd **létrehozása**:

    |Beállítás|Érték|
    |----|----|
    |Name (Név)| myNsgPrivate |
    |Előfizetés| Jelölje ki az előfizetését|
    |Erőforráscsoport | Válassza ki **meglévő** válassza *myResourceGroup*.|
    |Hely| Válassza ki **USA keleti régiója** |

4. A hálózati biztonsági csoport létrehozása után adja meg a *myNsgPrivate*, a a **keresést az erőforrások, a szolgáltatások és a dokumentumok** a portál felső részén. Ha **myNsgPrivate** megjelenik a keresési eredmények között, jelölje be.
5. A **beállítások**, jelölje be **kimenő biztonsági szabályok**.
6. Válassza ki **+ Hozzáadás**.
7. Hozzon létre egy szabályt, amely a kimenő hozzáférést biztosít a nyilvános IP-címek az Azure Storage szolgáltatás. Adja meg, vagy válassza ki, a következő adatokat, majd **OK**:

    |Beállítás|Érték|
    |----|----|
    |Forrás| Select **VirtualNetwork** |
    |Forrás porttartományok| * |
    |Cél | Válassza ki **címke szolgáltatás**|
    |Cél szolgáltatás címke | Válassza ki **tároló**|
    |Cél porttartományok| * |
    |Protokoll|Bármelyik|
    |Műveletek|Engedélyezés|
    |Prioritás|100|
    |Name (Név)|Allow-Storage-All|
8. Hozzon létre egy szabályt, amely felülbírálja az alapértelmezett biztonsági szabály, amely a kimenő hozzáférést biztosít az összes nyilvános IP-címeket. Lépésekkel 6 és 7 újra, a következő értékekkel:

    |Beállítás|Érték|
    |----|----|
    |Forrás| Select **VirtualNetwork** |
    |Forrás porttartományok| * |
    |Cél | Válassza ki **címke szolgáltatás**|
    |Cél szolgáltatás címke| Válassza ki **Internet**|
    |Cél porttartományok| * |
    |Protokoll|Bármelyik|
    |Műveletek|Megtagadás|
    |Prioritás|110|
    |Name (Név)|Deny-Internet-All|

9. A **beállítások**, jelölje be **bejövő biztonsági szabályok**.
10. Válassza ki **+ Hozzáadás**.
11. Hozzon létre egy szabályt, amely lehetővé teszi, hogy a távoli asztal protokoll (RDP) forgalmat bárhonnan alhálózathoz bejövő. A szabály felülbírálja a alapértelmezett szabály, amely megtiltja a minden bejövő forgalom az internetről. Távoli asztali kapcsolatokhoz engedélyezett az alhálózat, hogy a kapcsolat egy későbbi lépésben tesztelhető. Lépésekkel 6 és 7 újra, a következő értékekkel:

    |Beállítás|Érték|
    |----|----|
    |Forrás| Bármelyik |
    |Forrás porttartományok| * |
    |Cél | Válassza ki **címke szolgáltatás**|
    |Cél szolgáltatás címke| Select **VirtualNetwork**|
    |Cél porttartományok| 3389 |
    |Protokoll|Bármelyik|
    |Műveletek|Engedélyezés|
    |Prioritás|120|
    |Name (Név)|Allow-RDP-All|

12. A **beállítások**, jelölje be **alhálózatok**.
13. Válassza ki **+ társítása**
14. A **alhálózatot társítani**, jelölje be **virtuális hálózati** , és válassza **myVirtualNetwork** alatt **virtuális hálózatot választ**.
15. A **alhálózat kiválasztása**, jelölje be **titkos**, majd válassza ki **OK**.

## <a name="restrict-network-access-to-a-resource"></a>Egy erőforrás való hálózati hozzáférés korlátozása

Azure-végpontok engedélyezett szolgáltatások segítségével létrehozott erőforrások való hálózati hozzáférés korlátozása szükséges lépések szolgáltatásban függően változik. Az egyes szolgáltatások lépéseit minden egyes szolgáltatás dokumentációjában találhat. Ez a cikk hátralévő része az Azure Storage-fiók, mint például a hálózati hozzáférés korlátozása.

### <a name="create-a-storage-account"></a>Create a storage account

1. Válassza ki **+ hozzon létre egy erőforrást** a felső, bal oldali sarkában az Azure-portálon.
2. Válassza ki **tárolási**, majd válassza ki **tárfiók - blob, a fájl, a tábla, a várólista**.
3. Adja meg, vagy válassza ki, a következő információkat, fogadja el a többi alapértelmezett beállításokat, majd válassza ki **létrehozása**:

    |Beállítás|Érték|
    |----|----|
    |Name (Név)| Adjon meg egy nevet, amely egyedi az összes Azure helyszínen, 3 – 24 karakter hosszúságúnak, használatával csak számokat és kisbetűket tartalmazhatnak.|
    |Fióktípus|StorageV2 (általános célú v2)|
    |Replikáció| Helyileg redundáns tárolás (LRS)|
    |Előfizetés| Jelölje ki az előfizetését|
    |Erőforráscsoport | Válassza ki **meglévő** válassza *myResourceGroup*.|
    |Hely| Válassza ki **USA keleti régiója** |

### <a name="create-a-file-share-in-the-storage-account"></a>Fájlmegosztás létrehozása a tárfiókban lévő

1. A tárfiók létrehozása után adja meg a tárfiók nevét a **keresést az erőforrások, a szolgáltatások és a dokumentumok** mezőt, a portál tetején. Ha a tárfiók neve megjelenik a keresési eredmények között, válassza ki azt.
2. Válassza ki **fájlok**, az alábbi ábrán látható módon:

    ![Tárfiók](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 
3. Válassza ki **+ fájlmegosztás**a **File szolgáltatás**.
4. Adja meg *saját fájlmegosztás* alatt **neve**, majd válassza ki **OK**.
5. Zárja be a **File szolgáltatás** mezőbe.

### <a name="enable-network-access-from-a-subnet"></a>Egy alhálózatot a hálózati hozzáférés engedélyezése

Alapértelmezés szerint a storage-fiókok bármely hálózatban lévő ügyfelek hálózati kapcsolatokat fogadjon. Csak egy adott alhálózaton való hozzáférés engedélyezése, és minden egyéb hálózat hálózati elérésének megtagadása, végezze el az alábbi lépéseket:

1. A **beállítások** válassza ki a tárfiók **tűzfalak és a virtuális hálózatok**.
2. A **virtuális hálózatok**, jelölje be **hálózatok kijelölt**.
3. Válassza ki **meglévő virtuális hálózat hozzáadása**.
4. A **hálózatokat adhat**, a következő értékek, majd válassza ki és **Hozzáadás**:

    |Beállítás|Érték|
    |----|----|
    |Előfizetés| Válassza ki előfizetését.|
    |Virtuális hálózatok|Válassza ki **myVirtualNetwork**a **virtuális hálózatok**|
    |Alhálózatok| Válassza ki **titkos**a **alhálózatok**|

    ![Tűzfalak és a virtuális hálózatok](./media/tutorial-restrict-network-access-to-resources/storage-firewalls-and-virtual-networks.png) 

5. Kattintson a **Mentés** gombra.
6. Zárja be a **tűzfalak és a virtuális hálózatok** mezőbe.
7. A **beállítások** válassza ki a tárfiók **hívóbetűk**, az alábbi ábrán látható módon:

      ![Tűzfalak és a virtuális hálózatok](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

8. Megjegyzés: a **kulcs** érték, mivel manuálisan is beírhatja azt egy későbbi lépésben Ha a fájlmegosztás leképezése egy virtuális gép egy meghajtó-betűjellel kell.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Tesztelje a storage-fiók a hálózati hozzáférést, telepítenie kell a virtuális gépek minden egyes alhálózatot.

### <a name="create-the-first-virtual-machine"></a>Az első virtuális gép létrehozása

1. Válassza ki **+ hozzon létre egy erőforrást** az Azure portál bal oldali, felső sarkában található.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet.
3. Adja meg, vagy válassza ki, a következő adatokat, majd **OK**:

    |Beállítás|Érték|
    |----|----|
    |Name (Név)| myVmPublic|
    |Felhasználónév|Adjon meg a nevet.|
    |Jelszó| Adja meg a jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport| Válassza ki **meglévő** válassza **myResourceGroup**.|
    |Hely| Válassza ki **USA keleti régiója**.|

    ![Adja meg a virtuális géppel kapcsolatos alapvető információk](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)
4. Válassza ki a virtuális gép méretét majd **válasszon**.
5. A **beállítások**, jelölje be **hálózati** majd **myVirtualNetwork**. Válassza ki **alhálózati**, és válassza ki **nyilvános**, az alábbi ábrán látható módon:

    ![Válasszon virtuális hálózatot](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)
6. Az a **összegzés** lapon jelölje be **létrehozása** elindítani a virtuális gépek telepítése során. A virtuális gép telepítése néhány percet vesz igénybe, de továbbra is a következő lépés során a virtuális Gépet hoz létre.

### <a name="create-the-second-virtual-machine"></a>A második virtuális gép létrehozása

1-6 lépéseket újra, de a 3, a virtuális gép neve *myVmPrivate* 5. lépésben, válassza ki a **titkos** alhálózat.

A virtuális gép telepítése néhány percet vesz igénybe. Ne folytassa a következő lépéssel mindaddig, amíg az végzett a létrehozással és a beállítások nyissa meg a portálon.

## <a name="confirm-access-to-storage-account"></a>Erősítse meg a tárfiók eléréséhez

1. Egyszer a *myVmPrivate* VM végzett a létrehozással, Azure megnyitja azt a beállításait. Csatlakozás a virtuális gép kiválasztásával a **Connect** gombra kattint, az alábbi ábrán látható módon:

    ![Csatlakozzon a virtuális géphez](./media/tutorial-restrict-network-access-to-resources/connect-to-virtual-machine.png)

2. Kijelölése után a **Connect** gombra, a távoli asztal protokoll (RDP) fájl jön létre, és a számítógép letölti.  
3. Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza ki a **Connect**. Adja meg a felhasználónevet és a virtuális gép létrehozásakor megadott jelszót. Válassza ki szeretne **több lehetőséget**, majd **használjon más fiókot**, hogy a virtuális gép létrehozása után a megadott hitelesítő adatok megadása. 
4. Kattintson az **OK** gombra.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a figyelmeztetést kap, válassza ki a **Igen** vagy **Folytatás**, hogy a kapcsolat.
6. Az a *myVmPrivate* VM PowerShell-lel Z meghajtóra Azure fájlmegosztás hozzárendelését. Cserélje le a parancsok futtatásához `<storage-account-key>` és `<storage-account-name>` megadott és a lekért értékekkel [hozzon létre egy tárfiókot](#create-a-storage-account).

    ```powershell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
    ```
    
    PowerShell-kimenet visszaadása hasonló a következő egy példa a kimenetre:

    ```powershell
    Name           Used (GB)     Free (GB) Provider      Root
    ----           ---------     --------- --------      ----
    Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
    ```

    Az Azure-fájlmegosztáshoz sikeresen leképezve a Z meghajtóra.

7. Győződjön meg arról, hogy a virtuális gép kimenő kapcsolat bármely nyilvános IP-címek a parancssorba:

    ```
    ping bing.com
    ```
    
    Nem érkezett válasz, mert a társított hálózati biztonsági csoport kapni a *titkos* alhálózat nem engedélyezi a nyilvános IP-címek kívül a címek az Azure Storage szolgáltatás kimenő hozzáférést.

8. Zárja be a távoli asztali munkamenetet a *myVmPrivate* virtuális gép.

## <a name="confirm-access-is-denied-to-storage-account"></a>Erősítse meg a tárfiók a hozzáférés megtagadva

1. Adja meg *myVmPublic* a a **keresést az erőforrások, a szolgáltatások és a dokumentumok** a portál felső részén.
2. Ha **myVmPublic** megjelenik a keresési eredmények között, jelölje be.
3. Végezze el a lépéseket 1-6 [erősítse meg a tárfiók hozzáférést](#confirm-access-to-storage-account) a a *myVmPublic* virtuális gép.

    Nincs hozzáférése, és megjelenik egy `New-PSDrive : Access is denied` hiba. Hozzáférés megtagadva, mert a *myVmPublic* virtuális gép telepítve van a *nyilvános* alhálózat. A *nyilvános* alhálózati nincs engedélyezve az Azure Storage szolgáltatásvégpont, és a tárfiók csak a hálózati hozzáférés lehetővé teszi a *titkos* alhálózati, nem a *nyilvános*alhálózat.

4. Zárja be a távoli asztali munkamenetet a *myVmPublic* virtuális gép.

5. A számítógépről, keresse meg az Azure [portal](https://portal.azure.com).
6. Adja meg a létrehozott storage-fiók nevét a **keresést az erőforrások, a szolgáltatások és a dokumentumok** mezőbe. Ha a tárfiók neve megjelenik a keresési eredmények között, válassza ki azt.
7. Válassza a **Files** (Fájlok) lehetőséget.
8. A hibaüzenet a következő ábrán látható:

    ![A hozzáférés megtagadva hiba](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)

    Hozzáférés megtagadva, mert a számítógép nincs a *titkos* alhálózata a *MyVirtualNetwork* virtuális hálózat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Már nincs szükség, ha az erőforráscsoport és a benne található összes erőforrást törli:

1. Adja meg *myResourceGroup* a a **keresési** a portál felső részén. Amikor látja **myResourceGroup** válassza ki azt a keresési eredmények között.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Adja meg *myResourceGroup* a **típus az ERŐFORRÁSCSOPORT-név:** válassza **törlése**.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy végpontot a virtuális hálózati alhálózat engedélyezve van. Megtudta, hogy engedélyezhető-e a végpontok a több Azure-szolgáltatásokkal üzembe helyezett erőforrás. Létrehozott egy Azure Storage-fiók és a tárolási fiók csak egy virtuális hálózati alhálózat erőforrásainak korlátozott hálózati hozzáférést. Létrehozása előtt Szolgáltatásvégpontok éles környezetben virtuális hálózatok, javasoljuk, hogy alaposan feltérképezése [szolgáltatás végpontjait](virtual-network-service-endpoints-overview.md).

Ha több virtuális hálózat már rendelkezik fiókjába, érdemes lehet összekapcsolni két virtuális hálózatok, az egyes virtuális hálózati erőforrások is kommunikálhatnak egymással. A következő oktatóanyag megtudhatja, hogyan csatlakozzon a virtuális hálózatok továbblépés.

> [!div class="nextstepaction"]
> [Virtuális hálózatok csatlakoztatása](./tutorial-connect-virtual-networks-portal.md)
