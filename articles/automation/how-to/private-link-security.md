---
title: Hálózatok biztonságos csatlakoztatása az Azure privát hivatkozással Azure Automation
description: Hálózatok biztonságos csatlakoztatása az Azure privát hivatkozással Azure Automation
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 07/09/2020
ms.subservice: ''
ms.openlocfilehash: a4985784a17f2e0350a7b2c7a4f62f574862d50c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91714362"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation-preview"></a>Hálózatok biztonságos csatlakoztatása az Azure privát hivatkozásával Azure Automation (előzetes verzió)

Az Azure privát végpont egy hálózati adapter, amely privát és biztonságos módon csatlakoztatja Önt egy Azure privát kapcsolat által működtetett szolgáltatáshoz. A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza az Automation szolgáltatást a VNet. A VNet lévő gépek és az Automation-fiók közötti hálózati forgalom a VNet és a Microsoft gerinc hálózatán található privát kapcsolaton keresztül történik, ami kiküszöböli a nyilvános internetről való kitettséget.

Például van egy olyan VNet, amelyben le van tiltva a kimenő internet-hozzáférés. Az Automation-fiókját azonban saját maga szeretné elérni, és olyan automatizálási funkciókat használhat, mint például a webhookok, az állapot-konfiguráció és a runbook-feladatok hibrid Runbook-feldolgozókon. Emellett azt is szeretné, hogy a felhasználók csak a VNET keresztül férhessenek hozzá az Automation-fiókhoz.  A privát végpontok üzembe helyezése ezeket a célokat éri el.

Ez a cikk a használatáról és az Automation-fiókkal (előzetes verzió) rendelkező privát végpontok beállításával foglalkozik.

![A Azure Automation privát hivatkozásának fogalmi áttekintése](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> A Azure Automation (előzetes verzió) szolgáltatással való privát kapcsolat támogatása csak az Azure kereskedelmi és az Azure US government-felhőkben érhető el.

## <a name="advantages"></a>Előnyök

A privát hivatkozással a következőket teheti:

- A nyilvános hálózati hozzáférés megnyitása nélkül csatlakozhat a Azure Automationhoz.
- A nyilvános hálózati hozzáférés megnyitása nélkül csatlakozhat a Azure Monitor Log Analytics munkaterülethez.

    >[!NOTE]
    >Erre akkor van szükség, ha az Automation-fiókja egy Log Analytics munkaterülethez van csatolva a feladatok adatai továbbításához, és ha engedélyezte az olyan szolgáltatásokat, mint például a Update Management, a Change Tracking és a leltár, az állapot-konfiguráció vagy a Start/Stop VMs during off-hours. További információ a Azure Monitor magánhálózati hivatkozásáról: az [Azure Private link használata a hálózatok biztonságos csatlakoztatásához Azure monitorhoz](../../azure-monitor/platform/private-link-security.md).

- Győződjön meg arról, hogy az Automation-adatai csak a jogosult magánhálózatok használatával érhetők el.
- A privát végponton keresztül összekapcsolt Azure Automation erőforrás definiálásával megakadályozhatja a kiszűrése származó adatok kiépítését.
- Biztonságosan csatlakoztathatja a helyszíni hálózatot, hogy Azure Automation ExpressRoute és privát kapcsolaton keresztül.
- Minden forgalom megtartása az Microsoft Azure gerinc hálózatán belül.

További információ:  [a privát hivatkozás legfontosabb előnyei](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Működés

Azure Automation privát hivatkozás egy vagy több privát végpontot (és így a bennük található virtuális hálózatokat) csatlakoztat az Automation-fiók erőforrásához. Ezek a végpontok olyan gépek, amelyek webhookok használatával indítanak el egy runbook, a hibrid Runbook-feldolgozói szerepkört és a DSC-csomópontokat üzemeltető gépeket.

Miután létrehozta az Automation számára a magánhálózati végpontokat, az összes nyilvános Automation URL-cím, amelyet Ön vagy egy gép közvetlenül tud felvenni, a VNet egy privát végpontra van leképezve.

Az előzetes kiadás részeként egy Automation-fiók nem fér hozzá a privát végponton keresztül védett Azure-erőforrásokhoz. Például Azure Key Vault, Azure SQL, Azure Storage-fiók stb.

### <a name="webhook-scenario"></a>Webhook forgatókönyv

A runbookok elindításához tegye a webhook URL-címét. Az URL-cím például a következőre hasonlít: `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="state-configuration-agentsvc-scenario"></a>Állapot-konfiguráció (agentsvc) forgatókönyv

Az állapot-konfiguráció lehetővé teszi az Azure Configuration Management szolgáltatás használatát, amellyel bármilyen Felhőbeli vagy helyszíni adatközpont csomópontjain írhat, kezelhet és lefordíthatja a PowerShell kívánt állapotú konfigurációs (DSC) konfigurációit.

A számítógépen lévő ügynök regisztrálja a DSC szolgáltatást, majd a szolgáltatás-végpont használatával kéri le a DSC-konfigurációt. Az ügynök szolgáltatás végpontja a következőképpen néz ki: `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` .

A nyilvános & magánhálózati végpont URL-címe azonos lenne, azonban egy magánhálózati IP-címre lesz leképezve, ha a magánhálózati kapcsolat engedélyezve van.

## <a name="planning-based-on-your-network"></a>Tervezés a hálózat alapján

Az Automation-fiók erőforrásának beállítása előtt vegye figyelembe a hálózat elkülönítésének követelményeit. Értékelje ki a virtuális hálózatok hozzáférését a nyilvános internethez, valamint a hozzáférési korlátozásokat az Automation-fiókhoz (beleértve a privát kapcsolati csoport hatókörének beállítását is, hogy az Automation-fiókjával integráltan Azure Monitor naplókat. Emellett az Automation szolgáltatás [DNS-rekordjainak](./automation-region-dns-records.md) felülvizsgálatát is tartalmazza a csomag részeként, hogy a támogatott funkciók a probléma nélkül működjenek.

### <a name="connect-to-a-private-endpoint"></a>Kapcsolódás privát végponthoz

Hozzon létre egy privát végpontot a hálózat összekapcsolásához. Ezt a [Azure Portal Private link Centerben](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)hozhatja létre. Miután a publicNetworkAccess és a privát kapcsolatra vonatkozó módosításokat alkalmazza, akár 35 percet is igénybe vehet, amíg érvénybe lépnek.

Ebben a szakaszban egy privát végpontot fog létrehozni az Automation-fiókhoz.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása > hálózatkezelés > Private link Center (előzetes verzió)** lehetőséget.

2. A **Private link Centerben – áttekintés**, a **szolgáltatáshoz való magánhálózati kapcsolat**létrehozásához válassza az **Indítás**lehetőséget.

3. A **virtuális gép létrehozása – alapismeretek**területen adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. Ezt az előző szakaszban hozta létre.  |
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Név | Adja meg a *PrivateEndpoint*. |
    | Region | Válassza a **YourRegion**lehetőséget. |
    |||

4. Válassza a **Tovább: erőforrás**elemet.

5. A **privát végpont létrehozása – erőforrás**területen adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |Kapcsolati módszer  | Válassza a kapcsolódás egy Azure-erőforráshoz a címtárban lehetőséget.|
    | Előfizetés| Válassza ki előfizetését. |
    | Erőforrás típusa | Válassza a **Microsoft. Automation/automationAccounts**lehetőséget. |
    | Erőforrás |*MyAutomationAccount* kiválasztása|
    |Cél alerőforrás |A forgatókönyvtől függően válassza a *webhook* vagy a *DSCAndHybridWorker* lehetőséget.|
    |||

6. Válassza a **Tovább: konfigurálás**lehetőséget.

7. A **privát végpont létrehozása – konfiguráció**területen adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |**HÁLÓZATKEZELÉS**| |
    | Virtuális hálózat| Válassza a *MyVirtualNetwork*lehetőséget. |
    | Alhálózat | Válassza a *mySubnet*lehetőséget. |
    |**PRIVÁT DNS-INTEGRÁCIÓ**||
    |Integrálás saját DNS-zónával |Válassza az **Igen** lehetőséget. |
    |Privát DNS-zóna |Select *(New) privatelink. Azure – Automation.net* |
    |||

8. Válassza az **Áttekintés + létrehozás** lehetőséget. Az **Áttekintés és létrehozása** lapra kerül, ahol az Azure érvényesíti az Ön konfigurációját.

9. Amikor megjelenik a **Megfelelt az ellenőrzésen** üzenet, válassza a **Létrehozás** lehetőséget.

A Private **link Centerben (előzetes verzió)** válassza a **privát végpontok** lehetőséget a privát kapcsolati erőforrás megtekintéséhez.

![Automation Resource Private-hivatkozás](./media/private-link-security/private-link-automation-resource.png)

Válassza ki az erőforrást az összes adat megtekintéséhez. Ez létrehoz egy új privát végpontot az Automation-fiókjához, és hozzárendeli egy magánhálózati IP-címet a virtuális hálózatból. A **kapcsolatok állapota** **jóváhagyottként**jelenik meg.

Ehhez hasonlóan a rendszer létrehoz egy egyedi teljes tartománynevet (FQDN) az állapot-konfigurációhoz (agentsvc) és a hibrid Runbook-feldolgozói feladatok futtatókörnyezetéhez (jrds). Mindegyikük külön IP-címet kap a VNet, és a **kapcsolatok állapota** **jóváhagyottként**jelenik meg.

Ha a szolgáltatás felhasználójának Azure RBAC engedélyei vannak az Automation-erőforráshoz, akkor választhatják az automatikus jóváhagyási módszert. Ebben az esetben, ha a kérelem eléri az Automation-szolgáltatói erőforrást, nincs szükség beavatkozásra a szolgáltatótól, és a rendszer automatikusan jóváhagyja a kapcsolódást.

## <a name="set-public-network-access-flags"></a>Nyilvános hálózati hozzáférési jelzők beállítása

Beállíthat egy Automation-fiókot az összes nyilvános konfiguráció megtagadásához, és a hálózati biztonság növelése érdekében csak privát végpontokon keresztüli kapcsolatokat engedélyezhet. Ha az Automation-fiókhoz való hozzáférést csak a VNet belül szeretné korlátozni, és nem engedélyezi a hozzáférést a nyilvános internetről, a tulajdonságot beállíthatja a következőre: `publicNetworkAccess` `$false` .

Ha a **nyilvános hálózati hozzáférés** beállítás értéke `$false` , csak a magánhálózati végpontokon keresztüli kapcsolatok engedélyezettek, és az összes nyilvános végponton keresztüli kapcsolat megtagadva egy unathorized hibaüzenettel és a 401-es http-állapottal. 

A következő PowerShell-szkript bemutatja, hogyan `Get` és `Set` a **nyilvános hálózati hozzáférés** tulajdonságot az Automation-fiók szintjén:

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

## <a name="dns-configuration"></a>DNS-konfiguráció

Ha a kapcsolati karakterlánc részeként egy teljes tartománynevet (FQDN) használó privát kapcsolati erőforráshoz csatlakozik, fontos, hogy a DNS-beállításokat helyesen konfigurálja a lefoglalt magánhálózati IP-címhez való feloldáshoz. Előfordulhat, hogy a meglévő Azure-szolgáltatások már rendelkeznek DNS-konfigurációval, ha nyilvános végponton keresztül csatlakoznak. A DNS-konfigurációt felül kell vizsgálni és frissíteni kell, hogy az a privát végponton keresztül kapcsolódjon.

A magánhálózati végponthoz társított hálózati adapter tartalmazza a DNS konfigurálásához szükséges összes információt, beleértve a teljes TARTOMÁNYNEVEt és az adott privát kapcsolati erőforrás számára lefoglalt magánhálózati IP-címeket is.

A következő beállításokkal konfigurálhatja a magánhálózati végpontok DNS-beállításait:

* A gazda fájl használata (csak teszteléshez ajánlott). A gazdagépen található fájl használatával felülbírálhatja a névfeloldást a DNS használatával a névfeloldáshoz.

* Használjon [privát DNS-zónát](../../dns/private-dns-privatednszone.md). Saját DNS-zónák használatával felülbírálhatja egy adott privát végpont DNS-feloldását. A saját DNS-zónák összekapcsolhatók a virtuális hálózattal bizonyos tartományok feloldásához. Ha engedélyezni szeretné, hogy az ügynök a privát végponton keresztül kommunikáljon a virtuális gépen, hozzon létre egy saját DNS rekordot a következőként: `privatelink.azure-automation.net` . Adjon *hozzá egy új DNS-* rekordot a privát végpont IP-címéhez.

* Használja a DNS-továbbítót (nem kötelező). A DNS-továbbító használatával felülbírálhatja egy adott privát kapcsolati erőforrás DNS-feloldását. Ha a [DNS-kiszolgáló](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) egy virtuális hálózaton található, létrehozhat egy DNS-továbbítási szabályt, amely egy magánhálózati DNS-zónát használ, hogy leegyszerűsítse az összes privát kapcsolati erőforrás konfigurációját.

További információ: [Azure Private Endpoint DNS-konfiguráció](../../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>További lépések

További információ a privát végpontról: [Mi az az Azure Private Endpoint?](../../private-link/private-endpoint-overview.md).