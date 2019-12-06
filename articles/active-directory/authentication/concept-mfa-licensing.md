---
title: Azure MFA-verziók és-fogyasztási csomagok – Azure Active Directory
description: Információk a multi-Factor Authentication-ügyfélről, valamint az elérhető különböző módszerekről és verziókról.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61a2afad33aea7fa2a21135dca0995ad862a2258
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848663"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Azure-Multi-Factor Authentication beszerzése

A fiókok védelme érdekében a kétlépéses ellenőrzésnek standard szintűnek kell lennie a szervezeten belül. Ez a funkció különösen fontos olyan fiókoknál, amelyek privilegizált hozzáféréssel rendelkeznek az erőforrásokhoz. Emiatt a Microsoft az Office 365 és a Azure Active Directory (Azure AD) rendszergazdái számára alapszintű kétlépéses ellenőrzési funkciókat kínál, többletköltség nélkül. Ha szeretné frissíteni a rendszergazdák szolgáltatásait, vagy a kétlépéses ellenőrzést a többi felhasználóra is kiterjesztheti, az Azure Multi-Factor Authentication többféleképpen is megvásárolhatja.

> [!IMPORTANT]
> A cikk célja, hogy segítséget nyújtson az Azure-Multi-Factor Authentication megvásárlásának különböző módjairól. A díjszabással és a számlázással kapcsolatos részletes információkért tekintse meg a [multi-Factor Authentication díjszabási oldalát](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication elérhető verziói

A következő táblázat a többtényezős hitelesítés verziói közötti különbségeket ismerteti:

| Verzió | Leírás |
| --- | --- |
| Ingyenes lehetőség | Az Azure AD ingyenes előnyeit kihasználó ügyfelek [biztonsági alapértékekkel](../conditional-access/concept-conditional-access-security-defaults.md) engedélyezhetik a többtényezős hitelesítést a környezetében. |
| Multi-Factor Authentication for Office 365 | Ezt a verziót az Office 365 vagy Microsoft 365 portálon kezelheti. A rendszergazdák [kétlépéses ellenőrzéssel biztonságossá tehetik az Office 365-erőforrásokat](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Ez a verzió egy Office 365-előfizetés részét képezi. |
| Multi-Factor Authentication az Azure AD-rendszergazdák számára | Az Azure ad-bérlők Azure AD globális rendszergazdai szerepkörét hozzárendelő felhasználók további díjak nélkül engedélyezhetik a kétlépéses ellenőrzést. |
| Azure Multi-Factor Authentication | Gyakran a "teljes" verziónak nevezik, az Azure Multi-Factor Authentication a funkciók legszélesebb körét kínálja. További konfigurációs lehetőségeket biztosít a [Azure Portalon](https://portal.azure.com), a speciális jelentéskészítésen, valamint számos helyszíni és felhőalapú alkalmazás támogatásán keresztül. Az Azure Multi-Factor Authentication [prémium szintű Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features) és [Microsoft 365 vállalati verzió](https://www.microsoft.com/microsoft-365/business)szolgáltatás. |

> [!NOTE]
> Az új ügyfelek már nem vásárolhatják meg az Azure Multi-Factor Authentication önálló ajánlatként, amely 2018. szeptember 1-től érvényes. A többtényezős hitelesítés továbbra is elérhető lesz prémium szintű Azure AD vagy Microsoft 365 Vállalati verzió licencek szolgáltatásában.

## <a name="feature-comparison-of-versions"></a>A verziók összehasonlítása

A következő táblázat felsorolja az Azure Multi-Factor Authentication különböző verzióiban elérhető funkciókat.

> [!NOTE]
> Ez az összehasonlító táblázat a Multi-Factor Authentication egyes verziói részét képező szolgáltatásokat ismerteti. Ha rendelkezik a teljes Azure Multi-Factor Authentication szolgáltatással, előfordulhat, hogy egyes funkciók nem lesznek elérhetők attól függően, hogy az MFA-t a [felhőben vagy a helyszíni MFA-ban használja-](concept-mfa-whichversion.md)e.
>

| Szolgáltatás | Multi-Factor Authentication for Office 365 | Multi-Factor Authentication az Azure AD-rendszergazdák számára | Azure Multi-Factor Authentication | Alapértelmezett biztonsági szabályok | 
| --- |:---:|:---:|:---:|:---:|
| Azure AD-rendszergazdai fiókok biztosítása MFA-val |● |● (Csak Azure AD globális rendszergazdai fiókok) |● |● |
| Mobile App második tényezőként |● |● |● |● |
| Telefonhívás második tényezőként |● |● |● |   |
| SMS második tényezőként |● |● |● |   |
| Az MFA-t nem támogató ügyfelekhez tartozó alkalmazások jelszavai |● |● |● |   |
| Az ellenőrzési módszerek rendszergazdai ellenőrzése |● |● |● |   |
| A nem rendszergazdai fiókok hitelesítése MFA-val |● | |● |● |
| PIN-mód | | |● |   |
| Csalási riasztás | | |● |   |
| MFA-jelentések | | |● |   |
| Egyszeri mellőzés | | |● |   |
| Egyéni üdvözlések a telefonhívásokhoz | | |● |   |
| Telefonos hívások egyéni hívóazonosító | | |● |   |
| Megbízható IP-címek | | |● |   |
| MFA megjegyzése megbízható eszközökön |● |● |● |   |
| MFA helyszíni alkalmazásokhoz | | |● |   |

> [!IMPORTANT]
> Az 2019-as naptól kezdve a telefonhívási lehetőségek nem lesznek elérhetők az MFA és a SSPR felhasználók számára ingyenes/próbaverziós Azure AD-bérlők esetében. Ez a változás nem érinti az SMS-üzeneteket. A telefonos hívás továbbra is elérhető lesz a fizetős Azure AD-bérlők felhasználói számára. Ez a változás csak az ingyenes/próbaverziós Azure AD-bérlőket befolyásolja.

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Azure-Multi-Factor Authentication bekapcsolása az Azure AD-rendszergazdák számára

A globális rendszergazdai szerepkörrel rendelkező felhasználók az Azure AD-bérlők számára lehetővé teszik az Azure AD-beli globális rendszergazdai fiókok kétlépéses ellenőrzését további díjak nélkül. Microsoft-fiók használata esetén regisztrálhat a többtényezős hitelesítésre a Microsoft-fiók támogatási cikkben található útmutatás alapján, a [kétlépéses ellenőrzésről](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification). Ha nem Microsoft-fiókot használ, kapcsolja be a többtényezős hitelesítést a globális rendszergazdák számára a következő cikkben található útmutatás használatával: a [felhasználó vagy csoport kétlépéses ellenőrzésének megkövetelése](howto-mfa-userstates.md).

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>Azure-Multi-Factor Authentication vásárlása

Vásárolhat olyan licenceket, amelyek tartalmazzák az Azure Multi-Factor Authentication, például a prémium szintű Azure Active Directory, vagy egy olyan licencet, amely prémium szintű Azure AD vagy feltételes hozzáférést tartalmaz, és hozzárendeli azokat a felhasználókhoz Azure Active Directory.

### <a name="consumption-based-licensing"></a>Fogyasztáson alapuló licencelés

A fogyasztáson alapuló licencelés az új ügyfelek számára már nem érhető el, 2018. szeptember 1-től érvényes.

Az új hitelesítési szolgáltatók már nem hozhatók létre. szeptember 1., 2018. A meglévő hitelesítési szolgáltatók továbbra is használhatók és frissíthetők. A többtényezős hitelesítés továbbra is elérhető lesz prémium szintű Azure AD licencekben.

Azure Multi-Factor Authentication-szolgáltató használata esetén két, az Azure-előfizetésen keresztül számlázott használati modell érhető el:

1. **Engedélyezett felhasználó** – olyan vállalatok számára, amelyek a hitelesítéshez rendszeresen szükséges, rögzített számú alkalmazottra szeretnének engedélyezni a kétlépéses ellenőrzést. A felhasználónkénti számlázás az MFA-ban az Azure AD-bérlőben és az Azure MFA-kiszolgálón engedélyezett felhasználók számán alapul. Ha a felhasználók számára engedélyezve van az MFA az Azure AD-ben és az Azure MFA-kiszolgálón, és a tartomány-szinkronizálás (Azure AD Connect) engedélyezve van, akkor a felhasználók nagyobb készletét számoljuk el. Ha a tartomány szinkronizálása nincs engedélyezve, akkor az Azure AD-ben és az Azure MFA-kiszolgálón az MFA számára engedélyezett összes felhasználó összegét számoljuk fel. A számlázás napi rendszerességgel van elszámolva és jelenteni a kereskedelmi rendszernek.

   > [!NOTE]
   > 1\. számlázási példa: jelenleg 5 000 felhasználó engedélyezett az MFA-hoz. Az MFA-rendszer 31-re osztja ezt a számot, és az adott napra vonatkozóan 161,29 jelentést küld. Holnap további 15 felhasználót engedélyez, így az MFA-rendszerjelentések az adott napon 161,77 felhasználókat. A számlázási ciklus végére az Azure-előfizetéssel számlázott felhasználók teljes száma körülbelül 5 000-ra növelhető.
   >
   > 2\. számlázási példa: licencekkel és felhasználókkal rendelkező felhasználók vegyesen rendelkeznek, így a különbség a felhasználónkénti Azure MFA-szolgáltatón keresztül végezhető el. A bérlőn 4 500 Enterprise Mobility + Security licenc található, de a 5 000-es felhasználók engedélyezve vannak az MFA-hoz. Az Azure-előfizetése 500-felhasználók számára lett kiszámlázva, és naponta, a 16,13 felhasználóként számolt be.
   >

1. **Hitelesítéssel** – olyan vállalatok számára, amelyeknek engedélyezni kívánják a kétlépéses ellenőrzést olyan felhasználók nagy csoportja számára, akik ritkán igényelnek hitelesítést. A számlázás a kétlépéses ellenőrzési kérelmek száma alapján történik, függetlenül attól, hogy az ellenőrzések sikeresek vagy megtagadva. Ez a számlázás a 10 hitelesítés csomagjában megjelenik az Azure használati utasításban, és naponta jelentést küld.

   > [!NOTE]
   > 3\. számlázási példa: az Azure MFA szolgáltatás jelenleg 3 105 2-es ellenőrzési kérést kapott. Az Azure-előfizetése 310,5-es hitelesítési csomagra van kiszámlázva.
   >

Fontos megjegyezni, hogy a licenccel rendelkezhet, de továbbra is kiszámlázható a használaton alapuló konfiguráció. Ha létrehoz egy hitelesítési Azure MFA-szolgáltatót, akkor a kétlépéses ellenőrzési kérelemért, valamint a licenccel rendelkező felhasználók által végrehajtott kérelmekért kell fizetnie. Ha a felhasználónkénti Azure MFA-szolgáltatót olyan tartományon állítja be, amely nincs az Azure AD-bérlőhöz társítva, akkor is engedélyezett felhasználóként kell fizetnie, ha a felhasználók licenccel rendelkeznek az Azure AD-ben.

## <a name="next-steps"></a>Következő lépések

- A díjszabással kapcsolatos további információkért lásd: az [Azure MFA díjszabása](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
