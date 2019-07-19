---
title: 'Azure Active Directory Domain Services: Funkciók | Microsoft Docs'
description: A Azure Active Directory Domain Services szolgáltatásai
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: e0f38ed8367f076c9f9c4da77440bb3e9603d514
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234208"
---
# <a name="features-provided-by-azure-active-directory-domain-services"></a>A Azure Active Directory Domain Services által biztosított szolgáltatások

A következő szolgáltatások érhetők el Azure AD Domain Services felügyelt tartományokban.

* **Egyszerű üzembe helyezési élmény:** Csak néhány kattintással engedélyezheti az Azure AD-címtár Azure AD Domain Servicesét. A felügyelt tartomány magában foglalja a csak felhőalapú felhasználói fiókokat és a helyszíni címtárból szinkronizált felhasználói fiókokat.
* **Tartományhoz való csatlakozás támogatása:** Az Azure-beli virtuális hálózatban egyszerűen létrehozhat tartományhoz csatlakozó számítógépeket, amelyekben a felügyelt tartomány elérhető. A Windows ügyfél és kiszolgáló operációs rendszereinek tartományhoz való csatlakoztatása zökkenőmentesen működik a Azure AD Domain Services által kiszolgált tartományokon. Az ilyen tartományokhoz tartozó automatikus tartomány-csatlakoztatási eszközöket is használhatja.
* **Egy tartományi példány Azure AD-címtárban:** Mindegyik Azure AD-címtárhoz létrehozhat egyetlen Active Directory tartományt.
* **Egyéni névvel rendelkező tartományok létrehozása:** Azure AD Domain Services használatával létrehozhat olyan tartományokat, amelyek egyéni névvel rendelkeznek (például "contoso100.com"). Az ellenőrzött és a nem ellenőrzött tartományneveket egyaránt használhatja. Szükség esetén létrehozhat egy olyan tartományt is, amely az Azure AD-címtár által kínált beépített tartományi utótaggal ("*. onmicrosoft.com") rendelkezik.
* **Az Azure AD-vel integrált:** Nem kell konfigurálnia vagy kezelnie a replikálást Azure AD Domain Servicesra. A felhasználói fiókok, csoporttagság és felhasználói hitelesítő adatok (jelszavak) az Azure AD-címtárból automatikusan elérhetők a Azure AD Domain Services. Az Azure AD-bérlőből vagy a helyszíni címtárból származó attribútumok új felhasználóit, csoportjait vagy módosításait a rendszer automatikusan szinkronizálja Azure AD Domain Services.
* **NTLM-és Kerberos-hitelesítés:** Az NTLM-és Kerberos-hitelesítés támogatása lehetővé teszi a Windows-alapú hitelesítésre támaszkodó alkalmazások központi telepítését.
* **Vállalati hitelesítő adatok/jelszavak használata:** Az Azure AD-bérlőben lévő felhasználók jelszavai Azure AD Domain Serviceskal működnek. A felhasználók a vállalati hitelesítő adataikat a tartományhoz csatlakozó számítógépekhez használhatják, interaktív módon vagy távoli asztalon is bejelentkezhetnek, és hitelesíthetők a felügyelt tartományon.
* **LDAP-kötés & LDAP-olvasási támogatás:** Az LDAP-kötések használatára támaszkodó alkalmazások segítségével hitelesítheti a felhasználókat az Azure AD Domain Services által szervizelt tartományokban. Emellett az LDAP olvasási műveleteket használó alkalmazások a címtár felhasználó-vagy számítógép-attribútumainak lekérdezésére is használhatók Azure AD Domain Services.
* **Secure LDAP (LDAPs):** A címtárhoz való hozzáférést biztonságos LDAP (LDAPs) használatával engedélyezheti. A Secure LDAP hozzáférés alapértelmezés szerint a virtuális hálózaton belül elérhető. A biztonságos LDAP-hozzáférést azonban igény szerint is engedélyezheti az interneten keresztül.
* **Csoportházirend:** Egyetlen beépített csoportházirend-objektumot is használhat a felhasználók és számítógépek tárolók számára, hogy kikényszerítse a felhasználói fiókok és a tartományhoz csatlakoztatott számítógépek szükséges biztonsági házirendjeinek megfelelőségét. Létrehozhat saját egyéni csoportházirend-objektumokat is, és hozzárendelheti azokat egyéni szervezeti egységekhez a [csoportházirend kezeléséhez](manage-group-policy.md).
* **DNS kezelése:** Az "HRE DC rendszergazdák" csoport tagjai a felügyelt tartományhoz tartozó DNS-t kezelhetik az ismerős DNS-felügyeleti eszközök, például a DNS-felügyeleti MMC beépülő modul segítségével.
* **Egyéni szervezeti egységek (OU-EK) létrehozása:** Az "HRE DC rendszergazdák" csoport tagjai egyéni szervezeti egységeket hozhatnak létre a felügyelt tartományban. Ezek a felhasználók teljes körű rendszergazdai jogosultságokat kapnak az egyéni szervezeti egységeken keresztül, így ezek az egyéni szervezeti egységeken belül hozzáadhatók és eltávolíthatók a szolgáltatásfiókok, a számítógépek, a csoportok stb.
* **Számos Azure-beli globális régióban elérhető:** [Az Azure régiói](https://azure.microsoft.com/regions/#services/) lapon találja azoknak az Azure-régióknak a felsorolását, amelyekben elérhetők az Azure AD tartományi szolgáltatások.
* **Magas rendelkezésre állás:** A Azure AD Domain Services magas rendelkezésre állást biztosít a tartomány számára. Ez a funkció garantálja a magasabb szolgáltatási időt és a hibákhoz való rugalmasságot. A beépített állapotfigyelő szolgáltatás lehetővé teszi a hibák automatikus szervizelését azáltal, hogy az új példányok segítségével lecseréli a sikertelen példányokat, és további szolgáltatásokat biztosít a tartomány számára.
* **AD-fiók zárolásának védelme:** A felhasználói fiókok 30 percen belül le vannak zárva, ha öt érvénytelen jelszó van használatban 2 percen belül. A fiókok automatikus zárolása 30 perc után automatikusan megtörténik.
* **Ismerős felügyeleti eszközök használata:** A felügyelt tartományok felügyeletéhez használhat ismerős Windows Server Active Directory felügyeleti eszközöket, például a Active Directory felügyeleti központ vagy a Active Directory PowerShellt.
