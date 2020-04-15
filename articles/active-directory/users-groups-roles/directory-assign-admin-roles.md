---
title: Rendszergazdai szerepkör-leírások és engedélyek – Azure AD | Microsoft dokumentumok
description: A rendszergazdai szerepkörök felhasználókat vehetnek fel, rendszergazdai szerepköröket rendelhetnek hozzá, alaphelyzetbe állíthatják a felhasználói jelszavakat, kezelhetik a felhasználói licenceket, vagy kezelhetik a tartományokat.
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfe8aa088538663ac3e64f5913ff031e6160b045
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382645"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Adminisztrátori szerepkörök engedélyei az Azure Active Directoryban

Az Azure Active Directory (Azure AD) használatával korlátozott rendszergazdákat jelölhet ki az identitáskezelési feladatok kevésbé kiemelt szerepkörökben történő kezeléséhez. A rendszergazdák olyan célokra rendelhetők hozzá, mint a felhasználók hozzáadása vagy módosítása, felügyeleti szerepkörök hozzárendelése, felhasználói jelszavak alaphelyzetbe állítása, felhasználói licencek kezelése és tartománynevek kezelése. Az alapértelmezett felhasználói engedélyek csak az Azure AD felhasználói beállításaiban módosíthatók.

## <a name="limit-use-of-global-administrator"></a>A globális rendszergazda használatának korlátozása

A globális rendszergazdai szerepkörhöz rendelt felhasználók elolvashatják és módosíthatják az Azure AD-szervezet minden felügyeleti beállítását. Alapértelmezés szerint az a személy, aki feliratkozik egy Azure-előfizetésre, az Azure AD-szervezet globális rendszergazdai szerepkörrel rendelkezik. Csak a globális rendszergazdák és a kiemelt szerepkörrel rendelkező rendszergazdák delegálhatnak rendszergazdai szerepköröket. A vállalkozásra jelentett kockázat csökkentése érdekében azt javasoljuk, hogy ezt a szerepkört rendelje hozzá a szervezet lehető legkevesebb emberéhez.

Ajánlott eljárásként azt javasoljuk, hogy ezt a szerepkört a szervezetötötnél kevesebb emberhez rendelje hozzá. Ha a szervezetben ötnél több rendszergazda van hozzárendelve a globális rendszergazdai szerepkörhöz, az alábbiakban néhány módszerként csökkentheti annak használatát.

### <a name="find-the-role-you-need"></a>A szükséges szerepkör megkeresése

Ha frusztráló, hogy megtalálja a szerepkört, amire szüksége van a számos szerepkör listájából, az Azure AD a szerepkör-kategóriák alapján a szerepkörek részhalmazait jeleníti meg. Tekintse meg az [Azure AD-szerepkörök és rendszergazdák](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) új **típusszűrőjét,** hogy csak a kiválasztott típusú szerepköröket jelenítse meg.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Olyan szerepkör most már létezik, amely nem létezett a globális rendszergazdai szerepkör hozzárendelésekénél

Lehetséges, hogy egy szerepkör vagy szerepkörök kerültek az Azure AD-hez, amelyek részletesebb engedélyeket biztosítanak, amelyek nem voltak lehetőség, ha egyes felhasználók globális rendszergazda emelt. Idővel további szerepköröket vezetünk be, amelyek olyan feladatokat végeznek, amelyeket csak a globális rendszergazdai szerepkör végezhet korábban. Ezeket a következő Elérhető [szerepkörökben láthatja.](#available-roles)

## <a name="assign-or-remove-administrator-roles"></a>Rendszergazdai szerepkörök hozzárendelése vagy eltávolítása

Ha tudni szeretné, hogyan rendelhet felügyeleti szerepköröket egy felhasználóhoz az Azure Active Directoryban, olvassa el [a Rendszergazdai szerepkörök megtekintése és hozzárendelése az Azure Active Directoryban című témakört.](directory-manage-roles-portal.md)

## <a name="available-roles"></a>Elérhető szerepkörök

A következő rendszergazdai szerepkörök érhetők el:

### <a name="application-administrator"></a>[Alkalmazás-rendszergazda](#application-administrator-permissions)

Ebben a szerepkörben lévő felhasználók létrehozhatják és kezelhetik a vállalati alkalmazások, az alkalmazásregisztrációk és az alkalmazásproxy-beállítások minden aspektusát. Vegye figyelembe, hogy az ehhez a szerepkörhöz rendelt felhasználók nem kerülnek tulajdonosként új alkalmazásregisztrációk vagy vállalati alkalmazások létrehozásakor.

Az alkalmazás-rendszergazdák kezelhetik az alkalmazás hitelesítő adatait, amely lehetővé teszi számukra az alkalmazás megszemélyesítését. Így a szerepkörhöz rendelt felhasználók csak azon alkalmazások hitelesítő adatait kezelhetik, amelyek vagy nincsenek hozzárendelve egyetlen Azure AD-szerepkörhöz sem, vagy csak a következő rendszergazdai szerepkörökhöz vannak rendelve:
* alkalmazás-rendszergazda
* Alkalmazásfejlesztő
* Felhőalkalmazás-rendszergazda
* Címtárolvasók

Ha egy alkalmazás olyan más szerepkörhöz van hozzárendelve, amely nem szerepel a fent említett módon, akkor az alkalmazásfelügyelő nem kezelheti az alkalmazás hitelesítő adatait. 
 
Ez a szerepkör is lehetővé teszi, hogy _hozzájáruljon_ a delegált engedélyek és alkalmazás engedélyek, kivéve az engedélyeket a Microsoft Graph API-t.

> [!IMPORTANT]
> Ez a kivétel azt jelenti, hogy továbbra is beleegyezhet _más_ alkalmazások (például harmadik féltől származó alkalmazások vagy regisztrált alkalmazások) engedélyeire, de nem járulhat hozzá magának az Azure AD-nek az engedélyekhez. Ezek az engedélyek továbbra is _kérheti_ az alkalmazás regisztrációrészeként, de _megadása_ (azaz hozzájárul) ezeket az engedélyeket igényel egy Azure AD-rendszergazda. Ez azt jelenti, hogy a rosszindulatú felhasználók nem tudják könnyen megemelni az engedélyeiket, például azáltal, hogy létrehoznak és hozzájárulnak egy olyan alkalmazáshoz, amely a teljes könyvtárba tud írni, és az alkalmazás engedélyein keresztül globális adminisztrátorrá válhatnak.

### <a name="application-developer"></a>[Alkalmazásfejlesztő](#application-developer-permissions)

A szerepkörben lévő felhasználók akkor hozhatnak létre alkalmazásregisztrációkat, ha a "Felhasználók regisztrálhatnak alkalmazásokat" beállítás Nem. Ez a szerepkör is engedélyt ad a hozzájárulásra a saját nevében, ha a "Felhasználók beleegyezhetnek abba, hogy a vállalati adatokhoz a nevükben hozzáférő alkalmazások" beállítás Nem. Az ehhez a szerepkörhöz rendelt felhasználók tulajdonosként kerülnek hozzáadásra az új alkalmazásregisztrációk vagy vállalati alkalmazások létrehozásakor.

### <a name="authentication-administrator"></a>[Hitelesítési rendszergazda](#authentication-administrator-permissions)

A hitelesítési rendszergazdai szerepkör jelenleg nyilvános előzetes verzióban érhető el. Az ezzel a szerepkörrel rendelkező felhasználók beállíthatják vagy alaphelyzetbe állíthatják a nem jelszó hitelesítő adatokat, és frissíthetik az összes felhasználó jelszavait. A hitelesítési rendszergazdák megkövetelhetik a felhasználóktól, hogy újra regisztráljanak a meglévő nem jelszó hitelesítő adatokkal (például MFA vagy FIDO), és vonják vissza **a emlékszik mfa-t az eszközön,** amely a nem rendszergazda vagy csak a következő szerepkörhöz rendelt felhasználók következő bejelentkezésekor kéri az MFA-t:

* Hitelesítési rendszergazda
* Címtárolvasók
* Vendég meghívó
* Üzenetközpont olvasója
* Jelentések olvasó

> [!IMPORTANT]
> Ezzel a szerepkörrel rendelkező felhasználók módosíthatják a hitelesítő adatokat azok számára, akik hozzáférhetnek a bizalmas vagy személyes adatokhoz vagy a kritikus konfigurációhoz az Azure Active Directoryn belül és kívül. A felhasználó hitelesítő adatainak módosítása azt jelentheti, hogy fel lehet vállalni a felhasználó identitását és engedélyeit. Például:
>
>- Alkalmazásregisztráció és vállalati alkalmazástulajdonosok, akik kezelhetik a tulajdonukban lévő alkalmazások hitelesítő adatait. Ezek az alkalmazások előfordulhat, hogy az Azure AD és máshol nem biztosított hitelesítési rendszergazdák kiemelt engedélyekkel rendelkeznek. Ezen az útvonalon keresztül a hitelesítési rendszergazda felveheti az alkalmazás tulajdonosának identitását, majd tovább feltételezheti egy kiemelt jogosultságú alkalmazás identitását az alkalmazás hitelesítő adatainak frissítésével.
>- Az Azure-előfizetés-tulajdonosok, akik hozzáférhetnek a bizalmas vagy személyes adatokhoz vagy a kritikus konfigurációhoz az Azure-ban.
>- Biztonsági csoport és az Office 365-csoport tulajdonosai, akik kezelhetik a csoporttagságot. Ezek a csoportok hozzáférést biztosíthatnak bizalmas vagy személyes adatokhoz vagy kritikus konfigurációhoz az Azure AD-ben és máshol.
>- Az Azure AD-n kívüli egyéb szolgáltatások, például az Exchange Online, az Office Security and Compliance Center és az emberi erőforrás-rendszerek rendszergazdái.
>- Nem adminisztrátorok, például vezetők, jogi tanácsadók és humánerőforrás-alkalmazottak, akik érzékeny vagy személyes adatokhoz férhetnek hozzá.

### <a name="azure-devops-administrator"></a>[Azure DevOps-rendszergazda](#azure-devops-administrator-permissions)

Ezzel a szerepkörrel rendelkező felhasználók kezelhetik az Azure DevOps-szabályzatot, hogy az új Azure DevOps-szervezet létrehozása konfigurálható felhasználók vagy csoportok készletére korlátozza. Ebben a szerepkörben lévő felhasználók kezelhetik ezt a szabályzatot bármely Azure DevOps-szervezeten keresztül, amely támogatja a vállalat Azure AD-szervezet.

Az összes vállalati Azure DevOps-szabályzatot ebben a szerepkörben lévő felhasználók is kezelhetik.

### <a name="azure-information-protection-administrator"></a>[Azure információvédelmi rendszergazda](#azure-information-protection-administrator-permissions)

Ezzel a szerepkörrel rendelkező felhasználók rendelkeznek az Azure Information Protection szolgáltatás összes engedélyével. Ez a szerepkör lehetővé teszi az Azure Information Protection szabályzat címkéinek konfigurálását, a védelmi sablonok kezelését és a védelem aktiválását. Ez a szerepkör nem ad engedélyt az Identitásvédelmi központban, a Kiemelt identitáskezelésben, az Office 365 szolgáltatásállapotának figyelésében vagy az Office 365 Biztonsági & megfelelőségi központban.

### <a name="b2c-user-flow-administrator"></a>[B2C felhasználói folyamat rendszergazdája](#b2c-user-flow-administrator-permissions)

Ezzel a szerepkörrel rendelkező felhasználók létrehozhatnak és kezelhetnek B2C felhasználói folyamatokat (más néven "beépített" szabályzatokat) az Azure Portalon.Felhasználói folyamatok létrehozásával vagy szerkesztésével ezek a felhasználók módosíthatják a felhasználói élmény html/CSS/javascript tartalmát, módosíthatják az MFA-követelményeket egy felhasználói folyamatonként, módosíthatják a jogcímeket a jogkivonatban, és módosíthatják a munkamenet-beállításokat a bérlő összes házirendjéhez. Másrészt ez a szerepkör nem tartalmazza a felhasználói adatok áttekintésének lehetőségét, illetve a bérlői sémában szereplő attribútumok módosítását.Az identitáskezelési keretrendszer (más néven egyéni) házirendek módosításai szintén kívül esnek a szerepkör hatókörén.

### <a name="b2c-user-flow-attribute-administrator"></a>[B2C felhasználói folyamat attribútumának rendszergazdája](#b2c-user-flow-attribute-administrator-permissions)

Ezzel a szerepkörrel rendelkező felhasználók egyéni attribútumokat adnak hozzá vagy törölnek, amelyek a bérlő összes felhasználói folyamata számára elérhetők.Mint ilyen, az ezzel a szerepkörrel rendelkező felhasználók módosíthatják vagy új elemeket adhatnak a végfelhasználói sémához, és befolyásolhatják az összes felhasználói folyamat viselkedését, és közvetve változásokat eredményezhetnek a végfelhasználóktól felkérhető és végül az alkalmazásoknak jogcímként elküldött adatokban.Ez a szerepkör nem szerkesztheti a felhasználói folyamatokat.

### <a name="b2c-ief-keyset-administrator"></a>[B2C IEF kulcskészlet-rendszergazda](#b2c-ief-keyset-administrator-permissions)

A felhasználó létrehozhat és kezelhet házirendkulcsokat és titkos kulcsokat a tokentitkosításhoz, a jogkivonat-aláírásokhoz és a jogcímtitkosításhoz/-visszafejtéshez.Új kulcsok hozzáadásával a meglévő kulcstárolók, ez a korlátozott rendszergazda átgörgetési titkos kulcsok szükség szerint anélkül, hogy befolyásolna a meglévő alkalmazások.Ez a felhasználó láthatja a teljes tartalmát ezek a titkok és a lejárati dátumok után is létrehozása.

> [!IMPORTANT]
> Ez egy kényes szerep.A kulcskészlet-rendszergazdai szerepkört gondosan naplózni kell, és körültekintően kell hozzárendelni az elő- és gyártás előtti időszakban.

### <a name="b2c-ief-policy-administrator"></a>[B2C IEF házirend-rendszergazda](#b2c-ief-policy-administrator-permissions)

Ebben a szerepkörben lévő felhasználók hozhatnak létre, olvashatnak, frissíthetik és törölhetik az Azure AD B2C összes egyéni szabályzatát, és ezért teljes hozzáféréssel rendelkeznek a megfelelő Azure AD B2C-bérlő identitáskezelési keretrendszere felett. A házirendek szerkesztésével a felhasználó közvetlen összevonást hozhat létre külső identitásszolgáltatókkal, módosíthatja a címtársémát, módosíthatja az összes felhasználó felé néző tartalmat (HTML, CSS, JavaScript), módosíthatja a hitelesítés befejezéséhez szükséges követelményeket, új felhasználókat hozhat létre, felhasználói adatokat küldhet külső rendszereknek, beleértve a teljes áttelepítést is, és szerkesztheti az összes felhasználói információt, beleértve a bizalmas mezőket, például a jelszavakat és a telefonszámokat. Ezzel szemben ez a szerepkör nem módosíthatja a titkosítási kulcsokat, és nem szerkesztheti a bérlőben az összevonáshoz használt titkos kulcsokat.

> [!IMPORTANT]
> A B2 IEF-házirend-rendszergazda egy rendkívül bizalmas szerepkör, amelyet nagyon korlátozott mértékben kell hozzárendelni az éles környezetben lévő bérlők számára.A felhasználók tevékenységeit szorosan naplózni kell, különösen az éles környezetben lévő bérlők számára.

### <a name="billing-administrator"></a>[Számlázási rendszergazda](#billing-administrator-permissions)

Lebonyolítja a vásárlásokat, kezeli az előfizetéseket és a támogatási jegyeket, valamint figyeli a szolgáltatás állapotát.

### <a name="cloud-application-administrator"></a>[Felhőalkalmazás-rendszergazda](#cloud-application-administrator-permissions)

A szerepkörben lévő felhasználók ugyanolyan engedélyekkel rendelkeznek, mint az Alkalmazás-rendszergazda szerepkör, kivéve az alkalmazásproxy kezelését. Ez a szerepkör lehetővé teszi a vállalati alkalmazások és a alkalmazásregisztrációk minden aspektusának létrehozását és kezelését. Ez a szerepkör is lehetővé teszi, hogy hozzájáruljon a delegált engedélyek, és alkalmazás engedélyeket, kivéve a Microsoft Graph API-t. Az ehhez a szerepkörhöz rendelt felhasználók nem kerülnek tulajdonosként új alkalmazásregisztrációk vagy vállalati alkalmazások létrehozásakor.

A felhőalkalmazás-rendszergazdák kezelhetik az alkalmazás hitelesítő adatait, amelyek lehetővé teszik számukra az alkalmazás megszemélyesítését. Így a szerepkörhöz rendelt felhasználók csak azon alkalmazások hitelesítő adatait kezelhetik, amelyek vagy nincsenek hozzárendelve egyetlen Azure AD-szerepkörhöz sem, vagy csak a következő rendszergazdai szerepkörökhöz vannak rendelve:
* Alkalmazásfejlesztő
* Felhőalkalmazás-rendszergazda
* Címtárolvasók

Ha egy alkalmazás olyan más szerepkörhöz van hozzárendelve, amely nem szerepel a fent említett módon, akkor a Felhőalkalmazás-rendszergazda nem tudja kezelni az alkalmazás hitelesítő adatait.

### <a name="cloud-device-administrator"></a>[Felhőeszköz-rendszergazda](#cloud-device-administrator-permissions)

Ebben a szerepkörben lévő felhasználók engedélyezhetik, letilthatják és törölhetik az eszközöket az Azure AD-ben, és olvashatják a Windows 10 BitLocker kulcsokat (ha vannak ilyenek) az Azure Portalon. A szerepkör nem ad engedélyt az eszköz más tulajdonságainak kezelésére.

### <a name="compliance-administrator"></a>[Megfelelőségi rendszergazda](#compliance-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók rendelkeznek a Megfelelőségi szolgáltatások kezeléséhez a Microsoft 365 megfelelőségi központban, a Microsoft 365 Felügyeleti központban, az Azure-ban és az Office 365 Biztonsági &-megfelelőségi központban. A hozzárendeltek az Exchange Felügyeleti központ és a Teams & Skype Vállalati verzió felügyeleti központjainak összes szolgáltatását is kezelhetik, és támogatási jegyeket hozhatnak létre az Azure és a Microsoft 365 számára. További információ az [Office 365 rendszergazdai szerepkörei című oldalon](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)található.

A | Meg tudja csinálni
----- | ----------
[A Microsoft 365 megfelelőségi központja](https://protection.office.com) | A szervezet adatainak védelme és kezelése a Microsoft 365 szolgáltatásaiban<br>Megfelelőségi riasztások kezelése
[Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | A szervezet jogszabályi megfelelőségi tevékenységeinek nyomon követése, hozzárendelése és ellenőrzése
[Az Office 365 Biztonsági & megfelelőségi központja](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Adatirányítás kezelése<br>Jogi és adatvizsgálat végrehajtása<br>Adatalany-kérelem kezelése<br><br>Ez a szerepkör az Office 365 Biztonsági & Megfelelőségi központ szerepköralapú hozzáférés-vezérlésének [megfelelőségi rendszergazdai szerepkörcsoportjával](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) megegyező engedélyekkel rendelkezik.
[megkezdése](https://docs.microsoft.com/intune/role-based-access-control) | Az Intune összes naplózási adatának megtekintése
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Írásvédett engedélyekkel rendelkezik, és képes a riasztások kezelésére<br>Fájlházirendek létrehozása és módosítása, valamint fájlirányítási műveletek engedélyezése<br>Megtekintheti az összes beépített jelentést az Adatkezelés csoportban

### <a name="compliance-data-administrator"></a>[Megfelelőségi adatok rendszergazdája](#compliance-data-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók nak van engedélye az adatok nyomon követésére a Microsoft 365 megfelelőségi központban, a Microsoft 365 Felügyeleti központban és az Azure-ban. A felhasználók nyomon követhetik a megfelelőségi adatokat az Exchange Felügyeleti központban, a Megfelelőségi kezelőben és a Teamsben & Skype Vállalati verzió felügyeleti központjában, és támogatási jegyeket hozhatnak létre az Azure és a Microsoft 365 számára.

A | Meg tudja csinálni
----- | ----------
[A Microsoft 365 megfelelőségi központja](https://protection.office.com) | A megfelelőségi szabályzatok figyelése a Microsoft 365 szolgáltatásaiban<br>Megfelelőségi riasztások kezelése
[Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | A szervezet jogszabályi megfelelőségi tevékenységeinek nyomon követése, hozzárendelése és ellenőrzése
[Az Office 365 Biztonsági & megfelelőségi központja](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Adatirányítás kezelése<br>Jogi és adatvizsgálat végrehajtása<br>Adatalany-kérelem kezelése<br><br>Ez a szerepkör az Office 365 Biztonsági & Megfelelőségi központ szerepköralapú hozzáférés-vezérlésének [megfelelőségi adatok at felügyelő szerepkör-vezérlésével](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) megegyező engedélyekkel rendelkezik.
[megkezdése](https://docs.microsoft.com/intune/role-based-access-control) | Az Intune összes naplózási adatának megtekintése
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Írásvédett engedélyekkel rendelkezik, és képes a riasztások kezelésére<br>Fájlházirendek létrehozása és módosítása, valamint fájlirányítási műveletek engedélyezése<br>Megtekintheti az összes beépített jelentést az Adatkezelés csoportban

### <a name="conditional-access-administrator"></a>[Feltételes hozzáférés-rendszergazda](#conditional-access-administrator-permissions)

Ezzel a szerepkörrel rendelkező felhasználók kezelhetik az Azure Active Directory feltételes hozzáférés beállításait.
> [!NOTE]
> Az Exchange ActiveSync feltételes hozzáférési szabályzatának az Azure-ban történő központi telepítéséhez a felhasználónak globális rendszergazdának is kell lennie.

### <a name="customer-lockbox-access-approver"></a>[Ügyfélszéf-hozzáférés jóváhagyója](#customer-lockbox-access-approver-permissions)

Kezeli [az ügyfélszéf-kérelmeket](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) a szervezetben. E-mailes értesítéseket kapnak az Ügyfélszéf-kérelmekről, és jóváhagyhatják és elutasíthatják a Microsoft 365 felügyeleti központ kéréseit. A Customer Lockbox funkciót is be- és kikapcsolhatják. Csak a globális rendszergazdák állíthatják alaphelyzetbe a szerepkörhöz rendelt személyek jelszavait.

### <a name="desktop-analytics-administrator"></a>[Asztali elemzési rendszergazda](#desktop-analytics-administrator-permissions)


Az ebben a szerepkörben lévő felhasználók kezelhetik az Asztali elemzés és az Office testreszabása & házirend-szolgáltatásokat. A Desktop Analytics esetében ez magában foglalja az eszközleltár megtekintését, a telepítési tervek létrehozását, a központi telepítés és az állapot megtekintését. Az Office testreszabása & házirend szolgáltatás esetén ez a szerepkör lehetővé teszi a felhasználók számára az Office-házirendek kezelését.

### <a name="device-administrator"></a>[Eszközrendszergazda](#device-administrators-permissions)

Ez a szerepkör csak az [Eszközbeállítások](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)ban érhető el hozzárendeléshez, mint egy további helyi rendszergazda. Ezzel a szerepkörrel rendelkező felhasználók helyi számítógép-rendszergazdákká válnak az Azure Active Directoryhoz csatlakozó összes Windows 10-eszközön. Nem képesek az azure-beli Active Directoryban eszközobjektumok kezelésére.

### <a name="directory-readers"></a>[Címtárolvasók](#directory-readers-permissions)

A szerepkörben lévő felhasználók alapvető címtáradatokat olvashatnak. Ezt a szerepkört a következőkre kell használni:
* Adott vendégfelhasználók olvasási hozzáférésének megadása az összes vendégfelhasználó számára való megadása helyett.
* Adott rendszergazdai felhasználók hozzáférésének megadása az Azure Portalhoz, ha "Az Azure AD-portálhoz való hozzáférés korlátozása csak rendszergazdáknak" beállítás "Igen" lesz.
* Szolgáltatásnévi szolgáltatások hozzáférésének megadása a Directory.Read.All könyvtárat tartalmazó könyvtárhoz.

### <a name="directory-synchronization-accounts"></a>[Címtár-szinkronizálási fiókok](#directory-synchronization-accounts-permissions)

Ne használja. Ez a szerepkör automatikusan hozzá van rendelve az Azure AD Connect szolgáltatáshoz, és nem célja vagy támogatása semmilyen más használatra.

### <a name="directory-writers"></a>[Könyvtár írók](#directory-writers-permissions)

Ez egy örökölt szerepkör, amelyet olyan alkalmazásokhoz kell hozzárendelni, amelyek nem támogatják a [hozzájárulási keretrendszert.](../develop/quickstart-register-app.md) Nem rendelhető hozzá egyetlen felhasználóhoz sem.

### <a name="dynamics-365-administrator--crm-administrator"></a>[Dynamics 365 rendszergazda / CRM-rendszergazda](#crm-service-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók globális engedélyekkel rendelkeznek a Microsoft Dynamics 365 Online rendszerben, ha a szolgáltatás jelen van, valamint a támogatási jegyek kezelésével és a szolgáltatás állapotának figyelésével. További [információ: A szolgáltatásfelügyeleti szerepkör használata a bérlő kezeléséhez.](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)

> [!NOTE]
> A Microsoft Graph API-ban és az Azure AD PowerShellben ez a szerepkör "Dynamics 365 service administrator" néven van azonosítva. Ez a "Dynamics 365 administrator" az [Azure portalon.](https://portal.azure.com)

### <a name="exchange-administrator"></a>[Exchange-rendszergazda](#exchange-service-administrator-permissions)

Az ilyen szerepkörrel rendelkező felhasználók globális engedélyekkel rendelkeznek a Microsoft Exchange Online-on belül, ha a szolgáltatás jelen van. Emellett létrehozhatja és kezelheti az összes Office 365-csoportot, kezelheti a támogatási jegyeket, és figyelheti a szolgáltatások állapotát. További információ Az [Office 365 rendszergazdai szerepkörei című.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

> [!NOTE]
> A Microsoft Graph API-ban és az Azure AD PowerShellben ez a szerepkör "Exchange Service Administrator" néven van azonosítva. Az [Azure Portalon](https://portal.azure.com)"Exchange-rendszergazda". Az Exchange Online rendszergazdája az [Exchange Felügyeleti központban.](https://go.microsoft.com/fwlink/p/?LinkID=529144)

### <a name="external-identity-provider-administrator"></a>[Külső identitásszolgáltató rendszergazdája](#external-identity-provider-administrator-permissions)

Ez a rendszergazda kezeli az Azure Active Directory-bérlők és a külső identitásszolgáltatók összevonását.Ezzel a szerepkörrel a felhasználók új identitásszolgáltatókat adhatnak hozzá, és konfigurálhatják az összes elérhető beállítást (pl. hitelesítési útvonal, szolgáltatásazonosító, hozzárendelt kulcstárolók).Ez a felhasználó engedélyezheti, hogy a bérlő megbízzon a külső identitásszolgáltatók hitelesítéseiben.Az eredményül kapott hatás a végfelhasználói élménytől a bérlő típusától függ:

* Az Azure Active Directory-bérlők az alkalmazottak és a partnerek: Az összevonás hozzáadása (pl. a Gmail) azonnal hatással lesz az összes vendég meghívók még nem váltották be. Lásd: [A Google hozzáadása identitásszolgáltatóként a B2B vendégfelhasználók számára.](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)
* Az Azure Active Directory B2C-bérlők: Egy összevonás hozzáadása (például a Facebook, vagy egy másik Azure AD-szervezet) nem befolyásolja azonnal a végfelhasználói folyamatok, amíg az identitásszolgáltató hozzáadása egy felhasználói folyamat (más néven egy beépített szabályzat). Például [A Microsoft-fiók konfigurálása identitásszolgáltatóként](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) című témakörben.A felhasználói folyamatok módosításához a "B2C felhasználói folyamat rendszergazdájának" korlátozott szerepköre szükséges.

### <a name="global-administrator--company-administrator"></a>[Globális rendszergazda / vállalati rendszergazda](#company-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók hozzáférhetnek az Azure Active Directory összes felügyeleti szolgáltatásához, valamint az Azure Active Directory-identitásokat használó szolgáltatásokhoz, például a Microsoft 365 biztonsági központhoz, a Microsoft 365 megfelelőségi központhoz, az Exchange Online-hoz, a SharePoint Online-hoz és a Skype Vállalati online verzióhoz. Az a személy, aki feliratkozik az Azure Active Directory-bérlő lesz a globális rendszergazda. A vállalatnál több globális rendszergazda is lehet. A globális rendszergazdák bármely felhasználó és az összes többi rendszergazda jelszavát is alaphelyzetbe állíthatják.

> [!NOTE]
> A Microsoft Graph API-ban és az Azure AD PowerShellben ez a szerepkör "vállalati rendszergazdaként" van azonosítva. Az [Azure Portalon](https://portal.azure.com)"globális rendszergazda".
>
>

### <a name="global-reader"></a>[Globális olvasó](#global-reader-permissions)

A szerepkörben lévő felhasználók olvashatják a beállításokat és a felügyeleti információkat a Microsoft 365-szolgáltatásokban, de nem tudnak felügyeleti műveleteket eljárni. A globális olvasó a globális adminisztrátor írásvédett megfelelője. Globális olvasó hozzárendelése globális rendszergazda helyett tervezéshez, auditokhoz vagy vizsgálatokhoz. A Globális olvasó és más korlátozott rendszergazdai szerepkörök, például az Exchange-rendszergazda együttes használatával megkönnyítse a munka elvégzését a Globális rendszergazda szerepkör hozzárendelése nélkül. A Globális olvasó együttműködik a Microsoft 365 Felügyeleti központtal, az Exchange Felügyeleti központtal, a SharePoint Felügyeleti központtal, a Teams felügyeleti központtal, a Biztonsági központtal, a Megfelelőségi központtal, az Azure AD felügyeleti központtal és az Eszközkezelési felügyeleti központtal.

> [!NOTE]
> Globális olvasó szerepe van néhány korlátozás most -
>
>- [OneDrive Felügyeleti központ](https://admin.onedrive.com/) – A OneDrive Felügyeleti központ nem támogatja a Globális olvasó szerepkört.
>- [Azure AD portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) – A globális olvasó nem tudja olvasni a vállalati alkalmazások kiépítési módját.
>- [M365 felügyeleti központ](https://admin.microsoft.com/Adminportal/Home#/homepage) – A globális olvasó nem tudja olvasni az ügyfélszéf-kérelmeket. Az M365 Felügyeleti központ bal oldali ablaktáblájában a **Támogatás** csoportban nem találja az **Ügyfél széfkérelmek** lapot.
>- [M365 Biztonsági központ](https://security.microsoft.com/homepage) – A globális olvasó nem tudja leolvasni az érzékenységi és megőrzési címkéket. Az M365 biztonsági központ bal oldali ablaktáblájában nem találhatók **érzékenységi címkék,** **megőrzési címkék**és **címkeelemzési** lapok.
>- [Office Biztonsági & Compliance Center](https://sip.protection.office.com/homepage) – A globális olvasó nem tudja olvasni az SCC naplóit, nem végezhet tartalomkeresést, és nem tudja megnézni a Biztonságos pontszám lehetőséget.
>- [Teams felügyeleti központ](https://admin.teams.microsoft.com) – A globális olvasó nem tudja olvasni **a Teams életciklusát**, **az Analytics & a jelentéseket,** **az IP-telefoneszköz-kezelést** és **az alkalmazáskatalógust.**
>- [A kiemelt hozzáférés-kezelés (PAM)](https://docs.microsoft.com/office365/securitycompliance/privileged-access-management-overview) nem támogatja a globális olvasó szerepkört.
>- [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) – A globális olvasó csak [a központi jelentéskészítéshez](https://docs.microsoft.com/azure/information-protection/reports-aip) támogatott, és ha az Azure AD-szervezet nem szerepel az [egyesített címkézési platformon.](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)
>
> Ezek a funkciók jelenleg fejlesztés alatt állnak.
>

### <a name="groups-administrator"></a>[Csoportok rendszergazdája](#groups-administrator-permissions)

Ebben a szerepkörben lévő felhasználók csoportokat hozhatnak létre/kezelhetnek, például elnevezési és elévülési házirendeket. Fontos megérteni, hogy egy felhasználó hozzárendelése ehhez a szerepkörhöz lehetővé teszi számukra, hogy a bérlő összes csoportját különböző munkaterheléseken , például a Teamsben, a SharePointban, a Yammerben az Outlook mellett kezeljék. A felhasználó a különböző felügyeleti portálokon, például a Microsoft Felügyeleti központban, az Azure Portalon, valamint a számítási feladatokra vonatkozóan, például a Teamsben és a SharePoint Felügyeleti központokban is kezelheti a különböző csoportok beállításait.

### <a name="guest-inviter"></a>[Vendég meghívó](#guest-inviter-permissions)

Ebben a szerepkörben lévő felhasználók kezelhetik az Azure Active Directory B2B vendégfelhasználói meghívásait, ha a **tagok meghívhatják** a felhasználói beállítást, és nem. További információ a B2B-együttműködésről az [Azure AD B2B együttműködésről](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)című oldalon. Nem tartalmaz semmilyen más engedélyeket.

### <a name="helpdesk-administrator"></a>[Ügyfélszolgálati rendszergazda](#helpdesk-administrator-permissions)

Ezzel a szerepkörrel rendelkező felhasználók módosíthatják a jelszavakat, érvényteleníthetik a frissítési jogkivonatokat, kezelhetik a szolgáltatáskérelmeket, és figyelhetik a szolgáltatás állapotát. A frissítési jogkivonat érvénytelenítése esetén a felhasználó újra bejelentkezik. Az ügyfélszolgálati rendszergazdák alaphelyzetbe állíthatják a jelszavakat, és érvényteleníthetik a nem rendszergazda ként vagy csak a következő szerepkörökkel rendelkező felhasználók frissítési jogkivonatait:

* Címtárolvasók
* Vendég meghívó
* Ügyfélszolgálati rendszergazda
* Üzenetközpont olvasója
* Jelentések olvasó

> [!IMPORTANT]
> Ezzel a szerepkörrel rendelkező felhasználók módosíthatják a jelszavakat azok számára, akik hozzáférhetnek a bizalmas vagy személyes adatokhoz vagy a kritikus konfigurációhoz az Azure Active Directoryn belül és kívül. A felhasználó jelszavának módosítása azt jelentheti, hogy fel lehet vállalni a felhasználó identitását és engedélyeit. Például:
>
>- Alkalmazásregisztráció és vállalati alkalmazástulajdonosok, akik kezelhetik a tulajdonukban lévő alkalmazások hitelesítő adatait. Ezek az alkalmazások előfordulhat, hogy az Azure AD és máshol nem biztosított ügyfélszolgálati rendszergazdák kiemelt engedélyekkel rendelkeznek. Ezen az útvonalon keresztül a helpdesk-rendszergazda felveheti az alkalmazás tulajdonosának identitását, majd tovább feltételezheti egy kiemelt jogosultságú alkalmazás identitását az alkalmazás hitelesítő adatainak frissítésével.
>- Az Azure-előfizetés-tulajdonosok, akik hozzáférhetnek a bizalmas vagy személyes adatokhoz vagy a kritikus konfigurációhoz az Azure-ban.
>- Biztonsági csoport és az Office 365-csoport tulajdonosai, akik kezelhetik a csoporttagságot. Ezek a csoportok hozzáférést biztosíthatnak bizalmas vagy személyes adatokhoz vagy kritikus konfigurációhoz az Azure AD-ben és máshol.
>- Az Azure AD-n kívüli egyéb szolgáltatások, például az Exchange Online, az Office Security and Compliance Center és az emberi erőforrás-rendszerek rendszergazdái.
>- Nem adminisztrátorok, például vezetők, jogi tanácsadók és humánerőforrás-alkalmazottak, akik érzékeny vagy személyes adatokhoz férhetnek hozzá.

A felügyeleti engedélyek delegálása a felhasználók részhalmazai felett, valamint házirendek alkalmazása a felhasználók egy részhalmazára a felügyeleti egységek segítségével [lehetséges (jelenleg nyilvános előzetes verzióban).](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units)

Ezt a szerepkört korábban "Jelszó-rendszergazda" néven hívták az [Azure Portalon.](https://portal.azure.com/) Az Azure AD "Helpdesk Administrator" neve most antól megegyezik az Azure AD PowerShellben és a Microsoft Graph API-ban lévő nevével.

### <a name="intune-administrator"></a>[Intune-rendszergazda](#intune-service-administrator-permissions)

Az ilyen szerepkörrel rendelkező felhasználók globális engedélyekkel rendelkeznek a Microsoft Intune Online-on belül, ha a szolgáltatás jelen van. Ezenkívül ez a szerepkör lehetővé teszi a felhasználók és eszközök kezelését a házirendek társítása, valamint a csoportok létrehozása és kezelése érdekében. További információ a [Microsoft Intune szerepköralapú felügyeleti vezérlőjével (RBAC).](https://docs.microsoft.com/intune/role-based-access-control)

Ez a szerepkör az összes biztonsági csoportot létrehozhatja és kezelheti. Az Intune-rendszergazda azonban nem rendelkezik rendszergazdai jogosultságokkal az Office-csoportok felett. Ez azt jelenti, hogy a rendszergazda nem frissítheti a bérlő összes Office-csoportjának tulajdonosait vagy tagságait. Az általa létrehozott Office-csoportot azonban kezelheti, amely a végfelhasználói jogosultságok részeként érkezik. Tehát minden általa létrehozott Office-csoportot (nem biztonsági csoportot) be kell számítani a 250-es kvótájába.

> [!NOTE]
> A Microsoft Graph API-ban és az Azure AD PowerShellben ez a szerepkör "Intune Service Administrator" néven van azonosítva. Az [Azure Portalon](https://portal.azure.com)"Intune-rendszergazda".

### <a name="kaizala-administrator"></a>[Kaizala adminisztrátor](#kaizala-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók globális jogosultsággal rendelkeznek a Microsoft Kaizala beállításainak kezeléséhez, ha a szolgáltatás jelen van, valamint a támogatási jegyek kezeléséhez és a szolgáltatások állapotának figyeléséhez. Emellett a felhasználó hozzáférhet a Kaizala szervezet tagok általi elfogadásával & használatával kapcsolatos jelentésekhez, valamint a Kaizala-műveletek használatával létrehozott üzleti jelentésekhez.

### <a name="license-administrator"></a>[Licencrendszergazda](#license-administrator-permissions)

A szerepkörben lévő felhasználók hozzáadhatják, eltávolíthatják és frissíthetik a felhasználók, csoportok licenchozzárendeléseit (csoportalapú licencelés használatával), és kezelhetik a felhasználók használati helyét. A szerepkör nem teszi lehetővé előfizetések megvásárlását vagy kezelését, csoportok létrehozását és kezelését, illetve felhasználók létrehozását vagy kezelését a használati helyen túl. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

### <a name="message-center-privacy-reader"></a>[Üzenetközpont adatvédelmi olvasója](#message-center-privacy-reader-permissions)

Az ebben a szerepkörben lévő felhasználók figyelhetik az üzenetközpontban lévő összes értesítést, beleértve az adatvédelmi üzeneteket is. Az Üzenetközpont adatvédelmi olvasói e-mailben értesítést kapnak, beleértve az adatvédelmi adatokkal kapcsolatosakat is, és leiratkozhatnak az Üzenetközpont beállításaival. Adatvédelmi üzeneteket csak a globális rendszergazda és az Üzenetközpont adatvédelmi olvasója olvashat. Emellett ez a szerepkör csoportok, tartományok és előfizetések megtekintésének lehetővé teszi. Ez a szerepkör nem rendelkezik jogosultsági engedéllyel a szolgáltatáskérések megtekintéséhez, létrehozásához és kezeléséhez.

### <a name="message-center-reader"></a>[Üzenetközpont olvasója](#message-center-reader-permissions)

Az ebben a szerepkörben lévő felhasználók figyelhetik az értesítéseket és a tanácsadó állapotfrissítéseket az [Office 365 Üzenetközpontban](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) a szervezetük számára a konfigurált szolgáltatásokon, például az Exchange, az Intune és a Microsoft Teams szolgáltatásban. Az Üzenetközpont olvasói heti e-mailben kivonatolják a bejegyzéseket, frissítéseket, és megoszthatják az üzenetközpont-bejegyzéseket az Office 365-ben. Az Azure AD-ben a szerepkörhöz rendelt felhasználók csak az Azure AD-szolgáltatások, például a felhasználók és a csoportok csak olvasási hozzáféréssel rendelkeznek. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

### <a name="office-apps-administrator"></a>[Office-alkalmazások rendszergazdája](#office-apps-administrator-permissions)

A szerepkörben lévő felhasználók kezelhetik az Office 365-alkalmazások felhőbeállításait. Ez magában foglalja a felhőszabályzatok kezelését, az önkiszolgáló letöltéskezelést és az Office-alkalmazásokhoz kapcsolódó jelentések megtekintését. Ez a szerepkör emellett lehetővé teszi a támogatási jegyek kezelését és a szolgáltatás állapotának figyelése a fő felügyeleti központban. Az ehhez a szerepkörhöz hozzárendelt felhasználók az Office-alkalmazások új funkcióinak kommunikációját is kezelhetik. 

### <a name="partner-tier1-support"></a>[Partner tier1 támogatás](#partner-tier1-support-permissions)

Ne használja. Ez a szerepkör elavult, és a jövőben el lesz távolítva az Azure AD-ből. Ezt a szerepkört a Microsoft viszonteladási partnereinek kis száma isznak, és nem általános használatra szánták.

### <a name="partner-tier2-support"></a>[Partner tier2 támogatás](#partner-tier2-support-permissions)

Ne használja. Ez a szerepkör elavult, és a jövőben el lesz távolítva az Azure AD-ből. Ezt a szerepkört a Microsoft viszonteladási partnereinek kis száma isznak, és nem általános használatra szánták.

### <a name="password-administrator"></a>[Jelszó-rendszergazda](#password-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók csak korlátozottmértékben kezelhetik a jelszavakat. Ez a szerepkör nem teszi lehetővé a szolgáltatáskérelmek kezelését vagy a szolgáltatás állapotának figyelését. A jelszó-rendszergazdák csak a nem rendszergazda vagy a következő szerepkörök tagjainak jelszavát állíthatják alaphelyzetbe:

* Címtárolvasók
* Vendég meghívó
* Jelszó-rendszergazda

### <a name="power-bi-administrator"></a>[Power BI-rendszergazda](#power-bi-service-administrator-permissions)

Az ilyen szerepkörrel rendelkező felhasználók globális engedélyekkel rendelkeznek a Microsoft Power BI-n belül, ha a szolgáltatás jelen van, valamint kezelhetik a támogatási jegyeket és figyelhetik a szolgáltatás állapotát. További információ [A Power BI rendszergazdai szerepkörének ismertetése](https://docs.microsoft.com/power-bi/service-admin-role)című témakörben található.

> [!NOTE]
> A Microsoft Graph API-ban és az Azure AD PowerShellben ez a szerepkör "Power BI-szolgáltatás rendszergazdája" néven van azonosítva. Ez a "Power BI-rendszergazda" az [Azure Portalon.](https://portal.azure.com)

### <a name="power-platform-administrator"></a>[A Power Platform rendszergazdája](#power-platform-administrator-permissions)

Ebben a szerepkörben lévő felhasználók létrehozhatják és kezelhetik a környezetek, a PowerApps, a Folyamatok és az adatveszteség-megelőzési házirendek minden aspektusát. Emellett az ezzel a szerepkörrel rendelkező felhasználók kezelhetik a támogatási jegyeket, és figyelhetik a szolgáltatás állapotát.

### <a name="privileged-authentication-administrator"></a>[Kiemelt hitelesítési rendszergazda](#privileged-authentication-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók beállíthatják vagy alaphelyzetbe állíthatják a nem jelszó hitelesítő adatokat az összes felhasználó számára, beleértve a globális rendszergazdákat is, és frissíthetik az összes felhasználó jelszavait. A kiemelt jogosultságú hitelesítési rendszergazdák kényszeríthetik a felhasználókat, hogy újra regisztráljanak a meglévő nem jelszó hitelesítő adatok (pl. MFA, FIDO) ellen, és visszavonják a "Ne mfa az eszközön" parancsot, és az összes felhasználó következő bejelentkezésekor az MFA-t kérik.

### <a name="privileged-role-administrator"></a>[Kiemelt szerepkör-rendszergazda](#privileged-role-administrator-permissions)

Ezzel a szerepkörrel rendelkező felhasználók kezelhetik a szerepkör-hozzárendeléseket az Azure Active Directoryban, valamint az Azure AD kiemelt identitáskezelésen belül. Emellett ez a szerepkör lehetővé teszi a kiemelt identitáskezelés és a felügyeleti egységek minden aspektusának kezelését.

> [!IMPORTANT]
> Ez a szerepkör lehetővé teszi az összes Azure AD-szerepkör hozzárendelésének kezelését, beleértve a globális rendszergazdai szerepkört is. Ez a szerepkör nem tartalmaz más kiemelt képességeket az Azure AD-ben, például a felhasználók létrehozása vagy frissítése. A szerepkörhöz rendelt felhasználók azonban további szerepkörök hozzárendelésével további jogosultságot adhatnak maguknak vagy másoknak.

### <a name="reports-reader"></a>[Jelentések olvasó](#reports-reader-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók megtekinthetik a használati jelentési adatokat és a jelentések irányítópultját a Microsoft 365 Felügyeleti központban, valamint a Power BI bevezetési környezetcsomagját. Emellett a szerepkör hozzáférést biztosít a bejelentkezési jelentésekhez és tevékenységekhez az Azure AD-ben és a Microsoft Graph jelentéskészítési API által visszaadott adatokhoz. A Jelentések olvasó szerepkörhöz rendelt felhasználó csak a megfelelő használati és bevezetési mutatókhoz férhet hozzá. Nem rendelkeznek rendszergazdai engedélyekkel a beállítások konfigurálásához vagy a termékspecifikus felügyeleti központok, például az Exchange eléréséhez. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

### <a name="search-administrator"></a>[Keresés rendszergazdája](#search-administrator-permissions)

A szerepkörben szereplő felhasználók teljes hozzáféréssel rendelkeznek a Microsoft 365 felügyeleti központ összes Microsoft Search felügyeleti szolgáltatásához. A keresési rendszergazdák átruházhatják a keresési rendszergazdák és a Keresésszerkesztő szerepköröket a felhasználókra, valamint létrehozhatnak és kezelhetnek tartalmakat, például könyvjelzőket, Q&As-t és helyeket. Emellett ezek a felhasználók megtekinthetik az üzenetközpontot, figyelhetik a szolgáltatás állapotát, és szolgáltatási kérelmeket hozhatnak létre.

### <a name="search-editor"></a>[Keresés szerkesztő](#search-editor-permissions)

A szerepkörben szereplő felhasználók a Microsoft 365 Felügyeleti központban hozhatnak létre, kezelhetnek és törölhetnek tartalmakat a Microsoft Search szolgáltatáshoz, beleértve a könyvjelzőket, a Q&As-t és a helyeket.

### <a name="security-administrator"></a>[Biztonsági rendszergazda](#security-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók rendelkeznek a Microsoft 365 biztonsági központ, az Azure Active Directory identitásvédelem, az Azure Information Protection és az Office 365 Biztonsági & megfelelőségi központ biztonsággal kapcsolatos szolgáltatásainak kezeléséhez szükséges engedélyekkel. Az Office 365-engedélyekről [az Engedélyek az Office 365 Biztonsági & Megfelelőségi központban](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)található.

A | Meg tudja csinálni
--- | ---
[A Microsoft 365 biztonsági központja](https://protection.office.com) | A biztonsággal kapcsolatos házirendek figyelése a Microsoft 365 szolgáltatásaiban<br>Biztonsági fenyegetések és riasztások kezelése<br>Jelentések megtekintése
Identitásvédelmi központ | A Biztonsági olvasó szerepkör összes engedélye<br>Ezenkívül az Identitásvédelmi Központ összes műveletének végrehajtása, kivéve a jelszavak alaphelyzetbe állítását
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | A Biztonsági olvasó szerepkör összes engedélye<br>**Az** Azure AD szerepkör-hozzárendelések és -beállítások nem kezelhetők
[Az Office 365 Biztonsági & megfelelőségi központja](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Biztonsági házirendek kezelése<br>Biztonsági fenyegetések megtekintése, kivizsgálása és megválaszolása<br>Jelentések megtekintése
Azure Komplex veszélyforrások elleni védelem | A gyanús biztonsági tevékenységek figyelése és az azokra való reagálás
Windows Defender ATP és EDR | Szerepkörök hozzárendelése<br>Gépcsoportok kezelése<br>Végponti veszélyforrások észlelésének és automatikus szervizelésének konfigurálása<br>Riasztások megtekintése, vizsgálata és megválaszolása
[megkezdése](https://docs.microsoft.com/intune/role-based-access-control) | Felhasználói, eszköz-, regisztrációs, konfigurációs és alkalmazásadatok megtekintése<br>Nem lehet módosítani az Intune-t
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Rendszergazdák hozzáadása, házirendek és beállítások hozzáadása, naplók feltöltése és cégirányítási műveletek végrehajtása
[Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, szerkesztheti a biztonsági házirendeket, megtekintheti a riasztásokat és ajánlásokat, elvetheti a riasztásokat és javaslatokat
[Az Office 365 szolgáltatás állapota](https://docs.microsoft.com/office365/enterprise/view-service-health) | Az Office 365-szolgáltatások állapotának megtekintése

### <a name="security-operator"></a>[Biztonsági operátor](#security-operator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók kezelhetik a riasztásokat, és globális, csak olvasható hozzáféréssel rendelkezhetnek a biztonsággal kapcsolatos funkciókhoz, beleértve a Microsoft 365 biztonsági központban, az Azure Active Directoryban, az Identitásvédelemben, a Kiemelt identitáskezelésben és az Office 365 Biztonsági & megfelelőségi központban található összes információt. Az Office 365-engedélyekről [az Engedélyek az Office 365 Biztonsági & Megfelelőségi központban](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center)található.

A | Meg tudja csinálni
--- | ---
[A Microsoft 365 biztonsági központja](https://protection.office.com) | A Biztonsági olvasó szerepkör összes engedélye<br>Biztonsági fenyegetésekre vonatkozó riasztások megtekintése, vizsgálata és megválaszolása
Identitásvédelmi központ | A Biztonsági olvasó szerepkör összes engedélye<br>Ezenkívül az Identitásvédelmi Központ összes műveletének végrehajtása, kivéve a jelszavak alaphelyzetbe állítását
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | A Biztonsági olvasó szerepkör összes engedélye
[Az Office 365 Biztonsági & megfelelőségi központja](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | A Biztonsági olvasó szerepkör összes engedélye<br>Biztonsági riasztások megtekintése, vizsgálata és megválaszolása
Windows Defender ATP és EDR | A Biztonsági olvasó szerepkör összes engedélye<br>Biztonsági riasztások megtekintése, vizsgálata és megválaszolása
[megkezdése](https://docs.microsoft.com/intune/role-based-access-control) | A Biztonsági olvasó szerepkör összes engedélye
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | A Biztonsági olvasó szerepkör összes engedélye
[Az Office 365 szolgáltatás állapota](https://docs.microsoft.com/office365/enterprise/view-service-health) | Az Office 365-szolgáltatások állapotának megtekintése

### <a name="security-reader"></a>[Biztonsági olvasó](#security-reader-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók globális, csak olvasható hozzáféréssel rendelkeznek a biztonsággal kapcsolatos szolgáltatásokhoz, beleértve a Microsoft 365 biztonsági központ, az Azure Active Directory, az identitásvédelem, a kiemelt identitáskezelés, valamint az Azure Active Directory bejelentkezési jelentések és naplók olvasásának lehetőségét, valamint az Office 365 Biztonsági & megfelelőségi központban található összes információt. Az Office 365-engedélyekről [az Engedélyek az Office 365 Biztonsági & Megfelelőségi központban](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)található.

A | Meg tudja csinálni
--- | ---
[A Microsoft 365 biztonsági központja](https://protection.office.com) | A biztonsággal kapcsolatos házirendek megtekintése a Microsoft 365 szolgáltatásaiban<br>Biztonsági fenyegetések és riasztások megtekintése<br>Jelentések megtekintése
Identitásvédelmi központ | A biztonsági szolgáltatások biztonsági jelentéseiben és beállítási információiban szereplő összes információ elolvasása<br><ul><li>Anti-spam<li>Titkosítás<li>Adatveszteség-megelőzés<li>Kártevők elleni<li>Speciális fenyegetésvédelem<li>Adathalászat elleni<li>Levélfolyam-szabályok
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Csak olvasható hozzáféréssel rendelkezik az Azure AD kiemelt identitáskezelés: szabályzatok és jelentések az Azure AD szerepkör-hozzárendelések és a biztonsági felülvizsgálatok.<br>**Nem lehet** regisztrálni az Azure AD kiemelt identitáskezelés, vagy bármilyen módosítást. A kiemelt identitáskezelési portálon vagy a PowerShellen keresztül a szerepkörben szereplő személyek további szerepköröket aktiválhatnak (például globális rendszergazda vagy kiemelt szerepkör-rendszergazda), ha a felhasználó jogosult rájuk.
[Az Office 365 Biztonsági & megfelelőségi központja](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Biztonsági szabályzatok megtekintése<br>Biztonsági fenyegetések megtekintése és vizsgálata<br>Jelentések megtekintése
Windows Defender ATP és EDR | Riasztások megtekintése és vizsgálata. Ha bekapcsolja a szerepköralapú hozzáférés-vezérlést a Windows Defender ATP-ben, az írásvédett engedélyekkel rendelkező felhasználók, például az Azure AD Security olvasói szerepkör elveszítik a hozzáférést, amíg hozzá nem rendelik őket egy Windows Defender ATP-szerepkörhöz.
[megkezdése](https://docs.microsoft.com/intune/role-based-access-control) | Megtekinti a felhasználó, az eszköz, a regisztráció, a konfiguráció és az alkalmazás adatait. Az Intune-ban nem hajthat végre változtatásokat.
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Írásvédett engedélyekkel rendelkezik, és képes a riasztások kezelésére
[Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Megtekintheti a javaslatokat és riasztásokat, megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, de nem módosíthatja
[Az Office 365 szolgáltatás állapota](https://docs.microsoft.com/office365/enterprise/view-service-health) | Az Office 365-szolgáltatások állapotának megtekintése

### <a name="service-support-administrator"></a>[Szolgáltatástámogatási rendszergazda](#service-support-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók támogatási kérelmeket nyithatnak meg a Microsoft for Azure és Office 365 szolgáltatásokhoz, és megtekinthetik a szolgáltatás irányítópultját és az üzenetközpontot az [Azure Portalon](https://portal.azure.com) és a [Microsoft 365 Felügyeleti központban.](https://admin.microsoft.com) További információ a [Rendszergazdai szerepkörök ről.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

> [!NOTE]
> Ezt a szerepkört korábban "Szolgáltatás-rendszergazda" néven hívták az [Azure Portalon](https://portal.azure.com) és a [Microsoft 365 Felügyeleti központban.](https://admin.microsoft.com) Átneveztük "Szolgáltatástámogatási rendszergazdára", hogy igazodjon a Microsoft Graph API, az Azure AD Graph API és az Azure AD PowerShell exsiting nevéhez.

### <a name="sharepoint-administrator"></a>[SharePoint-rendszergazda](#sharepoint-service-administrator-permissions)

Az ilyen szerepkörrel rendelkező felhasználók globális engedélyekkel rendelkeznek a Microsoft SharePoint Online-ban, ha a szolgáltatás jelen van, valamint az összes Office 365-csoportot létrehozhatják és kezelhetik, támogatási jegyeket kezelnek, és figyelhetik a szolgáltatás állapotát. További információ a [Rendszergazdai szerepkörök ről.](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)

> [!NOTE]
> A Microsoft Graph API-ban és az Azure AD PowerShellben ez a szerepkör "SharePoint-szolgáltatás-rendszergazdaként" van azonosítva. Ez a "SharePoint-rendszergazda" az [Azure portalon.](https://portal.azure.com)

### <a name="skype-for-business--lync-administrator"></a>[Skype Vállalati verzió/ Lync-rendszergazda](#lync-service-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók globális engedélyekkel rendelkeznek a Microsoft Skype Vállalati verzióban, ha a szolgáltatás jelen van, valamint kezelhetik a Skype-specifikus felhasználói attribútumokat az Azure Active Directoryban. Ezenkívül ez a szerepkör lehetővé teszi a támogatási jegyek kezelését és a szolgáltatások állapotának figyelését, valamint a Teams és a Skype Vállalati verzió felügyeleti központjának elérését. A fióknak a Teamshez is licenceltnek kell lennie, különben nem futtathatja a Teams PowerShell-parancsmagjait. További információ [A Skype Vállalati verzió rendszergazdai szerepköréről](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) és a Teams licencelési adatairól a Skype Vállalati verzió és a Microsoft Teams [bővítménylicencelésben](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> A Microsoft Graph API-ban és az Azure AD PowerShellben ez a szerepkör "Lync-szolgáltatás-rendszergazdaként" van azonosítva. Ez a "Skype Vállalati verzió rendszergazdája" az [Azure portalon.](https://portal.azure.com/)

### <a name="teams-communications-administrator"></a>[Teams kommunikációs rendszergazdája](#teams-communications-administrator-permissions)

Ebben a szerepkörben a felhasználók kezelhetik a Microsoft Teams hangalapú & telefonszolgáltatással kapcsolatos számítási feladatainak szempontjait. Ez magában foglalja a telefonszám-hozzárendelés felügyeleti eszközeit, a hang- és értekezlet-házirendeket, valamint a híváselemzési eszközkészlet teljes körű elérését.

### <a name="teams-communications-support-engineer"></a>[A Teams kommunikációs támogatási mérnöke](#teams-communications-support-engineer-permissions)

A szerepkörben részt vevő felhasználók a Microsoft Teams & a Skype Vállalati verzióban a Microsoft Teams & Skype Vállalati verzió felügyeleti központjában található felhasználói híváshibaelhárítási eszközök segítségével elháríthatják a kommunikációs problémákat. A szerepkörben részt vevő felhasználók megtekinthetik az összes érintett résztvevő teljes hívásrekord-információit. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

### <a name="teams-communications-support-specialist"></a>[Teams kommunikációs támogatási specialistája](#teams-communications-support-specialist-permissions)

A szerepkörben részt vevő felhasználók a Microsoft Teams & a Skype Vállalati verzióban a Microsoft Teams & Skype Vállalati verzió felügyeleti központjában található felhasználói híváshibaelhárítási eszközök segítségével elháríthatják a kommunikációs problémákat. A szerepkörben lévő felhasználók csak az adott felhasználó által keresett felhasználó hívásának felhasználói adatait tekinthetik meg. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

### <a name="teams-service-administrator"></a>[Teams szolgáltatás rendszergazdája](#teams-service-administrator-permissions)

A szerepkörben szereplő felhasználók a Microsoft Teams számítási feladatainak minden aspektusát kezelhetik a Microsoft Teams & Skype Vállalati verzió felügyeleti központján és a megfelelő PowerShell-modulokon keresztül. Ez magában foglalja többek között a telefonos, üzenetküldési, értekezletekkel és magukkal a csapatokkal kapcsolatos összes felügyeleti eszközt. Ez a szerepkör emellett lehetővé teszi az összes Office 365-csoport létrehozását és kezelését, a támogatási jegyek kezelését és a szolgáltatások állapotának figyelését.

### <a name="user-administrator"></a>[Felhasználói rendszergazda](#user-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók felhasználókat hozhatnak létre, és bizonyos korlátozásokkal kezelhetik a felhasználók minden aspektusát (lásd alább), és frissíthetik a jelszó elévülési szabályzatait. Ezenkívül az ezzel a szerepkörrel rendelkező felhasználók létrehozhatják és kezelhetik az összes csoportot. Ez a szerepkör felhasználói nézetek létrehozását és kezelését, támogatási jegyek kezelését és a szolgáltatás állapotának figyelését is magában foglalja. A felhasználói rendszergazdák nem rendelkeznek engedéllyel a legtöbb rendszergazdai szerepkörben lévő felhasználók bizonyos felhasználói tulajdonságainak kezeléséhez. Az ezzel a szerepkörrel rendelkező felhasználók nem rendelkeznek az MFA kezeléséhez. A korlátozás alóli kivételeket az alábbi táblázat tartalmazza.

| | |
| --- | --- |
|Általános engedélyek|<p>Felhasználók és csoportok létrehozása</p><p>Felhasználói nézetek létrehozása és kezelése</p><p>Office-támogatási jegyek kezelése<p>Jelszóelévési házirendek frissítése|
|<p>Minden felhasználón, beleértve az összes rendszergazdát is</p>|<p>Licencek kezelése</p><p>Az egyszerű felhasználónév kivételével az összes felhasználói tulajdonság kezelése</p>
|Csak olyan felhasználóknál, akik nem adminisztrátorok, vagy az alábbi korlátozott rendszergazdai szerepkörök bármelyikében:<ul><li>Címtárolvasók<li>Vendég meghívó<li>Ügyfélszolgálati rendszergazda<li>Üzenetközpont olvasója<li>Jelentések olvasó<li>Felhasználói rendszergazda|<p>Törlés és visszaállítás</p><p>Letiltás és engedélyezés</p><p>Tokenek frissítésének érvénytelenítése</p><p>Az összes felhasználói tulajdonság kezelése, beleértve az egyszerű felhasználónevet is</p><p>Új jelszó létrehozása</p><p>Frissítési (FIDO) eszközkulcsok</p>|

> [!IMPORTANT]
> Ezzel a szerepkörrel rendelkező felhasználók módosíthatják a jelszavakat azok számára, akik hozzáférhetnek a bizalmas vagy személyes adatokhoz vagy a kritikus konfigurációhoz az Azure Active Directoryn belül és kívül. A felhasználó jelszavának módosítása azt jelentheti, hogy fel lehet vállalni a felhasználó identitását és engedélyeit. Például:
>
>- Alkalmazásregisztráció és vállalati alkalmazástulajdonosok, akik kezelhetik a tulajdonukban lévő alkalmazások hitelesítő adatait. Ezek az alkalmazások előfordulhat, hogy az Azure AD és máshol nem biztosított a felhasználói rendszergazdák jogosultsággal rendelkeznek. Ezen az útvonalon keresztül a felhasználói rendszergazda felveheti az alkalmazás tulajdonosának identitását, majd tovább feltételezheti egy kiemelt jogosultságú alkalmazás identitását az alkalmazás hitelesítő adatainak frissítésével.
>- Az Azure-előfizetés-tulajdonosok, akik hozzáférhetnek a bizalmas vagy személyes adatokhoz vagy a kritikus konfigurációhoz az Azure-ban.
>- Biztonsági csoport és az Office 365-csoport tulajdonosai, akik kezelhetik a csoporttagságot. Ezek a csoportok hozzáférést biztosíthatnak bizalmas vagy személyes adatokhoz vagy kritikus konfigurációhoz az Azure AD-ben és máshol.
>- Az Azure AD-n kívüli egyéb szolgáltatások, például az Exchange Online, az Office Security and Compliance Center és az emberi erőforrás-rendszerek rendszergazdái.
>- Nem adminisztrátorok, például vezetők, jogi tanácsadók és humánerőforrás-alkalmazottak, akik érzékeny vagy személyes adatokhoz férhetnek hozzá.

## <a name="role-permissions"></a>Szerepkör engedélyei

Az alábbi táblázatok ismertetik az egyes szerepkörek adott adott engedélyeket az Azure Active Directoryban. Egyes szerepkörök további engedélyekkel rendelkezhetnek az Azure Active Directoryn kívüli Microsoft-szolgáltatásokban.

### <a name="application-administrator-permissions"></a>Alkalmazásrendszergazdai engedélyek

Az alkalmazásregisztrációk és a vállalati alkalmazások minden aspektusát létrehozhatja és kezelheti.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/Application/appProxyAuthentication/update | Az Azure Active Directory egyszerű szolgáltatásain frissítheti az alkalmazásproxy-hitelesítés i. |
| microsoft.directory/Application/appProxyUrlSettings/update | Frissítse az alkalmazásproxy belső és külső URL-címeit az Azure Active Directoryban. |
| microsoft.directory/applications/applicationProxy/read | Olvassa el az összes alkalmazásproxy-tulajdonságot. |
| microsoft.directory/applications/applicationProxy/update | Frissítse az összes alkalmazásproxy-tulajdonságot. |
| microsoft.directory/applications/audience/update | Frissítse az applications.audience tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/authentication/update | Frissítse a applications.authentication tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/basic/update | Az Azure Active Directory ban lévő alkalmazások alapvető tulajdonságainak frissítése. |
| microsoft.directory/applications/create | Alkalmazások létrehozása az Azure Active Directoryban. |
| microsoft.directory/applications/credentials/update | Frissítse az applications.credentials tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/delete | Alkalmazások törlése az Azure Active Directoryban. |
| microsoft.directory/applications/owners/update | Frissítse az applications.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/permissions/update | Frissítse az applications.permissions tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/policies/update | Frissítse az applications.policies tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/appRoleAssignments/create | Hozzon létre appRoleAssignments az Azure Active Directoryban. |
| microsoft.directory/appRoleAssignments/read | Az appRoleAssignments olvasása az Azure Active Directoryban. |
| microsoft.directory/appRoleAssignments/update | Frissítse az appRoleAssignments-eket az Azure Active Directoryban. |
| microsoft.directory/appRoleAssignments/delete | Törölje az appRoleAssignments-eket az Azure Active Directoryban. |
| microsoft.directory/auditLogs/allProperties/read | Olvassa el az összes tulajdonságot (beleértve a kiemelt tulajdonságokat is) az Azure Active Directory naplózási naplóiban. |
| microsoft.directory/connectorGroups/everything/read | Alkalmazásproxy-összekötő csoport tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.directory/connectorGroups/everything/update | Frissítse az összes alkalmazásproxy-összekötő csoport tulajdonságait az Azure Active Directoryban. |
| microsoft.directory/connectorGroups/create | Hozzon létre alkalmazásproxy-összekötő csoportokat az Azure Active Directoryban. |
| microsoft.directory/connectorGroups/delete | Az Azure Active Directoryban törölje az alkalmazásproxy-összekötő csoportokat. |
| microsoft.directory/connectors/everything/read | Olvassa el az Azure Active Directory összes alkalmazásproxy-összekötő tulajdonságát. |
| microsoft.directory/connectors/create | Hozzon létre alkalmazásproxy-összekötőket az Azure Active Directoryban. |
| microsoft.directory/policies/applicationConfiguration/basic/read | A policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/policies/applicationConfiguration/basic/update | A policies.applicationConfiguration tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.directory/policies/applicationConfiguration/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.directory/policies/applicationConfiguration/delete | Házirendek törlése az Azure Active Directoryban. |
| microsoft.directory/policies/applicationConfiguration/owners/read | A policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/policies/applicationConfiguration/owners/update | A policies.applicationConfiguration tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | A policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Frissítse a servicePrincipals.appRoleAssignedTo tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Frissítse a ServicePrincipals.appRoleAssignments tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/audience/update | Frissítse a servicePrincipals.audience tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/authentication/update | Frissítse a ServicePrincipals.authentication tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/basic/update | Az Azure Active Directory alapvető tulajdonságainak frissítése. |
| microsoft.directory/servicePrincipals/create | Hozzon létre serviceprincipals az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/credentials/update | Frissítse a servicePrincipals.credentials tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/delete | Az Azure Active Directory ban lévő szolgáltatásfővéi törlése. |
| microsoft.directory/servicePrincipals/owners/update | Frissítse a ServicePrincipals.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/permissions/update | Frissítse a ServicePrincipals.permissions tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/policies/update | Frissítse a ServicePrincipals.policies tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/signInReports/allProperties/read | Olvassa el az összes tulajdonságot (beleértve a kiemelt tulajdonságokat is) az Azure Active Directoryban a signInReports szolgáltatásban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |

### <a name="application-developer-permissions"></a>Alkalmazásfejlesztői engedélyek

A "Felhasználók regisztrálhatnak alkalmazásokat" beállítástól függetlenül létrehozhat alkalmazásregisztrációkat.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/applications/createAsOwner | Alkalmazások létrehozása az Azure Active Directoryban. A létrehozó lesz az első tulajdonos, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumkvótájába. |
| microsoft.directory/appRoleAssignments/createAsOwner | Hozzon létre appRoleAssignments az Azure Active Directoryban. A létrehozó lesz az első tulajdonos, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumkvótájába. |
| microsoft.directory/oAuth2PermissionGrants/createAsOwner | OAuth2PermissionGrants létrehozása az Azure Active Directoryban. A létrehozó lesz az első tulajdonos, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumkvótájába. |
| microsoft.directory/servicePrincipals/createAsOwner | Hozzon létre serviceprincipals az Azure Active Directoryban. A létrehozó lesz az első tulajdonos, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumkvótájába. |

### <a name="authentication-administrator-permissions"></a>Hitelesítési rendszergazdai engedélyek

A hitelesítési módszer adatainak megtekintése, beállítása és alaphelyzetbe állítása bármely nem rendszergazda felhasználó számára engedélyezett.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | Érvényteleníti az összes felhasználói frissítési jogkivonatot az Azure Active Directoryban. |
| microsoft.directory/users/strongHitelesítés/frissítés | Frissítse az erős hitelesítési tulajdonságokat, például az MFA hitelesítő adatait. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |
| microsoft.directory/users/password/update | Az Office 365-szervezet összes felhasználójának jelszavának frissítése. További részleteket az online dokumentációban talál. |

### <a name="azure-devops-administrator-permissions"></a>Az Azure DevOps rendszergazdai engedélyei

Kezelheti az Azure DevOps szervezeti szabályzatát és beállításait.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti [szerepkörleírásban](#azure-devops-administrator) talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.devOps/allEntities/allTasks | Az Azure DevOps olvasása és konfigurálása. |

### <a name="azure-information-protection-administrator-permissions"></a>Az Azure Information Protection Administrator engedélyei

Az Azure Information Protection szolgáltatás minden aspektusát kezelheti.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti [szerepkörleírásban](#) talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Az Azure Information Protection minden aspektusát kezelheti. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |

### <a name="b2c-user-flow-administrator-permissions"></a>B2C felhasználói folyamat rendszergazdai engedélyei

A felhasználói folyamatok minden aspektusának létrehozása és kezelése.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Felhasználói folyamatok olvasása és konfigurálása az Azure Active Directory B2C-ben. |

### <a name="b2c-user-flow-attribute-administrator-permissions"></a>B2C felhasználói folyamat attribútumának rendszergazdai engedélyei

Hozza létre és kezelje az összes felhasználói folyamat számára elérhető attribútumsémát.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Felhasználói attribútumok olvasása és konfigurálása az Azure Active Directory B2C-ben. |

### <a name="b2c-ief-keyset-administrator-permissions"></a>B2C IEF-kulcskészlet-rendszergazdai engedélyek

Az összevonás és a titkosítás titkos kulcsainak kezelése az identitáskezelési keretrendszerben.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Kulcskészletek olvasása és konfigurálása az Azure Active Directory B2C-ben. |

### <a name="b2c-ief-policy-administrator-permissions"></a>B2C IEF házirend-rendszergazdai engedélyek

Hozzon létre és kezeljen megbízhatósági keretházirendeket az identitáskezelési keretrendszerben.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Egyéni szabályzatok olvasása és konfigurálása az Azure Active Directory B2C-ben. |

### <a name="billing-administrator-permissions"></a>Számlázási rendszergazdai engedélyek

A számlázással kapcsolatos gyakori feladatokat, például a fizetési adatok frissítését végezheti el.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/organization/basic/update | Frissítse az azure-beli Active Directory szervezetalapvető tulajdonságait. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.commerce.billing/allEntities/allTasks | Az Office 365-ös számlázás minden aspektusát kezelheti. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |

### <a name="cloud-application-administrator-permissions"></a>Felhőalkalmazás-rendszergazdai engedélyek

Az alkalmazásregisztrációk és a vállalati alkalmazások minden aspektusát létrehozhatja és kezelheti, kivéve az alkalmazásproxyt.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/applications/audience/update | Frissítse az applications.audience tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/authentication/update | Frissítse a applications.authentication tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/basic/update | Az Azure Active Directory ban lévő alkalmazások alapvető tulajdonságainak frissítése. |
| microsoft.directory/applications/create | Alkalmazások létrehozása az Azure Active Directoryban. |
| microsoft.directory/applications/credentials/update | Frissítse az applications.credentials tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/delete | Alkalmazások törlése az Azure Active Directoryban. |
| microsoft.directory/applications/owners/update | Frissítse az applications.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/permissions/update | Frissítse az applications.permissions tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/policies/update | Frissítse az applications.policies tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/appRoleAssignments/create | Hozzon létre appRoleAssignments az Azure Active Directoryban. |
| microsoft.directory/appRoleAssignments/update | Frissítse az appRoleAssignments-eket az Azure Active Directoryban. |
| microsoft.directory/appRoleAssignments/delete | Törölje az appRoleAssignments-eket az Azure Active Directoryban. |
| microsoft.directory/auditLogs/allProperties/read | Olvassa el az összes tulajdonságot (beleértve a kiemelt tulajdonságokat is) az Azure Active Directory naplózási naplóiban. |
| microsoft.directory/policies/applicationConfiguration/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.directory/policies/applicationConfiguration/basic/read | A policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/policies/applicationConfiguration/basic/update | A policies.applicationConfiguration tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.directory/policies/applicationConfiguration/delete | Házirendek törlése az Azure Active Directoryban. |
| microsoft.directory/policies/applicationConfiguration/owners/read | A policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/policies/applicationConfiguration/owners/update | A policies.applicationConfiguration tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | A policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Frissítse a servicePrincipals.appRoleAssignedTo tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Frissítse a ServicePrincipals.appRoleAssignments tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/audience/update | Frissítse a servicePrincipals.audience tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/authentication/update | Frissítse a ServicePrincipals.authentication tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/basic/update | Az Azure Active Directory alapvető tulajdonságainak frissítése. |
| microsoft.directory/servicePrincipals/create | Hozzon létre serviceprincipals az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/credentials/update | Frissítse a servicePrincipals.credentials tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/delete | Az Azure Active Directory ban lévő szolgáltatásfővéi törlése. |
| microsoft.directory/servicePrincipals/owners/update | Frissítse a ServicePrincipals.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/permissions/update | Frissítse a ServicePrincipals.permissions tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/policies/update | Frissítse a ServicePrincipals.policies tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/signInReports/allProperties/read | Olvassa el az összes tulajdonságot (beleértve a kiemelt tulajdonságokat is) az Azure Active Directoryban a signInReports szolgáltatásban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |

### <a name="cloud-device-administrator-permissions"></a>Felhőeszköz-rendszergazdai engedélyek

Teljes hozzáférés az eszközök kezeléséhez az Azure AD-ben.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Olvassa el az összes tulajdonságot (beleértve a kiemelt tulajdonságokat is) az Azure Active Directory naplózási naplóiban. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Az azure Active Directoryban olvassa el az devices.bitLockerRecoveryKeys tulajdonságot. |
| microsoft.directory/devices/delete | Eszközök törlése az Azure Active Directoryban. |
| microsoft.directory/devices/disable | Letilthatja az eszközöket az Azure Active Directoryban. |
| microsoft.directory/devices/enable | Engedélyezze az eszközöket az Azure Active Directoryban. |
| microsoft.directory/signInReports/allProperties/read | Olvassa el az összes tulajdonságot (beleértve a kiemelt tulajdonságokat is) az Azure Active Directoryban a signInReports szolgáltatásban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |

### <a name="company-administrator-permissions"></a>Vállalati rendszergazdai engedélyek

Az Azure AD-identitásokat használó Azure AD- és Microsoft-szolgáltatások minden aspektusát kezelheti. Ezt a szerepkört globális rendszergazdai szerepkörnek is nevezik. 

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Hozza létre és törölje az összes erőforrást, és olvassa el és frissítse a szabványos tulajdonságokat a microsoft.aad.cloudAppSecurity webhelyen. |
| microsoft.directory/administrativeUnits/allProperties/allTasks | Hozzon létre és töröljön felügyeletiegységeket, és olvassa el és frissítse az Azure Active Directory összes tulajdonságát. |
| microsoft.directory/applications/allProperties/allTasks | Alkalmazások létrehozása és törlése, valamint az Azure Active Directory összes tulajdonságának olvasása és frissítése. |
| microsoft.directory/appRoleAssignments/allProperties/allTasks | Hozzon létre és töröljön appRoleAssignments, és olvassa el és frissítse az összes tulajdonság az Azure Active Directoryban. |
| microsoft.directory/auditLogs/allProperties/read | Olvassa el az összes tulajdonságot (beleértve a kiemelt tulajdonságokat is) az Azure Active Directory naplózási naplóiban. |
| microsoft.directory/contacts/allProperties/allTasks | Kapcsolatok létrehozása és törlése, valamint az Azure Active Directory összes tulajdonságának olvasása és frissítése. |
| microsoft.directory/contracts/allProperties/allTasks | Szerződések et hozhat létre és törölhet, valamint olvashatja és frissítheti az Azure Active Directory összes tulajdonságát. |
| microsoft.directory/devices/allProperties/allTasks | Hozzon létre és töröljön eszközöket, és olvassa el és frissítse az Azure Active Directory összes tulajdonságát. |
| microsoft.directory/directoryRoles/allProperties/allTasks | Hozzon létre és töröljön directoryRoles, és olvassa el és frissítse az összes tulajdonság az Azure Active Directoryban. |
| microsoft.directory/directoryRoleTemplates/allProperties/allTasks | Hozzon létre és töröljön directoryRoleTemplates, és olvassa el és frissítse az összes tulajdonság az Azure Active Directoryban. |
| microsoft.directory/domains/allProperties/allTasks | Tartományok létrehozása és törlése, valamint az Azure Active Directory összes tulajdonságának olvasása és frissítése. |
| microsoft.directory/groups/allProperties/allTasks | Csoportok létrehozása és törlése, valamint az Azure Active Directory összes tulajdonságának olvasása és frissítése. |
| microsoft.directory/groupSettings/allProperties/allTasks | Hozzon létre és töröljön groupSettings, és olvassa el és frissítse az összes tulajdonság az Azure Active Directoryban. |
| microsoft.directory/groupSettingTemplates/allProperties/allTasks | Hozzon létre és töröljön groupSettingTemplates, és olvassa el és frissítse az összes tulajdonság az Azure Active Directoryban. |
| microsoft.directory/loginTenantBranding/allProperties/allTasks | Hozzon létre és töröljön loginTenantBranding, és olvassa el és frissítse az összes tulajdonság az Azure Active Directoryban. |
| microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth2PermissionGrants létrehozása és törlése, valamint az Azure Active Directory összes tulajdonságának olvasása és frissítése. |
| microsoft.directory/organization/allProperties/allTasks | Hozzon létre és töröljön szervezetet, és olvassa el és frissítse az Azure Active Directory összes tulajdonságát. |
| microsoft.directory/policies/allProperties/allTasks | Szabályzatok létrehozása és törlése, valamint az Azure Active Directory összes tulajdonságának olvasása és frissítése. |
| microsoft.directory/roleAssignments/allProperties/allTasks | Hozzon létre és töröljön szerepkör-hozzárendeléseket, és olvassa el és frissítse az Azure Active Directory összes tulajdonságát. |
| microsoft.directory/roleDefinitions/allProperties/allTasks | Hozzon létre és töröljön roleDefinitions, és olvassa el és frissítse az összes tulajdonság az Azure Active Directoryban. |
| microsoft.directory/scopedRoleMemberships/allProperties/allTasks | ScopedRoleMemberships létrehozása és törlése, valamint az Azure Active Directory összes tulajdonságának olvasása és frissítése. |
| microsoft.directory/serviceAction/activateService | Az Aktiválási szolgáltatás szolgáltatás művelete az Azure Active Directoryban |
| microsoft.directory/serviceAction/disableDirectoryFeature | Az Disabledirectoryfeature szolgáltatás művelet végrehajtása az Azure Active Directoryban |
| microsoft.directory/serviceAction/enableDirectoryFeature | Az Enabledirectoryfeature szolgáltatás műveletvégrehajtása az Azure Active Directoryban |
| microsoft.directory/serviceAction/getAvailableExtentionProperties | A Getavailable extentionproperties szolgáltatás műveletvégrehajtása az Azure Active Directoryban |
| microsoft.directory/servicePrincipals/allProperties/allTasks | Hozzon létre és töröljön servicePrincipals, és olvassa el és frissítse az összes tulajdonság az Azure Active Directoryban. |
| microsoft.directory/signInReports/allProperties/read | Olvassa el az összes tulajdonságot (beleértve a kiemelt tulajdonságokat is) az Azure Active Directoryban a signInReports szolgáltatásban. |
| microsoft.directory/subscribedSkus/allProperties/allTasks | Hozzon létre és töröljön előfizetettSkus, és olvassa el és frissítse az összes tulajdonság az Azure Active Directoryban. |
| microsoft.directory/users/allProperties/allTasks | Felhasználók létrehozása és törlése, valamint az Azure Active Directory összes tulajdonságának olvasása és frissítése. |
| microsoft.directorySync/allEntities/allTasks | Az Azure AD Connect ben hajtsa végre az összes műveletet. |
| microsoft.aad.identityProtection/allEntities/allTasks | Hozza létre és törölje az összes erőforrást, valamint olvassa el és frissítse a szabványos tulajdonságokat a microsoft.aad.identityProtection webhelyen. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Olvassa el a microsoft.aad.privilegedIdentityManagement összes erőforrását. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Olvassa el a microsoft.azure.advancedThreatProtection összes erőforrását. |
| microsoft.azure.informationProtection/allEntities/allTasks | Az Azure Information Protection minden aspektusát kezelheti. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.commerce.billing/allEntities/allTasks | Az Office 365-ös számlázás minden aspektusát kezelheti. |
| microsoft.intune/allEntities/allTasks | Az Intune minden aspektusának kezelése. |
| microsoft.office365.complianceManager/allEntities/allTasks | Az Office 365 Megfelelőségi kezelőjének minden aspektusának kezelése |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Az asztali elemzés minden aspektusát kezelheti. |
| microsoft.office365.exchange/allEntities/allTasks | Az Exchange Online minden aspektusát kezelheti. |
| microsoft.office365.lockbox/allEntities/allTasks | Az Office 365 ügyfélszéfjének minden aspektusának kezelése |
| microsoft.office365.messageCenter/messages/read | Üzenetek olvasása a microsoft.office365.messageCenter alkalmazásban. |
| microsoft.office365.messageCenter/securityMessages/read | Olvassa el a securityMessages programot a microsoft.office365.messageCenter alkalmazásban. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Az Office 365 Védelmi Központ minden aspektusának kezelése. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | A microsoft.office365.securityComplianceCenter webhelyen létrehozhatja és törölheti az összes erőforrást, valamint elolvashatja és frissítheti a szabványos tulajdonságokat. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.sharepoint/allEntities/allTasks | A microsoft.office365.sharepoint webhelyen létrehozhatja és törölheti az összes erőforrást, valamint elolvashatja és frissítheti a szabványos tulajdonságokat. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype Vállalati online verzió minden aspektusát kezelheti. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.usageReports/allEntities/read | Olvassa el az Office 365 használati jelentéseit. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | A Dynamics 365 minden aspektusát kezelheti. |
| microsoft.powerApps.powerBI/allEntities/allTasks | A Power BI minden aspektusát kezelheti. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Olvassa el a microsoft.windows.defender(microsoft.windows.defenderAdvancedThreatProtection) összes erőforrását. |

### <a name="compliance-administrator-permissions"></a>Megfelelőségi rendszergazdai engedélyek

Az Azure AD-ben és az Office 365-ben olvashatja és kezelheti a megfelelőségi konfigurációkat és jelentéseket.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.complianceManager/allEntities/allTasks | Az Office 365 Megfelelőségi kezelőjének minden aspektusának kezelése |
| microsoft.office365.exchange/allEntities/allTasks | Az Exchange Online minden aspektusát kezelheti. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.sharepoint/allEntities/allTasks | A microsoft.office365.sharepoint webhelyen létrehozhatja és törölheti az összes erőforrást, valamint elolvashatja és frissítheti a szabványos tulajdonságokat. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype Vállalati online verzió minden aspektusát kezelheti. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |

### <a name="compliance-data-administrator-permissions"></a>Megfelelőségi adatok rendszergazdájának engedélyei

Megfelelőségi tartalom létrehozása és kezelése.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Olvassa el és konfigurálja a Microsoft Cloud App Security alkalmazást. |
| microsoft.azure.informationProtection/allEntities/allTasks | Az Azure Information Protection minden aspektusát kezelheti. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.complianceManager/allEntities/allTasks | Az Office 365 Megfelelőségi kezelőjének minden aspektusának kezelése |
| microsoft.office365.exchange/allEntities/allTasks | Az Exchange Online minden aspektusát kezelheti. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.sharepoint/allEntities/allTasks | A microsoft.office365.sharepoint webhelyen létrehozhatja és törölheti az összes erőforrást, valamint elolvashatja és frissítheti a szabványos tulajdonságokat. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype Vállalati online verzió minden aspektusát kezelheti. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |

### <a name="conditional-access-administrator-permissions"></a>Feltételes hozzáférési rendszergazdai engedélyek

Kezelheti a feltételes hozzáférés képességeit.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/policies/conditionalAccess/basic/read | A policies.conditionalAccess tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/policies/conditionalAccess/basic/update | Frissítse a policies.conditionalAccess tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/policies/conditionalAccess/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.directory/policies/conditionalAccess/delete | Házirendek törlése az Azure Active Directoryban. |
| microsoft.directory/policies/conditionalAccess/owners/read | A policies.conditionalAccess tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/policies/conditionalAccess/owners/update | Frissítse a policies.conditionalAccess tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/policies/conditionalAccess/policiesAppliedTo/read | A policies.conditionalAccess tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/policies/conditionalAccess/tenantDefault/update | Frissítse a policies.conditionalAccess tulajdonságot az Azure Active Directoryban. |

### <a name="crm-service-administrator-permissions"></a>CRM szolgáltatásrendszergazdai engedélyek

A Dynamics 365 termék minden aspektusát kezelheti.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | A Dynamics 365 minden aspektusát kezelheti. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |

### <a name="customer-lockbox-access-approver-permissions"></a>Ügyfél lockbox hozzáférési jóváhagyói engedélyei

Jóváhagyhatja a Microsoft támogatási kérelmeit az ügyfél szervezeti adatainak elérésére.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.lockbox/allEntities/allTasks | Az Office 365 ügyfélszéfjének minden aspektusának kezelése |

### <a name="desktop-analytics-administrator-permissions"></a>Asztali elemzés rendszergazdai engedélyei

Kezelheti az Asztali elemzés és az Office testreszabása & házirend szolgáltatások. A Desktop Analytics esetében ez magában foglalja az eszközleltár megtekintését, a telepítési tervek létrehozását, a központi telepítés és az állapot megtekintését. Az Office testreszabása & házirend szolgáltatás esetén ez a szerepkör lehetővé teszi a felhasználók számára az Office-házirendek kezelését.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Az asztali elemzés minden aspektusát kezelheti. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |

### <a name="device-administrators-permissions"></a>Eszközrendszergazdák engedélyei

A szerepkörhöz rendelt felhasználók hozzáadódnak a helyi rendszergazdák csoporthoz az Azure AD-hez csatlakozó eszközökön.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/groupSettings/alapszintű/olvasás | Alapvető tulajdonságok olvasása a groupSettings az Azure Active Directoryban. |
| microsoft.directory/groupSettingTemplates/basic/read | Alapvető tulajdonságok olvasása a groupSettingTemplates az Azure Active Directoryban. |

### <a name="directory-readers-permissions"></a>Címtárolvasók engedélyei
Olvasni az alapvető könyvtárinformációkat. A kérelmekhez való hozzáférés biztosításához, nem felhasználók nak szánt.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/administrativeUnits/basic/read | Az Azure Active Directory ban található felügyeleti egységek alapvető tulajdonságainak olvasása. |
| microsoft.directory/administrativeUnits/members/read | Olvassa el a administrativeUnits.members tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/basic/read | Az Azure Active Directoryban az alkalmazások alapvető tulajdonságainak olvasása. |
| microsoft.directory/applications/owners/read | Olvassa el a applications.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/policies/read | A applications.policies tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/contacts/basic/read | Az Azure Active Directoryban lévő névjegyek alapvető tulajdonságainak olvasása. |
| microsoft.directory/contacts/memberOf/read | Olvassa el a contacts.memberOf tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/contracts/basic/read | Az Azure Active Directoryban a szerződések alapvető tulajdonságait olvashatja. |
| microsoft.directory/devices/basic/read | Az Azure Active Directory ban lévő eszközök alapvető tulajdonságainak olvasása. |
| microsoft.directory/devices/memberOf/read | Az azure Active Directoryban olvasd el a devices.memberOf tulajdonságot. |
| microsoft.directory/devices/registeredOwners/read | Olvassa devices.registeredOwners tulajdonság az Azure Active Directoryban. |
| microsoft.directory/devices/registeredUsers/read | Az eszközök.registeredUsers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/directoryRoles/basic/read | Az Azure Active Directoryban a directoryRoles alapvető tulajdonságainak olvasása. |
| microsoft.directory/directoryRoles/eligibleMembers/read | Olvasd el a directoryRoles.eligibleMembers tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/directoryRoles/members/read | Olvasd el a directoryRoles.members tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/domains/basic/read | Az Azure Active Directory tartományaiban alapvető tulajdonságokat olvashat. |
| microsoft.directory/groups/appRoleAssignments/read | A groups.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/groups/basic/read | Az Azure Active Directory csoportok alapvető tulajdonságainak olvasása. |
| microsoft.directory/groups/memberOf/read | Olvassa el a groups.memberOf tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/members/read | A groups.members tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/groups/owners/read | A groups.owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/groups/settings/read | A groups.settings tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/groupSettings/alapszintű/olvasás | Alapvető tulajdonságok olvasása a groupSettings az Azure Active Directoryban. |
| microsoft.directory/groupSettingTemplates/basic/read | Alapvető tulajdonságok olvasása a groupSettingTemplates az Azure Active Directoryban. |
| microsoft.directory/oAuth2PermissionGrants/basic/read | Az azure-active directoryban az oAuth2PermissionGrants alapvető tulajdonságait olvassa el. |
| microsoft.directory/organization/basic/read | Az Azure Active Directory ban a szervezet alapvető tulajdonságait olvashatja. |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | Olvassa el a organization.trustedCAsForPasswordlessAuth tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/roleAssignments/basic/read | Az Azure Active Directoryban a szerepkör-hozzárendelések alapvető tulajdonságainak olvasása. |
| microsoft.directory/roleDefinitions/basic/read | Az Azure Active Directoryban a roleDefinitions alapvető tulajdonságainak olvasása. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Olvassa el a servicePrincipals.appRoleAssignedTo tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | Olvassa el a servicePrincipals.appRoleAssignments tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/basic/read | Az Azure Active Directory ban a servicePrincipals alapvető tulajdonságait olvassa el. |
| microsoft.directory/servicePrincipals/memberOf/read | Olvassa el a ServicePrincipals.memberOf tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Olvassa el a servicePrincipals.oAuth2PermissionGrants tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/ownedObjects/read | Olvassa el a servicePrincipals.ownedObjects tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/owners/read | Olvassa el a ServicePrincipals.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/policies/read | Olvassa el a ServicePrincipals.policies tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/subscribedSkus/basic/read | Az Azure Active Directoryban az előfizetettSkus alapvető tulajdonságait olvashatja. |
| microsoft.directory/users/appRoleAssignments/read | A users.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/basic/read | Az Azure Active Directory felhasználóinak alapvető tulajdonságainak olvasása. |
| microsoft.directory/users/directReports/read | A users.directReports tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/manager/read | A users.manager tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/memberOf/read | A users.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | A users.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/ownedDevices/read | A users.ownedDevices tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/ownedObjects/read | A users.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/registeredDevices/read | A users.registeredDevices tulajdonság olvasása az Azure Active Directoryban. |

### <a name="directory-synchronization-accounts-permissions"></a>Címtár-szinkronizálási fiókok engedélyei

Csak az Azure AD Connect szolgáltatás használja.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/organization/dirSync/update | Frissítse a organization.dirSync tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/policies/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.directory/policies/delete | Házirendek törlése az Azure Active Directoryban. |
| microsoft.directory/policies/basic/read | Az Azure Active Directory szabályzatai alapvető tulajdonságait olvashatja. |
| microsoft.directory/policies/basic/update | Az Azure Active Directory szabályzatai alapvető tulajdonságainak frissítése. |
| microsoft.directory/policies/owners/read | A policies.owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/policies/owners/update | Frissítse a policies.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/policies/policiesAppliedTo/read | A policies.policiesAppliedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/policies/tenantDefault/update | Frissítse a policies.tenantDefault tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Olvassa el a servicePrincipals.appRoleAssignedTo tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Frissítse a servicePrincipals.appRoleAssignedTo tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | Olvassa el a servicePrincipals.appRoleAssignments tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Frissítse a ServicePrincipals.appRoleAssignments tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/audience/update | Frissítse a servicePrincipals.audience tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/authentication/update | Frissítse a ServicePrincipals.authentication tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/basic/read | Az Azure Active Directory ban a servicePrincipals alapvető tulajdonságait olvassa el. |
| microsoft.directory/servicePrincipals/basic/update | Az Azure Active Directory alapvető tulajdonságainak frissítése. |
| microsoft.directory/servicePrincipals/create | Hozzon létre serviceprincipals az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/credentials/update | Frissítse a servicePrincipals.credentials tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/memberOf/read | Olvassa el a ServicePrincipals.memberOf tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Olvassa el a servicePrincipals.oAuth2PermissionGrants tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/owners/read | Olvassa el a ServicePrincipals.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/owners/update | Frissítse a ServicePrincipals.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/ownedObjects/read | Olvassa el a servicePrincipals.ownedObjects tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/permissions/update | Frissítse a ServicePrincipals.permissions tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/policies/read | Olvassa el a ServicePrincipals.policies tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/policies/update | Frissítse a ServicePrincipals.policies tulajdonságot az Azure Active Directoryban. |
| microsoft.directorySync/allEntities/allTasks | Az Azure AD Connect ben hajtsa végre az összes műveletet. |

### <a name="directory-writers-permissions"></a>Címtárírók engedélyei

Lehet olvasni & írni az alapvető könyvtárinformációkat. A kérelmekhez való hozzáférés biztosításához, nem felhasználók nak szánt.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/groups/create | Hozzon létre csoportokat az Azure Active Directoryban. |
| microsoft.directory/groups/createAsOwner | Hozzon létre csoportokat az Azure Active Directoryban. A létrehozó lesz az első tulajdonos, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumkvótájába. |
| microsoft.directory/groups/appRoleAssignments/update | Frissítse a groups.appRoleAssignments tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/basic/update | Az Azure Active Directory csoportok alapvető tulajdonságainak frissítése. |
| microsoft.directory/groups/members/update | Frissítse a groups.members tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/owners/update | Frissítse a groups.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/settings/update | Frissítse a groups.settings tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groupSettings/alapszintű/frissítés | Az Azure Active Directory alapvető tulajdonságainak frissítése a groupSettings szolgáltatásban. |
| microsoft.directory/groupSettings/create | Hozzon létre groupSettings az Azure Active Directoryban. |
| microsoft.directory/groupBeállítások/törlés | Törölje a groupSettings szolgáltatást az Azure Active Directoryban. |
| microsoft.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/users/assignLicense | Az Azure Active Directory felhasználóinak licenceit kezelheti. |
| microsoft.directory/users/basic/update | Az Azure Active Directory felhasználóinak alapvető tulajdonságainak frissítése. |
| microsoft.directory/users/invalidateAllRefreshTokens | Érvényteleníti az összes felhasználói frissítési jogkivonatot az Azure Active Directoryban. |
| microsoft.directory/users/manager/update | Frissítse a users.manager tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/users/userPrincipalName/update | Frissítse a users.userPrincipalName tulajdonságot az Azure Active Directoryban. |

### <a name="exchange-service-administrator-permissions"></a>Exchange-szolgáltatásrendszergazdai engedélyek

Az Exchange termék minden aspektusát kezelheti.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Frissítse a groups.unified tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/unified/basic/update | Az Office 365 Csoportok alapvető tulajdonságainak frissítése. |
| microsoft.directory/groups/unified/create | Hozza létre az Office 365-csoportokat. |
| microsoft.directory/groups/unified/delete | Az Office 365-csoportok törlése. |
| microsoft.directory/groups/unified/members/update | Office 365-csoportok tagságának frissítése. |
| microsoft.directory/groups/unified/owners/update | Az Office 365-csoportok tulajdonjogának frissítése. |
| microsoft.office365.exchange/allEntities/allTasks | Az Exchange Online minden aspektusát kezelheti. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.usageReports/allEntities/read | Olvassa el az Office 365 használati jelentéseit. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |

### <a name="external-identity-provider-administrator-permissions"></a>Külső identitásszolgáltató rendszergazdájának engedélyei

Konfigurálja az identitásszolgáltatókat a közvetlen összevonásban való használatra.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Az Azure Active Directory B2C-ben olvassa és konfigurálja az identitásszolgáltatókat. |

### <a name="global-reader-permissions"></a>Globális olvasó engedélyei
Mindent eltud olvasni, amit egy globális rendszergazda tud, de nem szerkeszthet semmit.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti [szerepkörleírásban](#global-reader) talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.commerce.billing/allEntities/read    | Olvassa el az Office 365-ös számlázás minden aspektusát. |
| microsoft.directory/administrativeUnits/basic/read    | Az Azure Active Directory ban található felügyeleti egységek alapvető tulajdonságainak olvasása. |
| microsoft.directory/administrativeUnits/members/read    | Olvassa el a administrativeUnits.members tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/basic/read    | Az Azure Active Directoryban az alkalmazások alapvető tulajdonságainak olvasása. |
| microsoft.directory/applications/owners/read    | Olvassa el a applications.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/applications/policies/read    | A applications.policies tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/contacts/basic/read    | Az Azure Active Directoryban lévő névjegyek alapvető tulajdonságainak olvasása. |
| microsoft.directory/contacts/memberOf/read    | Olvassa el a contacts.memberOf tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/contracts/basic/read    | Az Azure Active Directoryban a szerződések alapvető tulajdonságait olvashatja. |
| microsoft.directory/devices/basic/read    | Az Azure Active Directory ban lévő eszközök alapvető tulajdonságainak olvasása. |
| microsoft.directory/devices/memberOf/read    | Az azure Active Directoryban olvasd el a devices.memberOf tulajdonságot. |
| microsoft.directory/devices/registeredOwners/read    | Olvassa devices.registeredOwners tulajdonság az Azure Active Directoryban. |
| microsoft.directory/devices/registeredUsers/read    | Az eszközök.registeredUsers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/directoryRoles/basic/read    | Az Azure Active Directoryban a directoryRoles alapvető tulajdonságainak olvasása. |
| microsoft.directory/directoryRoles/eligibleMembers/read    | Olvasd el a directoryRoles.eligibleMembers tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/directoryRoles/members/read    | Olvasd el a directoryRoles.members tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/domains/basic/read    | Az Azure Active Directory tartományaiban alapvető tulajdonságokat olvashat. |
| microsoft.directory/groups/appRoleAssignments/read    | A groups.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/groups/basic/read    | Az Azure Active Directory csoportok alapvető tulajdonságainak olvasása. |
| microsoft.directory/groups/hiddenMembers/read    | Olvassa el a groups.hiddenMembers tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/memberOf/read    | Olvassa el a groups.memberOf tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/members/read    | A groups.members tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/groups/owners/read    | A groups.owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/groups/settings/read    | A groups.settings tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/groupSettings/alapszintű/olvasás    | Alapvető tulajdonságok olvasása a groupSettings az Azure Active Directoryban. |
| microsoft.directory/groupSettingTemplates/basic/read    | Alapvető tulajdonságok olvasása a groupSettingTemplates az Azure Active Directoryban. |
| microsoft.directory/oAuth2PermissionGrants/basic/read    | Az azure-active directoryban az oAuth2PermissionGrants alapvető tulajdonságait olvassa el. |
| microsoft.directory/organization/basic/read    | Az Azure Active Directory ban a szervezet alapvető tulajdonságait olvashatja. |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read    | Olvassa el a organization.trustedCAsForPasswordlessAuth tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/policies/standard/read    | Az Azure Active Directoryban szabványos szabályzatok olvasása. |
| microsoft.directory/roleAssignments/basic/read    | Az Azure Active Directoryban a szerepkör-hozzárendelések alapvető tulajdonságainak olvasása. |
| microsoft.directory/roleDefinitions/basic/read    | Az Azure Active Directoryban a roleDefinitions alapvető tulajdonságainak olvasása. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read    | Olvassa el a servicePrincipals.appRoleAssignedTo tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read    | Olvassa el a servicePrincipals.appRoleAssignments tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/basic/read    | Az Azure Active Directory ban a servicePrincipals alapvető tulajdonságait olvassa el. |
| microsoft.directory/servicePrincipals/memberOf/read    | Olvassa el a ServicePrincipals.memberOf tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read    | Olvassa el a servicePrincipals.oAuth2PermissionGrants tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/ownedObjects/read    | Olvassa el a servicePrincipals.ownedObjects tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/owners/read    | Olvassa el a ServicePrincipals.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/policies/read    | Olvassa el a ServicePrincipals.policies tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/signInReports/allProperties/read    | Olvassa el az összes tulajdonságot (beleértve a kiemelt tulajdonságokat is) az Azure Active Directoryban a signInReports szolgáltatásban. |
| microsoft.directory/subscribedSkus/basic/read    | Az Azure Active Directoryban az előfizetettSkus alapvető tulajdonságait olvashatja. |
| microsoft.directory/users/appRoleAssignments/read    | A users.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/basic/read    | Az Azure Active Directory felhasználóinak alapvető tulajdonságainak olvasása. |
| microsoft.directory/users/directReports/read    | A users.directReports tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/manager/read    | A users.manager tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/memberOf/read    | A users.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read    | A users.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/ownedDevices/read    | A users.ownedDevices tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/ownedObjects/read    | A users.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/registeredDevices/read    | A users.registeredDevices tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/strongHitelesítés/olvasás    | Olvassa el az erős hitelesítési tulajdonságokat, például az MFA hitelesítő adatokat. |
| microsoft.office365.exchange/allEntities/read    | Olvassa el az Exchange Online minden aspektusát. |
| microsoft.office365.messageCenter/messages/read    | Üzenetek olvasása a microsoft.office365.messageCenter alkalmazásban. |
| microsoft.office365.messageCenter/securityMessages/read    | Olvassa el a securityMessages programot a microsoft.office365.messageCenter alkalmazásban. |
| microsoft.office365.protectionCenter/allEntities/read    | Olvassa el az Office 365 Védelmi központ minden aspektusát. |
| microsoft.office365.securityComplianceCenter/allEntities/read    | Olvassa el a microsoft.office365.securityComplianceCenter összes szabványos tulajdonságát. |
| microsoft.office365.usageReports/allEntities/read    | Olvassa el az Office 365 használati jelentéseit. |
| microsoft.office365.webPortal/allEntities/standard/read    | A microsoft.office365.webPortal webhelyen található összes erőforrás szabványos tulajdonságait olvassa el. |

### <a name="groups-administrator-permissions"></a>Csoportok rendszergazdai engedélyei
A csoportok és a csoportbeállítások minden aspektusát kezelheti, például az elnevezési és elévülési házirendeket.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/groups/basic/read | Az Azure Active Directory csoportok szabványos tulajdonságainak olvasása.  |
| microsoft.directory/groups/basic/update | Az Azure Active Directory csoportok alapvető tulajdonságainak frissítése. |
| microsoft.directory/groups/create | Hozzon létre csoportokat az Azure Active Directoryban. |
| microsoft.directory/groups/createAsOwner | Hozzon létre csoportokat az Azure Active Directoryban. A létrehozó lesz az első tulajdonos, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumkvótájába. |
| microsoft.directory/groups/delete | Csoportok törlése az Azure Active Directoryban. |
| microsoft.directory/groups/hiddenMembers/read | Olvassa el a groups.hiddenMembers tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/members/update | Frissítse a groups.members tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/owners/update | Frissítse a groups.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/restore | Csoportok visszaállítása az Azure Active Directoryban. |
| microsoft.directory/groups/settings/update | Frissítse a groups.settings tulajdonságot az Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.messageCenter/messages/read | Üzenetek olvasása a microsoft.office365.messageCenter alkalmazásban. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |

### <a name="guest-inviter-permissions"></a>Vendégmeghívó engedélyei
Meghívhatja a vendégfelhasználókat a "tagok meghívhatvendégek" beállítástól függetlenül.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/users/appRoleAssignments/read | A users.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/basic/read | Az Azure Active Directory felhasználóinak alapvető tulajdonságainak olvasása. |
| microsoft.directory/users/directReports/read | A users.directReports tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/inviteGuest | Vendégfelhasználók meghívása az Azure Active Directoryban. |
| microsoft.directory/users/manager/read | A users.manager tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/memberOf/read | A users.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | A users.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/ownedDevices/read | A users.ownedDevices tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/ownedObjects/read | A users.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/users/registeredDevices/read | A users.registeredDevices tulajdonság olvasása az Azure Active Directoryban. |

### <a name="helpdesk-administrator-permissions"></a>Helpdesk rendszergazdai engedélyek

A nem rendszergazdák és a helpdesk-rendszergazdák jelszavainak alaphelyzetbe állítása.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Az azure Active Directoryban olvassa el az devices.bitLockerRecoveryKeys tulajdonságot. |
| microsoft.directory/users/invalidateAllRefreshTokens | Érvényteleníti az összes felhasználói frissítési jogkivonatot az Azure Active Directoryban. |
| microsoft.directory/users/password/update | Az Azure Active Directory összes felhasználójának jelszavát frissítse. További részleteket az online dokumentációban talál. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |

### <a name="intune-service-administrator-permissions"></a>Intune-szolgáltatás rendszergazdai engedélyei

Az Intune-termék minden aspektusát kezelheti.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Az Azure Active Directoryban lévő névjegyek alapvető tulajdonságainak frissítése. |
| microsoft.directory/contacts/create | Hozzon létre névjegyeket az Azure Active Directoryban. |
| microsoft.directory/contacts/delete | Névjegyek törlése az Azure Active Directoryban. |
| microsoft.directory/devices/basic/update | Az Azure Active Directory ban lévő eszközök alapvető tulajdonságainak frissítése. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Az azure Active Directoryban olvassa el az devices.bitLockerRecoveryKeys tulajdonságot. |
| microsoft.directory/devices/create | Hozzon létre eszközöket az Azure Active Directoryban. |
| microsoft.directory/devices/delete | Eszközök törlése az Azure Active Directoryban. |
| microsoft.directory/devices/registeredOwners/update | Frissítse az devices.registeredOwners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/devices/registeredUsers/update | Frissítse az devices.registeredUsers tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/appRoleAssignments/update | Frissítse a groups.appRoleAssignments tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/basic/update | Az Azure Active Directory csoportok alapvető tulajdonságainak frissítése. |
| microsoft.directory/groups/create | Hozzon létre csoportokat az Azure Active Directoryban. |
| microsoft.directory/groups/createAsOwner | Hozzon létre csoportokat az Azure Active Directoryban. A létrehozó lesz az első tulajdonos, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumkvótájába. |
| microsoft.directory/groups/delete | Csoportok törlése az Azure Active Directoryban. |
| microsoft.directory/groups/hiddenMembers/read | Olvassa el a groups.hiddenMembers tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/members/update | Frissítse a groups.members tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/owners/update | Frissítse a groups.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/restore | Csoportok visszaállítása az Azure Active Directoryban. |
| microsoft.directory/groups/settings/update | Frissítse a groups.settings tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/users/basic/update | Az Azure Active Directory felhasználóinak alapvető tulajdonságainak frissítése. |
| microsoft.directory/users/manager/update | Frissítse a users.manager tulajdonságot az Azure Active Directoryban. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.intune/allEntities/allTasks | Az Intune minden aspektusának kezelése. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |

### <a name="kaizala-administrator-permissions"></a>Kaizala rendszergazdai engedélyek

Kezelheti a Microsoft Kaizala beállításait.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | Olvassa el az Office 365 Felügyeleti központ ot. |

### <a name="license-administrator-permissions"></a>Licencrendszergazdai engedélyek

A felhasználók és csoportok terméklicenceit kezelheti.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/users/assignLicense | Az Azure Active Directory felhasználóinak licenceit kezelheti. |
| microsoft.directory/users/usageLocation/update | Frissítse a users.usageLocation tulajdonságot az Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |

### <a name="lync-service-administrator-permissions"></a>Lync szolgáltatásfelügyeleti engedélyek

A Skype Vállalati verziós termék minden aspektusát kezelheti.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype Vállalati online verzió minden aspektusát kezelheti. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.usageReports/allEntities/read    | Olvassa el az Office 365 használati jelentéseit. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |


### <a name="message-center-privacy-reader-permissions"></a>Üzenetközpont adatvédelmi olvasói engedélyei

Olvashatja a Message Center-bejegyzéseket, az adatvédelmi üzeneteket, a csoportokat, a tartományokat és az előfizetéseket.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.messageCenter/messages/read | Üzenetek olvasása a microsoft.office365.messageCenter alkalmazásban. |
| microsoft.office365.messageCenter/securityMessages/read | Olvassa el a securityMessages programot a microsoft.office365.messageCenter alkalmazásban. |

### <a name="message-center-reader-permissions"></a>Üzenetközpont-olvasó engedélyei
Csak az Office 365 Üzenetközpontban olvashatüzeneteket és frissítéseket a szervezetükszámára. 

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.messageCenter/messages/read | Üzenetek olvasása a microsoft.office365.messageCenter alkalmazásban. |

### <a name="office-apps-administrator-permissions"></a>Office-alkalmazások rendszergazdai engedélyei
Kezelheti az Office-alkalmazások felhőszolgáltatásait, beleértve a házirendek és beállítások kezelését, és kezelheti az "újdonságok" funkciótartalmának kiválasztását, kijelölését és közzétételét a végfelhasználó eszközeire.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.messageCenter/messages/read | Üzenetek olvasása a microsoft.office365.messageCenter alkalmazásban. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.userCommunication/allEntities/allTasks | Olvassa el és frissítse az Újdonságok üzenetek láthatóságát. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |

### <a name="partner-tier1-support-permissions"></a>Partner szint1 támogatási engedélyei

Ne használja - nem általános használatra szánták.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Az Azure Active Directoryban lévő névjegyek alapvető tulajdonságainak frissítése. |
| microsoft.directory/contacts/create | Hozzon létre névjegyeket az Azure Active Directoryban. |
| microsoft.directory/contacts/delete | Névjegyek törlése az Azure Active Directoryban. |
| microsoft.directory/groups/create | Hozzon létre csoportokat az Azure Active Directoryban. |
| microsoft.directory/groups/createAsOwner | Hozzon létre csoportokat az Azure Active Directoryban. A létrehozó lesz az első tulajdonos, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumkvótájába. |
| microsoft.directory/groups/members/update | Frissítse a groups.members tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/owners/update | Frissítse a groups.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/users/assignLicense | Az Azure Active Directory felhasználóinak licenceit kezelheti. |
| microsoft.directory/users/basic/update | Az Azure Active Directory felhasználóinak alapvető tulajdonságainak frissítése. |
| microsoft.directory/users/delete | Felhasználók törlése az Azure Active Directoryban. |
| microsoft.directory/users/invalidateAllRefreshTokens | Érvényteleníti az összes felhasználói frissítési jogkivonatot az Azure Active Directoryban. |
| microsoft.directory/users/manager/update | Frissítse a users.manager tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/users/password/update | Az Azure Active Directory összes felhasználójának jelszavát frissítse. További részleteket az online dokumentációban talál. |
| microsoft.directory/users/restore | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.directory/users/userPrincipalName/update | Frissítse a users.userPrincipalName tulajdonságot az Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |

### <a name="partner-tier2-support-permissions"></a>Partner tier2 támogatási engedélyei

Ne használja - nem általános használatra szánták.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Az Azure Active Directoryban lévő névjegyek alapvető tulajdonságainak frissítése. |
| microsoft.directory/contacts/create | Hozzon létre névjegyeket az Azure Active Directoryban. |
| microsoft.directory/contacts/delete | Névjegyek törlése az Azure Active Directoryban. |
| microsoft.directory/domains/allTasks | Tartományok létrehozása és törlése, valamint a szabványos tulajdonságok olvasása és frissítése az Azure Active Directoryban. |
| microsoft.directory/groups/create | Hozzon létre csoportokat az Azure Active Directoryban. |
| microsoft.directory/groups/delete | Csoportok törlése az Azure Active Directoryban. |
| microsoft.directory/groups/members/update | Frissítse a groups.members tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/restore | Csoportok visszaállítása az Azure Active Directoryban. |
| microsoft.directory/organization/basic/update | Frissítse az azure-beli Active Directory szervezetalapvető tulajdonságait. |
| microsoft.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/users/assignLicense | Az Azure Active Directory felhasználóinak licenceit kezelheti. |
| microsoft.directory/users/basic/update | Az Azure Active Directory felhasználóinak alapvető tulajdonságainak frissítése. |
| microsoft.directory/users/delete | Felhasználók törlése az Azure Active Directoryban. |
| microsoft.directory/users/invalidateAllRefreshTokens | Érvényteleníti az összes felhasználói frissítési jogkivonatot az Azure Active Directoryban. |
| microsoft.directory/users/manager/update | Frissítse a users.manager tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/users/password/update | Az Azure Active Directory összes felhasználójának jelszavát frissítse. További részleteket az online dokumentációban talál. |
| microsoft.directory/users/restore | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.directory/users/userPrincipalName/update | Frissítse a users.userPrincipalName tulajdonságot az Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |

### <a name="password-administrator-permissions"></a>Jelszórendszergazda-engedélyek

A nem rendszergazdák és a jelszó-rendszergazdák jelszavait visszaállíthatja.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/users/password/update | Az Azure Active Directory összes felhasználójának jelszavát frissítse. További részleteket az online dokumentációban talál. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |

### <a name="power-bi-service-administrator-permissions"></a>Power BI-szolgáltatás rendszergazdai engedélyei

A Power BI-termék minden aspektusát kezelheti.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>
| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.powerApps.powerBI/allEntities/allTasks | A Power BI minden aspektusát kezelheti. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |


### <a name="power-platform-administrator-permissions"></a>A Power Platform rendszergazdájának engedélyei

A Microsoft Dynamics 365, a PowerApps és a Microsoft Flow minden aspektusát létrehozhatja és kezelheti. 

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>
| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.dynamics365/allEntities/allTasks | A Dynamics 365 minden aspektusát kezelheti. |
| microsoft.flow/allEntities/allTasks | A Microsoft Flow minden aspektusát kezelheti. |
| microsoft.powerApps/allEntities/allTasks | A PowerApps minden aspektusát kezelheti. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |

### <a name="privileged-authentication-administrator-permissions"></a>Kiemelt hitelesítési rendszergazdai engedélyek

Bármely felhasználó (rendszergazda vagy nem rendszergazda) számára engedélyezett a hitelesítési módszer adatainak megtekintése, beállítása és visszaállítása.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | Érvényteleníti az összes felhasználói frissítési jogkivonatot az Azure Active Directoryban. |
| microsoft.directory/users/strongHitelesítés/frissítés | Frissítse az erős hitelesítési tulajdonságokat, például az MFA hitelesítő adatait. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |
| microsoft.directory/users/password/update | Az Office 365-szervezet összes felhasználójának jelszavának frissítése. További részleteket az online dokumentációban talál. |

### <a name="privileged-role-administrator-permissions"></a>Kiemelt szerepkör-rendszergazdai engedélyek

Az Azure AD szerepkör-hozzárendelések kezelésére, és a kiemelt identitáskezelés minden szempontból.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Hozza létre és törölje az összes erőforrást, és olvassa el és frissítse a szabványos tulajdonságokat a microsoft.aad.privilegedIdentityManagement webhelyen. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/allTasks | A servicePrincipals.appRoleAssignedTo tulajdonság olvasása és konfigurálása az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | A servicePrincipals.oAuth2PermissionGrants tulajdonság olvasása és konfigurálása az Azure Active Directoryban. |
| microsoft.directory/administrativeUnits/allProperties/allTasks | Felügyeleti egységek létrehozása és kezelése (a tagokkal együtt) |
| microsoft.directory/roleAssignments/allProperties/allTasks | Szerepkör-hozzárendelések létrehozása és kezelése. |
| microsoft.directory/roleDefinitions/allProperties/allTasks | Szerepkör-definíciók létrehozása és kezelése. |

### <a name="reports-reader-permissions"></a>A Jelentések olvasója engedélyei

Olvashatja a bejelentkezési és naplózási jelentéseket.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Olvassa el az összes tulajdonságot (beleértve a kiemelt tulajdonságokat is) az Azure Active Directory naplózási naplóiban. |
| microsoft.directory/signInReports/allProperties/read | Olvassa el az összes tulajdonságot (beleértve a kiemelt tulajdonságokat is) az Azure Active Directoryban a signInReports szolgáltatásban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.usageReports/allEntities/read | Olvassa el az Office 365 használati jelentéseit. |

### <a name="search-administrator-permissions"></a>Rendszergazda keresése engedélyek

A Microsoft Search beállításainak minden aspektusát létrehozhatja és kezelheti.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Üzenetek olvasása a microsoft.office365.messageCenter alkalmazásban. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Hozza létre és törölje az összes erőforrást, és olvassa el és frissítse az összes tulajdonságot a microsoft.office365.search fájlban. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |

### <a name="search-editor-permissions"></a>Keresésszerkesztő engedélyei

Létrehozhatja és kezelheti a szerkesztői tartalmakat, például a könyvjelzőket, a Q és as-t, a helyeket, az alaprajzot.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Üzenetek olvasása a microsoft.office365.messageCenter alkalmazásban. |
| microsoft.office365.search/content/allProperties/allTasks | Tartalom létrehozása és törlése, valamint a microsoft.office365.search összes tulajdonságának olvasása és frissítése. |

### <a name="security-administrator-permissions"></a>Biztonsági rendszergazdai engedélyek

Biztonsági információk és jelentések olvasása és a konfiguráció kezelése az Azure AD és az Office 365.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/applications/policies/update | Frissítse az applications.policies tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/auditLogs/allProperties/read | Olvassa el az összes tulajdonságot (beleértve a kiemelt tulajdonságokat is) az Azure Active Directory naplózási naplóiban. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Az azure Active Directoryban olvassa el az devices.bitLockerRecoveryKeys tulajdonságot. |
| microsoft.directory/policies/basic/update | Az Azure Active Directory szabályzatai alapvető tulajdonságainak frissítése. |
| microsoft.directory/policies/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.directory/policies/delete | Házirendek törlése az Azure Active Directoryban. |
| microsoft.directory/policies/owners/update | Frissítse a policies.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/policies/tenantDefault/update | Frissítse a policies.tenantDefault tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/policies/update | Frissítse a ServicePrincipals.policies tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/signInReports/allProperties/read | Olvassa el az összes tulajdonságot (beleértve a kiemelt tulajdonságokat is) az Azure Active Directoryban a signInReports szolgáltatásban. |
| microsoft.aad.identityProtection/allEntities/read | Olvassa el a microsoft.aad.identityProtection összes erőforrását. |
| microsoft.aad.identityProtection/allEntities/update | Frissítse a microsoft.aad.identityProtection összes erőforrását. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Olvassa el a microsoft.aad.privilegedIdentityManagement összes erőforrását. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.protectionCenter/allEntities/read | Olvassa el az Office 365 Védelmi központ minden aspektusát. |
| microsoft.office365.protectionCenter/allEntities/update | Frissítse a microsoft.office365.protectionCenter összes erőforrását. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |

### <a name="security-operator-permissions"></a>Biztonsági operátor engedélyei

Biztonsági eseményeket hoz létre és kezel.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Olvassa el és konfigurálja a Microsoft Cloud App Security alkalmazást. |
| microsoft.aad.identityProtection/allEntities/read | Olvassa el a microsoft.aad.identityProtection összes erőforrását. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Olvassa el a microsoft.aad.privilegedIdentityManagement összes erőforrását. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Az Azure AD komplex veszélyforrások elleni védelem olvasása és konfigurálása. |
| microsoft.intune/allEntities/allTasks | Az Intune minden aspektusának kezelése. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | A Biztonsági & megfelelőségi központ olvasása és konfigurálása. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Olvassa el és konfigurálja a Windows Defender komplex veszélyforrások elleni védelmét. |

### <a name="security-reader-permissions"></a>Biztonsági olvasó engedélyei

Az Azure AD-ben és az Office 365-ben biztonsági információkat és jelentéseket olvashat.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Olvassa el az összes tulajdonságot (beleértve a kiemelt tulajdonságokat is) az Azure Active Directory naplózási naplóiban. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Az azure Active Directoryban olvassa el az devices.bitLockerRecoveryKeys tulajdonságot. |
| microsoft.directory/policies/conditionalAccess/basic/read | A policies.conditionalAccess tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.directory/signInReports/allProperties/read | Olvassa el az összes tulajdonságot (beleértve a kiemelt tulajdonságokat is) az Azure Active Directoryban a signInReports szolgáltatásban. |
| microsoft.aad.identityProtection/allEntities/read | Olvassa el a microsoft.aad.identityProtection összes erőforrását. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Olvassa el a microsoft.aad.privilegedIdentityManagement összes erőforrását. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.protectionCenter/allEntities/read | Olvassa el az Office 365 Védelmi központ minden aspektusát. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |

### <a name="service-support-administrator-permissions"></a>Szolgáltatástámogatási rendszergazdai engedélyek

Elolvashatja a szolgáltatás állapotával kapcsolatos információkat, és kezelheti a támogatási jegyeket.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |

### <a name="sharepoint-service-administrator-permissions"></a>SharePoint-szolgáltatásrendszergazda inek

A SharePoint-szolgáltatás minden aspektusát kezelheti.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Frissítse a groups.unified tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/unified/basic/update | Az Office 365 Csoportok alapvető tulajdonságainak frissítése. |
| microsoft.directory/groups/unified/create | Hozza létre az Office 365-csoportokat. |
| microsoft.directory/groups/unified/delete | Az Office 365-csoportok törlése. |
| microsoft.directory/groups/unified/members/update | Office 365-csoportok tagságának frissítése. |
| microsoft.directory/groups/unified/owners/update | Az Office 365-csoportok tulajdonjogának frissítése. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.sharepoint/allEntities/allTasks | A microsoft.office365.sharepoint webhelyen létrehozhatja és törölheti az összes erőforrást, valamint elolvashatja és frissítheti a szabványos tulajdonságokat. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.usageReports/allEntities/read    | Olvassa el az Office 365 használati jelentéseit. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |

### <a name="teams-communications-administrator-permissions"></a>Teams kommunikációs rendszergazdai engedélyei

A Microsoft Teams szolgáltatáson belül kezelheti a hívásokat és az értekezleteket.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.usageReports/allEntities/read | Olvassa el az Office 365 használati jelentéseit. |

### <a name="teams-communications-support-engineer-permissions"></a>Teams kommunikációs támogatási mérnöki engedélyei

A Teams en belüli kommunikációs problémák at speciális eszközökkel háríthatja el.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |

### <a name="teams-communications-support-specialist-permissions"></a>Teams kommunikációs támogatási szakemberének engedélyei

Az alapvető eszközök segítségével elháríthatja a Teamsen belüli kommunikációs problémákat.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |

### <a name="teams-service-administrator-permissions"></a>Teams-szolgáltatás rendszergazdai engedélyei

Kezelheti a Microsoft Teams szolgáltatást.

> [!NOTE]
> Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információt a fenti szerepkörleírásban talál.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/groups/hiddenMembers/read | Olvassa el a groups.hiddenMembers tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Frissítse a groups.unified tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/unified/basic/update | Az Office 365 Csoportok alapvető tulajdonságainak frissítése. |
| microsoft.directory/groups/unified/create | Hozza létre az Office 365-csoportokat. |
| microsoft.directory/groups/unified/delete | Az Office 365-csoportok törlése. |
| microsoft.directory/groups/unified/members/update | Office 365-csoportok tagságának frissítése. |
| microsoft.directory/groups/unified/owners/update | Az Office 365-csoportok tulajdonjogának frissítése. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.usageReports/allEntities/read | Olvassa el az Office 365 használati jelentéseit. |

### <a name="user-administrator-permissions"></a>Felhasználói rendszergazdai engedélyek
A felhasználók és csoportok minden aspektusát kezelheti, beleértve a jelszavak alaphelyzetbe állítását a korlátozott rendszergazdák számára.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/appRoleAssignments/create | Hozzon létre appRoleAssignments az Azure Active Directoryban. |
| microsoft.directory/appRoleAssignments/delete | Törölje az appRoleAssignments-eket az Azure Active Directoryban. |
| microsoft.directory/appRoleAssignments/update | Frissítse az appRoleAssignments-eket az Azure Active Directoryban. |
| microsoft.directory/contacts/basic/update | Az Azure Active Directoryban lévő névjegyek alapvető tulajdonságainak frissítése. |
| microsoft.directory/contacts/create | Hozzon létre névjegyeket az Azure Active Directoryban. |
| microsoft.directory/contacts/delete | Névjegyek törlése az Azure Active Directoryban. |
| microsoft.directory/groups/appRoleAssignments/update | Frissítse a groups.appRoleAssignments tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/basic/update | Az Azure Active Directory csoportok alapvető tulajdonságainak frissítése. |
| microsoft.directory/groups/create | Hozzon létre csoportokat az Azure Active Directoryban. |
| microsoft.directory/groups/createAsOwner | Hozzon létre csoportokat az Azure Active Directoryban. A létrehozó lesz az első tulajdonos, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumkvótájába. |
| microsoft.directory/groups/delete | Csoportok törlése az Azure Active Directoryban. |
| microsoft.directory/groups/hiddenMembers/read | Olvassa el a groups.hiddenMembers tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/members/update | Frissítse a groups.members tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/owners/update | Frissítse a groups.owners tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/groups/restore | Csoportok visszaállítása az Azure Active Directoryban. |
| microsoft.directory/groups/settings/update | Frissítse a groups.settings tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/users/assignLicense | Az Azure Active Directory felhasználóinak licenceit kezelheti. |
| microsoft.directory/users/basic/update | Az Azure Active Directory felhasználóinak alapvető tulajdonságainak frissítése. |
| microsoft.directory/users/create | Felhasználók létrehozása az Azure Active Directoryban. |
| microsoft.directory/users/delete | Felhasználók törlése az Azure Active Directoryban. |
| microsoft.directory/users/invalidateAllRefreshTokens | Érvényteleníti az összes felhasználói frissítési jogkivonatot az Azure Active Directoryban. |
| microsoft.directory/users/manager/update | Frissítse a users.manager tulajdonságot az Azure Active Directoryban. |
| microsoft.directory/users/password/update | Az Azure Active Directory összes felhasználójának jelszavát frissítse. További részleteket az online dokumentációban talál. |
| microsoft.directory/users/restore | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.directory/users/userPrincipalName/update | Frissítse a users.userPrincipalName tulajdonságot az Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/alapszintű/olvasott | A microsoft.office365.webPortal webhelyen az összes erőforrás alapvető tulajdonságait olvassa el. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Szolgáltatásállapot ának olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 támogatási jegyek létrehozása és kezelése. |

## <a name="role-template-ids"></a>Szerepkörsablon azonosítói

A szerepkörsablon-azonosítókat elsősorban a Microsoft Graph API vagy a PowerShell-felhasználók használják.

Grafikon megjelenítéseNév | Az Azure Portal megjelenítendő neve | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
alkalmazás-rendszergazda | Alkalmazás-rendszergazda | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Alkalmazásfejlesztő | Alkalmazásfejlesztő | CF1C38E5-3621-4004-A7CB-879624DCED7C
Hitelesítési rendszergazda | Hitelesítési rendszergazda | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure DevOps-rendszergazda | Azure DevOps-rendszergazda | e3973bdf-4987-49ae-837a-ba8e231c7286
Azure információvédelmi rendszergazda | Az Azure information protection rendszergazdája | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C felhasználói folyamat rendszergazdája | B2C felhasználói folyamat rendszergazdája | 6e591065-9bad-43ed-90f3-e9424366d2f0
B2C felhasználói folyamat attribútumának rendszergazdája | B2C felhasználói folyamat attribútumának rendszergazdája | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
B2C IEF kulcskészlet-rendszergazda | B2C IEF kulcskészlet-rendszergazda | aaf43236-0c0d-4d5f-883a-6955382ac081
B2C IEF házirend-rendszergazda | B2C IEF házirend-rendszergazda | 3edaf663-341e-4475-9f94-5c398ef6c070
Számlázási rendszergazda | Számlázási rendszergazda | b0f54661-2d74-4c50-afa3-1ec803f12efe
Felhőalkalmazás-rendszergazda | Felhőalkalmazás-rendszergazda | 158c047a-c907-4556-b7ef-446551a6b5f7
Felhőeszköz-rendszergazda | Felhőeszköz-rendszergazda | 7698a772-787b-4ac8-901f-60d6b08affd2
Vállalati rendszergazda | Globális rendszergazda | 62e90394-69f5-4237-9190-012177145e10
Megfelelőségi rendszergazda | Megfelelőségi rendszergazda | 17315797-102d-40b4-93e0-432062caca18
Megfelelőségi adatok rendszergazdája | Megfelelőségi adatok rendszergazdája | e6d1a23a-da11-4be4-9570-befc86d067a7
Feltételes hozzáférés-rendszergazda | Feltételes hozzáférés rendszergazdája | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
CRM szolgáltatás rendszergazdája | A Dynamics 365 rendszergazdája | 44367163-eba1-44c3-98af-f5787879f96a
Ügyfél lockbox hozzáférési jóváhagyója | Ügyfélszéf-hozzáférés jóváhagyója | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Asztali elemzési rendszergazda | Asztali elemzési rendszergazda | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Eszközrendszergazdák | Eszközrendszergazdák | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Eszközillesztés | Eszközillesztés | 9c094953-4995-41c8-84c8-3ebb9bb32c93f
Eszközkezelők | Eszközkezelők | 2b499bcd-da44-4968-8aec-78e1674fa64d
Eszközfelhasználók | Eszközfelhasználók | d405c6df-0af8-4e3b-95e4-4d06e542189e
Címtárolvasók | Címtárolvasók | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Címtár-szinkronizálási fiókok | Címtár-szinkronizálási fiókok | d29b2b05-8046-44ba-8758-1e26182fcf32
Könyvtár írók | Címtár írók | 9360feb5-f418-4baa-8175-e2a00bac4301
Exchange-szolgáltatás rendszergazdája | Exchange-rendszergazda | 29232cdf-9323-42fd-ade2-1d097af3e4de
Külső identitásszolgáltató rendszergazdája | Külső identitásszolgáltató rendszergazdája | be2f45a1-457d-42af-a067-6ec1fa63bc45
Globális olvasó | Globális olvasó | f2ef992c-3afb-46b9-b7cf-a126ee74c451
Csoportok rendszergazdája | Csoportok rendszergazdája | fdd7a751-b60b-444a-984c-02652fe8fa1c 
Vendég meghívó | Vendég meghívó | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Ügyfélszolgálati rendszergazda | Ügyfélszolgálati rendszergazda | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Intune szolgáltatás rendszergazdája | Intune-rendszergazda | 3a2c62db-5318-420d-8d74-23affee5d9d5
Kaizala adminisztrátor | Kaizala rendszergazda | 74ef975b-6605-40af-a5d2-b9539d836353
Licencrendszergazda | Licencrendszergazda | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Lync-szolgáltatás rendszergazdája | Skype Vállalati verzió-rendszergazda | 75941009-915a-4869-abe7-691bff18279e
Üzenetközpont adatvédelmi olvasója | Üzenetközpont adatvédelmi olvasója | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Üzenetközpont olvasója | Üzenetközpont-olvasó | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Office-alkalmazások rendszergazdája | Office-alkalmazások rendszergazdája | 2b745bdf-0803-4d80-aa65-822c4493daac
Partner tier1 támogatás | Partnerszintű támogatás | 4ba39ca4-527c-499a-b93d-d9b492c50246
Partner tier2 támogatás | Partnerszintű 2 szintű támogatás | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Jelszó-rendszergazda | Jelszókezelő | 966707d0-3269-4727-9be2-8c3a10f19b9d
Power BI-szolgáltatásadminisztrátor | Power BI-rendszergazda | a9ea8996-122f-4c74-9520-8edcd192826c
A Power Platform rendszergazdája | Az Energiaplatform rendszergazdája | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
Kiemelt hitelesítési rendszergazda | Kiemelt hitelesítési rendszergazda | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Kiemelt szerepkör-rendszergazda | Kiemelt szerepkör-rendszergazda | e8611ab8-c189-46e8-94e1-60213ab1f814
Jelentések olvasó | Jelentések olvasó | 4a5d8f65-41da-4de4-8968-e035b65339cf
Keresés rendszergazdája | Keresés rendszergazdája | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Keresés szerkesztő | Keresés szerkesztő | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Biztonsági rendszergazda | Biztonsági rendszergazda | 194ae4cb-b126-40b2-bd5b-6091b380977d
Biztonsági operátor | Biztonsági operátor | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Biztonsági olvasó | Biztonsági olvasó | 5d6b6bb7-de71-4623-b4af-96380a352509
Szolgáltatástámogatási rendszergazda | Szolgáltatás-rendszergazda | f023fd81-a637-4b56-95fd-791ac0226033
SharePoint-szolgáltatás rendszergazdája | SharePoint-rendszergazda | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Teams kommunikációs rendszergazdája | Teams kommunikációs rendszergazdája | baf37b3a-610e-45da-9e62-d9d1e5e8914b
A Teams kommunikációs támogatási mérnöke | A Teams kommunikációs támogatási mérnöke | f70938a0-fc10-4177-9e90-2178f8765737
Teams kommunikációs támogatási specialistája | Teams kommunikációs támogatási specialistája | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Teams szolgáltatás rendszergazdája | Teams szolgáltatás rendszergazdája | 69091246-20e8-4a56-aa4d-066075b2a7a8
Felhasználó | Felhasználó | a0b1b346-4d3e-4e8b-98f8-753987be4970
Felhasználói fiók rendszergazdája | Rendszergazda | fe930be7-5e62-47db-91af-98c3a49a38b1
Munkahelyi eszközillesztés | Munkahelyi eszközillesztés | c34f683f-4d5a-4403-affd-6615e00e3a7f

## <a name="deprecated-roles"></a>Elavult szerepkörök

A következő szerepköröket nem szabad használni. Elavultak, és a jövőben el lesznek távolítva az Azure AD-ből.

* AdHoc licencadminisztrátor
* Eszközillesztés
* Eszközkezelők
* Eszközfelhasználók
* E-mail ellenőrzött felhasználó készítője
* Postaláda-rendszergazda
* Munkahelyi eszközillesztés

## <a name="roles-not-shown-in-the-portal"></a>A portálon nem látható szerepkörök

Nem minden PowerShell vagy MS Graph API által visszaadott szerepkör látható az Azure Portalon. Az alábbi táblázat ezeket a különbségeket rendezi.

API-név | Az Azure Portal neve | Megjegyzések
-------- | ------------------- | -------------
Vállalati rendszergazda | Globális rendszergazda | [Név megváltozott a jobb egyértelműség érdekében](directory-assign-admin-roles.md#role-template-ids)
CRM szolgáltatás rendszergazdája | A Dynamics 365 rendszergazdája | [A jelenlegi termékmárkaismertet tükrözi](directory-assign-admin-roles.md#role-template-ids)
Eszközillesztés | Elavult | [Elavult szerepkörök dokumentációja](directory-assign-admin-roles.md#deprecated-roles)
Eszközkezelők | Elavult | [Elavult szerepkörök dokumentációja](directory-assign-admin-roles.md#deprecated-roles)
Eszközfelhasználók | Elavult | [Elavult szerepkörök dokumentációja](directory-assign-admin-roles.md#deprecated-roles)
Címtár-szinkronizálási fiókok | Nem jelenik meg, mert nem szabad használni | [Címtár-szinkronizálási fiókok dokumentációja](directory-assign-admin-roles.md#directory-synchronization-accounts)
Könyvtár írók | Nem jelenik meg, mert nem szabad használni | [Címtárírók dokumentációja](directory-assign-admin-roles.md#directory-writers)
Vendégfelhasználó | Nem jelenik meg, mert nem használható  | NA
Lync-szolgáltatás rendszergazdája | Skype Vállalati verzió-rendszergazda | [A jelenlegi termékmárkaismertet tükrözi](directory-assign-admin-roles.md#role-template-ids)
Partner szintű 1. | Nem jelenik meg, mert nem szabad használni | [Partner Tier1 támogatási dokumentáció](directory-assign-admin-roles.md#partner-tier1-support)
Partner2 szintű támogatás | Nem jelenik meg, mert nem szabad használni | [Partner Tier2 támogatási dokumentáció](directory-assign-admin-roles.md#partner-tier2-support)
Nyomtató-rendszergazda | Megoldás folyamatban | Megoldás folyamatban
Nyomtató technikus | Megoldás folyamatban | Megoldás folyamatban
Korlátozott vendégfelhasználó | Nem jelenik meg, mert nem használható | NA
Felhasználó | Nem jelenik meg, mert nem használható | NA
Munkahelyi eszközillesztés | Elavult | [Elavult szerepkörök dokumentációja](directory-assign-admin-roles.md#deprecated-roles)

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni arról, hogyan rendelhet hozzá egy felhasználót egy Azure-előfizetés rendszergazdájaként, olvassa el [a Hozzáférés kezelése Azure-szerepkörökkel (Azure RBAC) című témakört.](../../role-based-access-control/role-assignments-portal.md)
* Ha többet szeretne megtudni arról, hogyan szabályozza az erőforrás-hozzáférést a Microsoft Azure-ban, [olvassa el A különböző szerepkörök ismertetése című témakört.](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* További információk az Azure Active Directory és az Azure-előfizetés kapcsolatáról: [Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directoryhoz?](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
