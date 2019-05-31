---
title: Az Azure DevTest Labs – gyakori kérdések |} A Microsoft Docs
description: Válaszok az Azure DevTest Labs kapcsolatos gyakori kérdésekre.
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
ms.date: 04/19/2019
ms.author: spelluru
ms.openlocfilehash: a46d816c04d9f5629c2ee9538016d42c53f9a331
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244390"
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs GYIK
Az Azure DevTest Labs kapcsolatos leggyakoribb kérdésekre adott válaszok.

## <a name="blog-post"></a>Blogbejegyzés
A DevTest Labs-csapat blogja kezdődően 2019. március 20. így visszavontuk. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Ahol követhető nyomon a funkciófrissítések mostantól?
Mostantól azt fogjuk kell üzenetküldés funkció- és informatív blogbejegyzések az Azure-blogban, és az Azure frissíti. Ezek a blogbejegyzések is összekapcsolja a dokumentáció tartalmaz, bárhol is szükséges.

Fizessen elő a [DevTest Labs Azure blogon](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) és [frissíti az Azure DevTest Labs](https://azure.microsoft.com/updates/?product=devtest-lab) licenchasználati DevTest Labs szolgáltatásban létrehozott új funkciókról.

### <a name="what-happens-to-the-existing-blog-posts"></a>Mi történik, a meglévő blogbejegyzések?
Jelenleg dolgozunk (kivéve a szolgáltatáskimaradás frissítések) áttelepítése meglévő blogbejegyzések a [DevTest Labs-dokumentáció](devtest-lab-overview.md). Ha az MSDN-blog elavult, azt a rendszer átirányítja a dokumentáció – áttekintés a DevTest Labs szolgáltatásban. Miután átirányítva, kereshet a cikk a "Szűrő által" cím keres. Hogy még nincs áttelepítve minden bejegyzés, de a hónap végén kell elvégezni. 


### <a name="where-do-i-see-outage-updates"></a>Hol láthatók szolgáltatáskimaradás frissítések?
Fogja azt könyvelési szolgáltatáskimaradás frissítéseket, a Twitter-leírót múlva frissítésétől kezdve a. Kövessen minket a Twitteren a legújabb frissítéseket a leállásokat és az ismert hibák.

### <a name="twitter"></a>Twitter
A Twitter-leírót: [@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Általános kérdések
### <a name="what-if-my-question-isnt-answered-here"></a>Mi történik, ha kérdésem itt nem választ?
Ha a kérdés nem szerepel a listán, tudassa velünk, így segítségére választ találjanak.

- Ez a GYIK végén kérdését. Az Azure Cache csapata és a Közösség többi tagjával, ez a cikk kapcsolatos érhet el.
- Szélesebb elérni, új kérdést tenne fel a a [Azure DevTest Labs MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Az Azure DevTest Labs-csapat és a Közösség más tagjai érhet el.
- Szolgáltatással kapcsolatos kéréseit, küldje el a kérelmek és ötlet segíthet megtenni [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="what-is-a-microsoft-account"></a>Mi a Microsoft-fiókkal?
Microsoft-fiókot egy olyan fiók, szinte mindenben a Microsoft eszközeit és szolgáltatásait használja. Egy e-mail címet és a Skype, Outlook.com, onedrive vállalati verzió, Windows phone, Azure és az Xbox Live-ba való bejelentkezéshez használt jelszó. Egy olyan fiók, az azt jelenti, hogy a fájlokat, fényképeket, névjegyek és beállítások követheti, bármilyen eszközön.
 
> [!NOTE]
> Microsoft-fiók használatával hívható meg egy Windows Live ID azonosítójával.

### <a name="why-should-i-use-azure-devtest-labs"></a>Miért érdemes használni az Azure DevTest Labs?
Az Azure DevTest Labs a csapat időt és pénzt takaríthat meg. A fejlesztők több különböző adatbázisok használatával hozhat létre saját környezeteket. Is használhatják összetevők a gyors üzembe helyezése, és konfigurálhatja az alkalmazásokat. Egyéni rendszerképek és képletek használatával sablonként menteni a virtuális gépek (VM), és könnyedén között a csapat reprodukálnia. DevTest Labs is kínál számos konfigurálható szabályzatok a labor rendszergazdák segítségével csökkentheti a veszteséget és a egy csapat-környezetek felügyeletét. Ezek a házirendek automatikus leállítási, költség küszöbértéket, felhasználónként, és maximális Virtuálisgép-méret maximális virtuális gépek közé tartozik. Részletesebb magyarázatra van szüksége a DevTest Labs szolgáltatásban, lásd: a [áttekintése](devtest-lab-overview.md) vagy a [bevezető videót](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>Mit jelent "megbízható önkiszolgáló"?
Önkiszolgáló megbízható, az azt jelenti, hogy a fejlesztők és tesztelők hozzon létre saját környezeteket igény szerint. Biztonsága érdekében, hogy a DevTest Labs segítségével állítsa be a megfelelő hozzáférés, a maximális hatékonyság és a költségek csökkentését, hogy a rendszergazdák rendelkeznek. A rendszergazdák is adja meg, melyik Virtuálisgép-méretek engedélyezett, a virtuális gépek maximális számát, és ha virtuális gép elindult, és állítsa le. DevTest Labs is megkönnyíti a költségek figyelése, és segítenek, hogy vegye figyelembe, hogy a labor-erőforrásokat vannak használatban, a riasztásokat állíthat be.

### <a name="how-can-i-use-devtest-labs"></a>Hogyan használható a DevTest Labs?
DevTest Labs akkor hasznos, bármikor szükséges fejlesztési vagy tesztelési környezetek, és azokat gyorsan reprodukálhatja vagy költségtakarékos házirendek segítségével kezelheti azokat.
Az alábbiakban néhány olyan forgatókönyvet használó ügyfeleink számára a DevTest Labs szolgáltatásban:

- Kezelheti a fejlesztési és tesztelési környezetek egy helyen. Házirendek használata csökkentheti a költségeket, és hozzon létre egyéni rendszerképek megosztani a csapat közötti épít fel.
- Egy alkalmazás fejlesztése egyéni rendszerképek használatával való mentése során a fejlesztési szakaszban a lemez állapotát.
- Nyomon követheti a folyamat viszonyítva költség.
- Minőségi assurance tesztelési tömeges tesztelési környezeteket hozhat létre.
- Összetevők és a képletek használatával egyszerűen konfigurálhatja, és Reprodukálja egy alkalmazás különböző környezetekben.
- Virtuális gépek elosztása a ötletbörzékhez (együttműködési fejlesztési és tesztelési munka), és ezután könnyedén megszüntetése őket, amikor véget ért az esemény.

### <a name="how-am-i-billed-for-devtest-labs"></a>Hogyan történik a számlázás a DevTest Labs?
DevTest Labs szolgáltatás ingyenes. Tesztkörnyezetek létrehozása és konfigurálása a házirendek, sablonok és összetevők a DevTest Labs szolgáltatásban díjmentes. Csak az Azure-erőforrások használják a Labs-környezetben, például a virtuális gépek, tárfiókok és virtuális hálózatok fizet. A labor-erőforrásokat kapcsolatos további információkért lásd: [Azure DevTest Labs díjszabás](https://azure.microsoft.com/pricing/details/devtest-lab/).

## <a name="security"></a>Biztonság

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Mik azok a különböző biztonsági szintek a DevTest Labs?
Szerepköralapú hozzáférés-vezérlés (RBAC) hozzáférés biztonsági határozza meg. Ha szeretné megtudni, hogyan működik a hozzáférés, segít a engedélyt, a szerepkör és egy hatókör közötti különbségekről további RBAC szerint.

- **Engedély**: Engedély egy meghatározott hozzáférést egy adott művelet. Például egy engedély az olvasási hozzáférést minden virtuális gép lehet.
- **Szerepkör**: A szerepkör az vannak csoportosítva, és a felhasználóhoz rendelt engedélyek egy készletét. Ha például egy előfizetés-tulajdonosi szerepkörrel rendelkező felhasználó hozzáfér egy előfizetésen belüli összes erőforrás.
- **Hatókör**: A hatókör egy Azure-erőforrás a hierarchiában egy szint. A hatókör lehet például egy erőforráscsoport, egy egyetlen labor vagy a teljes előfizetés.

DevTest Labs hatókörén belül van, amelyek meghatározzák a felhasználói engedélyek szerepkörök két típusa:

- **Labor tulajdonosa**: A lab tulajdonosa erőforrásokhoz való teljes hozzáférés a tesztkörnyezetben. Egy tesztlabor tulajdonosa is szabályzatok módosításához, olvasni és írni az olyan virtuális gépek, módosítsa a virtuális hálózat, és így tovább.
- **Lab-felhasználó**: A lab felhasználói megtekinthetik minden labor-erőforrásokat, például a virtuális gépek, a házirendek és a virtuális hálózatok. A lab-felhasználó, szabályzatok, vagy bármely más felhasználók által létrehozott virtuális gépek nem módosítható.

Egyéni szerepkörök a DevTest Labs szolgáltatásban hozhat létre. Egyéni szerepkörök létrehozása a DevTest Labs szolgáltatásban létrehozott kapcsolatban lásd: [felhasználó engedélyeket adott laborszabályzatok](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Mivel hatókörök hierarchikus, amikor a felhasználók számára egy bizonyos hatókörben, a felhasználó számára automatikusan biztosított ezeket az engedélyeket, minden egyes alacsonyabb szintű hatókörhöz. a hatókör. Például ha egy felhasználó az előfizetés tulajdonosa a szerepkör van hozzárendelve, a felhasználónak hozzáférése van az összes erőforrást az előfizetéshez. Ilyen erőforrások többek között a virtuális gépek, virtuális hálózatok és tesztkörnyezetek. Előfizetés tulajdonosaként automatikusan örökli a labor tulajdonosi szerepkörhöz. Azonban nem igaz ennek az ellenkezője. A lab tulajdonosa hozzáférés a laborokhoz, amely kisebb, mint az előfizetés szintjén hatókör. Tehát egy tesztlabor tulajdonosa nem látható, virtuális gépek, virtuális hálózatok vagy bármely más erőforrások, amelyek túlmutatnak a labor létrehozása.

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Hogyan szerepköralapú hozzáférés-vezérlő megadásához a DevTest Labs környezetben, győződjön meg arról, hogy az informatikai részleg szabályozhatja fejlesztői és tesztelési is munkájuk során?
Széles körű mintát, van azonban a részletek a szervezet függ.

Központi informatikai kell saját, csak hogy mire szükség, és engedélyezni szeretné, hogy a szükséges mértékű a projektet és az alkalmazás csapatok. Általában azt jelenti, hogy a központi informatikai az előfizetés tulajdonosa, és kezeli a központi informatikai funkciók, például a hálózati konfiguráció. Készletét **tulajdonosok** előfizetés kis kell lennie. Ezeket a tulajdonosokat további tulajdonosok jelöl ki, amikor szükség van, vagy előfizetési szintű szabályzatok, például "nincs nyilvános IP-címnél.

Előfizetésen, például az 1. szint vagy a 2. rétegbeli támogatási hozzáférést igénylő felhasználók alcsoportjaihoz lehet. Ebben az esetben javasoljuk, hogy ezek a felhasználók számára a **közreműködői** elérni, hogy azok is felügyelheti az erőforrásokat, de nem adja meg a felhasználói hozzáférés vagy házirendek beállítása.

A DevTest Labs-erőforrás a tulajdonosok, akik megközelíti a projekt vagy alkalmazás csapat helyvezérlőhöz kell tartoznia. Fontos, mivel megértik a gépek és a szükséges szoftverek szükséges követelményeknek. A szervezetek többségében a DevTest Labs erőforrás tulajdonosa gyakran a projekt/fejlesztési vezető. Erre a tulajdonosra kezelheti a felhasználók és a tesztkörnyezet-szabályzatait, és kezelheti a DevTest Labs-környezetben lévő összes virtuális gép.

A projekt vagy alkalmazás csapat tagjainak hozzá kell adni a **DevTest Labs-felhasználó** szerepkör. Ezek a felhasználók is létrehozhatnak virtuális gépeket (beágyazott a labor és előfizetés-szintű szabályzatok). Is kezelhetik saját virtuális gépeiket. Más felhasználókhoz tartozó virtuális gépek, nem tudja kezelni.

További információkért lásd: [Azure enterprise scaffold-előíró előfizetés-irányítás dokumentáció](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Hogyan hozhatok létre egy szerepkört, hogy a felhasználók egy adott feladat?
Egyéni szerepkörök létrehozása és az engedélyek hozzárendelése szerepkörhöz kapcsolatos átfogó című cikket, lásd: [felhasználó engedélyeket adott laborszabályzatok](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Íme egy példa a parancsfájl, amely létrehozza a szerepkör **DevTest Labs-speciális felhasználó**, amely jogosult arra, hogy a labor virtuális gépeinek kezdődik és:


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

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Hogyan biztosíthatja a szervezet vállalati biztonsági házirendek legyenek érvényben?
Egy szervezet előfordulhat, hogy érhet el, a következő műveletek végrehajtásával:

- Fejlesztés és egy átfogó biztonsági házirend közzététele. A szabályzat szabályainak használati feltételek a használatával társított articulates szoftver, a felhőbeli eszközöket. Azt is meghatározza, milyen jól sérti a szabályzatot.
- Egyéni rendszerkép, az egyéni összetevők és a egy folyamatot, amely lehetővé teszi, hogy az active Directoryval meghatározott biztonsági tartományon belüli vezénylési fejleszthet. Ez a megközelítés kikényszeríti a céges határhálózaton, és beállítja a környezeti vezérlőknek az alábbiakat közös csoportját. Ezek a vezérlők a környezetre leselkedő fejlesztő során figyelembe veheti a fejlesztés és a egy biztonságos fejlesztési életciklus során kövesse az általános folyamat részeként. A cél is, amely nem túl szigorú környezetet biztosít a május akadályozzák a fejlesztési, de ésszerű csoportját. A csoportházirendek a szervezeti egység (OU), amely tartalmazza a virtuális gépeiről, éles környezetben található összes csoportházirendek egy része lehet. Másik lehetőségként el megfelelően az összes azonosított kockázatok csökkentése, egy további csoportot.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>Hogyan biztosíthatja a szervezet adatok integritásának megőrzése, győződjön meg arról, hogy a távoli eljáráshívás a fejlesztők nem tudja eltávolítani a kód vagy vezeti be a kártevők vagy nem jóváhagyott szoftverek?
Nincsenek külső tanácsadók, alvállalkozók és az alkalmazottak, amelyeket a távoli eljáráshívás DevTest Labs-környezetben együttműködéshez a fenyegetés ellenőrzési több réteget.

Ahogy korábban is hangsúlyoztuk, az első lépés egy használati feltételei elkészíteni, és definiálva, amely egyértelműen ismerteti a következmények, ha valaki megsértik a házirendet kell rendelkeznie.

Az első réteg azoknak a vezérlőelemeknek a távoli hozzáféréshez, amely közvetlenül a labor nem kapcsolódik VPN-kapcsolaton keresztül egy távoli hozzáférési házirend alkalmazása.

A második réteg vezérlők, hogy a alkalmazni a csoportházirend-objektumok, amelyek megakadályozzák a másolás, és illessze be a távoli asztalon keresztül egy készletét. A hálózati házirend nem engedélyezi a kimenő a környezetben, például az FTP és RDP szolgáltatásokat a környezetén valósíthatja meg. Felhasználó által meghatározott útválasztás kényszerítheti az összes Azure hálózati forgalom vissza a helyszíni, de előfordulhat, hogy engedélyezi az adatok feltöltését, kivéve, ha megvizsgálja a tartalom és a munkamenetek proxyn keresztüli ellenőrzött URL-címek az Útválasztás nem sikerült tárfiókot. Nyilvános IP-címek támogatása a DevTest Labs szolgáltatásban, hogy ne engedélyezze az adatközponthíd-képzés egy külső hálózati erőforrás a virtuális hálózaton belül korlátozott lehet.

Végső soron korlátozások ugyanazt a típusú szükség van, a szervezet, amely volna az összes lehetséges módszer a cserélhető adathordozó vagy a külső URL-címek, amely fogadni tudta egy bejegyzést a tartalom. Tekintse meg a biztonsági szakembereknek, tekintse át és a egy biztonsági házirend bevezetése. További javaslatokért lásd: [Microsoft Kibertámadások biztonsági](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Tesztlabor-konfiguráció

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Hogyan hozhatok létre labor a Resource Manager-sablonnal?
Biztosítunk egy [lab Azure Resource Manager-sablonok GitHub-adattára](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) üzembe helyezhető-, vagy módosítsa a laborral kapcsolatos egyéni sablonokat létrehozni. Mindegyik sablon található hivatkozás segítségével üzembe a labort, mivel a saját Azure-előfizetésében. Vagy testre szabhatja a sablont, és [üzembe helyezése PowerShell vagy az Azure CLI használatával](../azure-resource-manager/resource-group-template-deploy.md).


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Ehelyett kellene a saját erőforráscsoport egyes gépek közös erőforráscsoportban kell létrehozni az összes virtuális gép is van? 
Igen, labortulajdonosként, vagy engedélyezheti a labor létrehozása erőforrás-csoport kiosztását az Ön számára, vagy az összes virtuális gép létrehozása egy közös erőforráscsoportban adjon meg. 

Csoport külön erőforrást. példa:
-   DevTest Labs létrehoz egy új erőforráscsoportot, minden nyilvános/titkos IP virtuális gép, üzembe helyezése
-   DevTest Labs hoz létre egy erőforráscsoportot az azonos méretű tartozó megosztott IP-gépek.

Gyakori erőforrás-csoport forgatókönyv:
-   Összes virtuális gépet hoz létre a közös erőforráscsoportban, adjon meg. További [a labor csoport kiosztott erőforrás](https://aka.ms/RGControl). 

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Hogyan karbantartása egy elnevezési konvenciója a DevTest Labs-környezetben?
Előfordulhat, hogy szeretné kiterjeszteni a jelenlegi vállalati elnevezési konvenciók Azure műveletekhez, és azokat konzisztens a DevTest Labs-környezetben. DevTest Labs, javasoljuk, hogy adott kezdési házirendek. Ezek a szabályzatok központi parancsfájlt és a JSON-sablonok, ha egységes telepít. Házirendek elnevezési implementálható az előfizetői szintre vonatkoznak az Azure szabályzatokkal. JSON-minták az Azure Policy, lásd: [Azure Policy minták](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Hány labs hozhat létre egy előfizetésen belül?
A megadott határértéket előfizetésenként létrehozható labs száma nem áll rendelkezésre. Azonban előfizetésenként használt erőforrások mennyisége korlátozva. Itt olvashat a [korlátok és kvóták az Azure-előfizetések](../azure-subscription-service-limits.md) és [ezek a korlátok növelése](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).


### <a name="how-many-vms-can-i-create-per-lab"></a>Hány virtuális gépet hozhat létre egy tesztlabor?
Nincs konkrét tesztlabor hozható létre virtuális gépek száma korlátlan. Azonban az erőforrásokat (virtuális gép magjainak, nyilvános IP-címeket, és így tovább) használt előfizetésenként korlátozott is. Itt olvashat a [korlátok és kvóták az Azure-előfizetések](../azure-subscription-service-limits.md) és [ezek a korlátok növelése](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Hogyan állapítható meg a felhasználók labor és a laborokban, amelyek szükségesek a vállalaton belül teljes számának aránya?
Azt javasoljuk, hogy üzleti egységek és az azonos alkalmazásfejlesztési projekt társított a fejlesztői csoportok társítva a azonos labor. Lehetővé teszi a szabályzatok, a képek és a Leállítás házirendek azonos típusú csoportban is alkalmazható.

Szükség lehet figyelembe venni a földrajzi határokon. Például a fejlesztők az északi kelet Egyesült Államok (US) felhasználhatja az USA 2. keleti régiója kiépített labor. És a fejlesztők számára, Dallas, Texas, és Denver, Colorado irányított használjon egy erőforrást, az USA déli középső régiója. Ha egy külső külső gyártótól származó egy együttműködésen alapuló tevékenységi, azok sikerült hozzárendelni a belső fejlesztők által nem használt laborokhoz.

Az Azure DevOps Projects belül egy adott projekt labor is használhatja. Ezután alkalmazza megadott Azure Active Directory csoporthoz, amely lehetővé teszi, hogy mindkét erőforráskészlettel való hozzáférés biztonságát. A virtuális hálózat a labor rendelt egyesíthetők a felhasználók egy másik határ lehet.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Hogyan tudjuk megakadályozni labor belüli erőforrások törlését?
Javasoljuk, hogy, megfelelő engedélyekkel a labor szintjén, hogy csak a jogosult felhasználók erőforrásokat törölheti vagy módosíthatja a laborszabályzatok. A fejlesztők belül kell elhelyezni a **DevTest Labs-felhasználó** csoport. A vezető fejlesztői vagy az infrastruktúra érdeklődő kell lennie a **DevTest Labs tulajdonosa**. Azt javasoljuk, hogy csak két labortulajdonosok. Ez a szabályzat kiterjesztése felé a kódtárat a sérülés elkerülése érdekében. Labor felhasználók erőforrások használatára jogosult, de nem tudja frissíteni a laborszabályzatok. Tekintse meg a következő cikkben, amely felsorolja a szerepkörök és a jogosultságokat, amelyeket a labor belül minden beépített csoport rendelkezik: [Tulajdonosok és felhasználók hozzáadása az Azure DevTest Labs szolgáltatásban](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Hogyan oszthatom meg egy közvetlen hivatkozást a laborhoz?

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a labor létrehozása.
2. Másolás a **labor URL-cím** a böngészőből, és megoszthatja a labor felhasználók.

> [!NOTE]
> Lab-felhasználó külső felhasználónak kinek van egy Microsoft-fiókkal, de aki nem tagja a szervezet Active Directory-példánynak számít, ha a felhasználó megjelenhet egy hibaüzenet, ha megpróbálnak hozzáférni a megosztott hivatkozás. Ha egy külső felhasználót a hibaüzenetet látja, kérje meg a felhasználót, hogy először válassza az Azure portal jobb felső sarkában a nevét. Ezután a menüben Directory szakaszában a felhasználó kiválaszthatja a könyvtárban, ahol a labor létezik.

## <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Miért nem látom virtuális gépeket a Virtual Machines lapon láthatók a DevTest Labs?
A DevTest Labs szolgáltatásban létrehozott virtuális gép, amikor felhőszolgáltatására, hozzáféréssel a virtuális Gépeket. Megtekintheti a virtuális gép, mind a labs lapon, majd a a **virtuális gépek** lapot. Hozzárendelt felhasználók a **DevTest Labs tulajdonosa** szerepkör tekintheti meg a labor létrehozása a lab-ben létrehozott összes virtuális gép **összes virtuális gép** lap. Azonban felhasználók, akik rendelkeznek a **DevTest Labs-felhasználó** szerepkör automatikusan nem teljesíthetők, hogy más felhasználók létrehozott Virtuálisgép-erőforrások olvasási hozzáférést. Ezért ezeken a virtuális gépeken nem jelennek meg a **virtuális gépek** lapot.


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Hogyan hozhatok létre több virtuális gép ugyanazt a sablont a egyszerre?
Egyszerre több virtuális gép ugyanazt a sablont hoz létre a két lehetősége van:

- Használhatja a [Azure fejlesztési és üzemeltetési feladatokat bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
- Is [Resource Manager-sablon létrehozása](devtest-lab-add-vm.md#save-azure-resource-manager-template) hoz létre egy virtuális Gépet, miközben és [üzembe helyezése a Resource Manager-sablon a Windows Powershellből](../azure-resource-manager/resource-group-template-deploy.md).
- Adja meg a létrehozandó virtuális gép létrehozása közben egy gép egynél több példányát is. Több példány a virtuális gépek létrehozásával kapcsolatos további tudnivalókért lásd a dokumentum [laborbeli virtuális gép létrehozása](devtest-lab-add-vm.md).

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Hogyan helyezhetek át meglévő Azure virtuális gépek a DevTest Labs labor be?
A meglévő virtuális gépek DevTest Labs másolása:

1.  A meglévő virtuális gép VHD-fájl másolása használatával egy [Windows PowerShell-parancsprogram](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1).
2.  Hozzon létre a [egyéni rendszerkép](devtest-lab-create-template.md) a DevTest Labs labor belül.
3.  Hozzon létre egy virtuális Gépet a labor létrehozása az egyéni rendszerképpel.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Tudok több lemez is csatlakoztatható a virtuális gépek?

Igen, több lemez is csatlakoztatható a virtuális gépekhez.

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Ha szeretne egy Windows operációs rendszer lemezképe használjam tesztelésre, kell vásárolni az MSDN-előfizetéssel?
Windows ügyfél operációsrendszer-lemezképek (Windows 7 vagy újabb verzió) használata a fejlesztési vagy tesztelési Azure-ban, hajtsa végre az alábbi lépések egyikét:

- [Az MSDN-előfizetés vásárlása](https://www.visualstudio.com/products/how-to-buy-vs).
- Ha nagyvállalati szerződéssel rendelkezik, hozzon létre egy Azure-előfizetést, a [Enterprise Dev/Test ajánlat](https://azure.microsoft.com/offers/ms-azr-0148p).

Az Azure-kreditek minden MSDN-ajánlat a kapcsolatos további információkért lásd: [havi Azure-kredit a Visual Studio-előfizetők](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Hogyan automatizálható a laborkörnyezetben található összes virtuális gép törlésének?
Labortulajdonosként virtuális gépek törölheti az Azure Portalon tesztkörnyezetben. Is törölheti a virtuális gépek a tesztkörnyezetben egy PowerShell-parancsprogram használatával. A következő példa alapján a **értékek módosítása** megjegyzés, módosítsa a paraméterek értékeit. A lab panelről az Azure Portalon kérheti le a subscriptionId, labResourceGroup és labName értékeket.

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

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Hogyan használhatok Resource Manager-sablonok a DevTest Labs Környezetemet?
A Resource Manager-sablonokat a DevTest Labs-környezetben szereplő lépések segítségével telepítheti a [környezetek funkció DevTest Labs szolgáltatásban létrehozott](devtest-lab-test-env.md) cikk. Alapvetően a Resource Manager-sablonok tekintsen meg egy Git-tárház (az Azure-Adattárakkal vagy GitHub), és adjon hozzá egy [a sablonok privát tárház](devtest-lab-test-env.md) a tesztkörnyezethez. Ebben a forgatókönyvben nem lehet hasznos, ha a DevTest Labs használatával fejlesztési gazdagépeken, de előfordulhat, hogy lehet hasznos, ha egy átmeneti környezet, amely jellemző az üzemi fejleszt.

Emellett akkor is érdemes megjegyezni, hogy a labor időszakonkénti felhasználói beállítás, vagy a virtuális gépek száma csak korlátozza a laborkörnyezet, és nem bármely (Resource Manager-sablonok) környezetekben a natív módon készített gépek száma.

## <a name="custom-images"></a>Egyéni rendszerképek

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Hogyan állítható be egy egyszerű, megismételhető folyamattal ahhoz, hogy saját egyéni szervezeti lemezképek DevTest Labs-környezetben?
Ez [videó, kép gyári mintát](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Ebben a forgatókönyvben a speciális helyzetben, és a megadott szkriptek mintaszkriptek csak. Ha módosítások szükségesek, kezelésére és karbantartására a környezetében használt parancsfájlokra szüksége.

Egy rendszerkép-előállító létrehozása a részletes információkért lásd: [egy egyéni rendszerkép-előállító létrehozása az Azure DevTest Labs szolgáltatásban](image-factory-create.md). 

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Mi a különbség egy egyéni rendszerkép és a egy képlet?
Egyéni rendszerkép egy felügyelt rendszerképet. A képlet egy képet további beállításokat is konfigurálhatja, majd mentse és Reprodukálja. Egyéni rendszerkép Ha több környezetek gyors létrehozását az ugyanazon alapvető, nem módosítható a lemezkép használatával szeretné használata előnyösebb lehet. Előfordulhat, hogy egy képlettel jobb, ha azt szeretné, a legújabb elemeket és a virtuális gép konfigurációjának állítja elő a virtuális hálózat vagy alhálózat részeként, vagy egy meghatározott méretű virtuális gépként. Részletesebb magyarázatra van szüksége, lásd: [egyéni rendszerképek és képletek DevTest Labs szolgáltatásban létrehozott](devtest-lab-comparing-vm-base-image-types.md).

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Mikor érdemes használni az egyéni rendszerkép vagy képlet?
Ebben a forgatókönyvben a döntő tényező általában ez költséghatékony, és újra felhasználhatja. Ha rendelkezik egy olyan forgatókönyvet, ahol sok felhasználó/labs megkövetelése a szoftver az alaprendszerképhez nagy kép, akkor csökkentheti a költségeket hozzon létre egy egyéni rendszerképet. Ez azt jelenti, hogy a lemezkép létrehozása után. Ez csökkenti a telepítési idő a virtuális gép és miatt a telepítő előfordulásakor futó virtuális gép költsége.

Egy további figyelembe vegye figyelembe azonban, a gyakori változásai a szoftvercsomag. Ha napi állít össze, és szükséges, hogy a felhasználók virtuális gépeket, a szoftver fontolja meg a képlet helyett egyéni rendszerkép.

Részletesebb magyarázatra van szüksége, lásd: [egyéni rendszerképek és képletek összehasonlítása](devtest-lab-comparing-vm-base-image-types.md) DevTest Labs-környezetben.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Hogyan automatizálható az egyéni lemezképek létrehozása VHD-fájlok feltöltése a folyamat?

Automatizálás létrehozása egyéni rendszerképek VHD fájlokat tölthet fel, két lehetősége van:

- Használat [AzCopy](../storage/common/storage-use-azcopy-v10.md) másolása vagy VHD-fájlok feltöltése a storage-fiók a tesztkörnyezetben társított.
- Használat [az Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). Storage Explorer egy önálló alkalmazás, amely Windows, OS X és Linux rendszereken.

A cél tárfiók a labor társított megkeresése:

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2.  A bal oldali menüben válassza ki a **erőforráscsoportok**.
3.  Keresse meg és válassza ki az erőforráscsoportot, amely a labor van társítva.
4.  A **áttekintése**, válasszon ki egy tárfiókot.
5.  Válassza ki a **Blobok** lehetőséget.
6.  Keresse meg a listában lévő feltöltések. Ha még nem létezik, térjen vissza a 4. lépés, és próbálja meg egy másik tárfiókba.
7.  Használja a **URL-cím** az AzCopy-parancsban céljaként.

Mikor célszerű használni és a saját egyéni szervezeti rendszerkép Azure Marketplace-rendszerképpel?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Mikor célszerű használni és a saját egyéni szervezeti rendszerkép Azure Marketplace-rendszerképpel?
Az Azure Marketplace-en alapértelmezés szerint használandó, kivéve, ha bizonyos szempontból problematikus jelenségeket vagy a szervezeti követelményeknek. Néhány gyakori példa:;

- Összetett szoftver telepítése, amely az alaprendszerképet része egy alkalmazás szükséges.
- Telepítés és a egy alkalmazás telepítése, amely nem használja az Azure Marketplace-rendszerképpel hozzáadandó tartó hatékonyan számos óráig is eltarthat.
- Fejlesztőknek és tesztelőknek hozzáférést igényelnek a virtuális gépek gyors, és a egy új virtuális gép telepítési idő minimalizálása érdekében szeretné.
- Megfelelőségi vagy szabályozási feltételek (például biztonsági szabályzat), amely minden gép helyen kell lennie.
- Egyéni rendszerképek használatával nem tekinthető kódjával. További összetettséget módon kell kezelni VHD-fájlok alaplemezképek mögöttes vezetnek. Is kell rendszeresen javítani ezeket alaplemezképek a szoftverfrissítésekhez. Ezek a frissítések közé tartozik az új operációs rendszer (OS) frissítéseket, és a frissítések vagy a szoftvercsomag maga a szükséges konfigurációs módosításokat.

## <a name="artifacts"></a>Összetevők

### <a name="what-are-artifacts"></a>Mik az összetevők?
Összetevők olyan testreszabható elemek, amelyek segítségével telepítheti a legújabb elemeket vagy a fejlesztői eszközöket egy virtuális géphez. Összetevők csatlakoztassa a virtuális Géphez a virtuális gép létrehozásakor. Után a virtuális gép ki van építve, az összetevők üzembe helyezése, és konfigurálja a virtuális Gépet. Különböző már meglévő összetevőkkel érhető el a [nyilvános GitHub-adattárból](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Emellett [hozhat létre saját összetevők](devtest-lab-artifact-author.md).

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Az összetevő nem sikerült a virtuális gépek létrehozása során. Hogyan háríthatom azt?
Megtudhatja, hogyan szerezhet be naplófájlokat a sikertelen összetevő, lásd: [DevTest Labs-környezetben az összetevők hibáinak diagnosztizálása](devtest-lab-troubleshoot-artifact-failure.md).

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Mikor célszerű használni egy szervezet olyan nyilvános összetevőtárral és a DevTest Labs szolgáltatásban létrehozott privát összetevőtárban?
A [nyilvános összetevőtárral](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) biztosít egy kezdeti szoftvercsomagok leggyakrabban használt. Segít a gyors üzembe helyezési nélkül teljesítheti a kitűzött időpontja reprodukálnia általános fejlesztői eszközöket és beépülő modulok. Kiválaszthatja, hogy a saját privát tárház üzembe helyezéséhez. Nyilvános és a egy privát tárház párhuzamosan is használhatja. Dönthet úgy is, a nyilvános tárházban letiltása. A feltételek, és üzembe helyezése a privát tárházat kell meghatározni a következő kérdések és szempontok szerint:

- Rendelkezik a szervezete olyan követelménnyel rendelkezik vállalati licencelt szoftvereket a DevTest Labs-ajánlatába részeként? Ha a válasz Igen, a privát tárházat kell létrehozni.
- Nem a szervezet egyéni szoftver, amely tartalmaz egy adott művelethez, amely részeként szükség van az általános üzembe helyezési folyamat fejlesztése? Ha a válasz Igen, a privát tárházat kell telepíteni.
- Ha a szervezet cégirányítási házirend szükséges elkülönítés, és a külső tárolóhelyekkel a szervezet által közvetlen kezelés alatt nem, privát összetevőtárban kell telepíteni. Ez a folyamat részeként egy kezdeti másolatot készít a nyilvános tárházban másolható és a privát tárházban integrálva. A nyilvános tárházban lehet letiltani, majd, hogy nem a szervezeten belül férhetnek azt többé. Ez a megközelítés arra kényszeríti a szervezeten belüli összes felhasználó csak egy adattárba, amely jogosult a szervezet rendelkezik, és minimalizálja a konfigurációs csúszásokat.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Egy szervezet tervezése egy adattárba vagy több tárházak engedélyezése?
A szervezet teljes irányítás és a konfiguráció-kezelési stratégia részeként azt javasoljuk, hogy egy központi tárházban használja. Több adattárak használata esetén a silók a nem felügyelt szoftver az idő függvényében előfordulhat, hogy válnak. Egy központi tárházban, a több csapat felhasználhatja az adattárból projektjeikhez tartozó összetevőket. Szabványügyi szervezet, a biztonság, a könnyű kezelés kikényszeríti, és kiküszöböli a párhuzamos erőfeszítések. A forrásadattárakból részeként a következő műveletek használata ajánlott eljárások a hosszú távú felügyeleti és népszerűsítését:

- Az Azure-Adattárakkal társítása az azonos Azure Active Directory-bérlő az Azure-előfizetés által használt a hitelesítéshez és engedélyezéshez.
- Hozzon létre egy csoportot nevű `All DevTest Labs Developers` az Azure Active Directoryban, amely központilag felügyelt. Minden fejlesztő, aki összetevő fejlesztési hozzájárul az ebbe a csoportba kell elhelyezni.
- Az azonos Azure Active Directory-csoport hozzáférést biztosít az Azure-Adattárakkal adattárba, és a labor létrehozása a használható.
- Az Azure-Adattárakkal Elágaztatás és elágaztatási használandó külön egy a-development tárházhoz az elsődleges éles adattárból. Tartalom csak hozzáadódik a főágba irányuló lekéréses kérelmet a megfelelő kód áttekintése után. A kód a felülvizsgáló jóváhagyja a módosítást, miután érdeklődő fejlesztő, aki felelős karbantartása a master ággal, egyesíti a frissített kóddal.

## <a name="cicd-integration"></a>CI/CD-integráció

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>DevTest Labs a CI/CD eszközlánc integrálása?
Ha használja az Azure DevOps, használhatja a [DevTest Labs-feladatok bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) a DevTest Labs szolgáltatásban létrehozott kibocsátási folyamatok automatizálásához. Ez a bővítmény elvégezhető feladatok a következők:

- Hozzon létre, és automatikusan üzembe helyezése egy virtuális Gépet. Is konfigurálhatja a virtuális Gépet a legújabb buildben az Azure File Copy vagy a PowerShell az Azure DevOps-szolgáltatásokkal feladatok használatával.
- Tesztelés reprodukálnia a hibát ugyanarról a virtuális gépről további vizsgálat után automatikusan rögzítheti a virtuális gép állapotát.
- Ha már nincs rá szükség, törölje a kibocsátási folyamat végén a virtuális gép.

Az alábbi blogbejegyzések ajánlat útmutató és az Azure DevOps-szolgáltatásokkal bővítmény használatával kapcsolatos információk:

- [DevTest Labs és az Azure DevOps-bővítmény](integrate-environments-devops-pipeline.md)
- [Az Azure DevOps-szolgáltatások egy meglévő DevTest Labs labor egy új virtuális gép üzembe helyezése](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Az Azure DevOps Services kiadáskezelés használatával DevTest Labs folyamatos központi telepítésére](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Egyéb folyamatos integrációs (CI) / folyamatos készregyártás (CD) fordíthatók, akkor érhető el, ugyanezeket a forgatókönyveket a telepítése [Azure Resource Manager-sablonok](https://azure.microsoft.com/resources/templates/) használatával [Azure PowerShell-parancsmagok](../azure-resource-manager/resource-group-template-deploy.md) és [.NET SDK-k](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Használhatja még [REST API-k a DevTest Labs](https://aka.ms/dtlrestapis) az eszközlánc integrálását.

## <a name="networking"></a>Hálózat

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>Mikor kell új virtuális hálózat létrehozása a DevTest Labs környezet és a egy meglévő virtuális hálózattal?
Ha a virtuális gépek kell együttműködik a meglévő infrastruktúra, akkor érdemes egy meglévő virtuális hálózaton belül a DevTest Labs-környezetben. ExpressRoute használatakor érdemes a lehető legkevesebb virtuális hálózatok vagy alhálózatok, hogy Ön nem darabolható az IP-címterületének, lekéri az előfizetések alatt használatra rendelt. 

Fontolja meg a virtuális hálózatok közötti társviszony-létesítési minta használatát itt ([Küllős modell](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)) túl. Ez a megközelítés lehetővé teszi a virtuális hálózat/alhálózat kommunikációt előfizetések között. Ellenkező esetben minden egyes DevTest Labs-környezet lehet a saját virtuális hálózaton. 

Nincsenek [korlátok](../azure-subscription-service-limits.md) virtuális hálózatok száma előfizetésenként száma. Az alapértelmezett értéke 50, bár ez a korlát 100-ra lehet megemelni.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Mikor célszerű használni a magánhálózati IP-és nyilvános IP-cím és megosztott IP-cím?
 
Ha egy helyek közötti VPN vagy Express Route használja, fontolja meg magánhálózati IP-címek, a gépek a belső hálózaton elérhetők, és nem érhető el legyenek a nyilvános interneten keresztül.

> [!NOTE]
> Labortulajdonosok módosíthatja a alhálózat házirend, győződjön meg arról, hogy senki véletlenül létrehozza a nyilvános IP-címek a virtuális gépek számára. Az előfizetés tulajdonosa hozzon létre egy előfizetési szabályzat meggátolja, hogy a nyilvános IP-címek létrehozása folyamatban.

Megosztott nyilvános IP-címek használata esetén a virtuális gépek tesztkörnyezetben megosztása a nyilvános IP-cím. Ez a megközelítés akkor lehet hasznos, amikor szüksége van egy adott előfizetéshez tartozó nyilvános IP-címek korlátait megszegéséhez elkerülése érdekében.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Hogyan győződjön meg arról, hogy a fejlesztési és tesztelése a virtuális gépek azok nem a nyilvános interneten érhető el? Vannak-e bármely hálózati konfiguráció beállítása javasolt minták?

Igen. Két szempontot kell figyelembe venni – bejövő és kimenő forgalmat.

- **Bejövő forgalom** – Ha a virtuális gép nem rendelkezik egy nyilvános IP-címet, akkor azt nem érik el az interneten. Általánosan használt megközelítése annak érdekében, hogy egy előfizetés-szintű szabályzat van beállítva, úgy, hogy a felhasználó nem hozhat létre egy nyilvános IP-címet.
- **Kimenő forgalom** – Ha azt szeretné, hogy megakadályozza a virtuális gépek elérése közvetlenül a nyilvános interneten és a vállalati tűzfalon keresztüli forgalmat majd irányíthatja a forgalmat a helyszíni express route-n keresztül, vagy VPN-, a kényszerített útválasztást.

> [!NOTE]
> Ha rendelkezik egy proxykiszolgáló, amely blokkolja a forgalmat proxybeállítások nélkül, ne felejtse el kivételeket hozzáadni a lab artifact storage-fiókot.

Hálózati biztonsági csoportok a virtuális gépek és alhálózatok is használhat. Ezt a lépést hozzáadja egy további rétegét védelem engedélyezése / letiltása a forgalmat.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Miért nem a meglévő virtuális hálózat mentése megfelelően?
Több lehetősége, hogy a virtuális hálózat neve pontokat tartalmaz. Ha igen, próbálkozzon az időszakok eltávolítja vagy lecseréli őket a kötőjelet tartalmazhat. Ezt követően próbálja meg újra a virtuális hálózat mentéséhez.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Miért kapok egy "Szülőerőforrás nem található" hiba történt, amikor egy virtuális Gépet a PowerShell üzembe helyezhető?
Ha egy erőforrás egy másik erőforrás szülő, a szülő erőforrás léteznie kell a gyermek-erőforrás létrehozása előtt. Ha a szülő erőforrás nem létezik, megjelenik egy **ParentResourceNotFound** üzenet. Ha nem ad meg egy függőség az erőforráson, a gyermek-erőforrás előfordulhat, hogy a szülő előtt telepíteni.

Virtuális gépek gyermek erőforrásokat egy erőforráscsoportba tartozó labor csoportban. Virtuális gépek üzembe helyezése a PowerShell használatával a Resource Manager-sablonok használatával, ha az a PowerShell-parancsfájl a megadott erőforráscsoport-név a labor létrehozása az erőforráscsoport nevének kell lennie. További információkért lásd: [gyakori Azure üzembehelyezési hibák elhárítása](../azure-resource-manager/resource-manager-common-deployment-errors.md).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Hol találhatok további hibainformációk, ha egy virtuális gép telepítése sikertelen?
Virtuális gép üzembe helyezési hibák tevékenységeket tartalmazó naplók rögzíti a rendszer. Lab VM tevékenységeket tartalmazó naplók alapján is megtalálhatja **Auditnaplók** vagy **virtuális gép diagnosztikai** a a labor virtuális gép lapon erőforrás menüben (a lap jelenik meg, miután kiválasztotta a virtuális gépről a saját virtuális gépek listája).

Egyes esetekben a központi telepítési hiba lép fel, virtuális gép üzembe helyezésének megkezdése előtt. Például akkor, ha a virtuális Géppel együtt létrehozott erőforrás az előfizetésre vonatkozó korlát túllépése. Ebben az esetben a hiba részletes adatait rögzíti a rendszer a labor-szintű Tevékenységnaplók. A Tevékenységnaplók alsó részén találhatók az **Konfigurace a zásady** beállításait. Az Azure-ban naplókat tevékenység használatával kapcsolatos további információk: [megtekintése az erőforrásokon végzett műveletek naplózásához tevékenységi naplóit](../azure-resource-manager/resource-group-audit.md).




