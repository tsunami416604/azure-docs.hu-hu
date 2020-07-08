---
title: Azure MFA-kiszolgáló és harmadik féltől származó virtuális magánhálózatok – Azure Active Directory
description: Részletes konfigurációs útmutatók az Azure MFA-kiszolgálóhoz a Cisco, a Citrix és a Juniper integrálásához.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80652846"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Speciális forgatókönyvek az Azure MFA-kiszolgálóval és a harmadik féltől származó VPN-megoldásokkal

Az Azure Multi-Factor Authentication-kiszolgáló (Azure MFA-kiszolgáló) segítségével zökkenőmentesen kapcsolódhat különböző külső VPN-megoldásokkal. Ez a cikk a Cisco &reg; ASA VPN-készülékre, a Citrix NETSCALER SSL VPN-készülékre, valamint a Juniper Networks Secure Access/Pulse Secure SSL VPN-készülék biztonságos elérésére koncentrál. A következő három közös berendezés kezelésére szolgáló konfigurációs útmutatókat hoztunk létre. Az Azure MFA-kiszolgáló a legtöbb más olyan rendszerrel is integrálható, amelyek RADIUS-, LDAP-, IIS-vagy jogcím-alapú hitelesítést használnak AD FS. További részleteket az [Azure MFA-kiszolgáló konfigurációjában](howto-mfaserver-deploy.md#next-steps)talál.

> [!IMPORTANT]
> 2019. július 1-től a Microsoft már nem kínál új, az MFA-kiszolgálót az új üzemelő példányokhoz. Azok a felhasználók, akik a többtényezős hitelesítést szeretnék megkövetelni a felhasználóknak, felhőalapú Azure-Multi-Factor Authentication kell használniuk. Azok a meglévő ügyfelek, akik aktiválták az MFA-kiszolgálót a július 1. előtt, le tudják tölteni a legújabb verziót, a jövőbeli frissítéseket, és az aktiválási hitelesítő adatokat a szokásos módon létrehozzák.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Cisco ASA VPN-készülék és Azure MFA-kiszolgáló
Az Azure MFA-kiszolgáló integrálható a Cisco &reg; ASA VPN-berendezéssel, hogy további biztonságot nyújtson a Cisco AnyConnect &reg; VPN-bejelentkezésekhez és a portálhoz való hozzáféréshez.  Használhatja az LDAP vagy a RADIUS protokollt is.  Válassza ki a következők egyikét a részletes, lépésenkénti konfigurációs útmutatók letöltéséhez.

| Konfigurációs útmutató | Description |
| --- | --- |
| [Cisco ASA a AnyConnect VPN és az Azure MFA konfigurálása az LDAP-hez](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | A Cisco ASA VPN-készülék integrálása az Azure MFA-val LDAP használatával |
| [Cisco ASA AnyConnect VPN-sel és Azure MFA-konfiguráció RADIUS-hez](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | A Cisco ASA VPN-készülék integrálása az Azure MFA-val a RADIUS használatával |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN és Azure MFA-kiszolgáló
Az Azure MFA-kiszolgáló integrálva van a Citrix NetScaler SSL VPN-berendezésével, hogy további biztonságot nyújtson a Citrix NetScaler SSL VPN-bejelentkezésekhez és a portálhoz való hozzáféréshez.  Használhatja az LDAP vagy a RADIUS protokollt is.  Válassza ki a következők egyikét a részletes, lépésenkénti konfigurációs útmutatók letöltéséhez.

| Konfigurációs útmutató | Description |
| --- | --- |
| [Citrix NetScaler SSL VPN és Azure MFA-konfiguráció LDAP-hez](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | A Citrix NetScaler SSL VPN integrálása az Azure MFA-berendezéssel LDAP használatával |
| [Citrix NetScaler SSL VPN és Azure MFA-konfiguráció a RADIUS-hez](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | A Citrix NetScaler SSL VPN-készülék integrálása az Azure MFA-val a RADIUS használatával |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Juniper/Pulse Secure SSL VPN-készülék és Azure MFA-kiszolgáló
Az Azure MFA-kiszolgáló integrálható a Juniper/Pulse Secure SSL VPN-berendezéssel, hogy további biztonságot nyújtson a Juniper/Pulse Secure SSL VPN-bejelentkezésekhez és a portálhoz való hozzáféréshez.  Használhatja az LDAP vagy a RADIUS protokollt is.  Válassza ki a következők egyikét a részletes, lépésenkénti konfigurációs útmutatók letöltéséhez.

| Konfigurációs útmutató | Description |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN és Azure MFA-konfiguráció LDAP-hez](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | A Juniper/Pulse Secure SSL VPN integrálása az Azure MFA-berendezéssel az LDAP használatával |
| [Juniper/Pulse Secure SSL VPN és Azure MFA-konfiguráció a RADIUS-hez](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | A Juniper/Pulse Secure SSL VPN-berendezés integrálása az Azure MFA-val a RADIUS használatával |

## <a name="next-steps"></a>További lépések

- [Bővítse meglévő hitelesítési infrastruktúráját az Azure NPS-bővítményével Multi-Factor Authentication](howto-mfa-nps-extension.md)

- [Az Azure Multi-Factor Authentication beállításainak konfigurálása](howto-mfa-mfasettings.md)
