---
title: Az alkalmazásproxy-alkalmazáshoz szükséges tűzfal portok |} A Microsoft Docs
description: Ismerje meg, milyen portokat megfelelő működéséhez az Azure AD-alkalmazásproxy megnyitásához
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
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
ms.openlocfilehash: ed618f2e1c8e8c975db38f15f50468c5d64016f7
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54464578"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Az alkalmazásproxy-alkalmazáshoz szükséges tűzfal portok

Tekintse meg a szükséges portok és a függvény az egyes portok teljes listáját, az Előfeltételek című szakaszában talál a [alkalmazásproxy dokumentáció](application-proxy-add-on-premises-application.md). Vegye figyelembe, hogy az alkalmazásproxy csak kimenő portokat használ.

Azt is ellenőrizze, hogy rendelkezik-e a szükséges portok megnyitásához nyissa meg az összes a [összekötő portok vizsgálati eszköz](https://aadap-portcheck.connectorporttest.msappproxy.net/) a helyszíni hálózatból. További zöld jelöljük azt jelenti, hogy a nagyobb rugalmasság. 

## <a name="app-proxy-regions"></a>Alkalmazásproxy-régiók

Dolgozunk a oly módon, hogy tudja, melyik ezekben a régiókban kell lennie a zöld, az Ön számára. Egyelőre ellenőrizze, hogy azok minden. USA középső Régiójában is el kell, függetlenül attól, mely régió.

Győződjön meg arról, hogy az eszközt a megfelelő eredményeket ad meg, hogy ne felejtse el:

-   Az eszköz a böngészőben nyissa meg a kiszolgálón, amelyre telepítve van az összekötő.

-   Győződjön meg arról, hogy minden olyan proxyk vagy a tűzfalak az összekötő alkalmazandó is érvényesek, ezen a weblapon. Ezt megteheti az Internet Explorerben a **beállítások**  - &gt; **Internetbeállítások**  - &gt; **kapcsolatok**  - &gt; **Lan-beállítások**. Ezen a lapon láthatja a mezőben "Használatát Proxy Server számára a helyi hálózaton". Válassza ezt a jelölőnégyzetet, és a proxykiszolgáló címét helyezze az "Address" mezőbe.

## <a name="next-steps"></a>További lépések
[Az Azure AD-alkalmazásproxy-összekötők ismertetése](application-proxy-connectors.md)
