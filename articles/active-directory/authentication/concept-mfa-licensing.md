---
title: Az Azure MFA-verziók és a használatalapú csomagok |} A Microsoft Docs
description: A multi-factor Authentication-ügyfél és a különböző módszerek és elérhető verziók vonatkozó adatokat.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 08/25/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: ec1a3e7e3e8969e161854a9f386d4b2e767ee48a
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970416"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Az Azure multi-factor Authentication beszerzése

Esetén, a fiókok védelmét, a kétlépéses ellenőrzés kell standard a szervezetben. Ez a szolgáltatás különösen fontos a rendszergazdai fiókok, amelyek a privilegizált erőforrásokhoz való hozzáférést. Ebből kifolyólag a Microsoft alapvető kétlépéses ellenőrzési funkciókat kínál az Office 365-höz, és az Azure-rendszergazdák nem kapcsolódik további költség. Ha azt szeretné, a rendszergazdák számára a szolgáltatások frissítésével, vagy a többi felhasználó kétlépéses ellenőrzés kiterjesztése, vásárolhat Azure multi-factor Authentication szolgáltatás. 

Ez a cikk ismerteti a rendszergazdák és az Azure MFA teljes verziója érhető el a verziók közötti különbség. Ha készen áll a teljes Azure MFA ajánlatok üzembe helyezéséhez, a későbbi szakasz ismerteti, megvalósítási lehetőségeit, és hogyan számítja ki a Microsoft használatalapú.


>[!IMPORTANT]
>Ez a cikk szinkronban kell lennie egy útmutató segítségével megismerheti az Azure multi-factor Authentication szolgáltatás vásárlása különböző módjait. Díjszabás és számlázás kapcsolatos részleteket, mindig tekintse át a [a multi-factor Authentication szolgáltatás díjszabási oldalán](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Az Azure multi-factor Authentication elérhető verzióinak

A következő táblázat ismerteti a multi-factor authentication szolgáltatás három verziója közötti különbségek:

| Verzió | Leírás |
| --- | --- |
| Multi-Factor Authentication az Office 365-höz |Ez a verzió kizárólag az Office 365-alkalmazásokkal működik, és az Office 365 portáljáról felügyelhető. A rendszergazdák is [védheti az Office 365-erőforrásokat az kétlépéses ellenőrzés](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Ez a verzió az Office 365-előfizetés részét képezi. |
| Többtényezős hitelesítés az Azure AD-rendszergazdák | Az Azure AD-bérlő globális rendszergazdai szerepkörrel rendelkező engedélyezheti a kétlépéses ellenőrzést, az Azure AD globális rendszergazdai fiókok további költségek nélkül.|
| Azure Multi-Factor Authentication | A "teljes" verziója más néven, az Azure multi-factor Authentication a képességek richest készletét kínálja. Elérhető további konfigurációs lehetőségeket biztosít a [az Azure portal](https://portal.azure.com), speciális jelentéskészítés és a helyszíni széles körű támogatása és a felhőbeli alkalmazásokhoz. Azure multi-factor Authentication szolgáltatás része a [Azure Active Directory Premium csomagok](https://www.microsoft.com/cloud-platform/azure-active-directory-features) és [Enterprise Mobility + Security csomag](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing), és is üzembe helyezhetők a felhőben vagy a helyszínen. |

## <a name="feature-comparison-of-versions"></a>Verzió szolgáltatásainak összehasonlítása
Az alábbi táblázat az Azure multi-factor Authentication szolgáltatás különféle verzióihoz elérhető funkciók listáját tartalmazza.

> [!NOTE]
> Az összehasonlító táblázatot a többtényezős hitelesítés minden verziója részét képező funkciókat ismerteti. Ha a teljes Azure multi-factor Authentication szolgáltatás, bizonyos szolgáltatások nem lesznek elérhetők, ha használja [MFA a felhőben vagy MFA helyszíni](concept-mfa-whichversion.md).


| Szolgáltatás | Multi-Factor Authentication az Office 365-höz | Többtényezős hitelesítés az Azure AD-rendszergazdák | Azure Multi-Factor Authentication |
| --- |:---:|:---:|:---:|
| MFA-val az Azure AD rendszergazdai fiókok védelme |● |● (csak az Azure AD globális rendszergazdai fiókok esetében) |● |
| Mobilalkalmazás második tényezőként |● |● |● |
| Telefonhívás második tényezőként |● |● |● |
| SMS második tényezőként |● |● |● |
| Alkalmazásjelszavak az MFA-t nem támogató ügyfelek |● |● |● |
| Hitelesítési módszerek rendszergazdai szabályozása |● |● |● |
| MFA-val nem rendszergazdai fiókok védelme |● (csak az Office 365-alkalmazásokhoz) | |● |
| PIN-mód | | |● |
| Csalási riasztás | | |● |
| MFA-jelentések | | |● |
| Egyszeri mellőzés | | |● |
| Egyéni üdvözlések a telefonhívásokhoz | | |● |
| Egyéni Hívóazonosító a telefonhívásokhoz | | |● |
| Megbízható IP-címek | | |● |
| MFA megjegyzése megbízható eszközökön |● |● |● |
| MFA SDK | | |● (elavult) | 
| MFA helyszíni alkalmazások | | |● |

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Az Azure AD-rendszergazdák az Azure multi-factor Authentication bekapcsolása
Az Azure AD-bérlő globális rendszergazdai szerepkörrel rendelkező engedélyezheti a kétlépéses ellenőrzést, az Azure AD globális rendszergazdai fiókok további költségek nélkül. Ha Microsoft-Account használ, regisztrálhat a multi-factor authentication [Itt](https://support.microsoft.com/en-us/help/12408/microsoft-account-about-two-step-verification). Ha nem használ egy Microsoft-Account, kapcsolja be a többtényezős hitelesítést a globális rendszergazdák [Itt](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states).

## <a name="how-to-get-azure-multi-factor-authentication"></a>Az Azure multi-factor Authentication beszerzése
Ha szeretné, hogy az Azure multi-factor Authentication által kínált összes funkcióját, több lehetőség van:

### <a name="option-1---mfa-licenses"></a>1 – MFA-licencek. lehetőséget

Az Azure multi-factor Authentication licencet vásárol, és hozzárendelheti azokat a felhasználókat az Azure Active Directoryban. 

Ha ezt a beállítást használja, csak hozzon létre Azure multi-factor Authentication-szolgáltatót, ha meg kell adnia a kétlépéses ellenőrzést, amelyek nem rendelkeznek licenccel az egyes felhasználók számára. Ellenkező esetben esetén előfordulhat, hogy után kell díjat kétszer.

### <a name="option-2---bundled-licenses-that-include-mfa"></a>MFA-beállítás 2 – kötegelve licencek

Vásároljon licencet, amely tartalmazza az Azure multi-factor Authentication, mint például az Azure Active Directory Premium vagy Enterprise Mobility + Security, és hozzárendelheti azokat a felhasználók az Azure Active Directoryban. 

Ha ezt a beállítást használja, létre kell hoznia az Azure multi-factor Authentication-szolgáltatót, csak ha is meg kell adnia a kétlépéses ellenőrzést, amelyek nem rendelkeznek licenccel az egyes felhasználók számára. Ellenkező esetben esetén előfordulhat, hogy után kell díjat kétszer. 

### <a name="option-3---mfa-consumption-based-model"></a>MFA fogyasztásalapú díjcsomagban 3 – beállítás

Hozzon létre Azure multi-factor Authentication-szolgáltatót egy Azure-előfizetésen belül. Az Azure MFA-szolgáltatók a következők: Azure-erőforrások, amelyek a nagyvállalati szerződéssel, Azure-ra vonatkozó pénzügyi kötelezettségvállalás vagy hitelkártyára, mint minden más Azure-erőforrás terhelve. Ezek a szolgáltatók csak a teljes körű Azure-előfizetések, nem korlátozott Azure-előfizetések rendelkezik egy 0 USD költségkeret hozható létre. Korlátozott előfizetések licenceket, például az 1. és 2 beállítások aktiválása során jönnek létre. 

Azure multi-factor Authentication-szolgáltatót használ, ha van elérhető az Azure-előfizetés díjszabása vonatkozik, amely két használati modellekről:  

1. **Engedélyezett felhasználónként** – vállalatok számára szeretné engedélyezni a kétlépéses ellenőrzés egy rögzített számú alkalmazottak, akik rendszeresen hitelesítésre van szükség. Felhasználóalapú számlázás az Azure AD-bérlőhöz, és az Azure MFA-kiszolgáló az MFA engedélyezett felhasználók számát alapul. Felhasználók engedélyezve vannak-e a multi-factor Authentication két Azure AD-ben és az Azure MFA-kiszolgáló és a tartomány-szinkronizáló (Azure AD Connect) engedélyezve van, akkor azt száma, hogy a felhasználók nagyobb készlete. Ha tartományi szinkronizálás nincs engedélyezve, akkor azt a count összege az összes olyan felhasználó, az Azure AD MFA engedélyezett, és Azure MFA-kiszolgáló. A számlázás napokra és a kereskedelmi rendszer jelentett naponta. 

  > [!NOTE]
  > A számlázás 1. példa: 5000 felhasználók jelenleg engedélyezve van az MFA-hoz. Az MFA-rendszer elosztja ezt a számot 31, és a jelentések 161.29 felhasználók az adott napra. Holnap engedélyezése további 15 felhasználót, így az MFA jelenti 161.77 felhasználók az adott napra. Az elszámolási időszakban a végén az Azure-előfizetés terhére felhasználók teljes száma ad legfeljebb 5000 körül. 
  >
  > A számlázás 2. példa:, csak a licenccel rendelkező felhasználók és a felhasználók anélkül, így a felhasználónkénti Azure MFA-szolgáltató a különbség a létre kell. Nincsenek 4500 Enterprise Mobility + Security-licencek a bérlő, de 5000 felhasználó engedélyezve van az MFA-hoz. Az Azure-előfizetés 500 felhasználónak kell fizetnie, napokra és más 16.13 felhasználói naponta jelentett. 

2. **Hitelesítésenként** – a nagyvállalatoknak, amely engedélyezi a felhasználók, akik ritkábban hitelesítésre van szükség nagyszámú kétlépéses ellenőrzést. A kétlépéses ellenőrzési kérelmeket, függetlenül attól, hogy ezen ellenőrzések sikeres, vagy a rendszer megtagadta a száma alapján számoljuk. A számlázás az Azure-használati utasítás a csomagokban 10 hitelesítésből álló megjelenik, és naponta jelentett. 

  > [!NOTE]
  > A számlázás 3. példa: még ma, az az Azure MFA szolgáltatás kapott 3,105 kétlépéses ellenőrzési kérelmeket. Az Azure-előfizetés elszámolási 310.5 hitelesítési csomagokat. 

Fontos megjegyezni, hogy az Azure MFA-licenccel is rendelkezik, de továbbra is díjat kell fizetnie a használat alapú konfigurálást. Ha beállította a hitelesítés az Azure MFA-szolgáltató, számlázzuk ki minden kétlépéses ellenőrzési kérelmet, még akkor is ezeket a műveleteket a licenccel rendelkező felhasználók. Ha egy tartományhoz, amely az Azure AD-bérlő nem kapcsolódik a beállítva a felhasználónkénti Azure MFA-szolgáltató, használatáért engedélyezett felhasználónként akkor is, ha a felhasználók rendelkeznek licenccel az Azure ad-ben. 

## <a name="next-steps"></a>További lépések

- További díjszabási információkért tekintse meg a [Azure MFA szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

- Döntse el, hogy üzembe helyezése az Azure MFA [a felhőben vagy helyszíni](concept-mfa-whichversion.md)
