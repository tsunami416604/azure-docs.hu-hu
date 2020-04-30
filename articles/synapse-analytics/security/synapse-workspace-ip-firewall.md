---
title: Az IP-tűzfalszabályok konfigurálása az Azure szinapszis Analyticsben
description: Egy cikk, amely megtanítja az IP-tűzfalszabályok konfigurálását az Azure szinapszis Analyticsben
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: f36a76187538d93b57d2d3f5973408f141271f67
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424110"
---
# <a name="azure-synapse-analytics-ip-firewall-rules-preview"></a>Azure szinapszis Analytics IP-tűzfalszabályok (előzetes verzió)

Ez a cikk ismerteti az IP-tűzfalszabályok szabályait, és megtanítja, hogyan konfigurálhatja őket az Azure szinapszis Analyticsben.

## <a name="ip-firewall-rules"></a>IP-tűzfalszabályok

Az IP-tűzfalszabályok engedélyezik vagy megtagadják a hozzáférését a szinapszis munkaterülethez az egyes kérések származó IP-címe alapján. Beállíthatja a munkaterület IP-tűzfalszabályok szabályait. A munkaterület szintjén konfigurált IP-tűzfalszabályok a munkaterület összes nyilvános végpontján érvényesek (SQL-készletek, SQL on-demand és fejlesztés).

## <a name="create-and-manage-ip-firewall-rules"></a>IP-tűzfalszabályok létrehozása és kezelése

Az IP-tűzfalszabályok kétféleképpen vehetők fel egy szinapszis-munkaterületre. Ha IP-tűzfalat szeretne hozzáadni a munkaterülethez, válassza a **Biztonság és hálózatkezelés** lehetőséget, és jelölje be az **összes IP-cím kapcsolatának engedélyezése** a munkaterület létrehozása során jelölőnégyzetet.

![Azure Portal a szinapszis-munkaterület IP-konfigurációját.](./media/synpase-workspace-ip-firewall/ip-firewall-1.png)

![Azure Portal a szinapszis-munkaterület IP-konfigurációját.](./media/synpase-workspace-ip-firewall/ip-firewall-2.png)

A munkaterület létrehozása után IP-tűzfalszabályok is hozzáadhatók egy szinapszis-munkaterülethez. Válasszon **tűzfalat** a Azure Portal **Biztonság** területen. Új IP-tűzfalszabály hozzáadásához adja meg a nevet, a kezdő IP-címet és a záró IP-címet. Ha végzett, kattintson a **Mentés** gombra.

![Az Azure szinapszis munkaterület IP-konfigurációja Azure Portalban.](./media/synpase-workspace-ip-firewall/ip-firewall-3.png)

## <a name="connecting-to-synapse-from-your-own-network"></a>Csatlakozás a Szinapszishoz a saját hálózatáról

A szinapszis-munkaterülethez a szinapszis Studio használatával csatlakozhat. A munkaterületen a SQL Server Management Studio (SSMS) használatával is csatlakozhat az SQL-erőforrásokhoz (az SQL-készletek és az SQL on-demand).

Győződjön meg arról, hogy a hálózat és a helyi számítógép tűzfala lehetővé teszi a kimenő kommunikációt a 80, 443 és 1443 TCP-portokon a szinapszis studióhoz.

Emellett engedélyeznie kell a kimenő kommunikációt a 53-es UDP-porton a szinapszis studióhoz. Ha a SSMS és a Power BI eszközzel szeretne csatlakozni, engedélyeznie kell a kimenő kommunikációt az 1433-as TCP-porton.

Ha az alapértelmezett átirányítási házirend-beállítást használja, lehetséges, hogy engedélyeznie kell a kimenő kommunikációt a további portokon. A kapcsolatok házirendjeiről itt olvashat bővebben.

## <a name="next-steps"></a>További lépések

[Azure szinapszis-munkaterület](../quickstart-create-workspace.md) létrehozása

Azure-beli szinapszis-munkaterület létrehozása [felügyelt munkaterület-VNet](./synapse-workspace-managed-vnet.md)