---
title: 42 karakter követ |} A Microsoft Docs
description: Keresés keresőmotor szerint a cím alatt jelenik meg. Ha több helyre van itt, használjon további kulcsszavakat és a egy leíró magyarázat, mint a cím
services: service-name
documentationcenter: dev-center-name
author: GitHub-alias-of-only-one-author
manager: manager-alias
editor: ''
tags: top-support-issue
ms.service: required
ms.devlang: may be required
ms.topic: article
ms.tgt_pltfrm: may be required
ms.workload: required
ms.date: mm/dd/yyyy
ms.author: Your MSFT alias or your full email address;semicolon separates two or more
ms.openlocfilehash: 46a7bc52e94189b932b0475e80f55d880a2e3ee9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58191678"
---
# <a name="title-maximum-120-characters-target-the-primary-keyword"></a>Cím (a cél elsődleges kulcsszó legfeljebb 120 karakter)
*Használja a 2-3 másodlagos kulcsszavak leírásában.*

*Válasszon egyet a következő nyilatkozatok a forgatókönyvtől függően. Ha a cikkben üzembe helyezési modell független, figyelmen kívül hagyja ezt.*

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] klasszikus üzemi modellben.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[Nyitó bekezdés]

* *Röviden, ez a cikk segít hibaelhárítása, és a közös legfelső szintű cause(s) konkrét hiba/hibák meghatározásához.*
* *A nyitó bekezdés remek azoktól, amelyeket a cím a különböző kulcsszó, de győződjön meg arról, hogy nem nagyon Terjengős. A mondatok flow is kell, és könnyen érthető.*
* *(Nem kötelező) – a kivételek listázza a kapcsolódó forgatókönyveket, amelyek nem szerepelnek ebben a cikkben. Például "Linux/nyílt Forráskódú forgatókönyvek nem terjed ki ebben a cikkben".*

Ezen {errors} |} {Problémákat} {nagyon általános reason}.

*Íme egy példa egy nyitó bekezdés.*

*Ha az Azure SQL Database-adatbázishoz csatlakozni próbál, a közös kapcsolati hibákat tapasztal a következők:*

* *A bejelentkezés sikertelen volt, a felhasználó számára. A jelszó módosítása sikertelen volt.*
* *Jelszó érvényesítése sikertelen volt.*
* *Nem sikerült engedélyezni a hozzáférést a megadott előfizetéshez.*

*Ezek a hibák akkor fordul elő, mert nincs engedélye az adatforrás eléréséhez.*

*Ha ez a cikk a számlázási témakörben, az alábbi megjegyzésben (az alábbi megjegyzést a némileg eltérnek a Ez a cikk alján) a következők:*

> [!NOTE]
> Ha a cikk olvasása során bármikor további segítségre lenne szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldásához.
> 
> 

*Ha nem, számlázási cikk, tartalmazza a következő hivatkozást:*
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance-optional"></a>Hibaelhárítási útmutató (nem kötelező)
* *Ez a szakasz akkor használja, ha az útmutató a tábla között vonatkozik.*
* *Ne nyissa meg a részleteket. Folyamatosan magas szintű iránymutatásként kiszolgálása érdekében.*

*Íme egy példa a hibaelhárítási útmutatót.*

*Általánosságban elmondható mindaddig, amíg a hibát jelzi, hogy "a kért Virtuálisgép-méret nem támogatott", mindig újra egy későbbi időpontban, elegendő erőforrás előfordulhat, hogy rendelkezik felszabadult fel a fürt a kérelem befogadásához. Ha a probléma a kért Virtuálisgép-méret nem támogatott, próbálkozzon egy másik virtuális gép méretét Ellenkező esetben az egyetlen lehetőség, hogy távolítsa el a rögzítési korlátozást.*

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
*A használhatóság és az egyszerűség, ami azt jelenti, a legegyszerűbb sorrendjében megoldások listája, a hatékony és a hasznos megoldás első kell végezze.*

*Válassza ki a alkalmazni az adott helyzethez verziók egyikét.*

| <em>1. verzió: A cikk az üzembe helyezési modell független</em> | <em>2. verzió: A Resource Manager és klasszikus lépések ugyanazok, nagymértékben</em> | <em>3. verzió: A lépések a Resource Manager és klasszikus többnyire eltérőek. <br />Ebben az esetben használja a <a href="https://github.com/Azure/azure-content-pr/blob/master/contributor-guide/custom-markdown-extensions.md#simple-selectors">egyszerű választók módszer a GitHub</a>. <br />Megjegyzés: Virtuális gép cikkeket ARM kivételt jelentenek ez alól, és ne használja az ARM/klasszikus választó.</em> |
|:--- |:--- |:--- |
| <p><h3>[Probléma 1] \ |[Hiba 1]</h3><h4>Ok</h4>[Részletek OK]</p><p><h4>1. megoldás</h4><em>(a legegyszerűbb és leghatékonyabb)</em></p><ol><li>[1. lépés]</li><li>[2. lépés]</li></ol><p><h4>2. megoldás</h4><em>(a kevésbé egyszerű és hatékony)</em></p><ol><li>[1. lépés]</li><li>[2. lépés]</li></ol><p><h3>[Probléma 2] \ |[Hiba 2]</h3><h4>Ok</h4>[Részletek OK]</p><p><h4>1. megoldás</h4><em>(a legegyszerűbb és leghatékonyabb)</em></p><ol><li>[1. lépés]</li><li>[2. lépés]</li></ol><p><h4>2. megoldás</h4><em>(a legegyszerűbb és leghatékonyabb)</em></p><ol><li>[1. lépés]</li><li>[2. lépés]</li></ol><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /> |

## <a name="next-steps"></a>További lépések
*Ezt a szakaszt, ha nincsenek konkrét 1 -3, a felhasználó megtétele magas vonatkozó következő lépések tartalmazzák. Ha a következő lépést nem törölhető. Ez nem egy helyen történő mutató hivatkozásokat. Ha további lépések mutató hivatkozásokat is, ügyeljen arra, hogy annak magyarázata, hogy miért érdemes a következő lépések-e a megfelelő / fontos tartalmazza.*

*Ha ez a cikk a számlázási témakörben, az alábbi megjegyzésben (az alábbi megjegyzést a némileg eltérnek a elején található ez a cikk) a következők:*

> [!NOTE]
> Ha további kérdései is vannak, kérjük, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
> 
> 

