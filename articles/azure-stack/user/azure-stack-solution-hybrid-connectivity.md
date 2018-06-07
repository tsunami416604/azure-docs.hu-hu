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
ms.date: 05/25/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 72c5c4b0f0ab752bb02e6bee7cd038afca44ee1b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605195"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Oktatóanyag: hibrid felhő kapcsolat konfigurálása az Azure és az Azure-verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A globális Azure és a hibrid kapcsolat minta használatával Azure verem biztonságot erőforrásait is elérik.

Ebben az oktatóanyagban egy minta környezet fog létrehozni:

> [!div class="checklist"]
> - Tartsa meg a helyi adatvédelmi és szabályozási követelmények felel meg, de van a globális Azure-erőforrások eléréséhez.
> - Egy korábbi rendszer karbantartása globális Azure cloud méretezhető alkalmazások telepítését és erőforrások használatakor.

## <a name="prerequisites"></a>Előfeltételek

Néhány összetevők szükségesek a hibrid kapcsolat központi telepítés létrehozásához. Felkészülési, néhány ezeket az összetevőket lépnek, ennek megfelelően tervezheti kell.

**Azure Stack**

Egy Azure OEM-hardver Partner telepítheti egy éles Azure-vermet, és az összes felhasználó telepítheti egy Azure verem Development Kit (ASDK).

**Az Azure verem összetevők**

Azure verem operátort kell telepíteni az App Service, tervek és ajánlatok létrehozása, bérlő előfizetés létrehozása és a Windows Server 2016-lemezkép hozzáadása. Ha már rendelkezik néhány összetevőt, győződjön meg arról, hogy azok megfelelnek az oktatóanyag elindítása előtt.

Ez az oktatóanyag feltételezi, hogy rendelkezik-e bizonyos Azure és az Azure-verem alapszintű ismeretét. Az oktatóanyag elindítása előtt további tudnivalókért olvassa el a következő cikkeket:

 - [Bevezetés az Azure-bA](https://azure.microsoft.com/overview/what-is-azure/)
 - [Az Azure verem kulcsfogalmak](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
 - Hozzon létre egy [webalkalmazás](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) az Azure-ban. Jegyezze meg a webes alkalmazás URL-CÍMÉT, az oktatóanyag szüksége.

### <a name="azure-stack"></a>Azure Stack

 - Az éles Azure verem használható, vagy telepítse az Azure-verem szoftverfejlesztői készlet https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >A ASDK telepítése 7 órát igénybe vehet, ezért ennek megfelelően tervezheti.

 - Telepítése [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) Azure verem PaaS-szolgáltatások.
 - [Tervek és ajánlatok létrehozása](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) a verem Azure környezetben.
 - [Hozzon létre a bérlői előfizetéshez](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) az Azure-verem környezeten belül.

### <a name="before-you-begin"></a>Előkészületek

Ellenőrizze, hogy a következő feltételeknek megfelelő hibrid felhő kapcsolat konfigurálásának megkezdése előtt:

 - Külsőleg irányuló nyilvános IPv4-cím a VPN-eszköz szükség van. Az IP-cím nem található a NAT mögött
 - Az azonos régió/hely összes erőforrások telepítése.

#### <a name="tutorial-example-values"></a>Útmutató példaértékek

Ebben az oktatóanyagban szereplő példák a következő értékeket használja. Ezek az értékek segítségével hozzon létre egy tesztkörnyezetben, vagy tekintse meg azokat a példák kra. További információkat a VPN Gateway beállításairól általánosságban [a VPN Gateway beállításaival kapcsolatos](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings) cikkben találhat.

Kapcsolat specifikációk:

 - **VPN-típus**: útválasztó-alapú
 - **Kapcsolat típusa**: pont-pont (IPsec)
 - **Átjáró típusa**: VPN
 - **Az Azure-kapcsolat neve**: Azure-átjáró-AzureStack-S2SGateway (a portál fog automatikus kitöltés Ez az érték)
 - **Az Azure verem kapcsolatnév**: AzureStack-átjáró-Azure-S2SGateway (a portál fog automatikus kitöltés Ez az érték)
 - **Megosztott kulcs**: minden kompatibilis VPN hardver, amelyben kapcsolat mindkét oldalán
 - **Előfizetés**: bármely elsődleges előfizetés
 - **Erőforráscsoport**: teszt-infrastruktúra

Hálózati és alhálózati IP-címek:

| Azure vagy az Azure-verem kapcsolat | Name (Név) | Alhálózat | IP-cím |
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

Az alábbi lépések segítségével virtuális hálózat létrehozása a portál használatával. E [példaértékeket](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) használata ebben a cikkben mint egy oktatóanyag. De konfigurálásához éles környezetben való használata ebben a cikkben, cserélje le a példában beállításokat a saját értékeit.

> [!IMPORTANT]
> Győződjön meg arról, hogy az Azure vagy az Azure-verem vNet címterületek IP-címek átfedésben nem létezik.

VNet létrehozása az Azure-ban:

1. A böngésző használatával csatlakozhat a [Azure-portálon](http://portal.azure.com/) és jelentkezzen be az Azure-fiókjával.
2. Válassza ki **hozzon létre egy erőforrást**. Az a **keresése a piactéren** mezőbe írja be `virtual network`nyelvhez. Található **virtuális hálózati** az eredmények listájában, és válassza a **virtuális hálózati**.
3. Az a **telepítési modell kiválasztása** listára, válassza ki **erőforrás-kezelő**, majd válassza ki **létrehozása**.
4. A **virtuális hálózat létrehozása**, konfigurálja a VNet beállításait. A kötelező mezőket a piros csillaggal előtagot.  Ha egy érvényes értéket ad meg, a csillag egy zöld pipa változik.

VNet létrehozása az Azure-készletben:

* Ismételje az előző lépést (1-4) használata az Azure-verem **bérlői portál**.

## <a name="add-a-gateway-subnet"></a>Átjáróalhálózat hozzáadása

Csatlakozás a virtuális hálózati átjáró, mielőtt létrehozásához szükséges, amelyet szeretne csatlakozni a virtuális hálózati átjáró alhálózatát. Az átjáró szolgáltatás használ az IP-cím az átjáró-alhálózat alapján.

Az a [Azure-portálon](http://portal.azure.com/), keresse meg az erőforrás-kezelő virtuális hálózaton a virtuális hálózati átjáró létrehozásához.

1. Válassza ki a virtuális hálózat lehetőségre a **virtuális hálózati** lap.
2. A **beállítások**, jelölje be **alhálózatok**.
3. Az a **alhálózatok** lapon jelölje be **+ átjáróalhálózatot** megnyitásához a **alhálózat hozzáadása** lap.

    ![Átjáró alhálózatának hozzáadása](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. A **neve** az az alhálózat automatikusan bekerül a "GatewaySubnet". Ez az érték ismeri fel az alhálózati, az átjáró alhálózatának Azure szükség.
5. Módosítsa a **-címtartományt** értékek, amelyek megfelelnek a konfigurációs követelményeinek, majd válassza ki az ismertetett **OK**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>A virtuális hálózati átjáró létrehozása az Azure és az Azure verem

Az alábbi lépések segítségével a virtuális hálózati átjáró létrehozása az Azure-ban.

1. A portál lap bal oldalán válassza ki a **+** és a keresési mezőbe írja be a "virtuális hálózati átjáró".
2. A **eredmények**, jelölje be **virtuális hálózati átjáró**.
3. A **virtuális hálózati átjáró**, jelölje be **létrehozása** megnyitásához a **virtuális hálózati átjáró létrehozása** lap.
4. A **virtuális hálózati átjáró létrehozása**, adja meg a hálózati átjáró értékeit, ahogy az **oktatóanyag példaértékeket**, és az alábbi értékeket:

    - **SKU**: alapvető
    - **Virtuális hálózati**: válassza ki a korábban létrehozott virtuális hálózatot. Az átjáró alhálózatának létrehozott automatikusan ki van jelölve.
    - **Első IP-konfiguráció**: Ez az, hogy az átjáró nyilvános IP-cím.
        - Válassza ki **létrehozás átjáró IP-konfiguráció**, amely veszi, hogy a **nyilvános IP-cím kiválasztása** lap.
        - Válassza ki **+ új létrehozása** megnyitásához a **nyilvános IP-cím létrehozása** lap.
        - Adjon meg egy **neve** a nyilvános IP-cím. A Termékváltozat, hagyja **alapvető**, majd válassza ki **OK** menti a módosításokat.

       > [!Note]
       > VPN-átjáró jelenleg csak dinamikus nyilvános IP-címek hozzárendelését támogatja. Ez azonban nem jelenti azt, hogy az IP-cím módosítása után a VPN-átjáró van hozzárendelve. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Átméretezése, alaphelyzetbe állítása vagy más belső karbantartás vagy frissítés VPN-átjárót, hogy ne módosítsa az IP-címet.

4. Ellenőrizze az átjáró beállításait.
5. Válassza ki **létrehozása** a VPN-átjáró létrehozásához. Az átjáró beállításainak ellenőrzését, és a "Deploying virtuális hálózati átjáró" csempe az irányítópulton látható.

   >[!Note]
   >Az átjáró létrehozása akár 45 percet is igénybe vehet. Előfordulhat, hogy a kész állapot megjelenítéséhez frissítenie kell a portáloldalt.

    Az átjáró létrehozása után megtekintheti a virtuális hálózatot a portálon bármikor rendelt IP-címét. Az átjáró csatlakoztatott eszközként fog megjelenni. Az átjáró kapcsolatos további információk megtekintéséhez válassza ki az eszközt.

6. Ismételje az előző lépést (1-5) a Azure verem üzemelő példányon.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>A helyi hálózati átjáró létrehozása az Azure és az Azure-verem

A helyi hálózati átjáró általában a helyszínt jelenti. A hely adjon olyan nevet, az Azure vagy az Azure-verem is vonatkozik, és adja meg:

- A létrehozni kívánt kapcsolat a helyszíni VPN-eszköz IP-címe.
- Az IP-cím előtagokat, amely a VPN-átjáró számára a VPN-eszközön keresztül történik. Az Ön által meghatározott címelőtagok a helyszíni hálózatán található előtagok.

  >[!Note]
  >A helyszíni hálózati módosításokat, vagy a nyilvános IP-címet a VPN-eszközön módosítani kell, ha később könnyen frissítheti ezeket az értékeket.

1. Válassza a portál **+ hozzon létre egy erőforrást**.
2. A keresési mezőbe, írja be a **helyi hálózati átjáró**, majd jelölje be **Enter** kereséséhez. Ez visszaad egy listát az eredményekkel.
3. Válassza ki **helyi hálózati átjáró**, majd jelölje be **létrehozása** megnyitásához a **helyi hálózati átjáró létrehozása** lap.
4. A **helyi hálózati átjáró létrehozása**, adja meg a helyi hálózati átjáró értékeit használja a **oktatóanyag példaértékeket**. Az alábbi értékeket tartalmazza.

    - **IP-cím**: Ez az Azure vagy az Azure-verem való csatlakozáshoz használni kívánt VPN-eszköz nyilvános IP-címét. Adjon meg egy érvényes nyilvános IP-címet, amely nem NAT mögött, így Azure el lehet-e érni a címet. Ha az IP-cím jelenleg nem rendelkezik, helyőrzőként is használhatja a példa egy értéket, de kell lépjen vissza, és cserélje le a helyőrző a VPN-eszköz nyilvános IP-címét. Azure nem tud csatlakozni az eszköz csak érvényes cím.
    - **Címtér**: Ez az, hogy a címtartomány a hálózat, amely a helyi hálózati jelöli. Több címtartományt is felvehet. Győződjön meg arról, hogy a megadott tartományok nem fednek át más hálózatokkal, amelyhez csatlakozni kíván a tartományait. Az Azure a helyszíni VPN-eszköz IP-címéhez irányítja át a megadott címtartományt. A saját értékeit használja, ha a helyszíni hely nem ilyen kiválasztható érték például való kapcsolódáshoz.
    - **BGP-beállítások konfigurálása**: csak a BGP konfigurálásakor használja. Más esetben ne jelölje be ezt a jelölőnégyzetet.
    - **Előfizetés**: Győződjön meg arról, hogy a megfelelő előfizetés jelenik-e.
    - **Erőforráscsoport**: válassza ki a használni kívánt erőforráscsoportot. Hozzon létre egy új erőforráscsoportot, vagy kiválaszthat egy korábban létrehozott.
    - **Hely**: válassza ki azt a helyet, amely az objektum létrejön. Előfordulhat, hogy szeretné kiválasztani a virtuális hálózat található ugyanarra a helyre, de még nem szükséges ehhez.
5. Ha befejezte a szükséges értékeket, válassza ki a **létrehozása** a helyi hálózati átjáró létrehozásához.
6. Ismételje ezeket a lépéseket (1-5) a Azure verem üzemelő példányon.

## <a name="configure-your-connection"></a>A kapcsolat beállítása

A helyszíni hálózaton a helyek közötti kapcsolatok létesítéséhez VPN-eszközre van szükség. Konfigurálja VPN-eszköz kapcsolat nevezzük. A kapcsolat konfigurálásához lesz szüksége:

- Megosztott kulcs. Ez ugyanaz a megosztott kulcs, amelyet a helyek közötti VPN-kapcsolat létrehozásakor ad meg. A példákban alapvető megosztott kulcsot használunk. Javasoljuk egy ennél összetettebb kulcs létrehozását.
- A virtuális hálózati átjáró nyilvános IP-címe. A nyilvános IP-címet az Azure Portalon, valamint a PowerShell vagy a CLI használatával is megtekintheti. A nyilvános IP-címének a VPN-átjáró, az Azure portál használatával, navigáljon a virtuális hálózati átjárók, majd válassza ki az átjáró nevét.

Az alábbi lépések segítségével a virtuális hálózati átjáró és a helyszíni VPN-eszköz közötti pont-pont VPN-kapcsolatot.

1. Válassza ki az Azure-portálon **+ hozzon létre egy erőforrást**.
2. Keresse meg **kapcsolatok**.
3. A **eredmények**, jelölje be **kapcsolatok**.
4. A **kapcsolat**, jelölje be **létrehozása**.
5. A **kapcsolat létrehozása**, a következő beállításokat:

    - **Kapcsolat típusa**: válassza ki a pont-pont (IPSec).
    - **Erőforráscsoport**: a vizsgálati erőforráscsoport kiválasztása.
    - **Virtuális hálózati átjáró**: válassza ki a létrehozott virtuális hálózati átjáró.
    - **Helyi hálózati átjáró**: válassza ki a helyi hálózati átjáró létrehozott.
    - **Kapcsolat neve**: A rendszer automatikusan kitölti a értékeinek két átjáró használatával.
    - **Megosztott kulcs**: ezt az értéket meg kell egyeznie az értéket, amelyet a helyi helyszíni VPN-eszköz alkalmaz. Az oktatóanyag példában "abc123", de lehet (és kell) használható összetettebb. Fontos, hogy ezt az értéket ugyanazt az értéket, amely a VPN-eszköz konfigurálása során meghatározott kell lennie.
    - Az értékek **előfizetés**, **erőforráscsoport**, és **hely** rögzítettek.

6. Válassza ki **OK** a VPN-kapcsolat létrehozásához.

Láthatja, hogy a kapcsolat a **kapcsolatok** a virtuális hálózati átjáró oldalán. Az állapot kerül a *ismeretlen* való *csatlakozás*, majd a *sikeres*.

## <a name="next-steps"></a>További lépések

- Azure Cloud mintájával kapcsolatos további tudnivalókért lásd: [felhőalapú kialakítási mintájával](https://docs.microsoft.com/azure/architecture/patterns).
