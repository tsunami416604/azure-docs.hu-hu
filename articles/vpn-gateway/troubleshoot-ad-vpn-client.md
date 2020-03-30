---
title: 'VPN-átjáró: VPN-ügyfél hibaelhárítása – Azure AD-hitelesítés'
description: VPN-átjáró P2S Azure AD-hitelesítési ügyfeleinek – problémamegoldás
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 8871e92f0911c4d3cbcc1772bef1daeb5c70b5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74151973"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Azure AD-hitelesítési VPN-ügyfél – problémamegoldás

Ez a cikk segít a VPN-ügyfél hibaelhárításával, hogy a point-to-site VPN és az Azure Active Directory-hitelesítés használatával csatlakozzon egy virtuális hálózathoz.

## <a name="view-status-log"></a><a name="status"></a>Állapotnapló megtekintése

A hibaüzenetek állapotnaplójának megtekintése.

![naplók](./media/troubleshoot-ad-vpn-client/1.png)

1. Az állapotnaplók megjelenítéséhez kattintson az ügyfélablak jobb alsó sarkában lévő nyilak **ikonra.**
2. Ellenőrizze a naplókban, hogy vannak-e olyan hibák, amelyek a problémát jelezhetik.
3. A hibaüzenetek piros színnel jelennek meg.

## <a name="clear-sign-in-information"></a><a name="clear"></a>Bejelentkezési információk törlése

Törölje a bejelentkezési adatokat.

![bejelentkezés](./media/troubleshoot-ad-vpn-client/2.png)

1. Válassza ki a ... a hibaelhárításhoz kívánt profil mellett. Válassza **a ->-es ügyfél törlése lehetőséget.**
2. Kattintson a **Mentés** gombra.
3. Próbáljon meg csatlakozni.
4. Ha a kapcsolat továbbra is sikertelen, folytassa a következő szakasszal.

## <a name="run-diagnostics"></a><a name="diagnostics"></a>Diagnosztika futtatása

Futtassa a diagnosztikát a VPN-ügyfélen.

![diagnosztika](./media/troubleshoot-ad-vpn-client/3.png)

1. Kattintson a **...** a diagnosztikát futtatni kívánt profil mellett. Válassza **a Diagnosztizálás -> Futtatás i diagnosztika lehetőséget.**
2. Az ügyfél tesztsorozatot futtat, és megjeleníti a teszt eredményét

   * Internet-hozzáférés – Ellenőrzi, hogy az ügyfél rendelkezik-e internetkapcsolattal
   * Ügyfélhitelesítő adatok – Ellenőrizze, hogy az Azure Active Directory hitelesítési végpontja elérhető-e
   * Kiszolgáló feloldható – A konfigurált VPN-kiszolgáló IP-címének feloldásához kapcsolatba lép a DNS-kiszolgálóval
   * Kiszolgáló elérhető – Ellenőrzi, hogy a VPN-kiszolgáló válaszol-e vagy sem
3. Ha a tesztek bármelyike sikertelen, lépjen kapcsolatba a hálózati rendszergazdával a probléma megoldásához.
4. A következő szakasz bemutatja, hogyan gyűjtheti össze a naplókat, ha szükséges.

## <a name="collect-client-log-files"></a><a name="logfiles"></a>Ügyfélnapló-fájlok gyűjtése

Gyűjtse össze a VPN-ügyfél naplófájljait. A naplófájlok at lehet küldeni a támogatás / rendszergazda egy módszert az Ön által választott. Például e-mail.

1. Kattintson a "..." a diagnosztikát futtatni kívánt profil mellett. Válassza **a Diagnosztizálás -> Naplók megjelenítése könyvtárat.**

   ![naplók megjelenítése](./media/troubleshoot-ad-vpn-client/4.png)
2. A Windows Intéző megnyílik a naplófájlokat tartalmazó mappába.

   ![fájl megtekintése](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>További lépések

További információ: [Azure Active Directory-bérlő létrehozása az Azure AD-hitelesítést használó P2S Open VPN-kapcsolatokhoz című témakörben.](openvpn-azure-ad-tenant.md)