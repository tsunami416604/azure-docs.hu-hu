---
title: Privát végpont létrehozása biztonságos kapcsolathoz
titleSuffix: Azure Cognitive Search
description: Privát végpont beállítása virtuális hálózatban az Azure Cognitive Search szolgáltatással való biztonságos kapcsolat érdekében
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 2664b1abd4131cf1dca186c7b044e338bf1efa84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945820"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search-preview"></a>Privát végpont létrehozása az Azure Cognitive Search (előzetes verzió) biztonságos kapcsolatához

Ebben a cikkben a portál használatával hozzon létre egy új Azure Cognitive Search szolgáltatáspéldány, amely nem érhető el egy nyilvános IP-cím. Ezután konfiguráljon egy Azure virtuális gépet ugyanabban a virtuális hálózatban, és használja a keresési szolgáltatás eléréséhez egy privát végponton keresztül.

> [!Important]
> Az Azure Cognitive Search privát végponttámogatása [kérésre](https://aka.ms/SearchPrivateLinkRequestAccess) korlátozott hozzáférésű előzetes verzióként érhető el. Az előzetes verziójú funkciók szolgáltatásiszint-szerződés nélkül vannak biztosítva, és éles számítási feladatokhoz nem ajánlottak. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 
>
> Miután hozzáférést kapott az előzetes verzióhoz, konfigurálhatja a szolgáltatás privát végpontjait az Azure Portalon vagy a [Management REST API 2019-10-06-preview-verzióján](https://docs.microsoft.com/rest/api/searchmanagement/)keresztül.
>   

## <a name="why-use-private-endpoint-for-secure-access"></a>Miért érdemes a Privát végpontot használni a biztonságos hozzáféréshez?

Az Azure Cognitive Search [privát végpontjai](../private-link/private-endpoint-overview.md) lehetővé teszik, hogy a virtuális hálózaton lévő ügyfél biztonságosan hozzáférjen a keresési indexben lévő adatokhoz [egy privát kapcsolaton](../private-link/private-link-overview.md)keresztül. A privát végpont a keresési szolgáltatás [virtuális hálózati címteréből](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) származó IP-címet használ. Az ügyfél és a keresési szolgáltatás közötti hálózati forgalom áthalad a virtuális hálózaton és a Microsoft gerinchálózatán lévő privát kapcsolaton, így kiküszöböli a nyilvános internetről érkező expozíciót. A Private Link et támogató egyéb PaaS-szolgáltatások listáját a termék dokumentációjában található [elérhetőségi szakaszban](../private-link/private-link-overview.md#availability) találja.

A keresési szolgáltatás privát végpontjai lehetővé teszik a következőket:

- A keresési szolgáltatás nyilvános végpontján lévő összes kapcsolat letiltása.
- Növelje a virtuális hálózat biztonságát azáltal, hogy lehetővé teszi az adatok kiszivárgásának letiltását a virtuális hálózatról.
- Biztonságosan csatlakozhat a keresési szolgáltatáshoz olyan helyszíni hálózatokról, amelyek [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy [ExpressRoutes](../expressroute/expressroute-locations.md) használatával csatlakoznak a virtuális hálózathoz, privát társviszony-létesítéssel.

> [!NOTE]
> Az előzetes verzióban jelenleg vannak olyan korlátozások, amelyeket tudnia kell:
> * Csak az **alapszintű** keresési szolgáltatásokhoz érhető el. 
> * Elérhető az USA nyugati régiójában 2, az USA nyugati középső régiójában, az USA keleti régiójában, az USA déli középső régiójában, Ausztrália keleti részén és Ausztrália délkeleti régióiban.
> * Ha a szolgáltatásvégpont privát, egyes portálfunkciók le vannak tiltva. A szolgáltatásszintű adatok at megtekintheti és kezelheti, de biztonsági okokból a portálhozzáférés az indexadatokhoz és a szolgáltatás különböző összetevőihez, például az indexhez, az indexelőhöz és a skillset definíciókhoz korlátozott.
> * Ha a szolgáltatás végpontja privát, a [Search REST API-t](https://docs.microsoft.com/rest/api/searchservice/) kell használnia a dokumentumok indexbe való feltöltéséhez.
> * Az alábbi hivatkozást kell használnia a privát végpont támogatási lehetőségének megtekintéséhez az Azure Portalon:https://portal.azure.com/?feature.enablePrivateEndpoints=true



## <a name="request-access"></a>Hozzáférés kérése 

Kattintson [a hozzáférés kérése](https://aka.ms/SearchPrivateLinkRequestAccess) gombra, ha regisztrálhat erre az előnézeti funkcióra. Az űrlap adatokat kér Önről, a vállalatról és az általános hálózati topológiáról. Miután áttekintettük a kérését, egy visszaigazoló e-mailt kap további utasításokkal.

## <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Ebben a szakaszban egy virtuális hálózatot és alhálózatot hoz létre a virtuális gép üzemeltetéséhez, amely a keresési szolgáltatás privát végpontjának eléréséhez lesz használva.

1. Az Azure Portal kezdőlapja lapon válassza **az Erőforrás** > **létrehozása virtuális** > **hálózat**létrehozása lehetőséget.

1. A **Virtuális hálózat létrehozása**mezőbe írja be vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg *a MyVirtualNetwork hálózatot* |
    | Címtér | Adja meg *a 10.1.0.0/16 értéket* |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza **az Új létrehozása**lehetőséget, írja be a *myResourceGroup parancsot,* majd kattintson az OK gombra. **OK** |
    | Hely | Válassza ki **az USA nyugati régióját** vagy a használt régiót|
    | Alhálózat - név | Adja meg *a mySubnet-et* |
    | Alhálózat – címtartomány | Adja meg *a 10.1.0.0/24 értéket* |
    |||

1. Hagyja a többit alapértelmezettként, és válassza a **Létrehozás lehetőséget.**


## <a name="create-a-search-service-with-a-private-endpoint"></a>Keresési szolgáltatás létrehozása saját végponttal

Ebben a szakaszban egy új Azure Cognitive Search szolgáltatást hoz létre egy privát végpontdal. 

1. Az Azure Portal képernyőjének bal felső részén válassza az > Erőforrás létrehozása**a Webes** > **Azure kognitív keresés ének** **lehetőséget.**

1. Az **Új keresési szolgáltatás – Alapismeretek**mezőbe írja be vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | **A PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **PÉLDÁNY részletei** |  |
    | URL-cím | Adjon meg egy egyedi nevet. |
    | Hely | Válassza ki azt a régiót, amelyet akkor adott meg, amikor hozzáférést kért ehhez az előnézeti funkcióhoz. |
    | Tarifacsomag | Válassza **a Tarifacsomag módosítása és** az **Alapszintű**lehetőséget. Ez a szint szükséges az előzetes verzióhoz. |
    |||
  
1. Válassza a **Tovább lehetőséget: Méretezés**.

1. Hagyja az értékeket alapértelmezettként, és válassza **a Tovább: Hálózat lehetőséget.**

1. Az **Új keresési szolgáltatás – Hálózat területen**válassza a **Privát** lehetőséget a **végponti kapcsolathoz(adatokhoz)** lehetőséget.

1. Az **Új keresési szolgáltatás – Hálózat területen**válassza a + Add **(Hozzáadás)** lehetőséget a **Privát végpont**csoportban. 

1. A **Saját végfelhasználói végpont létrehozása**mezőbe írja be vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    | Hely | Válassza a **Nyugat-USA lehetőséget.**|
    | Név | Adja meg *a myPrivateEndpoint*.  |
    | Cél alerőforrás | Hagyja meg az alapértelmezett **searchService szolgáltatást.** |
    | **Hálózati** |  |
    | Virtuális hálózat  | Válassza a *MyVirtualNetwork* elemet a *myResourceGroup*erőforráscsoportból. |
    | Alhálózat | Válassza a *mySubnet*lehetőséget . |
    | **PRIVÁT DNS-INTEGRÁCIÓ** |  |
    | Integrálás privát DNS-zónával  | Hagyja meg az alapértelmezett **Igen**. |
    | Privát DNS-zóna  | Hagyja meg az alapértelmezett ** (Új) privatelink.search.windows.net**. |
    |||

1. Válassza **az OK gombot.** 

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A véleményezés + **létrehozás** lap, ahol az Azure érvényesíti a konfigurációt. 

1. Amikor megjelenik az **Érvényesítési átadott** üzenet, válassza a **Létrehozás gombot.** 

1. Az új szolgáltatás üzembe helyezését követően keresse meg az imént létrehozott erőforrást.

1. Válassza a bal oldali tartalom menü **Billentyűparancs parancsát.**

1. Másolja az **elsődleges rendszergazdai kulcsot** későbbre, amikor a szolgáltatáshoz csatlakozik.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Az Azure Portal képernyőjének bal felső részén válassza az**Compute** >  **Erőforrás** > létrehozása**virtuális gép**létrehozása lehetőséget.

1. A **Virtuális gép létrehozása - Alapjai**csoportban adja meg vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | **A PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.  |
    | **PÉLDÁNY részletei** |  |
    | Virtuális gép neve | Adja meg *a myVm*- |
    | Régió | Válassza ki **az USA nyugati régióját** vagy a használt régiót. |
    | Rendelkezésre állási beállítások | Hagyja meg az alapértelmezett **Nincs szükség infrastruktúra-redundanciára**. |
    | Kép | Válassza a **Windows Server 2019 Datacenter lehetőséget.** |
    | Méret | Hagyja meg az alapértelmezett **Standard DS1 v2 -t.** |
    | **RENDSZERGAZDAI FIÓK** |  |
    | Felhasználónév | Adja meg az Ön által választott felhasználónevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Írja be újra a jelszót. |
    | **BEJÖVŐ PORTSZABÁLYOK** |  |
    | Nyilvános bejövő portok | Hagyja meg az alapértelmezett **Kijelölt portok engedélyezése**lehetőséget . |
    | Bejövő portok kiválasztása | Hagyja meg az alapértelmezett **RDP -t (3389)**. |
    | **TAKARÍTSON MEG PÉNZT** |  |
    | Már van Windows-licence? | Hagyja meg az alapértelmezett **Nem értéket.** |
    |||

1. Válassza a **Tovább: Lemezek**lehetőséget.

1. A **Virtuális gép létrehozása – Lemezek**csoportban hagyja meg az alapértelmezett beállításokat, és válassza a **Tovább: Hálózat lehetőséget.**

1. A **Virtuális gép létrehozása - Hálózat csoportban**válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Virtuális hálózat | Hagyja meg az alapértelmezett **MyVirtualNetwork**.  |
    | Címtér | Hagyja meg az alapértelmezett **10.1.0.0/24**értéket.|
    | Alhálózat | Hagyja meg az alapértelmezett **mySubnet (10.1.0.0/24)**.|
    | Nyilvános IP-cím | Hagyja meg az alapértelmezett **(új) myVm-ip**. |
    | Nyilvános bejövő portok | Válassza **a Kijelölt portok engedélyezése**lehetőséget. |
    | Bejövő portok kiválasztása | Válassza a **HTTP** és **az RDP**lehetőséget.|
    ||

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A véleményezés + **létrehozás** lap, ahol az Azure érvényesíti a konfigurációt.

1. Amikor megjelenik az **Érvényesítési átadott** üzenet, válassza a **Létrehozás gombot.** 


## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Töltse le, majd csatlakozzon a VM *myVm-hez* az alábbiak szerint:

1. A portál keresősávjában írja be a *myVm*.

1. Kattintson a **Csatlakozás** gombra. A **Csatlakozás** gomb kiválasztása után megnyílik **a Csatlakozás a virtuális géphez.**

1. Válassza **az RDP-fájl letöltése lehetőséget.** Az Azure létrehoz egy Remote Desktop Protocol (*.rdp*) fájlt, és letölti azt a számítógépre.

1. Nyissa meg a downloaded.rdp* fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy **további lehetőségek közül** > **választhat: Másik fiók használatával**adja meg a virtuális gép létrehozásakor megadott hitelesítő adatokat.

1. Válassza **az OK gombot.**

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha tanúsítványfigyelmeztetést kap, válassza az **Igen** vagy **a Folytatás**lehetőséget.

1. Miután megjelenik a virtuális gép asztala, minimalizálja azt, hogy visszatérjen a helyi asztalra.  


## <a name="test-connections"></a>Kapcsolatok tesztelése

Ebben a szakaszban ellenőrizni fogja a privát hálózati hozzáférést a keresési szolgáltatáshoz, és privát módon csatlakozik a privát végpont használatával.

Visszahívás a bevezetésből, hogy a keresési szolgáltatással folytatott összes interakcióhoz szükség van a [Search REST API-ra.](https://docs.microsoft.com/rest/api/searchservice/) A portál és a .NET SDK nem támogatott ebben az előzetes verzióban.

1. A *myVM*távoli asztalán nyissa meg a PowerShellt.

1. Írja be az 'nslookup [search service name].search.windows.net" kifejezést.

    A következőhöz hasonló üzenetet fog kapni:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. A virtuális gép, csatlakozzon a keresési szolgáltatáshoz, és hozzon létre egy indexet. Ezt a [rövid útmutatót](search-get-started-postman.md) követve új keresési indexet hozhat létre a szolgáltatásban a Postmanben a REST API használatával. A Postman-től érkező kérelmek beállításához a keresési szolgáltatás végpontjára (https://[[search service name].search.windows.net) és az előző lépésben másolt rendszergazdai api-kulcsra van szükség.

1. A virtuális gép ről a rövid útmutató befejezése a megerősítést, hogy a szolgáltatás teljes mértékben működőképes.

1. Zárja be a távoli asztali kapcsolatot a *myVM-mel.* 

1. Annak ellenőrzéséhez, hogy a szolgáltatás nem érhető el nyilvános végponton, nyissa meg a Postman a helyi munkaállomáson, és próbálja meg az első néhány feladatot a rövid útmutatóban. Ha hibaüzenetet kap arról, hogy a távoli kiszolgáló nem létezik, sikeresen konfigurált egy privát végpontot a keresési szolgáltatáshoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha befejezte a privát végpont, a keresési szolgáltatás és a virtuális gép használatát, törölje az erőforráscsoportot és az összes benne lévő erőforrást:
1. Írja be a *myResourceGroup* kifejezést a portál tetején lévő **Keresőmezőbe,** és válassza a *myResourceGroup* elemet a keresési eredmények közül. 
1. Válassza az **Erőforráscsoport törlése** elemet. 
1. Írja be a *myResourceGroup értéket*  **az ERŐFORRÁSCSOPORT NEVÉNEK BEÍRÁSához,** és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozott egy virtuális gépet egy virtuális hálózaton, és egy keresési szolgáltatást egy privát végpontdal. Ön az internetről csatlakozott a virtuális géphez, és biztonságosan kommunikált a keresési szolgáltatással a Privát hivatkozás használatával. Ha többet szeretne megtudni a Privát végpontról, olvassa el [a Mi az Azure-beli privát végpont?](../private-link/private-endpoint-overview.md).
