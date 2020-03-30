---
title: Alkalmazásproxy-alkalmazáshoz szükséges tűzfalportok megnyitása
description: Megtudhatja, hogy az Azure AD alkalmazásproxy megfelelő működéséhez milyen portokat kell megnyitnia
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b676508c73ff0233526f19b865bb9e4f7b80f12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275553"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Alkalmazásproxy-alkalmazáshoz szükséges tűzfalportok megnyitása

A szükséges portok teljes listáját és az egyes portok funkcióját az [Alkalmazásproxy dokumentációjának](application-proxy-add-on-premises-application.md)előfeltételei című szakaszában találja. Vegye figyelembe, hogy az alkalmazásproxy csak kimenő portokat használ.

Azt is ellenőrizheti, hogy az összes szükséges port nyitva van-e, ha megnyitja az [összekötő portok teszteszközét](https://aadap-portcheck.connectorporttest.msappproxy.net/) a helyszíni hálózatról. Több zöld pipa nagyobb rugalmasságot jelent. 

## <a name="app-proxy-regions"></a>Alkalmazásproxy-területek

Dolgozunk azon a módszeren, hogy tudassam Önnel, hogy e régiók közül melyiknek kell zöldnek lennie az Ön számára. Egyelőre győződj meg róla, hogy mind azok. Az USA központja is szükséges, függetlenül attól, hogy melyik régióban van.

Annak érdekében, hogy az eszköz a megfelelő eredményeket adja, győződjön meg róla, hogy:

-   Nyissa meg az eszközt egy böngészőben abból a kiszolgálóból, amelyen az összekötőt telepítette.

-   Győződjön meg arról, hogy az összekötőre vonatkozó proxyk vagy tűzfalak is érvényesek erre az oldalra. Ezt az Internet Explorer programban teheti meg az **Internetbeállítások**  - &gt; **kapcsolatok**  - &gt; helyi beállításai **című** **lapon.**  - &gt; Ezen az oldalon a "Proxykiszolgáló használata a helyi hálózathoz" mező látható. Jelölje be ezt a jelölőnégyzetet, és helyezze a proxycímet a "Cím" mezőbe.

## <a name="next-steps"></a>További lépések
[Az Azure AD alkalmazásproxy-összekötők megismerése](application-proxy-connectors.md)
