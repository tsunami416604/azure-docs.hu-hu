---
title: Ajánlott biztonsági eljárások IaaS számítási feladatokat az Azure-ban |} A Microsoft Docs
description: " Az áttelepítés az Azure IaaS munkaterhelések számos lehetőséget kínál a tervek reményeket lehetőségeket "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2018
ms.author: barclayn
ms.openlocfilehash: 6033a61351423e65490edfe0b0607f2395c80f86
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52498350"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Ajánlott biztonsági eljárások IaaS számítási feladatokhoz az Azure-ban

A legtöbb infrastruktúra-szolgáltatás (IaaS) esetben [Azure-beli virtuális gépek (VM)](https://docs.microsoft.com/azure/virtual-machines/) a fő munkaterhelés olyan szervezeteknek, amelyek használják a felhő számítási rendszer. A tény, egyértelmű az [hibrid forgatókönyvek](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) szervezetek számára, ahová a lassú számítási feladatok migrálása a felhőbe. Ilyen esetben hajtsa végre a [IaaS általános biztonsági szempontjai](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx), és a alkalmazni ajánlott biztonsági eljárások a virtuális gépek.

A biztonsági felelősséget a felhőszolgáltatás típusát alapul. Az alábbi táblázat foglalja össze a Microsoft és az Ön felelőssége egyenleg:

![A felelősségi területeket](./media/azure-security-iaas/sec-cloudstack-new.png)

Biztonsági követelmények számos tényező befolyásolja, többek között a különböző típusú számítási változhat. Ajánlott eljárások valamelyikét biztonságossá teheti a saját maga rendszereit. Mint minden más a biztonság hogy válassza ki a megfelelő beállításokat, és hogyan a megoldások is kiegészíti egymást hézagok kitöltésével.

Ez a cikk bemutatja a bevált biztonsági gyakorlatokat a virtuális gépek és az operációs rendszerek.

Az ajánlott eljárások a vélemény konszenzus alapulnak, és dolgozhat az aktuális Azure platform olyan képességeit, és a szolgáltatáskészletek. Vélemények és technológiák idővel változhat, mert ez a cikk változásokkal frissülni fog.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Hitelesítés és hozzáférés-vezérlés használatával virtuális gépek védelme
Az első lépés a virtuális gépek védelmére, győződjön meg arról, hogy csak jogosult felhasználók állítsa be új virtuális gépek és virtuális gépek hozzáférést.

**Ajánlott eljárás**: ellenőrzési Virtuálisgép-hozzáférés.   
**Részletes**: használata [Azure házirendek](../azure-policy/azure-policy-introduction.md) egyezmények az erőforrások létrehozásához a szervezetben, és létrehozzon testreszabott házirendeket. Ezek a szabályzatok alkalmazása erőforrások, például [erőforráscsoportok](../azure-resource-manager/resource-group-overview.md). Virtuális gépek egy erőforráscsoporthoz tartozó öröklik a rájuk vonatkozó szabályzatoknak.

Ha a szervezet több előfizetéssel rendelkezik, szüksége lehet hatékonyan kezelheti a hozzáférést, a házirendek és a megfelelőségi ezen előfizetések esetében. [Az Azure felügyeleti csoportok](../azure-resource-manager/management-groups-overview.md) adja meg a fenti előfizetések hatókörének szintjét. Előfizetéseinek felügyeleti csoportokba való rendezésére (tárolók), és ezeket a csoportokat a szabályozási feltételek vonatkoznak. A felügyeleti csoporton belül az összes előfizetés automatikusan örökli a alkalmazni a csoport feltételeket. A felügyeleti csoportok nagy léptékű, nagyvállalati szintű felügyeletet tesznek lehetővé, függetlenül az előfizetése típusától.

**Ajánlott eljárás**: csökkentheti a telepítő és a virtuális gépek üzembe helyezési változékonyságát.   
**Részletes**: használata [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) megerősítése az üzembe helyezési lehetősége, és könnyebben megértéséhez, valamint a környezetében a virtuális gépek leltár-sablonokat.

**Ajánlott eljárás**: emelt szintű hozzáférés biztonságossá tétele.   
**Részletes**: használja a [legalacsonyabb jogosultsági megközelítés](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) és eléréséhez, és állítsa be a virtuális gépek az Azure beépített szerepkört:

- [Virtuális gépek Közreműködője](../role-based-access-control/built-in-roles.md#virtual-machine-contributor): kezelheti a virtuális gépek, de nem a virtuális hálózati vagy tárolási fiók, amelyhez csatlakoznak.
- [Virtuális gépek hagyományos Közreműködője](../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): kezelheti a klasszikus üzemi modellben, de nem a virtuális hálózati vagy tárolási fiók, amelyhez a virtuális gépek csatlakoznak használatával létrehozott virtuális gépek.
- [Biztonsági rendszergazda](../role-based-access-control/built-in-roles.md#security-admin): A Security Center csak: megtekintheti biztonsági házirendek, biztonsági állapotot, szerkessze a biztonsági szabályzatok, riasztások megtekintése és javaslatok, riasztások és javaslatok elvetése.
- [DevTest Labs-felhasználó](../role-based-access-control/built-in-roles.md#devtest-labs-user): is mindent megtekinthessen, és csatlakozzon, indítsa el, indítsa újra a és leállíthatja a virtuális gépeket.

Az előfizetés-adminisztrátorok és a társrendszergazdákat módosíthatja ezt a beállítást, így azok egy adott előfizetés összes virtuális gép Rendszergazdák. Győződjön meg arról, hogy az összes előfizetés-adminisztrátorok és jelentkezzen be a gépek bármelyikét társrendszergazdákat megbízik.

> [!NOTE]
> Azt javasoljuk, hogy Ön kialakíthattunk virtuális gépek az azonos életciklussal rendelkező ugyanabban az erőforráscsoportban. Erőforráscsoportok használatával telepíthet, figyelése és az erőforrások költségeket erőforráscsoportonként.
>
>

Szervezetek, amelyek vezérlik a Virtuálisgép-hozzáférés és a telepítő a teljes virtuális gép a biztonság növelése.

## <a name="use-multiple-vms-for-better-availability"></a>A jobb rendelkezésre állás érdekében több virtuális gép használata
Ha a virtuális gép fut, szeretné, hogy a magas rendelkezésre állást igénylő kritikus fontosságú alkalmazásai, javasoljuk, hogy több virtuális gép használja. A jobb rendelkezésre állás érdekében használjon egy [rendelkezésre állási csoport](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

Rendelkezésre állási logikus csoportosításai, amelyek garantálják, hogy a Virtuálisgép-erőforrások benne legyenek különítve egymástól, az Azure-adatközpontban üzembe helyezésük használhatja az Azure-ban. Az Azure gondoskodik arról, hogy a virtuális gépeket helyez egy rendelkezésre állási készlet futtatási több fizikai kiszolgálót, a számítási állványokon, a tárolási egységek és a hálózati kapcsolók. Ha hardveres vagy Azure szoftveres hiba lép fel, a virtuális gépeknek csak egy részhalmazát érinti, és a teljes alkalmazás továbbra is elérhető az ügyfelek számára. A rendelkezésre állási csoportok egy alapvető funkció akkor, ha a megbízható felhőalapú megoldásokat létrehozni.

## <a name="protect-against-malware"></a>Kártevők elleni védelem
Telepítenie kell a kártevőkkel szembeni védelem, azonosításához és eltávolításához a vírusok, kémprogramok és más, kártevő szoftverek. Telepíthet [Microsoft Antimalware](azure-security-antimalware.md) vagy egy Microsoft-partner végpontvédelmi megoldás ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [A Windows Defender](https://www.microsoft.com/search/result.aspx?q=Windows+defender+endpoint+protection), és [System Center Endpoint Protection](https://www.microsoft.com/search/result.aspx?q=System+Center+endpoint+protection)).

Microsoft Antimalware-t a szolgáltatások, mint például a valós idejű védelem, ütemezett vizsgálatát, kártevő szoftverek eltávolítása, aláírás frissítések, kártevőfrissítések, reporting mintákat és kizárási eseménygyűjtés tartalmazza. Az éles környezetben a külön-külön üzemeltetett környezetben használhatja a kártevőirtó bővítmény védelmet a virtuális gépek és felhőszolgáltatások segítségével.

Integrálható a Microsoft Antimalware-t és a partneri megoldások [az Azure Security Center](https://docs.microsoft.com/azure/security-center/) könnyű üzembe helyezés és a beépített észlelések (riasztások és incidensek).

**Ajánlott eljárás**: egy kártevőirtó megoldást, kártevő szoftverek elleni védelem érdekében telepíteni.   
**Részletes**: [a Microsoft partneri megoldás és a Microsoft Antimalware telepítése](../security-center/security-center-install-endpoint-protection.md)

**Ajánlott eljárás**: a kártevőirtó megoldások integrálása az alkalmazásvédelmi állapotának figyelése a Security Center.   
**Részletes**: [a Security Centerrel végpontvédelmi problémák kezelése](../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>A virtuális gép frissítéseinek kezelése
Az Azure virtuális gépek, például az összes helyszíni virtuális gépek úgy van kialakítva, hogy a felhasználó által felügyelt lehet. Az Azure nem Windows-frissítések mappához történő küldéssel őket. A virtuális gép frissítéseinek kezelése kell.

**Ajánlott eljárás**: a virtuális gépek naprakészen tartása.   
**Részletes**: használja a [az Update Management](../automation/automation-update-management.md) megoldás az Azure-ban, a helyszíni környezetekben vagy más felhőben üzembe helyezett Windows és Linux számítógépek operációs rendszer frissítéseinek kezelése az Azure automationben szolgáltatók. Az elérhető frissítések állapota minden ügynökszámítógépen egyszerűen felmérhető, és felügyelhető a kiszolgálók szükséges frissítéseinek telepítése is.

Update Management által felügyelt számítógépekre, hajtsa végre az értékelési és frissítéstelepítések a következő konfigurációk használatával:

- A Microsoft Monitoring Agent (MMA) for Windows vagy Linux rendszeren
- PowerShell-célállapotkonfiguráció (DSC) Linux rendszerre
- Automation hibrid runbook-feldolgozó
- A Microsoft Update vagy Windows Server Update Services (WSUS) Windows-számítógépekhez

Windows Update segítségével, hagyja a Windows Update automatikus beállítás engedélyezve van.

**Ajánlott eljárás**: Ellenőrizze, hogy az Ön által készített rendszerképekből tartalmazzák a Windows-frissítések legutóbbi ciklikus üzembe helyezéskor.   
**Részletes**: keresése és telepítése az összes Windows frissíti, minden üzembe helyezés az első lépés. Ez a mérték különösen fontos a alkalmazni, vagy a saját könyvtár származó lemezképek központi telepítésekor. Az Azure Piactérről származó rendszerképek alapértelmezés szerint automatikusan frissülnek, bár lehet késéssel (akár néhány hét) egy nyilvános kiadása után.

**Ajánlott eljárás**: rendszeres időközönként ismételt üzembe helyezése a virtuális gépek kényszerített egy friss az operációsrendszer-verziót.   
**Részletes**: Adja meg a virtuális Gépet egy [Azure Resource Manager-sablon](../azure-resource-manager/resource-group-authoring-templates.md) így könnyen ugyanakkor azt. Egy sablon használatával biztosítja a javított és biztonságos virtuális gép szükség esetén.

**Ajánlott eljárás**: telepítse a legújabb biztonsági frissítéseket.   
**Részletes**: az első számítási feladatokat, hogy ügyfeleink az Azure-bA néhány labs és kívülre irányuló rendszerek. Az Azure-beli virtuális gépek üzemeltetéséhez, alkalmazásokat vagy szolgáltatásokat, kell lennie az interneten érhető el, ha éberen javítással kapcsolatban. Javítás az operációs rendszer mellett. A külső alkalmazások veszéllyel biztonsági réseket is vezethet problémákat, amelyek elkerülhető, ha a helyes javításkezelőhöz van beállítva.

**Ajánlott eljárás**: Telepítse és tesztelje a biztonsági mentési megoldást.   
**Részletes**: biztonsági másolatot kell kezelni ugyanolyan módon, hogy kezelje-e fel más műveletekhez. Ez a rendszerek, amelyek részei az éles környezet kiterjesztése a felhőbe.

Teszt és fejlesztési rendszerek hasonló felhasználók hozzászoktak rendelkezik a helyszíni környezetben szerzett tapasztalataikról alapján visszaállítási képességeket biztosító biztonsági mentési stratégia kell követnie. Éles számítási feladatok Azure-bA áthelyezni kell integrálható a meglévő biztonsági mentési megoldások, amikor csak lehetséges. Vagy használhat [Azure Backup](../backup/backup-azure-vms-first-look-arm.md) segítségünkre a biztonsági mentés követelményeinek.

Szervezetek számára, amelyek a szoftverfrissítési szabályzatok kényszerítése nem több, biztonsági réseit ismert, korábban rögzített fenyegetések érhetők el. Iparági előírások teljesítéséhez a vállalatok bizonyítania kell, hogy azok a szokott, és a megfelelő biztonsági vezérlők segítségével a számítási feladatok biztonságát a felhőben található.

Szoftverfrissítés-ajánlott eljárások a hagyományos adatközpontok és az Azure IaaS számos Hasonlóságok. Azt javasoljuk, hogy az aktuális szoftverfrissítési szabályzatok az Azure-ban található virtuális gépekre való értékeli.

## <a name="manage-your-vm-security-posture"></a>Kezelheti a virtuális gép biztonsági rendszer kialakításához
Kibertámadások folyamatosan fejlődik. Egy figyelési képesség, gyorsan észlelheti a fenyegetéseket, az erőforrások való illetéktelen hozzáférés megakadályozása, riasztások aktiválása céljából, és csökkenti a vakriasztások a virtuális gépek védelmére van szükség.

A biztonsági állapotát, figyelheti a [Windows](../security-center/security-center-virtual-machine.md) és [Linux rendszerű virtuális gépek](../security-center/security-center-linux-virtual-machine.md), használja [az Azure Security Center](../security-center/security-center-intro.md). A Security Centerben védelme érdekében a virtuális gépek a következő lehetőségeket kihasználva:

- Operációs rendszer biztonsági beállításai ajánlott konfigurációs szabályokkal a alkalmazni.
- Azonosítsa, és töltse le a rendszer biztonsági és kritikus frissítések hiányoznak.
- Helyezze üzembe a végpont kártevőkkel szembeni védelem vonatkozó javaslatokat.
- Lemeztitkosítás ellenőrzése.
- Mérje fel, és a biztonsági rések.
- Fenyegetések észlelése.

A Security Center folyamatosan figyelheti a fenyegetéseket, és a potenciális fenyegetéseket a biztonsági riasztásokban érhetők el. Biztonsági incidensek nevű egyetlen nézetben korrelált fenyegetések vannak összesítve.

A Security Center az adatokat tároló [Azure Log Analytics](../log-analytics/log-analytics-overview.md). A log Analytics betekintést nyerhet az alkalmazások és erőforrások működését, a lekérdezési nyelvet és elemzési motort biztosít. Emellett adatgyűjtés [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md), megoldásokat, és a virtuális gépeken a felhőben vagy a helyszínen telepített ügynökök. A közös funkcióknak köszönhetően átfogó képet alkothat a környezetről.

Szervezetek számára, amelyek a virtuális gépek nem erős biztonság kényszerítése nélkül is működőképesek maradhatnak a jogosulatlan felhasználók biztonsági vezérlők megkerülésére lehetséges kísérletek maradnak.

## <a name="monitor-vm-performance"></a>Virtuális gép teljesítményének monitorozása
Erőforráshoz való visszaélés problémát jelenthetnek, ha a virtuális gép folyamat több erőforrást, mint azok kell. Teljesítménnyel kapcsolatos problémák a virtuális gép a szolgáltatáskimaradás, amely megsérti a rendelkezésre állási rendszerbiztonsági tagot vezethet. Ez akkor különösen fontos, az IIS vagy más webkiszolgálók futtató virtuális gépek, mert a magas CPU- vagy memóriahasználatra is alapozható jelezheti a szolgáltatásmegtagadási (DoS) támadások. Probléma jelentkezik, míg nem csak reaktív figyelése a Virtuálisgép-hozzáférési feltétlenül szükséges, hanem alapteljesítményének a normál működés során mért szemben proaktív módon.

Javasoljuk, hogy használjon [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) , a resource health értékes információkhoz juthat. Azure Monitor-funkciók:

- [Erőforrás diagnosztikai naplófájlok](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md): a Virtuálisgép-erőforrások figyeli, és azonosítja a potenciális problémákat, mely veszélyeztetheti a teljesítmény és rendelkezésre állás.
- [Az Azure Diagnostics bővítmény](../monitoring-and-diagnostics/azure-diagnostics.md): Windows virtuális gépek monitorozási és diagnosztikai képességeket biztosít. Ezek a képességek engedélyezéséhez a kiterjesztéssel együtt részeként a [Azure Resource Manager-sablon](../virtual-machines/windows/extensions-diagnostics-template.md).

Szervezetek számára, amelyek nem a virtuális gép teljesítményének figyelése nem tudja megállapítani, bizonyos változtatásokat a teljesítmény minták-e normál és rendellenes. Egy virtuális Gépet, amely értéke a szokásosnál több erőforrást egy külső erőforrás vagy a virtuális Gépen futó feltört folyamat a támadás utalhat.

## <a name="encrypt-your-virtual-hard-disk-files"></a>A virtuálismerevlemez-fájlok titkosítása
Azt javasoljuk, hogy a titkosítási kulcsok és titkok mellett a virtuális merevlemezek (VHD) a rendszerindító kötet és a tárolás, adatok kötetek védelme érdekében titkosítja.

[Az Azure Disk Encryption](azure-security-disk-encryption-overview.md) segítséget nyújt a Windows és Linux rendszerű IaaS virtuális gépek lemezeinek titkosításához. Az Azure Disk Encryption használja az iparági szabványnak megfelelő [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) Windows szolgáltatása és a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) biztosít az operációs rendszer és az adatlemezek kötettitkosítását Linux funkcióját. A megoldás integrált [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) segítségével vezérelheti és felügyelheti a lemeztitkosítási kulcsokat és titkos kulcsokat a key vault-előfizetés. A megoldás emellett biztosítja, hogy a virtuálisgép-lemezeken lévő összes adatot is titkosítása az Azure Storage-ban.

Ajánlott eljárások az Azure Disk Encryption használatával a következők:

**Ajánlott eljárás**: virtuális gépek titkosításának engedélyezése.   
**Részletes**: az Azure Disk Encryption állít elő, és írja a titkosítási kulcsok a key vaultban. Az Azure AD-hitelesítés kezelése a titkosítási kulcsok a key vaultban lévő igényel. Hozzon létre egy Azure AD-alkalmazást erre a célra. Hitelesítési célra használhatja vagy titkos kulcs-alapú ügyfélhitelesítés vagy [az Azure AD ügyféltanúsítvány-alapú ügyfél-hitelesítés](../active-directory/active-directory-certificate-based-authentication-get-started.md).

**Ajánlott eljárás**: egy további titkosítási kulcsok biztonsági réteget a kulcstitkosítási kulcs-(KEK) használja. Egy KEK hozzáadása a kulcstartóhoz.   
**Részletes**: használja a [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) parancsmaggal hozzon létre egy fő titkosítási kulcsot a key vaultban. A helyszíni hardveres biztonsági modul (HSM) a kulcskezeléshez is importálhat egy KEK. További információkért lásd: a [Key Vault-dokumentáció](../key-vault/key-vault-hsm-protected-keys.md). Amikor egy kulcsalapú titkosítás kulcsa van megadva, az Azure Disk Encryption a kulcs segítségével burkolhatja a titkosítási titkos kulcsait a Key Vault írása előtt. Ezt a kulcsot egy letéti másolatát tárolja egy helyszíni HSM kulcskezelés kulcsok a véletlen törlés elleni további védelmet nyújt.

**Ajánlott eljárás**: igénybe vehet egy [pillanatkép](../virtual-machines/windows/snapshot-copy-managed-disk.md) és/vagy lemezek előtt biztonsági másolatot készíteni. Biztonsági másolatok adja meg a helyreállítási beállítást, ha a nem várt hiba történik a titkosítás során.   
**Részletes**: a felügyelt lemezekkel rendelkező virtuális gépek biztonsági igényelnek, a titkosítás előtt. Biztonsági másolat elkészítése után használhatja a **Set-azurermvmdiskencryptionextension parancs** parancsmag megadásával a felügyelt lemezek titkosítása a *- skipVmBackup* paraméter. Biztonsági mentése és visszaállítása titkosított virtuális gépek kapcsolatos további információkért lásd: a [Azure Backup](../backup/backup-azure-vms-encryption.md) cikk.

**Ajánlott eljárás**: ahhoz, hogy a titkosítási titkos kulcsok nem adatbázisközi regionális határokat, az Azure Disk Encryption van szüksége, a key vaulttal és a virtuális gépeket ugyanabban a régióban kell lenniük.   
**Részletes**: létrehozása és használata a key vault, amely ugyanabban a régióban, mint a virtuális gép titkosítását.

Amikor az Azure Disk Encryption alkalmazza, a következő üzleti igényekre is képes eleget tenni:

- IaaS virtuális gépek biztonságosak, iparági szabványnak megfelelő titkosítási technológia szervezeti biztonsági és megfelelőségi követelmények teljesítésére keresztül inaktív.
- IaaS virtuális gépek indítsa el az ügyfél által felügyelt kulcsok és szabályzatok, és használatuk a key vaultban lévő naplózhatók.

## <a name="next-steps"></a>További lépések
Lásd: [Azure ajánlott biztonsági eljárások és minták](security-best-practices-and-patterns.md) további ajánlott biztonsági eljárások szeretne használni, amikor a tervezése, telepítése, és a felhőalapú megoldások kezelése az Azure használatával.

Az alábbi forrásanyagokat biztosít az Azure biztonsági és a kapcsolódó Microsoft-szolgáltatások kapcsolatos általános információk érhetők el:
* [Az Azure Security csapat blogja](https://blogs.msdn.microsoft.com/azuresecurity/) – az Azure Security legújabb naprakész információk
* [A Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – Ha a jelenteni lehet a Microsoft biztonsági réseket, beleértve a problémák az Azure-ral, vagy az e-mailen keresztül secure@microsoft.com
