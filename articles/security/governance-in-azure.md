---
title: Irányítás az Azure-ban |} A Microsoft Docs
description: Ismerje meg a felhőalapú számítástechnikai szolgáltatások méretezhetők felfelé és lefelé az alkalmazás vagy a vállalat igényeinek.
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
ms.openlocfilehash: 579e900ee6616af8fd197e501364acd8e18d3e37
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970523"
---
# <a name="governance-in-azure"></a>Irányítás az Azure-ban

Az Azure kínál számos biztonsági beállítások és irányítása őket, hogy a szervezet üzemelő példányok egyedi követelményeinek teljesítheti.

Az Azure cloud cégirányítási hivatkozik a döntéshozatal elősegítéséhez, a feltételek és a szabályzatok a tervezésének, architektúra, beszerzési, üzembe helyezés, műveletet és kezelését a felhő-számítástechnika. Az Azure cloud cégirányítási beépített naplózási és áttekintéséhez és az Azure platform a felhasználási szervezetek beküldte tanácsadási megközelítést nyújt. 

Hozzon létre egy Azure-felhő cégirányítási tervet, hogy Önnek kell tennie szűrőtípusok a személyeket, folyamatokat és technológiák most helyen. Ezután hozhat létre, amelyek megkönnyítik a keretrendszerek következetesen támogatásához az üzleti igényeknek megfelelően a rugalmasságot biztosítanak a felhasználók számára, miközben az a funkciók az Azure informatikai.

## <a name="implementation-of-policies-processes-and-standards"></a>Végrehajtási házirendek, eljárások és szabványok 

Felügyeleti szerepkörök és felelősségek való végrehajtásának információk biztonsági házirendeket és folyamatos működésének felügyelete az Azure-ban hozott létre. Az Azure felügyeleti biztonsági és a megfelelő csapatok (beleértve a harmadik felek) belül folytonossági eljárások felügyeletéért felelős. Emellett elősegíti a biztonsági házirendek, folyamatok és szabványoknak való megfelelést.

### <a name="account-provisioning"></a>Fiók kiépítése

Fiók hierarchia meghatározása az egyik legfontosabb lépés használhatja, és az Azure-szolgáltatások a vállalaton belül struktúra. Fontos a mag irányítási szerkezete. Nagyvállalati Szerződés (EA) rendelkező ügyfelek feloszthatja a részlegek, fiókok és előfizetések környezetét.

![Fiók kiépítése](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Ha nem rendelkezik nagyvállalati szerződéssel, érdemes [Azure címkék](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) az előfizetés szintjén a hierarchia meghatározásához. Azure-előfizetése a alapszintű egység, amely tartalmazza az összes erőforrást. Azure-magok és az erőforrások száma például számos korlátok is meghatározza. Előfizetések tartalmazhat [erőforráscsoportok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), amely erőforrásokat is tartalmazhat. [Szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) elvek vonatkoznak ezek három szinten.

Minden vállalati eltér. Nem vállalati vállalatok esetében a hierarchiában, az Azure címkék használatával lehetővé teszi a rugalmasság az Azure hogyan vannak rendszerezve. Az Azure-erőforrások üzembe helyezése, előtt modell egy hierarchia, és a számlázás, az erőforrás-hozzáférés és az összetettség gyakorolt hatás megértéséhez.

### <a name="subscription-controls"></a>Előfizetés vezérlők

Előfizetések meghatározhatja az erőforrások használati jelentett és a számlázás. Beállíthat külön számlázási és fizetési előfizetések. Egy Azure-fiók több előfizetéssel is rendelkezhet. Előfizetések segítségével határozza meg a vállalat több szervezeti egységek az Azure-erőforrások használatára.

Ha például a vállalat rendelkezik informatikai, HR, és a Marketing részlegek, és ezeket a szervezeti futnak a különböző projektek. A vállalat a számlázás az Azure-erőforrások, például a virtuális gépek minden részleg használati is alapulhatnak. A vállalat ezután vezérelheti az egyes szervezeti egységek pénzügyeit.

Az Azure-előfizetések létrehozására a három paramétert:

- Egyedi feliratkozói azonosítója

- A számlázás helye

- Rendelkezésre álló erőforrások készlete

Egyéni ezeket a paramétereket egy Microsoft-fiók azonosító, hitelkártyaszám és a teljes körű Azure-szolgáltatások közé tartozik. A Microsoft megköveteli a használati korlátok, az előfizetés típusától függően.

Azure-regisztrációjához hierarchiák határozza meg, hogyan szolgáltatások struktúrája nagyvállalati szerződés belül. A nagyvállalati szerződés portál segítségével az ügyfelek való felosztásához, amelyek a szervezet igényeinek megfelelően testre szabható rugalmas hierarchiák alapján nagyvállalati szerződés társított Azure-erőforrásokhoz való hozzáférést. A hierarchia minta egyeznie kell a szervezet felügyeleti és földrajzi szerkezetét, hogy figyelembe vegye a társított számlázás- és erőforrás-hozzáférés.

A három magas szintű hierarchia minták működési, üzleti egység, és a földrajzi. Szervezeti fiók csoporthoz egy felügyeleti szerkezet. Minden részleg fiókokhoz rendelhetők az előfizetéseket, ami a számlázási és számos kulcsfontosságú korlátok silók létrehozása az Azure-ban (például a virtuális gépek és a storage-fiókok száma).

![Előfizetés vezérlők](./media/governance-in-azure/security-governance-in-azure-fig2.png)


Az Azure-előfizetések nagyvállalati szerződéssel rendelkező szervezeteknek, hajtsa végre a negyedik szintű hierarchia:

1. Vállalati regisztrálási rendszergazdaként

2. Szervezeti egység rendszergazdája

3. Fióktulajdonos

4. Szolgáltatás-rendszergazda

Ezt a hierarchiát szabályozza a következőket:

- A számlázás kapcsolat.

- A fiókadminisztrátori.

- RBAC-n keresztül erőforrásokhoz való hozzáférés.

- Határok:

  - Használat és elszámolás (díjszabási kártyát ajánlat számok alapján)

  - Korlátok

  - Virtuális hálózat

- Melléklet az Azure Active Directory (Azure AD). Sok előfizetést egy Azure AD-példányt társítható.

- Egy vállalati regisztrációs fiókhoz társítva.

### <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

[Az RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) részletes hozzáférés az Azure-erőforrások kezelését teszi lehetővé. Az RBAC használatával biztosíthat csak olyan mértékű hozzáférést a felhasználóknak frissíteniük kell a munkája elvégzéséhez. Vállalatok kell összpontosítania a jogosultságot ad az alkalmazottak a pontos szükséges engedélyeket. Túl sok engedély teszik elérhetővé a támadók számára egy fiókot. Túl kevés engedélyek jelenti azt, hogy az alkalmazottak nem munkavégzéséhez hatékony. 

Helyett, így mindenki korlátozás nélküli engedélyeket az Azure-előfizetés vagy erőforrásokhoz, engedélyezheti csak bizonyos műveleteket. Ha például az RBAC használatával lehetővé teszik egy alkalmazott egy előfizetésben található virtuális gépek kezelése, miközben egy másik alkalmazott kezeli az SQL-adatbázisok ugyanabban az előfizetésben.

Hozzáférést biztosítani, akkor szerepköröket rendelhet felhasználókat, csoportokat vagy alkalmazásokat egy bizonyos hatókörben. Szerepkör-hozzárendelés hatóköre egy előfizetés, erőforráscsoport vagy egyetlen erőforrás lehet. Egy szülő hatókörben hozzárendelt szerepkör is hozzáférést biztosít az ebben lévő gyermekei. Például egy erőforráscsoporthoz hozzáféréssel rendelkező felhasználó az összes erőforrást, amely tartalmaz, mint például a websites, a virtuális gépek és alhálózatok kezelheti. Minden egyes-előfizetésben legfeljebb 2000 szerepkör-hozzárendeléseket is létrehozhat.

A szerepkör az engedélyek csoportja, és számos beépített szerepkörrel rendelkezik az RBAC. A következő beépített szerepkörök minden erőforrástípus vonatkoznak:

- **Tulajdonos** összes erőforrás, beleértve a másoknak való hozzáférés delegálására jogosultak is teljes hozzáféréssel rendelkezik.

- **Közreműködői** is létrehozása és kezelése minden típusú Azure-erőforrások, de nem adhat hozzáférést másoknak.

- **Olvasó** tekintheti meg az összes Azure-erőforrások.

A beépített szerepkörök az Azure-ban a rest lehetővé teszik az adott Azure-erőforrások kezelése. A virtuális gépek Közreműködője szerepkör például lehetővé teszi, hogy a felhasználó számára a virtuális gépek létrehozása és kezelése. A beépített szerepkörök és a műveletek listáját lásd: [beépített RBAC-szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Az RBAC az Azure Portalon és az Azure Resource Manager API-k az Azure-erőforrások felügyeleti műveleteket támogatja. A legtöbb esetben az RBAC-szintnek műveleteket az Azure-erőforrások nem lehet engedélyezni. További információ az RBAC hogyan történő kiterjesztése az műveletekhez: [megismerheti a szerepkör-definíciók](https://docs.microsoft.com/azure/role-based-access-control/role-definitions).

Ha a beépített szerepkörök nem felelnek meg a megadott típusú hozzáférést igényeinek, [hozzon létre egy egyéni szerepkört](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). Csakúgy, mint a beépített szerepkörök egyéni szerepkörök, felhasználók, csoportok és az előfizetés, erőforráscsoport és erőforrás-hatókör-alkalmazásokkal is hozzárendelhető. Létrehozhat egyéni szerepköröket is az [Azure PowerShell-lel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [Azure CLI-vel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli), és a [REST API-val](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

### <a name="resource-management"></a>Erőforrás-kezelés

Az Azure két üzembe helyezési modellt kínál: [klasszikus](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) és Azure Resource Manager.

A klasszikus modellben az egyes erőforrások egymástól függetlenül létezik. Nincs lehetőség a kapcsolódó erőforrások csoportosítása. Rendelkezik manuálisan követheti nyomon erőforrásokat a megoldás vagy az alkalmazások alkotják, és összehangolt kezelheti őket a vágólapra. Az alapvető egysége a előfizetésre szükség. Meglehetősen nehéz, ami nagyszámú előfizetések létrehozása egy előfizetésen belüli erőforrások felosztania.

A Resource Manager üzemi modell fogalma magában foglalja egy [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Az erőforráscsoport közös életciklussal rendelkező erőforrások tárolója. A megoldás összes erőforrását, vagy csak azokat az erőforrásokat egy csoportként kezelheti kívánt is alkalmas. A szervezet számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között.

A Resource Manager-alapú üzemi modell számos előnyt kínál:

- A megoldás összes szolgáltatását egy csoportként helyezheti üzembe, felügyelheti és figyelheti meg, és nem különálló erőforrásokként kell kezelnie azokat.

- Ismételt üzembe helyezése a megoldás teljes életciklusa során, és biztos lehet benne, hogy az erőforrások telepítése konzisztens lesz.

- Hozzáférés-vezérlés az erőforráscsoportban lévő összes erőforrást alkalmazhatja. Ezek a házirendek a rendszer automatikusan alkalmazza, amikor új erőforrásokat ad az erőforráscsoporthoz.

- Címkékkel láthatja el az erőforrásokat, így logikusan rendszerezhető az előfizetés összes erőforrása.

- A megoldás infrastruktúráját JavaScript Object Notation (JSON) formátumban definiálhatja. Ez a JSON-fájl az úgynevezett Resource Manager-sablon.

- Megadhatja, hogy a helyes sorrendben üzembe helyezésük erőforrások közti függőségeket.

![Resource Manager](./media/governance-in-azure/security-governance-in-azure-fig4.png)

A sablonokra vonatkozó javaslatokat talál a [Best practices for creating Azure Resource Manager templates](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) (Az Azure Resource Manager-sablonok létrehozásának ajánlott eljárásai) című cikkben.

Az Azure Resource Manager segítségével, győződjön meg arról, hogy az erőforrások a megfelelő sorrendben jöjjenek létre függőségek elemzésével biztosítja. Ha egy erőforrás támaszkodik egy értéket egy másik erőforráshoz (például egy virtuális gépet kellene a tárfiókot lemezek), akkor [beállíthat egy függőséget](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies) a sablonban.

A sablonokat az infrastruktúra frissítésére is használhatja. Hozzáadhat például egy erőforrást a megoldáshoz, valamint hozzáadhat konfigurációs szabályokat a már telepített erőforrásokhoz. Ha a sablon határozza meg, de az erőforrás már létezik egy erőforrás létrehozását, a Resource Manager egy új eszköz létrehozása helyett frissítést végez. Resource Manager frissíti a meglévő eszközt állapot, mintha új lenne.

Resource Manager bővítményeket biztosít a forgatókönyvek amikor szüksége van további műveleteket, például szoftver, amely nem szerepel a telepítő telepíti.

### <a name="resource-tracking"></a>Erőforrás-követés

Ahogy a szervezet felhasználói erőforrások hozzáadása az előfizetéshez, válik fontosabb erőforrásokat társítani a megfelelő részleg, a vevő és a környezet. Metaadatok csatlakoztathat erőforrásai a [címkék](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags). Címkék használatával az erőforrás vagy a tulajdonos kapcsolatos adatok megadása. A címkék lehetővé teszi nemcsak összesíteni és csoportosíthatók az erőforrások több módszert is, de is az adatokat a jóváírási céljából.

Használja a címkék, ha egy összetett gyűjteménye, erőforráscsoport és erőforrások, vizuálisan megjeleníteni az eszközöket, amelyek a legésszerűbb is ugyanúgy kell. Megjelölheti például, amelyek hasonló szerepet töltenek a szervezetben, vagy a részleghez tartozó erőforrások.

Címkék nélkül a felhasználók a szervezet több erőforrást, amely a későbbiekben azonosítani és felügyelni nehéz lehet, hogy hozhat létre. Például előfordulhat, hogy törölni kívánt projekt összes erőforrását. Ha a projekt nem címkézett ezeket az erőforrásokat, kell manuálisan látja őket. A címkézés hasznos módja a felesleges költségek csökkentéséhez az előfizetésében.

Egy címke megosztása ugyanabban az erőforráscsoportban található erőforrásokat nem szükséges. Létrehozhat saját címkerendszert, győződjön meg arról, hogy a szervezet összes felhasználója közös címkék alkalmazása véletlenül némileg eltérő címkéket (például "részleg" helyett "Részl") helyett.

Erőforrás-szabályzatok lehetővé teszik a szervezet általános szabályok létrehozása. Győződjön meg arról, hogy erőforrások megfelelő címkékkel látja a megfelelő értékeket a szabályzatokat hozhat létre.

A címkézett erőforrásokat az Azure Portalon is megtekintheti. A [használati jelentés](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) az előfizetés tartalmazza a címkék nevét és értékeit, így Ön is részletezheti a költségeket címkék alapján.

Címkékkel kapcsolatos további információkért lásd: [számlázási címkék szabályzatának kezdeményezése](../azure-policy/scripts/billing-tags-policy-init.md).

Az alábbi korlátozások érvényesek a címkékre:

- Minden egyes erőforrás vagy erőforráscsoport legfeljebb 15 címkenév kulcs/érték párral rendelkezhet. A korlátozás csak a közvetlenül az erőforráscsoportra vagy erőforrásra alkalmazott címkékre érvényes. Egy erőforráscsoport tartalmazhatja, amelyek mindegyikére 15 címkenév kulcs/érték párral sok erőforrást.

- A címke neve legfeljebb 512 karakter hosszúságú lehet.

- A címke értéke legfeljebb 256 karakter hosszúságú lehet.

- Az egyes erőforráscsoportokra alkalmazott címkéket nem öröklik az adott erőforráscsoportba tartozó erőforrások.

Ha több mint 15 értéket kell társítania egy erőforráshoz, használjon JSON-sztringet a címke értékéhez. A JSON-karakterlánc is tartalmazhat egyetlen címkekulcs alkalmazott sok érték.

#### <a name="tags-for-billing"></a>A számlázási címkék

A címkék lehetővé teszik az elszámolási adatok. Például ha különböző szervezetek több virtuális gépet futtat, címkék használata használata a költséghely szerint. Címkék használatával költségek kategorizálása futtatókörnyezet, például a számlázási használata az éles környezetben futó virtuális gépek szerint.

Információ a címkék használatával lekérheti a [Azure erőforrás-használat és RateCard API-k](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) vagy a használati vesszővel elválasztott értékeket (CSV) fájl. A használati fájl letöltése a [fiókok az Azure portal](https://account.windowsazure.com/) vagy a [a nagyvállalati szerződések portáljának](https://ea.azure.com/).

Számlázási adatok való programozott hozzáféréssel kapcsolatos további információkért lásd: [betekintést nyerhet a Microsoft Azure erőforrás-használat](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). REST API-műveleteket, lásd: [Azure Billing – REST API-referencia](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Ha Ön a használati adatok letöltése CSV-szolgáltatásokhoz, amelyek támogatják a címkék a számlázási, a címkék a címkék oszlopban jelennek meg.

### <a name="critical-resource-controls"></a>Kritikus fontosságú erőforrás-vezérlők

Ahogy a szervezet alapvető szolgáltatásához ad hozzá az előfizetés, válik fontosabb, győződjön meg arról, hogy ezek a szolgáltatások érhetők el üzleti fenntartásához. [Erőforrás-zárolások](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) korlátozhatja a műveletek az értékes erőforrásokat, módosítsa vagy törölje őket jelentős hatást gyakorol az alkalmazások és a felhő-infrastruktúra rendelkezik. Egy előfizetés, erőforráscsoport vagy erőforrás zárolása alkalmazhat. Zárolások általában alapvető erőforrások, például a virtuális hálózatok, az átjárók és a storage-fiókok a alkalmazni.

Erőforrás-zárolások jelenleg támogatja a két érték: **védve** és **ReadOnly**. **Védve** azt jelenti, hogy a felhasználók (a szükséges engedélyekkel) is olvasni vagy módosítani az erőforrást, de nem lehet törölni. **Csak olvasható** azt jelenti, hogy engedéllyel rendelkező felhasználók nem lehet törölni vagy módosítani az erőforrást.

Erőforrás-zárolások csak vonatkozik, amelyek a felügyeleti sík, olyan küldött műveleteket tartalmaz, amely egy operations <https://management.azure.com>. A zárolása nem korlátozza, erőforrások hogyan hajthat végre a saját funkciók. Erőforrás-módosítások korlátozva, de az erőforrás-műveletek nem korlátozódnak. Ha például egy **ReadOnly** SQL-adatbázis zárolása megakadályozza a törlését vagy módosítását az adatbázis, de nem akadályozza meg a létrehozása, frissítése vagy törlése az adatbázisban található adatok.

Alkalmazása **ReadOnly** váratlan eredményekhez vezethet, mivel bizonyos műveletek, amelyek úgy tűnik, például olvasási műveletekhez szükséges további művelet. Például elhelyezése egy **ReadOnly** a storage-fiók zárolása megakadályozza, hogy minden felhasználó a kulcsok listázása. A kulcsok listázása művelet POST-kérés történik, mert a visszaadott kulcsok az írási műveletek érhetők el.

![Kritikus fontosságú erőforrás-vezérlők](./media/governance-in-azure/security-governance-in-azure-fig5.png)

A példában egy másik elhelyezése egy **ReadOnly** egy Azure App Service erőforrás zárolása megakadályozza, hogy a Visual Studio Server Explorer fájl az erőforrás jelenik meg, mert a kapcsolati írási hozzáférésre van szüksége.

Szerepköralapú hozzáférés-vezérlés, ellentétben a felügyeleti zárolások összes felhasználók és szerepkörök korlátozások alkalmazásához használhat. Engedélyek beállításával kapcsolatos további információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Amikor alkalmazza a zárolást, egy szülő hatókörben, a hatókörön belüli összes erőforrás azonos zárolási öröklik. Hozzáadását a későbbiekben még akkor is, erőforrások a zárolási öröklik a szülő. A legszigorúbb zárolást az öröklési ciklus élvez elsőbbséget.

Létrehozásához, vagy felügyeleti zárolások törlése, Microsoft.Authorization/ vagy Microsoft.Authorization/locks/ műveletek hozzáféréssel kell rendelkeznie. A beépített szerepkörök csak a tulajdonos és a felhasználói hozzáférés rendszergazdája megkapják ezeket a műveleteket.

### <a name="api-access-to-billing-information"></a>API-hozzáférés az számlázási adatokat

Használat és a resource adatok be az előnyben részesített adatelemző eszközökkel Azure Billing API-k használatával. Az Azure erőforrás-használat és RateCard API-k segítségével pontos előrejelzésére és kezelésére a költségeket. Az API-k vannak megvalósítva egy erőforrás-szolgáltató és a termékcsalád az az Azure Resource Manager által elérhetővé tett API-k egy részét.

#### <a name="resource-usage-api-preview"></a>Erőforrás-használati API (előzetes verzió)

Az Azure használata [erőforrás-használati API](https://msdn.microsoft.com/library/azure/mt219003) kívánt becsült Azure-használati adatait. Az API-t tartalmazza:

- **Az RBAC**: konfigurálása hozzáférési házirendek a a [az Azure portal](https://portal.azure.com/) vagy [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview) megadásához, hogy mely felhasználók vagy alkalmazások révén ingyenesen juthatnak az előfizetés használati adatokat. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés vagy a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörrel.

- **Óránkénti vagy napi aggregáció**: Hívóit adhatja meg szeretnének-e az Azure használati adatok óránkénti vagy napi lépésekben. Az alapértelmezett érték a napi.

- **(Erőforrás-címkét tartalmaz) példány metaadatok**: példányszintű részleteket, például a teljesen minősített erőforrás-URI (/subscriptions/ {előfizetés azonosítója} /...), erőforrás-csoport adatai, és az erőforráscímkék. A metaadatok segítségével determinisztikus és programozott módon használati lefoglalni a címkék az alkalmazási helyzetek, például a kereszt-díjszabási alapján.

- **Az erőforrás metaadatának**: erőforrás részleteit, például a fogyasztásmérő neve, mérőszám kategóriája, fogyasztásmérő alkategóriája, egységek és régió ad a hívó, amit felhasznált jobb megértése. Erőforrás-metaadatok terminológia igazítása az Azure Portalon keresztül is dolgozunk az Azure használati CSV, a nagyvállalati szerződés számlázási CSV és egyéb nyilvános, adatainak korreláltatására élményt nyújt segítséget.

- **Minden használati típusok ajánlat**: használati adatok érhető el az összes típusok, beleértve a használatalapú fizetés, MSDN, pénzügyi kötelezettségvállalást, a fizetésre használható Kredit és nagyvállalati szerződéssel rendelkező kínálnak.

#### <a name="resource-ratecard-api"></a>Erőforrás RateCard API

Az Azure-erőforrás RateCard API használatával elérhető Azure-erőforrások és a becsült díjszabási információkat az egyes listájának beolvasása. Az API-t tartalmazza:

- **Az RBAC**: a hozzáférési szabályzatok konfigurálása az Azure Portalon, vagy adja meg, hogy mely felhasználók vagy alkalmazások beszerezheti az RateCard adatokhoz való hozzáférés az Azure PowerShell-parancsmagok használatával. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés az olvasó, tulajdonosi vagy közreműködői szerepkörhöz.

- **Használatalapú fizetés, az MSDN, a pénzügyi kötelezettségvállalás és a fizetésre használható Kredit ajánlatokat (de nem a nagyvállalati szerződéssel rendelkező) támogatása**: Ez az API az Azure-ajánlat szintű díjszabás információival. Ez az API hívója erőforrás részleteit és a díjak az ajánlati információkat kell adja át. Nagyvállalati szerződéssel rendelkező jelenleg nem támogatott, mert a nagyvállalati szerződésre vonatkozó ajánlatok testreszabott regisztrációs mértékek. 

#### <a name="scenarios"></a>Forgatókönyvek

A használat és RateCard API-k együttes használata lehetővé teszi az ezekben a forgatókönyvekben:

- **Tudnivalók az Azure a hónap során töltött**: a használati kombinációját használja, és RateCard API-k segítségével megismerkedhet a felhő jobb csak a hónap során. Elemezheti az óránkénti vagy a napi használat és a költség becslése.

- **Riasztásokat állíthat be**: becsült felhőbeli fogyasztást és a költségek, és erőforrás-alapú vagy pénzügyi-alapú riasztásokat állíthat be a használat és RateCard API-k használatával.

- **Számlázási előrejelzése**: Get, a becsült használat és a felhőbeli költségek, és gépi tanulási algoritmusok előre jelezni, hogy mi a számla lenne az elszámolási időszakban végén a alkalmazni.

- **Hajtsa végre az elemzés előzetes használatalapú**: előrejelezheti, hogy mekkora a számla lenne a várható használat a számítási feladatok Azure-ba való áthelyezésekor a RateCard API-val. Ha rendelkezik meglévő számítási feladatokat, az egyéb felhőkben vagy privát felhők, a használat az Azure-ral is leképezheti díjak beolvasni egy jobb becslés az Azure-költségek. Ez a becslés teszi lehetővé az ajánlat forgáspont, és hasonlítsa össze a különböző típusú túl az utólagos elszámolású, például a pénzügyi kötelezettségvállalás és a fizetésre használható Kredit között.

- **Lehetőségelemzési elemzésével**: képes meghatározni, hogy azt helyett egy másik régióban található, vagy az Azure-erőforrás egy másik konfigurációs a számítási feladatok futtatásához. Az Azure erőforrás-használati díjak szerint előfordulhat, hogy különbözik az Azure-régiót használ. Azt is meghatározhatja, hogy ha egy másik Azure-ajánlat típus jobb sebesség nyújt egy Azure-erőforrás.

### <a name="networking-controls"></a>Hálózati vezérlők

Erőforrásokhoz való hozzáférés lehet (a vállalati hálózaton belül) belső vagy külső (az interneten) keresztül. A felhasználók a szervezet véletlenül a nem megfelelő helyszíni helyezni erőforrásokat, és potenciálisan rosszindulatú hozzáférés meg is gyerekjáték. Csakúgy, mint a helyszíni eszközök, vállalatok hozzá kell adnia a megfelelő szabályozásokkal győződjön meg arról, hogy az Azure-felhasználók a helyes döntések meghozatalában.

![Hálózati vezérlők](./media/governance-in-azure/security-governance-in-azure-fig6.png)

A következő alapvető erőforrások előfizetés-irányítás, egyszerű hozzáférés vezérlése érdekében adja meg.

#### <a name="network-connectivity"></a>Hálózati kapcsolat

[Virtuális hálózatok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) alhálózatok tároló objektumok. Bár ez nem feltétlenül szükséges, egy virtuális hálózatot gyakran használják a belső vállalati erőforrásokhoz az alkalmazásoknak az összekapcsolása. Az Azure Virtual Network szolgáltatás lehetővé teszi, hogy biztonságosan csatlakoztathatja az Azure-erőforrások egymáshoz a virtuális hálózatok.

Egy virtuális hálózat saját felhőbeli hálózatának egy reprezentációja. Egy virtuális hálózat egy dedikált az előfizetéshez az Azure felhő logikai elkülönítése. Virtuális hálózatok is csatlakozhat a helyszíni hálózathoz.

Az alábbiakban az Azure virtual Network hálózatokhoz használható képességek:

- **Elkülönítés**: virtuális hálózatok különítve egymástól. Létrehozhat önálló virtuális hálózatok a fejlesztési, tesztelési és éles környezetek, amelyek ugyanazt a CIDR címblokkokat használják. Ezzel szemben létrehozhat több virtuális hálózat, amely eltérő a CIDR címblokkokat használja, és hálózatok kapcsolhatja össze. Több alhálózatra is oszthatja a virtuális hálózat. Az Azure belső névfeloldást biztosít a virtuális gépek és az Azure Cloud Services-szerepkörpéldányok, amely egy virtuális hálózathoz csatlakoznak. Igény szerint konfigurálhatja egy virtuális hálózatot az Azure belső névfeloldást használata helyett a saját DNS-kiszolgálók használatához.

- **Internetkapcsolat**: minden Azure virtual machines és a egy virtuális hálózathoz csatlakozó Felhőszolgáltatásiszerepkör-példányokat a alapértelmezés szerint Internet-hozzáféréssel rendelkezik. Bejövő hozzáférést bizonyos erőforrásokhoz, igény szerint is engedélyezheti.

- **Azure-erőforrások kapcsolat**: Azure-erőforrások, például a Cloud Services és a virtuális gépek, csatlakozhat ugyanahhoz a virtuális hálózathoz. Az erőforrások keresztül is csatlakozhat egymással magánhálózati IP-címek, még akkor is, ha külön alhálózatokon. Az Azure biztosítja az alapértelmezett alhálózatokat, virtuális hálózatok és a helyszíni hálózatok közötti útválasztást, így konfigurálhatja és kezelheti az útvonalak nem kell.

- **Virtuális hálózati kapcsolat**: virtuális hálózat kapcsolható össze egymással. Bármely virtuális hálózathoz csatlakozó erőforrás és bármely más virtuális hálózaton erőforrás kommunikálhat.

- **Helyszíni kapcsolatok**: csatlakozhat virtuális hálózatok között a hálózat és az Azure magánhálózati kapcsolatokat, vagy egy helyek közötti virtuális magánhálózat (VPN) kapcsolaton keresztül a helyszíni hálózatok az interneten keresztül.

- **Forgalom szűrése**: hálózati forgalom (bejövő és kimenő) virtuális gépek és Felhőszolgáltatások forrás IP-cím és port, cél IP-cím és port és protokoll szerint szűrheti.

- **Útválasztás**: igény szerint felülbírálhatja az alapértelmezett Azure útválasztás, a saját útvonalak konfigurálásához, vagy egy hálózati átjárón keresztül a BGP-útvonalak segítségével.

#### <a name="network-access-controls"></a>Hálózati hozzáférés-vezérléssel

[Hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG), például egy tűzfal, és hogyan erőforrás "beszélhetünk" a hálózaton keresztül szabályokat adja meg. Szabályozhatja, hogyan egy alhálózat (vagy a virtuális gép) kapcsolódhatnak az interneten vagy más alhálózatokra az azonos virtuális hálózatba biztosítanak.

A hálózati biztonsági csoportok biztonsági szabályokat, amelyek engedélyezik vagy megtagadják a hálózati forgalmat az Azure virtuális hálózataihoz csatlakoztatott erőforrásokhoz listáját tartalmazza. Az NSG-k társíthatók alhálózatokhoz, egyedi virtuális gépek (klasszikus) vagy az egyes hálózati adapterek (NIC) csatolva a virtuális gépeken (Resource Manager).

Ha az NSG-t hozzárendelik egy alhálózathoz, a szabályok érvényesek lesznek az alhálózathoz csatlakozó összes erőforrást. Tovább korlátozhatja a forgalom társít egy NSG-t egy virtuális Géphez vagy hálózati adapterhez.

## <a name="security-and-compliance-with-organizational-standards"></a>Biztonság és a szervezeti szabványoknak való megfelelés

Minden cég rendelkezik a különböző igények és fog élhessen előnyt biztosít a felhőalapú megoldások. Továbbra is vevők mindenféle az ugyanazon alapvető kétségei vannak a felhőbe. Milyen ügyfelei a felhőszolgáltatóknak, amelyek szeretnék a következő:

- **Az adatok biztonságos**: informatikai vezetők tudomásul veszi, hogy a felhő nagyobb biztonságot és a felügyeleti vezérlő tud-e biztosítani. De továbbra is érintett, hogy a felhőbe való migrálás hagyja őket sebezhetőbbé támadókat, mint a jelenlegi belső fejlesztésű megoldások.

- **Az adatok bizalmas jellegének megőrzése**: felhőszolgáltatások egyedi adatvédelmi kihívások előléptetése. Vállalatok keresse meg a felhőbe, az infrastruktúra költségein mentéséhez, és növelheti a rugalmasságot, azokat is aggódni az adatok tárolására, ki fér hozzá, és hogyan lekérdezi a használt irányítását.

- **Ossza meg velünk vezérlő**: még akkor is, mivel a vállalatok további innovatív megoldások üzembe helyezéséhez a felhő előnyeit, azok aggódik elvesztése adataik felett. A legutóbbi közzétételeket kormányzati szervei jogi és a aktusokról azt jelenti, hogy keresztül éri el a vásárlói adatokat, győződjön meg arról, bizonyos informatikai igazgatók óvatos az adataikat a felhőben tárolja.

- **Átlátszóság előléptetése**: üzleti döntéshozók megismerheti a biztonsági, adatvédelmi és vezérlés fontosságát. Azonban akkor is érdemes egymástól függetlenül győződjön meg arról, hogyan azok adatot tárol, és a biztonságos hozzáférés lehetővé teszi.

- **Megfelelőség**: vállalatok bontsa ki a felhőalapú technológiák használatát, mivel az összetettséget és a szabványoknak és előírásoknak hatókörén tovább fejlődik. Vállalatok kell tudnia, hogy teljesülnek-e azok megfelelőségéről.

## <a name="security-configuration-for-monitoring-logging-and-auditing"></a>Biztonsági konfiguráció, a monitorozás, naplózás és naplózás

Az Azure-előfizetők több eszközről is kezelhetik felhőkörnyezeteiket. Ilyen eszköz például előfordulhat, hogy a felügyeleti munkaállomásokról, fejlesztői PC-kről és jogosult végfelhasználói eszközökről is, amelyek feladatspecifikus engedélyekkel rendelkeznek. 

Bizonyos esetekben a felügyeleti feladatkörök webalapú konzolok, például az Azure Portalon keresztül. Egyéb esetben lehet közvetlen kapcsolat az Azure-bA a helyszíni rendszerekből a VPN-eket, távoli asztali szolgáltatásokat, ügyfél-alkalmazásprotokollokon, vagy (szoftveresen) az Azure Service Management API (SMAPI) keresztül. Ezenkívül az ügyfél-végpontok lehetnek vagy tartományhoz csatlakoztatott vagy pedig elkülönítettek és felügyelet nélküliek, például táblagépek vagy az okostelefonok.

Is ugyanakkor nagymértékű kockázatot üzembe helyezése a felhőben. Nem könnyű kezelését, nyomon követheti, és rendszergazdai műveletek naplózhatók. Ez a felhőszolgáltatások kezelésére használt ügyfél-végpontok nem szabályozott hozzáférést biztonsági fenyegetésekkel is eredményezhet. Az általános vagy személyes munkaállomások fejlesztésre és infrastruktúra-kezelésre való használata olyan kiszámíthatatlan fenyegetési vektoroknak enged utat, mint például a webböngészés (pl. alapesetben megbízható weboldalak megfertőződése, ún. watering hole attack) vagy az e-mail (pl. pszichológiai manipuláció és adathalászat).

Megfigyelés, naplózás, és a naplózás biztosítják a követésének és értelmezésének rendszergazdai tevékenységet. Naplózás összes művelet teljes mindig nem megvalósítható a keletkező adatmennyiség miatt. De a felügyeleti házirendek hatékonyságának naplózása ajánlott eljárás.

Az Azure security irányítás az Azure Active Directory Domain Services (AD DS) csoportházirend-objektumok segítségével szabályozhatja a rendszergazdák összes Windows felületek, például a fájlmegosztást. A felügyeleti munkaállomásokra a figyelés, naplózás és folyamatok naplózását. Kövessen nyomon minden rendszergazdai és fejlesztői hozzáférést és tevékenységet.

### <a name="azure-security-center"></a>Azure Security Center

[Az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) központi előfizetés erőforrásainak biztonsági állapotát jeleníti meg. Javaslatok, amelyek meggátolják a feltört erőforrásokat biztosít. Engedélyezheti, hogy a részletesebb szabályzatok – például szabályzatok alkalmazása az adott erőforrás-csoportok lehetővé teszik a vállalat a megfelelő, azok állapotát a kockázatnak, címzést használ.

![Azure Security Center](./media/governance-in-azure/security-governance-in-azure-fig7.png)

Security Center integrált biztonsági monitorozást és felügyeletet biztosít az Azure-előfizetésekre, megkönnyíti a nehezen észlelhető fenyegetések azonosítását szabályzatkezelést, és a biztonsági megoldások széles ökoszisztémájával képes együttműködni. Miután engedélyezte a [biztonsági házirendek](https://docs.microsoft.com/azure/security-center/security-center-policies) az előfizetéshez tartozó erőforrások, a Security Center elemzi az erőforrások azonosíthatja a potenciális biztonsági réseket. A hálózati konfigurációval kapcsolatos információk azonnal elérhetők.

Az Azure Security Center ajánlott gyakorlat elemzési és biztonsági házirendek kezelése az Azure-előfizetésen belüli összes erőforrás kombinációját jelenti. Lehetővé teszi a biztonsági csoportok és a kockázati tisztviselők megelőzését, észlelését és vállalatot érintő biztonsági fenyegetésekre automatikusan gyűjti és elemzi az Azure-erőforrások, a hálózati és a partneri megoldások, például kártevőirtó-programok és tűzfalak biztonsági adatait.

Emellett az Azure Security Center bővített analitikát alkalmaz, beleértve a gépi tanulás és viselkedéselemzés. A Microsoft-termékek és szolgáltatások, a Microsoft Digital Crimes Unit (DCU), a Microsoft Security Response Center (MSRC) globális fenyegetésészlelési intelligenciát használ, és a külső hírcsatornák. Alkalmazhat [biztonsági cégirányítási](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) széles körben az előfizetés szintjén. Vagy szűkíthetők azt le a konkrét követelmények, és azokat a szabályzat-definíció egyedi erőforrásai a alkalmazni.

Végül az Azure Security Center elemzi az erőforrás biztonsági állapota ezen házirendek alapján, és adja meg a részletes betekintést biztosító irányítópultok és a riasztási az eseményeket, mint például a kártevő-észlelési vagy rosszindulatú IP-kapcsolati kísérletek ezen információk segítségével. Javaslatok alkalmazásával kapcsolatos további információkért lásd: [biztonsági javaslatok alkalmazása az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Az Azure Security Center figyeli a következő Azure-erőforrások:

- Virtuális gépek (VM) (beleértve a felhőszolgáltatások)

- Virtuális hálózatok

- SQL Database-adatbázisok

- Partneri megoldások az Azure-előfizetésében integrált, például a virtuális gépeken és a egy WebApp tűzfalat a [App Service Environment-környezet](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme)

A Security Center első használatakor az adatgyűjtés engedélyezése az összes virtuális gépet az előfizetésében. Azt javasoljuk, hogy maradjon az adatgyűjtés engedélyezve van, de is [tiltsa le azt](https://docs.microsoft.com/azure/security-center/security-center-faq) a Security Center-házirendben.

### <a name="log-analytics"></a>Log Analytics

Az Azure Log Analytics szoftverek fejlesztési és a szolgáltatás csapata információ-biztonság és [cégirányítási program](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) támogatja az üzleti követelmények. Létrehozásakor törvényeknek és szabályozásoknak ismertetett módon [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) és [Microsoft Megbízhatósági központ – megfelelőség](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Hogyan hozza létre a Log Analytics biztonsági követelmények, azonosítja a biztonsági vezérlőket, és kezeli, és a figyelők kockázatok ott is ismerteti. Évente a csapat áttekinti a házirendek, szabványok, eljárásokra és irányelveket.

Minden Log Analytics fejlesztői csapat egyik tagja megkapja a hivatalos alkalmazás biztonsági képzés. A verziókövetési rendszerét védi minden egyes szoftverfrissítési projekt fejlesztés alatt.

A Microsoft rendelkezik egy biztonsági és megfelelőségi csapat felügyeli, és minden szolgáltatás a Microsoft értékeli. A csapat alkotó Information security officer, és azok még nem fejlesztése a Log Analytics mérnöki projektjei társítva. A biztonsági tisztviselők rendelkezik a saját felügyeleti láncot. Független értékelések termékek és szolgáltatások biztosításához, biztonsági és megfelelőségi programokról.

A Log Analytics központi funkcióit az Azure-ban futó szolgáltatások biztosítják. Mindegyik szolgáltatás egy adott felügyeleti funkciót biztosít, és a szolgáltatások kombinálásával különféle felügyeleti forgatókönyvek valósíthatók meg.

![Azure-szolgáltatások kezelése](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

A felügyeleti szolgáltatások arra tervezték, a felhőben. Ön teljes mértékben Azure-ban üzemeltetett, így nem jár együtt üzembe helyezése és kezelése a helyszíni erőforrásokhoz. Konfigurációs minimális, és helyezheti üzembe lehet percek alatt.

Helyezzen egy ügynök minden olyan Windows vagy Linux rendszerű számítógépen a helyi adatközpontban, és adatokat küld a Log Analytics szolgáltatásba. Itt elemezhetők más felhőbeli vagy helyszíni szolgáltatásokból gyűjtött adatokkal együtt. Az Azure Backup és az Azure Site Recovery használatával a helyszíni erőforrások biztonsági mentésére és magas rendelkezésre állásának a felhő előnyeit.

![Felügyeleti szolgáltatások az Azure-irányítópulton](./media/governance-in-azure/security-governance-in-azure-fig8.png)

A felhőben futó Runbookok általában nem férnek hozzá a helyszíni erőforrásokhoz, de egy vagy több olyan számítógép, amely a runbookokat az adatközpontban is telepíthető egy ügynök. Amikor elindít egy runbookot, hogy adja meg, hogy annak futtatását a felhőben vagy a helyi feldolgozón.

A Microsoft és partnerei, hogy növelje befektetéseit a Log Analytics az Azure-előfizetése adhat különböző megoldások érhetők el. Például az Azure kínál [felügyeleti megoldások](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions)--előrecsomagolt csoportok logika, amely egy vagy több felügyeleti szolgáltatás segítségével egy felügyeleti forgatókönyv megvalósításához.

![Katalógus megoldások az Azure-ban](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Partnerként létrehozhat saját megoldások támogatják az alkalmazások és szolgáltatások és azok a felhasználók számára az Azure Marketplace piactéren vagy a rövid útmutató sablonok.

## <a name="performance-alerting-and-monitoring"></a>Teljesítmény-figyelmeztetések és monitorozás

### <a name="alerting"></a>Riasztások kezelése

Riasztások egy metódust az Azure-erőforrások figyelési metrikákat, eseményeket és naplókat. Ezek a értesíteni, a megadott feltétel teljesülése esetén.

Szolgáltatások, beleértve a különböző riasztások állnak rendelkezésre:

- **Az Azure Application Insights**: lehetővé teszi, hogy a webes teszt- és a mérőszám riasztások. További információkért lásd: [riasztások beállításához az Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) és [rendelkezésre állásának és válaszkészségének megfigyelése bármely webhelyen](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- **Log Analytics**: lehetővé teszi az Útválasztás és diagnosztikai naplókat a Log Analytics szolgáltatásba. Lehetővé teszi a mérőszám, a napló és a más riasztástípusok. További információkért lásd: [riasztások a Log Analyticsben](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- **Az Azure Monitor**: lehetővé teszi, hogy a riasztások metrikaértékek és a tevékenységnapló-események alapján. Használhatja a [Azure Monitor REST API](https://msdn.microsoft.com/library/dn931943.aspx) riasztások kezelésével. További információkért lásd: [riasztások létrehozása az Azure portal, PowerShell vagy a parancssori felület segítségével](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Figyelés

Teljesítményproblémákat okozhat a cloud App befolyásolhatja az üzleti. Több egymáshoz kapcsolódó összetevők és a gyakori kiadások romlását fordulhat elő, tetszőleges időpontban. És ha egy alkalmazást fejleszt, a felhasználók általában problémákat, amelyek nem találta meg a tesztelés. Ezek a problémák azonnal tudnia kell, és diagnosztizálása és kijavítása azokat az eszközöket.

Nincs a különböző eszközök, Azure-alkalmazások és szolgáltatások figyelésre. Azok a funkciók némelyike átfedik egymást. Részben okozza felismerhetetlenné fejlesztési és a egy alkalmazás művelet között.

Az alábbiakban a fő eszközöket:

- **Az Azure Monitor** alapszintű eszköz a szolgáltatások az Azure-ban. Biztosít egy szolgáltatás és a környezete adatforgalmáról infrastruktúra-szintű adatokat. Ha az Azure-ban, és eldönti, hogy méretezhető és állíthat le erőforrásokat az alkalmazások felügyeli, az Azure Monitor segítségével indítsa el.

- **Az Application Insights** fejlesztési, valamint egy figyelési megoldás éles környezetben is használható. Úgy működik, hogy egy csomag telepítése az alkalmazásban, így azt áttekintést nyújt a több belső eseményeit. A válaszidőket a függőségek, kivételek hívásláncokat – hibakereső pillanatképek és végrehajtási profiljainak szerepel. Eszközöket biztosít, az összes ezt a telemetriát is segítséget nyújt az alkalmazás hibakeresése, és a segítségével megismerheti, mit felhasználók általi használatát, elemzésére. Beállíthatja, hogy a válaszidők ugrásszerű-e valami miatt az alkalmazás vagy külső resourcing probléma. Használatakor a Visual Studio és az alkalmazás vétkes, akkor lépjen közvetlenül a probléma kódsort, hogy kijavíthassuk.

- **Log Analytics** van azok számára, akik teljesítmény hangolása és megtervezik a karbantartást, éles környezetben futó alkalmazások. Azt gyűjti, és összesíti az adatokat számos más forrásból, 10 – 15 perces késéssel. Az Azure-hoz, egy teljes körű IT-felügyeleti megoldást biztosít a helyszíni és külső felhő alapú infrastruktúra (például az Amazon Web Services). Eszközök adatainak elemzését forrásokból, lehetővé teszi az összes napló összetett lekérdezéseket, és proaktív módon riasztja Önt a megadott feltételek biztosít. Akár az a központi tárházban egyéni adatok gyűjtése és majd lekérdezheti, és megjelenítheti az adatokat.

- **A System Center Operations Manager** kezeléséhez és monitorozásához nagyméretű felhőbeli telepítések van. Esetleg már ismerős vele a helyszíni Windows Server felügyeleti eszközként és Hyper-V-alapú felhők, de is integrálása és kezelése az Azure-alkalmazások. Többek között azt is telepítheti az Application Insights meglévő élő alkalmazások. Ha egy alkalmazás leáll, az Operations Manager kiderül, hogy másodpercek alatt.


## <a name="next-steps"></a>További lépések

- [Az Azure Resource Manager-sablonok létrehozásának ajánlott eljárásai](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)

- [Példák a megvalósítása az Azure előfizetés-irányítás](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-examples)

- [A Microsoft Azure Government](https://docs.microsoft.com/azure/azure-government/)
