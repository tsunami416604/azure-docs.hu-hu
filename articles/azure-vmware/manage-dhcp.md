---
title: A DHCP létrehozása és kezelése
description: Ez a cikk bemutatja, hogyan kezelheti a DHCP-t az Azure VMware megoldásban.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: cb74ed4474cc14081e59142f2f60915fccd47559
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461056"
---
# <a name="how-to-create-and-manage-dhcp-in-azure-vmware-solution"></a>A DHCP létrehozása és kezelése az Azure VMware-megoldásban

A NSX-T lehetővé teszi a DHCP konfigurálását a saját felhőhöz. Ha a NSX-T használatával futtatja a DHCP-kiszolgálót, tekintse meg a [DHCP-kiszolgáló létrehozása](#create-dhcp-server)című témakört. Ellenkező esetben, ha harmadik féltől származó külső DHCP-kiszolgálóval rendelkezik a hálózaton, tekintse meg a [DHCP-továbbító szolgáltatás létrehozása](#create-dhcp-relay-service)című témakört.

## <a name="create-dhcp-server"></a>DHCP-kiszolgáló létrehozása

A következő lépésekkel konfigurálhatja a DHCP-kiszolgálót a NSX-T-n.

1. A NSX Managerben navigáljon a **hálózatkezelés** lapra, és válassza a **DHCP**lehetőséget. 
1. Válassza a **kiszolgáló hozzáadása** lehetőséget, majd adja meg a kiszolgáló nevét és IP-címét. 
1. Válassza a **Mentés** lehetőséget.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="DHCP-kiszolgáló hozzáadása" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>A DHCP-kiszolgáló összekötése az 1. rétegbeli átjáróval.

1. Válassza ki az **1. szintű átjárókat**, az átjárót a listából, majd válassza a **Szerkesztés**lehetőséget.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="DHCP-kiszolgáló hozzáadása" border="true":::

1. Alhálózat hozzáadásához válassza a **nincs IP-foglalási csoport** lehetőséget.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="DHCP-kiszolgáló hozzáadása" border="true":::

1. A **típushoz**válassza a **DHCP helyi kiszolgáló** lehetőséget. 
1. Válassza a **DHCP-kiszolgáló** **alapértelmezett DHCP** lehetőséget, majd kattintson a **Mentés**gombra.


1. Az **1. rétegbeli átjáró** ablakban válassza a **Mentés**lehetőséget. 
1. A befejezéshez kattintson a **Szerkesztés Bezárás** gombra.

### <a name="add-a-network-segment"></a>Hálózati szegmens hozzáadása

A DHCP-kiszolgáló létrehozása után hozzá kell adnia egy hálózati szegmenst.

1. A NSX-T területen válassza a **hálózatkezelés** fület, majd válassza a **szegmensek** lehetőséget a **kapcsolat**alatt. 
1. Válassza a **szegmens hozzáadása** lehetőséget, és nevezze el a szegmenst és a kapcsolódást az 1. rétegbeli átjáróhoz. 
1. Válassza az **alhálózatok beállítása** lehetőséget az új alhálózat konfigurálásához. 

   :::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="DHCP-kiszolgáló hozzáadása" border="true":::

1. Az **alhálózatok beállítása** ablakban válassza az **alhálózat hozzáadása**elemet. 
1. Adja meg az átjáró IP-címét és a DHCP-tartományt, majd válassza a **Hozzáadás** , majd az **alkalmaz** lehetőséget.

1. Válassza a **Mentés** lehetőséget az új hálózati szegmens hozzáadásához.

## <a name="create-dhcp-relay-service"></a>DHCP-továbbító szolgáltatás létrehozása

1. Válassza a **hálózatkezelés** fület, majd az **IP-kezelés**területen válassza a **DHCP**lehetőséget. 
1. Válassza a **kiszolgáló hozzáadása**lehetőséget. 
1. Válassza a DHCP-továbbító lehetőséget a **kiszolgáló típusa** mezőben, majd adja meg a továbbító kiszolgáló kiszolgálójának nevét és IP-címét. 
1. Válassza a **Mentés** lehetőséget.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="DHCP-kiszolgáló hozzáadása" border="true":::

1. Válassza ki az **1. rétegbeli átjárókat** a **kapcsolat**alatt. 
1. Válassza ki a függőleges három pontot az 1. rétegbeli átjárón, és válassza a **Szerkesztés**lehetőséget.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="DHCP-kiszolgáló hozzáadása" border="true":::

1. Az IP-címek kiosztásának definiálásához válassza a **nincs IP-foglalási készlet** lehetőséget.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="DHCP-kiszolgáló hozzáadása" border="true":::

1. Válassza a **DHCP-továbbító kiszolgáló** lehetőséget a **típushoz**.
1. Válassza ki a DHCP Relay-kiszolgálót a **DHCP-továbbítóhoz**. 
1. Válassza a **Mentés** lehetőséget.


## <a name="specify-a-dhcp-range-ip-on-a-segment"></a>DHCP-tartomány IP-címének meghatározása egy szegmensen

> [!NOTE]
> Ez a konfiguráció szükséges a DHCP-továbbítási funkciók megvalósításához a DHCP-ügyfél szegmensén. 

1. A **kapcsolat**területen válassza a **szegmensek**lehetőséget. 
1. Válassza ki a függőleges ellipsziseket, és válassza a **Szerkesztés**lehetőséget. 

   >[!TIP]
   >Ha új szegmenst szeretne felvenni, válassza a **szegmens hozzáadása**elemet.

1. Adja meg a szegmens részleteit. 
1. Válassza ki az **alhálózatok beállítása** az alhálózat hozzáadásához vagy módosításához területen található értéket.

   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="DHCP-kiszolgáló hozzáadása" border="true":::

1. Válassza ki a függőleges ellipsziseket, és válassza a **Szerkesztés**lehetőséget. Ha új alhálózatot kell létrehoznia, válassza az **alhálózat hozzáadása** lehetőséget az átjáró létrehozásához és a DHCP-tartomány konfigurálásához. 
1. Adja meg az IP-készlet tartományát, és válassza az **alkalmaz**, majd a **Mentés** lehetőséget.

   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="DHCP-kiszolgáló hozzáadása" border="true":::

   A szegmenshez hozzá van rendelve egy DHCP-kiszolgáló készlet.

   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="DHCP-kiszolgáló hozzáadása" border="true":::
