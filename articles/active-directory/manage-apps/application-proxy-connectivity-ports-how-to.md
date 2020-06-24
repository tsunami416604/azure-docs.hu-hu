---
title: Az alkalmazásproxy-alkalmazásokhoz szükséges tűzfal-portok megnyitása
description: Megtudhatja, milyen portokat kell megnyitni az Azure-AD Application Proxy megfelelő működéséhez
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 192e721e25612019649fed86b44135fb6bfef89c
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2020
ms.locfileid: "84764706"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Az alkalmazásproxy-alkalmazásokhoz szükséges tűzfal-portok megnyitása

Ha meg szeretné tekinteni a szükséges portok és az egyes portok funkcióinak teljes listáját, olvassa el az [alkalmazásproxy dokumentációjának](application-proxy-add-on-premises-application.md)előfeltételek című szakaszát. vegye figyelembe, hogy az alkalmazásproxy csak kimenő portokat használ.

Azt is ellenőrizheti, hogy az összes szükséges portot megnyitotta-e. Ehhez nyissa meg az [összekötő portok tesztelése eszközt](https://aadap-portcheck.connectorporttest.msappproxy.net/) a helyszíni hálózatról. A további zöld pipa nagyobb rugalmasságot jelent. 

## <a name="app-proxy-regions"></a>Alkalmazásproxy-régiók

Dolgozunk azon, hogy tudd, hogy mely régióknak kell Zöldek az Ön számára. Egyelőre győződjön meg róla, hogy az összes. Az USA középső régiója is szükséges, függetlenül attól, hogy melyik régióban található.

Győződjön meg arról, hogy az eszköz biztosítja a megfelelő eredményeket:

-   Nyissa meg az eszközt egy böngészőben azon a kiszolgálón, amelyen az összekötőt telepítette.

-   Győződjön meg arról, hogy az összekötőre érvényes proxyk vagy tűzfalak is érvényesek erre az oldalra. Ez az Internet Explorerben a **Beállítások**  - &gt; **Internetbeállítások**  - &gt; **kapcsolatok**  - &gt; **LAN-beállítások**lehetőségre kattintva végezhető el. Ezen az oldalon a "proxykiszolgáló használata a helyi hálózathoz" mező látható. Jelölje be ezt a jelölőnégyzetet, és helyezze el a proxy címe mezőt a "címek" mezőbe.

## <a name="next-steps"></a>További lépések
[Az Azure AD Application Proxy-összekötők ismertetése](application-proxy-connectors.md)
