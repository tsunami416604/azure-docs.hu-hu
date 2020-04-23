---
title: Ismerje meg a virtuális gépek tartalmának naplózását
description: Ismerje meg, hogy az Azure Policy hogyan használja a Vendég konfigurációs ügynököt a virtuális gépeken belüli beállítások naplózásához.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 89f7cc3931971d70b441490f77b67ace89434c2b
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025220"
---
# <a name="understand-azure-policys-guest-configuration"></a>Az Azure-szabályzat vendégkonfigurációjának megismerése

Az Azure-erőforrások naplózásán és [kijavításán](../how-to/remediate-resources.md) túl az Azure Policy naplózhatja a beállításokat a számítógépen belül. Az érvényesítést a Vendégkonfiguráció bővítmény és ügyfél végzi. A bővítmény az ügyfélen keresztül ellenőrzi a beállításokat, például a következőket:

- Az operációs rendszer konfigurációja
- Alkalmazás konfigurációja vagy jelenléte
- Környezeti beállítások

Jelenleg a legtöbb Azure-szabályzat vendégkonfigurációs szabályzata csak a számítógépen belüli naplózási beállításokat. Nem alkalmaznak konfigurációkat. A kivétel az [alábbiakban hivatkozott](#applying-configurations-using-guest-configuration)beépített házirend.

## <a name="resource-provider"></a>Erőforrás-szolgáltató

A Vendégkonfiguráció használata előtt regisztrálnia kell az erőforrás-szolgáltatót. Az erőforrás-szolgáltató automatikusan regisztrálva lesz, ha a vendégkonfigurációs házirend hozzárendelése a portálon keresztül történik. Manuálisan regisztrálhat a [portálon](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), az [Azure PowerShellen](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)vagy az [Azure CLI-n](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)keresztül.

## <a name="extension-and-client"></a>Kiterjesztés és ügyfél

A beállítások naplózásához egy gépen belül, a [virtuális gép bővítmény](../../../virtual-machines/extensions/overview.md) engedélyezve van. A bővítmény letölti a vonatkozó házirend-hozzárendelést és a megfelelő konfigurációs definíciót.

> [!Important]
> A vendégkonfiguráció bővítmény az Azure virtuális gépeken végzett naplózáshoz szükséges.
> A bővítmény nagy méretekben történő üzembe helyezéséhez rendelje hozzá a következő házirend-definíciókat:
>   - [Előfeltételek telepítése a vendégkonfigurációs házirend windowsos virtuális gépeken való engedélyezéséhez.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)
>   - [A vendégkonfigurációs házirend linuxos virtuális gépeken való engedélyezéséhez telepítse az előfeltételeket.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

### <a name="limits-set-on-the-extension"></a>A kiterjesztésre beállított korlátok

Annak érdekében, hogy a bővítmény a számítógépen belül futó alkalmazásokat ne befolyásolja, a vendégkonfiguráció nem haladhatja meg a PROCESSZOR 5%-át. Ez a korlátozás a beépített és az egyéni definíciókra egyaránt létezik.

### <a name="validation-tools"></a>Érvényesítési eszközök

A számítógépen belül a vendégkonfigurációs ügyfél helyi eszközöket használ a naplózás futtatásához.

Az alábbi táblázat az egyes támogatott operációs rendszereken használt helyi eszközök listáját tartalmazza:

|Operációs rendszer|Érvényesítési eszköz|Megjegyzések|
|-|-|-|
|Windows|[A Windows PowerShell kívánt állapotkonfigurációja](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ha a Ruby és a Python nem a gépen, akkor a Vendég konfiguráció bővítmény telepíti őket. |

### <a name="validation-frequency"></a>Érvényesítési gyakoriság

A Vendég konfigurációs ügyfél 5 percenként ellenőrzi az új tartalmat. A vendég-hozzárendelés fogadása után a rendszer 15 perces időközönként újra ellenőrzi a konfiguráció beállításait.
Az eredményeket a rendszer a folyamat befejezésekor küldi el a vendégkonfigurációerőforrás-szolgáltatónak. Amikor egy [házirend-kiértékelési eseményindító](../how-to/get-compliance-data.md#evaluation-triggers) történik, a számítógép állapota a vendég konfigurációs erőforrás-szolgáltató. A frissítés hatására az Azure Policy kiértékeli az Azure Resource Manager tulajdonságait. Egy igény szerinti Azure-szabályzat kiértékelése lekéri a legújabb értéket a vendég konfigurációs erőforrás-szolgáltató. Azonban nem indítja el a konfiguráció új naplózását a gépen belül.

## <a name="supported-client-types"></a>Támogatott ügyféltípusok

Vendég konfigurációs házirendek tartalmazzák az új verziók. Az Azure piactéren elérhető operációs rendszerek régebbi verziói ki vannak zárva, ha a vendégkonfigurációs ügynök nem kompatibilis. Az alábbi táblázat az Azure-lemezképek en támogatott operációs rendszerek listáját tartalmazza:

|Közzétevő|Name (Név)|Verziók|
|-|-|-|
|Canonical|Ubuntu Server|14.04 és újabb|
|Credativ között|Debian|8 és újabb|
|Microsoft|Windows Server|2012 és újabb|
|Microsoft|Windows-ügyfél|Windows 10|
|OpenLogic|CentOS|7.3 és újabb|
|Red Hat|Red Hat Enterprise Linux|7.4 és újabb|
|Suse|SLES|12 SP3 és újabb|

Az egyéni virtuálisgép-lemezképeket vendégkonfigurációs házirendek támogatják, amennyiben azok a fenti táblázatban szereplő operációs rendszerek egyike.

### <a name="unsupported-client-types"></a>Nem támogatott ügyféltípusok

A Windows Server Nano Server egyetlen verzióban sem támogatott.

## <a name="guest-configuration-extension-network-requirements"></a>Vendégkonfiguráció-bővítmény hálózati követelményei

Az Azure vendégkonfigurációs erőforrás-szolgáltatójával való kommunikációhoz a gépeknek kimenő hozzáférésre van szükségük az Azure-adatközpontokhoz a **443-as**porton. Ha egy azure-beli hálózat nem engedélyezi a kimenő forgalmat, konfigurálja a kivételeket [a Hálózati biztonsági csoport](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) szabályaival.
A "GuestAndHybridManagement" [szolgáltatáscímke](../../../virtual-network/service-tags-overview.md) a Vendég konfigurációs szolgáltatásra hivatkozik.

## <a name="azure-managed-identity-requirements"></a>Az Azure felügyelt identitáskövetelményei

A **deployifnotexists** házirendek, amelyek hozzáadják a bővítményt a virtuális gépekhez, szintén engedélyezik a rendszer hez rendelt felügyelt identitást, ha nem létezik.

> [!WARNING]
> Ne engedélyezze a felügyelt identitást a virtuális gépekhez hozzárendelt felhasználó számára olyan házirendek hatókörében, amelyek lehetővé teszik a rendszer hez rendelt felügyelt identitást. A felhasználó által hozzárendelt identitás lesz cserélni, és a gép nem válaszol.

## <a name="guest-configuration-definition-requirements"></a>Vendég konfigurációdefiníciójának követelményei

A Vendégkonfiguráció által futtatott minden egyes naplózáshoz két házirend-definíció, egy **DeployIfNotExists** és egy **AuditIfNotExists** definíció szükséges. 

A **DeployIfNotExists** házirend-definíció ellenőrzi és kijavítja a következő elemeket:

- Ellenőrizze, hogy a gép kapott-e kiértékelhető konfigurációt. Ha jelenleg nincs hozzárendelés, kapjuk meg a hozzárendelést, és készítse elő a gépet a következők szerint:
  - Hitelesítés a gépre [felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md) használatával
  - A **Microsoft.GuestConfiguration** bővítmény legújabb verziójának telepítése
  - [Érvényesítési eszközök](#validation-tools) és függőségek telepítése, ha szükséges

Ha a **DeployIfNotExists** hozzárendelés nem megfelelő, [egy szervizelési feladat](../how-to/remediate-resources.md#create-a-remediation-task) használható.

Ha a DeployIfNotExists hozzárendelés megfelelő, az AuditIfNotExists házirend-hozzárendelés megállapítja, hogy a vendég-hozzárendelés megfelelő vagy nem megfelelő.Once the **DeployIfNotExists** assignment is Compliant, the **AuditIfNotExists** policy assignment determines if the guest assignment is Compliant or Not compliant. Az ellenőrző eszköz biztosítja az eredményeket a Vendég konfigurációs ügyfél. Az ügyfél továbbítja az eredményeket a vendég bővítménynek, amely elérhetővé teszi őket a Vendég konfigurációs erőforrás-szolgáltatón keresztül.

Az Azure Policy a vendégkonfigurációs erőforrás-szolgáltatók **complianceStatus** tulajdonságát használja a megfelelőségi csomópontmegfelelőség **jelentéséhez.** További információt a [Megfelelőségi adatok beszerzése](../how-to/get-compliance-data.md)című témakörben talál.

> [!NOTE]
> A **DeployIfNotExists** házirend szükséges az **AuditIfNotExists** házirend eredmények visszaadásához. A **DeployIfNotExists nélkül**az **AuditIfNotExists** házirend állapotként "0/0" erőforrásokat jelenít meg.

A Vendégkonfiguráció összes beépített házirendje szerepel egy olyan kezdeményezésben, amely csoportosítja a hozzárendelésekben használt definíciókat. Az _ \[Előzetes\]verzió_ : Naplózási jelszó biztonság a Linux és a Windows gépeken belül 18 házirendet tartalmaz. Hat **DeployIfNotExists** és **AuditIfNotExists** pár van Windows-hoz és három pár Linuxra. A [házirend-definíciós](definition-structure.md#policy-rule) logika ellenőrzi, hogy csak a cél operációs rendszer kiértékelése.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Az operációs rendszer beállításainak naplózása az iparági alapértékek et követve

Az Azure Policy egyik kezdeményezése lehetővé teszi az operációs rendszer beállításainak naplózását egy "alapkonfiguráció" után. A definíció, _ \[előzetes verzió:\]Az Azure biztonsági alapkonfiguráció-beállításainak meg nem felelő Windows-virtuális gépek naplózása_ az Active Directory csoportházirendje indikáta alapján tartalmaz szabályokat.

A legtöbb beállítás paraméterként érhető el. A paraméterek lehetővé teszik a naplózástestreszabását. Igazítsa a szabályzatot az Ön igényeihez, vagy rendelje hozzá a szabályzatot harmadik felek adataihoz, például az iparági szabályozási szabványokhoz.

Egyes paraméterek támogatják az egész értéktartományt. A Jelszó maximális korbeállítása például naplózhatja a tényleges csoportházirend-beállítást. Az "1,70" tartomány megerősíti, hogy a felhasználóknak legalább 70 naponta, de legalább egy napon belül módosítaniuk kell jelszavukat.

Ha a szabályzatot egy Azure Resource Manager központi telepítési sablon használatával rendeli hozzá, használjon paraméterfájlt a kivételek kezeléséhez. A fájlok at egy verzió-ellenőrzési rendszerbe, például a Git-be. A fájlmódosításokkal kapcsolatos megjegyzések bizonyítják, hogy egy hozzárendelés miért kivétel a várt érték alól.

#### <a name="applying-configurations-using-guest-configuration"></a>Konfigurációk alkalmazása vendégkonfigurációval

Az Azure Policy legújabb funkciója konfigurálja a gépeken belüli beállításokat. A _definíció: Az időzóna konfigurálása Windows gépeken_ az időzóna konfigurálásával módosítja a gépet.

A _Konfigurálás_kezdetű definíciók hozzárendelésekénél a kiszolgáló előfeltételeit is hozzá kell rendelnie a _Vendégkonfigurációs házirend Windows-gépeken való engedélyezéséhez._ Ezeket a definíciókat egy kezdeményezésben is kombinálhatja, ha úgy dönt.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Házirendek hozzárendelése az Azure-on kívüli gépekhez

A vendégkonfigurációhoz elérhető naplózási házirendek közé tartozik a **Microsoft.HybridCompute/machines** erőforrástípusa. Az Azure Arc [for Servers kiszolgálókra](../../../azure-arc/servers/overview.md) beépített, a házirend-hozzárendelés hatálya alá tartozó gépek automatikusan belekerülnek.

### <a name="multiple-assignments"></a>Több hozzárendelés

Vendég konfigurációs házirendek jelenleg csak akkor támogatja, hogy ugyanazt a vendég-hozzárendelést egyszer egy gépen, akkor is, ha a házirend-hozzárendelés különböző paramétereket használ.

## <a name="client-log-files"></a>Ügyfélnapló-fájlok

A Vendég konfigurációs bővítmény a következő helyekre írja a naplófájlokat:

Windows:`C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux:`/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Ahol `<version>` az aktuális verziószámra vonatkozik.

### <a name="collecting-logs-remotely"></a>Naplók távoli gyűjtése

A Vendég konfigurációs konfigurációk vagy -modulok hibaelhárításának `Test-GuestConfigurationPackage` első lépése ként a parancsmag használata kell, hogy legyen, a [Windows egyéni vendégkonfigurációs naplózási házirendjének létrehozásának](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows)lépéseit követve.
Ha ez nem sikerül, az ügyfélnaplók gyűjtése segíthet a problémák diagnosztizálásában.

#### <a name="windows"></a>Windows

Adatok rögzítése a naplófájlokból [az Azure VM Run Command használatával,](../../../virtual-machines/windows/run-command.md)a következő példa PowerShell-parancsfájl hasznos lehet.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Adatok rögzítése a naplófájlokból [az Azure VM Run Command használatával,](../../../virtual-machines/linux/run-command.md)a következő példa Bash parancsfájl hasznos lehet.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Vendég konfigurációs minták

Vendégkonfiguráció beépített házirend minták érhetők el a következő helyeken:

- [Beépített házirend-definíciók – Vendég konfigurációja](../samples/built-in-policies.md#guest-configuration)
- [Beépített kezdeményezések – Vendégkonfiguráció](../samples/built-in-initiatives.md#guest-configuration)
- [Az Azure Policy mintát vesz a GitHub-tárból](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>További lépések

- További információ az egyes beállítások részleteinek megtekintéséről a [Vendég konfiguráció megfelelőségi nézetéből](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)
- Tekintse át a példákat az [Azure Policy-mintákban.](../samples/index.md)
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](effects.md).
- Ismerje meg, hogyan hozhat [létre programozott házirendeket.](../how-to/programmatically-create.md)
- További információ a [megfelelőségi adatok beszedéséről.](../how-to/get-compliance-data.md)
- További információ a [nem megfelelő erőforrások kiújulásáról.](../how-to/remediate-resources.md)
- Tekintse át, hogy mi a felügyeleti csoport az [Erőforrások rendszerezése az Azure felügyeleti csoportokkal.](../../management-groups/overview.md)
