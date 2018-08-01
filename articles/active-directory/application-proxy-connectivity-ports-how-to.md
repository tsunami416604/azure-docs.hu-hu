---
title: Az alkalmazásproxy-alkalmazáshoz szükséges tűzfal portok |} A Microsoft Docs
description: Ismerje meg, milyen portokat megfelelő működéséhez az Azure AD-alkalmazásproxy megnyitásához
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 6c26fb7cf88a44e7392d6be934d9dfae43b31c08
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39362779"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Az alkalmazásproxy-alkalmazáshoz szükséges tűzfal portok

Tekintse meg a szükséges portok és a függvény az egyes portok teljes listáját, az Előfeltételek című szakaszában talál a [alkalmazásproxy dokumentáció](manage-apps/application-proxy-enable.md). Vegye figyelembe, hogy az alkalmazásproxy csak kimenő portokat használ.

Azt is ellenőrizze, hogy rendelkezik-e a szükséges portok megnyitásához nyissa meg az összes a [összekötő portok vizsgálati eszköz](https://aadap-portcheck.connectorporttest.msappproxy.net/) a helyszíni hálózatból. További zöld jelöljük azt jelenti, hogy a nagyobb rugalmasság. 

## <a name="app-proxy-regions"></a>Alkalmazásproxy-régiók

Dolgozunk a oly módon, hogy tudja, melyik ezekben a régiókban kell lennie a zöld, az Ön számára. Egyelőre ellenőrizze, hogy azok minden. USA középső Régiójában is el kell, függetlenül attól, mely régió.

Győződjön meg arról, hogy az eszközt a megfelelő eredményeket ad meg, hogy ne felejtse el:

-   Az eszköz a böngészőben nyissa meg a kiszolgálón, amelyre telepítve van az összekötő.

-   Győződjön meg arról, hogy minden olyan proxyk vagy a tűzfalak az összekötő alkalmazandó is érvényesek, ezen a weblapon. Ezt megteheti az Internet Explorerben a **beállítások**  - &gt; **Internetbeállítások**  - &gt; **kapcsolatok**  - &gt; **Lan-beállítások**. Ezen a lapon láthatja a mezőben "Használatát Proxy Server számára a helyi hálózaton". Válassza ezt a jelölőnégyzetet, és a proxykiszolgáló címét helyezze az "Address" mezőbe.

## <a name="next-steps"></a>További lépések
[Az Azure AD-alkalmazásproxy-összekötők ismertetése](manage-apps/application-proxy-connectors.md)
