---
title: Hitelesítés és felhasználói engedélyek az Azure Analysis Servicesben |} A Microsoft Docs
description: Ismerje meg a hitelesítés és felhasználói engedélyek az Azure Analysis Servicesben.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6280544a42d0d5012b01446ec8c3bc386ef861dd
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49428419"
---
# <a name="authentication-and-user-permissions"></a>Hitelesítés és felhasználói engedélyek
Az Azure Analysis Services az Azure Active Directory (Azure AD) identity management és a felhasználói hitelesítés. Bármely felhasználó létrehozása, kezelése vagy csatlakozhat egy Azure Analysis Services kiszolgálói rendelkeznie kell egy érvényes felhasználói identitás egy [Azure AD-bérlő](../active-directory/fundamentals/active-directory-administer.md) ugyanabban az előfizetésben.

Az Azure Analysis Services támogatja a [Azure AD B2B együttműködés](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md). B2B, a szervezeten kívüli felhasználók is hívható meg az Azure AD-címtár vendégfelhasználókat. Vendégek lehet az Azure AD-bérlő egy másik címtárban vagy bármilyen érvényes e-mail címet. Egyszer meghívót, és a felhasználó elfogadja a meghívó által küldött e-mailt az Azure-ban, a felhasználói identitás bekerül a bérlő címtárát. Ezeket az identitásokat a biztonsági csoportok és a egy kiszolgálói rendszergazda vagy az adatbázis-szerepkör tagjai is hozzáadhatók.

![Az Azure Analysis Services hitelesítési architektúra](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Hitelesítés
Az összes ügyfél alkalmazások és eszközök használatához legalább egy, az Analysis Services [klienskódtárak](analysis-services-data-providers.md) (AMO, MSOLAP, ADOMD) szeretne csatlakozni egy kiszolgálóhoz. 

Mindhárom ügyfélkódtárat mind az Azure AD interaktív flow és a nem interaktív hitelesítési módszereket támogatja. A két nem interaktív módok, az Active Directory-jelszó és a Active Directory integrált hitelesítési módszerek AMOMD és MSOLAP-alkalmazásokban használható. Két módszer közül a felugró párbeszédpanel soha nem eredményez.

Ügyfélalkalmazások, például az Excel és a Power BI Desktopban, és eszközökkel, mint például az ssms-ben és az SSDT telepítése a kódtárakat, legfrissebb verzióját a frissített a legfrissebb verzióit. Havi Power BI Desktopban, az ssms-ben és az SSDT frissülnek. Excel [frissítése és az Office 365](https://support.office.com/article/When-do-I-get-the-newest-features-in-Office-2016-for-Office-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Office 365-frissítések kevésbé gyakori, és egyes szervezetek használja a késleltetett csatornáról, jelentése frissítések mentése a három hónappal a funkciófrissítéseket.

Attól függően, az ügyfélalkalmazás vagy az eszköz, amellyel hitelesítés és bejelentkezés módja típusa eltérő lehet. Mindegyik alkalmazáshoz különböző szolgáltatások támogatása előfordulhat, hogy csatlakozik a felhőszolgáltatásokhoz, mint például az Azure Analysis Services.

A Power BI Desktop, az SSDT és SSMS támogatja az Active Directory univerzális-hitelesítést, az interaktív metódus, amely az Azure multi-factor Authentication (MFA) is támogatja. Az Azure MFA segít biztonságosabb a hozzáférés az adatokhoz és alkalmazásokhoz biztosít egy egyszerű bejelentkezési folyamat során. Erős hitelesítés (telefonhívás, szöveges üzenet, intelligens kártyák PIN-kód vagy mobilalkalmazásbeli értesítés) többféle hitelesítési lehetőséget kínál. Interaktív, az Azure AD MFA egy felugró párbeszédpanel érvényesítéshez eredményezhet. **Univerzális hitelesítés ajánlott**.

Ha nincs kiválasztva egy Windows-fiókot és univerzális hitelesítéssel vagy érhető el (Excel), az Azure-bA bejelentkezik [Active Directory összevonási szolgáltatások (AD FS)](../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) megadása kötelező. Az összevonási, az Azure AD és Office 365-felhasználók a helyszíni hitelesítő adatok használatával hitelesített, és az Azure-erőforrások eléréséhez.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)
Azure Analysis Services szolgáltatásai támogatják a érkező kapcsolatokat [SSMS V17.1](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) és újabb Windows-hitelesítés, Active Directory jelszavas hitelesítést és az Active Directory univerzális hitelesítéssel. Általában javasoljuk, használja az Active Directory univerzális hitelesítéssel, mivel:

*  Interaktív és a nem interaktív hitelesítési módszereket támogatja.

*  Az Azure AS-bérlőbe meghívott vendégfelhasználók Azure B2B támogatja. A kiszolgálóhoz való csatlakozáshoz, vendég felhasználók Active Directory univerzális hitelesítéssel jelöljön ki a kiszolgálóhoz való kapcsolódás során.

*  Támogatja a többtényezős hitelesítés (MFA). Az Azure MFA segít biztonságosabb a hozzáférés az adatokhoz és alkalmazásokhoz az ellenőrzési lehetőségek: telefonhívás, szöveges üzenet, intelligens kártyák PIN-kód vagy mobilalkalmazásbeli értesítés. Interaktív, az Azure AD MFA egy felugró párbeszédpanel érvényesítéshez eredményezhet.

### <a name="sql-server-data-tools-ssdt"></a>SQL Server Data Tools (SSDT)
Az SSDT Active Directory univerzális hitelesítéssel az MFA-támogatással az Azure Analysis Services csatlakozik. Jelentkezzen be az első üzembe helyezés az Azure-bA a rendszer kéri. Felhasználók az Azure-bA egy olyan fiókkal rendelkező kiszolgáló-rendszergazdai engedélyek a kiszolgálón telepíti, be kell jelentkeznie. Bejelentkezés az Azure-bA az első alkalommal, amikor egy token van hozzárendelve. Az SSDT gyorsítótárazza a jogkivonat memórián belüli a jövőbeli újrakapcsolódások száma.

### <a name="power-bi-desktop"></a>A Power BI Desktopban
A Power BI Desktop csatlakozik az Azure Analysis Services használata az Active Directory univerzális hitelesítéssel az MFA-támogatással. Jelentkezzen be az első kapcsolat az Azure-bA a rendszer kéri. Felhasználók jelentkezzen be az Azure-bA egy olyan fiókkal, amely egy kiszolgáló-rendszergazda vagy az adatbázis-szerepkör része.

### <a name="excel"></a>Excel
Az Excel-felhasználók egy Windows-fiókot, egy szervezet azonosítója (e-mail-cím), vagy egy külső e-mail-cím használatával is csatlakozni egy kiszolgálóhoz. Az Azure AD egy külső e-mail-identitások léteznie kell.

## <a name="user-permissions"></a>Felhasználói engedélyek

**Kiszolgáló-Rendszergazdák** jellemző egy Azure Analysis Services-kiszolgálópéldányra. Az Azure portal, az SSMS és az SSDT feladatait, mint az adatbázisok hozzáadása és felhasználói szerepkörök kezelése hasonló eszközök csatlakoznak. Alapértelmezés szerint a felhasználót, hogy létrehozza a kiszolgáló automatikusan hozzáadódik az Analysis Services-kiszolgálói rendszergazdaként. Más rendszergazdák is felvehetők az Azure portal vagy az SSMS használatával. Kiszolgáló-rendszergazdák ugyanabban az előfizetésben az Azure AD-bérlő a fiókkal kell rendelkeznie. További tudnivalókért lásd: [a kiszolgálók rendszergazdáinak kezelése](analysis-services-server-admins.md). 

**Adatbázis-felhasználók** csatlakozás modell olyan adatbázisai az ügyfélalkalmazások, mint az Excel vagy a Power BI használatával. Adatbázis-szerepkörökhöz felhasználókat kell felvenni. Adatbázis-szerepkörök definiálása a rendszergazda, a folyamat vagy az adatbázis olvasási engedélyeket. Fontos tudni, adatbázis-felhasználók a rendszergazdai szerepköre nem azonos a kiszolgáló-rendszergazdák. Azonban alapértelmezés szerint kiszolgáló-rendszergazdák egyúttal adatbázis-rendszergazdák. További tudnivalókért lásd: [adatbázis-szerepkörök és a felhasználók kezelése](analysis-services-database-users.md).

**Az Azure erőforrás-tulajdonosok**. Erőforrás-tulajdonosok az Azure-előfizetés erőforrásainak kezelése. Erőforrás-tulajdonosok használatával adhat hozzá az Azure AD-felhasználói identitásokat tulajdonosi vagy közreműködői szerepkörrel egy előfizetésen belül **hozzáférés-vezérlés** az Azure Portalon vagy Azure Resource Manager-sablonokkal. 

![Hozzáférés-vezérlés az Azure Portalon](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Ezen a szinten szerepkörök vonatkozik a felhasználók vagy fiókok, amelyek feladatokat kell elvégeznie, elvégezhető a portálon vagy az Azure Resource Manager-sablonok használatával. További tudnivalókért lásd: [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md). 


## <a name="database-roles"></a>Adatbázis-szerepkörök

 Egy táblázatos modell definiált szerepkörök választhatók: adatbázis-szerepkörökhöz. Amelyek a szerepkör tartalmaz tagokat álló Azure AD-felhasználók és biztonsági csoportokból, amelyekre konkrét engedélyeket, amelyek meghatározzák a művelet a tagon egy modelladatbázissal hajthatja végre. A rendszer az adatbázis-szerepkört külön objektumként hozza létre az adatbázisban, és csak arra az adatbázisra vonatkozik, amelyben az adott szerepkör létre lett hozva.   
  
 Alapértelmezés szerint egy új táblázatosmodell-projekt létrehozásakor a jelentésmodell-projekt nem rendelkezik minden olyan szerepkörhöz. Szerepkörök a szerepkörkezelő párbeszédpanel az SSDT használatával lehet definiálni. Ha szerepkörök modell projekt tervezése során vannak definiálva, csak a modell munkaterület-adatbázis az alkalmazásuk. A modell üzembe lett helyezve, amikor a rendszer alkalmazza ugyanezeket a szerepköröket az üzembe helyezett modell. A modell üzembe helyezését követően server és adatbázis-rendszergazdák kezelheti szerepkörök és a tagok az SSMS használatával. További tudnivalókért lásd: [adatbázis-szerepkörök és a felhasználók kezelése](analysis-services-database-users.md).
  


## <a name="next-steps"></a>További lépések

[Erőforrásokhoz való hozzáférés kezelése Azure Active Directory-csoportokkal](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Adatbázis-szerepkörök és a felhasználók kezelése](analysis-services-database-users.md)  
[A kiszolgálók rendszergazdáinak kezelése](analysis-services-server-admins.md)  
[Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md)  