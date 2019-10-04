---
title: Hitelesítés a bérlők között – Azure Resource Manager
description: Leírja, hogyan kezeli a Azure Resource Manager a különböző bérlők hitelesítési kérelmeit.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: tomfitz
ms.openlocfilehash: 625a17156eaf199af0d51151c6fd37769b8f7b4a
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848753"
---
# <a name="authenticate-requests-across-tenants"></a>Kérelmek hitelesítése a bérlők között

Több-bérlős alkalmazások létrehozásakor előfordulhat, hogy a különböző bérlők erőforrásaihoz tartozó hitelesítési kérelmeket kell kezelnie. Gyakori eset az, amikor az egyik bérlőn lévő virtuális gépnek egy másik bérlőben lévő virtuális hálózathoz kell csatlakoznia. A Azure Resource Manager a kisegítő tokenek tárolására szolgáló fejléc-értéket biztosít a különböző bérlőknek küldött kérések hitelesítéséhez.

## <a name="header-values-for-authentication"></a>A hitelesítés fejlécének értékei

A kérelem a következő hitelesítési fejléc-értékekkel rendelkezik:

| Fejléc neve | Leírás | Példaérték |
| ----------- | ----------- | ------------ |
| Authorization | Elsődleges jogkivonat | &lt;Tulajdonos elsődleges – token&gt; |
| x-ms-authorization-auxiliary | Kiegészítő tokenek | &lt;&gt; &lt;Tulajdonos-&gt;kiegészítő – token1, EncryptedBearer-kiegészítő – token2, tulajdonosi segéd – token3 &lt;&gt; |

A kiegészítő fejléc legfeljebb három kiegészítő tokent tud tárolni. 

A több-bérlős alkalmazás kódjában szerezze be a többi bérlő hitelesítési jogkivonatát, és tárolja azokat a kiegészítő fejlécekben. Az összes tokennek ugyanahhoz a felhasználóhoz vagy alkalmazáshoz kell tartoznia. A felhasználót vagy alkalmazást vendégként kell meghívni a többi bérlőnek.

## <a name="processing-the-request"></a>A kérelem feldolgozása

Amikor az alkalmazás egy kérelmet küld a Resource managernek, a rendszer az elsődleges jogkivonat identitása alatt futtatja a kérést. Az elsődleges tokennek érvényesnek és lejártnak kell lennie. Ennek a tokennek egy olyan bérlőtől kell származnia, amely képes kezelni az előfizetést.

Ha a kérelem egy másik bérlőtől származó erőforrásra hivatkozik, a Resource Manager ellenőrzi a kisegítő jogkivonatokat annak megállapításához, hogy a kérés feldolgozható-e. A fejlécben szereplő összes kiegészítő tokennek érvényesnek és lejártnak kell lennie. Ha bármelyik jogkivonat lejárt, a Resource Manager egy 401-es választ ad vissza. A válasz tartalmazza az ügyfél-azonosítót és a bérlő AZONOSÍTÓját, amely érvénytelen a jogkivonat esetében. Ha a kiegészítő fejléc érvényes jogkivonatot tartalmaz a bérlőhöz, a rendszer feldolgozza a több-bérlős kérelmet.

## <a name="next-steps"></a>További lépések
* A hitelesítési kérelmek Azure Resource Manager API-kkal való elküldéséről további információt a [Resource Manager hitelesítési API használata az](resource-manager-api-authentication.md)előfizetések eléréséhez című témakörben talál.
* További információ a tokenekről: [Azure Active Directory hozzáférési tokenek](/azure/active-directory/develop/access-tokens).
