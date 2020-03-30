---
title: Hálózati kiszolgáló integrálása VPN-átjáró RADIUS-hitelesítésével az MFA-hoz
description: Ez a témakör az Azure gateway RADIUS-hitelesítés integrálását ismerteti a hálózati kiszolgálóval a többtényezős hitelesítéshez.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 941b6ac86941824351f83592998e8735e3eb8ee5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780368"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Az Azure VPN-átjáró RADIUS-hitelesítésének integrálása hálózati kiszolgálóval a többtényezős hitelesítéshez 

A cikk ismerteti, hogyan integrálható a hálózati házirend-kiszolgáló (NPS) az Azure VPN-átjáró RADIUS-hitelesítéssel a többtényezős hitelesítés (MFA) biztosításához a pont-hely VPN-kapcsolatokhoz. 

## <a name="prerequisite"></a>Előfeltétel

Az MFA engedélyezéséhez a felhasználóknak az Azure Active Directoryban (Azure AD) kell lenniük, amelyet a helyszíni vagy a felhőbeli környezetből kell szinkronizálni. Emellett a felhasználónak már be kell fejeznie az MFA automatikus igénylési folyamatát.  További információ: [A fiókom beállítása kétlépéses ellenőrzéshez című témakörben](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md) talál.

## <a name="detailed-steps"></a>Részletes lépések

### <a name="step-1-create-a-virtual-network-gateway"></a>1. lépés: Virtuális hálózati átjáró létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A virtuális hálózati átjárót üzemeltető virtuális hálózatban válassza az **Alhálózatok**lehetőséget, majd az **Átjáró alhálózat** ot az alhálózat létrehozásához. 

    ![Az átjáró alhálózatának hozzáadásáról szóló kép](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Hozzon létre egy virtuális hálózati átjárót a következő beállítások megadásával:

    - **Átjáró típusa**: válassza ki a **VPN** elemet.
    - **VPN-típus**: Válassza **az Útvonal alapú**lehetőséget.
    - **Termékváltozat:** Válasszon termékváltozat-típust a követelmények alapján.
    - **Virtuális hálózat**: Válassza ki azt a virtuális hálózatot, amelyben létrehozta az átjáró alhálózatot.

        ![A virtuális hálózati átjáró beállításairól szóló kép](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>2. lépés A hálózati kiszolgáló konfigurálása az Azure MFA-hoz

1. A n-ps-kiszolgálón [telepítse a nps bővítményt az Azure MFA.](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension)
2. Nyissa meg a nps konzolt, kattintson a jobb gombbal **a RADIUS-ügyfelek**elemre, és válassza az **Új parancsot.** Hozza létre a RADIUS-ügyfelet a következő beállítások megadásával:

    - **Rövid név**: Írjon be bármilyen nevet.
    - **Cím (IP vagy DNS)**: Írja be az 1.
    - **Megosztott titok:** írjon be bármilyen titkos kulcsot, és jegyezze meg későbbi használatra.

      ![A RADIUS-ügyfél beállításairól szóló kép](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  A **Speciális** lapon állítsa a szállító nevét **RADIUS-szabványra,** és győződjön meg arról, hogy a **További beállítások** jelölőnégyzet nincs bejelölve.

    ![A RADIUS-ügyfél speciális beállításairól szóló kép](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Nyissa meg **a Házirendek** > **hálózati házirendek című**lehetőséget, kattintson duplán a Kapcsolatok a Microsoft **Útválasztás és távelérés kiszolgálóhoz** házirendre, válassza a Hozzáférés **megadása**lehetőséget, majd kattintson **az OK**gombra.

### <a name="step-3-configure-the-virtual-network-gateway"></a>3. lépés A virtuális hálózati átjáró konfigurálása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Nyissa meg a létrehozott virtuális hálózati átjárót. Győződjön meg arról, hogy az átjáró típusa **VPN-re** van állítva, és hogy a **VPN-típus útvonalalapú.**
3. Kattintson **a Pont a hely hez beállítás** > **most gombra,** majd adja meg a következő beállításokat:

    - **Címkészlet**: Írja be az 1.
    - **Hitelesítés típusa**: Válassza a **RADIUS-hitelesítés lehetőséget.**
    - **Kiszolgáló IP-címe**: Írja be a nps-kiszolgáló IP-címét.

      ![A webhely beállításairól szóló kép](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>További lépések

- [Azure többtényezős hitelesítés](../active-directory/authentication/multi-factor-authentication.md)
- [Meglévő hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure Multi-Factor Authenticationnel](../active-directory/authentication/howto-mfa-nps-extension.md)
