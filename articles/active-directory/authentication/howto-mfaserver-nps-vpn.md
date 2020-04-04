---
title: Azure MFA-kiszolgáló és külső VPN-ek – Azure Active Directory
description: Az Azure MFA Server részletes konfigurációs útmutatói a Cisco, a Citrix és a Juniper integrálására.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01decb99a9eb24ae60250f83f1f961b4c1690bc0
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652846"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Speciális forgatókönyvek az Azure MFA Server és a külső VPN-megoldások segítségével

Az Azure Multi-Factor Authentication Server (Azure MFA Server) segítségével zökkenőmentesen csatlakozhat a különböző külső VPN-megoldásokhoz. Ez a cikk&reg; a Cisco ASA VPN-készülékre, a Citrix NetScaler SSL VPN-készülékre és a Juniper Networks Secure Access/Pulse Secure Connect Secure SSL VPN-készülékre összpontosít. Létrehoztunk konfigurációs útmutatókat e három közös készülék kezelésére. Az Azure MFA Server a legtöbb más, RADIUS-, LDAP-, IIS-hitelesítést vagy jogcímalapú hitelesítést használó rendszerrel is integrálható az AD FS szolgáltatásba. További részleteket az [Azure MFA Server konfigurációiban](howto-mfaserver-deploy.md#next-steps)talál.

> [!IMPORTANT]
> 2019. július 1-jéig a Microsoft a továbbiakban nem ajánlja fel az MFA Server alkalmazást az új telepítésekhez. Azoknak az új ügyfeleknek, akik többtényezős hitelesítést szeretnének megkövetelni a felhasználóiktól, felhőalapú Azure többtényezős hitelesítést kell használniuk. Azok a meglévő ügyfelek, akik július 1-je előtt aktiválták az MFA-kiszolgálót, a szokásos módon letölthetik a legújabb verziót, a jövőbeli frissítéseket, és a szokásos módon létrehozhatják az aktiválási hitelesítő adatokat.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Cisco ASA VPN-készülék és Azure MFA Server
Az Azure MFA Server&reg; integrálható a Cisco ASA VPN-készülékkel, hogy további biztonságot nyújtson a Cisco AnyConnect&reg; VPN-bejelentkezések és a portálhozzáférés számára.  Használhatja az LDAP vagy a RADIUS protokollt.  Válassza ki az alábbi műveletek egyikét a részletes, részletes konfigurációs útmutatók letöltéséhez.

| Konfigurációs útmutató | Leírás |
| --- | --- |
| [Cisco ASA anyconnect VPN- és Azure MFA-konfigurációval LDAP-hoz](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integrálja a Cisco ASA VPN-készülékét az Azure MFA-val az LDAP használatával |
| [Cisco ASA anyconnect VPN- és Azure MFA-konfigurációval radius-hoz](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integrálja cisco ASA VPN-készülékét az Azure MFA-val a RADIUS használatával |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN és Azure MFA Server
Az Azure MFA Server integrálható a Citrix NetScaler SSL VPN-készülékkel, hogy további biztonságot nyújtson a Citrix NetScaler SSL VPN-bejelentkezések és a portálhozzáférés számára.  Használhatja az LDAP vagy a RADIUS protokollt.  Válassza ki az alábbi műveletek egyikét a részletes, részletes konfigurációs útmutatók letöltéséhez.

| Konfigurációs útmutató | Leírás |
| --- | --- |
| [Citrix NetScaler SSL VPN és Azure MFA-konfiguráció LDAP-hoz](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrálja a Citrix NetScaler SSL VPN-t az Azure MFA-készülékkel LDAP használatával |
| [Citrix NetScaler SSL VPN és Azure MFA-konfiguráció RADIUS-hoz](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integrálja a Citrix NetScaler SSL VPN-készülékét az Azure MFA-val a RADIUS használatával |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Juniper/Pulse Secure SSL VPN-készülék és Az Azure MFA Server
Az Azure MFA-kiszolgáló integrálható a Juniper/Pulse Secure SSL VPN-készülékkel, hogy további biztonságot nyújtson a Juniper/Pulse Secure SSL VPN bejelentkezések és a portálhozzáférés számára.  Használhatja az LDAP vagy a RADIUS protokollt.  Válassza ki az alábbi műveletek egyikét a részletes, részletes konfigurációs útmutatók letöltéséhez.

| Konfigurációs útmutató | Leírás |
| --- | --- |
| [Juniper/Pulse Biztonságos SSL VPN és Az Azure MFA-konfiguráció LDAP-hoz](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integrálja a Juniper/Pulse Secure SSL VPN-t az Azure MFA-készülékkel ldap használatával |
| [Juniper/Pulse Biztonságos SSL VPN és Az Azure MFA-konfiguráció RADIUS-hoz](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integrálja juniper/Pulse Secure SSL VPN-készülékét az Azure MFA-val a RADIUS használatával |

## <a name="next-steps"></a>További lépések

- [Bővítse meglévő hitelesítési infrastruktúráját az Azure többtényezős hitelesítéshez szükséges nps-bővítménysel](howto-mfa-nps-extension.md)

- [Az Azure Multi-Factor Authentication beállításainak konfigurálása](howto-mfa-mfasettings.md)
