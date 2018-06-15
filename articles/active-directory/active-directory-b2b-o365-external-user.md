---
title: Külső megosztás Office 365 és Azure Active Directory B2B együttműködés |} Microsoft Docs
description: Erőforrások megosztása külső partnerei, az Office 365 és az Azure Active Directory B2B együttműködés, és ismerteti.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/24/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 4421e6ca2bdce721c360f2701b3693024a694eb6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33931962"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Külső megosztás Office 365 és Azure Active Directory B2B együttműködés

Az Office 365 (OneDrive, SharePoint online-hoz, egységes csoportok stb.) és az Azure Active Directory (Azure AD) B2B együttműködés megosztása külső rendszer technikailag az ugyanaz. Minden külső megosztás (kivéve a onedrive-on vagy a SharePoint Online), többek között a vendégek az Office 365-csoportok, már használja az Azure AD B2B együttműködés meghívó API-k a megosztásához.

A OneDrive vagy a SharePoint Online rendelkezik egy külön meghívó manager. A onedrive-on vagy a SharePoint Online elindítása előtt az Azure AD fejlesztett támogatását külső megosztás támogatása. Idővel OneDrive vagy a SharePoint Online külső megosztás esedékes számos szolgáltatást és a termék használó felhasználók sok több millió tartozó a-épül megosztása mintát. Van azonban néhány finom eltérések vannak a onedrive-on vagy a SharePoint Online külső megosztás működése, és az Azure AD B2B együttműködés működéséről között:

- A OneDrive vagy a SharePoint Online hozzáadja a felhasználókat a könyvtár után a felhasználók rendelkeznek sikerült beváltani a meghívást. Igen előtt érvényesítési, nem látható a felhasználó Azure AD portálon. Ha egy másik hely időközben felkéri a felhasználó, egy új meghívó jön létre. Azonban Azure AD B2B együttműködés használata esetén felhasználót adnak hozzá közvetlenül a meghívót, hogy azok megjelenni everywhere.

- Az érvényesítési élmény, a onedrive-on vagy a SharePoint Online az Azure AD B2B együttműködés élményt fog kinézni. Miután egy felhasználó visszaváltja meghívót, az feladatait egyformának.

- Az Azure AD B2B együttműködés meghívott felhasználók is tárolható a onedrive-on vagy a SharePoint Online-ból párbeszédpanelek. OneDrive vagy a SharePoint Online meghívott felhasználók is jelenik meg az Azure AD után azok beváltani a meghívást.

- Külső megosztás kezelése a onedrive-on vagy a SharePoint Online az Azure AD B2B együttműködés, állítsa be a onedrive-on vagy a SharePoint Online külső megosztási beállítással **csak a könyvtárban már külső felhasználókkal való megosztásának engedélyezése**. Felhasználók ugorjon a külsőleg megosztott hely, és válassza ki a külső együttműködők, hogy a rendszergazda hozzáad. A rendszergazda adhat hozzá a külső együttműködők a B2B együttműködés meghívót API-k használatával.

![A onedrive-on vagy a SharePoint Online külső megosztásának beállítása](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>További lépések

* [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Egy szerepkör B2B együttműködés felhasználók hozzáadása](active-directory-b2b-add-guest-to-role.md)
* [B2B együttműködés meghívókat delegálása](active-directory-b2b-delegate-invitations.md)
* [Dinamikus csoportok és a B2B együttműködés](active-directory-b2b-dynamic-groups.md)