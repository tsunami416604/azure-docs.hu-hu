---
title: Hitelesítés a bérlőn – Azure Resource Manager
description: Ismerteti, hogyan Azure Resource Manager kezeli a hitelesítési kérések bérlők között.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: tomfitz
ms.openlocfilehash: 6554c05f40f580a6d7ae086e1d09834298f86621
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54108376"
---
# <a name="authenticate-requests-across-tenants"></a>Kérelmek hitelesítése a bérlőn

Több-bérlős alkalmazás létrehozásakor, szükség lehet az erőforrásra, amely a különböző bérlők hitelesítési kérések kezelésére. Egy gyakori forgatókönyv esetén, egy bérlő virtuális gép csatlakoztatnia kell a virtuális hálózat egy másik bérlőben. Az Azure Resource Manager biztosít egy fejléc értéke a különböző bérlők küldött kérelmek hitelesítéséhez kiegészítő jogkivonatok tárolására.

## <a name="header-values-for-authentication"></a>Hitelesítési fejléc érték

A kérelem a következő hitelesítési fejléc értékekkel rendelkezik:

| Fejléc neve | Leírás | Példaérték |
| ----------- | ----------- | ------------ |
| Engedélyezés | Elsődleges jogkivonat | Tulajdonosi &lt;elsődleges-token&gt; |
| x-ms-engedélyezési – kiegészítő | Kiegészítő jogkivonatok | Tulajdonosi &lt;– kiegészítő-token1&gt;; EncryptedBearer &lt;– kiegészítő-token2&gt;; Tulajdonosi &lt;– kiegészítő-token3&gt; |

A kiegészítő fejléc legfeljebb három kiegészítő jogkivonatok képes tárolni. 

A kód a több-bérlős alkalmazás a hitelesítési token beszerzése a más bérlők számára, és a kiegészítő fejlécekben tárolja őket. Az összes a tokenek az ugyanazon felhasználó vagy alkalmazás kell lennie. A felhasználónak vagy alkalmazásnak kell meghívót kapott a következőként vendégként a más bérlők számára.

## <a name="processing-the-request"></a>A kérelem feldolgozása

Ha az alkalmazás elküld egy kérelmet a Resource Managerhez, a kérelem fut az identitás a elsődleges jogkivonat. Az elsődleges jogkivonat érvényes, és a fennmaradó kell lennie. Ez a token is kezelheti az előfizetést egy bérlőhöz kell lennie.

Amikor a kérelem egy erőforrásra hivatkozik, a másik bérlőben, Resource Manager ellenőrzi a kiegészítő jogkivonatok meghatározni, ha a kérés dolgozhassák fel. A fejlécben szereplő összes kiegészítő jogkivonatok érvényes és lejárt kell lennie. Ha bármely jogkivonat lejárt, a Resource Manager 401-es válaszkódot adja vissza. A válasz tartalmazza az ügyfél a jogkivonat nem érvényes Azonosítóját és bérlőazonosítóját. Ha a kiegészítő fejléc érvényes jogkivonatot tartalmaz a bérlőhöz tartozó, a több-bérlős kérelem feldolgozása.

## <a name="next-steps"></a>További lépések
* Küldött hitelesítési kérések az Azure Resource Manager API-kkal kapcsolatos tudnivalókért lásd: [használható erőforrás-kezelő hitelesítési API az előfizetések hozzáféréséhez](resource-manager-api-authentication.md).
* Jogkivonatok kapcsolatos további információkért lásd: [Azure Active Directory hozzáférési jogkivonatok](/azure/active-directory/develop/access-tokens).
