---
title: Office 365 külső megosztás és B2B együttműködés – Azure AD
description: Az O365 és az Azure Active Directory B2B együttműködés használatával ismerteti az erőforrások külső partnerekkel való megosztását.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/24/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d70aed6fbe0f09ea6284f913c88186ecf94e297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272268"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Az Office 365 külső megosztása és az Azure Active Directory B2B együttműködése

Az Office 365 külső megosztása (OneDrive, SharePoint Online, Egyesített csoportok stb.) és az Azure Active Directory (Azure AD) B2B együttműködése technikailag ugyanaz. Minden külső megosztás (kivéve a OneDrive/SharePoint Online-t), beleértve az Office 365 Csoportok vendégeit is, már használja az Azure AD B2B együttműködési meghívó API-kat megosztásra.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Miben különbözik az Azure AD B2B a SharePoint Online külső megosztásától?

A OneDrive/SharePoint Online külön híváskezelővel rendelkezik. A OneDrive-on/SharePoint Online-on a külső megosztás támogatása az Azure AD támogatása előtt kezdődött. Idővel a OneDrive/SharePoint Online külső megosztása számos funkciót és több millió felhasználót gyűjtött össze, akik a termék beépített megosztási mintáját használják. A OneDrive/SharePoint Online külső megosztásának működése és az Azure AD B2B-együttműködés működése között azonban van néhány apró különbség. A OneDrive/SharePoint Online külső megosztásáról a [Külső megosztás áttekintése című témakörben olvashat bővebben.](https://docs.microsoft.com/sharepoint/external-sharing-overview) A folyamat általában eltér az Azure AD B2B az alábbi módokon:

- A OneDrive/SharePoint Online a felhasználók beváltása után hozzáadja a felhasználókat a címtárhoz. Így a beváltás előtt nem látja a felhasználót az Azure AD-portálon. Ha időközben egy másik webhely meghívja a felhasználót, új meghívó jön létre. Azonban az Azure AD B2B együttműködés használatakor a felhasználók azonnal hozzáadódik a meghívást, hogy azok jelennek meg mindenhol.

- A OneDrive/SharePoint Online beváltási élménye eltér az Azure AD B2B együttműködésben. Miután a felhasználó beváltottegy meghívót, az élmények ugyanúgy néznek ki.

- Az Azure AD B2B együttműködésre meghívott felhasználók a OneDrive/SharePoint Online megosztási párbeszédpaneleiből választhatnak. A OneDrive/SharePoint Online meghívott felhasználói a meghívók beváltása után is megjelennek az Azure AD-ben.

- A licencelési követelmények eltérőek. Minden egyes fizetős Azure AD-licenc esetén legfeljebb 5 vendégfelhasználó férhet hozzá a fizetős Azure AD-funkciókhoz. A licencelésről a SharePoint Online külső megosztásának áttekintése című témakörben olvashat bővebben az [Azure AD B2B licenceléséről](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance) és [a "Mi a külső felhasználó?" című témakörben.](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-happens-when-users-share)

Ha az Azure AD B2B együttműködéssel szeretné kezelni a onedrive/sharepoint online külső megosztást, állítsa a OneDrive/SharePoint Online külső megosztási beállítását **úgy, hogy a megosztás engedélyezése csak a szervezet címtárában már meglévő külső felhasználókkal.** A felhasználók a külsőleg megosztott webhelyekre léphetnek, és választhatnak a rendszergazda által hozzáadott külső közreműködők közül. A rendszergazda hozzáadhatja a külső közreműködők a B2B együttműködési meghívás API-k.


![A OneDrive/SharePoint Online külső megosztási beállítása](media/o365-external-user/odsp-sharing-setting.png)

A külső megosztás engedélyezése után a SharePoint Online (SPO) személyek választójában a meglévő vendégfelhasználók keresésének lehetősége alapértelmezés szerint ki van kapcsolva, hogy megfeleljen az örökölt viselkedésnek.

Ezt a szolgáltatást a "ShowPeoplePickerSuggestionsForGuestUsers" beállítással engedélyezheti a bérlői és webhelycsoport szintjén. A szolgáltatást a Set-SPOTenant és a Set-SPOSite parancsmagokkal állíthatja be, amelyek lehetővé teszik a tagok számára a címtárban lévő összes vendégfelhasználó keresését. A bérlői hatókör változásai nincsenek hatással a már kiépített SPO-helyekre.

## <a name="next-steps"></a>További lépések

* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [B2B együttműködési felhasználó hozzáadása szerepkörhöz](add-guest-to-role.md)
* [B2B együttműködési meghívók delegálása](delegate-invitations.md)
* [Dinamikus csoportok és B2B együttműködés](use-dynamic-groups.md)
* [Az Azure Active Directory B2B együttműködéssel kapcsolatos hibaelhárítása](troubleshoot.md)
