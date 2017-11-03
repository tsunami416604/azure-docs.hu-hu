---
title: "LinkedIn-integráció konfigurálása az Azure AD |} Microsoft Docs"
description: "Ismerteti, hogyan engedélyezhető vagy tiltható le Microsoft-alkalmazások az Azure Active Directoryban LinkedIn-integráció."
services: active-directory
author: jeffgilb
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: 48b26bfcce67ce915c404a0ab2ac0f399c3b821d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="enabling-linkedin-integration-in-azure-active-directory"></a>Az Azure Active Directoryban LinkedIn-integráció engedélyezése
LinkedIn-integráció engedélyezése lehetővé teszi, hogy a felhasználók mindkét LinkedIn nyilvános adatok eléréséhez és, ha az általuk választott, a személyes LinkedIn hálózaton belül Microsoft-alkalmazások. Minden felhasználó egymástól függetlenül választhat LinkedIn-fiókjuk csatlakozni a munkahelyi fiókjával.

### <a name="linkedin-integration-from-your-end-users-perspective"></a>A végfelhasználók szempontjából LinkedIn-integráció
Ha a szervezet a végfelhasználók LinkedIn fiókjukat kapcsolódnak a munkahelyi fiókkal, képesek pontosabban azonosítani a működnek a belül, és a szervezeten kívüli személyeknek. Csatlakozás a két fiók lehetővé teszi, hogy a felhasználó LinkedIn kapcsolatok és a szervezet Microsoft-alkalmazások használhatók profiladatok, de azok is mindig nem vesznek el az adatok megosztása LinkedIn engedély eltávolításával. Az integráció is használ a nyilvánosan elérhető profiladatok, és a felhasználók eldönthetik, hogy a nyilvános profil és hálózati információk megosztása LinkedIn adatvédelmi beállítások a Microsoft-alkalmazások.

>[!NOTE]
> Az Azure AD LinkedIn-integráció engedélyezése lehetővé teszi, hogy az alkalmazások és szolgáltatások eléréséhez a végfelhasználók adatok egy részét. Olvassa el a [Microsoft adatvédelmi nyilatkozatát](https://privacy.microsoft.com/privacystatement/) további információt az adatvédelmi megfontolások, ha engedélyezi a LinkedIn-integráció az Azure ad-ben. 

## <a name="enable-linkedin-integration"></a>LinkedIn-integráció engedélyezése
A vállalatok LinkedIn-integráció alapértelmezés szerint engedélyezve van a Azure AD-ben. Igen Ez a funkció a bérlő számára legyen elérhető, ha a szervezete összes felhasználója látható LinkedIn-szolgáltatások és a profilok. LinkedIn-integráció engedélyezése lehetővé teszi a felhasználók a szervezeten belül Microsoft szolgáltatások, például a profilok megtekintés, küld egy e-maileket az Outlook LinkedIn funkcióinak használatát. E funkció letiltása megakadályozza a LinkedIn-szolgáltatásokat, és leállítja a felhasználói fiók kapcsolatok és adatmegosztását LinkedIn és a szervezet Microsoft-szolgáltatások között.

> [!IMPORTANT]
> Nyissa meg helyi, állami és kormányzati bérlők a szolgáltatás nem érhető el. Ezenkívül az Azure AD szolgáltatás hogyan frissíti, LinkedIn integrációs funkciók, például az összes Azure bérlők nem telepít egy időben. Nem engedélyezhető a LinkedIn-integráció az Azure AD, amíg ez a funkció az Azure-bérlőhöz való lett állítva.

1. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **felhasználók és csoportok**.
3. Az a **felhasználók és csoportok** panelen válassza **felhasználói beállítások**.
4. A **LinkedIn integrációs**, jelölje be Igen vagy nem engedélyezhető vagy tiltható le a LinkedIn-integráció.
   ![LinkedIn-integráció engedélyezése](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="learn-more"></a>Részletek 
* [LinkedIn-információkat és szolgáltatások a Microsoft-alkalmazások](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn Súgó](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Következő lépések
A következő hivatkozás segítségével engedélyezheti vagy tilthatja le a LinkedIn-integráció az Azure AD az Azure-portálon:

[LinkedIn-integráció konfigurálása](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 