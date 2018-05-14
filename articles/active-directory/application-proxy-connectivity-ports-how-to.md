---
title: A tűzfal portok az alkalmazásproxy-alkalmazáshoz szükséges |} Microsoft Docs
description: Megtudhatja, mi lehet megnyitni az Azure AD-alkalmazásproxy megfelelő működéséhez a portok
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 0b7b8642d29a815ba40655dd9cab519a2b12ecd8
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>A tűzfal portok az alkalmazásproxy-alkalmazáshoz szükséges

A szükséges portokon és a függvény minden egyes port teljes listájának megtekintéséhez az Előfeltételek című a [alkalmazásproxy dokumentáció](manage-apps/application-proxy-enable.md). Vegye figyelembe, hogy a alkalmazásproxy csak használja-e a kimenő portokat.

Azt is ellenőrizheti, hogy rendelkezik-e a szükséges portok megnyitásához nyissa meg az összes a [összekötő portok vizsgálati eszköz](https://aadap-portcheck.connectorporttest.msappproxy.net/) a helyszíni hálózatról. További zöld jelölők azt jelenti, hogy a nagyobb rugalmasság. 

## <a name="app-proxy-regions"></a>Alkalmazás Proxy régiók

Jelenleg is dolgozunk oly módon, hogy tudja, amelyek e régiók kell lennie a zöld meg. Most győződjön meg arról, hogy az összes. USA középső RÉGIÓJA is függetlenül melyik régióban jogkörrel rendelkezik arra szükség.

Győződjön meg arról, hogy az eszköz a jobb eredmények lehetővé teszi, hogy ügyeljen arra, hogy:

-   Nyissa meg az eszköz a böngésző a kiszolgálóról, ahol az összekötőt telepítette.

-   Győződjön meg arról, hogy bármely proxyk vagy az összekötő alkalmazandó tűzfalak is alkalmazzák ezen a lapon. Ezt megteheti az Internet Explorerben nyissa meg **beállítások**  - &gt; **Internetbeállítások**  - &gt; **kapcsolatok**  - &gt; **Lan-beállítások**. Ezen a lapon tekintse meg a "Hálózati használata az egy Proxy kiszolgáló esetében a" mező. Válassza ezt a jelölőnégyzetet, és a proxykiszolgáló címét helyezze az "Address" mezőbe.

## <a name="next-steps"></a>További lépések
[Az Azure AD-alkalmazásproxy összekötők ismertetése](application-proxy-understand-connectors.md)
