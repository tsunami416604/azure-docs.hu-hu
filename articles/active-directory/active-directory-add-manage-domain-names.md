---
title: "Az Azure Active Directoryban egyéni tartománynevek kezelése |} Microsoft Docs"
description: "Felügyeleti fogalmakat és használati útmutatók az Azure Active Directoryban egyéni tartományok kezelése"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: cf3523bd-9ee0-439e-963d-ccea038867b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
robots: NOINDEX
ms.openlocfilehash: 5ae19bb370064de96cf466ca09b13d02563d65a4
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Az Azure Active Directoryban egyéni tartománynevek kezelése
A tartomány neve lehet sok címtárerőforrásokkal fontos azonosítója részeként:

* A felhasználói név vagy e-mail cím egy felhasználó számára
* A cím egy csoporthoz
* A app ID URI az alkalmazáshoz

Az Azure Active Directory (Azure AD) erőforrás neve, a rendszer már ellenőrzi a könyvtárban, amely tartalmazza az erőforrás tulajdonosa lehet tartalmazhatnak. Csak egy globális rendszergazda Azure AD-ben végrehajthat tartomány feladatokat.

> [!IMPORTANT]
> A Microsoft javasolja, hogy az Azure Portalon található [Azure AD felügyeleti központból](https://aad.portal.azure.com) kezelje az Azure AD-t az ebben a cikkben javasolt klasszikus Azure portál helyett. Az Azure AD felügyeleti központban a tartománynevek kezelése című történő [az Azure Active Directoryban egyéni tartománynevek kezelése](active-directory-domains-manage-azure-portal.md).

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Állítsa be az elsődleges tartomány nevét az Azure AD-címtár
A könyvtár jön létre, a kezdeti tartománynevet, például "contoso.onmicrosoft.com," esetén is az elsődleges tartomány nevét a címtáron. Az elsődleges tartomány esetén egy új felhasználó számára az alapértelmezett tartomány nevét az új felhasználó létrehozása a [a klasszikus Azure portálon](https://manage.windowsazure.com/), vagy más portálokon, például az Office 365 felügyeleti portálon. Ez leegyszerűsíti és felgyorsítja a rendszergazda számára hozzon létre új felhasználók a portálon.

Az elsődleges tartomány nevét a címtáron módosítása:

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com/) az Azure AD-címtár globális rendszergazdájának felhasználói fiókjával.
2. Válassza ki **Active Directory** a bal oldali navigációs sávon.
3. Nyissa meg a címtárat.
4. Válassza ki a **tartományok** fülre.
5. Válassza ki a **módosítsa elsődleges** gombra a parancssávon.
6. Válassza ki a tartományt, amelyet az új elsődleges tartományt a címtáron.

Módosíthatja a címtáron bármely, a ellenőrzött egyéni tartományt is, hogy az nem összevont elsődleges tartományneve. Az elsődleges tartomány a címtáron módosítása nem módosítja a felhasználónevek, a meglévő felhasználókat.

## <a name="add-custom-domain-names-to-your-azure-ad"></a>Egyéni tartománynevek hozzáadása az Azure AD
Minden Azure AD-címtár legfeljebb 900 egyéni tartományneveket adhat hozzá. A folyamat [további egyéni tartománynév hozzáadása](active-directory-add-domain.md) megegyezik az első egyéni tartománynevet.

## <a name="add-subdomains-of-a-custom-domain"></a>Altartományok az egyéni tartomány hozzáadása
Ha szeretne egy harmadik szintű tartomány neve, pl. "europe.contoso.com" adni a könyvtárhoz, először adja hozzá, és ellenőrizze a második szintű tartomány, például contoso.com. Az altartomány Azure AD által automatikusan fog ellenőrizhető. Jelenik meg, hogy a most felvett altartomány ellenőrzése megtörtént, frissítse a lapot a böngészőben, amely felsorolja azokat a tartományokat a címtárban.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Mi a teendő, ha a DNS-regisztráló webhelyén módosítja az egyéni tartománynevet
Ha módosítja a DNS-regisztráló webhelyén az egyéni tartománynevet, továbbra is az egyéni tartománynevet használja az Azure AD maga megszakítás nélkül, és további konfigurációs feladatok nélkül. Az egyéni tartománynevet és az Office 365 használatakor, Intune, illetve egyéb szolgáltatások egyéni tartománynevekkel az Azure ad-ben, tekintse meg a szolgáltatások dokumentációjában.

## <a name="delete-a-custom-domain-name"></a>Egyéni tartománynév törlése
Egy egyéni tartománynevet törölheti az Azure AD-ből, ha a szervezet már nem használja ezt a nevet, vagy ha ezt a nevet használja egy másik Azure AD-val kell.

Törölni egy egyéni tartománynevet, akkor előbb ellenőrizze, hogy a címtárban nincsenek erőforrások támaszkodjon a tartomány nevét. A címtárban lévő tartománynév nem törölhető, ha:

* Minden olyan felhasználó, egy felhasználónevet, e-mail címét vagy a tartománynevet tartalmazó proxycímmel rendelkezik.
* Bármely csoport rendelkezik egy e-mail címet, vagy a proxykiszolgáló címét, amely a tartomány nevét tartalmazza.
* Az Azure AD bármely alkalmazásnak egy app ID URI, amely a tartomány nevét tartalmazza.

Módosít, vagy minden ilyen erőforrás törlése az Azure AD-címtárát, az egyéni tartománynév törlése előtt.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Használja a Powershellt vagy a Graph API tartománynevek kezelése
A legtöbb kezelési feladatot az Azure Active Directoryban tartománynevek is elvégezhető, Microsoft PowerShell használatával, vagy az Azure AD Graph API-val programozott módon.

* [Tartománynevek kezelése az Azure AD PowerShell segítségével](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Tartománynevek kezelése az Azure AD Graph API segítségével](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Következő lépések
* [Tartománynevek megismerése az Azure ad-ben](active-directory-add-domain-concepts.md)
* [Egyéni tartománynevek kezelése](active-directory-add-manage-domain-names.md)

