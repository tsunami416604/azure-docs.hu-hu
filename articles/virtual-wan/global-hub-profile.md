---
title: Az Azure Virtual WAN globális vagy hubalapú VPN-profilok letöltése | Microsoft dokumentumok
description: Ismerje meg a Virtual WAN automatizált skálázható ág-ág kapcsolat, a rendelkezésre álló régiók és partnerek.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/31/2020
ms.author: alzam
ms.openlocfilehash: 3b7e765dbd024d46939e8989993f0c882b2a8f4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965228"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Globális vagy hubalapú profil letöltése felhasználói VPN-ügyfelek számára

Az Azure Virtual WAN kétféle kapcsolatot kínál a távoli felhasználók számára: globális és hub alapú. A következő szakaszok segítségével megismerheti és letöltheti a profilt.

## <a name="global-profile"></a>Globális profil

A profil egy terheléselosztóra mutat, amely tartalmazza az összes aktív felhasználói VPN-központot. A felhasználó a felhasználó földrajzi helyéhez legközelebb eső hubra irányítja. Ez a fajta kapcsolat akkor hasznos, ha a felhasználók gyakran utaznak különböző helyekre. A **globális** profil letöltése:

1. Lépjen a virtuális WAN-ra.
2. Kattintson **a Felhasználói VPN-konfiguráció gombra.**
3. Jelölje ki azt a konfigurációt, amelyhez le szeretné tölteni a profilt.
4. Kattintson **a Virtuális WAN felhasználói VPN-profil letöltése gombra.**

   ![Globális profil](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>Hub-alapú profil

A profil egyetlen hubra mutat. A felhasználó csak ezzel a profillal tud csatlakozni az adott elosztóhoz. A **hub-alapú** profil letöltése:

1. Lépjen a virtuális WAN-ra.
2. Kattintson a **Központ** elemre az Áttekintés lapon.

    ![Hub-profil 1](./media/global-hub-profile/hub1.png)
3. Kattintson **a Felhasználói VPN (pontról webhelyre) parancsra.**
4. Kattintson **a Virtuális központ felhasználói VPN-profiljának letöltése gombra.**

   ![Hub profil 2](./media/global-hub-profile/hub2.png)
5. Ellenőrizze **eaptls**.
6. Kattintson **a Profil létrehozása és letöltése gombra.**

   ![Hub profil 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>További lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.
