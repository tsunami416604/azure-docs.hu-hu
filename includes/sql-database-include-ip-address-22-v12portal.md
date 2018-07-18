---
title: Kiszolgálószintű tűzfalszabályok
description: Kiszolgálószintű tűzfalszabályok
keywords: SQL-kapcsolatot, a kapcsolati karakterlánc
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 07bbba28b7e1b048ef3a4e1afa4db255967dc093
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39117273"
---
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza a bal oldali lista **minden szolgáltatás**.

3. Görgessen és válassza ki **SQL Server-kiszolgálók**.

    ![Keresse meg az Azure SQL Database-kiszolgáló a portálon][b21-FindServerInPortal]
5. A szűrő szövegmezőbe elindításához írja be a kiszolgáló nevét. A sor jelenik meg.

6. Válassza ki a kiszolgáló a sort. Megjelenik egy panel, a kiszolgáló számára.

7. Az a kiszolgáló panelen válassza ki a **beállítások**.

8. Válassza ki **tűzfal**.

    ![Válassza ki a beállítások > tűzfal][b31-SettingsFirewallNavig]
9. Válassza ki **adja hozzá ügyfél IP**. Az első szövegbeviteli mezőbe írja be az új szabály nevét.

10. Írja be az alacsony és magas IP cím értékeit engedélyezni kívánja a tartományon.

    * Alacsony érték end jöhet lehet **.0** és a magas érték végén **.255**.

11. Kattintson a **Mentés** gombra.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
