---
title: Hitelesítés bérlőkön keresztül
description: Ez a témakör azt ismerteti, hogy az Azure Resource Manager hogyan kezeli a bérlők közötti hitelesítési kérelmeket.
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 7a13ba6f6cbfc10c52484c45e4011da7a0d8ee4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478824"
---
# <a name="authenticate-requests-across-tenants"></a>Kérelmek hitelesítése a bérlők között

Több-bérlős alkalmazás létrehozásakor előfordulhat, hogy a különböző bérlőkben lévő erőforrások hitelesítési kérelmeit kell kezelnie. Gyakori forgatókönyv, amikor egy virtuális gép az egyik bérlőben kell csatlakoznia egy virtuális hálózat egy másik bérlőben. Az Azure Resource Manager egy fejlécértéket biztosít a kiegészítő jogkivonatok tárolására a kérelmek hitelesítéséhez a különböző bérlők számára.

## <a name="header-values-for-authentication"></a>A hitelesítés fejlécértékei

A kérelem a következő hitelesítési fejlécértékeket tartalmazza:

| Fejléc neve | Leírás | Példaérték |
| ----------- | ----------- | ------------ |
| Engedélyezés | Elsődleges jogkivonat | Tulajdonos &lt;elsődleges token&gt; |
| x-ms-engedélyezési segéd | Kiegészítő tokenek | Bemutatóra &lt;szóló&gt;kiegészítő-token1 , &lt;EncryptedBearer&gt;auxiliary-token2 , Bearer &lt;auxiliary-token3&gt; |

A kiegészítő fejléc legfeljebb három kiegészítő jogkivonatot képes tárolni. 

A több-bérlős alkalmazás kódjában a hitelesítési jogkivonatot más bérlők számára, és tárolja őket a kiegészítő fejlécek. Az összes jogkivonatnak ugyanattól a felhasználótól vagy alkalmazástól kell származnia. A felhasználót vagy alkalmazást vendégként meg kell hívni a többi bérlőhöz.

## <a name="processing-the-request"></a>A kérelem feldolgozása

Amikor az alkalmazás kérelmet küld az Erőforrás-kezelőnek, a kérelem az elsődleges jogkivonat identitása alatt fut. Az elsődleges jogkivonatnak érvényesnek és le nem jártnak kell lennie. Ez a jogkivonat egy olyan bérlőtől kell származnia, amely kezelheti az előfizetést.

Amikor a kérelem egy különböző bérlőből származó erőforrásra hivatkozik, az Erőforrás-kezelő ellenőrzi a kiegészítő jogkivonatokat annak meghatározásához, hogy a kérelem feldolgozható-e. A fejlécben lévő összes kiegészítő jogkivonatnak érvényesnek és le nem jártnak kell lennie. Ha bármelyik jogkivonat lejárt, az Erőforrás-kezelő egy 401-es válaszkódot ad vissza. A válasz tartalmazza az ügyfél-azonosítót és a bérlői azonosítót a jogkivonatból, amely nem érvényes. Ha a kiegészítő fejléc érvényes jogkivonatot tartalmaz a bérlő számára, a kereszt-bérlői kérelem feldolgozása.

## <a name="next-steps"></a>További lépések

* A hitelesítési kérelmekről a [Hitelesítési folyamatok és alkalmazásforgatókönyvek](../../active-directory/develop/authentication-flows-app-scenarios.md)című témakörben olvashat.
* A jogkivonatokról az [Azure Active Directory-hozzáférési jogkivonatok](../../active-directory/develop/access-tokens.md)című témakörben talál további információt.
