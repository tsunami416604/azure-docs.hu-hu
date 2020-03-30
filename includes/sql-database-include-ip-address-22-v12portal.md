---
title: Kiszolgálószintű tűzfalszabályok
description: Kiszolgálószintű tűzfalszabályok
keywords: sql kapcsolat,kapcsolati karakterlánc
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 8d0f9899dbb7599340b8d15ca010a0157011fb9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179198"
---
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. A bal oldali listában válassza a **Minden szolgáltatás lehetőséget.**

3. Görgessen és válassza az **SQL-kiszolgálókat**.

    ![Az Azure SQL Database-kiszolgáló megkeresése a portálon][b21-FindServerInPortal]
5. A szűrő szövegmezőjébe írja be a kiszolgáló nevét. Megjelenik a sor.

6. Válassza ki a kiszolgáló sorát. Megjelenik a kiszolgáló panelje.

7. A kiszolgálópanelen válassza a **Beállítások lehetőséget.**

8. Válassza **a Tűzfal**lehetőséget.

    ![A tűzfal beállításainak >][b31-SettingsFirewallNavig]
9. Válassza **az Ügyfél IP hozzáadása**lehetőséget. Írja be az új szabály nevét az első szövegmezőbe.

10. Írja be az engedélyezni kívánt tartomány alacsony és magas IP-címértékeit.

    * Ez lehet kéznél levő -hoz volna a alacsony érték vég -val **.0** és a magas érték vég -val **.255.**

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
