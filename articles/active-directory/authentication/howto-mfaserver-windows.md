---
title: Windows-hitelesítés és Azure MFA-kiszolgáló – Azure Active Directory
description: A Windows-hitelesítés és az Azure Multi-Factor Authentication-kiszolgáló üzembe helyezése.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4abfb970ca322724adb0f8919b7509bc8a641378
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80652798"
---
# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Windows-hitelesítés és Azure Multi-Factor Authentication-kiszolgáló

Az Azure Multi-Factor Authentication-kiszolgáló Windows-hitelesítés szakaszának segítségével engedélyezheti és konfigurálhatja a Windows-hitelesítést alkalmazásokhoz. A Windows-hitelesítés beállítása előtt tartsa szem előtt az alábbi listát:

* Beállítás után indítsa újra a terminálszolgáltatások Azure Multi-Factor Authentication szolgáltatását a beállítás életbe léptetéséhez.
* Ha az "Azure Multi-Factor Authentication felhasználói egyeztetés megkövetelése" jelölőnégyzet be van jelölve, és nem szerepel a felhasználók listáján, akkor az újraindítás után nem fog tudni bejelentkezni a gépre.
* A Megbízható IP-címek attól függnek, hogy az alkalmazás képes-e biztosítani az ügyfél IP-címének hitelesítését. Jelenleg csak a Terminálszolgáltatások támogatott.  

> [!IMPORTANT]
> 2019. július 1-től a Microsoft már nem kínál új, az MFA-kiszolgálót az új üzemelő példányokhoz. Azok a felhasználók, akik a többtényezős hitelesítést szeretnék megkövetelni a felhasználóknak, felhőalapú Azure-Multi-Factor Authentication kell használniuk. Azok a meglévő ügyfelek, akik aktiválták az MFA-kiszolgálót a július 1. előtt, le tudják tölteni a legújabb verziót, a jövőbeli frissítéseket, és az aktiválási hitelesítő adatokat a szokásos módon létrehozzák.

> [!NOTE]
> Ez a szolgáltatás nem támogatott a Terminálszolgáltatások védelmének biztosítására Windows Server 2012 R2-n.

## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Az alkalmazások Windows-hitelesítéssel történő biztonságossá tételéhez kövesse az alábbi eljárást

1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a Windows-hitelesítés ikonra.
   ![Windows-hitelesítés az MFA-kiszolgálón](./media/howto-mfaserver-windows/windowsauth.png)
2. Jelölje be a **Windows-hitelesítés engedélyezése** jelölőnégyzetet. Alapértelmezés szerint a jelölőnégyzet nincs bejelölve.
3. Az Alkalmazások lapon a rendszergazda konfigurálhatja egy vagy több alkalmazás esetében a Windows-hitelesítést.
4. Kiszolgáló vagy alkalmazás kiválasztása – meghatározza, hogy a kiszolgáló/alkalmazás engedélyezve van-e. Kattintson az **OK** gombra.
5. Kattintson a **Hozzáadás...** lehetőségre.
6. A Megbízható IP-címek lapon beállíthatja, hogy a rendszer kihagyja az Azure Multi-Factor Authenticationt adott IP-címekről származó Windows-munkamenetek esetén. Ha például az alkalmazottak az alkalmazást az irodából és otthonról használják, dönthet úgy, hogy nem szeretné, ha a telefonjaik folyamatosan csörögnének az Azure Multi-Factor Authentication miatt az irodában. Ehhez az irodai alhálózatot Megbízható IP-címek bejegyzésként kell megadni.
7. Kattintson a **Hozzáadás...** lehetőségre.
8. Válassza az **Egyetlen IP**-cím lehetőséget, ha egyetlen IP-címet szeretne kihagyni.
9. Válassza az **IP-címtartomány** lehetőséget, ha egy teljes IP-címtartományt szeretne kihagyni. Példa: 10.63.193.1-10.63.193.100.
10. Válassza az **Alhálózat** lehetőséget, ha egy IP-címtartományt szeretne megadni alhálózat megjelöléssel. Adja meg az alhálózat kezdő IP-címét, és válassza ki a megfelelő hálózati maszkot a legördülő listából.
11. Kattintson az **OK** gombra.

## <a name="next-steps"></a>További lépések

- [Külső felektől származó VPN-készülékek konfigurálása Azure MFA-kiszolgálóhoz](howto-mfaserver-nps-vpn.md)

- [Meglévő hitelesítési infrastruktúrájának kiegészítése az Azure MFA NPS bővítményével](howto-mfa-nps-extension.md)
