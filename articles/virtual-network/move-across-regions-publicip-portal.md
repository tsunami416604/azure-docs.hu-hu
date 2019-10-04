---
title: Azure-beli nyilvános IP-cím áthelyezése egy másik Azure-régióba a Azure Portal használatával
description: Azure Resource Manager sablonnal áthelyezheti az Azure nyilvános IP-címét az egyik Azure-régióból a másikba a Azure Portal használatával.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 2610afe9df06d28f2b75bd0023f7ec5a3fe9e56c
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219226"
---
# <a name="move-azure-public-ip-to-another-region-using-the-azure-portal"></a>Azure-beli nyilvános IP-cím áthelyezése másik régióba a Azure Portal használatával

Különböző helyzetekben érdemes áthelyezni a meglévő Azure nyilvános IP-címeket az egyik régióból a másikba. Előfordulhat például, hogy létre szeretne hozni egy nyilvános IP-címet ugyanazzal a konfigurációval és SKU-val a teszteléshez. Előfordulhat, hogy a vész-helyreállítási tervezés részeként egy nyilvános IP-címet is át szeretne helyezni egy másik régióba.

Az Azure nyilvános IP-címei régió-specifikusak, és nem helyezhetők át egyik régióból a másikba. A nyilvános IP-címek meglévő konfigurációjának exportálásához azonban használhat egy Azure Resource Manager sablont is.  Az erőforrást egy másik régióban is elvégezheti, ha a nyilvános IP-címet egy sablonba exportálja, módosítja a paramétereket, hogy azok megfeleljenek a célként megadott régiónak, majd üzembe helyezi a sablont az új régióban.  A Resource Managerrel és a sablonokkal kapcsolatos további [információkért lásd: gyors útmutató: Azure Resource Manager-sablonok létrehozása és üzembe helyezése a](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)Azure Portal használatával.


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure-beli nyilvános IP-cím az áthelyezni kívánt Azure-régióban található.

- Az Azure nyilvános IP-címei nem helyezhetők át a régiók között.  Az új nyilvános IP-címet hozzá kell rendelnie a célként megadott régióban található erőforrásokhoz.

- Nyilvános IP-konfiguráció exportálásához és sablon üzembe helyezéséhez egy másik régióban lévő nyilvános IP-cím létrehozásához szüksége lesz a hálózati közreműködő szerepkörre vagy magasabbra.

- Azonosítsa a forrás hálózatkezelési elrendezést és az összes éppen használt erőforrást. Ez az elrendezés tartalmaz, de nem korlátozódik a terheléselosztó, a hálózati biztonsági csoportok (NSG) és a virtuális hálózatok számára.

- Győződjön meg arról, hogy az Azure-előfizetés lehetővé teszi, hogy nyilvános IP-címeket hozzon létre a használt célcsoportban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Győződjön meg arról, hogy az előfizetése elegendő erőforrással rendelkezik a nyilvános IP-címek ezen folyamathoz való hozzáadásának támogatásához.  Tekintse meg a következőt: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Előkészítés és áthelyezés
A következő lépések bemutatják, hogyan készítse elő a nyilvános IP-címet a konfiguráció áthelyezéséhez egy Resource Manager-sablon használatával, és a nyilvános IP-konfigurációt helyezze át a célként megadott régióba a Azure Portal használatával.

### <a name="export-the-template-and-deploy-from-a-script"></a>Sablon exportálása és üzembe helyezése parancsfájlból

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **erőforráscsoporthoz**.
2. Keresse meg a forrás nyilvános IP-címet tartalmazó erőforráscsoportot, és kattintson rá.
3. Válassza > **Beállítások** > **Exportálás sablon**lehetőséget.
4. A **sablon exportálása** panelen válassza a **telepítés** lehetőséget.
5. Kattintson a **sablon** > **szerkesztése paraméterek** lehetőségre a **Parameters. JSON** fájl megnyitásához az online szerkesztőben.
8. A nyilvános IP-cím paraméterének szerkesztéséhez módosítsa a forrás nyilvános IP-címe **paraméter** > **értéke** alatt található tulajdonságot a cél nyilvános IP-címére, és győződjön meg arról, hogy a név idézőjelek közé esik:

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```
8.  Kattintson a **Mentés** gombra a szerkesztőben.

9.  Kattintson a sablon**szerkesztése** elemre, hogy megnyissa a **template. JSON** fájlt az online szerkesztőben. > 

10. A nyilvános IP-címet áthelyező cél régió szerkesztéséhez módosítsa a **Location (hely** ) tulajdonságot az **erőforrások**területen.

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```

11. A régióbeli hely kódjának beszerzéséhez tekintse meg az [Azure-helyeket](https://azure.microsoft.com/global-infrastructure/locations/).  A régió kódja a régió neve szóközök nélkül, **Közép-USA** = **CentralUS**.

12. A sablon egyéb paramétereit is módosíthatja, és a követelményektől függően választható:

    * **SKU** – a konfigurációban a nyilvános IP-cím SKU-jának a standard és az alap közötti értékről a standard típusra módosítható a **sablon. JSON** fájljának **SKU** > **Name** tulajdonságának megváltoztatásával:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Az alapszintű és a standard SKU nyilvános IP-címei közötti különbségekkel kapcsolatos további információkért lásd: [nyilvános IP-cím létrehozása, módosítása vagy törlése](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address):

    * **Nyilvános IP-kiosztási módszer** és **Üresjárati időkorlát** – a sablon mindkét beállítását módosíthatja úgy, hogy a **PublicIPAllocationMethod** tulajdonságot **dinamikusról** **statikusra** vagy statikusra változtatja **a** dinamikus értékre. Az Üresjárat időkorlátja módosítható úgy, hogy módosítja a **idleTimeoutInMinutes** tulajdonságot a kívánt értékre.  Az alapértelmezett érték **4**:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        A kiosztási módszerekkel és az üresjárati időtúllépési értékekkel kapcsolatos további információkért lásd: [nyilvános IP-cím létrehozása, módosítása vagy törlése](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


13. Kattintson a **Save (Mentés** ) gombra az online szerkesztőben.

14. Az alapértékek**előfizetése** lehetőségre kattintva válassza ki azt az előfizetést, ahol a cél nyilvános IP-címet telepíteni fogja. > 

15. Az alapszintű**erőforráscsoport** elemre kattintva kiválaszthatja azt az erőforráscsoportot, amelyben a célként megadott nyilvános IP-címet telepíteni fogja. >   Az **új létrehozása** lehetőségre kattintva létrehozhat egy új erőforráscsoportot a célként megadott nyilvános IP-címhez.  Győződjön meg arról, hogy a név nem ugyanaz, mint a meglévő forrás nyilvános IP-cím forrás-erőforráscsoport.

16. Győződjön meg arról, hogy az **alapvető beállítások** > **helye** arra a célhelyre van beállítva, ahol a nyilvános IP-címet telepíteni kívánja.

17. Ellenőrizze a **Beállítások** területen, hogy a név megegyezik-e a fenti Parameters Editorban megadott névvel.

18. Jelölje be a jelölőnégyzetet a **feltételek és KIkötések**területen.

19. A cél nyilvános IP-cím üzembe helyezéséhez kattintson a **vásárlás** gombra.

## <a name="discard"></a>Elvetés

Ha el szeretné vetni a cél nyilvános IP-címet, törölje a célként megadott nyilvános IP-címet tartalmazó erőforráscsoportot.  Ehhez válassza ki az erőforráscsoportot az irányítópulton a portálon, és válassza a **Törlés** lehetőséget az Áttekintés oldal tetején.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások elvégzéséhez és a nyilvános IP-cím áthelyezésének befejezéséhez törölje a forrás nyilvános IP-címet vagy erőforráscsoportot. Ehhez válassza ki a nyilvános IP-címet vagy erőforráscsoportot az irányítópulton a portálon, és válassza a **Törlés** lehetőséget az egyes lapok tetején.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure nyilvános IP-címet helyezett át egyik régióból a másikba, és megtisztította a forrás erőforrásait.  Ha többet szeretne megtudni a régiók és a vész-helyreállítás között az Azure-ban, tekintse meg a következőt:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
