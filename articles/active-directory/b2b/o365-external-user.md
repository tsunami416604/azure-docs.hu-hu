---
title: Külső megosztás az Office 365 és az Azure Active Directory B2B együttműködés |} A Microsoft Docs
description: Ismerteti az erőforrások megosztása külső partnerekkel, az Office 365 és az Azure Active Directory B2B együttműködés segítségével.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/24/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 7e0a65e4a5807cb9a6b39feecfd2d5b2643ea4a9
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331018"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Külső megosztás az Office 365 és Azure Active Directory B2B-együttműködés

A külső megosztás (onedrive vállalati verzió, SharePoint online-hoz, egységesített csoportok stb.) az Office 365 és Azure Active Directory (Azure AD) B2B-együttműködés technikailag ugyanarra a dologra. Minden külső megosztás (kivéve a onedrive vállalati verzió/SharePoint online-hoz), beleértve a vendégeket az Office 365-csoportok, már használja az Azure AD B2B együttműködés meghívó API-k megosztása.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Az Azure AD B2B Miben különbözik a külső megosztást a SharePoint online-ban?

Onedrive vállalati verzió/SharePoint online-ban rendelkezik egy külön meghívó manager. Támogatja a külső megosztása a OneDrive/SharePoint online-ban előtt az Azure AD fejlesztett támogat. Az idő múlásával esedékes számos funkcióval rendelkezik külső megosztás OneDrive/SharePoint online-hoz, és több millió felhasználók, akik a termék használatához a beépített minta megosztása. Vannak azonban bizonyos finom eltérések külső megosztás OneDrive/SharePoint Online működését, és az Azure AD B2B együttműködés működése között. További OneDrive/SharePoint Online külső megosztása a [külső megosztása – áttekintés](https://docs.microsoft.com/sharepoint/external-sharing-overview). A folyamat általában különbözik az Azure AD B2B, a következő lehetőségeket biztosítva:

- Onedrive vállalati verzió/SharePoint online-hoz hozzáadja a felhasználókat arra a könyvtárra után a felhasználók beváltott a meghívást. Tehát előtt érvényesítési, nem látja a felhasználó Azure AD portálon. Ha egy másik hely addig felkéri a felhasználó, egy új meghívó jön létre. Azonban az Azure AD B2B együttműködés használata esetén hozzá lesznek adva azonnal meghívására, hogy azok meg mindenhol.

- Az érvényesítési élmény, a OneDrive/SharePoint online-ban a felhasználói felület az Azure AD B2B együttműködés fog kinézni. Miután egy felhasználó visszaváltja meghívót, a tapasztalatok egyformának.

- Az Azure AD B2B együttműködés meghívott felhasználók is tárolható a OneDrive/SharePoint online-hoz megosztása párbeszédpanel. OneDrive/SharePoint Online meghívott felhasználók is jelennek meg az Azure ad-ben után azokat a Meghívók beváltásához.

- A licencelési követelményeket eltérőek. Minden fizetős Azure AD-licencre, esetében engedélyezheti legfeljebb 5 vendégfelhasználót eléréséhez a fizetős Azure AD-funkciók. Licenceléssel kapcsolatos további tudnivalókért lásd: [Azure AD B2B-licencelés](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance) és ["Mi egy külső felhasználót?" a SharePoint Online-ban külső megosztása – áttekintés](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-is-an-external-user).

Külső megosztás kezelése a OneDrive/SharePoint online-ban az Azure AD B2B együttműködés, állítsa be a onedrive vállalati verzió/SharePoint online-hoz beállítással megosztása külső **a szervezet csak a külső felhasználók már létező megosztás engedélyezése Directory**. A felhasználók folytassa a külsőleg megosztott helyek és a rendszergazda által hozzáadott külső közreműködők választhatok. A rendszergazda adhat hozzá a külső együttműködők a B2B-együttműködés meghívó API-k használatával.


![A OneDrive/SharePoint Online külső megosztási beállítás](media/o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>További lépések

* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [B2B-együttműködés felhasználó hozzáadása szerepkörhöz](add-guest-to-role.md)
* [B2B-együttműködés meghívók delegálása](delegate-invitations.md)
* [Dinamikus csoportok és B2B-együttműködés](use-dynamic-groups.md)