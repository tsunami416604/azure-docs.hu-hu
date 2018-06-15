---
title: Nincs előfizetés hibát talált, amikor megpróbál bejelentkezni az Azure portál vagy az Azure-fiók center |} Microsoft Docs
description: A megoldást kínál a probléma, amelyben nem találhatók előfizetések hiba akkor fordul elő, amikor jelentkezzen be Azure-portálon vagy az Azure-fiók center.
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 475a4ad72a1c2fc2ebf99387e193713797cc2586
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34070617"
---
# <a name="no-subscriptions-found-error-in-azure-portal-or-azure-account-center"></a>Nem található a hiba az Azure portálon vagy az Azure-fiók center előfizetés.

A "Nem található előfizetés" hibaüzenetet kaphat, ha megpróbál bejelentkezni a [Azure-portálon](https://portal.azure.com/) vagy a [Azure Account Center](https://account.windowsazure.com/Subscriptions). Ez a cikk megoldást kínál a probléma.

## <a name="symptom"></a>Jelenség

Amikor megpróbál bejelentkezni a [Azure-portálon](https://portal.azure.com/) vagy a [Azure-fiók center](https://account.windowsazure.com/Subscriptions), a következő hibaüzenetet kapja: "Nem található előfizetés".

## <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, ha bejelölte a megfelelő könyvtárban, vagy ha a fiók nem rendelkezik megfelelő engedélyekkel. 

## <a name="solution"></a>Megoldás

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>1. forgatókönyv: Hibaüzenet érkezett a [Azure-portálon](https://portal.azure.com)

A probléma kijavítása:

* Győződjön meg arról, hogy a megfelelő Azure-címtár kattintson a jobb felső sarokban a fiók van kiválasztva.

  ![Válassza ki azt a címtárat a bal felső sarkában az Azure-portálon](./media/billing-no-subscriptions-found/directory-switch.png)
* Ha a jobb oldali Azure-címtár van kiválasztva, de továbbra is megjelenik a hibaüzenet [fiókját tulajdonos rendelkezik](billing-add-change-azure-subscription-administrator.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>2. forgatókönyv: Hibaüzenet érkezett a [Azure Account Center](https://account.windowsazure.com/Subscriptions)

Ellenőrizze, hogy a használt fiók a fiók rendszergazdájához. A fiók rendszergazdájához, aki ellenőrzéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [előfizetések megtekintéséhez az Azure portálon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Válassza ki az előfizetést, és ellenőrizze, majd keresse meg a **beállítások**.
1. Válassza ki **tulajdonságok**. A fiókadminisztrátor az előfizetés jelenik meg a **Fiókadminisztrátor** mezőbe.  

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha további segítségre van, [forduljon a támogatási szolgálathoz](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409) a probléma elhárítva gyors eléréséhez. 
