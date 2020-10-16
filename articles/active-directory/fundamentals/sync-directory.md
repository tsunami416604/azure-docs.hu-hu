---
title: Címtár-szinkronizálás Azure Active Directory
description: A szinkronizálási minta megvalósítására szolgáló építészeti útmutató
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 862d17948cb09c18f9372f8b8b981e5efa6be71b
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114180"
---
# <a name="directory-synchronization"></a>Címtár-szinkronizálás

Számos szervezet rendelkezik egy hibrid infrastruktúrával, amely magában foglalja a helyszíni és a Felhőbeli összetevőket is. A felhasználók identitásának szinkronizálása a helyi és a Felhőbeli címtárak között lehetővé teszi, hogy a felhasználók egyetlen hitelesítő adatokkal férhessenek hozzá az erőforrásokhoz. 

A szinkronizálás folyamata 

* objektum létrehozása bizonyos feltételek alapján
* az objektum naprakészen tartása
* az objektum eltávolítása, ha a feltételek már nem teljesülnek. 

A helyszíni kiépítés magában foglalja a helyszíni forrásokból (például Active Directory) való kiépítést Azure Active Directory (Azure AD) számára. 

## <a name="use-when"></a>A következő esetekben használja

A helyszíni Active Directory környezetekről az Azure AD-be kell szinkronizálnia az azonosító adatokat.

![építészeti diagram](./media/authentication-patterns/dir-sync-auth.png)

## <a name="components-of-system"></a>A System összetevői

* **Felhasználó**: az Azure ad használatával fér hozzá egy alkalmazáshoz.

* **Webböngésző**: az alkalmazás külső URL-címének eléréséhez a felhasználó által kommunikáló összetevő.

* **Application**: webalkalmazás, amely az Azure ad hitelesítésre és engedélyezésre való használatára támaszkodik.

* **Azure ad**: a szervezet helyi könyvtárából származó azonosító adatok szinkronizálása Azure ad Connect használatával. 

* **Azure ad Connect**: a helyszíni identitás-infrastruktúrák Microsoft Azure ADhoz való csatlakoztatására szolgáló eszköz. A varázsló és az interaktív élmények segítségével üzembe helyezheti és konfigurálhatja a csatlakozáshoz szükséges előfeltételeket és összetevőket, beleértve a szinkronizálást és a bejelentkezést az Active Directory-ból az Azure AD-be. 

* **Active Directory**: a Active Directory a legtöbb Windows Server operációs rendszerhez tartozó címtárszolgáltatás. Active Directory tartományi szolgáltatások (AD DS) rendszert futtató kiszolgálókat tartományvezérlőknek nevezzük. A tartomány minden felhasználóját és számítógépét hitelesítik és engedélyezik.

## <a name="implement-directory-synchronization-with-azure-ad"></a>Címtár-szinkronizálás megvalósítása az Azure AD-vel

* [Mi az az identitáslétesítés?](https://docs.microsoft.com/azure/active-directory/cloud-provisioning/what-is-provisioning) 

* [Hibrid identitási címtár-integrációs eszközök](https://docs.microsoft.com/azure/active-directory/hybrid/plan-hybrid-identity-design-considerations-tools-comparison) 

* [Azure AD Connect telepítési ütemterv](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-roadmap)
