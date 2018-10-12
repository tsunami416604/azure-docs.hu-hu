---
title: Az Azure Active Directory rendszergazdája szerepkör engedélyei |} A Microsoft Docs
description: Rendszergazda szerepkörrel is adja hozzá a felhasználók, rendszergazdai szerepkörök hozzárendelése, felhasználók új jelszavainak létrehozására, a felhasználói licencek kezelése vagy tartományok kezelése.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/25/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 755b94b71fdaefb261741f4d4e756b90d8148280
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116895"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Az Azure Active Directory rendszergazdája szerepkör engedélyei

Az Azure Active Directory (Azure AD), a különböző funkciók kiszolgálása érdekében külön rendszergazdák is kijelölhet. A rendszergazdák a feladatok végrehajtását, mint hozzáadása vagy módosítása a felhasználók, rendszergazdai szerepkörök hozzárendelése, felhasználói jelszó alaphelyzetbe állítása, a felhasználói licencek kezelése és tartománynevek kezelése az Azure AD portálon lehet kiválasztani.

A globális rendszergazda az összes rendszergazdai funkciójához hozzáfér. Alapértelmezés szerint az Azure-előfizetésre feliratkozó személy van rendelve a címtár globális rendszergazdai szerepkörrel. Csak a globális rendszergazdák is engedélyezheti – delegálja rendszergazdai szerepköröket.

## <a name="assign-or-remove-administrator-roles"></a>Vagy vonja meg a rendszergazdai szerepkörökről

Rendszergazdai szerepkörök hozzárendelése az Azure Active Directory felhasználó kapcsolatban lásd: [nézet és rendelhet hozzá rendszergazdai szerepköröket az Azure Active Directoryban](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Elérhető szerepkörök

A következő rendszergazdai szerepkörök érhetők el:

* **[Alkalmazás-rendszergazda](#application-administrator)**: az ehhez a szerepkörhöz felhasználók létrehozhatják és vállalati alkalmazásokat, alkalmazásregisztrációkat és alkalmazásproxy-beállítások minden szempontjának kezeléséhez. Ez a szerepkör lehetővé teszi, hogy engedélyt adjanak az delegált engedélyeket, és az Alkalmazásengedélyek, kivéve a Microsoft Graph és az Azure AD Graph is nyújt. Ezen szerepkör tagjai nem adódnak tulajdonosként, amikor új alkalmazásregisztráció vagy a vállalati alkalmazások létrehozása.

  <b>Fontos</b>: Ez a szerepkör képes kezelni az alkalmazás hitelesítő adatokat biztosít. Ehhez a szerepkörhöz tartozó felhasználók hitelesítő adatok hozzáadása egy alkalmazáshoz, és ezeket a hitelesítő adatokat használja a megszemélyesíteni az alkalmazás azonosítóját. Ha az alkalmazás azonosítóját hozzáférési engedélyt kapott az Azure Active Directoryhoz, például létrehozni vagy frissíteni a felhasználói és más objektumok, majd ehhez a szerepkörhöz hozzárendelt felhasználó sikerült végrehajtani az alkalmazás megszemélyesítése közben ezeket a műveleteket. Ezt a lehetőséget az alkalmazás identitás megszemélyesítése lehet egy megszerzését keresztül a felhasználó mit tehetnek a szerepkör-hozzárendeléseket keresztül az Azure ad-ben. Fontos megérteni, hogy a felhasználó az alkalmazás-rendszergazda szerepkör hozzárendelése biztosít az Alkalmazásidentitás megszemélyesítését.

* **[Alkalmazás fejlesztője](#application-developer)**: a szerepkör felhasználói alkalmazásregisztrációkat hozhat létre. Ha a "Felhasználók regisztrálhatnak alkalmazásokat" beállítás értéke nem. Ez a szerepkör lehetővé teszi a saját nevükben jóváhagyást tagok amikor a "Felhasználók engedélyezhetik, hogy az alkalmazások hozzáférjenek a céges adatok saját nevükben való" beállítás értéke nem. Ezen szerepkör tagjai tulajdonosként való hozzáadásakor új alkalmazásregisztráció vagy a vállalati alkalmazások létrehozásakor.

* **[Számlázási adminisztrátor](#billing-administrator)**: lebonyolítja a vásárlásokat, kezeli az előfizetéseket, támogatási jegyeket, és figyeli a szolgáltatás állapotát.

* **[Felhőalkalmazás-rendszergazda](#cloud-application-administrator)**: a szerepkör felhasználói ugyanazokkal az engedélyekkel rendelkeznek az alkalmazás-rendszergazda szerepkörnek képes kezelni az application proxy kivételével. A szerepkörök létrehozása és kezelése vállalati alkalmazásokkal és az alkalmazást az alkalmazásregisztrációk minden aspektusát. Ez a szerepkör lehetővé teszi, hogy engedélyt adjanak az delegált engedélyeket, és az Alkalmazásengedélyek, kivéve a Microsoft Graph és az Azure AD Graph is nyújt. Ezen szerepkör tagjai nem adódnak tulajdonosként, amikor új alkalmazásregisztráció vagy a vállalati alkalmazások létrehozása.

  <b>Fontos</b>: Ez a szerepkör képes kezelni az alkalmazás hitelesítő adatokat biztosít. Ehhez a szerepkörhöz tartozó felhasználók hitelesítő adatok hozzáadása egy alkalmazáshoz, és ezeket a hitelesítő adatokat használja a megszemélyesíteni az alkalmazás azonosítóját. Ha az alkalmazás azonosítóját hozzáférési engedélyt kapott az Azure Active Directoryhoz, például létrehozni vagy frissíteni a felhasználói és más objektumok, majd ehhez a szerepkörhöz hozzárendelt felhasználó sikerült végrehajtani az alkalmazás megszemélyesítése közben ezeket a műveleteket. Ezt a lehetőséget az alkalmazás identitás megszemélyesítése lehet egy megszerzését keresztül a felhasználó mit tehetnek a szerepkör-hozzárendeléseket keresztül az Azure ad-ben. Fontos megérteni, hogy egy felhasználó hozzárendelése a Felhőalkalmazás-rendszergazda szerepkört biztosít az Alkalmazásidentitás megszemélyesítését.

* **[Felhőbeli Eszközrendszergazda](#cloud-device-administrator)**: az ehhez a szerepkörhöz felhasználók engedélyezhetik, tiltsa le, és eszközök törlése az Azure ad-ben és (ha van ilyen) olvassa el a Windows 10-es BitLocker-kulcsok az Azure Portalon. A szerepkör nem biztosítanak engedélyeket más tulajdonságokat, az eszköz kezelésére.

* **[Szabályozási ügyintéző](#compliance-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók az Office 365 Security & Compliance Centerben és Exchange felügyeleti központban a felügyeleti engedélyekkel rendelkeznek. További információ: [Office 365 rendszergazdai szerepköreinek](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Feltételes hozzáférésű rendszergazda](#conditional-access-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók képesek az Azure Active Directory feltételes hozzáférés beállításainak ellenőrzése.
  > [!NOTE]
  > Exchange ActiveSync feltételes hozzáférési szabályzatot az Azure-beli üzembe helyezéséhez a felhasználó is egy globális rendszergazdának kell lennie.
  
* **[Eszközadminisztrátorok](#device-administrators)**: érhető el ezt a szerepkört csak egy további helyi rendszergazdaként a hozzárendelés [eszközbeállítások](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Az ehhez a szerepkörhöz tartozó felhasználók a helyi gépek rendszergazdái lesznek az Azure Active Directoryval összekapcsolt összes Windows 10-eszközön. Nem rendelkeznek képes kezelni az Azure Active Directoryban. 

* **[Címtárolvasók](#directory-readers)**: egy örökölt szerepkör, amely hozzá kell rendelni az alkalmazásokat, amelyek nem támogatják a [hozzájárulási keretrendszer](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Azt nem lehet hozzárendelni egy felhasználóhoz sem.

* **[Címtár-szinkronizálási fiókok](#directory-synchronization-accounts)**: ne használja. Ez a szerepkör automatikusan hozzá lesz rendelve az Azure AD Connect szolgáltatást, és nem szándékos vagy bármely más használata támogatott.

* **[Címtárírók](#directory-writers)**: egy örökölt szerepkör, amely hozzá kell rendelni az alkalmazásokat, amelyek nem támogatják a [hozzájárulási keretrendszer](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Azt nem lehet hozzárendelni egy felhasználóhoz sem.

* **[Szolgáltatás-rendszergazda Dynamics 365 és CRM-Szolgáltatásadminisztrátor](#dynamics-365-service-administrator)**: az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft Dynamics 365 online-ban, ha a szolgáltatás nem található, ezenkívül lehetővé teszi a támogatási jegyek kezelését és szolgáltatás állapotának figyelése. További információ: [a szolgáltatás-rendszergazdai szerepkör használata kezelheti a bérlő](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

* **[Exchange-szolgáltatások rendszergazdája](#exchange-service-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók a Microsoft Exchange online-ban, globális engedélyekkel rendelkeznek, a szolgáltatás megléte esetén. További információ: [Office 365 rendszergazdai szerepköreinek](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Globális / vállalati rendszergazda](#company-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók férhetnek hozzá az Azure Active Directoryval, valamint a szolgáltatások, például az Exchange online-hoz, az Azure Active Directory-identitásokat használó összes rendszergazdai funkciójához SharePoint Online és Skype vállalati Online verzióhoz. A személy, aki regisztrál az Azure Active Directory-bérlő globális rendszergazdája lesz. Csak a globális rendszergazdák más rendszergazdai szerepköröket rendelhet hozzá. A vállalat a egynél több globális rendszergazda is lehet. A globális rendszergazdák bármely felhasználó és minden más rendszergazdák jelszavát is alaphelyzetbe.

  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja a folyamat "Vállalati rendszergazdaként". Az "Globális rendszergazda" a [az Azure portal](https://portal.azure.com).
  >
  >

* **[Vendég meghívója](#guest-inviter)**: az ehhez a szerepkörhöz felhasználók kezelhetik az Azure Active Directory B2B vendégfelhasználói meghívókat során a **tagok küldhetnek meghívót** felhasználói beállítás értéke nem. További információ a B2B-együttműködés: [kapcsolatos Azure AD B2B együttműködés](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Nem tartalmaz semmilyen más engedélyt.

* **[Information Protection-rendszergazda](#information-protection-administrator)**: az ehhez a szerepkörhöz tartozó felhasználók minden engedéllyel rendelkezni az Azure Information Protection szolgáltatásban. Ez a szerepkör lehetővé teszi, hogy címkéket az Azure Information Protection-házirend konfigurálása, a védelmi sablonokat kezeléséhez és a védelem aktiválása. Ez a szerepkör nem biztosít az Identity Protection Centerben, Privileged Identity Management, Monitor Office 365 Service Healthben vagy Office 365 Security & Compliance Centerben engedélyeket.

* **[Intune-Szolgáltatásadminisztrátor](#intune-service-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók a Microsoft Intune Online globális engedélyekkel rendelkeznek, ha a szolgáltatás nem található. Ez a szerepkör ezenkívül tartalmazza a képes kezelni a felhasználók és eszközök számára annak érdekében, hogy társítja a házirendet, valamint csoportok létrehozásához és kezeléséhez. További információ: [szerepköralapú vezérlés (RBAC) a Microsoft Intune-nal](https://docs.microsoft.com/intune/role-based-access-control)

* **[Licencek adminisztrátora](#license-administrator)**: a szerepkör felhasználói hozzáadása, eltávolítása, és a felhasználók, csoportok (Csoportalapú licencelést használ) a licenc-hozzárendelések frissítése, és beállíthatja a felhasználási hely, a felhasználók. A szerepkör nem teszi lehetővé a beszerzési vagy -előfizetések kezelése, hozzon létre vagy kezelheti a csoportokat, vagy hozzon létre vagy kezelni a felhasználókat a felhasználási hely túli biztosít.

* **[Üzenet Center olvasó](#message-center-reader)**: figyelheti, hogy a szerepkör felhasználói értesítések és a tanácsadás egészségügyi módosításokat [Office 365 üzenetközpontjában](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) konfigurált szolgáltatások, például az Exchange, Intune, a szervezeten belül és a Microsoft Teams. Üzenetközpont heti e-mail emésztett hozzászólás, frissítések fogadása és oszthatnak meg üzenet center bejegyzések az Office 365-ben. Az Azure AD-ben ehhez a szerepkörhöz tartozó felhasználók csak van csak olvasási hozzáféréssel az Azure AD szolgáltatások, például a felhasználók és csoportok. 

* **[1. rétegbeli támogatása partneri](#partner-tier1-support)**: ne használja. Ez a szerepkör elavult és törlődni fog a későbbiekben az Azure AD-ből. Ez a szerepkör kis számú Microsoft-viszonteladói partnerek általi használatra van, és nem célja általános használatra.

* **[2. rétegbeli támogatása partneri](#partner-tier2-support)**: ne használja. Ez a szerepkör elavult és törlődni fog a későbbiekben az Azure AD-ből. Ez a szerepkör kis számú Microsoft-viszonteladói partnerek általi használatra van, és nem célja általános használatra.

* **[Jelszókezelő / ügyfélszolgálati adminisztrátor](#helpdesk-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók is módosíthatja a jelszavát, érvényteleníti frissítési biztonsági jogkivonat, kezelhetik és szolgáltatások állapotának figyelése. A frissítési jogkivonatok érvénytelenítése kényszeríti a felhasználót, hogy jelentkezzen be újra. Segélyszolgálat rendszergazdák új jelszót és érvényteleníti a frissítési biztonsági jogkivonat, más felhasználók, akik nem rendszergazdák, vagy csak a következő szerepkörök tagjai:
  * Directory-olvasók
  * Vendég meghívója
  * Ügyfélszolgálati adminisztrátor
  * Üzenetközpont-olvasó
  * Jelentésolvasó
  
  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör "Ügyfélszolgálati adminisztrátor" azonosítja. Ezt "Jelszókezelő" szerepel a [az Azure portal](https://portal.azure.com/).
  >
  
* **[A Power BI-Szolgáltatásadminisztrátor](#power-bi-service-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók kezelhetik a támogatási jegyeket, és a szolgáltatások állapotának figyelése szolgáltatásuk, ezenkívül a Microsoft Power BI, a szolgáltatás megléte esetén globális engedélyekkel rendelkeznek. További információ: [a Power BI rendszergazdai szerepkörét ismertető](https://docs.microsoft.com/power-bi/service-admin-role).

* **[Kiemelt szerepkörű rendszergazda](#privileged-role-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók kezelhetik a szerepkör-hozzárendelések az Azure Active Directoryban, valamint az Azure AD Privileged Identity Management belül. Emellett ez a szerepkör lehetővé teszi minden aspektusát Privileged Identity Management kezelését.

  <b>Fontos</b>: A szerepkörök a minden Azure AD-szerepkörök, többek között a globális rendszergazdai szerepkör a tagság kezeléséhez. Ez a szerepkör nem tartalmaz semmilyen más emelt szintű képességek például létrehozása vagy frissítése a felhasználók Azure AD-ben. Azonban ehhez a szerepkörhöz tartozó felhasználók adhatnak magukat vagy mások további jogosultságok további szerepkörök hozzárendelésével.

* **[Jelentések olvasó](#reports-reader)**: Ezzel a szerepkörrel rendelkező felhasználók megtekinthetik a használati adatok és az Office 365 felügyeleti központot, és a bevezetési környezeti jelentések irányítópult a Power bi-ban csomag jelentéskészítés. Ezenkívül a szerepkör-bejelentkezés hozzáférést biztosít jelentéseket és a tevékenység az Azure ad-ben és a Microsoft Graph által visszaadott adatok jelentéskészítési API-t. A jelentések Olvasó szerepkörhöz rendelt felhasználó hozzáférhet, csak a releváns használati és mérőszámairól. Nincs szükség semmilyen konfigurálni vagy a hozzáférés a termékspecifikus felügyeleti központok, mint az Exchange-rendszergazdai jogosultságokkal. 

* **[Biztonsági rendszergazda](#security-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók rendelkeznek a biztonsági olvasói szerepkör, valamint a lehetővé teszi a biztonsággal kapcsolatos szolgáltatások konfigurációjának kezelését csak olvasási engedélyei: Azure Active Directory Identity Protection, Az Azure Information Protection, és az Office 365 Security & Compliance Centerben. Office 365-engedélyekkel kapcsolatos további információt [az engedélyek az Office 365 Security & Compliance Centerben](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  | Eleme ennek | Teheti meg |
  | --- | --- |
  | Identity Protection Center |<ul><li>A biztonsági olvasó szerepkör összes engedélyt.<li>Ezenkívül lehetővé teszi új jelszavak kivételével az összes IPC műveletek végrehajtásához. |
  | Privileged Identity Management |<ul><li>A biztonsági olvasó szerepkör összes engedélyt.<li>**Nem lehet** kezelése az Azure ad-ben szerepkörtagságai és -beállítások. |
  | <p>A figyelő az Office 365 szolgáltatás állapota</p><p>Office 365 Biztonsági és megfelelőségi központ |<ul><li>A biztonsági olvasó szerepkör összes engedélyt.<li>Konfigurálhatja a komplex veszélyforrások elleni védelem szolgáltatást (kártevők és vírusok védelmi, rosszindulatú URL-cím konfigurációs, URL-cím nyomkövetés, stb.) az összes beállítást. |
  
* **[Biztonsági olvasó](#security-reader)**: Ezzel a szerepkörrel rendelkező felhasználók globális csak olvasási hozzáférése van, többek között az Azure Active Directory, a Identity Protection, a Privileged Identity Management, valamint lehetővé teszi az Azure Active Directory olvasási Jelentkezzen be a jelentések és naplók. A szerepkör csak olvasási jogosultság az Office 365 Security & Compliance Centerben is nyújt. Office 365-engedélyekkel kapcsolatos további információt [az engedélyek az Office 365 Security & Compliance Centerben](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  | Eleme ennek | Teheti meg |
  | --- | --- |
  | Identity Protection Center |Olvassa el a biztonsági jelentések és a beállítási információk biztonsági funkciók<ul><li>Levélszemét<li>Titkosítás<li>Adatveszteség-megelőzés<li>Kártevőirtó<li>Komplex veszélyforrások elleni védelem<li>Adathalászat elleni<li>Mailflow szabályok |
  | Privileged Identity Management |<p>Csak olvasási hozzáférést minden információt illesztett rendelkezik az Azure AD PIM-ben: szabályzatok és a jelentések az Azure AD szerepkör-hozzárendeléseket, biztonsági ellenőrzi, és a jövőben olvassa el a hozzáférési házirend adatok és jelentések forgatókönyvek mellett az Azure AD szerepkör-hozzárendelés.<p>**Nem lehet** iratkozzon fel az Azure AD PIM-ben, vagy ne módosítsa. A PIM a portálon vagy a Powershellen keresztül valaki ezt a szerepkört a további szerepkörök (például a globális rendszergazdai vagy a kiemelt szerepkörű rendszergazda), számukra egy jelölt felhasználó esetén is aktiválhatja. |
  | <p>A figyelő az Office 365 szolgáltatás állapota</p><p>Office 365 Biztonsági és megfelelőségi központ</p> |<ul><li>Olvasás és riasztáskezelés<li>Biztonsági szabályzatok olvasása<li>Fenyegetések felderítése, a Cloud App Discovery és a Keresés és vizsgálat karantén olvasása<li>Az összes jelentés olvasása |

* **[A szolgáltatástámogatási rendszergazda](#service-support-administrator)**: az ehhez a szerepkörhöz tartozó felhasználók támogatási kérelmeket nyithatnak a Microsoft Azure és az Office 365 szolgáltatásokban, és a szolgáltatás irányítópultját és üzenetközpontját center az Azure Portalon és az Office 365 felügyeleti portálján megtekinthetik a. További információ: [Office 365 rendszergazdai szerepköreinek](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[A SharePoint szolgáltatás-rendszergazda](#sharepoint-service-administrator)**: az ehhez a szerepkörhöz tartozó felhasználók globális engedélyekkel rendelkeznek a Microsoft SharePoint online-ban, a szolgáltatás megléte esetén, ezenkívül kezelhetik a támogatási jegyeket, és a szolgáltatások állapotának figyelése. További információ: [Office 365 rendszergazdai szerepköreinek](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Skype vállalati verzió / Lync-szolgáltatások rendszergazdája](#lync-service-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók a Microsoft Skype vállalati verzió, globális engedélyekkel rendelkeznek a szolgáltatás megléte esetén, valamint kezelheti a Skype-specifikus felhasználói attribútumok az Azure Active A könyvtár. Ezenkívül a szerepkörök a kezelhetik a támogatási jegyeket, és a szolgáltatások állapotának figyelése, valamint a Teams és a Skype for Business felügyeleti központ eléréséhez. A fiók is licenccel kell rendelkezniük a csapatok vagy csoportokkal PowerShell-parancsmagok nem lesz futtatható. További információ: [kapcsolatos a Skype for Business rendszergazdai szerepkör](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) és a licencelési adatokat csapat [Skype for Business és a Microsoft Teams kiegészítő licencelési](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > A Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja "Lync-Szolgáltatásadminisztrátor". Az "Skype for Business szolgáltatás-rendszergazda" a [az Azure portal](https://portal.azure.com/).
  >
  >

* **[Rendszergazda kommunikációs csapatok](#teams-communications-administrator)**: az ehhez a szerepkörhöz felhasználók kezelhetik a hang- és a telefonos kapcsolatos Microsoft Teams számítási aspektusait. Ez magában foglalja a telefon száma hozzárendelés, hang- és értekezlet házirendek és a hívás analytics eszközkészlet teljes hozzáférést a felügyeleti eszközöket.

* **[Támogatási szakértő kommunikációs csapatok](#teams-communications-support-engineer)**: a szerepkör felhasználói belül a Microsoft Teams és a Skype kommunikációs problémák elhárítását, a használatával a felhasználó üzleti hívja a Microsoft Teams és a Skype vállalati verzió hibaelhárítási eszközök Üzleti felügyeleti központban. A szerepkör felhasználói tekintheti meg az összes érintett résztvevők a teljes hívás rekord adatait.

* **[Támogatási szakértő kommunikációs csapatok](#teams-communications-support-specialist)**: a szerepkör felhasználói belül a Microsoft Teams és a Skype kommunikációs problémák elhárítását, a használatával a felhasználó üzleti hívja a Microsoft Teams és a Skype vállalati verzió hibaelhárítási eszközök Üzleti felügyeleti központban. A szerepkör felhasználói csak tekintheti meg a felhasználói adatok hívásában az adott felhasználó rendelkezik kulcskeresési.

* **[Szolgáltatás-rendszergazda csapatok](#teams-service-administrator)**: az ehhez a szerepkörhöz felhasználók kezelhetik a Microsoft Teams munkaterhelés, a Microsoft Teams és a Skype vállalati felügyeleti központot és a megfelelő PowerShell-modulok minden aspektusát. Ez magában foglalja, többek között a más területeken, telefonos, üzenetkezelés, értekezletek és a csapatok maguk kapcsolatos összes felügyeleti eszközök. Ez a szerepkör is képes kezelni az Office 365-csoportokat biztosít.

* **[Felhasználóifiók-adminisztrátor](#user-account-administrator)**: Ezzel a szerepkörrel rendelkező felhasználók felhasználók létrehozása, és bizonyos korlátozásokkal (lásd alább) a felhasználók minden aspektusa kezelhető. Továbbá ezzel a szerepkörrel rendelkező felhasználók is csoportok létrehozásához és kezeléséhez minden. Ez a szerepkör is létrehozhatók és kezelhetők a felhasználói nézetek, kezelhetik a támogatási jegyeket, és szolgáltatások állapotának figyelése lehetővé teszi.

  | | |
  | --- | --- |
  |Általános engedélyek|<p>Felhasználók és csoportok létrehozása</p><p>Létre és kezelhet felhasználói nézetek</p><p>Office-támogatási jegyek kezelése|
  |<p>Az összes felhasználó többek között az összes rendszergazda</p>|<p>Licencek kezelése</p><p>Egyszerű felhasználónév kivételével az összes felhasználó tulajdonságainak kezelése</p>
  |Csak a nem rendszergazda jogosultságú és a következők egyikét a korlátozott rendszergazdai szerepkörök felhasználóknak:<ul><li>Directory-olvasók<li>Vendég meghívója<li>Ügyfélszolgálati adminisztrátor<li>Üzenetközpont-olvasó<li>Jelentésolvasó<li>Felhasználóifiók-adminisztrátor|<p>Törlés és helyreállítás</p><p>Letiltása és engedélyezése</p><p>Érvénytelenítéséhez frissítési jogkivonatok</p><p>Egyszerű felhasználónév többek között az összes felhasználói tulajdonságainak kezelése</p><p>Új jelszó létrehozása</p><p>Frissítés (FIDO) eszközkulcsok</p>

Az alábbi táblázatok ismertetik az Azure Active Directory minden egyes szerepkörhöz megadott engedélyeket. Egyes szerepkörök lehet további engedélyek a Microsoft services, Azure Active Directory-en kívül.

### <a name="application-administrator"></a>Alkalmazás-rendszergazda
Az alkalmazásregisztrációkkal és a vállalati alkalmazásokkal kapcsolatos összes létrehozási és felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Applications/Audience/Update | Frissítés az Azure Active Directoryban applications.audience tulajdonság. |
| Microsoft.aad.Directory/Applications/Authentication/Update | Frissítés az Azure Active Directoryban applications.authentication tulajdonság. |
| Microsoft.aad.Directory/Applications/Basic/Update | Alkalmazások az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Applications/Create | Alkalmazások létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Applications/credentials/Update | Frissítés az Azure Active Directoryban applications.credentials tulajdonság. |
| Microsoft.aad.Directory/Applications/DELETE | Törölje az alkalmazást az Azure Active Directoryban. |
| Microsoft.aad.Directory/Applications/Owners/Update | Az applications.owners tulajdonság az Azure Active Directory frissítéséhez. |
| Microsoft.aad.Directory/Applications/permissions/Update | Frissítés az Azure Active Directoryban applications.permissions tulajdonság. |
| Microsoft.aad.Directory/Applications/Policies/Update | Frissítés az Azure Active Directoryban applications.policies tulajdonság. |
| microsoft.aad.directory/appRoleAssignments/create | Alkalmazások szerepkör-hozzárendeléseinek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/read | Olvassa el az alkalmazások szerepkör-hozzárendelései az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/update | Az Azure Active Directoryban hozzárendeléseinek frissítése. |
| microsoft.aad.directory/appRoleAssignments/delete | Alkalmazások szerepkör-hozzárendelései az Azure Active Directoryban törölje. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Frissítés az Azure Active Directoryban policies.applicationConfiguration tulajdonság. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Törölje a szabályzatok az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Frissítés az Azure Active Directoryban policies.applicationConfiguration tulajdonság. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/basic/update | Az Azure Active Directoryban szolgáltatásnevek alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/servicePrincipals/create | Szolgáltatásnevek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/delete | Szolgáltatásnevek törlése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Frissítse a servicePrincipals.appRoleAssignedTo tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Frissítse a servicePrincipals.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/update | Frissítse a servicePrincipals.owners tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/update | Frissítés az Azure Active Directoryban servicePrincipals.policies tulajdonság. |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| microsoft.aad.reports/allEntities/read | Azure AD-jelentések olvasása. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="application-developer"></a>Alkalmazásfejlesztő
Hozhat létre alkalmazást az alkalmazásregisztrációk független a "felhasználók regisztrálhatnak alkalmazásokat" beállítás.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Alkalmazások létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Alkalmazások szerepkör-hozzárendeléseinek létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Az oauth2-engedélyezések létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Szolgáltatásnevek létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |

### <a name="billing-administrator"></a>Számlázási adminisztrátor
Számlázással kapcsolatos általános feladatokat hajthat végre, például frissítheti a fizetési adatokat.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Organization/Basic/Update | Az Azure Active Directory szervezeti alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Frissítés az Azure Active Directoryban organization.trustedCAsForPasswordlessAuth tulajdonság. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.commerce.billing/allEntities/allTasks | Jogosultság az Office 365 számlázásának teljes körű felügyeletére. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="cloud-application-administrator"></a>Felhőalkalmazás-rendszergazda
Az Application Proxy kivételével az alkalmazásregisztrációkkal és a vállalati alkalmazásokkal kapcsolatos összes létrehozási és felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Applications/Audience/Update | Frissítés az Azure Active Directoryban applications.audience tulajdonság. |
| Microsoft.aad.Directory/Applications/Authentication/Update | Frissítés az Azure Active Directoryban applications.authentication tulajdonság. |
| Microsoft.aad.Directory/Applications/Basic/Update | Alkalmazások az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Applications/Create | Alkalmazások létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Applications/credentials/Update | Frissítés az Azure Active Directoryban applications.credentials tulajdonság. |
| Microsoft.aad.Directory/Applications/DELETE | Törölje az alkalmazást az Azure Active Directoryban. |
| Microsoft.aad.Directory/Applications/Owners/Update | Az applications.owners tulajdonság az Azure Active Directory frissítéséhez. |
| Microsoft.aad.Directory/Applications/permissions/Update | Frissítés az Azure Active Directoryban applications.permissions tulajdonság. |
| Microsoft.aad.Directory/Applications/Policies/Update | Frissítés az Azure Active Directoryban applications.policies tulajdonság. |
| microsoft.aad.directory/appRoleAssignments/create | Alkalmazások szerepkör-hozzárendeléseinek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/update | Az Azure Active Directoryban hozzárendeléseinek frissítése. |
| microsoft.aad.directory/appRoleAssignments/delete | Alkalmazások szerepkör-hozzárendelései az Azure Active Directoryban törölje. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Frissítés az Azure Active Directoryban policies.applicationConfiguration tulajdonság. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Törölje a szabályzatok az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Frissítés az Azure Active Directoryban policies.applicationConfiguration tulajdonság. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Policies.applicationConfiguration tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Frissítse a servicePrincipals.appRoleAssignedTo tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Frissítse a servicePrincipals.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/basic/update | Az Azure Active Directoryban szolgáltatásnevek alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/servicePrincipals/create | Szolgáltatásnevek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/delete | Szolgáltatásnevek törlése az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/update | Frissítse a servicePrincipals.owners tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/update | Frissítés az Azure Active Directoryban servicePrincipals.policies tulajdonság. |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| microsoft.aad.reports/allEntities/read | Azure AD-jelentések olvasása. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="cloud-device-administrator"></a>Felhőbeli Eszközrendszergazda
Teljes körű jogosultság az eszközök Azure AD-beli kezelésére.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Devices/DELETE | Eszközök törlése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Devices/disable | Letilthatja az eszközöket az Azure Active Directoryban. |
| Microsoft.aad.Directory/Devices/enable | Engedélyezze az eszközök az Azure Active Directoryban. |
| microsoft.aad.reports/allEntities/read | Azure AD-jelentések olvasása. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="company-administrator"></a>Céges rendszergazda
Az Azure AD-identitásokat használó Azure AD- és Microsoft-szolgáltatásokkal kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a szerepkört.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Hozzon létre és administrativeUnits, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/applications/allProperties/allTasks | Hozzon létre, és törölje az alkalmazást, és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Hozzon létre és hozzárendeléseinek, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/contacts/allProperties/allTasks | Hozzon létre és törléséhez, és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Hozzon létre és szerződések törlése és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/devices/allProperties/allTasks | Hozzon létre, és törölje az eszközt, és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Hozzon létre és directoryRoles, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Hozzon létre és directoryRoleTemplates, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/domains/allProperties/allTasks | És törölje a tartományok, és olvassa el tulajdonságok létrehozása és frissítése az összes az Azure Active Directoryban. |
| microsoft.aad.directory/groups/allProperties/allTasks | És törölje a csoportokat, és olvassa el tulajdonságok létrehozása és frissítése az összes az Azure Active Directoryban. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Hozzon létre és groupSettings, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Hozzon létre és groupSettingTemplates, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Hozzon létre és loginTenantBranding, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Hozzon létre és törölni az oauth2-engedélyezések, és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/organization/allProperties/allTasks | Hozzon létre és törlése a szervezeten belül, és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/policies/allProperties/allTasks | És törli a szabályzatokat, és olvassa el tulajdonságok létrehozása és frissítése az összes az Azure Active Directoryban. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | És törölje a visszaadandó szerepkörkiosztások száma, és olvassa el tulajdonságok létrehozása és frissítése az összes az Azure Active Directoryban. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Hozzon létre és roleDefinitions, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Hozzon létre és scopedRoleMemberships, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/serviceAction/activateService | A Activateservice szolgáltatási művelet hajtható végre, az Azure Active Directoryban |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | A Disabledirectoryfeature szolgáltatási művelet hajtható végre, az Azure Active Directoryban |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | A Enabledirectoryfeature szolgáltatási művelet hajtható végre, az Azure Active Directoryban |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | A Getavailableextentionproperties szolgáltatási művelet hajtható végre, az Azure Active Directoryban |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Hozzon létre és szolgáltatásnevek, törlése és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Hozzon létre és subscribedSkus, törölje és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/users/allProperties/allTasks | Hozzon létre, és törli a felhasználókat, és olvassa el, és az összes tulajdonság az Azure Active Directory frissítéséhez. |
| microsoft.aad.directorySync/allEntities/allTasks | Az összes művelet végrehajtása az Azure AD Connectben. |
| microsoft.aad.identityProtection/allEntities/allTasks | Hozzon létre és az összes erőforrás törlése, és olvassa el és microsoft.aad.identityProtection az alapvető tulajdonságainak frissítése. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Olvassa el a microsoft.aad.privilegedIdentityManagement található összes erőforrást. |
| microsoft.aad.reports/allEntities/allTasks | Azure AD-jelentések olvasása és konfigurálása. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.informationProtection/allEntities/allTasks | Az Azure Information Protection minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.commerce.billing/allEntities/allTasks | Jogosultság az Office 365 számlázásának teljes körű felügyeletére. |
| microsoft.intune/allEntities/allTasks | Az Intune teljes körű felügyelete. |
| Microsoft.office365.complianceManager/allEntities/allTasks | Office 365-megfelelőségi vezető minden szempontjának kezeléséhez |
| Microsoft.office365.Exchange/allEntities/allTasks | Az Exchange Online teljes körű felügyelete. |
| Microsoft.office365.lockbox/allEntities/allTasks | Office 365-ügyfél kulcstároló minden szempontjának kezeléséhez |
| microsoft.powerApps.powerBI/allEntities/allTasks | A Power BI teljes körű felügyelete. |
| Microsoft.office365.protectionCenter/allEntities/allTasks | Az Office 365 védelmi központ minden szempontjának kezeléséhez. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Hozzon létre és az összes erőforrás törlése, és olvassa el és microsoft.office365.sharepoint az alapvető tulajdonságainak frissítése. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Összes felügyeleti jogosultsággal a Skype vállalati online verziójához. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Jogosultság a Dynamics 365 számlázásának teljes körű felügyeletére. |

### <a name="compliance-administrator"></a>Szabályozási ügyintéző
Megtekintheti és kezelheti a megfelelőségi konfigurációt és jelentéseket az Azure AD-ban és az Office 365-ben.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.complianceManager/allEntities/allTasks | Office 365-megfelelőségi vezető minden szempontjának kezeléséhez |
| Microsoft.office365.Exchange/allEntities/allTasks | Az Exchange Online teljes körű felügyelete. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Hozzon létre és az összes erőforrás törlése, és olvassa el és microsoft.office365.sharepoint az alapvető tulajdonságainak frissítése. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Összes felügyeleti jogosultsággal a Skype vállalati online verziójához. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="conditional-access-administrator"></a>Feltételes hozzáférésű rendszergazda
A feltételes hozzáférési lehetőségekkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Policies.conditionalAccess tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Frissítés az Azure Active Directoryban policies.conditionalAccess tulajdonság. |
| microsoft.aad.directory/policies/conditionalAccess/create | Szabályzatok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Törölje a szabályzatok az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Policies.conditionalAccess tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Frissítés az Azure Active Directoryban policies.conditionalAccess tulajdonság. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Policies.conditionalAccess tulajdonság olvasása az Azure Active Directoryban. |

### <a name="crm-service-administrator"></a>CRM-szolgáltatásadminisztrátor
A Dynamics 365 termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Jogosultság a Dynamics 365 számlázásának teljes körű felügyeletére. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="customer-lockbox-access-approver"></a>Ügyfélszéf hozzáférés-jóváhagyója
Jóváhagyhatja a szervezeti ügyféladatok elérésére vonatkozó Microsoft-támogatási kérelmeket.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| Microsoft.office365.lockbox/allEntities/allTasks | Office 365-ügyfél kulcstároló minden szempontjának kezeléséhez |

### <a name="device-administrators"></a>Eszközadminisztrátorok
Ezen szerepkör tagjai a helyi Rendszergazdák csoport, az Azure AD-hez csatlakoztatott eszközök kerülnek.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Az Azure Active Directoryban groupSettings a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Az Azure Active Directoryban groupSettingTemplates a alapvető tulajdonságainak olvasása. |

### <a name="directory-readers"></a>Directory-olvasók
Alapszintű directory információkat olvashatja. Az alkalmazásokhoz való hozzáférés biztosítása, nem javasolt a felhasználók számára.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a szerepkört.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Az Azure Active Directoryban administrativeUnits a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/administrativeUnits/members/read | Az administrativeUnits.members tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Applications/Audience/Read | Applications.audience tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Applications/Authentication/Read | Applications.authentication tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Applications/Basic/Read | Az alkalmazások az Azure Active Directory alapvető tulajdonságainak olvasása. |
| Microsoft.aad.Directory/Applications/credentials/Read | Applications.credentials tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Applications/Owners/Read | Az applications.owners tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Applications/permissions/Read | Applications.permissions tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Applications/Policies/Read | Applications.policies tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Contacts/Basic/Read | A partnerek az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/contacts/memberOf/read | Contacts.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Contracts/Basic/Read | A szerződés az Azure Active Directory alapvető tulajdonságainak olvasása. |
| Microsoft.aad.Directory/Devices/Basic/Read | Az eszközökön az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/devices/memberOf/read | A devices.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/devices/registeredOwners/read | Devices.registeredOwners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/devices/registeredUsers/read | Devices.registeredUsers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/directoryRoles/basic/read | Az Azure Active Directoryban directoryRoles a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | DirectoryRoles.eligibleMembers tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/directoryRoles/members/read | DirectoryRoles.members tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Domains/Basic/Read | Az Azure Active Directory-tartományokban alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Groups.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Basic/Read | A csoportok az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/groups/memberOf/read | Groups.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Members/Read | Groups.members tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Owners/Read | Olvassa el a groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Settings/Read | Groups.settings tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/groupSettings/basic/read | Az Azure Active Directoryban groupSettings a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Az Azure Active Directoryban groupSettingTemplates a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Az Azure Active Directoryban az oauth2-engedélyezések alapvető tulajdonságainak olvasása. |
| Microsoft.aad.Directory/Organization/Basic/Read | A szervezet az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Organization.trustedCAsForPasswordlessAuth tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/roleAssignments/basic/read | A visszaadandó szerepkörkiosztások száma az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/roleDefinitions/basic/read | Az Azure Active Directoryban roleDefinitions a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | A servicePrincipals.appRoleAssignedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | A servicePrincipals.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/basic/read | A szolgáltatásnevek Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | A servicePrincipals.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | A servicePrincipals.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | ServicePrincipals.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/read | A servicePrincipals.owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/read | ServicePrincipals.policies tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/subscribedSkus/basic/read | Az Azure Active Directoryban subscribedSkus a alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/users/appRoleAssignments/read | A users.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Basic/Read | A felhasználók az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/users/directReports/read | Users.directReports tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/invitedBy/read | A users.invitedBy tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/invitedUsers/read | Users.invitedUsers tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Manager/Read | A users.manager tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/memberOf/read | A users.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Users.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedDevices/read | A users.ownedDevices tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedObjects/read | A users.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/registeredDevices/read | A users.registeredDevices tulajdonság olvasása az Azure Active Directoryban. |

### <a name="directory-synchronization-accounts"></a>Címtár-szinkronizálási fiókok
Csak Azure AD Connect szolgáltatást használják.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a szerepkört.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Frissítés az Azure Active Directoryban organization.dirSync tulajdonság. |
| Microsoft.aad.Directory/Policies/Create | Szabályzatok létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/DELETE | Törölje a szabályzatok az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/Basic/Read | A szabályzatok az Azure Active Directory alapvető tulajdonságainak olvasása. |
| Microsoft.aad.Directory/Policies/Basic/Update | Házirendek az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Policies/Owners/Read | Policies.owners tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/Owners/Update | Frissítés az Azure Active Directoryban policies.owners tulajdonság. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Policies.policiesAppliedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | A servicePrincipals.appRoleAssignedTo tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Frissítse a servicePrincipals.appRoleAssignedTo tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | A servicePrincipals.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Frissítse a servicePrincipals.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/basic/read | A szolgáltatásnevek Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/servicePrincipals/basic/update | Az Azure Active Directoryban szolgáltatásnevek alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/servicePrincipals/create | Szolgáltatásnevek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | A servicePrincipals.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | A servicePrincipals.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/read | A servicePrincipals.owners tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/owners/update | Frissítse a servicePrincipals.owners tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | ServicePrincipals.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/read | ServicePrincipals.policies tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/servicePrincipals/policies/update | Frissítés az Azure Active Directoryban servicePrincipals.policies tulajdonság. |
| microsoft.aad.directorySync/allEntities/allTasks | Az összes művelet végrehajtása az Azure AD Connectben. |

### <a name="directory-writers"></a>Directory-írók
Olvasás, és alapszintű directory adatokat írnak a. Az alkalmazásokhoz való hozzáférés biztosítása, nem javasolt a felhasználók számára.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/groups/Create | Csoportok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/createAsOwner | Csoportok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Frissítés az Azure Active Directoryban groups.appRoleAssignments tulajdonság. |
| Microsoft.aad.Directory/groups/Basic/Update | Az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/groups/Members/Update | Frissítés az Azure Active Directoryban groups.members tulajdonság. |
| Microsoft.aad.Directory/groups/Owners/Update | Frissítse a groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Settings/Update | Frissítés az Azure Active Directoryban groups.settings tulajdonság. |
| microsoft.aad.directory/groupSettings/basic/update | Az Azure Active Directoryban groupSettings alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/groupSettings/create | GroupSettings létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groupSettings/delete | Az Azure Active Directoryban groupSettings törlése. |
| microsoft.aad.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| Microsoft.aad.Directory/Users/Basic/Update | Az Azure Active Directory felhasználók alapszintű tulajdonságainak frissítése. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Manager/Update | A users.manager tulajdonság frissítése az Azure Active Directory frissítéséhez. |
| microsoft.aad.directory/users/userPrincipalName/update | Frissítés az Azure Active Directoryban users.userPrincipalName tulajdonság. |

### <a name="exchange-service-administrator"></a>Exchange-szolgáltatások rendszergazdája
Az Exchange termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.Exchange/allEntities/allTasks | Az Exchange Online teljes körű felügyelete. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="guest-inviter"></a>Vendég meghívója
A tagok vendégmeghívási jogosultágát szabályozó beállítástól függetlenül meghívhat vendégeket.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a szerepkört.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | A users.appRoleAssignments tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Basic/Read | A felhasználók az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.aad.directory/users/directReports/read | Users.directReports tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/invitedBy/read | A users.invitedBy tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/inviteGuest | Vendégfelhasználók meghívása az Azure Active Directoryba. |
| microsoft.aad.directory/users/invitedUsers/read | Users.invitedUsers tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Manager/Read | A users.manager tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/memberOf/read | A users.memberOf tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Users.oAuth2PermissionGrants tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedDevices/read | A users.ownedDevices tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/ownedObjects/read | A users.ownedObjects tulajdonság olvasása az Azure Active Directoryban. |
| microsoft.aad.directory/users/registeredDevices/read | A users.registeredDevices tulajdonság olvasása az Azure Active Directoryban. |

### <a name="helpdesk-administrator"></a>Ügyfélszolgálati adminisztrátor
Alaphelyzetbe állíthatja a nem rendszergazdák és a segélyszolgálat rendszergazdák jelszavát.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Password/Update | Frissítés az Azure Active Directory összes felhasználó jelszavát. További részleteket talál online dokumentációjában talál. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="information-protection-administrator"></a>Information Protection-rendszergazda
Az Azure Information Protection termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Az Azure Information Protection minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="intune-service-administrator"></a>Intune szolgáltatás rendszergazdája
Az Intune termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Contacts/Basic/Update | Partnerek az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Contacts/Create | Az Azure Active Directoryban létre. |
| Microsoft.aad.Directory/Contacts/DELETE | Az Azure Active Directoryban törléséhez. |
| Microsoft.aad.Directory/Devices/Basic/Update | Az Azure Active Directoryban eszközök alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Devices/Create | Eszközök létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Devices/DELETE | Eszközök törlése az Azure Active Directoryban. |
| microsoft.aad.directory/devices/registeredOwners/update | Frissítés az Azure Active Directoryban devices.registeredOwners tulajdonság. |
| microsoft.aad.directory/devices/registeredUsers/update | Frissítés az Azure Active Directoryban devices.registeredUsers tulajdonság. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Frissítés az Azure Active Directoryban groups.appRoleAssignments tulajdonság. |
| Microsoft.aad.Directory/groups/Basic/Update | Az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/groups/Create | Csoportok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/createAsOwner | Csoportok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| Microsoft.aad.Directory/groups/DELETE | Az Azure Active Directoryban csoportok törlése. |
| microsoft.aad.directory/groups/hiddenMembers/read | Groups.hiddenMembers tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Members/Update | Frissítés az Azure Active Directoryban groups.members tulajdonság. |
| Microsoft.aad.Directory/groups/Owners/Update | Frissítse a groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/restore | Csoportok visszaállítása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Settings/Update | Frissítés az Azure Active Directoryban groups.settings tulajdonság. |
| microsoft.aad.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Basic/Update | Az Azure Active Directory felhasználók alapszintű tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Users/Manager/Update | A users.manager tulajdonság frissítése az Azure Active Directory frissítéséhez. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.intune/allEntities/allTasks | Az Intune teljes körű felügyelete. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="license-administrator"></a>Speciális licencadminisztrátor
A felhasználók és csoportok terméklicencek kezelheti.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| microsoft.aad.directory/users/usageLocation/update | Frissítse a users.usageLocation tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="lync-service-administrator"></a>Lync-szolgáltatások rendszergazdája
A Skype Vállalati verzió termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Összes felügyeleti jogosultsággal a Skype vállalati online verziójához. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="message-center-reader"></a>Üzenetközpont-olvasó
Csak a szervezet Office 365 Üzenetközpontbeli üzeneteit és frissítéseit olvashatja. 

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.accessmessagecenter/allEntities/allTasks | Az összes erőforrás létrehozása és törlése, valamint az általános tulajdonságok olvasása és frissítése az Üzenetközpontban. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |

### <a name="partner-tier1-support"></a>1. szintű partnertámogatás
Ne használjon – általános használatra nem alkalmas.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Contacts/Basic/Update | Partnerek az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Contacts/Create | Az Azure Active Directoryban létre. |
| Microsoft.aad.Directory/Contacts/DELETE | Az Azure Active Directoryban törléséhez. |
| Microsoft.aad.Directory/groups/Create | Csoportok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/createAsOwner | Csoportok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| Microsoft.aad.Directory/groups/Members/Update | Frissítés az Azure Active Directoryban groups.members tulajdonság. |
| Microsoft.aad.Directory/groups/Owners/Update | Frissítse a groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| microsoft.aad.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| Microsoft.aad.Directory/Users/Basic/Update | Az Azure Active Directory felhasználók alapszintű tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Users/DELETE | Felhasználók törlése az Azure Active Directoryban. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Manager/Update | A users.manager tulajdonság frissítése az Azure Active Directory frissítéséhez. |
| Microsoft.aad.Directory/Users/Password/Update | Frissítés az Azure Active Directory összes felhasználó jelszavát. További részleteket talál online dokumentációjában talál. |
| Microsoft.aad.Directory/Users/restore | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/users/userPrincipalName/update | Frissítés az Azure Active Directoryban users.userPrincipalName tulajdonság. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="partner-tier2-support"></a>2. szintű partnertámogatás
Ne használjon – általános használatra nem alkalmas.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Contacts/Basic/Update | Partnerek az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Contacts/Create | Az Azure Active Directoryban létre. |
| Microsoft.aad.Directory/Contacts/DELETE | Az Azure Active Directoryban törléséhez. |
| microsoft.aad.directory/domains/allTasks | Hozzon létre és tartományok törlése, és olvassa el és az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/groups/Create | Csoportok létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/DELETE | Az Azure Active Directoryban csoportok törlése. |
| Microsoft.aad.Directory/groups/Members/Update | Frissítés az Azure Active Directoryban groups.members tulajdonság. |
| Microsoft.aad.Directory/groups/restore | Csoportok visszaállítása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Organization/Basic/Update | Az Azure Active Directory szervezeti alapvető tulajdonságainak frissítése. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Frissítés az Azure Active Directoryban organization.trustedCAsForPasswordlessAuth tulajdonság. |
| microsoft.aad.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| Microsoft.aad.Directory/Users/Basic/Update | Az Azure Active Directory felhasználók alapszintű tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Users/DELETE | Felhasználók törlése az Azure Active Directoryban. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Manager/Update | A users.manager tulajdonság frissítése az Azure Active Directory frissítéséhez. |
| Microsoft.aad.Directory/Users/Password/Update | Frissítés az Azure Active Directory összes felhasználó jelszavát. További részleteket talál online dokumentációjában talál. |
| Microsoft.aad.Directory/Users/restore | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/users/userPrincipalName/update | Frissítés az Azure Active Directoryban users.userPrincipalName tulajdonság. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="power-bi-service-administrator"></a>Power BI-szolgáltatásadminisztrátor
A Power BI termékkel kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| microsoft.powerApps.powerBI/allEntities/allTasks | A Power BI teljes körű felügyelete. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="privileged-role-administrator"></a>Kiemelt szerepkörű rendszergazda
Az Azure AD szerepkör-hozzárendelések és a Privileged Identity Management minden aspektusát felügyelheti.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/directoryRoles/update | Az Azure Active Directoryban directoryRoles frissítése. |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Hozzon létre és az összes erőforrás törlése, és olvassa el és microsoft.aad.privilegedIdentityManagement az alapvető tulajdonságainak frissítése. |

### <a name="reports-reader"></a>Jelentésolvasó
Megtekintheti a bejelentkezési és a naplózási jelentéseket.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.reports/allEntities/read | Azure AD-jelentések olvasása. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.usageReports/allEntities/Read | Az Office 365-beli használati jelentések olvasása. |

### <a name="security-administrator"></a>Biztonsági rendszergazda
Olvassa el a biztonsági adatokat és jelentéseket, és beállíthatja az Azure AD-konfiguráció és az Office 365.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Applications/Policies/Update | Frissítés az Azure Active Directoryban applications.policies tulajdonság. |
| Microsoft.aad.Directory/Policies/Basic/Update | Házirendek az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Policies/Create | Szabályzatok létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/DELETE | Törölje a szabályzatok az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/Owners/Update | Frissítés az Azure Active Directoryban policies.owners tulajdonság. |
| microsoft.aad.directory/servicePrincipals/policies/update | Frissítés az Azure Active Directoryban servicePrincipals.policies tulajdonság. |
| microsoft.aad.identityProtection/allEntities/read | Olvassa el a microsoft.aad.identityProtection található összes erőforrást. |
| microsoft.aad.identityProtection/allEntities/update | Frissítés microsoft.aad.identityProtection található összes erőforrást. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Olvassa el a microsoft.aad.privilegedIdentityManagement található összes erőforrást. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| Microsoft.office365.protectionCenter/allEntities/Read | Az Office 365 Védelmi központ minden aspektusának olvasása. |
| Microsoft.office365.protectionCenter/allEntities/Update | Frissítés microsoft.office365.protectionCenter található összes erőforrást. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="security-reader"></a>Biztonsági olvasó
Megtekintheti a biztonsági információkat és jelentéseket az Azure AD-ban és az Office 365-ben.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.identityProtection/allEntities/read | Olvassa el a microsoft.aad.identityProtection található összes erőforrást. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Olvassa el a microsoft.aad.privilegedIdentityManagement található összes erőforrást. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| Microsoft.office365.protectionCenter/allEntities/Read | Az Office 365 Védelmi központ minden aspektusának olvasása. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="service-support-administrator"></a>Szolgáltatástámogatási rendszergazda
Megtekintheti a szolgáltatásállapot-adatokat, és kezelheti a támogatási jegyeket.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="sharepoint-service-administrator"></a>SharePoint szolgáltatás-rendszergazda
A SharePoint-szolgáltatásokkal kapcsolatos összes felügyeleti jogosultsággal rendelkezik.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Hozzon létre és az összes erőforrás törlése, és olvassa el és microsoft.office365.sharepoint az alapvető tulajdonságainak frissítése. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |

### <a name="teams-communications-administrator"></a>Csapatok kommunikáció rendszergazda
Kezelheti a hívási és értekezletek funkciók a Microsoft Teams szolgáltatásban.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Policies/Basic/Read | A szabályzatok az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| Microsoft.office365.usageReports/allEntities/Read | Az Office 365-beli használati jelentések olvasása. |

### <a name="teams-communications-support-engineer"></a>Csapatok kommunikáció támogatási szakértő
Kommunikációs csapatok fejlett eszközökkel kapcsolatos problémák elhárításához.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Policies/Basic/Read | A szabályzatok az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="teams-communications-support-specialist"></a>Csapatok kommunikáció támogatási szakértő
Kommunikációs csapatok alapvető eszközökkel kapcsolatos problémák elhárításához.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| Microsoft.aad.Directory/Policies/Basic/Read | A szabályzatok az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |

### <a name="teams-service-administrator"></a>Csapatok szolgáltatás-rendszergazda
Kezelheti a Microsoft Teams szolgáltatás.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

  > [!NOTE]
  > Ez a szerepkör az Azure Active Directoryn kívül további engedélyekkel rendelkezik. További információkért lásd a fenti szerepkör leírása.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Groups.hiddenMembers tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Policies/Basic/Read | A szabályzatok az Azure Active Directory alapvető tulajdonságainak olvasása. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |
| Microsoft.office365.usageReports/allEntities/Read | Az Office 365-beli használati jelentések olvasása. |

### <a name="user-account-administrator"></a>Felhasználóifiók-adminisztrátor
A felhasználókkal és a csoportokkal kapcsolatos összes felügyeleti rendelkezik, beleértve a korlátozott rendszergazdák jelszavának alaphelyzetbe állítását is.

  > [!NOTE]
  > Ez a szerepkör további engedélyek örökli a címtár olvasói szerepköréhez.
  >
  >

| **Műveletek** | **Leírás** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Alkalmazások szerepkör-hozzárendeléseinek létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/appRoleAssignments/delete | Alkalmazások szerepkör-hozzárendelései az Azure Active Directoryban törölje. |
| microsoft.aad.directory/appRoleAssignments/update | Az Azure Active Directoryban hozzárendeléseinek frissítése. |
| Microsoft.aad.Directory/Contacts/Basic/Update | Partnerek az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Contacts/Create | Az Azure Active Directoryban létre. |
| Microsoft.aad.Directory/Contacts/DELETE | Az Azure Active Directoryban törléséhez. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Frissítés az Azure Active Directoryban groups.appRoleAssignments tulajdonság. |
| Microsoft.aad.Directory/groups/Basic/Update | Az Azure Active Directory alapvető tulajdonságainak frissítése. |
| Microsoft.aad.Directory/groups/Create | Csoportok létrehozása az Azure Active Directoryban. |
| microsoft.aad.directory/groups/createAsOwner | Csoportok létrehozása az Azure Active Directoryban. Létrehozót első tulajdonosként kerül, és a létrehozott objektum beleszámít a létrehozó 250 létrehozott objektumos kvótáját. |
| Microsoft.aad.Directory/groups/DELETE | Az Azure Active Directoryban csoportok törlése. |
| microsoft.aad.directory/groups/hiddenMembers/read | Groups.hiddenMembers tulajdonság olvasása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Members/Update | Frissítés az Azure Active Directoryban groups.members tulajdonság. |
| Microsoft.aad.Directory/groups/Owners/Update | Frissítse a groups.owners tulajdonság frissítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/restore | Csoportok visszaállítása az Azure Active Directoryban. |
| Microsoft.aad.Directory/groups/Settings/Update | Frissítés az Azure Active Directoryban groups.settings tulajdonság. |
| microsoft.aad.directory/users/appRoleAssignments/update | Frissítse a users.appRoleAssignments tulajdonság az Azure Active Directoryban. |
| microsoft.aad.directory/users/assignLicense | Az Azure Active Directory felhasználók licenceinek kezelése. |
| Microsoft.aad.Directory/Users/Basic/Update | Az Azure Active Directory felhasználók alapszintű tulajdonságainak frissítése. |
| Microsoft.aad.Directory/Users/Create | Felhasználók létrehozása az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/DELETE | Felhasználók törlése az Azure Active Directoryban. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Az összes felhasználófrissítési jogkivonat érvénytelenítése az Azure Active Directoryban. |
| Microsoft.aad.Directory/Users/Manager/Update | A users.manager tulajdonság frissítése az Azure Active Directory frissítéséhez. |
| Microsoft.aad.Directory/Users/Password/Update | Frissítés az Azure Active Directory összes felhasználó jelszavát. További részleteket talál online dokumentációjában talál. |
| Microsoft.aad.Directory/Users/restore | Törölt felhasználók visszaállítása az Azure Active Directoryban. |
| microsoft.aad.directory/users/userPrincipalName/update | Frissítés az Azure Active Directoryban users.userPrincipalName tulajdonság. |
| microsoft.azure.accessService/allEntities/allTasks | Az Azure szolgáltatás minden szempontjának kezeléséhez. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Olvassa el, és konfigurálja az Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Létrehozhat és kezelhet az Azure támogatási jegyeket. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Office 365-szolgáltatás-állapot olvasása és konfigurálása. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Office 365-támogatási jegyek létrehozása és kezelése. |


## <a name="deprecated-roles"></a>Elavult szerepkörök

A következő szerepkörök nem használható. Ezeket a már elavult, és eltávolítja a későbbiekben az Azure AD-ből.

* Speciális licencek adminisztrátora
* Eszköz-csatlakoztatás
* Eszközkezelők
* Eszközök felhasználói számára
* Ellenőrzött e-mail című felhasználó-létrehozó
* Postaláda-adminisztrátor
* Munkahelyi eszköz-csatlakoztatás

## <a name="next-steps"></a>További lépések

* Felhasználók hozzárendelése egy Azure-előfizetés rendszergazdájaként kapcsolatos további információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../../role-based-access-control/role-assignments-portal.md)
* Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* További információk az Azure Active Directory és az Azure-előfizetés kapcsolatáról: [Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directoryhoz?](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
