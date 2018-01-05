---
title: "Egyszeri bejelentkezés kezelése az Azure AD-alkalmazásproxy |} Microsoft Docs"
description: "Az egyszeri bejelentkezés az alkalmazásproxy alapjainak megismerése"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 60422b4c8256df024bbc870fc43ec6b491f674f1
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="how-does-azure-ad-application-proxy-provide-single-sign-on"></a>Hogyan nyújt az Azure AD-alkalmazásproxy egyszeri bejelentkezéshez?

Egyszeri bejelentkezés az Azure AD alkalmazásproxy egyik fő eleme.  Mivel a felhasználók csak kell bejelentkezni az Azure Active Directory, a felhőben a legjobb felhasználói élményt biztosít. Az Azure Active Directory hitelesítéshez, ha az alkalmazásproxy-összekötő kezeli a hitelesítési a helyszíni alkalmazások. A háttéralkalmazás nem sikerült megállapítani, jelentkezzen be a Proxy és a normál használata egy tartományhoz csatlakoztatott eszközön keresztül egy távoli felhasználó közötti különbség. 

Azure Active Directoryval az egyszeri bejelentkezés az alkalmazások használatához kell kiválasztania **Azure Active Directory** előtti hitelesítési módszerként. Ha **csatlakoztatott** ezt követően a felhasználók nem hitelesítéséhez az Azure Active Directory összes, de a rendszer átirányítja rögtön az alkalmazást. E beállítás konfigurálása először közzétenni egy alkalmazást, vagy keresse meg az alkalmazás az Azure portálon és a alkalmazás proxybeállításokat. 

Az egyszeri bejelentkezésre vonatkozó beállításokat megtekintéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Navigáljon a **Azure Active Directory** > **vállalati alkalmazások** > **összes alkalmazás**.
3. Válassza ki az alkalmazást, amelynek egyszeri bejelentkezésre vonatkozó beállításokat szeretne kezelni.
4. Válassza ki **egyszeri bejelentkezés**.

   ![Egyszeri bejelentkezés legördülő menü](./media/application-proxy-sso-overview/single-sign-on-mode.png)

A legördülő menü az egyszeri bejelentkezés az alkalmazás öt lehetőségeket mutatja be:

* Azure AD-beli egyszeri bejelentkezés letiltva
* Jelszó alapú bejelentkezés
* Csatolt bejelentkezés
* Integrált Windows-hitelesítés
* Fejléc-alapú bejelentkezés

## <a name="azure-ad-single-sign-on-disabled"></a>Azure AD-beli egyszeri bejelentkezés letiltva

Ha nem szeretné használni az Azure Active Directory-integráció az egyszeri bejelentkezés az alkalmazáshoz, válassza a **az Azure AD az egyszeri bejelentkezés le van tiltva**. Ezt a lehetőséget választja a felhasználók kétszer hitelesíthetők. Először hitelesítéséhez az Azure Active Directoryhoz, és magának az alkalmazásnak, majd jelentkezzen be. 

Ez a beállítás akkor hasznos, ha a helyszíni alkalmazások nincs szükség a felhasználók hitelesítéséhez, de hozzá szeretne adni az Azure Active Directory, a távelérés biztonsági réteget. 

## <a name="password-based-sign-on"></a>Jelszó alapú bejelentkezés

Ha azt szeretné, az Azure Active Directory használatával és egy jelszó-tárolónak a helyszíni alkalmazások, **jelszóalapú bejelentkezés**. Ez a beállítás akkor hasznos, ha az alkalmazás végzi a hitelesítést egy felhasználónév/jelszó kombinált jogkivonatot, vagy a fejlécek helyett. A jelszó alapú bejelentkezés a felhasználóknak kell jelentkezzen be az alkalmazás az első alkalommal. Ezt követően Azure Active Directory választékát kínálja a felhasználónevet és jelszót a felhasználó nevében. 

Jelszó alapú bejelentkezés beállításával kapcsolatos információkért lásd: [az egyszeri bejelentkezés az alkalmazásproxy vaulting jelszó](application-proxy-sso-azure-portal.md).

## <a name="linked-sign-on"></a>Csatolt bejelentkezés

Ha már rendelkezik egyszeri bejelentkezéshez megoldás beállítása a helyszíni identitások, válassza a **bejelentkezés kapcsolódó**. Ez a beállítás lehetővé teszi az Azure Active Directory kihasználhatják a meglévő SSO megoldások, de továbbra is a felhasználók távoli hozzáférést biztosít az alkalmazást. 

Csatolt bejelentkezés (hivatalosan néven meglévő egyszeri bejelentkezés) kapcsolatos információkért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="integrated-windows-authentication"></a>Integrált Windows-hitelesítés

Ha a helyszíni alkalmazások integrált Windows-Authentication(IWA) használ, vagy ha azt szeretné, az egyszeri bejelentkezés, a Kerberos által korlátozott delegálás (KCD) használandó **integrált Windows-hitelesítés**. Ezzel a beállítással a felhasználók csak kell hitelesítéséhez az Azure Active Directoryhoz, és majd az alkalmazásproxy-összekötő megszemélyesít egy Kerberos-jogkivonata, és jelentkezzen be az alkalmazás a felhasználó. 

Integrált Windows-hitelesítés beállításával kapcsolatos információkért lásd: [Kerberos által korlátozott delegálás az egyszeri bejelentkezés az alkalmazásproxy](active-directory-application-proxy-sso-using-kcd.md).

## <a name="header-based-sign-on"></a>Fejléc-alapú bejelentkezés 

Ha az alkalmazások fejlécek használnak a hitelesítéshez, válassza a **fejléc-alapú bejelentkezés**. Ezzel a beállítással a felhasználók csak kell hitelesítés az Azure Active Directoryban. Microsoft-partnereknek, egy harmadik fél hitelesítési szolgáltatással PingAccess, amely az Azure Active Directory jogkivonat lefordítani a fejléc formátuma az alkalmazás neve. 

Fejléc-alapú hitelesítés beállításával kapcsolatos információkért lásd: [fejléc-alapú hitelesítés egyszeri bejelentkezéshez az alkalmazásproxy](application-proxy-ping-access.md).

## <a name="next-steps"></a>További lépések

- [Az egyszeri bejelentkezés az alkalmazásproxy vaulting jelszó](application-proxy-sso-azure-portal.md)
- [Kerberos által korlátozott delegálás az egyszeri bejelentkezés az alkalmazásproxy](active-directory-application-proxy-sso-using-kcd.md)
- [Az egyszeri bejelentkezés az alkalmazásproxy fejléc-alapú hitelesítés](application-proxy-ping-access.md) 
