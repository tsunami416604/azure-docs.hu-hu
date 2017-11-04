---
title: "A böngésző lapon és a keresési eredményeket megjelenítő lap címe"
description: "A cikk leírása, amely érkezési oldalak és a legtöbb keresési eredmények között megjelenik"
services: service-name
documentationcenter: dev-center-name
author: GitHub-alias-of-only-one-author
manager: manager-alias
editor: 
tags: comma-separates-additional-tags-if-required
ms.service: required
ms.devlang: may be required
ms.topic: article
ms.tgt_pltfrm: may be required
ms.workload: required
ms.date: mm/dd/yyyy
ms.author: Your MSFT alias or your full email address;semicolon separates two or more
ms.openlocfilehash: 1f86194857a54ed3a903c2696efc9b23d482c274
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="title-maximum-120-characters-target-the-primary-keyword"></a>Cím (legfeljebb 120 karakter, célként megadott elsődleges kulcsszó)
*A leírás másodlagos kulcsszavak 2-3 használható.*

*Válasszon egyet a következő nyilatkozatok a forgatókönyvtől függően. Ha a cikk telepítési modell független, figyelmen kívül hagyja ezt.*

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]klasszikus üzembe helyezési modellben.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[!INCLUDE [learn-about-deployment-models](../../learn-about-deployment-models-both-include.md)]

## <a name="summary-optional-especially-when-the-article-is-short"></a>(Nem kötelező, különösen akkor, ha a cikk rövid) összegzés
* *Röviden ismertesse a problémát.*
* *Az összefoglaló szakasz célszerű azoktól, amelyeket a cím a különböző kulcsszavak használhatják, de győződjön meg arról, hogy nem nagyon Terjengős. A mondatok flow is kell, és könnyen érthető.*
* *(Nem kötelező) – a kivételek a megfelelő helyzetek, amelyekre nem vonatkozik ez a cikk a listában. Például "Linux/OSS forgatókönyvek nem kezelt ebben a cikkben".*

*Ha ez a cikk a számlázási témakör, a következő Megjegyzés: a (az alábbi megjegyzést némileg eltérnek a Ez a cikk alján) a következők:*

> [!NOTE]
> Ha a cikk olvasása során bármikor további segítségre lenne szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldásához.
> 
> 

*Ha nem a számlázási cikkében, használja a következő hivatkozást:*
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Jelenség
* *Milyen műveletek lenne a felhasználó megpróbál végrehajtani?*
* *Melyek voltak sikertelenek?*
* *Milyen rendszerek és szoftverek lenne a felhasználó használja?*
* *Milyen hiba volt kimutatták, üzenetek?*
* *Képernyőkép felvétele, ha lehetséges.*

## <a name="cause"></a>Ok
* *Mi okozza a problémát.*

## <a name="solution"></a>Megoldás
* *Ha lehetséges adja hozzá a pillanatképek.*
* *Ha nincsenek a több megoldások, helyezze őket sorrendjében összetettségét, és utasításokkal láthatja el közülük kiválasztásával.*

| <em>1. verzió: A cikk: független a központi telepítési modell</em> | <em>2. verziójú: Resource Manager és klasszikus lépései megegyeznek nagymértékben</em> | <em>3. verzió: Többnyire eltérőek a Resource Manager és klasszikus lépéseit. <br />Ebben az esetben használja a <a href="https://github.com/Azure/azure-content-pr/blob/master/contributor-guide/custom-markdown-extensions.md#simple-selectors">egyszerű választók módszer a Githubon</a>. <br />Megjegyzés: Virtuális gép cikkek ARM kivételekhez és ne használja az ARM/klasszikus választó.</em> |
|:--- |:--- |:--- |
| <p><h3>1 megoldás</h3><em>(a legegyszerűbb és leghatékonyabb)</em></p><ol><li>[1. lépés]</li><li>[2. lépés]</li></ol><p><h3>Megoldás 2</h3><em>(a kisebb egyszerű és hatékony)</em></p><ol><li>[1. lépés]</li><li>[2. lépés]</li></ol><br /><br /><br /><br /><br /><br /><br /><br /> |<p><h3>1 megoldás</h3><em>(a legegyszerűbb és leghatékonyabb)</em></p><ol><li>[1. lépés]</li><li>Ha a klasszikus üzembe helyezési modellt használ [ehhez információ].<br />Ha a Resource Manager üzembe helyezési modellben [ehhez információ].</li><li>[3. lépés]</li></ol><p><h3>Megoldás 2</h3><em>(a kisebb egyszerű és hatékony)</em></p><ol><li>[1. lépés]</li><li>Ha a klasszikus üzembe helyezési modellt használ [ehhez információ].<br />Ha a Resource Manager üzembe helyezési modellben [ehhez információ].</li><li>[3. lépés]</li></ol> |<img src="media/markdown-template-for-support-articles-symptom-cause-resolution/rm-classic.png" alt="ARM-Classic"><p><h3>1 megoldás</h3><em>(a legegyszerűbb és leghatékonyabb)</em></p><ol><li>[1. lépés]</li><li>[2. lépés]</li></ol><p><h3>Megoldás 2</h3><em>(a kisebb egyszerű és hatékony)</em></p><ol><li>[1. lépés]</li><li>[2. lépés]</li></ol><br /><br /><br /><br /> |

## <a name="next-steps"></a>Következő lépések
*Ezt a szakaszt, ha nincsenek konkrét 1 -3, a felhasználó gyorsabban magas vonatkozó lépéseket tartalmazza. Ha nem a következő lépést törlése. Ez az vonatkozó hivatkozásokat. Ha további lépések mutató hivatkozásokat tartalmaz, győződjön meg arról, annak magyarázata, hogy miért a következő lépésekre vonatkozó / fontos tartalmazza.*

*Ha ez a cikk a számlázási témakör, a következő Megjegyzés: a (az alábbi megjegyzést némileg eltérnek a elején Ez a cikk) a következők:*

> [!NOTE]
> Ha további kérdései is vannak, kérjük, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
> 
> 

