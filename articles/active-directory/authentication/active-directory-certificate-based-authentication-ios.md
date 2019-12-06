---
title: Tanúsítványalapú hitelesítés iOS rendszeren – Azure Active Directory
description: Ismerje meg a támogatott forgatókönyveket és a tanúsítványalapú hitelesítés konfigurálásának követelményeit iOS-eszközökkel rendelkező megoldásokban
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2f9e7d71ab660c4df6f65d6bebe1d3854086bdd
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848799"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Tanúsítvány alapú hitelesítés Azure Active Directory iOS rendszeren

az iOS-eszközök tanúsítványalapú hitelesítést (CBA) használhatnak a Azure Active Directory hitelesítéséhez az eszközön lévő ügyféltanúsítványt használva, amikor csatlakoznak a következőhöz:

* Office Mobile-alkalmazások, például a Microsoft Outlook és a Microsoft Word
* Exchange ActiveSync-(EAS-) ügyfelek

A szolgáltatás konfigurálásával nem kell megadnia a Felhasználónév és a jelszó kombinációját bizonyos levelezési és Microsoft Office alkalmazásokban a mobileszközön.

Ez a témakör az Office 365 Enterprise, Business, Education, USA kormánya, Kína és Németország területén található bérlők felhasználói számára készült iOS-es (Android-) eszközökön történő konfigurálásával kapcsolatos követelményeket és támogatott forgatókönyveket ismerteti.

Ez a funkció előzetes verzióban érhető el az Office 365 US government Defense és Federal tervekben.

## <a name="microsoft-mobile-applications-support"></a>Microsoft Mobile-alkalmazások támogatása

| Alkalmazások | Támogatás |
| --- | --- |
| Azure Information Protection alkalmazás |![Pipa jelzi az alkalmazás támogatását][1] |
| Intune Vállalati portál |![Pipa jelzi az alkalmazás támogatását][1] |
| Microsoft Teams |![Pipa jelzi az alkalmazás támogatását][1] |
| OneNote |![Pipa jelzi az alkalmazás támogatását][1] |
| OneDrive |![Pipa jelzi az alkalmazás támogatását][1] |
| Outlook |![Pipa jelzi az alkalmazás támogatását][1] |
| Power BI |![Pipa jelzi az alkalmazás támogatását][1] |
| Skype Vállalati verzió |![Pipa jelzi az alkalmazás támogatását][1] |
| Word/Excel/PowerPoint |![Pipa jelzi az alkalmazás támogatását][1] |
| Yammer |![Pipa jelzi az alkalmazás támogatását][1] |

## <a name="requirements"></a>Követelmények

Az eszköz operációsrendszer-verziójának iOS 9-es vagy újabb verziójúnak kell lennie

Konfigurálni kell egy összevonási kiszolgálót.

Az iOS-es Office-alkalmazásokhoz Microsoft Authenticator szükséges.

Az ügyféltanúsítványok visszavonásához az ADFS-tokennek a következő jogcímeket kell tartalmaznia Azure Active Directory:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (az ügyféltanúsítvány sorozatszáma)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (az ügyféltanúsítvány kiállítójának karakterlánca)

Azure Active Directory hozzáadja ezeket a jogcímeket a frissítési jogkivonathoz, ha az ADFS-tokenben (vagy bármely más SAML-jogkivonatban) elérhetők. Ha a frissítési tokent ellenőrizni kell, a rendszer ezt az információt használja a visszavonás ellenőrzéséhez.

Ajánlott eljárásként frissítenie kell a szervezet ADFS-hibájának lapjait a következő információkkal:

* A Microsoft Authenticator iOS rendszerre való telepítésének követelménye
* Útmutató felhasználói tanúsítvány beszerzéséhez.

További információ: [AD FS bejelentkezési lapok testreszabása](https://technet.microsoft.com/library/dn280950.aspx).

Egyes Office-alkalmazások (amelyeken engedélyezve van a modern hitelesítés) a kérésben a "*prompt = login*" üzenetet küldik az Azure ad-nek. Alapértelmezés szerint az Azure AD az ADFS-kérelemben*az "* *wauth = usernamepassworduri*" (az U/P hitelesítésének megkövetelése) és a "*wfresh = 0*" (az ADFS-t az SSO-állapot mellőzése és a friss hitelesítés elvégzése érdekében kéri). Ha engedélyezni szeretné a tanúsítványalapú hitelesítést ezekhez az alkalmazásokhoz, módosítania kell az Azure AD alapértelmezett viselkedését. Az összevont tartomány beállításainál állítsa a "*PromptLoginBehavior*" beállítást a "*Letiltva*" értékre.
A feladat végrehajtásához a [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) parancsmagot használhatja:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Exchange ActiveSync-ügyfelek támogatása

Az iOS 9-es vagy újabb verzióiban a natív iOS levelezési ügyfélprogram támogatott. Az összes többi Exchange ActiveSync-alkalmazás esetében annak megállapításához, hogy ez a funkció támogatott-e, forduljon az alkalmazás-fejlesztőhöz.

## <a name="next-steps"></a>Következő lépések

Ha tanúsítványalapú hitelesítést szeretne konfigurálni a környezetében, olvassa el a következő témakört: a [tanúsítványalapú hitelesítés első lépései Androidon](../authentication/active-directory-certificate-based-authentication-get-started.md) .

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
