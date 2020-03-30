---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 410570302eec418f1e4bcb75d6413936a96b5171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471528"
---
A feltételes hozzáférés lehetővé teszi a részletes hozzáférés-vezérlést alkalmazásonként. A feltételes hozzáférés használatához az Azure AD Premium 1 vagy nagyobb licencelést kell alkalmazni a feltételes hozzáférési szabályok hatálya alá tartozó felhasználókra.

1. Keresse meg a **Vállalati alkalmazások – Minden alkalmazás** lapot, és kattintson az Azure **VPN**.

   - Kattintson **a Feltételes hozzáférés gombra.**
   - Kattintson az **Új házirend gombra** az **Új** ablaktábla megnyitásához.
2. Az **Új** ablaktáblán keresse meg **a Hozzárendelések -> felhasználók és csoportok lehetőséget.** A **Felhasználók és csoportok ->** **Belefoglalás** lapon:

   - Kattintson **a Felhasználók és csoportok kijelölése gombra.**
   - Ellenőrizze **a Felhasználók és csoportok**lehetőséget.
   - A **Kijelölés gombra** kattintva jelölje ki az MFA által érintett csoportot vagy felhasználói csoportokat.
   - Kattintson a **Done** (Kész) gombra.

   ![Hozzárendelések](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. Az **Új** ablaktáblán keresse meg a **Hozzáférés-vezérlők -> Támogatás** ablaktáblát:

   - Kattintson **a Hozzáférés megadása gombra.**
   - Kattintson **a Többtényezős hitelesítés megkövetelése gombra.**
   - Kattintson **az Összes kijelölt vezérlőelőírás a gombra.**
   - Kattintson a **Kiválasztás** gombra.
   
   ![Hozzáférés megadása - MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. A **Házirend engedélyezése** szakaszban:

   - Válassza **a Be**lehetőséget.
   - Kattintson **a Létrehozás gombra.**

   ![Házirend engedélyezése](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)