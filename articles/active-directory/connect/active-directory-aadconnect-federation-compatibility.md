---
title: "Az Azure AD összevonás kompatibilitási listája"
description: "Ezen a lapon nem a Microsofttól Identitásszolgáltatók, amelyek segítségével az egyszeri bejelentkezés megvalósítása van."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: bce5867017647764546d872d97943d5d4f01f2d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-federation-compatibility-list"></a>Az Azure AD összevonás kompatibilitási listája
Az Azure Active Directory egyszeri bejelentkezést biztosít, és a bővített alkalmazásbiztonsági hozzáférést az Office 365 és más Microsoft Online services az hibrid és a csak felhőalapú hitelesítés megvalósításához anélkül, hogy a nem Microsoft-megoldás. Office 365, például a Microsoft Online services, a legtöbb a directory services, hitelesítéshez és engedélyezéshez integrálva van az Azure Active Directoryban. Egyszeri bejelentkezés az SaaS-alkalmazásokhoz ezer is biztosít az Azure Active Directory és a helyszíni webalkalmazások. Tekintse meg az Azure Active Directory alkalmazáskatalógusában az támogatott SaaS-alkalmazásokhoz.

Olyan szervezeteknek, amelyek befektettek nem Microsoft-összevonási megoldások Ez a témakör útmutató a Microsoft Online services konfigurálása egyszeri bejelentkezés a Windows Server Active Directory-felhasználók nem a Microsofttól Identitásszolgáltatók az alábbi "az Azure Active Directory összevonási kompatibilitási listán" használatával. 

![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
[Számítógép (csoport) Oxford](http://oxfordcomputergroup.com/), egy harmadik fél nevében, a Microsoft tesztelte ezek egyszeri bejelentkezést az Azure Active Directoryban nem a Microsofttól Identitásszolgáltatók összehasonlítja a használati esetek közös használatával.

Hogyan férhetnek a az itt felsorolt külső identitásszolgáltatótól információkért forduljon Oxford a számítógép csoport [ idp@oxfordcomputergroup.com ](mailto:idp@oxfordcomputergroup.com).

> [!IMPORTANT]
> Számítógép (csoport) Oxford csak egyetlen bejelentkezés forgatókönyvekben összevonási funkcióinak tesztelve. Oxford számítógépcsoport nem hajtott végre semmilyen tesztelése a szinkronizálás, a kéttényezős hitelesítést, az egyszeri bejelentkezés forgatókönyvekben összetevői stb.
> 
> Bejelentkezés által UPN a másik azonosító használata még nem lett tesztelve az ezt a programot.
> 
> 

* [Azure Active Directory](#azure-active-directory)
* [4.5 AuthAnvil egyszeri bejelentkezés](#authanvil-single-sign-on-45)
* [BIG-IP-cím hozzáférés-kezelési házirend BIG-IP ver. 11.3 x – 11, 6 x](#big-ip-with-access-policy-manager-big-ip-ver-113x--116x) 
* [BitGlass](#bitglass)
* [CA biztonságos felhőalapú](#ca-secure-cloud) 
* [Hitelesítésszolgáltató SiteMinder 12.52](#ca-siteminder-1252-sp1-cumulative-release-4) 
* [Centrify](#centrify) 
* [Dell egy felhőalapú hozzáférés identitáskezelő v7.1](#dell-one-identity-cloud-access-manager-v71) 
* [DigitalPersona összetett hitelesítés](#digitalpersona-composite-authentication)
* [IBM Tivoli összevont 6.2.2 Identity Manager](#ibm-tivoli-federated-identity-manager-622) 
* [IceWall összevonási 3.0-s verzió](#icewall-federation-version-30) 
* [Memority](#memority)
* [Hozzáférés-kezelési NetIQ 4.x](#netiq-access-manager-4x) 
* [Okta](#okta) 
* [OneLogin](#onelogin) 
* [Optimális IDM virtuális identitás Server összevonási szolgáltatások](#optimal-idm-virtual-identity-server-federation-services) 
* [7.2, 6.11, PingFederate 8.x](#pingfederate-611-72-8x)
* [RadiantOne CFS 3.0](#radiantone-cfs-30) 
* [Sailpoint IdentityNow](#sailpoint-identitynow)
* [SecureAuth IdP 7.2.0](#secureauth-idp-720) 
* [Aláírás, és nyissa meg 5.3-as](#signgo-53) 
* [SoftBank technológia Online szolgáltatás kapu](#softbank)
* [Egy VMware-munkaterület](#vmware-workspace-one)



> [!IMPORTANT]
> Mivel ezek a harmadik féltől származó termékekre, a Microsoft nem támogatja a központi telepítési, konfigurációs, hibaelhárítási, ajánlott eljárásokat, stb. kapcsolatos problémák és ezek identitás-szolgáltatóktól kérdéseket. Támogatás, illetve ezek identitás-szolgáltatóktól kérdéseket lépjen kapcsolatba a támogatott harmadik felek közvetlenül.
> 
> A harmadik fél Identitásszolgáltatók teszteltük együttműködését a Microsoft felhőszolgáltatásaival WS-Federation és csak a WS-Trust protokollok használatával. Tesztelés nem tartalmazza az SAML protokoll használatával.
> 


## <a name="azure-active-directory"></a>Azure Active Directory

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrix a bejelentkezési élmény: 

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |None |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |None |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |
| Például az Office 2016 adal-t használó modern alkalmazások |Támogatott |None |

Az AD FS az Azure Active Directory használatáról további információk: [Active Directory összevonási szolgáltatások (ADFS)](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs).

További információ az Azure Active Directory használatáról és a jelszó-szinkronizálás: [az Azure AD Connect](active-directory-aadconnect.md).

## <a name="authanvil-single-sign-on-45"></a>4.5 AuthAnvil egyszeri bejelentkezés

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az:

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |Integrált Windows-hitelesítés nem támogatott. |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |Integrált Windows-hitelesítés nem támogatott. |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

További információkért lásd: [AuthAnvil egyszeri bejelentkezés.](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-).


## <a name="big-ip-with-access-policy-manager-big-ip-ver-113x--116x"></a>BIG-IP-cím hozzáférés-kezelési házirend BIG-IP ver. 11.3 x – 11, 6 x

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az: 

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |None |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Nem támogatott |Nem támogatott |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

BIG-IP-hozzáférési házirend Managerrel kapcsolatos további információkért lásd: [BIG-IP-hozzáférési házirend Manager.](https://f5.com/products/modules/access-policy-manager) 

Ennek beállításához a BIG-IP-hozzáférés házirend-kezelési útmutatást STS az egyszeri bejelentkezéses felhasználói élmény biztosítása az Active Directory-felhasználók számára a pdf letöltéséhez [BIG-IP](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf).

## <a name="bitglass"></a>BitGlass

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az:

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |Integrált Windows-hitelesítés nem támogatott. |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |Integrált Windows-hitelesítés nem támogatott. |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

További információ a BitGlass: [BitGlass](http://www.bitglass.com).

## <a name="ca-secure-cloud"></a>CA biztonságos felhőalapú

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az:

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |Integrált Windows-hitelesítés nem támogatott. |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |Integrált Windows-hitelesítés nem támogatott. |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

A CA biztonságos felhő kapcsolatos további információkért lásd: [hitelesítésszolgáltató Secure Cloud](http://www.ca.com/us/products/security-as-a-service.aspx).

## <a name="ca-siteminder-1252-sp1-cumulative-release-4"></a>Hitelesítésszolgáltató SiteMinder 12.52 SP1 összesített kiadás 4

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az: 

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |None |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |None |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

Hitelesítésszolgáltató SiteMinder kapcsolatos további információkért lásd: [hitelesítésszolgáltató SiteMinder összevonási](http://www.ca.com/us/products/ca-single-sign-on.html). 

## <a name="centrify"></a>Centrify

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az:

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |None |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |None |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |Ügyfél hozzáférés-vezérlés nem támogatott. |

Centrify kapcsolatos további információkért lásd: [Centrify](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp).

## <a name="dell-one-identity-cloud-access-manager-v71"></a>Dell egy felhőalapú hozzáférés identitáskezelő v7.1

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az:

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |None |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |None |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

Kapcsolatos Dell több identitás felhő hozzáférés-kezelési további információkért lásd: [Dell több identitás felhő hozzáférés-kezelési](http://software.dell.com/products/cloud-access-manager).

 A konfigurálása a STS az egyszeri bejelentkezéses felhasználói élmény biztosítása az Office 365-felhasználók számára, lásd: [Office 365-felhasználók konfigurálása](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365). 

## <a name="digitalpersona-composite-authentication"></a>DigitalPersona összetett hitelesítés  

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az:

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |Integrált Windows-hitelesítés nem támogatott.|
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |Integrált Windows-hitelesítés nem támogatott.|
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

További információ: [DigitalPersona összetett hitelesítés](http://www.crossmatch.com/uploadedFiles/Support/Reference_Material/DigitalPersona-Office-365-Deployment-Guide.pdf).


## <a name="ibm-tivoli-federated-identity-manager-622"></a>IBM Tivoli összevont 6.2.2 Identity Manager

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az: 

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |None |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |None |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

IBM Tivoli összevont identitás Managerrel kapcsolatban további információkért lásd: [IBM biztonsági hozzáférés-kezelési Microsoft Applications](http://www-01.ibm.com/support/docview.wss?uid=swg24029517).

## <a name="icewall-federation-version-30"></a>IceWall összevonási 3.0-s verzió

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az:

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |Integrált Windows-hitelesítés nem támogatott. |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |Integrált Windows-hitelesítés nem támogatott. |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

IceWall összevonási kapcsolatos további információkért lásd: [IceWall összevonási Version 3.0](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/) és [IceWall összevonási és az Office 365](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html).

## <a name="memority"></a>Memority

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrix a bejelentkezési élmény: 

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |None |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |None |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

Memority használatával kapcsolatos további információk: [Memority](http://www.memority.com).


## <a name="netiq-access-manager-4x"></a>Hozzáférés-kezelési NetIQ 4.x

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az:

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |None|
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |None|
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

További információkért lásd: [NetIQ hozzáférés-kezelési](https://www.netiq.com/documentation/access-manager-43/admin/data/b65ogn0.html#b12iqp0m).

## <a name="okta"></a>Okta

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az: 

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |Integrált Windows-hitelesítés szükséges a további webalkalmazás-kiszolgáló és a Okta alkalmazás beállításait. |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |Integrált Windows-hitelesítés |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

Az Okta kapcsolatos további információkért lásd: [Okta](https://www.okta.com/).

## <a name="onelogin"></a>OneLogin

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az: 

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |Integrált Windows-hitelesítés |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |Integrált Windows-hitelesítés |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

OneLogin kapcsolatos további információkért lásd: [OneLogin](https://www.onelogin.com/).

## <a name="optimal-idm-virtual-identity-server-federation-services"></a>Optimális IDM virtuális identitás Server összevonási szolgáltatások

A következő, a forgatókönyv támogatja a mátrix az egyszeri bejelentkezéses élményt:

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |None |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |Integrált Windows-hitelesítés |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |

Az ügyfél-hozzáférési kapcsolatos információkért lásd: szabályzatok [Office 365 szolgáltatások helye alapján az ügyfél számára a hozzáférés korlátozása](https://technet.microsoft.com/library/hh526961.aspx).





## <a name="pingfederate-611-72-8x"></a>7.2, 6.11, PingFederate 8.x

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az:

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |None |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |None |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

A PingFederate az STS konfigurálása az egyszeri bejelentkezéses élményt biztosíthat az Active Directory-felhasználók számára, lásd: a következők egyikét: 

- [PingFederate 6.11](http://go.microsoft.com/fwlink/?LinkID=266321)
- [PingFederate 7.2](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)
- [PingFederate 8.x](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## <a name="radiantone-cfs-30"></a>RadiantOne CFS 3.0

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az: 

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |None |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |Integrált Windows-hitelesítés |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

RadiantOne CFS kapcsolatos további információkért lásd: [RadiantOne CFS](http://www.radiantlogic.com/products/radiantone-cfs/).

## <a name="sailpoint-identitynow"></a>Sailpoint IdentityNow

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az:

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |Integrált Windows-hitelesítés nem támogatott. |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |Integrált Windows-hitelesítés nem támogatott. |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

További információkért lásd: [Sailpoint IdentityNow](https://www.sailpoint.com/idaas-identity-as-a-service-identitynow/).

## <a name="secureauth-idp-720"></a>SecureAuth IdP 7.2.0

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az: 

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |None |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |None |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

SecureAuth kapcsolatos további információkért lásd: [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293).














## <a name="signgo-53"></a>Aláírás, és nyissa meg 5.3-as

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az:

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |Támogatott Kerberos-szerződések |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |None |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

Bejelentkezési & Ugrás 5.3 támogatja a Kerberos-hitelesítés Kerberos szerződés konfigurációs keresztül.  Ezzel a konfigurációval segítségért lépjen kapcsolatba az Ilex, vagy megtekintheti a útmutató [bejelentkezési & Ugrás](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)

## <a name="softbank-technology-online-service-gate"></a>SoftBank technológia Online szolgáltatás kapu

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az:

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |Integrált Windows-hitelesítés nem támogatott. |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |Integrált Windows-hitelesítés nem támogatott. |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

SoftBank technológia Online szolgáltatás kapu kapcsolatos további információk: [Softbank](https://www.softbanktech.jp/service/list/osg-pro-ent/)

## <a name="vmware-workspace-one"></a>Egy VMware-munkaterület

Az alábbiakban áttekintjük a forgatókönyv támogatási mátrixot az egyszeri bejelentkezéses felhasználói élmény biztosítása az:

| Ügyfél | Támogatás | Kivételek |
| --- | --- | --- |
| Web-alapú ügyfelek, például Exchange webes elérés és a SharePoint online-hoz |Támogatott |Integrált Windows-hitelesítés nem támogatott. |
| Például a Lync, az Office-előfizetés, a CRM Gazdagügyfél-alkalmazások |Támogatott |Integrált Windows-hitelesítés nem támogatott. |
| Például az Outlook és az ActiveSync e-mailek gazdag ügyfelek |Támogatott |None |

További információt lásd: [egy VMware-munkaterület](http://www.vmware.com/pdf/vidm-office365-saml.pdf)

