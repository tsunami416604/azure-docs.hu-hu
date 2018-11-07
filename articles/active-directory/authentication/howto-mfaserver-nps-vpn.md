---
title: Speciális forgatókönyvek az Azure MFA és a külső VPN-EK
description: Részletes konfigurációs útmutatók Cisco, Citrix és Juniper integrálása az Azure MFA-kiszolgáló.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 5e5aaafe2659fbf126a025f479374559c1737216
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253975"
---
# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-third-party-vpn-solutions"></a>Speciális forgatókönyvek az Azure multi-factor Authentication és a külső VPN-megoldások

Az Azure multi-factor Authentication segítségével zökkenőmentesen együttműködnek a különböző külső VPN-megoldások. Ez a cikk a Cisco® ASA VPN-berendezés, Citrix NetScaler SSL VPN-berendezés és a Juniper hálózatok biztonságos hozzáférés/Pulse Secure Csatlakozás biztonságos SSL VPN-berendezés összpontosít. Létrehozott konfigurációs útmutatók három gyakori készülék megoldása érdekében. A multi-factor Authentication-kiszolgáló legtöbb RADIUS, az LDAP, az IIS vagy AD FS jogcímalapú hitelesítést használó más rendszerekkel integrálhatja. További információk a [MFA-kiszolgáló konfigurációk](howto-mfaserver-deploy.md#next-steps).

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Cisco ASA VPN-berendezés és az Azure multi-factor Authentication
Az Azure multi-factor Authentication integrálható a Cisco® ASA VPN-berendezés további biztonságot nyújt a Cisco AnyConnect® VPN-bejelentkezések és a portál elérésére.  RADIUS vagy LDAP protokollt használhat.  Válassza ki a következők egyikét a részletes részletes konfigurációs útmutatók letöltéséhez.

| Konfigurációs útmutató | Leírás |
| --- | --- |
| [Cisco Anyconnect VPN- és Azure MFA konfigurálása az LDAP használatára az ASA](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | A Cisco ASA VPN-berendezés integrálása az Azure MFA, az LDAP |
| [Cisco Anyconnect VPN- és Azure MFA konfigurálása a RADIUS-ASA](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | A Cisco ASA VPN-berendezés integrálása az Azure MFA RADIUS használata |

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>A Citrix NetScaler SSL VPN és az Azure multi-factor Authentication
Az Azure multi-factor Authentication integrálható a Citrix NetScaler SSL VPN-berendezés további biztonságot nyújt a Citrix NetScaler SSL VPN-bejelentkezések és a portál elérésére.  RADIUS vagy LDAP protokollt használhat.  Válassza ki a következők egyikét a részletes részletes konfigurációs útmutatók letöltéséhez.

| Konfigurációs útmutató | Leírás |
| --- | --- |
| [A Citrix NetScaler SSL VPN- és Azure MFA konfigurálása az LDAP használatára](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | A Citrix NetScaler SSL VPN integrálása az Azure MFA-berendezés LDAP használatával |
| [A Citrix NetScaler SSL VPN- és Azure MFA konfigurálása a RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | A Citrix NetScaler SSL VPN-berendezés integrálása az Azure MFA RADIUS használata |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Juniper/Pulse Secure SSL VPN-berendezés és az Azure multi-factor Authentication
Az Azure multi-factor Authentication integrálható a Juniper/Pulse Secure SSL VPN-berendezés további biztonságot nyújt a Juniper/Pulse Secure SSL VPN-bejelentkezések és a portál elérésére.  RADIUS vagy LDAP protokollt használhat.  Válassza ki a következők egyikét a részletes részletes konfigurációs útmutatók letöltéséhez.

| Konfigurációs útmutató | Leírás |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN és az Azure MFA konfigurálása az LDAP használatára](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | A Juniper/Pulse Secure SSL VPN integrálása az Azure MFA-berendezés LDAP használatával |
| [A RADIUS Juniper/Pulse Secure SSL VPN és az Azure MFA konfigurálása](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | A Juniper/Pulse Secure SSL VPN-berendezés integrálása az Azure MFA RADIUS használata |

## <a name="next-steps"></a>További lépések

- [Az Azure multi-factor Authentication az NPS-bővítményt a meglévő hitelesítési infrastruktúrájának bővítésével](howto-mfa-nps-extension.md)

- [Az Azure Multi-Factor Authentication beállításainak konfigurálása](howto-mfa-mfasettings.md)