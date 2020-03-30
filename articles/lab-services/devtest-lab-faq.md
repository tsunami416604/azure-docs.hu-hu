---
title: Azure DevTest Labs – gyakori kérdések | Microsoft dokumentumok
description: Ez a cikk választ ad az Azure DevTest Labs-ről szóló gyakori kérdésekre.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: de99e9b1e4adceaf08beaf8ad3b5ea114b31a586
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270783"
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs GYIK
Válaszok az Azure DevTest Labs-sel kapcsolatos leggyakoribb kérdésekre.

## <a name="blog-post"></a>Blogbejegyzést
A DevTest Labs Team blog2019. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Mostantól hol követhetem nyomon a funkciófrissítéseket?
Mostantól funkciófrissítéseket és informatív blogbejegyzéseket teszünk közzé az Azure-blogban és az Azure-frissítésekben. Ezek a blogbejegyzések is linket a dokumentációt, ahol szükséges.

Iratkozzon fel a [DevTest Labs Azure Blogjára](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) és a [DevTest Labs Azure-frissítéseire,](https://azure.microsoft.com/updates/?product=devtest-lab) hogy értesüljön a DevTest Labs új funkcióiról.

### <a name="what-happens-to-the-existing-blog-posts"></a>Mi történik a meglévő blogbejegyzésekkel?
Jelenleg dolgozunk a meglévő blogbejegyzések áttelepítésén (kivéve a kimaradásfrissítéseket) a [DevTest Labs dokumentációba.](devtest-lab-overview.md) Ha az MSDN-blog elavult, a rendszer átirányítja a DevTest Labs dokumentációs áttekintésére. Az átirányítás után a keresett cikket a "Szűrés szerint" címben keresheti meg. Még nem vándoroltak át az összes álláshely még, de meg kell tenni a hónap végéig. 


### <a name="where-do-i-see-outage-updates"></a>Hol láthatom a kimaradási frissítéseket?
Mi lesz kiküldetés kimaradás frissítések segítségével a Twitter kezelni mostantól. Kövess minket a Twitteren, hogy megkapja a legújabb frissítéseket a leállásokról és az ismert hibákról.

### <a name="twitter"></a>Twitter
A Twitter fogantyú:[@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Általános kérdések
### <a name="what-if-my-question-isnt-answered-here"></a>Mi van, ha a kérdésemre nem kap választ?
Ha kérdése nem szerepel itt, tudassa velünk, hogy segíthessünk megtalálni a választ.

- Tegye fel kérdését a GYIK végén.
- A szélesebb közönség eléréséhez tegyen fel egy kérdést az [Azure DevTest Labs MSDN fórumán.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs) Lépjen kapcsolatba az Azure DevTest Labs csapatával és a közösség többi tagjával.
- A szolgáltatáskérések esetén küldje el kéréseit és ötleteit az [Azure DevTest Labs User Voice szolgáltatásba.](https://feedback.azure.com/forums/320373-azure-devtest-labs)

### <a name="what-is-a-microsoft-account"></a>Mi az a Microsoft-fiók?
A Microsoft-fiók olyan fiók, amelyet szinte mindenhez használhat, amit a Microsoft-eszközökkel és -szolgáltatásokkal végez. Ez egy e-mail cím és jelszó, amellyel bejelentkezik a Skype-ba, Outlook.com, a OneDrive-ra, a Windows phone-ra, az Azure-ra és az Xbox Live-ra. Egyetlen fiók azt jelenti, hogy a fájlok, fényképek, névjegyek és beállítások bármilyen eszközön követhetik Önt.

> [!NOTE]
> Egy Microsoft-fiók, amelyet korábban Windows Live ID azonosítónak hívtak.

### <a name="why-should-i-use-azure-devtest-labs"></a>Miért érdemes használni az Azure DevTest Labs?
Az Azure DevTest Labs időt és pénzt takaríthat meg a csapatnak. A fejlesztők több különböző alap használatával hozhatják létre saját környezetüket. Emellett az összetevők segítségével gyorsan telepíthetik és konfigurálhatják az alkalmazásokat. Egyéni lemezképek és képletek használatával a virtuális gépeket sablonként mentheti, és egyszerűen reprodukálhatja őket a csapaton keresztül. A DevTest Labs számos konfigurálható szabályzatot is kínál, amelyeksegítségével a laboradminisztrátorok csökkenthetik a hulladék mennyiségét és kezelhetik a csapat környezeteit. Ezek a házirendek közé tartozik az automatikus leállítás, a költségküszöbértéket, a felhasználónkénti maximális virtuális gépeket és a maximális virtuális gép mérete. A DevTest Labs részletesebb magyarázatát az [áttekintésben](devtest-lab-overview.md) vagy a [bevezető videóban találja.](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs)

### <a name="what-does-worry-free-self-service-mean"></a>Mit jelent az, hogy "gondtalan önkiszolgáló"?
A gondtalan önkiszolgáló azt jelenti, hogy a fejlesztők és a tesztelők szükség szerint saját környezetet hoznak létre. A rendszergazdák biztonsággal rendelkeznek annak tudatában, hogy a DevTest Labs segíthet a megfelelő hozzáférés beállításában, a hulladék-és a költségek csökkentésében. A rendszergazdák megadhatják, hogy mely virtuális gépméretek engedélyezettek, a virtuális gépek maximális száma, és mikor indul el és állítsa le a virtuális gépek. A DevTest Labs emellett megkönnyíti a költségek figyelését és a riasztások beállítását, hogy tisztában legyen a laborerőforrások felhasználásának módjával.

### <a name="how-can-i-use-devtest-labs"></a>Hogyan használhatom a DevTest Labs-t?
A DevTest Labs bármikor hasznos, ha fejlesztői vagy tesztelési környezeteket igényel, és gyorsan szeretné reprodukálni őket, vagy költségtakarékos házirendekkel kezelni őket.
Íme néhány forgatókönyv, amelyhez ügyfeleink a DevTest Labs-t használják:

- A fejlesztési és tesztelési környezeteket egy helyen kezelheti. A szabályzatok segítségével csökkentheti a költségeket, és egyéni lemezképeket hozhat létre a buildek csapaton keresztüli megosztásához.
- Egyéni lemezképek használatával fejleszthet egy alkalmazást a lemezállapot mentéséhez a fejlesztési szakaszokban.
- A költségek nyomon követése a haladáshoz viszonyítva.
- Hozzon létre tömegteszt-környezeteket a minőségbiztosítási teszteléshez.
- Az összetevők és képletek segítségével egyszerűen konfigurálhatja és reprodukálhatja az alkalmazásokat különböző környezetekben.
- Virtuális gépek terjesztése hackathons (együttműködésen alapuló fejlesztési vagy tesztelési munka), majd könnyen megszüntetheti őket, amikor az esemény véget ér.

### <a name="how-am-i-billed-for-devtest-labs"></a>Hogyan számláznak nekem a DevTest Labs?
DevTest Labs egy ingyenes szolgáltatás. Laborok létrehozása és a szabályzatok, sablonok és összetevők konfigurálása a DevTest Labs ingyenes. Csak a laborokban használt Azure-erőforrásokért kell fizetnie, például a virtuális gépekért, a tárfiókokért és a virtuális hálózatokért. A laborerőforrások költségeiről az [Azure DevTest Labs díjszabása](https://azure.microsoft.com/pricing/details/devtest-lab/)című témakörben talál további információt.

## <a name="security"></a>Biztonság

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Milyen biztonsági szintek vannak a DevTest Labs-ben?
A biztonsági hozzáférést a szerepköralapú hozzáférés-vezérlés (RBAC) határozza meg. A hozzáférés működésének megismeréséhez segít megtanulni az rbac által meghatározott engedélyek, szerepkör és hatókör közötti különbségeket.

- **Engedély**: Az engedély egy adott művelethez való meghatározott hozzáférés. Például egy engedély lehet olvasni való hozzáférést az összes virtuális géphez.
- **Szerep**: A szerepkör olyan engedélyek csoportja, amelyek csoportosíthatók és hozzárendelhetők egy felhasználóhoz. Például egy előfizetés-tulajdonosi szerepkörrel rendelkező felhasználó hozzáfér az előfizetésen belüli összes erőforráshoz.
- **Hatókör:** A hatókör egy Azure-erőforrás hierarchiáján belüli szint. Egy hatókör lehet például egy erőforráscsoport, egy tesztkörnyezet vagy a teljes előfizetés.

A DevTest Labs hatókörén belül kétféle szerepkör határozza meg a felhasználói engedélyeket:

- **Labor tulajdonosa:** A labor tulajdonosa hozzáfér a labor összes erőforrásához. A tesztkörnyezet tulajdonosa módosíthatja a szabályzatokat, olvashat és írhat bármely virtuális gépnek, módosíthatja a virtuális hálózatot, és így tovább.
- **Labor felhasználó:** A tesztkörnyezet-felhasználó megtekintheti az összes tesztkörnyezet-erőforrások, például a virtuális gépek, szabályzatok és a virtuális hálózatok. Azonban a tesztkörnyezet-felhasználó nem módosíthatja a szabályzatokat, vagy a más felhasználók által létrehozott virtuális gépek.

Egyéni szerepköröket is létrehozhat a DevTest Labs-ben. Ha tudni szeretné, hogyan hozhat létre egyéni szerepköröket a DevTest Labs alkalmazásban, olvassa el [a Felhasználói engedélyek megadása adott laborházirendeknek című témakört.](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)

Mivel a hatókörök hierarchikusak, ha egy felhasználó rendelkezik engedélyekkel egy adott hatókörben, a felhasználó automatikusan megkapja ezeket az engedélyeket a hatókör minden alacsonyabb szintű hatókörében. Ha például egy felhasználó az előfizetés tulajdonosának szerepkörrel van elrendelve, a felhasználó hozzáférhet az előfizetés összes erőforrásához. Ezek az erőforrások közé tartoznak a virtuális gépek, a virtuális hálózatok és a laborok. Az előfizetés tulajdonosa automatikusan örökli a labor tulajdonosszerepkörét. Ennek az ellenkezője azonban nem igaz. A tesztkörnyezet tulajdonosa rendelkezik hozzáféréssel egy tesztkörnyezethez, amely alacsonyabb hatókörrel rendelkezik, mint az előfizetési szint. Így a tesztkörnyezet tulajdonosa nem láthatja a virtuális gépeket, a virtuális hálózatokat vagy a tesztkörnyezeten kívüli egyéb erőforrásokat.

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Hogyan definiálhatom a szerepköralapú hozzáférés-vezérlést a DevTest Labs környezetekhez annak érdekében, hogy az informatikai szolgáltatások szabályozhassák, miközben a fejlesztők/tesztek el tudják végezni a munkájukat?
Széles minta létezik, azonban a részletek a szervezettől függnek.

A központi informatikai szolgáltatásoknak csak azt kell birtokolniuk, ami szükséges, és lehetővé kell tenniük, hogy a projekt- és alkalmazáscsapatok a szükséges szintű ellenőrzéssel rendelkezhessenek. Ez általában azt jelenti, hogy a központi informatikai a központi informatikai tulajdonosa az előfizetés, és kezeli az alapvető informatikai funkciók, például a hálózati konfigurációk. Az előfizetés **tulajdonosainak** kicsinek kell lennie. Ezek a tulajdonosok szükség esetén további tulajdonosokat jelölhetnek ki, vagy előfizetési szintű szabályzatokat alkalmazhatnak, például "Nincs nyilvános IP".

Előfordulhat, hogy a felhasználók egy részhalmaza, amely hozzáférést igényel egy előfizetés, például a Tier1 vagy tier 2 támogatás. Ebben az esetben azt javasoljuk, hogy adjon ezeknek a felhasználóknak a **közreműködői** hozzáférést, hogy azok kezelni tudják az erőforrásokat, de ne biztosítsanak felhasználói hozzáférést vagy módosítsák a házirendeket.

A DevTest Labs erőforrás tulajdonosa a tulajdonosok, akik közel vannak a projekt/alkalmazás csapat. Ez azért van, mert megértik a gépekre vonatkozó követelményeiket és a szükséges szoftvereket. A legtöbb szervezetben a DevTest Labs erőforrás tulajdonosa általában a projekt/fejlesztési vezető. Ez a tulajdonos kezelheti a felhasználókat és a szabályzatokat a tesztkörnyezetben, és kezelheti a DevTest Labs környezetben lévő összes virtuális gépet.

A projekt/alkalmazás csapat tagjai hozzá kell adni a **DevTest Labs-felhasználók** szerepkör. Ezek a felhasználók virtuális gépeket hozhatnak létre (a labor- és előfizetési szintű szabályzatokkal összhangban). Saját virtuális gépeiket is kezelhetik. Nem kezelhetik a más felhasználókhoz tartozó virtuális gépeket.

További információ: [Azure Enterprise scaffold – előíró előfizetés-cégirányítási dokumentáció.](/azure/architecture/cloud-adoption/appendix/azure-scaffold)


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Hogyan hozhatok létre olyan szerepkört, amely lehetővé teszi a felhasználók számára egy adott feladat elvégzését?
Az egyéni szerepkörök létrehozásáról és a szerepkörhöz rendelt engedélyekről a [Felhasználói engedélyek megadása adott laborházirendekhez](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)című témakörben olvashat. Íme egy példa egy parancsfájlra, amely létrehozza a **DevTest Labs Advanced User**szerepkört, amely engedéllyel rendelkezik a laborban lévő összes virtuális gép indításához és leállításához:


```powershell
$policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
$policyRoleDef.Id = $null
$policyRoleDef.Name = "DevTest Labs Advanced User"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
$policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  
```

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Hogyan biztosíthatja egy szervezet a vállalati biztonsági házirendek érvényben lévő működését?
Egy szervezet a következő műveletekkel érheti el:

- Átfogó biztonsági politika kidolgozása és közzététele. A szabályzat a szoftverhasználatával, felhőeszközökkel kapcsolatos elfogadható használatra vonatkozó szabályokat fogalmazza meg. Azt is meghatározza, hogy mi sérti egyértelműen a politikát.
- Egyéni lemezképet, egyéni összetevőket és olyan telepítési folyamatot fejleszthet ki, amely lehetővé teszi az active directoryval definiált biztonsági területen belüli vezénylést. Ez a megközelítés kényszeríti a vállalati határt, és meghatározza a közös környezetvédelmi vezérlők. Ezek a környezet elleni vezérlők a fejlesztő figyelembe veheti, ahogy azok fejlesztése és kövesse a biztonságos fejlesztési életciklus részeként a teljes folyamat. A cél az is, hogy olyan környezetet biztosítsunk, amely nem túlságosan korlátozó, ami akadályozhatja a fejlődést, hanem ésszerű ellenőrzési sorozat. A csoportházirendek a szervezeti egység (OU) tartalmazó labor virtuális gépek lehet egy részhalmaza a teljes csoport házirendek, amelyek az éles környezetben található. Alternatív megoldásként további készleteket is jelenthetnek az azonosított kockázatok megfelelő csökkentése érdekében.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>Hogyan biztosíthatja egy szervezet az adatok integritását annak biztosítása érdekében, hogy a kapcsolathasználattal foglalkozó fejlesztők ne távolíthassák el a kódot, ne vezethessenek be rosszindulatú programokat vagy nem jóváhagyott szoftvereket?
Több rétegű vezérlés, hogy csökkentsék a külső tanácsadók, vállalkozók vagy alkalmazottak, amelyek a terés közben a DevTest Labs együttműködik.

Amint azt korábban említettük, az első lépésnek elfogadható használatra vonatkozó szabályzatot kell kidolgoznia és meghatároznia, amely egyértelműen felvázolja a következményeket, ha valaki megsérti a szabályzatot.

A távelérés vezérlésének első rétege a távelérési házirend alkalmazása olyan VPN-kapcsolaton keresztül, amely nem közvetlenül kapcsolódik a tesztkörnyezethez.

A vezérlők második rétege olyan csoportházirend-objektumok alkalmazásával, amelyek megakadályozzák a távoli asztalon történő másolást és beillesztést. A hálózati házirend et úgy lehet végrehajtani, hogy a környezetből, például az FTP- és RDP-szolgáltatásokból származó kimenő szolgáltatások ne legyenek kikötve a környezetből. A felhasználó által definiált útválasztás kényszerítheti az összes Azure-hálózati forgalmat vissza a helyszíni, de az útválasztás nem tudta figyelembe venni az összes URL-eket, amelyek lehetővé teszik az adatok feltöltését, kivéve, ha egy proxyn keresztül ellenőrzik a tartalom és a munkamenetek. Nyilvános IP-k korlátozható a DevTest Labs-t támogató virtuális hálózaton belül, hogy ne engedélyezze a külső hálózati erőforrások áthidalását.

Végső soron ugyanazt a korlátozástípust kell alkalmazni az egész szervezetben, amely figyelembe veszi a cserélhető adathordozók vagy külső URL-ek minden lehetséges módszerét, amely képes elfogadni egy tartalombejegyzést. A biztonsági házirend áttekintéséhez és végrehajtásához forduljon biztonsági szakemberéhez. További javaslatokat a Microsoft Cyber Security ( microsoft kiberbiztonság ) [témakörben t.](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs)

## <a name="lab-configuration"></a>Labor konfigurációja

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Hogyan hozhatok létre tesztkörnyezetet egy Erőforrás-kezelő sablonból?
A [GitHub-tárházat a laborAzure Resource Manager-sablonok,](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) amelyek üzembe helyezheti, mint-van, vagy módosíthatja, hogy egyéni sablonokat hozzon létre a laborok. Minden sablon rendelkezik egy hivatkozást a labor üzembe helyezéséhez, ahogy az a saját Azure-előfizetésében van. Vagy testreszabhatja a sablont, és [üzembe helyezheti a PowerShell vagy az Azure CLI használatával.](../azure-resource-manager/templates/deploy-powershell.md)


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Lehet az összes virtuális gépet létre kell hozni egy közös erőforráscsoport helyett, amelyek minden gép a saját erőforráscsoportban?
Igen, a labor tulajdonosaként vagy hagyja, hogy a labor kezelje az erőforráscsoport-foglalást, vagy az összes virtuális gépet egy közös erőforráscsoportban, amely et megadott.

Külön erőforráscsoport-forgatókönyv:
-   A DevTest Labs új erőforráscsoportot hoz létre minden nyilvános/privát IP virtuális géphez, amelyet felpörget
-   DevTest Labs létrehoz egy erőforráscsoportot az azonos méretű megosztott IP-gépekhez.

Gyakori erőforráscsoport-forgatókönyv:
-   Az összes virtuális gép a megadott közös erőforráscsoportban van megpördülve. További információ [a tesztkörnyezet erőforráscsoport-hozzárendelése.](https://aka.ms/RGControl)

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Hogyan tarthatok fenn elnevezési konvenciót a DevTest Labs környezetben?
Érdemes lehet kiterjeszteni a jelenlegi vállalati elnevezési konvenciók az Azure-műveletek, és azok konzisztensek a DevTest Labs környezetben. DevTest Labs telepítésekor azt javasoljuk, hogy speciális indítási szabályzatok. Ezeket a házirendeket egy központi parancsfájl és JSON-sablonok a konzisztencia kényszerítése érdekében. Az elnevezési szabályzatok az előfizetés szintjén alkalmazott Azure-szabályzatokon keresztül valósíthatók meg. JSON-minták az Azure-szabályzat, tekintse meg az [Azure-szabályzat mintákat.](../governance/policy/samples/index.md)

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Hány labort hozhatok létre ugyanazon előfizetés keretében?
Nincs egy adott korlátot a laborok, amelyek előfizetésenként hozhatók létre. Az előfizetésenként felhasznált erőforrások mennyisége azonban korlátozott. Az [Azure-előfizetések korlátairól és kvótáiról,](../azure-resource-manager/management/azure-subscription-service-limits.md) valamint [arról, hogyan növelheti ezeket a korlátokat.](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)


### <a name="how-many-vms-can-i-create-per-lab"></a>Hány virtuális gépet hozhatok létre laboronként?
Nincs konkrét korlát a tesztkörnyezetben létrehozható virtuális gépek száma. Azonban a felhasznált erőforrások (virtuális gép magok, nyilvános IP-címek és így tovább) előfizetésenként korlátozott. Az [Azure-előfizetések korlátairól és kvótáiról,](../azure-resource-manager/management/azure-subscription-service-limits.md) valamint [arról, hogyan növelheti ezeket a korlátokat.](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Hogyan határozhatom meg a felhasználók egy tesztkörnyezetben, és a teljes számú laborok, amelyek szükségesek a szervezeten belül?
Azt javasoljuk, hogy az azonos fejlesztési projekthez társított részlegek és fejlesztői csoportok ugyanahhoz a laborhoz vannak társítva. Lehetővé teszi, hogy azonos típusú házirendek, képek és leállítási házirendek mindkét csoportra alkalmazható legyen.

Előfordulhat, hogy figyelembe kell vennie a földrajzi határokat is. Az Egyesült Államok (USA) északkeleti részén(USA) például az USA keleti részén2 kiépített labort használhatnak. A coloradói Dallasban és Denverben lévő fejlesztők et pedig arra utasíthatják, hogy használjanak egy erőforrást az USA déli részén. Ha egy külső harmadik féllel együttműködve, akkor lehet hozzárendelni egy labor, amely nem használja a belső fejlesztők.

Egy tesztkörnyezetet is használhat egy adott projekthez az Azure DevOps-projekteken belül. Ezután egy adott Azure Active Directory-csoporton keresztül alkalmazza a biztonságot, amely lehetővé teszi a hozzáférést mindkét erőforráskészlethez. A tesztkörnyezethez rendelt virtuális hálózat egy másik határ lehet a felhasználók konszolidálására.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Hogyan előzhetjük meg az erőforrások törlését egy laborban?
Azt javasoljuk, hogy állítsa be a megfelelő engedélyeket a labor szintjén, hogy csak a jogosult felhasználók törölhetik az erőforrásokat, vagy módosíthatja a labor szabályzatok. A fejlesztőket a **DevTest Labs-felhasználók** csoportba kell helyezni. A vezető fejlesztő nek vagy az infrastruktúra-vezetőnek a **DevTest Labs tulajdonosának**kell lennie. Azt javasoljuk, hogy csak két labor tulajdonosok. Ez a házirend a kódtár felé terjed a korrupció elkerülése érdekében. A laborfelhasználók jogosultak az erőforrások használatára, de nem frissíthetik a laborházirendeket. Tekintse meg az alábbi cikket, amely felsorolja az egyes beépített csoportok szerepköröket és jogokat egy tesztkörnyezetben: [Tulajdonosok és felhasználók hozzáadása az Azure DevTest Labs-ben.](devtest-lab-add-devtest-user.md)

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Hogyan oszthatok meg közvetlen kapcsolatot a laborommal?

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a laborban.
2. Másolja a **tesztkörnyezet URL-címét** a böngészőből, majd ossza meg a tesztkörnyezet felhasználóival.

> [!NOTE]
> Ha egy tesztkörnyezet-felhasználó olyan külső felhasználó, aki Rendelkezik Microsoft-fiókkal, de nem tagja a szervezet Active Directory-példányának, a felhasználó hibaüzenetet láthat, amikor megpróbál hozzáférni a megosztott hivatkozáshoz. Ha egy külső felhasználó hibaüzenetet lát, kérje meg a felhasználót, hogy először válassza ki a nevét az Azure Portal jobb felső sarkában. Ezután a könyvtár szakaszában a menü, a felhasználó kiválaszthatja a könyvtárat, ahol a labor létezik.

## <a name="virtual-machines"></a>Virtuális gépek

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Miért nem látom a virtuális gépeket a Virtuális gépek oldalon, amelyet a DevTest Labs-ben látok?
Amikor virtuális gép devtest labs, engedélyt kap a virtuális gép eléréséhez. A virtuális gép megtekintheti a laborok és a **virtuális gépek** lapon is megtekintheti. A **DevTest Labs tulajdonosi** szerepkörhöz rendelt felhasználók láthatják a laborban létrehozott összes virtuális gépet a **tesztkörnyezet minden virtuális gép** lapján. Azonban a felhasználók, akik a **DevTest Labs felhasználói** szerepkör nem kapolvasási hozzáférést a virtuális gép erőforrások, amelyek más felhasználók által létrehozott. Így ezek a virtuális gépek nem jelennek meg a **virtuális gépek** oldalon.


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Hogyan hozhatok létre egyszerre több virtuális gépet ugyanabból a sablonból?
Két lehetősége van arra, hogy egyszerre több virtuális gépet hozzon létre ugyanabból a sablonból:

- Használhatja az [Azure DevOps-feladatok bővítményt.](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)
- [Erőforrás-kezelő sablont hozhat létre](devtest-lab-add-vm.md#save-azure-resource-manager-template) virtuális gép létrehozása közben, és [telepítheti az Erőforrás-kezelő sablont a Windows PowerShellből.](../azure-resource-manager/templates/deploy-powershell.md)
- A virtuális gépek létrehozása során létrehozandó gépnek több példányát is megadhatja. Ha többet szeretne megtudni a virtuális gépek több példányának létrehozásáról, olvassa el a labor [virtuális gépek létrehozásáról](devtest-lab-add-vm.md)szóló dokumentum.

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Hogyan helyezhetem át a meglévő Azure-beli virtuális gépeimet a DevTest Labs laborjába?
A meglévő virtuális gépek másolása a DevTest Labs alkalmazásba:

1.  Másolja a meglévő virtuális gép virtuális merevlemez-fájlját [egy Windows PowerShell-parancsfájl](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1)használatával.
2.  Hozza létre az [egyéni képet](devtest-lab-create-template.md) a DevTest Labs laborban.
3.  Hozzon létre egy virtuális gép a laborban az egyéni lemezképből.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Több lemezt is csatlakoztathatok a virtuális gépekhez?

Igen, több lemezt is csatlakoztathat a virtuális gépekhez.

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Ha Windows operációs rendszerrendszer-lemezképet szeretnék használni a teszteléshez, MSDN-előfizetést kell vásárolnom?
Ha a Windows ügyféloperációs rendszerű rendszerképeket (Windows 7 vagy újabb verzió) szeretné használni az Azure-beli fejlesztéshez vagy teszteléshez, tegye az alábbi lépések egyikét:

- [Vásároljon MSDN-előfizetést.](https://www.visualstudio.com/products/how-to-buy-vs)
- Ha nagyvállalati szerződéssel rendelkezik, hozzon létre egy Azure-előfizetést a [nagyvállalati fejlesztési/tesztelési ajánlattal.](https://azure.microsoft.com/offers/ms-azr-0148p)

Az egyes MSDN-ajánlatokhoz nyújtott Azure-kreditekről a [Visual Studio-előfizetőknek szóló Havi Azure-kreditben](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)talál további információt.


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Hogyan automatizálhatom az összes virtuális gép törlésének folyamatát a laboromban?
Labortulajdonosként törölheti a virtuális gépeket a laborból az Azure Portalon. A laborban lévő összes virtuális gépet is törölheti egy PowerShell-parancsfájl használatával. A következő példában a megjegyzés **módosításához megadott értékek** alatt módosítsa a paraméterértékeket. Az előfizetési azonosító, a labResourceGroup és a labName értékeket az Azure Portal on a lab pane.You can retrieve the subscriptionId, labResourceGroup, and labName values from the lab pane in the Azure Portal.

```powershell
# Delete all the VMs in a lab.

# Values to change:
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource group here>"
$labName = "<Enter lab name here>"

# Sign in to your Azure account.
Connect-AzAccount

# Select the Azure subscription that has the lab. This step is optional
# if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab that has the VMs that you want to delete.
$lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the VMs from that lab.
$labVMs = Get-AzResource | Where-Object {
          $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
          $_.Name -like "$($lab.Name)/*"}

# Delete the VMs.
foreach($labVM in $labVMs)
{
    Remove-AzResource -ResourceId $labVM.ResourceId -Force
}
```

## <a name="environments"></a>Környezetek

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Hogyan használhatom az Erőforrás-kezelő sablonjait a DevTest Labs környezetben?
Az Erőforrás-kezelő-sablonokat egy DevTest Labs-környezetben telepíti a DevTest Labs cikk [környezetek szolgáltatásában](devtest-lab-test-env.md) említett lépések használatával. Alapvetően ellenőrizze a Resource Manager-sablonokat egy Git-tárházba (azure-tárházba vagy GitHubba), és hozzáadhat egy [privát tárházat a sablonokhoz](devtest-lab-test-env.md) a laborba. Ez a forgatókönyv nem lehet hasznos, ha fejlesztői gépek üzemeltetéséhez devtest labs használatával, de hasznos lehet, ha egy átmeneti környezet, amely az éles környezetben reprezentatív.

Azt is érdemes megjegyezni, hogy a virtuális gépek száma laboronként vagy felhasználónként i beállítás csak korlátozza a gépek száma natívan létrehozott a laborban is, és nem olyan környezetben (Resource Manager sablonok).

## <a name="custom-images"></a>Egyéni rendszerképek

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Hogyan állíthatok be egy könnyen megismételhető folyamatot, hogy az egyéni szervezeti lemezképeimet devtest labs környezetbe hozzam?
Lásd ezt [a videót a Kép Gyár minta](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Ebben a forgatókönyvben egy speciális forgatókönyv, és a megadott parancsfájlok csak minta parancsfájlok. Ha bármilyen módosításra van szükség, a környezetben használt parancsfájlokat kell kezelnie és karbantartania.

A lemezképgyár létrehozásáról az [Egyéni lemezképgyár létrehozása az Azure DevTest Labs ben](image-factory-create.md)című témakörben talál részletes információt.

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Mi a különbség az egyéni és a képlet között?
Az egyéni lemezkép felügyelt lemezkép. A képlet olyan kép, amelyet további beállításokkal konfigurálhat, majd menthet és reprodukálhat. Az egyéni lemezképek előnyösek lehetnek, ha gyorsan szeretne több környezetet létrehozni ugyanazzal az alapvető, nem módosítható lemezképpel. A képlet lehet jobb, ha azt szeretné, hogy reprodukálja a konfiguráció a virtuális gép a legújabb bitek, egy virtuális hálózat vagy alhálózat részeként, vagy egy adott méretű virtuális gép. Részletesebb magyarázat: Egyéni képek [és képletek összehasonlítása a DevTest Labs alkalmazásban.](devtest-lab-comparing-vm-base-image-types.md)

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Mikor érdemes képletet és egyéni képet használni?
Ebben a forgatókönyvben a döntő tényező általában a költség és az újrafelhasználás. Ha van egy forgatókönyv, ahol sok felhasználó/labs igényel egy lemezképet, amely sok szoftver tetején az alap lemezkép, majd csökkentheti a költségeket egy egyéni lemezkép létrehozásával. Ez azt jelenti, hogy a kép egyszer jön létre. Csökkenti a virtuális gép beállítási idejét és a telepítés során futó virtuális gép miatt felmerülő költségeket.

A szoftvercsomag módosításainak gyakorisága azonban további tényező. Ha naponta futtatja a buildeket, és megköveteli, hogy a szoftver a felhasználók virtuális gépein legyen, fontolja meg egy képlet használatát egyéni lemezkép helyett.

Részletesebb magyarázat: Egyéni képek [és képletek összehasonlítása](devtest-lab-comparing-vm-base-image-types.md) a DevTest Labs alkalmazásban.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Hogyan automatizálhatom a VHD-fájlok feltöltésének folyamatát egyéni képek létrehozásához?

A VHD-fájlok egyéni képek létrehozásához történő feltöltésének automatizálásához két lehetősége van:

- Az [AzCopy](../storage/common/storage-use-azcopy-v10.md) segítségével másolja vagy töltse fel a VHD-fájlokat a tesztkörnyezethez társított tárfiókba.
- Használja [az Azure Storage Explorer t.](../vs-azure-tools-storage-manage-with-storage-explorer.md) A Storage Explorer egy önálló alkalmazás, amely Windows, OS X és Linux rendszeren fut.

A tesztkörnyezethez társított céltárfiók megkeresése:

1.  Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2.  A bal oldali menüben válassza az **Erőforráscsoportok**lehetőséget.
3.  Keresse meg és válassza ki a laborhoz társított erőforráscsoportot.
4.  Az **Áttekintés csoportban**válassza ki az egyik tárfiókot.
5.  Válassza **a Blobok lehetőséget.**
6.  Keresse meg a feltöltéseket a listában. Ha nincs ilyen, térjen vissza a 4.
7.  Használja az **URL-címet** az AzCopy parancsban.

Mikor érdemes azure piactéri lemezképet használni a saját egyéni szervezeti lemezképemmel szemben?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Mikor érdemes azure piactéri lemezképet használni a saját egyéni szervezeti lemezképemmel szemben?
Az Azure Marketplace-t kell használni alapértelmezés szerint, kivéve, ha konkrét aggályok vagy szervezeti követelmények. Néhány gyakori példa: néhány gyakori példa;

- Összetett szoftverbeállítás, amely megköveteli, hogy egy alkalmazás az alapkép része legyen.
- Egy alkalmazás telepítése és beállítása több órát is igénybe vehet, ami nem hatékony számítási idő hozzáadása az Azure Marketplace-lemezképhez.
- A fejlesztőknek és a tesztelőknek gyorsan hozzá kell férniük egy virtuális géphez, és szeretnék minimalizálni az új virtuális gépek beállítási idejét.
- Megfelelőségi vagy szabályozási feltételek (például biztonsági házirendek), amelyeknek minden géphez érvényben kell lenniük.
- Az egyéni képek használata nem tekinthető félvállról. Ők be extra összetettsége, ahogy most kell kezelni vHD fájlokat az alapul szolgáló alap képek. Azt is meg kell, hogy rendszeresen patch ezeket az alapképeket szoftverfrissítésekkel. Ezek a frissítések új operációsrendszer-frissítéseket tartalmaznak, valamint a szoftvercsomaghoz szükséges frissítéseket vagy konfigurációs módosításokat.

## <a name="artifacts"></a>Összetevők

### <a name="what-are-artifacts"></a>Mik azok a leletek?
Az összetevők testreszabható elemek, amelyek segítségével telepítheti a legújabb biteket, vagy üzembe helyezheti a fejlesztői eszközöket egy virtuális gépre. Összetevők csatolása a virtuális géphez a virtuális gép létrehozásakor. A virtuális gép kiépítése után a műtermékek üzembe helyezik és konfigurálják a virtuális gép. Különböző már meglévő összetevők érhetők el [a nyilvános GitHub-tárházban.](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) [Saját műtárgyakat is megírt.](devtest-lab-artifact-author.md)

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>A műtermék nem sikerült a virtuális gép létrehozása során. Hogyan háríthatók el a hibaelhárításai mezei hiba?
A sikertelen műtermék naplóinak beolvassa a [DevTest Labs összetevő-hibáinak diagnosztizálása.](devtest-lab-troubleshoot-artifact-failure.md)

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Mikor kell egy szervezetnek nyilvános må± személyes må± összetevő-tárházat használnia a DevTest Labsben?
A [nyilvános műtermék-tárház](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) a leggyakrabban használt szoftvercsomagok kezdeti készletét biztosítja. Segít a gyors üzembe helyezésben anélkül, hogy időt kellene befektetnie a közös fejlesztői eszközök és bővítmények reprodukálására. Választhat, hogy saját privát tárház üzembe helyezése. A nyilvános és a magántártár párhuzamosan használhatja. Dönthet úgy is, hogy letiltja a nyilvános tárházat. A magántárház üzembe helyezésének kritériumait a következő kérdéseknek és szempontoknak kell vezérelniük:

- A szervezet nek van-e követelménye, hogy a DevTest Labs-ajánlatrészeként vállalati licencelt szoftverrel kell rendelkeznie? Ha a válasz igen, akkor létre kell hozni egy privát tárházat.
- A szervezet olyan egyéni szoftvert fejleszt, amely egy adott műveletet biztosít, amely a teljes kiépítési folyamat részeként szükséges? Ha a válasz igen, akkor egy privát tárházat kell telepíteni.
- Ha a szervezet cégirányítási házirendje elkülönítést igényel, és a külső adattárak nincsenek a szervezet közvetlen konfigurációs felügyelete alatt, egy privát műtermék-tárházat kell telepíteni. Ennek a folyamatnak a részeként a nyilvános tárház első példánya másolható és integrálható a magántárházba. Ezután a nyilvános tárház letiltható, így a szervezeten belül senki sem férhet hozzá. Ez a megközelítés arra kényszeríti a szervezeten belüli összes felhasználót, hogy csak egy tárház, amely a szervezet által jóváhagyott, és minimalizálja a konfigurációs eltolódás.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Egy szervezet tervezze meg egyetlen tárházat, vagy engedélyezze a több tárházat?
A szervezet általános cégirányítási és konfigurációkezelési stratégiájának részeként azt javasoljuk, hogy használjon egy központi tárházat. Ha több adattárat használ, azok idővel a nem felügyelt szoftverek silóivá válhatnak. Egy központi tárház, több csapat is felhasználhatja a projektjeik összetevői. Kikényszeríti a szabványosítást, a biztonságot, a könnyű kezelhetőséget, és kiküszöböli az erőfeszítések megkettőzését. A központosítás részeként a következő intézkedések a hosszú távú irányítás és fenntarthatóság ajánlott gyakorlatai:

- Társítsa az Azure-tárház ugyanahhoz az Azure Active Directory-bérlőhöz, amelyet az Azure-előfizetés hitelesítéshez és engedélyezéshez használ.
- Hozzon létre `All DevTest Labs Developers` egy központilag felügyelt, az Azure Active Directoryban elnevezett csoportot. Minden olyan fejlesztőt, aki hozzájárul a műtermék fejlesztéséhez, ebbe a csoportba kell helyezni.
- Ugyanaz az Azure Active Directory-csoport használható az Azure Repos tárházhoz és a laborhoz való hozzáférés biztosításához.
- Az Azure Repos,elágazás vagy elágazás kell használni egy külön fejlesztés alatt álló tárház az elsődleges éles tárház. A tartalom csak a megfelelő kódellenőrzés után kerül a főágba lekéréses kérelemmel. Miután a kódfelülvizsgáló jóváhagyja a módosítást, a főág karbantartásáért felelős vezető fejlesztő egyesíti a frissített kódot.

## <a name="cicd-integration"></a>CI/CD integráció

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>A DevTest Labs integrálható a CI/CD eszközláncommal?
Ha Az Azure DevOps használata, a [DevTest Labs feladatok bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) segítségével automatizálhatja a kiadási folyamat a DevTest Labs. A bővítmény rel a következő feladatokat teheti:

- Virtuális gép automatikus létrehozása és üzembe helyezése. A virtuális gép a legújabb build az Azure File Copy vagy a PowerShell Azure DevOps Services feladatok használatával is konfigurálható.
- Automatikusan rögzítse a virtuális gép állapotát a tesztelés után, hogy reprodukálja a hibát ugyanazon a virtuális gépen további vizsgálat céljából.
- Törölje a virtuális gép végén a kiadási folyamat, ha már nincs rá szükség.

Az alábbi blogbejegyzések útmutatást és információkat nyújtanak az Azure DevOps Services bővítmény használatával kapcsolatban:

- [DevTest Labs és az Azure DevOps-bővítmény](integrate-environments-devops-pipeline.md)
- [Új virtuális gép üzembe helyezése egy meglévő DevTest Labs-laborban az Azure DevOps-szolgáltatásokból](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Az Azure DevOps Services kiadáskezeléshasználata a DevTest Labs folyamatos üzembe helyezéséhez](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Más folyamatos integrációs (CI)/folyamatos kézbesítési (CD) eszközláncok esetében ugyanazokat a forgatókönyveket érheti el az [Azure Resource Manager-sablonok](https://azure.microsoft.com/resources/templates/) Azure [PowerShell-parancsmagok](../azure-resource-manager/templates/deploy-powershell.md) és [.NET SDK-k](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/)használatával történő üzembe helyezésével. A [DEVTest Labs REST API-kat](https://aka.ms/dtlrestapis) is használhatja az eszközlánccal való integrációhoz.

## <a name="networking"></a>Hálózat

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>Mikor hozzak létre új virtuális hálózatot a DevTest Labs környezethez, szemben egy meglévő virtuális hálózat használatával?
Ha a virtuális gépeknek kell együttműködniük a meglévő infrastruktúrával, majd fontolja meg egy meglévő virtuális hálózat használatát a DevTest Labs környezetben. Ha ExpressRoute-ot használ, érdemes minimalizálni a virtuális hálózatok / alhálózatok mennyiségét, hogy ne töredje szét az ELŐFIZETÉSek ben való használatra kijelölt IP-címterületet.

Fontolja meg a virtuális hálózat társviszony-létesítési minta itt[(Hub-Küllőmodell)](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)is. Ez a megközelítés lehetővé teszi a virtuális hálózat/alhálózati kommunikációt az előfizetések között. Ellenkező esetben minden DevTest Labs-környezet saját virtuális hálózattal rendelkezhet.

Előfizetésenként a virtuális hálózatok száma [korlátozott.](../azure-resource-manager/management/azure-subscription-service-limits.md) Az alapértelmezett összeg 50, bár ez a korlát 100-ra emelhető.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Mikor érdemes megosztott IP-t és nyilvános IP-t használni a privát IP-címhez?

Ha helyek közötti VPN-t vagy Expressz-útvonalat használ, fontolja meg a privát IP-k használatát, hogy a gépek elérhetők legyenek a belső hálózaton keresztül, és ne legyenek elérhetők a nyilvános interneten keresztül.

> [!NOTE]
> A labortulajdonosok módosíthatják ezt az alhálózati szabályzatot annak érdekében, hogy véletlenül senki ne hozza létre a virtuális gépek nyilvános IP-címét. Az előfizetés tulajdonosának létre kell hoznia egy előfizetési szabályzatot, amely megakadályozza a nyilvános IP-k létrehozását.

Megosztott nyilvános IP-cím használata esetén a tesztkörnyezetben lévő virtuális gépek nyilvános IP-címet osztanak meg. Ez a megközelítés akkor lehet hasznos, ha el kell kerülnie a nyilvános IP-címek re vonatkozó korlátozások megszegését egy adott előfizetéshez.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Hogyan biztosítható, hogy a fejlesztő és a tesztelési virtuális gépek nem tudják elérni a nyilvános internetet? Vannak-e ajánlott minták a hálózati konfiguráció beállításához?

Igen. Két szempontot kell figyelembe venni : bejövő és kimenő forgalmat.

- **Bejövő forgalom** – Ha a virtuális gép nem rendelkezik nyilvános IP-címmel, akkor nem érhető el az interneten keresztül. A közös megközelítés annak biztosítása, hogy egy előfizetés-szintű szabályzat van beállítva, úgy, hogy egyetlen felhasználó sem hozhat létre nyilvános IP-címet.
- **Kimenő forgalom** – Ha meg szeretné akadályozni, hogy a virtuális gépek közvetlenül hozzáférjenek a nyilvános internethez, és a forgalmat vállalati tűzfalon keresztül kényszerítsék ki, akkor a helyszíni forgalmat expressz útvonalon vagy VPN-en keresztül irányíthatja, kényszerített útválasztás használatával.

> [!NOTE]
> Ha olyan proxykiszolgálóval rendelkezik, amely blokkolja a proxybeállítások nélküli forgalmat, ne felejtsen el kivételeket hozzáadni a tesztkörnyezet műtermék-tárfiókjához.

A hálózati biztonsági csoportokat virtuális gépekhez vagy alhálózatokhoz is használhatja. Ez a lépés egy további védelmi réteget ad hozzá a forgalom engedélyezéséhez / blokkolásához.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Miért nem megfelelő a meglévő virtuális hálózat mentése?
Az egyik lehetőség az, hogy a virtuális hálózat neve időszakokat tartalmaz. Ha igen, próbálja meg eltávolítani az időszakokat, vagy cserélje le őket kötőjelekkel. Ezután próbálja meg újra menteni a virtuális hálózatot.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Miért jelenik meg a "Szülő erőforrás nem található" hibaüzenet, amikor kiépítek egy virtuális gép a PowerShellből?
Ha az egyik erőforrás szülő egy másik erőforrás, a szülő erőforrásnak léteznie kell a gyermekerőforrás létrehozása előtt. Ha a szülő erőforrás nem létezik, megjelenik egy **ParentResourceNotFound** üzenet. Ha nem ad meg függőséget a szülő erőforrás, a gyermek erőforrás lehet telepíteni, mielőtt a szülő.

A virtuális gépek egy erőforráscsoport egy tesztkörnyezet ben lévő gyermekerőforrások. Ha az Erőforrás-kezelő sablonok segítségével telepíti a virtuális gépek et a PowerShell használatával, a PowerShell-parancsfájlban megadott erőforráscsoport neve a labor erőforráscsoport neve kell lennie. További információt az [Azure gyakori telepítési hibáinak elhárítása című témakörben](../azure-resource-manager/templates/common-deployment-errors.md)talál.

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Hol találhatok további hibainformációkat, ha a virtuális gép telepítése sikertelen?
A virtuális gép telepítési hibáit a tevékenységnaplók rögzítik. A labor virtuálisgép-tevékenységnaplókat a **naplók** vagy a **virtuálisgép-diagnosztika** csoportban találja az erőforrás menüjében a tesztkörnyezet virtuális gépi lapján (a lap a virtuális gép kiválasztása után jelenik meg a Virtuális gépek listából).

Néha a telepítési hiba a virtuális gép üzembe helyezése előtt fordul elő. Egy példa, ha a virtuális géptel létrehozott erőforrás előfizetési korlátját túllépi. Ebben az esetben a hiba részleteit a laborszintű tevékenységnaplók rögzítik. A tevékenységnaplók a **Konfigurációs és házirendek** beállításainak alján találhatók. A tevékenységnaplók Azure-beli használatáról az [Erőforrásokkal kapcsolatos műveletek naplózási naplóinak megtekintése című](../azure-resource-manager/management/view-activity-logs.md)témakörben talál további információt.

### <a name="why-do-i-get-location-is-not-available-for-resource-type-error-when-trying-to-create-a-lab"></a>Miért jelenik meg a "hely nem érhető el erőforrástípushoz" hibaüzenet jelenik meg egy tesztkörnyezet létrehozásakor?
Labor létrehozásakor az alábbihoz hasonló hibaüzenet jelenhet meg:

```
The provided location 'australiacentral' is not available for resource type 'Microsoft.KeyVault/vaults'. List of available regions for the resource type is 'northcentralus,eastus,northeurope,westeurope,eastasia,southeastasia,eastus2,centralus,southcentralus,westus,japaneast,japanwest,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia,canadacentral,canadaeast,uksouth,ukwest,westcentralus,westus2,koreacentral,koreasouth,francecentral,southafricanorth
```

A hiba az alábbi lépések egyikével oldható meg:

#### <a name="option-1"></a>1. lehetőség
Ellenőrizze az erőforrástípus elérhetőségét az Azure-régiókban a [Termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/) lapon. Ha az erőforrástípus nem érhető el egy adott régióban, devtest labs nem támogatja a labor létrehozása az adott régióban. Válasszon másik területet a tesztkörnyezet létrehozásakor.

#### <a name="option-2"></a>2. lehetőség
Ha az erőforrástípusa elérhető a régióban, ellenőrizze, hogy regisztrálva van-e az előfizetéssel. Meg lehet tenni az előfizetés tulajdonosa szinten, ahogy ebben a [cikkben](../azure-resource-manager/management/resource-providers-and-types.md)látható .
