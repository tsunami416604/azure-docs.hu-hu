---
title: „Nem található előfizetés” hiba – Bejelentkezés az Azure Portalra | Microsoft Docs
description: Ez a cikk megoldást kínál arra a problémára, amikor „Nem található előfizetés” hiba jelenik meg az Azure Portalra vagy az Azure Fiókközpontba történő bejelentkezéskor.
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
ms.openlocfilehash: 094d79026a55e651a1f67a5047dff20c769c359a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "60371231"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>„Nem található előfizetés” bejelentkezési hiba az Azure Portalon vagy az Azure Fiókközpontban

Előfordulhat, hogy „Nem található előfizetés” hibaüzenetet kap, amikor megpróbál bejelentkezni az [Azure Portalra](https://portal.azure.com/) vagy az [Azure Fiókközpontba](https://account.windowsazure.com/Subscriptions). Ez a cikk megoldást kínál erre a problémára.

## <a name="symptom"></a>Hibajelenség

Amikor megpróbál bejelentkezni az [Azure Portalra](https://portal.azure.com/) vagy az [Azure Fiókközpontba](https://account.windowsazure.com/Subscriptions), a következő hibaüzenet jelenik meg: „Nem található előfizetés”.

## <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, ha nem a megfelelő címtárat választotta ki, vagy ha a fiókja nem rendelkezik megfelelő engedélyekkel. 

## <a name="solution"></a>Megoldás

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>1\. forgatókönyv: A hibaüzenet az [Azure Portalon](https://portal.azure.com) jelenik meg

A probléma megoldása:

* A jobb felső sarokban kattintson a fiókjára, és ellenőrizze, hogy a megfelelő Azure-címtárat választotta-e ki.

  ![Az Azure Portal jobb felső sarkában válassza ki a címtárat](./media/billing-no-subscriptions-found/directory-switch.png)
* Ha a megfelelő Azure-címtár van kiválasztva, de továbbra is megjelenik a hibaüzenet, [rendelje a fiókjához a Tulajdonos szerepkört](../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>2\. forgatókönyv: A hibaüzenet az [Azure Fiókközpontban](https://account.windowsazure.com/Subscriptions) jelenik meg

Ellenőrizze, hogy az Ön által használt fiók a Fiókadminisztrátor. Annak ellenőrzéséhez, hogy ki a Fiókadminisztrátor, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portal Előfizetések nézetébe](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Válassza ki az ellenőrizni kívánt előfizetést, majd lépjen a **Beállítások** területre.
1. Válassza ki a **Tulajdonságok** elemet. Az előfizetés fiókadminisztrátora a **Fiókadminisztrátor** mezőben látható.  

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
