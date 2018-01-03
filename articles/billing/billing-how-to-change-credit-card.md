---
title: "A hitelkártya módosítása az Azure-bA |} Microsoft Docs"
description: "Ismerteti, hogyan kell fizetni az Azure-előfizetés a használt hitelkártya módosítása"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2017
ms.author: genli
ms.openlocfilehash: 9cab81b6072c6f096f6f1a419cebcca9630ebde3
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/23/2017
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Hozzáadása, frissítése vagy az Azure-bA vagy követel kártya eltávolítása

Az Account Center hozzáadása egy új hitelkártya, egy meglévő hitelkártya módosítása vagy törlése a hitelkártya, amelyek nem használják. Kell [Fiókadminisztrátort](billing-subscription-transfer.md#whoisaa) módosítások végrehajtásához.

**Kíván a számla fizet váltani?** Lásd: [Azure-előfizetések a számla kibocsátása után kell fizetnie](billing-how-to-pay-by-invoice.md).
 
<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card"></a>Adja hozzá egy új vagy követel kártya

1. Jelentkezzen be a [Account Center](https://account.windowsazure.com/Subscriptions) fiók rendszergazdaként.
1. Válasszon egy előfizetést.
1. Az oldal jobb oldalán válassza a **Fizetési módok kezelése** lehetőséget.

    ![Képernyőkép a kezelés fizetési módszerek jelölőnégyzetet.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Jelölje ki "+" hozzáadása a kártya.

    ![Képernyőkép a Szerkesztés beállítás mellett a fizetési módot.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Követel meg, vagy a tartozik vonatkozó információkat.
1. Kattintson a **Mentés** gombra. 

Ha hibaüzenetet kap, miután hozzáadta a hitelkártya, lásd: [hitelkártya elutasítva: Azure-előfizetési](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-or-debit-card"></a>Meglévő jóváírás vagy bankkártyával frissítése

Ha hitelkártyával lekérdezi megújítani, és a szám változatlan marad, frissítse a meglévő hitelkártya adatait, például a lejárati dátum. Ha a hitelkártya száma módosításokat, mert a kártya egy elveszett, ellopott, vagy lejárt, kövesse a a [adja hozzá a hitelkártya fizetési módszerként](#addcard) szakasz. Nem kell frissíteni a CVV.

1. Jelentkezzen be a [Azure Account Center](https://account.windowsazure.com/Subscriptions) fiók rendszergazdaként.
1. Válassza ki az előfizetést, amely csatolva van a kártya.
1. Válassza ki **fizetési módok kezelése**.
1. Válassza ki **szerkesztése** mellett a frissíteni kívánt kártya.
1. A vagy követel kártya adatainak frissítéséhez.
1. Kattintson a **Mentés** gombra.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Az Azure-előfizetés egy másik hitelkártya használatára

1. Jelentkezzen be a [Azure Account Center](https://account.windowsazure.com/Subscriptions) fiók rendszergazdaként.
1. Válassza ki az előfizetést, amely csatolva van a kártya.
1. Az oldal jobb oldalán válassza a **Fizetési módok kezelése** lehetőséget.
1. Kattintson a **használja helyette** a kártya, amely a használni kívánt mellett. Ez is frissíti, ez a kártya jelenleg társított más előfizetéseket. 

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>Távolítsa el a fiókot vagy követel kártya

1. Jelentkezzen be a [Azure Account Center](https://account.windowsazure.com/Subscriptions) a fiókadminisztrátora.
1. Válassza ki az előfizetést, amely csatolva van a kártya.
3. Az oldal jobb oldalán válassza a **Fizetési módok kezelése** lehetőséget.
4. Kattintson a **törlése** a hitelkártya, hogy törölni szeretné.

Ha hitelkártyával más aktív Microsoft előfizetések tartozik, nem távolítható el az Azure-fiókjába. Távolítsa el a hitelkártya összes aktív előfizetést, amely rendelkezik a Microsofttal, és próbálkozzon újra.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Az előfizetésem le van tiltva. Miért nem távolítható el a hitelkártyámmal most?

Az előfizetés le van tiltva, vagy visszavont után 90 nappal korábbinak véglegesen törli az előfizetését várunk. Azt tartsa a fizetési módot, a fájl a megőrzési időszak alatt abban az esetben, ha azt szeretné, az előfizetés újraaktiválásához. Ezt követően az előfizetés teljesen törölve.

Ha el kell távolítania vagy követel kártyáját, mielőtt a 90 napos megőrzési időszak végén, [az előfizetés újraaktiválásához](billing-subscription-become-disable.md). Nem lehet aktiválni, ha [kérje az Azure támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Miért kapok folyton "bejelentkezése lejárt. Kattintson ide a bejelentkezéshez vissza"?

Ha ez a hibaüzenet akkor is, ha már bejelentkezve folyton, és vissza a, próbálja meg újra egy titkos munkamenet.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Miként használható az egy másik kártyát az egyes előfizetésekhez van?

Sajnos az előfizetések már használja a kártyának, esetén nem lehetséges különböző kártyák használhatják. Amikor regisztrál egy új előfizetést, akkor kiválaszthatja, egy új fizetési mód használata az adott előfizetéshez tartozó.

### <a name="how-do-i-make-payments"></a>Hogyan tehetem kifizetések?

Ha beállított hitelkártyával vagy bankkártyával egy a fizetési módot, azt automatikusan díjat számítanak kártyáját minden számlázási időszak után. Nem kell tennie semmit.

Ha Ön [szerinti fizetésre](billing-how-to-pay-by-invoice.md), a helyre a befizetést a számla alján felsorolt küldése.

### <a name="how-do-i-make-a-one-time-payment"></a>Hogyan tehetem egy egyszeri fizetési?

Sajnos Azure jelenleg nem támogatja a egyszeri kifizetések vagy követel kártyákhoz. 

### <a name="how-do-i-change-the-tax-id"></a>Az Adószám módosítása?

Adja hozzá, vagy az Adószám módosítása, látogasson el a [ **profil** az Azure Account Center](https://account.azure.com/Profile), majd jelölje be **rekord adó**. Az Adószám adó kivétel számítások használja, és a számlán megjelenik.

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további segítségre van, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.
