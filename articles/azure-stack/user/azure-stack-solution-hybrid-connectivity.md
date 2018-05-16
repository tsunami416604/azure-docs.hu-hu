---
title: Hibrid felhő kapcsolat konfigurálása az Azure és az Azure-verem |} Microsoft Docs
description: Tudnivalók a hibrid felhő kapcsolat konfigurálása az Azure és az Azure-verem.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 048e2636aabe406728c8fe1b93ef861f13346256
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Oktatóanyag: hibrid felhő kapcsolat konfigurálása az Azure és az Azure-verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A globális Azure és a hibrid kapcsolat minta használatával Azure verem biztonságot erőforrásait is elérik. 

Ebben az oktatóanyagban egy minta környezet fog létrehozni:

> [!div class="checklist"]
> - A helyi adatvédelmi vagy jogszabályi követelmények megtartása, de van a globális Azure-erőforrások eléréséhez.
> - Egy korábbi rendszer karbantartása globális Azure cloud méretezhető alkalmazások telepítését és erőforrások használatakor.

## <a name="prerequisites"></a>Előfeltételek

Néhány összetevőt egy hibrid kapcsolat központi telepítés létrehozásához szükségesek, és előkészítéséhez hosszabb időt vehet igénybe. 

Egy Azure OEM-hardver Partner előfordulhat, hogy telepíteni egy éles Azure-vermet, és minden felhasználó is telepítheti egy ASDK. Azure verem operátort kell is telepítheti az App Service, csomagok és ajánlatok létrehozása, bérlő előfizetés létrehozása és a Windows Server 2016-lemezkép hozzáadása. 

Ha már rendelkezik néhány ezeket az összetevőket, győződjön meg arról, hogy azok megfelelnek megkezdése előtt.

Ez a témakör feltételezi, hogy rendelkezik-e bizonyos Azure és az Azure-verem ismerete. Ha azt szeretné, további, a folytatás előtt mindenképpen indítsa el az alábbi témakörök:
 - [Bevezetés az Azure-bA](https://azure.microsoft.com/overview/what-is-azure/)
 - [Az Azure verem kulcsfogalmak](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure
 - Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
 - Hozzon létre egy [webalkalmazás](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) az Azure-ban. Jegyezze meg az új webes alkalmazás URL-CÍMÉT, mert a rendszer később.

### <a name="azure-stack"></a>Azure Stack
 - Az éles Azure verem használható, vagy Azure verem szoftverfejlesztői készlet központi telepítése az alábbi hivatkozásokon elérhető:
    - https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 
    - A telepítés általában befejeződik, ezért ennek megfelelően tervezheti néhány órát vesz igénybe.
 - Telepítése [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) Azure verem PaaS-szolgáltatások. 
 - [Terv/ajánlatok létrehozása](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) az Azure-verem környezeten belül. 
 - [Hozzon létre a bérlői előfizetéshez](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) az Azure-verem környezeten belül. 


### <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

 - Győződjön meg arról, hogy rendelkezik egy kifelé irányuló, nyilvános IPv4-címmel a VPN-eszköz számára. Ez az IP-cím nem lehet NAT mögötti.
 - Gondoskodjon arról, hogy az azonos régió/hely összes erőforrások telepítése.

#### <a name="example-values"></a>Példaértékek

A cikkben szereplő példák a következő értékeket használják. Ezek az értékek segítségével hozzon létre egy tesztkörnyezetben vagy jobb megértése érdekében ebben a cikkben szereplő példák őket. További információkat a VPN Gateway beállításairól általánosságban [a VPN Gateway beállításaival kapcsolatos](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings) cikkben találhat.

Kapcsolat specifikációk:
 - **VPN-típus**: útválasztó-alapú
 - **Kapcsolat típusa**: pont-pont (IPsec)
 - **Átjáró típusa**: VPN
 - **Az Azure-kapcsolat neve**: Azure-átjáró-AzureStack-S2SGateway (a portál fog automatikus kitöltés Ez az érték)
 - **Az Azure verem kapcsolatnév**: AzureStack-átjáró-Azure-S2SGateway (a portál fog automatikus kitöltés Ez az érték)
 - **Megosztott kulcs**: minden kompatibilis VPN hardver, amelyben kapcsolat mindkét oldalán
 - **Előfizetés**: bármely elsődleges előfizetés
 - **Erőforráscsoport**: teszt-infrastruktúra

| Azure vagy az Azure-verem kapcsolat | Név | Alhálózat | IP-cím |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Az Azure virtuális hálózat | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | A GatewaySubnet<br>10.100.103.0/24 |  |
| Az Azure verem virtuális hálózat | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | A GatewaySubnet <br>10.100101.0/24 |  |
| Az Azure virtuális hálózati átjáró | Azure-átjáró |  |  |
| Az Azure verem virtuális hálózati átjáró | AzureStack-átjáró |  |  |
| Az Azure nyilvános IP-cím | Azure-GatewayPublicIP |  | Határozza meg a létrehozásakor |
| Az Azure verem nyilvános IP-cím | AzureStack-GatewayPublicIP |  | Határozza meg a létrehozásakor |
| Az Azure helyi hálózati átjáró | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Az Azure verem nyilvános IP-cím értéke |
| A verem Azure helyi hálózati átjáró | Azure-S2SGateway<br>10.100.102.0/23 |  | Az Azure nyilvános IP-cím értéke |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Virtuális hálózat létrehozása a globális Azure és az Azure-verem

> [!Note]  
> Biztosítania kell, hogy nincs-e az Azure vagy az Azure-verem vNet címterületek IP-címek nem átfedésének. 

VNet létrehozása a Resource Manager üzembe helyezési modellel, az Azure portál használatával. Használja a [példaértékeket](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values), ha a lépéseket csupán az oktatóanyag elvégzése érdekében hajtja végre. Ha a lépéseket nem az oktatóanyag keretében hajtja végre, ne felejtse el az értékeket a saját értékeire cserélni. 

1. Egy böngészőből lépjen az [Azure Portalra](http://portal.azure.com/), majd jelentkezzen be az Azure-fiókjával.
2. Kattintson az **Erőforrás létrehozása** gombra. Az a **keresése a piactéren** mezőbe írja be `virtual network`nyelvhez. Keresse meg **virtuális hálózati** visszaadott listából, majd nyissa meg a **virtuális hálózati** lap.
3. A virtuális hálózat lap alján a közelében a **telepítési modell kiválasztása** listára, válassza ki **erőforrás-kezelő**, majd válassza ki **létrehozása**. Ez megnyitja a „Virtuális hálózat létrehozása” lapot.
4. A **Virtuális hálózat létrehozása** lapon konfigurálja a VNet beállításait. A mezők kitöltése közben a vörös felkiáltójelből zöld pipa lesz, ha a mezőbe beírt karakterek érvényesek.
5. Ismételje meg az alábbi lépéseket a **bérlői portál** Azure vermet.

## <a name="add-a-gateway-subnet"></a>Átjáróalhálózat hozzáadása

Mielőtt csatlakoztatja virtuális hálózatát egy átjáróhoz, létre kell hoznia az átjáróalhálózatot ahhoz a virtuális hálózathoz, amelyhez csatlakozni szeretne. Az átjárószolgáltatások az átjáróalhálózatban megadott IP-címeket használják.

Keresse meg a [portálon](http://portal.azure.com/) azt a Resource Manager-alapú virtuális hálózatot, amelyhez létre kíván hozni egy virtuális hálózati átjárót.

1. Az a **beállítások** szakasz a hálózatok lapon jelölje be a **alhálózatok** bontsa ki a **alhálózatok** lap.
2. Az a **alhálózatok** lapon jelölje be **+ átjáróalhálózatot** megnyitásához a **alhálózat hozzáadása** lap.

    ![](media/azure-stack-solution-hybrid-connectivity/image4.png)

3. Az alhálózat **nevénél** automatikusan megjelenik a „GatewaySubnet” érték. Ez az érték ismeri fel az alhálózati, az átjáró alhálózatának Azure szükség. Módosítsa a automatikusan kitöltött **-címtartományt** értékek megfelelnek a konfigurációs követelményeinek, majd válassza ki **OK** hozza létre az alhálózatot a lap alján.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>A virtuális hálózati átjáró létrehozása az Azure és az Azure verem

1. A portál lap bal oldalán válassza ki a +, és írja be a "Virtuális hálózati átjáró" keresésben. A **eredmények**, keresse meg és jelölje ki **virtuális hálózati átjáró**.
2. Alján a **virtuális hálózati átjáró** lapon jelölje be **létrehozása**. Ez megnyitja a **Virtuális hálózati átjáró létrehozása** lapot.
3. Az a **virtuális hálózati átjáró létrehozása** adja meg azokat az értékeket a virtuális hálózati átjáró, ahogy az a példában szereplő értékeket, valamint az alábbi részletes értékeket.
    - **SKU**: alapvető
    - **Virtuális hálózati**: válassza ki a korábban létrehozott virtuális hálózatot. Automatikusan kiválasztja azt a korábban létrehozott átjáró-alhálózatot. 
    - **Első IP-konfiguráció**: Ez az, hogy az átjáró nyilvános IP-cím. 
        - Kattintson a **létrehozás átjáró IP-konfiguráció** és a rendszer ekkor a a **nyilvános IP-cím kiválasztása** lap.
        - Kattintson a **+ új létrehozása** megnyitásához a **nyilvános IP-cím létrehozása** lap.
        - Adjon meg egy **neve** a nyilvános IP-cím. A Termékváltozat, hagyja **alapvető**, majd jelölje be **OK** menti a módosításokat a lap alján.

    > [!Note]  
    > VPN-átjáró jelenleg csak támogatja a dinamikus nyilvános IP-címek hozzárendelését. Ez azonban nem jelenti azt, hogy az IP-cím módosul a VPN Gateway-hez való hozzárendelése után. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.

4. Ellenőrizze a beállításokat. 
5. A **Létrehozás** gombra kattintva hozzákezdhet a VPN-átjáró létrehozásához. A rendszer ellenőrzi a beállításokat, és az irányítópulton megjelenik a „Virtuális hálózati átjáró üzembe helyezése” csempe. Az átjáró létrehozása akár 45 percet is igénybe vehet. Előfordulhat, hogy a kész állapot megjelenítéséhez frissítenie kell a portáloldalt.

    Az átjáró létrehozása után úgy tekintheti meg a hozzárendelt IP-címet, ha megnézi a virtuális hálózatot a portálon. Az átjáró csatlakoztatott eszközként fog megjelenni. Kiválaszthatja a csatlakoztatott eszközre (a virtuális hálózati átjáró) további információk megjelenítéséhez.
6. Ismételje ezeket a lépéseket az Azure-verem üzemelő példányon.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>A helyi hálózati átjáró létrehozása az Azure és az Azure-verem

A helyi hálózati átjáró általában a helyszínt jelenti. A hely adjon egy nevet, amely szerint az Azure vagy az Azure-verem is hivatkozik rá, majd adja meg, amelyhez létrehoz egy kapcsolatot a helyszíni VPN-eszköz IP-címét. Emellett megadhatja azokat az IP-címelőtagokat, amelyek a VPN-átjárón keresztül a VPN-eszközre lesznek irányítva. Az Ön által meghatározott címelőtagok a helyszíni hálózatán található előtagok. Ha a helyszíni hálózat megváltozik, vagy módosítania kell a VPN-eszköz nyilvános IP-címét, könnyen frissítheti az értékeket később.

1. Válassza a portál **+ hozzon létre egy erőforrást**.
2. A keresési mezőbe, írja be **helyi hálózati átjáró**, majd nyomja le az **Enter** kereséséhez. Ez visszaad egy listát az eredményekkel. Kattintson a **helyi hálózati átjáró**, majd jelölje be a **létrehozása** gombra kattintva nyissa meg a **helyi hálózati átjáró létrehozása** lap.
3. Az a **létrehozása helyi hálózati átjáró oldal**, adja meg a részletes, a példában szereplő értékeket, valamint részletes alatt az értékeket a helyi hálózati átjáró értékeit.
    - **IP-cím**: Ez az Azure vagy az Azure-verem való csatlakozáshoz használni kívánt VPN-eszköz nyilvános IP-címét. Adjon meg egy érvényes nyilvános IP-címet. Az IP-cím nem lehet NAT mögött, és elérhetőnek kell lennie az Azure számára. Ha most még nem rendelkezik az IP-címmel, használhatja a példában látható értékeket, később azonban vissza kell térnie ide, és lecserélni a helyőrző IP-címet a VPN-eszköz nyilvános IP-címére. Ellenkező esetben az Azure nem fog tudni csatlakozni.
    - A **Címtartomány** annak a hálózatnak a címtartományára utal, amelyet a helyi hálózat képvisel. Több címtartományt is felvehet. Ügyeljen arra, hogy az itt megadott címtartományok ne legyenek átfedésben olyan egyéb hálózatok címtartományaival, amelyekhez csatlakozni kíván. Az Azure a helyszíni VPN-eszköz IP-címéhez irányítja át a megadott címtartományt. Itt használja saját értékeket, ha azt szeretné, helyhez való kapcsolódáshoz a helyszíni, nem az az értékeket, a példában látható módon.
    - **BGP-beállítások konfigurálása**: csak a BGP konfigurálásakor használja. Más esetben ne jelölje be ezt a jelölőnégyzetet.
    - **Előfizetés**: Győződjön meg arról, hogy a megfelelő előfizetés jelenik-e.
    - **Erőforráscsoport**: válassza ki a használni kívánt erőforráscsoportot. Létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy korábban létrehozottat.
    - **Hely**: válassza ki azt a helyet, amely az objektum létrejön. Érdemes ugyanazt a helyet kiválasztani, ahol a virtuális hálózat is található, de ez nem kötelező.
4. Az értékek megadása után válassza ki a **létrehozása** gombra a helyi hálózati átjáró létrehozásához a lap alján.
5. Ismételje ezeket a lépéseket az Azure-verem üzemelő példányon.

## <a name="configure-your-connection"></a>A kapcsolat beállítása

A helyszíni hálózaton a helyek közötti kapcsolatok létesítéséhez VPN-eszközre van szükség. Ebben a lépésben konfigurálja a VPN-eszköz kapcsolat néven ismert. A kapcsolat konfigurálásakor a következők szükségesek:
    - Megosztott kulcs. Ez ugyanaz a megosztott kulcs, amelyet a helyek közötti VPN-kapcsolat létrehozásakor ad meg. A példákban alapvető megosztott kulcsot használunk. Javasoljuk egy ennél összetettebb kulcs létrehozását.
    - A virtuális hálózati átjáró nyilvános IP-címe. A nyilvános IP-címet az Azure Portalon, valamint a PowerShell vagy a CLI használatával is megtekintheti. A nyilvános IP-címének a VPN-átjáró, az Azure portál használatával, navigáljon a virtuális hálózati átjárók, majd válassza ki az átjáró nevét.
Hozzon létre egy helyek közötti VPN-kapcsolatot a virtuális hálózati átjáró és a helyszíni VPN-eszköz között.
1. Válassza a portál **+ hozzon létre egy erőforrást**.
2. A keresési mezőbe, írja be **kapcsolatok**, majd nyomja le az **Enter** kereséséhez. Ez visszaad egy listát az eredményekkel. Kattintson a **kapcsolatok**, majd válassza ki a Létrehozás gombra kattintva megnyithatja a **kapcsolatok létrehozása** lap.
3. Az a **kapcsolatok létrehozása** lapján adja meg a beállításokat a kapcsolathoz.
     - Alapjai:
        1. **Kapcsolat típusa**: válassza ki a pont-pont (IPSec).
        2. **Erőforráscsoport**: (a tesztelési erőforráscsoport kiválasztása)
     - Beállítások:
        1. **Virtuális hálózati átjáró**: válassza ki a korábban létrehozott virtuális hálózati átjáró.
        2. **Helyi hálózati átjáró**: válassza ki a korábban létrehozott helyi hálózati átjáró. 
        3. **Kapcsolat neve**: A rendszer automatikusan feltölti a két átjáró értékeivel. 
        4. **Megosztott kulcs**: értékének meg kell egyeznie az értéket, amelyet a helyi helyszíni VPN-eszköz alkalmaz. A példában az abc123 értéket használtuk, de lehetséges (és javasolt) ennél összetettebb értéket használni. A legfontosabb, hogy az itt megadott értéknek egyeznie kell azzal az értékkel, amelyet a VPN-eszköz konfigurálásakor ad meg.
    - A fennmaradó **Subscription** (Előfizetés), **Resource Group** (Erőforráscsoport) és **Location** (Hely) mezők értékei rögzítettek.
4. A kapcsolat létrehozásához kattintson az **OK** gombra. Látni fogja, a képernyőn megjelenő flash létrehozása kapcsolat.
5. Megtekintheti a kapcsolat a ** a virtuális hálózati átjáró kapcsolatok oldalán. Az állapot kerül a **ismeretlen** való **csatlakozás**, majd a **sikeres**.

## <a name="next-steps"></a>További lépések

- Azure Cloud mintájával kapcsolatos további tudnivalókért lásd: [felhőalapú kialakítási mintájával](https://docs.microsoft.com/azure/architecture/patterns).
