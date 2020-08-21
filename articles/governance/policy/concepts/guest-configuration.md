---
title: Tudnivalók a virtuális gépek tartalmának naplózásáról
description: Megtudhatja, hogyan használja a Azure Policy a vendég konfigurációs ügynököt a beállítások naplózására a virtuális gépeken belül.
ms.date: 08/07/2020
ms.topic: conceptual
ms.openlocfilehash: 21034aaae42aa4abfa6848ce22db5fa4c21a11ce
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685765"
---
# <a name="understand-azure-policys-guest-configuration"></a>Az Azure Policy vendégkonfigurációjának ismertetése

A Azure Policy a számítógépeken belül is naplózhatja a beállításokat, mind az Azure-ban, mind az [arc-csatlakoztatott gépeken](../../../azure-arc/servers/overview.md)futó gépek esetében. Az érvényesítést a Vendégkonfiguráció bővítmény és ügyfél végzi. A bővítmény az ügyfélen keresztül ellenőrzi a beállításokat, például a következőket:

- Az operációs rendszer konfigurációja
- Alkalmazás konfigurációja vagy jelenléte
- Környezeti beállítások

Jelenleg az Azure Policy vendégkonfigurációs szabályzatok többsége csak a gépen belüli beállításokat naplózza.
Nem alkalmaznak konfigurációkat. A kivétel egy, az [alábbiakban hivatkozott](#applying-configurations-using-guest-configuration)beépített szabályzat.

## <a name="enable-guest-configuration"></a>Vendég konfiguráció engedélyezése

A környezetben lévő gépek állapotának naplózásához, beleértve az Azure-ban és az arc-csatlakoztatott gépeken található gépeket is, tekintse át a következő adatokat.

## <a name="resource-provider"></a>Erőforrás-szolgáltató

A vendég konfiguráció használatához regisztrálnia kell az erőforrás-szolgáltatót. Az erőforrás-szolgáltató automatikusan regisztrálva van, ha a vendég konfigurációs szabályzatának hozzárendelése a portálon történik. Manuálisan is regisztrálhat a [portálon](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure PowerShellon](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)vagy az [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)-n keresztül.

## <a name="deploy-requirements-for-azure-virtual-machines"></a>Azure-beli virtuális gépekre vonatkozó követelmények üzembe helyezése

A beállítások számítógépeken belüli naplózásához a virtuálisgép- [bővítmény](../../../virtual-machines/extensions/overview.md) engedélyezve van, és a gépnek rendszer által felügyelt identitással kell rendelkeznie. A bővítmény letölti a vonatkozó szabályzat-hozzárendelést és a megfelelő konfigurációdefiníciót. Az identitás a gép hitelesítésére szolgál, ahogy az beolvassa és beírja a vendég konfigurációs szolgáltatásba. A bővítmény nem szükséges az arc-csatlakoztatott gépekhez, mert az az arc csatlakoztatott számítógép ügynökének része.

> [!IMPORTANT]
> Az Azure-beli virtuális gépek naplózásához a vendég konfiguráció kiterjesztése és a felügyelt identitás szükséges. A bővítmény nagy léptékű üzembe helyezéséhez rendelje hozzá a következő házirend-kezdeményezést:
> 
> `Deploy prerequisites to enable Guest Configuration policies on virtual machines`

### <a name="limits-set-on-the-extension"></a>A bővítményre beállított korlátok

Ha korlátozni szeretné a bővítménynek a gépen belül futó alkalmazásoktól való korlátozását, a vendég konfigurációja nem lépheti túl a CPU 5%-át. Ez a korlátozás a beépített és az egyéni definíciók esetében is létezik. Ugyanez érvényes a vendég konfigurációs szolgáltatáshoz az arc-csatlakoztatott gépi ügynökben.

### <a name="validation-tools"></a>Ellenőrzési eszközök

A gépen belül a vendég konfigurációs ügyfél helyi eszközöket használ a naplózás futtatásához.

Az alábbi táblázat felsorolja az egyes támogatott operációs rendszereken használt helyi eszközöket. Beépített tartalom esetén a vendég konfigurációja automatikusan betölti ezeket az eszközöket.

|Operációs rendszer|Érvényesítési eszköz|Jegyzetek|
|-|-|-|
|Windows|[PowerShell desired State Configuration](/powershell/scripting/dsc/overview/overview) v2| A rendszer csak az Azure Policy által használt mappába betöltve. Nem ütközik a Windows PowerShell DSC-vel. A PowerShell Core nincs hozzáadva a rendszer elérési útjához.|
|Linux|[Chef Inspect](https://www.chef.io/inspec/)| Telepíti a Chef Inspect verzió 2.2.61 az alapértelmezett helyen, és hozzáadja a rendszer elérési útjához. Az inspec-csomag függőségei, például a Ruby és a Python is telepítve vannak. |

### <a name="validation-frequency"></a>Ellenőrzés gyakorisága

A vendég konfigurációs ügyfél 5 percenként keres új tartalmat. A vendég-hozzárendelés fogadása után a rendszer 15 perces időközönként újra bejelöli a konfiguráció beállításait. A rendszer a naplózás befejezésekor elküldi az eredményeket a vendég konfiguráció erőforrás-szolgáltatójának. A szabályzatok [kiértékelésének](../how-to/get-compliance-data.md#evaluation-triggers) bekövetkeztekor a számítógép állapota a vendég konfiguráció erőforrás-szolgáltatóba íródik. Ez a frissítés Azure Policyt okoz a Azure Resource Manager tulajdonságainak kiértékeléséhez. Az igény szerinti Azure Policy kiértékelése a vendég konfiguráció erőforrás-szolgáltató legújabb értékét kérdezi le. Azonban nem aktiválja a számítógép konfigurációjának új naplózását.

## <a name="supported-client-types"></a>Támogatott ügyfelek típusai

A vendég-konfigurációs házirendek tartalmazzák az új verziókat. Az Azure Marketplace-en elérhető operációs rendszerek régebbi verziói ki vannak zárva, ha a vendég konfigurációs ügynök nem kompatibilis.
Az alábbi táblázat az Azure-lemezképekben támogatott operációs rendszerek listáját tartalmazza:

|Publisher|Név|Verziók|
|-|-|-|
|Canonical|Ubuntu Server|14,04 és újabb verziók|
|Credativ|Debian|8 és újabb verziók|
|Microsoft|Windows Server|2012 és újabb verziók|
|Microsoft|Windows-ügyfél|Windows 10|
|OpenLogic|CentOS|7,3 és újabb verziók|
|Red Hat|Red Hat Enterprise Linux|7,4 – 7,8|
|SUSE|SLES|12 SP3 és újabb verziók|

Az egyéni virtuálisgép-lemezképeket a vendég-konfigurációs házirendek támogatják, feltéve, hogy a fenti táblázatban szereplő operációs rendszerek egyike.

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

Az Azure-beli virtuális gépek a helyi hálózati adaptert vagy egy privát hivatkozást is használhatnak a vendég konfigurációs szolgáltatással való kommunikációhoz.

Az Azure arc-gépek a helyszíni hálózati infrastruktúrával csatlakozva érhetik el az Azure-szolgáltatásokat, és bejelenthetik a megfelelőségi állapotot.

### <a name="communicate-over-virtual-networks-in-azure"></a>Kommunikáció az Azure-beli virtuális hálózatokkal

A virtuális hálózatokat használó virtuális gépek számára a porton elérhető Azure-adatközpontokhoz kimenő hozzáférésre lesz szükség `443` . Ha az Azure-ban olyan magánhálózati virtuális hálózatot használ, amely nem engedélyezi a kimenő forgalmat, konfigurálja a kivételeket a hálózati biztonsági csoport szabályaival. A "GuestAndHybridManagement" szolgáltatási címke használható a vendég konfigurációs szolgáltatásra való hivatkozáshoz.

### <a name="communicate-over-private-link-in-azure"></a>Kommunikáció privát kapcsolaton keresztül az Azure-ban

A virtuális gépek [privát hivatkozást](../../../private-link/private-link-overview.md) is használhatnak a vendég konfigurációs szolgáltatással való kommunikációhoz. Alkalmazza a címkét a név `EnablePrivateNeworkGC` és az érték `TRUE` használatával a funkció engedélyezéséhez. A címkét a rendszer a számítógépre vonatkozó vendég-konfigurációs házirendek előtt vagy után is alkalmazhatja.

A forgalmat az Azure [virtuális nyilvános IP-címével](../../../virtual-network/what-is-ip-address-168-63-129-16.md) irányítjuk, hogy biztonságos, hitelesített csatornát hozzon létre az Azure platform erőforrásaival.

### <a name="azure-arc-connected-machines"></a>Azure arc-csatlakoztatott gépek

Az Azure arc-on kívül található csomópontok a vendég konfigurációs szolgáltatáshoz való kapcsolódást igénylik. Az [Azure arc dokumentációjában](../../../azure-arc/servers/overview.md)megadott hálózati és proxy-követelmények részletei.

Az Azure-beli vendég-konfigurációs erőforrás-szolgáltatóval való kommunikációhoz a gépeknek kimenő hozzáférésre van szükségük az Azure-adatközpontok **443**-es portján Ha egy Azure-beli hálózat nem engedélyezi a kimenő forgalmat, konfigurálja a kivételeket a [hálózati biztonsági csoportokra](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) vonatkozó szabályokkal. A "GuestAndHybridManagement" [szolgáltatási címke](../../../virtual-network/service-tags-overview.md) használható a vendég konfigurációs szolgáltatásra való hivatkozáshoz.

## <a name="managed-identity-requirements"></a>Felügyelt identitásokra vonatkozó követelmények

A kezdeményezésben szereplő szabályzat-definíciók a [vendég-konfigurációs szabályzatok virtuális gépeken való engedélyezéséhez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12794019-7a00-42cf-95c2-882eed337cc8) a rendszer által hozzárendelt felügyelt identitás engedélyezése, ha az egyik nem létezik. Az identitás létrehozását kezelő kezdeményezésben két házirend-definíció található. Ha a házirend-definícióban szereplő feltételek biztosítják a megfelelő viselkedést az Azure-beli gépi erőforrás aktuális állapota alapján.

Ha a gép jelenleg nem rendelkezik felügyelt identitásokkal, a hatályos szabályzat a következő lesz: [ \[ előnézet \] : rendszerhez rendelt felügyelt identitás hozzáadása a vendég konfigurációs hozzárendeléseinek engedélyezéséhez az identitás nélküli virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e)

Ha a gépnek jelenleg van felhasználó által hozzárendelt rendszeridentitása, a hatályos szabályzat a következő: [ \[ előnézet \] : rendszerhez rendelt felügyelt identitás hozzáadása a vendég konfigurációs hozzárendeléseinek a felhasználó által hozzárendelt identitású virtuális gépeken való engedélyezéséhez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6)

## <a name="guest-configuration-definition-requirements"></a>A vendég konfigurációjának meghatározására vonatkozó követelmények

Minden vendég konfigurációhoz tartozó naplózási futtatáshoz két házirend-definíció, egy **DeployIfNotExists** -definíció és egy **AuditIfNotExists** -definíció szükséges. A **DeployIfNotExists** házirend-definíciói az egyes gépeken végzett naplózási függőségeket kezelik.

A **DeployIfNotExists** házirend-definíciója ellenőrzi és kijavította a következő elemeket:

- Ellenőrizze, hogy a gép hozzárendelt-e egy konfigurációt az értékeléshez. Ha jelenleg nincs hozzárendelés, szerezze be a hozzárendelést, és készítse elő a gépet a alábbiak szerint:
  - Hitelesítés a gépen [felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md) használatával
  - A **Microsoft. GuestConfiguration** bővítmény legújabb verziójának telepítése
  - [Ellenőrzési eszközök](#validation-tools) és függőségek telepítése szükség esetén

Ha a **DeployIfNotExists** -hozzárendelés nem megfelelő, akkor a rendszer [szervizelési feladatot](../how-to/remediate-resources.md#create-a-remediation-task) is felhasználhat.

Ha a **DeployIfNotExists** -hozzárendelés megfelelő, a **AuditIfNotExists** szabályzat-hozzárendelés meghatározza, hogy a vendég-hozzárendelés megfelelő vagy nem megfelelő-e. Az érvényesítési eszköz biztosítja az eredményeket a vendég konfigurációs ügyfelének. Az ügyfél továbbítja az eredményeket a vendég bővítménynek, ami elérhetővé teszi őket a vendég-konfiguráció erőforrás-szolgáltatóján keresztül.

Azure Policy a vendég-konfiguráció erőforrás-szolgáltatói **complianceStatus** tulajdonságot **használja a megfelelőségi csomópont** megfelelőségének jelentéséhez. További információ: a [megfelelőségi adatok beszerzése](../how-to/get-compliance-data.md).

> [!NOTE]
> Az **DeployIfNotExists** szabályzat szükséges ahhoz, hogy a **AuditIfNotExists** -házirend eredményét visszaállítsa. A **DeployIfNotExists**nélkül a **AuditIfNotExists** házirend "0/0" erőforrást jelenít meg állapotként.

A vendég konfigurációhoz tartozó összes beépített szabályzatot egy olyan kezdeményezés tartalmazza, amely csoportosítja a definíciókat a hozzárendelésekben való használathoz. Az előnézet nevű beépített kezdeményezés _ \[ : a \] jelszavas biztonság naplózása a Linux és a Windows rendszerű gépeken_ 18 szabályzatot tartalmaz. A Windows hat **DeployIfNotExists** és **AuditIfNotExists** pár, a Linux esetében pedig három pár. A [szabályzat-definíció](definition-structure.md#policy-rule) logikája ellenőrzi, hogy csak a cél operációs rendszer van-e kiértékelve.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Az operációs rendszer beállításainak naplózása az iparági alapkonfigurációkat követve

A Azure Policyban az egyik kezdeményezés lehetővé teszi az operációs rendszer beállításainak naplózását az "alapkonfiguráció" érték után. Az előzetes verzió: az Azure-beli _ \[ \] biztonsági alapbeállításoknak meg nem felelő Windows-alapú virtuális gépek naplózása_ Active Directory csoportházirend alapján.

A beállítások többsége paraméterekként érhető el. A paraméterek lehetővé teszik a naplózott elemek testreszabását.
A szabályzatot a követelményekkel igazíthatja, vagy a szabályzatot harmadik féltől származó információkra, például iparági szabályozási szabványokra is kiképezheti.

Egyes paraméterek egy egész érték tartományát támogatják. A jelszó maximális kora beállítás például naplózhatja a hatályos Csoportházirend beállítást. Az "1, 70" tartomány megerősíti, hogy a felhasználóknak legalább 70 naponta meg kell változtatniuk a jelszavukat, de nem kevesebb mint egy napot.

Ha a szabályzatot egy Azure Resource Manager sablon (ARM-sablon) használatával rendeli hozzá, a kivételek kezeléséhez használja a parameters (paraméterek) fájlt. A fájlokat egy verziókövetés rendszerbe (például a git-ba) tekintheti meg. A fájl módosításaival kapcsolatos megjegyzések igazolják, hogy egy hozzárendelés miért kivétel a várt értéktől.

#### <a name="applying-configurations-using-guest-configuration"></a>Konfigurációk alkalmazása a vendég konfiguráció használatával

A Azure Policy legújabb funkciója a számítógépeken belüli beállítások konfigurálását végzi. A definíció a _Windows rendszerű gépeken beállított időzónát konfigurálja_ úgy, hogy az időzóna konfigurálásával megváltoztatja a gépet.

Ha a _konfigurálással_kezdődő definíciókat rendeli hozzá, akkor a definíciók _központi telepítésének előfeltételeit is hozzá kell rendelnie a Windows rendszerű virtuális gépeken a vendég-konfigurációs szabályzat engedélyezés_ Ezeket a definíciókat a választott kezdeményezéssel kombinálhatja.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Szabályzatok kiosztása az Azure-on kívüli gépekhez

A vendég konfigurációhoz elérhető naplózási házirendek közé tartozik a **Microsoft. HybridCompute/Machines** erőforrástípus. Az Azure-ív részét képező, a házirend-hozzárendelés hatókörében lévő kiszolgálók automatikusan beletartoznak a [szolgáltatásba](../../../azure-arc/servers/overview.md) .

### <a name="multiple-assignments"></a>Több hozzárendelés

A vendég-konfigurációs házirendek jelenleg csak egyszer használják a vendég-hozzárendelést egy gépenként, még akkor is, ha a házirend-hozzárendelés eltérő paramétereket használ.

## <a name="client-log-files"></a>Ügyfél naplófájljai

A vendég konfigurációs bővítmény naplófájlokat ír a következő helyszínekre:

Windows `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Ahol `<version>` az aktuális verziószámra hivatkozik.

### <a name="collecting-logs-remotely"></a>Naplók távoli gyűjtése

A vendég konfigurációs konfigurációk vagy modulok hibaelhárításának első lépéseként a parancsmagot kell használnia, amely azt `Test-GuestConfigurationPackage` ismerteti, hogyan [hozhat létre egyéni vendég-konfigurációs naplózási szabályzatot a Windows rendszerhez](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows).
Ha ez nem sikerül, az ügyfél naplói összegyűjtése segíthet a problémák diagnosztizálásában.

#### <a name="windows"></a>Windows

Adatok rögzítése a naplófájlokból az [Azure VM Run paranccsal](../../../virtual-machines/windows/run-command.md), a következő példa a PowerShell-szkript hasznos lehet.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Adatok rögzítése a naplófájlokból az [Azure VM Run paranccsal](../../../virtual-machines/linux/run-command.md), a következő példa a bash-szkript hasznos lehet.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Vendég konfigurációs minták

A vendég konfiguráció beépített házirendjének mintái a következő helyszíneken érhetők el:

- [Beépített szabályzat-definíciók – vendég konfigurációja](../samples/built-in-policies.md#guest-configuration)
- [Beépített kezdeményezések – vendég konfigurációja](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy Samples GitHub-tárház](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan tekintheti meg az egyes beállításokat a [vendég konfiguráció megfelelősége nézetből](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)
- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](./definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](./effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/get-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).
