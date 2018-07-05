---
title: Az Azure Active Directoryban egyéni tartománynevek kezelése |} A Microsoft Docs
description: Felügyeleti fogalmakat és a egy tartománynevet az Azure Active Directory felügyeletéhez útmutatók
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.openlocfilehash: b503ef4c5cd922d4f7b58940cfc98a83a78ae986
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449918"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Az Azure Active Directoryban egyéni tartománynevek kezelése
Tartománynév fontos része annak a directory-erőforrások esetében az azonosító: felhasználói név vagy e-mail-cím egy felhasználó, csoport, a cím első része, és a egy alkalmazás az alkalmazás Alkalmazásazonosító URI-ja része lehet. Egy erőforrást az Azure Active Directory (Azure AD-) tartalmazhatnak, amely szerint a címtár, amely tartalmazza az erőforrás tulajdonosa már ellenőrizve van egy tartománynevet. Csak globális rendszergazda az Azure AD tartományi felügyeleti feladatok is végrehajthatja.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Állítsa be az elsődleges tartomány nevét az Azure AD-címtár
Ha a címtár létrejött, a kezdeti tartománynevet, például "contoso.onmicrosoft.com", is tartománynév elsődleges. Az elsődleges tartomány a egy új felhasználó alapértelmezett tartománynév, ha új felhasználót hoz létre. Elsődleges tartománynév beállítása leegyszerűsíti a folyamatot a rendszergazdák az új felhasználók létrehozása a portálon. Az elsődleges tartomány nevének módosítása:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza az **Azure Active Directory** elemet.
3. Válassza ki **egyéni tartománynevek**.
     
   ![Nyitó felhasználók kezelése](./media/domains-manage/add-custom-domain.png)
4. Válassza ki a tartományt, érdemes az elsődleges tartomány nevét.
5. Válassza ki a **elsődlegessé** parancsot. Erősítse meg választását, amikor a rendszer kéri.
   
   ![Tartománynév elsődlegessé alakítás](./media/domains-manage/make-primary-domain.png)

Módosíthatja a át minden olyan ellenőrzött egyéni tartománynak, amely nincs összevonva az elsődleges tartomány nevét. Az elsődleges tartomány a címtár módosítása nem módosítja a felhasználónevek, bármely meglévő felhasználó számára.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Egyéni tartománynevek hozzáadása az Azure AD-bérlővel
Felügyelt 900 tartománynév legfeljebb is összeadódhatnak. Az összevonáshoz minden tartományban a helyszíni Active Directoryval való konfigurálásakor, adhat hozzá minden könyvtárban 450 tartománynevek legfeljebb. További információkért lásd: [az összevont és felügyelt tartománynevek](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names).

## <a name="add-subdomains-of-a-custom-domain"></a>Egyéni tartomány altartományait hozzáadása
Ha például a "europe.contoso.com" harmadik szintű tartománynév hozzáadása a címtárhoz, kell először adja hozzá és ellenőrizze a második szintű tartomány, például a contoso.com. Az altartomány rendszer automatikusan ellenőrzi az Azure AD által. Látható, hogy az imént hozzáadott altartomány ellenőrzése megtörtént, frissítse a lapot a böngészőben, amely azokat a tartományokat listázza.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Mi a teendő, ha módosítja a DNS-regisztráló az egyéni tartománynevet?
Ha módosítja a DNS-regisztráló az egyéni tartománynevet, továbbra is az egyéni tartománynevét használja az Azure AD-magát és további konfigurációs feladatok megszakítás nélkül. Ha az egyéni tartománynevet az Office 365-öt használ, Intune-ban vagy más szolgáltatások által használt egyéni tartománynevek, az Azure ad-ben, tekintse meg ezeket a szolgáltatásokat a dokumentációban.

## <a name="delete-a-custom-domain-name"></a>Egyéni tartománynév törlése
Egyéni tartománynév törölheti az Azure AD-ből, ha a szervezet már nem használja ezt a nevet, vagy ha szeretné használni ezt a nevet egy másik Azure AD-val.

Egyéni tartománynév törlése, akkor előbb ellenőrizze, hogy nincsenek erőforrások a címtár támaszkodik a tartomány nevét. A címtárban lévő tartománynév nem törölhető, ha:

* Minden felhasználó rendelkezik egy felhasználónevet, a e-mail cím vagy a proxykiszolgáló címét, amely tartalmazza a tartománynevet.
* Minden csoport rendelkezik egy e-mail-címet vagy a proxykiszolgáló címét, amely tartalmazza a tartománynevet.
* Bármilyen alkalmazást az Azure ad-alkalmazás Alkalmazásazonosító URI-ja, amely tartalmazza a tartománynevet rendelkezik.

Módosítani kell, vagy bármely ilyen erőforrás törlése az Azure AD-címtárban, az egyéni tartománynév törlése előtt.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Tartománynevek kezelése a PowerShell vagy Graph API használata
A tartománynevek az Azure Active Directoryban a legtöbb kezelési feladatot a Microsoft PowerShell-lel, vagy programozott módon az Azure AD Graph API használatával is elvégezhető.

* [Tartománynevek kezelése az Azure ad-ben a PowerShell segítségével](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Tartománynevek kezelése az Azure AD Graph API-val](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>További lépések
* [Egyéni tartománynevek hozzáadása](../fundamentals/add-custom-domain.md)

