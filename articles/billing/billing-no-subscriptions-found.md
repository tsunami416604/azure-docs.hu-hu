---
title: Nem találhatók előfizetések hiba – az Azure portal bejelentkezési |} A Microsoft Docs
description: Minden megoldást kínál, amelyben nem találhatók előfizetések hiba lép fel probléma esetén jelentkezzen be az Azure portal vagy az Azure account Center webhelyen.
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: c6a2f14900d3a0d6f9e16e9b0c6d0bdfff97d6b5
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903836"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Nem találhatók előfizetések bejelentkezési hiba történt az Azure Portalon vagy az Azure account Center webhelyen

Egy "Nem találhatók előfizetések" hibaüzenetet kaphat, ha megpróbál bejelentkezni a [az Azure portal](https://portal.azure.com/) vagy a [Azure Account Center](https://account.windowsazure.com/Subscriptions). Ez a cikk egy megoldást nyújt a probléma.

## <a name="symptom"></a>Jelenség

Amikor próbál bejelentkezni a [az Azure portal](https://portal.azure.com/) vagy a [Azure account Center webhelyen](https://account.windowsazure.com/Subscriptions), a következő hibaüzenetet kapja: "Nem találhatók előfizetések".

## <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, ha az nem megfelelő könyvtárba, vagy ha a fiókja nem rendelkezik megfelelő engedélyekkel. 

## <a name="solution"></a>Megoldás

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>1. forgatókönyv: Hibaüzenet érkezik a [Azure Portalon](https://portal.azure.com)

A probléma megoldásához:

* Győződjön meg arról, hogy a megfelelő Azure-címtár kattintson a jobb felső sarokban fiókja van-e kiválasztva.

  ![Válassza ki a címtár tetején, az Azure portal jobb](./media/billing-no-subscriptions-found/directory-switch.png)
* Ha a megfelelő Azure-könyvtár van kijelölve, de továbbra is megjelenik a hibaüzenet [a tulajdonosi szerepkör hozzárendelése a fiókhoz](../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>2. forgatókönyv: Hibaüzenet érkezik a [Azure Account Center](https://account.windowsazure.com/Subscriptions)

Ellenőrizze, hogy a fiókot, amelynek Ön a fiók rendszergazdája. Annak ellenőrzéséhez, aki a fiók rendszergazdája, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [előfizetések megtekintése az Azure Portalon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Válassza ki az előfizetést, gombra, majd keresse meg a kívánt **beállítások**.
1. Válassza ki **tulajdonságok**. Az előfizetés fiókadminisztrátori fiókjával jelenik meg a **Fiókadminisztrátor** mezőbe.  

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
