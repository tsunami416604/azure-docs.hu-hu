---
title: Az Azure Analysis Services hitelesítése és felhasználói engedélyei| Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogy az Azure Analysis Services hogyan használja az Azure Active Directoryt (Azure AD) az identitáskezeléshez és a felhasználói hitelesítéshez.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4a054c3c042e18f1679acd75e5ba5ad74f66edff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572768"
---
# <a name="authentication-and-user-permissions"></a>Hitelesítés és felhasználói engedélyek

Az Azure Analysis Services az Azure Active Directoryt (Azure AD) használja az identitáskezeléshez és a felhasználói hitelesítéshez. Az Azure Analysis Services-kiszolgálót létrehozó, kezelő vagy ahhoz csatlakozó felhasználóknak érvényes felhasználói identitással kell rendelkezniük egy [Azure AD-bérlőben](../active-directory/fundamentals/active-directory-administer.md) ugyanabban az előfizetésben.

Az Azure Analysis Services támogatja az [Azure AD B2B együttműködést.](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) A B2B segítségével a szervezeten kívüli felhasználók meghívást kaphatnak vendégfelhasználóként egy Azure AD-címtárban. A vendégek lehetnek egy másik Azure AD-bérlői könyvtárból vagy bármely érvényes e-mail címről. Miután meghívta, és a felhasználó elfogadja az Azure-ból e-mailben küldött meghívást, a felhasználói identitás hozzáadódik a bérlői könyvtárhoz. Ezek az identitások hozzáadhatóak biztonsági csoportokhoz, illetve kiszolgálói rendszergazdai vagy adatbázis-szerepkör tagjaiként.

![Az Azure Analysis Services hitelesítési architektúrája](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Hitelesítés

Minden ügyfélalkalmazás és eszköz egy vagy több Analysis Services [ügyfélkódtárat](analysis-services-data-providers.md) (AMO, MSOLAP, ADOMD) használ a kiszolgálóhoz való csatlakozáshoz. 

Mindhárom ügyfélkódtárak támogatja az Azure AD interaktív folyamat, és a nem interaktív hitelesítési módszerek. A két nem interaktív módszer, az Active Directory jelszó és az Active Directory integrált hitelesítési módszer használható az AMOMD és MSOLAP használó alkalmazásokban. Ez a két módszer soha nem eredményez előugró párbeszédpaneleket.

Az olyan ügyfélalkalmazások, mint az Excel és a Power BI Desktop, valamint az olyan eszközök, mint az SSMS és az Analysis Services projektek bővítménye a Visual Studio számára, a legújabb kiadásra való frissítéskor telepítik a könyvtárak legújabb verzióit. A Power BI Desktop, az SSMS és az Analysis Services projektbővítménye havonta frissül. Az Excel [az Office 365-tel frissül.](https://support.office.com/article/When-do-I-get-the-newest-features-in-Office-2016-for-Office-365-da36192c-58b9-4bc9-8d51-bb6eed468516) Az Office 365 frissítései ritkábbak, és egyes szervezetek a halasztott csatornát használják, ami azt jelenti, hogy a frissítések legfeljebb három hónapra elhalasztva lesznek.

A használt ügyfélalkalmazástól vagy eszköztől függően a hitelesítés típusa és a bejelentkezés módjá eltérő lehet. Minden alkalmazás különböző funkciókat támogathat a felhőszolgáltatásokhoz, például az Azure Analysis Serviceshez való csatlakozáshoz.

A Power BI Desktop, a Visual Studio és az SSMS támogatja az Active Directory univerzális hitelesítést, amely egy interaktív módszer, amely támogatja az Azure többtényezős hitelesítést (MFA). Az Azure MFA segít megőrizni az adatokhoz és alkalmazásokhoz való hozzáférést, miközben egyszerű bejelentkezési folyamatot biztosít. Erős hitelesítést biztosít számos ellenőrzési lehetőséggel (telefonhívás, szöveges üzenet, intelligens kártyák pin-kóddal vagy mobilalkalmazás-értesítéssel). Interaktív MFA az Azure AD eredményezhet egy előugró párbeszédpanel érvényesítése. **Az univerzális hitelesítés ajánlott.**

Ha Windows-fiókkal jelentkezik be az Azure-ba, és az univerzális hitelesítés nincs kijelölve vagy nem érhető el (Excel), [az Active Directory összevonási szolgáltatások (AD FS)](../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) szükséges. A Federation, az Azure AD és az Office 365-felhasználók hitelesítése a helyszíni hitelesítő adatokkal, és hozzáférhet az Azure-erőforrásokhoz.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Az Azure Analysis Services-kiszolgálók támogatják az [SSMS V17.1-es](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) és újabb verzióiból érkező kapcsolatokat a Windows-hitelesítés, az Active Directory jelszó-hitelesítés és az Active Directory univerzális hitelesítés használatával. Általában az Active Directory univerzális hitelesítés használata ajánlott, mert:

*  Támogatja az interaktív és nem interaktív hitelesítési módszereket.

*  Támogatja az Azure B2B vendégfelhasználók meghívást az Azure AS-bérlő. Amikor kiszolgálóhoz csatlakozik, a vendégfelhasználóknak a kiszolgálóhoz való csatlakozáskor az Active Directory univerzális hitelesítést kell választaniuk.

*  Támogatja a többtényezős hitelesítést (MFA). Az Azure MFA számos ellenőrzési lehetőséggel segít az adatokhoz és alkalmazásokhoz való hozzáférés biztosításában: telefonhívás, szöveges üzenet, intelligens kártyák pin-kóddal vagy mobilalkalmazás-értesítéssel. Interaktív MFA az Azure AD eredményezhet egy előugró párbeszédpanel érvényesítése.

### <a name="visual-studio"></a>Visual Studio

A Visual Studio az Active Directory univerzális hitelesítés és az MFA-támogatás használatával csatlakozik az Azure Analysis Services szolgáltatáshoz. A rendszer kéri a felhasználókat, hogy jelentkezzenek be az Azure-ba az első üzembe helyezéskor. A felhasználóknak kiszolgálói rendszergazdai engedélyekkel rendelkező fiókkal kell bejelentkezniük az Azure-ba azon a kiszolgálón, amelyen telepítik őket. Amikor első alkalommal jelentkezik be az Azure-ba, egy jogkivonat van hozzárendelve. A jogkivonat a memóriában gyorsítótárazva van a jövőbeli újracsatlakozásokhoz.

### <a name="power-bi-desktop"></a>Power BI Desktop

A Power BI Desktop az Active Directory univerzális hitelesítés és mfa-támogatás használatával csatlakozik az Azure Analysis Services szolgáltatáshoz. A rendszer kéri a felhasználókat, hogy jelentkezzenek be az Azure-ba az első kapcsolaton. A felhasználóknak olyan fiókkal kell bejelentkezniük az Azure-ba, amely egy kiszolgálórendszergazdai vagy adatbázis-szerepkörben található.

### <a name="excel"></a>Excel

Az Excel-felhasználók Windows-fiókkal, szervezeti azonosítóval (e-mail-címmel) vagy külső e-mail-címmel csatlakozhatnak a kiszolgálóhoz. A külső e-mail identitások nak vendégfelhasználóként kell létezniük az Azure AD-ben.

## <a name="user-permissions"></a>Felhasználói engedélyek

**A kiszolgálórendszergazdák** egy Azure Analysis Services-kiszolgálópéldányra jellemzőek. Olyan eszközökkel csatlakoznak, mint az Azure Portal, az SSMS és a Visual Studio, hogy olyan feladatokat hajtsanak végre, mint az adatbázisok hozzáadása és a felhasználói szerepkörök kezelése. Alapértelmezés szerint a kiszolgálót létrehozó felhasználó automatikusan hozzáadódik az Analysis Services kiszolgáló rendszergazdájaként. Más rendszergazdák is hozzáadhatók az Azure Portal vagy az SSMS használatával. A kiszolgáló rendszergazdáinak ugyanabban az előfizetésben rendelkezniük kell egy fiókkal az Azure AD-bérlőben. További információ: [Kiszolgálógazdák kezelése](analysis-services-server-admins.md). 

**Az adatbázis-felhasználók** az ügyfélalkalmazások, például az Excel vagy a Power BI segítségével kapcsolódnak modelladatbázisokhoz. A felhasználókat hozzá kell adni az adatbázis-szerepkörökhöz. Az adatbázis-szerepkörök rendszergazdai, folyamat- és olvasási engedélyeket határoznak meg az adatbázishoz. Fontos megérteni, hogy a rendszergazdai engedélyekkel rendelkező szerepkörben lévő adatbázis-felhasználók nem ismerik meg a kiszolgáló rendszergazdáit. Alapértelmezés szerint azonban a kiszolgáló rendszergazdái is adatbázis-rendszergazdák. További információ: [Adatbázis-szerepkörök és felhasználók kezelése.](analysis-services-database-users.md)

**Az Azure-erőforrások tulajdonosai**. Az erőforrás-tulajdonosok kezelik az Azure-előfizetés erőforrásait. Az erőforrás-tulajdonosok hozzáadhatják az Azure AD felhasználói identitásokat a tulajdonosi vagy közreműködői szerepkörökhöz egy előfizetésen belül az Azure Portal **hozzáférés-vezérlésével** vagy az Azure Resource Manager-sablonokkal. 

![Hozzáférés-vezérlés az Azure Portalon](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Szerepkörök ezen a szinten vonatkoznak a felhasználók vagy fiókok, amelyek feladata, hogy a portálon vagy az Azure Resource Manager-sablonok használatával elvégezhető feladatokat kell végrehajtani. További információ: [Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md). 

## <a name="database-roles"></a>Adatbázis-szerepkörök

 A táblázatos modellhez definiált szerepkörök adatbázis-szerepkörök. Ez azt, hogy a szerepkörök az Azure AD-felhasználókból és biztonsági csoportokból álló tagokat tartalmaznak, amelyek meghatározott engedélyekkel rendelkeznek, amelyek meghatározzák, hogy a tagok milyen műveletet végrehajthatnak egy modelladatbázison. A rendszer az adatbázis-szerepkört külön objektumként hozza létre az adatbázisban, és csak arra az adatbázisra vonatkozik, amelyben az adott szerepkör létre lett hozva.   
  
 Alapértelmezés szerint új táblázatos modellprojekt létrehozásakor a modellprojektnek nincsenek szerepkörei. A szerepkörök a Visual Studio Szerepkör-kezelő párbeszédpanelén adhatók meg. Amikor a szerepkörök a modellprojekt tervezése során vannak definiálva, azok csak a modell munkaterületi adatbázisára lesznek alkalmazva. A modell üzembe helyezésekor ugyanazokat a szerepköröket alkalmazza az üzembe helyezett modell. A modell telepítése után a kiszolgáló- és adatbázis-rendszergazdák kezelhetik a szerepköröket és a tagokat az SSMS használatával. További információ: [Adatbázis-szerepkörök és felhasználók kezelése.](analysis-services-database-users.md)
  
## <a name="next-steps"></a>További lépések

[Erőforrásokhoz való hozzáférés kezelése az Azure Active Directory-csoportokkal](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Adatbázis-szerepkörök és felhasználók kezelése](analysis-services-database-users.md)  
[A kiszolgálók rendszergazdáinak kezelése](analysis-services-server-admins.md)  
[Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md)  