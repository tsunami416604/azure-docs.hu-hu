---
title: Azure AD biztonságos hibrid hozzáférés | Microsoft Docs
description: Ez a cikk azokat a partneri megoldásokat ismerteti, amelyekkel az Azure AD-vel integrálhatja az örökölt helyszíni, nyilvános Felhőbeli és saját felhőalapú alkalmazásokat. Az alkalmazások kézbesítési vezérlőit vagy hálózatait az Azure AD-hez való csatlakozással védheti meg örökölt alkalmazásait.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 9/10/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94a118b6d526d538015b7aa076b2715ed68af338
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032058"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Biztonságos hibrid hozzáférés: az örökölt alkalmazások védelme Azure Active Directory

Mostantól a helyszíni és a Felhőbeli örökölt hitelesítési alkalmazásokat a következővel érheti el Azure Active Directory (AD) szolgáltatáshoz való csatlakoztatásával:

- [Azure-AD Application Proxy](#secure-hybrid-access-through-azure-ad-application-proxy)

- [Meglévő alkalmazás-kézbesítési vezérlők és hálózatok](#secure-hybrid-access-through-networking-and-delivery-controllers)

- [Virtuális magánhálózati (VPN) alkalmazások](#secure-hybrid-access-through-vpn-applications)

Az Azure ad-funkciókkal, például az Azure AD [feltételes hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) és az Azure ad [Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)szolgáltatással áthidalhatja a szakadékot, és megerősítheti a biztonsági helyzeteket az összes alkalmazásban.

## <a name="secure-hybrid-access-through-azure-ad-application-proxy"></a>Biztonságos hibrid hozzáférés az Azure AD Application Proxy
  
A [alkalmazásproxy](https://aka.ms/whyappproxy) használatával [biztonságos távoli hozzáférést](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) biztosíthat a helyszíni webalkalmazásokhoz. A felhasználóknak nem kell VPN-t használniuk. A felhasználók az [egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-sso)után egyszerűen csatlakozhatnak az alkalmazásaihoz bármely eszközről. Az alkalmazásproxy távoli hozzáférést biztosít a szolgáltatáshoz, és lehetővé teszi a [helyi alkalmazások egyszerű közzétételét](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application) a vállalati hálózaton kívüli felhasználók számára. Segít a felhőalapú hozzáférés-kezelés méretezésében anélkül, hogy módosítania kellene a helyszíni alkalmazásokat. A következő lépésként [tervezze meg az Azure ad Application proxy üzembe helyezését](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan) .

## <a name="azure-ad-partner-integrations"></a>Azure AD-partneri integrációk

### <a name="secure-hybrid-access-through-networking-and-delivery-controllers"></a>Biztonságos hibrid hozzáférés hálózati és kézbesítési vezérlőkön keresztül

Az [Azure ad Application Proxyon](https://aka.ms/whyappproxy)kívül, hogy lehetővé tegye a [zéró megbízhatósági keretrendszer](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)használatát, a Microsoft partnerei harmadik féltől származó szolgáltatókkal. Használhatja a meglévő hálózatkezelési és kézbesítési vezérlőit, és egyszerűen biztosíthatja az üzleti folyamatai szempontjából kritikus régebbi alkalmazások, de az Azure AD-vel való védekezését. Valószínűleg már mindent megtalál, amire szüksége lehet az alkalmazások védelmének megkezdéséhez.

![A képek biztonságos hibrid hozzáférést mutatnak a hálózati partnerekkel és az alkalmazás-proxyval](media/secure-hybrid-access/secure-hybrid-access.png)

A következő hálózati szolgáltatók előre elkészített megoldásokat és részletes útmutatást nyújtanak az Azure AD-vel való integráláshoz.

- [Akamai Enterprise Application Access (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Citrix Application Delivery Controller (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)

- [Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

### <a name="secure-hybrid-access-through-vpn-applications"></a>Biztonságos hibrid hozzáférés VPN-alkalmazásokon keresztül

A VPN-megoldásokkal biztonságos hozzáférést biztosíthat a vállalati hálózathoz bármely eszközről, bármikor, bármilyen helyen, a szervezet adatainak védelme mellett. Azáltal, hogy az Azure AD-t identitás-szolgáltatóként (IDENTITÁSSZOLGÁLTATÓ) használja, modern hitelesítési és engedélyezési módszereket használhat, például az Azure AD [egyszeri bejelentkezést](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) és a [többtényezős hitelesítést](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) a helyszíni örökölt alkalmazások biztonságossá tételéhez.  

![A képen a VPN-partnerek és az alkalmazásproxy biztonságos hibrid hozzáférése látható ](media/secure-hybrid-access/app-proxy-vpn.png)

A következő VPN-szállítók előre elkészített megoldásokat és részletes útmutatást nyújtanak az Azure AD-vel való integráláshoz.

• [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

• [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

• [F5 Big-IP APM](https://aka.ms/f5-hybridaccessguide)

• [Palo Alto hálózatok – globális védelem](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

• [Zscaler privát hozzáférése (ZPA)](https://aka.ms/zscaler-hybridaccessguide)
