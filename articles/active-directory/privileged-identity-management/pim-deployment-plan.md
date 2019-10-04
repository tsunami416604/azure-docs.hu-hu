---
title: Privileged Identity Management üzembe helyezése (PIM) – Azure Active Directory | Microsoft Docs
description: Útmutatás Azure AD Privileged Identity Management (PIM) üzembe helyezésének megtervezéséhez.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/08/2019
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ff46a693766ab12b53baba1ad8e4a56e174a076
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804484"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Azure AD Privileged Identity Management (PIM) üzembe helyezése

Ez a részletes útmutató ismerteti, hogyan tervezheti meg Azure Active Directory (Azure AD) Privileged Identity Management (PIM) üzembe helyezését a szervezetben.

> [!TIP]
> A dokumentum teljes egészében a következőként megjelölt elemeket fogja látni:
> 
> :heavy_check_mark: **A Microsoft javasolja**
> 
> Ezek általános javaslatok, és csak akkor kell megvalósítani, ha az adott vállalati igényekre vonatkoznak.

## <a name="step-1-learn-about-pim"></a>1\.lépés Tudnivalók a PIM-ról

Azure AD Privileged Identity Management (PIM) segítségével felügyelheti az Azure AD, az Azure-erőforrások és más Microsoft online-szolgáltatások Kiemelt felügyeleti szerepköreit. Egy olyan világban, ahol jogosultságokkal rendelkező identitások vannak hozzárendelve és elfelejtve, a PIM olyan megoldásokat kínál, mint az igény szerinti hozzáférés, a jóváhagyási munkafolyamatok kérelmezése és a teljes körűen integrált hozzáférési felülvizsgálatok, így azonosíthatja, feltárhatja és megakadályozhatja a jogosultsági szintű rosszindulatú tevékenységeket a szerepkörök valós idejűek. A PIM üzembe helyezése a Kiemelt szerepköröknek a szervezeten belüli kezeléséhez nagy mértékben csökkenti a kockázatot, miközben értékes információkhoz vezet a Kiemelt szerepkörök tevékenységeivel kapcsolatban.

### <a name="business-value-of-pim"></a>PIM üzleti értéke

A [minimális jogosultsági szintű hozzáférés](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) és az igény szerinti hozzáférés elvét kikényszerítve **kezelheti a szervezet kockázatkezelését** . Azáltal, hogy minimalizálja a felhasználók állandó hozzárendeléseinek számát a Kiemelt szerepkörökhöz, valamint a jóváhagyások érvényesítését és az MFA-t a jogosultságszint-emelésre, nagy mértékben csökkentheti az emelt szintű hozzáféréshez kapcsolódó biztonsági kockázatokat a szervezetében. A minimális jogosultságok érvényesítése és az igény szerinti hozzáférés is lehetővé teszi, hogy megtekintse a Kiemelt szerepkörökhöz való hozzáférés előzményeit, és nyomon követhesse a biztonsági problémákat.

A **címek megfelelősége és irányítása** – a PIM üzembe helyezése létrehoz egy környezetet a folyamatos identitások irányításához. A magas jogosultsági szintű identitások igény szerinti emelése lehetővé teszi a PIM számára, hogy nyomon kövesse a szervezet privilegizált hozzáférési tevékenységeit. A szervezeten belüli állandó és jogosult szerepkörök összes hozzárendelésére vonatkozó értesítéseket is megtekinthet és fogadhat. A hozzáférési felülvizsgálattal rendszeresen ellenőrizheti és eltávolíthatja a szükségtelen jogosultságú identitásokat, és gondoskodhat arról, hogy a szervezet megfeleljen a legszigorúbb identitási, hozzáférési és biztonsági szabványoknak.

**Csökkentse a költségeket** – csökkentse a hatékonyság, az emberi hibák és a biztonsági problémák megszüntetését a PIM megfelelő üzembe helyezésével. A nettó eredmény az emelt szintű identitásokkal kapcsolatos számítógépes bűncselekmények csökkentése, ami költséges és nehézkes a helyreállításhoz. A PIM emellett segít a szervezetnek a szabályok és szabványok betartása érdekében a naplózási hozzáférési információkhoz kapcsolódó költségeket csökkenteni.

További információkért lásd: [Mi az Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="licensing-requirements"></a>Licencelési követelmények

A PIM használatához a címtárnak a következő fizetős vagy próbaverziós licencek egyikével kell rendelkeznie:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- M5 Microsoft 365

További információ: [a PIM használatára vonatkozó licencfeltételek](subscription-requirements.md).

### <a name="key-pim-terminology"></a>Key PIM-terminológia

| Kifejezés vagy fogalom | Leírás |
| --- | --- |
| támogatható | Szerepkör-hozzárendelés, amely megköveteli, hogy a felhasználó egy vagy több műveletet hajtson végre a szerepkör használatához. Ha egy felhasználó jogosult a szerepkörre, az azt jelenti, hogy aktiválhatja a szerepkört, amikor kiemelt feladatokat kell elvégeznie. A hozzáférés nem különbözik a jogosult szerepkör-hozzárendeléssel rendelkező valakinek. Az egyetlen különbség, hogy néhány embernek nincs szüksége arra, hogy minden alkalommal hozzáférjen. |
| aktiválás | Egy vagy több művelet végrehajtásának folyamata olyan szerepkör használatára, amelyhez a felhasználó jogosult. A műveletek tartalmazhatják a többtényezős hitelesítés (MFA) ellenőrzését, üzleti indoklást biztosítanak, vagy a kijelölt jóváhagyók jóváhagyását kérik. |
| igény szerinti (JIT) hozzáférés | Olyan modell, amelyben a felhasználók ideiglenes engedélyeket kapnak a Kiemelt feladatok végrehajtásához, ami megakadályozza, hogy a rosszindulatú vagy jogosulatlan felhasználók hozzáférjenek az engedélyek lejárta után. A hozzáférés csak akkor adható meg, ha a felhasználóknak szüksége van rá. |
| a minimális jogosultsági szintű hozzáférés elve | Ajánlott biztonsági eljárás, amelyben minden felhasználó csak a végrehajtásra jogosult feladatok végrehajtásához szükséges minimális jogosultságokkal rendelkezik. Ez a gyakorlat lekicsinyíti a globális rendszergazdák számát, és bizonyos helyzetekben adott rendszergazdai szerepköröket használ. |

További információ: [terminológia](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-pim-works"></a>A PIM működésének magas szintű áttekintése

1. A PIM úgy van beállítva, hogy a felhasználók jogosultak legyenek a Kiemelt szerepkörökre.
1. Ha egy jogosult felhasználónak a Kiemelt szerepkört kell használnia, aktiválja a szerepkört a PIM-ben.
1. A szerepkörhöz konfigurált PIM-beállításoktól függően a felhasználónak végre kell hajtania bizonyos lépéseket (például többtényezős hitelesítés végrehajtása, jóváhagyás beszerzése vagy ok megadása).)
1. Miután a felhasználó sikeresen aktiválta a szerepkört, egy előre konfigurált időszakot kapnak a szerepkörhöz.
1. A rendszergazdák megtekinthetik az összes PIM-tevékenység előzményeit a naplóban. Emellett a PIM-funkciók, például a hozzáférési felülvizsgálatok és a riasztások révén tovább biztonságossá tehetik a bérlőket, és megfelelnek a megfelelőségnek.

További információkért lásd: [Mi az Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-pim"></a>A PIM által felügyelhető szerepkörök

**Azure ad-szerepkörök** – ezek a szerepkörök mind Azure Active Directory (például globális rendszergazda, Exchange-rendszergazda és biztonsági rendszergazda). A szerepkörökről és azok funkcióival kapcsolatos további információkért tekintse meg [Azure Active Directory a rendszergazdai szerepkör engedélyei](../users-groups-roles/directory-assign-admin-roles.md)című részt. A rendszergazdák által hozzárendelt szerepkörök meghatározásával kapcsolatos segítségért tekintse meg a [legkevésbé Kiemelt szerepkörök feladat szerint feladatot](../users-groups-roles/roles-delegate-by-task.md).

**Azure-erőforrás szerepkörei** – ezek a szerepkörök egy Azure-erőforráshoz, erőforráscsoporthoz, előfizetéshez vagy felügyeleti csoportjához vannak társítva. A PIM igény szerinti hozzáférést biztosít a beépített szerepkörökhöz, például a tulajdonoshoz, a felhasználói hozzáférés-adminisztrátorhoz és a Közreműködőhöz, valamint [egyéni szerepkörökhöz](../../role-based-access-control/custom-roles.md). Az Azure erőforrás-szerepkörökkel kapcsolatos további információkért lásd: [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md).

További információ: [a PIM-ben nem felügyelhető szerepkörök](pim-roles.md).

## <a name="step-2-plan-your-deployment"></a>2\.lépés Az üzembe helyezés megtervezése

Ez a szakasz arra összpontosít, hogy mit kell tennie a PIM üzembe helyezése előtt a szervezetben. Fontos, hogy kövesse az utasításokat, és Ismerje meg az ebben a szakaszban ismertetett fogalmakat, mivel útmutatást nyújt a szervezet emelt szintű identitásához igazított legjobb terv létrehozásához.

### <a name="identify-your-stakeholders"></a>Az érintettek azonosítása

A következő szakasz segítségével azonosíthatja a projektben érintett összes érintettet, és ki kell jelentkeznie, ellenőriznie vagy tájékoztatnia kell. Külön táblákat tartalmaz a PIM Azure AD-szerepkörökhöz és a PIM Azure-beli erőforrás-szerepkörökhöz való telepítéséhez. Adja hozzá az érintetteket az alábbi táblázathoz a szervezete számára megfelelő módon.

- TEHÁT = kijelentkezés ezen a projekten
- R = a projekt áttekintése és bemenet megadása
- I = tájékozott a projektről

#### <a name="stakeholders-pim-for-azure-ad-roles"></a>Érintettek PIM Azure AD-szerepkörökhöz

| Name (Név) | Role | Action |
| --- | --- | --- |
| Név és e-mail | **Identity Architect vagy Azure globális rendszergazda**<br/>Az Identity Management csapatának képviselője, amely meghatározza, hogy a változás hogyan igazodik a szervezet alapvető Identity Management-infrastruktúrához. | SO/R/I |
| Név és e-mail | **Szolgáltatás tulajdonosa/soros kezelő**<br/>Egy szolgáltatás vagy szolgáltatási csoport informatikai tulajdonosai. Ezek kulcsfontosságúak a döntések meghozatalában, és segítenek a PIM kivezetésében a csapatuk számára. | SO/R/I |
| Név és e-mail | **Biztonsági tulajdonos**<br/>A biztonsági csapat képviselője, amely kijelentkezhet, hogy a terv megfelel a szervezete biztonsági követelményeinek. | SO/R |
| Név és e-mail | **INFORMATIKAI támogatás kezelője/ügyfélszolgálata**<br/>Az informatikai támogatási szervezet egyik képviselője, aki a változás támogatását nyújtja a helpdesk szemszögéből. | R/I |
| A kísérleti felhasználók neve és e-mail-címe | **Kiemelt szerepkörű felhasználók**<br/>Azon felhasználók csoportja, amelyeken a Privileged Identity Management implementálva van. Tudnia kell, hogyan kell aktiválni a szerepköreiket a PIM implementálása után. | I |

#### <a name="stakeholders-pim-for-azure-resource-roles"></a>Érintettek PIM Azure-erőforrás-szerepkörökhöz

| Name (Név) | Role | Action |
| --- | --- | --- |
| Név és e-mail | **Előfizetés/erőforrás tulajdonosa**<br/>Az egyes előfizetésekhez vagy erőforrásokhoz tartozó, a PIM-t központilag telepíteni kívánó tulajdonos | SO/R/I |
| Név és e-mail | **Biztonsági tulajdonos**<br/>A biztonsági csapat képviselője, amely kijelentkezhet, hogy a terv megfelel a szervezete biztonsági követelményeinek. | SO/R |
| Név és e-mail | **INFORMATIKAI támogatás kezelője/ügyfélszolgálata**<br/>Az informatikai támogatási szervezet egyik képviselője, aki a változás támogatását nyújtja a helpdesk szemszögéből. | R/I |
| A kísérleti felhasználók neve és e-mail-címe | **RBAC szerepkör-felhasználók**<br/>Azon felhasználók csoportja, amelyeken a Privileged Identity Management implementálva van. Tudnia kell, hogyan kell aktiválni a szerepköreiket a PIM implementálása után. | I |

### <a name="enable-pim"></a>PIM engedélyezése

A tervezési folyamat részeként először meg kell adnia és engedélyeznie kell a PIM-t a [PIM-dokumentum használatának megkezdése](pim-getting-started.md)után. A PIM engedélyezése olyan funkciók elérését teszi lehetővé, amelyek kifejezetten az üzembe helyezéssel kapcsolatos segítségre vannak kialakítva.

Ha a cél a PIM Azure-erőforrásokhoz való üzembe helyezése, kövesse a [felderített Azure-erőforrásokat a PIM-dokumentumban való felügyelethez](pim-resource-roles-discover-resources.md). Csak az egyes erőforrások, erőforráscsoportok és előfizetések tulajdonosai tudják felderíteni őket a PIM-ben. Ha Ön globális rendszergazda, aki az Azure-erőforrások számára kívánja üzembe helyezni a PIM-t, az [összes Azure-előfizetés kezeléséhez megemelheti a hozzáférést](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) , hogy a felderítéshez a címtárban lévő összes Azure-erőforráshoz hozzáférjen. Azt javasoljuk azonban, hogy az egyes előfizetésekhez tartozó tulajdonosok jóváhagyása előtt az erőforrásaikat a PIM-rel kezelhesse.

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

1. A szervezete Kiemelt szerepkörrel rendelkező szerepköreinek listázása. A [PIM varázsló](pim-security-wizard.md#run-the-wizard) segítségével a következőhöz hasonló oldalra juthat el.

    ![Kiemelt szerepkörök felderítése ablaktábla, amely kiemelt szerepkörökkel rendelkezik](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. A szervezet összes globális rendszergazdája számára Ismerje meg, hogy miért van szükségük a szerepkörre. A korábbi dokumentáció elolvasása alapján, ha a személy feladatát egy vagy több részletes rendszergazdai szerepkör is elvégezheti, távolítsa el őket a globális rendszergazdai szerepkörből, és ennek megfelelően végezze el a hozzárendeléseket Azure Active Directory (a következő hivatkozásként: A Microsoft jelenleg csak a globális rendszergazdai szerepkörrel rendelkező 10 rendszergazdával rendelkezik. További információt a [Microsoft a PIM használatáról](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access).

1. Az összes többi Azure AD-szerepkör esetében tekintse át a hozzárendelések listáját, azonosítsa azokat a rendszergazdákat, akiknek már nincs szüksége a szerepkörre, majd távolítsa el őket a hozzárendeléseiről.

A 3. és 4. lépés automatizálásához használhatja a hozzáférés-felülvizsgálati funkciót a PIM-ben. Az [Azure ad-szerepkörök a PIM-ben való hozzáférési felülvizsgálatának elindítása](pim-how-to-start-security-review.md)című rész lépéseit követve hozzáférési felülvizsgálatot állíthat be minden olyan Azure ad-szerepkörhöz, amely egy vagy több taggal rendelkezik.

![Hozzáférési felülvizsgálati ablaktábla létrehozása Azure AD-szerepkörökhöz](./media/pim-deployment-plan/create-access-review.png)

A véleményezőket a **Tagok (saját)** értékre kell beállítani. Ez egy e-mailt küld a szerepkör összes tagjának, hogy megbizonyosodjon róla, hogy szükségük van-e a hozzáférésre. A speciális beállításokban be kell kapcsolni a **jóváhagyás** szükségességét is, hogy a felhasználók tudják, miért van szükségük a szerepkörre. Ezen információk alapján eltávolíthatja a felhasználókat a szükségtelen szerepkörökből, és a globális rendszergazdák esetében részletesebb rendszergazdai szerepköröket delegálhat.

A hozzáférési felülvizsgálatok az e-mailekre támaszkodva értesítik a felhasználókat, hogy vizsgálják át a szerepkörük hozzáférését. Ha olyan Kiemelt jogosultságú fiókkal rendelkezik, amelyekhez nem tartozik e-mailek társítva, akkor ügyeljen arra, hogy a másodlagos e-mail-mezőket feltöltse a fiókoknál. További információ: [proxyAddresses attribútum az Azure ad-ben](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Azure-erőforrásszerepkörök

Az Azure-előfizetések és-erőforrások esetében beállíthat egy hasonló hozzáférési felülvizsgálati folyamatot, amellyel áttekintheti a szerepköröket az egyes előfizetésekben vagy erőforrásokban. Ennek a folyamatnak a célja az egyes előfizetésekhez vagy erőforrásokhoz csatolt tulajdonosi és felhasználói hozzáférési rendszergazdai hozzárendelések csökkentése, valamint a szükségtelen hozzárendelések eltávolítása. A szervezetek azonban gyakran delegálják ezeket a feladatokat az egyes előfizetések vagy erőforrások tulajdonosának, mert jobban megértették az adott szerepköröket (különösen az egyéni szerepköröket).

Ha a globális rendszergazdai szerepkörrel rendelkező rendszergazda a PIM Azure-erőforrásokhoz való üzembe helyezését kísérli meg a szervezetében, akkor az [összes Azure-előfizetés kezeléséhez megemelheti a hozzáférést](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) az egyes előfizetésekhez való hozzáféréshez. Ezután megkeresheti az egyes előfizetések tulajdonosait, és dolgozhat velük a szükségtelen hozzárendelések eltávolításához és a tulajdonosi szerepkör-hozzárendelés minimalizálásához.

Az Azure-előfizetésekhez tartozó tulajdonosi szerepkörrel rendelkező felhasználók az Azure- [erőforrások hozzáférési felülvizsgálatait](pim-resource-roles-start-access-review.md) is használhatják az Azure ad-szerepkörökhöz korábban ismertetett eljáráshoz hasonló, szükségtelen szerepkör-hozzárendelések naplózására és eltávolítására.

### <a name="decide-which-role-assignments-should-be-protected-by-pim"></a>Döntse el, hogy a PIM mely szerepkör-hozzárendeléseket kívánja védeni

Miután feltöltötte a Kiemelt szerepkör-hozzárendeléseket a szervezetében, el kell döntenie, hogy mely szerepköröket kell a PIM szolgáltatással védelemmel ellátni.

Ha a PIM védi a szerepkört, a hozzájuk rendelt jogosult felhasználóknak a szerepkör által biztosított jogosultságokat kell használniuk. A jogosultságszint-emelési folyamat magában foglalhatja a jóváhagyás beszerzését, a többtényezős hitelesítés végrehajtását és/vagy az aktiválásuk okát. A PIM az értesítések és a PIM és az Azure AD naplózási eseménynaplók használatával is nyomon követheti a jogosultságszint-emeléseket.

Ha kiválasztja, hogy a PIM-mel milyen szerepköröket kell védelemmel ellátni, az egyes szervezetek esetében pedig különbözőek lehetnek. Ez a szakasz az Azure AD-vel és az Azure-erőforrás-szerepkörökkel kapcsolatos ajánlott eljárásokat ismerteti.

#### <a name="azure-ad-roles"></a>Azure AD-szerepkörök

Fontos, hogy rangsorolja a legtöbb engedélyekkel rendelkező Azure AD-szerepkörök védelmét. Az összes PIM-ügyfél használati mintái alapján a PIM által kezelt első 10 Azure AD-szerepkör a következő:

1. Globális rendszergazda
1. Biztonsági rendszergazda
1. Felhasználói adminisztrátor
1. Exchange-rendszergazda
1. SharePoint-rendszergazda
1. Intune-rendszergazda
1. Biztonsági olvasó
1. Szolgáltatásadminisztrátor
1. Számlázási adminisztrátor
1. Skype Vállalati verzió rendszergazdája

> [!TIP]
> :heavy_check_mark: A **Microsoft azt javasolja** , hogy az összes globális rendszergazdát és biztonsági rendszergazdát a PIM használatával az első lépésként kezelje, mivel ezek a legtöbbet képesek a sérülésre.

Fontos figyelembe venni, hogy milyen adatok és engedélyek vannak a szervezete számára leginkább kényesek. Előfordulhat például, hogy egyes szervezetek a PIM használatával szeretnék védelemmel ellátni Power BI rendszergazdai szerepkört vagy a csapatuk rendszergazdai szerepkörét, mivel képesek hozzáférni az adataihoz és/vagy módosítani az alapvető munkafolyamatokat.

Ha vannak olyan szerepkörök, amelyekhez vendég felhasználó van rendelve, különösen sebezhetőek a támadásokkal szemben.

> [!TIP]
> :heavy_check_mark: A **Microsoft azt javasolja** , hogy a PIM használatával minden szerepkört kezelje a vendég felhasználókkal, hogy csökkentse a feltört vendég felhasználói fiókokkal kapcsolatos kockázatokat.

Az olvasói szerepkörök, például a címtár-olvasó, az üzenetsor-olvasó és a biztonsági olvasó úgy vélik, hogy kevésbé fontosak a más szerepkörökhöz képest, mert nem rendelkeznek írási engedéllyel. Azonban láttuk, hogy egyes ügyfelek is védik ezeket a szerepköröket, mert az ilyen fiókokhoz hozzáférő támadók képesek lehetnek bizalmas adatok (például személyazonosításra alkalmas adatok) olvasására. Ezt figyelembe kell vennie annak eldöntése során, hogy a cégen belüli olvasói szerepköröket a PIM használatával kell-e felügyelni.

#### <a name="azure-resource-roles"></a>Azure-erőforrásszerepkörök

Annak eldöntéséhez, hogy mely szerepkör-hozzárendeléseket kell a PIM használatával felügyelni az Azure-erőforráshoz, először azonosítania kell a szervezete számára létfontosságú előfizetéseket/erőforrásokat. Ilyen előfizetések/erőforrások például a következők:

- A legérzékenyebb adatokat üzemeltető erőforrások
- Az alapszintű, az ügyfelek felé irányuló alkalmazások a következőktől függenek:

Ha Ön globális rendszergazda, aki nem tudja, hogy mely előfizetések/erőforrások fontosak, érdemes felvennie a szervezet előfizetésének tulajdonosait, hogy összegyűjtse az egyes előfizetések által kezelt erőforrások listáját. Ezután az előfizetés-tulajdonosokkal együttműködve az erőforrásokat a súlyossági szint alapján csoportosíthatja abban az esetben, ha azok biztonsága megsérül (alacsony, közepes és magas). Ezen súlyossági szint alapján rangsorolni kell az erőforrások PIM-vel való felügyeletét.

> [!TIP]
> :heavy_check_mark: A **Microsoft azt javasolja** , hogy a kritikus szolgáltatások előfizetésének/erőforrás-tulajdonosainak a PIM-munkafolyamatot a bizalmas előfizetésekben/erőforrásokban lévő összes szerepkörhöz állítsa be.

A PIM for Azure-erőforrások támogatják az időhöz kötött szolgáltatásfiókok használatát. A szolgáltatási fiókokat ugyanúgy kell kezelnie, mint a normál felhasználói fiókok kezelését.

A nem kritikus fontosságú előfizetések/erőforrások esetében nem kell beállítania a PIM-t az összes szerepkörhöz. A tulajdonosi és a felhasználói hozzáférés rendszergazdai szerepköreit azonban továbbra is a PIM-mel kell védetté tenni.

> [!TIP]
> :heavy_check_mark: A **Microsoft azt javasolja** , hogy a PIM segítségével kezelje a tulajdonosi szerepköröket és a felhasználói hozzáférés rendszergazdai szerepköreit az összes előfizetés/erőforrás számára.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Döntse el, hogy melyik szerepkör-hozzárendelés legyen állandó vagy jogosult

Ha úgy döntött, hogy a PIM által kezelendő szerepkörök listáját választotta, el kell döntenie, hogy mely felhasználók kapják meg a jogosult szerepkört és a véglegesen aktív szerepkört. A tartósan aktív szerepkörök a Azure Active Directory és az Azure-erőforrások által hozzárendelt normál szerepkörök, míg a jogosult szerepkörök csak a PIM-ben rendelhetők hozzá.

> [!TIP]
> :heavy_check_mark: A **Microsoft azt javasolja** , hogy az Azure ad-szerepkörökhöz és az Azure-erőforrásokhoz tartozó, az ajánlott [két](../users-groups-roles/directory-emergency-access.md), az állandó globális rendszergazdai jogosultságokkal nem rendelkező, véglegesen aktív hozzárendeléseket is tartalmazzon. szerepkör.

Habár a nulla állandó rendszergazda használatát javasoljuk, időnként nehéz lehet a szervezetek számára ezt azonnal elérni. Az alábbi szempontokat érdemes figyelembe venni a döntés végrehajtásakor:

- Jogosultságszint-emelés gyakorisága – ha a felhasználónak csak egyszer kell kiemelni a Kiemelt jogosultságú hozzárendelést, nem rendelkezhet állandó hozzárendeléssel. Másfelől, ha a felhasználónak szüksége van a napi feladatra és a PIM használatára, nagy mértékben csökkentheti a termelékenységet, akkor az állandó szerepkörnek tekinthetők.
- A szervezetre jellemző esetek – ha az a személy, aki a jogosult szerepkört egy nagyon távoli csapattól vagy egy magas prioritású ügyvezetőtől kapja, a jogosultságszint-emelési folyamattal folytatott kommunikációt és érvényesítést nehéz megállapítani, az állandó szerepkörnek tekinthetők.

> [!TIP]
> :heavy_check_mark: A **Microsoft azt javasolja** , hogy állítson be ismétlődő hozzáférési felülvizsgálatokat az állandó szerepkör-hozzárendelésekkel rendelkező felhasználók számára (ha van ilyen). További információ az ismétlődő hozzáférési felülvizsgálatról a telepítési terv utolsó szakaszában

### <a name="draft-your-pim-settings"></a>A PIM beállításainak piszkozata

A PIM-megoldás implementálása előtt érdemes a PIM-beállításokat a szervezet által használt összes Kiemelt szerepkörhöz bekészíteni. Ez a szakasz néhány példát mutat be a PIM-beállításokra bizonyos szerepkörök esetében (csak referenciák, és a szervezete számára eltérőek lehetnek). Ezeket a beállításokat részletesen ismertetjük a Microsoft javaslataival a táblázatok után.

#### <a name="pim-settings-for-azure-ad-roles"></a>PIM-beállítások Azure AD-szerepkörökhöz

| Role | MFA megkövetelése | Értesítés | Incidens jegy | Jóváhagyás szükséges | Jóváhagyó | Aktiválás időtartama | Állandó rendszergazda |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Globális rendszergazda | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Egyéb globális rendszergazdák | 1 óra | Vészhelyzeti hozzáférési fiókok |
| Exchange-rendszergazda | :heavy_check_mark: | :heavy_check_mark: | x | x | Nincsenek | 2 óra | Nincsenek |
| Ügyfélszolgálati adminisztrátor | x | x | :heavy_check_mark: | x | Nincsenek | 8 óra | Nincsenek |

#### <a name="pim-settings-for-azure-resource-roles"></a>Az Azure-erőforrás szerepköreinek PIM-beállításai

| Role | MFA megkövetelése | Értesítés | Jóváhagyás szükséges | Jóváhagyó | Aktiválás időtartama | Aktív rendszergazda | Aktív lejárat | Jogosult lejárat |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Kritikus előfizetések tulajdonosa | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Az előfizetés egyéb tulajdonosai | 1 óra | Nincsenek | n/a | 3 hónap |
| Kevésbé kritikus előfizetések felhasználói hozzáférésének rendszergazdája | :heavy_check_mark: | :heavy_check_mark: | x | Nincsenek | 1 óra | Nincsenek | n/a | 3 hónap |
| Virtuális gépek közreműködője | x | :heavy_check_mark: | x | Nincsenek | 3 óra | Nincsenek | n/a | 6 hónap |

Az alábbi táblázat az egyes beállításokat ismerteti.

| Beállítás | Leírás |
| --- | --- |
| Role | Annak a szerepkörnek a neve, amelynek a beállításait definiálja. |
| MFA megkövetelése | Azt határozza meg, hogy a jogosult felhasználónak az MFA-t kell-e elvégeznie a szerepkör aktiválása előtt.<br/><br/> :heavy_check_mark: A **Microsoft azt javasolja** , hogy minden rendszergazdai szerepkör esetében érvényesítse az MFA-t, különösen akkor, ha a szerepkörök vendég felhasználókkal rendelkeznek. |
| Értesítés | Ha igaz értékre van állítva, a globális rendszergazda, a Kiemelt szerepkörű rendszergazda és a szervezet biztonsági rendszergazdája e-mailben értesítést fog kapni, ha egy jogosult felhasználó aktiválja a szerepkört.<br/><br/>**Megjegyzés:** Egyes szervezetek nem rendelkeznek e-mail-címmel, amely a rendszergazdai fiókjához van kötve, az e-mail-értesítések beszerzéséhez be kell állítania egy alternatív e-mail címet, hogy a rendszergazdák megkapják ezeket az e-maileket. |
| Incidens jegy | Azt határozza meg, hogy a jogosult felhasználónak rögzítenie kell-e az incidens jegy számát a szerepkör aktiválása során. Ez a beállítás segít a szervezetnek az egyes aktiválások belső incidensek számával történő azonosításában a nemkívánatos aktiválások enyhítése érdekében.<br/><br/> :heavy_check_mark: A **Microsoft azt javasolja** , hogy a PIM és a belső rendszere számára is kihasználja az incidensek jegyének számát. Ez különösen olyan jóváhagyók számára hasznos, akiknek az aktiváláshoz környezetre van szükségük. |
| Jóváhagyás szükséges | Azt határozza meg, hogy a jogosult felhasználónak meg kell-e kapnia a szerepkör aktiválásához szükséges jóváhagyást.<br/><br/> :heavy_check_mark: A **Microsoft javasolja** , hogy állítson be jóváhagyást a legtöbb jogosultsággal rendelkező szerepkörökhöz. Az összes PIM-ügyfél, a globális rendszergazda, a felhasználói rendszergazda, az Exchange-rendszergazda, a biztonsági rendszergazda és a jelszó-rendszergazda felhasználási mintái alapján a jóváhagyás beállításával leggyakrabban a leggyakoribb szerepkörök. |
| Jóváhagyó | Ha a jogosult szerepkör aktiválásához jóváhagyásra van szükség, sorolja fel azokat a személyeket, akiknek jóvá kell hagyniuk a kérést. Alapértelmezés szerint a PIM úgy állítja be a jóváhagyót, hogy az összes olyan felhasználó legyen, aki Kiemelt szerepkörű rendszergazda, függetlenül attól, hogy azok állandóak vagy jogosultak-e.<br/><br/>**Megjegyzés:** Ha a felhasználó is jogosult egy Azure AD-szerepkörre és a szerepkör jóváhagyójának használatára, nem lesznek képesek jóváhagyni magukat.<br/><br/> :heavy_check_mark: A **Microsoft azt javasolja** , hogy válassza ki, hogy a jóváhagyók olyanok legyenek, akik a leghatékonyabban ismerik az adott szerepkört és a gyakran használt felhasználókat a globális rendszergazda helyett. |
| Aktiválás időtartama | Az az időtartam, ameddig egy felhasználó a szerepkör lejárta előtt aktiválva lesz. |
| Állandó rendszergazda | Azoknak a felhasználóknak a listája, akik a szerepkör állandó rendszergazdája lesznek (soha nem kell aktiválni).<br/><br/> :heavy_check_mark: A **Microsoft azt javasolja** , hogy az összes szerepkörhöz nulla állandó rendszergazdai jogosultsággal rendelkezzen, kivéve a globális rendszergazdákat. További információt a WHO-ban jogosultnak kell lennie, és ki kell a terv végleges aktív szakaszát. |
| Aktív rendszergazda | Az Azure-erőforrások esetében az aktív rendszergazda azoknak a felhasználóknak a listája, akik számára soha nem kell aktiválni a szerepkört. Ez nem olyan állandó rendszergazda, mint az Azure AD-szerepkörök esetében, mert lejárati időt állíthat be, amikor a felhasználó elveszíti ezt a szerepkört. |
| Aktív lejárat | Az Azure erőforrás-szerepkörökhöz tartozó aktív szerepkör-hozzárendelés lejár a beállított időszak után. 15 nap, 1 hónap, 3 hónap, 6 hónap, 1 év vagy tartósan aktív lehetőség közül választhat. |
| Jogosult lejárat | Az Azure-erőforrás szerepköreihez tartozó jogosult szerepkör-hozzárendelés a beállított időszak után lejár. 15 nap, 1 hónap, 3 hónap, 6 hónap, 1 év vagy tartósan jogosult lehetőség közül választhat. |

## <a name="step-3-implement-your-solution"></a>3\. lépés. A megoldás implementálása

A megfelelő tervezés alapja az, hogy az alkalmazás sikeresen telepíthető Azure Active Directory használatával.  Intelligens biztonságot és integrációt biztosít, amely leegyszerűsíti a bevezetést, miközben csökkenti a sikeres üzembe helyezések idejét.  Ez a kombináció biztosítja, hogy az alkalmazás könnyen integrálható legyen, miközben csökkenti a végfelhasználók idejét.

### <a name="identify-test-users"></a>Tesztelési felhasználók azonosítása

Ez a szakasz a felhasználók és a felhasználók csoportjainak azonosítására használható a megvalósítás ellenőrzéséhez. A tervezés szakaszban kiválasztott beállítások alapján azonosítsa azokat a felhasználókat, akiket szeretne tesztelni az egyes szerepkörökhöz.

> [!TIP]
> :heavy_check_mark: A **Microsoft azt javasolja** , hogy az egyes Azure ad-szerepkörök szolgáltatás-tulajdonosai legyenek a tesztek felhasználóinak, hogy megismerjék a folyamatot, és legyenek a kivezetéshez szükséges belső támogatók.

A táblázatban azonosíthatja azokat a felhasználókat, akik ellenőrizni fogják, hogy az egyes szerepkörök beállításai működnek-e.

| Szerepkörnév | Felhasználók tesztelése |
| --- | --- |
| &lt;Szerepkör neve&gt; | &lt;A szerepkör tesztelésére szolgáló felhasználók&gt; |
| &lt;Szerepkör neve&gt; | &lt;A szerepkör tesztelésére szolgáló felhasználók&gt; |

### <a name="test-implementation"></a>Tesztelés megvalósítása

Most, hogy azonosította a felhasználók tesztelését, ezzel a lépéssel konfigurálhatja a PIM-t a tesztelési felhasználók számára. Ha a szervezet a PIM felhasználói felületének a Azure Portalban való használata helyett a PIM-munkafolyamatot szeretné beépíteni a saját belső alkalmazásba, a PIM összes művelete a [Graph API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root)-n keresztül is támogatott.

#### <a name="configure-pim-for-azure-ad-roles"></a>A PIM konfigurálása Azure AD-szerepkörökhöz

1. [Konfigurálja az Azure ad szerepkör beállításait](pim-how-to-change-default-settings.md) a tervezett beállítások alapján.

1. Navigáljon az **Azure ad-szerepkörökhöz**, kattintson a **szerepkörök**elemre, majd válassza ki az imént konfigurált szerepkört.

1. Ha a tesztelési felhasználók csoportjának már állandó rendszergazdája van, jogosult lehet a keresésre, és az állandóról jogosultra konvertálhatja őket, ha a három pontra kattint a sorban. Ha még nem rendelkeznek szerepkör-hozzárendelésekkel, létrehozhat [egy új jogosult hozzárendelést](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Ismételje meg a 1-3. lépést a tesztelni kívánt összes szerepkörhöz.

1. Miután beállította a felhasználók tesztelését, el kell küldenie az [Azure ad-szerepkör aktiválásához](pim-how-to-activate-role.md)szükséges hivatkozást.

#### <a name="configure-pim-for-azure-resource-roles"></a>A PIM konfigurálása Azure-beli erőforrás-szerepkörökhöz

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

### <a name="communicate-pim-to-affected-stakeholders"></a>A PIM kommunikálása az érintett felekkel

A PIM üzembe helyezése további lépéseket fog bevezetni a Kiemelt szerepkörök felhasználói számára. Bár a PIM nagy mértékben csökkenti a Kiemelt identitásokkal kapcsolatos biztonsági problémákat, a változást a bérlői szintű üzembe helyezés előtt hatékonyan kell közölni. A befolyásolt rendszergazdák számától függően a szervezetek gyakran belső dokumentumot, videót vagy e-mailt hoznak létre a változásról. A közleményekben gyakran szerepelnek a következők:

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
> :heavy_check_mark: A **Microsoft javasolja** , hogy a PIM-munkafolyamaton keresztül adja meg az időt az ügyfélszolgálati/támogatási csapattal (ha a szervezet belső informatikai támogatási csapattal rendelkezik). Adja meg a megfelelő dokumentációt, valamint a kapcsolattartási adatait.

### <a name="move-to-production"></a>Átállás éles üzemre

A tesztelés befejezése és sikeres végrehajtása után helyezze át a PIM-t éles környezetbe úgy, hogy megismétli a saját PIM-konfigurációban definiált szerepkörök összes felhasználójának tesztelési fázisában szereplő összes lépést. Az Azure AD-szerepkörökhöz tartozó PIM esetében a szervezetek gyakran tesztelik és bevezetik a PIM-t a globális rendszergazdák számára a PIM más szerepkörökhöz való tesztelése és kiépítése előtt. Az Azure-erőforrások mellett a szervezetek a PIM egy Azure-előfizetést általában tesztelik és kivezetik.

### <a name="in-the-case-a-rollback-is-needed"></a>Ha visszaállításra van szükség

Ha a PIM nem tudott megfelelően működni az éles környezetben, a következő visszaállítási lépések segíthetnek a megfelelő állapot visszaállításában a PIM beállítása előtt:

#### <a name="azure-ad-roles"></a>Azure AD-szerepkörök

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Nyissa meg **Azure ad Privileged Identity Management**.
1. Kattintson az **Azure ad-szerepkörök** elemre, majd a **szerepkörök**elemre.
1. Minden konfigurált szerepkörnél kattintson a három pontra ( **..** .) a jogosult hozzárendeléssel rendelkező összes felhasználó esetében.
1. Kattintson a **véglegesvé tétele** lehetőségre a szerepkör-hozzárendelés véglegesvé tételéhez.

#### <a name="azure-resource-roles"></a>Azure-erőforrásszerepkörök

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Nyissa meg **Azure ad Privileged Identity Management**.
1. Kattintson az **Azure-erőforrások** elemre, majd kattintson egy előfizetésre vagy erőforrásra, amelyet vissza szeretne állítani.
1. Kattintson a **szerepkörök**elemre.
1. Minden konfigurált szerepkörnél kattintson a három pontra ( **..** .) a jogosult hozzárendeléssel rendelkező összes felhasználó esetében.
1. Kattintson a **véglegesvé tétele** lehetőségre a szerepkör-hozzárendelés véglegesvé tételéhez.

## <a name="step-4-next-steps-after-deploying-pim"></a>4\. lépés. Következő lépések a PIM üzembe helyezése után

A PIM sikeres üzembe helyezése az éles környezetben jelentős előrelépést jelent a szervezet emelt szintű identitásának védelme szempontjából. A PIM üzembe helyezése további PIM-funkciókat is tartalmaz, amelyeket biztonsági és megfelelőségi célra kell használni.

### <a name="use-pim-alerts-to-safeguard-your-privileged-access"></a>A védett hozzáférés védelme a PIM-riasztások használatával

Érdemes kihasználni a PIM beépített riasztási funkcióját a bérlő jobb védelme érdekében. További információ: [biztonsági riasztások](pim-how-to-configure-security-alerts.md#security-alerts). Ezek a riasztások a következők: a rendszergazdák nem Kiemelt szerepköröket használnak, a szerepköröket a PIM-en kívül kell kiosztani, a szerepköröket pedig túl gyakran aktiválják. A szervezet teljes körű védelem érdekében rendszeresen menjen végig a riasztások listáján, és javítsa ki a problémákat. A riasztásokat a következő módon tekintheti meg és javíthatja:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Nyissa meg **Azure ad Privileged Identity Management**.
1. Kattintson az **Azure ad-szerepkörök** elemre, majd a **riasztások**elemre.

> [!TIP]
> :heavy_check_mark: A **Microsoft azt javasolja** , hogy az összes riasztást azonnal feltüntetve. Közepes és alacsony súlyosságú riasztások esetén tájékoztatni kell, és módosítania kell a módosításokat, ha úgy gondolja, hogy biztonsági fenyegetés van.

Ha az adott riasztások bármelyike nem hasznos, vagy nem vonatkozik a szervezetére, a riasztást bármikor elvetheti a riasztások lapon. Később bármikor visszaállíthatja ezt az elbocsátást az Azure AD beállítások lapján.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Ismétlődő hozzáférési felülvizsgálatok beállítása a szervezet emelt szintű identitásának rendszeres auditálásához

A hozzáférési felülvizsgálatok lehetővé teszik a legjobb megoldást arra, hogy a Kiemelt szerepkörökkel vagy konkrét felülvizsgálók által hozzárendelt felhasználókat Kérdezzen rá, hogy az egyes felhasználóknak szükségük van-e a privilegizált identitásra. A hozzáférési felülvizsgálatok nagyszerűek, ha csökkenteni szeretné a támadási felületet, és megfelelőnek kell lennie. A hozzáférési felülvizsgálat elindításával kapcsolatos további információkért tekintse meg az [Azure ad-szerepkörök hozzáférési](pim-how-to-start-security-review.md) felülvizsgálatait és az [Azure Resource roles-hozzáférési](pim-resource-roles-start-access-review.md)felülvizsgálatokat. Egyes szervezetek esetében az időszakos hozzáférési felülvizsgálat elvégzése szükséges a törvények és szabályozások betartásához, míg mások esetében a hozzáférési felülvizsgálat a legjobb módszer a legalacsonyabb jogosultsági szint érvényesítésére a szervezeten belül.

> [!TIP]
> :heavy_check_mark: A **Microsoft javasolja** , hogy negyedévente hozzáférési felülvizsgálatokat állítson be az összes Azure ad-és Azure-erőforrás-szerepkörhöz.

A legtöbb esetben az Azure AD-szerepkörökhöz tartozó felülvizsgáló maga a felhasználó, miközben az Azure-erőforrás szerepköreihez tartozó felülvizsgáló az előfizetés tulajdonosa, amely a szerepkör. Gyakran előfordul azonban, hogy a vállalatok olyan Kiemelt jogosultságú fiókkal rendelkeznek, amelyek nem kapcsolódnak egy adott személy e-mail-címéhez. Ezekben az esetekben senki nem olvas és nem tekinti át a hozzáférést.

> [!TIP]
> :heavy_check_mark: A **Microsoft azt javasolja** , hogy adjon hozzá egy másodlagos e-mail-címet minden olyan fiókhoz, amelynek Kiemelt szerepkörű hozzárendelései vannak, amelyek nem kapcsolódnak rendszeresen ellenőrzött e-mail-címekhez

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>A biztonság és a megfelelőség javítása érdekében hozza ki a legtöbbet a naplóból

A napló az a hely, ahol naprakész maradhat, és megfelel a szabályozásoknak. A PIM jelenleg a szervezeti előzmények 30 napos előzményeit tárolja a naplójában, beleértve a következőket:

- A jogosult szerepkörök aktiválása/inaktiválása
- Szerepkör-hozzárendelési tevékenységek a PIM-n belül és kívül
- A szerepkör beállításaiban bekövetkezett változások
- A szerepkör-aktiválásra vonatkozó kérés/jóváhagyás/megtagadás tevékenységek jóváhagyási beállítással
- Frissítés riasztásokra

Ezeket a naplókat akkor érheti el, ha Ön globális rendszergazda vagy Kiemelt szerepkörű rendszergazda. További információ: Azure [ad-szerepkörök naplózási előzményei](pim-how-to-use-audit-log.md) és [Naplózási előzmények Azure-beli erőforrás-szerepkörökhöz](azure-pim-resource-rbac.md).

> [!TIP]
> :heavy_check_mark: A **Microsoft azt javasolja** , hogy legalább egy rendszergazdát beolvasson az összes naplózási eseményről hetente, és havonta exportálja a naplózási eseményeket.

Ha hosszabb ideig szeretné automatikusan tárolni a naplózási eseményeket, a rendszer automatikusan szinkronizálja a PIM naplóját az [Azure ad-naplókba](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> :heavy_check_mark: A **Microsoft azt javasolja** , hogy állítsa be az [Azure log-figyelést](../reports-monitoring/concept-activity-logs-azure-monitor.md) , hogy az Azure Storage-fiókban a biztonsági és megfelelőségi igényeknek megfelelően archiválja a naplózási eseményeket.
