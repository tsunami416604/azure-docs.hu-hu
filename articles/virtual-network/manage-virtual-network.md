---
title: Létrehozása, módosítása vagy törlése az Azure virtuális hálózat |} Microsoft Docs
description: Megtudhatja, hogyan létrehozása, módosítása vagy törlése az Azure virtuális hálózat.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 56839c38de135a805c51bb96ad5d7abc41ebcad7
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895373"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Létrehozása, módosítása vagy a virtuális hálózat törlése

Megtudhatja, hogyan létrehozása és egy virtuális hálózat törlése és módosítása céljából beállításaihoz, például a DNS-kiszolgálók és az IP-címterek, meglévő virtuális hálózat. Ha most ismerkedik a virtuális hálózatok, többet is megtudhat a rájuk vonatkozó a [virtuális hálózat áttekintése](virtual-networks-overview.md) vagy; Ehhez hajtsa végre a [oktatóanyag](quick-create-portal.md). A virtuális hálózati alhálózatot tartalmaz. Létrehozása, módosítása és alhálózatok törlése, lásd: [alhálózatok kezelése](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bármely szakaszának lépéseit befejezése előtt hajtsa végre a következőket:

- Ha még nem rendelkezik Azure-fiókja, regisztráljon egy [ingyenes próbafiók](https://azure.microsoft.com/free).
- A portál használatával, nyissa meg a https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsokkal ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/powershell), vagy a PowerShell futtatásával a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ebben az oktatóanyagban az Azure PowerShell modul verziója 5.7.0 szükséges vagy újabb. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Azure parancssori felület (CLI) parancsok használata ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/bash), vagy a CLI-t a számítógépen való futtatásával. Ez az oktatóanyag az Azure parancssori felület 2.0.31 verziója szükséges, vagy később. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure parancssori felület, is futtatásához szükséges `az login` az Azure VPN-kapcsolat létrehozásához.
- Hozzá kell rendelni a fiókot, jelentkezzen be, vagy csatlakozzon az Azure-ba, a [hálózat közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepel a megfelelő műveleteket rendelt [engedélyek ](#permissions).

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Válassza ki **+ hozzon létre egy erőforrást** > **hálózati** > **virtuális hálózati**.
2. Adja meg vagy válassza ki a következő beállítások értékeit, majd válassza **létrehozása**:
    - **Név**: A névnek egyedinek kell lennie a [erőforráscsoport](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) válassza ki a virtuális hálózat létrehozásához. A név nem módosítható, a virtuális hálózat létrejötte után. Több virtuális hálózat adott idő alatt is létrehozhat. A elnevezésére vonatkozó javaslatokat, lásd: [elnevezési konvenciói](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). Következő elnevezési segítségével könnyebben több virtuális hálózat kezeléséhez.
    - **Címtér**: A címtartomány a virtuális hálózat egy vagy több egymást nem átfedő címtartomány CIDR-formátumban megadott áll. A címtartományt, megadhatja a public vagy private (az RFC 1918) lehet. A címtartomány nyilvános vagy titkos határozza meg, hogy a címtartomány elérhető csak a virtuális hálózathoz csatlakozó virtuális hálózatot, és a helyszíni hálózatokhoz a virtuális hálózathoz csatlakozó belül. Nem adható hozzá a következő címtartományokat:
        - 224.0.0.0/4 (csoportos küldés)
        - 255.255.255.255/32 (közvetítés)
        - 127.0.0.0/8 (visszacsatolás)
        - 169.254.0.0/16 (kapcsolatszintű)
        - 168.63.129.16/32 (belső DNS)

      Bár csak egy címtartomány a virtuális hálózat létrehozásakor definiálhat, adhat hozzá további címtartományok a címtartomány a virtuális hálózat létrejötte után. Címtartomány felvétele meglévő virtuális hálózat kapcsolatban [hozzáadását vagy eltávolítását címtartomány](#add-or-remove-an-address-range).

      >[!WARNING]
      >Ha egy virtuális hálózati címtartományt, amely egy másik virtuális hálózati átfedésben van, vagy a helyszíni hálózat, a két hálózat nem lehet csatlakozni. Előtt adhat meg egy címtartományt, gondolja át, akkor előfordulhat, hogy kívánja-e csatlakozni a virtuális hálózat más virtuális hálózatok és a helyszíni hálózatokhoz a jövőben.
      >
      >

    - **Alhálózati név**: az alhálózat neve a virtuális hálózaton belül egyedinek kell lennie. Az alhálózat neve nem módosítható, miután az alhálózat létre van hozva. A portálhoz szükséges, hogy adhat meg egy alhálózatot egy virtuális hálózatban, létrehozásakor annak ellenére, hogy a virtuális hálózat nem rendelkezik alhálózatokkal szükséges. A portál csak egy alhálózat segítségével megadhatja, ha a virtuális hálózat létrehozása. Adhat hozzá további alhálózatokat a virtuális hálózati később, a virtuális hálózat létrejötte után. Adjon hozzá egy alhálózatot a virtuális hálózat, lásd: [alhálózatok kezelése](virtual-network-manage-subnet.md). Virtuális hálózat már több alhálózat működik az Azure parancssori felület vagy a PowerShell használatával is létrehozhat.

      >[!TIP]
      >A rendszergazdák néha, különböző alhálózatokon való vagy szabályozza a forgalom útválasztásához az alhálózatok közötti létrehozni. Definiálása előtt célszerű alhálózatok, fontolja meg, hogyan lehet szűrni kívánt és irányíthatja a forgalmat az alhálózatok között. Az alhálózatok közötti forgalmat szűrő kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](security-overview.md). Az Azure automatikusan útvonalak forgalom között az alhálózatokat, de szeretné felülbírálni az Azure alapértelmezett útvonalak. Azures alapértelmezett alhálózati a forgalom útválasztásához kapcsolatos további információkért lásd: [Útválasztás – áttekintés](virtual-networks-udr-overview.md).
      >

    - **Alhálózati címtartományt**: a megadott virtuális hálózat a címtér kell lennie. A legkisebb megadható tartománya /29, biztosító nyolc IP-cím az alhálózat. Azure fenntartja az első és utolsó cím protokoll megfelelési minden alhálózatban. Három további címek az Azure szolgáltatás használati számára vannak fenntartva. Ennek eredményeképpen a virtuális hálózat /29 alhálózati cím tartománnyal csak három használható IP-címmel rendelkezik. Ha azt tervezi, VPN-átjáró egy virtuális hálózathoz csatlakozni, létre kell hoznia egy átjáró-alhálózatot. További információ [adott cím tartomány szempontjai átjáró alhálózatok](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). A címtartomány meghatározott feltételek mellett az alhálózat létrehozása után módosíthatja. A alhálózati címtartományt módosításáról további tudnivalókért lásd: [alhálózatok kezelése](virtual-network-manage-subnet.md).
    - **Előfizetés**: Válasszon egy [előfizetés](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Egynél több Azure-előfizetés nem használhatja ugyanazt a virtuális hálózatot. Azonban csatlakozhat a virtuális hálózat egy előfizetés másik előfizetéssel, valamint a virtuális hálózatok [virtuális hálózati társviszony-létesítés](virtual-network-peering-overview.md). Az Azure-erőforrásokkal, a virtuális hálózathoz csatlakozó virtuális hálózatnak ugyanahhoz az előfizetéshez kell lennie.
    - **Erőforráscsoport**: Válasszon ki egy létező [erőforráscsoport](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) vagy hozzon létre egy újat. Egy Azure-erőforrás a virtuális hálózathoz csatlakozó lehet, mint a virtuális hálózat ugyanabban az erőforráscsoportban vagy egy másik erőforráscsoportban található.
    - **Hely**: válassza ki az Azure [hely](https://azure.microsoft.com/regions/), más néven egy régiót. Virtuális hálózat csak egy Azure-beli hely lehet. Azonban csatlakozhat egy helyet a virtuális hálózat egy másik helyre a virtuális hálózat VPN-átjáró használatával. Az Azure-erőforrásokkal, a virtuális hálózathoz csatlakozó ugyanazon a helyen, ahol a virtuális hálózatot kell lennie.

**Parancsok**

- Az Azure CLI: [az hálózati virtuális hálózat létrehozása](/cli/azure/network/vnet)
- PowerShell: [új-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Virtuális hálózatok megjelenítése és beállítások

1. Adja meg a keresési mezőbe, a portál felső, *virtuális hálózatok* be a keresőmezőbe. Ha **virtuális hálózatok** jelennek meg a keresési eredmények között, válassza ki azt.
2. A virtuális hálózatok listájában jelölje ki a virtuális hálózat, amely meg szeretné tekinteni a beállításokat.
3. Az alábbi beállítások találhatók a kiválasztott virtuális hálózat:
    - **Áttekintés**: információkat nyújt azokról a virtuális hálózat, beleértve a címterület és a DNS-kiszolgálók. Az alábbi képernyőfelvételen látható egy virtuális hálózat nevű áttekintése beállításainak **MyVNet**:

        ![Hálózati illesztő – áttekintés](./media/manage-virtual-network/vnet-overview.png)

      Áthelyezheti egy virtuális hálózat egy másik előfizetés vagy az erőforrás csoporthoz kiválasztásával **módosítás** melletti **erőforráscsoport** vagy **előfizetés neve**. Hogyan kívánja áthelyezni a virtuális hálózat kapcsolatban [erőforrások áthelyezése egy másik erőforráscsoportban vagy előfizetés](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). A cikk felsorolja az előfeltételeket és erőforrások áthelyezése az Azure portál, a PowerShell és az Azure parancssori felület használatával. A virtuális hálózathoz csatlakozó összes erőforrás át kell helyezni a virtuális hálózathoz.
    - **Címtér**: A a virtuális hálózati hozzárendelt címterek vannak felsorolva. Megtudhatja, hogyan hozzá és távolíthat el a címterület címtartomány, hajtsa végre a lépéseket a [hozzáadását vagy eltávolítását címtartomány](#add-or-remove-an-address-range).
    - **Csatlakoztatott eszközök**: minden olyan erőforrásnál, amely a virtuális hálózathoz kapcsolódó találhatók. Az előző képernyőképet három hálózati adapterrel és egy terheléselosztó a virtuális hálózathoz csatlakoznak. Új erőforrásokat hoz létre, és a virtuális hálózat jelennek meg. Ha törli a virtuális hálózathoz csatlakozó erőforrás, akkor már nem szerepelnek a listán.
    - **Alhálózatok**: a virtuális hálózaton belül található alhálózatok listája látható. Rendszerdémon hozzáadása és eltávolítása az alhálózat, lásd: [alhálózatok kezelése](virtual-network-manage-subnet.md).
    - **DNS-kiszolgálók**: megadhatja, hogy az Azure belső DNS-kiszolgáló vagy egy egyéni DNS-kiszolgálót biztosít a névfeloldást a virtuális hálózathoz csatlakozó eszközöket. Ha virtuális hálózat létrehozása az Azure portál használatával, Azure DNS-kiszolgálókat használ a névfeloldáshoz, a virtuális hálózaton belül alapértelmezés szerint. A DNS-kiszolgálók módosításához végrehajtásához a [módosítás DNS-kiszolgálók](#change-dns-servers) ebben a cikkben.
    - **Társviszony**: Ha nincsenek meglévő társviszony az előfizetést, akkor az itt felsorolt. Meglévő társviszony beállítások megtekintése vagy létrehozása, módosítása vagy törlése esetében. Társviszony kapcsolatos további információkért lásd: [virtuális hálózati társviszony-létesítés](virtual-network-peering-overview.md).
    - **Tulajdonságok**: a virtuális hálózat, beleértve a virtuális hálózati erőforrás-azonosító és az Azure-előfizetésre vonatkozó beállításokat jeleníti meg.
    - **Diagram**: A diagram összes eszközről, amelyen a virtuális hálózathoz kapcsolódó vizuális ábrázolását biztosítja. A diagram van néhány alapvető tudnivalók az eszközök. Ebben a nézetben a diagramon eszközöket kezelni válassza ki azt az eszközt.
    - **Közös Azure beállításai**: közös Azure beállításaival kapcsolatos további tudnivalókért tekintse meg a következő információkat:
        *   [Tevékenységnapló](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
        *   [Hozzáférés-vezérlés (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
        *   [Címkék](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Zárolások feloldása](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Automatizálási parancsfájl](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Parancsok**

- Az Azure CLI: [az hálózati vnet megjelenítése](/cli/azure/network/vnet#az_network_vnet_show)
- PowerShell: [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Adja hozzá, vagy távolítsa el az címtartománya

Adja hozzá, és távolítsa el a virtuális hálózat címtartományát. Az címtartománya CIDR-formátumban kell megadni, és nem lehetnek átfedésben más címtartományai belül az azonos virtuális hálózatban. A címtartományt, megadhatja a public vagy private (az RFC 1918) lehet. A címtartomány nyilvános vagy titkos határozza meg, hogy a címtartomány elérhető csak a virtuális hálózathoz csatlakozó virtuális hálózatot, és a helyszíni hálózatokhoz a virtuális hálózathoz csatlakozó belül. Nem adható hozzá a következő címtartományokat:

- 224.0.0.0/4 (csoportos küldés)
- 255.255.255.255/32 (közvetítés)
- 127.0.0.0/8 (visszacsatolás)
- 169.254.0.0/16 (kapcsolatszintű)
- 168.63.129.16/32 (belső DNS)

Adja hozzá, vagy távolítsa el a címtartomány:

1. Adja meg a keresési mezőbe, a portál felső, *virtuális hálózatok* be a keresőmezőbe. Ha **virtuális hálózatok** jelennek meg a keresési eredmények között, válassza ki azt.
2. A virtuális hálózatok listájában jelölje ki a virtuális hálózat legyen lehet hozzáadni vagy eltávolítani egy címtartományt.
3. Válassza ki **Címtéren**a **beállítások**.
4. Hajtsa végre az alábbi lehetőségek közül:
    - **Címtartomány felvétele**: Adja meg az új címek tartományát. A címtartomány a virtuális hálózathoz definiált meglévő címtartomány nem lehet átfedésben.
    - **Távolítsa el a címtartomány**: a címtartomány el szeretné távolítani a jobb oldalon válassza ki a **...** , majd jelölje be **eltávolítása**. Alhálózat létezik-e a tartománya, ha a címtartomány nem távolítható el. Címtartomány törléséhez először törölnie kell alhálózatok (és az alhálózatban lévő erőforrásokat), hogy a címtartomány szerepel.
5. Kattintson a **Mentés** gombra.

**Parancsok**

- Az Azure CLI: [az hálózat virtuális hálózat frissítése](/cli/azure/network/vnet#az_network_vnet_update)
- PowerShell: [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)

## <a name="change-dns-servers"></a>Módosítsa a DNS-kiszolgálók

Minden virtuális gépek csatlakoznak-e a virtuális hálózati register állít be a virtuális hálózat DNS-kiszolgálókkal. Névfeloldás használata a megadott DNS-kiszolgáló is. Mindegyik hálózati interfész (NIC) a virtuális gép lehet a saját DNS-kiszolgáló beállításai. Ha egy hálózati Adaptert a saját DNS-kiszolgáló beállításait, a virtuális hálózat DNS-kiszolgáló beállításai felülbírálják a. A hálózati adapter DNS-beállítások kapcsolatos további információkért lásd: [hálózati illesztő feladatok és a beállítások](virtual-network-network-interface.md#change-dns-servers). További információt a névfeloldást a virtuális gépek és az Azure Felhőszolgáltatások szerepkörpéldányok, lásd: [névfeloldását virtuális gépek és a szerepkörpéldányok](virtual-networks-name-resolution-for-vms-and-role-instances.md). Hozzáadása, módosítása, vagy távolítsa el a DNS-kiszolgáló:

1. Adja meg a keresési mezőbe, a portál felső, *virtuális hálózatok* be a keresőmezőbe. Ha **virtuális hálózatok** jelennek meg a keresési eredmények között, válassza ki azt.
2. A virtuális hálózatok listájában jelölje ki a virtuális hálózat DNS-kiszolgálóinak módosítani szeretné.
3.  Válassza ki **DNS-kiszolgálók**a **beállítások**.
4. A következő lehetőségek közül:
    - **Alapértelmezett (Azure által biztosított)**: összes erőforrás nevét és privát IP-címek automatikusan regisztrálva van az Azure DNS-kiszolgálók. Feloldhatja a nevek közötti minden olyan erőforrásnál, amely ugyanahhoz a virtuális hálózathoz csatlakoznak. Virtuális hálózatok közötti névfeloldás Ez a beállítás nem használható. Névfeloldás a virtuális hálózatok közötti, egy egyéni DNS-kiszolgálót kell használnia.
    - **Egyéni**: legfeljebb a Azure virtuális hálózat egy vagy több kiszolgálót is hozzáadhat. DNS-kiszolgálói korlátok kapcsolatos további információkért lásd: [Azure korlátozását](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). A következő lehetőségek közül választhat:
        - **Adjon hozzá egy címet**: a kiszolgáló hozzáadása a virtuális hálózat DNS-kiszolgálók listájához. Ezt a lehetőséget is regisztrálja a DNS-kiszolgáló Azure. Ha már egy DNS-kiszolgáló már regisztrálva van az Azure, kiválaszthatja, hogy a DNS-kiszolgáló a listában.
        - **Távolítsa el az címet**: mellett a kiszolgálót, amely el szeretné távolítani, válassza ki a **...** , majd **eltávolítása**. A kiszolgáló törlésével eltávolítja a kiszolgáló csak a virtuális hálózatok listája. A DNS-kiszolgáló használatára a virtuális hálózatok az Azure-ban regisztrált marad.
        - **DNS-kiszolgálócímek átrendezése**: fontos, hogy ellenőrizze, hogy a DNS-kiszolgálók, a környezetnek a megfelelő sorrendben felsorolja. DNS-kiszolgálók listáját akkor megadott sorrendben használhatók. A ciklikus multiplexelési telepítési nem működik. A listán az első DNS-kiszolgáló elérhető, ha az ügyfél használja a DNS-kiszolgáló, függetlenül attól, hogy a DNS-kiszolgáló megfelelően működik-e. Távolítson el minden felsorolt DNS-kiszolgálót, és adja őket újra a kívánt sorrendben.
        - **Módosítsa a címet**: Jelölje ki a DNS-kiszolgáló, a listában, és írja be az új cím.
5. Kattintson a **Mentés** gombra.
6. Indítsa újra a virtuális gépek, amelyek a virtuális hálózathoz kapcsolódnak, hozzárendeli az új DNS-kiszolgáló beállításai. Virtuális gépek továbbra is az aktuális DNS-beállítások azok újraindításáig.

**Parancsok**

- Az Azure CLI: [az hálózat virtuális hálózat frissítése](/cli/azure/network/vnet#az_network_vnet_update)
- PowerShell: [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)

## <a name="delete-a-virtual-network"></a>A virtuális hálózat törlése

Virtuális hálózat csak akkor, ha nincsenek erőforrások csatlakozik, vagy törölheti. Ha nincsenek erőforrások csatlakoznak bármely alhálózatot a virtuális hálózaton belül, először törölnie kell az erőforrásokat, amelyek a virtuális hálózat összes alhálózatát csatlakoznak. Erőforrás törlése lépései eltérőek attól függően, hogy az erőforrás. Az alhálózatokhoz kapcsolódó erőforrások törlése, olvassa el minden erőforrástípus törli dokumentációját. A virtuális hálózat törlése:

1. Adja meg a keresési mezőbe, a portál felső, *virtuális hálózatok* be a keresőmezőbe. Ha **virtuális hálózatok** jelennek meg a keresési eredmények között, válassza ki azt.
2. A virtuális hálózatok listájában jelölje ki a törölni kívánt virtuális hálózat.
3. Győződjön meg arról, hogy nincsenek-e csatlakoztatva a virtuális hálózathoz kiválasztásával eszközök **csatlakoztatott eszközök**a **beállítások**. Ha csatlakoztatott eszközön, törölnie kell azokat a virtuális hálózat törlése előtt. Ha nincsenek csatlakoztatott eszközök, válassza ki a **áttekintése**.
4. Válassza a **Törlés** elemet.
5. Válassza ki a virtuális hálózat a törlés megerősítéséhez **Igen**.

**Parancsok**

- Az Azure CLI: [azure-hálózat virtuális hálózat törlése](/cli/azure/network/vnet#az_network_vnet_delete)
- PowerShell: [Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork)

## <a name="permissions"></a>Engedélyek

Virtuális hálózatok feladatok elvégzésekor, a fiókot hozzá kell rendelni a [hálózat közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkör, amely hozzá van rendelve a megfelelő műveleteket az alábbi táblázatban felsorolt:

| Műveletek                                  |   Name (Név)                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/read   |   Olvassa el a virtuális hálózat              |
|Microsoft.Network/virtualNetworks/write  |   A virtuális hálózat létrehozása vagy módosítása  |
|Microsoft.Network/virtualNetworks/delete |   A virtuális hálózat törlése            |

## <a name="next-steps"></a>További lépések

- Hozzon létre egy virtuális hálózat használatával [PowerShell](powershell-samples.md) vagy [Azure CLI](cli-samples.md) parancsfájlok, vagy az Azure használatával [Resource Manager-sablonok](template-samples.md)
- Létrehozása és alkalmazása [Azure házirend](policy-samples.md) virtuális hálózatok