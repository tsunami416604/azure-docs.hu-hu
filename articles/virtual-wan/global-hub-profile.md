---
title: Azure Virtual WAN globális vagy hub-alapú VPN-profilok letöltése | Microsoft Docs
description: Ismerje meg a virtuális WAN-alapú, automatikusan méretezhető ág-ág kapcsolatot, az elérhető régiókat és a partnereket.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 4/20/2020
ms.author: alzam
ms.openlocfilehash: b63bb861f4df087f852bb1bf599d32100d063f7e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733180"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Globális vagy hub-alapú profil letöltése a felhasználók VPN-ügyfelei számára

Az Azure Virtual WAN két típusú kapcsolatot biztosít a távoli felhasználók számára: globális és hub-alapú. A következő fejezetekben megismerheti és letöltheti a profilokat. 

> [!IMPORTANT]
> A RADIUS-hitelesítés csak a hub-alapú profilt támogatja.

## <a name="global-profile"></a>Globális profil

A profil olyan terheléselosztó számára mutat, amely az összes aktív felhasználói VPN-hubokat tartalmazza. A felhasználót a rendszer a felhasználó földrajzi helyéhez legközelebb eső hubhoz irányítja. Ez a típusú kapcsolat akkor hasznos, ha a felhasználók gyakran különböző helyszínekre utaznak. A **globális** profil letöltése:

1. Lépjen a virtuális WAN-ra.
2. Kattintson a **felhasználói VPN-konfiguráció**elemre.
3. Jelölje ki azt a konfigurációt, amelyhez le szeretné tölteni a profilt.
4. Kattintson a **virtuális WAN-felhasználói VPN-profil letöltése**elemre.

   ![Globális profil](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>Hub-alapú profil

A profil egyetlen hubhoz mutat. A felhasználó csak az adott hubhoz tud csatlakozni a profil használatával. A **hub-alapú** profil letöltése:

1. Lépjen a virtuális WAN-ra.
2. Az Áttekintés lapon kattintson a **hub** elemre.

    ![1. hub-profil](./media/global-hub-profile/hub1.png)
3. Kattintson **a felhasználó VPN (pont – hely)** elemre.
4. Kattintson a **virtuális központ felhasználói VPN-profil letöltése**elemre.

   ![2. hub-profil](./media/global-hub-profile/hub2.png)
5. **EAPTLS**-keresés.
6. Kattintson **a profil előállítása és letöltése**elemre.

   ![3. hub-profil](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>További lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.
