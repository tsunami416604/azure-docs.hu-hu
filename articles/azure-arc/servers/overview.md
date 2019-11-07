---
title: Az Azure arc for Servers áttekintése
description: Ismerje meg, hogyan használható az Azure arc a kiszolgálókon az infrastruktúra és az alkalmazások életciklusának automatizálására.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Azure Automation, DSC, PowerShell, a kívánt állapot konfigurálása, frissítés kezelése, változások követése, leltár, runbookok, Python, grafikus, hibrid
ms.date: 11/04/2019
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 57dbfa0c2063c2059955294ccc1337bb76b72614
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606089"
---
# <a name="what-is-azure-arc-for-servers"></a>Mi az Azure arc a kiszolgálókhoz

Az Azure arc for Servers szolgáltatás lehetővé teszi az Azure-on kívüli gépek felügyeletét.
Ha egy nem Azure-beli gép csatlakozik az Azure-hoz, az egy **csatlakoztatott számítógép** lesz, és erőforrásként van kezelve az Azure-ban. Minden **csatlakoztatott számítógép** rendelkezik erőforrás-azonosítóval, egy előfizetéshez tartozó erőforráscsoport részeként, valamint a szabványos Azure-konstrukciók, például a Azure Policy és a címkézés előnyei.

Az Azure-hoz való kapcsolódáshoz minden gépen telepíteni kell egy ügynök-csomagot. A dokumentum további részében részletesebben ismertetjük a folyamatot.

A gépek **csatlakoztatva** vagy **leválasztva** állapotba kerülnek, attól függően, hogy az ügynök hogyan jelentkezett be a közelmúltban. Az egyes bejelentkezéseket szívverésnek nevezzük. Ha egy gép nem jelentkezett be az elmúlt 5 percen belül, az offline állapotba kerül, amíg a kapcsolat vissza nem áll.  <!-- For more information on troubleshooting agent connectivity, see [Troubleshooting Azure Arc for servers](troubleshoot/arc-for-servers.md). -->

![Csatlakoztatott kiszolgálók](./media/overview/arc-for-servers-onboarded-servers.png)

## <a name="clients"></a>Ügyfelek

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A nyilvános előzetes verzióban a következőket támogatjuk:

- Windows Server 2012 R2 és újabb
- Ubuntu 16,04 és 18,04

A nyilvános előzetes kiadás kiértékelésre szolgál, és nem használható a kritikus termelési erőforrások kezeléséhez.

## <a name="azure-subscription-and-service-limits"></a>Azure-előfizetések és-szolgáltatások korlátai

Győződjön meg arról, hogy elolvasta a Azure Resource Manager korlátozásokat, és tervezze meg a csatlakoztatni kívánt gépek számát az [előfizetéshez](../../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager)és az [erőforráscsoportokhöz](../../azure-subscription-service-limits.md#resource-group-limits)tartozó útmutatásnak megfelelően. Alapértelmezés szerint az erőforráscsoport legfeljebb 800-kiszolgálót foglal le.

## <a name="networking-configuration"></a>Hálózati konfiguráció

A telepítés és a futtatókörnyezet során az ügynöknek kapcsolódnia kell az **Azure arc Service-végpontokhoz**. Ha a tűzfal blokkolja a kimenő kapcsolatot, győződjön meg arról, hogy a következő URL-címek nincsenek letiltva alapértelmezés szerint. Minden kapcsolat kimenő az ügynöktől az Azure-ba, és **SSL**-védelemmel van ellátva. Minden forgalmat egy **https** -proxyn keresztül lehet irányítani. Ha engedélyezi azokat az IP-tartományokat vagy tartományneveket, amelyekhez a kiszolgálók csatlakozni tudnak, engedélyeznie kell a 443-es portot a következő szolgáltatási címkékhez és DNS-nevekhez.

Szolgáltatás címkéi:

* AzureActiveDirectory
* AzureTrafficManager

Az egyes szolgáltatási címkék/régiók IP-címeinek listáját lásd: JSON-fájl – [Azure IP-címtartományok és szolgáltatás-címkék – nyilvános felhő](https://www.microsoft.com/download/details.aspx?id=56519). A Microsoft az egyes Azure-szolgáltatásokat és az általa használt IP-tartományokat tartalmazó heti frissítéseket tesz közzé. További részletekért lásd a [szolgáltatás címkéi](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)című témakört.

Ezek a DNS-nevek a szolgáltatási címke IP-címtartomány-információi mellett vannak megadva, mert a szolgáltatások többsége jelenleg nem rendelkezik szolgáltatás-címkézési regisztrációval, és így az IP-címek változhatnak. Ha a tűzfal-konfigurációhoz IP-tartományok szükségesek, akkor a **AzureCloud** szolgáltatás címkét kell használni az összes Azure-szolgáltatáshoz való hozzáférés engedélyezéséhez. Ne tiltsa le ezen URL-címek biztonsági figyelését vagy ellenőrzését, de engedélyezze azokat más internetes forgalomként.

| Tartománykörnyezet | Szükséges Azure-szolgáltatásvégpontok |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Vendégkonfiguráció|
|\* – agentservice-prod-1.azure-automation.net|Vendégkonfiguráció|
|*. his.hybridcompute.azure-automation.net|Hibrid identitási szolgáltatás|

### <a name="installation-network-requirements"></a>Telepítési hálózati követelmények

Töltse le az [Azure Connected Machine Agent csomagot](https://aka.ms/AzureConnectedMachineAgent) a hivatalos terjesztési kiszolgálókról az alábbi helyeknek elérhetőknek kell lenniük a környezetből. Dönthet úgy, hogy letölti a csomagot egy fájlmegosztás számára, és onnan telepíti az ügynököt. Ebben az esetben előfordulhat, hogy a Azure Portalból generált bevezetési parancsfájlt módosítani kell.

Windows:

* `aka.ms`
* `download.microsoft.com`

Linux:

* `aka.ms`
* `packages.microsoft.com`

Tekintse meg a [proxykiszolgáló](quickstart-onboard-powershell.md#proxy-server-configuration)konfigurálása című szakaszt, amely arról nyújt tájékoztatást, hogyan konfigurálhatja az ügynököt a proxy használatára.

## <a name="register-the-required-resource-providers"></a>A szükséges erőforrás-szolgáltatók regisztrálása

Ahhoz, hogy használni lehessen az Azure arc-kiszolgálókat, regisztrálnia kell a szükséges erőforrás-szolgáltatókat.

* **Microsoft. HybridCompute**
* **Microsoft. GuestConfiguration**

Az erőforrás-szolgáltatókat az alábbi parancsokkal regisztrálhatja:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Az erőforrás-szolgáltatókat a portálon is regisztrálhatja a [Azure Portal](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal)alatti lépéseket követve.

## <a name="supported-scenarios"></a>Támogatott forgatókönyvek

A csomópontok regisztrálása után elkezdheti a csomópontok kezelését más Azure-szolgáltatásokkal.

A nyilvános előzetes verzióban a **csatlakoztatott gépeken**a következő forgatókönyvek támogatottak.

## <a name="guest-configuration"></a>Vendégkonfiguráció

Miután csatlakoztatta a gépet az Azure-hoz, hozzárendelheti az Azure-szabályzatokat a **csatlakoztatott gépekhez** ugyanazzal a tapasztalattal, mint a szabályzat-hozzárendelés az Azure Virtual Machines szolgáltatásban.

További információ: [Azure Policy vendég konfigurációjának ismertetése](../../governance/policy/concepts/guest-configuration.md).

A **csatlakoztatott gépekhez** tartozó vendég-konfigurációs ügynök naplói a következő helyen találhatók:

* Windows – `%ProgramFiles%\AzureConnectedMachineAgent\logs\dsc.log`
* Linux: – `/opt/logs/dsc.log`

## <a name="log-analytics"></a>Log Analytics

A [Microsoft monitoring Agent (MMA)](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) által gyűjtött és log Analytics munkaterületen tárolt adatok naplózása mostantól a géphez, például a **ResourceId**-hez kapcsolódó tulajdonságokat is tartalmaz, amelyek az erőforrás-központú napló eléréséhez használhatók.

- Azok a gépek, amelyeken már telepítve van az MMA-ügynök, a frissített felügyeleti csomagokon keresztül engedélyezve lesznek az **Azure arc** funkciói.
- Az [MMA-ügynök 10.20.18011 vagy újabb verziója](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows#agent-and-vm-extension-version) szükséges az Azure arc a kiszolgálók integrálásához.
- [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#log-queries)a naplózási adatként való lekérdezéskor a visszaadott Adatséma tartalmazni fogja a hibrid **ResourceId** az űrlap `/subscriptions/<SubscriptionId/resourceGroups/<ResourceGroup>/providers/Microsoft.HybridCompute/machines/<MachineName>`.

További információ: [Bevezetés a log Analytics használatába Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

<!-- MMA agent version 10.20.18011 and later -->

## <a name="next-steps"></a>További lépések

A számítógépek Azure arc-kiszolgálókkal való összekapcsolásának két módja van.

* **Interaktív módon** – kövesse a [portál](quickstart-onboard-portal.md) rövid útmutatóját egy parancsfájl létrehozásához a portálon, és futtassa azt a gépen. Ez a legjobb megoldás, ha egyszerre egy gépet csatlakoztat.
* **Léptékben** – kövesse a [PowerShell](quickstart-onboard-powershell.md) rövid útmutatóját egy egyszerű szolgáltatásnév létrehozásához, amely nem interaktív módon csatlakozik a gépekhez.
