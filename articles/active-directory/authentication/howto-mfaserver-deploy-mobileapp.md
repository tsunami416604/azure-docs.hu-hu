---
title: Az Azure MFA-kiszolgáló Mobile App Web Service szolgáltatása | Microsoft Docs
description: Konfigurálja az MFA-kiszolgálót leküldéses értesítések a Microsoft Authenticator alkalmazást használó felhasználóknak történő küldésére.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: d31cf6569f642d2c7b61abe25148f5cfb199f0a2
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159440"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>A mobilalkalmazásos hitelesítés engedélyezése az Azure Multi-Factor Authentication-kiszolgálóval

A Microsoft Authenticator alkalmazás egy további sávon kívüli ellenőrzési lehetőséget kínál. Automatikus telefonhívás vagy SMS küldése helyett a felhasználó bejelentkezésekor az Azure Multi-Factor Authentication értesítést küld le a Microsoft Authenticator alkalmazásba a felhasználó okostelefonján vagy táblagépén. A felhasználó egyszerűen az **Ellenőrzés** (vagy a PIN-kód megadása után a „Hitelesítés”) elemre koppint a bejelentkezés befejezéséhez.

Ha a telefonon a vétel nem megbízható, kétlépéses ellenőrzést biztosító mobilalkalmazás használata ajánlott. Ha az alkalmazást OATH token előállítására használja, nincs szükség hálózatra vagy internetkapcsolatra.

> [!IMPORTANT]
> Ha telepítette az Azure Multi-Factor Authentication-kiszolgáló 8.x-es vagy újabb verzióját, a következő lépések legtöbbjére nincs szükség. A mobilalkalmazások hitelesítését [a mobilalkalmazás konfigurálását](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server) ismertető szakasz lépéseit követve állíthatja be.

## <a name="requirements"></a>Követelmények

A Microsoft Authenticator alkalmazás használatához az Azure Multi-Factor Authentication-kiszolgáló 8.x-es vagy újabb verzióját kell futtatnia.

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>A mobilalkalmazás beállításainak konfigurálása az Azure Multi-Factor Authentication-kiszolgálón

1. A Multi-Factor Authentication-kiszolgáló konzoljában kattintson a Felhasználói portál ikonra. Ha a felhasználók vezérelhetik a hitelesítési módszereiket, a Beállítások lapon a **Módszer kiválasztásának engedélyezése a felhasználóknak** lehetőség alatt jelölje be a **Mobile App** jelölőnégyzetét. Ha a szolgáltatás nincs engedélyezve, a végfelhasználóknak kapcsolatba kell lépniük a támogatási szolgálattal a Mobile App aktiválásának befejezéséhez.
2. Jelölje be a **Mobilalkalmazás aktiválásának engedélyezése a felhasználóknak** jelölőnégyzetet.
3. Jelölje be a **Felhasználó beléptetésének engedélyezése** jelölőnégyzetet.
4. Kattintson a **Mobile App** ikonra.
5. A **Fiók neve** mezőben adja meg a fiókhoz tartozó mobilalkalmazásban megjeleníteni kívánt cég- vagy szervezetnevet.
   ![MFA-kiszolgáló konfigurálása – A Mobile App beállításai](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>További lépések

- [Speciális, az Azure Multi-Factor Authenticationre és külső VPN-ekre vonatkozó forgatókönyvek](howto-mfaserver-nps-vpn.md).
