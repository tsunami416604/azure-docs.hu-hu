---
title: Lejárt esedékességű egyenlegről értesítő e-mail az Azure-tól
description: A cikk azt ismerteti, hogyan fizetheti be a lejárt esedékességű Azure-előfizetéssel kapcsolatos tartozást
author: genlin
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: banders
ms.openlocfilehash: c042f5cee3d0a1e874729911a2162e48c1630a72
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226430"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Az Azure-előfizetés lejárt esedékességű tartozásának rendezése

Ez a cikk a Microsoft Online Services Program-fiókkal rendelkező ügyfelekre vonatkozik.

Ha a kifizetése nem érkezik meg, vagy azt nem tudjuk feldolgozni, előfordulhat, hogy kap egy e-mailt, vagy megjelenik egy értesítés az Azure Portalon vagy a Fiókközpontban.
Amennyiben Ön a [Fiókadminisztrátor](billing-subscription-transfer.md#whoisaa), az [Azure Portalon](https://portal.azure.com) rendezheti a kiegyenlítetlen díjakat. Ha Önnek számlás fizetés van beállítva fizetési módként, küldje az utalást a számla alján megadott helyre.

> [!IMPORTANT]
> * Ha ugyanahhoz a bankkártyájához több előfizetés is tartozik, és mindegyik befizetése lejárt esedékességű, a teljes fennmaradó egyenleget egyszerre kell kifizetnie.
> * A kiegyenlítetlen díjakhoz használt fizetési eszköz lesz innentől az új aktív fizetési eszköz az összes olyan előfizetés esetén, amelynél korábban sikertelen volt a díj befizetése.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Lejárt esedékességű tartozás rendezése az Azure Portalon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) fiókadminisztrátorként.
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.
1. Az Áttekintés lapon megjelenik az előfizetések listája. Ha az előfizetés állapota lejárt, kattintson az **Egyenleg rendezése** hivatkozásra.
    ![Az egyenlet rendezését mutató hivatkozást megjelenítő képernyőkép](./media/billing-azure-subscription-past-due-balance/settle-balance-entry-point.png)
1. A kiegyenlítetlen egyenleg az összes Microsoft-szolgáltatásra vonatkozó, sikertelen fizetési mód használata miatt fennálló terhelést tükrözi.
1. Válasszon ki egy fizetési módot az egyenleg kifizetéséhez. Ez a fizetési mód válik az aktív fizetési móddá mindegyik olyan előfizetés esetén, amelynél korábban sikertelen volt a fizetés.
    ![A fizetési mód kiválasztására szolgáló hivatkozást megjelenítő képernyőkép](./media/billing-azure-subscription-past-due-balance/settle-balance-screen.png)
1. Ha a kiválasztott fizetési módnak szintén vannak a Microsoft-szolgáltatásokhoz kapcsolódó kiegyenlítetlen díjai, azok megjelennek a teljes kiegyenlítetlen egyenlegben. Ezeket a kiegyenlítetlen díjakat is rendeznie kell.
1. Kattintson a **Fizetés** parancsra.

## <a name="troubleshoot-declined-credit-card"></a>Elutasított hitelkártya hibáinak elhárítása

Ha a hitelkártyát a pénzügyi intézet elutasította, lépjen kapcsolatba a pénzügyi intézettel a probléma megoldása érdekében. Lépjen kapcsolatba a bankkal, és ellenőrizze a következőket:
- Engedélyezve vannak a kártyán a nemzetközi tranzakciók?
- A kártyáján beállított hitelkeret vagy a kártya egyenlege lehetővé teszi az összeg kifizetését?
- Engedélyezve vannak a kártyán az ismétlődő fizetések?

## <a name="not-getting-billing-email-notifications"></a>Nem kap számlázással kapcsolatos e-mail-értesítéseket?

Amennyiben Ön a fiókadminisztrátor, [ellenőrizze, hogy melyik e-mail-cím van beállítva az értesítésekhez](billing-how-to-change-azure-account-profile.md). Javasoljuk, hogy olyan e-mail-címet adjon meg, amelyet rendszeresen ellenőriz. Amennyiben az e-mail-cím helyesen van megadva, ellenőrizze a levélszemét-mappát.

## <a name="if-i-forget-to-pay-what-happens"></a>Mi történik, ha elfelejtek fizetni?

A szolgáltatás fel lesz függesztve, és az erőforrásai nem lesznek többé elérhetők. Az Azure-adatai a szolgáltatás felfüggesztését követő 90 nap után törölve lesznek. További tudnivalókért lásd a [Microsoft Adatvédelmi központ az adatok kezelését](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) ismertető cikkét.

Ha tudja, hogy a befizetése fel lett dolgozva, de az előfizetése ennek ellenére még mindig le van tiltva, lépjen kapcsolatba az [Azure-támogatással](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
