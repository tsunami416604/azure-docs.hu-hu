---
title: RADIUS-hitelesítés Azure Active Directory
description: Építészeti útmutató a RADIUS-hitelesítés megvalósításához a Azure Active Directory használatával.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 155b359c109de948ab9b9d6862ef7507ee76f619
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576812"
---
# <a name="radius-authentication-with-azure-active-directory"></a>RADIUS-hitelesítés Azure Active Directory

A Remote Authentication Dial-In User Service (RADIUS) olyan hálózati protokoll, amely a központi hitelesítés és a betárcsázási felhasználók engedélyezésének engedélyezésével védi a hálózatot. Számos alkalmazás továbbra is a RADIUS protokollt használja a felhasználók hitelesítéséhez.

A Microsoft Windows Server nevű szerepkör a hálózati házirend-kiszolgáló (NPS), amely RADIUS-kiszolgálóként működhet, és támogatja a RADIUS-hitelesítést.

Azure Active Directory (Azure AD) lehetővé teszi a többtényezős hitelesítés használatát a RADIUS-alapú rendszerekkel. Ha az ügyfél a korábban említett RADIUS-munkaterhelések valamelyikére szeretné alkalmazni az Azure Multi-Factor Authenticationt, telepítheti az Azure Multi-Factor Authentication NPS-bővítményt a Windows hálózati házirend-kiszolgálóra. 

A Windows hálózati házirend-kiszolgáló hitelesíti a felhasználó hitelesítő adatait Active Directory, majd elküldi a Multi-Factor Authentication kérést az Azure-nak. A felhasználó Ezután kap egy kihívást a mobil hitelesítő adataival kapcsolatban. Ha a művelet sikeres, az ügyfélalkalmazás csatlakozhat a szolgáltatáshoz. 

## <a name="use-when"></a>A következő esetekben használja: 

Multi-Factor Authenticationt kell hozzáadnia az alkalmazásokhoz, például:
* Virtuális magánhálózat (VPN)
* Wi-Fi hozzáférés
* Távoli asztali átjáró (RDG)
* Virtuális asztali infrastruktúra (VDI)
* Minden más, ami a RADIUS protokolltól függ, hogy hitelesítse a felhasználókat a szolgáltatásban. 

> [!NOTE]
> Ahelyett, hogy a RADIUS-t és az Azure Multi-Factor Authentication NPS bővítményt alkalmazza az Azure Multi-Factor Authentication VPN-munkaterhelésekre való alkalmazására, javasoljuk, hogy frissítse a VPN-t az SAML-ra, és közvetlenül összevonása a VPN-t az Azure AD-vel. Ez biztosítja a VPN számára az Azure AD-védelem teljes szélességét, beleértve a feltételes hozzáférést, a Multi-Factor Authentication, az eszközök megfelelőségét és az identitások védelmét.

![építészeti diagram](./media/authentication-patterns/radius-auth.png)


## <a name="components-of-the-system"></a>A System összetevői 

* **Ügyfélalkalmazás (VPN-ügyfél)** : hitelesítési kérés küldése a RADIUS-ügyfélnek.

* **RADIUS-ügyfél** : átalakítja az ügyfélalkalmazás kérelmeit, és elküldi azokat a RADIUS-kiszolgálónak, amelyen telepítve van az NPS-bővítmény.

* **RADIUS-kiszolgáló** : a Active Directory a RADIUS-kérelem elsődleges hitelesítésének végrehajtásához kapcsolódik. A művelet sikere után továbbítja a kérést az Azure Multi-Factor Authentication NPS bővítménynek.

* **NPS-bővítmény** : egy másodlagos hitelesítésre vonatkozó kérést indít az Azure multi-Factor Authentication számára. Ha ez sikeres, a hálózati házirend-kiszolgáló bővítménye végrehajtja a hitelesítési kérést azáltal, hogy a RADIUS-kiszolgálót olyan biztonsági jogkivonatokkal biztosítja, amelyek az Azure biztonsági jogkivonat-szolgáltatása által kiadott Multi-Factor Authentication jogcímeket tartalmaznak

* **Azure multi-Factor Authentication** : kommunikáció az Azure ad-vel a felhasználó adatainak beolvasása és másodlagos hitelesítés végrehajtása a felhasználó által konfigurált ellenőrzési módszer használatával.

## <a name="implement-radius-with-azure-ad"></a>RADIUS implementálása az Azure AD-vel 

* [Az Azure Multi-Factor Authentication képességeinek biztosítása az NPS használatával](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension) 

* [Az Azure Multi-Factor Authentication NPS bővítmény konfigurálása](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-advanced) 

* [VPN az Azure Multi-Factor Authentication az NPS bővítmény használatával](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-vpn) 

  
‎ 

 
