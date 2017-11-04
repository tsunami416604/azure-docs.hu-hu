---
title: "IOS rendszerű eszközökön az Azure Active Directory-alapú hitelesítés |} Microsoft Docs"
description: "További tudnivalók a támogatott forgatókönyveket és a megoldások konfigurálását tanúsítvány alapú hitelesítést követelményei az iOS-eszközök"
services: active-directory
author: MarkusVi
documentationcenter: na
manager: femila
ms.assetid: 26a6fc54-0153-44fb-b970-9b432c99e9f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 8e9610fee635ec24f8f7eaeba0c6a19a421c0456
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>IOS rendszerű eszközökön az Azure Active Directory-alapú hitelesítés

Tanúsítvány alapú hitelesítést (CBA) lehetővé teszi, hogy hitelesítse Azure Active Directory ügyféltanúsítvánnyal egy Windows-, Android vagy iOS eszközön az Exchange online fiókot való csatlakozáskor:

* Office-mobilalkalmazások például a Microsoft Outlook és a Microsoft Word   
* Exchange ActiveSync (EAS) ügyfeleket

Ez a funkció beállítása szükségtelenné teszi adjon meg egy felhasználónév és jelszó kombinációjával egyes mail és a Microsoft Office-alkalmazásokat a mobileszközön.

Ez a témakör a követelményeket és a támogatott forgatókönyveket CBA konfigurálásához a felhasználók számára a bérlő az Office 365 Enterprise, vállalati, oktatási, USA szövetségi kormányzatának, Kína iOS(Android)-eszközön, és Németország tervez.

Ez a funkció érhető el az előzetes verzió az Office 365 US Government védelmet és szövetségi csomagokban.




## <a name="microsoft-mobile-applications-support"></a>A Microsoft mobilalkalmazás-támogatás

| Alkalmazások | Támogatás |
| --- | --- |
| Azure Information Protection alkalmazás |![Jelölőnégyzet][1] |
| Intune vállalati portál |![Jelölőnégyzet][1] |
| Microsoft Teams |![Jelölőnégyzet][1] |
| OneNote |![Jelölőnégyzet][1] |
| OneDrive |![Jelölőnégyzet][1] |
| Outlook |![Jelölőnégyzet][1] |
| Power BI |![Jelölőnégyzet][1] |
| A Skype vállalati verzió |![Jelölőnégyzet][1] |
| Word / Excel / PowerPoint |![Jelölőnégyzet][1] |
| Yammer |![Jelölőnégyzet][1] |


## <a name="requirements"></a>Követelmények

Az eszköz operációs rendszere verziójúnak kell lennie az iOS 9-es vagy újabb verzió

Az összevonási kiszolgáló be kell állítani.  

A Microsoft Authenticator Office-alkalmazások IOS szükség.  

Az Azure Active Directory ügyféltanúsítvány visszavonni az AD FS jogkivonat a következő jogcímeket kell rendelkeznie:  

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
  (A sorozatszámát az ügyféltanúsítvány)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
  (Az ügyfél-tanúsítvány kiállítója karakterlánc)

Az Azure Active Directory ezeket a jogcímeket, hogy a frissítési jogkivonat ad hozzá, ha azok elérhetők az AD FS jogkivonat (vagy bármely más SAML-jogkivonat). Ha a frissítési token érvényesíthető, ezek az információk segítségével ellenőrizze a visszavont tanúsítványok.

Ajánlott eljárásként a az AD FS hibalapok frissítenie kell a következőkkel:

* IOS rendszerű eszközökön a Microsoft Authenticator telepítésével kapcsolatos követelmények
* Útmutatás felhasználói tanúsítvány beszerzésére.

További részletekért lásd: [az AD FS bejelentkezési oldalainak testreszabása](https://technet.microsoft.com/library/dn280950.aspx).

Néhány Office-alkalmazások (a modern hitelesítést) küldése "*kérdezzen rá a bejelentkezési =*" az Azure AD a kérelemben. Alapértelmezés szerint az Azure AD fordítja le a az AD FS a kérésben '*wauth = usernamepassworduri*"(kéri U/P hitelesítés elvégzéséhez AD FS) és"*wfresh = 0*"(kéri az AD FS figyelmen kívül az egyszeri bejelentkezési állapotát, és egy friss hitelesítést). Ha szeretné engedélyezni a tanúsítvány alapú hitelesítést ezekhez az alkalmazásokhoz, módosítania az Azure AD alapértelmezés. Állítson be a "*PromptLoginBehavior*"az összevont tartományt beállításait úgy, hogy"*letiltott*".
Használhatja a [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) parancsmag, ez a feladat végrehajtásához:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`


## <a name="exchange-activesync-clients-support"></a>Exchange ActiveSync-ügyfelek támogatását.
IOS 9-es vagy újabb a natív iOS e-mail ügyfél támogatott. Minden Exchange ActiveSync alkalmazások annak meghatározásához, ha ezt támogatja, lépjen kapcsolatba az alkalmazás fejlesztőjének.  


## <a name="next-steps"></a>Következő lépések

Ha Tanúsítványalapú hitelesítés konfigurálása a környezetben, tekintse meg a [első lépések az Android-alapú hitelesítés](active-directory-certificate-based-authentication-get-started.md) utasításokat.


<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
