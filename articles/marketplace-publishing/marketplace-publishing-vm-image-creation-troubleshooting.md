---
title: Virtuális merevlemez létrehozása során kapcsolatos gyakori hibák elhárítása |} Microsoft Docs
description: A gyakori hibaelhárítási kérdésekre adott válaszok és a virtuális merevlemez létrehozása során.
services: Azure Marketplace
documentationcenter: ''
author: msmbaldwin
manager: ''
editor: ''
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 09/26/2016
ms.author: mbaldwin
ms.openlocfilehash: 65361ad5bd7c3311c428b64b8476ec8f2ea2d17b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
ms.locfileid: "29941601"
---
# <a name="how-to-troubleshoot-common-issues-encountered-during-vhd-creation"></a>Virtuális merevlemez létrehozása során észlelt közös kapcsolatos problémák elhárítása
Ez a cikk az Azure piactér közzétevő vagy a közös rendszergazda problémát tapasztal vagy közös kérdése van a közzététel vagy a virtuális gép solution(s) kezelése közben segítségével valósul meg.

1. Hogyan változtathatom meg a gazdagép neve?
   
    Virtuális gép létrehozása után a felhasználók a gazdagép neve nem lehet frissíteni.
2. Hogyan lehet a távoli asztal szolgáltatás vagy a bejelentkezés jelszó alaphelyzetbe állítása?
   
   * [Útmutató a Windows virtuális gép](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
   * [Útmutató a Linux virtuális gép](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
3. Hogyan lehet új ssh tanúsítványokat létrehozni?
   
   Tekintse meg a hivatkozásra: [https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
4. Egy nyitott VPN-tanúsítványt konfigurálhat, hogyan?
   
   Tekintse meg a hivatkozásra: [https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/](https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/)
5. Újdonságok a Microsoft server szoftver futtatásához a Microsoft Azure virtuális gép környezetben (infrastruktúra-,--szolgáltatás) a támogatási szabályzata?
   
   Tekintse meg a hivatkozásra: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
6. Virtuális gépek rendelkeznek egyedi azonosítót?
   
   Azure Azure virtuális gép egyedi Azonosítót a minden virtuális gép kódolja. A részleteket a ez blog és dokumentáció található.
7. A virtuális gép hogyan kezelhetők az egyéni parancsprogramok futtatására szolgáló bővítmény a indítási feladat?
   
   Tekintse meg a hivatkozásra: [https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)
8. Hogyan lehet a virtuális gép létrehozása az Azure-portálról a prémium szintű Storage feltöltött virtuális merevlemez használatával?
   
   Ez a funkció még nem támogatott.
9. Az Azure piactéren támogatja a 32 bites alkalmazást?
   
   A hivatkozás a támogatási szabályzata kapcsolatos részletekért tekintse meg: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
10. Minden alkalommal, amikor a képet készíteni a VHD-k kísérlet, a hibaüzenet jelenik meg: ". Virtuális merevlemez már regisztrálva van lemezképtárban erőforrásként"PowerShell. I nem hozta létre a előtt képet és nem volt található ilyen nevű képet az Azure-ban. Hogyan lehet elhárítani ezt?
    
    Ez általában fordulhat elő, ha a felhasználó létesített egy virtuális Gépet a virtuális merevlemezről, de a zárolási, hogy a virtuális merevlemezen. Győződjön meg arról, hogy van-e nincs lefoglalva a virtuális merevlemez virtuális gép. Ha a hiba továbbra is fennáll, majd léptesse elő egy támogatási jegy ezen a hivatkozáson keresztül vagy a közzétételi portal vonatkozó ez (leírás 11 kérdésre adott válasz szerepel).