---
title: Privileged Identity Management (PIM) üzembe helyezése – Azure AD | Microsoft Docs
description: Útmutatás Azure AD Privileged Identity Management (PIM) üzembe helyezésének megtervezéséhez.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/06/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e8250661fdbd6c67faade31caaed61ee8a399fe
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008090"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Azure AD Privileged Identity Management (PIM) üzembe helyezése

Ez a részletes útmutató ismerteti, hogyan tervezhető meg a Privileged Identity Management (PIM) üzembe helyezése a Azure Active Directory (Azure AD) szervezetében.

> [!TIP]
> Ebben a cikkben a következőként megjelölt elemeket fogja látni:
>
> : heavy_check_mark: a **Microsoft javasolja**
>
> Ezek általános javaslatok, és csak akkor kell megvalósítani, ha az adott vállalati igényekre vonatkoznak.

## <a name="learn-about-privileged-identity-management"></a>Tudnivalók a Privileged Identity Management

A Azure AD Privileged Identity Management segítségével felügyelheti az Azure AD, az Azure-erőforrások és más Microsoft Online Services Kiemelt felügyeleti szerepköreit. Egy olyan világban, ahol privilegizált identitások vannak hozzárendelve és elfelejtve, Privileged Identity Management olyan megoldásokat kínál, mint az igény szerinti hozzáférés, a jóváhagyási munkafolyamatok kérelmezése és a teljes körűen integrált hozzáférési felülvizsgálatok, amelyek segítségével valós időben azonosíthatja, feltárhatja és megakadályozhatja a Kiemelt szerepkörök rosszindulatú tevékenységeit. A munkahelyen a Kiemelt szerepkörök kezeléséhez Privileged Identity Management üzembe helyezése nagy mértékben csökkenti a kockázatokat, miközben a Kiemelt szerepkörök tevékenységeivel kapcsolatos értékes információkra felszínre kerül.

### <a name="business-value-of-privileged-identity-management"></a>Privileged Identity Management üzleti értéke

A [minimális jogosultsági szintű hozzáférés](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) és az igény szerinti hozzáférés elvét kikényszerítve **kezelheti a szervezet kockázatkezelését** . Azáltal, hogy minimalizálja a felhasználók állandó hozzárendeléseinek számát a Kiemelt szerepkörökhöz, valamint a jóváhagyások érvényesítését és az MFA-t a jogosultságszint-emelésre, nagy mértékben csökkentheti az emelt szintű hozzáféréshez kapcsolódó biztonsági kockázatokat a szervezetében. A minimális jogosultságok érvényesítése és az igény szerinti hozzáférés is lehetővé teszi, hogy megtekintse a Kiemelt szerepkörökhöz való hozzáférés előzményeit, és nyomon követhesse a biztonsági problémákat.

A **megfelelőség és az irányítás** – a Privileged Identity Management üzembe helyezése a környezet folyamatos identitás-irányítását eredményezi. A magas jogosultsági szintű identitások igény szerinti megemelése lehetővé teszi, hogy a Privileged Identity Management nyomon kövessék a szervezet privilegizált hozzáférési tevékenységeit. A szervezeten belüli állandó és jogosult szerepkörök összes hozzárendelésére vonatkozó értesítéseket is megtekinthet és fogadhat. A hozzáférési felülvizsgálattal rendszeresen ellenőrizheti és eltávolíthatja a szükségtelen jogosultságú identitásokat, és gondoskodhat arról, hogy a szervezet megfeleljen a legszigorúbb identitási, hozzáférési és biztonsági szabványoknak.

**Csökkentse a költségeket** – csökkentse a hatékonyságot, az emberi hibákat és a biztonsági problémákat a Privileged Identity Management megfelelő üzembe helyezésével. A nettó eredmény az emelt szintű identitásokkal kapcsolatos számítógépes bűncselekmények csökkentése, ami költséges és nehézkes a helyreállításhoz. A Privileged Identity Management a szervezet számára is segít csökkenteni a hozzáférés-naplózási információkhoz kapcsolódó költségeket, amikor megfelel a szabályozásoknak és szabványoknak.

További információ: [Mi az Azure ad Privileged Identity Management?](pim-configure.md)

### <a name="licensing-requirements"></a>Licenckövetelmények

Privileged Identity Management használatához a címtárnak a következő fizetős vagy próbaverziós licencek egyikével kell rendelkeznie:

- Prémium szintű Azure AD P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 oktatási célokra A5
- Microsoft 365 Nagyvállalati verzió E5

További információ: [a Privileged Identity Management használatára vonatkozó licencfeltételek](subscription-requirements.md).

### <a name="key-terminology"></a>Kulcsfontosságú terminológia

| Kifejezés vagy fogalom | Leírás |
| --- | --- |
| támogatható | Szerepkör-hozzárendelés, amely megköveteli, hogy a felhasználó egy vagy több műveletet hajtson végre a szerepkör használatához. Ha egy felhasználó jogosult a szerepkörre, az azt jelenti, hogy aktiválhatja a szerepkört, amikor kiemelt feladatokat kell elvégeznie. A hozzáférés nem különbözik a jogosult szerepkör-hozzárendeléssel rendelkező valakinek. Az egyetlen különbség, hogy néhány embernek nincs szüksége arra, hogy minden alkalommal hozzáférjen. |
| aktiválás | Egy vagy több művelet végrehajtásának folyamata olyan szerepkör használatára, amelyhez a felhasználó jogosult. A műveletek tartalmazhatják a többtényezős hitelesítés (MFA) ellenőrzését, üzleti indoklást biztosítanak, vagy a kijelölt jóváhagyók jóváhagyását kérik. |
| igény szerinti (JIT) hozzáférés | Olyan modell, amelyben a felhasználók ideiglenes engedélyeket kapnak a Kiemelt feladatok végrehajtásához, ami megakadályozza, hogy a rosszindulatú vagy jogosulatlan felhasználók hozzáférjenek az engedélyek lejárta után. A hozzáférés csak akkor adható meg, ha a felhasználóknak szüksége van rá. |
| a minimális jogosultsági szintű hozzáférés elve | Ajánlott biztonsági eljárás, amelyben minden felhasználó csak a végrehajtásra jogosult feladatok végrehajtásához szükséges minimális jogosultságokkal rendelkezik. Ez a gyakorlat lekicsinyíti a globális rendszergazdák számát, és bizonyos helyzetekben adott rendszergazdai szerepköröket használ. |

További információ: [terminológia](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-privileged-identity-management-works"></a>A Privileged Identity Management működésének magas szintű áttekintése

1. A Privileged Identity Management úgy van beállítva, hogy a felhasználók jogosultak legyenek a Kiemelt szerepkörökre.
1. Ha egy jogosult felhasználónak a Kiemelt szerepkört kell használnia, aktiválja a szerepkört Privileged Identity Managementban.
1. A szerepkörhöz konfigurált Privileged Identity Management beállításoktól függően a felhasználónak végre kell hajtania bizonyos lépéseket (például a többtényezős hitelesítés végrehajtása, a jóváhagyás beszerzése vagy az OK megadása).
1. Miután a felhasználó sikeresen aktiválta a szerepkört, egy előre konfigurált időszakot kapnak a szerepkörhöz.
1. A rendszergazdák megtekinthetik az összes Privileged Identity Management tevékenység előzményeit a naplóban. Emellett az Azure AD-szervezeteket továbbra is biztonságossá tehetik, és Privileged Identity Management-funkciókkal, például hozzáférési felülvizsgálatokkal és riasztásokkal teljesítik a megfelelőséget.

További információ: [Mi az Azure ad Privileged Identity Management?](pim-configure.md)

### <a name="roles-that-can-be-managed-by-privileged-identity-management"></a>A Privileged Identity Management felügyelhető szerepkörök

**Azure ad-szerepkörök** – ezek a szerepkörök mind Azure Active Directory (például globális rendszergazda, Exchange-rendszergazda és biztonsági rendszergazda). A szerepkörökről és azok funkcióival kapcsolatos további információkért tekintse meg [Azure Active Directory a rendszergazdai szerepkör engedélyei](../users-groups-roles/directory-assign-admin-roles.md)című részt. A rendszergazdák által hozzárendelt szerepkörök meghatározásával kapcsolatos segítségért tekintse meg a [legkevésbé Kiemelt szerepkörök feladat szerint feladatot](../users-groups-roles/roles-delegate-by-task.md).

**Azure-erőforrás szerepkörei** – ezek a szerepkörök egy Azure-erőforráshoz, erőforráscsoporthoz, előfizetéshez vagy felügyeleti csoportjához vannak társítva. A Privileged Identity Management igény szerinti hozzáférést biztosít a beépített szerepkörökhöz, például a tulajdonoshoz, a felhasználói hozzáférés-adminisztrátorhoz és a Közreműködőhöz, valamint [egyéni szerepkörökhöz](../../role-based-access-control/custom-roles.md). Az Azure-beli erőforrás-szerepkörökkel kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md).

További információ: [Privileged Identity Managementban nem felügyelhető szerepkörök](pim-roles.md).

## <a name="plan-your-deployment"></a>Az üzembe helyezés megtervezése

Ez a szakasz azt ismerteti, hogy mit kell tennie a Privileged Identity Management a szervezeten belüli üzembe helyezése előtt. Fontos, hogy kövesse az utasításokat, és Ismerje meg az ebben a szakaszban ismertetett fogalmakat, mivel útmutatást nyújt a szervezet emelt szintű identitásához igazított legjobb terv létrehozásához.

### <a name="identify-your-stakeholders"></a>Az érintettek azonosítása

A következő szakasz segítségével azonosíthatja a projektben érintett összes érintettet, és ki kell jelentkeznie, ellenőriznie vagy tájékoztatnia kell. Külön táblákat tartalmaz a Privileged Identity Management Azure AD-szerepkörökhöz való üzembe helyezéséhez és az Azure-beli erőforrás-szerepkörök Privileged Identity Managementához. Adja hozzá az érintetteket az alábbi táblázathoz a szervezete számára megfelelő módon.

- TEHÁT = kijelentkezés ezen a projekten
- R = a projekt áttekintése és bemenet megadása
- I = tájékozott a projektről

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Érintett felek: Privileged Identity Management Azure AD-szerepkörökhöz

| Név | Role | Művelet |
| --- | --- | --- |
| Név és e-mail | **Identity Architect vagy Azure globális rendszergazda**<br/>Az Identity Management csapatának képviselője, amely meghatározza, hogy a változás hogyan igazodik a szervezet alapvető Identity Management-infrastruktúrához. | SO/R/I |
| Név és e-mail | **Szolgáltatás tulajdonosa/soros kezelő**<br/>Egy szolgáltatás vagy szolgáltatási csoport informatikai tulajdonosai. Ezek kulcsfontosságúak a döntések meghozatalában és a munkatársaik Privileged Identity Managementának elősegítésében. | SO/R/I |
| Név és e-mail | **Biztonsági tulajdonos**<br/>A biztonsági csapat képviselője, amely kijelentkezhet, hogy a terv megfelel a szervezete biztonsági követelményeinek. | SO/R |
| Név és e-mail | **INFORMATIKAI támogatás kezelője/ügyfélszolgálata**<br/>Az informatikai támogatási szervezet egyik képviselője, aki a változás támogatását nyújtja a helpdesk szemszögéből. | R/I |
| A kísérleti felhasználók neve és e-mail-címe | **Kiemelt szerepkörű felhasználók**<br/>Azon felhasználók csoportja, amelyeken a Privileged Identity Management implementálva van. Tudnia kell, hogyan aktiválja a szerepköröket Privileged Identity Management megvalósítása után. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-resource-roles"></a>Érintett felek: Privileged Identity Management az Azure-erőforrás szerepköreihez

| Név | Role | Művelet |
| --- | --- | --- |
| Név és e-mail | **Előfizetés/erőforrás tulajdonosa**<br/>Az egyes előfizetések vagy erőforrások tulajdonosának a Privileged Identity Management központilag telepíteni kívánó képviselője | SO/R/I |
| Név és e-mail | **Biztonsági tulajdonos**<br/>A biztonsági csapat képviselője, amely kijelentkezhet, hogy a terv megfelel a szervezete biztonsági követelményeinek. | SO/R |
| Név és e-mail | **INFORMATIKAI támogatás kezelője/ügyfélszolgálata**<br/>Az informatikai támogatási szervezet egyik képviselője, aki a változás támogatását nyújtja a helpdesk szemszögéből. | R/I |
| A kísérleti felhasználók neve és e-mail-címe | **Azure-szerepkör felhasználói**<br/>Azon felhasználók csoportja, amelyeken a Privileged Identity Management implementálva van. Tudnia kell, hogyan aktiválja a szerepköröket Privileged Identity Management megvalósítása után. | I |

### <a name="enable-privileged-identity-management"></a>Privileged Identity Management engedélyezése

A tervezési folyamat részeként először el kell fogadnia a Privileged Identity Managementt, és engedélyeznie kell az [Privileged Identity Management cikk használatának](pim-getting-started.md) első lépéseit. A Privileged Identity Management engedélyezése olyan funkciók elérését teszi lehetővé, amelyek kifejezetten az üzemelő példány segítségére lettek kialakítva.

Ha a cél az Azure-erőforrásokhoz való Privileged Identity Management üzembe helyezése, kövesse az [Azure-erőforrások felderítése Privileged Identity Management cikkben való felügyeletét](pim-resource-roles-discover-resources.md) ismertető cikket. Csak az előfizetések és a felügyeleti csoportok tulajdonosai tudják felderíteni és bevezetni ezeket az erőforrásokat Privileged Identity Managementba. A bevezetést követően a PIM funkció minden szinten elérhető a tulajdonosok számára, beleértve a felügyeleti csoportot, az előfizetést, az erőforráscsoportot és az erőforrást. Ha Ön a globális rendszergazda, aki az Azure-erőforrások Privileged Identity Management telepítését kísérli meg, az [összes Azure-előfizetés kezeléséhez megemelheti a hozzáférést](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) , hogy az összes Azure-erőforráshoz hozzáférhessen a felderítéshez. Azt javasoljuk azonban, hogy az egyes előfizetésekhez tartozó tulajdonosok jóváhagyása előtt az erőforrásaikat Privileged Identity Management-mel kezelhesse.

### <a name="enforce-principle-of-least-privilege"></a>A legalacsonyabb jogosultsági szint betartatásának elve

Fontos, hogy az Azure AD-hez és az Azure-erőforrás szerepköreihez egyaránt érvényesítse a legalacsonyabb jogosultsági szint elvét a szervezetben.

#### <a name="azure-ad-roles"></a>Azure AD-szerepkörök

Az Azure AD-szerepkörök esetében gyakori, hogy a szervezetek jelentős számú rendszergazdához rendelik a globális rendszergazdai szerepkört, ha a legtöbb rendszergazdának csak egy vagy két konkrét rendszergazdai szerepkörre van szüksége. A Kiemelt szerepkör-hozzárendelések is általában nem maradnak felügyelet alatt.

> [!NOTE]
> Gyakori buktatók a szerepkör-delegálásban:
>
> - Az Exchange-hez felelős rendszergazda a globális rendszergazdai szerepkört kapja, annak ellenére, hogy csak az Exchange-rendszergazdai szerepkörre van szükségük a napi feladat elvégzéséhez.
> - A globális rendszergazdai szerepkör hozzá van rendelve egy Office-rendszergazdához, mert a rendszergazdának a biztonsági és a SharePoint-rendszergazdai szerepkörökre is szüksége van, és egyszerűbb csak egyetlen szerepkört delegálni.
> - A rendszergazda biztonsági rendszergazdai szerepkört rendelt a feladat elvégzéséhez, de soha nem távolították el.

Az alábbi lépések végrehajtásával kényszerítheti ki az Azure AD-szerepkörökhöz tartozó legalacsonyabb jogosultsági szint elvét.

1. A szerepkörök részletességének megismeréséhez olvassa el és ismerkedjen meg az [elérhető Azure ad-rendszergazdai szerepkörökkel](../users-groups-roles/directory-assign-admin-roles.md#available-roles). Az Azure AD-ben Ön és csapata is hivatkozhat a [rendszergazdai szerepkörökre az Azure ad-ben](../users-groups-roles/roles-delegate-by-task.md), amely ismerteti a legkevésbé Kiemelt szerepkört az adott feladatokhoz.

1. A szervezete Kiemelt szerepkörrel rendelkező szerepköreinek listázása. A Privileged Identity Management [felderítési és](pim-security-wizard.md) adatellenőrzési (előzetes verzió) segítségével megtekintheti a következőhöz hasonló oldalt.

    ![Felderítési és észlelési (előzetes) lap, amellyel csökkentheti az expozíciót a Kiemelt szerepkörök használatával](./media/pim-deployment-plan/new-preview-page.png)

1. A szervezet összes globális rendszergazdája számára Ismerje meg, hogy miért van szükségük a szerepkörre. A korábbi dokumentáció elolvasása alapján, ha a személy feladatát egy vagy több részletes rendszergazdai szerepkörrel is elvégezheti, távolítsa el őket a globális rendszergazdai szerepkörből, és ennek megfelelően végezze el a hozzárendeléseket Azure Active Directoryban (hivatkozás: a Microsoft jelenleg csak a globális rendszergazdai szerepkörrel rendelkező 10 rendszergazdával rendelkezik. Ismerje meg, [hogyan használja a Microsoft a Privileged Identity Management](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)).

1. Az összes többi Azure AD-szerepkör esetében tekintse át a hozzárendelések listáját, azonosítsa azokat a rendszergazdákat, akiknek már nincs szüksége a szerepkörre, majd távolítsa el őket a hozzárendeléseiről.

Az utolsó két lépés automatizálásához használhatja a Privileged Identity Management hozzáférési felülvizsgálatait. Az [Privileged Identity Management Azure ad-szerepkörökhöz való hozzáférési felülvizsgálat indítása](pim-how-to-start-security-review.md)című rész lépéseit követve hozzáférési felülvizsgálatot állíthat be minden olyan Azure ad-szerepkörhöz, amely egy vagy több taggal rendelkezik.

![Hozzáférési felülvizsgálati ablaktábla létrehozása Azure AD-szerepkörökhöz](./media/pim-deployment-plan/create-access-review.png)

A véleményezőket a **Tagok (saját)** értékre kell beállítani. Ez egy e-mailt küld a szerepkör összes tagjának, hogy megbizonyosodjon róla, hogy szükségük van-e a hozzáférésre. A speciális beállításokban be kell kapcsolni a **jóváhagyás** szükségességét is, hogy a felhasználók tudják, miért van szükségük a szerepkörre. Ezen információk alapján eltávolíthatja a felhasználókat a szükségtelen szerepkörökből, és a globális rendszergazdák esetében részletesebb rendszergazdai szerepköröket delegálhat.

A hozzáférési felülvizsgálatok az e-mailekre támaszkodva értesítik a felhasználókat, hogy vizsgálják át a szerepkörük hozzáférését. Ha olyan Kiemelt jogosultságú fiókkal rendelkezik, amelyekhez nem tartozik e-mailek társítva, akkor ügyeljen arra, hogy a másodlagos e-mail-mezőket feltöltse a fiókoknál. További információ: [proxyAddresses attribútum az Azure ad-ben](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Azure-erőforrásszerepkörök

Az Azure-előfizetések és-erőforrások esetében beállíthat egy hasonló hozzáférési felülvizsgálati folyamatot, amellyel áttekintheti a szerepköröket az egyes előfizetésekben vagy erőforrásokban. Ennek a folyamatnak a célja az egyes előfizetésekhez vagy erőforrásokhoz csatolt tulajdonosi és felhasználói hozzáférési rendszergazdai hozzárendelések csökkentése, valamint a szükségtelen hozzárendelések eltávolítása. A szervezetek azonban gyakran delegálják ezeket a feladatokat az egyes előfizetések vagy erőforrások tulajdonosának, mert jobban megértették az adott szerepköröket (különösen az egyéni szerepköröket).

Ha Ön rendszergazda, és a globális rendszergazdai szerepkörrel próbálta üzembe helyezni Privileged Identity Management Azure-erőforrásokhoz a szervezetében, a [hozzáférést az összes Azure-előfizetés kezeléséhez megemelheti](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) , hogy hozzáférjen az egyes előfizetésekhez. Ezután megkeresheti az egyes előfizetések tulajdonosait, és dolgozhat velük a szükségtelen hozzárendelések eltávolításához és a tulajdonosi szerepkör-hozzárendelés minimalizálásához.

Az Azure-előfizetésekhez tartozó tulajdonosi szerepkörrel rendelkező felhasználók az Azure- [erőforrások hozzáférési felülvizsgálatait](pim-resource-roles-start-access-review.md) is használhatják az Azure ad-szerepkörökhöz korábban ismertetett eljáráshoz hasonló, szükségtelen szerepkör-hozzárendelések naplózására és eltávolítására.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Döntse el, hogy mely szerepkör-hozzárendeléseket szeretné védeni Privileged Identity Management

Miután feltöltötte a Kiemelt szerepkör-hozzárendeléseket a szervezetében, el kell döntenie, hogy mely szerepköröket kell védelemmel ellátni Privileged Identity Management.

Ha Privileged Identity Management védi a szerepkört, akkor a hozzá tartozó jogosult felhasználóknak a szerepkör által biztosított jogosultságokat kell használniuk. A jogosultságszint-emelési folyamat magában foglalhatja a jóváhagyás beszerzését, a többtényezős hitelesítés végrehajtását és/vagy az aktiválásuk okát. Privileged Identity Management az értesítéseken és a Privileged Identity Management és az Azure AD naplózási eseménynaplókban is nyomon követheti a jogosultságszint-emeléseket.

Ha kijelöli, hogy mely szerepköröket szeretné a védelemmel ellátni Privileged Identity Management, akkor az egyes szervezetek számára is bonyolult lehet. Ez a szakasz az Azure AD-vel és az Azure-erőforrás-szerepkörökkel kapcsolatos ajánlott eljárásokat ismerteti.

#### <a name="azure-ad-roles"></a>Azure AD-szerepkörök

Fontos, hogy rangsorolja a legtöbb engedélyekkel rendelkező Azure AD-szerepkörök védelmét. Az összes Privileged Identity Management ügyfél használati mintái alapján a Privileged Identity Management által kezelt első 10 Azure AD-szerepkör a következő:

1. Globális rendszergazda
1. Biztonsági rendszergazda
1. Felhasználói rendszergazda
1. Exchange-rendszergazda
1. SharePoint-rendszergazda
1. Intune-rendszergazda
1. Biztonsági olvasó
1. Szolgáltatás-rendszergazda
1. Számlázási adminisztrátor
1. Skype Vállalati verzió-rendszergazda

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az összes globális rendszergazdát és biztonsági rendszergazdát a Privileged Identity Management első lépéseként kezelje, mivel azok a biztonság sérülése esetén a legtöbbet tehetik.

Fontos figyelembe venni, hogy milyen adatok és engedélyek vannak a szervezete számára leginkább kényesek. Előfordulhat például, hogy egyes szervezetek a Privileged Identity Management használatával szeretnék védelemmel ellátni Power BI rendszergazdai szerepkört vagy a csapatuk rendszergazdai szerepkörét, mivel képesek hozzáférni az adataihoz és/vagy módosítani az alapvető munkafolyamatokat.

Ha vannak olyan szerepkörök, amelyekhez vendég felhasználó van rendelve, különösen sebezhetőek a támadásokkal szemben.

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az Privileged Identity Management segítségével minden szerepkört kezelje a vendég felhasználókkal, hogy csökkentse a feltört vendég felhasználói fiókokkal kapcsolatos kockázatokat.

Az olvasói szerepkörök, például a címtár-olvasó, az üzenetsor-olvasó és a biztonsági olvasó úgy vélik, hogy kevésbé fontosak a más szerepkörökhöz képest, mert nem rendelkeznek írási engedéllyel. Azonban láttuk, hogy egyes ügyfelek is védik ezeket a szerepköröket, mert az ilyen fiókokhoz hozzáférő támadók bizalmas adatokat, például személyes adatokat is beolvashatnak. Ezt figyelembe kell vennie annak eldöntése során, hogy a cégen belüli olvasói szerepköröket Privileged Identity Management használatával kell-e felügyelni.

#### <a name="azure-resource-roles"></a>Azure-erőforrásszerepkörök

Annak eldöntéséhez, hogy mely szerepkör-hozzárendeléseket kell felügyelni az Azure Resource Privileged Identity Management használatával, először azonosítania kell a szervezete számára leglényegesebb előfizetéseket/erőforrásokat. Ilyen előfizetések/erőforrások például a következők:

- A legérzékenyebb adatokat üzemeltető erőforrások
- Az alapszintű, az ügyfelek felé irányuló alkalmazások a következőktől függenek:

Ha Ön globális rendszergazda, aki nem tudja, hogy mely előfizetések/erőforrások fontosak, érdemes felvennie a szervezet előfizetésének tulajdonosait, hogy összegyűjtse az egyes előfizetések által kezelt erőforrások listáját. Ezután az előfizetés-tulajdonosokkal együttműködve az erőforrásokat a súlyossági szint alapján csoportosíthatja abban az esetben, ha azok biztonsága megsérül (alacsony, közepes és magas). A súlyossági szint alapján rangsorolni kell az erőforrásokat a Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy a kritikus szolgáltatások előfizetésének/erőforrás-tulajdonosának használatával Privileged Identity Management munkafolyamatot hozzon létre a bizalmas előfizetéseken/erőforrásokon belüli összes szerepkörhöz.

Az Azure-erőforrások Privileged Identity Managementa támogatja az időhöz kötött szolgáltatásfiókok használatát. A szolgáltatási fiókokat ugyanúgy kell kezelnie, mint a normál felhasználói fiókok kezelését.

A nem kritikus fontosságú előfizetések/erőforrások esetében nem szükséges Privileged Identity Management beállítani az összes szerepkörhöz. A tulajdonosi és a felhasználói hozzáférés rendszergazdai szerepköreit azonban továbbra is meg kell Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az összes előfizetés/erőforrás tulajdonosi szerepköreit és felhasználói hozzáférés-felügyeleti szerepköreit Privileged Identity Management használatával kezelje.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Döntse el, hogy melyik szerepkör-hozzárendelés legyen állandó vagy jogosult

Ha úgy döntött, hogy Privileged Identity Management által felügyelni kívánt szerepkörök listáját választotta, el kell döntenie, hogy mely felhasználók kapják meg a jogosult szerepkört és a véglegesen aktív szerepkört. A tartósan aktív szerepkörök a Azure Active Directory és az Azure-erőforrások által hozzárendelt normál szerepkörök, míg a jogosult szerepkörök csak Privileged Identity Management társíthatók.

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az Azure ad-szerepkörökhöz és az Azure-erőforrásokhoz tartozó, az ajánlott [két](../users-groups-roles/directory-emergency-access.md), az állandó globális rendszergazdai szerepkörrel rendelkező, véglegesen aktív hozzárendeléseket is tartalmazzon.

Habár a nulla állandó rendszergazda használatát javasoljuk, időnként nehéz lehet a szervezetek számára ezt azonnal elérni. Az alábbi szempontokat érdemes figyelembe venni a döntés végrehajtásakor:

- Jogosultságszint-emelés gyakorisága – ha a felhasználónak csak egyszer kell kiemelni a Kiemelt jogosultságú hozzárendelést, nem rendelkezhet állandó hozzárendeléssel. Ha azonban a felhasználónak szüksége van a napi feladatra, és a Privileged Identity Management használata jelentősen csökkenti a termelékenységet, akkor az állandó szerepkörnek tekinthetők.
- A szervezetre jellemző esetek – ha az a személy, aki a jogosult szerepkört egy nagyon távoli csapattól vagy egy magas prioritású ügyvezetőtől kapja, a jogosultságszint-emelési folyamattal folytatott kommunikációt és érvényesítést nehéz megállapítani, az állandó szerepkörnek tekinthetők.

> [!TIP]
> : heavy_check_mark: a **Microsoft javasolja** , hogy állítson be ismétlődő hozzáférési felülvizsgálatokat a állandó szerepkör-hozzárendelésekkel rendelkező felhasználók számára (ha van ilyen). További információ az ismétlődő hozzáférési felülvizsgálatról a telepítési terv utolsó szakaszában

### <a name="draft-your-privileged-identity-management-settings"></a>A Privileged Identity Management beállításainak tervezete

A Privileged Identity Management-megoldás implementálása előtt érdemes a szervezet által használt összes Kiemelt szerepkörhöz tartozó Privileged Identity Management-beállításokat előkészíteni. Ez a szakasz néhány példát mutat be az egyes szerepkörök Privileged Identity Management beállításaira (csak referenciák, és a szervezete számára eltérőek lehetnek). Ezeket a beállításokat részletesen ismertetjük a Microsoft javaslataival a táblázatok után.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Azure AD-szerepkörök Privileged Identity Management beállításainak megadása

| Role | MFA megkövetelése | Értesítés | Incidens jegy | Jóváhagyás megkövetelése | Jóváhagyó | Aktiválás időtartama | Állandó rendszergazda |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Globális rendszergazda | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Egyéb globális rendszergazdák | 1 óra | Vészhelyzeti hozzáférési fiókok |
| Exchange-rendszergazda | :heavy_check_mark: | :heavy_check_mark: | x | x | Nincsenek | 2 óra | Nincsenek |
| Segélyszolgálat rendszergazdája | x | x | :heavy_check_mark: | x | Nincsenek | 8 óra | Nincsenek |

#### <a name="privileged-identity-management-settings-for-azure-resource-roles"></a>Az Azure-erőforrás szerepköreinek Privileged Identity Management beállításai

| Role | MFA megkövetelése | Értesítés | Jóváhagyás megkövetelése | Jóváhagyó | Aktiválás időtartama | Aktív rendszergazda | Aktív lejárat | Jogosult lejárat |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Kritikus előfizetések tulajdonosa | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Az előfizetés egyéb tulajdonosai | 1 óra | Nincsenek | n.a. | 3 hónap |
| Kevésbé kritikus előfizetések felhasználói hozzáférésének rendszergazdája | :heavy_check_mark: | :heavy_check_mark: | x | Nincsenek | 1 óra | Nincsenek | n.a. | 3 hónap |
| Virtuális gépek közreműködője | x | :heavy_check_mark: | x | Nincsenek | 3 óra | Nincsenek | n.a. | 6 hónap |

Az alábbi táblázat az egyes beállításokat ismerteti.

| Beállítás | Leírás |
| --- | --- |
| Role | Annak a szerepkörnek a neve, amelynek a beállításait definiálja. |
| MFA megkövetelése | Azt határozza meg, hogy a jogosult felhasználónak az MFA-t kell-e elvégeznie a szerepkör aktiválása előtt.<br/><br/> : heavy_check_mark: a **Microsoft azt javasolja** , hogy minden rendszergazdai szerepkör esetében érvényesítse az MFA-t, különösen akkor, ha a szerepkörök vendég felhasználókkal rendelkeznek. |
| Értesítés | Ha igaz értékre van állítva, a globális rendszergazda, a Kiemelt szerepkörű rendszergazda és a szervezet biztonsági rendszergazdája e-mailben értesítést fog kapni, ha egy jogosult felhasználó aktiválja a szerepkört.<br/><br/>**Megjegyzés:** Egyes szervezetek nem rendelkeznek e-mail-címmel, amely a rendszergazdai fiókjához van kötve, az e-mail-értesítések beszerzéséhez be kell állítania egy alternatív e-mail címet, hogy a rendszergazdák megkapják ezeket az e-maileket. |
| Incidens jegy | Azt határozza meg, hogy a jogosult felhasználónak rögzítenie kell-e az incidens jegy számát a szerepkör aktiválása során. Ez a beállítás segít a szervezetnek az egyes aktiválások belső incidensek számával történő azonosításában a nemkívánatos aktiválások enyhítése érdekében.<br/><br/> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az incidensek jegyei száma alapján a belső rendszerébe kösse a Privileged Identity Management. Ez különösen olyan jóváhagyók számára hasznos, akiknek az aktiváláshoz környezetre van szükségük. |
| Jóváhagyás megkövetelése | Azt határozza meg, hogy a jogosult felhasználónak meg kell-e kapnia a szerepkör aktiválásához szükséges jóváhagyást.<br/><br/> : heavy_check_mark: a **Microsoft javasolja** , hogy állítson be jóváhagyást a legtöbb engedéllyel rendelkező szerepkörökhöz. Az összes Privileged Identity Management ügyfél, a globális rendszergazda, a felhasználói rendszergazda, az Exchange-rendszergazda, a biztonsági rendszergazda és a jelszó-rendszergazda használati mintái alapján a jóváhagyás beállításával leggyakrabban a leggyakoribb szerepkörök. |
| Jóváhagyó | Ha a jogosult szerepkör aktiválásához jóváhagyásra van szükség, sorolja fel azokat a személyeket, akiknek jóvá kell hagyniuk a kérést. Alapértelmezés szerint a Privileged Identity Management úgy állítja be a jóváhagyót, hogy az összes olyan felhasználó legyen, aki Kiemelt szerepkörű rendszergazda, függetlenül attól, hogy azok állandóak vagy jogosultak-e.<br/><br/>**Megjegyzés:** Ha a felhasználó is jogosult egy Azure AD-szerepkörre és a szerepkör jóváhagyójának használatára, nem lesznek képesek jóváhagyni magukat.<br/><br/> : heavy_check_mark: a **Microsoft azt javasolja** , hogy a jóváhagyók közül válassza ki azokat a felhasználókat, akik a leghatékonyabban ismerik az adott szerepkört és annak gyakori felhasználóit, nem pedig globális rendszergazdaként. |
| Aktiválás időtartama | Az az időtartam, ameddig egy felhasználó a szerepkör lejárta előtt aktiválva lesz. |
| Állandó rendszergazda | Azoknak a felhasználóknak a listája, akik a szerepkör állandó rendszergazdája lesznek (soha nem kell aktiválni).<br/><br/> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az összes szerepkörhöz nulla állandó rendszergazdai jogosultságot biztosítson, kivéve a globális rendszergazdákat. További információt a WHO-ban jogosultnak kell lennie, és ki kell a terv végleges aktív szakaszát. |
| Aktív rendszergazda | Az Azure-erőforrások esetében az aktív rendszergazda azoknak a felhasználóknak a listája, akik számára soha nem kell aktiválni a szerepkört. Ez nem olyan állandó rendszergazda, mint az Azure AD-szerepkörök esetében, mert lejárati időt állíthat be, amikor a felhasználó elveszíti ezt a szerepkört. |
| Aktív lejárat | Az Azure erőforrás-szerepkörökhöz tartozó aktív szerepkör-hozzárendelés lejár a beállított időszak után. 15 nap, 1 hónap, 3 hónap, 6 hónap, 1 év vagy tartósan aktív lehetőség közül választhat. |
| Jogosult lejárat | Az Azure-erőforrás szerepköreihez tartozó jogosult szerepkör-hozzárendelés a beállított időszak után lejár. 15 nap, 1 hónap, 3 hónap, 6 hónap, 1 év vagy tartósan jogosult lehetőség közül választhat. |

## <a name="implement-your-solution"></a>A megoldás implementálása

A megfelelő tervezés alapja az, hogy az alkalmazás sikeresen telepíthető Azure Active Directory használatával.  Intelligens biztonságot és integrációt biztosít, amely leegyszerűsíti a bevezetést, miközben csökkenti a sikeres üzembe helyezések idejét.  Ez a kombináció biztosítja, hogy az alkalmazás könnyen integrálható legyen, miközben csökkenti a végfelhasználók idejét.

### <a name="identify-test-users"></a>Tesztelési felhasználók azonosítása

Ez a szakasz a felhasználók és a felhasználók csoportjainak azonosítására használható a megvalósítás ellenőrzéséhez. A tervezés szakaszban kiválasztott beállítások alapján azonosítsa azokat a felhasználókat, akiket szeretne tesztelni az egyes szerepkörökhöz.

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az egyes Azure ad-szerepkörök szolgáltatás-tulajdonosai legyenek a tesztek felhasználóinak, hogy megismerjék a folyamatot, és belső tanácsadóként váljon a kivezetéshez.

A táblázatban azonosíthatja azokat a felhasználókat, akik ellenőrizni fogják, hogy az egyes szerepkörök beállításai működnek-e.

| Szerepkörnév | Felhasználók tesztelése |
| --- | --- |
| &lt;Szerepkörnév&gt; | &lt;A szerepkör tesztelésére szolgáló felhasználók&gt; |
| &lt;Szerepkörnév&gt; | &lt;A szerepkör tesztelésére szolgáló felhasználók&gt; |

### <a name="test-implementation"></a>Tesztelés megvalósítása

Most, hogy azonosította a felhasználók tesztelését, ezzel a lépéssel konfigurálhatja a Privileged Identity Management a tesztelési felhasználók számára. Ha a szervezete Privileged Identity Management munkafolyamatot kíván beépíteni a saját belső alkalmazásba ahelyett, hogy a Azure Portal Privileged Identity Management kellene használnia, a [Graph API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root)-ban a Privileged Identity Management összes művelete is támogatott.

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Privileged Identity Management konfigurálása Azure AD-szerepkörökhöz

1. [Konfigurálja az Azure ad szerepkör beállításait](pim-how-to-change-default-settings.md) a tervezett beállítások alapján.

1. Navigáljon az **Azure ad-szerepkörökhöz**, kattintson a **szerepkörök**elemre, majd válassza ki az imént konfigurált szerepkört.

1. Ha a tesztelési felhasználók csoportjának már állandó rendszergazdája van, jogosult lehet a keresésre, és az állandóról jogosultra konvertálhatja őket, ha a három pontra kattint a sorban. Ha még nem rendelkeznek szerepkör-hozzárendelésekkel, létrehozhat [egy új jogosult hozzárendelést](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Ismételje meg a 1-3. lépést a tesztelni kívánt összes szerepkörhöz.

1. Miután beállította a felhasználók tesztelését, el kell küldenie az [Azure ad-szerepkör aktiválásához](pim-how-to-activate-role.md)szükséges hivatkozást.

#### <a name="configure-privileged-identity-management-for-azure-resource-roles"></a>Privileged Identity Management konfigurálása Azure-beli erőforrás-szerepkörökhöz

1. [Konfigurálja az Azure erőforrás-szerepkör beállításait](pim-resource-roles-configure-role-settings.md) egy olyan szerepkörhöz, amely egy adott előfizetésben vagy erőforrásban szeretne tesztelni.

1. Navigáljon az előfizetéshez tartozó **Azure-erőforrásokhoz** , és kattintson a **szerepkörök**elemre, és válassza ki az imént konfigurált szerepkört.

1. Ha a tesztelési felhasználók csoportjának már aktív rendszergazdája van, akkor rákereshet rájuk, és [frissítheti a szerepkör-hozzárendelését](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Ha még nem rendelkeznek szerepkörrel, [új szerepkört rendelhet hozzá](pim-resource-roles-assign-roles.md#assign-a-role).

1. Ismételje meg a 1-3. lépést a tesztelni kívánt összes szerepkörhöz.

1. Miután beállította a felhasználók tesztelését, el kell küldenie az [Azure-erőforrás szerepkörének aktiválásához](pim-resource-roles-activate-your-roles.md)szükséges hivatkozást.

Ezt a szakaszt kell használnia annak ellenőrzéséhez, hogy a szerepkörökhöz beállított összes konfiguráció megfelelően működik-e. A tesztek dokumentálása a következő táblázattal végezhető el. Az érintett felhasználókkal folytatott kommunikáció optimalizálásához ezt a szakaszt is érdemes használni.

| Role | Az aktiválás során várt viselkedés | Tényleges eredmények |
| --- | --- | --- |
| Globális rendszergazda | (1) MFA megkövetelése<br/>(2) jóváhagyás szükséges<br/>(3) a jóváhagyó értesítést kap, és jóváhagyhatja<br/>(4) a szerepkör az előre beállított idő után lejár |  |
| Az előfizetés *X* tulajdonosa | (1) MFA megkövetelése<br/>(2) a jogosult hozzárendelés a beállított időszak után lejár |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Privileged Identity Management kommunikál az érintett felekkel

A Privileged Identity Management üzembe helyezése további lépéseket fog bevezetni a Kiemelt szerepkörök felhasználói számára. Bár a Privileged Identity Management nagy mértékben csökkenti a Kiemelt identitásokkal kapcsolatos biztonsági problémákat, a változást a szervezet teljes körű üzembe helyezése előtt hatékonyan kell közölni. A befolyásolt rendszergazdák számától függően a szervezetek gyakran belső dokumentumot, videót vagy e-mailt hoznak létre a változásról. A közleményekben gyakran szerepelnek a következők:

- Mi az a PIM?
- Milyen előnyökkel jár a szervezet számára
- Kik lesznek érintettek
- A PIM kidobásakor
- Milyen további lépésekre lesz szükség a felhasználók számára a szerepkör aktiválásához
    - A dokumentációra mutató hivatkozásokat kell elküldenie:
    - [Azure AD-szerepkörök aktiválása](pim-how-to-activate-role.md)
    - [Azure-erőforrás szerepköreinek aktiválása](pim-resource-roles-activate-your-roles.md)
- A PIM-vel kapcsolatos esetleges problémákért forduljon a kapcsolattartási adatokhoz vagy az ügyfélszolgálathoz

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az ügyfélszolgálati/támogatási csapatával időt állítsanak be a Privileged Identity Management munkafolyamaton keresztül (ha a szervezet belső informatikai támogatási csapattal rendelkezik). Adja meg a megfelelő dokumentációt, valamint a kapcsolattartási adatait.

### <a name="move-to-production"></a>Átállás éles üzemre

A tesztelés befejezése és sikeres végrehajtása után helyezze át Privileged Identity Management az éles környezetbe úgy, hogy a tesztelési fázisok összes lépését megismétli a Privileged Identity Management-konfigurációban meghatározott szerepkörök összes felhasználója számára. Az Azure AD-szerepkörökhöz Privileged Identity Management a szervezetek gyakran tesztelik és kivezetik Privileged Identity Management a globális rendszergazdák számára, mielőtt tesztelik és kivezetik a többi szerepkörhöz Privileged Identity Management. Az Azure-erőforrások mellett a szervezetek általában egy Azure-előfizetést Privileged Identity Management egyszerre tesztelnek és állítanak elő.

### <a name="in-the-case-a-rollback-is-needed"></a>Ha visszaállításra van szükség

Ha Privileged Identity Management az éles környezetben nem tudott megfelelően dolgozni, a következő visszaállítási lépések segítséget nyújthatnak a megfelelő állapot visszaállításához a Privileged Identity Management beállítása előtt:

#### <a name="azure-ad-roles"></a>Azure AD-szerepkörök

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Nyissa meg **Azure ad Privileged Identity Management**.
1. Kattintson az **Azure ad-szerepkörök** elemre, majd a **szerepkörök**elemre.
1. Minden konfigurált szerepkörnél kattintson a három pontra (**..**.) a jogosult hozzárendeléssel rendelkező összes felhasználó esetében.
1. Kattintson a **véglegesvé tétele** lehetőségre a szerepkör-hozzárendelés véglegesvé tételéhez.

#### <a name="azure-resource-roles"></a>Azure-erőforrásszerepkörök

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Nyissa meg **Azure ad Privileged Identity Management**.
1. Kattintson az **Azure-erőforrások** elemre, majd kattintson egy előfizetésre vagy erőforrásra, amelyet vissza szeretne állítani.
1. Kattintson a **szerepkörök**elemre.
1. Minden konfigurált szerepkörnél kattintson a három pontra (**..**.) a jogosult hozzárendeléssel rendelkező összes felhasználó esetében.
1. Kattintson a **véglegesvé tétele** lehetőségre a szerepkör-hozzárendelés véglegesvé tételéhez.

## <a name="next-steps-after-deploying"></a>A telepítés utáni következő lépések

A Privileged Identity Management éles környezetben történő üzembe helyezése jelentős előrelépést jelent a szervezet emelt szintű identitásának védelme szempontjából. A Privileged Identity Management üzembe helyezése további Privileged Identity Management funkciókat is tartalmaz, amelyeket a biztonság és a megfelelőség érdekében érdemes használni.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Az emelt szintű hozzáférés védelme Privileged Identity Management riasztások használatával

A szervezete hatékonyabb védelme érdekében Privileged Identity Management beépített riasztási funkcióit kell használnia. További információ: [biztonsági riasztások](pim-how-to-configure-security-alerts.md#security-alerts). Ezek a riasztások a következők: a rendszergazdák nem Kiemelt szerepköröket használnak, a szerepköröket a Privileged Identity Managementon kívül kell kiosztani, a szerepköröket túl gyakran aktiválják. A szervezet teljes körű védelem érdekében rendszeresen menjen végig a riasztások listáján, és javítsa ki a problémákat. A riasztásokat a következő módon tekintheti meg és javíthatja:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Nyissa meg **Azure ad Privileged Identity Management**.
1. Kattintson az **Azure ad-szerepkörök** elemre, majd a **riasztások**elemre.

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az összes riasztást azonnal feltüntetve. Közepes és alacsony súlyosságú riasztások esetén tájékoztatni kell, és módosítania kell a módosításokat, ha úgy gondolja, hogy biztonsági fenyegetés van.

Ha az adott riasztások bármelyike nem hasznos, vagy nem vonatkozik a szervezetére, a riasztást bármikor elvetheti a riasztások lapon. Később bármikor visszaállíthatja ezt az elbocsátást az Azure AD beállítások lapján.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Ismétlődő hozzáférési felülvizsgálatok beállítása a szervezet emelt szintű identitásának rendszeres auditálásához

A hozzáférési felülvizsgálatok lehetővé teszik a legjobb megoldást arra, hogy a Kiemelt szerepkörökkel vagy konkrét felülvizsgálók által hozzárendelt felhasználókat Kérdezzen rá, hogy az egyes felhasználóknak szükségük van-e a privilegizált identitásra. A hozzáférési felülvizsgálatok nagyszerűek, ha csökkenteni szeretné a támadási felületet, és megfelelőnek kell lennie. A hozzáférési felülvizsgálat elindításával kapcsolatos további információkért tekintse meg az [Azure ad-szerepkörök hozzáférési](pim-how-to-start-security-review.md) felülvizsgálatait és az [Azure Resource roles-hozzáférési](pim-resource-roles-start-access-review.md)felülvizsgálatokat. Egyes szervezetek esetében az időszakos hozzáférési felülvizsgálat elvégzése szükséges a törvények és szabályozások betartásához, míg mások esetében a hozzáférési felülvizsgálat a legjobb módszer a legalacsonyabb jogosultsági szint érvényesítésére a szervezeten belül.

> [!TIP]
> : heavy_check_mark: a **Microsoft javasolja** , hogy negyedévente hozzáférési felülvizsgálatokat állítson be az összes Azure ad-és Azure-erőforrás-szerepkörhöz.

A legtöbb esetben az Azure AD-szerepkörökhöz tartozó felülvizsgáló maga a felhasználó, miközben az Azure-erőforrás szerepköreihez tartozó felülvizsgáló az előfizetés tulajdonosa, amely a szerepkör. Gyakran előfordul azonban, hogy a vállalatok olyan Kiemelt jogosultságú fiókkal rendelkeznek, amelyek nem kapcsolódnak egy adott személy e-mail-címéhez. Ezekben az esetekben senki nem olvas és nem tekinti át a hozzáférést.

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy adjon hozzá egy másodlagos e-mail-címet minden olyan fiókhoz, amelynek Kiemelt szerepkörű hozzárendelései vannak, amelyek nem kapcsolódnak rendszeresen ellenőrzött e-mail-címekhez.

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>A biztonság és a megfelelőség javítása érdekében hozza ki a legtöbbet a naplóból

A napló az a hely, ahol naprakész maradhat, és megfelel a szabályozásoknak. A Privileged Identity Management jelenleg a szervezeti előzmények 30 napos előzményeit tárolja a naplójában, beleértve a következőket:

- A jogosult szerepkörök aktiválása/inaktiválása
- Szerepkör-hozzárendelési tevékenységek Privileged Identity Managementon belül és kívül
- A szerepkör beállításaiban bekövetkezett változások
- A szerepkör-aktiválásra vonatkozó kérés/jóváhagyás/megtagadás tevékenységek jóváhagyási beállítással
- Frissítés riasztásokra

Ezeket a naplókat akkor érheti el, ha Ön globális rendszergazda vagy Kiemelt szerepkörű rendszergazda. További információ: Azure [ad-szerepkörök naplózási előzményei](pim-how-to-use-audit-log.md) és [Naplózási előzmények Azure-beli erőforrás-szerepkörökhöz](azure-pim-resource-rbac.md).

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy legalább egy rendszergazdát beolvasson az összes naplózási eseményről hetente, és havi rendszerességgel exportálja a naplózási eseményeket.

Ha hosszabb ideig szeretné automatikusan tárolni a naplózási eseményeket, Privileged Identity Management naplóját a rendszer automatikusan szinkronizálja az [Azure ad-naplókba](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy állítsa be az [Azure-napló figyelését](../reports-monitoring/concept-activity-logs-azure-monitor.md) egy Azure Storage-fiókban lévő naplózási események archiválásához a biztonság és a megfelelőség érdekében.
