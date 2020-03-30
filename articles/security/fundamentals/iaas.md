---
title: Az Azure-beli IaaS-számítási feladatok biztonsági gyakorlati tanácsai | Microsoft dokumentumok
description: " A számítási feladatok Azure IaaS-be való áttelepítése lehetőséget teremt a tervek újraértékelésére "
services: security
documentationcenter: na
author: barclayn
manager: rkarlin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: barclayn
ms.openlocfilehash: 0a4daf61d6b791a01f5bfb18e6cfca8118b2f421
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255944"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Ajánlott biztonsági eljárások IaaS számítási feladatokhoz az Azure-ban
Ez a cikk a virtuális gépek és az operációs rendszerek biztonsági gyakorlati tanácsait ismerteti.

Az ajánlott eljárások a véleménykonszenzuson alapulnak, és a jelenlegi Azure platform-képességekkel és szolgáltatáskészletekkel működnek. Mivel a vélemények és a technológiák idővel változhatnak, ez a cikk frissül, hogy tükrözze ezeket a változásokat.

A legtöbb infrastruktúra szolgáltatásként (IaaS) [forgatókönyvek, Az Azure virtuális gépek (VM-ek)](/azure/virtual-machines/) a fő számítási feladatok a felhőalapú számítástechnika használó szervezetek számára. Ez a tény nyilvánvaló a [hibrid forgatókönyvekben,](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) ahol a szervezetek szeretnék lassan áttelepíteni a számítási feladatokat a felhőbe. Ilyen esetekben kövesse az [IaaS általános biztonsági szempontjait,](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)és alkalmazza a biztonsági gyakorlati tanácsokat az összes virtuális gépre.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Virtuális gépek védelme hitelesítéssel és hozzáférés-vezérléssel
A virtuális gépek védelmének első lépése annak biztosítása, hogy csak a jogosult felhasználók állíthassanak be új virtuális gépeket és a virtuális gépekhez való hozzáférést.

> [!NOTE]
> A Linux-alapú virtuális gépek azure-beli biztonságának növelése érdekében integrálhatja az Azure AD-hitelesítéssel. Ha [Az Azure AD-hitelesítés linuxos virtuális gépek,](/azure/virtual-machines/linux/login-using-aad)központilag szabályozhatja és kényszerítheti a virtuális gépekhez való hozzáférést engedélyező vagy megtagadása házirendek.
>
>

**Ajánlott eljárás:** A virtuális gép elérésének szabályozása.   
**Részletes:** [Azure-szabályzatok](/azure/azure-policy/azure-policy-introduction) használatával szabályokat hozhat létre a szervezet erőforrásaira vonatkozóan, és testre szabott szabályzatokat hozhat létre. Alkalmazza ezeket a házirendeket erőforrásokra, például [erőforráscsoportokra.](/azure/azure-resource-manager/resource-group-overview) Az erőforráscsoporthoz tartozó virtuális gépek öröklik a házirendeket.

Ha a cég több előfizetéssel rendelkezik, szüksége lehet egy hatékony módszerre az előfizetések hozzáférésének, szabályzatainak és megfelelőségének kezelésére. [Az Azure felügyeleti csoportok](/azure/azure-resource-manager/management-groups-overview) az előfizetések feletti hatókört biztosítanak. Az előfizetéseket felügyeleti csoportokba (tárolókba) rendezheti, és a cégirányítási feltételeket ezekre a csoportokra alkalmazhatja. A felügyeleti csoporton belüli összes előfizetés automatikusan örökli a csoportra vonatkozó feltételeket. A felügyeleti csoportok nagy léptékű, nagyvállalati szintű felügyeletet tesznek lehetővé, függetlenül az előfizetése típusától.

**Ajánlott eljárás:** Csökkentse a variabilitást a virtuális gépek beállításában és üzembe helyezésében.   
**Részlet:** Az [Azure Resource Manager-sablonok](/azure/azure-resource-manager/resource-group-authoring-templates) használatával erősítse a telepítési lehetőségeket, és megkönnyítse a virtuális gépek megértését és leltározását a környezetben.

**Ajánlott eljárás:** Biztonságos emelt szintű hozzáférés.   
**Részlet:** A [legkevesebb jogosultsági szintű megközelítés](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) és a beépített Azure-szerepkörök használatával engedélyezheti a felhasználók számára a virtuális gépek elérését és beállítását:

- [Virtuálisgép-közreműködő:](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)Kezelheti a virtuális gépeket, de nem tudja azt a virtuális hálózatot vagy tárfiókot, amelyhez kapcsolódnak.
- [Klasszikus virtuálisgép-közreműködő:](../../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor)A klasszikus központi telepítési modell használatával létrehozott virtuális gépek et kezelheti, de nem azt a virtuális hálózatot vagy tárfiókot, amelyhez a virtuális gépek csatlakoznak.
- [Biztonsági rendszergazda](../../role-based-access-control/built-in-roles.md#security-admin): Csak a Biztonsági központban: Megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, szerkesztheti a biztonsági házirendeket, megtekintheti a riasztásokat és javaslatokat, elvetheti a riasztásokat és a javaslatokat.
- [DevTest Labs user](../../role-based-access-control/built-in-roles.md#devtest-labs-user): Mindent megtekinthet, és csatlakozhat, elindíthatja, újraindíthatja és leállíthatja a virtuális gépeket.

Az előfizetés-rendszergazdák és a társrendszergazdák módosíthatják ezt a beállítást, így az előfizetésben lévő összes virtuális gép rendszergazdáivá válhatnak. Győződjön meg arról, hogy megbízik az összes előfizetés-rendszergazdában és társadminisztrátorban, hogy bármelyik gépére bejelentkezhet.

> [!NOTE]
> Azt javasoljuk, hogy az azonos életciklusú virtuális gépeket ugyanabba az erőforráscsoportba konszolidálja. Erőforráscsoportok használatával üzembe helyezheti, figyelheti és összesítheti az erőforrások számlázási költségeit.
>
>

A virtuális gépek elérését és beállítását vezérlő szervezetek javítják a virtuális gépek általános biztonságát.

## <a name="use-multiple-vms-for-better-availability"></a>Több virtuális gép használata a jobb elérhetőség érdekében
Ha a virtuális gép olyan kritikus alkalmazásokat futtat, amelyekmagas rendelkezésre állással kell rendelkezniük, javasoljuk, hogy több virtuális gépet használjon. A jobb rendelkezésre állás érdekében használjon [rendelkezésre állási készletet](../../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) vagy rendelkezésre [állási zónákat.](../../availability-zones/az-overview.md)

A rendelkezésre állási csoport egy logikai csoportosítás, amely az Azure-ban annak biztosítására, hogy a virtuális gép erőforrásokat helyez el benne vannak elkülönítve egymástól, amikor üzembe helyezett egy Azure-adatközpontban. Az Azure biztosítja, hogy a rendelkezésre állási csoportba helyezett virtuális gépek több fizikai kiszolgálón, számítási állványokon, tárolóegységeken és hálózati kapcsolókon fussanak. Ha hardver- vagy Azure-szoftverhiba lép fel, a virtuális gépeknek csak egy részhalmaza érintett, és a teljes alkalmazás továbbra is elérhető az ügyfelek számára. A rendelkezésre állási készletek alapvető fontosságúak, ha megbízható felhőalapú megoldásokat szeretne építeni.

## <a name="protect-against-malware"></a>Védekezés a kártevők ellen
A vírusok, kémprogramok és egyéb rosszindulatú szoftverek azonosítása és eltávolítása érdekében kártevőirtó védelmet kell telepítenie. Telepítheti a [Microsoft Antimalware](antimalware.md) vagy a Microsoft partnere végpontvédelmi megoldását ([Trend Micro,](https://help.deepsecurity.trendmicro.com/Welcome.html) [Broadcom,](https://www.broadcom.com/products) [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/windows/comprehensive-security)és System [Center Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)).

A Microsoft Antimalware olyan funkciókat tartalmaz, mint a valós idejű védelem, az ütemezett vizsgálat, a kártevők szervizelése, az aláírásfrissítések, a motorfrissítések, a mintajelentések és a kizárási események gyűjtése. Az éles környezettől elkülönítve üzemeltetett környezetekben egy kártevőirtó bővítmény t, amely segít megvédeni a virtuális gépeket és a felhőalapú szolgáltatásokat.

Integrálhatja a Microsoft antimalware és partner megoldások [az Azure Security Center](../../security-center/index.yml) a könnyű üzembe helyezés és a beépített észlelések (riasztások és incidensek).

**Ajánlott eljárás:** Telepítsen kártevőirtó megoldást a kártevők elleni védelemre.   
**Részletes :** [Microsoft-partnermegoldás vagy Microsoft Antimalware telepítése](../../security-center/security-center-install-endpoint-protection.md)

**Ajánlott eljárás:** Integrálja kártevőirtó megoldását a Security Centerrel a védelem állapotának figyeléséhez.   
**Részletek**: [Végpontvédelmi problémák kezelése a Biztonsági központtal](../../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>A virtuális gép frissítéseinek kezelése
Az Azure virtuális gépek, mint minden helyszíni virtuális gépek, célja, hogy a felhasználó által felügyelt. Ezekre az Azure nem küldi le a Windows-frissítéseket. A virtuális gép frissítéseit kell kezelnie.

**Ajánlott eljárás:** Tartsa naprakészen a virtuális gépeket.   
**Részlet:** Az Azure Automation [frissítéskezelési](../../automation/automation-update-management.md) megoldásával kezelheti az Azure-ban, a helyszíni környezetekben vagy más felhőszolgáltatókban telepített Windows- és Linux-számítógépek operációsrendszer-frissítéseit. Az elérhető frissítések állapota minden ügynökszámítógépen egyszerűen felmérhető, és felügyelhető a kiszolgálók szükséges frissítéseinek telepítése is.

A Frissítéskezelés által kezelt számítógépek a következő konfigurációk használatával hajtják végre a felméréseket és frissítik az üzemelő példányokat:

- Microsoft Monitoring Agent (MMA) Windows vagy Linux rendszerhez
- PowerShell-célállapotkonfiguráció (DSC) Linux rendszerre
- Automation hibrid runbook-feldolgozó
- Microsoft Update vagy Windows Server Update Services (WSUS) Windows-számítógépekhez

A Windows Update használata esetén hagyja békén az automatikus Windows Update beállítást.

**Ajánlott eljárás:** A központi telepítés során győződjön meg arról, hogy a rendszerképek a Windows-frissítések legutóbbi körét tartalmazzák.   
**Részlet:** Minden telepítés első lépéseként keresse meg és telepítse az összes Windows-frissítést. Ez az intézkedés különösen fontos, hogy alkalmazza a lemezképek, amelyek ön vagy a saját könyvtár. Bár az Azure Piactérről származó képek alapértelmezés szerint automatikusan frissülnek, a nyilvános kiadás után (akár néhány hétig) is előfordulhat.

**Ajánlott eljárás:** Rendszeresen telepítse újra a virtuális gépeket az operációs rendszer új verziójának kényszerítéséhez.   
**Részlet:** Határozza meg a virtuális gép egy [Azure Resource Manager sablon,](../../azure-resource-manager/templates/template-syntax.md) így könnyen újraüzembe helyezheti azt. A sablon használatával egy javított és biztonságos virtuális gép, amikor szüksége van rá.

**Ajánlott eljárás:** A biztonsági frissítések gyors alkalmazása a virtuális gépeken.   
**Részletes :** Engedélyezze az Azure Security Center (ingyenes szint vagy standard szint) számára a [hiányzó biztonsági frissítések azonosítását és alkalmazását.](../../security-center/security-center-apply-system-updates.md)

**Gyakorlati tanács:** Telepítse a legújabb biztonsági frissítéseket.   
**Részletes:** Az első számítási feladatok, amelyekaz ügyfelek áthelyezni az Azure-ban a laborok és a külső néző rendszerek. Ha az Azure-beli virtuális gépek olyan alkalmazásokat vagy szolgáltatásokat üzemeltetnek, amelyeknek elérhetőnek kell lenniük az interneten, legyen ekképp figyeljen a javításra. Patch túl az operációs rendszer. A partneralkalmazások nem javított biztonsági rései olyan problémákhoz is vezethetnek, amelyek elkerülhetők, ha jó javításkezelés van érvényben.

**Ajánlott eljárás:** Biztonsági mentési megoldás telepítése és tesztelése.   
**Részlet:** A biztonsági mentést ugyanúgy kell kezelni, mint bármely más műveletet. Ez igaz az okra a rendszerekre, amelyek az éles környezet részét képezik a felhőre kiterjesztve.

A tesztelési és fejlesztési rendszereknek olyan biztonsági mentési stratégiákat kell követniük, amelyek a felhasználók által megszoktak, a helyszíni környezetekben szerzett tapasztalatokhoz hasonló visszaállítási képességeket biztosítanak. Az Azure-ba áthelyezett éles számítási feladatoknak lehetőség szerint integrálniuk kell a meglévő biztonsági mentési megoldásokkal. Vagy használhatja [az Azure Backup](../../backup/backup-azure-vms-first-look-arm.md) segítségével a biztonsági mentési követelmények teljesítéséhez.

Azok a szervezetek, amelyek nem kényszerítik ki a szoftverfrissítési házirendeket, jobban ki vannak téve az ismert, korábban javított biztonsági réseket kihasználó fenyegetéseknek. Az iparági előírásoknak való megfelelés érdekében a vállalatoknak bizonyítaniuk kell, hogy szorgalmasak, és megfelelő biztonsági ellenőrzéseket használnak a felhőben található munkaterhelések biztonságának biztosítása érdekében.

A hagyományos adatközpontok és az Azure IaaS szoftverfrissítési gyakorlati tanácsai számos hasonlóságot mutatnak. Azt javasoljuk, hogy értékelje ki a jelenlegi szoftverfrissítési szabályzatok, hogy az Azure-ban található virtuális gépek.

## <a name="manage-your-vm-security-posture"></a>A virtuális gép biztonsági állapotának kezelése
A kiberfenyegetések folyamatosan fejlődnek. A virtuális gépek védelme olyan figyelési képességre van szükség, amely gyorsan észleli a fenyegetéseket, megakadályozza az erőforrásokhoz való jogosulatlan hozzáférést, riasztásokat vált ki, és csökkenti a hamis riasztásokat.

A [Windows](../../security-center/security-center-virtual-machine.md) és [linuxos virtuális gépek](../../security-center/security-center-linux-virtual-machine.md)biztonsági állapotának figyeléséhez használja az Azure [Security Center](../../security-center/security-center-intro.md)t. A Security Centerben az alábbi lehetőségek kihasználásával védheti meg a virtuális gépeket:

- Operációs rendszer biztonsági beállításainak alkalmazása ajánlott konfigurációs szabályokkal.
- Azonosítsa és töltse le a rendszer biztonságát és a hiányzó kritikus frissítéseket.
- Javaslatok at endpoint antimalware védelem.
- Lemeztitkosítás ellenőrzése.
- A biztonsági rések felmérése és kiújítása.
- Észlelni a fenyegetéseket.

A Security Center aktívan figyelheti a fenyegetéseket, és a biztonsági riasztások ban potenciális fenyegetések vannak kitéve. A korrelált fenyegetések összesítése egyetlen nézetben, úgynevezett biztonsági incidenssel van összesítve.

A Security Center az [Azure Monitor naplóiban](/azure/log-analytics/log-analytics-overview)tárolja az adatokat. Az Azure Monitor naplói egy lekérdezési nyelvet és elemzési motort biztosít, amely betekintést nyújt az alkalmazások és erőforrások működésébe. Az adatok at is gyűjti az [Azure Monitor,](../../batch/monitoring-overview.md)felügyeleti megoldások, és a felhőben vagy a helyszínen telepített ügynökök. A közös funkcióknak köszönhetően átfogó képet alkothat a környezetről.

Azok a szervezetek, amelyek nem kényszerítik ki a virtuális gépeik erős biztonságát, továbbra sem ismerik a jogosulatlan felhasználók által a biztonsági vezérlők megkerülésére tett esetleges kísérleteket.

## <a name="monitor-vm-performance"></a>A virtuális gépek teljesítményének figyelése
Az erőforrásokkal való visszaélés akkor lehet probléma, ha a virtuális gép folyamatai több erőforrást használnak fel, mint amennyit kellene. A virtuális gép teljesítményével kapcsolatos problémák szolgáltatáskimaradáshoz vezethetnek, ami sérti a rendelkezésre állás biztonsági elvét. Ez különösen fontos az IIS-t vagy más webkiszolgálókat üzemeltető virtuális gépek esetében, mivel a magas processzor- vagy memóriahasználat szolgáltatásmegtagadási (DoS) támadásra utalhat. Elengedhetetlen, hogy a virtuális gép hozzáférésének figyelése nem csak reaktív, miközben egy probléma fordul elő, hanem proaktív módon ellen alapszintű teljesítmény normál működés során mért.

Azt javasoljuk, hogy az [Azure Monitor](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) használatával betekintést nyerhet az erőforrás állapotába. Az Azure Monitor szolgáltatásai:

- [Erőforrás-diagnosztikai naplófájlok:](../../azure-monitor/platform/platform-logs-overview.md)figyeli a virtuális gép erőforrásait, és azonosítja azokat a lehetséges problémákat, amelyek veszélyeztethetik a teljesítményt és a rendelkezésre állást.
- [Azure Diagnostics bővítmény:](/azure/azure-monitor/platform/diagnostics-extension-overview)Figyelési és diagnosztikai képességeket biztosít a Windows virtuális gépeken. Ezeket a képességeket úgy engedélyezheti, hogy a bővítményt az Azure Resource Manager sablon részeként [tartalmazza.](/azure/virtual-machines/windows/extensions-diagnostics-template)

A virtuális gépek teljesítményét nem figyelő szervezetek nem tudják meghatározni, hogy a teljesítményminták bizonyos változásai normálisak vagy rendellenesek-e. A virtuális gép, amely a szokásosnál több erőforrást fogyaszt, egy külső erőforrás vagy a virtuális gépben futó feltört folyamat támadását jelezheti.

## <a name="encrypt-your-virtual-hard-disk-files"></a>A virtuális merevlemez-fájlok titkosítása
Azt javasoljuk, hogy titkosítsa a virtuális merevlemezek (VHDs) védelme érdekében a rendszerindító kötet és a tárolóban lévő adatkötetek, valamint a titkosítási kulcsok és titkos kulcsok.

[Az Azure Disk Encryption](../azure-security-disk-encryption-overview.md) segítségével titkosíthatja a Windows és a Linux IaaS virtuálisgép-lemezeket. Az Azure Disk Encryption a Windows szabványos [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) szolgáltatását és a Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) szolgáltatását használja az operációs rendszer és az adatlemezek kötettitkosításának biztosításához. A megoldás integrálva van az [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) szolgáltatással, amely ekkel vezérelheti és kezelheti a kulcstároló-előfizetésben lévő lemeztitkosítási kulcsokat és titkos kulcsokat. A megoldás azt is biztosítja, hogy a virtuális gép lemezein lévő összes adat inkultatra titkosítva legyen az Azure Storage-ban.

Az alábbiakban az Azure Disk Encryption használatával kapcsolatos gyakorlati tanácsokat tanaszatként használja:

**Ajánlott eljárás:** Titkosítás engedélyezése a virtuális gépeken.   
**Részlet:** Az Azure Disk Encryption létrehozza és írja a titkosítási kulcsokat a kulcstartóba. A titkosítási kulcsok kezelése a kulcstartóban Azure AD-hitelesítést igényel. Ehhez a célból hozzon létre egy Azure AD-alkalmazást. Hitelesítési célokra használhatja az ügyfél titkos hitelesítését vagy [az ügyféltanúsítvány-alapú Azure AD-hitelesítést.](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)

**Ajánlott eljárás:** Kulcstitkosítási kulcs (KEK) használata a titkosítási kulcsok további biztonsági rétegéhez. KEK hozzáadása a kulcstartóhoz.   
**Részlet:** Az [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) parancsmag segítségével hozzon létre egy kulcstitkosítási kulcsot a kulcstartóban. KEK-et is importálhat a helyszíni hardveres biztonsági modulból (HSM) a kulcskezeléshez. További információt a [Key Vault dokumentációjában](../../key-vault/key-vault-hsm-protected-keys.md)talál. Kulcstitkosítási kulcs megadása esetén az Azure Disk Encryption ezt a kulcsot használja a titkosítási titkos kulcsok beburkolásához, mielőtt a Key Vaultba írna. A kulcs letéti példányának egy helyszíni kulcskezelésben való tartása további védelmet nyújt a kulcsok véletlen törlése ellen.

**Ajánlott eljárás:** A lemezek titkosítása előtt [készítsen pillanatképet](../../virtual-machines/windows/snapshot-copy-managed-disk.md) és/vagy biztonsági mentést. A biztonsági mentések helyreállítási lehetőséget biztosítanak, ha a titkosítás során váratlan hiba történik.   
**Részlet:** A felügyelt lemezekkel rendelkező virtuális gépeknek biztonsági mentésre van szükségük a titkosítás előtt. A biztonsági mentés után a **Set-AzVMDiskEncryptionExtension** parancsmag segítségével titkosíthatja a felügyelt lemezeket a *-skipVmBackup* paraméter megadásával. A titkosított virtuális gépek biztonsági mentéséről és visszaállításáról az Azure Backup cikkében olvashat [bővebben.](../../backup/backup-azure-vms-encryption.md)

**Ajánlott eljárás:** Annak érdekében, hogy a titkosítási titkok ne lépjék át a regionális határokat, az Azure Disk Encryption a kulcstartónak és a virtuális gépeknek ugyanabban a régióban kell lennie.   
**Részletes:** Hozzon létre és használjon egy key vault, amely ugyanabban a régióban, mint a virtuális gép titkosítva.

Az Azure disk encryption alkalmazásakor a következő üzleti igényeket elégítheti ki:

- Az IaaS virtuális gépek az iparági szabványnak megfelelő titkosítási technológiával vannak biztosítva a szervezeti biztonsági és megfelelőségi követelmények nek megfelelően.
- Az IaaS-virtuális gépek ügyfél által vezérelt kulcsok és szabályzatok alatt kezdődnek, és naplózhatja azok használatát a kulcstartóban.

## <a name="restrict-direct-internet-connectivity"></a>Közvetlen internetkapcsolat korlátozása
A virtuális gép közvetlen internetkapcsolatának figyelése és korlátozása. A támadók folyamatosan kutatják át a nyilvános felhő IP-tartományait a nyitott felügyeleti portok után, és "egyszerű" támadásokat kísérelnek meg, például gyakori jelszavakat és ismert, nem javított biztonsági réseket. Az alábbi táblázat az ilyen támadások elleni védekezéshez ajánlott eljárásokat sorolja fel:

**Ajánlott eljárás:** Megakadályozza a véletlen hálózati útválasztásnak és biztonságnak való kitettséget.   
**Részlet:** Az RBAC használatával győződjön meg arról, hogy csak a központi hálózati csoport rendelkezik engedéllyel a hálózati erőforrásokhoz.

**Ajánlott eljárás:** Azonosítsa és orvosolja a "bármely" forrás IP-címről hozzáférést lehetővé tenni lehetővé lehetővé tenni.   
**Részlet**: Használja az Azure Security Centert. A Security Center azt javasolja, hogy korlátozza a hozzáférést az internetfelé irányuló végpontokon keresztül, ha a hálózati biztonsági csoportok bármelyikének van egy vagy több bejövő szabálya, amely lehetővé teszi a hozzáférést "bármely" forrás IP-címéről. A Security Center azt javasolja, hogy a bejövő szabályok szerkesztésével korlátozza a [hozzáférést](../../security-center/security-center-network-recommendations.md) a forrás IP-címekhez, amelyeknek ténylegesen hozzáférésre van szükségük.

**Ajánlott eljárás:** Felügyeleti portok korlátozása (RDP, SSH).   
**Részletes:** [Just-in-time (JIT) virtuális gép-hozzáférés](../../security-center/security-center-just-in-time.md) segítségével zárolhatja a bejövő forgalmat az Azure virtuális gépek, csökkentve a támadásoknak való kitettséget, miközben könnyen elérhető a virtuális gépekhez való csatlakozáshoz, ha szükséges. Ha a jit engedélyezve van, a Security Center zárolja az Azure-beli virtuális gépek bejövő forgalmat egy hálózati biztonsági csoport szabály létrehozásával. Válassza ki a portokat a virtuális gép, amelyhez a bejövő forgalom lesz zárolva. Ezeket a portokat a JIT megoldás vezérli.

## <a name="next-steps"></a>További lépések
Tekintse meg [az Azure biztonsági gyakorlati tanácsait és mintáit,](best-practices-and-patterns.md) amelyek további biztonsági gyakorlati tanácsokat tartalmaznak a felhőbeli megoldások tervezése, üzembe helyezése és kezelése során az Azure használatával.

Az alábbi források az Azure biztonságával és a kapcsolódó Microsoft-szolgáltatásokkal kapcsolatos általánosabb információk biztosításához érhetők el:
* [Azure Security Team Blog](https://blogs.msdn.microsoft.com/azuresecurity/) – naprakész információk az Azure Security legújabb adatairól
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – ahol a Microsoft biztonsági rései, beleértve az Azure-ral kapcsolatos problémákat is, jelenthetők, vagy e-mailben asecure@microsoft.com
