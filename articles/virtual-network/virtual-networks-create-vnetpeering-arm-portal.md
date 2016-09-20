<properties
   pageTitle="Társviszony-létesítés virtuális hálózatok között az Azure Portal használatával | Microsoft Azure"
   description="Ismerje meg, hogyan hozhat létre virtuális hálózatot az Azure Portallal a Resource Managerben."
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/02/2016"
   ms.author="narayanannamalai"/>

# Társviszony-létesítés virtuális hálózatok között az Azure Portal használatával

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Ha a fenti forgatókönyv alapján kíván létrehozni társviszonyt virtuális hálózatok között az Azure Portal használatával, kövesse az alábbi lépéseket.

1. Egy böngészőből keresse fel a http://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. A virtuális hálózatok közötti társviszony-létesítéshez két hivatkozást kell létrehoznia a két virtuális hálózat között, egyet-egyet mindkét irányban. Először a VNET1-től a VNET2 felé irányuló virtuális hálózatok közötti társviszonyt hozhatja létre. A portálon kattintson a **Tallózás** elemre > **válassza a Virtuális hálózatok** elemet. 

    ![Társviszony-létesítés virtuális hálózatok között az Azure Portalon](./media/virtual-networks-create-vnetpeering-arm-portal/figure01.png)

3. A Virtuális hálózatok panelen válassza ki a VNET1 hálózatot, kattintson a Társviszonyok elemre, majd kattintson a Hozzáadás parancsra.

    ![Társviszony kiválasztása](./media/virtual-networks-create-vnetpeering-arm-portal/figure02.png)

4. A Társviszony hozzáadása panelen adjon meg egy LinkToVnet2 nevű társviszony-létesítési csatolást, válassza ki az előfizetést és a VNET2 társ virtuális hálózatot, majd kattintson az OK gombra.

    ![Virtuális hálózat csatolása](./media/virtual-networks-create-vnetpeering-arm-portal/figure03.png)

5. Miután a virtuális hálózatok között társviszony-létesítési csatolás létrejött. A csatolás állapotát a következőképpen láthatja:

    ![Csatolás állapota](./media/virtual-networks-create-vnetpeering-arm-portal/figure04.png)

6. Ezután hozza létre a VNET2-től a VNET1 felé irányuló virtuális hálózatok közötti társviszony-létesítési csatolást. A Virtuális hálózatok panelen válassza ki a VNET2 hálózatot, kattintson a Társviszonyok elemre, majd kattintson a Hozzáadás parancsra. 

    ![Társ másik virtuális hálózatból](./media/virtual-networks-create-vnetpeering-arm-portal/figure05.png)

7. A Társviszony hozzáadása panelen adjon meg egy LinkToVnet1 nevű társviszony-létesítési csatolást, válassza ki az előfizetést és a társ virtuális hálózatot, majd kattintson az OK gombra.

    ![Virtuális hálózat csempéjének létrehozása](./media/virtual-networks-create-vnetpeering-arm-portal/figure06.png)

8. Miután a virtuális hálózatok között társviszony-létesítési csatolás létrejött. A csatolás állapotát a következőképpen láthatja:

    ![Végső csatolás állapota](./media/virtual-networks-create-vnetpeering-arm-portal/figure07.png)

9. Ellenőrizze a LinkToVnet2 állapotát, amely szintén Kapcsolódva állapotúra változik.  

    ![Végső csatolás állapota 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure08.png)

10. MEGJEGYZÉS: A virtuális hálózatok közötti társviszony csak akkor jön létre, ha mindkét csatolás csatlakoztatva van. 

Mindegyik csatolás rendelkezik néhány konfigurálható tulajdonsággal:

|Beállítás|Leírás|Alapértelmezett|
|:-----|:----------|:------|
|AllowVirtualNetworkAccess|Megadja, hogy a társ virtuális hálózat címtere része legyen-e a Virtual_network címkének|Igen|
|AllowForwardedTraffic|Lehetővé teszi a társított virtuális hálózaton kívülről érkező forgalom elfogadását vagy elutasítását|Nem|
|AllowGatewayTransit|Lehetővé teszi a társ virtuális hálózat számára a virtuális hálózat átjárójának használatát|Nem|
|UseRemoteGateways|A társ virtuális hálózat átjárójának használata. A társ virtuális hálózatnak rendelkeznie kell konfigurált átjáróval, és be kell jelölni az AllowGatewayTransit tulajdonságot. Nem használhatja ezt a beállítást, ha konfigurált átjárót.|Nem|

A virtuális hálózatok közötti társviszony mindegyik csatolása rendelkezik a fenti tulajdonságokkal. A portálon a virtuális hálózatok közötti társviszony-létesítési csatolásra kattintva módosíthatja bármelyik elérhető beállítást, majd a Mentés gombra kattintva érvényesítheti a módosításokat.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

1. Egy böngészőből keresse fel a http://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Ebben a példában két, az A és B előfizetést használunk, valamint két felhasználót (FelhasználóA és FelhasználóB) az előfizetésekben lévő megfelelő jogosultságokkal.
3. A portálon kattintson a Tallózás elemre, és válassza a Virtuális hálózatok elemet. Kattintson a VNET lehetőségre, és kattintson a Hozzáadás elemre.

    ![2. forgatókönyv: tallózás](./media/virtual-networks-create-vnetpeering-arm-portal/figure09.png)

4. A Hozzáférés hozzáadása panelen kattintással válasszon ki egy szerepkört, és válassza a Hálózati közreműködő lehetőséget, kattintson a Felhasználók hozzáadása elemre, írja be a FelhasználóB bejelentkezési nevét, majd kattintson az OK gombra.

    ![RBAC](./media/virtual-networks-create-vnetpeering-arm-portal/figure10.png)

    Ez nem követelmény, társviszony akkor is létesíthető, ha a felhasználók önállóan adnak ki társviszony-létesítési kérelmet a megfelelő virtuális hálózataikra, és a kérések megegyeznek. Ha a helyi virtuális hálózat felhasználóihoz hozzáadja a másik virtuális hálózat rendszergazdai engedéllyel rendelkező felhasználóit, azzal leegyszerűsítheti a portálon történő beállítást. 

5. Ezután jelentkezzen be az Azure portálon a FelhasználóB felhasználóval, aki a B előfizetés rendszergazdai engedéllyel rendelkező felhasználója. Kövesse a fenti lépéseket a FelhasználóA Hálózati közreműködőként történő hozzáadásához.

    ![RBAC2](./media/virtual-networks-create-vnetpeering-arm-portal/figure11.png)

    MEGJEGYZÉS: Ki- és bejelentkezhet mindkét felhasználói munkamenetben a böngészőben annak ellenőrzéséhez, hogy a hitelesítést sikeresen engedélyezte.

6. Jelentkezzen be a portálra a FelhasználóA felhasználóként, navigáljon a VNET3 panelre, kattintson a Társviszony elemre, jelölje be az „Ismerem az erőforrás-azonosítómat” jelölőnégyzetet, és írja be a VNET5 erőforrás-azonosítóját az alábbi formátumban.

    /subscriptions/<erőforrás-azonosító>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/<VNET name>

    ![Erőforrás-azonosító](./media/virtual-networks-create-vnetpeering-arm-portal/figure12.png)

7. Jelentkezzen be a portálra a FelhasználóB felhasználóként, majd kövesse a fenti lépéseket egy társviszony-létesítési csatolás létrehozásához a VNET5 és a VNET3 között. 

    ![2. erőforrás-azonosító](./media/virtual-networks-create-vnetpeering-arm-portal/figure13.png)

8. Létrejön a társviszony, és a VNet3 hálózat bármely virtuális gépének képesnek kell lennie kommunikálni a VNet5 hálózat bármelyik virtuális gépével.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Első lépésként a virtuális hálózatok közötti társviszony-csatolásokat hoz létre a HubVnet és a VNET1 között. Figyelje meg, hogy a Továbbított forgalom engedélyezése beállítás nincs bejelölve a csatolás esetén.

    ![Alapszintű társviszony](./media/virtual-networks-create-vnetpeering-arm-portal/figure14.png)

2. Következő lépésként létrehozhatók a VNET1 és a HubVnet közötti társviszony-létesítési csatolások. Figyelje meg, hogy a „Továbbított forgalom engedélyezése” beállítás be van jelölve. 

    ![Alapszintű társviszony](./media/virtual-networks-create-vnetpeering-arm-portal/figure15a.png)

3. Miután létrejött a társviszony, ezen [cikk](virtual-network-create-udr-arm-ps.md) alapján meghatározhat felhasználó által megadott útvonalat a VNet1 forgalmának egy virtuális készüléken történő átirányításához a készülék képességeinek használata érdekében. Amikor meghatározza a következő ugrás címét az útvonalban, a címet beállíthatja a virtuális készülék IP-címére a társ HubVNet virtuális hálózatban.

## Virtuális hálózatok közötti társviszony eltávolítása

1.  Egy böngészőből keresse fel a http://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2.  Lépjen a Virtuális hálózat panelre, kattintson a Társviszonyok elemre, kattintson az eltávolítani kívánt csatolásra, majd kattintson a Törlés gombra. 

    ![1. törlés](./media/virtual-networks-create-vnetpeering-arm-portal/figure15.png)

3. Miután eltávolított egy csatolást a virtuális hálózatok közötti társviszonyban, a társviszony-csatolás állapota leválasztottra változik.

    ![2. törlés](./media/virtual-networks-create-vnetpeering-arm-portal/figure16.png)

4. Ebben az állapotban addig nem hozhatja létre újra a csatolást, amíg a társviszony-csatolás állapota Kezdeményezettre nem változik. Javasoljuk, hogy mindkét csatolást távolítsa el, mielőtt újra létrehozza a virtuális hálózatok közötti társviszonyt. 



<!--HONumber=sep16_HO1-->


