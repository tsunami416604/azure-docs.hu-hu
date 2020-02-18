---
title: Tudnivalók a virtuális gépek tartalmának naplózásáról
description: Megtudhatja, hogyan használja a Azure Policy a vendég konfigurációs ügynököt a beállítások naplózására a virtuális gépeken belül.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 73f986774fc13ac8c69cd800c977c909b591a74c
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2020
ms.locfileid: "77369740"
---
# <a name="understand-azure-policys-guest-configuration"></a>Az Azure Policy Vendég konfiguráció ismertetése

Az Azure-erőforrások naplózása és [szervizelését](../how-to/remediate-resources.md) után Azure Policy naplózhatja a beállításokat a gépen belül. Az érvényesítést a Vendégkonfiguráció bővítmény és ügyfél végzi. A bővítmény az ügyfélen keresztül ellenőrzi a beállításokat, például a következőket:

- Az operációs rendszer konfigurációja
- Alkalmazás konfigurációja vagy jelenléte
- Környezeti beállítások

Az Azure Policy Vendégkonfiguráció jelenleg csak a gépen belüli beállításokat naplózza. Nem alkalmaz konfigurációkat.

## <a name="extension-and-client"></a>Bővítmény és az ügyfél

A beállítások számítógépeken belüli naplózásához a [virtuálisgép-bővítmény](../../../virtual-machines/extensions/overview.md) engedélyezve van. A bővítmény letölti a megfelelő szabályzat-hozzárendelés és a megfelelő konfiguráció definíciója.

### <a name="limits-set-on-the-extension"></a>A bővítményre beállított korlátok

Ha korlátozni szeretné a bővítménynek a gépen belül futó alkalmazásoktól való korlátozását, a vendég konfigurációja nem lépheti túl a CPU-kihasználtság 5%-át. Ez a korlátozás a beépített és az egyéni definíciók esetében is létezik.

## <a name="register-guest-configuration-resource-provider"></a>Vendég-konfigurációs erőforrás-szolgáltató regisztrálása

Vendég-konfiguráció használata előtt regisztrálnia kell az erőforrás-szolgáltató. A portálon keresztül vagy a Powershellen keresztül lehet regisztrálni. Az erőforrás-szolgáltató automatikusan regisztrálva van, ha a vendég konfigurációs szabályzatának hozzárendelése a portálon történik.

### <a name="registration---portal"></a>Regisztráció – portál

Az erőforrás-szolgáltató regisztrálása Vendég konfiguráció az Azure Portalon keresztül, kövesse az alábbi lépéseket:

1. Indítsa el a Azure Portal, és kattintson a **minden szolgáltatás**elemre. Keresse meg és válassza ki az **előfizetéseket**.

1. Keresse meg és kattintson arra az előfizetésre, amely engedélyezi a Vendég konfigurációját.

1. Az **előfizetés** lap bal oldali menüjében kattintson az **erőforrás-szolgáltatók**elemre.

1. Szűrje vagy görgessen a **Microsoft. GuestConfiguration**, majd kattintson a **regisztráció** elemre ugyanazon a sorban.

### <a name="registration---powershell"></a>Regisztráció – PowerShell

Vendég konfigurációs PowerShell-lel az erőforrás-szolgáltató regisztrálásához futtassa a következő parancsot:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>Érvényesítési eszközök

A gépen belül a vendég konfigurációs ügyfél helyi eszközöket használ a naplózás futtatásához.

Az alábbi táblázat az egyes támogatott operációs rendszeren használja a helyi eszközök listáját:

|Operációs rendszer|Fürtérvényesítési eszköz|Megjegyzések|
|-|-|-|
|Windows|[Windows PowerShell desired State Configuration](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef Inspect](https://www.chef.io/inspec/)| Ruby és Python telepíti a Vendég Configuration bővítményt. |

### <a name="validation-frequency"></a>Ellenőrzés gyakorisága

A vendég konfigurációs ügyfél 5 percenként keres új tartalmat. A vendég-hozzárendelés fogadása után a rendszer 15 percenként ellenőrzi a beállításokat. A rendszer a naplózás befejeződése után azonnal elküldi az eredményeket a vendég konfiguráció erőforrás-szolgáltatójának. A szabályzatok [kiértékelésének](../how-to/get-compliance-data.md#evaluation-triggers) bekövetkeztekor a számítógép állapota a vendég konfiguráció erőforrás-szolgáltatóba íródik. Ez a frissítés Azure Policyt okoz a Azure Resource Manager tulajdonságainak kiértékeléséhez. Az igény szerinti Azure Policy kiértékelése a vendég konfiguráció erőforrás-szolgáltató legújabb értékét kérdezi le. Azonban nem aktiválja a számítógép konfigurációjának új naplózását.

## <a name="supported-client-types"></a>Támogatott ügyfél típusú

Az alábbi táblázat az Azure-rendszerképek támogatott operációs rendszerek listája látható:

|Közzétevő|Name (Név)|Verziók|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Windows-ügyfél|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> A vendég konfigurációja képes a támogatott operációs rendszert futtató csomópontok naplózására. Ha egyéni rendszerképet használó virtuális gépeket szeretne naplózni, duplikálnia kell a **DeployIfNotExists** -definíciót, és módosítania kell az **IF** szakaszt a rendszerkép tulajdonságainak belefoglalásához.

### <a name="unsupported-client-types"></a>Nem támogatott ügyfélalkalmazás típusa

A Windows Server Nano Server semmilyen verzióban nem támogatott.

## <a name="guest-configuration-extension-network-requirements"></a>A vendég konfigurációs bővítmény hálózati követelményei

Az Azure-beli vendég-konfigurációs erőforrás-szolgáltatóval való kommunikációhoz a gépeknek kimenő hozzáférésre van szükségük az Azure-adatközpontok **443**-es portján Ha az Azure-ban olyan magánhálózati virtuális hálózatot használ, amely nem engedélyezi a kimenő forgalmat, konfigurálja a kivételeket a [hálózati biztonsági csoport](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) szabályaival.
A "GuestAndHybridManagement" [szolgáltatási címke](../../../virtual-network/service-tags-overview.md) használható a vendég konfigurációs szolgáltatásra való hivatkozáshoz.

## <a name="guest-configuration-definition-requirements"></a>Vendég konfigurációkra definíciója

Minden vendég konfigurációhoz tartozó naplózási futtatáshoz két házirend-definíció, egy **DeployIfNotExists** -definíció és egy **AuditIfNotExists** -definíció szükséges. A **DeployIfNotExists** definíciója a gép előkészítésére szolgál a vendég konfigurációs ügynökkel és más összetevőkkel az [ellenőrzési eszközök](#validation-tools)támogatásához.

A **DeployIfNotExists** házirend-definíciója ellenőrzi és kijavította a következő elemeket:

- Ellenőrizze, hogy a gép hozzárendelt-e egy konfigurációt az értékeléshez. Ha jelenleg nincs hozzárendelés, szerezze be a hozzárendelést, és készítse elő a gépet a alábbiak szerint:
  - Hitelesítés a gépen [felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md) használatával
  - A **Microsoft. GuestConfiguration** bővítmény legújabb verziójának telepítése
  - [Ellenőrzési eszközök](#validation-tools) és függőségek telepítése szükség esetén

Ha a **DeployIfNotExists** -hozzárendelés nem megfelelő, akkor a rendszer [szervizelési feladatot](../how-to/remediate-resources.md#create-a-remediation-task) is felhasználhat.

Ha a **DeployIfNotExists** -hozzárendelés megfelelő, a **AuditIfNotExists** szabályzat-hozzárendelés a helyi ellenőrzési eszközöket használja annak megállapításához, hogy a konfigurációs hozzárendelés megfelelő vagy nem megfelelő-e. A fürtérvényesítési eszköz biztosít a Vendég konfigurációs ügyfél az eredményeket. Az ügyfél a Vendég a bővítmény elérhetővé teszi azokat a Vendég-konfigurációs erőforrás-szolgáltatón keresztül továbbítja az eredményeket.

Azure Policy a vendég-konfiguráció erőforrás-szolgáltatói **complianceStatus** tulajdonságot **használja a megfelelőségi csomópont** megfelelőségének jelentéséhez. További információ: a [megfelelőségi adatok beszerzése](../how-to/get-compliance-data.md).

> [!NOTE]
> Az **DeployIfNotExists** szabályzat szükséges ahhoz, hogy a **AuditIfNotExists** -házirend eredményét visszaállítsa. A **DeployIfNotExists**nélkül a **AuditIfNotExists** házirend "0/0" erőforrást jelenít meg állapotként.

Beépített Vendég konfigurációs szabályzatainak csoportra vonatkozó definíciókat használja a hozzárendelések kezdeményezések szerepelnek. A _\[Preview\]nevű beépített kezdeményezés: a jelszó biztonsági beállításainak naplózása Linux és Windows rendszerű gépeken_ 18 szabályzatot tartalmaz. A Windows hat **DeployIfNotExists** és **AuditIfNotExists** pár, a Linux esetében pedig három pár. A [szabályzat-definíció](definition-structure.md#policy-rule) logikája ellenőrzi, hogy csak a cél operációs rendszer van-e kiértékelve.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Az operációs rendszer beállításainak naplózása az iparági alapkonfigurációkat követve

A Azure Policyban elérhető kezdeményezések egyike lehetővé teszi a virtuális gépeken belüli operációs rendszer beállításainak naplózását a Microsoft alapkonfigurációját követve. A definíció, _\[előzetes verzió\]: az Azure-beli biztonsági alapbeállításoknak nem megfelelő Windows-alapú virtuális gépek naplózása_ a Active Directory csoportházirend beállításai alapján teljes naplózási szabályt tartalmaz.

A beállítások többsége paraméterekként érhető el. Ez a funkció lehetővé teszi, hogy testreszabja a naplózást, hogy a szabályzatot a szervezeti követelményekhez igazítsa, vagy a szabályzatot a harmadik féltől származó információkra, például az iparági szabályozási szabványokra.

Egyes paraméterek egy egész érték tartományát támogatják. A jelszó maximális élettartama paraméter például beállítható egy tartomány operátor használatával, hogy rugalmasságot biztosítson a gépek tulajdonosainak. Azt is megteheti, hogy a felhasználóknak a jelszavuk módosítására vonatkozó hatályos Csoportházirend beállítás értéke legfeljebb 70 nap, de nem lehet kevesebb, mint egy nap. A paraméterhez tartozó info-Bubble kifejezésben leírtak szerint ez az üzleti házirend az érvényes naplózási értékre van állítva, az értéket állítsa "1, 70" értékre.

Ha a szabályzatot egy Azure Resource Manager telepítési sablonnal rendeli hozzá, egy paraméter-fájllal kezelheti ezeket a beállításokat a verziókövetés segítségével. Egy olyan eszközzel, mint például a git a naplózási szabályzatok változásainak az egyes beadási dokumentumokkal kapcsolatos megjegyzésekkel való kezeléséhez, tanúsítja, hogy a hozzárendelés miért kivétel a várt értéktől.

#### <a name="applying-configurations-using-guest-configuration"></a>Konfigurációk alkalmazása a vendég konfiguráció használatával

A Azure Policy legújabb funkciója a számítógépeken belüli beállítások konfigurálását végzi. A definíció a _Windows rendszerű gépeken beállított időzónát konfigurálja_ úgy, hogy az időzóna konfigurálásával megváltoztatja a gépet.

Ha a _konfigurálással_kezdődő definíciókat rendeli hozzá, akkor a definíciók _központi telepítésének előfeltételeit is hozzá kell rendelnie a Windows rendszerű virtuális gépeken a vendég-konfigurációs szabályzat engedélyezés_ Ezeket a definíciókat a választott kezdeményezéssel kombinálhatja.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Szabályzatok kiosztása az Azure-on kívüli gépekhez

A vendég konfigurációhoz elérhető naplózási házirendek közé tartozik a **Microsoft. HybridCompute/Machines** erőforrástípus. Az Azure-ív részét képező, a házirend-hozzárendelés hatókörében lévő kiszolgálók automatikusan beletartoznak a [szolgáltatásba](../../../azure-arc/servers/overview.md) .

### <a name="multiple-assignments"></a>Több hozzárendelés

A vendég-konfigurációs házirendek jelenleg csak egyszer használják a vendég-hozzárendelést egy gépenként, még akkor is, ha a házirend-hozzárendelés eltérő paramétereket használ.

## <a name="built-in-resource-modules"></a>Beépített erőforrás-modulok

A vendég konfigurációs bővítmény telepítésekor a "GuestConfiguration" PowerShell-modul a DSC-erőforrás moduljainak legújabb verziójához tartozik. Ez a modul letölthető a PowerShell-galéria a "manuális Letöltés" hivatkozásra kattintva a modul oldal [GuestConfiguration](https://www.powershellgallery.com/packages/GuestConfiguration/). A ". nupkg" fájlformátumot átnevezheti ". zip" névre a kibontáshoz és a felülvizsgálathoz.

## <a name="client-log-files"></a>Ügyfél naplófájljai

A vendég konfigurációs bővítmény naplófájlokat ír a következő helyszínekre:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Ahol a `<version>` az aktuális verziószámra hivatkozik.

### <a name="collecting-logs-remotely"></a>Naplók távoli gyűjtése

A vendég konfigurációs konfigurációk vagy modulok hibaelhárításának első lépéseként az `Test-GuestConfigurationPackage` parancsmagot kell használnia, amely a [vendég konfigurációs csomag tesztelésének](../how-to/guest-configuration-create.md#test-a-guest-configuration-package)lépéseit követi.
Ha ez nem sikerül, az ügyfél naplói összegyűjtése segíthet a problémák diagnosztizálásában.

#### <a name="windows"></a>Windows

Ha az Azure virtuális gép futtatási parancsát szeretné használni a Windows rendszerű számítógépeken található naplófájlok adatainak rögzítéséhez, a következő példa a PowerShell-szkript hasznos lehet. További információ: [PowerShell-parancsfájlok futtatása a Windows rendszerű virtuális gépen a Run paranccsal](../../../virtual-machines/windows/run-command.md).

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$latestVersion = Get-ChildItem -Path 'C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\' | ForEach-Object {$_.FullName} | Sort-Object -Descending | Select-Object -First 1
Select-String -Path "$latestVersion\dsc\logs\dsc.log" -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Ha az Azure virtuális gép futtatási parancsát szeretné használni a Linux rendszerű számítógépeken található naplófájlok adatainak rögzítéséhez, a következő példa a bash-szkript hasznos lehet. További információ: [rendszerhéj-parancsfájlok futtatása Linux rendszerű virtuális gépen a Run paranccsal](../../../virtual-machines/linux/run-command.md)

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
latestVersion=$(find /var/lib/waagent/ -type d -name "Microsoft.GuestConfiguration.ConfigurationforLinux-*" -maxdepth 1 -print | sort -z | sed -n 1p)
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' "$latestVersion/GCAgent/logs/dsc.log" | tail
```

## <a name="guest-configuration-samples"></a>Vendég konfigurációs minták

A szabályzat vendég konfigurációjának beépített kezdeményezései a következő helyszíneken érhetők el:

- [Beépített szabályzat-definíciók – vendég konfigurációja](../samples/built-in-policies.md#guest-configuration)
- [Beépített kezdeményezések – vendég konfigurációja](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy Samples GitHub-tárház](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/get-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).
