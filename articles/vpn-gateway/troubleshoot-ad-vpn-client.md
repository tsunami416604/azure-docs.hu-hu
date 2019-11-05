---
title: 'Azure AD-hitelesítéssel rendelkező VPN-ügyfél hibáinak megoldása: Azure VPN Gateway | Microsoft Docs'
description: Az Azure AD hitelesítési ügyfeleivel VPN Gateway P2S hibáinak megoldása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: e239f283d5065e032e98e87a72a4bfe9fbafbdb1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517279"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Azure AD-hitelesítéssel rendelkező VPN-ügyfél hibáinak megoldása

Ebből a cikkből megtudhatja, hogyan oldhatja fel a VPN-ügyfelet a virtuális hálózathoz pont – hely VPN és Azure Active Directory hitelesítés használatával való csatlakozáshoz.

## <a name="status"></a>Állapotüzenetek megtekintése

A hibaüzenetek állapotára vonatkozó napló megtekintése.

![naplók](./media/troubleshoot-ad-vpn-client/1.png)

1. Az **állapotüzenetek**megjelenítéséhez kattintson az ügyfél ablakának jobb alsó sarkában található nyilak ikonra.
2. Keresse meg a hibát jelző hibákat a naplókban.
3. A hibaüzenetek piros színnel jelennek meg.

## <a name="clear"></a>Bejelentkezési adatok törlése

Törölje a bejelentkezési adatokat.

![bejelentkezés](./media/troubleshoot-ad-vpn-client/2.png)

1. Válassza a... a megoldani kívánt profil mellett. Válassza a **Konfigurálás – > a mentett fiók törlése**lehetőséget.
2. Kattintson a **Mentés** gombra.
3. Próbáljon meg csatlakozni.
4. Ha a kapcsolat továbbra is sikertelen, folytassa a következő szakasszal.

## <a name="diagnostics"></a>Diagnosztika futtatása

Futtasson diagnosztikát a VPN-ügyfélen.

![diagnosztika](./media/troubleshoot-ad-vpn-client/3.png)

1. Kattintson a **.** . azon profil mellett, amelyen diagnosztikát kíván futtatni. Válassza a **Diagnosztizálás – > futtatási diagnosztika**lehetőséget.
2. Az ügyfél több tesztet fog futtatni, és megjeleníti a teszt eredményét.

   * Internet-hozzáférés – ellenőrzi, hogy az ügyfél rendelkezik-e internetkapcsolattal
   * Ügyfél hitelesítő adatai – ellenőrizze, hogy a Azure Active Directory hitelesítési végpont elérhető-e
   * Kiszolgáló feloldható – Kapcsolatfelvétel a DNS-kiszolgálóval a konfigurált VPN-kiszolgáló IP-címének feloldásához
   * A kiszolgáló érhető el – ellenőrzi, hogy a VPN-kiszolgáló válaszol-e vagy sem
3. Ha a tesztek bármelyike meghiúsul, a probléma megoldásához forduljon a hálózati rendszergazdához.
4. A következő szakasz bemutatja, hogyan gyűjtheti össze a naplókat, ha szükséges.

## <a name="logfiles"></a>Ügyfél-naplófájlok összegyűjtése

Gyűjtse össze a VPN-ügyfél naplófájljait. A naplófájlok a támogatás/rendszergazda számára a választott módszer használatával küldhetők el. Például: e-mail.

1. Kattintson a "..." azon profil mellett, amelyen diagnosztikát kíván futtatni. Válassza a **Diagnosztizálás – > naplók megjelenítése könyvtárat**.

   ![naplók megjelenítése](./media/troubleshoot-ad-vpn-client/4.png)
2. A Windows Intéző megnyílik a naplófájlokat tartalmazó mappához.

   ![fájl megtekintése](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>További lépések

További információ: [Azure Active Directory-bérlő létrehozása az Azure ad-hitelesítést használó P2S nyitott VPN-kapcsolatokhoz](openvpn-azure-ad-tenant.md).