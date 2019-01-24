---
title: 'Az Azure Active Directory tartományi szolgáltatások: Funkciók |} A Microsoft Docs'
description: Az Azure Active Directory Domain Services szolgáltatásokat
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: 82bca4f150adfb940609dd1642e74e7b42340c22
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54849039"
---
# <a name="azure-ad-domain-services"></a>Azure AD Domain Services
## <a name="features"></a>Szolgáltatások
A következő funkciók érhetők el Azure AD tartományi szolgáltatásokkal felügyelt tartományban.

* **Egyszerű üzembe helyezési feladatok:** Azure AD tartományi szolgáltatásokat az Azure AD-címtárhoz, mindössze néhány kattintással használatával engedélyezheti. A felügyelt tartományra csak felhőalapú felhasználói fiókok és a helyszíni címtárból szinkronizált felhasználói fiókok tartalmazza.
* **Tartományhoz való csatlakozás támogatását:** Számítógépek tartományhoz való csatlakozás az Azure virtuális hálózat, a felügyelt tartomány érhető el egyszerűen. A tartományhoz való csatlakozás élmény a Windows ügyfél és kiszolgáló operációs rendszerek működik zökkenőmentesen az Azure AD tartományi szolgáltatások által kiszolgált tartományok ellen. Használhatja ilyen tartományok elleni eszközök automatikus tartományhoz való csatlakozást.
* **Az Azure AD-címtár egyik tartomány példányonként:** Létrehozhat egy egyetlen Active Directory-tartomány minden egyes Azure AD-címtárhoz.
* **Hozzon létre tartományok egyedi nevek:** (Például "contoso100.com") nevű egyéni tartományok az Azure AD Domain Services használatával hozhat létre. Használhatja az egyik ellenőrzött vagy nem ellenőrzött tartomány nevét. Igény szerint is létrehozhat egy tartományi beépített tartományutótagjával (azt jelenti, ' *. onmicrosoft.com ") az Azure AD-címtár által kínált.
* **Az Azure AD-vel integrált:** Nem kell konfigurálni vagy felügyelni a replikálás az Azure AD tartományi szolgáltatásokat. Felhasználói fiókok, a csoporttagságokat és a felhasználói hitelesítő adatokat (jelszó) az Azure AD-címtár automatikusan elérhetők az Azure AD tartományi szolgáltatásokban. Új felhasználók, csoportok vagy az Azure AD-bérlő vagy a helyszíni címtár attribútumok változásait automatikusan szinkronizálja az Azure AD tartományi szolgáltatásokra.
* **Az NTLM és Kerberos-hitelesítéshez:** Az NTLM és Kerberos hitelesítés támogatása üzembe helyezheti a Windows-Integrated hitelesítést használó alkalmazások.
* **Használja a vállalati hitelesítő adatok és jelszavak:** Az Azure AD-bérlő felhasználó jelszavát az Azure AD tartományi szolgáltatásokkal működik. Felhasználók a vállalati hitelesítő adatait a tartományhoz csatlakozó gépek használja, jelentkezzen be interaktívan, vagy a távoli asztali kapcsolaton keresztül, és a felügyelt tartomány hitelesítése.
* **LDAP-kötés & LDAP olvassa el a támogatás:** A felhasználók hitelesítése a tartományok Azure AD tartományi szolgáltatások által kiszolgált LDAP-kötések használó alkalmazások is használhatja. Emellett alkalmazásokat, amelyek használják az LDAP-olvasási műveletek lekérdezés felhasználó/számítógép-attribútumok a címtárból is dolgozhat ellen az Azure AD tartományi szolgáltatásokat.
* **Biztonságos LDAP (LDAPS):** Engedélyezheti a hozzáférést a könyvtár keresztül biztonságos LDAP (LDAPS). Secure LDAP-hozzáférését alapértelmezés szerint a virtuális hálózaton belül érhető el. Azonban Ön is engedélyezheti a secure LDAP-hozzáférését az interneten keresztül.
* **Csoportházirend:** A felhasználók és számítógépek használhat egyetlen beépített csoportházirend-objektum minden egyes tárolókat kényszerítse a megfelelőséget a szükséges biztonsági szabályzatok felhasználói fiókokat és a tartományhoz csatlakoztatott számítógépeket. Emellett a saját egyéni csoportházirend-objektumok létrehozása és egyéni szervezeti egységek, és hozzárendelheti azokat [a Csoportházirend kezelése](active-directory-ds-admin-guide-administer-group-policy.md).
* **DNS kezelése:** Az "AAD DC rendszergazdák" csoport tagjai DNS kezelheti a felügyelt tartomány már jól ismert DNS felügyeleti eszközökkel, például a DNS-felügyeleti beépülő MMC-modulban.
* **Hozzon létre egyéni szervezeti egységhez (OU):** Az "AAD DC rendszergazdák" csoport tagjai a felügyelt tartomány hozhat létre egyéni szervezeti egységekhez. Ezek a felhasználók kapnak teljes körű rendszergazdai jogosultságokkal keresztül egyéni szervezeti egységek, így azok hozzáadhat/eltávolíthat szolgáltatásfiókok, számítógépek, csoportok stb. ezeket az egyéni szervezeti belül.
* **Az Azure globális számos régióban elérhető:** [Az Azure régiói](https://azure.microsoft.com/regions/#services/) lapon találja azoknak az Azure-régióknak a felsorolását, amelyekben elérhetők az Azure AD tartományi szolgáltatások.
* **Magas rendelkezésre állás:** Az Azure AD tartományi szolgáltatások magas rendelkezésre állást a tartomány kínál. Ez a funkció a garancia magasabb a hasznos üzemidő és hibatűrést biztosít. Beépített állapotfigyelési ajánlatok automatizált szervizelési hibák esetén cserélje le a sikertelen példányok és folyamatos szolgáltatásokat a tartomány új példányokat kell indítani.
* **AD fiókzárolási:** Felhasználói fiókok zárolt 30 percig öt érvénytelen jelszavak használata 2 percen belül. Fiókok 30 perc után lesznek automatikusan feloldani.
* **Használja a jól ismert felügyeleti eszközöket:** A felügyelt tartomány felügyeletéhez használhatja a jól ismert Windows Server Active Directory kezelőeszközöket, például az Active Directory felügyeleti központ vagy az Active Directory PowerShell.
