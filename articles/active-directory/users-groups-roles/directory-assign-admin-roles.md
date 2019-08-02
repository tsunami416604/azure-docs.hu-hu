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
ms.date: 07/17/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5167ba170181bed6dc76d4ca3df79306f432eaf2
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722667"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Rendszergazdai szerepkör engedélyei Azure Active Directory

A Azure Active Directory (Azure AD) használatával korlátozott rendszergazdákat jelölhet ki a kevésbé Kiemelt szerepkörökben lévő Identity Tasks-feladatok kezelésére. A rendszergazdák olyan célokra rendelhetők hozzá, mint a felhasználók hozzáadása vagy módosítása, a rendszergazdai szerepkörök hozzárendelése, a felhasználói jelszavak alaphelyzetbe állítása, a felhasználói licencek kezelése és a tartománynevek kezelése. Az alapértelmezett felhasználói engedélyeket csak az Azure AD felhasználói beállításaiban lehet módosítani.

A globális rendszergazda hozzáfér az összes felügyeleti szolgáltatáshoz. Alapértelmezés szerint az Azure-előfizetésre feliratkozó személy van rendelve a címtár globális rendszergazdai szerepkörrel. Csak a globális rendszergazdák és a Kiemelt szerepkörű rendszergazdák delegálják a rendszergazdai szerepköröket. Az üzleti kockázat csökkentése érdekében javasoljuk, hogy ezt a szerepkört csak néhány személy számára rendeljen a vállalatában.

## <a name="assign-or-remove-administrator-roles"></a>Rendszergazdai szerepkörök kiosztása vagy eltávolítása

Ha meg szeretné tudni, hogyan rendelhet hozzá rendszergazdai szerepköröket egy felhasználóhoz Azure Active Directoryban, tekintse meg [a rendszergazdai szerepkörök megtekintése és társítása a Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Elérhető szerepkörök

A következő rendszergazdai szerepkörök érhetők el:

* **[Alkalmazás rendszergazdája](#application-administrator)** : Az ebben a szerepkörben lévő felhasználók a vállalati alkalmazások, az alkalmazások regisztrációi és az alkalmazásproxy-beállítások összes aspektusát hozhatják létre és kezelhetik. Ez a szerepkör lehetővé teszi a delegált engedélyekhez való hozzájárulást, valamint az Microsoft Graph és az Azure AD Graph alkalmazás engedélyeinek kizárását is. Az ehhez a szerepkörhöz hozzárendelt felhasználók nem lesznek hozzáadva tulajdonosként új alkalmazás-regisztrációk vagy vállalati alkalmazások létrehozásakor.

  <b>Fontos</b>: Ez a szerepkör lehetővé teszi az alkalmazás hitelesítő adatainak kezelését. Az ehhez a szerepkörhöz hozzárendelt felhasználók hitelesítő adatokat adhatnak hozzá egy alkalmazáshoz, és ezekkel a hitelesítő adatokkal megszemélyesítheti az alkalmazás identitását. Ha az alkalmazás identitása hozzáférést kapott a Azure Active Directoryhoz, például felhasználói vagy más objektumok létrehozására vagy frissítésére, akkor a szerepkörhöz hozzárendelt felhasználó elvégezheti ezeket a műveleteket az alkalmazás megszemélyesítése közben. Előfordulhat, hogy az alkalmazás identitásának megszemélyesítése megemelő jogosultságot jelent a felhasználó számára az Azure AD-beli szerepkör-hozzárendelésein keresztül. Fontos tisztában lenni azzal, hogy egy felhasználó az alkalmazás-rendszergazdai szerepkörhöz való hozzárendelésével megszemélyesítheti az alkalmazás identitását.

* **[Alkalmazás fejlesztője](#application-developer)** : Az ebben a szerepkörben lévő felhasználók létrehozhatnak alkalmazás-regisztrációkat, ha a "felhasználók regisztrálhatnak alkalmazásokat" beállítás a nem értékre van állítva. Ez a szerepkör lehetővé teszi az engedély megadását a saját nevében, amikor a "felhasználók beleegyeznek az alkalmazásokkal a vállalati adatok eléréséhez" beállítás értéke nem. Az ehhez a szerepkörhöz hozzárendelt felhasználók új alkalmazás-regisztrációk vagy vállalati alkalmazások létrehozásakor tulajdonosként lesznek hozzáadva.

* **[Hitelesítési rendszergazda](#authentication-administrator)** : Az ezzel a szerepkörrel rendelkező felhasználók a jelszóval nem rendelkező hitelesítő adatokat állíthatnak be vagy állíthatnak vissza, és frissíthetik az összes felhasználó jelszavát. A hitelesítési rendszergazdák megkövetelhetik, hogy a felhasználók újra regisztráljanak a meglévő, nem jelszóval nem rendelkező hitelesítő adatokkal (például MFA vagy pont), és visszavonják **az MFA**-t az eszközön, amely a nem rendszergazdák vagy a felhasználók következő bejelentkezésére kéri az MFA-t csak a következő szerepköröket rendeli hozzá:
  * Hitelesítés rendszergazdája
  * Címtárolvasó
  * Vendég meghívója
  * Üzenetközpont-olvasó
  * Jelentésolvasó

  A hitelesítési rendszergazdai szerepkör jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

  <b>Fontos</b>: Az ezzel a szerepkörrel rendelkező felhasználók megváltoztathatják azoknak a személyeknek a hitelesítő adatait, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz, illetve a Azure Active Directoryon belüli és kívüli kritikus konfigurációhoz. A felhasználók hitelesítő adatainak módosítása azt jelentheti, hogy a felhasználó identitását és engedélyeit feltételezi. Példa:

  * Az alkalmazás regisztrálása és a vállalati alkalmazások tulajdonosai, akik kezelhetik a saját alkalmazások hitelesítő adatait. Ezek az alkalmazások jogosultsági szintű engedélyekkel rendelkezhetnek az Azure AD-ben, és másutt nem kapják meg a hitelesítési rendszergazdákat. Ezen az elérési úton a hitelesítési rendszergazda feltételezheti az alkalmazás tulajdonosának identitását, majd az alkalmazás hitelesítő adatainak frissítésével továbbra is feltételezheti a Kiemelt alkalmazások identitását.
  * Az Azure-előfizetések tulajdonosai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz vagy az Azure-beli kritikus konfigurációhoz.
  * Biztonsági csoport és Office 365 csoport tulajdonosai, akik kezelhetik a csoporttagság kezelését. Ezek a csoportok hozzáférést biztosíthatnak a bizalmas vagy magánjellegű információkhoz, illetve a kritikus konfigurációhoz az Azure AD-ben és máshol.
  * Az Azure AD-n kívül más szolgáltatásokban, például az Exchange Online-ban, az Office biztonsági és megfelelőségi központban és a humán erőforrás-rendszerekben található rendszergazdák.
  * Nem rendszergazdák, például vezetők, jogi tanácsadás és emberi erőforrások alkalmazottai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz.

* **[Azure Information Protection rendszergazda](#azure-information-protection-administrator)** : Az ehhez a szerepkörhöz tartozó felhasználók minden engedéllyel rendelkeznek a Azure Information Protection szolgáltatásban. Ez a szerepkör lehetővé teszi a címkék konfigurálását a Azure Information Protection házirend számára, a védelmi sablonok kezelését és a védelem aktiválását. Ez a szerepkör nem biztosít engedélyeket az Identity Protection Centerben, az Privileged Identity Management, az Office 365 Service Health vagy az Office 365 Biztonsági és megfelelőségi központ figyeléséhez.

* **[B2C felhasználói folyamat rendszergazdája](#b2c-user-flow-administrator)** : Az ezzel a szerepkörrel rendelkező felhasználók B2C-Felhasználókövetés (más néven "beépített" szabályzatokat) hozhatnak létre és kezelhetnek az Azure Portalon. A felhasználói folyamatok létrehozásával vagy szerkesztésével ezek a felhasználók módosíthatják a felhasználói élmény HTML/CSS/JavaScript tartalmát, módosíthatják a felhasználói folyamatok MFA-követelményeit, megváltoztathatják a jogkivonatokat, és módosíthatják a munkamenet beállításait a bérlő összes házirendje számára. Másfelől azonban ez a szerepkör nem teszi lehetővé a felhasználói adat áttekintését, illetve a bérlői sémában található attribútumok módosítását. Az Identity Experience Framework (más néven egyéni) házirendek módosításai a szerepkör hatókörén kívül esnek.

* **[B2C felhasználói folyamat attribútumának rendszergazdája](#b2c-user-flow-attribute-administrator)** : Az ezzel a szerepkörrel rendelkező felhasználók hozzáadhatnak vagy törölhetnek egyéni attribútumokat a bérlő összes felhasználói folyamata számára. Így az ezzel a szerepkörrel rendelkező felhasználók megváltoztathatják vagy hozzáadhatják az új elemeket a végfelhasználói sémához, és befolyásolhatják az összes felhasználói folyamat viselkedését, és közvetetten azt eredményezik, hogy a végfelhasználók milyen adatokat igényelhetnek, és végül jogcímeket küldenek az alkalmazásoknak. Ez a szerepkör nem szerkesztheti a felhasználói folyamatokat.

* **[B2C IEF kulcskészlet rendszergazdája](#b2c-ief-keyset-administrator)** :    A felhasználók létrehozhatják és kezelhetik a jogkivonat-titkosításhoz, a jogkivonat-aláírásokhoz és a jogcím titkosításához és visszafejtéséhez szükséges szabályzatokat és titkos kulcsokat. Ha új kulcsokat ad hozzá a meglévő kulcstárolóhoz, ez a korlátozott rendszergazda szükség szerint átválthatja a titkokat a meglévő alkalmazások befolyásolása nélkül. A felhasználók a létrehozásuk után is láthatják a titkok teljes tartalmát és a lejárat dátumát.
    
  <b>Fontos:</b> ez egy bizalmas szerepkör. A kulcskészlet rendszergazdai szerepkörét körültekintően kell naplózni, és körültekintően kell kiosztani az üzemen belül és az éles környezetben.

* **[B2C IEF házirend rendszergazdája](#b2c-ief-policy-administrator)** : Az ebben a szerepkörben lévő felhasználók létrehozhatnak, olvashatnak, frissíthetnek és törölhetnek minden egyéni szabályzatot a Azure AD B2Cban, így teljes körűen szabályozható a megfelelő Azure AD B2C-bérlő identitás-ellenőrzési keretrendszere. A szabályzatok szerkesztésével a felhasználó közvetlen kapcsolatot hozhat létre külső identitás-szolgáltatókkal, módosíthatja a címtár-sémát, módosíthatja az összes felhasználó felé irányuló tartalmat (HTML, CSS, JavaScript), módosíthatja a követelményeket a hitelesítés befejezéséhez, új felhasználók létrehozásához, küldéshez a külső rendszerekre vonatkozó felhasználói adatok, beleértve a teljes áttelepítést, és az összes felhasználói adat szerkesztése, beleértve a bizalmas mezőket, például a jelszavakat és a telefonszámokat. Ezzel ellentétben ez a szerepkör nem változtathatja meg a titkosítási kulcsokat, vagy szerkesztheti a bérlőn való összevonáshoz használt titkokat.

  <b>Fontos:</b> A B2 IEF szabályzat rendszergazdája rendkívül kényes szerepkör, amelyet az élesben lévő bérlők számára nagyon korlátozott mértékben kell hozzárendelni. Ezeknek a felhasználóknak a tevékenységeit alaposan meg kell vizsgálni, különösen az éles üzemben lévő bérlők esetében.

* **[Számlázási rendszergazda](#billing-administrator)** : Vásárol, kezeli az előfizetéseket és a támogatási jegyeket, és figyeli a szolgáltatás állapotát.

* **[Cloud Application Administrator](#cloud-application-administrator)** : Az ebben a szerepkörben lévő felhasználók ugyanazok az engedélyek, mint az alkalmazás-rendszergazda szerepkör, az alkalmazásproxy felügyeletének lehetősége nélkül. Ez a szerepkör lehetővé teszi a vállalati alkalmazások és az alkalmazások regisztrálásának minden aspektusának létrehozását és felügyeletét. Ez a szerepkör lehetővé teszi a delegált engedélyekhez való hozzájárulást, valamint az Microsoft Graph és az Azure AD Graph alkalmazás engedélyeinek kizárását is. Az ehhez a szerepkörhöz hozzárendelt felhasználók nem lesznek hozzáadva tulajdonosként új alkalmazás-regisztrációk vagy vállalati alkalmazások létrehozásakor.

  <b>Fontos</b>: Ez a szerepkör lehetővé teszi az alkalmazás hitelesítő adatainak kezelését. Az ehhez a szerepkörhöz hozzárendelt felhasználók hitelesítő adatokat adhatnak hozzá egy alkalmazáshoz, és ezekkel a hitelesítő adatokkal megszemélyesítheti az alkalmazás identitását. Ha az alkalmazás identitása hozzáférést kapott a Azure Active Directoryhoz, például felhasználói vagy más objektumok létrehozására vagy frissítésére, akkor a szerepkörhöz hozzárendelt felhasználó elvégezheti ezeket a műveleteket az alkalmazás megszemélyesítése közben. Előfordulhat, hogy az alkalmazás identitásának megszemélyesítése megemelő jogosultságot jelent a felhasználó számára az Azure AD-beli szerepkör-hozzárendelésein keresztül. Fontos tisztában lenni azzal, hogy egy felhasználó a Cloud Application Administrator szerepkörhöz való hozzárendelésével lehetővé válik az alkalmazás identitásának megszemélyesítése.

* **[Felhőalapú eszköz rendszergazdája](#cloud-device-administrator)** : A szerepkörben lévő felhasználók engedélyezheti, letilthatja és törölheti az eszközöket az Azure AD-ben, és beolvashatja a Windows 10 BitLocker-kulcsokat (ha vannak) a Azure Portal. A szerepkör nem biztosít engedélyeket az eszköz egyéb tulajdonságainak kezeléséhez.

* **[Megfelelőségi rendszergazda](#compliance-administrator)** : Az ehhez a szerepkörhöz tartozó felhasználók jogosultak a megfelelőséggel kapcsolatos szolgáltatások kezelésére a Microsoft 365 megfelelőségi központban, Microsoft 365 felügyeleti központban, az Azure-ban és az Office 365 Biztonsági és megfelelőségi központ. A felhasználók az Exchange felügyeleti központban és a Teams & a Skype vállalati felügyeleti központban is kezelhetik az összes funkciót, és támogatási jegyeket hozhatnak létre az Azure-hoz és a Microsoft 365hoz. További információk az [Office 365 rendszergazdai szerepköreivel kapcsolatban](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)érhetők el.

  A | Elvégezhető
  ----- | ----------
  [Microsoft 365 megfelelőségi központ](https://protection.office.com) | A szervezet adatainak védelme és kezelése Microsoft 365 szolgáltatások között<br>Megfelelőségi riasztások kezelése
  [Megfelelőség-kezelő](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | A szervezet szabályozási megfelelőségi tevékenységeinek nyomon követése, kiosztása és ellenőrzése
  [Office 365 Biztonsági és megfelelőségi központ](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Az adatszabályozás kezelése<br>Jogi és adatvizsgálati műveletek végrehajtása<br>Az adattulajdonosi kérelem kezelése
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Az összes Intune-naplózási adatbázis megtekintése
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Csak olvasási jogosultsággal rendelkezik, és képes kezelni a riasztásokat<br>Házirendeket hozhat létre és módosíthat, valamint engedélyezheti a fájlok irányítási műveleteit<br> Megtekintheti az összes beépített jelentést adatkezelés

* **[Megfelelőségi adatkezelési rendszergazda](#compliance-data-administrator)** : Az ezzel a szerepkörrel rendelkező felhasználók a Microsoft 365 megfelelőségi központban, Microsoft 365 felügyeleti központban és az Azure-ban is jogosultak az adatvédelemre és azok nyomon követésére. A felhasználók az Exchange felügyeleti központban, a megfelelőség-kezelőben és a Teams & Skype vállalati felügyeleti központban is kezelhetik az összes funkciót, és támogatási jegyeket hozhatnak létre az Azure-hoz és a Microsoft 365hoz.

  A | Elvégezhető
  ----- | ----------
  [Microsoft 365 megfelelőségi központ](https://protection.office.com) | Megfelelőséggel kapcsolatos szabályzatok monitorozása Microsoft 365 szolgáltatások között<br>Megfelelőségi riasztások kezelése
  [Megfelelőség-kezelő](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | A szervezet szabályozási megfelelőségi tevékenységeinek nyomon követése, kiosztása és ellenőrzése
  [Office 365 Biztonsági és megfelelőségi központ](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Az adatszabályozás kezelése<br>Jogi és adatvizsgálati műveletek végrehajtása<br>Az adattulajdonosi kérelem kezelése
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Az összes Intune-naplózási adatbázis megtekintése
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Csak olvasási jogosultsággal rendelkezik, és képes kezelni a riasztásokat<br>Házirendeket hozhat létre és módosíthat, valamint engedélyezheti a fájlok irányítási műveleteit<br> Megtekintheti az összes beépített jelentést adatkezelés

* **[Feltételes hozzáférésű rendszergazda](#conditional-access-administrator)** : Az ezzel a szerepkörrel rendelkező felhasználók kezelhetik Azure Active Directory feltételes hozzáférési beállításokat.
  > [!NOTE]
  > Az Exchange ActiveSync feltételes hozzáférési szabályzatának Azure-beli üzembe helyezéséhez a felhasználónak globális rendszergazdának is kell lennie.
  
* **[Ügyfélszéf hozzáférési jóváhagyó](#customer-lockbox-access-approver)** : A szervezet [Ügyfélszéf](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) -kérelmeit kezeli. E-mail-értesítéseket kapnak Ügyfélszéf kérelmekről, és a Microsoft 365 felügyeleti központban hagyhatják jóvá és tagadhatják meg a kérelmeket. Emellett be-vagy kikapcsolhatja a Ügyfélszéf funkciót. Csak a globális rendszergazdák állíthatják alaphelyzetbe a szerepkörhöz rendelt személyek jelszavát.
  <!--  This was announced in August of 2018. https://techcommunity.microsoft.com/t5/Security-Privacy-and-Compliance/Customer-Lockbox-Approver-Role-Now-Available/ba-p/223393-->

* **[Asztali elemzési rendszergazda](#desktop-analytics-administrator)** : Az ebben a szerepkörben lévő felhasználók kezelhetik az asztali elemzési és az Office testreszabási & házirend-szolgáltatásait. Az asztali elemzések esetében ez magában foglalja az eszközök leltárának megtekintését, a központi telepítési tervek létrehozását, a központi telepítés és az állapot megtekintését. Az Office testreszabása & házirend-szolgáltatás esetében ez a szerepkör lehetővé teszi a felhasználóknak az Office-házirendek kezelését.

* **[Eszköz rendszergazdája](#device-administrators)** : Ez a szerepkör csak az [eszközbeállítások](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)további helyi rendszergazdája számára érhető el. Az ehhez a szerepkörhöz tartozó felhasználók a helyi gépek rendszergazdái lesznek az Azure Active Directoryval összekapcsolt összes Windows 10-eszközön. Nem tudja kezelni az eszközök objektumait Azure Active Directoryban. 

* **[Directory-olvasók](#directory-readers)** : Ez a szerepkör csak olyan örökölt alkalmazásokhoz rendelhető hozzá, amelyek nem támogatják az [engedélyezési keretrendszert](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Ne rendelje hozzá a felhasználókhoz.

* **[Címtár-szinkronizálási fiókok](#directory-synchronization-accounts)** : Ne használja. Ezt a szerepkört a rendszer automatikusan hozzárendeli a Azure AD Connect szolgáltatáshoz, és semmilyen más használatra nem javasolt vagy nem támogatott.

* **[Címtár-írók](#directory-writers)** : Ez egy örökölt szerepkör, amelyet olyan alkalmazásokhoz kell rendelni, amelyek nem támogatják az [engedélyezési keretrendszert](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Nem rendelhető hozzá egyetlen felhasználóhoz sem.

* **[Dynamics 365 Administrator/CRM-rendszergazda](#crm-service-administrator)** : Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft Dynamics 365 online-on belül, ha a szolgáltatás megtalálható, valamint a támogatási jegyek felügyeletének és a szolgáltatás állapotának figyelésének lehetősége. További információ: [a szolgáltatás-rendszergazdai szerepkör használata a bérlő kezeléséhez](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).
  > [!NOTE] 
  > A Microsoft Graph API, az Azure AD Graph API és az Azure AD PowerShell esetében ez a szerepkör "Dynamics 365 szolgáltatás-rendszergazda" néven azonosítható. A [Azure Portal](https://portal.azure.com)a "Dynamics 365 Administrator".

* **[Exchange-rendszergazda](#exchange-service-administrator)** : Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft Exchange Online-ban, ha van ilyen szolgáltatásuk. Emellett képes az Office 365-csoportok létrehozására és felügyeletére, a támogatási jegyek kezelésére és a szolgáltatás állapotának figyelésére. További információ az [Office 365 rendszergazdai szerepköreiről](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > A Microsoft Graph API-ban, az Azure AD Graph API és az Azure AD PowerShellben ez a szerepkör "Exchange Service Administrator" néven azonosítható. A [Azure Portal](https://portal.azure.com)az "Exchange Administrator". Az [Exchange felügyeleti központban](https://go.microsoft.com/fwlink/p/?LinkID=529144)az "Exchange Online rendszergazdán". 

* **[Külső identitás-szolgáltató rendszergazdája](#external-identity-provider-administrator)** : Ez a rendszergazda kezeli az Azure Active Directory bérlők és a külső identitás-szolgáltatók közötti összevonást. Ezzel a szerepkörrel a felhasználók új identitás-szolgáltatókat adhatnak hozzá, és konfigurálhatják az összes rendelkezésre álló beállítást (például a hitelesítési útvonalat, a szolgáltatás azonosítóját, a hozzárendelt kulcstárolókat). Ez a felhasználó lehetővé teheti a bérlő számára, hogy megbízzon a külső identitás-szolgáltatóktól érkező hitelesítésekben. A végfelhasználói tapasztalatok eredményének következményei a bérlő típusától függenek:
  * Azure Active Directory bérlők alkalmazottai és partnerei számára: A Szövetség (például a Gmail) hozzáadása azonnal hatással lesz az összes olyan vendég meghívóra, amely még nincs beváltva. Lásd: [a Google hozzáadása identitás-szolgáltatóként a B2B vendég felhasználói](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)számára.
  * Azure Active Directory B2C bérlők: Az összevonások (például a Facebook vagy más Azure Active Directory) hozzáadása nem érinti azonnal a végfelhasználói folyamatokat, amíg az identitás-szolgáltató hozzá nem adódik a felhasználói folyamatokban (más néven beépített szabályzat). Lásd: [Microsoft-fiók konfigurálása identitás](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) -szolgáltatóként egy példához. A felhasználói folyamatok módosításához a "B2C felhasználói folyamat rendszergazdája" korlátozott szerepkörre van szükség.

* **[Globális rendszergazda/vállalati rendszergazda](#company-administrator)** : Az ehhez a szerepkörhöz tartozó felhasználók hozzáférhetnek a Azure Active Directory összes felügyeleti funkciójához, valamint olyan szolgáltatásokhoz, amelyek olyan Azure Active Directory identitásokat használnak, mint például a Microsoft 365 Security Center, a Microsoft 365 megfelelőségi központ, az Exchange Online, a SharePoint Online és a Skype vállalati online verzió. A Azure Active Directory bérlőre feliratkozik személy globális rendszergazda lesz. Csak a globális rendszergazdák oszthatnak ki más rendszergazdai szerepköröket. A vállalatnál több globális rendszergazda is lehet. A globális rendszergazdák alaphelyzetbe állíthatják bármely felhasználó és az összes többi rendszergazda jelszavát.

  > [!NOTE]
  > A Microsoft Graph API, az Azure AD Graph API és az Azure AD PowerShell esetében ez a szerepkör a "vállalati rendszergazda" néven azonosítható. Ez a [Azure Portal](https://portal.azure.com)globális rendszergazdája.
  >
  >

* **[Vendég](#guest-inviter)** meghívója: Az ebben a szerepkörben lévő felhasználók kezelhetik Azure Active Directory B2B vendég felhasználói meghívókat, amikor a **tagok** meghívhatják a felhasználói beállítást a nem értékre. További információ a B2B-együttműködésről az [Azure ad B2B együttműködésről](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Nem tartalmaz más engedélyeket.

* **[Segélyszolgálat rendszergazdája](#helpdesk-administrator)** : Az ezzel a szerepkörrel rendelkező felhasználók módosíthatják a jelszavakat, érvénytelenítik a frissítési jogkivonatokat, kezelhetik a szolgáltatási kérelmeket, és figyelik a szolgáltatás állapotát A frissítési jogkivonat érvénytelenítése kényszeríti a felhasználót, hogy jelentkezzen be újra. Az ügyfélszolgálati rendszergazdák alaphelyzetbe állíthatja a jelszavakat, és érvénytelenítheti azokat a más felhasználók frissítési jogkivonatait, akik nem rendszergazdák vagy csak a következő szerepkörökhöz vannak rendelve:
  * Címtárolvasó
  * Vendég meghívója
  * Ügyfélszolgálati adminisztrátor
  * Üzenetközpont-olvasó
  * Jelentésolvasó
  
  <b>Fontos</b>: Az ezzel a szerepkörrel rendelkező felhasználók megváltoztathatják azokat a személyeket, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz, illetve a Azure Active Directoryon belül és kívül is kritikus konfigurációhoz. A felhasználó jelszavának módosítása azt jelentheti, hogy a felhasználó identitását és engedélyeit feltételezi. Példa:
  * Az alkalmazás regisztrálása és a vállalati alkalmazások tulajdonosai, akik kezelhetik a saját alkalmazások hitelesítő adatait. Ezek az alkalmazások jogosultsági szintű engedélyekkel rendelkezhetnek az Azure AD-ben, és máshol nem biztosítanak ügyfélszolgálati rendszergazdának. Ezen az elérési úton egy ügyfélszolgálati rendszergazda képes lehet feltételezni az alkalmazás tulajdonosának identitását, majd az alkalmazás hitelesítő adatainak frissítésével tovább feltételezni egy emelt szintű alkalmazás identitását.
  * Az Azure-előfizetések tulajdonosai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz vagy az Azure-beli kritikus konfigurációhoz.
  * Biztonsági csoport és Office 365 csoport tulajdonosai, akik kezelhetik a csoporttagság kezelését. Ezek a csoportok hozzáférést biztosíthatnak a bizalmas vagy magánjellegű információkhoz, illetve a kritikus konfigurációhoz az Azure AD-ben és máshol.
  * Az Azure AD-n kívül más szolgáltatásokban, például az Exchange Online-ban, az Office biztonsági és megfelelőségi központban és a humán erőforrás-rendszerekben található rendszergazdák.
  * Nem rendszergazdák, például vezetők, jogi tanácsadás és emberi erőforrások alkalmazottai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz.


  > [!NOTE]
  > A rendszergazdai engedélyek delegálása a felhasználók részhalmaza felett és a házirendek a felhasználók egy részhalmazára való alkalmazása a [felügyeleti egységekkel (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units)lehetséges.


  > [!NOTE]
  > Ezt a szerepkört korábban "jelszó-rendszergazdaként" nevezték a [Azure Portal](https://portal.azure.com/). A nevét az Azure AD PowerShellben, az Azure AD Graph API és Microsoft Graph API-ban szereplő nevével megegyező "segélyszolgálat-rendszergazda" névre változtatjuk.

* **[Intune-rendszergazda](#intune-service-administrator)** : Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek Microsoft Intune online-ban, ha a szolgáltatás jelen van. Emellett ez a szerepkör lehetővé teszi a felhasználók és eszközök felügyeletét a házirendek társítása, valamint a csoportok létrehozása és kezelése céljából. További információ a [szerepköralapú adminisztrációs vezérlő (RBAC) és a Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)
  > [!NOTE]
  > A Microsoft Graph API, az Azure AD Graph API és az Azure AD PowerShell esetében ez a szerepkör "Intune szolgáltatás-rendszergazda" néven azonosítható. A [Azure Portal](https://portal.azure.com)"Intune-rendszergazda".
  
 * **[Kaizala-rendszergazda](#kaizala-administrator)** : Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a beállítások Microsoft Kaizala való kezeléséhez, ha a szolgáltatás megtalálható, valamint a támogatási jegyek felügyeletének és a szolgáltatás állapotának figyelésének lehetősége.
Emellett a felhasználó hozzáférhet a bevezetéshez kapcsolódó jelentésekhez & a Kaizala használatát a Kaizala műveletekkel létrehozott szervezeti tagok és üzleti jelentések segítségével. 

* **[Licenc rendszergazdája](#license-administrator)** : Az ebben a szerepkörben lévő felhasználók hozzáadhatják, eltávolíthatják és frissíthetik a licenc-hozzárendeléseket a felhasználók, csoportok (csoporton alapuló licencelés) használatával, és kezelhetik a felhasználók használati helyét. A szerepkör nem biztosít előfizetéseket, nem hozhat létre és kezelhet csoportokat, illetve nem hozhat létre vagy kezelhet felhasználókat a használat helyén kívül. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

* A **[Message Center adatvédelmi olvasója](#message-center-privacy-reader)** : Az ebben a szerepkörben lévő felhasználók az üzenetközpont összes értesítését nyomon tudják követni, beleértve az adatvédelmi üzeneteket is. Az üzenetközpont adatvédelmi olvasói e-mail-értesítéseket kapnak, beleértve az adatvédelemmel kapcsolatos értesítőket is, és lefizethetnek az üzenetközpont beállításaival. Az adatvédelmi üzeneteket csak a globális rendszergazda és az üzenetközpont adatvédelmi olvasója tudja olvasni. Emellett ez a szerepkör a csoportok, tartományok és előfizetések megtekintésére is lehetőséget tartalmaz. Ez a szerepkör nem jogosult a szolgáltatási kérelmek megtekintésére, létrehozására és kezelésére.

* **[Üzenetközpont-olvasó](#message-center-reader)** : Az ebben a szerepkörben lévő felhasználók az [Office 365 Message Centerben](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) ellenőrizhetik az értesítéseket és a tanácsadói állapot frissítéseit, például az Exchange, az Intune és a Microsoft Teams szolgáltatásban konfigurált szolgáltatásokat. Az üzenetsor-olvasó hetente e-mail-kivonatokat fogad a hozzászólások, a frissítések, és megoszthatja az üzenetsor-bejegyzéseket az Office 365-ben. Az Azure AD-ben az ehhez a szerepkörhöz hozzárendelt felhasználók csak olvasási hozzáféréssel rendelkeznek az Azure AD-szolgáltatásokhoz, például a felhasználókhoz és a csoportokhoz. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

* **[Partneri Tier1-támogatás](#partner-tier1-support)** : Ne használja. Ez a szerepkör elavult, és a jövőben el lesz távolítva az Azure AD-ből. Ez a szerepkör kis számú Microsoft-viszonteladói partner általi használatra készült, és nem általános használatra készült.

* **[Partneri szint-támogatás](#partner-tier2-support)** : Ne használja. Ez a szerepkör elavult, és a jövőben el lesz távolítva az Azure AD-ből. Ez a szerepkör kis számú Microsoft-viszonteladói partner általi használatra készült, és nem általános használatra készült.

* **[Jelszó-rendszergazda](#password-administrator)** : Az ezzel a szerepkörrel rendelkező felhasználók korlátozottan kezelhetik a jelszavakat. Ez a szerepkör nem teszi lehetővé a szolgáltatási kérelmek felügyeletét vagy a szolgáltatás állapotának figyelését. A jelszó-rendszergazdák alaphelyzetbe állíthatják más felhasználók jelszavát, akik nem rendszergazdák vagy csak a következő szerepkörök tagjai:
  * Címtárolvasó
  * Vendég meghívója
  * Jelszókezelő

* **[Power bi rendszergazda](#power-bi-service-administrator)** : Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft Power BI-ban, ha van ilyen szolgáltatásuk, ezenkívül kezelhetik a támogatási jegyeket, és figyelhetik a szolgáltatás állapotát. További információ [a Power bi rendszergazdai szerepkör megismeréséhez](https://docs.microsoft.com/power-bi/service-admin-role).
  > [!NOTE]
  > A Microsoft Graph API, az Azure AD Graph API és az Azure AD PowerShell esetében ez a szerepkör "Power BI szolgáltatás-rendszergazda" néven azonosítható. A [Azure Portal](https://portal.azure.com)"Power bi rendszergazda".

* **[Kiemelt jogosultságú hitelesítés rendszergazdája](#privileged-authentication-administrator)** : Az ezzel a szerepkörrel rendelkező felhasználók az összes felhasználóra vonatkozóan állíthatnak be vagy állíthatnak vissza nem jelszavas hitelesítő adatokat, beleértve a globális rendszergazdákat is, és frissíthetik az összes felhasználó jelszavát. A Kiemelt jogosultságú hitelesítő rendszergazdák kényszerítheti a felhasználókat, hogy újra regisztráljanak a meglévő, nem jelszóval nem rendelkező hitelesítő adatokkal (pl. MFA, pont), és visszavonják az eszközön az MFA megjegyzésének visszavonását, az összes felhasználó következő bejelentkezésekor az MFA-t kérik. A Kiemelt hitelesítő rendszergazdák a következőket tehetik:
  * A felhasználók újbóli regisztrálásának kényszerítése a meglévő, nem jelszavas hitelesítő adatokkal (például MFA, pont)
  * A "Remember MFA on the Device" utasítás visszavonása az MFA-ra a következő bejelentkezéskor

* **[Kiemelt szerepkörű rendszergazda](#privileged-role-administrator)** : Az ezzel a szerepkörrel rendelkező felhasználók a szerepkör-hozzárendeléseket kezelhetik Azure Active Directoryban, valamint a Azure AD Privileged Identity Managementon belül is. Emellett ez a szerepkör lehetővé teszi Privileged Identity Management és felügyeleti egységek valamennyi aspektusának kezelését.

  <b>Fontos</b>: Ez a szerepkör lehetővé teszi az összes Azure AD-szerepkör hozzárendelésének kezelését, beleértve a globális rendszergazdai szerepkört is. Ez a szerepkör nem tartalmaz más, az Azure AD-ben található, például felhasználók létrehozásához vagy frissítéséhez szükséges jogosultságokkal rendelkező képességeket. Az ehhez a szerepkörhöz hozzárendelt felhasználók azonban további jogosultságokat is biztosíthatnak a további szerepkörök hozzárendelésével.

* **[Jelentések olvasója](#reports-reader)** : Az ezzel a szerepkörrel rendelkező felhasználók megtekinthetik a használati jelentéskészítési adatokat és a jelentések irányítópultot Microsoft 365 felügyeleti központban, valamint a Power BI bevezetésének környezeti csomagjában. Emellett a szerepkör hozzáférést biztosít a bejelentkezési jelentésekhez és tevékenységekhez az Azure AD-ben, és a Microsoft Graph jelentési API által visszaadott adat. A jelentés-olvasó szerepkörhöz hozzárendelt felhasználó csak a megfelelő használati és bevezetési metrikákat érheti el. Nem rendelkeznek rendszergazdai jogosultsággal a beállítások konfigurálásához, illetve a termékspecifikus felügyeleti központok, például az Exchange eléréséhez. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

* **[Keresési rendszergazda](#search-administrator)** : Az ebben a szerepkörben lévő felhasználók teljes hozzáféréssel rendelkeznek a Microsoft 365 felügyeleti központban található összes Microsoft keresési felügyeleti szolgáltatáshoz. A rendszergazdák a keresési rendszergazdákat és a keresési szerkesztői szerepköröket delegálják a felhasználók számára, valamint tartalmat hozhatnak létre és kezelhetnek, például a könyvjelzőket, a Q &t és a helyet. Emellett ezek a felhasználók megtekinthetik az üzenetközpont, figyelheti a szolgáltatás állapotát és szolgáltatási kérelmeket hozhatnak létre.

* **[Keresési szerkesztő](#search-editor)** : Az ebben a szerepkörben lévő felhasználók létrehozhatnak, kezelhetnek és törölhetnek tartalmakat a Microsoft Search szolgáltatáshoz a Microsoft 365 felügyeleti központban, beleértve a könyvjelzőket, a Q & a-t és a helyet.

* **[Biztonsági rendszergazda](#security-administrator)** : Az ehhez a szerepkörhöz tartozó felhasználók a Microsoft 365 Security Center, a Azure Active Directory Identity Protection, a Azure Information Protection és az Office 365 Biztonsági és megfelelőségi központ biztonsággal kapcsolatos funkcióinak kezelésére jogosultak. Az Office 365-engedélyekkel kapcsolatos további információk [az office 365 biztonsági és megfelelőségi központ engedélyekben](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)találhatók.
  
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

* **[Biztonsági operátor](#security-operator)** : Az ezzel a szerepkörrel rendelkező felhasználók kezelhetik a riasztásokat, és globális olvasási hozzáféréssel rendelkeznek a biztonsággal kapcsolatos funkcióhoz, beleértve a Microsoft 365 Security Center, a Azure Active Directory, az Identity Protection, a Privileged Identity Management és az Office 365 összes adatát is. Biztonsági és megfelelőségi központ. Az Office 365-engedélyekkel kapcsolatos további információk [az office 365 biztonsági és megfelelőségi központ engedélyekben](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center)találhatók.

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
<!--* **[Security Operator](#security-operator)**: Users with this role can manage alerts and have global read-only access on security-related feature, including all information in Microsoft 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management.-->

* **[Biztonsági olvasó](#security-reader)** : Az ehhez a szerepkörhöz tartozó felhasználók globális olvasási hozzáféréssel rendelkeznek a biztonsággal kapcsolatos funkcióhoz, beleértve a Microsoft 365 Security Center, a Azure Active Directory, az Identity Protection, a Privileged Identity Management, valamint az Azure Active olvasásának lehetőségét. Címtár-bejelentkezési jelentések és naplók, valamint az Office 365 Biztonsági és megfelelőségi központ. Az Office 365-engedélyekkel kapcsolatos további információk [az office 365 biztonsági és megfelelőségi központ engedélyekben](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)találhatók.

  A | Elvégezhető
  --- | ---
  [Microsoft 365 Security Center](https://protection.office.com) | A biztonsággal kapcsolatos házirendek megtekintése Microsoft 365 szolgáltatások között<br>Biztonsági fenyegetések és riasztások megtekintése<br>Jelentések megtekintése
  Identity Protection Center | A biztonsági funkciókkal kapcsolatos összes biztonsági jelentés és beállítás olvasása<br><ul><li>Levélszemét elleni<li>Encryption<li>Adatveszteség-megelőzés<li>Kártevők elleni<li>Komplex veszélyforrások elleni védelem<li>Adathalászat elleni védekezés<li>Mailflow-szabályok
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | A csak olvasási hozzáféréssel rendelkezik az Azure AD PIM felületén található összes információhoz: Szabályzatok és jelentések az Azure AD szerepkör-hozzárendelésekhez, a biztonsági felülvizsgálatokhoz és a jövőbeli olvasási hozzáféréshez a szabályzat adataihoz és jelentéseihez az Azure AD szerepkör-hozzárendelésen kívüli forgatókönyvekhez.<br>**Nem lehet** regisztrálni az Azure ad PIM-re, és nem végezheti el a módosításokat. A PIM-portálon vagy a PowerShellen keresztül a szerepkörhöz tartozó valaki további szerepköröket (például globális rendszergazda vagy Kiemelt szerepkörű rendszergazda) is aktiválhat, ha a felhasználó jogosult számukra.
  [Office 365 Biztonsági és megfelelőségi központ](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Biztonsági szabályzatok megtekintése<br>Biztonsági fenyegetések megtekintése és kivizsgálása<br>Jelentések megtekintése
  Windows Defender ATP és EDR | Riasztások megtekintése és kivizsgálása
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | A felhasználó, az eszköz, a beléptetés, a konfiguráció és az alkalmazás adatainak megtekintése. Nem lehet módosítani az Intune-t.
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Csak olvasási jogosultsággal rendelkezik, és képes kezelni a riasztásokat
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Megtekintheti a javaslatokat és riasztásokat, megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, de nem végezhet módosításokat
  [Office 365 szolgáltatás állapota](https://docs.microsoft.com/office365/enterprise/view-service-health) | Az Office 365-szolgáltatások állapotának megtekintése

* **[Szolgáltatás-támogatási rendszergazda](#service-support-administrator)** : Az ezzel a szerepkörrel rendelkező felhasználók megnyithatja a Microsoft Azure és az Office 365 Services támogatási kérelmeit, és megtekintheti a szolgáltatás irányítópultját és az üzenetközpont a [Azure Portal](https://portal.azure.com) és [Microsoft 365 felügyeleti központban](https://admin.microsoft.com). További információ az [Office 365 rendszergazdai szerepköreiről](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > A Microsoft Graph API, az Azure AD Graph API és az Azure AD PowerShell esetében ez a szerepkör "szolgáltatás-támogatási rendszergazdaként" azonosítható. Ez a [Azure Portal](https://portal.azure.com), a [Microsoft 365 felügyeleti központ](https://admin.microsoft.com)és az Intune-portál "szolgáltatás rendszergazdája".

* **[SharePoint-rendszergazda](#sharepoint-service-administrator)** : Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft SharePoint Online-ban, ha a szolgáltatás megtalálható, valamint az Office 365-csoportok létrehozását és felügyeletét, a támogatási jegyek kezelését és a szolgáltatás állapotának figyelését. További információ az [Office 365 rendszergazdai szerepköreiről](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > A Microsoft Graph API, az Azure AD Graph API és az Azure AD PowerShell esetében ez a szerepkör "SharePoint szolgáltatás-rendszergazdaként" azonosítható. A [Azure Portal](https://portal.azure.com)a "SharePoint Administrator".

* **[Skype vállalati verzió/Lync-rendszergazda](#lync-service-administrator)** : Az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft Skype vállalati verziójában, ha a szolgáltatás megtalálható, valamint a Skype-specifikus felhasználói attribútumok kezelése a Azure Active Directoryban. Emellett ez a szerepkör lehetővé teszi a támogatási jegyek felügyeletét és a szolgáltatás állapotának figyelését, valamint a csapatok és a Skype vállalati felügyeleti központ elérését. A fióknak a csapatoknak is licenccel kell rendelkeznie, vagy nem futtathat Team PowerShell-parancsmagokat. További információ [a Skype for Business-rendszergazdai szerepkörrel és a](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) Teams licencelési információkkal kapcsolatban a [Skype vállalati verzió és a Microsoft Teams bővítmény licencelése](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing) szolgáltatásban

  > [!NOTE]
  > A Microsoft Graph API-ban, az Azure AD Graph API és az Azure AD PowerShellben ez a szerepkör "Lync szolgáltatás-rendszergazda" néven azonosítható. A [Azure Portal](https://portal.azure.com/)"Skype vállalati rendszergazda".

* **[Csapatok rendszergazdája](#teams-service-administrator)** : A szerepkör felhasználói a Microsoft Teams munkaterhelésének minden aspektusát felügyelhetik a Microsoft csapatainak & a Skype vállalati felügyeleti központ és a megfelelő PowerShell-modulok használatával. Ez többek között a telefonos, üzenetküldési, értekezlet-és a csapatokhoz kapcsolódó felügyeleti eszközökre is kiterjed. Ez a szerepkör Emellett lehetővé teszi az Office 365-csoportok létrehozását és kezelését, a támogatási jegyek kezelését és a szolgáltatás állapotának figyelését.
  > [!NOTE]
  > A Microsoft Graph API-ban, az Azure AD Graph API és az Azure AD PowerShellben ez a szerepkör "csapatok szolgáltatás-rendszergazdája" néven azonosítható. A [Azure Portal](https://portal.azure.com)a "Teams Administrator".

* **[Csapatok kommunikációs rendszergazdája](#teams-communications-administrator)** : Az ebben a szerepkörben lévő felhasználók kezelhetik a Microsoft Teams munkaterhelésének a hang& telefonos szolgáltatással kapcsolatos aspektusait. Ez magában foglalja a telefonszám-hozzárendelés, a hang-és értekezlet-szabályzatok felügyeleti eszközeit, valamint a Call Analytics eszközkészlet teljes hozzáférését.

* **[Csapat kommunikációs támogató mérnök](#teams-communications-support-engineer)** : Az ebben a szerepkörben lévő felhasználók a Microsoft Teams szolgáltatásban a Skype for Business felügyeleti központban a & Microsoft Teams felhasználói hívás hibaelhárítási eszközeivel végezhetik el a kommunikációval & kapcsolatos problémákat. Az ebben a szerepkörben lévő felhasználók megtekinthetik a teljes hívási rekord adatait az összes érintett résztvevő számára. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

* **[Csapat kommunikációs támogatási szakértő](#teams-communications-support-specialist)** : Az ebben a szerepkörben lévő felhasználók a Microsoft Teams szolgáltatásban a Skype for Business felügyeleti központban a & Microsoft Teams felhasználói hívás hibaelhárítási eszközeivel végezhetik el a kommunikációval & kapcsolatos problémákat. Az ebben a szerepkörben lévő felhasználók csak az adott felhasználóra vonatkozó hívásban tekinthetik meg a felhasználói adatokat. Ez a szerepkör nem rendelkezik hozzáféréssel a támogatási jegyek megtekintéséhez, létrehozásához és kezeléséhez.

* **[Felhasználói rendszergazda](#user-administrator)** : Az ezzel a szerepkörrel rendelkező felhasználók létrehozhatnak felhasználókat, és kezelhetik a felhasználók minden aspektusát bizonyos korlátozásokkal (lásd alább), és frissíthetik a jelszó-elévülési házirendeket. Emellett az ezzel a szerepkörrel rendelkező felhasználók az összes csoportot is létrehozhatják és kezelhetik. Ez a szerepkör magában foglalja a felhasználói nézetek létrehozását és kezelését, a támogatási jegyek kezelését és a szolgáltatás állapotának figyelését is.

  | | |
  | --- | --- |
  |Általános engedélyek|<p>Felhasználók és csoportok létrehozása</p><p>Felhasználói nézetek létrehozása és kezelése</p><p>Office-támogatási jegyek kezelése<p>Jelszó-elévülési szabályzatok frissítése|
  |<p>Minden felhasználónál, beleértve az összes rendszergazdát</p>|<p>Licencek kezelése</p><p>Az összes felhasználói tulajdonság kezelése az egyszerű felhasználónév kivételével</p>
  |Csak olyan felhasználóknál, akik nem rendszergazdák vagy a következő korlátozott rendszergazdai szerepkörök valamelyikében vannak:<ul><li>Címtárolvasó<li>Vendég meghívója<li>Ügyfélszolgálati adminisztrátor<li>Üzenetközpont-olvasó<li>Jelentésolvasó<li>Felhasználói rendszergazda|<p>Törlés és visszaállítás</p><p>Letiltás és engedélyezés</p><p>Frissítési tokenek érvénytelenítése</p><p>Az összes felhasználói tulajdonság kezelése, beleértve az egyszerű felhasználónevet</p><p>Másik jelszó kérése</p><p>Eszköz kulcsainak frissítése</p>
  
  <b>Fontos</b>: Az ezzel a szerepkörrel rendelkező felhasználók megváltoztathatják azokat a személyeket, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz, illetve a Azure Active Directoryon belül és kívül is kritikus konfigurációhoz. A felhasználó jelszavának módosítása azt jelentheti, hogy a felhasználó identitását és engedélyeit feltételezi. Példa:
  * Az alkalmazás regisztrálása és a vállalati alkalmazások tulajdonosai, akik kezelhetik a saját alkalmazások hitelesítő adatait. Ezek az alkalmazások jogosultsági szintű engedélyekkel rendelkezhetnek az Azure AD-ben, és máshol nem kapnak felhasználói rendszergazdákat. Ezen az elérési úton a felhasználói rendszergazda feltételezheti az alkalmazás tulajdonosának identitását, majd az alkalmazáshoz tartozó hitelesítő adatok frissítésével továbbra is feltételezheti a Kiemelt alkalmazások identitását.
  * Az Azure-előfizetések tulajdonosai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz vagy az Azure-beli kritikus konfigurációhoz.
  * Biztonsági csoport és Office 365 csoport tulajdonosai, akik kezelhetik a csoporttagság kezelését. Ezek a csoportok hozzáférést biztosíthatnak a bizalmas vagy magánjellegű információkhoz, illetve a kritikus konfigurációhoz az Azure AD-ben és máshol.
  * Az Azure AD-n kívül más szolgáltatásokban, például az Exchange Online-ban, az Office biztonsági és megfelelőségi központban és a humán erőforrás-rendszerekben található rendszergazdák.
  * Nem rendszergazdák, például vezetők, jogi tanácsadás és emberi erőforrások alkalmazottai, akik hozzáférhetnek a bizalmas vagy magánjellegű információkhoz.

## <a name="role-permissions"></a>Szerepkör-engedélyek
Az alábbi táblázatok ismertetik az egyes szerepkörökhöz Azure Active Directory adott engedélyeket. Egyes szerepkörök további engedélyekkel rendelkezhetnek a Azure Active Directoryon kívüli Microsoft-szolgáltatásokban.

### <a name="application-administrator"></a>Alkalmazás-rendszergazda
Az alkalmazásregisztrációkkal és a vállalati alkalmazásokkal kapcsolatos összes létrehozási és felügyeleti jogosultsággal rendelkezik.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. Directory/alkalmazások/célközönség/frissítés | Az applications.audience tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/authentication/update | Az applications.authentication tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/alkalmazások/alap/frissítés | Az Application objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/alkalmazások/létrehozás | Application objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/applications/credentials/update | Az applications.credentials tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/delete | Az Application objektumok törlése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/alkalmazások/tulajdonosok/frissítés | Az applications.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/permissions/update | Az applications.permissions tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/policies/update | A applications.policies tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/create | AppRoleAssignment objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/read | Az AppRoleAssignment objektumok olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/update | Az AppRoleAssignment objektumok frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/delete | Az AppRoleAssignment objektumok törlése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/auditLogs/allProperties/READ | Az Azure Active Directorybeli auditnaplók összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | A policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | A policies.applicationConfiguration tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Szabályzatok törlése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/applicationConfiguration/tulajdonosok/olvasás | A policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/applicationConfiguration/tulajdonosok/frissítés | A policies.applicationConfiguration tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | A policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | A servicePrincipals.appRoleAssignedTo tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | A servicePrincipals.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/audience/update | A servicePrincipals.audience tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/authentication/update | A servicePrincipals.authentication tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/basic/update | A ServicePrincipal objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/create | A servicePrincipals létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/credentials/update | A servicePrincipals.credentials tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/delete | A servicePrincipals törlése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/update | A servicePrincipals.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/permissions/update | A servicePrincipals.permissions tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/update | A servicePrincipals.policies tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/signInReports/allProperties/READ | Az Azure Active Directorybeli bejelentkezési jelentések összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="application-developer"></a>Alkalmazásfejlesztő
A "felhasználók regisztrálhatnak alkalmazásokat" beállítástól független alkalmazás-regisztrációkat hozhat létre.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. Directory/alkalmazások/createAsOwner | Application objektumok létrehozása az Azure Active Directoryban. A létrehozó hozzá lett adva az első tulajdonosként, és a létrehozott objektum a Creator 250 létrehozott objektum-kvótájának számít. |
| Microsoft. HRE. Directory/appRoleAssignments/createAsOwner | AppRoleAssignment objektumok létrehozása az Azure Active Directoryban. A létrehozó hozzá lett adva az első tulajdonosként, és a létrehozott objektum a Creator 250 létrehozott objektum-kvótájának számít. |
| Microsoft. HRE. Directory/Oauth2permissiongrant objektumok/createAsOwner | Az oAuth2PermissionGrants létrehozása az Azure Active Directoryban. A létrehozó hozzá lett adva az első tulajdonosként, és a létrehozott objektum a Creator 250 létrehozott objektum-kvótájának számít. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | A servicePrincipals létrehozása az Azure Active Directoryban. A létrehozó hozzá lett adva az első tulajdonosként, és a létrehozott objektum a Creator 250 létrehozott objektum-kvótájának számít. |

### <a name="authentication-administrator"></a>Hitelesítés rendszergazdája
A nem rendszergazda felhasználók számára engedélyezett a hitelesítési módszer adatainak megtekintése, beállítása és alaphelyzetbe állítása.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. Directory/felhasználók/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/strongAuthentication/update | Az erős hitelesítési tulajdonságok, például az MFA hitelesítő adatainak frissítése. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| Microsoft. HRE. Directory/felhasználók/jelszó/frissítés | Az Office 365-szervezetben lévő összes felhasználó jelszavának frissítése. További részletekért tekintse meg az online dokumentációt. |

### <a name="azure-information-protection-administrator"></a>Azure Information Protection rendszergazda
A Azure Information Protection szolgáltatás összes aspektusát képes kezelni.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Az Azure Information Protection teljes körű felügyelete. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="b2c-user-flow-administrator"></a>B2C felhasználói folyamat rendszergazda
A felhasználói folyamatok összes aspektusának létrehozása és kezelése.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. B2C/userFlows/allTasks | Felhasználói folyamatok olvasása és konfigurálása Azure Active Directory B2Cban. |

### <a name="b2c-user-flow-attribute-administrator"></a>B2C felhasználói folyamat attribútum rendszergazda
Az összes felhasználói folyamat számára elérhető attribútum-séma létrehozása és kezelése.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. B2C/userAttributes/allTasks | A Azure Active Directory B2C felhasználói attribútumainak olvasása és konfigurálása. |

### <a name="b2c-ief-keyset-administrator"></a>B2C IEF kulcskészlet rendszergazda
Az identitási élmény keretrendszerében kezelheti az összevonás és a titkosítás titkait.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. B2C/trustFramework/alapkészletek/allTasks | A kulcs-készletek olvasása és konfigurálása Azure Active Directory B2Cban. |

### <a name="b2c-ief-policy-administrator"></a>B2C IEF szabályzat rendszergazda
Megbízhatósági keretrendszer-szabályzatok létrehozása és kezelése az Identity Experience Framework-ben.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. B2C/trustFramework/policies/allTasks | Egyéni házirendek olvasása és konfigurálása Azure Active Directory B2Cban. |

### <a name="billing-administrator"></a>Számlázási adminisztrátor
Számlázással kapcsolatos általános feladatokat hajthat végre, például frissítheti a fizetési adatokat.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. Directory/szervezet/alapszintű/frissítés | Az Organization objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.commerce.billing/allEntities/allTasks | Jogosultság az Office 365 számlázásának teljes körű felügyeletére. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |


### <a name="cloud-application-administrator"></a>Felhőalkalmazás-rendszergazda
Az Application Proxy kivételével az alkalmazásregisztrációkkal és a vállalati alkalmazásokkal kapcsolatos összes létrehozási és felügyeleti jogosultsággal rendelkezik.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. Directory/alkalmazások/célközönség/frissítés | Az applications.audience tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/authentication/update | Az applications.authentication tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/alkalmazások/alap/frissítés | Az Application objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/alkalmazások/létrehozás | Application objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/applications/credentials/update | Az applications.credentials tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/delete | Az Application objektumok törlése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/alkalmazások/tulajdonosok/frissítés | Az applications.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/permissions/update | Az applications.permissions tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/applications/policies/update | A applications.policies tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/create | AppRoleAssignment objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/update | Az AppRoleAssignment objektumok frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/delete | Az AppRoleAssignment objektumok törlése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/auditLogs/allProperties/READ | Az Azure Active Directorybeli auditnaplók összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.aad.directory/policies/applicationConfiguration/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | A policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | A policies.applicationConfiguration tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Szabályzatok törlése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/applicationConfiguration/tulajdonosok/olvasás | A policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/applicationConfiguration/tulajdonosok/frissítés | A policies.applicationConfiguration tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | A policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | A servicePrincipals.appRoleAssignedTo tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | A servicePrincipals.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/audience/update | A servicePrincipals.audience tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/authentication/update | A servicePrincipals.authentication tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/basic/update | A ServicePrincipal objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/create | A servicePrincipals létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/credentials/update | A servicePrincipals.credentials tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/delete | A servicePrincipals törlése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/update | A servicePrincipals.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/permissions/update | A servicePrincipals.permissions tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/update | A servicePrincipals.policies tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/signInReports/allProperties/READ | Az Azure Active Directorybeli bejelentkezési jelentések összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="cloud-device-administrator"></a>Felhőeszköz-rendszergazda
Teljes körű jogosultság az eszközök Azure AD-beli kezelésére.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. Directory/auditLogs/allProperties/READ | Az Azure Active Directorybeli auditnaplók összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | A devices.bitLockerRecoveryKeys tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/eszközök/törlés | A Device objektumok törlése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/eszközök/letiltás | Device objektumok letiltása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/eszközök/engedélyezés | Eszközök engedélyezése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/signInReports/allProperties/READ | Az Azure Active Directorybeli bejelentkezési jelentések összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="company-administrator"></a>Céges rendszergazda
Az Azure AD-identitásokat használó Azure AD- és Microsoft-szolgáltatásokkal kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a következőben: microsoft.aad.cloudAppSecurity. |
| Microsoft. HRE. Directory/Administrativeunit/allProperties/allTasks | AdministrativeUnit objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/alkalmazások/allProperties/allTasks | Application objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/appRoleAssignments/allProperties/allTasks | AppRoleAssignment objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/auditLogs/allProperties/READ | Az Azure Active Directorybeli auditnaplók összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| Microsoft. HRE. Directory/Contacts/allProperties/allTasks | Contact objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/szerződések/allProperties/allTasks | Contract objektumok létrehozása és törlése, illetve az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/eszközök/allProperties/allTasks | Device objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | DirectoryRole objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | DirectoryRoleTemplate objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/domains/allProperties/allTasks | Domain objektumok létrehozása és törlése, illetve az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/allProperties/allTasks | Group objektumok létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/groupSettings/allProperties/allTasks | GroupSetting objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | GroupSettingTemplate objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/loginTenantBranding/allProperties/allTasks | LoginTenantBranding objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/Oauth2permissiongrant objektumok/allProperties/allTasks | OAuth2PermissionGrant objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/szervezet/allProperties/allTasks | Organization objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/allProperties/allTasks | Policy objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/roleAssignments/allProperties/allTasks | RoleAssignment objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/roleDefinitions/allProperties/allTasks | A roleDefinitions létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | ScopedRoleMembership objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/serviceAction/activateService | Végrehajthatja az Activateservice szolgáltatásműveletet az Azure Active Directoryban |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | A Disabledirectoryfeature szolgáltatásművelet végrehajtása az Azure Active Directoryban |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Végrehajthatja az Enabledirectoryfeature szolgáltatásműveletet az Azure Active Directoryban |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | A Getavailableextentionproperties szolgáltatásművelet végrehajtásaz az Azure Active Directoryban |
| Microsoft. HRE. Directory/servicePrincipals/allProperties/allTasks | ServicePrincipal objektumok létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/signInReports/allProperties/READ | Az Azure Active Directorybeli bejelentkezési jelentések összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| Microsoft. HRE. Directory/subscribedSkus/allProperties/allTasks | A subscribedSkus létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/allProperties/allTasks | User objektumok létrehozása és törlése, illetve az összes tulajdonság olvasása és frissítése az Azure Active Directoryban. |
| microsoft.aad.directorySync/allEntities/allTasks | Az összes művelet végrehajtása az Azure AD Connectben. |
| microsoft.aad.identityProtection/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a következőben: microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Az összes erőforrás olvasása a következőben: microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | A microsoft.azure.advancedThreatProtection összes erőforrásának olvasása. |
| microsoft.azure.informationProtection/allEntities/allTasks | Az Azure Information Protection teljes körű felügyelete. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.commerce.billing/allEntities/allTasks | Jogosultság az Office 365 számlázásának teljes körű felügyeletére. |
| microsoft.intune/allEntities/allTasks | Az Intune teljes körű felügyelete. |
| microsoft.office365.complianceManager/allEntities/allTasks | Az Office 365 Megfelelőségkezelő teljes körű felügyelete |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Az Asztali elemzés teljes körű felügyelete. |
| microsoft.office365.exchange/allEntities/allTasks | Az Exchange Online teljes körű felügyelete. |
| microsoft.office365.lockbox/allEntities/allTasks | Az Office 365 Ügyfélszéf teljes körű felügyelete |
| microsoft.office365.messageCenter/messages/read | Read messages in microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Read securityMessages in microsoft.office365.messageCenter. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Az Office 365 Védelmi központ teljes körű felügyelete. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a következőben: microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.sharepoint/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a következőben: microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype Vállalati online verzió teljes körű felügyelete. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.usageReports/allEntities/read | Az Office 365-beli használati jelentések olvasása. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Jogosultság a Dynamics 365 számlázásának teljes körű felügyeletére. |
| microsoft.powerApps.powerBI/allEntities/allTasks | A Power BI teljes körű felügyelete. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | A microsoft.windows.defenderAdvancedThreatProtection összes erőforrásának olvasása. |

### <a name="compliance-administrator"></a>Szabályozási ügyintéző
Megtekintheti és kezelheti a megfelelőségi konfigurációt és jelentéseket az Azure AD-ban és az Office 365-ben.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.complianceManager/allEntities/allTasks | Az Office 365 Megfelelőségkezelő teljes körű felügyelete |
| microsoft.office365.exchange/allEntities/allTasks | Az Exchange Online teljes körű felügyelete. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.sharepoint/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a következőben: microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype Vállalati online verzió teljes körű felügyelete. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="compliance-data-administrator"></a>Megfelelőségi adatkezelő
A megfelelőségi tartalom létrehozása és kezelése.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Microsoft Cloud App Security olvasása és konfigurálása. |
| microsoft.azure.informationProtection/allEntities/allTasks | Az Azure Information Protection teljes körű felügyelete. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.complianceManager/allEntities/allTasks | Az Office 365 Megfelelőségkezelő teljes körű felügyelete |
| microsoft.office365.exchange/allEntities/allTasks | Az Exchange Online teljes körű felügyelete. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.sharepoint/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a következőben: microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype Vállalati online verzió teljes körű felügyelete. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="conditional-access-administrator"></a>Feltételes hozzáférésű rendszergazda
Kezelheti a feltételes hozzáférési képességeket.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. Directory/házirendek/conditionalAccess/Basic/READ | A policies.conditionalAccess tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | A policies.conditionalAccess tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/conditionalAccess/Create | Szabályzatok létrehozása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/conditionalAccess/delete | Szabályzatok törlése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/conditionalAccess/tulajdonosok/olvasás | A policies.conditionalAccess tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/conditionalAccess/tulajdonosok/frissítés | A policies.conditionalAccess tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/conditionalAccess/policiesAppliedTo/READ | A policies.conditionalAccess tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | A policies.conditionalAccess tulajdonság frissítése az Azure Active Directoryban. |

### <a name="crm-service-administrator"></a>CRM-szolgáltatásgazda
A Dynamics 365 termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Jogosultság a Dynamics 365 számlázásának teljes körű felügyeletére. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="customer-lockbox-access-approver"></a>Ügyfélszéf-hozzáférési jóváhagyó
Jóváhagyhatja a szervezeti ügyféladatok elérésére vonatkozó Microsoft-támogatási kérelmeket.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.lockbox/allEntities/allTasks | Az Office 365 Ügyfélszéf teljes körű felügyelete |

### <a name="desktop-analytics-administrator"></a>Asztali elemzési rendszergazda
Felügyelheti az asztali elemzési és az Office testreszabási & házirend-szolgáltatásokat. Az asztali elemzések esetében ez magában foglalja az eszközök leltárának megtekintését, a központi telepítési tervek létrehozását, a központi telepítés és az állapot megtekintését. Az Office testreszabása & házirend-szolgáltatás esetében ez a szerepkör lehetővé teszi a felhasználóknak az Office-házirendek kezelését.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Az Asztali elemzés teljes körű felügyelete. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="device-administrators"></a>Eszközrendszergazda
Az ehhez a szerepkörhöz hozzárendelt felhasználók hozzá lesznek adva a helyi Rendszergazdák csoporthoz az Azure AD-hez csatlakoztatott eszközökön.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. Directory/groupSettings/Basic/READ | A GroupSetting objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/groupSettingTemplates/Basic/READ | A GroupSettingTemplate objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |

### <a name="directory-readers"></a>Címtárolvasó
Az alapvető címtáradatok olvasása. Az alkalmazásokhoz való hozzáférés biztosítása nem a felhasználók számára ajánlott.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. Directory/Administrativeunit/Basic/READ | Az AdministrativeUnit objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/Administrativeunit/tagok/olvasás | Az administrativeUnits.members tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/alkalmazások/alap/olvasás | Az Application objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/alkalmazások/tulajdonosok/olvasás | Az applications.owners tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/alkalmazások/házirendek/olvasás | Az applications.policies tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/Contacts/Basic/READ | A Contact objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/Contacts/memberOf/READ | A contacts.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/szerződések/alap/olvasás | A Contract objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/eszközök/alap/olvasás | A Device objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/eszközök/memberOf/READ | A devices.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/eszközök/registeredOwners/READ | A devices.registeredOwners tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/eszközök/registeredUsers/READ | A revices.registeredUsers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/directoryRoles/basic/read | A DirectoryRole objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | A rirectoryRoles.eligibleMembers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/directoryRoles/members/read | A directoryRoles.members tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/domains/basic/read | A Domain objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/appRoleAssignments/read | A groups.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/alap/olvasás | A Group objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/memberOf/READ | A groups.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/tagok/olvasás | A groups.members tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/tulajdonosok/olvasás | A groups.owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/settings/read | A groups.settings tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/groupSettings/Basic/READ | A GroupSetting objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/groupSettingTemplates/Basic/READ | A GroupSettingTemplate objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/Oauth2permissiongrant objektumok/Basic/READ | Az oAuth2PermissionGrants alaptulajdonságainak olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/szervezet/alap/olvasás | Az Organization objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/szervezet/trustedCAsForPasswordlessAuth/olvasás | Az organization.trustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/roleAssignments/Basic/READ | A szerepkör-hozzárendelések alaptulajdonságainak olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/roleDefinitions/Basic/READ | A szerepkör-definíciók alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | A servicePrincipals.appRoleAssignedTo tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/servicePrincipals/appRoleAssignments/READ | A servicePrincipals.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/servicePrincipals/Basic/READ | A ServicePrincipal objektumok általános tulajdonságainak olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/servicePrincipals/memberOf/READ | A servicePrincipals.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/servicePrincipals/Oauth2permissiongrant objektumok/Basic/READ | A servicePrincipals.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | A servicePrincipals.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/servicePrincipals/tulajdonosok/olvasás | A servicePrincipals.owners tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/servicePrincipals/házirendek/olvasás | A servicePrincipals.policies tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/subscribedSkus/Basic/READ | A SubscribedSku objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/appRoleAssignments/read | A users.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/alap/olvasás | Felhasználók alaptulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/directReports/read | A users.directReports tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/kezelő/olvasás | A users.manager tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/memberOf/olvasás | A users.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/Users/Oauth2permissiongrant objektumok/Basic/READ | A users.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/ownedDevices/olvasás | A users.ownedDevices tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/ownedObjects/olvasás | A users.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/registeredDevices/olvasás | A users.registeredDevices tulajdonság olvasása az Azure Active Directoryban. |

### <a name="directory-synchronization-accounts"></a>Címtár-szinkronizálási fiókok
Csak Azure AD Connect szolgáltatás használja.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Az organization.dirSync tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/létrehozás | Szabályzatok létrehozása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/törlés | Szabályzatok törlése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/alap/olvasás | A Policy objektumok alapvető tulajdonságainak olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/alapszintű/frissítés | A Policy objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/tulajdonosok/olvasás | A policies.owners tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/tulajdonosok/frissítés | A policies.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | A policies.policiesAppliedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/tenantDefault/update | A policies.tenantDefault tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | A servicePrincipals.appRoleAssignedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | A servicePrincipals.appRoleAssignedTo tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/servicePrincipals/appRoleAssignments/READ | A servicePrincipals.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | A servicePrincipals.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/audience/update | A servicePrincipals.audience tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/authentication/update | A servicePrincipals.authentication tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/servicePrincipals/Basic/READ | A ServicePrincipal objektumok általános tulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/basic/update | A ServicePrincipal objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/create | A servicePrincipals létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/credentials/update | A servicePrincipals.credentials tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/servicePrincipals/memberOf/READ | A servicePrincipals.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/servicePrincipals/Oauth2permissiongrant objektumok/Basic/READ | A servicePrincipals.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/servicePrincipals/tulajdonosok/olvasás | A servicePrincipals.owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/update | A servicePrincipals.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | A servicePrincipals.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/permissions/update | A servicePrincipals.permissions tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/servicePrincipals/házirendek/olvasás | A servicePrincipals.policies tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/update | A servicePrincipals.policies tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directorySync/allEntities/allTasks | Az összes művelet végrehajtása az Azure AD Connectben. |

### <a name="directory-writers"></a>Címtárírók
Olvasás & az alapszintű könyvtár adatainak írása. Az alkalmazásokhoz való hozzáférés biztosítása nem a felhasználók számára ajánlott.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. Directory/csoportok/létrehozás | Group objektumok létrehozása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/createAsOwner | Group objektumok létrehozása az Azure Active Directoryban. A létrehozó hozzá lett adva az első tulajdonosként, és a létrehozott objektum a Creator 250 létrehozott objektum-kvótájának számít. |
| microsoft.aad.directory/groups/appRoleAssignments/update | A groups.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/alapszintű/frissítés | A Group objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/tagok/frissítés | A groups.members tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/tulajdonosok/frissítés | A groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/settings/update | A groups.settings tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/groupSettings/Basic/Update | A GroupSetting objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/groupSettings/Create | GroupSetting objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groupSettings/delete | A GroupSetting objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/users/appRoleAssignments/update | A users.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/assignLicense | Felhasználók licenceinek kezelése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/alapszintű/frissítés | Felhasználók alaptulajdonságainak frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/manager/update | A users.manager tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/userPrincipalName/update | A users.userPrincipalName tulajdonság frissítése az Azure Active Directoryban. |

### <a name="exchange-service-administrator"></a>Exchange-szolgáltatások rendszergazdája
Az Exchange termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | A groups.unified tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/Unified/Basic/Update | Az Office 365-csoportok alapvető tulajdonságainak frissítése. |
| Microsoft. HRE. Directory/csoportok/egységes/létrehozás | Office 365-csoportok létrehozása. |
| Microsoft. HRE. Directory/csoportok/egyesített/delete | Office 365-csoportok törlése. |
| Microsoft. HRE. Directory/csoportok/egyesített/tagok/frissítés | Az Office 365-csoportok tagságának frissítése. |
| Microsoft. HRE. Directory/csoportok/Unified/owners/Update | Az Office 365-csoportok tulajdonosának frissítése. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.exchange/allEntities/allTasks | Az Exchange Online teljes körű felügyelete. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="external-identity-provider-administrator"></a>Külső identitás-szolgáltató rendszergazdája
Konfigurálja az identitás-szolgáltatókat a közvetlen összevonás használatához.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. B2C/identityProviders/allTasks | Identitás-szolgáltatók olvasása és konfigurálása Azure Active Directory B2Cban. |

### <a name="guest-inviter"></a>Vendég meghívója
A tagok vendégmeghívási jogosultágát szabályozó beállítástól függetlenül meghívhat vendégeket.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | A users.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/alap/olvasás | Felhasználók alaptulajdonságainak olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/directReports/read | A users.directReports tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/inviteGuest | Vendégfelhasználók meghívása az Azure Active Directoryba. |
| Microsoft. HRE. Directory/felhasználók/kezelő/olvasás | A users.manager tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/memberOf/olvasás | A users.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/Users/Oauth2permissiongrant objektumok/Basic/READ | A users.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/ownedDevices/olvasás | A users.ownedDevices tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/ownedObjects/olvasás | A users.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/registeredDevices/olvasás | A users.registeredDevices tulajdonság olvasása az Azure Active Directoryban. |

### <a name="helpdesk-administrator"></a>Ügyfélszolgálati adminisztrátor
Alaphelyzetbe állíthatja a nem rendszergazdák és a segélyszolgálat rendszergazdák jelszavát.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | A devices.bitLockerRecoveryKeys tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/jelszó/frissítés | Frissítse a Azure Active Directory összes felhasználójának jelszavát. További részletekért tekintse meg az online dokumentációt. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="intune-service-administrator"></a>Intune szolgáltatás rendszergazdája
Az Intune termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. Directory/Contacts/Basic/Update | A Contact objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/Contacts/Create | Contact objektumok létrehozása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/Contacts/delete | A Contact objektumok törlése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/eszközök/alap/frissítés | A Device objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | A devices.bitLockerRecoveryKeys tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/eszközök/létrehozás | Device objektumok létrehozása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/eszközök/törlés | A Device objektumok törlése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/eszközök/registeredOwners/Update | A devices.registeredOwners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/devices/registeredUsers/update | A devices.registeredUsers tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/appRoleAssignments/update | A groups.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/alapszintű/frissítés | A Group objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/létrehozás | Group objektumok létrehozása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/createAsOwner | Group objektumok létrehozása az Azure Active Directoryban. A létrehozó hozzá lett adva az első tulajdonosként, és a létrehozott objektum a Creator 250 létrehozott objektum-kvótájának számít. |
| Microsoft. HRE. Directory/csoportok/törlés | A Group objektumok törlése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/hiddenMembers/READ | A groups.hiddenMembers tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/tagok/frissítés | A groups.members tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/tulajdonosok/frissítés | A groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/visszaállítás | A Group objektumok visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/settings/update | A groups.settings tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/appRoleAssignments/update | A users.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/alapszintű/frissítés | Felhasználók alaptulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/manager/update | A users.manager tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.intune/allEntities/allTasks | Az Intune teljes körű felügyelete. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |

### <a name="kaizala-administrator"></a>Kaizala-rendszergazda
Kezelheti a Microsoft Kaizala beállításait.  

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >  
  
| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | Olvassa el az Office 365 felügyeleti központot. |

### <a name="license-administrator"></a>Licencadminisztrátor
A a felhasználókra és a csoportokra vonatkozó licenceket is képes kezelni.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. Directory/felhasználók/assignLicense | Felhasználók licenceinek kezelése az Azure Active Directoryban. |
| microsoft.aad.directory/users/usageLocation/update | A users.usageLocation tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="lync-service-administrator"></a>Lync-szolgáltatások rendszergazdája
A Skype Vállalati verzió termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | A Skype Vállalati online verzió teljes körű felügyelete. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="message-center-privacy-reader"></a>A Message Center adatvédelmi olvasója
Beolvashatja az üzenetsor-bejegyzéseket, az adatvédelmi üzeneteket, a csoportokat, a tartományokat és az előfizetéseket.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.messageCenter/messages/read | Read messages in microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Read securityMessages in microsoft.office365.messageCenter. |

### <a name="message-center-reader"></a>Üzenetközpont-olvasó
Csak a szervezet Office 365 Üzenetközpontbeli üzeneteit és frissítéseit olvashatja. 

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.messageCenter/messages/read | Read messages in microsoft.office365.messageCenter. |

### <a name="partner-tier1-support"></a>1\. szintű partnertámogatás
Nem használható – általános használatra nem ajánlott.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. Directory/Contacts/Basic/Update | A Contact objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/Contacts/Create | Contact objektumok létrehozása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/Contacts/delete | A Contact objektumok törlése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/létrehozás | Group objektumok létrehozása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/createAsOwner | Group objektumok létrehozása az Azure Active Directoryban. A létrehozó hozzá lett adva az első tulajdonosként, és a létrehozott objektum a Creator 250 létrehozott objektum-kvótájának számít. |
| Microsoft. HRE. Directory/csoportok/tagok/frissítés | A groups.members tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/tulajdonosok/frissítés | A groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/appRoleAssignments/update | A users.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/assignLicense | Felhasználók licenceinek kezelése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/alapszintű/frissítés | Felhasználók alaptulajdonságainak frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/törlés | A User objektumok törlése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/manager/update | A users.manager tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/jelszó/frissítés | Frissítse a Azure Active Directory összes felhasználójának jelszavát. További részletekért tekintse meg az online dokumentációt. |
| Microsoft. HRE. Directory/felhasználók/visszaállítás | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/users/userPrincipalName/update | A users.userPrincipalName tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="partner-tier2-support"></a>2\. szintű partnertámogatás
Nem használható – általános használatra nem ajánlott.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. Directory/Contacts/Basic/Update | A Contact objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/Contacts/Create | Contact objektumok létrehozása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/Contacts/delete | A Contact objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/domains/allTasks | Domain objektumok létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/létrehozás | Group objektumok létrehozása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/törlés | A Group objektumok törlése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/tagok/frissítés | A groups.members tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/visszaállítás | A Group objektumok visszaállítása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/szervezet/alapszintű/frissítés | Az Organization objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/appRoleAssignments/update | A users.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/assignLicense | Felhasználók licenceinek kezelése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/alapszintű/frissítés | Felhasználók alaptulajdonságainak frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/törlés | A User objektumok törlése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/manager/update | A users.manager tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/jelszó/frissítés | Frissítse a Azure Active Directory összes felhasználójának jelszavát. További részletekért tekintse meg az online dokumentációt. |
| Microsoft. HRE. Directory/felhasználók/visszaállítás | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/users/userPrincipalName/update | A users.userPrincipalName tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="password-administrator"></a>Jelszókezelő
Alaphelyzetbe állíthatja a nem rendszergazdák és a jelszó-rendszergazdák jelszavát.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. Directory/felhasználók/jelszó/frissítés | Frissítse a Azure Active Directory összes felhasználójának jelszavát. További részletekért tekintse meg az online dokumentációt. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |

### <a name="power-bi-service-administrator"></a>Power BI-szolgáltatásgazda
A Power BI termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.powerApps.powerBI/allEntities/allTasks | A Power BI teljes körű felügyelete. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="privileged-authentication-administrator"></a>Kiemelt jogosultságú hitelesítés rendszergazdája
Jogosult bármely felhasználó (rendszergazda vagy nem rendszergazda) hitelesítési módszerrel kapcsolatos adatainak megtekintésére, beállítására és alaphelyzetbe állítására.

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. Directory/felhasználók/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/strongAuthentication/update | Az erős hitelesítési tulajdonságok, például az MFA hitelesítő adatainak frissítése. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| Microsoft. HRE. Directory/felhasználók/jelszó/frissítés | Az Office 365-szervezetben lévő összes felhasználó jelszavának frissítése. További részletekért tekintse meg az online dokumentációt. |
### <a name="privileged-role-administrator"></a>Kiemelt szerepkörű rendszergazda
Felügyelheti a szerepkör-hozzárendeléseket az Azure AD-ben, és a Privileged Identity Management összes aspektusát.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a következőben: microsoft.aad.privilegedIdentityManagement. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/allTasks | A servicePrincipals. appRoleAssignedTo tulajdonság olvasása és konfigurálása Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | A servicePrincipals. Oauth2permissiongrant objektumok tulajdonság olvasása és konfigurálása Azure Active Directoryban. |
| Microsoft. HRE. Directory/Administrativeunit/allProperties/allTasks | Felügyeleti egységek létrehozása és kezelése (beleértve a tagokat) |
| Microsoft. HRE. Directory/roleAssignments/allProperties/allTasks | Szerepkör-hozzárendelések létrehozása és kezelése. |
| Microsoft. HRE. Directory/roleDefinitions/allProperties/allTasks | Szerepkör-definíciók létrehozása és kezelése. |

### <a name="reports-reader"></a>Jelentésolvasó
Megtekintheti a bejelentkezési és a naplózási jelentéseket.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. Directory/auditLogs/allProperties/READ | Az Azure Active Directorybeli auditnaplók összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| Microsoft. HRE. Directory/signInReports/allProperties/READ | Az Azure Active Directorybeli bejelentkezési jelentések összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.usageReports/allEntities/read | Az Office 365-beli használati jelentések olvasása. |

### <a name="search-administrator"></a>Keresés a Rendszergazdában
A a Microsoft keresési beállításainak minden aspektusát létrehozhatja és kezelheti.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Read messages in microsoft.office365.messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Az összes erőforrás létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése a Microsoft. Office 365. Search webhelyen. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.usageReports/allEntities/read | Az Office 365-beli használati jelentések olvasása. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |

### <a name="search-editor"></a>Keresési szerkesztő
Létrehozhat és kezelhet olyan szerkesztési tartalmakat, mint a könyvjelzők, a Q és a, a helyszínek, az alaprajz.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Read messages in microsoft.office365.messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Tartalom létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése a Microsoft. Office 365. Search fájlból. |
| microsoft.office365.usageReports/allEntities/read | Az Office 365-beli használati jelentések olvasása. |

### <a name="security-administrator"></a>Biztonsági rendszergazda
A biztonsági információk és jelentések beolvasása, valamint a konfiguráció kezelése az Azure AD-ben és az Office 365-ben.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | A applications.policies tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/auditLogs/allProperties/READ | Az Azure Active Directorybeli auditnaplók összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | A devices.bitLockerRecoveryKeys tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/alapszintű/frissítés | A Policy objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/létrehozás | Szabályzatok létrehozása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/törlés | Szabályzatok törlése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/házirendek/tulajdonosok/frissítés | A policies.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/policies/tenantDefault/update | A policies.tenantDefault tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/update | A servicePrincipals.policies tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/signInReports/allProperties/READ | Az Azure Active Directorybeli bejelentkezési jelentések összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.aad.identityProtection/allEntities/read | Az összes erőforrás olvasása a következőben: microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Az összes erőforrás frissítése a következőben: microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Az összes erőforrás olvasása a következőben: microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.protectionCenter/allEntities/read | Az Office 365 Védelmi központ minden aspektusának olvasása. |
| microsoft.office365.protectionCenter/allEntities/update | Az összes erőforrás frissítése a következőben: microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="security-operator"></a>Biztonsági operátor
Biztonsági események létrehozása és kezelése.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Microsoft Cloud App Security olvasása és konfigurálása. |
| microsoft.aad.identityProtection/allEntities/read | Az összes erőforrás olvasása a következőben: microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Az összes erőforrás olvasása a következőben: microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Az Azure AD komplex veszélyforrások elleni védelem olvasása és konfigurálása. |
| microsoft.intune/allEntities/allTasks | Az Intune teljes körű felügyelete. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Biztonsági és megfelelőségi központ olvasása és konfigurálása. |
| microsoft.office365.usageReports/allEntities/read | Az Office 365-beli használati jelentések olvasása. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | A Windows Defender komplex veszélyforrások elleni védelem olvasása és konfigurálása. |

### <a name="security-reader"></a>Biztonsági olvasó
Megtekintheti a biztonsági információkat és jelentéseket az Azure AD-ban és az Office 365-ben.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. Directory/auditLogs/allProperties/READ | Az Azure Active Directorybeli auditnaplók összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | A devices.bitLockerRecoveryKeys tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/signInReports/allProperties/READ | Az Azure Active Directorybeli bejelentkezési jelentések összes tulajdonságának olvasása (beleértve a kiemelt tulajdonságokat). |
| microsoft.aad.identityProtection/allEntities/read | Az összes erőforrás olvasása a következőben: microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Az összes erőforrás olvasása a következőben: microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.protectionCenter/allEntities/read | Az Office 365 Védelmi központ minden aspektusának olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="service-support-administrator"></a>Szolgáltatástámogatási rendszergazda
Megtekintheti a szolgáltatásállapot-adatokat, és kezelheti a támogatási jegyeket.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="sharepoint-service-administrator"></a>SharePoint szolgáltatás-rendszergazda
A SharePoint-szolgáltatásokkal kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | A groups.unified tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/Unified/Basic/Update | Az Office 365-csoportok alapvető tulajdonságainak frissítése. |
| Microsoft. HRE. Directory/csoportok/egységes/létrehozás | Office 365-csoportok létrehozása. |
| Microsoft. HRE. Directory/csoportok/egyesített/delete | Office 365-csoportok törlése. |
| Microsoft. HRE. Directory/csoportok/egyesített/tagok/frissítés | Az Office 365-csoportok tagságának frissítése. |
| Microsoft. HRE. Directory/csoportok/Unified/owners/Update | Az Office 365-csoportok tulajdonosának frissítése. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.sharepoint/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése a következőben: microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="teams-communications-administrator"></a>Csapatok kommunikációs rendszergazdája
Kezelheti a Microsoft Teams szolgáltatás hívási és értekezletfunkcióit.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.usageReports/allEntities/read | Az Office 365-beli használati jelentések olvasása. |

### <a name="teams-communications-support-engineer"></a>Teams-kommunikációs támogatási szakember
Speciális eszközökkel háríthatja el a Teams szolgáltatásban fellépő kommunikációs problémákat.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="teams-communications-support-specialist"></a>Teams-kommunikációs támogatási szakértő
Alapszintű eszközökkel háríthatja el a Teams szolgáltatásban fellépő kommunikációs problémákat.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="teams-service-administrator"></a>Teams-szolgáltatásadminisztrátor
Kezelheti a Microsoft Teams szolgáltatást.

  > [!NOTE]
  > Ez a szerepkör a Azure Active Directoryon kívül további engedélyekkel rendelkezik. További információ: a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft. HRE. Directory/csoportok/hiddenMembers/READ | A groups.hiddenMembers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | A groups.unified tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/Unified/Basic/Update | Az Office 365-csoportok alapvető tulajdonságainak frissítése. |
| Microsoft. HRE. Directory/csoportok/egységes/létrehozás | Office 365-csoportok létrehozása. |
| Microsoft. HRE. Directory/csoportok/egyesített/delete | Office 365-csoportok törlése. |
| Microsoft. HRE. Directory/csoportok/egyesített/tagok/frissítés | Az Office 365-csoportok tagságának frissítése. |
| Microsoft. HRE. Directory/csoportok/Unified/owners/Update | Az Office 365-csoportok tulajdonosának frissítése. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.usageReports/allEntities/read | Az Office 365-beli használati jelentések olvasása. |

### <a name="user-administrator"></a>Felhasználói rendszergazda
A felhasználókkal és a csoportokkal kapcsolatos összes felügyeleti rendelkezik, beleértve a korlátozott rendszergazdák jelszavának alaphelyzetbe állítását is.

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | AppRoleAssignment objektumok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/delete | Az AppRoleAssignment objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/update | Az AppRoleAssignment objektumok frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/Contacts/Basic/Update | A Contact objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/Contacts/Create | Contact objektumok létrehozása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/Contacts/delete | A Contact objektumok törlése az Azure Active Directoryban. |
| microsoft.aad.directory/groups/appRoleAssignments/update | A groups.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/alapszintű/frissítés | A Group objektumok alapvető tulajdonságainak frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/létrehozás | Group objektumok létrehozása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/createAsOwner | Group objektumok létrehozása az Azure Active Directoryban. A létrehozó hozzá lett adva az első tulajdonosként, és a létrehozott objektum a Creator 250 létrehozott objektum-kvótájának számít. |
| Microsoft. HRE. Directory/csoportok/törlés | A Group objektumok törlése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/hiddenMembers/READ | A groups.hiddenMembers tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/tagok/frissítés | A groups.members tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/tulajdonosok/frissítés | A groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/csoportok/visszaállítás | A Group objektumok visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/settings/update | A groups.settings tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/appRoleAssignments/update | A users.appRoleAssignments tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/assignLicense | Felhasználók licenceinek kezelése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/alapszintű/frissítés | Felhasználók alaptulajdonságainak frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/létrehozás | User objektumok létrehozása az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/törlés | A User objektumok törlése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/manager/update | A users.manager tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft. HRE. Directory/felhasználók/jelszó/frissítés | Frissítse a Azure Active Directory összes felhasználójának jelszavát. További részletekért tekintse meg az online dokumentációt. |
| Microsoft. HRE. Directory/felhasználók/visszaállítás | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/users/userPrincipalName/update | A users.userPrincipalName tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Az Azure Service Health olvasása és konfigurálása. |
| microsoft.azure.supportTickets/allEntities/allTasks | Az Azure-támogatási jegyek létrehozása és kezelése. |
| microsoft.office365.webPortal/allEntities/basic/read | A microsoft.office365.webPortal összes erőforrása alapvető terméktulajdonságainak olvasása. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

## <a name="role-template-ids"></a>Szerepkör-sablon azonosítói

A szerepkör-sablon azonosítóit elsősorban Graph API vagy PowerShell-felhasználók használják.

Gráf displayName | Azure Portal megjelenítendő név | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Alkalmazás-rendszergazda | Alkalmazás-rendszergazda | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Alkalmazásfejlesztő | Alkalmazásfejlesztő | CF1C38E5-3621-4004-A7CB-879624DCED7C
Hitelesítés rendszergazdája | Hitelesítési rendszergazda | c4e39bd9-1100-46d3-8c65-fb160da0071f
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
