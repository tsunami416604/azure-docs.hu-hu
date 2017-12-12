---
title: "Az Azure Active Directoryban egyéni tartománynevek kezelése |} Microsoft Docs"
description: "Felügyeleti fogalmakat és a tartomány nevét az Azure Active Directory felügyeletéhez használati útmutatók"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 5063cd0a-dba2-4ba9-aa65-b8117490d73a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.openlocfilehash: 64c1be4358305a736ac1dd8a1b7194c80100d256
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Az Azure Active Directoryban egyéni tartománynevek kezelése
A tartománynév sok címtárerőforrásokkal azonosítója fontos része: felhasználói név vagy e-mail cím egy felhasználó, a cím egy csoport része, és hozzájárulhat az alkalmazás az alkalmazás URI azonosítója. Az Azure Active Directory (Azure AD) erőforrás lehetnek egy tartomány nevét, amely már ellenőrizve, a könyvtárban, amely tartalmazza az erőforrás tulajdonosa. Csak egy globális rendszergazda Azure AD-ben végrehajthat tartomány feladatokat.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Állítsa be az elsődleges tartomány nevét az Azure AD-címtár
A könyvtár jön létre, a kezdeti tartománynevet, például "contoso.onmicrosoft.com," esetén is az elsődleges tartomány nevét. Az elsődleges tartomány az alapértelmezett tartomány nevét az új felhasználó esetén létrehoz egy új felhasználót. Elsődleges tartománynév beállítása leegyszerűsíti és felgyorsítja a rendszergazda számára hozzon létre új felhasználók a portálon. Az elsődleges tartomány nevének módosítása:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **az Azure Active Directory**.
3. Válassza ki **egyéni tartománynevek**.
     
   ![Nyitó felhasználók kezelése](./media/active-directory-domains-manage-azure-portal/add-custom-domain.png)
4. Válassza ki a tartományban, amelyhez az elsődleges tartomány tenni kívánt nevét.
5. Válassza ki a **ellenőrizze elsődleges** parancsot. Erősítse meg választását, amikor a rendszer kéri.
   
   ![Ellenőrizze a tartománynév elsődleges](./media/active-directory-domains-manage-azure-portal/make-primary-domain.png)

Módosíthatja a címtáron bármely, a ellenőrzött egyéni tartományt is, hogy az nem összevont elsődleges tartományneve. Az elsődleges tartomány a címtáron módosítása nem módosítja a felhasználónevek, a meglévő felhasználókat.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Egyéni tartománynevek hozzáadása az Azure AD-bérlő
900 felügyelt tartománynév legfeljebb is egyezzen. Az összevonáshoz a tartományok konfigurálása a helyszíni Active Directoryval, adhat hozzá minden könyvtárban 450 tartománynevek legfeljebb. További információkért lásd: [külső és a felügyelt tartománynév](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names).

## <a name="add-subdomains-of-a-custom-domain"></a>Altartományok az egyéni tartomány hozzáadása
Ha szeretne egy harmadik szintű tartomány neve, pl. "europe.contoso.com" adni a könyvtárhoz, először adja hozzá, és ellenőrizze a második szintű tartomány, például contoso.com. Az altartomány Azure AD által automatikusan fog ellenőrizhető. Jelenik meg, hogy a most felvett altartomány ellenőrzése megtörtént, frissítse a lapot a böngészőben, amely felsorolja azokat a tartományokat.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Mi a teendő, ha a DNS-regisztráló webhelyén módosítja az egyéni tartománynevet
Ha módosítja a DNS-regisztráló webhelyén az egyéni tartománynevet, továbbra is az egyéni tartománynevet használja az Azure AD maga megszakítás nélkül, és további konfigurációs feladatok nélkül. Az egyéni tartománynevet és az Office 365 használatakor, Intune, illetve egyéb szolgáltatások egyéni tartománynevekkel az Azure ad-ben, tekintse meg a szolgáltatások dokumentációjában.

## <a name="delete-a-custom-domain-name"></a>Egyéni tartománynév törlése
Egy egyéni tartománynevet törölheti az Azure AD-ből, ha a szervezet már nem használja ezt a nevet, vagy ha ezt a nevet használja egy másik Azure AD-val kell.

Törölni egy egyéni tartománynevet, akkor előbb ellenőrizze, hogy a címtárban nincsenek erőforrások támaszkodjon a tartomány nevét. A címtárban lévő tartománynév nem törölhető, ha:

* Bármely felhasználó rendelkezik egy felhasználónevet, a e-mail cím vagy a proxykiszolgáló címét, amely tartalmazza a tartománynevet.
* Bármely csoport rendelkezik egy e-mail címet, vagy a proxykiszolgáló címét, amely a tartomány nevét tartalmazza.
* Az Azure AD bármely alkalmazásnak egy app ID URI, amely a tartomány nevét tartalmazza.

Módosít, vagy minden ilyen erőforrás törlése az Azure AD-címtárát, az egyéni tartománynév törlése előtt.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Használja a Powershellt vagy a Graph API tartománynevek kezelése
A legtöbb kezelési feladatot az Azure Active Directoryban tartománynevek is elvégezhető, Microsoft PowerShell használatával, vagy az Azure AD Graph API-val programozott módon.

* [Tartománynevek kezelése az Azure AD PowerShell segítségével](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Tartománynevek kezelése az Azure AD Graph API segítségével](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Következő lépések
* [Egyéni tartománynevek hozzáadása](add-custom-domain.md)

