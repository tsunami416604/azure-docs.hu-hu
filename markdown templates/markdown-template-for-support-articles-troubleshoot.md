---
title: "42 karakter követ |} Microsoft Docs"
description: "A keresőmotorok a cím alatt jelenik meg. Itt további elegendő hellyel rendelkezik, több kulcsszót és egy leíró magyarázat, mint a cím használata"
services: service-name
documentationcenter: dev-center-name
author: GitHub-alias-of-only-one-author
manager: manager-alias
editor: 
tags: top-support-issue
ms.service: required
ms.devlang: may be required
ms.topic: article
ms.tgt_pltfrm: may be required
ms.workload: required
ms.date: mm/dd/yyyy
ms.author: Your MSFT alias or your full email address;semicolon separates two or more
ms.openlocfilehash: e791ff0377137557b6e9f38a8e1b6b35be948ab6
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2018
---
# <a name="title-maximum-120-characters-target-the-primary-keyword"></a>Cím (legfeljebb 120 karakter, célként megadott elsődleges kulcsszó)
*A leírás másodlagos kulcsszavak 2-3 használható.*

*Válasszon egyet a következő nyilatkozatok a forgatókönyvtől függően. Ha a cikk telepítési modell független, figyelmen kívül hagyja ezt.*

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]klasszikus üzembe helyezési modellben.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[!INCLUDE [learn-about-deployment-models](../../learn-about-deployment-models-both-include.md)]

[Nyitó bekezdés]

* *Röviden, hogy ez a cikk segít hibaelhárítása, és a közös legfelső szintű cause(s) adott hiba/hibák.*
* *A nyitó bekezdésben szereplő esetekben célszerű azoktól, amelyeket a cím a különböző kulcsszavak használhatják, de győződjön meg arról, hogy nem nagyon Terjengős. A mondatok flow is kell, és könnyen érthető.*
* *(Nem kötelező) – a kivételek a megfelelő helyzetek, amelyekre nem vonatkozik ez a cikk a listában. Például "Linux/OSS forgatókönyvek nem kezelt ebben a cikkben".*

A {hibák} |} {Problémákat} {nagyon általános reason}.

*Íme egy példa egy nyitó bekezdésben szereplő esetekben.*

*Csatlakozzon az Azure SQL Database megkísérlésekor a közös kapcsolatot észlelt hibák a következők:*

* *A felhasználó bejelentkezése sikertelen volt. A jelszó módosítása sikertelen volt.*
* *Jelszó érvényesítése sikertelen volt.*
* *Nem sikerült a hozzáférés hitelesítése a megadott előfizetéshez.*

*Ezek a hibák akkor fordulhat elő, mert nincs engedélye az adatforrás elérésére.*

*Ha ez a cikk a számlázási témakör, a következő Megjegyzés: a (az alábbi megjegyzést némileg eltérnek a Ez a cikk alján) a következők:*

> [!NOTE]
> Ha a cikk olvasása során bármikor további segítségre lenne szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldásához.
> 
> 

*Ha nem a számlázási cikkében, a következő hivatkozás a következők:*
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance-optional"></a>Hibaelhárítási útmutatót (nem kötelező)
* *Használja az ebben a szakaszban, ha a tábla között a ezek az útmutatások érvényesek.*
* *Ne nyissa meg a részleteket. Legyen magas szintű iránymutatásként kiszolgálásához.*

*Íme egy példa a hibaelhárítási útmutatót.*

*Általában mindaddig, amíg a hiba nem jelzi, "a kért Virtuálisgép-méret nem támogatott", mindig újból megpróbálkozhat egy későbbi időpontban elegendő erőforrás előfordulhat, hogy rendelkezik felszabadult fel a fürt a kérelem olyan módon. Ha a probléma a kért Virtuálisgép-méret nem támogatott, próbálkozzon egy másik Virtuálisgép-méretet; Ellenkező esetben az egyetlen lehetősége a kitűzési korlátozás távolítható el.*

## <a name="troubleshooting-steps"></a>Hibaelhárítási útmutató
*Sorrendjében használhatósága és az egyszerűség, ezért a legegyszerűbb megoldás listában, a hatékony és a hasznos megoldás kell elejére.*

*Válassza ki, amelyek érvényesek az Ön helyzetéhez verziók egyikét.*

| <em>1. verzió: A cikk: független a központi telepítési modell</em> | <em>2. verziójú: Resource Manager és klasszikus lépései megegyeznek nagymértékben</em> | <em>3. verzió: Többnyire eltérőek a Resource Manager és klasszikus lépéseit. <br />Ebben az esetben használja a <a href="https://github.com/Azure/azure-content-pr/blob/master/contributor-guide/custom-markdown-extensions.md#simple-selectors">egyszerű választók módszer a Githubon</a>. <br />Megjegyzés: A virtuális gép cikkeket összegyűjtő ARM kivételek, és ne használja az ARM/klasszikus választó.</em> |
|:--- |:--- |:--- |
| <p><h3>[1. probléma] \ |[Hiba 1]</h3><h4>Ok</h4>[OK Részletek]</p><p><h4>1 megoldás</h4><em>(a legegyszerűbb és leghatékonyabb)</em></p><ol><li>[1. lépés]</li><li>[2. lépés]</li></ol><p><h4>Megoldás 2</h4><em>(a kisebb egyszerű és hatékony)</em></p><ol><li>[1. lépés]</li><li>[2. lépés]</li></ol><p><h3>[Probléma 2] \ |[Hiba 2]</h3><h4>Ok</h4>[OK Részletek]</p><p><h4>1 megoldás</h4><em>(a legegyszerűbb és leghatékonyabb)</em></p><ol><li>[1. lépés]</li><li>[2. lépés]</li></ol><p><h4>Megoldás 2</h4><em>(a legegyszerűbb és leghatékonyabb)</em></p><ol><li>[1. lépés]</li><li>[2. lépés]</li></ol><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /> |

## <a name="next-steps"></a>További lépések
*Ezt a szakaszt, ha nincsenek konkrét 1 -3, a felhasználó gyorsabban magas vonatkozó lépéseket tartalmazza. Ha nem a következő lépést törlése. Ez az vonatkozó hivatkozásokat. Ha további lépések mutató hivatkozásokat tartalmaz, győződjön meg arról, annak magyarázata, hogy miért a következő lépésekre vonatkozó / fontos tartalmazza.*

*Ha ez a cikk a számlázási témakör, a következő Megjegyzés: a (az alábbi megjegyzést némileg eltérnek a elején Ez a cikk) a következők:*

> [!NOTE]
> Ha további kérdései is vannak, kérjük, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
> 
> 

