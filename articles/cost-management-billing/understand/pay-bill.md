---
title: A Microsoft Azure-számlák befizetése
description: Megismerheti, hogyan fizetheti be számláit az Azure Portalon. A portálon történő fizetéshez egy számlázási profil tulajdonosának, közreműködőjének vagy számlakezelőjének kell lennie.
keywords: számlázás, késedelem, egyenleg, azonnali fizetés,
author: banders
ms.reviewer: judupont
tags: billing, past due, pay now, bill, invoice, pay
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 12/17/2020
ms.author: banders
ms.openlocfilehash: 2983f870d6ab20dac4da487ba03ebf84905f8dcc
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680308"
---
# <a name="how-to-pay-your-bill-for-microsoft-azure"></a>A Microsoft Azure-számlák befizetésének módja

Ez a cikk a Microsoft Ügyfélszerződéssel (MCA) rendelkező ügyfelekre vonatkozik.

[Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése](#check-access-to-a-microsoft-customer-agreement)

Az Azure-számlákat kétféle módon lehet befizetni. Az egyik a számlázási profil alapértelmezett fizetési módja, a másik pedig az **Azonnali fizetés** néven ismert egyszeri befizetés.

Ha rendelkezik Azure-kreditekkel, a rendszer automatikusan alkalmazza őket a számlára az egyes számlázási időszakokban.

## <a name="pay-by-default-payment-method"></a>Fizetés az alapértelmezett fizetési móddal

A számlázási profil alapértelmezett fizetési módja lehet hitel- vagy bankkártyás, illetve csekkes/banki átutalásos.

### <a name="credit-or-debit-card"></a>Hitel- vagy bankkártya

Ha a számlázási profil alapértelmezett fizetési módja hitel- vagy bankkártyás, minden számlázási időszakban automatikusan megterheljük a kártyát.

Ha a hitel- vagy bankkártya automatikus terhelését valamilyen okból elutasítják, egy egyszeri fizetést is elvégezhet hitel- vagy bankkártyával az Azure Portalon, az **Azonnali fizetés** lehetőséggel.

Ha az alapértelmezett fizetési módot szeretné átállítani csekkes/átutalásos fizetésre, tekintse meg [A számlás fizetés működése](../manage/pay-by-invoice.md) című szakaszt.

### <a name="check-or-wire-transfer"></a>Csekk vagy banki átutalás

Ha a számlázási profil alapértelmezett fizetési módja a csekk/banki átutalás, kövesse a PDF-formátumú számlán található fizetési útmutatást.

Abban az esetben, ha a számla összege a pénznem küszöbértéke alatt van, egyszeri fizetést is elvégezhet hitel- vagy bankkártyával az Azure Portalon, az **Azonnali fizetés** lehetőséggel. Ha a számla összege meghaladja a pénznem küszöbértékét, a számla nem fizethető hitel- vagy bankkártyával. A pénznem küszöbértéke az Azure Portalon az **Azonnali fizetés** lehetőség kiválasztása után látható.

## <a name="pay-now-in-the-azure-portal"></a>Azonnali fizetés az Azure Portalon

Az Azure Portalon történő fizetéshez a megfelelő [MCA-engedélyekkel](../manage/understand-mca-roles.md) kell rendelkeznie, vagy a számlázási fiók adminisztrátorának kell lennie. A számlázási fiók adminisztrátora az a felhasználó, aki eredetileg regisztrálta az MCA-fiókot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Végezzen keresést a **Költségkezelés + Számlázás** oldalon.
1. A bal oldali menüben válassza a **Számlázás** területen lévő **Számlák** elemet.
1. Ha a számlák bármelyike esedékes vagy lejárt, akkor az adott számlához megjelenik egy kék **Azonnali fizetés** hivatkozás. Válassza **Azonnali fizetés** lehetőséget.
1. Az Azonnali fizetés ablakban válassza a **Fizetési mód kiválasztása** lehetőséget, majd válasszon ki egy meglévő bankkártyát, vagy adjon hozzá egy újat.
1. Miután kiválasztotta a fizetési módot, válassza az **Azonnali fizetés** lehetőséget.

A számla állapota 24 órán belül *Kifizetve* lesz.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>Következő lépések

- A csekkel/banki átutalással való fizetésre való jogosultság megszerzése érdekében tekintse meg [a számlás fizetés működését](../manage/pay-by-invoice.md) ismertető szakaszt