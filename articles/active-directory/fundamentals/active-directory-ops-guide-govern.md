---
title: Az Azure Active Directory cégirányítási műveletek útmutatójának útmutatója
description: Ez a műveleti referencia-útmutató ismerteti azokat az ellenőrzéseket és műveleteket, amelyeket meg kell tennie az irányítás kezelésének biztosítása érdekében
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 4826bcdc85e0c6189c51aa262014fe154bb479b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535456"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Az Azure Active Directory cégirányítási műveletek útmutatójának útmutatója

Az Azure [AD-műveletek referencia-útmutatóezi](active-directory-ops-guide-intro.md) az ellenőrzések és a műveleteket kell tennie, hogy értékelje és tanúsítsa a hozzáférést biztosított nem kiemelt és kiemelt identitások, naplózási és a környezet változásainak tesztelése.

> [!NOTE]
> Ezek az ajánlások a közzététel időpontjától aktuálisak, de idővel változhatnak. A szervezeteknek folyamatosan értékelniük kell cégirányítási gyakorlataikat, ahogy a Microsoft-termékek és -szolgáltatások idővel fejlődnek.

## <a name="key-operational-processes"></a>Kulcsfontosságú működési folyamatok

### <a name="assign-owners-to-key-tasks"></a>Tulajdonosok hozzárendelése a legfontosabb feladatokhoz

Az Azure Active Directory kezeléséhez a legfontosabb operatív feladatok és folyamatok folyamatos végrehajtására van szükség, amelyek nem feltétlenül részei a bevezetési projektnek. Továbbra is fontos, hogy ezeket a feladatokat a környezet optimalizálása érdekében állítsa be. A legfontosabb feladatok és az ajánlott tulajdonosok a következők:

| Tevékenység | Tulajdonos |
| :- | :- |
| Az Azure AD naplózási naplóinak archiválása a SIEM rendszerben | InfoSec műveleti csapat |
| A nem megfelelően kezelt alkalmazások felderítése | IAM műveleti csapat |
| Rendszeresen vizsgálja felül a kérelmekhez való hozzáférést | InfoSec architektúra csapata |
| A külső identitásokhoz való hozzáférés rendszeres felülvizsgálata | InfoSec architektúra csapata |
| Rendszeresen tekintse át, hogy kinek van kiemelt szerepköre | InfoSec architektúra csapata |
| Biztonsági kapuk definiálása a kiemelt szerepkörök aktiválásához | InfoSec architektúra csapata |
| A hozzájárulási támogatások rendszeres felülvizsgálata | InfoSec architektúra csapata |
| Katalógusok és hozzáférési csomagok tervezése alkalmazásokhoz és erőforrásokhoz a szervezet alkalmazottai számára | Alkalmazástulajdonosok |
| Biztonsági házirendek megadása a felhasználók hozzáférési csomagokhoz rendeléséhez | InfoSec csapat + Alkalmazástulajdonosok |
| Ha a házirendek jóváhagyási munkafolyamatokat is tartalmaznak, rendszeresen tekintse át a munkafolyamat-jóváhagyásokat | Alkalmazástulajdonosok |
| Tekintse át a biztonsági házirendek kivételeit, például a feltételes hozzáférési házirendeket a hozzáférés-felülvizsgálatok használatával | InfoSec műveleti csapat |

A lista áttekintésekor előfordulhat, hogy tulajdonost kell hozzárendelnie a tulajdonosból hiányzó feladatokhoz, vagy módosítania kell a fenti javaslatokhoz nem igazodó tulajdonossal rendelkező feladatok tulajdonjogát.

#### <a name="owner-recommended-reading"></a>Tulajdonos ajánlott olvasás

- [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Irányítás az Azure-ban](https://docs.microsoft.com/azure/security/governance-in-azure)

### <a name="configuration-changes-testing"></a>Konfigurációs módosítások tesztelése

Vannak olyan módosítások, amelyek a tesztelés során különleges szempontokat igényelnek, az egyszerű technikáktól kezdve, például a felhasználók célrészhalmazának bevezetésétől a párhuzamos tesztbérlőben történő módosítás üzembe helyezéséig. Ha még nem valósított meg tesztelési stratégiát, az alábbi táblázatban szereplő irányelvek alapján meg kell határoznia egy tesztmegközelítést:

| Forgatókönyv| Ajánlás |
|-|-|
|A hitelesítéstípus módosítása összevontról PHS/PTA-ra vagy fordítva| A [szakaszos bevezetés](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) segítségével tesztelje a hitelesítési típus módosításának hatását.|
|Új feltételes hozzáférési (CA) házirend vagy identitásvédelmi házirend létrehozása|Hozzon létre egy új hitelesítésbiztosítási házirendet, és rendelje hozzá a tesztfelhasználókhoz.|
|Alkalmazás tesztkörnyezetének beépítése|Adja hozzá az alkalmazást egy éles környezethez, rejtse el a MyApps panelről, és rendelje hozzá a felhasználók teszteléséhez a minőségbiztosítási fázisban.|
|Szinkronizálási szabályok módosítása|Végezze el a módosításokat egy tesztben az Azure AD Connect-ben ugyanazzal a konfigurációval, amely jelenleg éles környezetben van, más néven átmeneti módban, és elemezze a CSExport eredményeit. Ha elégedett, cserélje ki a termelésre, ha készen áll.|
|A márkaépítés megváltoztatása|Teszt egy külön tesztbérlőben.|
|Új funkció begurulása|Ha a funkció támogatja a felhasználók célcsoportjának bevezetését, azonosítsa a kísérleti felhasználókat, és építse n ki. Az önkiszolgáló jelszó-visszaállítás és a többtényezős hitelesítés például meghatározott felhasználókat vagy csoportokat célozhat meg.|
|Alkalmazás átépítése egy helyszíni identitásszolgáltatóról (IdP), például az Active Directoryból az Azure AD-be|Ha az alkalmazás támogatja a több IdP-konfigurációk, például a Salesforce, konfigurálja mindkettőt, és tesztelje az Azure AD változási ablakban (abban az esetben, ha az alkalmazás bevezeti a HRD-oldal). Ha az alkalmazás nem támogatja a több idPs, ütemezése a vizsgálat során a változás vezérlő ablak és a program állásidő.|
|Dinamikus csoportszabályok frissítése|Hozzon létre egy párhuzamos dinamikus csoportot az új szabállyal. Hasonlítsa össze a számított eredménnyel, például futtassa a PowerShell azonos feltétellel.<br>Ha a tesztmegfeleltet, cserélje ki azaz helyeket, ahol a régi csoportot használták (ha lehetséges).|
|Terméklicencek áttelepítése|Lásd: [Egyetlen felhasználó licencének módosítása egy licencelt csoportban az Azure Active Directoryban.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-change-licenses)|
|Az AD FS-szabályok , például az engedélyezés, a kiállítás, az MFA módosítása|A csoportjogcím használatával a felhasználók részhalmazát célozhatja meg.|
|Az AD FS hitelesítési élményének vagy hasonló farmszintű módosításoknak a módosítása|Hozzon létre egy párhuzamos farmot azonos állomásnévvel, valósítsa meg a konfigurációs módosításokat, tesztelje az ügyfelektől a HOSTS fájlt, a hálózati terheléselosztási útválasztási szabályokat vagy hasonló útválasztást.<br>Ha a célplatform nem támogatja a HOSTS fájlokat (például a mobileszközöket), szabályozhatja a módosítást.|

## <a name="access-reviews"></a>Hozzáférési felülvizsgálatok

### <a name="access-reviews-to-applications"></a>Hozzáférés az alkalmazásokhoz

Idővel a felhasználók felhalmozódhatnak az erőforrásokhoz való hozzáférés, ahogy mozognak a különböző csapatok és pozíciók között. Fontos, hogy az erőforrás-tulajdonosok rendszeresen tekintse át az alkalmazásokhoz való hozzáférést, és távolítsa el azokat a jogosultságokat, amelyekre már nincs szükség a felhasználók teljes életciklusa során. Az Azure [AD-hozzáférés-felülvizsgálatok](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) lehetővé teszik a szervezetek számára a csoporttagságok, a vállalati alkalmazásokhoz való hozzáférés és a szerepkör-hozzárendelések hatékony kezelését. Az erőforrás-tulajdonosoknak rendszeresen át kell tekinteniük a felhasználók hozzáférését, hogy megbizonyosodjanak arról, hogy csak a megfelelő személyek rendelkeznek folyamatos hozzáféréssel. Ideális esetben érdemes azure AD-hozzáférési felülvizsgálatok ehhez a feladathoz.

![Access-ellenőrzések kezdőlapja](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> Minden olyan felhasználónak, aki a hozzáférési felülvizsgálatokkal kommunikál, fizetős Azure AD Premium P2 licenccel kell rendelkeznie.

### <a name="access-reviews-to-external-identities"></a>Hozzáférés a külső identitásokhoz

Rendkívül fontos, hogy a külső identitásokhoz való hozzáférés csak a szükséges erőforrásokra korlátozva maradjon a szükséges idő alatt. Hozzon létre egy rendszeres automatikus hozzáférés-felülvizsgálati folyamatot az összes külső identitáshoz és alkalmazás-hozzáféréshez az Azure [AD-hozzáférési felülvizsgálatok](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)használatával. Ha egy folyamat már létezik a helyszínen, fontolja meg az Azure AD-hozzáférési felülvizsgálatok használatát. Ha egy alkalmazás kivonva van, vagy már nem használják, távolítsa el az összes külső identitások, amelyek hozzáfértek az alkalmazáshoz.

> [!NOTE]
> Minden olyan felhasználónak, aki a hozzáférési felülvizsgálatokkal kommunikál, fizetős Azure AD Premium P2 licenccel kell rendelkeznie.

## <a name="privileged-account-management"></a>Kiemelt fiókkezelés

### <a name="privileged-account-usage"></a>Kiemelt fiók használata

A hackerek gyakran a rendszergazdai fiókokat és a kiemelt hozzáférés egyéb elemeit célozzák meg, hogy gyorsan hozzáférjenek a bizalmas adatokhoz és rendszerekhez.Mivel a kiemelt szerepkörrel rendelkező felhasználók idővel felhalmozódnak, fontos, hogy rendszeresen tekintse át és kezelje a rendszergazdai hozzáférést, és biztosítson just-in-time kiemelt hozzáférést az Azure AD és az Azure-erőforrásokhoz.

Ha a szervezetben nem létezik olyan folyamat, amely a kiemelt jogosultságú fiókok kezelésére lenne, vagy jelenleg olyan rendszergazdákkal rendelkezik, akik a szokásos felhasználói fiókjukat használják a szolgáltatások és az erőforrások kezelésére, azonnal meg kell kezdenie a külön fiókok használatát, például a szokásos napi fiókok használatát. tevékenységek; a másik a privilegizált hozzáféréshez, és az MFA-val konfigurálva. Még jobb, ha a szervezet rendelkezik egy Azure AD Premium P2-előfizetéssel, majd azonnal üzembe kell helyeznie [az Azure AD kiemelt identitáskezelés](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure#license-requirements) (PIM). Ugyanebben a jogalapon tekintse át ezeket a kiemelt jogosultságú fiókokat is, és adott esetben [rendeljen hozzá kevésbé kiemelt szerepköröket.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

A kiemelt fiókkezelés egy másik szempontja, amelyet végre kell hajtani, az e fiókok [hozzáférési felülvizsgálatának](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) meghatározása, manuálisan vagy [automatizáltan a PIM-en keresztül.](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-perform-security-review)

#### <a name="privileged-account-management-recommended-reading"></a>Kiemelt fiókkezelés ajánlott olvasása

- [Szerepkörök az Azure AD kiemelt identitáskezelésében](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-roles)

### <a name="emergency-access-accounts"></a>Vészelérési fiókok

A szervezeteknek létre kell hozniuk [a vészhelyzeti fiókokat,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) hogy felkészüljenek az Azure AD kezelésére olyan esetekben, mint például a hitelesítési kimaradások, például:

- A hitelesítési infrastruktúrák kimaradási összetevői (AD FS, Helyszíni AD, MFA-szolgáltatás)
- Az adminisztratív személyzet fluktuációja

Annak elkerülése érdekében, hogy véletlenül ki legyen zárva a bérlőből, mert nem tud bejelentkezni vagy aktiválni egy meglévő egyéni felhasználó fiókját rendszergazdaként, hozzon létre két vagy több vészhelyzeti fiókot, és győződjön meg arról, hogy azok a [Microsoft bevált gyakorlatainak](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) és [az üvegtörési eljárásoknak](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency)vannak megvalósítva és összhangban.

### <a name="privileged-access-to-azure-ea-portal"></a>Kiemelt hozzáférés az Azure EA portálhoz

Az [Azure Nagyvállalati Szerződés (Azure EA) portál](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/) lehetővé teszi, hogy Azure-előfizetéseket hozzon létre egy fő nagyvállalati szerződés, amely egy hatékony szerepet a nagyvállalati. Gyakori, hogy a portál létrehozása még az Azure AD létrehozása előtt, ezért az Azure AD-identitások használatával zárolhatja, eltávolíthatja a személyes fiókokat a portálról, győződjön meg arról, hogy megfelelő delegálás van érvényben, és csökkenteni kell a zárolás kockázatát .

Ahhoz, hogy egyértelmű, ha az EA portál engedélyezési szintje jelenleg "vegyes mód", el kell távolítania minden [Microsoft-fiókok](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) az összes kiemelt hozzáférés az EA portálon, és konfigurálja az EA portál csak az Azure AD-fiókok használata. Ha az EA portál delegált szerepkörei nincsenek konfigurálva, meg kell találnia és végre kell hajtania a részlegek és a fiókok delegált szerepköreit is.

#### <a name="privileged-access-recommended-reading"></a>Kiemelt hozzáférés ajánlott olvasása

- [Adminisztrátori szerepkörök engedélyei az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

## <a name="entitlement-management"></a>Jogosultságkezelés

[A jogosultságkezelés (EM)](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) lehetővé teszi az alkalmazástulajdonosok számára, hogy erőforrásokat kötegeljenek, és a szervezet adott személyiségeihez rendeljék őket (belső és külső egyaránt). Az EM lehetővé teszi az önkiszolgáló regisztrációt és a vállalkozások tulajdonosainak történő delegálást, miközben a cégirányítási házirendek biztosítják a hozzáférést, a hozzáférési időtartamokat, és engedélyezik a jóváhagyási munkafolyamatokat. 

> [!NOTE]
> Az Azure AD jogosultságkezelés hez Azure AD Premium P2-licencek szükségesek.

## <a name="summary"></a>Összefoglalás

A biztonságos identitás-szabályozásnak nyolc szempontja van. Ez a lista segít azonosítani azokat a műveleteket, amelyeket meg kell tennie a nem kiemelt és kiemelt identitások, naplózás és a környezet változásainak ellenőrzéséhez biztosított hozzáférés felmérése és tesztelése érdekében.

- Rendeljen tulajdonosokat a legfontosabb feladatokhoz.
- Tesztelési stratégia megvalósítása.
- Az Azure AD Access-értékelések segítségével hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket.
- Hozzon létre egy rendszeres, automatikus hozzáférési felülvizsgálati folyamatot a külső identitások és az alkalmazáshozzáférés minden típusa számára.
- Hozzon létre egy hozzáférési felülvizsgálati folyamatot a rendszergazdai hozzáférés rendszeres áttekintéséhez és kezeléséhez, és csak időben elérhető kiemelt hozzáférést biztosítson az Azure AD és az Azure-erőforrásokhoz.
- Az Azure AD váratlan kimaradások kezelésére való felkészüléshez vészhelyzeti fiókok kiépítése.
- Az Azure EA-portálhoz való hozzáférés zárolása.
- A Jogosultságkezelés megvalósítása az erőforrások gyűjteményéhez való szabályozott hozzáférés biztosítása érdekében.

## <a name="next-steps"></a>További lépések

Ismerkedés az [Azure AD működési ellenőrzésekkel és műveletekkel.](active-directory-ops-guide-ops.md)
