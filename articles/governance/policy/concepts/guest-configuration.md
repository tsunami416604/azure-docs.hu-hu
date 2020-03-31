---
title: Ismerje meg a virtuális gépek tartalmának naplózását
description: Ismerje meg, hogy az Azure Policy hogyan használja a Vendég konfigurációs ügynököt a virtuális gépeken belüli beállítások naplózásához.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: cc2ba11f75da5f993b99c90e5d0cc1030003203e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257256"
---
# <a name="understand-azure-policys-guest-configuration"></a>Az Azure-szabályzat vendégkonfigurációjának megismerése

Az Azure-erőforrások naplózásán és [kijavításán](../how-to/remediate-resources.md) túl az Azure Policy naplózhatja a beállításokat a számítógépen belül. Az érvényesítést a Vendégkonfiguráció bővítmény és ügyfél végzi. A bővítmény az ügyfélen keresztül ellenőrzi a beállításokat, például a következőket:

- Az operációs rendszer konfigurációja
- Alkalmazás konfigurációja vagy jelenléte
- Környezeti beállítások

Az Azure Policy Vendégkonfiguráció jelenleg csak a gépen belüli beállításokat naplózza. Nem alkalmaz konfigurációkat.

## <a name="extension-and-client"></a>Kiterjesztés és ügyfél

A beállítások naplózásához egy gépen belül, a [virtuális gép bővítmény](../../../virtual-machines/extensions/overview.md) engedélyezve van. A bővítmény letölti a vonatkozó házirend-hozzárendelést és a megfelelő konfigurációs definíciót.

### <a name="limits-set-on-the-extension"></a>A kiterjesztésre beállított korlátok

A bővítmény a számítógépen belül futó alkalmazások ütközésének korlátozása érdekében a vendégkonfiguráció nem haladhatja meg a PROCESSZOR-kihasználtság 5%-át. Ez a korlátozás a beépített és az egyéni definíciókra egyaránt létezik.

## <a name="register-guest-configuration-resource-provider"></a>Vendégkonfigurációs erőforrás-szolgáltató regisztrálása

A Vendégkonfiguráció használata előtt regisztrálnia kell az erőforrás-szolgáltatót. Regisztrálhat a portálon keresztül vagy a PowerShellen keresztül. Az erőforrás-szolgáltató automatikusan regisztrálva lesz, ha a vendégkonfigurációs házirend hozzárendelése a portálon keresztül történik.

### <a name="registration---portal"></a>Regisztráció - Portál

Ha regisztrálni szeretné az erőforrás-szolgáltatót a Vendégkonfigurációhoz az Azure Portalon keresztül, kövesse az alábbi lépéseket:

1. Indítsa el az Azure Portalt, és kattintson az **Összes szolgáltatás elemre.** Keressen rá, és válassza **az Előfizetések**lehetőséget.

1. Keresse meg és kattintson arra az előfizetésre, amelyhez engedélyezni szeretné a Vendég konfigurációt.

1. Az **Előfizetés** lap bal oldali menüjében kattintson az **Erőforrás-szolgáltatók**gombra.

1. Szűrje a microsoft.GuestConfiguration mezőt, vagy görgessen addig, amíg meg nem találja a **Microsoft.GuestConfiguration**programot, majd kattintson a **Regisztráció** gombra ugyanabban a sorban.

### <a name="registration---powershell"></a>Regisztráció - PowerShell

Ha regisztrálni szeretné a Vendégkonfiguráció erőforrás-szolgáltatóját a PowerShellen keresztül, futtassa a következő parancsot:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>Érvényesítési eszközök

A számítógépen belül a vendégkonfigurációs ügyfél helyi eszközöket használ a naplózás futtatásához.

Az alábbi táblázat az egyes támogatott operációs rendszereken használt helyi eszközök listáját tartalmazza:

|Operációs rendszer|Érvényesítési eszköz|Megjegyzések|
|-|-|-|
|Windows|[A Windows PowerShell kívánt állapotkonfigurációja](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| A Ruby és a Python a Vendég konfigurációbővítmény által van telepítve. |

### <a name="validation-frequency"></a>Érvényesítési gyakoriság

A Vendég konfigurációs ügyfél 5 percenként ellenőrzi az új tartalmat. A vendég-hozzárendelés fogadása után a beállításokat 15 perces időközönként ellenőrzi a rendszer. Az eredményeket a rendszer elküldi a vendégkonfigurációs erőforrás-szolgáltatónak, amint a naplózás befejeződik. Amikor egy [házirend-kiértékelési eseményindító](../how-to/get-compliance-data.md#evaluation-triggers) történik, a számítógép állapota a vendég konfigurációs erőforrás-szolgáltató. A frissítés hatására az Azure Policy kiértékeli az Azure Resource Manager tulajdonságait. Egy igény szerinti Azure-szabályzat kiértékelése lekéri a legújabb értéket a vendég konfigurációs erőforrás-szolgáltató. Azonban nem indítja el a konfiguráció új naplózását a gépen belül.

## <a name="supported-client-types"></a>Támogatott ügyféltípusok

Az alábbi táblázat az Azure-lemezképek támogatott operációs rendszereinek listáját tartalmazza:

|Közzétevő|Név|Verziók|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ között|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Windows-ügyfél|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5, 7.6|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> A vendégkonfiguráció naplózhatja a támogatott operációs rendszert futtató csomópontokat. Ha egyéni lemezképet használó virtuális gépeket szeretne naplózni, meg kell kettőznie a **DeployIfNotExists** definíciót, és módosítania kell az **If** szakaszt a lemezkép tulajdonságainak felvételéhez.

### <a name="unsupported-client-types"></a>Nem támogatott ügyféltípusok

A Windows Server Nano Server egyetlen verzióban sem támogatott.

## <a name="guest-configuration-extension-network-requirements"></a>Vendégkonfiguráció-bővítmény hálózati követelményei

Az Azure vendégkonfigurációs erőforrás-szolgáltatójával való kommunikációhoz a gépeknek kimenő hozzáférésre van szükségük az Azure-adatközpontokhoz a **443-as**porton. Ha olyan magánvirtuális hálózatot használ az Azure-ban, amely nem engedélyezi a kimenő forgalmat, konfigurálja a kivételeket [a Hálózati biztonsági csoport](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) szabályaival.
A "GuestAndHybridManagement" [szolgáltatáscímke](../../../virtual-network/service-tags-overview.md) a Vendég konfigurációs szolgáltatásra hivatkozik.

## <a name="guest-configuration-definition-requirements"></a>Vendég konfigurációdefiníciójának követelményei

A Vendégkonfiguráció által futtatott minden egyes naplózáshoz két házirend-definíció, egy **DeployIfNotExists** és egy **AuditIfNotExists** definíció szükséges. A **DeployIfNotExists** definíció a vendégkonfigurációs ügynökkel és más összetevőkkel való előkészítésére szolgál az [ellenőrzőeszközök támogatásához.](#validation-tools)

A **DeployIfNotExists** házirend-definíció ellenőrzi és kijavítja a következő elemeket:

- Ellenőrizze, hogy a gép kapott-e kiértékelhető konfigurációt. Ha jelenleg nincs hozzárendelés, kapjuk meg a hozzárendelést, és készítse elő a gépet a következők szerint:
  - Hitelesítés a gépre [felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md) használatával
  - A **Microsoft.GuestConfiguration** bővítmény legújabb verziójának telepítése
  - [Érvényesítési eszközök](#validation-tools) és függőségek telepítése, ha szükséges

Ha a **DeployIfNotExists** hozzárendelés nem megfelelő, [egy szervizelési feladat](../how-to/remediate-resources.md#create-a-remediation-task) használható.

Ha a DeployIfNotExists hozzárendelés megfelelő, az AuditIfNotExists házirend-hozzárendelés a helyi ellenőrzési eszközök segítségével határozza meg, hogy a konfigurációs hozzárendelés megfelelő vagy nem megfelelő.Once the **DeployIfNotExists** assignment is Compliant, the **AuditIfNotExists** policy assignment uses the local validation tools to determine if the configuration assignment is Compliant or Not compliant. Az ellenőrző eszköz biztosítja az eredményeket a Vendég konfigurációs ügyfél. Az ügyfél továbbítja az eredményeket a vendég bővítménynek, amely elérhetővé teszi őket a Vendég konfigurációs erőforrás-szolgáltatón keresztül.

Az Azure Policy a vendégkonfigurációs erőforrás-szolgáltatók **complianceStatus** tulajdonságát használja a megfelelőségi csomópontmegfelelőség **jelentéséhez.** További információt a [Megfelelőségi adatok beszerzése](../how-to/get-compliance-data.md)című témakörben talál.

> [!NOTE]
> A **DeployIfNotExists** házirend szükséges az **AuditIfNotExists** házirend eredmények visszaadásához. A **DeployIfNotExists nélkül**az **AuditIfNotExists** házirend állapotként "0/0" erőforrásokat jelenít meg.

A Vendégkonfiguráció összes beépített házirendje szerepel egy olyan kezdeményezésben, amely csoportosítja a hozzárendelésekben használt definíciókat. A beépített kezdeményezés _ \[neve\]Preview: Audit Jelszó biztonsági beállítások linuxos és Windows-gépeken belül_ 18 házirendeket tartalmaz. Hat **DeployIfNotExists** és **AuditIfNotExists** pár van Windows-hoz és három pár Linuxra. A [házirend-definíciós](definition-structure.md#policy-rule) logika ellenőrzi, hogy csak a cél operációs rendszer kiértékelése.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Az operációs rendszer beállításainak naplózása az iparági alapértékek et követve

Az Azure Policy egyik kezdeményezése lehetővé teszi az operációs rendszer beállításainak naplózását a virtuális gépeken a Microsoft "alapkonfigurációja" után. A definíció, _ \[előzetes verzió:\]Az Azure biztonsági alapkonfiguráció-beállításainak meg nem felelő Windows-virtuális gépek naplózása_ az Active Directory csoportházirend beállításai alapján tartalmazza a naplózási szabályok teljes készletét.

A legtöbb beállítás paraméterként érhető el. Ez a funkció lehetővé teszi a naplózásra kerülő adatok testreszabását, hogy a szabályzat igazodjon a szervezeti követelményekhez, vagy hogy a szabályzatot harmadik féltől származó információkhoz, például az iparági szabályozási szabványokhoz rendelje.

Egyes paraméterek támogatják az egész értéktartományt. Például a Maximális jelszó kor paraméter beállítható egy tartomány operátor, hogy rugalmasságot biztosít a gép tulajdonosok. Naplózhatja, hogy a felhasználók jelszavának módosítására kötelező tényleges csoportházirend-beállítás legfeljebb 70 nap, de nem lehet kevesebb, mint egy nap. Amint azt az info-buborék a paraméter, hogy ez az üzleti politika a tényleges naplózási érték, állítsa az értéket "1,70".

Ha a szabályzatot egy Azure Resource Manager központi telepítési sablon használatával rendeli hozzá, paraméterfájl használatával kezelheti ezeket a beállításokat a forrásvezérlőből. Egy eszköz, például a Git segítségével kezelheti a naplózási házirendek módosításait, és minden egyes beadási dokumentumhoz megjegyzéseket fűzhet arra vonatkozóan, hogy miért kell egy hozzárendelésnek kivételt lennie a várt érték alól.

#### <a name="applying-configurations-using-guest-configuration"></a>Konfigurációk alkalmazása vendégkonfigurációval

Az Azure Policy legújabb funkciója konfigurálja a gépeken belüli beállításokat. A _definíció: Az időzóna konfigurálása Windows gépeken_ az időzóna konfigurálásával módosítja a gépet.

A _Konfigurálás_kezdetű definíciók hozzárendelésekénél a kiszolgáló előfeltételeit is hozzá kell rendelnie a _Vendégkonfigurációs házirend Windows-gépeken való engedélyezéséhez._ Ezeket a definíciókat egy kezdeményezésben is kombinálhatja, ha úgy dönt.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Házirendek hozzárendelése az Azure-on kívüli gépekhez

A vendégkonfigurációhoz elérhető naplózási házirendek közé tartozik a **Microsoft.HybridCompute/machines** erőforrástípusa. Az Azure Arc [for Servers kiszolgálókra](../../../azure-arc/servers/overview.md) beépített, a házirend-hozzárendelés hatálya alá tartozó gépek automatikusan belekerülnek.

### <a name="multiple-assignments"></a>Több hozzárendelés

Vendég konfigurációs házirendek jelenleg csak akkor támogatja, hogy ugyanazt a vendég-hozzárendelést egyszer egy gépen, akkor is, ha a házirend-hozzárendelés különböző paramétereket használ.

## <a name="built-in-resource-modules"></a>Beépített erőforrásmodulok

A Vendég konfigurációs bővítmény telepítésekor a "GuestConfiguration" PowerShell-modul a DSC-erőforrásmodulok legújabb verziója része. Ez a modul letölthető a PowerShell-galériából a "Kézi letöltés" linksegítségével a [GuestConfiguration](https://www.powershellgallery.com/packages/GuestConfiguration/)modullapról. A '.nupkg' fájlformátum átnevezhető ".zip" névre, hogy kibontsa és felültekintse.

## <a name="client-log-files"></a>Ügyfélnapló-fájlok

A Vendég konfigurációs bővítmény a következő helyekre írja a naplófájlokat:

Windows:`C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux:`/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Ahol `<version>` az aktuális verziószámra vonatkozik.

### <a name="collecting-logs-remotely"></a>Naplók távoli gyűjtése

A Vendég konfigurációs konfigurációk vagy -modulok hibaelhárításának `Test-GuestConfigurationPackage` első lépése ként a parancsmag használata kell, hogy legyen, a [Windows egyéni vendégkonfigurációs naplózási házirendjének létrehozásának](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows)lépéseit követve.
Ha ez nem sikerül, az ügyfélnaplók gyűjtése segíthet a problémák diagnosztizálásában.

#### <a name="windows"></a>Windows

Az Azure VM Run Command funkció használatával a Windows-gépek naplófájljaiból származó adatok rögzítéséhez a következő példa PowerShell-parancsfájl hasznos lehet. További információt a [PowerShell-parancsfájlok futtatása a Windows virtuális gépen a Run Para paranccsal című témakörben talál.](../../../virtual-machines/windows/run-command.md)

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Az Azure VM Run Command képesség használatával adatokat rögzíthet a linuxos gépek naplófájljaiból, a következő példa Bash parancsfájl hasznos lehet. További információ: [Shell szkriptek futtatása a Linux virtuális gépben a Run Para paranccsal című témakörben.](../../../virtual-machines/linux/run-command.md)

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Vendég konfigurációs minták

A Házirend vendégkonfigurációjának beépített kezdeményezéseinek forrása a következő helyeken érhető el:

- [Beépített házirend-definíciók – Vendég konfigurációja](../samples/built-in-policies.md#guest-configuration)
- [Beépített kezdeményezések – Vendégkonfiguráció](../samples/built-in-initiatives.md#guest-configuration)
- [Az Azure Policy mintát vesz a GitHub-tárból](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>További lépések

- Tekintse át a példákat az [Azure Policy-mintákban.](../samples/index.md)
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](effects.md).
- Ismerje meg, hogyan hozhat [létre programozott házirendeket.](../how-to/programmatically-create.md)
- További információ a [megfelelőségi adatok beszedéséről.](../how-to/get-compliance-data.md)
- További információ a [nem megfelelő erőforrások kiújulásáról.](../how-to/remediate-resources.md)
- Tekintse át, hogy mi a felügyeleti csoport az [Erőforrások rendszerezése az Azure felügyeleti csoportokkal.](../../management-groups/overview.md)
