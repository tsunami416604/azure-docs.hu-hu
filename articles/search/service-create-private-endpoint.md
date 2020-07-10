---
title: Privát végpont létrehozása biztonságos kapcsolatok létrehozásához
titleSuffix: Azure Cognitive Search
description: Magánhálózati végpont beállítása egy virtuális hálózatban az Azure Cognitive Search szolgáltatással való biztonságos kapcsolódáshoz
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 27fb165c36c17cee83cd9f90eba3bdcb9e32d517
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206897"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search"></a>Hozzon létre egy privát végpontot biztonságos kapcsolódáshoz az Azure Cognitive Search

Ebben a cikkben a Azure Portal használatával hozzon létre egy új Azure Cognitive Search Service-példányt, amely nem érhető el az interneten keresztül. Ezután konfiguráljon egy Azure-beli virtuális gépet ugyanabban a virtuális hálózatban, és használja azt a keresési szolgáltatás privát végponton keresztüli eléréséhez.

A privát végpontokat az [Azure privát kapcsolata](../private-link/private-link-overview.md)külön szolgáltatásként biztosíthatja. A költségekkel kapcsolatos további információkért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/private-link/).

> [!Important]
> Az Azure Cognitive Search privát végpontjának támogatása a Azure Portal vagy a 2020-03-13-es [verziójú felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/)használatával konfigurálható. Ha a szolgáltatás végpontja privát, egyes portál-funkciók le vannak tiltva. Megtekintheti és kezelheti a szolgáltatási szint adatait, de a portál hozzáférése az adatok indexeléséhez és a szolgáltatás különböző összetevőihez, például az index, az indexelő és a készségkészlet-definíciók biztonsági okokból korlátozottak.

## <a name="why-use-a-private-endpoint-for-secure-access"></a>Miért érdemes privát végpontot használni a biztonságos hozzáféréshez?

Az Azure Cognitive Search [magánhálózati végpontok](../private-link/private-endpoint-overview.md) lehetővé teszik, hogy egy virtuális hálózaton lévő ügyfél biztonságosan hozzáférhessen egy keresési indexben lévő adathoz egy [privát hivatkozáson](../private-link/private-link-overview.md)keresztül. A privát végpont egy IP-címet használ a keresési szolgáltatáshoz tartozó [virtuális hálózati címtartomány](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) alapján. Az ügyfél és a keresési szolgáltatás közötti hálózati forgalom áthalad a virtuális hálózaton és a Microsoft gerinc hálózatán található privát kapcsolaton, ami kiküszöböli a nyilvános internetről való kitettséget. A privát hivatkozást támogató egyéb Pásti-szolgáltatások listájáért tekintse meg a termék dokumentációjának [rendelkezésre állási szakaszát](../private-link/private-link-overview.md#availability) .

A keresési szolgáltatás privát végpontjai a következőket teszik lehetővé:

- A keresési szolgáltatás nyilvános végpontján lévő összes kapcsolat blokkolása.
- Növelje a virtuális hálózat biztonságát azáltal, hogy letiltja a virtuális hálózat kiszűrése.
- Biztonságosan csatlakozhat a keresési szolgáltatáshoz olyan helyszíni hálózatokról, amelyek VPN-vagy [Expressroute](../expressroute/expressroute-locations.md) [-](../vpn-gateway/vpn-gateway-about-vpngateways.md) kapcsolaton keresztül csatlakoznak a virtuális hálózathoz.

## <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Ebben a szakaszban létre fog hozni egy virtuális hálózatot és alhálózatot a keresési szolgáltatás privát végpontjának eléréséhez használni kívánt virtuális gép üzemeltetéséhez.

1. A Azure Portal Kezdőlap lapon válassza az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **virtuális hálózat**lehetőséget.

1. A **virtuális hálózat létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Előfizetés | Az előfizetés kiválasztása|
    | Erőforráscsoport | Válassza az **új létrehozása**elemet, írja be a *myResourceGroup*, majd kattintson **az OK gombra** . |
    | Név | *MyVirtualNetwork* megadása |
    | Régió | Válassza ki a kívánt régiót |
    |||

1. Hagyja meg az alapértelmezett értékeket a többi beállításnál. Kattintson a **felülvizsgálat + létrehozás** , majd a **Létrehozás** elemre.

## <a name="create-a-search-service-with-a-private-endpoint"></a>Keresési szolgáltatás létrehozása privát végponttal

Ebben a szakaszban egy új Azure Cognitive Search szolgáltatást fog létrehozni egy privát végponttal. 

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **webes**  >  **Azure-Cognitive Search**lehetőséget.

1. Az **új Search Service – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki az előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **PÉLDÁNY RÉSZLETEI** |  |
    | URL-cím | Adjon meg egy egyedi nevet. |
    | Hely | Válassza ki a kívánt régiót. |
    | Tarifacsomag | Válassza az **árképzés módosítása** lehetőséget, és válassza ki a kívánt szolgáltatási szintet. (Az **ingyenes** szintet nem támogatja. **Alap** vagy magasabb értéknek kell lennie.) |
    |||
  
1. Válassza a **Next (tovább): skála**lehetőséget.

1. Hagyja meg az alapértelmezett értéket, és válassza a **Tovább: hálózatkezelés**lehetőséget.

1. Az **új Search Service-hálózatkezelés**területen válassza a **privát** lehetőséget a **végponti kapcsolathoz (adatkapcsolat)**.

1. Az **új Search Service-hálózatkezelés**területen válassza a **+ Hozzáadás** lehetőséget a **privát végpont**alatt. 

1. A **privát végpont létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Előfizetés | Válassza ki az előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    | Hely | Válassza az **USA nyugati**régiója lehetőséget.|
    | Név | Adja meg a *myPrivateEndpoint*.  |
    | Cél alerőforrása | Hagyja meg az alapértelmezett **searchService**. |
    | **HÁLÓZATI** |  |
    | Virtuális hálózat  | Válassza ki a *MyVirtualNetwork* az erőforráscsoport *myResourceGroup*. |
    | Alhálózat | Válassza a *mySubnet*lehetőséget. |
    | **MAGÁNHÁLÓZATI DNS-INTEGRÁCIÓ** |  |
    | Integrálás saját DNS-zónával  | Hagyja meg az alapértelmezett **Igen értéket**. |
    | Privát DNS-zóna  | Hagyja meg az alapértelmezett * * (új) privatelink.search.windows.net * * értéket. |
    |||

1. Válassza az **OK** lehetőséget. 

1. Válassza az **Áttekintés és létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt. 

1. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget. 

1. Miután befejeződött az új szolgáltatás üzembe helyezése, keresse meg az imént létrehozott erőforrást.

1. A bal oldali tartalom menüben válassza a **kulcsok** lehetőséget.

1. Másolja az **elsődleges rendszergazdai kulcsot** később, amikor csatlakozik a szolgáltatáshoz.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép**lehetőséget.

1. A **virtuális gép létrehozása – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki az előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.  |
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Virtuális gép neve | Adja meg a *myVm*. |
    | Régió | Válassza az **USA nyugati** régiója vagy bármely Ön által használt régiót. |
    | Rendelkezésre állási beállítások | Az alapértelmezett **infrastruktúra-redundancia megadása nem kötelező**. |
    | Rendszerkép | Válassza a **Windows Server 2019 Datacenter**lehetőséget. |
    | Méret | Hagyja meg az alapértelmezett **standard DS1 v2**értéket. |
    | **RENDSZERGAZDAI FIÓK** |  |
    | Felhasználónév | Adja meg a választott felhasználónevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Adja meg újra a jelszót. |
    | **BEJÖVŐ PORTOK SZABÁLYAI** |  |
    | Nyilvános bejövő portok | Hagyja meg az alapértelmezett **engedélyezett portok engedélyezése beállítást**. |
    | Bejövő portok kiválasztása | Hagyja meg az alapértelmezett **RDP-t (3389)**. |
    | **PÉNZ MEGTAKARÍTÁSA** |  |
    | Már van Windows-licence? | Hagyja meg az alapértelmezett **nem**értéket. |
    |||

1. Válassza a **Tovább: lemezek**lehetőséget.

1. A **virtuális gép létrehozása – lemezek**területen hagyja meg az alapértelmezett értékeket, és válassza a **Tovább: hálózatkezelés**lehetőséget.

1. A **virtuálisgép-hálózat létrehozása**területen válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Virtuális hálózat | Hagyja meg az alapértelmezett **MyVirtualNetwork**.  |
    | Címtér | Hagyja meg az alapértelmezett **10.1.0.0/24**értéket.|
    | Alhálózat | Hagyja meg az alapértelmezett **mySubnet (10.1.0.0/24)**.|
    | Nyilvános IP-cím | Hagyja meg az alapértelmezett **(új) myVm-IP-címet**. |
    | Nyilvános bejövő portok | Válassza a **kiválasztott portok engedélyezése**lehetőséget. |
    | Bejövő portok kiválasztása | Válassza a **http** és az **RDP**lehetőséget.|
    ||

1. Válassza az **Áttekintés és létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt.

1. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget. 


## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Töltse le, majd kapcsolódjon a virtuális gép *myVm* a következőképpen:

1. A portál keresési sávján adja meg a *myVm*.

1. Kattintson a **Csatlakozás** gombra. A **Kapcsolódás** gombra kattintva megnyílik a **virtuális géphez való kapcsolódás** .

1. Válassza az **RDP-fájl letöltése** lehetőséget. Az Azure létrehoz egy RDP protokoll (*. rdp*) fájlt, és letölti a számítógépre.

1. Nyissa meg a letöltött. rdp fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy a **More choices**  >  virtuális gép létrehozásakor megadott hitelesítő adatok megadásához több választási lehetőséget kell választania**egy másik fiók használatával**.

1. Válassza az **OK** lehetőséget.

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás**lehetőséget.

1. Ha megjelenik a virtuális gép asztala, csökkentse a helyi asztalra való visszatérést.  


## <a name="test-connections"></a>Kapcsolatok tesztelése

Ebben a szakaszban ellenőrzi, hogy a magánhálózati hozzáférés a keresési szolgáltatáshoz történik-e, és hogy a privát végpont használatával csatlakozik-e.

Ha a keresési szolgáltatás végpontja privát, egyes portál-funkciók le vannak tiltva. Megtekintheti és kezelheti a szolgáltatási szint beállításait, de a portál hozzáférése az indexhez és a szolgáltatás különböző egyéb összetevőihez, például az index, az indexelő és a készségkészlet-definíciók biztonsági okokból korlátozottak.

1. A *myVM*távoli asztal nyissa meg a PowerShellt.

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

1. A virtuális gépről kapcsolódjon a keresési szolgáltatáshoz, és hozzon létre egy indexet. [Ezt a](search-get-started-postman.md) rövid útmutatót követve létrehozhat egy új keresési indexet a Poster szolgáltatásban a REST API használatával. A Poster-kérelmek beállítása a keresési szolgáltatás végpontját (https://[Search szolgáltatás neve]. Search. Windows. net) és a felügyeleti API-kulcsot, amelyet az előző lépésben másolt.

1. A virtuális gép gyors üzembe helyezésének befejezésével megerősíti, hogy a szolgáltatás teljesen működőképes.

1. A távoli asztali kapcsolat bezárásával *myVM*. 

1. Annak ellenőrzéséhez, hogy a szolgáltatás nem érhető el nyilvános végponton, nyissa meg a Poster szolgáltatást a helyi munkaállomáson, és próbálja meg a gyors útmutató első több feladatát. Ha hibaüzenet jelenik meg arról, hogy a távoli kiszolgáló nem létezik, sikeresen konfigurált egy magánhálózati végpontot a keresési szolgáltatáshoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha végzett a privát végpont, a Search szolgáltatás és a virtuális gép használatával, törölje az erőforráscsoportot és a benne lévő összes erőforrást:
1. Adja meg a *myResourceGroup*a   portál tetején található **keresőmezőbe** , és válassza a *myResourceGroup*lehetőséget   a keresési eredmények közül. 
1. Válassza az **Erőforráscsoport törlése** elemet. 
1. Írja be a *myResourceGroup*   **nevet az erőforráscsoport neveként** , majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozott egy virtuális GÉPET egy virtuális hálózaton és egy keresési szolgáltatást egy privát végponttal. Az internetről csatlakozik a virtuális géphez, és biztonságosan kommunikál a keresési szolgáltatással a privát hivatkozás használatával. További információ a privát végpontról: [Mi az az Azure Private Endpoint?](../private-link/private-endpoint-overview.md).
