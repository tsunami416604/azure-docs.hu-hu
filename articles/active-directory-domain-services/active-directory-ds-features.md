---
title: 'Az Azure Active Directory tartományi szolgáltatások: Szolgáltatások |} Microsoft Docs'
description: Az Azure Active Directory tartományi szolgáltatások jellemzői
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: maheshu
ms.openlocfilehash: 5dd3cde69c6aa36c3d9cb3060dc6deb59ff74a5a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214967"
---
# <a name="azure-ad-domain-services"></a>Azure AD Domain Services
## <a name="features"></a>Szolgáltatások
Az alábbi szolgáltatások az Azure AD tartományi szolgáltatások által felügyelt tartományok érhetők el.

* **Egyszerű üzembe helyezését:** Azure AD tartományi szolgáltatásokat az Azure AD-címtár néhány kattintással használatával engedélyezheti. A felügyelt tartományok csak felhőalapú felhasználói fiókokhoz és egy helyszíni címtárral szinkronizálja a felhasználói fiókokat tartalmazza.
* **Tartományhoz való csatlakozást támogatása:** egyszerűen az Azure virtuális hálózat, a felügyelt tartományok érhető el a számítógépek tartományhoz való csatlakozást. A tartományhoz való csatlakozást élmény a Windows ügyfél és kiszolgáló operációs rendszerek works zökkenőmentesen Azure AD tartományi szolgáltatások által kiszolgált tartományok ellen. Használhatja az automatikus tartományhoz való csatlakozást tooling eszköz ilyen tartományok ellen is.
* **Egy tartomány példány lehet az Azure AD-címtár:** egy egyetlen Active Directory-tartomány minden Azure AD-címtár is létrehozhat.
* **Hozzon létre egyéni nevek tartományok:** hozhat létre Azure AD tartományi szolgáltatásokat használó tartományok egyéni nevű (például "contoso100.com"). Használhatja bármelyik ellenőrzött és érvényes vagy nem ellenőrzött tartomány nevét. Szükség esetén is létrehozhat egy tartományt a beépített tartományi utótaggal rendelkező (Ez azt jelenti, hogy "*. onmicrosoft.com") az Azure AD-címtár által kínált.
* **Az Azure ad-vel integrált:** nem kell konfigurálniuk vagy az Azure AD tartományi szolgáltatásokra replikáció kezelésére. Felhasználói fiókok, a csoporttagságot és a felhasználói hitelesítő adatokat (jelszó) az Azure AD-címtár automatikusan elérhetők az Azure AD tartományi szolgáltatásokban. Új felhasználók, csoportok vagy az attribútumok változásait a Azure AD-bérlőn vagy a helyszíni címtár automatikusan szinkronizálódnak az Azure AD tartományi szolgáltatásokra.
* **Az NTLM és Kerberos hitelesítés:** NTLM és Kerberos hitelesítés támogatása, a Windows-Integrated hitelesítést használó alkalmazások telepítése.
* **A vállalati hitelesítő adatokat vagy jelszavakat:** jelszavak felhasználók az Azure ad bérlői az Azure AD tartományi szolgáltatások használata. Felhasználók tartományhoz való csatlakozást gépekre vállalati hitelesítő adataikkal, jelentkezzen be, interaktív vagy a távoli asztali kapcsolaton keresztül, és hitelesítést, a felügyelt tartományra.
* **LDAP-kötés & LDAP olvassa el a támogatási szolgálathoz:** használható alkalmazások LDAP kötések Azure AD tartományi szolgáltatások által kiszolgált tartományokban lévő felhasználók hitelesítésére. Emellett használó alkalmazások LDAP olvasási műveletek lekérdezés felhasználó/számítógép attribútumait a könyvtárból is is dolgozhat Azure AD tartományi szolgáltatásokat.
* **Biztonságos LDAP (LDAPS):** engedélyezheti a directory-elérés keresztül biztonságos LDAP (LDAPS). Biztonságos LDAP hozzáférést alapértelmezés szerint a virtuális hálózaton belül érhető el. Az interneten keresztül azonban biztonságos LDAP hozzáférést is engedélyezheti.
* **Csoportházirend:** egyetlen beépített csoportházirend-objektum minden egyes használ a felhasználók és számítógépek tárolók kényszerítse a megfelelőséget szükséges biztonsági házirendek felhasználói fiókokhoz és a tartományhoz csatlakoztatott számítógépeken. Is a saját egyéni csoportházirend-objektumok létrehozása, és rendeljen hozzájuk egyéni szervezeti egységek [csoportházirendet](active-directory-ds-admin-guide-administer-group-policy.md).
* **DNS kezelése:** a "AAD DC rendszergazdák" csoportba DNS kezelheti a felügyelt tartományok a jól ismert DNS-felügyeleti eszközök például a DNS felügyeleti beépülő MMC-modulban.
* **Hozzon létre egyéni szervezeti egységekhez (OU-k):** a "AAD DC rendszergazdák" csoportba a felügyelt tartomány hozhat létre egyéni szervezeti egységekhez. Ezek a felhasználók teljes körű rendszergazdai jogosultságokkal kapnak egyéni szervezeti egységek, keresztül, így azok hozzáadására és eltávolítására szolgáltatásfiókok, számítógépek, csoportok stb. ezeket egyéni szervezeti egységek belül.
* **Sok Azure globális régiókban elérhető:** tekintse meg a [Azure-szolgáltatások régiónként](https://azure.microsoft.com/regions/#services/) lapon találja azoknak az Azure-régiókban, amelyben az Azure AD tartományi szolgáltatások érhető el.
* **Magas rendelkezésre állású:** Azure AD tartományi szolgáltatások kínál a magas rendelkezésre állást a tartományra. Ez a funkció magasabb hasznos üzemidő és rugalmasság hibákra vonatkozó kínál. Beépített állapotfigyelési ajánlatokat a sikertelen szervizelés alatt automatizálható, ha új példányok sikertelen példányt használ, és folyamatos kiszolgálása a tartomány fel forgó.
* **AD-fiókot zárolási védelem:** felhasználói fiókok zárolt 30 percig öt érvénytelen jelszavak használata 2 percen belül. Fiókok 30 másodperc után automatikusan fel lesznek.
* **Használja az tisztában kezelőeszközöket:** jól ismert Windows Server Active Directory felügyeleti eszközökkel, például az Active Directory felügyeleti központ vagy az Active Directory PowerShell segítségével felügyelheti a felügyelt tartományok.
