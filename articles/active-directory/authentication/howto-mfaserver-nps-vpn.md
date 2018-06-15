---
title: Speciális forgatókönyvek az Azure MFA és a külső VPN-EK
description: Cisco, Citrix vagy Juniper integrálása az Azure MFA részletes konfigurációs útmutatói.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 08/25/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 725f2893419b2b921f262fc4516f0f8a2ecf4c37
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33869291"
---
# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-third-party-vpn-solutions"></a>Speciális forgatókönyvek az Azure multi-factor Authentication és a külső VPN-megoldások

Az Azure multi-factor Authentication segítségével különböző külső VPN-megoldások zökkenőmentesen csatlakozzon. Ez a cikk a Cisco® ASA VPN-készülék, Citrix NetScaler SSL VPN-készülék és a Juniper hálózatok biztonságos hozzáférést/Pulse Secure Csatlakozás biztonságos SSL VPN-készülék összpontosít. Létrehozott konfigurációs útmutatók három közös készülék megoldására. A multi-factor Authentication kiszolgáló is integrálható a legtöbb más RADIUS, LDAP, az IIS vagy AD FS jogcímalapú hitelesítést használó rendszerek. További részletek találhatók [MFA kiszolgáló konfigurációk](howto-mfaserver-deploy.md#next-steps).

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Cisco ASA VPN-készülék és az Azure multi-factor Authentication
Az Azure multi-factor Authentication integrálható a Cisco® ASA VPN-készülék további biztonsága érdekében Cisco AnyConnect® VPN bejelentkezéseket és a portál hozzáféréséhez.  Használhatja az LDAP vagy a RADIUS protokollt.  Válassza ki a részletes konfigurációs részletes útmutatók letölteni a következők egyikét.

| Konfigurációs útmutató | Leírás |
| --- | --- |
| [Cisco ASA LDAP Anyconnect VPN- és az Azure MFA-konfiguráció](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | A Cisco ASA VPN-készülék integrálása az Azure MFA Használatát az LDAP segítségével |
| [Cisco ASA RADIUS Anyconnect VPN- és az Azure MFA-konfiguráció](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | A Cisco ASA VPN-készülék integrálása az Azure MFA RADIUS használata |

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Citrix NetScaler SSL VPN- és az Azure többtényezős hitelesítés
Az Azure multi-factor Authentication integrálható a Citrix NetScaler SSL VPN-készülék biztonságának növelése érdekében a Citrix NetScaler SSL VPN bejelentkezéseket és a portál hozzáféréséhez.  Használhatja az LDAP vagy a RADIUS protokollt.  Válassza ki a részletes konfigurációs részletes útmutatók letölteni a következők egyikét.

| Konfigurációs útmutató | Leírás |
| --- | --- |
| [LDAP Citrix NetScaler SSL VPN- és az Azure MFA-konfiguráció](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | A Citrix NetScaler SSL VPN integrálása az Azure MFA készülék LDAP segítségével |
| [A RADIUS-Citrix NetScaler SSL VPN- és az Azure MFA-konfiguráció](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | A Citrix NetScaler SSL VPN-készülék integrálása az Azure MFA RADIUS használata |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Juniper/Pulse Secure SSL VPN-készülék és az Azure multi-factor Authentication
Az Azure multi-factor Authentication integrálható a Juniper/Pulse Secure SSL VPN-készülék további biztonsága érdekében Juniper/Pulse Secure SSL VPN bejelentkezéseket és a portál hozzáféréséhez.  Használhatja az LDAP vagy a RADIUS protokollt.  Válassza ki a részletes konfigurációs részletes útmutatók letölteni a következők egyikét.

| Konfigurációs útmutató | Leírás |
| --- | --- |
| [LDAP Juniper/Pulse biztonságos SSL VPN és az Azure MFA konfigurációja](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | A Juniper/Pulse Secure SSL VPN integrálása az Azure MFA készülék LDAP segítségével |
| [A RADIUS-Juniper/Pulse biztonságos SSL VPN- és Azure MFA-konfiguráció](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | A Juniper/Pulse Secure SSL VPN-készülék integrálása az Azure MFA RADIUS használata |

## <a name="next-steps"></a>További lépések

- [A meglévő hitelesítési infrastruktúrát a hálózati házirend-kiszolgáló kiterjesztésű kiegészítheti az Azure multi-factor Authentication](howto-mfa-nps-extension.md)

- [Az Azure Multi-Factor Authentication beállításainak konfigurálása](howto-mfa-mfasettings.md)