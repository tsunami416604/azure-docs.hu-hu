---
title: "Létrehozása, módosítása vagy törlése az Azure virtuális hálózat |} Microsoft Docs"
description: "Megtudhatja, hogyan létrehozása, módosítása vagy törlése az Azure virtuális hálózat."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.openlocfilehash: 6a4a4d29cbfa8a695ed1ad3f007e6ff3e859bda4
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="create-change-or-delete-a-virtual-network"></a>Létrehozása, módosítása vagy a virtuális hálózat törlése

Megtudhatja, hogyan létrehozása és egy virtuális hálózat törlése és módosítása céljából beállításaihoz, például a DNS-kiszolgálók és az IP-címterek, meglévő virtuális hálózat.

Egy virtuális hálózat a felhőben saját hálózati ábrázolása. A virtuális hálózat, amely az Azure-előfizetéshez Azure-felhő logikai elkülönítése. Minden virtuális hálózathoz, az Ön által létrehozott a következő műveletek végezhetők el:
- Válassza ki a címteret hozzárendelni. Egy vagy több címtartományai Classless Inter-Domain Routing (CIDR) jelölésrendszer, például a 10.0.0.0/16 által meghatározott egy áll.
- Válassza ki az Azure által biztosított DNS-kiszolgáló használatára, vagy a saját DNS-kiszolgáló használatára. A virtuális hálózathoz csatlakozó összes erőforrást a DNS-kiszolgáló feloldani a virtuális hálózaton belül vannak hozzárendelve.
- A virtuális hálózati szegmenseket alhálózatokra külön-külön címtartományt, a virtuális hálózat a címtér. Létrehozása, módosítása és alhálózatok törlése, lásd: [hozzáadása, módosítása vagy törlése alhálózatok](virtual-network-manage-subnet.md).

Ez a cikk azt ismerteti, hogyan létrehozása, módosítása és törlése a virtuális hálózatok az Azure Resource Manager telepítési modell használatával.

## <a name="before"></a>Előkészületek

Az ebben a cikkben ismertetett feladatok megkezdése előtt hajtsa végre a következő előfeltételek teljesülését:

- Ha most ismerkedik a virtuális hálózatok használata, azt javasoljuk, hogy tekintse át a gyakorlatban [az első Azure virtuális hálózat létrehozása](quick-create-portal.md). Ebben a gyakorlatban segítségével Megismerkedés a virtuális hálózatok.
- Virtuális hálózatok korlátairól kapcsolatos információkért tekintse át a [Azure korlátozását](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Jelentkezzen be az Azure-portálon, az Azure parancssori eszköz (Azure CLI) vagy az Azure PowerShell használatával az Azure-fiókjával. Ha nem rendelkezik Azure-fiókja, regisztráljon egy [ingyenes próbafiók](https://azure.microsoft.com/free).
- Ha azt tervezi, a jelen cikkben ismertetett feladatok végrehajtásához PowerShell-parancsok használata, először [Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Győződjön meg arról, hogy rendelkezik-e a legújabb verziója található az Azure PowerShell-parancsmagjai telepítve vannak-e. A példákban PowerShell-parancsok súgójának megjelenítéséhez írja be a következőt `get-help <command> -full`.
- Ha azt tervezi, a jelen cikkben ismertetett feladatok végrehajtásához Azure parancssori felület parancsai használni, először [telepítése és konfigurálása az Azure parancssori felület](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Győződjön meg arról, hogy rendelkezik-e telepítve az Azure parancssori felület legújabb verziója. Ha segítséget szeretne kérni az Azure parancssori felület parancsait, adja meg a `az <command> --help`.


## <a name="create-vnet"></a>Virtuális hálózat létrehozása

Virtuális hálózat létrehozása:

1. Jelentkezzen be a [portal](https://portal.azure.com) egy olyan fiókkal, amely hozzá van rendelve a hálózat közreműködő szerepkört az előfizetés (minimum) engedélyeit. Szerepkörök és engedélyek hozzárendelése a fiókok kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Kattintson a **új** > **hálózati** > **virtuális hálózati**.
3. Az a **virtuális hálózati** panelen, a a **telepítési modell kiválasztása** mezőben hagyja **erőforrás-kezelő** kiválasztva, és kattintson **létrehozása**.
4. Az a **virtuális hálózat létrehozása** panelen adja meg vagy válassza ki a következő beállítások értékeit, majd kattintson **létrehozása**:
    - **Név**: A névnek egyedinek kell lennie a [erőforráscsoport](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) válassza ki a virtuális hálózat létrehozásához. A név nem módosítható, a virtuális hálózat létrejötte után. Több virtuális hálózat adott idő alatt is létrehozhat. A elnevezésére vonatkozó javaslatokat, lásd: [elnevezési konvenciói](/azure/architecture/best-practices/naming-conventions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). Következő elnevezési segítségével könnyebben több virtuális hálózat kezeléséhez.
    - **Címtér**: Adja meg a címtartomány CIDR-formátumban. A címterület adhat meg a public vagy private (az RFC 1918) lehet. A címtartomány nyilvános vagy titkos határozza meg, hogy a címtartomány érhető el csak a virtuális hálózathoz csatlakozó virtuális hálózatot, és a helyszíni hálózatokhoz a virtuális hálózathoz csatlakozó belül. Nem adható hozzá a következő címterek:
        - 224.0.0.0/4 (csoportos küldés)
        - 255.255.255.255/32 (közvetítés)
        - 127.0.0.0/8 (visszacsatolás)
        - 169.254.0.0/16 (kapcsolatszintű)
        - 168.63.129.16/32 (belső DNS)

      Bár csak egy címtartomány a virtuális hálózat létrehozásakor definiálhat, további címterületeket a virtuális hálózat létrejötte után adhat hozzá. Egy meglévő virtuális hálózat hozzáadása, lásd: [hozzáadása vagy eltávolítása egy](#add-address-spaces) ebben a cikkben.

      >[!WARNING]
      >Ha egy virtuális hálózati cím szóközt tartalmaz, amely egy másik virtuális hálózati átfedésben vagy a helyszíni hálózat, a két hálózat nem lehet csatlakozni. Definiálása előtt célszerű egy, fontolja meg, akkor előfordulhat, hogy kívánja-e csatlakozni a virtuális hálózat más virtuális hálózatok és a helyszíni hálózatokhoz a jövőben.
      >
      >

    - **Alhálózati név**: az alhálózat neve a virtuális hálózaton belül egyedinek kell lennie. Az alhálózat neve nem módosítható, miután az alhálózat létre van hozva. A portálhoz szükséges, hogy adhat meg egy alhálózatot egy virtuális hálózatban, létrehozásakor annak ellenére, hogy a virtuális hálózat nem rendelkezik alhálózatokkal szükséges. A portál csak egy alhálózat segítségével megadhatja, ha a virtuális hálózat létrehozása. Adhat hozzá további alhálózatokat a virtuális hálózati később, a virtuális hálózat létrejötte után. Adjon hozzá egy alhálózatot a virtuális hálózat, lásd: [hozzon létre egy alhálózatot](virtual-network-manage-subnet.md#create-subnet) a [létrehozása, módosítása vagy törlése alhálózatok](virtual-network-manage-subnet.md). Virtuális hálózat már több alhálózat működik az Azure parancssori felület vagy a PowerShell használatával is létrehozhat.

      >[!TIP]
      >Egyes esetekben rendszergazdák hozzon létre különböző alhálózatokon való vagy szabályozza a forgalom útválasztásához az alhálózatok között. Definiálása előtt célszerű alhálózatok, fontolja meg, hogyan lehet szűrni kívánt és irányíthatja a forgalmat az alhálózatok között. Az alhálózatok közötti forgalmat szűrő kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](virtual-networks-nsg.md). Az Azure automatikusan útvonalak forgalom között az alhálózatokat, de szeretné felülbírálni az Azure alapértelmezett útvonalak. További információt az Azure alapértelmezett alhálózati a forgalom útválasztásához, lásd: [felhasználó által definiált útvonalak](virtual-networks-udr-overview.md).
      >

    - **Alhálózati címtartományt**: a megadott virtuális hálózat a címtér kell lennie. A legkisebb megadható tartománya /29, biztosító nyolc IP-cím az alhálózat. Azure fenntartja az első és utolsó cím protokoll megfelelési minden alhálózatban. Három további címek az Azure szolgáltatás használati számára vannak fenntartva. Ennek eredményeképpen a virtuális hálózat /29 alhálózati cím tartománnyal csak három használható IP-címmel rendelkezik. Ha azt tervezi, VPN-átjáró egy virtuális hálózathoz csatlakozni, létre kell hoznia egy átjáró-alhálózatot. További információ [adott cím tartomány szempontjai átjáró alhálózatok](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). A címtartomány meghatározott feltételek mellett az alhálózat létrehozása után módosíthatja. A alhálózati címtartományt módosításáról további tudnivalókért lásd: [alhálózati beállítások módosítása](#change-subnet) a [hozzáadása, módosítása vagy törlése alhálózatok](virtual-network-manage-subnet.md).
    - **Előfizetés**: Válasszon egy [előfizetés](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Egynél több Azure-előfizetés nem használhatja ugyanazt a virtuális hálózatot. Azonban keresztül csatlakozhat egy előfizetésben található virtuális hálózat virtuális hálózatok más előfizetésekhez. Csatlakoztassa a virtuális hálózatok különböző előfizetésekhez, használja az Azure VPN Gateway vagy a virtuális hálózati társviszony-létesítés. Az Azure-erőforrásokkal, a virtuális hálózathoz csatlakozó virtuális hálózatnak ugyanahhoz az előfizetéshez kell lennie.
    - **Erőforráscsoport**: Válasszon ki egy létező [erőforráscsoport](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) vagy hozzon létre egy újat. Egy Azure-erőforrás a virtuális hálózathoz csatlakozó lehet, mint a virtuális hálózat ugyanabban az erőforráscsoportban vagy egy másik erőforráscsoportban található.
    - **Hely**: válassza ki az Azure [hely](https://azure.microsoft.com/regions/), más néven egy régiót. Virtuális hálózat csak egy Azure-beli hely lehet. Azonban csatlakozhat egy helyet a virtuális hálózat egy másik helyre a virtuális hálózat VPN-átjáró használatával. Az Azure-erőforrásokkal, a virtuális hálózathoz csatlakozó ugyanazon a helyen, ahol a virtuális hálózatot kell lennie.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|Azure CLI|[az hálózati virtuális hálózat létrehozása](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_vnet_create)|
|PowerShell|[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name = "view-vnet"></a>Virtuális hálózatok megjelenítése és beállítások

Virtuális hálózatok és beállítások megtekintéséhez:

1. Jelentkezzen be a [portal](https://portal.azure.com) egy olyan fiókkal, amely hozzá van rendelve a hálózat közreműködő szerepkört az előfizetés (minimum) engedélyeit. Szerepkörök és engedélyek hozzárendelése a fiókok kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. A portál keresési mezőbe, írja be a **virtuális hálózatok**. A keresési eredmények között kattintson **virtuális hálózatok**.
3. Az a **virtuális hálózatok** panelen kattintson a virtuális hálózatra, hogy meg szeretné tekinteni a beállításokat.
4. A panel a kiválasztott virtuális hálózat szerepel a következő beállításokat:
    - **Áttekintés**: információkat nyújt azokról a virtuális hálózat, beleértve a címterület és a DNS-kiszolgálók. Az alábbi képernyőfelvételen látható egy virtuális hálózat nevű áttekintése beállításainak **MyVNet**:

        ![Hálózati illesztő – áttekintés](./media/virtual-network-manage-network/vnet-overview.png)

      Az a **áttekintése** panelen áthelyezheti egy virtuális hálózat egy másik előfizetés vagy az erőforrás-csoporthoz. Hogyan kívánja áthelyezni a virtuális hálózat kapcsolatban [erőforrások áthelyezése egy másik erőforráscsoportban vagy előfizetés](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). A cikk felsorolja az előfeltételeket és erőforrások áthelyezése az Azure portál, a PowerShell és az Azure parancssori felület használatával. A virtuális hálózathoz csatlakozó összes erőforrás át kell helyezni a virtuális hálózathoz.
    - **Címtér**: A a virtuális hálózati hozzárendelt címterek vannak felsorolva. Megtudhatja, hogyan hozzá és távolíthat el egy címtartománnyal, hajtsa végre a lépéseket a [hozzáadása vagy eltávolítása egy](#address-spaces) ebben a cikkben.
    - **Csatlakoztatott eszközök**: minden olyan erőforrásnál, amely a virtuális hálózathoz kapcsolódó találhatók. Az előző képernyőképet három hálózati adapterrel és egy terheléselosztó a virtuális hálózathoz csatlakoznak. Új erőforrásokat hoz létre, és a virtuális hálózat jelennek meg. Ha töröl egy erőforrást, a virtuális hálózathoz csatlakozó, már nem szerepel a listában.
    - **Alhálózatok**: a virtuális hálózaton belül található alhálózatok listája látható. Rendszerdémon hozzáadása és eltávolítása az alhálózat, lásd: [hozzon létre egy alhálózatot](virtual-network-manage-subnet.md#create-subnet) és [alhálózat törlése](virtual-network-manage-subnet.md#delete-subnet) a [hozzáadása, módosítása vagy törlése alhálózatok](virtual-network-manage-subnet.md).
    - **DNS-kiszolgálók**: megadhatja, hogy az Azure belső DNS-kiszolgáló vagy egy egyéni DNS-kiszolgálót biztosít a névfeloldást a virtuális hálózathoz csatlakozó eszközöket. Ha virtuális hálózat létrehozása az Azure portál használatával, Azure DNS-kiszolgálókat használ a névfeloldáshoz, a virtuális hálózaton belül alapértelmezés szerint. A DNS-kiszolgálók módosításához végrehajtásához a [hozzáadása, módosítása vagy eltávolítása a DNS-kiszolgáló](#dns-servers) ebben a cikkben.
    - **Társviszony**: Ha nincsenek meglévő társviszony az előfizetést, akkor az itt felsorolt. Meglévő társviszony beállítások megtekintése vagy létrehozása, módosítása vagy törlése esetében. Társviszony kapcsolatos további információkért lásd: [virtuális hálózati társviszony-létesítés](virtual-network-peering-overview.md).
    - **Tulajdonságok**: a virtuális hálózat, beleértve a virtuális hálózati erőforrás-azonosító és az Azure-előfizetésre vonatkozó beállításokat jeleníti meg.
    - **Diagram**: A diagram összes eszközről, amelyen a virtuális hálózathoz kapcsolódó vizuális ábrázolását biztosítja. A diagram van néhány alapvető tudnivalók az eszközök. Ebben a nézetben a diagramon eszköz kezeléséhez kattintson arra az eszközre.
    - **Közös Azure beállításai**: közös Azure beállításaival kapcsolatos további tudnivalókért tekintse meg a következő információkat:
        *   [Tevékenységnapló](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
        *   [Hozzáférés-vezérlés (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
        *   [Címkék](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
        *   [Zárolások feloldása](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Automatizálási parancsfájl](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Parancsok**

|Eszköz|Parancs|
|---|---|
|Azure CLI|[az hálózati vnet megjelenítése](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_vnet_show)|
|PowerShell|[Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork/?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="add-address-spaces"></a>Hozzáadni vagy eltávolítani egy címtartománnyal

Adja hozzá, és távolítsa el a virtuális hálózat-címterét. Egy címterület CIDR-formátumban kell megadni, és nem lehet átfedésben más címterületeket a virtuális hálózaton belül. Megadhatja a címterek public vagy private (az RFC 1918) lehet. A címtartomány nyilvános vagy titkos határozza meg, hogy a címtartomány érhető el csak a virtuális hálózathoz csatlakozó virtuális hálózatot, és a helyszíni hálózatokhoz a virtuális hálózathoz csatlakozó belül. Nem adható hozzá a következő címterek:

- 224.0.0.0/4 (csoportos küldés)
- 255.255.255.255/32 (közvetítés)
- 127.0.0.0/8 (visszacsatolás)
- 169.254.0.0/16 (kapcsolatszintű)
- 168.63.129.16/32 (belső DNS)

Adja hozzá, vagy távolítsa el az-címtér:

1. Jelentkezzen be a [portal](https://portal.azure.com) egy olyan fiókkal, amely hozzá van rendelve a hálózat közreműködő szerepkört az előfizetés (minimum) engedélyeit. Szerepkörök és engedélyek hozzárendelése a fiókok kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. A portál keresési mezőbe, írja be a **virtuális hálózatok**. A keresési eredmények között, válassza ki a **virtuális hálózatok**.
3. Az a **virtuális hálózatok** panelen kattintson a virtuális hálózat legyen lehet hozzáadni vagy eltávolítani egy címtartománnyal.
4. A virtuális hálózati panelen a **beállítások**, kattintson a **Címtéren**.
5. A címterület paneljén hajtsa végre az alábbi lehetőségek közül:
    - **Egy címtartomány felvétele**: Adja meg az új címtartományt. A címtartomány a virtuális hálózathoz definiált meglévő címterület nem lehet átfedésben.
    - **Távolítsa el az címtéren**: kattintson a jobb gombbal a címteret, és kattintson **eltávolítása**. Ha alhálózat létezik-e a címterületen belülre, nem távolítható el a címterület. Egy eltávolításához először törölnie kell alhálózatok (és az alhálózatok kapcsolódó erőforrásokat), amely létezik a címterületen belülre.
6. Kattintson a **Save** (Mentés) gombra.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|Azure CLI|Csak az erőforrás-kezelő|[az hálózat virtuális hálózat frissítése](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_vnet_update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="dns-servers"></a>Hozzáadása, módosítása vagy eltávolítása a DNS-kiszolgáló

Minden virtuális gépek csatlakoznak-e a virtuális hálózati register állít be a virtuális hálózat DNS-kiszolgálókkal. Névfeloldás használata a megadott DNS-kiszolgáló is. Mindegyik hálózati interfész (NIC) a virtuális gép lehet a saját DNS-kiszolgáló beállításai. Ha egy hálózati Adaptert a saját DNS-kiszolgáló beállításait, a virtuális hálózat DNS-kiszolgáló beállításai felülbírálják a. A hálózati adapter DNS-beállítások kapcsolatos további információkért lásd: [hálózati illesztő feladatok és a beállítások](virtual-network-network-interface.md#change-dns-servers). További információt a névfeloldást a virtuális gépek és az Azure Felhőszolgáltatások szerepkörpéldányok, lásd: [névfeloldását virtuális gépek és a szerepkörpéldányok](virtual-networks-name-resolution-for-vms-and-role-instances.md). Hozzáadása, módosítása, vagy távolítsa el a DNS-kiszolgáló:

1. Jelentkezzen be a [portal](https://portal.azure.com) egy olyan fiókkal, amely hozzá van rendelve a hálózat közreműködő szerepkört az előfizetés (minimum) engedélyeit. Szerepkörök és engedélyek hozzárendelése a fiókok kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. A portál keresési mezőbe, írja be a **virtuális hálózatok**. A keresési eredmények között, válassza ki a **virtuális hálózatok**.
3. Az a **virtuális hálózatok** panelen kattintson a virtuális hálózat DNS-beállításait módosítani szeretné.
4. A virtuális hálózati panelen a **beállítások**, kattintson a **DNS-kiszolgálók**.
5. Válassza ki a DNS-kiszolgálók felsoroló panelen a következő lehetőségek közül:
    - **Alapértelmezett (Azure által biztosított)**: összes erőforrás nevét és privát IP-címek automatikusan regisztrálva van az Azure DNS-kiszolgálók. Feloldhatja a nevek közötti minden olyan erőforrásnál, amely ugyanahhoz a virtuális hálózathoz csatlakoznak. Virtuális hálózatok közötti névfeloldás Ez a beállítás nem használható. Névfeloldás a virtuális hálózatok közötti, egy egyéni DNS-kiszolgálót kell használnia.
    - **Egyéni**: legfeljebb a Azure virtuális hálózat egy vagy több kiszolgálót is hozzáadhat. DNS-kiszolgálói korlátok kapcsolatos további információkért lásd: [Azure korlátozását](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). A következő lehetőségek közül választhat:
        - **Adjon hozzá egy címet**: a kiszolgáló hozzáadása a virtuális hálózat DNS-kiszolgálók listájához. Ezt a lehetőséget is regisztrálja a DNS-kiszolgáló Azure. Ha már egy DNS-kiszolgáló már regisztrálva van az Azure, kiválaszthatja, hogy a DNS-kiszolgáló a listában.
        - **Távolítsa el az címet**: a kiszolgálót, amely el szeretné távolítani, mellett kattintson **X**. A kiszolgáló törlésével eltávolítja a kiszolgáló csak a virtuális hálózatok listája. A DNS-kiszolgáló használatára a virtuális hálózatok az Azure-ban regisztrált marad.
        - **DNS-kiszolgálócímek átrendezése**: fontos, hogy ellenőrizze, hogy a DNS-kiszolgálók, a környezetnek a megfelelő sorrendben felsorolja. DNS-kiszolgálók listáját akkor megadott sorrendben használhatók. A ciklikus multiplexelési telepítési nem működik. A listán az első DNS-kiszolgáló elérhető, ha az ügyfél használja a DNS-kiszolgáló, függetlenül attól, hogy a DNS-kiszolgáló megfelelően működik-e. Távolítson el minden felsorolt DNS-kiszolgálót, és adja őket újra a kívánt sorrendben.
        - **Módosítsa a címet**: Jelölje ki a DNS-kiszolgáló, a listában, és írja be az új nevet.
6. Kattintson a **Save** (Mentés) gombra.
7. Indítsa újra a virtuális gépek, amelyek a virtuális hálózathoz kapcsolódnak, hozzárendeli az új DNS-kiszolgáló beállításai. Virtuális gépek továbbra is az aktuális DNS-beállítások azok újraindításáig.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|Azure CLI|[az hálózat virtuális hálózat frissítése](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_vnet_update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-vnet"></a>A virtuális hálózat törlése

Virtuális hálózat csak akkor, ha nincsenek erőforrások csatlakozik, vagy törölheti. Ha nincsenek erőforrások csatlakoznak bármely alhálózatot a virtuális hálózaton belül, először törölnie kell az erőforrásokat, amelyek a virtuális hálózat összes alhálózatát csatlakoznak. Erőforrás törlése lépései eltérőek attól függően, hogy az erőforrás. Az alhálózatokhoz kapcsolódó erőforrások törlése, olvassa el minden erőforrástípus törli dokumentációját. A virtuális hálózat törlése:

1. Jelentkezzen be a [portal](https://portal.azure.com) egy olyan fiókkal, amely a hálózat közreműködő szerepkört az előfizetés (minimum) hozzárendelt engedélyeit. Szerepkörök és engedélyek hozzárendelése a fiókok kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. A portál keresési mezőbe, írja be a **virtuális hálózatok**. A keresési eredmények között kattintson **virtuális hálózatok**.
3. Az a **virtuális hálózatok** panelen válassza ki a törölni kívánt virtuális hálózatot.
4. A virtuális hálózat panel annak ellenőrzéséhez, hogy nincsenek-e eszközök a virtuális hálózathoz csatlakozik, a **beállítások**, kattintson a **csatlakoztatott eszközök**. Ha csatlakoztatott eszközön, törölnie kell azokat a virtuális hálózat törlése előtt. Ha nincsenek csatlakoztatott eszközök, kattintson a **áttekintése**.
5. Kattintson a panel tetején a **törlése** ikonra.
6. A virtuális hálózat a törlés megerősítéséhez kattintson **Igen**.


**Parancsok**

|Eszköz|Parancs|
|---|---|
|Azure CLI|[Azure-hálózat virtuális hálózat törlése](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_vnet_delete)|
|PowerShell|[Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>Következő lépések

- Hozzon létre egy virtuális Gépet, és csatlakoztassa a virtuális hálózat, [hozzon létre egy virtuális hálózatot, és csatlakozzon a virtuális gépek](quick-create-portal.md#create-virtual-machines).
- A virtuális hálózaton belül alhálózatok közötti hálózati forgalom szűrésére, lásd: [hálózati biztonsági csoportok létrehozása a](virtual-networks-create-nsg-arm-pportal.md).
- Egyenrangú egy virtuális hálózat egy másik virtuális hálózathoz, lásd: [hozzon létre egy virtuális hálózati társviszony-létesítés](virtual-network-create-peering.md#portal).
- Egy a helyszíni hálózathoz való csatlakozás a virtuális hálózati beállításokkal kapcsolatos további tudnivalókért lásd: [VPN-átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).
