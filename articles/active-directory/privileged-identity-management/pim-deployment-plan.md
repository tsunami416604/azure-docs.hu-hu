---
title: Emelt szintű identitáskezelés (PIM) telepítése – Azure AD | Microsoft dokumentumok
description: Bemutatja, hogyan tervezd meg az Azure AD kiemelt identitáskezelés (PIM) üzembe helyezését.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/04/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8c77b3454026aa309d979bd938674e7c3ae7b6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025996"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Az Azure AD kiemelt identitáskezelés (PIM) telepítése

Ez a részletes útmutató ismerteti, hogyan tervezze meg a kiemelt identitáskezelés (PIM) az Azure Active Directory (Azure AD) szervezetben üzembe helyezését.

> [!TIP]
> Ebben a cikkben a következő ként jelölt elemek jelennek meg:
> 
> :heavy_check_mark: **A Microsoft ajánlja**
> 
> Ezek általános javaslatok, és csak akkor hajtsa végre, ha az adott vállalati igényekre vonatkoznak.

## <a name="learn-about-privileged-identity-management"></a>Tudnivalók a kiemelt identitáskezelésről

Az Azure AD kiemelt identitáskezelés segítségével kezelheti a kiemelt felügyeleti szerepköröket az Azure AD, az Azure-erőforrások és más Microsoft Online Services szolgáltatásban. Egy olyan világban, ahol a kiemelt identitások vannak hozzárendelve és elfelejtve, a Kiemelt identitáskezelés olyan megoldásokat kínál, mint az just-in-time hozzáférés, a jóváhagyási munkafolyamatok kérése és a teljes mértékben integrált hozzáférési felülvizsgálatok, így azonosíthatja, feltárhatja és megelőzheti a rosszindulatú a kiváltságos szerepek valós idejű tevékenységeit. A kiemelt identitáskezelés üzembe helyezése a kiemelt szerepkörök kezelése a szervezetben nagyban csökkenti a kockázatot, miközben a kiemelt szerepkörök tevékenységeinek értékes betekintést nyújt.

### <a name="business-value-of-privileged-identity-management"></a>A kiemelt identitáskezelés üzleti értéke

**Kockázat kezelése** – A szervezet védelme a [minimális jogosultsági hozzáférés](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) és a just-in-time hozzáférés elvének érvényesítésével. A felhasználók jogosultsággal rendelkező szerepkörökhöz való állandó hozzárendeléseinek számának minimalizálásával, valamint a jóváhagyások és az MFA jogosultságszint-emelésre való kényszerítésével jelentősen csökkentheti a szervezeten belüli kiemelt hozzáféréssel kapcsolatos biztonsági kockázatokat. A legkisebb jogosultság és a just-in-time hozzáférés kényszerítése azt is lehetővé teszi, hogy tekintse meg a kiemelt szerepkörökhöz való hozzáférés előzményeit, és nyomon kövesse a biztonsági problémákat, ahogy azok megtörténnek.

**Címmegfelelőség és -A** kiemelt identitáskezelés üzembe helyezése környezetet hoz létre a folyamatos identitáskezeléshez. A kiemelt identitások just-in-time jogosultsági szintű jogosultsággal rendelkező identitások jogosultsági szintű identitások szolgáltatása lehetővé teszi a kiemelt identitáskezelés a szervezet kiemelt hozzáférési tevékenységeinek nyomon követését. Emellett megtekintheti és fogadhatja az állandó és jogosult szerepkörök szervezeten belüli összes hozzárendeléséről szóló értesítéseket. A hozzáférés-felülvizsgálat révén rendszeresen naplózhatja és eltávolíthatja a szükségtelen kiemelt identitásokat, és meggyőződhet arról, hogy a szervezet megfelel a legszigorúbb identitás-, hozzáférési és biztonsági szabványoknak.

**Költségek csökkentése** – Csökkentse a költségeket a hatékonysági problémák, az emberi hibák és a biztonsági problémák megszüntetésével a Kiemelt identitáskezelés megfelelő telepítésével. A nettó eredmény a kiváltságos identitásokkal kapcsolatos kiberbűncselekmények csökkentése, amelyekből költségesés nehéz kiheverni őket. A kiemelt identitáskezelés a szabályok és szabványok betartásával kapcsolatban a hozzáféréssel kapcsolatos információk naplózásával kapcsolatos költségek csökkentésében is segítséget nyújt.

További információ: [Mi az Azure AD kiemelt identitáskezelés?](pim-configure.md).

### <a name="licensing-requirements"></a>Licenckövetelmények

A Kiemelt identitáskezelés használatához a címtárnak rendelkeznie kell az alábbi fizetett vagy próbalicencek egyikével:

- Prémium szintű Azure AD P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

További információt a [Kiemelt identitáskezelés használatára vonatkozó licenckövetelmények](subscription-requirements.md)című témakörben talál.

### <a name="key-terminology"></a>Kulcsfontosságú terminológia

| Kifejezés vagy fogalom | Leírás |
| --- | --- |
| Támogatható | Olyan szerepkör-hozzárendelés, amelya szerepkör használatához egy vagy több műveletet kell végrehajtania. Ha egy felhasználó jogosulttá vált egy szerepkörre, ez azt jelenti, hogy aktiválhatja a szerepkört, amikor kiemelt feladatokat kell végrehajtania. Nincs különbség a hozzáférés adott valaki egy állandó versus jogosult szerepkör-hozzárendelés. Az egyetlen különbség az, hogy néhány ember nem kell, hogy a hozzáférés minden alkalommal. |
| aktiválás | Egy vagy több művelet végrehajtása egy olyan szerepkör használatára, amelyre a felhasználó jogosult. A műveletek közé tartozhat a többtényezős hitelesítés (MFA) ellenőrzése, az üzleti indoklás megadása vagy a kijelölt jóváhagyók jóváhagyásának kérése. |
| just-in-time (JIT) hozzáférés | Olyan modell, amelyben a felhasználók ideiglenes engedélyeket kapnak a kiemelt feladatok végrehajtására, ami megakadályozza, hogy rosszindulatú vagy jogosulatlan felhasználók hozzáférjenek az engedélyek lejárta után. A hozzáférés csak akkor érhető el, ha a felhasználóknak szükségük van rá. |
| a legkisebb jogosultsághoz való hozzáférés elve | Ajánlott biztonsági gyakorlat, amelyben minden felhasználó csak a feladatok elvégzéséhez szükséges minimális jogosultságokkal rendelkezik. Ez a gyakorlat minimálisra csökkenti a globális rendszergazdák számát, és ehelyett bizonyos esetekben adott rendszergazdai szerepköröket használ. |

További információ: [Terminológia](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-privileged-identity-management-works"></a>A kiemelt identitáskezelés működésének magas szintű áttekintése

1. A kiemelt identitáskezelés úgy van beállítva, hogy a felhasználók jogosultak legyenek a kiemelt szerepkörökre.
1. Ha egy jogosult felhasználónak a kiemelt szerepkörét kell használnia, aktiválja a szerepkört a kiemelt identitáskezelés ben.
1. A szerepkörhöz beállított kiemelt identitáskezelési beállításoktól függően a felhasználónak végre kell hajtson végre bizonyos lépéseket (például többtényezős hitelesítést, jóváhagyást vagy ok megadását.)
1. Miután a felhasználó sikeresen aktiválta a szerepkörét, megkapja a szerepkört egy előre konfigurált időszakra.
1. A rendszergazdák megtekinthetik az összes kiemelt identitáskezelési tevékenység előzményeit a naplóban. Emellett tovább biztosíthatják az Azure AD-szervezeteket, és megfelelhetnek a megfelelőségnek a kiemelt identitáskezelési funkciók, például a hozzáférési felülvizsgálatok és riasztások használatával.

További információ: [Mi az Azure AD kiemelt identitáskezelés?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-privileged-identity-management"></a>Kiemelt identitáskezelés által kezelhető szerepkörök

**Azure AD-szerepkörök** – Ezek a szerepkörök mind az Azure Active Directoryban (például a globális rendszergazda, az Exchange-rendszergazda és a biztonsági rendszergazda). A szerepkörökről és azok funkcióiról az [Azure Active Directory rendszergazdai szerepkör-engedélyei](../users-groups-roles/directory-assign-admin-roles.md)című dokumentumban olvashat bővebben. Ha segítségre van szüksége annak meghatározásához, hogy mely szerepköröket szeretné hozzárendelni a rendszergazdákhoz, olvassa el [a legkevésbé kiemelt szerepkörök feladat szerint című témakört.](../users-groups-roles/roles-delegate-by-task.md)

**Azure-erőforrás-szerepkörök** – Ezek a szerepkörök egy Azure-erőforráshoz, erőforráscsoporthoz, előfizetéshez vagy felügyeleti csoporthoz kapcsolódnak. A kiemelt identitáskezelés csak időben biztosít hozzáférést a beépített szerepkörökhöz, például a tulajdonoshoz, a felhasználói hozzáférés rendszergazdájához és a közreműködőhöz, valamint [az egyéni szerepkörökhöz.](../../role-based-access-control/custom-roles.md) Az Azure-erőforrás-szerepkörökről további információt a [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md)című témakörben talál.

További információt a [Kiemelt identitáskezelés ben nem kezelhető szerepkörök című témakörben](pim-roles.md)talál.

## <a name="plan-your-deployment"></a>Az üzembe helyezés megtervezése

Ez a szakasz arra összpontosít, hogy mit kell tennie, mielőtt a szervezetben üzembe helyezne kiemelt identitáskezelést. Alapvető fontosságú, hogy kövesse az utasításokat, és megérteni a fogalmakebben a szakaszban, mivel ezek végigvezeti, hogy megteremtse a legjobb terv szabott a szervezet kiemelt identitások.

### <a name="identify-your-stakeholders"></a>Az érdekelt felek azonosítása

A következő szakasz segítséget nyújt a projektben részt vevő összes érdekelt fél azonosításában, és a regisztrációt, az értékelést vagy a tájékoztatáshoz szükséges adatokat. Külön táblákat tartalmaz a kiemelt identitáskezelés azure AD-szerepkörökhöz és az Azure-erőforrás-szerepkörök kiemelt identitáskezelés telepítéséhez. A szervezetnek megfelelően vegye fel az érdekelt feleket az alábbi táblázatba.

- SO = A projekt aláírása
- R = Tekintse át ezt a projektet, és adjon meg
- I = Tájékoztatás erről a projektről

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Érdekelt felek: Kiemelt identitáskezelés Azure AD-szerepkörökhöz

| Név | Szerepkör | Műveletek |
| --- | --- | --- |
| Név és e-mail | **Identitástervező vagy Az Azure globális rendszergazdája**<br/>Az identitáskezelő csapat képviselője, aki annak meghatározásáért felelős, hogy ez a változás hogyan igazodik a szervezet alapvető identitáskezelési infrastruktúrájához. | SO/R/I |
| Név és e-mail | **Szolgáltatás tulajdonosa / Line manager**<br/>Egy szolgáltatás informatikai tulajdonosainak vagy egy szolgáltatáscsoportnak a képviselője. Ezek kulcsfontosságúak a döntések meghozatalában és a kiemelt identitáskezelés bevezetésében a csapat számára. | SO/R/I |
| Név és e-mail | **Biztonság tulajdonosa**<br/>A biztonsági csoport képviselője, aki aláírhatja, hogy a terv megfelel a szervezet biztonsági követelményeinek. | SO/R |
| Név és e-mail | **INFORMATIKAI támogatási menedzser / Ügyfélszolgálat**<br/>Az informatikai támogatási szervezet képviselője, aki az ügyfélszolgálati szempontból tud hozzájárulni a módosítás támogathatóságához. | R/I |
| A próbafelhasználók neve és e-mail címe | **Kiemelt szerepkör-felhasználók**<br/>Az a felhasználói csoport, amelyhez a kiemelt identitáskezelés van megvalósítva. Tudniuk kell, hogyan aktiválhatják a szerepköreiket, ha a kiemelt identitáskezelés megvalósítása után. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-resource-roles"></a>Érdekelt felek: Kiemelt identitáskezelés Azure-erőforrás-szerepkörökhöz

| Név | Szerepkör | Műveletek |
| --- | --- | --- |
| Név és e-mail | **Előfizetés / Erőforrás-tulajdonos**<br/>A Kiemelt identitáskezelés felügyeletét használó minden egyes előfizetés vagy erőforrás informatikai tulajdonosainak képviselője | SO/R/I |
| Név és e-mail | **Biztonság tulajdonosa**<br/>A biztonsági csoport képviselője, aki aláírhatja, hogy a terv megfelel a szervezet biztonsági követelményeinek. | SO/R |
| Név és e-mail | **INFORMATIKAI támogatási menedzser / Ügyfélszolgálat**<br/>Az informatikai támogatási szervezet képviselője, aki az ügyfélszolgálati szempontból tud hozzájárulni a módosítás támogathatóságához. | R/I |
| A próbafelhasználók neve és e-mail címe | **RBAC szerepkör-felhasználók**<br/>Az a felhasználói csoport, amelyhez a kiemelt identitáskezelés van megvalósítva. Tudniuk kell, hogyan aktiválhatják a szerepköreiket, ha a kiemelt identitáskezelés megvalósítása után. | I |

### <a name="enable-privileged-identity-management"></a>Kiemelt identitáskezelés engedélyezése

A tervezési folyamat részeként először hozzá kell járulnia, és engedélyeznie kell a kiemelt identitáskezelést a Kiemelt identitáskezelés cikk [használatának megkezdéséhez](pim-getting-started.md) követve. A kiemelt identitáskezelés engedélyezése hozzáférést biztosít bizonyos funkciókhoz, amelyek kifejezetten a központi telepítés elősegítésére szolgálnak.

Ha a cél az Azure-erőforrások kiemelt identitáskezelése üzembe helyezése, kövesse [a discover Azure-erőforrások kezelésére a kiemelt identitáskezelés cikkben.](pim-resource-roles-discover-resources.md) Csak az előfizetések és felügyeleti csoportok tulajdonosai fedezhetik fel és hozhatják létre ezeket az erőforrásokat a kiemelt identitáskezeléshez. A bedeszkázott rendszer bedeszkázása után a PIM-funkció minden szinten elérhető a tulajdonosok számára, beleértve a felügyeleti csoportot, az előfizetést, az erőforráscsoportot és az erőforrást. Ha Ön globális rendszergazda, aki az Azure-erőforrásokhoz próbálja üzembe helyezni a kiemelt identitáskezelést, [az összes Azure-előfizetés kezeléséhez hozzáférhet,](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) hogy hozzáférést biztosítson magának a címtárban lévő összes Azure-erőforráshoz felderítéscéljából. Azonban azt javasoljuk, hogy jóváhagyást kapjon az egyes előfizetés-tulajdonosok, mielőtt az erőforrások kezelése a kiemelt identitáskezelés.

### <a name="enforce-principle-of-least-privilege"></a>A legkisebb jogosultság elvének érvényesítése

Fontos, hogy győződjön meg arról, hogy a szervezetben a legkisebb jogosultság elvét az Azure AD és az Azure erőforrás-szerepkörök.

#### <a name="azure-ad-roles"></a>Azure AD-szerepkörök

Az Azure AD-szerepkörök esetében gyakori, hogy a szervezetek a globális rendszergazdai szerepkört jelentős számú rendszergazdához rendelik, ha a legtöbb rendszergazdának csak egy vagy két adott rendszergazdai szerepkörre van szüksége. Kiemelt szerepkör-hozzárendelések is általában nem felügyelt marad.

> [!NOTE]
> Gyakori buktatók a szerepkör-delegálásban:
>
> - Az Exchange-ért felelős rendszergazda globális rendszergazdai szerepkört kap, még akkor is, ha csak az Exchange-rendszergazdai szerepkörre van szüksége a napi feladat elvégzéséhez.
> - A Globális rendszergazda szerepkör egy Office-rendszergazdához van rendelve, mert a rendszergazdának biztonsági és SharePoint-rendszergazdai szerepkörökre is szüksége van, és egyszerűbb csak egy szerepkört delegálni.
> - A rendszergazda már régen biztonsági rendszergazdai szerepkört kapott egy feladat végrehajtásához, de soha nem lett eltávolítva.

Kövesse az alábbi lépéseket az Azure AD-szerepkörök minimális jogosultsági elvének érvényesítéséhez.

1. A szerepkörök részletességét az elérhető Azure [AD-rendszergazdai szerepkörök](../users-groups-roles/directory-assign-admin-roles.md#available-roles)olvasásával és megértésével értheti meg. Önnek és a csapatának az [Azure AD-ben identitásfeladat szerint](../users-groups-roles/roles-delegate-by-task.md)is hivatkoznia kell a rendszergazdai szerepkörökre, amely ismerteti az adott feladatok legkevésbé kiemelt szerepkörét.

1. Azok nak a listája, akik kiemelt szerepköröket töltenek be a szervezetben. A Kiemelt [identitáskezelés varázslóval](pim-security-wizard.md#run-the-wizard) a következőhöz hasonló lapot érhet el.

    ![A Kiemelt szerepkörök panelje, amely megmutatja, hogy ki rendelkezik kiemelt szerepkörrel](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. A szervezet összes globális rendszergazdája számára megtudhatja, hogy miért van szükségük a szerepkörre. Az előző dokumentáció elolvasása alapján, ha a személy feladatát egy vagy több részletes rendszergazdai szerepkör is el tudja végezni, távolítsa el őket a globális rendszergazdai szerepkörből, és ennek megfelelően hajtsa végre a hozzárendeléseket az Azure Active Directoryban (hivatkozásként: A Microsoft jelenleg csak mintegy 10 rendszergazda a globális rendszergazdai szerepkörrel rendelkezik. További információ [arról, hogy](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)a Microsoft hogyan használja a Kiemelt identitáskezelést .

1. Az összes többi Azure AD-szerepkör esetében tekintse át a hozzárendelések listáját, azonosítsa azokat a rendszergazdákat, akiknek már nincs szükségük a szerepkörre, és távolítsa el őket a hozzárendeléseikből.

Az utolsó két lépés automatizálásához használhatja a hozzáférési felülvizsgálatok a kiemelt identitáskezelés. A kiemelt [identitáskezelés ben az Azure AD-szerepkörök hozzáférési felülvizsgálatának elindításához](pim-how-to-start-security-review.md)szükséges lépéseket követve beállíthat egy hozzáférési felülvizsgálatot minden olyan Azure AD-szerepkörhöz, amely egy vagy több taggal rendelkezik.

![Access-ellenőrző ablaktábla létrehozása az Azure AD-szerepkörökhöz](./media/pim-deployment-plan/create-access-review.png)

Az ellenőrzőket **tagokra (saját)** kell állítani. Ez küld egy e-mailt, hogy minden tag a szerepet, hogy őket, hogy erősítse meg, hogy szükségük van a hozzáférést. A speciális beállításokban is be kell kapcsolnia **a Jóváhagyás okának megkövetelése** beállítást, hogy a felhasználók megtudják, miért van szükségük a szerepkörre. Ezen információk alapján eltávolíthatja a felhasználókat a szükségtelen szerepkörökből, és részletesebb rendszergazdai szerepköröket delegálhat a globális rendszergazdák esetében.

A hozzáférési felülvizsgálatok e-mailekre támaszkodnak, hogy értesítsék az embereket a szerepkörökhöz való hozzáférésük áttekintéséről. Ha olyan kiemelt fiókokkal rendelkezik, amelyekhez nincs enek csatolva az e-mailek, mindenképpen fbanja fel a másodlagos e-mail mezőt ezeken a fiókokon. További információ: [proxyAddresses attribútum az Azure AD-ben.](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)

#### <a name="azure-resource-roles"></a>Azure-erőforrásszerepkörök

Az Azure-előfizetések és -erőforrások, beállíthat egy hasonló Access-felülvizsgálati folyamat, hogy áttekintse a szerepkörök et az egyes előfizetések vagy erőforrások. A folyamat célja az egyes előfizetésekhez vagy erőforrásokhoz csatolt tulajdonosi és felhasználói hozzáférés-rendszergazdai hozzárendelések minimalizálása, valamint a szükségtelen hozzárendelések eltávolítása. A szervezetek azonban gyakran delegálják ezeket a feladatokat az egyes előfizetések vagy erőforrások tulajdonosának, mert jobban ismerik az adott szerepköröket (különösen az egyéni szerepköröket).

Ha Ön olyan rendszergazda, aki globális rendszergazdai szerepkörrel próbálja telepíteni a szervezetben az Azure-erőforrások hoz a kiemelt identitáskezelést, [az összes Azure-előfizetés kezeléséhez hozzáférhet](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) az egyes előfizetésekhez való hozzáféréshez. Ezután megkeresheti az egyes előfizetés-tulajdonosokat, és együttműködhet velük a szükségtelen hozzárendelések eltávolításához és a tulajdonosi szerepkör-hozzárendelés minimalizálásához.

Az Azure-előfizetés tulajdonosi szerepkörrel rendelkező felhasználók az [Azure-erőforrások hozzáférési ellenőrzéseit](pim-resource-roles-start-access-review.md) is használhatják az Azure-szerepkörökhöz hasonló szükségtelen szerepkör-hozzárendelések naplózásához és eltávolításához.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Döntse el, hogy mely szerepkör-hozzárendeléseket kell védenie a kiemelt identitáskezelésnek

A kiemelt szerepkör-hozzárendelések szolgáltatásának a szervezetben való megtisztítása után el kell döntenie, hogy mely szerepköröket kell védenie a kiemelt identitáskezeléssel.

Ha egy szerepkört a Kiemelt identitáskezelés véd, a hozzá rendelt jogosult felhasználóknak magasabb szintre kell emelkedniük a szerepkör által biztosított jogosultságok használatához. A jogosultságszint-emelési folyamat magában foglalhatja a jóváhagyás megszerzését, a többtényezős hitelesítés elvégzését és/vagy az aktiválás okának megadását is. A kiemelt identitáskezelés is nyomon követheti a jogosultságok értesítések és a kiemelt identitáskezelés és az Azure AD naplózási eseménynaplók segítségével is nyomon követheti a jogosultsággal rendelkező identitáskezelés és az Azure AD naplózási eseménynaplók.

A kiemelt identitáskezeléssel védeni szeretne szerepkörök kiválasztása nehéz lehet, és az egyes szervezetek esetében eltérő lesz. Ez a szakasz az Azure AD és az Azure erőforrás-szerepkörökhöz vonatkozó gyakorlati tanácsok.

#### <a name="azure-ad-roles"></a>Azure AD-szerepkörök

Fontos, hogy fontos az Azure AD-szerepkörök, amelyek a legtöbb engedéllyel rendelkeznek. Az összes kiemelt identitáskezelési ügyfél használati mintái alapján a kiemelt identitáskezelés által kezelt 10 Legfontosabb Azure AD-szerepkör a következő:

1. Globális rendszergazda
1. Biztonsági rendszergazda
1. Rendszergazda
1. Exchange-rendszergazda
1. SharePoint-rendszergazda
1. Intune-rendszergazda
1. Biztonsági olvasó
1. Szolgáltatás-rendszergazda
1. Számlázási rendszergazda
1. Skype Vállalati verzió-rendszergazda

> [!TIP]
> :heavy_check_mark: **A Microsoft azt javasolja,** hogy első lépésként kezelje a kiemelt identitáskezelést használó összes globális rendszergazdát és biztonsági rendszergazdát, mivel ők azok, amelyek a legtöbb kárt okozhatják, ha feltörték őket.

Fontos figyelembe venni, hogy milyen adatok és engedélyek a legérzékenyebbek a szervezet számára. Előfordulhat például, hogy egyes szervezetek meg szeretnék védeni power bi rendszergazdai szerepkörüket vagy a Teams-rendszergazdai szerepkörüket a Kiemelt identitáskezelés használatával, mivel hozzáférhetnek az adatokhoz és/vagy módosíthatják az alapvető munkafolyamatokat.

Ha vannak olyan szerepkörök, amelyekhez vendégfelhasználók vannak hozzárendelve, különösen ki vannak téve a támadásoknak.

> [!TIP]
> :heavy_check_mark: A **Microsoft azt javasolja,** hogy a kiemelt identitáskezelés használatával kezelje az összes szerepkört a sérült vendégfelhasználói fiókokkockázatának csökkentése érdekében.

Az olvasói szerepkörök, például a Címtárolvasó, a Message Center-olvasó és a Biztonsági olvasó néha kevésbé fontosak más szerepkörökhöz képest, mivel nem rendelkeznek írási engedéllyel. Egyes ügyfelek azonban ezeket a szerepköröket is védik, mivel azok a támadók, akik hozzáfértek ezekhez a fiókokhoz, bizalmas adatokat, például személyes adatokat olvashatnak. Ezt figyelembe kell venni annak eldöntésekor, hogy a szervezet olvasói szerepköreit kell-e kezelni a Kiemelt identitáskezelés használatával.

#### <a name="azure-resource-roles"></a>Azure-erőforrásszerepkörök

Annak eldöntésekor, hogy mely szerepkör-hozzárendeléseket kell kezelni a Kiemelt identitáskezelés azure-erőforráshasználatával, először azonosítania kell a szervezet számára legfontosabb előfizetéseket/erőforrásokat. Ilyen előfizetések/erőforrások például a következők:

- A legérzékenyebb adatokat tartalmazó erőforrások
- Az alapvető, ügyfélközpontú alkalmazásoktól függő erőforrások

Ha Ön globális rendszergazda, akinek nem sikerül eldöntenie, hogy mely előfizetések/erőforrások a legfontosabbak, vegye fel a szükséges információkat a szervezet előfizetés-tulajdonosaihoz, és gyűjtse össze az egyes előfizetések által kezelt erőforrások listáját. Ezután az előfizetés-tulajdonosokkal együtt kell csoportosítani az erőforrásokat a súlyossági szint alapján abban az esetben, ha azok sérülnek (alacsony, közepes, magas). Az erőforrások kezelését a kiemelt identitáskezelés sel kell rangsorolni a súlyossági szint alapján.

> [!TIP]
> :heavy_check_mark: **A Microsoft azt javasolja,** hogy a kritikus szolgáltatások előfizetés-/erőforrás-tulajdonosaival együttműködve állítsa be a kiemelt identitáskezelési munkafolyamatot a bizalmas előfizetéseken/erőforrásokon belüli összes szerepkörhöz.

Az Azure-erőforrások privilegizált identitáskezelése időhöz kötött szolgáltatásfiókokat támogat. A szolgáltatásfiókokat pontosan ugyanúgy kell kezelnie, mint egy normál felhasználói fiókot.

Az előfizetések/erőforrások, amelyek nem olyan kritikus, nem kell beállítani a kiemelt identitáskezelés minden szerepkörhöz. A tulajdonosi és a felhasználói hozzáférés-rendszergazdai szerepköröket azonban továbbra is védenie kell a Kiemelt identitáskezelés szolgáltatással.

> [!TIP]
> :heavy_check_mark: A **Microsoft azt javasolja,** hogy a Kiemelt identitáskezelés használatával kezelje az összes előfizetés/erőforrás tulajdonosi szerepköreit és felhasználói hozzáférés-rendszergazdai szerepköreit.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Annak eldöntése, hogy mely szerepkör-hozzárendelések legyenek állandóak vagy jogosultak

Miután úgy döntött, hogy a kiemelt identitáskezelés által kezelendő szerepkörök listáját, el kell döntenie, hogy mely felhasználók nak kell kapnia a jogosult szerepkört a tartósan aktív szerepkörrel szemben. A tartósan aktív szerepkörök az Azure Active Directory és az Azure-erőforrásokon keresztül hozzárendelt normál szerepkörök, míg a jogosult szerepkörök csak a kiemelt identitáskezelésben rendelhetők hozzá.

> [!TIP]
> :heavy_check_mark: **A Microsoft azt javasolja,** hogy az ajánlott két [törésüveg-segélyhívó fióktól](../users-groups-roles/directory-emergency-access.md)eltérő, állandó globális rendszergazdai szerepkörrel rendelkező, állandó állandó globális rendszergazdai szerepkörrel rendelkező, az Azure AD-szerepkörökhöz és az Azure-erőforrás-szerepkörökhöz nem tartozik véglegesen aktív hozzárendelések.

Annak ellenére, hogy azt javasoljuk, nulla állandó rendszergazda, néha nehéz a szervezetek számára, hogy ezt azonnal elérni. A következő dolgokat kell figyelembe venni a döntés meghozatalakor:

- Jogosultságszint-emelés gyakorisága – Ha a felhasználónak csak egyszer van szüksége a kiemelt hozzárendelésre, nem kell rendelkeznie az állandó hozzárendeléssel. Másrészt, ha a felhasználónak szüksége van a szerepkör a napi feladat, és a kiemelt identitáskezelés használata jelentősen csökkentené a termelékenységet, akkor lehet tekinteni az állandó szerepkör.
- A szervezetre jellemző esetek – Ha a jogosult szerepkört kapó személy egy nagyon távoli csapattól vagy egy magas rangú vezetőtől származik, addig a pontig, hogy a jogosultsági folyamat kommunikálása és érvényesítése nehéz, akkor az állandó szerepkörhöz lehet tekinteni.

> [!TIP]
> :heavy_check_mark: **A Microsoft azt javasolja,** hogy állítsa be az ismétlődő hozzáférési ellenőrzéseket az állandó szerepkör-hozzárendeléssel rendelkező felhasználók számára (ha van ilyen). További információ az ismétlődő hozzáférés-felülvizsgálatról a telepítési terv utolsó részében

### <a name="draft-your-privileged-identity-management-settings"></a>A kiemelt identitáskezelési beállítások megrajzolása

A kiemelt identitáskezelési megoldás megvalósítása előtt célszerű a kiemelt identitáskezelési beállításokat a szervezet által használt minden kiemelt szerepkörhöz megfogalmazni. Ez a szakasz néhány példát tartalmaz az adott szerepkörök kiemelt identitáskezelési beállításaira (ezek csak referenciaként szolgálnak, és eltérőek lehetnek a szervezet számára). Ezeket a beállításokat a microsoft javaslatok a táblák után ismertetik részletesen.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Az Azure AD-szerepkörök kiemelt identitáskezelési beállításai

| Szerepkör | MFA megkövetelése | Értesítés | Incidens jegy | Jóváhagyás megkövetelése | Approver | Aktiválás időtartama | Állandó rendszergazda |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Globális rendszergazda | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Egyéb globális rendszergazdák | 1 óra | Vészelérési fiókok |
| Exchange-rendszergazda | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | None | 2 óra | None |
| Ügyfélszolgálati rendszergazda | :x: | :x: | :heavy_check_mark: | :x: | None | 8 óra | None |

#### <a name="privileged-identity-management-settings-for-azure-resource-roles"></a>Az Azure-erőforrás-szerepkörök kiemelt identitáskezelési beállításai

| Szerepkör | MFA megkövetelése | Értesítés | Jóváhagyás megkövetelése | Approver | Aktiválás időtartama | Aktív rendszergazda | Aktív lejárat | Jogosult lejárat |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| A kritikus előfizetések tulajdonosa | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Az előfizetés egyéb tulajdonosai | 1 óra | None | n/a | 3 hónap |
| Kevésbé kritikus előfizetések felhasználói hozzáférési rendszergazdája | :heavy_check_mark: | :heavy_check_mark: | :x: | None | 1 óra | None | n/a | 3 hónap |
| Virtuális gépek közreműködője | :x: | :heavy_check_mark: | :x: | None | 3 óra | None | n/a | 6 hónap |

Az alábbi táblázat az egyes beállításokat ismerteti.

| Beállítás | Leírás |
| --- | --- |
| Szerepkör | Annak a szerepkörnek a neve, amelyhez a beállításokat definiálja. |
| MFA megkövetelése | Azt jelzi, hogy a jogosult felhasználónak végre kell-e hajtania az MFA-t a szerepkör aktiválása előtt.<br/><br/> :heavy_check_mark: **A Microsoft azt javasolja,** hogy az MFA-t minden rendszergazdai szerepkörhöz kényszerítse, különösen akkor, ha a szerepkörök vendégfelhasználókkal rendelkeznek. |
| Értesítés | Ha értéke igaz, a globális rendszergazda, a kiemelt szerepkör-rendszergazda és a szervezet biztonsági rendszergazdája e-mailben értesítést kap, amikor egy jogosult felhasználó aktiválja a szerepkört.<br/><br/>**Megjegyzés:** Egyes szervezetek nem rendelkeznek a rendszergazdai fiókokhoz kötött e-mail címmel, az e-mail értesítések levételéhez alternatív e-mail címet kell beállítania, hogy a rendszergazdák megkapják ezeket az e-maileket. |
| Incidens jegy | Azt jelzi, hogy a jogosult felhasználónak rögzítenie kell-e egy incidensjegyszámát a szerepkör aktiválásakor. Ezzel a beállítással a szervezet azonosíthatja az egyes aktiválásokat egy belső incidensszámmal a nem kívánt aktiválások csökkentése érdekében.<br/><br/> :heavy_check_mark: **A Microsoft azt javasolja,** hogy használja ki az incidens jegyszámok at kötni Privilegizált identitáskezelés a belső rendszer. Ez különösen hasznos a jóváhagyók, akiknek szükségük van összefüggésben az aktiváláshoz. |
| Jóváhagyás megkövetelése | Azt jelzi, hogy a jogosult felhasználónak szüksége van-e jóváhagyásra a szerepkör aktiválásához.<br/><br/> :heavy_check_mark: **A Microsoft azt javasolja,** hogy a legtöbb engedéllyel rendelkező szerepkörök jóváhagyását állítsa be. A jóváhagyási beállítással rendelkező leggyakoribb szerepkörök az összes kiemelt identitáskezelési ügyfél, globális rendszergazda, felhasználói rendszergazda, Exchange-rendszergazda, biztonsági rendszergazda és jelszórendszergazda használati mintái alapján. |
| Approver | Ha a jogosult szerepkör aktiválásához jóváhagyás szükséges, sorolja fel azokat, akiknek jóvá kell hagyniuk a kérelmet. Alapértelmezés szerint a Kiemelt identitáskezelés beállítja a jóváhagyó, hogy minden olyan felhasználó, aki egy kiemelt szerepkör-rendszergazda, függetlenül attól, hogy állandó vagy jogosult.<br/><br/>**Megjegyzés:** Ha egy felhasználó is jogosult egy Azure AD-szerepkörés a szerepkör jóváhagyója, akkor nem lesz képes jóváhagyni magukat.<br/><br/> :heavy_check_mark: **A Microsoft azt javasolja,** hogy a jóváhagyókat válassza ki, hogy azok legyenek, akik a leginkább ismerik az adott szerepkört és annak gyakori felhasználóit, nem pedig globális rendszergazdákat. |
| Aktiválás időtartama | Az az időtartam, amaddig, amíg a felhasználó aktiválódik a szerepkörben, mielőtt lejár. |
| Állandó rendszergazda | Azon felhasználók listája, akik a szerepkör állandó rendszergazdája lesznek (soha nem kell aktiválniuk).<br/><br/> :heavy_check_mark: **A Microsoft azt javasolja,** hogy a globális rendszergazdák kivételével minden szerepkörhöz nulla állandó rendszergazda legyen. Tudjon meg többet arról, hogy ki legyen jogosult, és ki legyen tartósan aktív része a tervnek. |
| Aktív rendszergazda | Az Azure-erőforrások esetében az aktív rendszergazda azoknak a felhasználóknak a listája, akiknek soha nem kell aktiválniuk a szerepkör használatához. Ez nem nevezik állandó rendszergazda, mint az Azure AD-szerepkörökben, mert beállíthat egy lejárati idő, amikor a felhasználó elveszíti ezt a szerepkört. |
| Aktív lejárat | Az Azure-erőforrás-szerepkörök aktív szerepkör-hozzárendelése a beállított időszak után lejár. Választhat 15 nap, 1 hónap, 3 hónap, 6 hónap, 1 év vagy tartósan aktív. |
| Jogosult lejárat | Az Azure-erőforrás-szerepkörök jogosult szerepkör-hozzárendelése a beállított időszak után lejár. Választhat 15 nap, 1 hónap, 3 hónap, 6 hónap, 1 év vagy véglegesen jogosult. |

## <a name="implement-your-solution"></a>Valósítsa meg a megoldást

A megfelelő tervezés alapja az az alap, amely alapján az Azure Active Directoryval sikeresen üzembe helyezhet egy alkalmazást.  Intelligens biztonságot és integrációt biztosít, amely leegyszerűsíti a bevezetést, miközben csökkenti a sikeres üzembe helyezési időt.  Ez a kombináció biztosítja, hogy az alkalmazás könnyen integrálható legyen, miközben enyhíti a végfelhasználók leállási idejét.

### <a name="identify-test-users"></a>Tesztfelhasználók azonosítása

Ebben a szakaszban azonosíthatja a felhasználók és vagy felhasználói csoportok egy csoportját a megvalósítás érvényesítéséhez. A tervezési szakaszban kiválasztott beállítások alapján azonosítsa az egyes szerepkörek teszteléséhez tesztelni kívánt felhasználókat.

> [!TIP]
> :heavy_check_mark: **A Microsoft azt javasolja,** hogy az egyes Azure AD-szerepkörök szolgáltatástulajdonosait tegye tesztfelhasználóvá, hogy megismerhessék a folyamatot, és a bevezetés belső érdekképviseleti szereplőjévé válhassanak.

Ebben a táblában azonosítsa azokat a tesztfelhasználókat, akik ellenőrzik, hogy az egyes szerepkörek beállításai működnek-e.

| Szerepkörnév | Felhasználók tesztelése |
| --- | --- |
| &lt;Szerepkörnév&gt; | &lt;Felhasználók a szerepkör teszteléséhez&gt; |
| &lt;Szerepkörnév&gt; | &lt;Felhasználók a szerepkör teszteléséhez&gt; |

### <a name="test-implementation"></a>Teszt megvalósítása

Most, hogy azonosította a tesztfelhasználókat, ezzel a lépéssel konfigurálhatja a kiemelt identitáskezelést a tesztfelhasználók számára. Ha a szervezet szeretné beépíteni a kiemelt identitáskezelési munkafolyamatot a saját belső alkalmazásába, ahelyett, hogy az Azure Portalon a Kiemelt identitáskezelés használatával, a kiemelt identitáskezelés összes műveletét is támogatja a [graph API-n](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root)keresztül.

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Kiemelt identitáskezelés konfigurálása Azure AD-szerepkörökhöz

1. [Konfigurálja az Azure AD szerepkör beállításait](pim-how-to-change-default-settings.md) a tervezett alapján.

1. Keresse meg az **Azure AD-szerepköröket,** kattintson a **Szerepkörök**elemre, majd válassza ki az imént konfigurált szerepkört.

1. A tesztfelhasználók csoportja esetében, ha már állandó rendszergazda, akkor jogosulttá teheti őket, ha megkeresi őket, és állandóból jogosulttá alakítja őket, ha a sorukban lévő három pontra kattint. Ha még nem rendelkeznek szerepkör-hozzárendelésekkel, [létrehozhat egy új jogosult hozzárendelést.](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role)

1. Ismételje meg az 1-3.

1. Miután beállította a tesztfelhasználókat, el kell küldenie nekik az [Azure AD-szerepkör aktiválásának linkjét.](pim-how-to-activate-role.md)

#### <a name="configure-privileged-identity-management-for-azure-resource-roles"></a>Kiemelt identitáskezelés konfigurálása Azure-erőforrás-szerepkörökhöz

1. [Konfigurálja az Azure-erőforrás szerepkör beállításait](pim-resource-roles-configure-role-settings.md) egy előfizetésen vagy erőforráson belül, amelyet tesztelni szeretne.

1. Keresse meg az adott előfizetés **Azure-erőforrásait,** és kattintson a **Szerepkörök**elemre, válassza ki az imént konfigurált szerepkört.

1. A tesztfelhasználók csoportja esetében, ha már aktív rendszergazda, akkor jogosulttá teheti őket, ha megkeresi őket, és [frissíti a szerepkör-hozzárendelésüket.](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment) Ha még nem rendelkeznek a szerepkörrel, [új szerepkört rendelhet hozzá.](pim-resource-roles-assign-roles.md#assign-a-role)

1. Ismételje meg az 1-3.

1. Miután beállította a tesztfelhasználókat, el kell küldenie nekik az [Azure-erőforrásszerepkör aktiválásának linkjét.](pim-resource-roles-activate-your-roles.md)

Ebben a fázisban ellenőrizze, hogy a szerepkörökhöz beállított összes konfiguráció megfelelően működik-e. Az alábbi táblázat segítségével dokumentálhatja a teszteket. Ezt a szakaszt is érdemes használnia az érintett felhasználókkal való kommunikáció optimalizálásához.

| Szerepkör | Várt viselkedés az aktiválás során | Tényleges eredmények |
| --- | --- | --- |
| Globális rendszergazda | (1) Többes faszükségtelmi szerződés megkövetelése<br/>(2) Jóváhagyás megkövetelése<br/>(3) A jóváhagyó értesítést kap, és jóváhagyhatja<br/>(4) A szerepkör az előre beállított idő után lejár |  |
| Az előfizetés tulajdonosa *X* | (1) Többes faszükségtelmi szerződés megkövetelése<br/>(2) a jogosult hozzárendelés a beállított időszak után lejár |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>A kiemelt identitáskezelés kommunikációja az érintett érdekelt felekkel

Kiemelt identitáskezelés telepítése további lépéseket vezet be a kiemelt szerepkörök felhasználói számára. Bár a kiemelt identitáskezelés jelentősen csökkenti a kiemelt identitásokkal társított biztonsági problémákat, a módosítást hatékonyan kell közölni a bérlői szintű üzembe helyezés előtt. Az érintett rendszergazdák számától függően a szervezetek gyakran úgy döntenek, hogy belső dokumentumot, videót vagy e-mailt hoznak létre a módosításról. Ezek a közlemények gyakran szerepelnek:

- Mi az a PIM
- Mi az előny a szervezet számára?
- Ki lesz hatással
- Mikor kerül bevezetésre a PIM-
- Milyen további lépésekre lesz szükség ahhoz, hogy a felhasználók aktiválják szerepkörüket?
    - Önnek kell küldeni linkeket a dokumentáció:
    - [Azure AD-szerepkörök aktiválása](pim-how-to-activate-role.md)
    - [Azure-erőforrás-szerepkörök aktiválása](pim-resource-roles-activate-your-roles.md)
- A PIM-mel kapcsolatos problémák elérhetőségi adatai vagy ügyfélszolgálati hivatkozása

> [!TIP]
> :heavy_check_mark: **A Microsoft azt javasolja,** hogy állítson be időt az ügyfélszolgálati/támogatási csapatával, hogy végigvezethesse őket a Kiemelt identitáskezelési munkafolyamaton (ha a szervezet belső informatikai támogatási csapattal rendelkezik). Adja meg nekik a megfelelő dokumentációt, valamint elérhetőségi adatait.

### <a name="move-to-production"></a>Átállás éles üzemre

Miután a tesztelés befejeződött és sikeres, helyezze át a kiemelt identitáskezelés éles környezetbe a tesztelési fázisok összes lépésének ismétlésével a kiemelt identitáskezelési konfigurációban megadott szerepkörök összes felhasználója számára. Az Azure AD-szerepkörök kiemelt identitáskezelése esetén a szervezetek gyakran tesztelik és vezetik be a kiemelt identitáskezelést a globális rendszergazdák számára, mielőtt tesztelnék és bevezetnék a kiemelt identitáskezelést más szerepkörökhöz. Eközben az Azure-erőforrás, szervezetek általában teszt és roll-out privilegizált identitáskezelés egy Azure-előfizetés egy időben.

### <a name="in-the-case-a-rollback-is-needed"></a>Abban az esetben, ha vissza kell vonni

Ha a kiemelt identitáskezelés nem működött megfelelően az éles környezetben, a következő visszaállítási lépések segíthetnek visszaállítani egy ismert jó állapotba a kiemelt identitáskezelés beállítása előtt:

#### <a name="azure-ad-roles"></a>Azure AD-szerepkörök

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**
1. Kattintson **az Azure AD-szerepkörök,** majd **a Szerepkörök**elemre.
1. Minden konfigurált szerepkörhöz kattintson a három pontra (**...**) minden jogosult hozzárendeléssel rendelkező felhasználó esetében.
1. A szerepkör-hozzárendelés állandóvá tételéhez kattintson az **Állandóvá** tétel gombra.

#### <a name="azure-resource-roles"></a>Azure-erőforrásszerepkörök

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**
1. Kattintson **az Azure-erőforrások,** majd kattintson egy előfizetés vagy erőforrás vissza szeretne igényelni.
1. Kattintson **a Szerepkörök gombra.**
1. Minden konfigurált szerepkörhöz kattintson a három pontra (**...**) minden jogosult hozzárendeléssel rendelkező felhasználó esetében.
1. A szerepkör-hozzárendelés állandóvá tételéhez kattintson az **Állandóvá** tétel gombra.

## <a name="next-steps-after-deploying"></a>Következő lépések az üzembe helyezés után

A kiemelt identitáskezelés éles környezetben történő sikeres üzembe helyezése jelentős előrelépést jelent a szervezet kiemelt identitásainak védelme szempontjából. A kiemelt identitáskezelés telepítésével további kiemelt identitáskezelési funkciókat, amelyeket a biztonság és a megfelelőség érdekében kell használni.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Kiemelt identitáskezelési riasztások használata a kiemelt hozzáférés védelméhez

A kiemelt identitáskezelés beépített riasztási funkcióját kell használnia a bérlő jobb védelme érdekében. További információt a Biztonsági riasztások című [témakörben talál.](pim-how-to-configure-security-alerts.md#security-alerts) Ezek a riasztások a következők: a rendszergazdák nem használnak kiemelt szerepköröket, szerepkörök vannak hozzárendelve a kiemelt identitáskezelés, szerepkörök aktiválása túl gyakran és így tovább. A szervezet teljes körű védelme érdekében rendszeresen át kell néznie a riasztások listáját, és ki kell javítania a problémákat. Az értesítéseket a következőképpen tekintheti meg és javíthatja ki:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**
1. Kattintson **az Azure AD-szerepkörök,** majd **a Riasztások**elemre.

> [!TIP]
> :heavy_check_mark: **A Microsoft azt javasolja,** hogy azonnal foglalkozzon a magas súlyosllyal jelölt összes riasztással. Közepes és alacsony súlyosságú riasztások esetén legyen tájékozott, és módosítsa, ha úgy véli, hogy biztonsági fenyegetés van.

Ha az adott riasztások bármelyike nem hasznos, vagy nem vonatkozik a szervezetre, bármikor elvetheti a riasztást a riasztások oldalon. Ezt az elbocsátást később az Azure AD-beállítások lapon bármikor visszaállíthatja.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Ismétlődő hozzáférési felülvizsgálatok beállítása a szervezet kiemelt identitásainak rendszeres naplózásához

Access-felülvizsgálatok a legjobb módja annak, hogy kérje a kiemelt szerepkörökkel vagy adott ellenőrzők, hogy minden felhasználónak szüksége van a kiemelt identitás. A hozzáférési felülvizsgálatok nagyszerűek, ha csökkenteni szeretné a támadási felületet, és megfelelő szeretne maradni. A hozzáférés-felülvizsgálat elindításáról további információt az [Azure AD-szerepkörök hozzáférési felülvizsgálata](pim-how-to-start-security-review.md) és az [Azure-erőforrás-szerepkörök hozzáférési felülvizsgálata című témakörben talál.](pim-resource-roles-start-access-review.md) Egyes szervezetek esetében a hozzáférési felülvizsgálat elvégzéséhez rendszeres hozzáférés-felülvizsgálat szükséges ahhoz, hogy megfeleljen a törvényeknek és rendeleteknek, míg mások számára a hozzáférési felülvizsgálat a legjobb módja annak, hogy a szervezeten belül érvényesítse a legkevésbé bizalmas főt.

> [!TIP]
> :heavy_check_mark: **A Microsoft azt javasolja,** hogy állítsa be negyedéves hozzáférési felülvizsgálatok az összes Azure AD és Azure erőforrás-szerepkörök.

A legtöbb esetben az Azure AD-szerepkörök véleményezője maguk a felhasználók, míg az Azure-erőforrás-szerepkörök véleményezője az előfizetés tulajdonosa, amelyben a szerepkör van. Azonban gyakran ez a helyzet, ha a vállalatok kiemelt fiókokkal rendelkeznek, amelyek nem kapcsolódnak egy adott személy e-mail címéhez. Ezekben az esetekben senki sem olvassa el és nem ellenőrzi a hozzáférést.

> [!TIP]
> :heavy_check_mark: **A Microsoft azt javasolja,** hogy adjon meg egy másodlagos e-mail címet minden olyan, kiemelt szerepkör-hozzárendeléssel rendelkező fiókhoz, amely nem kapcsolódik rendszeresen ellenőrzött e-mail-címhez

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Hozza ki a legtöbbet naplójából a biztonság és a megfelelőség javítása érdekében

A napló az a hely, ahol naprakész maradhat, és megfelelhet a szabályzatoknak. A Kiemelt identitáskezelés jelenleg a szervezet összes előzményének 30 napos előzményeit tárolja a naplójában, beleértve a következőket:

- A jogosult szerepkörök aktiválása/kikapcsolása
- Szerepkör-hozzárendelési tevékenységek a kiemelt identitáskezelésen belül és kívül
- A szerepkör-beállítások módosításai
- A szerepkör aktiválásához szükséges tevékenységek kérése/jóváhagyása/megtagadása a jóváhagyási beállítással
- Frissítés a riasztásokhoz

Ezeket a naplókat globális rendszergazda vagy kiemelt szereprendszergazda esetén érheti el. További információt az [Azure AD-szerepkörök naplózási előzményei](pim-how-to-use-audit-log.md) és [az Azure-erőforrás-szerepkörök naplózási előzményei című témakörben talál.](azure-pim-resource-rbac.md)

> [!TIP]
> :heavy_check_mark: **A Microsoft azt javasolja,** hogy legalább egy rendszergazdával olvassa el heti rendszerességgel az összes naplózási eseményt, és exportálja a naplózási eseményeket havonta.

Ha hosszabb ideig szeretné automatikusan tárolni a naplózási eseményeket, a kiemelt identitáskezelés naplózási naplója automatikusan szinkronizálódik az [Azure AD naplózási naplóiba.](../reports-monitoring/concept-audit-logs.md)

> [!TIP]
> :heavy_check_mark: **A Microsoft azt javasolja,** hogy állítsa be az [Azure-naplófigyelést](../reports-monitoring/concept-activity-logs-azure-monitor.md) az Azure storage-fiók naplózási eseményeinek archiválásához a biztonság és a megfelelőség szükségessége érdekében.
