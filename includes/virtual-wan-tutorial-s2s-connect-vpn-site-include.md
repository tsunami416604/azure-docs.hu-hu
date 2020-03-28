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
ms.openlocfilehash: 8b338f25e9771f5947fd494cfb00d0f6cb9ef67a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75450830"
---
1. Válassza **a VPN-helyek csatlakoztatása** lehetőséget a **Helyek csatlakoztatása** lap megnyitásához.

    ![Csatlakoztassa](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "csatlakozásra")

   Töltse ki a következő mezőket:

   * Adjon meg egy előmegosztott kulcsot. Ha nem ad meg kulcsot, az Azure automatikusan létrehoz egyet.
   * Válassza ki a Protokoll és az IPsec beállításait. Lásd az [alapértelmezett/egyéni IPSec-adatok] (https://docs.microsoft.com/azure/virtual-wan/virtual-wan-ipsec)
   * Válassza ki az **Alapértelmezett útvonal propagálása**megfelelő beállítást. Az **Engedélyezés** beállítás lehetővé teszi, hogy a virtuális elosztó egy megtanult alapértelmezett útvonalat továbbítson ehhez a kapcsolathoz. Ez a jelző csak akkor engedélyezi az alapértelmezett útvonal-terjesztést egy kapcsolatra, ha a Virtual WAN hub már megtanulta az alapértelmezett útvonalat a tűzfal központi telepítésének eredményeként, vagy ha egy másik csatlakoztatott hely kényszerített bújtatása engedélyezve van. Az alapértelmezett útvonal nem a Virtual WAN hubból származik.

2. Kattintson a **Csatlakozás** gombra.
3. Néhány perc múlva a webhely megmutatja a kapcsolat és a kapcsolat állapotát.

   ![Állapot](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **Kapcsolat állapota:** Ez az Azure-erőforrás állapota a kapcsolat, amely összeköti a VPN-hely az Azure hub VPN-átjáró. Miután ez a vezérlősík-művelet sikeres, az Azure VPN-átjáró és a helyszíni VPN-eszköz folytatja a kapcsolatot.

   **Kapcsolat állapota:** Ez a tényleges kapcsolat (adatelérési út) állapota az Azure VPN-átjárója és a VPN-hely között. A következő állapotok bármelyikét megmutathatja:

    * **Ismeretlen**: Ez az állapot általában akkor látható, ha a háttérrendszerek egy másik állapotra való áttérésen dolgoznak.
    * **Csatlakozás:** Az Azure VPN-átjáró megpróbálja elérni a tényleges helyszíni VPN-hely.
    * **Csatlakoztatva:** Az Azure VPN-átjáró és a helyszíni VPN-hely között létrejön a kapcsolat.
    * **Leválasztva:** Ez az állapot akkor látható, ha bármilyen okból (a helyszínen vagy az Azure-ban), a kapcsolat megszakadt.
4. A központi VPN-webhelyen belül a következőket is megteheti: 

   * A VPN-kapcsolat szerkesztése vagy törlése.
   * Törölje a webhelyet az Azure Portalon.
   * Töltse le az azure-i oldal részleteit a hely melletti környezet (...) menü használatával. Ha a központ összes csatlakoztatott webhelyének konfigurációját szeretné letölteni, válassza a **vpn-konfiguráció letöltése parancsot** a felső menüben.
