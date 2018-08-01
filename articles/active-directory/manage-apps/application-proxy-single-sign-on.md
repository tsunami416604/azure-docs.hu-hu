---
title: Egyszeri bejelentkezés kezelése az Azure AD-alkalmazásproxyval |} A Microsoft Docs
description: Egyszeri bejelentkezés alkalmazásproxyval való alapjainak ismertetése
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: dbdbe8b83af20f66ad2cc99d2a5665262479b4a9
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364148"
---
# <a name="how-does-azure-ad-application-proxy-provide-single-sign-on"></a>Hogyan nyújt az Azure AD-alkalmazásproxy egyszeri bejelentkezést?

Egyszeri bejelentkezés az Azure AD Application Proxy egyik fő eleme.  Mivel a felhasználók csak az Azure Active Directoryra a felhőbeli bejelentkezéshez a legjobb felhasználói élményt biztosít. Miután az Azure Active Directory hitelesítést végezni, az Application Proxy connector kezeli a helyszíni alkalmazás a hitelesítési. A háttéralkalmazás nem állapítható meg, hogy jelentkezzen be a Proxy és a egy normál használata egy tartományhoz csatlakoztatott eszközön keresztül egy távoli felhasználó közötti különbség. 

Azure Active Directory használata egyszeri bejelentkezéshez az alkalmazások számára, jelölje ki kell **Azure Active Directory** előtti hitelesítési módszerként. Ha **csatlakoztatott** , majd a felhasználók esetén az összes Azure Active Directory hitelesítést nem, de a rendszer átirányítja közvetlenül az alkalmazás. Ezzel a beállítással konfigurálhatja, amikor először közzétenni egy alkalmazást, vagy keresse meg az alkalmazás az Azure Portalon és az alkalmazásproxy beállításainak szerkesztése. 

Az egyszeri bejelentkezési lehetőségek megtekintéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Navigáljon a **Azure Active Directory** > **vállalati alkalmazások** > **minden alkalmazás**.
3. Válassza ki az alkalmazást, amelynek egyszeri bejelentkezésre vonatkozó beállításokat szeretné kezelni.
4. Válassza ki **egyszeri bejelentkezési**.

   ![Egyszeri bejelentkezés legördülő menü](./media/application-proxy-single-sign-on/single-sign-on-mode.png)

A legördülő menüből az egyszeri bejelentkezés az alkalmazás öt beállításai láthatók:

* Azure AD-beli egyszeri bejelentkezés letiltva
* Jelszóalapú bejelentkezés
* Csatolt bejelentkezés
* Integrált Windows-hitelesítés
* Fejlécalapú bejelentkezés

## <a name="azure-ad-single-sign-on-disabled"></a>Azure AD-beli egyszeri bejelentkezés letiltva

Ha nem szeretné használni az Azure Active Directory-integráció az egyszeri bejelentkezés az alkalmazáshoz, válassza a **az Azure AD egyszeri bejelentkezés le van tiltva**. Ezt a lehetőséget választja a felhasználók kétszer hitelesíthetők. Először az Azure Active Directory hitelesítést, és magát az alkalmazást, majd jelentkezzen be. 

Ez a beállítás akkor hasznos, ha a helyszíni alkalmazás nincs szükség a felhasználók számára a hitelesítést, de szeretne hozzáadni az Azure Active Directory, a távelérés biztonsági réteget. 

## <a name="password-based-sign-on"></a>Jelszóalapú bejelentkezés

Ha az Azure Active Directory a helyszíni alkalmazások és a jelszó-tárolónak használni szeretne, válassza ki a **jelszóalapú bejelentkezés**. Ez a beállítás akkor hasznos, ha az alkalmazás végzi a hitelesítést egy felhasználónév/jelszó kombinált jogkivonatot, vagy a fejlécek helyett. Jelszóalapú bejelentkezés, a felhasználóknak kell jelentkezzen be az alkalmazás az első alkalommal. Ezt követően az Azure Active Directory szolgáltat, amelyekkel a felhasználónevet és jelszót a felhasználó nevében. 

Jelszóalapú bejelentkezés beállításával kapcsolatos további információkért lásd: [jelszó az egyszeri bejelentkezés alkalmazásproxyval való vaulting](application-proxy-configure-single-sign-on-password-vaulting.md).

## <a name="linked-sign-on"></a>Csatolt bejelentkezés

Ha már rendelkezik egyszeri bejelentkezési megoldás beállítása a helyszíni identitások, válassza a **csatolt bejelentkezés**. Ez a beállítás lehetővé teszi az Azure Active Directory kihasználhatja meglévő SSO-megoldások, de továbbra is a felhasználók távoli hozzáférést biztosít az alkalmazásnak. 

Csatolt bejelentkezés (korábbi nevén meglévő egyszeri bejelentkezés) kapcsolatos információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="integrated-windows-authentication"></a>Integrált Windows-hitelesítés

Ha a helyszíni alkalmazások integrált Windows-Authentication(IWA) használ, vagy ha azt szeretné, a Kerberos által korlátozott delegálás (KCD) használata egyszeri bejelentkezéshez, válassza ki a **integrált Windows-hitelesítés**. Ezzel a beállítással a felhasználók csak az Azure Active Directory hitelesítést kell, és ezután a az Application Proxy connector megszemélyesíti a felhasználó számára egy Kerberos-token beszerzése és jelentkezzen be az alkalmazást. 

Integrált Windows-hitelesítés beállításával kapcsolatos további információkért lásd: [Kerberos által korlátozott delegálás az egyszeri bejelentkezés alkalmazásproxyval való](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="header-based-sign-on"></a>Fejlécalapú bejelentkezés 

Ha az alkalmazások hitelesítési fejléceket használni, válassza a **fejlécalapú bejelentkezés**. Ezzel a beállítással a felhasználók csak kell hitelesítést az Azure Active Directoryban. Microsoft-partnerek egy harmadik fél hitelesítési szolgáltatással nevű PingAccess, amely az Azure Active Directory-jogkivonat fordítja az alkalmazás a fejléc formátuma. 

Fejlécalapú hitelesítéskor beállításával kapcsolatos további információkért lásd: [fejlécalapú hitelesítéskor az egyszeri bejelentkezés alkalmazásproxyval való](application-proxy-configure-single-sign-on-with-ping-access.md).

## <a name="next-steps"></a>További lépések

- [Az egyszeri bejelentkezés alkalmazásproxyval való vaulting jelszó](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Kerberos által korlátozott delegálás az egyszeri bejelentkezést az alkalmazásproxy használatával](application-proxy-configure-single-sign-on-with-kcd.md)
- [Fejlécalapú hitelesítéskor az egyszeri bejelentkezést az alkalmazásproxy használatával](application-proxy-configure-single-sign-on-with-ping-access.md) 
