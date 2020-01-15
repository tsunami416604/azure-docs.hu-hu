---
title: Privát végpont létrehozása biztonságos kapcsolatok létrehozásához
titleSuffix: Azure Cognitive Search
description: Magánhálózati végpont beállítása egy virtuális hálózatban az Azure Cognitive Search szolgáltatással való biztonságos kapcsolódáshoz
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 2664b1abd4131cf1dca186c7b044e338bf1efa84
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945820"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search-preview"></a>Hozzon létre egy privát végpontot biztonságos kapcsolódáshoz az Azure Cognitive Searchhoz (előzetes verzió)

Ebben a cikkben a portál használatával hozzon létre egy új Azure Cognitive Search Service-példányt, amely nem érhető el nyilvános IP-címen keresztül. Ezután állítson be egy Azure-beli virtuális gépet ugyanabban a virtuális hálózatban, és használja azt a keresési szolgáltatás privát végponton keresztüli eléréséhez.

> [!Important]
> A privát végpontok támogatása az Azure Cognitive Search [számára korlátozott](https://aka.ms/SearchPrivateLinkRequestAccess) hozzáférésű előzetes verzióként érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlottak. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
>
> Miután hozzáférést kapott az előzetes verzióhoz, a Azure Portal vagy a [felügyeleti REST API 2019-10-06-Preview verziójának](https://docs.microsoft.com/rest/api/searchmanagement/)használatával konfigurálhatja a szolgáltatáshoz tartozó privát végpontokat.
>   

## <a name="why-use-private-endpoint-for-secure-access"></a>Miért érdemes privát végpontot használni a biztonságos hozzáféréshez?

Az Azure Cognitive Search [magánhálózati végpontok](../private-link/private-endpoint-overview.md) lehetővé teszik, hogy egy virtuális hálózaton lévő ügyfél biztonságosan hozzáférhessen egy keresési indexben lévő adathoz egy [privát hivatkozáson](../private-link/private-link-overview.md)keresztül. A privát végpont egy IP-címet használ a keresési szolgáltatáshoz tartozó [virtuális hálózati címtartomány](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) alapján. Az ügyfél és a keresési szolgáltatás közötti hálózati forgalom áthalad a virtuális hálózaton és a Microsoft gerinc hálózatán található privát kapcsolaton, ami kiküszöböli a nyilvános internetről való kitettséget. A privát hivatkozást támogató egyéb Pásti-szolgáltatások listájáért tekintse meg a termék dokumentációjának [rendelkezésre állási szakaszát](../private-link/private-link-overview.md#availability) .

A keresési szolgáltatás privát végpontjai a következőket teszik lehetővé:

- A keresési szolgáltatás nyilvános végpontján lévő összes kapcsolat blokkolása.
- Növelje a virtuális hálózat biztonságát azáltal, hogy letiltja a virtuális hálózat kiszűrése.
- Biztonságosan csatlakozhat a keresési szolgáltatáshoz olyan helyszíni hálózatokról, amelyek VPN-vagy [Expressroute](../expressroute/expressroute-locations.md) [-](../vpn-gateway/vpn-gateway-about-vpngateways.md) kapcsolaton keresztül csatlakoznak a virtuális hálózathoz.

> [!NOTE]
> Az előzetes verzióban jelenleg bizonyos korlátozások vonatkoznak a következőkre:
> * Csak **az alapszintű** keresési szolgáltatásokhoz érhető el. 
> * Elérhető az USA 2. nyugati régiójában, az USA nyugati középső régiójában, az USA keleti régiójában, az USA déli középső régiójában, Kelet-Ausztrália és Délkelet-ausztráliai régióban.
> * Ha a szolgáltatás végpontja privát, egyes portál-funkciók le vannak tiltva. Megtekintheti és kezelheti a szolgáltatási szint adatait, de a portál hozzáférése az adatok indexeléséhez és a szolgáltatás különböző összetevőihez, például az index, az indexelő és a készségkészlet-definíciók biztonsági okokból korlátozottak.
> * Ha a szolgáltatás végpontja privát, a [keresés REST API](https://docs.microsoft.com/rest/api/searchservice/) használatával kell feltöltenie a dokumentumokat az indexbe.
> * A következő hivatkozásra kattintva tekintheti meg a privát végpontok támogatási lehetőségét a Azure Portalban: https://portal.azure.com/?feature.enablePrivateEndpoints=true



## <a name="request-access"></a>Hozzáférés kérése 

Az előzetes verzióra való feliratkozáshoz kattintson a [hozzáférés kérése](https://aka.ms/SearchPrivateLinkRequestAccess) lehetőségre. Az űrlap adatokat kér az Ön, a vállalat és az általános hálózati topológiáról. Ha áttekintjük a kérést, további utasításokkal megerősítő e-mailt fog kapni.

## <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Ebben a szakaszban létre fog hozni egy virtuális hálózatot és alhálózatot a keresési szolgáltatás privát végpontjának eléréséhez használni kívánt virtuális gép üzemeltetéséhez.

1. A Azure Portal Kezdőlap lapon válassza az **erőforrás létrehozása** > **hálózatkezelés** > **virtuális hálózat**lehetőséget.

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


## <a name="create-a-search-service-with-a-private-endpoint"></a>Keresési szolgáltatás létrehozása privát végponttal

Ebben a szakaszban egy új Azure Cognitive Search szolgáltatást fog létrehozni egy privát végponttal. 

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása** > **webes** > **Azure Cognitive Search**lehetőséget.

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

1. Miután befejeződött az új szolgáltatás üzembe helyezése, keresse meg az imént létrehozott erőforrást.

1. A bal oldali tartalom menüben válassza a **kulcsok** lehetőséget.

1. Másolja az **elsődleges rendszergazdai kulcsot** később, amikor csatlakozik a szolgáltatáshoz.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása** > **számítási** > **virtuális gép**lehetőséget.

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
    | Nyilvános bejövő portok | Hagyja meg az alapértelmezett **engedélyezett portok engedélyezése beállítást**. |
    | Bejövő portok kiválasztása | Hagyja meg az alapértelmezett **RDP-t (3389)** . |
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


## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Töltse le, majd kapcsolódjon a virtuális gép *myVm* a következőképpen:

1. A portál keresési sávján adja meg a *myVm*.

1. Kattintson a **Csatlakozás** gombra. A **Kapcsolódás** gombra kattintva megnyílik a **virtuális géphez való kapcsolódás** .

1. Válassza az **RDP-fájl letöltése**lehetőséget. Az Azure létrehoz egy RDP protokoll ( *. rdp*) fájlt, és letölti a számítógépre.

1. Nyissa meg a letöltött. rdp fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához **több választási lehetőséget** kell kiválasztania > **eltérő fiókot használjon**.

1. Kattintson az **OK** gombra.

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás**lehetőséget.

1. Ha megjelenik a virtuális gép asztala, csökkentse a helyi asztalra való visszatérést.  


## <a name="test-connections"></a>Kapcsolatok tesztelése

Ebben a szakaszban ellenőrzi, hogy a magánhálózati hozzáférés a keresési szolgáltatáshoz történik-e, és hogy a privát végpont használatával csatlakozik-e.

Ha a keresési szolgáltatással folytatott összes interakciót igényli, a [keresési Rest APIt](https://docs.microsoft.com/rest/api/searchservice/)kell visszahívnia. Ez az előzetes verzió nem támogatja a portált és a .NET SDK-t.

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

1. A virtuális gépről kapcsolódjon a keresési szolgáltatáshoz, és hozzon létre egy indexet. [Ezt a](search-get-started-postman.md) rövid útmutatót követve létrehozhat egy új keresési indexet a Poster szolgáltatásban a REST API használatával. A Poster-kérelmek beállítása a keresési szolgáltatás végpontját (https://[Search szolgáltatás neve]. Search. Windows. net) és a felügyeleti API-kulcsot, amelyet az előző lépésben másolt.

1. A virtuális gép gyors üzembe helyezésének befejezésével megerősíti, hogy a szolgáltatás teljesen működőképes.

1. A távoli asztali kapcsolat bezárásával *myVM*. 

1. Annak ellenőrzéséhez, hogy a szolgáltatás nem érhető el nyilvános végponton, nyissa meg a Poster szolgáltatást a helyi munkaállomáson, és próbálja meg a gyors útmutató első több feladatát. Ha hibaüzenet jelenik meg arról, hogy a távoli kiszolgáló nem létezik, sikeresen konfigurált egy magánhálózati végpontot a keresési szolgáltatáshoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha végzett a privát végpont, a Search szolgáltatás és a virtuális gép használatával, törölje az erőforráscsoportot és a benne lévő összes erőforrást:
1. Adja meg a *myResourceGroup* a portál tetején található **keresőmezőbe** , és válassza ki a *myResourceGroup* a keresési eredmények közül. 
1. Válassza az **Erőforráscsoport törlése** elemet. 
1. Adja meg a *myResourceGroup* az **erőforráscsoport nevének** beírásához, majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben létrehozott egy virtuális GÉPET egy virtuális hálózaton és egy keresési szolgáltatást egy privát végponttal. Az internetről csatlakozik a virtuális géphez, és biztonságosan kommunikál a keresési szolgáltatással a privát hivatkozás használatával. További információ a privát végpontról: [Mi az az Azure Private Endpoint?](../private-link/private-endpoint-overview.md).
