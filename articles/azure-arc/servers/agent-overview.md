---
title: A csatlakoztatott számítógép Windows-ügynökének áttekintése
description: Ez a cikk részletes áttekintést nyújt az Azure arc használatára képes kiszolgálók (előzetes verzió) ügynökről, amely támogatja a hibrid környezetekben üzemeltetett virtuális gépek figyelését.
ms.date: 08/06/2020
ms.topic: conceptual
ms.openlocfilehash: d922652537034bef258c5bcde78fb178b092ed16
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212985"
---
# <a name="overview-of-azure-arc-enabled-servers-preview-agent"></a>Az Azure arc-kompatibilis kiszolgálók (előzetes verzió) ügynökének áttekintése

Az Azure arc-kompatibilis kiszolgálók (előzetes verzió) csatlakoztatott gépi ügynök lehetővé teszi az Azure-on kívül üzemeltetett Windows-és Linux-gépek felügyeletét a vállalati hálózaton vagy más felhőalapú szolgáltatón. Ez a cikk részletes áttekintést nyújt az ügynökről, a rendszerről és a hálózati követelményekről, valamint a különböző üzembe helyezési módszerekről.

## <a name="agent-component-details"></a>Ügynök-összetevő részletei

Az Azure-beli csatlakoztatott gépi ügynök csomag számos logikai összetevőt tartalmaz, amelyek együtt vannak csomagolva.

* A hibrid példány metaadatainak szolgáltatása (HIMDS) kezeli az Azure-hoz és a csatlakoztatott gép Azure-identitásához való kapcsolódást.

* A vendég konfigurációs ügynök a vendég házirend-és a vendég-konfigurációs funkciókat biztosítja, például annak felmérését, hogy a gép megfelel-e a szükséges házirendeknek.

    A leválasztott gép Azure Policy [vendég konfigurációjának](../../governance/policy/concepts/guest-configuration.md) következő viselkedését vegye figyelembe:

    * A leválasztott gépeket megcélozó vendég-konfigurációs házirend-hozzárendelés nem érinti.
    * A vendég-hozzárendelést a rendszer 14 napig helyileg tárolja. A 14 napos időszakon belül, ha a csatlakoztatott számítógép ügynöke újracsatlakozik a szolgáltatáshoz, a rendszer újraalkalmazza a házirend-hozzárendeléseket.
    * A hozzárendelések 14 nap után törlődnek, és a 14 napos időszak után nem lesznek hozzárendelve a géphez.

* A bővítmény ügynöke kezeli a virtuálisgép-bővítményeket, beleértve a telepítést, az eltávolítást és a frissítést. A bővítmények az Azure-ból tölthetők le, és a mappába kerülnek a `%SystemDrive%\AzureConnectedMachineAgent\ExtensionService\downloads` Windowsban, és a Linux rendszerhez `/opt/GC_Ext/downloads` . Windows rendszeren a bővítmény a következő elérési útra van telepítve, `%SystemDrive%\Packages\Plugins\<extension>` és Linux rendszeren a bővítmény telepítve van `/var/lib/waagent/<extension>` .

## <a name="download-agents"></a>Ügynökök letöltése

A Windows és a Linux rendszerhez készült Azure Connected Machine Agent csomagot az alább felsorolt helyekről töltheti le.

* [Windows agent Windows Installer csomag](https://aka.ms/AzureConnectedMachineAgent) a Microsoft letöltőközpontból.

* A Linux-ügynök csomagja a Microsoft [csomag adattárában](https://packages.microsoft.com/) található, az elosztás előnyben részesített csomag formátuma alapján (. RPM vagy. DEB).

A Windows és a Linux rendszerhez készült Azure-beli csatlakoztatott gépi ügynök a követelményektől függően manuálisan vagy automatikusan is frissíthető a legújabb kiadásra. További információ: [itt](manage-agent.md).

## <a name="prerequisites"></a>Előfeltételek

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A Windows és a Linux operációs rendszer következő verziói hivatalosan támogatottak az Azure-beli csatlakoztatott gépi ügynöknél: 

- Windows Server 2012 R2 és újabb verziók (beleértve a Windows Server Core-t)
- Ubuntu 16,04 és 18,04 (x64)
- CentOS Linux 7 (x64)
- SUSE Linux Enterprise Server (SLES) 15 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

>[!NOTE]
>A Windowshoz készült csatlakoztatott számítógép ügynökének előzetes kiadása csak az angol nyelv használatára konfigurált Windows Servert támogatja.
>

### <a name="required-permissions"></a>Szükséges engedélyek

* A gépek bevezetéséhez Ön az **Azure Connected Machine** bevezetési szerepkör tagja.

* Egy gép olvasásához, módosításához, újravezetéséhez és törléséhez az **Azure Connected machine erőforrás-rendszergazdai** szerepkör tagja. 

### <a name="azure-subscription-and-service-limits"></a>Azure-előfizetések és-szolgáltatások korlátai

Mielőtt a gépeket az Azure arc-kompatibilis kiszolgálókhoz (előzetes verzió) konfigurálja, tekintse át a Azure Resource Manager [előfizetési korlátait](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) és az [erőforráscsoport korlátait](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) , hogy megtervezze a csatlakoztatni kívánt gépek számát.

### <a name="transport-layer-security-12-protocol"></a>Transport Layer Security 1,2 protokoll

Az Azure-ba irányuló adatátvitel biztonságának biztosítása érdekében határozottan javasoljuk, hogy a gépet a Transport Layer Security (TLS) 1,2 használatára konfigurálja. A TLS/SSL (SSL) régebbi verziói sebezhetőnek találták, és miközben jelenleg is működnek a visszamenőleges kompatibilitás érdekében, **nem ajánlottak**.

|Platform/nyelv | Támogatás | További információ |
| --- | --- | --- |
|Linux | A Linux-disztribúciók általában az [OpenSSL](https://www.openssl.org) -t használják a TLS 1,2 támogatásához. | Ellenőrizze az OpenSSL- [changelog](https://www.openssl.org/news/changelog.html) , hogy az OpenSSL verziója támogatott-e.|
| Windows Server 2012 R2 és újabb | Támogatott, és alapértelmezés szerint engedélyezve van. | Annak megerősítéséhez, hogy továbbra is az [alapértelmezett beállításokat](/windows-server/security/tls/tls-registry-settings)használja.|

### <a name="networking-configuration"></a>Hálózatkezelési konfiguráció

A Linux és a Windows rendszerhez csatlakoztatott gépi ügynök a 443-as TCP-porton keresztül kommunikál az Azure-beli ív felé. Ha a gép tűzfalon vagy proxykiszolgálón keresztül csatlakozik az interneten keresztüli kommunikációhoz, tekintse át az alábbi követelményeket a hálózati konfigurációs követelmények megismeréséhez.

Ha a kimenő kapcsolatot a tűzfal vagy a proxykiszolgáló korlátozza, győződjön meg arról, hogy az alább felsorolt URL-címek nincsenek letiltva. Ha csak az ügynöknek a szolgáltatással való kommunikációhoz szükséges IP-tartományokat vagy tartományneveket engedélyezi, akkor a következő szolgáltatási címkékhez és URL-címekhez is engedélyeznie kell a hozzáférést.

Szolgáltatás címkéi:

* AzureActiveDirectory
* AzureTrafficManager

URLs

| Ügynök erőforrása | Description |
|---------|---------|
|`management.azure.com`|Azure Resource Manager|
|`login.windows.net`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`agentserviceapi.azure-automation.net`|Vendégkonfiguráció|
|`*-agentservice-prod-1.azure-automation.net`|Vendégkonfiguráció|
|`*.guestconfiguration.azure.com` |Vendégkonfiguráció|
|`*.his.arc.azure.com`|Hibrid identitási szolgáltatás|

Az egyes szolgáltatási címkék/régiók IP-címeinek listáját lásd: JSON-fájl – [Azure IP-címtartományok és szolgáltatás-címkék – nyilvános felhő](https://www.microsoft.com/download/details.aspx?id=56519). A Microsoft az egyes Azure-szolgáltatásokat és az általa használt IP-tartományokat tartalmazó heti frissítéseket tesz közzé. További információkért tekintse át a [szolgáltatás címkéit](../../virtual-network/security-overview.md#service-tags).

Az előző táblázatban szereplő URL-címeket a szolgáltatási címke IP-címtartomány információi mellett is meg kell adni, mivel a szolgáltatások többsége jelenleg nem rendelkezik a szolgáltatási címke regisztrálásával. Ennek megfelelően az IP-címek változhatnak. Ha a tűzfal-konfigurációhoz IP-címtartományok szükségesek, akkor a **AzureCloud** szolgáltatás címkét kell használni az összes Azure-szolgáltatás elérésének engedélyezéséhez. Ne tiltsa le ezen URL-címek biztonsági figyelését vagy ellenőrzését, és engedélyezze azokat más internetes forgalomként.

### <a name="register-azure-resource-providers"></a>Azure-erőforrás-szolgáltatók regisztrálása

Az Azure arc-kompatibilis kiszolgálók (előzetes verzió) az előfizetés alábbi Azure-erőforrás-szolgáltatói alapján használhatók a szolgáltatás használatához:

* **Microsoft. HybridCompute**
* **Microsoft. GuestConfiguration**

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

## <a name="installation-and-configuration"></a>Telepítés és konfigurálás

A hibrid környezetben az Azure-ban közvetlenül csatlakoztatható gépek a követelményektől függően különböző módszerekkel végezhetők el. Az alábbi táblázat az egyes módszereket ismerteti, amelyek alapján meghatározhatja, hogy melyik a legmegfelelőbb a szervezet számára.

| Metódus | Leírás |
|--------|-------------|
| Interaktív módon | Manuálisan telepítse az ügynököt egy vagy több gépen a [gépek Azure Portal-ból való összekapcsolása](onboard-portal.md)című témakör lépéseit követve.<br> A Azure Portal létrehozhat egy parancsfájlt, és végrehajthatja azt a gépen, hogy automatizálja az ügynök telepítésének és konfigurálásának lépéseit.|
| Skálán | Telepítse és konfigurálja az ügynököt több gépen a [számítógépek összekapcsolását követően egy egyszerű szolgáltatásnév használatával](onboard-service-principal.md).<br> Ez a metódus létrehoz egy egyszerű szolgáltatást, amely nem interaktív módon csatlakozik a gépekhez.|
| Skálán | Telepítse és konfigurálja az ügynököt több gépre, a módszert követve a [Windows POWERSHELL DSC használatával](onboard-dsc.md).<br> Ez a metódus egyszerű szolgáltatásnév használatával csatlakozik a gépekhez a PowerShell DSC-vel nem interaktív módon. |

## <a name="connected-machine-agent-technical-overview"></a>Csatlakoztatott gépi ügynök – technikai áttekintés

### <a name="windows-agent-installation-details"></a>A Windows-ügynök telepítésének részletei

A Windows rendszerhez készült csatlakoztatott számítógép-ügynök a következő három módszer egyikével telepíthető:

* Kattintson duplán a fájlra `AzureConnectedMachineAgent.msi` .
* Manuálisan futtassa a Windows Installer csomagot `AzureConnectedMachineAgent.msi` a parancs-rendszerhéjból.
* Egy PowerShell-munkamenetből egy parancsfájlból álló metódus használatával.

A Windowshoz készült csatlakoztatott számítógép-ügynök telepítése után a rendszer a következő további rendszerszintű konfigurációs módosításokat alkalmazza.

* A telepítés során a következő telepítési mappák jönnek létre.

    |Mappa |Description |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |Az ügynök által támogatott fájlokat tartalmazó alapértelmezett telepítési útvonal.|
    |%ProgramData%\AzureConnectedMachineAgent |Az ügynök konfigurációs fájljait tartalmazza.|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |A beszerzett jogkivonatokat tartalmazza.|
    |%ProgramData%\AzureConnectedMachineAgent\Config |Az ügynök konfigurációs fájlját tartalmazza `agentconfig.json` , amely rögzíti a regisztrációs adatokat a szolgáltatással.|
    |%SystemDrive%\Program Files\ArcConnectedMachineAgent\ExtensionService\GC | A vendég konfigurációs ügynök fájljait tartalmazó telepítési útvonal. |
    |%ProgramData%\GuestConfig |Az Azure-ból származó (alkalmazott) szabályzatokat tartalmazza.|
    |%SystemDrive%\AzureConnectedMachineAgent\ExtensionService\downloads | A bővítmények letölthetők az Azure-ból, és ide másolhatók.|

* Az ügynök telepítése során a következő Windows-szolgáltatások jönnek létre a célszámítógépen.

    |Szolgáltatásnév |Megjelenített név |Folyamatnév |Description |
    |-------------|-------------|-------------|------------|
    |himds |Azure Hybrid Instance Metadata Service |himds.exe |Ez a szolgáltatás implementálja az Azure-példány metaadatainak szolgáltatását (IMDS) az Azure-hoz és a csatlakoztatott gép Azure-identitásához való csatlakozás kezeléséhez.|
    |DscService |Vendég konfigurációs szolgáltatás |dsc_service.exe |A kívánt állapot-konfiguráció (DSC v2) az Azure-ban használt, a vendég házirend megvalósítására szolgáló kód.|

* Az ügynök telepítése során az alábbi környezeti változók jönnek létre.

    |Name |Alapértelmezett érték |Description |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Több naplófájl is elérhető a hibaelhárításhoz. Ezeket a következő táblázat ismerteti.

    |Napló |Description |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |Az ügynökök (HIMDS) szolgáltatás és az Azure interakciójának adatait rögzíti.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |A azcmagent eszköz parancsainak kimenetét tartalmazza a részletes (-v) argumentum használatakor.|
    |%ProgramData%\GuestConfig\ gc_agent_logs \ gc_agent. log |A DSC szolgáltatási tevékenység részleteit rögzíti,<br> különösen a HIMDS szolgáltatás és a Azure Policy közötti kapcsolat.|
    |% ProgramData% \GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |A DSC szolgáltatás telemetria és részletes naplózási adatait rögzíti.|
    |%SystemDrive%\ProgramData\GuestConfig\ ext_mgr_logs|A bővítmény ügynök összetevőjének adatait rögzíti.|
    |%SystemDrive%\ProgramData\GuestConfig\ extension_logs\<Extension>|A telepített bővítmény adatait rögzíti.|

* Létrejön a helyi biztonsági csoport **hibrid ügynök bővítményének alkalmazásai** .

* Az ügynök eltávolítása során a rendszer nem távolítja el a következő összetevőket.

    * %ProgramFiles%\AzureConnectedMachineAgent\Logs
    * %ProgramData%\AzureConnectedMachineAgent és alkönyvtárak
    * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Linux-ügynök telepítésének részletei

A Linux rendszerhez csatlakoztatott számítógép-ügynök a terjesztés előnyben részesített csomag formátumban van megadva (. RPM vagy. DEB), amely a Microsoft [Package adattárában](https://packages.microsoft.com/)található. Az ügynök telepítve van és konfigurálva van a shell script Bundle [Install_linux_azcmagent. sh](https://aka.ms/azcmagent).

A Linux rendszerhez készült csatlakoztatott gépi ügynök telepítése után a következő további rendszerszintű konfigurációs módosítások lesznek alkalmazva.

* A telepítés során a következő telepítési mappák jönnek létre.

    |Mappa |Description |
    |-------|------------|
    |/var/opt/azcmagent/ |Az ügynök által támogatott fájlokat tartalmazó alapértelmezett telepítési útvonal.|
    |/opt/azcmagent/ |
    |/opt/GC_Ext | A vendég konfigurációs ügynök fájljait tartalmazó telepítési útvonal.|
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |A beszerzett jogkivonatokat tartalmazza.|
    |/var/lib/GuestConfig |Az Azure-ból származó (alkalmazott) szabályzatokat tartalmazza.|
    |/opt/GC_Ext/downloads|A bővítmények letölthetők az Azure-ból, és ide másolhatók.|

* Az ügynök telepítése során a következő démonok jönnek létre a célszámítógépen.

    |Szolgáltatásnév |Megjelenített név |Folyamatnév |Description |
    |-------------|-------------|-------------|------------|
    |himdsd. Service |Azure Hybrid Instance Metadata Service |/opt/azcmagent/bin/himds |Ez a szolgáltatás implementálja az Azure-példány metaadatainak szolgáltatását (IMDS) az Azure-hoz és a csatlakoztatott gép Azure-identitásához való csatlakozás kezeléséhez.|
    |DSCD. Service |Vendég konfigurációs szolgáltatás |/opt/DSC/dsc_linux_service |Ez az Azure-ban a kívánt State Configuration (DSC v2) kód, amely a vendég házirend megvalósítására szolgál.|

* Több naplófájl is elérhető a hibaelhárításhoz. Ezeket a következő táblázat ismerteti.

    |Napló |Description |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |Az ügynökök (HIMDS) szolgáltatás és az Azure interakciójának adatait rögzíti.|
    |/var/opt/azcmagent/log/azcmagent.log |A azcmagent eszköz parancsainak kimenetét tartalmazza a részletes (-v) argumentum használatakor.|
    |/opt/logs/dsc.log |A DSC szolgáltatási tevékenység részleteit rögzíti,<br> különösen a himds szolgáltatás és a Azure Policy közötti kapcsolat.|
    |/opt/logs/dsc.telemetry.txt |A DSC szolgáltatás telemetria és részletes naplózási adatait rögzíti.|
    |/var/lib/GuestConfig/ext_mgr_logs |A bővítmény ügynök összetevőjének adatait rögzíti.|
    |/var/log/GuestConfig/extension_logs|A telepített bővítmény adatait rögzíti.|

* Az ügynök telepítése során az alábbi környezeti változók jönnek létre. Ezek a változók a ben vannak beállítva `/lib/systemd/system.conf.d/azcmagent.conf` .

    |Name |Alapértelmezett érték |Description |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Az ügynök eltávolítása során a rendszer nem távolítja el a következő összetevőket.

    * /var/opt/azcmagent
    * /opt/logs

## <a name="next-steps"></a>Következő lépések

Az Azure arc-kompatibilis kiszolgálók (előzetes verzió) kiértékelésének megkezdéséhez kövesse a [hibrid gépek az Azure-hoz való összekapcsolását ismertető cikket a Azure Portal](onboard-portal.md).
