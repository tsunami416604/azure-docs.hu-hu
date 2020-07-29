---
title: Szolgáltatás-címkék használata
titleSuffix: Azure SignalR Service
description: A szolgáltatás-címkék használatával engedélyezheti az Azure Signaler szolgáltatás kimenő forgalmát
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 8810309fef5dbbb35465a2af15d42fa8a59d5401
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84300175"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Szolgáltatás-címkék használata az Azure Signaler szolgáltatáshoz

A [hálózati biztonsági csoport](../virtual-network/security-overview.md#network-security-groups)konfigurálásakor használhat [szolgáltatási címkéket](../virtual-network/security-overview.md#service-tags) az Azure signaler szolgáltatáshoz. Lehetővé teszi a kimenő hálózati biztonsági szabályok definiálását az Azure Signaler szolgáltatás végpontjai számára anélkül, hogy IP-címeket kellene parancsmagba.

Az Azure Signaler szolgáltatás kezeli ezeket a szolgáltatási címkéket. Nem hozhat létre saját szolgáltatási címkét, vagy módosíthat egy meglévőt. A Microsoft kezeli ezeket a címeket, amelyek megfelelnek a szolgáltatás címkéjének, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

## <a name="use-service-tag-on-portal"></a>Szolgáltatási címke használata a portálon

Egy új kimenő hálózati biztonsági szabály hozzáadásával engedélyezheti a kimenő adatforgalmat az Azure Signaler szolgáltatásban:

1. Nyissa meg a hálózati biztonsági csoportot.

1. Kattintson a **kimenő biztonsági szabályok**nevű beállítások menüre.

1. Kattintson a felül található **+ Hozzáadás** gombra.

1. Válassza a **szolgáltatás címkét** a **célhely**területen.

1. Válassza a **AzureSignalR** elemet a **cél szolgáltatás címkéjén**.

1. Töltse ki a **443** -as **portot a célport tartományokban**.

    ![Kimenő biztonsági szabály létrehozása](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. Módosítsa a többi mezőt az igényeinek megfelelően.

1. Kattintson a **Hozzáadás** parancsra.


## <a name="next-steps"></a>További lépések

- [Hálózati biztonsági csoportok: szolgáltatás címkéi](../virtual-network/security-overview.md#security-rules)
