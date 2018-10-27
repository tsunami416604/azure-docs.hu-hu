---
title: 'Az Azure Active Directory tartományi szolgáltatások: Funkciók |} A Microsoft Docs'
description: Az Azure Active Directory Domain Services szolgáltatásokat
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
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
ms.openlocfilehash: cd4fdb1e59f5b85fdfd8eb7ca17b77d02dcdac05
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50157037"
---
# <a name="azure-ad-domain-services"></a>Azure AD Domain Services
## <a name="features"></a>Szolgáltatások
A következő funkciók érhetők el Azure AD tartományi szolgáltatásokkal felügyelt tartományban.

* **Egyszerű üzembe helyezési élmény:** engedélyezheti az Azure AD Domain Services használatával mindössze néhány kattintással az Azure AD-címtárhoz. A felügyelt tartományra csak felhőalapú felhasználói fiókok és a helyszíni címtárból szinkronizált felhasználói fiókok tartalmazza.
* **Tartományhoz való csatlakozás támogatását:** könnyedén tartományhoz csatlakozó számítógépek érhető el a felügyelt tartományhoz az Azure virtuális hálózatban. A tartományhoz való csatlakozás élmény a Windows ügyfél és kiszolgáló operációs rendszerek működik zökkenőmentesen az Azure AD tartományi szolgáltatások által kiszolgált tartományok ellen. Használhatja ilyen tartományok elleni eszközök automatikus tartományhoz való csatlakozást.
* **Az Azure AD-címtár egyik tartomány példányonként:** hozhat létre egy egyetlen Active Directory-tartomány minden egyes Azure AD-címtárhoz.
* **Egyéni névvel rendelkező tartományok létrehozása:** (például "contoso100.com") nevű egyéni tartományok az Azure AD Domain Services használatával is létrehozhat. Használhatja az egyik ellenőrzött vagy nem ellenőrzött tartomány nevét. Igény szerint is létrehozhat egy tartományi beépített tartományutótagjával (azt jelenti, ' *. onmicrosoft.com ") az Azure AD-címtár által kínált.
* **Az Azure AD-vel integrált:** , nem kell konfigurálni vagy felügyelni a replikálás az Azure AD tartományi szolgáltatásokat. Felhasználói fiókok, a csoporttagságokat és a felhasználói hitelesítő adatokat (jelszó) az Azure AD-címtár automatikusan elérhetők az Azure AD tartományi szolgáltatásokban. Új felhasználók, csoportok vagy az Azure AD-bérlő vagy a helyszíni címtár attribútumok változásait automatikusan szinkronizálja az Azure AD tartományi szolgáltatásokra.
* **Az NTLM és Kerberos-hitelesítéshez:** támogatása az NTLM és Kerberos-hitelesítéshez, üzembe helyezheti a Windows-Integrated hitelesítést használó alkalmazások.
* **Használja a vállalati hitelesítő adatok és jelszavak:** a bérlői az Azure AD tartományi szolgáltatások használata az Azure AD-felhasználó jelszavát. Felhasználók a vállalati hitelesítő adatait a tartományhoz csatlakozó gépek használja, jelentkezzen be interaktívan, vagy a távoli asztali kapcsolaton keresztül, és a felügyelt tartomány hitelesítése.
* **LDAP-kötés & LDAP olvassa el a támogatás:** a felhasználók hitelesítése a tartományok Azure AD tartományi szolgáltatások által kiszolgált LDAP-kötések használó alkalmazások is használhatja. Emellett alkalmazásokat, amelyek használják az LDAP-olvasási műveletek lekérdezés felhasználó/számítógép-attribútumok a címtárból is dolgozhat ellen az Azure AD tartományi szolgáltatásokat.
* **Biztonságos LDAP (LDAPS):** , lehetővé teszik, hogy a könyvtár keresztül biztonságos LDAP (LDAPS). Secure LDAP-hozzáférését alapértelmezés szerint a virtuális hálózaton belül érhető el. Azonban Ön is engedélyezheti a secure LDAP-hozzáférését az interneten keresztül.
* **Csoportházirend:** egyetlen beépített csoportházirend-objektum minden egyes használhatja a felhasználók és számítógépek tárolókat kényszerítse a megfelelőséget a szükséges biztonsági szabályzatok felhasználói fiókokat és a tartományhoz csatlakoztatott számítógépeket. Emellett a saját egyéni csoportházirend-objektumok létrehozása és egyéni szervezeti egységek, és hozzárendelheti azokat [a Csoportházirend kezelése](active-directory-ds-admin-guide-administer-group-policy.md).
* **DNS kezelése:** az "AAD DC rendszergazdák" csoport tagjai DNS kezelheti a felügyelt tartomány már jól ismert DNS felügyeleti eszközökkel, például a DNS-felügyeleti beépülő MMC-modulban.
* **Egyéni szervezeti egység (OU-k) létrehozása:** az "AAD DC rendszergazdák" csoport tagjai a felügyelt tartományban hozhat létre egyéni szervezeti egységekhez. Ezek a felhasználók kapnak teljes körű rendszergazdai jogosultságokkal keresztül egyéni szervezeti egységek, így azok hozzáadhat/eltávolíthat szolgáltatásfiókok, számítógépek, csoportok stb. ezeket az egyéni szervezeti belül.
* **Számos Azure globális régiókban érhető el:** tekintse meg a [az Azure-szolgáltatások régió szerint](https://azure.microsoft.com/regions/#services/) oldal az Azure-régiókban, amelyben az Azure AD Domain Services érhető el.
* **Magas rendelkezésre állás:** Azure AD tartományi szolgáltatások magas rendelkezésre állást a tartomány kínál. Ez a funkció a garancia magasabb a hasznos üzemidő és hibatűrést biztosít. Beépített állapotfigyelési ajánlatok automatizált szervizelési hibák esetén cserélje le a sikertelen példányok és folyamatos szolgáltatásokat a tartomány új példányokat kell indítani.
* **AD fiókzárolási:** felhasználói fiók zárolt 30 percig öt érvénytelen jelszavak használata 2 percen belül. Fiókok 30 perc után lesznek automatikusan feloldani.
* **Jól ismert felügyeleti eszközökkel:** jól ismert Windows Server Active Directory kezelőeszközöket, például az Active Directory felügyeleti központ vagy az Active Directory PowerShell segítségével a felügyelt tartomány felügyeletéhez.
