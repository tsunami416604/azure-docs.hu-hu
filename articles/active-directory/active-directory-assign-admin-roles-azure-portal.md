---
title: Rendszergazdai szerepkörök hozzárendelése az Azure Active Directory |} Microsoft Docs
description: Egy rendszergazdai szerepkörrel is vegye fel a felhasználók, rendszergazdai szerepkörök hozzárendelése, felhasználók új jelszavainak létrehozására, felhasználói licencek kezelése vagy tartományok kezelése. A rendszergazda szerepkörrel felruházott felhasználó ugyanazokkal az engedélyekkel rendelkezik, amelyhez a szervezet előfizetett összes felhőszolgáltatás.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 06/07/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 1de2482b7795bbed82874b6eea29f89f1ff52560
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938412"
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Rendszergazdai jogosultságok kiosztása az Azure Active Directoryban

Azure Active Directory (Azure AD) segítségével meghatározhat külön rendszergazdák számára, hogy különféle célokat szolgál. Rendszergazdák van a különböző szolgáltatások eléréséhez, az Azure portálon, és attól függően, hogy a szerepkör is hozzon létre vagy felhasználók szerkesztése, rendszergazdai szerepkörök hozzárendelése mások, felhasználók új jelszavainak létrehozására, felhasználói licencek kezelése és kezelhetik a tartományokat, többek között. A rendszergazda szerepkörrel felruházott felhasználó ugyanazokkal az engedélyekkel rendelkeznek az összes, amelyhez a szervezet fizet elő, függetlenül attól, hogy rendelje hozzá a szerepkört az Office 365 portál és az Azure portálon vagy az Azure AD-modul segítségével felhőszolgáltatások A Windows PowerShell.

## <a name="details-about-the-global-administrator-role"></a>A globális rendszergazdai szerepkörrel részleteit
A globális rendszergazda az összes felügyeleti funkcióhoz hozzáférése van. Alapértelmezés szerint a személy, aki az Azure-előfizetésre regisztrál a címtár globális rendszergazdai szerepkörrel rendelkeznek. Csak a globális rendszergazdák egyéb rendszergazdai szerepköröket rendelhet.

## <a name="assign-or-remove-administrator-roles"></a>Rendelje hozzá, vagy távolítsa el a rendszergazdai szerepkörökről
Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban a felhasználó további tudnivalókért lásd: [felhasználó hozzárendelése az Azure Active Directory rendszergazdai szerepkörök](fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="available-roles"></a>Elérhető szerepkörök
A következő rendszergazdai szerepkörök állnak rendelkezésre:

* **Alkalmazás-rendszergazda**: Ebben a szerepkörben levő felhasználók létrehozhatnak és vállalati alkalmazások, az alkalmazás regisztrációk és az alkalmazások proxy beállításait minden szempontjának kezeléséhez. Ez a szerepkör is számára lehetőséget ad a delegált jogosultságokkal sikeresen telepítették, és a Microsoft Graph és az Azure AD Graph Alkalmazásengedélyek hozzájárulás. A szerepkör tagjai nem hozzá szeretné adni tulajdonosok új alkalmazás-regisztráció és a vállalati alkalmazások létrehozásakor.

* **Alkalmazásfejlesztő**: a szerepet betöltő felhasználók alkalmazás regisztrációk hozhat létre. Ha a "A felhasználók regisztrálhatják alkalmazások" beállítása nem. Ezt a szerepkört is lehetővé teszi, hogy saját maguk beleegyezését tagok Ha a "Felhasználók is hozzájárul a nevében a vállalati adatokhoz hozzáférő alkalmazásokat" beállítás értéke nem. A szerepkör tagjai hozzá szeretné adni tulajdonosok új alkalmazás-regisztráció és a vállalati alkalmazások létrehozásakor.

* **Számlázási rendszergazda**: lebonyolítja a vásárlásokat, kezeli az előfizetéseket, támogatási jegyeket, és figyeli a szolgáltatás állapotát.

* **Alkalmazás-rendszergazda felhő**: a szerepet betöltő felhasználók ugyanazokkal az engedélyekkel rendelkezzenek, az alkalmazás-rendszergazda szerepkört kivéve képes kezelni az alkalmazásproxy. A szerepkörök a létrehozása és kezelése a vállalati alkalmazások és az alkalmazás regisztrációk minden elemét. Ez a szerepkör is számára lehetőséget ad a delegált jogosultságokkal sikeresen telepítették, és a Microsoft Graph és az Azure AD Graph Alkalmazásengedélyek hozzájárulás. A szerepkör tagjai nem hozzá szeretné adni tulajdonosok új alkalmazás-regisztráció és a vállalati alkalmazások létrehozásakor.

* **Megfelelőségi rendszergazda**: Ezzel a szerepkörrel rendelkező felhasználók jogosult felügyeleti belül a Office 365 biztonsági és megfelelőségi központ és az Exchange felügyeleti központban. További információ a "[Office 365 rendszergazdai szerepkörök](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)."

* **Feltételes hozzáférés rendszergazda**: Ezzel a szerepkörrel rendelkező felhasználók képesek az Azure Active Directory feltételes hozzáférési beállításainak kezelése.
  > [!NOTE]
  > Az Azure-ban az Exchange ActiveSync feltételes hozzáférési házirend telepítése, a felhasználó is globális rendszergazdának kell lennie.
  
* **Eszközrendszergazdák**: Ez a szerepkör érhető el a csak egy további helyi rendszergazdaként a hozzárendelés [eszközbeállítások](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Az ehhez a szerepkörhöz tartozó felhasználók a helyi gépek rendszergazdái lesznek az Azure Active Directoryval összekapcsolt összes Windows 10-eszközön. Nem rendelkeznek képes kezelni az eszközöket objektumok Azure Active Directoryban.

* **Directory olvasók**: egy örökölt szerepkör, amely hozzá kell rendelni, amelyek nem támogatják az alkalmazások a [hozzájárulás keretrendszer](active-directory-integrating-applications.md). Azt nem rendelhető hozzá azokat a felhasználókat.

* **Címtár-szinkronizálás fiókok**: ne használja. Ez a szerepkör lesz automatikusan hozzárendelve az Azure AD Connect szolgáltatást, és nem szánt vagy bármely más használata támogatott.

* **Directory írók**: egy örökölt szerepkör, amely hozzá kell rendelni, amelyek nem támogatják az alkalmazások a [hozzájárulás keretrendszer](active-directory-integrating-applications.md). Azt nem rendelhető hozzá azokat a felhasználókat.

* **Dynamics 365 rendszergazdai**: Ezzel a szerepkörrel rendelkező felhasználók rendelkeznek-e a Microsoft Dynamics 365, ha a szolgáltatás jelenlegi globális engedéllyel, valamint a támogatási jegyek kezelése és figyelése a szolgáltatás állapotát. További információ: [Office 365 rendszergazdai szerepkörök](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Exchange szolgáltatás-rendszergazda**: Ezzel a szerepkörrel rendelkező felhasználók engedélye globális belül a Microsoft Exchange online-hoz, ha a szolgáltatás jelen. További információ: [Office 365 rendszergazdai szerepkörök](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Globális rendszergazda / vállalati rendszergazda vagy a bérlői rendszergazda**: Ezzel a szerepkörrel rendelkező felhasználók férhetnek hozzá az Azure Active Directoryval, valamint a szolgáltatások, például az Exchange Online Azure Active Directoryba való összevonásához az összes felügyeleti funkcióhoz SharePoint Online és Skype vállalati Online verzióhoz. A személy, aki az Azure Active Directory-bérlő előfizet egy globális rendszergazdája lesz. Csak a globális rendszergazdák egyéb rendszergazdai szerepköröket rendelhet. A vállalat a egynél több globális rendszergazda is lehet. Globális rendszergazda alaphelyzetbe állíthatja a jelszavát, minden olyan felhasználó, és más rendszergazdák számára.

  > [!NOTE]
  > A Microsoft Graph API-val, a Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja "Vállalati rendszergazda". Az "Globális rendszergazda" a [Azure-portálon](https://portal.azure.com).
  >
  >

* **Vendég meghívó**: a szerepet betöltő felhasználók kezelhetik az Azure Active Directory B2B Vendég felhasználó meghívókat, amikor a felhasználó "Tagok kérhetnek" beállítás értéke nem. További információ a következő B2B együttműködés [kapcsolatos Azure AD B2B együttműködés](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Nem tartalmazza azokat az engedélyeket.

* **Információk védelme rendszergazda**: Ezzel a szerepkörrel rendelkező felhasználók jogosultsággal rendelkező csak az Azure Information Protection-szolgáltatáshoz. Nem rendelkeznek Identity Protection-központtól, a Privileged Identity Management, a figyelő Office 365 szolgáltatás állapotát, vagy Office 365 biztonsági és megfelelőségi központ felhasználói jogosultságokkal. Azok címkéket az Azure Information Protection-házirend konfigurálása, védelmi sablonok kezelése és védelme aktiválja.

* **Intune szolgáltatás-rendszergazda**: Ezzel a szerepkörrel rendelkező felhasználók engedélye globális belül a Microsoft Intune Online, ha a szolgáltatás jelen. Ez a szerepkör emellett képes kezelni a felhasználók és eszközök számára ahhoz, hogy társítja a házirendet, valamint a csoportok létrehozásához és kezeléséhez tartalmazza.

* **Postaláda-rendszergazda**: ezt a szerepkört csak használja az Exchange Online e-mailes támogatást részeként RIM Blackberry eszközöket. Ha a szervezete nem használja az Exchange Online e-mailek RIM Blackberry eszközön, ne használja ezt a szerepkört.

* **Üzenet Center olvasó**: a szerepet betöltő felhasználók figyelheti az értesítések és tanácsadó állapotfigyelő frissítések [Office 365 üzenetközpont](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) konfigurált szolgáltatások, például az Exchange, az Intune-ban és a Microsoft a szervezet számára Csoportok. Üzenetközpont olvasók heti e-mail emésztett közleményeket, a frissítések kap, és oszthatnak meg az üzenet center bejegyzéseket az Office 365-ben. Az Azure ad-ben ehhez a szerepkörhöz rendelt felhasználók csak lesz csak olvasási hozzáféréssel a Azure AD szolgáltatásokba például felhasználókat és csoportokat. 

* **1 támogatási réteg partneri**: ne használja. Ez a szerepkör elavult, és eltávolítja az Azure AD a jövőben. Ez a szerepkör van néhány továbbértékesítése Microsoft-partnerek számára készült, és nem általános használatra szánt.

* **Támogatási réteg 2 partnereinek**: ne használja. Ez a szerepkör elavult, és eltávolítja az Azure AD a jövőben. Ez a szerepkör van néhány továbbértékesítése Microsoft-partnerek számára készült, és nem általános használatra szánt.

* **Jelszókezelő / segélyszolgálat rendszergazda**: Ezzel a szerepkörrel rendelkező felhasználók jelszavak módosítása, szolgáltatáskérések kezelése és figyelése a szolgáltatás állapotát. Segélyszolgálat rendszergazdák jelszavak csak a felhasználók és más segélyszolgálat rendszergazdák módosíthatják. 

  > [!NOTE]
  > A Microsoft Graph API-val, a Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja "Segélyszolgálat rendszergazda". A "Jelszó-rendszergazda" a [Azure-portálon](https://portal.azure.com/).
  >
  >
  
* **A Power BI szolgáltatás-rendszergazda**: Ezzel a szerepkörrel rendelkező felhasználók rendelkeznek-e a Microsoft Power bi-ban, ha a szolgáltatás jelenlegi globális engedéllyel, valamint a támogatási jegyek kezelése és figyelése a szolgáltatás állapotát. További információ: [Office 365 rendszergazdai szerepkörök](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-001&ad=US).

* **Emelt szintű rendszergazdai szerepkör**: Ezzel a szerepkörrel rendelkező felhasználók kezelhetik a szerepkör-hozzárendelések az Azure Active Directoryban, valamint az Azure AD Privileged Identity Management belül. Emellett ez a szerepkör lehetővé teszi a Privileged Identity Management minden szempontját kezelését.

* **Jelentések olvasó**: Ezzel a szerepkörrel rendelkező felhasználók megtekinthetik a használati adatok és az Office 365 felügyeleti központ és az elfogadását környezet jelentések irányítópult csomagot Power bi reporting. Emellett a szerepkör által bejelentkezés jelentések és az Azure AD-tevékenység és a Microsoft Graph által visszaadott adatok reporting API-val. A jelentések olvasó szerepkört rendelt érhetnek el a felhasználók csak a vonatkozó használati és a bevezetési metrikákat. Nem rendelkeznek rendszergazdai engedéllyel a beállításokat, illetve a termék adott rendszergazdai központok például az Exchange hozzáférési konfigurálásához. 

* **Biztonsági rendszergazda**: Ezzel a szerepkörrel rendelkező felhasználók rendelkeznek a csak olvasási engedéllyel a biztonsági olvasó szerepkört, és képes kezelni a biztonsággal kapcsolatos szolgáltatások konfigurációs: Azure Active Directory azonosító adatok védelmét és az Azure- Adatvédelem, Privileged Identity Management és az Office 365 biztonsági és megfelelőségi központ. Office 365 engedélyekkel kapcsolatos további információt [engedélyek az Office 365 biztonsági és megfelelőségi központ](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Biztonsági olvasó**: Ezzel a szerepkörrel rendelkező felhasználók globális csak olvasási hozzáféréssel rendelkezik, többek között az összes adatot az Azure Active Directory, a azonosító adatok védelmét, a Privileged Identity Management, valamint Azure Active Directory bejelentkezési jelentések Olvasás és a naplók. A szerepkör is biztosít az Office 365 biztonsági és megfelelőségi központ olvasási engedéllyel. Office 365 engedélyekkel kapcsolatos további információt [engedélyek az Office 365 biztonsági és megfelelőségi központ](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Szolgáltatás-rendszergazda támogatja**: Ezzel a szerepkörrel rendelkező felhasználók megnyitható támogatási kérelmek Microsoft Azure és az Office 365-szolgáltatások és a szolgáltatás irányítópultját és a üzenet center az Azure portál és az Office 365 felügyeleti portálon található nézetek. További információ: [Office 365 rendszergazdai szerepkörök](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **A SharePoint szolgáltatás-rendszergazda**: Ezzel a szerepkörrel rendelkező felhasználók rendelkeznek-e a Microsoft SharePoint online-hoz, ha a szolgáltatás jelenlegi globális engedéllyel, valamint a támogatási jegyek kezelése és figyelése a szolgáltatás állapotát. További információ: [Office 365 rendszergazdai szerepkörök](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **A Skype vállalati verzió / Lync szolgáltatás-rendszergazda**: Ezzel a szerepkörrel rendelkező felhasználók engedélye globális belül Microsoft Skype vállalati, ha a szolgáltatás jelen, valamint kezelheti a Skype-specifikus felhasználói attribútumok az Azure Active Directoryban. Emellett a szerepkörök a támogatási jegyek kezelése és figyelése a szolgáltatás állapotát. További információ: [Office 365 rendszergazdai szerepkörök](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  > [!NOTE]
  > A Microsoft Graph API-val, a Azure AD Graph API és az Azure AD PowerShell a szerepkör azonosítja "Lync szolgáltatás-rendszergazda". Az "Skype az üzleti szolgáltatás-rendszergazda" a [Azure-portálon](https://portal.azure.com/).
  >
  >

* **Felhasználói fiók rendszergazdai**: Ezzel a szerepkörrel rendelkező felhasználók hozhat létre és kezelheti a felhasználók és csoportok összes tulajdonságát. Emellett ez a szerepkör lehetővé teszi a támogatási jegyek kezelése, és figyeli a szolgáltatás állapotát. Bizonyos korlátozások vonatkoznak. Például a szerepkör nem teszi lehetővé egy globális rendszergazda törlése. Felhasználói fiók rendszergazdák módosíthatják a jelszavak felhasználók, rendszergazdák segélyszolgálati és egyéb felhasználói fiók csak rendszergazdák.

## <a name="administrator-permissions"></a>Rendszergazdai engedélyek

### <a name="application-administrator"></a>Alkalmazás-rendszergazda

| Teheti meg | Nem hajtható végre |
| --- | --- |
| Olvassa el az összes címtár-információ<br>Alkalmazás-regisztrációk létrehozása<br>Alkalmazás regisztrálása tulajdonságainak frissítése<br>Szerezzen be a vállalati alkalmazások<br>Alkalmazás regisztrálása engedélyeinek kezelése<br>Alkalmazás regisztráció törlése<br>Alkalmazásbeállítások kezelése a vállalati egyszeri bejelentkezés<br>Kezelheti a vállalati alkalmazás kialakítási beállításai<br>Vállalati alkalmazások önkiszolgáló beállításainak kezelése<br>Vállalati alkalmazás engedélybeállítások kezelése<br>Alkalmazás-hozzáférés kezelése<br>Üzembe helyezési beállításainak kezelése<br>Vállalati alkalmazások törlése<br>Hozzájárulás nevében Mindenki engedélyt minden olyan kérelem esetében<br>Hozzájárul az Azure AD Graph vagy a Microsoft Graph kivételével az összes alkalmazás engedélykéréseket mindenki nevében<br>Application proxy beállításainak kezelése<br>Az Access services beállításai<br>A figyelő szolgáltatásának állapota<br>Támogatási jegyek kezelése<br>Rejtett olvasási csoporttagság | Létrehozása, szerkesztése és csoportok törlése<br>Felhasználói licencek kezelése<br>Címtár-szinkronizálás<br>Bejelentkezési jelentések megtekintése és a naplók | 

### <a name="application-developer"></a>Alkalmazásfejlesztő

| Teheti meg | Nem hajtható végre |
| --- | --- |
| Olvassa el az összes címtár-információ<br>Alkalmazás-regisztrációk létrehozása<br>Önkiszolgáló nevében hozzájárulás | Bejelentkezési megtekintése és a naplók<br>Rejtett olvasási csoporttagság |

### <a name="billing-administrator"></a>Számlázási adminisztrátor

| Teheti meg | Nem hajtható végre |
| --- | --- |
|<p>Vállalati és felhasználói adatok megtekintése</p><p>Office támogatási jegyek kezelése</p><p>Office-termékek számlázási és beszerzési műveletek végrehajtása</p> |<p>Felhasználói jelszavak átállítása</p><p>Hozzon létre és kezelheti a felhasználói nézetek</p><p>Létrehozása, szerkesztése, törlése a felhasználók és csoportok és felhasználói licencek kezelése</p><p>Tartományok kezelése</p><p>Vállalati adatok kezelése</p><p>Rendszergazdai szerepkörök delegálása</p><p>Címtár-szinkronizálás</p><p>Auditnaplók megtekintése</p> |

### <a name="cloud-application-administrator"></a>Felhőalkalmazás-rendszergazda

| Teheti meg | Nem hajtható végre |
| --- | --- |
| Olvassa el az összes címtár-információ<br>Alkalmazás-regisztrációk létrehozása<br>Alkalmazás regisztrálása tulajdonságainak frissítése<br>Szerezzen be a vállalati alkalmazások<br>Alkalmazás regisztrálása engedélyeinek kezelése<br>Alkalmazás regisztráció törlése<br>Alkalmazásbeállítások kezelése a vállalati egyszeri bejelentkezés<br>Kezelheti a vállalati alkalmazás kialakítási beállításai<br>Vállalati alkalmazások önkiszolgáló beállításainak kezelése<br>Vállalati alkalmazás engedélybeállítások kezelése<br>Alkalmazás-hozzáférés kezelése<br>Üzembe helyezési beállításainak kezelése<br>Vállalati alkalmazások törlése<br>Hozzájárulás nevében Mindenki engedélyt minden olyan kérelem esetében<br>Hozzájárul az Azure AD Graph vagy a Microsoft Graph kivételével az összes alkalmazás engedélykéréseket mindenki nevében<br>Az Access services beállításai<br>A figyelő szolgáltatásának állapota<br>Támogatási jegyek kezelése<br>Rejtett olvasási csoporttagság | Application proxy beállításainak kezelése<br>Létrehozása, szerkesztése és csoportok törlése<br>Felhasználói licencek kezelése<br>Címtár-szinkronizálás<br>Bejelentkezési jelentések megtekintése és a naplók |

### <a name="conditional-access-administrator"></a>Feltételes hozzáférésű rendszergazda

| Teheti meg | Nem hajtható végre |
| --- | --- |
|<p>Vállalati és felhasználói adatok megtekintése</p><p>Feltételes hozzáférési beállításainak kezelése</p> |<p>Felhasználói jelszavak átállítása</p><p>Hozzon létre és kezelheti a felhasználói nézetek</p><p>Létrehozása, szerkesztése, törlése a felhasználók és csoportok és felhasználói licencek kezelése</p><p>Tartományok kezelése</p><p>Vállalati adatok kezelése</p><p>Rendszergazdai szerepkörök delegálása</p><p>Címtár-szinkronizálás</p><p>Auditnaplók megtekintése</p>|

### <a name="global-administrator"></a>Globális rendszergazda
| Teheti meg | Nem hajtható végre |
| --- | --- |
|<p>Vállalati és felhasználói adatok megtekintése</p><p>Office támogatási jegyek kezelése</p><p>Office-termékek számlázási és beszerzési műveletek végrehajtása</p><p>Felhasználói jelszavak átállítása</p><p>Más rendszergazdák jelszavak alaphelyzetbe állítása</p> <p>Hozzon létre és kezelheti a felhasználói nézetek</p><p>Létrehozása, szerkesztése, törlése a felhasználók és csoportok és felhasználói licencek kezelése</p><p>Tartományok kezelése</p><p>Vállalati adatok kezelése</p><p>Rendszergazdai szerepkörök delegálása</p><p>Címtár-szinkronizálás</p><p>Engedélyezheti vagy tilthatja le a többtényezős hitelesítés</p><p>Auditnaplók megtekintése</p> |– |

### <a name="password-administrator--helpdesk-administrator"></a>Jelszókezelő / segélyszolgálat rendszergazda
| Teheti meg | Nem hajtható végre |
| --- | --- |
| <p>Vállalati és felhasználói adatok megtekintése</p><p>Office támogatási jegyek kezelése</p><p>Módosíthatja a felhasználók és más segélyszolgálat-rendszergazdák jelszavát</p>|<p>Office-termékek számlázási és beszerzési műveletek végrehajtása</p><p>Hozzon létre és kezelheti a felhasználói nézetek</p><p>Létrehozása, szerkesztése, törlése a felhasználók és csoportok és felhasználói licencek kezelése</p><p>Tartományok kezelése</p><p>Vállalati adatok kezelése</p><p>Rendszergazdai szerepkörök delegálása</p><p>Címtár-szinkronizálás</p><p>Jelentések megtekintése</p>|

### <a name="information-protection-administrator"></a>Information Protection-rendszergazda
Eleme ennek | Teheti meg
-------- | ---------
Azure Information Protection | <li>A globális és hatókörbe tartozó házirendek címkék és a beállítások konfigurálása<li>Konfigurálhatja és kezelheti a védelmi sablonok<li>Aktiválni vagy inaktiválni védelmi –
 
### <a name="reports-reader"></a>Jelentésolvasó 
Teheti meg | Nem hajtható végre
------ | ----------
Az Azure AD bejelentkezési jelentések és a naplók megtekintése<br>Vállalati és felhasználói adatok megtekintése<br>Office 365 használati irányítópulton | Hozzon létre és kezelheti a felhasználói nézetek<br>Létrehozása, szerkesztése, törlése a felhasználók és csoportok és felhasználói licencek kezelése<br>Rendszergazdai szerepkörök delegálása<br>Vállalati adatok kezelése

### <a name="security-reader"></a>Biztonsági olvasó
| Eleme ennek | Teheti meg |
| --- | --- |
| Identity Protection Center |Olvassa el az összes biztonsági jelentések és beállítási információk biztonsági szolgáltatások<ul><li>Levélszemét<li>Titkosítás<li>Adatveszteség-megelőzési<li>Kártevőirtó<li>Az Advanced threat protection<li>Adathalászat elleni<li>Mailflow szabályok |
| Privileged Identity Management |<p>Csak olvasási hozzáféréssel el az összes információ illesztett rendelkezik Azure AD PIM: házirendeket és az Azure AD szerepkör-hozzárendelések jelentéseinek, biztonsági ellenőrzi, és a jövőben olvasási hozzáféréssel házirend adatokkal és jelentésekkel forgatókönyvek mellett az Azure AD szerepkör-hozzárendelés.<p>**Nem lehet** előfizetés az Azure AD PIM, vagy végezze el a módosításokat. PIM a portálon, vagy a PowerShell segítségével valaki a szerepkör további szerepkörök (például a globális rendszergazda vagy a kiemelt szerepkör rendszergazda), ha a felhasználó őket jelöltségét ellenőrző aktiválhatja. |
| <p>A figyelő az Office 365 szolgáltatás állapota</p><p>Az Office 365 biztonsági és megfelelőségi központ</p> |<ul><li>Olvassa el és kezelheti a riasztásokat<li>Olvassa el a biztonsági házirendek<li>Olvassa el a fenyegetésfelderítési adataival, a Cloud App Discovery és a Keresés és a vizsgálat karantén<li>Olvassa el az összes jelentés |

### <a name="security-administrator"></a>Biztonsági rendszergazda
| Eleme ennek | Teheti meg |
| --- | --- |
| Identity Protection Center |<ul><li>Az olvasó biztonsági szerepkörhöz tartozó jogosultságok.<li>Emellett lehetővé teszi jelszó alaphelyzetbe állítása kivételével minden IPC műveletek végrehajtásához. |
| Privileged Identity Management |<ul><li>Az olvasó biztonsági szerepkörhöz tartozó jogosultságok.<li>**Nem lehet** kezelése az Azure AD szerepkörtagságok vagy beállítások. |
| <p>A figyelő az Office 365 szolgáltatás állapota</p><p>Az Office 365 biztonsági és megfelelőségi központ |<ul><li>Az olvasó biztonsági szerepkörhöz tartozó jogosultságok.<li>Az Advanced Threat Protection szolgáltatás (kártevők & vírus védelmet, rosszindulatú URL-cím config, URL nyomkövetésének, stb.) az összes beállításait is konfigurálhatja. |

### <a name="service-administrator"></a>Szolgáltatás-rendszergazda
| Teheti meg | Nem hajtható végre |
| --- | --- |
| <p>Vállalati és felhasználói adatok megtekintése</p><p>Office támogatási jegyek kezelése</p> |<p>Felhasználói jelszavak átállítása</p><p>Office-termékek számlázási és beszerzési műveletek végrehajtása</p><p>Hozzon létre és kezelheti a felhasználói nézetek</p><p>Létrehozása, szerkesztése, törlése a felhasználók és csoportok és felhasználói licencek kezelése</p><p>Tartományok kezelése</p><p>Vállalati adatok kezelése</p><p>Rendszergazdai szerepkörök delegálása</p><p>Címtár-szinkronizálás</p><p>Auditnaplók megtekintése</p> |

### <a name="user-account-administrator"></a>Felhasználóifiók-adminisztrátor
| Teheti meg | Nem hajtható végre |
| --- | --- |
| <p>Vállalati és felhasználói adatok megtekintése</p><p>Office támogatási jegyek kezelése</p><p>Felhasználók, rendszergazdák segélyszolgálati és egyéb felhasználói fiók rendszergazdák csak jelszavak módosítása</p><p>Hozzon létre és kezelheti a felhasználói nézetek</p><p>Létrehozása, szerkesztése, és a felhasználók és csoportok törlésére és korlátozásokkal a felhasználói licencek kezelése. Őt nem törölhet globális rendszergazdát, és hozhat létre más rendszergazdákat.</p> |<p>Office-termékek számlázási és beszerzési műveletek végrehajtása</p><p>Tartományok kezelése</p><p>Vállalati adatok kezelése</p><p>Rendszergazdai szerepkörök delegálása</p><p>Címtár-szinkronizálás</p><p>Engedélyezheti vagy tilthatja le a többtényezős hitelesítés</p><p>Auditnaplók megtekintése</p> |

### <a name="to-add-a-user-as-a-global-administrator"></a>A globális rendszergazdai felhasználó hozzáadása

1. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) a bérlő címtár globális rendszergazdai fiókkal.

   ![Az azure AD felügyeleti központ megnyitása](./media/active-directory-assign-admin-roles-azure-portal/active-directory-admin-center.png)

2. Válassza ki **felhasználók** > **minden felhasználó**.

3. Nyissa meg a globális rendszergazdának kijelölni kívánt felhasználót lapját.

4. A parancssávon válassza **Directory szerepkör**.

5. Válassza ki **szerepkör hozzáadása**.

6. A directory szerepkör lapon válassza ki a **globális rendszergazda** szerepkör, és kattintson **kiválasztása** mentéséhez.

## <a name="deprecated-roles"></a>Elavult szerepkörök

A következő szerepkörök nem használható. Ezek elavultak, ezért törlődni fognak az Azure AD a jövőben.

* Speciális licencek adminisztrátora
* Ellenőrzött e-mail című felhasználó-létrehozó
* Csatlakozás bármilyen eszközről
* Eszközkezelők
* Eszközök felhasználói
* Munkahelyi eszköz-csatlakoztatás

## <a name="next-steps"></a>További lépések

* Azure-előfizetések rendszergazdáinak módosításáról kapcsolatos további információkért lásd: [hozzáadása vagy módosítása az Azure-előfizetés rendszergazdái](../billing-add-change-azure-subscription-administrator.md)
* Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../role-based-access-control/rbac-and-directory-admin-roles.md)
* Hogyan Azure Active Directory vonatkozik-e az Azure-előfizetéshez további információkért lásd: [kapcsolódnak hogyan Azure-előfizetések az Azure Active Directoryval](fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Felhasználók kezelése](active-directory-create-users.md)
* [Jelszavak kezelése](active-directory-manage-passwords.md)
* [Csoportok kezelése](fundamentals/active-directory-manage-groups.md)
