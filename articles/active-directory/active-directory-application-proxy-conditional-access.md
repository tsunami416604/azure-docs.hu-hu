---
title: "Feltételes hozzáférés – helyszíni alkalmazások – az Azure AD |} Microsoft Docs"
description: "Bemutatja, hogyan adhat a feltételes hozzáférés beállítása alkalmazások közzététele távolról az Azure AD-alkalmazásproxy használatával érhető el."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 2e97722b-eb4e-4078-b607-9fed210d8a0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.openlocfilehash: 463946256f9e335fa6d98fc904835e5c3dc2725e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="working-with-conditional-access-in-azure-ad-application-proxy"></a>Feltételes hozzáférés az Azure AD alkalmazásproxy használata

>[!NOTE]
>Ez a cikk vonatkozik, a klasszikus Azure portálra, amely a használatból van. Azt javasoljuk, hogy használja a [Azure-portálon](https://portal.azure.com). Az Azure-portálon az alkalmazásproxy alkalmazások rendelkezzenek bármely más SaaS-alkalmazás feltételes hozzáférési funkciót. Feltételes hozzáférés kapcsolatos további információkért lásd: [Ismerkedés a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal-get-started.md).

Konfigurálhatja a hozzáférési szabályok feltételes hozzáférést biztosít a Proxy használatát közzétett alkalmazásokról. Ezzel a következőket teheti:

* Alkalmazásonként többtényezős hitelesítést
* Többtényezős hitelesítés szükséges, csak akkor, ha a felhasználók nem rendelkeznek a munkahelyi hálózatban
* Az alkalmazás elérésének, amikor nincsenek munkahelyi felhasználók megakadályozása

Ezek a szabályok alkalmazhatók minden felhasználó és csoport vagy csak bizonyos felhasználók és csoportok. Alapértelmezés szerint a szabály az alkalmazáshoz hozzáféréssel rendelkező felhasználók vonatkozik. Azonban a szabály is korlátozhatja a felhasználók számára, hogy a megadott biztonsági csoportok tagjai.  

Hozzáférési szabályok van kiértékelve, amikor egy felhasználó egy összevont alkalmazás által használt OAuth 2.0, az OpenID Connect, a SAML-alapú vagy a WS-Federation fér hozzá. Ezenkívül hozzáférési szabályok értékelésének OAuth 2.0 és az OpenID Connect egy frissítési jogkivonat olyan hozzáférési jogkivonatot szerezni.

## <a name="conditional-access-prerequisites"></a>Feltételes hozzáférés Előfeltételek
* Prémium szintű Azure Active Directory-előfizetéssel
* Egy összevont vagy kezelt Azure Active Directory-bérlő
* Összevont bérlők szükséges többtényezős hitelesítés (MFA)  
    ![Hozzáférési szabályainak beállítása – többtényezős hitelesítést](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## <a name="configure-per-application-multi-factor-authentication"></a>Alkalmazásonként többtényezős hitelesítés beállítása
1. Jelentkezzen be a klasszikus Azure portálon rendszergazdaként.
2. Lépjen az Active Directory területre, majd válassza ki azt a címtárat, amelyen az alkalmazásproxyt engedélyezni kívánja.
3. Kattintson a **alkalmazások** és görgessen le a **hozzáférési szabályok** szakasz. A hozzáférési szabályok szakaszban csak akkor jelenik meg, az alkalmazások közzététele az alkalmazásproxy használatával összevont hitelesítést használó.
4. A szabály engedélyezéséhez jelölje ki **engedélyezése hozzáférési szabályok** való **a**.
5. Adja meg a felhasználókat és csoportokat, akinek a szabályok vonatkoznak. Használja a **csoport hozzáadása** gombra, majd egy vagy több csoportot, amelyhez a hozzáférési szabály vonatkozik. Ezen a párbeszédpanelen kijelölt csoportok törlése is használható.  Ha a szabályok csoportjaira alkalmazhatók van kijelölve, a hozzáférési szabályok érvényesítik csak a megadott biztonsági csoportok egyikéhez tartozó felhasználók számára.  

   * Explicit módon a szabályból kizárni a biztonsági csoportokat, ellenőrizze a következőket **kivéve** , és adja meg egy vagy több csoportjára. Az Except listában csoport tagsággal rendelkező felhasználók nem szükségesek-e többtényezős hitelesítés.  
   * Ha a felhasználó a felhasználói a multi-factor authentication szolgáltatás használatával lett konfigurálva, az elsőbbséget élvez az alkalmazás a multi-factor authentication szabályokat. A felhasználó, aki engedélyezve van a felhasználói többtényezős hitelesítés szükséges többtényezős hitelesítést végezni, akkor is, ha az az alkalmazás a multi-factor authentication-szabályok alól. További információ [többtényezős hitelesítés és a felhasználói beállítások](../multi-factor-authentication/multi-factor-authentication.md).
6. Válassza ki a beállítani kívánt hozzáférési szabály:

   * **Többtényezős hitelesítés megkövetelése**: a felhasználók számára, akihez a hozzáférési szabályok alkalmazása is teljes többtényezős hitelesítést, amelyre a szabály vonatkozik az alkalmazás elérése előtt.
   * **Ha nem munkahelyi többtényezős hitelesítést**: megpróbált hozzáférni az alkalmazáshoz egy megbízható IP-címről felhasználóknak a multi-factor authentication végrehajtása nem kötelező. A többtényezős hitelesítési beállítások lapon konfigurálhatja a megbízható IP-címtartományok.
   * **Letiltja a hozzáférést, ha nem munkahelyi**: megpróbált hozzáférni az alkalmazást a vállalati hálózaton kívüli felhasználók nem fognak tudni hozzáférni az alkalmazáshoz.

## <a name="configuring-mfa-for-federation-services"></a>Többtényezős hitelesítés konfigurálása összevonási szolgáltatások
Összevont bérlők, többtényezős hitelesítés (MFA) hajthatja végre az Azure Active Directory vagy a helyszíni AD FS-kiszolgálón. Többtényezős hitelesítés alapértelmezés szerint bármely Azure Active Directory által üzemeltetett lapján történik. Helyszíni MFA konfigurálásához futtassa a Windows Powershellt, és a – SupportsMFA tulajdonság adhatja meg az Azure AD-modullal.

A következő példa bemutatja, hogyan engedélyezése a helyi multi-factor Authentication használatával a [Set-MsolDomainFederationSettings parancsmag](https://msdn.microsoft.com/library/azure/dn194088.aspx) a contoso.com tenant:`Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

Ez a jelző mellett az összevont bérlői AD FS-példányt kell állítani a többtényezős hitelesítést végezni. Kövesse az utasításokat [központi telepítése a Microsoft Azure multi-factor Authentication hitelesítés a helyszíni](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="see-also"></a>Lásd még:
* [Munkavégzés jogcímeket figyelembe vevő alkalmazásokkal](active-directory-application-proxy-claims-aware-apps.md)
* [Alkalmazások közzététele az alkalmazásproxy](active-directory-application-proxy-publish.md)
* [Egyszeri bejelentkezés engedélyezése](active-directory-application-proxy-sso-using-kcd.md)
* [Alkalmazások közzététele saját tartománynév használatával](active-directory-application-proxy-custom-domains.md)

A legújabb híreket és frissítéseket itt találja: [Alkalmazásproxy blog](http://blogs.technet.com/b/applicationproxyblog/).
