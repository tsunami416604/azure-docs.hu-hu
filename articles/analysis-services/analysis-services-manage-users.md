---
title: Az Azure Analysis Services hitelesítés és a felhasználói engedélyek |} Microsoft Docs
description: További tudnivalók az Azure Analysis Services hitelesítés és a felhasználó engedélyeit.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ede05667d794267443babacaa8b016206d86ab2c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="authentication-and-user-permissions"></a>Hitelesítés és a felhasználói engedélyek
Az Azure Analysis Services az Azure Active Directory (Azure AD) identitás- és felhasználói hitelesítés. Bármely felhasználó létrehozása, kezelése és csatlakozás az Azure Analysis Services szolgáltatáshoz server rendelkeznie kell érvényes felhasználói azonosítót egy [Azure AD-bérlő](../active-directory/active-directory-administer.md) ugyanahhoz az előfizetéshez.

Támogatja az Azure Analysis Services [Azure AD B2B együttműködés](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md). A B2B a szervezeten kívüli felhasználók más Azure AD-címtár felhasználói Vendég meghívható. A vendégek lehet egy másik Azure AD-bérlő címtár vagy bármilyen érvényes e-mail címet. Egyszer meghívott, és a felhasználó elfogadja a meghívót küldött e-mailben, az Azure-ból a felhasználói identitás hozzáadódik a bérlő címtárát. Ezeket az identitásokat biztonsági csoportokhoz, vagy egy kiszolgálói rendszergazda vagy az adatbázis-szerepkör tagjai lehet hozzáadni.

![Az Azure Analysis Services hitelesítési architektúra](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Hitelesítés
Minden ügyfél-alkalmazások és eszközök használja legalább egy, az Analysis Services [klienskódtárak](analysis-services-data-providers.md) (AMO-MSOLAP, ADOMD) egy kiszolgálóhoz való csatlakozáshoz. 

A három ügyfélcsoport kódtárakat mind az Azure AD interaktív folyamat és a nem interaktív hitelesítés támogatásához. A két nem interaktív módszerek, az Active Directory-jelszó és az Active Directory integrált hitelesítési módszerek használatával AMOMD és MSOLAP-alkalmazásokban használható. A két módszerről soha nem eredményez előugró párbeszédpanelen.

Ügyfél alkalmazások, például az Excel és a Power BI Desktopban, és például az SSMS és az SSDT telepítése a legújabb verzióra frissíti a könyvtárak legújabb verzióját. A Power BI Desktop, az SSMS és az SSDT havi frissítése. Excel [és az Office 365 frissítése](https://support.office.com/en-us/article/When-do-I-get-the-newest-features-in-Office-2016-for-Office-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Az Office 365 frissítéseit ritkábban, és egyes szervezetek a késleltetett csatorna használatára, azaz a frissítések a három hónapos mentése Elhalasztott.

Attól függően, hogy az ügyfélalkalmazás vagy a használt eszköz típusát és hitelesítés hogyan bejelentkezik lehetnek. Minden alkalmazás különböző szolgáltatások támogathatja a felhőalapú szolgáltatások, például az Azure Analysis Services való kapcsolódáshoz.

A Power BI Desktop SSDT és SSMS támogatja az Active Directory univerzális-hitelesítéssel, egy interaktív módszerhez is támogatja az Azure multi-factor Authentication (MFA). Az Azure MFA segít hozzáférés biztonságossá tételét adatokhoz és alkalmazásokhoz biztosít egy egyszerű bejelentkezési folyamat során. Erős hitelesítés több ellenőrzési lehetőségek (telefonhívás, szöveges üzenetet, intelligens kártyák PIN-kód és az értesítést a mobilalkalmazásban) biztosítja. Az Azure ad-val interaktív MFA érvényesítéshez előugró párbeszédpanelen eredményezhet. **Univerzális hitelesítés kiválasztása javasolt**.

Ha az Azure használatával, egy Windows-fiókot, és univerzális hitelesítési nincs kiválasztva, vagy a rendelkezésre álló (Excel), bejelentkezés [Active Directory összevonási szolgáltatások (AD FS)](../active-directory/connect/active-directory-aadconnect-azure-adfs.md) szükséges. Az összevonási, az Azure AD és az Office 365-felhasználókat a helyszíni hitelesítő adatok használatával hitelesített, és az Azure-erőforrások eléréséhez.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)
Az Azure Analysis Services-kiszolgálók támogatják az érkező kapcsolatokat [SSMS V17.1](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) és újabb Windows-hitelesítést, Active Directory jelszavas hitelesítést és az Active Directory univerzális hitelesítés használatával. Általában ajánlott Active Directory univerzális hitelesítést használni, mert:

*  Interaktív és a nem interaktív hitelesítési módszert támogat.

*  Az Azure AS-bérlőhöz való meghívott Azure B2B vendégfelhasználók támogatja. A kiszolgálóhoz való csatlakozáskor a vendégfelhasználók kell választania az Active Directory univerzális hitelesítése, a kiszolgálóhoz való csatlakozáskor.

*  Támogatja a többtényezős hitelesítés (MFA). Az Azure MFA segít hozzáférés biztonságossá tételét adatokhoz és alkalmazásokhoz az ellenőrzési lehetőségek közül: telefonhívás, szöveges üzenetet, intelligens kártyák PIN-kód és az értesítést a mobilalkalmazásban. Az Azure ad-val interaktív MFA érvényesítéshez előugró párbeszédpanelen eredményezhet.

### <a name="sql-server-data-tools-ssdt"></a>SQL Server Data Tools (SSDT)
Az SSDT hitelesítéssel Active Directory univerzális MFA-támogatással rendelkező Azure Analysis Services csatlakozik. Az első telepített jelentkezzen be Azure rendszer kéri a felhasználóktól. Felhasználók Azure-bA egy kiszolgálón rendszergazdai engedélyekkel rendelkező fiók a kiszolgálón telepíti azokat be kell jelentkeznie. Bejelentkezés az Azure-bA az első alkalommal, amikor egy token van hozzárendelve. Az SSDT gyorsítótárazza a token memórián belüli a jövőbeli újrakapcsolódások száma.

### <a name="power-bi-desktop"></a>A Power BI Desktop
A Power BI Desktop-hitelesítéssel Active Directory univerzális MFA-támogatással rendelkező Azure Analysis Services csatlakozik. Jelentkezzen be az Azure-bA az első csatlakozás a rendszer kéri a felhasználóktól. Felhasználók kell jelentkeznie az Azure-bA egy olyan fiókkal, amely megtalálható a rendszergazda vagy az adatbázis-szerepkör.

### <a name="excel"></a>Excel
Excel felhasználók egy Windows-fiókot, egy szervezet azonosítója (e-mail címet) vagy egy külső e-mail cím használatával is kapcsolódhatnak egy kiszolgálóhoz. Az Azure AD vendégfelhasználóként egy külső e-mail identitások léteznie kell.

## <a name="user-permissions"></a>Felhasználói engedélyek

**Kiszolgáló-Rendszergazdák** jellemző az Azure Analysis Services kiszolgálópéldányhoz. Például az Azure-portálon, a szolgáltatáshoz az SSMS és az SSDT végrehajtani a feladatokat, például adatbázisok hozzáadása és felhasználói szerepkörök kezelése csatlakoznak. Alapértelmezés szerint a felhasználót, hogy a kiszolgáló létrehozása automatikusan kerül egy Analysis Services-kiszolgáló rendszergazdaként. Más rendszergazdák számára az Azure portál vagy a szolgáltatáshoz az SSMS használatával lehet hozzáadni. Kiszolgáló-rendszergazdák fiókkal kell rendelkeznie az Azure AD-bérlő ugyanahhoz az előfizetéshez. További tudnivalókért lásd: [kiszolgáló-rendszergazdák kezelése](analysis-services-server-admins.md). 

**Adatbázis-felhasználók** modell olyan adatbázisai esetén ügyfélalkalmazások, például Excel vagy a Power BI használatával csatlakoznak. Adatbázis-szerepkörök felhasználókat kell felvenni. Adatbázis-szerepkörök definiálása a rendszergazda, a folyamat vagy olvasási engedéllyel az adatbázis. Fontos, hogy megértse, kiszolgáló-rendszergazdák eltérő adatbázis-felhasználók rendszergazdai jogosultságokkal rendelkező szerepkör. Azonban alapértelmezés szerint rendszergazdái egyaránt adatbázis-rendszergazdák. További tudnivalókért lásd: [adatbázis-szerepkörök és a felhasználók kezelése](analysis-services-database-users.md).

**Az Azure erőforrás-tulajdonosok**. Erőforrás-tulajdonosok egy Azure-előfizetés az erőforrások kezelése. Erőforrás-tulajdonosok használatával adhat hozzá az Azure AD felhasználói identitások tulajdonos vagy közreműködő szerepkört az előfizetésen belül **hozzáférés-vezérlés** Azure-portálon, vagy az Azure Resource Manager-sablonok. 

![Hozzáférés-vezérlés az Azure portálon](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Ezen a szinten szerepkörök a felhasználók vagy a fiókokat, amelyek feladatokat kell elvégezni, hogy elvégezhető legyen a portálon vagy az Azure Resource Manager-sablonok segítségével vonatkozik. További tudnivalókért lásd: [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md). 


## <a name="database-roles"></a>Adatbázis-szerepkörök

 Egy táblázatos modell meghatározott szerepköröket használják, adatbázis-szerepkörök. Ez azt jelenti, hogy a szerepkörök az Azure Active Directory-felhasználók álló tagok tartalmaznak, és biztonsági csoportokat, amelyek konkrét engedélyeket, amelyek meghatározzák a művelet a tagokkal rendelkezik egy modelladatbázissal vehet igénybe. Egy adatbázis-szerepkör külön objektumként az adatbázisban jön létre, és csak azokra az adatbázist, amelyben ez a szerepkör jön létre.   
  
 Alapértelmezés szerint egy táblázatos modell projekt létrehozásakor a jelentésmodell-projekt nem rendelkezik egyetlen szerepkörhöz sem. Szerepkörök SSDT-ben a szerepkörkezelő párbeszédpanel használatával határozható meg. Amikor a szerepkörök modell projekt tervezése során meghatározott, rendszer alkalmazza azokat csak a modell munkaterület-adatbázis. Ha a modell lett telepítve, ugyanazt a szerepkörök a telepített modell is vonatkozik. A modell központi telepítése után server és adatbázis-rendszergazdák kezelhetik szerepkörök és tagok SSMS használatával. További tudnivalókért lásd: [adatbázis-szerepkörök és a felhasználók kezelése](analysis-services-database-users.md).
  


## <a name="next-steps"></a>További lépések

[Az Azure Active Directoryval erőforrásokhoz való hozzáférés kezelése](../active-directory/active-directory-manage-groups.md)   
[Adatbázis-szerepkörök és a felhasználók kezelése](analysis-services-database-users.md)  
[A kiszolgálók rendszergazdáinak kezelése](analysis-services-server-admins.md)  
[Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md)  