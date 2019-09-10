---
title: Rendszergazdai szerepkör leírása és engedélyei – Azure Active Directory | Microsoft Docs
description: A rendszergazdai szerepkör felhasználók hozzáadására, rendszergazdai szerepkörök hozzárendelésére, felhasználói jelszavak visszaállítására, felhasználói licencek kezelésére vagy tartományok felügyeletére használható.
services: active-directory
author: curtand
manager: mtillman
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 09/05/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54e0648ff9aab12ad6a579fc7f6e45b98a9d6d03
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861175"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Rendszergazdai szerepkör engedélyei Azure Active Directory

A Azure Active Directory (Azure AD) használatával korlátozott rendszergazdákat jelölhet ki a kevésbé Kiemelt szerepkörökben lévő Identity Tasks-feladatok kezelésére. A rendszergazdák olyan célokra rendelhetők hozzá, mint a felhasználók hozzáadása vagy módosítása, a rendszergazdai szerepkörök hozzárendelése, a felhasználói jelszavak alaphelyzetbe állítása, a felhasználói licencek kezelése és a tartománynevek kezelése. Az alapértelmezett felhasználói engedélyeket csak az Azure AD felhasználói beállításaiban lehet módosítani.

## <a name="limit-the-use-of-global-administrator"></a>Globális rendszergazda használatának korlátozása

A globális rendszergazdai szerepkörhöz hozzárendelt felhasználók elolvashatják és módosíthatják az Azure AD-szervezet minden felügyeleti beállítását. Alapértelmezés szerint az Azure-előfizetésre regisztráló személy az Azure AD-szervezet globális rendszergazdai szerepköréhez van rendelve. Csak a globális rendszergazdák és a Kiemelt szerepkörű rendszergazdák delegálják a rendszergazdai szerepköröket. Az üzleti kockázat csökkentése érdekében javasoljuk, hogy ezt a szerepkört a szervezete legkevesebb lehetséges személyéhez rendelje.

Ajánlott eljárásként Azt javasoljuk, hogy a szerepkört a szervezetnél kevesebb mint 5 személyhez rendelje hozzá. Ha több mint öt felhasználó van hozzárendelve a globális rendszergazdai szerepkörhöz a szervezetében, a következő módokon csökkentheti annak használatát.

### <a name="find-the-role-you-need"></a>Keresse meg a szükséges szerepkört

Ha nem szeretné megkeresni a sok szerepkörből álló listáról származó szerepkört, az Azure AD a szerepkör-kategóriák alapján jeleníti meg a szerepkörök részhalmazait. Tekintse meg az [Azure ad-szerepkörök és-rendszergazdák](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) új **típusú** szűrőjét, hogy csak a kiválasztott típus szerepkörei jelenjenek meg.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Már létezik olyan szerepkör, amely nem létezett a globális rendszergazdai szerepkör hozzárendelésekor

Lehetséges, hogy egy szerepkört vagy szerepkört adtak hozzá az Azure AD-hez, amely olyan részletesebb engedélyeket biztosít, amelyek nem voltak elérhetők, amikor a felhasználók a globális rendszergazdához emeltek. Az idő múlásával további szerepköröket vezetünk be, amelyek csak a globális rendszergazdai szerepkörrel ellátott feladatokat hajtják végre. Ezek a következő [elérhető szerepkörökben](#available-roles)láthatók.

## <a name="assign-or-remove-administrator-roles"></a>Rendszergazdai szerepkörök kiosztása vagy eltávolítása

Ha meg szeretné tudni, hogyan rendelhet hozzá rendszergazdai szerepköröket egy felhasználóhoz Azure Active Directoryban, tekintse meg [a rendszergazdai szerepkörök megtekintése és társítása a Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Elérhető szerepkörök

A következő rendszergazdai szerepkörök érhetők el:

### <a name="application-administratorapplication-administrator-permissions"></a>[Alkalmazás-rendszergazda](#application-administrator-permissions)

Az ebben a szerepkörben lévő felhasználók a vállalati alkalmazások, az alkalmazások regisztrációi és az alkalmazásproxy-beállítások összes aspektusát hozhatják létre és kezelhetik. Ez a szerepkör lehetővé teszi a delegált engedélyekhez való hozzájárulást, valamint az Microsoft Graph és az Azure AD Graph alkalmazás engedélyeinek kizárását is. Az ehhez a szerepkörhöz hozzárendelt felhasználók nem lesznek hozzáadva tulajdonosként új alkalmazás-regisztrációk vagy vállalati alkalmazások létrehozásakor.

> [!IMPORTANT]
> Ez a szerepkör lehetővé teszi az alkalmazás hitelesítő adatainak kezelését. Az ehhez a szerepkörhöz hozzárendelt felhasználók hitelesítő adatokat adhatnak hozzá egy alkalmazáshoz, és ezekkel a hitelesítő adatokkal megszemélyesítheti az alkalmazás identitását. Ha az alkalmazás identitása hozzáférést kapott a Azure Active Directoryhoz, például felhasználói vagy más objektumok létrehozására vagy frissítésére, akkor a szerepkörhöz hozzárendelt felhasználó elvégezheti ezeket a műveleteket az alkalmazás megszemélyesítése közben. Előfordulhat, hogy az alkalmazás identitásának megszemélyesítése megemelő jogosultságot jelent a felhasználó számára az Azure AD-beli szerepkör-hozzárendelésein keresztül. Fontos tisztában lenni azzal, hogy egy felhasználó az alkalmazás-rendszergazdai szerepkörhöz való hozzárendelésével megszemélyesítheti az alkalmazás identitását.

### <a name="application-developerapplication-developer-permissions"></a>[Alkalmazásfejlesztő](#application-developer-permissions)

Az ebben a szerepkörben lévő felhasználók létrehozhatnak alkalmazás-regisztrációkat, ha a "felhasználók regisztrálhatnak alkalmazásokat" beállítás a nem értékre van állítva. Ez a szerepkör lehetővé teszi az engedély megadását a saját nevében, amikor a "felhasználók beleegyeznek az alkalmazásokkal a vállalati adatok eléréséhez" beállítás értéke nem. Az ehhez a szerepkörhöz hozzárendelt felhasználók új alkalmazás-regisztrációk vagy vállalati alkalmazások létrehozásakor tulajdonosként lesznek hozzáadva.

### <a name="authentication-administratorauthentication-administrator-permissions"></a>[Hitelesítés rendszergazdája](#authentication-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók a jelszóval nem rendelkező hitelesítő adatokat állíthatnak be vagy állíthatnak vissza, és frissíthetik az összes felhasználó jelszavát. A hitelesítési rendszergazdák megkövetelhetik, hogy a felhasználók újra regisztráljanak a meglévő, nem jelszóval nem rendelkező hitelesítő adatokkal (például MFA vagy pont), és visszavonják **az MFA**-t az eszközön, amely a nem rendszergazdák vagy a felhasználók következő bejelentkezésére kéri az MFA-t csak a következő szerepköröket rendeli hozzá:

* Hitelesítés rendszergazdája
* Címtárolvasó
* Vendég meghívója
* Üzenetközpont-olvasó
* Jelentésolvasó

A hitelesítési rendszergazdai szerepkör jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> Az ezzel a szerepkörrel rendelkező felhasználók megváltoztathatják azoknak a személyeknek a hitelesítő adatait, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz, illetve a Azure Active Directoryon belüli és kívüli kritikus konfigurációhoz. A felhasználók hitelesítő adatainak módosítása azt jelentheti, hogy a felhasználó identitását és engedélyeit feltételezi. Példa:

* Az alkalmazás regisztrálása és a vállalati alkalmazások tulajdonosai, akik kezelhetik a saját alkalmazások hitelesítő adatait. Ezek az alkalmazások jogosultsági szintű engedélyekkel rendelkezhetnek az Azure AD-ben, és másutt nem kapják meg a hitelesítési rendszergazdákat. Ezen az elérési úton a hitelesítési rendszergazda feltételezheti az alkalmazás tulajdonosának identitását, majd az alkalmazás hitelesítő adatainak frissítésével továbbra is feltételezheti a Kiemelt alkalmazások identitását.
* Az Azure-előfizetések tulajdonosai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz vagy az Azure-beli kritikus konfigurációhoz.
* Biztonsági csoport és Office 365 csoport tulajdonosai, akik kezelhetik a csoporttagság kezelését. Ezek a csoportok hozzáférést biztosíthatnak a bizalmas vagy magánjellegű információkhoz, illetve a kritikus konfigurációhoz az Azure AD-ben és máshol.
* Az Azure AD-n kívül más szolgáltatásokban, például az Exchange Online-ban, az Office biztonsági és megfelelőségi központban és a humán erőforrás-rendszerekben található rendszergazdák.
* Nem rendszergazdák, például vezetők, jogi tanácsadás és emberi erőforrások alkalmazottai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz.

### <a name="azure-devops-administratorazure-devops-administrator-permissions"></a>[Azure DevOps-rendszergazda](#azure-devops-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók kezelhetik az Azure DevOps-szabályzatot, hogy az új Azure DevOps-szervezet létrehozása konfigurálható felhasználók/HRE csoportokba legyen korlátozva. Az ebben a szerepkörben lévő felhasználók bármely olyan Azure DevOps-szervezeten keresztül kezelhetik ezt a szabályzatot, amely a vállalat Azure AD-beli biztonsági mentését végzi.

A jövőben az összes vállalati Azure DevOps-szabályzatot a felhasználók felügyelik majd a szerepkörben.

### <a name="azure-information-protection-administratorazure-information-protection-administrator-permissions"></a>[Azure Information Protection rendszergazda](#azure-information-protection-administrator-permissions)

Az ehhez a szerepkörhöz tartozó felhasználók minden engedéllyel rendelkeznek a Azure Information Protection szolgáltatásban. Ez a szerepkör lehetővé teszi a címkék konfigurálását a Azure Information Protection házirend számára, a védelmi sablonok kezelését és a védelem aktiválását. Ez a szerepkör nem biztosít engedélyeket az Identity Protection Centerben, az Privileged Identity Management, az Office 365 Service Health vagy az Office 365 Biztonsági és megfelelőségi központ figyeléséhez.

### <a name="b2c-user-flow-administratorb2c-user-flow-administrator-permissions"></a>[B2C felhasználói folyamat rendszergazdája](#b2c-user-flow-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók B2C-Felhasználókövetés (más néven "beépített" szabályzatokat) hozhatnak létre és kezelhetnek a Azure Portalban. A felhasználói folyamatok létrehozásával vagy szerkesztésével ezek a felhasználók módosíthatják a felhasználói élmény HTML/CSS/JavaScript tartalmát, módosíthatják a felhasználói folyamatok MFA-követelményeit, megváltoztathatják a jogkivonatokat, és módosíthatják a munkamenet beállításait a bérlő összes házirendje számára. Másfelől azonban ez a szerepkör nem teszi lehetővé a felhasználói adat áttekintését, illetve a bérlői sémában található attribútumok módosítását. Az Identity Experience Framework (más néven egyéni) házirendek változásai a szerepkör hatókörén kívül esnek.

### <a name="b2c-user-flow-attribute-administratorb2c-user-flow-attribute-administrator-permissions"></a>[B2C felhasználói folyamat attribútumának rendszergazdája](#b2c-user-flow-attribute-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók hozzáadhatnak vagy törölhetnek egyéni attribútumokat a bérlő összes felhasználói folyamata számára. Így az ezzel a szerepkörrel rendelkező felhasználók megváltoztathatják vagy hozzáadhatják az új elemeket a végfelhasználói sémához, és befolyásolhatják az összes felhasználói folyamat viselkedését, és közvetetten azt eredményezik, hogy a végfelhasználók milyen adatokat igényelhetnek, és végül jogcímeket küldenek az alkalmazásoknak. Ez a szerepkör nem szerkesztheti a felhasználói folyamatokat.

### <a name="b2c-ief-keyset-administratorb2c-ief-keyset-administrator-permissions"></a>[B2C IEF kulcskészlet rendszergazdája](#b2c-ief-keyset-administrator-permissions)

A felhasználók létrehozhatják és kezelhetik a jogkivonat-titkosításhoz, a jogkivonat-aláírásokhoz és a jogcím titkosításához és visszafejtéséhez szükséges szabályzatokat és titkos kulcsokat. Ha új kulcsokat ad hozzá a meglévő kulcstárolóhoz, ez a korlátozott rendszergazda szükség szerint átválthatja a titkokat a meglévő alkalmazások befolyásolása nélkül. A felhasználók a létrehozásuk után is láthatják a titkok teljes tartalmát és a lejárat dátumát.

> [!IMPORTANT]
> Ez egy bizalmas szerepkör. A kulcskészlet rendszergazdai szerepkörét körültekintően kell naplózni, és körültekintően kell kiosztani az üzem előtti és az éles környezetben.

### <a name="b2c-ief-policy-administratorb2c-ief-policy-administrator-permissions"></a>[B2C IEF házirend rendszergazdája](#b2c-ief-policy-administrator-permissions)

Az ebben a szerepkörben lévő felhasználók létrehozhatnak, olvashatnak, frissíthetnek és törölhetnek minden egyéni szabályzatot a Azure AD B2Cban, így teljes körűen szabályozható a megfelelő Azure AD B2C-bérlő identitás-ellenőrzési keretrendszere. A szabályzatok szerkesztésével a felhasználó közvetlen kapcsolatot hozhat létre külső identitás-szolgáltatókkal, módosíthatja a címtár-sémát, módosíthatja az összes felhasználó felé irányuló tartalmat (HTML, CSS, JavaScript), módosíthatja a követelményeket a hitelesítés befejezéséhez, új felhasználók létrehozásához, küldéshez a külső rendszerekre vonatkozó felhasználói adatok, beleértve a teljes áttelepítést, és az összes felhasználói adat szerkesztése, beleértve a bizalmas mezőket, például a jelszavakat és a telefonszámokat. Ezzel ellentétben ez a szerepkör nem változtathatja meg a titkosítási kulcsokat, vagy szerkesztheti a bérlőn való összevonáshoz használt titkokat.

> [!IMPORTANT]
> A B2 IEF szabályzat rendszergazdája rendkívül kényes szerepkör, amelyet az élesben lévő bérlők számára nagyon korlátozott mértékben kell hozzárendelni. Ezeknek a felhasználóknak a tevékenységeit alaposan meg kell vizsgálni, különösen az éles üzemben lévő bérlők esetében.

### <a name="billing-administratorbilling-administrator-permissions"></a>[Számlázási rendszergazda](#billing-administrator-permissions)

Megvásárolja, kezeli az előfizetéseket, kezeli a támogatási jegyeket, és figyeli a szolgáltatás állapotát.

### <a name="cloud-application-administratorcloud-application-administrator-permissions"></a>[Felhőalkalmazás-rendszergazda](#cloud-application-administrator-permissions)

Az ebben a szerepkörben lévő felhasználók ugyanazok az engedélyek, mint az alkalmazás-rendszergazda szerepkör, az alkalmazásproxy felügyeletének lehetősége nélkül. Ez a szerepkör lehetővé teszi a vállalati alkalmazások és az alkalmazások regisztrálásának minden aspektusának létrehozását és felügyeletét. Ez a szerepkör lehetővé teszi a delegált engedélyekhez való hozzájárulást, valamint az Microsoft Graph és az Azure AD Graph alkalmazás engedélyeinek kizárását is. Az ehhez a szerepkörhöz hozzárendelt felhasználók nem lesznek hozzáadva tulajdonosként új alkalmazás-regisztrációk vagy vállalati alkalmazások létrehozásakor.

> [!IMPORTANT]
> Ez a szerepkör lehetővé teszi az alkalmazás hitelesítő adatainak kezelését. Az ehhez a szerepkörhöz hozzárendelt felhasználók hitelesítő adatokat adhatnak hozzá egy alkalmazáshoz, és ezekkel a hitelesítő adatokkal megszemélyesítheti az alkalmazás identitását. Ha az alkalmazás identitása hozzáférést kapott a Azure Active Directoryhoz, például felhasználói vagy más objektumok létrehozására vagy frissítésére, akkor a szerepkörhöz hozzárendelt felhasználó elvégezheti ezeket a műveleteket az alkalmazás megszemélyesítése közben. Előfordulhat, hogy az alkalmazás identitásának megszemélyesítése megemelő jogosultságot jelent a felhasználó számára az Azure AD-beli szerepkör-hozzárendelésein keresztül. Fontos tisztában lenni azzal, hogy egy felhasználó a Cloud Application Administrator szerepkörhöz való hozzárendelésével lehetővé válik az alkalmazás identitásának megszemélyesítése.

### <a name="cloud-device-administratorcloud-device-administrator-permissions"></a>[Felhőalapú eszköz rendszergazdája](#cloud-device-administrator-permissions)

A szerepkörben lévő felhasználók engedélyezheti, letilthatja és törölheti az eszközöket az Azure AD-ben, és beolvashatja a Windows 10 BitLocker-kulcsokat (ha vannak) a Azure Portal. A szerepkör nem biztosít engedélyeket az eszköz egyéb tulajdonságainak kezeléséhez.

### <a name="compliance-administratorcompliance-administrator-permissions"></a>[Megfelelőségi rendszergazda](#compliance-administrator-permissions)

Az ehhez a szerepkörhöz tartozó felhasználók jogosultak a megfelelőséggel kapcsolatos szolgáltatások kezelésére a Microsoft 365 megfelelőségi központban, Microsoft 365 felügyeleti központban, az Azure-ban és az Office 365 Biztonsági és megfelelőségi központ. A engedményesek az Exchange felügyeleti központban és a Teams & a Skype vállalati felügyeleti központokban is kezelheti az összes funkciót, és támogatási jegyeket hozhat létre az Azure-hoz és a Microsoft 365hoz. További információk az [Office 365 rendszergazdai szerepköreivel kapcsolatban](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)érhetők el.

A | Elvégezhető
----- | ----------
[Microsoft 365 megfelelőségi központ](https://protection.office.com) | A szervezet adatainak védelme és kezelése Microsoft 365 szolgáltatások között<br>Megfelelőségi riasztások kezelése
[Megfelelőség-kezelő](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | A szervezet szabályozási megfelelőségi tevékenységeinek nyomon követése, kiosztása és ellenőrzése
[Office 365 Biztonsági és megfelelőségi központ](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Az adatszabályozás kezelése<br>Jogi és adatvizsgálati műveletek végrehajtása<br>Az adattulajdonosi kérelem kezelése
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Az összes Intune-naplózási adatbázis megtekintése
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Csak olvasási jogosultsággal rendelkezik, és képes kezelni a riasztásokat<br>Házirendeket hozhat létre és módosíthat, valamint engedélyezheti a fájlok irányítási műveleteit<br> Megtekintheti az összes beépített jelentést adatkezelés

### <a name="compliance-data-administratorcompliance-data-administrator-permissions"></a>[Megfelelőségi adatkezelő](#compliance-data-administrator-permissions)

Az ehhez a szerepkörhöz tartozó felhasználók rendelkeznek a Microsoft 365 megfelelőségi központban, Microsoft 365 felügyeleti központban és az Azure-ban lévő adatkövetési jogosultságokkal. A felhasználók az Exchange felügyeleti központban, a megfelelőség-kezelőben és a Teams & Skype vállalati felügyeleti központban is nyomon követhetik a megfelelőségi és támogatási jegyeket az Azure-hoz és Microsoft 365hoz.

A | Elvégezhető
----- | ----------
[Microsoft 365 megfelelőségi központ](https://protection.office.com) | Megfelelőséggel kapcsolatos szabályzatok monitorozása Microsoft 365 szolgáltatások között<br>Megfelelőségi riasztások kezelése
[Megfelelőség-kezelő](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | A szervezet szabályozási megfelelőségi tevékenységeinek nyomon követése, kiosztása és ellenőrzése
[Office 365 Biztonsági és megfelelőségi központ](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Az adatszabályozás kezelése<br>Jogi és adatvizsgálati műveletek végrehajtása<br>Az adattulajdonosi kérelem kezelése
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Az összes Intune-naplózási adatbázis megtekintése
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Csak olvasási jogosultsággal rendelkezik, és képes kezelni a riasztásokat<br>Házirendeket hozhat létre és módosíthat, valamint engedélyezheti a fájlok irányítási műveleteit<br> Megtekintheti az összes beépített jelentést adatkezelés

### <a name="conditional-access-administratorconditional-access-administrator-permissions"></a>[Feltételes hozzáférésű rendszergazda](#conditional-access-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók kezelhetik Azure Active Directory feltételes hozzáférési beállításokat.
> [!NOTE]
> Az Exchange ActiveSync feltételes hozzáférési szabályzatának Azure-beli üzembe helyezéséhez a felhasználónak globális rendszergazdának is kell lennie.

### <a name="customer-lockbox-access-approvercustomer-lockbox-access-approver-permissions"></a>[Ügyfélszéf hozzáférési jóváhagyó](#customer-lockbox-access-approver-permissions)

A szervezet [Ügyfélszéf-kérelmeit](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) kezeli. E-mail-értesítéseket kapnak Ügyfélszéf kérelmekről, és a Microsoft 365 felügyeleti központban hagyhatják jóvá és tagadhatják meg a kérelmeket. Emellett be-vagy kikapcsolhatja a Ügyfélszéf funkciót. Csak a globális rendszergazdák állíthatják alaphelyzetbe a szerepkörhöz rendelt személyek jelszavát.

### <a name="desktop-analytics-administratordesktop-analytics-administrator-permissions"></a>[Asztali elemzési rendszergazda](#desktop-analytics-administrator-permissions)


Az ebben a szerepkörben lévő felhasználók kezelhetik az asztali elemzési és az Office testreszabási & házirend-szolgáltatásait. Az asztali elemzések esetében ez magában foglalja az eszközök leltárának megtekintését, a központi telepítési tervek létrehozását, a központi telepítés és az állapot megtekintését. Az Office testreszabása & házirend-szolgáltatás esetében ez a szerepkör lehetővé teszi a felhasználóknak az Office-házirendek kezelését.

### <a name="device-administratordevice-administrators-permissions"></a>[Eszköz rendszergazdája](#device-administrators-permissions)

Ez a szerepkör csak az [eszközbeállítások](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)további helyi rendszergazdája számára érhető el. Az ezzel a szerepkörrel rendelkező felhasználók a Azure Active Directoryhoz csatlakozó összes Windows 10-es eszközön a helyi számítógép-Rendszergazdák lesznek. Nem tudja kezelni az eszközök objektumait Azure Active Directoryban.

### <a name="directory-readersdirectory-readers-permissions"></a>[Directory-olvasók](#directory-readers-permissions)

Ez a szerepkör csak olyan örökölt alkalmazásokhoz rendelhető hozzá, amelyek nem támogatják az [engedélyezési keretrendszert](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Ne rendelje hozzá a felhasználókhoz.

### <a name="directory-synchronization-accountsdirectory-synchronization-accounts-permissions"></a>[Címtár-szinkronizálási fiókok](#directory-synchronization-accounts-permissions)

Ne használja. Ezt a szerepkört a rendszer automatikusan hozzárendeli a Azure AD Connect szolgáltatáshoz, és semmilyen más használatra nem javasolt vagy nem támogatott.

### <a name="directory-writersdirectory-writers-permissions"></a>[Címtár-írók](#directory-writers-permissions)

Ez egy örökölt szerepkör, amelyet olyan alkalmazásokhoz kell rendelni, amelyek nem támogatják az [engedélyezési keretrendszert](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Nem rendelhető hozzá egyetlen felhasználóhoz sem.

### <a name="dynamics-365-administrator--crm-administratorcrm-service-administrator-permissions"></a>[Dynamics 365 Administrator/CRM-rendszergazda](#crm-service-administrator-permissions)

Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft Dynamics 365 online-on belül, ha a szolgáltatás megtalálható, valamint a támogatási jegyek felügyeletének és a szolgáltatás állapotának figyelésének lehetősége. További információ: [a szolgáltatás-rendszergazdai szerepkör használata a bérlő kezeléséhez](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> A Microsoft Graph API, az Azure AD Graph API és az Azure AD PowerShell esetében ez a szerepkör "Dynamics 365 szolgáltatás-rendszergazdaként" azonosítható. A [Azure Portal](https://portal.azure.com)a "Dynamics 365 Administrator".

### <a name="exchange-administratorexchange-service-administrator-permissions"></a>[Exchange-rendszergazda](#exchange-service-administrator-permissions)

Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft Exchange Online-ban, ha a szolgáltatás jelen van. Emellett képes az Office 365-csoportok létrehozására és felügyeletére, a támogatási jegyek kezelésére és a szolgáltatás állapotának figyelésére. További információ az [Office 365 rendszergazdai szerepköreiről](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> A Microsoft Graph API, az Azure AD Graph API és az Azure AD PowerShell esetében ez a szerepkör "Exchange szolgáltatás-rendszergazdaként" azonosítható. A [Azure Portal](https://portal.azure.com)az "Exchange Administrator". Az [Exchange felügyeleti központban](https://go.microsoft.com/fwlink/p/?LinkID=529144)az "Exchange Online Administrator".

### <a name="external-identity-provider-administratorexternal-identity-provider-administrator-permissions"></a>[Külső identitás-szolgáltató rendszergazdája](#external-identity-provider-administrator-permissions)

Ez a rendszergazda kezeli az Azure Active Directory bérlők és a külső identitás-szolgáltatók közötti összevonást. Ezzel a szerepkörrel a felhasználók új identitás-szolgáltatókat adhatnak hozzá, és konfigurálhatják az összes rendelkezésre álló beállítást (például a hitelesítési útvonalat, a szolgáltatás AZONOSÍTÓját, a hozzárendelt kulcstárolókat). Ez a felhasználó lehetővé teheti a bérlő számára, hogy megbízzon a külső identitás-szolgáltatóktól érkező hitelesítésekben. A végfelhasználói tapasztalatok eredményének következményei a bérlő típusától függenek:

* Azure Active Directory bérlők alkalmazottai és partnerei számára: A Szövetség (például a Gmail) hozzáadása azonnal hatással lesz az összes olyan vendég meghívóra, amely még nincs beváltva. Lásd: [a Google hozzáadása identitás-szolgáltatóként a B2B vendég felhasználói](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)számára.
* Azure Active Directory B2C bérlők: Az összevonások (például a Facebook vagy egy másik Azure AD-szervezet) hozzáadása nem befolyásolja azonnal a végfelhasználói folyamatokat, amíg az identitás-szolgáltatót nem adja hozzá felhasználói folyamatként (más néven beépített szabályzatnak). Lásd: [Microsoft-fiók konfigurálása identitás](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) -szolgáltatóként egy példához. A felhasználói folyamatok módosításához a "B2C felhasználói folyamat rendszergazdája" korlátozott szerepkörre van szükség.

### <a name="global-administrator--company-administratorcompany-administrator-permissions"></a>[Globális rendszergazda/vállalati rendszergazda](#company-administrator-permissions)

Az ehhez a szerepkörhöz tartozó felhasználók hozzáférhetnek a Azure Active Directory összes felügyeleti funkciójához, valamint olyan szolgáltatásokhoz, amelyek olyan Azure Active Directory identitásokat használnak, mint például a Microsoft 365 Security Center, a Microsoft 365 megfelelőségi központ, az Exchange Online, a SharePoint Online és a Skype vállalati online verzió. A Azure Active Directory bérlőre feliratkozik személy globális rendszergazda lesz. Csak a globális rendszergazdák oszthatnak ki más rendszergazdai szerepköröket. A vállalatnál több globális rendszergazda is lehet. A globális rendszergazdák alaphelyzetbe állíthatják bármely felhasználó és az összes többi rendszergazda jelszavát.

> [!NOTE]
> A Microsoft Graph API, az Azure AD Graph API és az Azure AD PowerShell esetében ez a szerepkör a "vállalati rendszergazda" néven azonosítható. Ez a [Azure Portal](https://portal.azure.com)globális rendszergazdája.
>
>

### <a name="guest-inviterguest-inviter-permissions"></a>[Vendég meghívója](#guest-inviter-permissions)

Az ebben a szerepkörben lévő felhasználók kezelhetik Azure Active Directory B2B vendég felhasználói meghívókat, amikor a **tagok meghívhatják** a felhasználói beállítást a nem értékre. További információ a B2B-együttműködésről az [Azure ad B2B együttműködésről](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Nem tartalmaz más engedélyeket.

### <a name="helpdesk-administratorhelpdesk-administrator-permissions"></a>[Segélyszolgálat rendszergazdája](#helpdesk-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók módosíthatják a jelszavakat, érvénytelenítik a frissítési jogkivonatokat, kezelhetik a szolgáltatási kérelmeket, és figyelik a szolgáltatás állapotát A frissítési jogkivonat érvénytelenítése kényszeríti a felhasználót, hogy jelentkezzen be újra. Az ügyfélszolgálati rendszergazdák alaphelyzetbe állíthatja a jelszavakat, és érvénytelenítheti azokat a más felhasználók frissítési jogkivonatait, akik nem rendszergazdák vagy csak a következő szerepkörökhöz vannak rendelve:

* Címtárolvasó
* Vendég meghívója
* Ügyfélszolgálati adminisztrátor
* Üzenetközpont-olvasó
* Jelentésolvasó

> [!IMPORTANT]
> Az ezzel a szerepkörrel rendelkező felhasználók megváltoztathatják azokat a személyeket, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz, illetve a Azure Active Directoryon belül és kívül is kritikus konfigurációhoz. A felhasználó jelszavának módosítása azt jelentheti, hogy a felhasználó identitását és engedélyeit feltételezi. Példa:

* Az alkalmazás regisztrálása és a vállalati alkalmazások tulajdonosai, akik kezelhetik a saját alkalmazások hitelesítő adatait. Ezek az alkalmazások jogosultsági szintű engedélyekkel rendelkezhetnek az Azure AD-ben, és máshol nem biztosítanak ügyfélszolgálati rendszergazdának. Ezen az elérési úton egy ügyfélszolgálati rendszergazda képes lehet feltételezni az alkalmazás tulajdonosának identitását, majd az alkalmazás hitelesítő adatainak frissítésével tovább feltételezni egy emelt szintű alkalmazás identitását.
* Az Azure-előfizetések tulajdonosai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz vagy az Azure-beli kritikus konfigurációhoz.
* Biztonsági csoport és Office 365 csoport tulajdonosai, akik kezelhetik a csoporttagság kezelését. Ezek a csoportok hozzáférést biztosíthatnak a bizalmas vagy magánjellegű információkhoz, illetve a kritikus konfigurációhoz az Azure AD-ben és máshol.
* Az Azure AD-n kívül más szolgáltatásokban, például az Exchange Online-ban, az Office biztonsági és megfelelőségi központban és a humán erőforrás-rendszerekben található rendszergazdák.
* Nem rendszergazdák, például vezetők, jogi tanácsadás és emberi erőforrások alkalmazottai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz.

> [!NOTE]
> A rendszergazdai engedélyek delegálása a felhasználók részhalmaza felett és a házirendek a felhasználók egy részhalmazára való alkalmazása a [felügyeleti egységekkel (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units)lehetséges.
>
> Ezt a szerepkört korábban "password Administrator" néven nevezték a [Azure Portal](https://portal.azure.com/). A nevét az Azure AD PowerShellben, az Azure AD Graph API és Microsoft Graph API-ban szereplő nevével megegyező "segélyszolgálat-rendszergazda" névre változtatjuk.

### <a name="intune-administratorintune-service-administrator-permissions"></a>[Intune-rendszergazda](#intune-service-administrator-permissions)

Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek Microsoft Intune online-ban, ha a szolgáltatás jelen van. Emellett ez a szerepkör lehetővé teszi a felhasználók és eszközök felügyeletét a házirendek társítása, valamint a csoportok létrehozása és kezelése céljából. További információ a [szerepköralapú adminisztrációs vezérlő (RBAC) és a Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)

> [!NOTE]
> A Microsoft Graph API, az Azure AD Graph API és az Azure AD PowerShell esetében ez a szerepkör "Intune szolgáltatás-rendszergazda" néven azonosítható. A [Azure Portal](https://portal.azure.com)"Intune-rendszergazda".

### <a name="kaizala-administratorkaizala-administrator-permissions"></a>[Kaizala-rendszergazda](#kaizala-administrator-permissions)

Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a beállítások Microsoft Kaizala való kezeléséhez, ha a szolgáltatás megtalálható, valamint a támogatási jegyek felügyeletének és a szolgáltatás állapotának figyelésének lehetősége. Emellett a felhasználó hozzáférhet a bevezetéshez kapcsolódó jelentésekhez & a Kaizala használatát a Kaizala műveletekkel létrehozott szervezeti tagok és üzleti jelentések segítségével.

### <a name="license-administratorlicense-administrator-permissions"></a>[Licenc rendszergazdája](#license-administrator-permissions)

Az ebben a szerepkörben lévő felhasználók hozzáadhatják, eltávolíthatják és frissíthetik a licenc-hozzárendeléseket a felhasználók, csoportok (csoporton alapuló licencelés) használatával, és kezelhetik a felhasználók használati helyét. A szerepkör nem biztosít előfizetéseket, nem hozhat létre és kezelhet csoportokat, illetve nem hozhat létre vagy kezelhet felhasználókat a használat helyén kívül. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

### <a name="message-center-privacy-readermessage-center-privacy-reader-permissions"></a>[A Message Center adatvédelmi olvasója](#message-center-privacy-reader-permissions)

Az ebben a szerepkörben lévő felhasználók az üzenetközpont összes értesítését nyomon tudják követni, beleértve az adatvédelmi üzeneteket is. Az üzenetközpont adatvédelmi olvasói e-mail-értesítéseket kapnak, beleértve az adatvédelemmel kapcsolatos értesítőket is, és lefizethetnek az üzenetközpont beállításaival. Az adatvédelmi üzeneteket csak a globális rendszergazda és az üzenetközpont adatvédelmi olvasója tudja olvasni. Emellett ez a szerepkör a csoportok, tartományok és előfizetések megtekintésére is lehetőséget tartalmaz. Ez a szerepkör nem jogosult a szolgáltatási kérelmek megtekintésére, létrehozására és kezelésére.

### <a name="message-center-readermessage-center-reader-permissions"></a>[Üzenetközpont-olvasó](#message-center-reader-permissions)

Az ebben a szerepkörben lévő felhasználók az [Office 365 Message Centerben](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) ellenőrizhetik az értesítéseket és a tanácsadói állapot frissítéseit, például az Exchange, az Intune és a Microsoft Teams szolgáltatásban konfigurált szolgáltatásokat. Az üzenetsor-olvasó hetente e-mail-kivonatokat fogad a hozzászólások, a frissítések, és megoszthatja az üzenetsor-bejegyzéseket az Office 365-ben. Az Azure AD-ben az ehhez a szerepkörhöz hozzárendelt felhasználók csak olvasási hozzáféréssel rendelkeznek az Azure AD-szolgáltatásokhoz, például a felhasználókhoz és a csoportokhoz. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

### <a name="partner-tier1-supportpartner-tier1-support-permissions"></a>[Partneri Tier1-támogatás](#partner-tier1-support-permissions)

Ne használja. Ez a szerepkör elavult, és a jövőben el lesz távolítva az Azure AD-ből. Ez a szerepkör kis számú Microsoft-viszonteladói partner általi használatra készült, és nem általános használatra készült.

### <a name="partner-tier2-supportpartner-tier2-support-permissions"></a>[Partneri szint-támogatás](#partner-tier2-support-permissions)

Ne használja. Ez a szerepkör elavult, és a jövőben el lesz távolítva az Azure AD-ből. Ez a szerepkör kis számú Microsoft-viszonteladói partner általi használatra készült, és nem általános használatra készült.

### <a name="password-administratorpassword-administrator-permissions"></a>[Jelszó-rendszergazda](#password-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók korlátozottan kezelhetik a jelszavakat. Ez a szerepkör nem teszi lehetővé a szolgáltatási kérelmek felügyeletét vagy a szolgáltatás állapotának figyelését. A jelszó-rendszergazdák alaphelyzetbe állíthatják más felhasználók jelszavát, akik nem rendszergazdák vagy csak a következő szerepkörök tagjai:

* Címtárolvasó
* Vendég meghívója
* Jelszókezelő

### <a name="power-bi-administratorpower-bi-service-administrator-permissions"></a>[Power BI rendszergazda](#power-bi-service-administrator-permissions)

Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft Power BIon belül, ha a szolgáltatás megtalálható, valamint a támogatási jegyek felügyeletének és a szolgáltatás állapotának figyelésének lehetősége. További információ [a Power bi rendszergazdai szerepkör megismeréséhez](https://docs.microsoft.com/power-bi/service-admin-role).

> [!NOTE]
> A Microsoft Graph API, az Azure AD Graph API és az Azure AD PowerShell esetében ez a szerepkör "Power BI szolgáltatás-rendszergazda" néven azonosítható. A [Azure Portal](https://portal.azure.com)"Power bi rendszergazda".

### <a name="privileged-authentication-administratorprivileged-authentication-administrator-permissions"></a>[Kiemelt jogosultságú hitelesítés rendszergazdája](#privileged-authentication-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók az összes felhasználóra vonatkozóan állíthatnak be vagy állíthatnak vissza nem jelszavas hitelesítő adatokat, beleértve a globális rendszergazdákat is, és frissíthetik az összes felhasználó jelszavát. A Kiemelt jogosultságú hitelesítő rendszergazdák kényszerítheti a felhasználókat, hogy újra regisztráljanak a meglévő, nem jelszóval nem rendelkező hitelesítő adatokkal (pl. MFA, pont), és visszavonják az eszközön az MFA megjegyzésének visszavonását, az összes felhasználó következő bejelentkezésekor az MFA-t kérik.

### <a name="privileged-role-administratorprivileged-role-administrator-permissions"></a>[Kiemelt szerepkörű rendszergazda](#privileged-role-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók a szerepkör-hozzárendeléseket kezelhetik Azure Active Directoryban, valamint a Azure AD Privileged Identity Managementon belül is. Emellett ez a szerepkör lehetővé teszi Privileged Identity Management és felügyeleti egységek valamennyi aspektusának kezelését.

> [!IMPORTANT]
> Ez a szerepkör lehetővé teszi az összes Azure AD-szerepkör hozzárendelésének kezelését, beleértve a globális rendszergazdai szerepkört is. Ez a szerepkör nem tartalmaz más, az Azure AD-ben található, például felhasználók létrehozásához vagy frissítéséhez szükséges jogosultságokkal rendelkező képességeket. Az ehhez a szerepkörhöz hozzárendelt felhasználók azonban további jogosultságokat is biztosíthatnak a további szerepkörök hozzárendelésével.

### <a name="reports-readerreports-reader-permissions"></a>[Jelentések olvasója](#reports-reader-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók megtekinthetik a használati jelentéskészítési adatokat és a jelentések irányítópultot Microsoft 365 felügyeleti központban, valamint a Power BI bevezetésének környezeti csomagjában. Emellett a szerepkör hozzáférést biztosít a bejelentkezési jelentésekhez és tevékenységekhez az Azure AD-ben, és a Microsoft Graph jelentési API által visszaadott adat. A jelentés-olvasó szerepkörhöz hozzárendelt felhasználó csak a megfelelő használati és bevezetési metrikákat érheti el. Nem rendelkeznek rendszergazdai jogosultsággal a beállítások konfigurálásához, illetve a termékspecifikus felügyeleti központok, például az Exchange eléréséhez. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

### <a name="search-administratorsearch-administrator-permissions"></a>[Keresés a Rendszergazdában](#search-administrator-permissions)

Az ebben a szerepkörben lévő felhasználók teljes hozzáféréssel rendelkeznek a Microsoft 365 felügyeleti központban található összes Microsoft keresési felügyeleti szolgáltatáshoz. A rendszergazdák a keresési rendszergazdákat és a keresési szerkesztői szerepköröket delegálják a felhasználók számára, valamint tartalmat hozhatnak létre és kezelhetnek, például a könyvjelzőket, a Q &t és a helyet. Emellett ezek a felhasználók megtekinthetik az üzenetközpont, figyelheti a szolgáltatás állapotát és szolgáltatási kérelmeket hozhatnak létre.

### <a name="search-editorsearch-editor-permissions"></a>[Keresési szerkesztő](#search-editor-permissions)

Az ebben a szerepkörben lévő felhasználók létrehozhatnak, kezelhetnek és törölhetnek tartalmakat a Microsoft Search szolgáltatáshoz a Microsoft 365 felügyeleti központban, beleértve a könyvjelzőket, a Q & a-t és a helyet.

### <a name="security-administratorsecurity-administrator-permissions"></a>[Biztonsági rendszergazda](#security-administrator-permissions)

Az ehhez a szerepkörhöz tartozó felhasználók a Microsoft 365 Security Center, a Azure Active Directory Identity Protection, a Azure Information Protection és az Office 365 Biztonsági és megfelelőségi központ biztonsággal kapcsolatos funkcióinak kezelésére jogosultak. Az Office 365-engedélyekkel kapcsolatos további információk [az office 365 biztonsági és megfelelőségi központ engedélyekben](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)találhatók.

A | Elvégezhető
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | A biztonsággal kapcsolatos házirendek figyelése Microsoft 365 szolgáltatások között<br>Biztonsági fenyegetések és riasztások kezelése<br>Jelentések megtekintése
Identity Protection Center | A biztonsági olvasó szerepkörre vonatkozó összes engedély<br>Emellett az összes Identity Protection Center-művelet elvégzésének lehetősége, kivéve a jelszavak alaphelyzetbe állítását
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | A biztonsági olvasó szerepkörre vonatkozó összes engedély<br>Az Azure AD szerepkör-hozzárendelések és-beállítások **nem** kezelhetők
[Office 365 Biztonsági és megfelelőségi központ](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Biztonsági házirendek kezelése<br>Biztonsági fenyegetések megtekintése, vizsgálata és reagálás<br>Jelentések megtekintése
Azure Advanced Threat Protection | Gyanús biztonsági tevékenység figyelése és reagálás
Windows Defender ATP és EDR | Szerepkörök hozzárendelése<br>Számítógépcsoportok kezelése<br>A végponti fenyegetések észlelésének és automatikus szervizelésének konfigurálása<br>Riasztások megtekintése, vizsgálata és válaszadás
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | A felhasználók, az eszközök, a beléptetés, a konfiguráció és az alkalmazás adatainak megtekintése<br>Nem lehet módosítani az Intune-t
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Rendszergazdák hozzáadása, szabályzatok és beállítások hozzáadása, naplók feltöltése és irányítási műveletek végrehajtása
[Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, szerkesztheti a biztonsági szabályzatokat, megtekintheti a riasztásokat és a javaslatokat,
[Office 365 szolgáltatás állapota](https://docs.microsoft.com/office365/enterprise/view-service-health) | Az Office 365-szolgáltatások állapotának megtekintése

### <a name="security-operatorsecurity-operator-permissions"></a>[Biztonsági operátor](#security-operator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók kezelhetik a riasztásokat, és globális olvasási hozzáféréssel rendelkeznek a biztonsággal kapcsolatos funkcióhoz, beleértve a Microsoft 365 Security Center, a Azure Active Directory, az Identity Protection, a Privileged Identity Management és az Office 365 összes adatát is. Biztonsági és megfelelőségi központ. Az Office 365-engedélyekkel kapcsolatos további információk [az office 365 biztonsági és megfelelőségi központ engedélyekben](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center)találhatók.

A | Elvégezhető
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | A biztonsági olvasó szerepkörre vonatkozó összes engedély<br>Biztonsági fenyegetésekkel kapcsolatos riasztások megtekintése, vizsgálata és reagálás
Identity Protection Center | A biztonsági olvasó szerepkörre vonatkozó összes engedély<br>Emellett az összes Identity Protection Center-művelet elvégzésének lehetősége, kivéve a jelszavak alaphelyzetbe állítását
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | A biztonsági olvasó szerepkörre vonatkozó összes engedély
[Office 365 Biztonsági és megfelelőségi központ](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | A biztonsági olvasó szerepkörre vonatkozó összes engedély<br>Biztonsági riasztások megtekintése, vizsgálata és reagálás
Windows Defender ATP és EDR | A biztonsági olvasó szerepkörre vonatkozó összes engedély<br>Biztonsági riasztások megtekintése, vizsgálata és reagálás
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | A biztonsági olvasó szerepkörre vonatkozó összes engedély
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | A biztonsági olvasó szerepkörre vonatkozó összes engedély
[Office 365 szolgáltatás állapota](https://docs.microsoft.com/office365/enterprise/view-service-health) | Az Office 365-szolgáltatások állapotának megtekintése

### <a name="security-readersecurity-reader-permissions"></a>[Biztonsági olvasó](#security-reader-permissions)

Az ehhez a szerepkörhöz tartozó felhasználók globális olvasási hozzáféréssel rendelkeznek a biztonsággal kapcsolatos funkcióhoz, beleértve a Microsoft 365 Security Center, a Azure Active Directory, az Identity Protection, a Privileged Identity Management, valamint az Azure Active olvasásának lehetőségét. Címtár-bejelentkezési jelentések és naplók, valamint az Office 365 Biztonsági és megfelelőségi központ. Az Office 365-engedélyekkel kapcsolatos további információk [az office 365 biztonsági és megfelelőségi központ engedélyekben](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)találhatók.

A | Elvégezhető
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | A biztonsággal kapcsolatos házirendek megtekintése Microsoft 365 szolgáltatások között<br>Biztonsági fenyegetések és riasztások megtekintése<br>Jelentések megtekintése
Identity Protection Center | A biztonsági funkciókkal kapcsolatos összes biztonsági jelentés és beállítás olvasása<br><ul><li>Levélszemét elleni<li>Encryption<li>Adatveszteség-megelőzés<li>Kártevők elleni<li>Komplex veszélyforrások elleni védelem<li>Adathalászat elleni védekezés<li>Mailflow-szabályok
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Csak olvasási hozzáféréssel rendelkezik a Azure AD Privileged Identity Managementban felkészített összes információhoz: Szabályzatok és jelentések az Azure AD szerepkör-hozzárendelésekhez és biztonsági felülvizsgálatokhoz.<br>**Nem lehet** regisztrálni a Azure ad Privileged Identity Managementre, és nem végezheti el a módosításokat. A Privileged Identity Management portálon vagy a PowerShellen keresztül a szerepkörhöz tartozó valaki további szerepköröket (például globális rendszergazda vagy Kiemelt szerepkörű rendszergazda) is aktiválhat, ha a felhasználó jogosult rájuk.
[Office 365 Biztonsági és megfelelőségi központ](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Biztonsági szabályzatok megtekintése<br>Biztonsági fenyegetések megtekintése és kivizsgálása<br>Jelentések megtekintése
Windows Defender ATP és EDR | Riasztások megtekintése és kivizsgálása. Ha bekapcsolja a szerepköralapú hozzáférés-vezérlést a Windows Defender ATP-ben, a csak olvasási jogosultsággal rendelkező felhasználók, például az Azure AD biztonsági olvasó szerepkör elvesztik a hozzáférést, amíg hozzá nem rendelnek egy Windows Defender ATP-szerepkörhöz.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | A felhasználó, az eszköz, a beléptetés, a konfiguráció és az alkalmazás adatainak megtekintése. Nem lehet módosítani az Intune-t.
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Csak olvasási jogosultsággal rendelkezik, és képes kezelni a riasztásokat
[Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Megtekintheti a javaslatokat és riasztásokat, megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, de nem végezhet módosításokat
[Office 365 szolgáltatás állapota](https://docs.microsoft.com/office365/enterprise/view-service-health) | Az Office 365-szolgáltatások állapotának megtekintése

### <a name="service-support-administratorservice-support-administrator-permissions"></a>[Szolgáltatás-támogatási rendszergazda](#service-support-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók megnyithatja a Microsoft Azure és az Office 365 Services támogatási kérelmeit, és megtekintheti a szolgáltatás irányítópultját és az üzenetközpont a [Azure Portal](https://portal.azure.com) és [Microsoft 365 felügyeleti központban](https://admin.microsoft.com). További információ a [rendszergazdai szerepkörökről](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> A Microsoft Graph API, az Azure AD Graph API és az Azure AD PowerShell esetében ez a szerepkör "szolgáltatás-támogatási rendszergazdaként" azonosítható. Ez a [Azure Portal](https://portal.azure.com), a [Microsoft 365 felügyeleti központ](https://admin.microsoft.com)és az Intune-portál "szolgáltatás rendszergazdája".

### <a name="sharepoint-administratorsharepoint-service-administrator-permissions"></a>[SharePoint-rendszergazda](#sharepoint-service-administrator-permissions)

Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft SharePoint Online-ban, ha a szolgáltatás megtalálható, valamint az Office 365-csoportok létrehozását és felügyeletét, a támogatási jegyek kezelését és a szolgáltatás állapotának figyelését. További információ a [rendszergazdai szerepkörökről](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> A Microsoft Graph API, az Azure AD Graph API és az Azure AD PowerShell esetében ez a szerepkör "SharePoint szolgáltatás-rendszergazdaként" azonosítható. A [Azure Portal](https://portal.azure.com)a "SharePoint Administrator".

### <a name="skype-for-business--lync-administratorlync-service-administrator-permissions"></a>[Skype vállalati verzió/Lync-rendszergazda](#lync-service-administrator-permissions)

Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft Skype vállalati verziójában, ha a szolgáltatás megtalálható, valamint a Skype-specifikus felhasználói attribútumok kezelése a Azure Active Directoryban. Emellett ez a szerepkör lehetővé teszi a támogatási jegyek felügyeletét és a szolgáltatás állapotának figyelését, valamint a csapatok és a Skype vállalati felügyeleti központ elérését. A fióknak a csapatoknak is licenccel kell rendelkeznie, vagy nem futtathat Team PowerShell-parancsmagokat. További információ [a Skype for Business-rendszergazdai szerepkörrel és a](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) Teams licencelési információkkal kapcsolatban a [Skype vállalati verzió és a Microsoft Teams bővítmény licencelése](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing) szolgáltatásban

> [!NOTE]
> A Microsoft Graph API, az Azure AD Graph API és az Azure AD PowerShell esetében ez a szerepkör "Lync szolgáltatás-rendszergazdaként" azonosítható. A [Azure Portal](https://portal.azure.com/)"Skype vállalati rendszergazda".

### <a name="teams-administratorteams-service-administrator-permissions"></a>[Csapatok rendszergazdája](#teams-service-administrator-permissions)

A szerepkör felhasználói a Microsoft Teams munkaterhelésének minden aspektusát felügyelhetik a Microsoft csapatainak & a Skype vállalati felügyeleti központ és a megfelelő PowerShell-modulok használatával. Ez többek között a telefonos, üzenetküldési, értekezlet-és a csapatokhoz kapcsolódó felügyeleti eszközökre is kiterjed. Ez a szerepkör Emellett lehetővé teszi az Office 365-csoportok létrehozását és kezelését, a támogatási jegyek kezelését és a szolgáltatás állapotának figyelését.
> [!NOTE]
> A Microsoft Graph API-ban, az Azure AD Graph API és az Azure AD PowerShellben ez a szerepkör "csapatok szolgáltatás-rendszergazdája" néven azonosítható. A [Azure Portal](https://portal.azure.com)a "Teams Administrator".

### <a name="teams-communications-administratorteams-communications-administrator-permissions"></a>[Csapatok kommunikációs rendszergazdája](#teams-communications-administrator-permissions)

Az ebben a szerepkörben lévő felhasználók kezelhetik a Microsoft Teams munkaterhelésének a hang& telefonos szolgáltatással kapcsolatos aspektusait. Ez magában foglalja a telefonszám-hozzárendelés, a hang-és értekezlet-szabályzatok felügyeleti eszközeit, valamint a Call Analytics eszközkészlet teljes hozzáférését.

### <a name="teams-communications-support-engineerteams-communications-support-engineer-permissions"></a>[Csapat kommunikációs támogató mérnök](#teams-communications-support-engineer-permissions)

Az ebben a szerepkörben lévő felhasználók a Microsoft Teams szolgáltatásban a Skype for Business felügyeleti központban a & Microsoft Teams felhasználói hívás hibaelhárítási eszközeivel végezhetik el a kommunikációval & kapcsolatos problémákat. Az ebben a szerepkörben lévő felhasználók megtekinthetik a teljes hívási rekord adatait az összes érintett résztvevő számára. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

### <a name="teams-communications-support-specialistteams-communications-support-specialist-permissions"></a>[A Teams Communications támogatási szakértője](#teams-communications-support-specialist-permissions)

Az ebben a szerepkörben lévő felhasználók a Microsoft Teams szolgáltatásban a Skype for Business felügyeleti központban a & Microsoft Teams felhasználói hívás hibaelhárítási eszközeivel végezhetik el a kommunikációval & kapcsolatos problémákat. Az ebben a szerepkörben lévő felhasználók csak az adott felhasználóra vonatkozó hívásban tekinthetik meg a felhasználói adatokat. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

### <a name="user-administratoruser-administrator-permissions"></a>[Felhasználói rendszergazda](#user-administrator-permissions)

Az ezzel a szerepkörrel rendelkező felhasználók létrehozhatnak felhasználókat, és kezelhetik a felhasználók minden aspektusát bizonyos korlátozásokkal (lásd alább), és frissíthetik a jelszó-elévülési házirendeket. Emellett az ezzel a szerepkörrel rendelkező felhasználók az összes csoportot is létrehozhatják és kezelhetik. Ez a szerepkör magában foglalja a felhasználói nézetek létrehozását és kezelését, a támogatási jegyek kezelését és a szolgáltatás állapotának figyelését is.

| | |
| --- | --- |
|Általános engedélyek|<p>Felhasználók és csoportok létrehozása</p><p>Felhasználói nézetek létrehozása és kezelése</p><p>Office-támogatási jegyek kezelése<p>Jelszó-elévülési szabályzatok frissítése|
|<p>Minden felhasználónál, beleértve az összes rendszergazdát</p>|<p>Licencek kezelése</p><p>Az összes felhasználói tulajdonság kezelése az egyszerű felhasználónév kivételével</p>
|Csak olyan felhasználóknál, akik nem rendszergazdák vagy a következő korlátozott rendszergazdai szerepkörök valamelyikében vannak:<ul><li>Címtárolvasó<li>Vendég meghívója<li>Ügyfélszolgálati adminisztrátor<li>Üzenetközpont-olvasó<li>Jelentésolvasó<li>Felhasználói rendszergazda|<p>Törlés és visszaállítás</p><p>Letiltás és engedélyezés</p><p>Frissítési tokenek érvénytelenítése</p><p>Az összes felhasználói tulajdonság kezelése, beleértve az egyszerű felhasználónevet</p><p>Másik jelszó kérése</p><p>Eszköz kulcsainak frissítése</p>

> [!IMPORTANT]
> Az ezzel a szerepkörrel rendelkező felhasználók megváltoztathatják azokat a személyeket, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz, illetve a Azure Active Directoryon belül és kívül is kritikus konfigurációhoz. A felhasználó jelszavának módosítása azt jelentheti, hogy a felhasználó identitását és engedélyeit feltételezi. Példa:

* Az alkalmazás regisztrálása és a vállalati alkalmazások tulajdonosai, akik kezelhetik a saját alkalmazások hitelesítő adatait. Ezek az alkalmazások jogosultsági szintű engedélyekkel rendelkezhetnek az Azure AD-ben, és máshol nem kapnak felhasználói rendszergazdákat. Ezen az elérési úton a felhasználói rendszergazda feltételezheti az alkalmazás tulajdonosának identitását, majd az alkalmazáshoz tartozó hitelesítő adatok frissítésével továbbra is feltételezheti a Kiemelt alkalmazások identitását.
* Az Azure-előfizetések tulajdonosai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz vagy az Azure-beli kritikus konfigurációhoz.
* Biztonsági csoport és Office 365 csoport tulajdonosai, akik kezelhetik a csoporttagság kezelését. Ezek a csoportok hozzáférést biztosíthatnak a bizalmas vagy magánjellegű információkhoz, illetve a kritikus konfigurációhoz az Azure AD-ben és máshol.
* Az Azure AD-n kívül más szolgáltatásokban, például az Exchange Online-ban, az Office biztonsági és megfelelőségi központban és a humán erőforrás-rendszerekben található rendszergazdák.
* Nem rendszergazdák, például vezetők, jogi tanácsadás és emberi erőforrások alkalmazottai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz.

## <a name="role-permissions"></a>Szerepkör-engedélyek

Az alábbi táblázatok ismertetik az egyes szerepkörökhöz Azure Active Directory adott engedélyeket. Egyes szerepkörök további engedélyekkel rendelkezhetnek a Azure Active Directoryon kívüli Microsoft-szolgáltatásokban.

### <a name="application-administrator-permissions"></a>Alkalmazás-rendszergazdai engedélyek

Az alkalmazás-regisztrációk és a vállalati alkalmazások minden aspektusát létrehozhatja és kezelheti.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/alkalmazás/appProxyAuthentication/Update | Az alkalmazás-proxy hitelesítési tulajdonságainak frissítése az Azure Active Directory lévő egyszerű szolgáltatásokban. |
| Microsoft. Directory/alkalmazás/appProxyUrlSettings/Update | Az alkalmazásproxy belső és külső URL-CÍMEInek frissítése Azure Active Directoryban. |
| Microsoft. Directory/alkalmazások/applicationProxy/olvasás | Az összes alkalmazásproxy tulajdonságainak olvasása. |
| Microsoft. Directory/alkalmazások/applicationProxy/Update | Az összes alkalmazásproxy tulajdonságainak frissítése. |
| microsoft.directory/applications/audience/update | Az Applications. célközönség tulajdonság frissítése Azure Active Directoryban. |
| microsoft.directory/applications/authentication/update | Az Applications. Authentication tulajdonság frissítése Azure Active Directoryban. |
| microsoft.directory/applications/basic/update | Azure Active Directory alkalmazásokban lévő alkalmazások alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/alkalmazások/létrehozás | Alkalmazások létrehozása a Azure Active Directoryban. |
| microsoft.directory/applications/credentials/update | Az Applications. hitelesítőadatok tulajdonság frissítése Azure Active Directoryban. |
| microsoft.directory/applications/delete | Alkalmazások törlése Azure Active Directory. |
| microsoft.directory/applications/owners/update | Az Applications. owners tulajdonság frissítése Azure Active Directoryban. |
| microsoft.directory/applications/permissions/update | Az Applications. permissions tulajdonság frissítése Azure Active Directoryban. |
| microsoft.directory/applications/policies/update | Az Applications. policies tulajdonság frissítése Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Create | Hozzon létre appRoleAssignments a Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/olvasás | AppRoleAssignments olvasása Azure Active Directoryban. |
| Microsoft. Directory/appRoleAssignments/Update | AppRoleAssignments frissítése Azure Active Directoryban. |
| Microsoft. Directory/appRoleAssignments/delete | Azure Active Directory appRoleAssignments törlése. |
| Microsoft. Directory/auditLogs/allProperties/READ | Olvassa el az összes tulajdonságot (beleértve a privilegizált tulajdonságokat is) a auditLogs Azure Active Directory. |
| Microsoft. Directory/connectorGroups/minden/olvasás | Az alkalmazás-proxy összekötő csoport tulajdonságainak olvasása Azure Active Directory. |
| Microsoft. Directory/connectorGroups/Everything/Update | Az összes alkalmazásproxy-összekötő csoport tulajdonságainak frissítése Azure Active Directory. |
| Microsoft. Directory/connectorGroups/Create | Alkalmazásproxy-összekötő csoportok létrehozása a Azure Active Directoryban. |
| Microsoft. Directory/connectorGroups/delete | Alkalmazásproxy-összekötő csoportok törlése a Azure Active Directoryban. |
| Microsoft. Directory/összekötők/minden/olvasás | Az összes alkalmazásproxy-összekötő tulajdonságainak olvasása Azure Active Directory. |
| Microsoft. Directory/összekötők/létrehozás | Alkalmazásproxy-összekötők létrehozása Azure Active Directoryban. |
| Microsoft. Directory/házirendek/applicationConfiguration/alap/olvasás | A policies. applicationConfiguration tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Basic/Update | A policies. applicationConfiguration tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/házirendek/applicationConfiguration/Create | Szabályzatok létrehozása a Azure Active Directoryban. |
| Microsoft. Directory/házirendek/applicationConfiguration/delete | Azure Active Directory szabályzatok törlése. |
| Microsoft. Directory/házirendek/applicationConfiguration/tulajdonosok/olvasás | A policies. applicationConfiguration tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/házirendek/applicationConfiguration/tulajdonosok/frissítés | A policies. applicationConfiguration tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/házirendek/applicationConfiguration/policyAppliedTo/READ | A policies. applicationConfiguration tulajdonság olvasása Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Frissítse a servicePrincipals. appRoleAssignedTo tulajdonságot a Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Frissítse a servicePrincipals. appRoleAssignments tulajdonságot a Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/audience/update | A servicePrincipals. célközönség tulajdonság frissítése Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/authentication/update | Frissítse a servicePrincipals. Authentication tulajdonságot a Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/basic/update | A servicePrincipals alapszintű tulajdonságainak frissítése Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Create | Hozzon létre servicePrincipals a Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Frissítse a servicePrincipals. hitelesítőadatok tulajdonságot a Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/delete | Azure Active Directory servicePrincipals törlése. |
| microsoft.directory/servicePrincipals/owners/update | A servicePrincipals. owners tulajdonság frissítése Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/permissions/update | Azure Active Directory servicePrincipals. permissions tulajdonságának frissítése. |
| microsoft.directory/servicePrincipals/policies/update | Frissítse a servicePrincipals. policies tulajdonságot a Azure Active Directoryban. |
| Microsoft. Directory/signInReports/allProperties/READ | Olvassa el az összes tulajdonságot (beleértve a privilegizált tulajdonságokat is) a signInReports Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="application-developer-permissions"></a>Alkalmazás-fejlesztői engedélyek

A "felhasználók regisztrálhatnak alkalmazásokat" beállítástól független alkalmazás-regisztrációkat hozhat létre.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/alkalmazások/createAsOwner | Alkalmazások létrehozása a Azure Active Directoryban. A létrehozó hozzá lett adva az első tulajdonosként, és a létrehozott objektum a Creator 250 létrehozott objektum-kvótájának számít. |
| Microsoft. Directory/appRoleAssignments/createAsOwner | Hozzon létre appRoleAssignments a Azure Active Directory. A létrehozó hozzá lett adva az első tulajdonosként, és a létrehozott objektum a Creator 250 létrehozott objektum-kvótájának számít. |
| Microsoft. Directory/Oauth2permissiongrant objektumok/createAsOwner | Hozzon létre Oauth2permissiongrant objektumok a Azure Active Directory. A létrehozó hozzá lett adva az első tulajdonosként, és a létrehozott objektum a Creator 250 létrehozott objektum-kvótájának számít. |
| Microsoft. Directory/servicePrincipals/createAsOwner | Hozzon létre servicePrincipals a Azure Active Directory. A létrehozó hozzá lett adva az első tulajdonosként, és a létrehozott objektum a Creator 250 létrehozott objektum-kvótájának számít. |

### <a name="authentication-administrator-permissions"></a>Hitelesítés-rendszergazdai engedélyek

A nem rendszergazda felhasználók számára engedélyezett a hitelesítési módszer adatainak megtekintése, beállítása és alaphelyzetbe állítása.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/felhasználók/invalidateAllRefreshTokens | Azure Active Directory összes felhasználói frissítési jogkivonatának érvénytelenítése. |
| Microsoft. Directory/felhasználók/strongAuthentication/Update | Az erős hitelesítési tulajdonságok, például az MFA hitelesítő adatainak frissítése. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| Microsoft. Directory/felhasználók/jelszó/frissítés | Az Office 365-szervezetben lévő összes felhasználó jelszavának frissítése. További részletekért tekintse meg az online dokumentációt. |

### <a name="azure-devops-administrator-permissions"></a>Azure DevOps-rendszergazdai engedélyek

Az Azure DevOps szervezeti házirendjét és beállításait tudja kezelni.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti [szerepkör leírása](#azure-devops-administrator) .
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Azure. devOps/allEntities/allTasks | Az Azure DevOps olvasása és konfigurálása. |

### <a name="azure-information-protection-administrator-permissions"></a>Rendszergazdai engedélyek Azure Information Protection

A Azure Information Protection szolgáltatás összes aspektusát képes kezelni.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti [szerepkör leírása](#) .
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection összes aspektusának kezelése. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="b2c-user-flow-administrator-permissions"></a>B2C felhasználói folyamat rendszergazdai engedélyei

A felhasználói folyamatok összes aspektusának létrehozása és kezelése.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. B2C/userFlows/allTasks | Felhasználói folyamatok olvasása és konfigurálása Azure Active Directory B2Cban. |

### <a name="b2c-user-flow-attribute-administrator-permissions"></a>B2C felhasználói folyamat attribútumának rendszergazdai engedélyei

Az összes felhasználói folyamat számára elérhető attribútum-séma létrehozása és kezelése.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. B2C/userAttributes/allTasks | A Azure Active Directory B2C felhasználói attribútumainak olvasása és konfigurálása. |

### <a name="b2c-ief-keyset-administrator-permissions"></a>B2C IEF kulcskészlet rendszergazdai engedélyei

Az identitási élmény keretrendszerében kezelheti az összevonás és a titkosítás titkait.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. B2C/trustFramework/alapkészletek/allTasks | A kulcs-készletek olvasása és konfigurálása Azure Active Directory B2Cban. |

### <a name="b2c-ief-policy-administrator-permissions"></a>B2C IEF házirend-rendszergazdai engedélyek

Megbízhatósági keretrendszer-szabályzatok létrehozása és kezelése az Identity Experience Framework-ben.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. B2C/trustFramework/policies/allTasks | Egyéni házirendek olvasása és konfigurálása Azure Active Directory B2Cban. |

### <a name="billing-administrator-permissions"></a>Számlázási rendszergazdai engedélyek

Olyan általános számlázási feladatokat hajthat végre, mint például a fizetési adatok frissítése.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/szervezet/alapszintű/frissítés | A Azure Active Directory szervezet alapszintű tulajdonságainak frissítése. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.commerce.billing/allEntities/allTasks | Az Office 365-számlázás minden aspektusának kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="cloud-application-administrator-permissions"></a>Felhőalapú alkalmazás-rendszergazdai engedélyek

Az alkalmazás-regisztráció és a vállalati alkalmazások minden aspektusát létrehozhatja és kezelheti az alkalmazásproxy kivételével.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/applications/audience/update | Az Applications. célközönség tulajdonság frissítése Azure Active Directoryban. |
| microsoft.directory/applications/authentication/update | Az Applications. Authentication tulajdonság frissítése Azure Active Directoryban. |
| microsoft.directory/applications/basic/update | Azure Active Directory alkalmazásokban lévő alkalmazások alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/alkalmazások/létrehozás | Alkalmazások létrehozása a Azure Active Directoryban. |
| microsoft.directory/applications/credentials/update | Az Applications. hitelesítőadatok tulajdonság frissítése Azure Active Directoryban. |
| microsoft.directory/applications/delete | Alkalmazások törlése Azure Active Directory. |
| microsoft.directory/applications/owners/update | Az Applications. owners tulajdonság frissítése Azure Active Directoryban. |
| microsoft.directory/applications/permissions/update | Az Applications. permissions tulajdonság frissítése Azure Active Directoryban. |
| microsoft.directory/applications/policies/update | Az Applications. policies tulajdonság frissítése Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Create | Hozzon létre appRoleAssignments a Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Update | AppRoleAssignments frissítése Azure Active Directoryban. |
| Microsoft. Directory/appRoleAssignments/delete | Azure Active Directory appRoleAssignments törlése. |
| Microsoft. Directory/auditLogs/allProperties/READ | Olvassa el az összes tulajdonságot (beleértve a privilegizált tulajdonságokat is) a auditLogs Azure Active Directory. |
| Microsoft. Directory/házirendek/applicationConfiguration/Create | Szabályzatok létrehozása a Azure Active Directoryban. |
| Microsoft. Directory/házirendek/applicationConfiguration/alap/olvasás | A policies. applicationConfiguration tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Basic/Update | A policies. applicationConfiguration tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/házirendek/applicationConfiguration/delete | Azure Active Directory szabályzatok törlése. |
| Microsoft. Directory/házirendek/applicationConfiguration/tulajdonosok/olvasás | A policies. applicationConfiguration tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/házirendek/applicationConfiguration/tulajdonosok/frissítés | A policies. applicationConfiguration tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/házirendek/applicationConfiguration/policyAppliedTo/READ | A policies. applicationConfiguration tulajdonság olvasása Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Frissítse a servicePrincipals. appRoleAssignedTo tulajdonságot a Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Frissítse a servicePrincipals. appRoleAssignments tulajdonságot a Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/audience/update | A servicePrincipals. célközönség tulajdonság frissítése Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/authentication/update | Frissítse a servicePrincipals. Authentication tulajdonságot a Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/basic/update | A servicePrincipals alapszintű tulajdonságainak frissítése Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Create | Hozzon létre servicePrincipals a Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Frissítse a servicePrincipals. hitelesítőadatok tulajdonságot a Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/delete | Azure Active Directory servicePrincipals törlése. |
| microsoft.directory/servicePrincipals/owners/update | A servicePrincipals. owners tulajdonság frissítése Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/permissions/update | Azure Active Directory servicePrincipals. permissions tulajdonságának frissítése. |
| microsoft.directory/servicePrincipals/policies/update | Frissítse a servicePrincipals. policies tulajdonságot a Azure Active Directoryban. |
| Microsoft. Directory/signInReports/allProperties/READ | Olvassa el az összes tulajdonságot (beleértve a privilegizált tulajdonságokat is) a signInReports Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="cloud-device-administrator-permissions"></a>Felhőalapú eszköz rendszergazdai engedélyei

Teljes hozzáférés az eszközök felügyeletéhez az Azure AD-ben.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/READ | Olvassa el az összes tulajdonságot (beleértve a privilegizált tulajdonságokat is) a auditLogs Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | A Devices. bitLockerRecoveryKeys tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/eszközök/törlés | Eszközök törlése Azure Active Directory. |
| Microsoft. Directory/eszközök/letiltás | Azure Active Directory eszközök letiltása. |
| Microsoft. Directory/eszközök/engedélyezés | Eszközök engedélyezése Azure Active Directoryban. |
| Microsoft. Directory/signInReports/allProperties/READ | Olvassa el az összes tulajdonságot (beleértve a privilegizált tulajdonságokat is) a signInReports Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |

### <a name="company-administrator-permissions"></a>Vállalati rendszergazdai engedélyek

Az Azure ad-identitásokat használó Azure AD-és Microsoft-szolgáltatások összes aspektusát képes kezelni.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a Microsoft. HRE. cloudAppSecurity. |
| Microsoft. Directory/Administrativeunit/allProperties/allTasks | Administrativeunit létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/alkalmazások/allProperties/allTasks | Alkalmazások létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/appRoleAssignments/allProperties/allTasks | AppRoleAssignments létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/auditLogs/allProperties/READ | Olvassa el az összes tulajdonságot (beleértve a privilegizált tulajdonságokat is) a auditLogs Azure Active Directory. |
| Microsoft. Directory/Contacts/allProperties/allTasks | Névjegyek létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/szerződések/allProperties/allTasks | Szerződések létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/eszközök/allProperties/allTasks | Eszközök létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/directoryRoles/allProperties/allTasks | DirectoryRoles létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/directoryRoleTemplates/allProperties/allTasks | DirectoryRoleTemplates létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/tartományok/allProperties/allTasks | Tartományok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/allProperties/allTasks | Csoportok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/groupSettings/allProperties/allTasks | GroupSettings létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/groupSettingTemplates/allProperties/allTasks | GroupSettingTemplates létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/loginTenantBranding/allProperties/allTasks | LoginTenantBranding létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/Oauth2permissiongrant objektumok/allProperties/allTasks | Oauth2permissiongrant objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/szervezet/allProperties/allTasks | Szervezet létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/házirendek/allProperties/allTasks | Szabályzatok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/roleAssignments/allProperties/allTasks | RoleAssignments létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/roleDefinitions/allProperties/allTasks | RoleDefinitions létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/scopedRoleMemberships/allProperties/allTasks | ScopedRoleMemberships létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/serviceAction/activateService | Elvégezheti a Activateservice szolgáltatás műveletét Azure Active Directory |
| Microsoft. Directory/serviceAction/disableDirectoryFeature | Elvégezheti a Disabledirectoryfeature szolgáltatás műveletét Azure Active Directory |
| Microsoft. Directory/serviceAction/enableDirectoryFeature | Elvégezheti a Enabledirectoryfeature szolgáltatás műveletét Azure Active Directory |
| Microsoft. Directory/serviceAction/getAvailableExtentionProperties | Elvégezheti a Getavailableextentionproperties szolgáltatás műveletét Azure Active Directory |
| Microsoft. Directory/servicePrincipals/allProperties/allTasks | ServicePrincipals létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/signInReports/allProperties/READ | Olvassa el az összes tulajdonságot (beleértve a privilegizált tulajdonságokat is) a signInReports Azure Active Directory. |
| Microsoft. Directory/subscribedSkus/allProperties/allTasks | SubscribedSkus létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/felhasználók/allProperties/allTasks | Felhasználók létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban. |
| Microsoft. directorySync/allEntities/allTasks | Azure AD Connect összes műveletének végrehajtása. |
| microsoft.aad.identityProtection/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a Microsoft. HRE. identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | A Microsoft. HRE. privilegedIdentityManagement összes erőforrásának olvasása. |
| microsoft.azure.advancedThreatProtection/allEntities/read | A Microsoft. Azure. advancedThreatProtection összes erőforrásának olvasása. |
| microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection összes aspektusának kezelése. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.commerce.billing/allEntities/allTasks | Az Office 365-számlázás minden aspektusának kezelése. |
| microsoft.intune/allEntities/allTasks | Az Intune összes aspektusának kezelése. |
| microsoft.office365.complianceManager/allEntities/allTasks | Az Office 365 megfelelőség-kezelő összes aspektusának kezelése |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Az asztali elemzések összes aspektusának kezelése. |
| microsoft.office365.exchange/allEntities/allTasks | Az Exchange Online összes aspektusának kezelése. |
| microsoft.office365.lockbox/allEntities/allTasks | Az Office 365 Ügyfélszéf összes aspektusának kezelése |
| microsoft.office365.messageCenter/messages/read | Read messages in microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Read securityMessages in microsoft.office365.messageCenter. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Az Office 365 Protection Center összes aspektusának kezelése. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a Microsoft. Office 365. securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.sharepoint/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a Microsoft. Office 365. SharePointban. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype vállalati online verzió összes aspektusának kezelése. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.usageReports/allEntities/read | Olvassa el az Office 365 használati jelentéseit. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | A Dynamics 365 összes aspektusának kezelése. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Power BI összes aspektusának kezelése. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | A Microsoft. Windows. defenderAdvancedThreatProtection összes erőforrásának olvasása. |

### <a name="compliance-administrator-permissions"></a>Megfelelőségi rendszergazdai engedélyek

A megfelelőségi konfiguráció és jelentések olvasása és kezelése az Azure AD-ben és az Office 365-ben.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.complianceManager/allEntities/allTasks | Az Office 365 megfelelőség-kezelő összes aspektusának kezelése |
| microsoft.office365.exchange/allEntities/allTasks | Az Exchange Online összes aspektusának kezelése. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.sharepoint/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a Microsoft. Office 365. SharePointban. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype vállalati online verzió összes aspektusának kezelése. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="compliance-data-administrator-permissions"></a>Megfelelőségi adatkezelési engedélyek rendszergazdai engedélyei

A megfelelőségi tartalom létrehozása és kezelése.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Microsoft Cloud App Security olvasása és konfigurálása. |
| microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection összes aspektusának kezelése. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.complianceManager/allEntities/allTasks | Az Office 365 megfelelőség-kezelő összes aspektusának kezelése |
| microsoft.office365.exchange/allEntities/allTasks | Az Exchange Online összes aspektusának kezelése. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.sharepoint/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a Microsoft. Office 365. SharePointban. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype vállalati online verzió összes aspektusának kezelése. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="conditional-access-administrator-permissions"></a>A feltételes hozzáférés rendszergazdai engedélyei

Kezelheti a feltételes hozzáférési képességeket.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/házirendek/conditionalAccess/alap/olvasás | A policies. conditionalAccess tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/Basic/Update | A policies. conditionalAccess tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/házirendek/conditionalAccess/Create | Szabályzatok létrehozása a Azure Active Directoryban. |
| Microsoft. Directory/házirendek/conditionalAccess/delete | Azure Active Directory szabályzatok törlése. |
| Microsoft. Directory/házirendek/conditionalAccess/tulajdonosok/olvasás | A policies. conditionalAccess tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/házirendek/conditionalAccess/tulajdonosok/frissítés | A policies. conditionalAccess tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/házirendek/conditionalAccess/policiesAppliedTo/READ | A policies. conditionalAccess tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/házirendek/conditionalAccess/tenantDefault/Update | A policies. conditionalAccess tulajdonság frissítése Azure Active Directoryban. |

### <a name="crm-service-administrator-permissions"></a>CRM szolgáltatás rendszergazdai engedélyei

A a Dynamics 365 termék összes aspektusát képes kezelni.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | A Dynamics 365 összes aspektusának kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="customer-lockbox-access-approver-permissions"></a>Ügyfél-kulcstároló hozzáférési jóváhagyó engedélyei

Jóváhagyhatja a Microsoft támogatási kérelmeit az ügyfél szervezeti adatkezeléséhez.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.lockbox/allEntities/allTasks | Az Office 365 Ügyfélszéf összes aspektusának kezelése |

### <a name="desktop-analytics-administrator-permissions"></a>Asztali elemzési rendszergazdai engedélyek

Felügyelheti az asztali elemzési és az Office testreszabási & házirend-szolgáltatásokat. Az asztali elemzések esetében ez magában foglalja az eszközök leltárának megtekintését, a központi telepítési tervek létrehozását, a központi telepítés és az állapot megtekintését. Az Office testreszabása & házirend-szolgáltatás esetében ez a szerepkör lehetővé teszi a felhasználóknak az Office-házirendek kezelését.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Az asztali elemzések összes aspektusának kezelése. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="device-administrators-permissions"></a>Eszköz-rendszergazdák engedélyei

Az ehhez a szerepkörhöz hozzárendelt felhasználók hozzá lesznek adva a helyi Rendszergazdák csoporthoz az Azure AD-hez csatlakoztatott eszközökön.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/groupSettings/Basic/READ | A Azure Active Directory groupSettings alapszintű tulajdonságainak olvasása. |
| Microsoft. Directory/groupSettingTemplates/Basic/READ | A Azure Active Directory groupSettingTemplates alapszintű tulajdonságainak olvasása. |

### <a name="directory-readers-permissions"></a>Directory-olvasók engedélyei
Az alapvető címtáradatok olvasása. Az alkalmazásokhoz való hozzáférés biztosítása nem a felhasználók számára ajánlott.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/Administrativeunit/Basic/READ | A Azure Active Directory Administrativeunit alapszintű tulajdonságainak olvasása. |
| Microsoft. Directory/Administrativeunit/tagok/olvasás | Olvassa el a Administrativeunit. Members tulajdonságot Azure Active Directory. |
| Microsoft. Directory/alkalmazások/alap/olvasás | Azure Active Directory-alkalmazások alapszintű tulajdonságainak olvasása. |
| Microsoft. Directory/alkalmazások/tulajdonosok/olvasás | Az Applications. owners tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/alkalmazások/házirendek/olvasás | Olvassa el az Applications. policies tulajdonságot Azure Active Directory. |
| Microsoft. Directory/Contacts/Basic/READ | A névjegyek alapszintű tulajdonságainak olvasása Azure Active Directory. |
| Microsoft. Directory/Contacts/memberOf/READ | A Contacts. memberOf tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/szerződések/alapszintű/beolvasás | Azure Active Directory-szerződések alapszintű tulajdonságainak olvasása. |
| Microsoft. Directory/eszközök/alap/olvasás | A Azure Active Directory eszközök alapszintű tulajdonságainak olvasása. |
| Microsoft. Directory/eszközök/memberOf/olvasás | A Devices. memberOf tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/eszközök/registeredOwners/olvasás | A Devices. registeredOwners tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/eszközök/registeredUsers/olvasás | A Devices. registeredUsers tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/directoryRoles/Basic/READ | A Azure Active Directory directoryRoles alapszintű tulajdonságainak olvasása. |
| Microsoft. Directory/directoryRoles/eligibleMembers/READ | Azure Active Directory directoryRoles. eligibleMembers tulajdonságának olvasása. |
| Microsoft. Directory/directoryRoles/tagok/olvasás | Olvassa el a directoryRoles. Members tulajdonságot Azure Active Directory. |
| Microsoft. Directory/tartományok/alapszintű/beolvasás | Alapszintű tulajdonságok olvasása Azure Active Directory tartományban. |
| Microsoft. Directory/csoportok/appRoleAssignments/olvasás | A groups. appRoleAssignments tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/csoportok/alap/olvasás | Azure Active Directory-csoportok alapszintű tulajdonságainak olvasása. |
| Microsoft. Directory/csoportok/memberOf/olvasás | A groups. memberOf tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/csoportok/tagok/olvasás | A groups. Members tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/csoportok/tulajdonosok/olvasás | A groups. owners tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/csoportok/beállítások/olvasás | A groups. Settings tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/groupSettings/Basic/READ | A Azure Active Directory groupSettings alapszintű tulajdonságainak olvasása. |
| Microsoft. Directory/groupSettingTemplates/Basic/READ | A Azure Active Directory groupSettingTemplates alapszintű tulajdonságainak olvasása. |
| Microsoft. Directory/Oauth2permissiongrant objektumok/Basic/READ | A Azure Active Directory Oauth2permissiongrant objektumok alapszintű tulajdonságainak olvasása. |
| Microsoft. Directory/szervezet/alap/olvasás | A Azure Active Directoryban található szervezet alapszintű tulajdonságainak olvasása. |
| Microsoft. Directory/szervezet/trustedCAsForPasswordlessAuth/olvasás | A Azure Active Directory szervezet. trustedCAsForPasswordlessAuth tulajdonságának olvasása. |
| Microsoft. Directory/roleAssignments/Basic/READ | A Azure Active Directory roleAssignments alapszintű tulajdonságainak olvasása. |
| Microsoft. Directory/roleDefinitions/Basic/READ | A Azure Active Directory roleDefinitions alapszintű tulajdonságainak olvasása. |
| Microsoft. Directory/servicePrincipals/appRoleAssignedTo/READ | Azure Active Directory servicePrincipals. appRoleAssignedTo tulajdonságának olvasása. |
| Microsoft. Directory/servicePrincipals/appRoleAssignments/READ | Azure Active Directory servicePrincipals. appRoleAssignments tulajdonságának olvasása. |
| Microsoft. Directory/servicePrincipals/Basic/READ | A Azure Active Directory servicePrincipals alapszintű tulajdonságainak olvasása. |
| Microsoft. Directory/servicePrincipals/memberOf/READ | Azure Active Directory servicePrincipals. memberOf tulajdonságának olvasása. |
| Microsoft. Directory/servicePrincipals/Oauth2permissiongrant objektumok/Basic/READ | Azure Active Directory servicePrincipals. Oauth2permissiongrant objektumok tulajdonságának olvasása. |
| Microsoft. Directory/servicePrincipals/ownedObjects/READ | Azure Active Directory servicePrincipals. ownedObjects tulajdonságának olvasása. |
| Microsoft. Directory/servicePrincipals/tulajdonosok/olvasás | Azure Active Directory servicePrincipals. owners tulajdonságának olvasása. |
| Microsoft. Directory/servicePrincipals/házirendek/olvasás | Olvassa el a servicePrincipals. policies tulajdonságot a Azure Active Directoryban. |
| Microsoft. Directory/subscribedSkus/Basic/READ | A Azure Active Directory subscribedSkus alapszintű tulajdonságainak olvasása. |
| Microsoft. Directory/felhasználók/appRoleAssignments/olvasás | A users. appRoleAssignments tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/felhasználók/alapszintű/beolvasás | Azure Active Directory-felhasználók alapszintű tulajdonságainak olvasása. |
| Microsoft. Directory/felhasználók/directReports/olvasás | A users. directReports tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/felhasználók/kezelő/olvasás | A users. Manager tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/felhasználók/memberOf/olvasás | A users. memberOf tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/felhasználók/Oauth2permissiongrant objektumok/alap/olvasás | A users. Oauth2permissiongrant objektumok tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/felhasználók/ownedDevices/olvasás | A users. ownedDevices tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/felhasználók/ownedObjects/olvasás | A users. ownedObjects tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/felhasználók/registeredDevices/olvasás | A users. registeredDevices tulajdonság olvasása Azure Active Directory. |

### <a name="directory-synchronization-accounts-permissions"></a>Címtár-szinkronizálási fiókok engedélyei

Csak Azure AD Connect szolgáltatás használja.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/szervezet/rSync/Update | Frissítse a szervezet. a (Azure Active Directory) tulajdonságot. |
| Microsoft. Directory/házirendek/létrehozás | Szabályzatok létrehozása a Azure Active Directoryban. |
| Microsoft. Directory/házirendek/törlés | Azure Active Directory szabályzatok törlése. |
| Microsoft. Directory/házirendek/alap/olvasás | Olvassa el a Azure Active Directory szabályzatok alapszintű tulajdonságait. |
| Microsoft. Directory/házirendek/alapszintű/frissítés | Azure Active Directory szabályzatok alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/házirendek/tulajdonosok/olvasás | A policies. owners tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/házirendek/tulajdonosok/frissítés | A policies. owners tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/házirendek/policiesAppliedTo/olvasás | A policies. policiesAppliedTo tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/házirendek/tenantDefault/Update | A policies. tenantDefault tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/appRoleAssignedTo/READ | Azure Active Directory servicePrincipals. appRoleAssignedTo tulajdonságának olvasása. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Frissítse a servicePrincipals. appRoleAssignedTo tulajdonságot a Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/appRoleAssignments/READ | Azure Active Directory servicePrincipals. appRoleAssignments tulajdonságának olvasása. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Frissítse a servicePrincipals. appRoleAssignments tulajdonságot a Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/audience/update | A servicePrincipals. célközönség tulajdonság frissítése Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/authentication/update | Frissítse a servicePrincipals. Authentication tulajdonságot a Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/Basic/READ | A Azure Active Directory servicePrincipals alapszintű tulajdonságainak olvasása. |
| microsoft.directory/servicePrincipals/basic/update | A servicePrincipals alapszintű tulajdonságainak frissítése Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Create | Hozzon létre servicePrincipals a Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Frissítse a servicePrincipals. hitelesítőadatok tulajdonságot a Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/memberOf/READ | Azure Active Directory servicePrincipals. memberOf tulajdonságának olvasása. |
| Microsoft. Directory/servicePrincipals/Oauth2permissiongrant objektumok/Basic/READ | Azure Active Directory servicePrincipals. Oauth2permissiongrant objektumok tulajdonságának olvasása. |
| Microsoft. Directory/servicePrincipals/tulajdonosok/olvasás | Azure Active Directory servicePrincipals. owners tulajdonságának olvasása. |
| microsoft.directory/servicePrincipals/owners/update | A servicePrincipals. owners tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/ownedObjects/READ | Azure Active Directory servicePrincipals. ownedObjects tulajdonságának olvasása. |
| microsoft.directory/servicePrincipals/permissions/update | Azure Active Directory servicePrincipals. permissions tulajdonságának frissítése. |
| Microsoft. Directory/servicePrincipals/házirendek/olvasás | Olvassa el a servicePrincipals. policies tulajdonságot a Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/policies/update | Frissítse a servicePrincipals. policies tulajdonságot a Azure Active Directoryban. |
| Microsoft. directorySync/allEntities/allTasks | Azure AD Connect összes műveletének végrehajtása. |

### <a name="directory-writers-permissions"></a>Directory-írók engedélyei

Olvasás & az alapszintű könyvtár adatainak írása. Az alkalmazásokhoz való hozzáférés biztosítása nem a felhasználók számára ajánlott.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/csoportok/létrehozás | Csoportok létrehozása a Azure Active Directoryban. |
| Microsoft. Directory/csoportok/createAsOwner | Csoportok létrehozása a Azure Active Directoryban. A létrehozó hozzá lett adva az első tulajdonosként, és a létrehozott objektum a Creator 250 létrehozott objektum-kvótájának számít. |
| microsoft.directory/groups/appRoleAssignments/update | A groups. appRoleAssignments tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/alapszintű/frissítés | Azure Active Directoryban lévő csoportok alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/csoportok/tagok/frissítés | A groups. Members tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/tulajdonosok/frissítés | A groups. owners tulajdonság frissítése Azure Active Directoryban. |
| microsoft.directory/groups/settings/update | A groups. Settings tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/groupSettings/Basic/Update | A groupSettings alapszintű tulajdonságainak frissítése Azure Active Directory. |
| Microsoft. Directory/groupSettings/Create | Hozzon létre groupSettings a Azure Active Directory. |
| Microsoft. Directory/groupSettings/delete | Azure Active Directory groupSettings törlése. |
| Microsoft. Directory/felhasználók/appRoleAssignments/Update | A users. appRoleAssignments tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/felhasználók/assignLicense | Licencek kezelése Azure Active Directory felhasználóinak. |
| Microsoft. Directory/felhasználók/alapszintű/frissítés | A Azure Active Directoryban lévő felhasználók alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/felhasználók/invalidateAllRefreshTokens | Azure Active Directory összes felhasználói frissítési jogkivonatának érvénytelenítése. |
| Microsoft. Directory/felhasználók/kezelő/frissítés | A users. Manager tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/felhasználók/userPrincipalName/Update | A users. userPrincipalName tulajdonság frissítése Azure Active Directoryban. |

### <a name="exchange-service-administrator-permissions"></a>Exchange szolgáltatás rendszergazdai engedélyei

Az Exchange termék összes aspektusát képes kezelni.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/csoportok/Unified/appRoleAssignments/Update | A groups. Unified tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/Unified/Basic/Update | Az Office 365-csoportok alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/csoportok/egységes/létrehozás | Office 365-csoportok létrehozása. |
| Microsoft. Directory/csoportok/egyesített/delete | Office 365-csoportok törlése. |
| Microsoft. Directory/csoportok/egyesített/tagok/frissítés | Az Office 365-csoportok tagságának frissítése. |
| Microsoft. Directory/csoportok/Unified/owners/Update | Az Office 365-csoportok tulajdonjogának frissítése. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.exchange/allEntities/allTasks | Az Exchange Online összes aspektusának kezelése. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="external-identity-provider-administrator-permissions"></a>Külső identitás-szolgáltató rendszergazdai engedélyei

Konfigurálja az identitás-szolgáltatókat a közvetlen összevonás használatához.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. B2C/identityProviders/allTasks | Identitás-szolgáltatók olvasása és konfigurálása Azure Active Directory B2Cban. |

### <a name="guest-inviter-permissions"></a>Vendég meghívójának engedélyei
Meghívhatja a vendég felhasználókat a "tagok hívhatják meg a vendégeket" beállítástól függetlenül.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/felhasználók/appRoleAssignments/olvasás | A users. appRoleAssignments tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/felhasználók/alapszintű/beolvasás | Azure Active Directory-felhasználók alapszintű tulajdonságainak olvasása. |
| Microsoft. Directory/felhasználók/directReports/olvasás | A users. directReports tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/felhasználók/inviteGuest | Vendég felhasználók meghívása Azure Active Directoryban. |
| Microsoft. Directory/felhasználók/kezelő/olvasás | A users. Manager tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/felhasználók/memberOf/olvasás | A users. memberOf tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/felhasználók/Oauth2permissiongrant objektumok/alap/olvasás | A users. Oauth2permissiongrant objektumok tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/felhasználók/ownedDevices/olvasás | A users. ownedDevices tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/felhasználók/ownedObjects/olvasás | A users. ownedObjects tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/felhasználók/registeredDevices/olvasás | A users. registeredDevices tulajdonság olvasása Azure Active Directory. |

### <a name="helpdesk-administrator-permissions"></a>Ügyfélszolgálati rendszergazdai engedélyek

Alaphelyzetbe állíthatja a nem rendszergazdák és az ügyfélszolgálati rendszergazdák jelszavát.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | A Devices. bitLockerRecoveryKeys tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/felhasználók/invalidateAllRefreshTokens | Azure Active Directory összes felhasználói frissítési jogkivonatának érvénytelenítése. |
| Microsoft. Directory/felhasználók/jelszó/frissítés | Frissítse a Azure Active Directory összes felhasználójának jelszavát. További részletekért tekintse meg az online dokumentációt. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="intune-service-administrator-permissions"></a>Intune szolgáltatás-rendszergazdai engedélyek

Az Intune-termék minden aspektusát képes kezelni.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/Contacts/Basic/Update | A névjegyek alapszintű tulajdonságainak frissítése Azure Active Directoryban. |
| Microsoft. Directory/névjegyek/létrehozás | Névjegyek létrehozása Azure Active Directoryban. |
| Microsoft. Directory/Contacts/delete | Névjegyek törlése Azure Active Directory. |
| Microsoft. Directory/eszközök/alapszintű/frissítés | A Azure Active Directory eszközök alapszintű tulajdonságainak frissítése. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | A Devices. bitLockerRecoveryKeys tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/eszközök/létrehozás | Hozzon létre eszközöket a Azure Active Directory. |
| Microsoft. Directory/eszközök/törlés | Eszközök törlése Azure Active Directory. |
| Microsoft. Directory/eszközök/registeredOwners/Update | A Devices. registeredOwners tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/eszközök/registeredUsers/Update | A Devices. registeredUsers tulajdonság frissítése Azure Active Directoryban. |
| microsoft.directory/groups/appRoleAssignments/update | A groups. appRoleAssignments tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/alapszintű/frissítés | Azure Active Directoryban lévő csoportok alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/csoportok/létrehozás | Csoportok létrehozása a Azure Active Directoryban. |
| Microsoft. Directory/csoportok/createAsOwner | Csoportok létrehozása a Azure Active Directoryban. A létrehozó hozzá lett adva az első tulajdonosként, és a létrehozott objektum a Creator 250 létrehozott objektum-kvótájának számít. |
| microsoft.directory/groups/delete | Csoportok törlése Azure Active Directory. |
| Microsoft. Directory/csoportok/hiddenMembers/olvasás | A groups. hiddenMembers tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/csoportok/tagok/frissítés | A groups. Members tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/tulajdonosok/frissítés | A groups. owners tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/visszaállítás | Csoportok visszaállítása a Azure Active Directoryban. |
| microsoft.directory/groups/settings/update | A groups. Settings tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/felhasználók/appRoleAssignments/Update | A users. appRoleAssignments tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/felhasználók/alapszintű/frissítés | A Azure Active Directoryban lévő felhasználók alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/felhasználók/kezelő/frissítés | A users. Manager tulajdonság frissítése Azure Active Directoryban. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.intune/allEntities/allTasks | Az Intune összes aspektusának kezelése. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |

### <a name="kaizala-administrator-permissions"></a>Kaizala-rendszergazdai engedélyek

Kezelheti a Microsoft Kaizala beállításait.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | Olvassa el az Office 365 felügyeleti központot. |

### <a name="license-administrator-permissions"></a>Licenc-rendszergazdai engedélyek

A a felhasználókra és a csoportokra vonatkozó licenceket is képes kezelni.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/felhasználók/assignLicense | Licencek kezelése Azure Active Directory felhasználóinak. |
| Microsoft. Directory/felhasználók/usageLocation/Update | A users. usageLocation tulajdonság frissítése Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |

### <a name="lync-service-administrator-permissions"></a>Lync szolgáltatás-rendszergazdai engedélyek

A a Skype vállalati verziójának minden aspektusát képes kezelni.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype vállalati online verzió összes aspektusának kezelése. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="message-center-privacy-reader-permissions"></a>A Message Center adatvédelmi olvasójának engedélyei

Beolvashatja az üzenetsor-bejegyzéseket, az adatvédelmi üzeneteket, a csoportokat, a tartományokat és az előfizetéseket.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.messageCenter/messages/read | Read messages in microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Read securityMessages in microsoft.office365.messageCenter. |

### <a name="message-center-reader-permissions"></a>Message Center-olvasó engedélyei
A szervezete üzenetei és frissítései csak az Office 365 Message Centerben olvashatók. 

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.messageCenter/messages/read | Read messages in microsoft.office365.messageCenter. |

### <a name="partner-tier1-support-permissions"></a>Partneri Tier1-támogatási engedélyek

Nem használható – általános használatra nem ajánlott.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/Contacts/Basic/Update | A névjegyek alapszintű tulajdonságainak frissítése Azure Active Directoryban. |
| Microsoft. Directory/névjegyek/létrehozás | Névjegyek létrehozása Azure Active Directoryban. |
| Microsoft. Directory/Contacts/delete | Névjegyek törlése Azure Active Directory. |
| Microsoft. Directory/csoportok/létrehozás | Csoportok létrehozása a Azure Active Directoryban. |
| Microsoft. Directory/csoportok/createAsOwner | Csoportok létrehozása a Azure Active Directoryban. A létrehozó hozzá lett adva az első tulajdonosként, és a létrehozott objektum a Creator 250 létrehozott objektum-kvótájának számít. |
| Microsoft. Directory/csoportok/tagok/frissítés | A groups. Members tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/tulajdonosok/frissítés | A groups. owners tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/felhasználók/appRoleAssignments/Update | A users. appRoleAssignments tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/felhasználók/assignLicense | Licencek kezelése Azure Active Directory felhasználóinak. |
| Microsoft. Directory/felhasználók/alapszintű/frissítés | A Azure Active Directoryban lévő felhasználók alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/felhasználók/törlés | Azure Active Directory felhasználók törlése. |
| Microsoft. Directory/felhasználók/invalidateAllRefreshTokens | Azure Active Directory összes felhasználói frissítési jogkivonatának érvénytelenítése. |
| Microsoft. Directory/felhasználók/kezelő/frissítés | A users. Manager tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/felhasználók/jelszó/frissítés | Frissítse a Azure Active Directory összes felhasználójának jelszavát. További részletekért tekintse meg az online dokumentációt. |
| Microsoft. Directory/felhasználók/visszaállítás | Azure Active Directory törölt felhasználók visszaállítása. |
| Microsoft. Directory/felhasználók/userPrincipalName/Update | A users. userPrincipalName tulajdonság frissítése Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="partner-tier2-support-permissions"></a>Partneri szint-támogatási engedélyek

Nem használható – általános használatra nem ajánlott.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/Contacts/Basic/Update | A névjegyek alapszintű tulajdonságainak frissítése Azure Active Directoryban. |
| Microsoft. Directory/névjegyek/létrehozás | Névjegyek létrehozása Azure Active Directoryban. |
| Microsoft. Directory/Contacts/delete | Névjegyek törlése Azure Active Directory. |
| Microsoft. Directory/tartományok/allTasks | Tartományok létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/létrehozás | Csoportok létrehozása a Azure Active Directoryban. |
| microsoft.directory/groups/delete | Csoportok törlése Azure Active Directory. |
| Microsoft. Directory/csoportok/tagok/frissítés | A groups. Members tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/visszaállítás | Csoportok visszaállítása a Azure Active Directoryban. |
| Microsoft. Directory/szervezet/alapszintű/frissítés | A Azure Active Directory szervezet alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/felhasználók/appRoleAssignments/Update | A users. appRoleAssignments tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/felhasználók/assignLicense | Licencek kezelése Azure Active Directory felhasználóinak. |
| Microsoft. Directory/felhasználók/alapszintű/frissítés | A Azure Active Directoryban lévő felhasználók alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/felhasználók/törlés | Azure Active Directory felhasználók törlése. |
| Microsoft. Directory/felhasználók/invalidateAllRefreshTokens | Azure Active Directory összes felhasználói frissítési jogkivonatának érvénytelenítése. |
| Microsoft. Directory/felhasználók/kezelő/frissítés | A users. Manager tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/felhasználók/jelszó/frissítés | Frissítse a Azure Active Directory összes felhasználójának jelszavát. További részletekért tekintse meg az online dokumentációt. |
| Microsoft. Directory/felhasználók/visszaállítás | Azure Active Directory törölt felhasználók visszaállítása. |
| Microsoft. Directory/felhasználók/userPrincipalName/Update | A users. userPrincipalName tulajdonság frissítése Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="password-administrator-permissions"></a>Jelszó-rendszergazdai engedélyek

Alaphelyzetbe állíthatja a nem rendszergazdák és a jelszó-rendszergazdák jelszavát.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/felhasználók/jelszó/frissítés | Frissítse a Azure Active Directory összes felhasználójának jelszavát. További részletekért tekintse meg az online dokumentációt. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |

### <a name="power-bi-service-administrator-permissions"></a>Power BI szolgáltatás-rendszergazdai engedélyek

A Power BI termék összes aspektusát képes kezelni.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Power BI összes aspektusának kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="privileged-authentication-administrator-permissions"></a>Kiemelt jogosultságú hitelesítés rendszergazdai engedélyei

Megtekintheti, beállíthatja és alaphelyzetbe állíthatja a hitelesítési módszer adatait bármely felhasználó számára (rendszergazda vagy nem rendszergazda).

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/felhasználók/invalidateAllRefreshTokens | Azure Active Directory összes felhasználói frissítési jogkivonatának érvénytelenítése. |
| Microsoft. Directory/felhasználók/strongAuthentication/Update | Az erős hitelesítési tulajdonságok, például az MFA hitelesítő adatainak frissítése. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| Microsoft. Directory/felhasználók/jelszó/frissítés | Az Office 365-szervezetben lévő összes felhasználó jelszavának frissítése. További részletekért tekintse meg az online dokumentációt. |

### <a name="privileged-role-administrator-permissions"></a>Kiemelt szerepkörű rendszergazdai jogosultságok

Felügyelheti a szerepkör-hozzárendeléseket az Azure AD-ben, és a Privileged Identity Management összes aspektusát.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a Microsoft. HRE. privilegedIdentityManagement. |
| Microsoft. Directory/servicePrincipals/appRoleAssignedTo/allTasks | A servicePrincipals. appRoleAssignedTo tulajdonság olvasása és konfigurálása Azure Active Directoryban. |
| Microsoft. Directory/servicePrincipals/Oauth2permissiongrant objektumok/allTasks | A servicePrincipals. Oauth2permissiongrant objektumok tulajdonság olvasása és konfigurálása Azure Active Directoryban. |
| Microsoft. Directory/Administrativeunit/allProperties/allTasks | Felügyeleti egységek létrehozása és kezelése (beleértve a tagokat) |
| Microsoft. Directory/roleAssignments/allProperties/allTasks | Szerepkör-hozzárendelések létrehozása és kezelése. |
| Microsoft. Directory/roleDefinitions/allProperties/allTasks | Szerepkör-definíciók létrehozása és kezelése. |

### <a name="reports-reader-permissions"></a>Jelentések olvasójának engedélyei

Beolvashatja a bejelentkezési és a naplózási jelentéseket.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/READ | Olvassa el az összes tulajdonságot (beleértve a privilegizált tulajdonságokat is) a auditLogs Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/READ | Olvassa el az összes tulajdonságot (beleértve a privilegizált tulajdonságokat is) a signInReports Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.usageReports/allEntities/read | Olvassa el az Office 365 használati jelentéseit. |

### <a name="search-administrator-permissions"></a>Rendszergazdai engedélyek keresése

A a Microsoft keresési beállításainak minden aspektusát létrehozhatja és kezelheti.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Read messages in microsoft.office365.messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Az összes erőforrás létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése a Microsoft. Office 365. Search webhelyen. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.usageReports/allEntities/read | Olvassa el az Office 365 használati jelentéseit. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |

### <a name="search-editor-permissions"></a>Keresési szerkesztői engedélyek

Létrehozhat és kezelhet olyan szerkesztési tartalmakat, mint a könyvjelzők, a Q és a, a helyszínek, az alaprajz.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Read messages in microsoft.office365.messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Tartalom létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése a Microsoft. Office 365. Search fájlból. |
| microsoft.office365.usageReports/allEntities/read | Olvassa el az Office 365 használati jelentéseit. |

### <a name="security-administrator-permissions"></a>Biztonsági rendszergazdai engedélyek

A biztonsági információk és jelentések beolvasása, valamint a konfiguráció kezelése az Azure AD-ben és az Office 365-ben.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.directory/applications/policies/update | Az Applications. policies tulajdonság frissítése Azure Active Directory. |
| Microsoft. Directory/auditLogs/allProperties/READ | Olvassa el az összes tulajdonságot (beleértve a privilegizált tulajdonságokat is) a auditLogs Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | A Devices. bitLockerRecoveryKeys tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/házirendek/alapszintű/frissítés | Azure Active Directory szabályzatok alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/házirendek/létrehozás | Szabályzatok létrehozása a Azure Active Directoryban. |
| Microsoft. Directory/házirendek/törlés | Azure Active Directory szabályzatok törlése. |
| Microsoft. Directory/házirendek/tulajdonosok/frissítés | A policies. owners tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/házirendek/tenantDefault/Update | A policies. tenantDefault tulajdonság frissítése Azure Active Directoryban. |
| microsoft.directory/servicePrincipals/policies/update | Frissítse a servicePrincipals. policies tulajdonságot a Azure Active Directoryban. |
| Microsoft. Directory/signInReports/allProperties/READ | Olvassa el az összes tulajdonságot (beleértve a privilegizált tulajdonságokat is) a signInReports Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | A Microsoft. HRE. identityProtection összes erőforrásának olvasása. |
| microsoft.aad.identityProtection/allEntities/update | Frissítse a Microsoft. HRE. identityProtection összes erőforrását. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | A Microsoft. HRE. privilegedIdentityManagement összes erőforrásának olvasása. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.protectionCenter/allEntities/read | Az Office 365 Protection Center összes aspektusának olvasása. |
| microsoft.office365.protectionCenter/allEntities/update | Frissítse a Microsoft. Office 365. protectionCenter összes erőforrását. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |

### <a name="security-operator-permissions"></a>Biztonsági operátor engedélyei

Biztonsági események létrehozása és kezelése.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Microsoft Cloud App Security olvasása és konfigurálása. |
| microsoft.aad.identityProtection/allEntities/read | A Microsoft. HRE. identityProtection összes erőforrásának olvasása. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | A Microsoft. HRE. privilegedIdentityManagement összes erőforrásának olvasása. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Az Azure AD komplex veszélyforrások elleni védelem olvasása és konfigurálása. |
| microsoft.intune/allEntities/allTasks | Az Intune összes aspektusának kezelése. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Biztonsági és megfelelőségi központ olvasása és konfigurálása. |
| microsoft.office365.usageReports/allEntities/read | Olvassa el az Office 365 használati jelentéseit. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | A Windows Defender komplex veszélyforrások elleni védelem olvasása és konfigurálása. |

### <a name="security-reader-permissions"></a>Biztonsági olvasó engedélyei

A biztonsági információkat és jelentéseket az Azure AD-ben és az Office 365-ben is elolvashatja.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/READ | Olvassa el az összes tulajdonságot (beleértve a privilegizált tulajdonságokat is) a auditLogs Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | A Devices. bitLockerRecoveryKeys tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/READ | Olvassa el az összes tulajdonságot (beleértve a privilegizált tulajdonságokat is) a signInReports Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | A Microsoft. HRE. identityProtection összes erőforrásának olvasása. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | A Microsoft. HRE. privilegedIdentityManagement összes erőforrásának olvasása. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.protectionCenter/allEntities/read | Az Office 365 Protection Center összes aspektusának olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |

### <a name="service-support-administrator-permissions"></a>Szolgáltatás-támogatási rendszergazdai engedélyek

A szolgáltatás állapotával kapcsolatos információk beolvasása és a támogatási jegyek kezelése.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="sharepoint-service-administrator-permissions"></a>A SharePoint szolgáltatás rendszergazdai engedélyei

A a SharePoint szolgáltatás összes aspektusát képes kezelni.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/csoportok/Unified/appRoleAssignments/Update | A groups. Unified tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/Unified/Basic/Update | Az Office 365-csoportok alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/csoportok/egységes/létrehozás | Office 365-csoportok létrehozása. |
| Microsoft. Directory/csoportok/egyesített/delete | Office 365-csoportok törlése. |
| Microsoft. Directory/csoportok/egyesített/tagok/frissítés | Az Office 365-csoportok tagságának frissítése. |
| Microsoft. Directory/csoportok/Unified/owners/Update | Az Office 365-csoportok tulajdonjogának frissítése. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.sharepoint/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a Microsoft. Office 365. SharePointban. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="teams-communications-administrator-permissions"></a>A csapat kommunikációs rendszergazdai engedélyei

A a Microsoft Teams szolgáltatás hívási és értekezlet-szolgáltatásainak kezelésére képes.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.usageReports/allEntities/read | Olvassa el az Office 365 használati jelentéseit. |

### <a name="teams-communications-support-engineer-permissions"></a>A Teams Communications támogatási szakemberének engedélyei

A speciális eszközöket használó csapatokon belüli kommunikációs problémák megoldására is képes.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |

### <a name="teams-communications-support-specialist-permissions"></a>A Teams Communications-támogatás szakosodott engedélyeket

Az alapszintű eszközöket használó csapatokon belüli kommunikációs problémák elhárítására használható.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |

### <a name="teams-service-administrator-permissions"></a>A Teams szolgáltatás rendszergazdai engedélyei

Felügyelheti a Microsoft Teams szolgáltatást.

> [!NOTE]
> Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
>
>

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/csoportok/hiddenMembers/olvasás | A groups. hiddenMembers tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/csoportok/Unified/appRoleAssignments/Update | A groups. Unified tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/Unified/Basic/Update | Az Office 365-csoportok alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/csoportok/egységes/létrehozás | Office 365-csoportok létrehozása. |
| Microsoft. Directory/csoportok/egyesített/delete | Office 365-csoportok törlése. |
| Microsoft. Directory/csoportok/egyesített/tagok/frissítés | Az Office 365-csoportok tagságának frissítése. |
| Microsoft. Directory/csoportok/Unified/owners/Update | Az Office 365-csoportok tulajdonjogának frissítése. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.usageReports/allEntities/read | Olvassa el az Office 365 használati jelentéseit. |

### <a name="user-administrator-permissions"></a>Felhasználói rendszergazdai engedélyek
Kezelheti a felhasználók és a csoportok minden aspektusát, beleértve a korlátozott rendszergazdák jelszavainak alaphelyzetbe állítását is.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. Directory/appRoleAssignments/Create | Hozzon létre appRoleAssignments a Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/delete | Azure Active Directory appRoleAssignments törlése. |
| Microsoft. Directory/appRoleAssignments/Update | AppRoleAssignments frissítése Azure Active Directoryban. |
| Microsoft. Directory/Contacts/Basic/Update | A névjegyek alapszintű tulajdonságainak frissítése Azure Active Directoryban. |
| Microsoft. Directory/névjegyek/létrehozás | Névjegyek létrehozása Azure Active Directoryban. |
| Microsoft. Directory/Contacts/delete | Névjegyek törlése Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/update | A groups. appRoleAssignments tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/alapszintű/frissítés | Azure Active Directoryban lévő csoportok alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/csoportok/létrehozás | Csoportok létrehozása a Azure Active Directoryban. |
| Microsoft. Directory/csoportok/createAsOwner | Csoportok létrehozása a Azure Active Directoryban. A létrehozó hozzá lett adva az első tulajdonosként, és a létrehozott objektum a Creator 250 létrehozott objektum-kvótájának számít. |
| microsoft.directory/groups/delete | Csoportok törlése Azure Active Directory. |
| Microsoft. Directory/csoportok/hiddenMembers/olvasás | A groups. hiddenMembers tulajdonság olvasása Azure Active Directory. |
| Microsoft. Directory/csoportok/tagok/frissítés | A groups. Members tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/tulajdonosok/frissítés | A groups. owners tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/csoportok/visszaállítás | Csoportok visszaállítása a Azure Active Directoryban. |
| microsoft.directory/groups/settings/update | A groups. Settings tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/felhasználók/appRoleAssignments/Update | A users. appRoleAssignments tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/felhasználók/assignLicense | Licencek kezelése Azure Active Directory felhasználóinak. |
| Microsoft. Directory/felhasználók/alapszintű/frissítés | A Azure Active Directoryban lévő felhasználók alapszintű tulajdonságainak frissítése. |
| Microsoft. Directory/felhasználók/létrehozás | Felhasználók létrehozása Azure Active Directoryban. |
| Microsoft. Directory/felhasználók/törlés | Azure Active Directory felhasználók törlése. |
| Microsoft. Directory/felhasználók/invalidateAllRefreshTokens | Azure Active Directory összes felhasználói frissítési jogkivonatának érvénytelenítése. |
| Microsoft. Directory/felhasználók/kezelő/frissítés | A users. Manager tulajdonság frissítése Azure Active Directoryban. |
| Microsoft. Directory/felhasználók/jelszó/frissítés | Frissítse a Azure Active Directory összes felhasználójának jelszavát. További részletekért tekintse meg az online dokumentációt. |
| Microsoft. Directory/felhasználók/visszaállítás | Azure Active Directory törölt felhasználók visszaállítása. |
| Microsoft. Directory/felhasználók/userPrincipalName/Update | A users. userPrincipalName tulajdonság frissítése Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A Microsoft. Office 365. webporting összes erőforrásának alapszintű tulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Az Office 365 Service Health olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

## <a name="role-template-ids"></a>Szerepkör-sablon azonosítói

A szerepkör-sablon azonosítóit elsősorban Graph API vagy PowerShell-felhasználók használják.

Gráf displayName | Azure Portal megjelenítendő név | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Alkalmazás-rendszergazda | Alkalmazás-rendszergazda | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Alkalmazásfejlesztő | Alkalmazásfejlesztő | CF1C38E5-3621-4004-A7CB-879624DCED7C
Hitelesítés rendszergazdája | Hitelesítési rendszergazda | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure DevOps-rendszergazda | Azure DevOps-rendszergazda | e3973bdf-4987-49ae-837a-ba8e231c7286
Azure Information Protection rendszergazda | Azure Information Protection rendszergazda | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C felhasználói folyamat rendszergazdája | B2C felhasználói folyamat rendszergazdája | 6e591065-9bad-43ed-90f3-e9424366d2f0
B2C felhasználói folyamat attribútum rendszergazda | B2C felhasználói folyamat attribútum rendszergazda | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
B2C IEF kulcskészlet rendszergazda | B2C IEF kulcskészlet rendszergazda | aaf43236-0c0d-4d5f-883a-6955382ac081
B2C IEF szabályzat rendszergazda | B2C IEF szabályzat rendszergazda | 3edaf663-341e-4475-9f94-5c398ef6c070
Számlázási adminisztrátor | Számlázási adminisztrátor | b0f54661-2d74-4c50-afa3-1ec803f12efe
Felhőalkalmazás-rendszergazda | Felhőalkalmazás-rendszergazda | 158c047a-c907-4556-b7ef-446551a6b5f7
Felhőeszköz-rendszergazda | Felhőeszköz-rendszergazda | 7698a772-787b-4ac8-901f-60d6b08affd2
Céges rendszergazda | Globális rendszergazda | 62e90394-69f5-4237-9190-012177145e10
Szabályozási ügyintéző | Szabályozási ügyintéző | 17315797-102d-40b4-93e0-432062caca18
Megfelelőségi adatkezelő | Megfelelőségi adatkezelő | e6d1a23a-da11-4be4-9570-befc86d067a7
Feltételes hozzáférésű rendszergazda | Feltételes hozzáférési rendszergazda | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
CRM-szolgáltatásgazda | Dynamics 365-rendszergazda | 44367163-eba1-44c3-98af-f5787879f96a
Ügyfélszéf-hozzáférési jóváhagyó | Ügyfélszéf hozzáférési jóváhagyó | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Asztali elemzési rendszergazda | Asztali elemzési rendszergazda | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Eszközrendszergazda | Eszköz-rendszergazdák | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Eszköz csatlakoztatása | Eszköz csatlakoztatása | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Eszközkezelők | Eszközkezelők | 2b499bcd-da44-4968-8aec-78e1674fa64d
Eszköz felhasználói | Eszköz felhasználói | d405c6df-0af8-4e3b-95e4-4d06e542189e
Címtárolvasó | Címtárolvasók | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Címtár-szinkronizálási fiókok | Címtár-szinkronizálási fiókok | d29b2b05-8046-44ba-8758-1e26182fcf32
Címtárírók | Címtárírók | 9360feb5-f418-4baa-8175-e2a00bac4301
Exchange-szolgáltatások rendszergazdája | Exchange-rendszergazda | 29232cdf-9323-42fd-ade2-1d097af3e4de
Külső identitás-szolgáltató rendszergazdája | Külső identitás-szolgáltató rendszergazdája | be2f45a1-457d-42af-a067-6ec1fa63bc45
Vendég meghívója | Vendég meghívója | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Ügyfélszolgálati adminisztrátor | Jelszókezelő | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Intune szolgáltatás rendszergazdája | Intune-rendszergazda | 3a2c62db-5318-420d-8d74-23affee5d9d5
Kaizala-rendszergazda | Kaizala-rendszergazda | 74ef975b-6605-40af-a5d2-b9539d836353
Licencadminisztrátor | Licencadminisztrátor | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Lync-szolgáltatások rendszergazdája | Skype Vállalati verzió rendszergazdája | 75941009-915a-4869-abe7-691bff18279e
A Message Center adatvédelmi olvasója | A Message Center adatvédelmi olvasója | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Üzenetközpont-olvasó | Üzenetközpont-olvasó | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
1\. szintű partnertámogatás | Partnerek 1. rétegbeli támogatása | 4ba39ca4-527c-499a-b93d-d9b492c50246
2\. szintű partnertámogatás | Partnerek 2. rétegbeli támogatása | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Jelszókezelő | Jelszókezelő | 966707d0-3269-4727-9be2-8c3a10f19b9d
Power BI-szolgáltatásgazda | Power BI-rendszergazda | a9ea8996-122f-4c74-9520-8edcd192826c
Kiemelt jogosultságú hitelesítés rendszergazdája | Kiemelt jogosultságú hitelesítési rendszergazda | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Kiemelt szerepkörű rendszergazda | Kiemelt szerepkörgazda | e8611ab8-c189-46e8-94e1-60213ab1f814
Jelentésolvasó | Jelentésolvasó | 4a5d8f65-41da-4de4-8968-e035b65339cf
Keresés a Rendszergazdában | Keresés a rendszergazdában | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Keresési szerkesztő | Keresési szerkesztő | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Biztonsági rendszergazda | Biztonsági rendszergazda | 194ae4cb-b126-40b2-bd5b-6091b380977d
Biztonsági operátor | Biztonsági operátor | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Biztonsági olvasó | Biztonsági olvasó | 5d6b6bb7-de71-4623-b4af-96380a352509
Szolgáltatástámogatási rendszergazda | Szolgáltatásadminisztrátor | f023fd81-a637-4b56-95fd-791ac0226033
SharePoint szolgáltatás-rendszergazda | SharePoint-rendszergazda | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Csapatok kommunikációs rendszergazdája | Csapatok kommunikációs rendszergazdája | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Teams-kommunikációs támogatási szakember | Teams-kommunikációs támogatási szakember | f70938a0-fc10-4177-9e90-2178f8765737
Teams-kommunikációs támogatási szakértő | Teams-kommunikációs támogatási szakértő | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Teams-szolgáltatásadminisztrátor | Teams-szolgáltatásadminisztrátor | 69091246-20e8-4a56-aa4d-066075b2a7a8
Felhasználó | Felhasználó | a0b1b346-4d3e-4e8b-98f8-753987be4970
Felhasználóifiók-adminisztrátor | Felhasználói adminisztrátor | fe930be7-5e62-47db-91af-98c3a49a38b1
Munkahelyi eszköz-csatlakoztatás | Munkahelyi eszközök csatlakoztatása | c34f683f-4d5a-4403-affd-6615e00e3a7f

## <a name="deprecated-roles"></a>Elavult szerepkörök

A következő szerepkörök nem használhatók. Elavultak, és a későbbiekben el lesznek távolítva az Azure AD-ből.

* Speciális licencek adminisztrátora
* Eszköz csatlakoztatása
* Eszközkezelők
* Eszköz felhasználói
* Ellenőrzött e-mail című felhasználó-létrehozó
* Postaláda-adminisztrátor
* Munkahelyi eszköz-csatlakoztatás

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni arról, hogyan rendeljen hozzá egy felhasználót egy Azure-előfizetés rendszergazdájához, tekintse meg [a hozzáférés kezelése a RBAC és a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md) című témakört.
* Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* További információk az Azure Active Directory és az Azure-előfizetés kapcsolatáról: [Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directoryhoz?](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
