---
title: Azure Arc kiszolgálókhoz (előzetes verzió) – áttekintés
description: Ismerje meg, hogyan használhatja az Azure Arc kiszolgálók kezelésére gépek, amelyek az Azure-on kívül üzemeltetett, mintha egy Azure-erőforrás.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: Azure automation, DSC, powershell, kívánt állapotkonfiguráció, frissítéskezelés, változáskövetés, leltár, runbookok, python, grafikus, hibrid
ms.date: 03/24/2020
ms.topic: overview
ms.openlocfilehash: 5fa39028f1041a063bab295adabf8145a8b46ae4
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308773"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Mi az Azure Arc kiszolgálókhoz (előzetes verzió)

Az Azure Arc kiszolgálókhoz (előzetes verzió) lehetővé teszi az Azure-on kívüli üzemeltetett Windows- és Linux-gépek kezelését a vállalati hálózaton vagy más felhőszolgáltatón, hasonlóan a natív Azure virtuális gépek kezeléséhez. Amikor egy hibrid gép csatlakozik az Azure-hoz, egy csatlakoztatott géplesz, és erőforrásként kezeli az Azure-ban. Minden csatlakoztatott gép rendelkezik egy erőforrás-azonosítóval, egy előfizetésen belüli erőforráscsoport részeként kezelik, és a szabványos Azure-konstrukciók, például az Azure Policy és a címkék alkalmazása előnyeit élvezi.

Ahhoz, hogy ezt a felhasználói élményt az Azure-on kívül üzemeltetett hibrid gépekkel nyújtsa, az Azure Connected Machine ügynököt minden olyan gépen telepíteni kell, amelyet az Azure-hoz való csatlakozásra tervez. Ez az ügynök nem biztosít semmilyen más funkciót, és nem helyettesíti az Azure [Log Analytics-ügynököt.](../../azure-monitor/platform/log-analytics-agent.md) A Windows és Linux log analytics-ügynöke akkor szükséges, ha proaktív módon szeretné figyelni a számítógépen futó operációs rendszert és számítási feladatokat, kezelni szeretné az Automation runbookok vagy megoldások, például az Update Management használatával, vagy más Azure-szolgáltatásokat, például az [Azure Security Centert.](../../security-center/security-center-intro.md)

>[!NOTE]
>Ez az előzetes verzió kiértékelési célokra készült, és azt javasoljuk, hogy ne kezelje a kritikus éles gépek.
>

## <a name="supported-scenarios"></a>Támogatott esetek

Az Azure Arc kiszolgálókhoz (előzetes verzió) a következő forgatókönyveket támogatja a csatlakoztatott gépekkel:

- [Az Azure Policy vendégkonfigurációihoz](../../governance/policy/concepts/guest-configuration.md) ugyanazzal a felhasználói felületre, mint az Azure virtuális gépek házirend-hozzárendelése.
- A Log Analytics-ügynök által gyűjtött és a Log Analytics-munkaterületen tárolt naplóadatok, amelyeket a gép most már tartalmaz a gépre jellemző tulajdonságokat, például az erőforrás-azonosítót, amely az [erőforrás-környezet](../../azure-monitor/platform/design-logs-deployment.md#access-mode) napló-hozzáférés támogatására használható.

## <a name="supported-regions"></a>Támogatott régiók

Az Azure Arc kiszolgálók (előzetes verzió) esetén csak bizonyos régiók támogatottak:

- Nyugat-us2
- WestEurope
- Nyugat-Ázsia

A legtöbb esetben a telepítési parancsfájl létrehozásakor kiválasztott hely nek a gép helyéhez földrajzilag az Azure-régiónak kell lennie. Az inkett adatok az Azure-földrajzi helyen tárolják a megadott régiót, ami szintén hatással lehet a választott régióra, ha rendelkezik az adatok tárolási követelményeivel. Ha az Azure-régió, amelyhez a gép csatlakozik, hatással van egy kimaradás, a csatlakoztatott gép nem érinti, de az Azure-t használó felügyeleti műveletek nem hajthatók végre. Regionális kimaradás esetén a rugalmasság érdekében, ha több olyan hellyel rendelkezik, amelyek földrajzilag redundáns szolgáltatást biztosítanak, a legjobb, ha az egyes helyeken lévő gépeket egy másik Azure-régióhoz csatlakoztatja.

## <a name="prerequisites"></a>Előfeltételek

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A Windows és linux os operációs rendszer következő verziói hivatalosan támogatottak az Azure Connected Machine ügynök számára: 

- Windows Server 2012 R2 és újabb verziók (a Windows Server Core rendszerrel együtt)
- Ubuntu 16.04 és 18.04
- CentOS Linux 7
- SUSE Linux Enterprise Server (SLES) 15
- Red Hat Enterprise Linux (RHEL) 7
- Amazon Linux 2

>[!NOTE]
>A Windows csatlakoztatott számítógép-ügynök e előzetes verziója csak az angol nyelv használatára konfigurált Windows Server rendszert támogatja.
>

### <a name="required-permissions"></a>Szükséges engedélyek

- A beépített gépekhez az Azure **Connected Machine bevezetési** szerepkör tagja.

- A számítógép olvasásához, módosításához, újbóli fedélzeti állapotba hozásában és törléséhez az **Azure Connected Machine Resource Administrator** szerepkör tagja. 

### <a name="azure-subscription-and-service-limits"></a>Az Azure előfizetési és szolgáltatási korlátai

Mielőtt konfigurálja a gépeket az Azure Arc kiszolgálókhoz (előzetes verzió), tekintse át az Azure Resource Manager [előfizetési korlátok](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) és [erőforráscsoport-korlátok](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) megtervezheti a csatlakoztatott gépek száma.

## <a name="tls-12-protocol"></a>TLS 1.2 protokoll

Az Azure-ba történő átvitel során az adatok biztonságának biztosítása érdekében javasoljuk, hogy konfigurálja a gépet a Transport Layer Security (TLS) 1.2 használatára. A TLS/Secure Sockets Layer (SSL) régebbi verziói sebezhetőnek bizonyultak, és bár jelenleg is működnek a visszamenőleges kompatibilitás érdekében, **nem ajánlottak.** 

|Platform/nyelv | Támogatás | További információ |
| --- | --- | --- |
|Linux | A Linux disztribúciók általában az [OpenSSL-re](https://www.openssl.org) támaszkodnak a TLS 1.2 támogatáshoz. | Ellenőrizze az [OpenSSL Changelog-ot,](https://www.openssl.org/news/changelog.html) hogy az OpenSSL verziója támogatott-e.|
| Windows Server 2012 R2 és újabb verziók | Alapértelmezés szerint támogatott és engedélyezett. | Annak ellenőrzése, hogy továbbra is az [alapértelmezett beállításokat](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)használja.|

### <a name="networking-configuration"></a>Hálózati konfiguráció

A Csatlakoztatott gép ügynök linuxos és windowsos kommunikál biztonságosan Azure Arc a TCP-porton keresztül 443. Ha a számítógép tűzfalon vagy proxykiszolgálón keresztül csatlakozik az interneten keresztüli kommunikációhoz, tekintse át az alábbi követelményeket a hálózati konfigurációs követelmények megértéséhez.

Ha a tűzfal vagy a proxykiszolgáló korlátozza a kimenő kapcsolatot, győződjön meg arról, hogy az alább felsorolt URL-címek nincsenek letiltva. Ha csak az ügynök nek a szolgáltatással való kommunikációhoz szükséges IP-tartományokat vagy tartományneveket engedélyezi, akkor a következő szolgáltatáscímkékhez és URL-címekhez is hozzáférést kell biztosítania.

Szolgáltatás címkék:

- AzureActiveDirectory
- AzureTrafficManager

Urls:

| Ügynök erőforrása | Leírás |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Vendégkonfiguráció|
|*-agentservice-prod-1.azure-automation.net|Vendégkonfiguráció|
|*.his.hybridcompute.azure-automation.net|Hibrid identitásszolgáltatás|

Az egyes szolgáltatáscímkékhez/-régiókhoz rendelt IP-címek listáját a JSON-fájl – [Azure IP-tartományok és szolgáltatáscímkék – nyilvános felhő tartalmazza.](https://www.microsoft.com/download/details.aspx?id=56519) A Microsoft heti frissítéseket tesz közzé, amelyek tartalmazzák az egyes Azure-szolgáltatások és az általa használt IP-tartományokat. További információért tekintse át [a Szolgáltatás címkéket.](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)

Az előző táblázatban szereplő URL-címekre a Szolgáltatáscímke IP-címtartományadatai mellett van szükség, mivel a szolgáltatások többsége jelenleg nem rendelkezik service tag regisztrációval. Mint ilyen, az IP-címek változhatnak. Ha a tűzfal konfigurációjához IP-címtartományok szükségesek, akkor az **AzureCloud** szolgáltatáscímkét kell használni az összes Azure-szolgáltatás elérésének engedélyezéséhez. Ne tiltsa le a biztonsági figyelést vagy az URL-ek ellenőrzését, ne tegye lehetővé számukra a többi internetes forgalmat.

### <a name="register-azure-resource-providers"></a>Regisztrálja az Azure-erőforrás-szolgáltatókat

Az Azure Arc kiszolgálókhoz (előzetes verzió) a következő Azure-erőforrás-szolgáltatóktól függ az előfizetésben a szolgáltatás használatához:

- **Microsoft.HybridCompute**
- **Microsoft.GuestKonfiguráció**

Ha nincsenek regisztrálva, a következő parancsokkal regisztrálhatja őket:

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

Az Azure Portalon is regisztrálhatja az erőforrás-szolgáltatókat az [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)on leírt lépéseket követve.

## <a name="connected-machine-agent"></a>Csatlakoztatott gép ügynöke

Az Azure Connected Machine ügynökcsomag Windows és Linux az alább felsorolt helyekről töltheti le.

- [Windows ügynök Windows Installer csomag](https://aka.ms/AzureConnectedMachineAgent) a Microsoft letöltőközpontból.
- A Linux-ügynökcsomag a Microsoft [csomagtárából](https://packages.microsoft.com/) kerül terjesztésre a terjesztési (. RPM vagy . DEB).

>[!NOTE]
>Az előnézet során csak egy csomag jelent meg, amely alkalmas az Ubuntu 16.04 vagy az 18.04 számára.

A Windows és Linux Azure Connected Machine ügynök manuálisan vagy automatikusan frissíthető a legújabb kiadásra a követelményektől függően. További információ: [itt](manage-agent.md).

### <a name="agent-status"></a>Ügynök állapota

A csatlakoztatott gép ügynök e-mailben normál szívverési üzenetet küld a szolgáltatásnak. Ha a szolgáltatás leállítja a szívveréses üzenetek fogadását egy gépről, a rendszer offline állapotúnak tekinti a gépet, és az állapot 15–30 percen belül megszakad a portálon **leválasztva** állapotra. Amikor a csatlakoztatott számítógép ügynöke további szívverési üzenetet kap, az állapota automatikusan **Csatlakoztatva**állapotra változik.

## <a name="install-and-configure-agent"></a>Ügynök telepítése és konfigurálása

A hibrid környezetben lévő gépek közvetlen csatlakoztatása az Azure-ral a követelményektől függően különböző módszerekkel valósítható meg. Az alábbi táblázat kiemeli az egyes módszereket annak meghatározására, hogy melyik működik a legjobban a szervezet számára.

| Módszer | Leírás |
|--------|-------------|
| Interaktívan | Manuálisan telepítse az ügynököt egyetlen vagy kis számú gépre az [Azure Portalról származó gépek csatlakoztatása](onboard-portal.md)című témakör lépéseit követve.<br> Az Azure Portalon létrehozhat egy parancsfájlt, és végrehajthatja azt a gépen az ügynök telepítési és konfigurációs lépéseinek automatizálásához.|
| Méretarányban | Telepítse és konfigurálja az ügynököt több gépre a [Kapcsológépek egyszerű szolgáltatás használatával történő összekapcsolása után.](onboard-service-principal.md)<br> Ez a módszer létrehoz egy egyszerű szolgáltatás, hogy csatlakoztassa a gépek nem interaktív módon.|
| Méretarányban | Telepítse és konfigurálja az ügynököt több gépre a [Windows PowerShell DSC használatával](onboard-dsc.md)című módszert követve.<br> Ez a módszer egy egyszerű szolgáltatás segítségével csatlakoztatja a gépeket nem interaktívan a PowerShell DSC-vel. |

## <a name="next-steps"></a>További lépések

- Az Azure Arc kiszolgálókra vonatkozó kiértékelésének megkezdéséhez (előzetes verzió) kövesse a [Hibrid gépek csatlakoztatása az Azure-hoz az Azure portálról című cikket.](onboard-portal.md) 
