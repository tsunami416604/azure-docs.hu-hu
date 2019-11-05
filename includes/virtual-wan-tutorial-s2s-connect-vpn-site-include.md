---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bcc4af837cc28a18345e49cd39ddf88d24e44dd8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488895"
---
1. Válassza a **VPN-helyek összekapcsolása** lehetőséget a **helyek összekapcsolását** ismertető lap megnyitásához.

    ![csatlakozni](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "csatlakozásra")

   Hajtsa végre a következő mezőket:

   * Adjon meg egy előmegosztott kulcsot. Ha nem ad meg kulcsot, az Azure automatikusan létrehoz egyet.
   * Válassza ki a protokollt és az IPsec-beállításokat. Tekintse meg az alapértelmezett/egyéni IPSec-részleteket (helyezze a lapra mutató hivatkozást)
   * Válassza ki a megfelelő beállítást az **alapértelmezett útvonal propagálásához**. Az **Engedélyezés** beállítás lehetővé teszi, hogy a virtuális központ a megtanult alapértelmezett útvonalat propagálja ehhez a kapcsolódáshoz. Ez a jelző az alapértelmezett útvonal-propagálást csak akkor engedélyezi egy kapcsolathoz, ha az alapértelmezett útvonalat már megismerte a virtuális WAN-központ a tűzfal központi telepítésének eredményeképpen, vagy ha egy másik csatlakoztatott hely kényszerített bújtatást engedélyez. Az alapértelmezett útvonal nem a virtuális WAN-hubhoz származik.

2. Kattintson a **Csatlakozás** gombra.
3. Néhány percen belül a hely megjeleníti a kapcsolat és a kapcsolat állapotát.

   ![állapota](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   A **kapcsolatok állapota:** Ez a kapcsolat Azure-erőforrásának állapota, amely összekapcsolja a VPN-helyet az Azure hub VPN-átjáróján. Ha a vezérlési sík működése sikeres, az Azure VPN Gateway és a helyszíni VPN-eszköz továbbra is kapcsolatot létesít.

   **Kapcsolat állapota:** Ez az Azure VPN Gateway és a VPN-hely közötti tényleges kapcsolat (adatelérési út) állapota. A következő állapotok bármelyikét megjelenítheti:

    * **Ismeretlen**: ez az állapot általában akkor látható, ha a háttérrendszer más állapotba való áttérésre is alkalmas.
    * **Csatlakozás**: az Azure VPN Gateway megpróbálja elérni a tényleges helyszíni VPN-helyet.
    * **Csatlakoztatva**: kapcsolat létrejött az Azure VPN Gateway és a helyszíni VPN-hely között.
    * **Leválasztva**: ez az állapot akkor látható, ha valamilyen okból (a helyszínen vagy az Azure-ban) a kapcsolat megszakadt.
4. Egy központi VPN-helyen belül a következőket teheti: 

   * A VPN-kapcsolat szerkesztése vagy törlése.
   * Törölje a helyet a Azure Portal.
   * Töltse le az adott ág-specifikus konfigurációt az Azure-oldalról a hely melletti Context (...) menü használatával. Ha a hub összes csatlakoztatott helyének konfigurációját le szeretné tölteni, válassza a felső menüben a **VPN-konfiguráció letöltése** lehetőséget.