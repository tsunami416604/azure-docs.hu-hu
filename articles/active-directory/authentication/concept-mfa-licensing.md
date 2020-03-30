---
title: Az Azure többtényezős hitelesítési verziói és felhasználási tervei
description: Ismerje meg az Azure többtényezős hitelesítési ügyfél és a különböző módszerek és verziók elérhető.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e74a7ab0c003aaf9d90211484b39f8322cd9c329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648002"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Az Azure többtényezős hitelesítésének szolgáltatásai és licencei

A szervezet felhasználói fiókjainak védelme érdekében többtényezős hitelesítést kell használni. Ez a funkció különösen fontos az erőforrásokhoz kiemelt hozzáféréssel rendelkező fiókok esetében. Az Office 365 és az Azure Active Directory (Azure AD) rendszergazdái többletköltség nélkül elérhetők az alapvető többtényezős hitelesítési funkciók. Ha frissíteni szeretné a rendszergazdák funkcióit, vagy ki szeretné terjeszteni a többtényezős hitelesítést a többi felhasználóra, többféleképpen vásárolhatazure-alapú többtényezős hitelesítést.

> [!IMPORTANT]
> Ez a cikk részletezi az Azure többtényezős hitelesítés licencelésének és használatával kapcsolatos különböző módoknak a részleteit. A díjszabással és a számlázással kapcsolatos részletekért tekintse meg az [Azure többtényezős hitelesítés díjszabási lapját.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Az Azure többtényezős hitelesítés elérhető verziói

Az Azure többtényezős hitelesítésa a szervezet igényeitől függően többféleképpen is használható és licencelhető. Előfordulhat, hogy az Azure AD, az Office 365, az EMS vagy a Microsoft 365 licenc től függően már jogosult az Azure Multi-Factor Authentication használatára. Az alábbi táblázat bemutatja az Azure többtényezős hitelesítésbe vételének különböző módjait, valamint az egyes funkciók és használati esetek némelyikét.

| Ha Ön a | Képességek és használati esetek |
| --- | --- |
| EMS vagy Microsoft 365 E3 és E5 | Az EMS E3 vagy a Microsoft 365 E3 (amely magában foglalja az EMS-t és az Office 365-öt is) tartalmazza az Azure AD Premium P1-et. Az EMS E5 vagy a Microsoft 365 E5 tartalmazza az Azure AD Premium P2-t. A következő szakaszokban felsorolt feltételes hozzáférési szolgáltatások kal többtényezős hitelesítést biztosíthat a felhasználóknak. |
| Prémium szintű Azure AD P1 | [Az Azure AD feltételes hozzáférés](../conditional-access/overview.md) használatával kérheti a felhasználók at többtényezős hitelesítés bizonyos forgatókönyvek vagy események, hogy megfeleljen az üzleti követelményeknek. |
| Prémium szintű Azure AD P2 | A legerősebb biztonsági pozíciót és jobb felhasználói élményt nyújtja. [Kockázatalapú feltételes hozzáférést ad](../conditional-access/howto-conditional-access-policy-risk.md) hozzá az Azure AD Premium P1 funkciókhoz, amelyek alkalmazkodnak a felhasználói mintákhoz, és minimálisra csökkentik a többtényezős hitelesítési utasításokat. |
| Office 365 Vállalati prémium verzió, E3 vagy E5 | Az Azure többtényezős hitelesítés e vagy engedélyezett, vagy le van tiltva az összes felhasználó, az összes bejelentkezési események. A felhasználók egy részhalmaza esetén, vagy csak bizonyos esetekben nem engedélyezhető többtényezős hitelesítés. A kezelés az Office 365 portálon keresztül történik. A továbbfejlesztett felhasználói élmény érdekében frissítsen az Azure AD Premium P1 vagy P2 szolgáltatásra, és használja a feltételes hozzáférést. További információt az [Office 365-erőforrások biztonságossá tétele többtényezős hitelesítéssel](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)című témakörben talál. |
| Ingyenes Azure AD | A [biztonsági alapértelmezések](../fundamentals/concept-fundamentals-security-defaults.md) segítségével minden felhasználó számára engedélyezheti a többtényezős hitelesítést, minden alkalommal, amikor hitelesítési kérelem érkezik. Nem rendelkezik az engedélyezett felhasználók vagy forgatókönyvek részletes vezérlésével, de biztosítja ezt a további biztonsági lépést.<br /> Még akkor is, ha a biztonsági alapértelmezések nem használják, hogy a többtényezős hitelesítés mindenki számára, az *Azure AD globális rendszergazdai* szerepkörrel hozzárendelt felhasználók konfigurálhatók a többtényezős hitelesítés. Az ingyenes szint ezen szolgáltatása gondoskodik arról, hogy a kritikus rendszergazdai fiókok többtényezős hitelesítéssel védettek legyenek. |

## <a name="feature-comparison-of-versions"></a>A verziók szolgáltatásösszehasonlítása

Az alábbi táblázat az Azure többtényezős hitelesítés különböző verzióiban elérhető funkciók listáját tartalmazza. Tervezze meg a felhasználói hitelesítés biztonságossá tétele szükséges, majd határozza meg, hogy melyik megközelítés felel meg ezeknek a követelményeknek. Például bár az Azure AD Free biztosítja az Azure többtényezős hitelesítést biztosító biztonsági alapértelmezett beállításokat, csak a mobil hitelesítő alkalmazás használható a hitelesítési parancssorhoz, telefonhívás vagy SMS nem. Ez a megközelítés lehet korlátozás, ha nem tudja biztosítani, hogy a mobil hitelesítési alkalmazás telepítve van a felhasználó személyes eszközére.

| Szolgáltatás | Azure AD Free - Biztonsági alapértelmezések | Azure AD Ingyenes - Azure AD globális rendszergazdák | Office 365 Vállalati prémium verzió, E3 vagy E5 | Azure AD Premium P1 vagy P2 |
| --- |:---:|:---:|:---:|:---:|
| Az Azure AD-bérlői rendszergazdai fiókok védelme az MFA-val | ● | ● (csak*Az Azure AD globális rendszergazdai fiókjai)* | ● | ● |
| Mobilalkalmazás második tényezőként | ● | ● | ● | ● |
| Telefonhívás, mint a második tényező | | ● | ● | ● |
| SMS mint második tényező | | ● | ● | ● |
| Rendszergazdai ellenőrzés az ellenőrzési módszerek felett | | ● | ● | ● |
| Csalási riasztás | | | | ● |
| MFA-jelentések | | | | ● |
| Egyéni üdvözlések a telefonhívásokhoz | | | | ● |
| Egyéni hívóazonosító telefonhívásokhoz | | | | ● |
| Megbízható IP-címek | | | | ● |
| MFA megjegyzése megbízható eszközökön | | ● | ● | ● |
| Többszintű behatással a helyszíni alkalmazásokhoz | | | | ● |

> [!IMPORTANT]
> 2019 márciusáta óta a telefonhívási beállítások már nem érhetők el az Azure többtényezős hitelesítésés az Azure Önkiszolgáló jelszó-visszaállítás felhasználói számára az Azure AD ingyenes/ próbaverziós bérlőkben. Az SMS-üzeneteket ez a változás nem érinti. A telefonhívások továbbra is elérhetők a felhasználók számára az Azure AD Premium P1 vagy P2 bérlők vagy az Office 365 Vállalati Prémium verzió, az E3 vagy az E5 szolgáltatásban.

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Az Azure többtényezős hitelesítésének megvásárlása és engedélyezése

Az Azure többtényezős hitelesítésének használatához regisztráljon egy jogosult Azure AD-rétegre, vagy vásároljon. Az Azure AD négy kiadásban érkezik: ingyenes, Office 365-ös alkalmazások kiadása (Office 365 Vállalati prémium verziós E3 vagy E5-ügyfelek esetén), Prémium P1 és Prémium P2.

Az ingyenes kiadás egy Azure-előfizetéshez tartozik. Az [alábbi szakaszban](#azure-ad-free-tier) megtudhatja, hogyan használhatja a biztonsági alapértékeket, illetve hogyan védheti meg a fiókokat az *Azure AD globális rendszergazdai* szerepkörrel.

Az Azure AD Premium kiadások a Microsoft képviselőjén, az [Open mennyiségi licencprogramon](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)és a [Felhőszolgáltató programon](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409)keresztül érhetők el. Az Azure- és Az Office 365-előfizetők online is vásárolhatják meg az Azure Active Directory Premium P1 és P2 szolgáltatást. [Jelentkezzen be a](https://portal.office.com/Commerce/Catalog.aspx) vásárláshoz.

> [!IMPORTANT]
> A fogyasztásalapú licencelés már nem érhető el az új ügyfelek számára 2018. A fogyasztásalapú modellt használó meglévő ügyfelek továbbra is használhatják az engedélyezett felhasználónkénti vagy hitelesítési számlázásonkénti használatot.

Miután megvásárolta a szükséges Azure AD-szintet, [tervezze meg és telepítse az Azure többtényezős hitelesítést.](howto-mfa-getstarted.md)

### <a name="azure-ad-free-tier"></a>Azure AD ingyenes csomag

Az Azure AD Free bérlő minden felhasználója használhatja az Azure többtényezős hitelesítést a biztonsági alapértelmezések használatával. Ezek a biztonsági alapértelmezések lehetővé teszik az Azure többtényezős hitelesítést minden felhasználó számára, minden bejelentkezéskor. A mobil hitelesítési alkalmazás az egyetlen módszer, amely használható az Azure többtényezős hitelesítéshez az Azure AD Free biztonsági alapértelmezett használataesetén.

* [További információ az Azure AD biztonsági beállításairól](../fundamentals/concept-fundamentals-security-defaults.md)
* [Biztonsági alapértelmezések engedélyezése a felhasználók számára az Azure AD Free szolgáltatásban](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Ha nem szeretné engedélyezni az Azure többtényezős hitelesítést az összes felhasználó és minden bejelentkezési esemény számára, ehelyett dönthet úgy, hogy csak az *Azure AD globális rendszergazdai* szerepkörrel védi a felhasználói fiókokat. Ez a megközelítés további hitelesítési utasításokat biztosít a kritikus rendszergazdai fiókokhoz. Az Azure többtényezős hitelesítését a használt fiók típusától függően az alábbi módokon engedélyezheti:

* Ha Microsoft-fiókot használ, [regisztráljon a többtényezős hitelesítéshez.](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)
* Ha nem használ [Microsoft-fiókot, kapcsolja be a többtényezős hitelesítést egy felhasználó vagy csoport számára az Azure AD-ben.](howto-mfa-userstates.md)

## <a name="next-steps"></a>További lépések

A költségekről további információt az [Azure többtényezős hitelesítés díjszabása](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)című témakörben talál.
