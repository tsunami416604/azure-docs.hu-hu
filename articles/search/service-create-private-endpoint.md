---
title: Privát végpont létrehozása biztonságos kapcsolatokhoz
titleSuffix: Azure Cognitive Search
description: A szolgáltatás jelenleg előzetes verzióban érhető el, és egy privát végponton és egy biztonságos VNet-kapcsolaton keresztül korlátozza a keresési szolgáltatási végpontokhoz való hozzáférést.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 0ed6319a33fc3db0b3144d91f4be10b7ab6121d7
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75865011"
---
# <a name="restrict-access-to-azure-cognitive-search-using-private-endpoint-and-a-virtual-network-connection"></a>Az Azure Cognitive Search elérésének korlátozása privát végpont és virtuális hálózati kapcsolat használatával.

> [!IMPORTANT] 
> A privát végpont támogatása jelenleg korlátozott hozzáférésű előzetes verzióként érhető el. Ez az előzetes verzió csak az **alapszintű**keresési szolgáltatásokhoz érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [REST API 2019-10-01-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nem érhető el portál vagy .NET SDK-támogatás.

Ebből a cikkből megtudhatja, hogyan hozhat létre olyan új keresési szolgáltatást, amely biztonságos kapcsolatokon keresztül érhető el, és nyilvános IP-címekről való hozzáférés nélkül. Az ügyfélkapcsolatok a szolgáltatással megegyező virtuális hálózatban üzembe helyezett Azure-beli virtuális gépekről engedélyezettek.

## <a name="about-private-endpoint-support"></a>Tudnivalók a privát végpontok támogatásáról

Az Azure Cognitive Search [magánhálózati végpontok](../private-link/private-endpoint-overview.md) lehetővé teszik, hogy a virtuális hálózaton lévő ügyfelek biztonságosan hozzáférjenek az [adatkapcsolatokhoz](../private-link/private-link-overview.md). A privát végpont egy IP-címet használ a keresési szolgáltatáshoz tartozó [virtuális hálózati címtartomány](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) alapján. Az ügyfél és a keresési szolgáltatás közötti hálózati forgalom áthalad a virtuális hálózaton és a Microsoft gerinc hálózatán lévő privát kapcsolaton, ami kiküszöböli a nyilvános internetre való kitettséget. A privát hivatkozást támogató Pásti-szolgáltatások listájának megtekintéséhez tekintse meg a termék dokumentációjának [rendelkezésre állási szakaszát](../private-link/private-link-overview.md#availability) .

A keresési szolgáltatás privát végpontja a következőket teszi lehetővé:

+ A keresési szolgáltatás nyilvános végpontján lévő összes kapcsolat blokkolása.
+ Növelje a virtuális hálózat biztonságát azáltal, hogy letiltja a virtuális hálózat kiszűrése.
+ Biztonságosan csatlakozhat a keresési szolgáltatáshoz olyan helyszíni hálózatokról, amelyek VPN-vagy [Expressroute](../expressroute/expressroute-locations.md) [-](../vpn-gateway/vpn-gateway-about-vpngateways.md) kapcsolaton keresztül csatlakoznak a virtuális hálózathoz.

> [!NOTE]
> Ha a szolgáltatás végpontja privát, egyes portál-funkciók le vannak tiltva. Megtekintheti és kezelheti a szolgáltatási szint adatait, de a portál hozzáférése az adatok indexeléséhez és a szolgáltatás különböző összetevőihez, például az index, az indexelő és a készségkészlet-definíciók biztonsági okokból korlátozottak.

## <a name="request-access"></a>Hozzáférés kérése 

Az előzetes verzióra való feliratkozáshoz kattintson a [hozzáférés kérése](https://aka.ms/SearchPrivateLinkRequestAccess) lehetőségre. Az űrlap adatokat kér az Ön, a vállalat és az általános hálózati topológiáról. Ha áttekintjük a kérést, további utasításokkal megerősítő e-mailt fog kapni.

## <a name="create-a-vm"></a>Virtuális gép létrehozása
Ebben a szakaszban létre fog hozni egy virtuális hálózatot és alhálózatot a keresési szolgáltatás privát végpontjának eléréséhez használni kívánt virtuális gép üzemeltetéséhez.

### <a name="set-up-the-virtual-network"></a>A virtuális hálózat beállítása
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. A bal felső sarokban válassza az **erőforrás létrehozása** > **hálózatkezelés** > **virtuális hálózat**lehetőséget.
1. A **virtuális hálózat létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Név | *MyVirtualNetwork* megadása |
    | Címtér | Adja meg a *10.1.0.0/16* értéket |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza az **új létrehozása**elemet, írja be a *myResourceGroup*, majd kattintson **az OK gombra** . |
    | Földrajzi egység | Válassza ki az **USA nyugati** régióját vagy bármely Ön által használt régiót|
    | Alhálózat – név | *MySubnet* megadása |
    | Alhálózat – címtartomány | Adja meg a *10.1.0.0/24* értéket |
    |||

1. Hagyja a többi értéket alapértelmezettként, és válassza a **Létrehozás**lehetőséget.


### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. A fő portál lap bal felső részén válassza az **erőforrás létrehozása** > **számítási** > **virtuális gép**lehetőséget.

1. A **virtuális gép létrehozása – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.  |
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Virtuális gép neve | Adja meg a *myVm*. |
    | Region (Régió) | Válassza az **USA nyugati** régiója vagy bármely Ön által használt régiót. |
    | Rendelkezésre állási beállítások | Az alapértelmezett **infrastruktúra-redundancia megadása nem kötelező**. |
    | Lemezkép | Válassza a **Windows Server 2019 Datacenter**lehetőséget. |
    | Méret | Hagyja meg az alapértelmezett **standard DS1 v2**értéket. |
    | **RENDSZERGAZDAI FIÓK** |  |
    | Felhasználónév | Adja meg a választott felhasználónevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Adja meg újra a jelszót. |
    | **BEJÖVŐ PORTOK SZABÁLYAI** |  |
    | Nyilvános bejövő portok | Hagyja meg az alapértelmezett **nincs**értéket. |
    | **PÉNZ MEGTAKARÍTÁSA** |  |
    | Már van Windows-licence? | Hagyja meg az alapértelmezett **nem**értéket. |
    |||

1. Válassza a **Tovább: lemezek**lehetőséget.

1. A **virtuális gép létrehozása – lemezek**területen hagyja meg az alapértelmezett értékeket, és válassza a **Tovább: hálózatkezelés**lehetőséget.

1. A **virtuálisgép-hálózat létrehozása**területen válassza ki ezt az információt:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Virtuális hálózat | Hagyja meg az alapértelmezett **MyVirtualNetwork**.  |
    | Címtér | Hagyja meg az alapértelmezett **10.1.0.0/24**értéket.|
    | Alhálózat | Hagyja meg az alapértelmezett **mySubnet (10.1.0.0/24)** .|
    | Nyilvános IP-cím | Hagyja meg az alapértelmezett **(új) myVm-IP-címet**. |
    | Nyilvános bejövő portok | Válassza a **kiválasztott portok engedélyezése**lehetőséget. |
    | Bejövő portok kiválasztása | Válassza a **http** és az **RDP**lehetőséget.|
    ||

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt.

1. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget.


## <a name="create-your-search-service-with-a-private-endpoint"></a>Saját keresési szolgáltatás létrehozása privát végponttal

Ebben a szakaszban egy új Azure Cognitive Search szolgáltatást fog létrehozni egy privát végponttal. 

1. A fő portál lap bal felső részén válassza az **erőforrás létrehozása** > **Web** > **Azure Cognitive Search**lehetőséget.

1. Az **új Search Service – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **PÉLDÁNY RÉSZLETEI** |  |
    | URL-cím | Adjon meg egy egyedi nevet. |
    | Földrajzi egység | Válassza ki azt a régiót, amelyet az előzetes verziójú szolgáltatáshoz való hozzáférés kérésekor adott meg. |
    | Díjcsomag | Válassza az **árképzési szint módosítása** lehetőséget, majd az **alapszintű**lehetőséget. Az előzetes verzióhoz ez a szintet kötelező megadni. |
    |||
  
1. Válassza a **Next (tovább): skála**lehetőséget.

1. Hagyja meg az alapértelmezett értéket, és válassza a **Tovább: hálózatkezelés**lehetőséget.

1. Az **új Search Service-hálózatkezelés**területen válassza a **privát** lehetőséget a **végponti kapcsolathoz (adatkapcsolat)** .

1. Az **új Search Service-hálózatkezelés**területen válassza a **+ Hozzáadás** lehetőséget a **privát végpont**alatt. 

1. A **privát végpont létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    | Földrajzi egység | Válassza az **USA nyugati**régiója lehetőséget.|
    | Név | Adja meg a *myPrivateEndpoint*.  |
    | Cél alerőforrása | Hagyja meg az alapértelmezett **searchService**. |
    | **HÁLÓZATI** |  |
    | Virtuális hálózat  | Válassza ki a *MyVirtualNetwork* az erőforráscsoport *myResourceGroup*. |
    | Alhálózat | Válassza a *mySubnet*lehetőséget. |
    | **MAGÁNHÁLÓZATI DNS-INTEGRÁCIÓ** |  |
    | Integrálás saját DNS-zónával  | Hagyja meg az alapértelmezett **Igen értéket**. |
    | Privát DNS-zóna  | Hagyja meg az alapértelmezett * * (új) privatelink.search.windows.net * * értéket. |
    |||

1. Kattintson az **OK** gombra. 

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt. 

1. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget. 
1. A szolgáltatás létrehozása után keresse meg az imént létrehozott erőforrást.
1. A bal oldali tartalom menüben válassza a **kulcsok** lehetőséget.
1. Másolja az **elsődleges rendszergazdai kulcsot** a következő lépésben való használatra.

 
## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Kapcsolódjon a virtuális gép *myVm* az internetről a következőképpen:

1. A portál keresési sávján adja meg a *myVm*.

1. Kattintson a **Csatlakozás** gombra. A **Kapcsolódás** gombra kattintva megnyílik a **virtuális géphez való kapcsolódás** .

1. Válassza az **RDP-fájl letöltése**lehetőséget. Az Azure létrehoz egy RDP protokoll ( *. rdp*) fájlt, és letölti a számítógépre.

1. Nyissa meg a letöltött. rdp fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához **több választási lehetőséget** kell kiválasztania > **eltérő fiókot használjon**.

1. Kattintson az **OK** gombra.

1. A bejelentkezéskor tanúsítványra vonatkozó figyelmeztetés jelenhet meg. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás**lehetőséget.

1. Ha megjelenik a virtuális gép asztala, csökkentse a helyi asztalra való visszatérést.  


## <a name="access-the-search-service-privately-from-the-vm"></a>A keresési szolgáltatás elérése a virtuális gépről

Ebben a szakaszban ellenőriznie kell a magánhálózati hozzáférést a keresési szolgáltatáshoz, és a magánhálózati végponton keresztül kell csatlakoznia a Storage-fiókhoz.

1. A *myVM*távoli asztal nyissa meg a PowerShellt.

1. Adja meg az "nslookup [Search szolgáltatás neve]. Search. Windows. net" kifejezést.

    Ehhez hasonló üzenet jelenik meg:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```
1. Kövesse ezt [a rövid útmutatót a virtuális](search-get-started-postman.md) gépről, és hozzon létre egy új keresési indexet a szolgáltatásában a REST API használatával.

1. Próbálja ki ugyanezeket a kérelmeket a Poster szolgáltatásban a helyi munkaállomáson.

1. Ha el tudja végezni a gyors üzembe helyezést a virtuális gépről, de hibaüzenetet kap arról, hogy a távoli kiszolgáló nem létezik a helyi munkaállomáson, sikeresen konfigurált egy privát végpontot a keresési szolgáltatáshoz.

1. A távoli asztali kapcsolat bezárásával *myVM*. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha végzett a privát végpont, a Search szolgáltatás fiók és a virtuális gép használatával, törölje az erőforráscsoportot és a benne lévő összes erőforrást: 

1. Adja meg a *myResourceGroup* a portál tetején található **keresőmezőbe** , és válassza ki a *myResourceGroup* a keresési eredmények közül. 
1. Válassza az **Erőforráscsoport törlése** elemet. 
1. Adja meg a *myResourceGroup* az **erőforráscsoport nevének** beírásához, majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben létrehozott egy virtuális GÉPET egy virtuális hálózaton és egy keresési szolgáltatást egy privát végponttal. Az internetről csatlakozik a virtuális géphez, és biztonságosan kommunikál a keresési szolgáltatással a privát hivatkozás használatával. 

> [!div class="nextstepaction"]
> [Mi az az Azure Private Endpoint?](../private-link/private-endpoint-overview.md).
