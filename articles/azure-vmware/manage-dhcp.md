---
title: A DHCP létrehozása és kezelése
description: Ez a cikk bemutatja, hogyan kezelheti a DHCP-t az Azure VMware megoldásban.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: ba7c3983f55ed729c77d2f24d3ef311a00522148
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069560"
---
# <a name="how-to-create-and-manage-dhcp-in-azure-vmware-solution"></a>A DHCP létrehozása és kezelése az Azure VMWare-megoldásban

A NSX-T lehetővé teszi a DHCP konfigurálását a saját felhőhöz. Ha azt tervezi, hogy a NSX-T használja a DHCP-kiszolgáló üzemeltetéséhez, tekintse meg a [DHCP-kiszolgáló létrehozása](#create-dhcp-server)című témakört. Ellenkező esetben, ha külső külső DHCP-kiszolgálóval rendelkezik a hálózaton, és továbbítani szeretné a kérelmeket a DHCP-kiszolgálónak, tekintse meg a [DHCP-továbbító szolgáltatás létrehozása](#create-dhcp-relay-service)című témakört.

## <a name="create-dhcp-server"></a>DHCP-kiszolgáló létrehozása

A következő lépésekkel konfigurálhatja a DHCP-kiszolgálót a NSX-T-n.

A NSX Manager alkalmazásban navigáljon a **hálózatkezelés** lapra, és válassza a **DHCP** lehetőséget az **IP-kezelés**területen. Válassza a **kiszolgáló hozzáadása** gombot. Ezután adja meg a kiszolgáló nevét és a kiszolgáló IP-címét. Ha elkészült, válassza a **Mentés**lehetőséget.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="DHCP-kiszolgáló hozzáadása" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>A DHCP-kiszolgáló összekötése az 1. rétegbeli átjáróval.

1. Válassza az **1. szintű átjárók**lehetőséget, válassza ki az átjárót, és válassza a **Szerkesztés** lehetőséget.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Válassza ki a használni kívánt átjárót" border="true":::

1. Alhálózat hozzáadása az **IP-lefoglalási csoport nélküli** kiválasztásával

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="alhálózat hozzáadása" border="true":::

1. A következő képernyőn válassza a **DHCP helyi kiszolgáló** elemet a **típus** legördülő listából. **DHCP-kiszolgáló**esetén válassza az **alapértelmezett DHCP** lehetőséget, majd válassza a **Mentés**lehetőséget.

   :::image type="content" source="./media/manage-dhcp/set-ip-address-management.png" alt-text="DHCP-kiszolgáló beállításainak kiválasztása" border="true":::

1. Az **1. rétegbeli átjáró** ablakban válassza a **Mentés**lehetőséget. A következő képernyőn megtekintheti a **mentett módosításokat**, majd a **Szerkesztés bezárásával** befejezheti a módosítást.

### <a name="add-a-network-segment"></a>Hálózati szegmens hozzáadása

A DHCP-kiszolgáló létrehozása után hozzá kell adnia egy hálózati szegmenst.

1. A NSX-T területen válassza a **hálózatkezelés** fület, majd válassza a **szegmensek** lehetőséget a **kapcsolat**alatt. Válassza a **szegmens hozzáadása**elemet. Nevezze el a szegmenst és az 1. rétegbeli átjáróhoz való kapcsolódást. Ezután válassza az **alhálózatok beállítása** lehetőséget az új alhálózat konfigurálásához. 

   :::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="új hálózati szegmens hozzáadása" border="true":::

1. Az **alhálózatok beállítása** ablakban válassza az **alhálózat hozzáadása**elemet. Adja meg az átjáró IP-címét és a DHCP-tartományt, majd válassza a **Hozzáadás** , majd az **alkalmaz** lehetőséget.

   :::image type="content" source="./media/manage-dhcp/add-subnet-segment.png" alt-text="hálózati szegmens hozzáadása" border="true":::

1. Ha elkészült, válassza a **Mentés** lehetőséget egy hálózati szegmens hozzáadásának befejezéséhez.

   :::image type="content" source="./media/manage-dhcp/segments-complete.png" alt-text="a szegmensek készek" border="true":::

## <a name="create-dhcp-relay-service"></a>DHCP-továbbító szolgáltatás létrehozása

1. A NSX-T ablakban válassza a **hálózatkezelés** fület, majd az **IP-kezelés**területen válassza a **DHCP**lehetőséget. Válassza a **kiszolgáló hozzáadása**lehetőséget. Válassza a DHCP-továbbító lehetőséget a **kiszolgáló típusa mezőben** , majd adja meg a továbbító kiszolgáló kiszolgálójának nevét és IP-címét. A módosítások mentéséhez válassza a **Mentés** gombot.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="DHCP-továbbító kiszolgáló létrehozása" border="true":::

1. Válassza ki az **1. rétegbeli átjárókat** a **kapcsolat**alatt. Válassza ki a függőleges három pontot az 1. rétegbeli átjárón, és válassza a **Szerkesztés**lehetőséget.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="1. szintű átjáró szerkesztése" border="true":::

1. Az IP-címek kiosztásának definiálásához válassza a **nincs IP-foglalási készlet** lehetőséget.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="IP-címek kiosztásának szerkesztése" border="true":::

1. A párbeszédpanelen a **típus**mezőben válassza a **DHCP-továbbító kiszolgáló**elemet. A **DHCP-továbbító** legördülő menüben válassza ki a DHCP-továbbító kiszolgálót. Ha elkészült, válassza a **Mentés** lehetőséget.

   :::image type="content" source="./media/manage-dhcp/set-ip-address-management-relay.png" alt-text="IP-címek kezelésének beállítása" border="true":::

## <a name="specify-a-dhcp-range-ip-on-segment"></a>DHCP-tartomány IP-címének meghatározása a szegmensen

> [!NOTE]
> Ez a konfiguráció szükséges a DHCP-továbbítási funkciók megvalósításához a DHCP-ügyfél szegmensén. 

1. A **kapcsolat**területen válassza a **szegmensek**lehetőséget. Válassza ki a függőleges ellipsziseket, és válassza a **Szerkesztés**lehetőséget. Ehelyett, ha új szegmenst szeretne felvenni, válassza a **szegmens hozzáadása** lehetőséget egy új szegmens létrehozásához.

   :::image type="content" source="./media/manage-dhcp/edit-segments.png" alt-text="hálózati alhálózat szerkesztése" border="true":::

1. Adja meg a szegmens részleteit. Válassza ki az **alhálózatok alatt található** értéket, vagy adja **meg az alhálózatokat** az alhálózat hozzáadásához vagy módosításához.

   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="hálózati szegmensek" border="true":::

1. Válassza ki a függőleges ellipsziseket, és válassza a **Szerkesztés**lehetőséget. Ha új alhálózatot kell létrehoznia, válassza az **alhálózat hozzáadása** lehetőséget az átjáró létrehozásához és a DHCP-tartomány konfigurálásához. Adja meg az IP-készlet tartományát, és válassza az **alkalmaz**, majd a **Mentés** lehetőséget.

   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="alhálózatok szerkesztése" border="true":::

1. Most már hozzá van rendelve egy DHCP-kiszolgáló készlet a szegmenshez.

   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="A szegmenshez hozzárendelt DHCP-kiszolgáló készlete" border="true":::
