---
title: IP-tűzfalszabályok konfigurálása az Azure Synapse Analytics szolgáltatásban
description: Az AZURE Synapse Analytics ip-tűzfalszabályainak konfigurálását tanító cikk
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: f36a76187538d93b57d2d3f5973408f141271f67
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424110"
---
# <a name="azure-synapse-analytics-ip-firewall-rules-preview"></a>Az Azure Synapse Analytics IP-tűzfalszabályai (előzetes verzió)

Ez a cikk ismerteti az IP tűzfal szabályokat, és bemutatja, hogyan konfigurálhatja őket az Azure Synapse Analytics.

## <a name="ip-firewall-rules"></a>IP tűzfalszabályai

Az IP-tűzfalszabályok az egyes kérelmek eredeti IP-címe alapján engedélyezik vagy megtagadják a hozzáférést a Szinapsze-munkaterülethez. Ip-tűzfalszabályokat állíthat be a munkaterülethez. A munkaterület szintjén konfigurált IP-tűzfalszabályok a munkaterület összes nyilvános végpontjára vonatkoznak (SQL-készletek, igény szerinti SQL és Fejlesztés).

## <a name="create-and-manage-ip-firewall-rules"></a>IP-tűzfalszabályok létrehozása és kezelése

A szinapsze-munkaterülethez kétféleképpen adhatók hozzá az IP-tűzfalszabályok. Ha IP-tűzfalat szeretne hozzáadni a munkaterülethez, jelölje be a **Biztonság + hálózat lehetőséget,** és jelölje be **a Kapcsolatok engedélyezése az összes IP-címről a** munkaterület létrehozása során jelölőnégyzetet.

![Az Azure Portal Synapse munkaterület IP-konfigurációja.](./media/synpase-workspace-ip-firewall/ip-firewall-1.png)

![Az Azure Portal Synapse munkaterület IP-konfigurációja.](./media/synpase-workspace-ip-firewall/ip-firewall-2.png)

Ip-tűzfalszabályokat a munkaterület létrehozása után is hozzáadhat a Szinapszis-munkaterülethez. Válassza **a Tűzfalak lehetőséget az** Azure Portal **biztonsága** csoportban. Új IP-tűzfalszabály hozzáadásához adjon neki nevet, az IP indítása és az IP befejezése címet. Ha végzett, kattintson a **Mentés** gombra.

![Az Azure Synapse-munkaterület IP-konfigurációja az Azure Portalon.](./media/synpase-workspace-ip-firewall/ip-firewall-3.png)

## <a name="connecting-to-synapse-from-your-own-network"></a>Csatlakozás a Synapse-hoz a saját hálózatáról

A Synapse Studio használatával csatlakozhat a Synapse munkaterülethez. Az SQL Server Management Studio (SSMS) segítségével is csatlakozhat a munkaterület SQL-erőforrásaihoz (SQL-készletek és SQL-készletek).

Győződjön meg arról, hogy a hálózat és a helyi számítógép tűzfala engedélyezi a kimenő kommunikációt a Synapse Studio 80-as, 443-as és 1443-as TCP-portján.

Emellett engedélyeznie kell a kimenő kommunikációt az 53-as UDP-porton a Synapse Studio számára. Ha olyan eszközökkel szeretne csatlakozni, mint az SSMS és a Power BI, engedélyeznie kell a kimenő kommunikációt az 1433-as TCP-porton.

Ha az alapértelmezett átirányítási kapcsolatházirend-beállítást használja, előfordulhat, hogy engedélyeznie kell a kimenő kommunikációt további portokon. A kapcsolati szabályzatokról itt olvashat bővebben.

## <a name="next-steps"></a>További lépések

Azure [Synapse-munkaterület](../quickstart-create-workspace.md) létrehozása

Azure Synapse-munkaterület létrehozása [felügyelt munkaterületvirtuális hálózattal](./synapse-workspace-managed-vnet.md)