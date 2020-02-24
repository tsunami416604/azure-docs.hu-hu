---
title: Lejárt esedékességű egyenlegről értesítő e-mail az Azure-tól
description: A cikk azt ismerteti, hogyan fizetheti be a lejárt esedékességű Azure-előfizetéssel kapcsolatos tartozást
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: cbfd5f7bf47cdaf43df00c710bd6680373d67b09
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200605"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Az Azure-előfizetés lejárt esedékességű tartozásának rendezése

Ez a cikk olyan ügyfelekre vonatkozik, akik hitelkártyával regisztráltak online az Azure-ra, és rendelkeznek számlázási fiókkal a Microsoft Online Services Programhoz kapcsolódóan. Ismerje meg, hogyan [ellenőrizheti számlázási fiókjának típusát](#check-the-type-of-your-account). Ha rendelkezik Microsoft Ügyfélszerződéshez tartozó számlázási fiókkal, tekintse meg a [Microsoft Azure-számlák befizetése](../understand/pay-bill.md) című szakaszt.

Ha a kifizetése nem érkezik meg, vagy azt nem tudjuk feldolgozni, e-mailt fog kapni, és megjelenik egy értesítés az Azure Portalon, amely arról tájékoztatja, hogy az előfizetése lejárt. Amennyiben az alapértelmezett fizetési mód a bankkártyás fizetés, a [fiókadminisztrátor](billing-subscription-transfer.md#whoisaa) az Azure Portalon rendezheti a ki nem egyenlített díjakat. Számlás (csekkes/átutalásos) fizetés esetén küldje az utalást a számla alján megadott helyre.

> [!IMPORTANT]
> * Ha ugyanahhoz a bankkártyájához több előfizetés is tartozik, és mindegyik befizetése lejárt esedékességű, a teljes fennmaradó egyenleget egyszerre kell kifizetnie.
> * A kiegyenlítetlen díjakhoz használt bankkártya lesz innentől az új alapértelmezett fizetési eszköz az összes olyan előfizetés esetén, amelynél korábban sikertelen volt a díj befizetése.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Lejárt esedékességű tartozás rendezése az Azure Portalon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) fiókadminisztrátorként.
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.
1. Az **Áttekintés** lapon válassza ki a lejárt előfizetést.
1. Az **Előfizetés áttekintése** lapon kattintson a piros, lejárt összegekhez kapcsolódó szalagcímre az összeg befizetéséhez.
    > [!NOTE]
    > Ha nem Ön a fiókadminisztrátor, nem fizetheti ki az összeget.
1. Az új **Egyenleg rendezése** lapon kattintson a **Fizetési mód kiválasztása** lehetőségre.
    ![A fizetési mód kiválasztására szolgáló hivatkozást megjelenítő képernyőkép](./media/resolve-past-due-balance/settle-balance-screen.png)

1. A jobb oldalon található új panelen válasszon ki egy bankkártyát a legördülő listából, vagy kattintson a kék **Új fizetési mód hozzáadása** hivatkozásra egy új hozzáadásához. Ez a bankkártya válik az aktív fizetési móddá mindegyik olyan előfizetés esetén, amelynél korábban sikertelen volt a fizetés.
     > [!NOTE]
     > * A kiegyenlítetlen egyenleg az összes Microsoft-szolgáltatásra vonatkozó, sikertelen fizetési mód használata miatt fennálló terhelést tükrözi.
     > * Ha a kiválasztott fizetési módnak szintén vannak a Microsoft-szolgáltatásokhoz kapcsolódó kiegyenlítetlen díjai, azok megjelennek a teljes kiegyenlítetlen egyenlegben. Ezeket a kiegyenlítetlen díjakat is rendeznie kell.
1. Kattintson a **Fizetés** parancsra.

## <a name="troubleshoot-declined-credit-card"></a>Elutasított hitelkártya hibáinak elhárítása

Ha a hitelkártyát a pénzügyi intézet elutasította, lépjen kapcsolatba a pénzügyi intézettel a probléma megoldása érdekében. Lépjen kapcsolatba a bankkal, és ellenőrizze a következőket:
- Engedélyezve vannak a kártyán a nemzetközi tranzakciók?
- A kártyáján beállított hitelkeret vagy a kártya egyenlege lehetővé teszi az összeg kifizetését?
- Engedélyezve vannak a kártyán az ismétlődő fizetések?

## <a name="not-getting-billing-email-notifications"></a>Nem kap számlázással kapcsolatos e-mail-értesítéseket?

Amennyiben Ön a fiókadminisztrátor, [ellenőrizze, hogy melyik e-mail-cím van beállítva az értesítésekhez](change-azure-account-profile.md). Javasoljuk, hogy olyan e-mail-címet adjon meg, amelyet rendszeresen ellenőriz. Amennyiben az e-mail-cím helyesen van megadva, ellenőrizze a levélszemét-mappát.

## <a name="if-i-forget-to-pay-what-happens"></a>Mi történik, ha elfelejtek fizetni?

A szolgáltatás fel lesz függesztve, és az erőforrásai nem lesznek többé elérhetők. Az Azure-adatai a szolgáltatás felfüggesztését követő 90 nap után törölve lesznek. További tudnivalókért lásd a [Microsoft Adatvédelmi központ az adatok kezelését](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) ismertető cikkét.

Ha tudja, hogy a befizetése fel lett dolgozva, de az előfizetése ennek ellenére még mindig le van tiltva, lépjen kapcsolatba az [Azure-támogatással](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="check-the-type-of-your-account"></a>A fiók típusának megtekintése
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
