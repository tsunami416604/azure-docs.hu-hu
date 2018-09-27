---
title: A megadott bankkártyára módosítása az Azure-hoz |} A Microsoft Docs
description: Ismerteti, hogyan lehet hogyan lehet Azure-előfizetés díjfizetéséhez használt bankkártya módosítása
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: cwatson
ms.openlocfilehash: 68f987daff5bc0ef81c248f6f5e75aaf1318b025
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395491"
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Hozzáadása, frissítése vagy hitelkártya vagy bankkártya eltávolítása az Azure-hoz

Az Account Center webhelyen új hitelkártya hozzáadásához, frissítse a meglévő bankkártyák, vagy törölje a nem használt hitelkártya. Kell [felhasználóifiók-adminisztrátor](billing-subscription-transfer.md#whoisaa) módosítások végrehajtásához.

**Váltson át a számla ellenében szeretné?** Lásd: [Azure előfizetések fizetése számla](billing-how-to-pay-by-invoice.md).
 
<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card"></a>Új hitelkártya vagy bankkártya kártya hozzáadása

1. Jelentkezzen be a [Fiókközpontban](https://account.windowsazure.com/Subscriptions) fiók rendszergazdaként.
1. Válasszon egy előfizetést.
1. Az oldal jobb oldalán válassza a **Fizetési módok kezelése** lehetőséget.

    ![Képernyőkép a kezelés fizetési módszereket lehetőség van kijelölve.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Jelölje be "+", adjon hozzá egy kártyát.

    ![Képernyőkép a Szerkesztés lehetőség mellett a fizetési módot.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Adja meg a jóváírás vagy a kártyaadatokon tartozik.
1. Kattintson a **Mentés** gombra. 

Miután hozzáadta a hitelkártya hibaüzenetet kap, ha [Azure-előfizetés elutasított bankkártya](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-or-debit-card"></a>Meglévő jóváírás vagy bankkártyával frissítése

Ha hitelkártyáját megújítani lekérdezi, és hány változatlan marad, frissítse a meglévő hitelkártya adatait, például a lejárati dátum. Ha a hitelkártya száma módosításokat, mert a kártya elveszik, ellopják, vagy lejárt, kövesse a a [hitelkártya hozzáadásához a fizetési módként alkalmazott](#addcard) szakaszban. Nem kell frissíteni a CVV.

1. Jelentkezzen be a [Azure Account Center](https://account.windowsazure.com/Subscriptions) fiók rendszergazdaként.
1. Válassza ki az előfizetést, amely kapcsolódik a kártya.
1. Válassza ki **fizetési módok kezelése**.
1. Válassza ki **szerkesztése** mellett a frissíteni kívánt kártya.
1. Frissítse a hitelkártya vagy bankkártya vonatkozó információkat.
1. Kattintson a **Mentés** gombra.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Más hitelkártya használata az Azure-előfizetés

1. Jelentkezzen be a [Azure Account Center](https://account.windowsazure.com/Subscriptions) fiók rendszergazdaként.
1. Válassza ki az előfizetést, amely kapcsolódik a kártya.
1. Az oldal jobb oldalán válassza a **Fizetési módok kezelése** lehetőséget.
1. Kattintson a **használja ehelyett** mellett a kártya, amelyet használni szeretne. Ez frissíti a jelenleg társított Ez a kártya egyéb előfizetéseket is. 

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>Hitelkártya vagy bankkártya eltávolítása a fiókból

1. Jelentkezzen be a [Azure Account Center](https://account.windowsazure.com/Subscriptions) rendszergazdaként.
1. Válassza ki az előfizetést, amely kapcsolódik a kártya.
3. Az oldal jobb oldalán válassza a **Fizetési módok kezelése** lehetőséget.
4. Kattintson a **törlése** a hitelkártya, amelyet törölni szeretne.

Ha a megadott bankkártyára más aktív Microsoft-előfizetések társítva, nem távolítható el az Azure-fiókjával. Távolítsa el a hitelkártyát az összes aktív előfizetést, amely rendelkezik a Microsofttal, és próbálkozzon újra.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Az előfizetés le van tiltva. Miért nem távolítható el fognak pénzt levonni most?

Után az előfizetés le van tiltva, vagy meg lett szakítva, hogy Várjon, amíg 90 nappal korábbinak végleges törlése az előfizetésében. Mindent megteszünk a fizetési módot, a fájl a megőrzési időszak alatt szeretne az előfizetés újraaktiválásához. Ezt követően az előfizetés teljes egészében törlődik.

Ha el kell távolítania a bankkártya vagy egy hitelkártya, mielőtt a 90 napos megőrzési időszak végén, [az előfizetés újraaktiválásához](billing-subscription-become-disable.md). Nem lehet aktiválni, ha [lépjen kapcsolatba az Azure-támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Miért kapok folyton "a bejelentkezési munkamenet lejárt. Ide kattintva jelentkezzen be újra"?

Ha ez a hibaüzenet továbbra is megkapja, még akkor is, ha már bejelentkezett és vissza a, próbálja meg újra a privát böngészési munkamenetet.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Hogyan használhatom egy másik kártyát minden egyes előfizetés van?

Sajnos Ha az előfizetések ugyanahhoz a kártyához már használ, azt, nem külön azokat különböző kártyák használatára. Azonban amikor regisztrál egy új előfizetést, választhat új fizetési mód használatára az adott előfizetéshez tartozó.

### <a name="how-do-i-make-payments"></a>Hogyan ellenőrizhetem, kifizetések?

Ha beállította hitelkártyával vagy bankkártyával kártya a fizetési módként alkalmazott, automatikusan díját számoljuk fel a kártya minden elszámolási időszak után. Nem kell semmit.

Ha Ön [szerinti fizetésre](billing-how-to-pay-by-invoice.md), a fizetési a helyet, a számla alján felsorolt küldése.

### <a name="how-do-i-make-a-one-time-payment"></a>Hogyan ellenőrizhetem, egyszeri fizetéssel?

Sajnos az Azure jelenleg nem támogatja az egyszeri kifizetések bankkártyával, illetve a kártyák. 

### <a name="how-do-i-change-the-tax-id"></a>Hogyan változtatható az Adóazonosító?

Hozzáadásához, vagy frissítési adó azonosítója, látogasson el a [ **profil** Azure Account Center webhelyen](https://account.azure.com/Profile), majd **rekord adó**. Az adóazonosító az adómentesség kiszámításához szükséges, és megjelenik a számlán.

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
