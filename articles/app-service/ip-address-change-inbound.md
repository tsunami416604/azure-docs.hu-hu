---
title: Bejövő IP-cím módosítása – az Azure App Service előkészítése
description: Ha a bejövő IP-cím lehet módosítani, megtudhatja, mit kell tennie, hogy az alkalmazása továbbra is működik, a módosítás után.
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: aaa89b5a3bb1af6878ed21e0160a534a1c989228
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721359"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Hogyan készíti elő a bejövő IP-cím módosítása

Ha kapott egy értesítés, hogy az Azure App Service-alkalmazás bejövő IP-címe módosul, kövesse a cikkben.

## <a name="determine-if-you-have-to-do-anything"></a>Ha meg semmit sem kell tennie meghatározása

* 1. lehetőség: Az App Service-alkalmazás nem rendelkezik egy egyéni tartományt, ha semmit nem kell.

* 2. lehetőség: Ha csak egy CNAME-rekordot (DNS-rekord mutató URI-t) a tartomány regisztrációs portálon (harmadik féltől származó DNS-szolgáltató vagy az Azure DNS-ben) van konfigurálva, akkor semmit nem kell.

* 3. lehetőség: Ha egy A rekordot (közvetlenül a IP-címre mutató DNS-rekord) a tartomány regisztrációs portálon (harmadik féltől származó DNS-szolgáltató vagy az Azure DNS-ben) van konfigurálva, cserélje le a meglévő IP-cím az újat. Az új IP-cím a következő utasításokat a következő szakaszban találja.

* 4. lehetőség: Ha az alkalmazás egy terheléselosztó mögé, IP-szűrő vagy bármely más IP mechanizmust, amely szükséges az alkalmazás IP-cím, cserélje le a meglévő IP-cím az újat. Az új IP-cím a következő utasításokat a következő szakaszban találja.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Az Azure Portalon keresse meg az új bejövő IP-cím

Az új bejövő IP-cím, az alkalmazás adott van, a portál a **virtuális IP-cím** mező. Az új IP-cím és a régit is csatlakozik az alkalmazás most már, és később a régit le lesz választva.

1.  Nyissa meg az [Azure Portalt](https://portal.azure.com).

2.  A bal oldali navigációs menüben válassza ki a **App Services**.

3.  Válassza ki az App Service-alkalmazást a listából.

1.  Ha az alkalmazás egy függvényalkalmazást, lásd: [függvényalkalmazás bejövő IP-cím](../azure-functions/ip-addresses.md#function-app-inbound-ip-address).

4.  Alatt a **beállítások** fejléc, kattintson a **tulajdonságok** a bal oldali navigációs és a keresés feliratú szakasz **virtuális IP-cím**.

5. Másolja ki az IP-címet, és konfigurálja újra a tartományi rekord vagy IP-mechanizmust.

## <a name="next-steps"></a>További lépések

Ez a cikk részletesen egy IP-cím módosítása az Azure által kezdeményezett előkészítése. IP-címek az Azure App Service kapcsolatos további információkért lásd: [bejövő és kimenő IP-címek az Azure App Service](overview-inbound-outbound-ips.md).
