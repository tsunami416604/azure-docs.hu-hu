---
title: Azure arc for Servers (előzetes verzió) – áttekintés
description: Ismerje meg, hogyan kezelheti az Azure arc for Servers szolgáltatásait az Azure-on kívül üzemeltetett gépek kezelésére, mintha az Azure-erőforrás.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: Azure Automation, DSC, PowerShell, a kívánt állapot konfigurálása, frissítés kezelése, változások követése, leltár, runbookok, Python, grafikus, hibrid
ms.date: 02/03/2020
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: c38bc2d5d9a7e23c0bb3041a5cb3b5cd1da9adb1
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114238"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Mi az Azure arc for Servers (előzetes verzió)

Az Azure arc for Servers (előzetes verzió) lehetővé teszi az Azure-on kívül üzemeltetett Windows-és Linux-gépek felügyeletét a vállalati hálózaton vagy más felhőalapú szolgáltatón, hasonlóan a natív Azure-beli virtuális gépek kezeléséhez. Ha egy hibrid gép csatlakozik az Azure-hoz, az egy csatlakoztatott gép lesz, és erőforrásként van kezelve az Azure-ban. Minden csatlakoztatott számítógép rendelkezik erőforrás-AZONOSÍTÓval, egy előfizetéshez tartozó erőforráscsoport részeként, valamint a szabványos Azure-konstrukciók, például a Azure Policy és a címkék alkalmazásának előnyei.

Ahhoz, hogy ezt a szolgáltatást az Azure-on kívül üzemeltetett hibrid gépekkel is el lehessen juttatni, az Azure-hoz csatlakoztatott számítógép-ügynököt minden olyan gépre telepíteni kell, amelyet az Azure-hoz való csatlakozásra tervez. Ez az ügynök nem nyújt semmilyen egyéb funkciót, és nem helyettesíti az Azure [log Analytics Agent ügynököt](../../azure-monitor/platform/log-analytics-agent.md). A Windows és Linux rendszerhez készült Log Analytics-ügynökre akkor van szükség, ha proaktívan szeretné figyelni a gépen futó operációs rendszert és munkaterheléseket, felügyelheti azt automatizálási runbookok vagy olyan megoldások használatával, mint például a Update Management, vagy más Azure-szolgáltatásokat is használhat, mint például a [Azure Security Center](../../security-center/security-center-intro.md).

>[!NOTE]
>Ez az előzetes kiadás a kiértékelés céljára szolgál, és javasoljuk, hogy ne kezelje a kritikus üzemi gépeket.
>

## <a name="supported-scenarios"></a>Támogatott esetek

Az Azure arc for Servers (előzetes verzió) a következő forgatókönyveket támogatja a csatlakoztatott gépekkel kapcsolatban:

- Az Azure Virtual Machines szolgáltatáshoz tartozó szabályzat-hozzárendeléssel megegyező módon rendeljen hozzá [Azure Policy vendég konfigurációkat](../../governance/policy/concepts/guest-configuration.md) .
- A Log Analytics ügynök által gyűjtött és a Log Analytics munkaterületen tárolt adatok naplózása a gépen a számítógép regisztrálva van, és a gép, például az erőforrás-azonosító, az [erőforrás-környezet](../../azure-monitor/platform/design-logs-deployment.md#access-mode) naplózási hozzáférésének támogatásához használható tulajdonságokat is tartalmaz.

## <a name="supported-regions"></a>Támogatott régiók

Az Azure arc for Servers (előzetes verzió) használata esetén csak bizonyos régiók támogatottak:

- WestUS2
- WestEurope
- WestAsia

## <a name="prerequisites"></a>Előfeltételek

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A Windows és a Linux operációs rendszer következő verziói hivatalosan támogatottak az Azure-beli csatlakoztatott gépi ügynöknél: 

- Windows Server 2012 R2 és újabb
- Ubuntu 16,04 és 18,04

>[!NOTE]
>A Windowshoz készült csatlakoztatott számítógép ügynökének előzetes kiadása csak az angol nyelv használatára konfigurált Windows Servert támogatja.
>

### <a name="required-permissions"></a>Szükséges engedélyek

- A gépek bevezetéséhez Ön az **Azure Connected Machine** bevezetési szerepkör tagja.

- Egy gép olvasásához, módosításához, újbóli előkészítéséhez és törléséhez az **Azure Connected machine erőforrás-rendszergazdai** szerepkör tagja. 

### <a name="azure-subscription-and-service-limits"></a>Azure-előfizetések és-szolgáltatások korlátai

Mielőtt a gépeket az Azure arc for Servers (előzetes verzió) értékre konfigurálja, tekintse át a Azure Resource Manager [előfizetési korlátait](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits---azure-resource-manager) és az [erőforráscsoport korlátait](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) , hogy megtervezze a csatlakoztatni kívánt gépek számát.

### <a name="networking-configuration"></a>Hálózati konfiguráció

A Linux és a Windows rendszerhez csatlakoztatott gépi ügynök a 443-as TCP-porton keresztül kommunikál az Azure-beli ív felé. Ha a gép tűzfalon vagy proxykiszolgálón keresztül csatlakozik az interneten keresztüli kommunikációhoz, tekintse át az alábbi követelményeket a hálózati konfigurációs követelmények megismeréséhez.

Ha a kimenő kapcsolatot a tűzfal vagy a proxykiszolgáló korlátozza, győződjön meg arról, hogy az alább felsorolt URL-címek nincsenek letiltva. Ha csak az ügynöknek a szolgáltatással való kommunikációhoz szükséges IP-tartományokat vagy tartományneveket engedélyezi, akkor a következő szolgáltatási címkékhez és URL-címekhez is engedélyeznie kell a hozzáférést.

Szolgáltatás címkéi:

- AzureActiveDirectory
- AzureTrafficManager

URLs

| Ügynök erőforrása | Leírás |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Vendégkonfiguráció|
|\* – agentservice-prod-1.azure-automation.net|Vendégkonfiguráció|
|*. his.hybridcompute.azure-automation.net|Hibrid identitási szolgáltatás|

Az egyes szolgáltatási címkék/régiók IP-címeinek listáját lásd: JSON-fájl – [Azure IP-címtartományok és szolgáltatás-címkék – nyilvános felhő](https://www.microsoft.com/download/details.aspx?id=56519). A Microsoft az egyes Azure-szolgáltatásokat és az általa használt IP-tartományokat tartalmazó heti frissítéseket tesz közzé. További információkért tekintse át a [szolgáltatás címkéit](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Az előző táblázatban szereplő URL-címeket a szolgáltatási címke IP-címtartomány információi mellett is meg kell adni, mivel a szolgáltatások többsége jelenleg nem rendelkezik a szolgáltatási címke regisztrálásával. Ennek megfelelően az IP-címek változhatnak. Ha a tűzfal-konfigurációhoz IP-címtartományok szükségesek, akkor a **AzureCloud** szolgáltatás címkét kell használni az összes Azure-szolgáltatás elérésének engedélyezéséhez. Ne tiltsa le ezen URL-címek biztonsági figyelését vagy ellenőrzését, és engedélyezze azokat más internetes forgalomként.

### <a name="register-azure-resource-providers"></a>Azure-erőforrás-szolgáltatók regisztrálása

Az Azure arc for Servers (előzetes verzió) az előfizetés alábbi Azure-erőforrás-szolgáltatói függ a szolgáltatás használatához:

- **Microsoft. HybridCompute**
- **Microsoft. GuestConfiguration**

Ha nincsenek regisztrálva, akkor az alábbi parancsokkal regisztrálhat:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Az erőforrás-szolgáltatókat a Azure Portal is regisztrálhatja a [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)szakaszban leírt lépéseket követve.

## <a name="connected-machine-agent"></a>Csatlakoztatott számítógép ügynöke

A Windows és a Linux rendszerhez készült Azure Connected Machine Agent csomagot az alább felsorolt helyekről töltheti le.

- [Windows agent Windows Installer csomag](https://aka.ms/AzureConnectedMachineAgent) a Microsoft letöltőközpontból.
- A Linux-ügynök csomagja a Microsoft [csomag adattárában](https://packages.microsoft.com/) található, az elosztás előnyben részesített csomag formátuma alapján (. RPM vagy. DEB).

>[!NOTE]
>Ebben az előzetes verzióban csak egy csomag jelenik meg, amely Ubuntu 16,04 vagy 18,04 esetén megfelelő.

## <a name="install-and-configure-agent"></a>Telepítse és konfigurálja az ügynököt

A hibrid környezetben az Azure-ban közvetlenül csatlakoztatható gépek a követelményektől függően különböző módszerekkel végezhetők el. Az alábbi táblázat az egyes módszereket ismerteti, amelyek alapján meghatározhatja, hogy melyik a legmegfelelőbb a szervezet számára.

| Módszer | Leírás |
|--------|-------------|
| Interaktív módon | Manuálisan telepítse az ügynököt egy vagy több gépen a [gépek Azure Portal-ból való összekapcsolása](quickstart-onboard-portal.md)című témakör lépéseit követve.<br> A Azure Portal létrehozhat egy parancsfájlt, és végrehajthatja azt a gépen, hogy automatizálja az ügynök telepítésének és konfigurálásának lépéseit.|
| Skálán | Telepítse és konfigurálja az ügynököt több gépen a [számítógépek összekapcsolását követően egy egyszerű szolgáltatásnév használatával](quickstart-onboard-powershell.md).<br> Ez a metódus létrehoz egy egyszerű szolgáltatást, amely nem interaktív módon csatlakozik a gépekhez.|


## <a name="next-steps"></a>Következő lépések

- Ha meg szeretné kezdeni a kiszolgálók Azure-ív kiértékelését (előzetes verzió), kövesse a [hibrid gépek az Azure-hoz való összekapcsolását ismertető cikket a Azure Portal](quickstart-onboard-portal.md). 