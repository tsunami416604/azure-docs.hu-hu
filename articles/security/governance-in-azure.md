---
title: Az Azure-ban irányítás |} Microsoft Docs
description: További információk a felhőalapú számítási szolgáltatás is méretezhető felfelé és lefelé az alkalmazás vagy a vállalat igényeinek.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 9c6509f25be7fe520a427e17ca1206e10f296fea
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110762"
---
# <a name="governance-in-azure"></a>Irányítás az Azure-ban

Azure lehetővé teszi számos biztonsági beállítások és képes azokat, hogy megfeleljen a szervezet központi telepítések igényeknek is.

Azure-felhőbe irányítás hivatkozik a döntéshozó folyamatainak, a feltételek és a házirendek a tervezési részt, architektúra, beszerzési, telepítési, műveletet és felügyelete a felhőalapú informatika. Azure-felhőbe irányítás biztosít egy integrált naplózási és áttekintését, és hogy tanácsadást a szervezet az Azure platformon a felhasználási tanácsadási megközelítést. 

A terv készítése az Azure-felhőbe irányítás, meg kell tennie egy részletes nézze meg a személyeket, folyamatok és technológiák most helyen. Ezután hozhat létre keretrendszerek, amelyek megkönnyítik az informatikai következetesen Azure szolgáltatásait is használni ugyanakkor biztosítható a felhasználók a rugalmasságot biztosít a üzleti igények támogatásához.

## <a name="implementation-of-policies-processes-and-standards"></a>Házirendek, folyamatok és szabványok végrehajtása 

Felügyeleti létesített szerepkörök és felelősségek információk biztonsági házirendek és a folyamatos működésének végrehajtásának felügyeletére Azure között. Az Azure felügyeleti biztonsági és a megfelelő csapatok (beleértve a harmadik felek) belül folytonossági eljárások felügyeletéért felelős. Azt is elősegíti a biztonsági házirendek, folyamatok és szabványoknak való megfelelés.

### <a name="account-provisioning"></a>Fiók kiépítése

Fiók hierarchia meghatározása szerkezeti Azure-szolgáltatásokat a vállalaton belül, és egy fő lépés. A core irányítási szerkezete. Az ügyfelek, akik rendelkeznek a nagyvállalati szerződés (EA) tovább lehet részlegek, a fiókok és az előfizetések a környezetben.

![Fiók kiépítése](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Ha nem rendelkezik nagyvállalati szerződéssel, érdemes lehet [Azure címkék](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) a hierarchia meghatározásához az előfizetés szintjén. Azure-előfizetéssel, amely tartalmazza az összes erőforrást alapvető egysége. Az Azure, például a magok száma és erőforrások több korlátok is meghatározza. Előfizetések tartalmazhat [erőforráscsoportok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), amely erőforrásai szerepelhetnek. [Szerepköralapú hozzáférés-vezérlést (RBAC)](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview) alapelvek a ezen három szinten.

Minden vállalati nem egyezik. A nem vállalati vállalatok számára Azure címkék használatával a hierarchia hogyan Azure szerveződik rugalmasságot tesz lehetővé. Az Azure-erőforrások telepítése előtt a hierarchiában a modell, és megismerheti a hatását a számlázás, erőforrás-hozzáférés és összetettségét.

### <a name="subscription-controls"></a>Előfizetés vezérlők

Előfizetések erőforrások használatának jelentett és számlázható hogyan határozza meg. Külön számlázási és támogatási előfizetéseinek állíthat be. Egy Azure-fiók több előfizetéssel is rendelkezhet. Előfizetések segítségével határozza meg a vállalat több részlegek az Azure erőforrás-használatát.

Például egy vállalat rendelkezik-e informatikai, HR, és Marketing részlegek, és ezek között különböző projektek futnak. A vállalat létrehozhatja a számlázási minden részleg használata az Azure-erőforrások, például a virtuális gépek. A vállalat ezután vezérelheti a pénzügyi minden részleg.

Azure-előfizetések létrehozása három paramétert:

- Előfizető egyedi azonosítója

- Számlázási helye

- Rendelkezésre álló erőforrások csoportja

Egy adott paraméterek adni egy Microsoft Fiókazonosító hitelkártyaszám és a teljes csomag Azure-szolgáltatáshoz. A Microsoft érvényesíti a felhasználás-korlátok, attól függően, hogy az előfizetés-típus.

Az Azure regisztrációs hierarchiák határozza meg, hogyan szolgáltatások felépítése nagyvállalati szerződéssel belül. A nagyvállalati szerződés portál lehetővé teszi az ügyfelek felosztása nagyvállalati szerződéssel rugalmas olyan hierarchiájára, amely a szervezet igényeinek megfelelően testre szabhatók alapján társított Azure-erőforrások eléréséhez. A hierarchia mintát egyeznie kell a szervezet felügyeleti és a kapcsolódó számlázási és erőforrás-hozzáférési fiók földrajzi szerkezetének.

A három magas szintű hierarchia minták működési, üzleti egység, és földrajzi. Szervezeti egységek olyan felügyeleti szerkezetet fiók csoporthoz. Minden részleg fiókokhoz rendelhetők hozzá előfizetések, amelyek a számlázási és számos kulcsfontosságú korlátok silók létrehozása az Azure (például a virtuális gépek és tárfiókok száma).

![Előfizetés vezérlők](./media/governance-in-azure/security-governance-in-azure-fig2.png)


A nagyvállalati szerződéssel rendelkező szervezetek számára Azure-előfizetések hierarchiája egy negyedik szintű:

1. Vállalati alkalmazásbeléptetési rendszergazda

2. szervezeti egység rendszergazdája

3. fiók tulajdonosának

4. Szolgáltatás-rendszergazda

Ezt a hierarchiát szabályozza a következő:

- Számlázási kapcsolatban.

- Fiók felügyeleti.

- Hozzáférés az erőforrásokhoz az RBAC-en keresztül.

- Határok:

  - Használati és elszámolási (díjszabási kártyát ajánlat számok alapján)

  - Korlátok

  - Virtuális hálózat

- Melléklet az Azure Active Directory (Azure AD). Egy Azure AD-példányban sok a lekérdezés társítható.

- Vállalati beléptetési fiók társítását.

### <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

[Az RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) lehetővé teszi a részletes hozzáféréskezelést az Azure-erőforrások. Az RBAC segítségével biztosíthat csak olyan mértékű hozzáférést a felhasználóknak frissíteniük kell a munkája elvégzéséhez. A vállalatok a pontos engedélyeket, amelyekre szükségük ad az alkalmazottak kell összpontosítania. Túl sok engedélyek támadók fiókot fedheti fel. Túl kevés engedélyek jelenti azt, hogy az alkalmazottak nem munkavégzéséhez hatékony. 

Jogosultságot ad a Mindenki helyett korlátozás engedélyek az Ön Azure-előfizetés vagy erőforrásokhoz, engedélyezheti a csak bizonyos műveleteket. Például az RBAC segítségével lehetővé teszik egy alkalmazott egy előfizetésben található virtuális gépek kezeléséhez, miközben egy másik alkalmazott kezeli az SQL-adatbázisok ugyanahhoz az előfizetéshez.

Hozzáférés biztosításához rendelhet szerepköröket felhasználók, csoportok és alkalmazások egy adott hatókörben. A szerepkör-hozzárendelés hatóköre lehet előfizetés, egy erőforráscsoport vagy egy erőforrást. A szülő hatókörben szerepkörrel is benne a gyermekek számára engedélyezi a hozzáférést. Például egy felhasználó, aki hozzáfér az erőforráscsoporthoz kezelheti az erőforrásokat tartalmaz, például webhelyekhez, virtuális gépek és alhálózatok. Minden egyes előfizetésen belül legfeljebb 2000 szerepkör-hozzárendelések is létrehozhat.

A szerepkör az engedélyek csoportja, és Szerepalapú több beépített szerepkörrel rendelkezik. A következő beépített szerepkörök alkalmazása az összes erőforrástípus:

- **Tulajdonos** összes erőforrást, beleértve a jogot, hogy mások számára delegálása teljes hozzáféréssel rendelkezik.

- **A közreműködői** is létrehozása és kezelése az Azure-erőforrások minden típusú, de nem tud hozzáférést biztosítani, mások számára.

- **Olvasó** tekintheti meg az összes Azure-erőforrások.

A beépített szerepkörök az Azure-ban a többi adott Azure-erőforrások felügyelet lehetővé tételéhez. Például a virtuális gép közreműködői szerepkör lehetővé teszi, hogy a felhasználó számára a virtuális gépek létrehozására és kezelésére. A beépített szerepkörök és a műveletek listáját, [beépített RBAC-szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Az RBAC az Azure-erőforrások felügyeleti műveletek támogatja az Azure portál és az Azure Resource Manager API-k. A legtöbb esetben az RBAC-szintnek műveletek az Azure-erőforrások nem lehet engedélyezni. Hogyan RBAC történő kiterjesztése adatok műveleteivel kapcsolatos információkért lásd: [megérteni a szerepkör-definíciók](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-definitions).

Ha a beépített szerepkörök nem felelnek meg a kívánt hozzáférés igényeinek, akkor [hozzon létre egy egyéni biztonsági szerepkört](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). Hasonlóan a beépített szerepkörök felhasználók, csoportok és a előfizetés, az erőforráscsoport és az erőforrás-hatókör alkalmazásokat egyéni szerepkörök rendelhetők. Egyéni szerepkörök használatával hozhat létre [Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli), és a [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

### <a name="resource-management"></a>Erőforrás-kezelés

Azure biztosít két üzembe helyezési modellek: [klasszikus](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) és az Azure Resource Manager.

A klasszikus modellben minden erőforrás létezik-e egymástól függetlenül. Nincs mód kapcsolódó erőforrások csoportosítása. Akkor kell manuálisan követheti nyomon erőforrások jött létre a megoldás vagy az alkalmazáshoz, és ne felejtse el összehangolt kezelheti őket. A felügyeleti alapvető egysége, az előfizetés. Feltörhető, ami nagyszámú előfizetések létrehozása egy előfizetésen belüli erőforrások le.

A Resource Manager üzembe helyezési modellel magába foglalja a [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Az erőforráscsoport közös életciklussal rendelkező erőforrások tárolója. A megoldás összes erőforrást, vagy csak azokat az erőforrásokat, amelyet szeretne kezelése csoportként része lehet. A szervezet számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között.

A Resource Manager-alapú üzemi modell számos előnyt kínál:

- A megoldás összes szolgáltatását egy csoportként helyezheti üzembe, felügyelheti és figyelheti meg, és nem különálló erőforrásokként kell kezelnie azokat.

- Ismételten a teljes életciklus megoldás üzembe helyezése, és biztos lehet abban, hogy az erőforrások telepítése konzisztens lesz.

- Hozzáférés-vezérlés az erőforráscsoportban alkalmazhatja az összes erőforrást. Ezek a házirendek automatikusan alkalmazzák, amikor új erőforrásokat ad az erőforráscsoportot.

- Címkékkel láthatja el az erőforrásokat, így logikusan rendszerezhető az előfizetés összes erőforrása.

- A megoldás infrastruktúráját JavaScript Object Notation (JSON) formátumban definiálhatja. Ez a JSON-fájl az úgynevezett Resource Manager-sablon.

- Meghatározhatja az erőforrások, hogy azok van telepítve, a megfelelő sorrendben közti függőségeket.

![Resource Manager](./media/governance-in-azure/security-governance-in-azure-fig4.png)

A sablonokra vonatkozó javaslatokat talál a [Best practices for creating Azure Resource Manager templates](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) (Az Azure Resource Manager-sablonok létrehozásának ajánlott eljárásai) című cikkben.

Az Azure Resource Manager segítségével győződjön meg arról, hogy az erőforrások a megfelelő sorrendben jöttek létre függőségek elemzésével. Ha egy erőforrást támaszkodik egy értéket egy másik erőforrás (például a virtuális gép a lemezeket a tárfiók kellene), akkor [függőség beállítása](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies) a sablonban.

A sablonokat az infrastruktúra frissítésére is használhatja. Hozzáadhat például egy erőforrást a megoldáshoz, valamint hozzáadhat konfigurációs szabályokat a már telepített erőforrásokhoz. Ha a sablon meghatározza a létrehoznia egy erőforrást, de az erőforrás már létezik, az erőforrás-kezelő egy új eszköz létrehozása helyett frissítést hajt végre. Erőforrás-kezelő állapot frissíti a meglévő eszközt, mintha új lenne.

Erőforrás-kezelő bővítményeket biztosít olyan forgatókönyvek amikor további műveletek, például a Szoftvertelepítés nem szerepel a telepítő van szüksége.

### <a name="resource-tracking"></a>Erőforrás-követés

A szervezet felhasználói erőforrásokat ad az előfizetés, ezért rendelni az erőforrások a megfelelő részleg, az ügyfél és a környezet fontosabbá válik. Metaadatok csatolhat erőforrásoknak [címkék](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags). Címkék használata az erőforrás vagy a tulajdonos ismertetik. Címkék lehetővé teszik a nem csak összesítése és többféle módon erőforrások csoport, de is az adatokat a jóváírási céljából.

Használjon címkék összetett gyűjteménye erőforráscsoportok és erőforrásokat, és ha kell vizuálisan megjeleníteni az eszközöket, amelyek a legtöbb legcélszerűbb módon. Például jelölheti meg az erőforrásokat, amelyek hasonló szerepet töltenek a szervezet vagy, amely a részleghez tartoznak.

Címkék nélkül a szervezet felhasználói több erőforrást, amely később azonosításához és kezeléséhez nehéz lehet hozhat létre. Például előfordulhat, hogy törölni kívánja a projekthez tartozó összes erőforrást. Ha ezeket az erőforrásokat a projekt nem címkével rendelkeznek, manuálisan találnia őket. A címkézés hasznos módja a felesleges költségek csökkentéséhez az előfizetésében.

Erőforrások azonos címkével lássa ugyanabban az erőforráscsoportban lenniük, hogy nem kell. Létrehozhat saját címkerendszert, annak érdekében, hogy a szervezete összes felhasználója helyett használjon a címkéket alkalmazása. véletlenül némileg eltérő címkéket (például "részleg" helyett "osztály").

Erőforrás-házirendek lehetővé teszik a szervezet általános szabályok létrehozásához. Győződjön meg arról, hogy a megfelelő értékekkel erőforrások címkézett házirendeket hozhat létre.

A címkézett erőforrásokat az Azure Portalon is megtekintheti. A [használati jelentés](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) számára az előfizetés címke neveit és értékeit tartalmazza, ezért oszthatja kimenő költségek címkék alapján.

Címkékkel kapcsolatos további információkért lásd: [számlázási címkéket házirend kezdeményezésére](../azure-policy/scripts/billing-tags-policy-init.md).

Az alábbi korlátozások érvényesek a címkékre:

- Minden erőforrás vagy erőforráscsoport legfeljebb 15 címke kulcs/érték párok rendelkezhet. A korlátozás csak a közvetlenül az erőforráscsoportra vagy erőforrásra alkalmazott címkékre érvényes. Erőforráscsoport 15 címke kulcs/érték párok tartalmazó sok erőforrást tartalmazhat.

- A címke neve legfeljebb 512 karakter hosszúságú lehet.

- A címke értéke legfeljebb 256 karakter hosszúságú lehet.

- Az egyes erőforráscsoportokra alkalmazott címkéket nem öröklik az adott erőforráscsoportba tartozó erőforrások.

Ha több mint 15 értéket kell társítania egy erőforráshoz, használjon JSON-sztringet a címke értékéhez. A JSON-karakterláncban tartalmazhat egyetlen címke kulcs alkalmazott sok értéket.

#### <a name="tags-for-billing"></a>Címkék a számlázáshoz

Címkék lehetővé teszik az elszámolási adatok. Például ha a különböző szervezetek több virtuális gép fut, címkék használata csoport használatra költségközpont által. Címkék segítségével költségek futtatókörnyezetben, például a számlázási használata az éles környezetben futó virtuális gépek szerinti kategóriák.

Információ a címkék keresztül lekérhesse a [Azure erőforrás-használat és RateCard API-k](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) vagy a használati vesszővel tagolt (CSV) fájl. Használat a fájl letöltése a [fiókok az Azure portál](https://account.windowsazure.com/) vagy a [EA portal](https://ea.azure.com/).

További információ a számlázási adatokat programozott hozzáférést: [betekintést nyerhet a Microsoft Azure erőforrás-felhasználás](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). A REST API-műveleteket, lásd: [Azure számlázási REST API-referencia](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Ha tölthetik le a fürt megosztott kötetei szolgáltatás használatát támogató szolgáltatások számlázási, a címkéket a címkék jelennek meg a címkék oszlop.

### <a name="critical-resource-controls"></a>Kritikus fontosságú erőforrás-vezérlők

Mivel a szervezet alapvető szolgáltatások ad hozzá az előfizetés, válik fontosabb, győződjön meg arról, hogy ezek a szolgáltatások elérhetők-e üzleti megszűnésének megelőzése érdekében. [Erőforrás zárolások](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) engedélyezi, hogy hol módosítsa vagy törölje azokat az alkalmazások és a felhőalapú infrastruktúra jelentős hatással van a jó minőségű erőforrásokon végrehajtott műveletek korlátozása. Zárolások alkalmazhat egy előfizetés, az erőforráscsoportot, vagy az erőforrás. Általában zárolások alkalmazása eligazodást erőforrások, például a virtuális hálózatok, az átjárók és a storage-fiókok.

Erőforrás zárolások jelenleg két értéket támogatja: **CanNotDelete** és **ReadOnly**. **CanNotDelete** azt jelenti, hogy a felhasználói (a megfelelő jogosultságokkal) is még olvasni, vagy módosítani az erőforrást, de nem lehet törölni. **Csak olvasható** azt jelenti, hogy a jogosult felhasználók nem lehet törölni vagy módosítani az erőforrást.

Erőforrás zárolások csak alkalmazása fordul elő a felügyeleti vezérlősík, amely küldött műveletek műveletek <https://management.azure.com>. A zárolás nem korlátozza, hogyan erőforrások feladatokat a saját. Erőforrás módosítások korlátozott, de erőforrás műveletek nem korlátozottak. Például egy **ReadOnly** zárolást SQL-adatbázis nem engedi törölhessék vagy módosíthassák az adatbázist, de nem akadályozza meg a létrehozása, frissítése vagy törlése az adatbázisban.

Alkalmazása **ReadOnly** váratlan eredményekhez vezethet, mivel bizonyos műveleteket, amelyek az adatok, például olvasási műveletek további műveleteket igényelnek. Például, ha kialakít egy **ReadOnly** zárolását egy tárfiókot minden felhasználót megakadályoz a kulcsainak listázása. A művelet kulcsainak listázása egy POST kérést segítségével kezel, mert a visszaadott kulcsok írási műveletek érhetők el.

![Kritikus fontosságú erőforrás-vezérlők](./media/governance-in-azure/security-governance-in-azure-fig5.png)

Például ha helyez el egy **csak olvasható** az Azure App Service erőforrás zárolását megakadályozza, hogy a Visual Studio Server Explorer megjelenítése az erőforrás fájlok, mert adott interakció írási hozzáféréssel kell rendelkeznie.

Szerepköralapú hozzáférés-vezérlés, ellentétben a felügyeleti zárolás korlátozás alkalmazandó összes felhasználók és szerepkörök használhat. Engedélyeinek beállításával kapcsolatos további tudnivalókért lásd: [RBAC és az Azure-portálon-hozzáférés kezelése](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

A szülő hatókörben zárolást alkalmazásakor hatókörön belüli összes erőforrás öröklik az azonos zárolása. A hozzáadott később még akkor is, erőforrások örökölt a zárolást. Az öröklés a legszigorúbb zár lép érvénybe.

Hozzon létre, vagy törölje a felügyeleti zárolás, Microsoft.Authorization/ vagy Microsoft.Authorization/locks/ műveletek hozzáféréssel kell rendelkeznie. A beépített szerepkörök csak a tulajdonos és a felhasználói hozzáférés adminisztrátora kapnak ezeket a műveleteket.

### <a name="api-access-to-billing-information"></a>API-hozzáférés az számlázási adatokat

Azure számlázás API-k segítségével lekéréses használati és erőforrás adatokat be a kívánt adatok elemzésére szolgáló eszközöket. Az Azure erőforrás-használat és RateCard API-k segítségével pontosan előre jelezni, és a költségek kezelésére. Az API-kat használják, mint a egy erőforrás-szolgáltató és az Azure erőforrás-kezelő által közzétett API-kban család része.

#### <a name="resource-usage-api-preview"></a>Erőforrás-használat API (előzetes verzió)

Használja az Azure [erőforrás-használati API](https://msdn.microsoft.com/library/azure/mt219003) a becsült Azure felhasználási adatok eléréséhez. Az API-t tartalmazza:

- **Szerepalapú**: konfigurálja a hozzáférési házirendek a [Azure-portálon](https://portal.azure.com/) vagy [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview) adhatja meg, mely felhasználók vagy alkalmazások ingyenesen juthatnak az előfizetés használati adatok. Hívóknak kell használnia szabványos Azure Active Directory-jogkivonatokat a hitelesítéshez. A hívó hozzáadása a számlázási olvasó, olvasó, tulajdonosi vagy közreműködői szerepkör egy adott Azure-előfizetés a használati adatok eléréséhez.

- **Óránként vagy naponta összesített**: hívóknak adhat meg, hogy meg kívánják óránként vagy naponta lépésekben az Azure használati adatok. Az alapértelmezett érték a napi.

- **(Erőforrás-címkét tartalmaz) példány metaadatok**: példányszintű részletek, például a teljesen minősített erőforrás URI beolvasása (/subscriptions/ {előfizetés-azonosító} /...), erőforrás csoport adatai, és az erőforráscímkék. A metaadatok segítségével deterministically és szoftveresen használati lefoglalni a címkék, például a kereszt-díjszabási használatából adódó alapján.

- **Erőforrás-metaadatok**: erőforrás részletek, például a mérési neve mérő kategória, mérő alkategóriát, egység vagy régió biztosítják a hívó szeretné jobban megismerni mi felhasznált. Erőforrás-metaadatok terminológia igazítása az Azure-portálon keresztül is dolgozunk Azure használati CSV, EA számlázási CSV, és más nyilvánosan elérhető lép segítséget adatok összefüggéseket keresztül lép.

- **Minden használati kínálnak típusok**: használati adatok érhető el az összes kínálnak, használatalapú fizetés, MSDN, pénzügyi kötelezettségvállalást, pénzügyi kreditet és EA is beleértve.

#### <a name="resource-ratecard-api"></a>Erőforrás RateCard API

Az Azure erőforrás RateCard API segítségével listájának elérhető Azure-erőforrások és az egyes becsült díjszabási információkat. Az API-t tartalmazza:

- **Az RBAC**: a hozzáférési házirendek konfigurálása az Azure portálon, vagy adja meg, hogy mely felhasználók vagy alkalmazások ingyenesen juthatnak az RateCard adatokat az Azure PowerShell-parancsmagok használatával. Hívóknak kell használnia szabványos Azure Active Directory-jogkivonatokat a hitelesítéshez. A hívó hozzáadása a olvasó, a tulajdonos vagy közreműködő szerepkört egy adott Azure-előfizetés a használati adatok eléréséhez.

- **Használatalapú fizetés, az MSDN, a pénzügyi kötelezettségvállalást a és a pénzügyi kreditet ajánlatok (de nem EA) támogatása**: Ez az API felület Azure ajánlat szintű arány információkat nyújt. Ez az API felület védőfalkezelőbe meg kell felelnie az ajánlat információkat az erőforrás részletek és sebességét. EA jelenleg nem támogatott, mert EA ajánlatok testreszabott beléptetési mértékek. 

#### <a name="scenarios"></a>Forgatókönyvek

A használati és RateCard API-k kombinációja lehetővé teszi, ezek a forgatókönyvek:

- **Azure megérteni a hónapban töltenek**: a használati együttes használata, és a hónap során töltött RateCard API-k, a felhő jobb betekintést eléréséhez. Elemezheti óránként és napi használat és kell fizetni becslése.

- **Riasztások beállítása**: a használati és RateCard API-k segítségével becsült felhő használat és díjak és erőforrás-alapú vagy pénzügyi-alapú értesítések beállítása.

- **Számlázási előrejelzése**: Get a becsült felhasználás és a felhő kiadásokat, és gépi tanulási algoritmusok előre jelezni, mi a számlázási lenne az elszámolási időszak végén alkalmazni.

- **Hajtsa végre a előtti felhasználási elemzés**: a RateCard API használatával megjósolható, hogy mekkora a számlázási okozna a várható használati a munkaterhelések az Azure-bA helyezi át. Ha meglévő alkalmazások más felhők vagy magánfelhőkben, az az Azure-ral használatának is leképezheti Azure jobb becslése beolvasandó díjszabás televíziózással töltenek. Ez a becslés lehetővé teszi a forgáspont kínálnak, és hasonlítsa össze a különböző típusú túl használatalapú fizetés, például a pénzügyi kötelezettségvállalást a és a pénzügyi kreditet között.

- **Hajtsa végre egy elemzési**: meg lehet határozzák meg, hogy egy másik régióban, vagy egy másik Azure-erőforrás-konfigurációban alkalmazásokat és szolgáltatásokat futtathatnak költséghatékonyabb. Azure-erőforrás költségek az Azure-régió használata esetén előfordulhat, hogy tér el a rendszer. Azt is meghatározhatja, hogy ha egy másik Azure-ajánlat típusa a nagyobb mértékben nyújt egy Azure-erőforrás.

### <a name="networking-controls"></a>Hálózati vezérlő

Erőforrásokhoz való hozzáférést lehet (a vállalati hálózatból) belső vagy külső (internetes) keresztül. Is egyszerűen a szervezet felhasználói erőforrások véletlenül be a megfelelő hellyel, és potenciálisan nyissa meg ezeket a rosszindulatú hozzáférésektől. Csakúgy, mint a helyszíni eszközök, a vállalatok megfelelő ellenőrzéssel győződjön meg arról, hogy Azure-felhasználók a megfelelő döntéseket kell hozzáadnia.

![Hálózati vezérlő](./media/governance-in-azure/security-governance-in-azure-fig6.png)

A következő alapvető erőforrások előfizetés irányításhoz, adja meg a hozzáférés alapvető irányítását.

#### <a name="network-connectivity"></a>Hálózati kapcsolat

[Virtuális hálózatok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) alhálózatok tároló objektumok. Bár nem feltétlenül szükséges, egy virtuális hálózatot gyakran használják alkalmazások belső vállalati erőforrásokhoz való csatlakozás. Az Azure Virtual Network szolgáltatás lehetővé teszi, hogy biztonságosan kapcsolódjanak a Azure-erőforrások egymástól a virtuális hálózatok.

Egy virtuális hálózat a felhőben saját hálózati ábrázolása. Egy virtuális hálózathoz hozzárendelve az előfizetés Azure-felhő logikai elkülönítése. Virtuális hálózatok a helyszíni hálózathoz is kapcsolódhatnak.

Az alábbiakban lehetőségeket biztosít az Azure virtuális hálózatok:

- **Elkülönítési**: virtuális hálózatok el különítve egymástól. Létrehozhat külön virtuális hálózatokat fejlesztési, tesztelési és éles, amelyek ugyanazon a CIDR címblokkokat használják. Ezzel szemben több virtuális hálózat, amely különböző a CIDR címblokkokat használja, és összekapcsolhatja az hálózatok is létrehozhat. Több virtuális hálózat is szegmentálhatja. Azure belső névfeloldást biztosít egy virtuális hálózathoz csatlakozó virtuális gépek és az Azure Cloud Services szerepkör-példányok. Konfigurálhatja az Azure belső névfeloldást használata helyett a saját DNS-kiszolgálók használandó virtuális hálózat.

- **Internetkapcsolat**: minden Azure virtuális gépek és Felhőszolgáltatások szerepkörpéldányokat egy virtuális hálózathoz csatlakozó rendelkezik internetkapcsolattal, alapértelmezés szerint. Bejövő hozzáférés adható konkrét erőforrásokhoz, engedélyezheti igény szerint is.

- **Az Azure erőforrás-kapcsolat**: kapcsolódás Azure-erőforrások, például a Cloud Services és a virtuális gépek, az azonos virtuális hálózatban. Az erőforrások kapcsolódhatnak egymáshoz magánhálózati IP-címek, még akkor is, ha külön alhálózatokon fontosságúak. Azure biztosít alapértelmezett között alhálózatokat, virtuális hálózatok és a helyszíni hálózatokban, így nem kell konfigurálnia és felügyelnie az útvonalakat.

- **Virtuális hálózati kapcsolat**: virtuális hálózatok kapcsolódhatnak egymáshoz. Minden virtuális hálózathoz kapcsolódó erőforrások ezután kommunikálhat bármely más virtuális hálózaton erőforrás.

- **A helyi kapcsolat**: kapcsolódás virtuális hálózatok a helyszíni hálózatokban magánhálózati kapcsolatok a hálózati és az Azure között, vagy a pont-pont virtuális magánhálózati (VPN) kapcsolaton keresztül az interneten keresztül.

- **Forgalomszűrést végez**: szűrheti a hálózati forgalom (bejövő és kimenő) a virtuális gépek és Felhőszolgáltatások forrás IP-cím és port, cél IP-cím és port és protokoll.

- **Útválasztás**: opcionálisan felülbírálhatja az alapértelmezett Azure útválasztás konfigurálása a saját útvonalak, vagy a hálózati átjáró BGP-útvonalakat használatával.

#### <a name="network-access-controls"></a>Hálózati hozzáférés-vezérlést

[Hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG-k), például egy tűzfal, és adjon meg szabályokat, a hogyan erőforrás "működik" a hálózaton keresztül. Ezek biztosítanak szabályozhatják, hogyan egy alhálózat (vagy a virtuális gép) is csatlakozni az internethez vagy más alhálózatok ugyanabban a virtuális hálózatban.

Hálózati biztonsági csoport felsorolja azokat a szabályokat, amelyek az Azure virtuális hálózatokhoz kapcsolódó erőforrások hálózati adatforgalom engedélyezéséhez vagy letiltásához. Az NSG-k társítható alhálózatok, az egyes virtuális gépek (klasszikus) vagy az egyes hálózati adapterek (NIC) csatolva a virtuális gépek (Resource Manager).

Amikor egy NSG-t hozzárendelnek egy alhálózathoz, a szabályok vonatkoznak a alhálózathoz csatlakoztatott összes erőforrást. További korlátozhatja forgalom NGS társítása a virtuális gép vagy a hálózati adapterhez.

## <a name="security-and-compliance-with-organizational-standards"></a>Biztonság és a vállalati szabványoknak való megfelelés

Minden üzleti eltérő igényeknek és a fog élvezzék által nyújtott megoldások különböző előnyeit. Továbbra is ügyfeleknek mindenfajta az ugyanazon alapvető kétségei vannak a felhőre történő. Mi az ügyfelek szeretné, hogy a szolgáltatók van:

- **Az adatok biztonságos**: informatikai vezetők megerősíti, hogy a felhő megnövekedett adat biztonsági és felügyeleti szabályozást biztosít. Azonban továbbra is érintett, hogy a felhőben történő hagyja őket sebezhetőbb a támadók, mint az aktuális belső fejlesztésű megoldások fontosságúak.

- **Tartsa titokban adataink**: felhőszolgáltatások egyedi adatvédelmi kihívást ablakába. Mivel a vállalatok keresse meg a felhőbe, az infrastruktúra használati díjait és javíthatják a nagyon hatékony eszközök, azokat is aggódni vezérelhető, hogy az adatok tárolására, ki fér hozzá, és felhasználásáról lekérdezi.

- **Vezérlő biztosítják**: még akkor is, mivel a vállalatok központi telepítése további innovatív megoldások a felhő előnyeit, fontosságúak érintett elveszíti az adatokat vezérlő. A legutóbbi közzétételeket, állami intézményekhez el az ügyféladatokat, mind a jogi, és a aktusokról azt jelenti, ellenőrizze a néhány igazgatók óvatos az adatok tárolása a felhőben.

- **Átláthatóságának elősegítése**: üzleti döntéshozók megérteni a biztonsági, adatvédelmi és vezérlő fontosságát. De is kívánják-egymástól függetlenül győződjön meg arról, hogyan azok adatot tárol, és a biztonságos hozzáférés lehetőséget.

- **Megfelelőség fenntartásában**: vállalatok bontsa ki a felhőalapú technológiái által nyújtott használatát, az összetettségét és szabványok és köre tovább fejleszteni. A vállalatok kell, hogy teljesülnek-e a megfelelőségi követelményeket.

## <a name="security-configuration-for-monitoring-logging-and-auditing"></a>Biztonsági beállítások a figyelés, és naplózás

Az Azure-előfizetők több eszközről kezelhetik felhőkörnyezeteiket. Ezek az eszközök közé tartozik a felügyeleti munkaállomásokról, fejlesztői PC-kről és még jogosult végfelhasználói eszközökről is, amelyek feladatspecifikus engedélyekkel rendelkeznek. 

Bizonyos esetekben a felügyeleti funkciók webalapú konzolok, például az Azure-portálon keresztül történik. Más esetekben előfordulhat, közvetlen kapcsolat az Azure-bA helyszíni rendszerekből VPN, Terminálszolgáltatások, ügyfél-alkalmazásprotokollokon, vagy (szoftveresen) az Azure Service API (SMAPI). Emellett ügyfél-végpontok lehetnek vagy tartományhoz csatlakoztatva vagy pedig elkülönítettek és felügyelet nélküliek, például a táblagépük vagy okostelefonjuk használatával.

A variancia jelentős kockázat adhat hozzá egy felhő üzembe helyezése. Kezelését, nyomon követheti és felügyeleti műveletek naplózhatók nehéz lehet. A variancia nem szabályozott hozzáférést a felhőszolgáltatások kezelésére használt ügyfél-végpontok biztonsági fenyegetésekkel is vezethet. Az általános vagy személyes munkaállomások fejlesztésre és infrastruktúra-kezelésre való használata olyan kiszámíthatatlan fenyegetési vektoroknak enged utat, mint például a webböngészés (pl. alapesetben megbízható weboldalak megfertőződése, ún. watering hole attack) vagy az e-mail (pl. pszichológiai manipuláció és adathalászat).

A megfigyelés és a naplózás biztosítják követésének és értelmezésének felügyeleti tevékenységek alapul. Az összes művelet teljes részletességű naplózása nem mindig lehet valósítható meg a keletkező adatmennyiség miatt. De a felügyeleti házirendek hatékonyságának naplózása az ajánlott eljárás.

Az Azure biztonsági irányítás az Azure Active Directory tartományi szolgáltatások (AD DS) csoportházirend-objektumok segítségével a rendszergazdák Windows felületek, például a fájlmegosztás szabályozhatja. Vegye fel felügyeleti munkaállomások figyelését, és naplózás folyamatokat. Kövessen nyomon minden rendszergazdai és fejlesztői hozzáférést és tevékenységet.

### <a name="azure-security-center"></a>Azure Security Center

[Az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) központi előfizetések erőforrások biztonsági állapotát jeleníti meg. Az ajánlásokat, amelyekkel megakadályozható a fertőzött erőforrásokat. Engedélyezheti, hogy a részletesebb házirendek – például házirendek alkalmazása az adott erőforrás-csoportok, amelyek lehetővé teszik a vállalat személyessé tétele érdekében a kockázat, azok még címzési azok állapotát.

![Azure Security Center](./media/governance-in-azure/security-governance-in-azure-fig7.png)

A Security Center itt integrált biztonsági monitorozást és kezelése az Azure-előfizetések között, megkönnyíti a nehezen észlelhető fenyegetések azonosítását szabályzatkezelést, és számos biztonsági megoldással együttműködik. Miután engedélyezte a [biztonsági házirendek](https://docs.microsoft.com/azure/security-center/security-center-policies) az előfizetéshez tartozó erőforrásokra, a Security Center a potenciális biztonsági réseket azonosításához az erőforrások biztonsági elemzi. A hálózati konfigurációval kapcsolatos információk azonnal elérhetők.

Az Azure Security Center ajánlott eljárás elemzési és biztonsági házirendek kezelése Azure-előfizetés belül az összes erőforrás kombinációját jelenti. Lehetővé teszi a biztonsági csoportok és a kockázati tisztviselő megakadályozása, észlelését és válaszadás a biztonsági fenyegetések automatikusan gyűjti és elemzi az Azure-erőforrások, a hálózati és a partneri megoldások, például kártevőirtó-programok és tűzfalak biztonsági adatait.

Ezenkívül az Azure Security Center bővített analitikát alkalmaz, beleértve a gépi tanulási és viselkedéssel összefüggő elemzésekkel. Globális fenyegetésfelderítési adataival, a Microsoft-termékek és szolgáltatások, a Microsoft Digital Crimes Unit (DCU), a Microsoft biztonsági válasz Center (MSRC) használ, és a külső hírcsatornák. Alkalmazhat [biztonsági irányítás](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) tágabb értelemben véve az előfizetés szintjén. Vagy szűkítheti az egyes konkrét követelmények, és alkalmazni azokat az egyes erőforrások házirenddefiníción keresztül.

Végül az Azure Security Center erőforrás biztonsági állapota ezek a házirendek alapján elemzi, és ezt az információt használja osztályon irányítópultok és a próbálkozások esemény, például a kártevő-észlelési vagy kártékony IP-kapcsolatot vonatkozó riasztások elküldésére. Javaslatok alkalmazásával kapcsolatos további információkért lásd: [végrehajtási biztonsági javaslatok az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Az Azure Security Center figyeli a következő Azure-erőforrások:

- Virtuális gépek (VM) (beleértve a felhőszolgáltatások)

- Virtuális hálózatok

- SQL Database-adatbázisok

- Partnermegoldások integrálva van az Azure-előfizetéshez, például a virtuális gépeken és a webes alkalmazás tűzfal a [App Service Environment-környezet](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme)

A Security Center első megnyitásakor adatgyűjtés engedélyezett az előfizetésében szereplő összes virtuális gépet. Azt javasoljuk, hogy mindig engedélyezett adatgyűjtés azonban úgy is [tiltsa le](https://docs.microsoft.com/azure/security-center/security-center-faq) a Security Center-házirendben.

### <a name="log-analytics"></a>Log Analytics

Az Azure Naplóelemzés szoftverek fejlesztési és a szolgáltatás csapat információ-biztonság és [cégirányítási program](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) támogatja az üzleti követelmények. Igazodjon a törvény és szabályozás részben ismertetett módon [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) és [Microsoft Megbízhatósági központ megfelelőségi](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Hogyan hoz létre a Naplóelemzési biztonsági követelmények biztonsági vezérlők azonosítja, és kezeli, és a figyelők kockázatok ott is ismerteti. Évente a csapata áttekinti a házirendek, szabványok, eljárások és útmutatást.

Minden Naplóelemzési fejlesztési csoport egy tagja megkapja a formális alkalmazás biztonsági képzés. A verziókezelő rendszer megvédi a fejlesztési minden szoftver projekt.

A Microsoft hogyan felügyeli, és értékeli az összes szolgáltatás a Microsoft biztonsági és megfelelőségi csoport rendelkezik. Biztonsági informatikusok jött létre a csoport, és nem fontosságúak Naplóelemzési fejlesztése mérnöki osztályai társítva. A biztonsági tisztviselő saját felügyeleti lánc rendelkezik. Akkor végezze el a termékek és szolgáltatások segítségével, győződjön meg arról, biztonsági és megfelelőségi független értékeléseket.

Naplóelemzési legfontosabb funkcióit biztosítja az Azure-ban futó szolgáltatások készlete. Mindegyik szolgáltatás egy adott felügyeleti funkciót biztosít, és a szolgáltatások kombinálásával különféle felügyeleti forgatókönyvek valósíthatók meg.

![Azure-szolgáltatások kezelése](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

A felügyeleti szolgáltatások arra tervezték, a felhőben. Azok még teljesen Azure szolgáltatásban üzemeltetett, így azok nem olyan tartalmaznak, telepítése és kezelése a helyszíni erőforrások. Konfigurációs minimális, és működik, és lehet percek.

Az Adatközpont bármilyen Windows vagy Linux rendszerű számítógépre helyeznek az ügynök, és adatokat küld a Naplóelemzési. Itt elemzése felhőalapú vagy helyszíni szolgáltatásokat gyűjtött összes egyéb adatokkal együtt. Azure biztonsági mentési és az Azure Site Recovery segítségével a biztonsági mentési és magas rendelkezésre állás érdekében a helyszíni erőforrások felhő előnyeit.

![Az Azure irányítópult szolgáltatások](./media/governance-in-azure/security-governance-in-azure-fig8.png)

A felhőben Runbookok általában nem tud hozzáférni a helyszíni erőforrások, de az ügynök telepíthető egy vagy több, az adatközpontban található runbookok futtató számítógépeken. Amikor elindít egy runbookot, akkor adja meg, hogy úgy, hogy a felhőben vagy a helyi feldolgozók futtassa le.

A Microsoft és a partnerek, hogy az Azure-előfizetéshez Naplóelemzési befektetéseit értékének növelése érdekében hozzáadhat különböző megoldások érhetők el. Például az Azure kínál [megoldások](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions)--előre csomagolt beállítása az logika, amely egy vagy több felügyeleti szolgáltatások használatával egy felügyeleti forgatókönyv megvalósításához.

![Az Azure-ban megoldások gyűjteménye](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Partnerként létrehozhat saját megoldások támogatják az alkalmazások és szolgáltatások és azok a felhasználók számára az Azure piactér vagy gyorsindítási sablonok keresztül.

## <a name="performance-alerting-and-monitoring"></a>Teljesítményriasztások és figyelése

### <a name="alerting"></a>Riasztások kezelése

A riasztások jelezhetik az Azure-erőforrás figyelési metrikákat, események vagy naplók metódus. Ezek értesítést küldenek, ha a megadott feltétel teljesül.

Riasztások szolgáltatásban, beleértve a érhetők el:

- **Az Azure Application Insights**: lehetővé teszi a webalkalmazás-teszt- és metrika riasztásokat. További információkért lásd: [riasztások beállítása a Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) és [figyelése rendelkezésre állásának és reakcióidőt, a webhely](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- **Naplófájl Analytics**: lehetővé teszi, hogy a tevékenység és a diagnosztikai naplók szolgáltatáshoz útválasztását. Lehetővé teszi a metrika, a napló és a más riasztástípusok ettől. További információkért lásd: [Naplóelemzési riasztások](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- **Az Azure figyelő**: lehetővé teszi, hogy a riasztások metrika értékek és a tevékenység alkalmazásnapló-események alapján. Használhatja a [Azure figyelő REST API](https://msdn.microsoft.com/library/dn931943.aspx) riasztások kezelése. További információkért lásd: [riasztások létrehozása az Azure-portálon, a PowerShell vagy a parancssori felület használatával](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Figyelés

A cloud App teljesítményproblémákat hatással lehet az üzleti. A több összekapcsolt összetevőkkel és gyakori kiadásokban degradations bármikor fordulhat elő. És ha az alkalmazást, a felhasználók általában felderítése a tesztjei során nem talált problémákat. Meg kell a problémákról értesülnek azonnal, eszközöket, és diagnosztizálása és elhárítása.

Nincs számos eszközt az Azure-alkalmazások és szolgáltatások figyelését. A funkciók némelyike átfedésben vannak. Ez az az oka részben a fellazítja a fejlesztési és a művelet az alkalmazások között.

Az alábbiakban a fő eszközöket:

- **Az Azure figyelő** alapvető eszköz a Azure-on futó szolgáltatások figyelése. Biztosít egy szolgáltatás és a környezet átviteli infrastruktúra szintű adatait. Ha az Ön által felügyelt Azure-ban és eldöntötte, hogy a méretezési felfelé vagy lefelé erőforrások az alkalmazásokat, Azure figyelő segítségével indítsa el.

- **Az Application Insights** fejlesztési és egy üzemi felügyeleti megoldás is használható. Működését tekintve a csomag telepítése az alkalmazásban, így biztosít jelenít meg több belső nézetét. Az adatok közé tartoznak a függőségi, kivétel nyomkövetési adatok, a pillanatképeket és végrehajtási profilok hibakeresés válaszidejét. Eszközöket biztosít minden a telemetriai adat segítséget az alkalmazás hibakeresését és segít megérteni a felhasználók tevékenységeit vele elemzése. Beállíthatja, hogy egy csúcs az igények válaszidők van valami miatt egy alkalmazást vagy egy külső resourcing probléma. Használatakor a Visual Studio és az alkalmazás hibás, akkor rögtön a probléma kódsort az, hogy kijavíthassuk.

- **Naplófájl Analytics** rendelkező kell állítani a teljesítményt és a karbantartási terv éles környezetben futó alkalmazások van. Azt gyűjti, és számos más forrásból, 10 – 15 perces késleltetéssel összesíti. Körű informatikai felügyeleti megoldást nyújt az Azure-ba, a helyszíni és a külső felhőalapú infrastruktúra (például az Amazon Web Services). Eszközök adatelemzéshez forrás lehetővé teszi összetett lekérdezések összes naplófájlt, és a megadott feltételek proaktív riasztást küldhet biztosít. Még a saját központi tárházban egyéni adatainak gyűjtéséről és majd lekérdezése, és adott adatok megjelenítése.

- **A System Center Operations Manager** van, felügyelheti és figyelheti a nagy felhőalapú telepítések. Valószínűleg már tisztában van, a helyi Windows Server felügyeleti eszközként és Hyper-V alapú felhők, de is integrálása és az Azure-alkalmazások kezelése. Többek között az Application Insights telepíthet a meglévő élő alkalmazásokra. Ha egy alkalmazás leáll, az Operations Manager megtudhatja, másodpercben.


## <a name="next-steps"></a>További lépések

- [Az Azure Resource Manager-sablonok létrehozásának ajánlott eljárásai](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)

- [Azure-előfizetés irányítás implementációi](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-examples)

- [A Microsoft Azure Government](https://docs.microsoft.com/azure/azure-government/)
