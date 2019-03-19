---
title: Az Azure AD Privileged Identity Management (PIM) üzembe helyezése |} A Microsoft Docs
description: Ismerteti, hogyan lehet az Azure AD Privileged Identity Management (PIM) telepítésének megtervezését.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/08/2019
ms.author: rolyon
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 804000b781ca1fc8d9f8834e3a8c0f0a78c52c11
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57878482"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Az Azure AD Privileged Identity Management (PIM) üzembe helyezése

Ez a részletes útmutató ismerteti az Azure AD Privileged Identity Management (PIM) Szolgáltatásának a szervezet telepítésének megtervezését.

> [!TIP]
> Ebben a dokumentumban jelölésű elem jelenik meg:
>
> :heavy_check_mark: **A Microsoft azt javasolja**
>
> Általános javaslatok, és ha azok a alkalmazni az adott nagyvállalati igényeknek csak akkor kell megvalósítania.

## <a name="step-1-learn-about-pim"></a>1. lépés További tudnivalók a PIM

Az Azure AD Privileged Identity Management (PIM) segít a kiemelt jogosultságú rendszergazdai szerepkörök kezelése az Azure ad-ben, Azure-erőforrások és más Microsoft Online Services. Egy olyan világban, ahol az emelt szintű identitások társítva, vagy elfelejti a PIM biztosít megoldások, például igény szerinti elérése, a kérelem-jóváhagyási munkafolyamatokat és a teljes körűen integrált hozzáférési felülvizsgálatok így azonosíthatja, tárhat fel, és az emelt szintű rosszindulatú tevékenységek megakadályozása szerepkörök valós időben. A szervezeten belül a kiemelt szerepkörök kezelése a PIM telepítése azonban nagymértékben csökkenti a kockázatokat közben felszínre hozza a kiemelt szerepkörök tevékenységeit kapcsolatos értékes információkat.

### <a name="business-value-of-pim"></a>A PIM üzleti értékét

**Kockázatkezelést** -elve tartat be a szervezet biztonságának [legalacsonyabb jogosultsági hozzáférés](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) és igény szerinti elérése. Minimalizálja a felhasználók számára a kiemelt szerepkörökhöz és korlátozást előírni jóváhagyások és a többtényezős hitelesítés jogosultságszint-emelési kérés állandó hozzárendelések számát, nagy mértékben csökkentheti emelt szintű hozzáférés a szervezet biztonsági kockázatokat. Kényszerítés a legalacsonyabb jogosultsági és just-in-time hozzáférés lehetővé teszi, hogy a kiemelt szerepkörökhöz való hozzáférésre előzményeinek megtekintése és nyomon követheti a biztonsági problémákat, mivel azok.

**Megfelelőségi és szabályozási** -PIM telepítése folyamatos identitáskezelést környezetet hoz létre. Emelt szintű identitások Just-in-time jogosultságszint-emelési lehetővé teszi a PIM-beli nyomon követheti, emelt szintű hozzáférés tevékenységek a szervezetben. Akkor is megtekinthetik és értesítések küldése az állandó és jogosult szerepkörök a szervezeten belüli összes hozzárendelést. Hozzáférési felülvizsgálat, keresztül rendszeresen naplózása és eltávolíthatja szükségtelen emelt jogosultsági szintű identitásait, és győződjön meg arról, hogy a szervezet legszigorúbb identitások, a hozzáférés és a biztonsági szabványoknak megfelelő.

**Csökkentheti a költségeket** – megszüntetésével hatékonysági, emberi hiba és biztonsági problémák PIM megfelelően telepítésével csökkentheti a költségeket. Az eredmény az emelt szintű identitások, amelyek költséges és bonyolult helyreállíthatók társított kibertámadások crimes csökkentését. A PIM is segít a szervezet társított adatok eléréséhez naplózás esetén, a szabályozásoknak és előírásoknak megfelelő költségeinek csökkentése.

További információkért lásd: [Mi az Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="licensing-requirements"></a>Licencelési követelményeket

A PIM használatát tervezi, a következő fizetett vagy a próbaverziós licencek a címtárban kell rendelkeznie:

- Prémium szintű Azure AD P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

További információkért lásd: [Licenckövetelményekért a PIM használatát tervezi](subscription-requirements.md).

### <a name="key-pim-terminology"></a>Kulcs a PIM-terminológia

| Kifejezés vagy fogalom | Leírás |
| --- | --- |
| Jogosult | Szerepkör-hozzárendelés, amelyhez a felhasználót a szerepkör használatához egy vagy több művelet végrehajtásához szükséges. Ha egy felhasználó jogosult szerepkör lett végrehajtva, ez azt jelenti, ha kiemelt feladatait van szükségük a szerepkör aktiválásához. Nincs megadott valaki egy állandó jogosult szerepkör-hozzárendelés és hozzáférési nincs különbség. Az egyetlen különbség, hogy néhány felhasználó nem kell olyan folyamatosan. |
| aktiválás | Folyamat használatához egy szerepkör, amely a felhasználó nem jogosult egy vagy több műveletet hajt végre. Műveletek tartalmazhat, egy üzleti indoklásának megadása, vagy jóváhagyási kérése a kijelölt jóváhagyókat többtényezős hitelesítés (MFA)-ellenőrzés elvégzése. |
| – igény (szerinti JIT) hozzáférési | A modell, amelyben a felhasználók megkapják a rendszerjogosultságú feladatait ideiglenes engedélyek, amely megakadályozza, hogy a rosszindulatú vagy jogosulatlan felhasználók hozzáférjenek a engedélyekkel rendelkezik lejárta után. Hozzáférés csak akkor, ha a felhasználók szüksége van rá. |
| legkisebb jogosultság hozzáférés elve | A feladatok elvégzéséhez jogosultak, amelyben minden felhasználó csak a minimális jogosultságokkal rendelkező megadott ajánlott biztonsági eljárás szükséges. Ez az eljárás minimálisra csökkentik a globális rendszergazdák, és helyette használja az adott rendszergazdai szerepköröket bizonyos forgatókönyvek esetén. |

További információkért lásd: [terminológia](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-pim-works"></a>Hogyan működik a PIM magas szintű áttekintése

1. A PIM be van állítva, így a felhasználók jogosultak a kiemelt szerepkörökhöz.
1. Amikor egy jogosult felhasználónak kell használnia a kiemelt szerepkörű, azok az PIM-ben a szerepkör aktiválása.
1. A PIM-beállítás a szerepkör, attól függően, a felhasználónak végre kell hajtania bizonyos lépéseket (például a többtényezős hitelesítés elvégzése, beolvasása a jóváhagyásra vagy egy OK megadása.)
1. Miután a felhasználó sikeresen aktiválja a szerepkörét, a szerepkör egy előre konfigurált időtartamon fognak kapni.
1. A rendszergazdák megtekinthetik a PIM-tevékenységet előzményeit v auditovacím protokolu. Ezek tovább biztonságossá tétele a bérlők, és megfelelnek a PIM-szolgáltatások, mint a hozzáférési felülvizsgálatok és riasztások segítségével.

További információkért lásd: [Mi az Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-pim"></a>Szerepkörök, amelyek felügyelhetők a PIM

**Az Azure AD-szerepkörök** – ezek a szerepkörök felelnek az Azure Active Directory (például a globális rendszergazda, Exchange-rendszergazda és biztonsági rendszergazda) belül az összes címtárbeli szerepkörök. Tudjon meg többet a szerepkörökről és a funkcióikat [rendszergazdája szerepkör engedélyei az Azure Active Directoryban](../users-groups-roles/directory-assign-admin-roles.md). A szerepköröket a rendszergazdákat meghatározása kapcsolatos útmutatásért lásd: [legalább kiemelt szerepköröket feladat](../users-groups-roles/roles-delegate-by-task.md).

**Azure-erőforrások szerepköreihez** – ezek a szerepkörök az Azure-erőforrás, erőforráscsoport, előfizetés vagy a felügyeleti csoport vannak csatolva. A PIM mindkét beépített szerepkörök, például a tulajdonos, a felhasználói hozzáférés rendszergazdája és a közreműködő, just-in-time hozzáférést biztosít, valamint [egyéni szerepkörök](../../role-based-access-control/custom-roles.md). Az Azure erőforrás-szerepkörökkel kapcsolatos további információkért lásd: [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md).

További információkért lásd: [szerepkörök nem kezelheti a PIM](pim-roles.md).

## <a name="step-2-plan-your-deployment"></a>2. lépés Az üzembe helyezés megtervezése

Ez a szakasz tárgyalja, hogy mit kell tennie a szervezet a PIM telepítése előtt. Ez elengedhetetlen, kövesse az utasításokat, és azok végigvezeti Önt a szabott emelt szintű identitások a szervezet igényeinek leginkább megfelelő csomagot létrehozni ebben a szakaszban a fogalmak értelmezését.

### <a name="identify-your-stakeholders"></a>A résztvevők azonosítása

Az alábbi szakasz segítségével, amelyek szerepet játszanak a projekt összes résztvevők azonosítása, és jelentkezzen ki, tekintse át és naprakész lehet szükség. Ez magában foglalja a PIM az Azure AD-szerepkörök és az Azure-erőforrások szerepköreihez tartozó PIM üzembe helyezésének külön táblákban. Adja hozzá a következő táblázat a szervezet számára megfelelő érintettek.

- EZÉRT = jelentkezzen ki a projekt
- Az R = tekintse át a projektet, és adja meg a bemeneti
- Szeretnék e projekt tájékoztatáson =

#### <a name="stakeholders-pim-for-azure-ad-roles"></a>Érintettek: A PIM az Azure AD-szerepkörök

| Name (Név) | Szerepkör | Műveletek |
| --- | --- | --- |
| Név és e-mail | **Identitás-mérnök vagy az Azure globális rendszergazda**<br/>Az identitás meghatározása, hogy ez a változás az alapvető identity management infrastruktúra a szervezetben való igazításának felelős csapat képviselőjével. | ÍGY/R/I |
| Név és e-mail | **Szolgáltatás tulajdonosa / manager sor**<br/>Egy szolgáltatás vagy -szolgáltatásokra informatikai tulajdonosai képviselőjével. Azok a kulcs döntéseket, és segítsen a PIM vezethet be a csapat számára. | ÍGY/R/I |
| Név és e-mail | **Biztonsági tulajdonosa**<br/>A biztonsági csapat, amely képes jelentkezzen ki, hogy a csomag megfelel a szervezet biztonsági követelményeinek képviselőjével. | EZÉRT / R |
| Név és e-mail | **Informatikai manager támogatás vagy segélyszolgálat**<br/>A szervezet IT-támogatási képviselőjével, amely bemeneti biztosít a segélyszolgálat szempontból a módosítása a támogathatóság. | R/I |
| Név és e-mail próbaüzem felhasználóinak | **Kiemelt szerepkörű felhasználók**<br/>A csoport felhasználói, amelynek a privileged identity management van megvalósítva. Akkor kell ismernie a szerepkörök aktiválására PIM implementálása után. | I |

#### <a name="stakeholders-pim-for-azure-resource-roles"></a>Érintettek: A PIM az Azure-erőforrásszerepkörök

| Name (Név) | Szerepkör | Műveletek |
| --- | --- | --- |
| Név és e-mail | **Előfizetés és erőforrás-tulajdonos**<br/>Az egyes előfizetésekhez vagy erőforrás, amely a PIM használata telepítendő informatikai tulajdonosai képviselőjével | ÍGY/R/I |
| Név és e-mail | **Biztonsági tulajdonosa**<br/>A biztonsági csapat, amely képes jelentkezzen ki, hogy a csomag megfelel a szervezet biztonsági követelményeinek képviselőjével. | EZÉRT / R |
| Név és e-mail | **Informatikai manager támogatás vagy segélyszolgálat**<br/>A szervezet IT-támogatási képviselőjével, amely bemeneti biztosít a segélyszolgálat szempontból a módosítása a támogathatóság. | R/I |
| Név és e-mail próbaüzem felhasználóinak | **Az RBAC-szerepkör felhasználói**<br/>A csoport felhasználói, amelynek a privileged identity management van megvalósítva. Akkor kell ismernie a szerepkörök aktiválására PIM implementálása után. | I |

### <a name="enable-pim"></a>PIM engedélyezése

A tervezési folyamat keretében, előbb futtatnia kell beleegyezik abba, és a PIM engedélyezése a következő a [használatához a PIM dokumentum](pim-getting-started.md). PIM engedélyezése hozzáférést biztosít bizonyos funkciókat, amelyeket kifejezetten a telepítés érdekében.

Ha a cél a PIM telepítése az Azure-erőforrásokhoz, kövesse a [Fedezze fel az Azure-erőforrások kezelése a PIM dokumentum](pim-resource-roles-discover-resources.md). Csak egyes erőforrás, az erőforráscsoportot és az előfizetés tulajdonosai tudják észlelje azokat a PIM belül. Ha Ön globális rendszergazda próbál PIM az Azure-erőforrások üzembe helyezése, érdemes [jogosultságszintjének emelése az összes Azure-előfizetések kezelése](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) saját kezűleg hozzáférést adhat a címtárban az összes Azure-erőforrásokhoz a felderítéshez. Azonban azt javasoljuk, hogy kap jóváhagyást az egyes az előfizetés-tulajdonost az erőforrásaikat a PIM kezelése előtt.

### <a name="enforce-principle-of-least-privilege"></a>Legalacsonyabb jogosultsági szint elvének kényszerítése

Győződjön meg arról, hogy a szervezet az Azure AD esetében a legalacsonyabb jogosultsági szint elvének kényszerítve, és az Azure-erőforrások szerepköreihez.

#### <a name="azure-ad-roles"></a>Azure AD-szerepkörök

Az Azure AD-szerepkörök szokás a szervezetek számára, hogy a globális rendszergazdai szerepkör a rendszergazdák, amikor a legtöbb rendszergazda csak kell egy jelentős számú vagy két adott rendszergazdai szerepkörök hozzárendelése. Nem felügyelt kiemelt szerepkörű hozzárendelések is általában maradhat.

> [!NOTE]
> A szerepkör-delegálás közös alkalmazásmegoldásokra:
>
> - A rendszergazda az Exchange felelős a globális rendszergazdai szerepkör van megadva, annak ellenére, hogy csak az Exchange-rendszergazdai szerepkör, a mindennapos munkájukhoz végrehajtásához szükséges.
> - A globális rendszergazdai szerepkör van hozzárendelése az rendszergazdájának, mert az rendszergazdájának kell a biztonsági és a SharePoint-rendszergazdai szerepköröket, és könnyebben csak egy szerepkört.
> - A rendszergazda hozzá volt rendelve a feladat végrehajtása régen biztonsági rendszergazdai szerepkörrel, de soha nem lett eltávolítva.

Kövesse az alábbi lépéseket az Azure AD-szerepkörökhöz tartozó minimális jogosultság elvének kényszerítésére.

1. A szerepkörök közül értelmezése kódmintáiban a [elérhető az Azure AD-rendszergazdai szerepköröket](../users-groups-roles/directory-assign-admin-roles.md#available-roles). Ön és csapata is kell hivatkoznia [rendszergazdai szerepkörök az Azure AD identity feladat](../users-groups-roles/roles-delegate-by-task.md), amely leírja, hogy a legalacsonyabb jogosultsági szintű szerepkört az adott feladatok.

1. Listája, aki rendelkezik az emelt szintű szerepkörök a szervezetben. Használhatja a [PIM varázsló](pim-security-wizard.md#run-the-wizard) az alábbihoz hasonló lap beolvasásához.

    ![Kiemelt szerepkörök észlelése](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. A szervezet minden globális rendszergazda, a megtudhatja, miért szükséges a szerepkör. Alapján az előző dokumentációját, olvasása, ha a személy feladatot végezhet el egy vagy több részletes rendszergazdai szerepköröket, kell azokat távolítani a globális rendszergazdai szerepkör és hozzárendeléseket ennek megfelelően az Azure Active Directory belül (referenciaként: A Microsoft jelenleg csak rendelkezik a globális rendszergazdai szerepkörrel rendelkező körülbelül 10 rendszergazdák. További információ: [hogyan használja a Microsoft a PIM](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)).

1. Minden más Azure AD-szerepkörök tekintse át a hozzárendelések listáját, azonosíthatja a rendszergazdák, akik már nem szükséges a szerepkör, és azokat eltávolítsa a hozzárendeléseket.

3. és 4 automatizálását, a hozzáférési felülvizsgálat függvény PIM belül használhat. Leírt lépések végrehajtásával [az Azure AD-szerepkörök hozzáférési felülvizsgálat indítása a PIM](pim-how-to-start-security-review.md), beállíthatja a hozzáférési felülvizsgálat minden Azure AD-szerepkörhöz, amely egy vagy több tagja van.

![Hozzáférési felülvizsgálat létrehozása](./media/pim-deployment-plan/create-access-review.png)

A felülvizsgálatot állítsa be **tagok (saját)**. Ez küld egy e-mailt el a szerepkör tagjai az összes használhassa őket, ellenőrizze, hogy szükségük van-e a hozzáférést. Emellett kapcsolja **a jóváhagyás oka szükséges** speciális beállításai között szereplő, hogy a felhasználók is állapot miért szükséges a szerepkör. Ezen információ alapján lesz távolítsa el a felhasználókat a szükségtelen szerepkörök, és a globális rendszergazdák esetén a részletes rendszergazdai szerepkörök delegálása.

A hozzáférési felülvizsgálatok az e-mailek értesítendő személyeket és az azok a szerepkörök hozzáférés felülvizsgálata támaszkodnak. Ha a privilegizált fiókok, amelyek nem rendelkeznek társított e-mailek, ügyeljen arra, töltse fel azokat a másodlagos e-mail mező. További információkért lásd: [az Azure AD proxyAddresses attribútum](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Azure-erőforrásszerepkörök

Az Azure-előfizetések és erőforrások állíthat be hozzáférési felülvizsgálati folyamat hasonló a szerepkörök az egyes előfizetésekhez vagy erőforrás áttekintéséhez. Ez a folyamat célja az egyes előfizetésekhez vagy erőforrás is feltárhatja, hogy távolítsa el a felesleges hozzárendelések csatolt tulajdonosi és felhasználói hozzáférés rendszergazdája hozzárendeléseinek minimalizálása érdekében. Azonban szervezetek gyakran az ilyen feladatok delegálása az egyes előfizetésekhez vagy erőforrás tulajdonosának, mert azok jobban megérthesse a meghatározott szerepkörök (különösen az egyéni szerepkörök).

Ha Ön globális rendszergazda szerepkörrel rendelkező rendszergazda próbál a PIM telepítése az Azure-erőforrásokhoz a szervezetben, érdemes [jogosultságszintjének emelése az összes Azure-előfizetések kezelése](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) minden egyes előfizetés eléréséhez. Ezután keresse meg az egyes előfizetés tulajdonosa és vele együttműködve távolítsa el a felesleges hozzárendelések, és minimalizálja a tulajdonosi szerepkör-hozzárendelés.

Azure-előfizetések a tulajdonosi szerepkörrel rendelkező felhasználók is igénybe vehetik a [hozzáférési felülvizsgálatok az Azure-erőforrások](pim-resource-roles-start-access-review.md) naplózása és az Azure AD-szerepkörökhöz tartozó korábban leírt folyamat hasonló a szükségtelen szerepkör-hozzárendelések eltávolítása.

### <a name="decide-which-role-assignments-should-be-protected-by-pim"></a>Döntse el, melyik szerepkör-hozzárendelések kell ellátni a PIM

Után törlése a kiemelt szerepkörök hozzárendeléseit a szervezetben, szüksége lesz döntse el, mely szerepköröket a PIM védelméhez.

Ha egy szerepkör a PIM által védett, hozzárendelve jogosult felhasználók kell jogosultságszint-emelés használata a jogosultságokat, a szerepkör által biztosított. A jogosultságszint-emelési folyamatot is behatolástesztelést, a multi-factor authentication végrehajtása, illetve aktiválása miért ok megadása. A PIM is nyomon követhető magasságadatok értesítések használatával, és a PIM és az Azure AD naplózási eseménynaplók.

Kiválasztása a szerepköröket a PIM védelme nehéz lehet, és minden egyes szervezet eltérő lesz. Ez a szakasz az ajánlott eljárás tanácsokat nyújt az Azure ad és az Azure-erőforrások szerepköreihez.

#### <a name="azure-ad-roles"></a>Azure AD-szerepkörök

Fontos rangsorolására védelme az Azure AD-szerepkörök, amelyek a legtöbb engedélyek száma. Az összes PIM-ügyfelek körében használati mintái alapján, az első 10 Azure AD-szerepköröket a PIM kezeli vannak:

1. Globális rendszergazda
1. Biztonsági rendszergazda
1. Felhasználói adminisztrátor
1. Exchange-rendszergazda
1. SharePoint-rendszergazda
1. Intune-rendszergazda
1. Biztonsági olvasó
1. Szolgáltatás-rendszergazda
1. Számlázási rendszergazda
1. Skype Vállalati verzió-rendszergazda

> [!TIP]
> :heavy_check_mark: **A Microsoft javasolja** kezelhetők a globális rendszergazdák és a PIM használatával az első lépés, azok, amelyekre a teheti meg, a legtöbb kárt Ha sérül a biztonsági rendszergazdák.

Fontos figyelembe venni, milyen adatokat, és engedéllyel a szervezete számára leginkább bizalmas. Például néhány szervezet érdemes a Power BI adminisztrátori szerepkörének vagy a PIM használata, mert a fióktulajdonoshoz adatelérésre és/vagy módosításához a core-munkafolyamatok csapatok rendszergazdai szerepkörükhöz védelme.

Ha vendég felhasználók a szerepköröket, azok különösen támadásokkal szemben.

> [!TIP]
> :heavy_check_mark: **A Microsoft javasolja** vendégfelhasználók feltört vendégfelhasználói fiókok kockázatának csökkentése érdekében a PIM használata az összes szerepkör felügyelt.

Olvasó szerepkörök, például a címtár olvasói, az üzenet Center olvasó és a biztonsági olvasó vannak más feltételezhetően kevésbé fontos, hogy nem rendelkezik írási engedéllyel a többi szerepkörhöz képest. Azonban úgy találtuk, egyes ügyfelek ezeket a szerepköröket is védheti, mert lehet, hogy ezek a fiókok hozzáférést szerzett, akik tudni elolvasni a bizalmas adatokat, például személyazonosításra alkalmas adatokat (PII). Akkor figyelembe kell venniük a olvasói szerepkörök a szervezet kell-e kezelni a PIM használata során.

#### <a name="azure-resource-roles"></a>Azure-erőforrásszerepkörök

Amikor eldönti, hogy melyik szerepkör-hozzárendelések felügyelje a PIM használata Azure-beli erőforráshoz, először azonosítania kell az előfizetések/erőforrások, amelyek a legtöbb létfontosságú a szervezet számára. Az ilyen előfizetések erőforrások példái a következők:

- Erőforrások, amelyek a leginkább bizalmas adatok
- Alapvető erőforrások, függő érintkező alkalmazások számára

Ha hiba történt annak eldöntésében, hogy melyik előfizetések/erőforrások legfontosabb kellene globális rendszergazda, akkor az elérhető előfizetések tulajdonosai gyűjtse össze az egyes előfizetések által felügyelt erőforrások listáját a szervezetben. Ezután együttműködve kell az erőforrás alapján abban az esetben sérül a biztonságuk súlyossági szint (alacsony, közepes, nagy) előfizetés tulajdonosai. A PIM ezt a súlyossági szintet alapján előnyben részesíti a erőforrások kezelésére.

> [!TIP]
> :heavy_check_mark: **A Microsoft javasolja** dolgozik, előfizetés és az erőforrások tulajdonosai PIM munkafolyamat bizalmas előfizetések/erőforrások belül minden szerepkör beállítása kritikus fontosságú szolgáltatásokat.

A PIM használata Azure-erőforrások időhöz kötött szolgáltatásfiókok támogatja. Kezelje szolgáltatásfiókok teljesen megegyezik, hogy hogyan normál felhasználói fiókot, kellene kezelnie.

Az előfizetések és-erőforrások, amelyek nem ilyen kritikusak nem kell minden szerepkör beállítása a PIM. Azonban továbbra is védeni kell a tulajdonosi és felhasználói hozzáférés rendszergazdai szerepköröket a PIM.

> [!TIP]
> :heavy_check_mark: **A Microsoft javasolja** tulajdonosi és felhasználói hozzáférés rendszergazdai szerepeket előfizetések és-erőforrások a PIM használata kezelheti.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Döntse el, melyik szerepkör-hozzárendelések állandó és jogosult kell lennie.

Miután eldöntötte a PIM által felügyelendő szerepkörök listája, el kell döntenie, hogy mely felhasználók kell kapnia a állandóan aktív szerepkör és a jogosult szerepkör. Állandóan aktív szerepkörök a hozzárendelt keresztül az Azure Active Directory és az Azure-erőforrások, amíg a jogosult szerepkörök csak lehet hozzárendelni a PIM normál szerepkörök.

> [!TIP]
> :heavy_check_mark: **A Microsoft javasolja** rendelkezik az Azure AD-szerepkörök és az Azure-erőforrás-től eltérő szerepkörök az ajánlott nulla véglegesen aktív hozzárendelések [két break üvegből vészelérési fiókok](../users-groups-roles/directory-emergency-access.md), kell rendelkeznie, amely a állandó globális rendszergazdai szerepkör.

Annak ellenére, hogy a nulla állandó rendszergazdai javasoljuk, hogy néha nehézséget jelent a szervezet számára, ennek érdekében azonnal. Az alábbiakban a döntés meghozatalakor megfontolandó szempontok:

- Gyakorisága jogosultságszint-emelési – Ha a felhasználó csak egyszer, a kiemelt jogosultságú hozzárendelés nem rendelkeznek az állandó hozzárendelés. Másrészről Ha a felhasználónak a szerepkör a mindennapos munkájukhoz, és a PIM használata volna meg, jelentősen csökkentheti a teljesítményükre, lehessen venni azokat a végleges szerepkörhöz.
- A szervezet számára – adott esetben a jogosult szerepkör alatt megadott személy egy nagyon távoli csapat vagy egy rangú ügyvezető, és hogy a pont, amely kommunikál, és a jogosultságszint-emelési folyamatot kényszerítése nehéz, ha lehessen venni azokat a végleges szerepkörhöz.

> [!TIP]
> :heavy_check_mark: **A Microsoft javasolja** állítsa be az ismétlődő hozzáférési felülvizsgálatokat állandó szerepkör-hozzárendelésekkel rendelkező felhasználók számára (kell rendelkezik ilyennel). További információ a központi telepítési csomag utolsó szakaszában az ismétlődő hozzáférési felülvizsgálat

### <a name="draft-your-pim-settings"></a>Vázlatszintű a PIM-beállítások

A PIM-megoldások megvalósításának előtt tanácsos a PIM beállításokat a szervezet használja minden kiemelt szerepkörhöz vázlatra. Ez a szakasz néhány példa a PIM beállításokat adott szerepkörök (csak referenciaként, és lehet, hogy a szervezet különböző) rendelkezik. Egyes beállítások kifejtett a Microsoft-javaslat részletei a táblázatok.

#### <a name="pim-settings-for-azure-ad-roles"></a>Az Azure AD-szerepköröket a PIM beállításai

| Szerepkör | MFA megkövetelése | Értesítés | Incidens jegy | Jóváhagyás szükséges | Jóváhagyó | Aktiválás időtartama | Az állandó rendszergazdai |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Globális rendszergazda | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Más globális rendszergazdákat | 1 óra | A vészelérési fiókok |
| Exchange-rendszergazda | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | None | 2 óra | None |
| Ügyfélszolgálati adminisztrátor | :x: | :x: | :heavy_check_mark: | :x: | None | 8 óra | None |

#### <a name="pim-settings-for-azure-resource-roles"></a>Az Azure-erőforrások szerepköreihez tartozó PIM-beállítások

| Szerepkör | MFA megkövetelése | Értesítés | Jóváhagyás szükséges | Jóváhagyó | Aktiválás időtartama | Aktív felügyeleti | Aktív lejárata | Jogosult lejárata |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Kritikus fontosságú előfizetéseket tulajdonosa | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Az előfizetés további tulajdonosok folyamatokhoz | 1 óra | None | n/a | 3 hónap |
| Felhasználói hozzáférés rendszergazdája kevésbé fontos előfizetések | :heavy_check_mark: | :heavy_check_mark: | :x: | None | 1 óra | None | n/a | 3 hónap |
| Virtuális gépek közreműködője | :x: | :heavy_check_mark: | :x: | None | 3 óra | None | n/a | 6 hónapra |

A következő táblázat ismerteti az egyes beállításoknak.

| Beállítás | Leírás |
| --- | --- |
| Szerepkör | A beállításokat a szerepkör nevét. |
| MFA megkövetelése | Hogy a jogosult felhasználónak kell hajthatok végre MFA a szerepkör aktiválása előtt.<br/><br/> :heavy_check_mark: **A Microsoft javasolja** , MFA kényszerítése minden rendszergazdai szerepkörhöz, különösen akkor, ha a szerepkörök rendelkeznek vendégfelhasználókat. |
| Értesítés | Értéke true, globális rendszergazda, a kiemelt szerepkörű rendszergazda, és a biztonsági rendszergazda a szervezet egy e-mail értesítést kap, amikor egy jogosult felhasználó aktiválja a szerepkörét.<br/><br/>**Megjegyzés:** Egyes szervezetek nem rendelkezik rendszergazdai fiókjait, az alábbi e-mail-értesítések kötött e-mail-címmel, egy másodlagos e-mail-cím beállítása, így a rendszergazdák fog kapni e-mailek kell végezze. |
| Incidens jegy | E a jogosult felhasználónak szüksége van egy incidens jegyszám rögzíti, ha a szerepkör aktiválása. Ez a beállítás segítségével a szervezetek egy belső incidensszám nemkívánatos aktiválások csökkentése érdekében az egyes aktiválási azonosításához.<br/><br/> :heavy_check_mark: **A Microsoft javasolja** kihasználhatja a PIM elősegítsék a belső rendszerbe történő incidens jegy számának. Ez különösen hasznos a jóváhagyókat, akik az aktiválási környezet szükséges. |
| Jóváhagyás szükséges | Hogy a jogosult felhasználónak el kell beolvasni a szerepkör aktiválásához jóváhagyás.<br/><br/> :heavy_check_mark: **A Microsoft javasolja** , hogy a legtöbb engedéllyel rendelkező szerepek jóváhagyás beállítása. Minden PIM-ügyfél használati mintái alapján a globális rendszergazdai, felhasználói rendszergazdája, Exchange-rendszergazdai, biztonsági rendszergazdai és jelszókezelő a leggyakrabban használt szerepkörök a jóváhagyás beállítása. |
| Jóváhagyó | Ha a jóváhagyásra szükség a jogosult szerepkör, akik jóvá kell hagynia a kérelmet listázásához aktiválását. Alapértelmezés szerint a PIM beállítja a jóváhagyó, minden olyan felhasználók, akik a kiemelt szerepkörű rendszergazda, azok állandó és jogosult-e lennie.<br/><br/>**Megjegyzés:** Ha egy felhasználó egyaránt jogosult az Azure AD-szerepkörhöz, és a szerepkör jóváhagyó, nem jóváhagyhatja magukat.<br/><br/> :heavy_check_mark: **A Microsoft javasolja** jóváhagyók azoknak, akik a legtöbb globális rendszergazda helyett az adott szerepkörrel és a gyakori felhasználók ismerő kell választania. |
| Aktiválás időtartama | Mennyi ideig aktiválódik egy felhasználót a szerepkör előtt le fog járni. |
| Az állandó rendszergazdai | Mely lesz a szerepkörhöz tartozó állandó rendszergazda felhasználóknak (soha nem kell aktiválása).<br/><br/> :heavy_check_mark: **A Microsoft javasolja** nulla állandó rendszergazda számára a globális rendszergazdák kivételével az összes szerepkör rendelkezik. Olvassa el több részletes ki kell tenni a jogosult és kik legyenek a terv állandóan aktív szakaszában. |
| Aktív felügyeleti | Az Azure-erőforrásokhoz a felhasználók, akik soha nem a szerepkör használatához aktiválnia kell a lista aktív rendszergazda. Ezt nem nevezzük állandó rendszergazda például az Azure AD-szerepkörök mert beállíthat egy lejárati időt, amikor a felhasználó elveszíti a szerepkör számára. |
| Aktív lejárata | Aktív szerepkör-hozzárendeléssel, az Azure-erőforrások szerepköreihez tartozó ez időszak konfigurálása után lejár. 15 nap, 1 hónap, a 3 hónapos, 6 havi, 1 év közül választhat, vagy állandóan aktív. |
| Jogosult lejárata | Egy jogosult szerepkör-hozzárendelés az Azure-erőforrások szerepköreihez tartozó ez időszak konfigurálása után lejár. 15 nap, 1 hónap, a 3 hónapos, 6 havi, 1 év közül választhat, vagy állandó jogosultság. |

## <a name="step-3-implement-your-solution"></a>3. lépés A megoldás megvalósítása

Alapját a megfelelő tervezést, amelyen üzembe helyezése sikeresen megtörtént az Azure Active Directory-alkalmazás alapja.  Intelligent security és az integráció, amely leegyszerűsíti előkészítés sikeres telepítések az idő csökkentése mellett biztosít.  Ez a kombináció biztosítja, hogy az alkalmazás integrálva van a könnyű ugyanakkor állásidő a végfelhasználók számára.

### <a name="identify-test-users"></a>Test-felhasználóinak azonosítása

Ez a szakasz segítségével azonosítsa azokat a felhasználókat és vagy felhasználói csoportokat a megvalósítás ellenőrzése. A tervezési szakaszban kiválasztott beállítások alapján, azonosítsa a felhasználók, tesztelni szeretné az egyes szerepkörökhöz.

> [!TIP]
> :heavy_check_mark: **A Microsoft javasolja** választja ki tesztfelhasználók lennie ahhoz, hogy megismerkedjen a folyamat, illetve egy a Bevezetés a belső advocator válnak az egyes Azure AD-szerepkörök a szolgáltatás tulajdonosait.

Ebben a táblázatban, amely ellenőrzi, hogy a beállítások az egyes szerepkörökhöz működésének tesztfelhasználók azonosításához.

| Szerepkörnév | Felhasználók tesztelése |
| --- | --- |
| &lt;Szerepkör neve&gt; | &lt;A felhasználók a szerepkör tesztelése&gt; |
| &lt;Szerepkör neve&gt; | &lt;A felhasználók a szerepkör tesztelése&gt; |

### <a name="test-implementation"></a>Teszt végrehajtása

Most, hogy azonosította a tesztfelhasználókat, ebben a lépésben használatával a test-felhasználók számára a privileged Identity Management konfigurálása. Ha a szervezet által támogatni kívánt PIM munkafolyamat beépítheti a PIM a felhasználói felület az Azure Portalon belülről használata helyett a saját belső alkalmazás, az PIM-ben minden művelet is támogatják a [graph API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-pim-for-azure-ad-roles"></a>Az Azure AD-szerepkörökhöz tartozó privileged Identity Management konfigurálása

1. [Az Azure AD directory szerepkör-beállítások konfigurálása](pim-how-to-change-default-settings.md) tervezett alapján.

1. Navigáljon a **Azure AD-szerepkörök**, kattintson a **szerepkörök**, és válassza ki az imént beállított szerepkört.

1. Tesztfelhasználók, csoport ha azok már állandó rendszergazda, akkor is használhatja őket jogosult keresése őket, és konvertálja azokat állandó jogosult a sorban lévő három pontra kattint. Ha a szerepkör-hozzárendelések nem rendelkeznek, még akkor is [győződjön meg arról, egy új jogosult hozzárendelés](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. A vizsgálni kívánt szerepkörök esetében ismételje meg az 1 – 3.

1. Tesztfelhasználók beállítása után el kell küldenie azokat a hivatkozásra, hogy hogyan [aktiválja az Azure AD-szerepkörhöz](pim-how-to-activate-role.md).

#### <a name="configure-pim-for-azure-resource-roles"></a>Az Azure-erőforrások szerepköreihez tartozó privileged Identity Management konfigurálása

1. [Az Azure szerepkör-beállítások konfigurálása](pim-resource-roles-configure-role-settings.md) előfizetés vagy a tesztelni kívánt erőforrás belül egy adott szerepkör esetében.

1. Navigáljon a **Azure-erőforrások** adott előfizetésre, és kattintson **szerepkörök**, válassza ki az imént beállított szerepkör.

1. Tesztfelhasználók, csoport, ha már aktív rendszergazda, akkor is használhatja őket jogosult kereséssel és [azok szerepkör-hozzárendelés frissítése](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Ha a szerepkör nem rendelkeznek, még akkor is [új szerepkör hozzárendelése](pim-resource-roles-assign-roles.md#assign-a-role).

1. A vizsgálni kívánt szerepkörök esetében ismételje meg az 1 – 3.

1. Tesztfelhasználók beállítása után el kell küldenie azokat a hivatkozásra, hogy hogyan [az Azure-erőforrás a szerepkör aktiválása](pim-resource-roles-activate-your-roles.md).

Ebben a szakaszban ellenőrizze, hogy megfelelően működik-e az összes konfigurációs beállítása a a szerepkörök használjon. Az alábbi táblázat segítségével a tesztek dokumentálja. Ebben a szakaszban segítségével optimalizálható a kommunikációt az érintett felhasználók is kell.

| Szerepkör | Az aktiválás során normális működés | A tényleges eredmények |
| --- | --- | --- |
| Globális rendszergazda | (1) a többtényezős hitelesítés megkövetelése<br/>(2) jóváhagyása szükséges<br/>(3) jóváhagyó értesítést kap, és hagyhatja jóvá<br/>(4) szerepkör előre beállított idő után jár le |  |
| Előfizetés tulajdonosának *X* | (1) a többtényezős hitelesítés megkövetelése<br/>(2) a jogosult hozzárendelés beállított idő elteltével lejár |  |

### <a name="communicate-pim-to-affected-stakeholders"></a>A PIM kommunikálni érintett résztvevőket

A PIM telepítése a felhasználók számára a kiemelt szerepkörök további lépéseket vezet be. Bár a PIM nagymértékben csökkenti az emelt szintű identitások társított biztonsági problémák, a módosítás kell hatékonyan továbbítani a bérlői szintű üzembe helyezés előtt. Érintett rendszergazdák számától függően a szervezetek gyakran választ hozzon létre egy belső dokumentumot, videó vagy változásáról e-mailt. Gyakran szerepelnek ezek a kommunikációk tartalmazza:

- Mi az a PIM
- Mi az a szervezet az előny
- Akik érinti
- Ha a PIM bevezetjük
- Milyen további lépések lesznek a felhasználók számára a szerepkör aktiválása szükséges
    - Hivatkozások el kell küldenie a dokumentáció tartalmaz:
    - [Az Azure AD-szerepkörök aktiválása](pim-how-to-activate-role.md)
    - [Aktiválja az Azure-erőforrásszerepkörök](pim-resource-roles-activate-your-roles.md)
- Kapcsolattartási adatok vagy az esetleges problémákat, miközben a PIM társított ügyfélszolgálati hivatkozás

> [!TIP]
> :heavy_check_mark: **A Microsoft javasolja** , hogy állítsa be a segélyszolgálat-támogatás csapattal időt megtudhatja, hogyan azokat a PIM munkafolyamat (Ha a szervezete egy belső IT-támogatási csapat). Adja meg azokat a megfelelő dokumentáció, valamint a kapcsolattartási adatokat.

### <a name="move-to-production"></a>Helyezze át az éles környezetbe

Ha a tesztelés nem sikeres, és a teljes PIM átállni éles üzemre újraindításához ismételje meg a lépéseket a tesztelési fázisban az egyes szerepkörök a PIM konfigurációjában meghatározott összes felhasználója számára. A PIM szolgáltatásra az Azure AD-szerepkörök a szervezetek gyakran tesztelése, és megkezdik a PIM azon globális rendszergazdák teszteléshez és egyéb szerepköröket a PIM bevezetéséről előtt. Mindeközben Azure-beli erőforráshoz, szervezetek általában tesztelése, és megkezdik a PIM egyszerre csak egy Azure-előfizetéssel.

### <a name="in-the-case-a-rollback-is-needed"></a>Abban az esetben egy visszaállítási van szükség

Nem sikerült a PIM igény szerint az éles környezetben működik, ha a visszaállítási lépések végrehajtásában, ha vissza kíván térni egy korábbi hibátlan állapotra a PIM beállítása előtt:

#### <a name="azure-ad-roles"></a>Azure AD-szerepkörök

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Nyissa meg **az Azure AD Privileged Identity Management**.
1. Kattintson a **Azure AD-szerepkörök** majd **szerepkörök**.
1. Az egyes szerepkörökhöz konfigurált, kattintson a három pont (**...** ) egy jogosult hozzárendelés az összes felhasználó számára.
1. Kattintson a **állandóvá** lehetőséget a szerepkör-hozzárendelés véglegesítéséhez.

#### <a name="azure-resource-roles"></a>Azure-erőforrásszerepkörök

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Nyissa meg **az Azure AD Privileged Identity Management**.
1. Kattintson a **Azure-erőforrások** és kattintson egy előfizetést, vagy szeretné visszaállítani kívánt erőforrást.
1. Kattintson a **szerepkörök**.
1. Az egyes szerepkörökhöz konfigurált, kattintson a három pont (**...** ) egy jogosult hozzárendelés az összes felhasználó számára.
1. Kattintson a **állandóvá** lehetőséget a szerepkör-hozzárendelés véglegesítéséhez.

## <a name="step-4-next-steps-after-deploying-pim"></a>4. lépés A PIM telepítése után a következő lépések

Sikeresen üzembe helyezése a PIM éles környezetben egy jelentős lépés a szervezet védelme tekintetében a rendszerjogosultságú identitásokat. A PIM telepítésének származik, amelyet használnia kell a biztonsági és megfelelőségi további PIM-funkciók.

### <a name="use-pim-alerts-to-safeguard-your-privileged-access"></a>Használja a PIM riasztások az emelt szintű hozzáférés védelme

A PIM beépített riasztási funkciók hatékonyabb védelem a bérlő kell használni. További információkért lásd: [biztonsági riasztások](pim-how-to-configure-security-alerts.md#security-alerts). Ezek a riasztások tartalmazzák: a rendszergazdák a kiemelt szerepkörök nem használ, a PIM-en kívül folyamatban van hozzárendelve, a szerepkörök aktiválása túl gyakran vannak folyamatban stb. Teljes körű védelme érdekében a szervezet, érdemes rendszeresen haladjon végig a riasztások listájában, és hárítsa el a problémákat. Tekintheti meg és javítsa ki a riasztások a következő módon:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Nyissa meg **az Azure AD Privileged Identity Management**.
1. Kattintson a **Azure AD-szerepkörök** majd **riasztások**.

> [!TIP]
> :heavy_check_mark: **A Microsoft javasolja** foglalkozik a magas súlyossági szintű azonnal megjelölt összes riasztás. A közepes és alacsony súlyossági szintű riasztások értesüljön és hajtsa végre a módosításokat, ha úgy véli, hogy van egy biztonsági kockázatot.

Ha a meghatározott riasztások valamelyik nem hasznos, vagy nem vonatkozik a szervezet számára, minden esetben elvetheti a riasztást a riasztások lapon. Bármikor visszatérhet a elbocsátása később az Azure ad-ben beállítások lapján található.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>A szervezet emelt szintű identitások rendszeres naplózását ismétlődő a hozzáférési felülvizsgálatok beállítása

A hozzáférési felülvizsgálatok a legjobb módja, hogy a kiemelt szerepkörökhöz, vagy adott felülvizsgálók rendelkező felhasználók kérdéseket, hogy minden felhasználó kell-e a privileged identity. A hozzáférési felülvizsgálatok kiválóak, ha azt szeretné csökkenteni a támadási felületet és a megfelelő marad. Hozzáférési felülvizsgálat indítása kapcsolatos további információkért lásd: [Azure AD-szerepkörök hozzáférési felülvizsgálatokkal](pim-how-to-start-security-review.md) és [Azure-erőforrásszerepkörök hozzáférési felülvizsgálatokkal](pim-resource-roles-start-access-review.md). Egyes szervezetekben a rendszeres hozzáférési felülvizsgálat végrehajtása szükséges törvényeknek és szabályozásoknak közben mások tanúsítvánnyal rendelkező, a hozzáférési felülvizsgálat a legjobb módja az egyszerű, a szervezeten belül a legalacsonyabb jogosultsági kényszerítése.

> [!TIP]
> :heavy_check_mark: **A Microsoft javasolja** beállítása negyedévente a hozzáférési felülvizsgálatok az Azure AD-hez és az Azure-erőforrások szerepköreihez.

A legtöbb esetben az Azure AD-szerepkörök felülvizsgálója ellenőrzi a felhasználók maguk, az Azure-erőforrások szerepköreihez tartozó felülvizsgáló pedig az előfizetést, amely a szerepkör tulajdonosa. Azonban gyakran az esetet, ahol a cégek privilegizált fiókok, amelyek nem kapcsolódnak az adott személy e-mail-címmel. Ezekben az esetekben senki nem olvas, és a hozzáférési felülvizsgálatok.

> [!TIP]
> :heavy_check_mark: **A Microsoft javasolja** egy másodlagos e-mail-címet, a kiemelt szerepkörök hozzárendeléseit, amelyek nem kapcsolódnak egy rendszeresen ellenőrzött e-mail-címmel rendelkező összes fiókok hozzáadása

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>A lehető leghatékonyabban a napló biztonsági és megfelelőségi javítása érdekében

A napló az a hely, ahol naprakész maradhat, és meg kell felelnie az előírásoknak. A PIM belül az auditálási napló többek között a szervezet előzmények 30 nap előzményeit tárolja:

- Jogosult szerepkörök aktiválása/inaktiválása
- Szerepkör-hozzárendelés tevékenységekről belüli és kívüli PIM
- Szerepkör-beállítások változásai
- Jóváhagyás beállítása a szerepkör-aktiválásra vonatkozó kérés/jóváhagyása vagy elutasítása tevékenységek
- Riasztások frissítése

Ezek érheti el naplók, ha Ön globális rendszergazda vagy a kiemelt szerepkörű rendszergazdák. További információkért lásd: [naplózási előzmények az Azure AD-szerepkörökhöz tartozó](pim-how-to-use-audit-log.md) és [naplózási előzmények az Azure-erőforrások szerepköreihez tartozó](azure-pim-resource-rbac.md).

> [!TIP]
> :heavy_check_mark: **A Microsoft javasolja** , hogy legalább egy olyan rendszergazda, olvassa el az összes hetente események naplózása és exportálása a naplózási események havi rendszerességgel történik.

Ha azt szeretné, a naplózási események tárolhat automatikusan egy hosszabb ideig, a PIM-auditnapló automatikusan szinkronizálva van a [az Azure AD auditnaplóinak](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> :heavy_check_mark: **A Microsoft javasolja** beállítását [figyelése az Azure log](../reports-monitoring/concept-activity-logs-azure-monitor.md) archiválására események a biztonsági és megfelelőségi kellene egy Azure storage-fiókját.
