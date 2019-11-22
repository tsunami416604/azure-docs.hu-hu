---
title: Az alkalmazásproxy-alkalmazásokhoz szükséges tűzfal-portok megnyitása
description: Ismerje meg, milyen portokat megfelelő működéséhez az Azure AD-alkalmazásproxy megnyitásához
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275553"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Az alkalmazásproxy-alkalmazáshoz szükséges tűzfal portok

Tekintse meg a szükséges portok és a függvény az egyes portok teljes listáját, az Előfeltételek című szakaszában talál a [alkalmazásproxy dokumentáció](application-proxy-add-on-premises-application.md). Vegye figyelembe, hogy az alkalmazásproxy csak kimenő portokat használ.

Azt is ellenőrizheti, hogy az összes szükséges portot megnyitotta-e. Ehhez nyissa meg az [összekötő portok tesztelése eszközt](https://aadap-portcheck.connectorporttest.msappproxy.net/) a helyszíni hálózatról. További zöld jelöljük azt jelenti, hogy a nagyobb rugalmasság. 

## <a name="app-proxy-regions"></a>Alkalmazásproxy-régiók

Dolgozunk a oly módon, hogy tudja, melyik ezekben a régiókban kell lennie a zöld, az Ön számára. Egyelőre ellenőrizze, hogy azok minden. USA középső Régiójában is el kell, függetlenül attól, mely régió.

Győződjön meg arról, hogy az eszközt a megfelelő eredményeket ad meg, hogy ne felejtse el:

-   Az eszköz a böngészőben nyissa meg a kiszolgálón, amelyre telepítve van az összekötő.

-   Győződjön meg arról, hogy minden olyan proxyk vagy a tűzfalak az összekötő alkalmazandó is érvényesek, ezen a weblapon. Ezt az Internet Explorerben a **beállítások** -&gt; **internet beállítások** -&gt; **kapcsolatok** -&gt; **LAN-beállítások**menüpontban teheti meg. Ezen a lapon láthatja a mezőben "Használatát Proxy Server számára a helyi hálózaton". Válassza ezt a jelölőnégyzetet, és a proxykiszolgáló címét helyezze az "Address" mezőbe.

## <a name="next-steps"></a>Következő lépések
[Az Azure AD-alkalmazásproxy-összekötők ismertetése](application-proxy-connectors.md)
