---
title: Az Azure nyilvános IP-cím áthelyezése egy másik Azure-régióba az Azure Portal használatával
description: Az Azure Resource Manager-sablonnal áthelyezi az Azure nyilvános IP-címét az egyik Azure-régióból a másikba az Azure Portal használatával.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 6d10265e8383b68ebe13c95d8b2a9632668e85da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641400"
---
# <a name="move-azure-public-ip-to-another-region-using-the-azure-portal"></a>Az Azure nyilvános IP-cím áthelyezése egy másik régióba az Azure Portal használatával

Vannak különböző forgatókönyvek, amelyekben szeretné áthelyezni a meglévő Azure nyilvános IP-k egyik régióból a másikba. Előfordulhat például, hogy egy nyilvános IP-címet szeretne létrehozni ugyanazzal a konfigurációval és termékku tesztelésre. Előfordulhat, hogy egy nyilvános IP-cím áthelyezése egy másik régióba a vész-helyreállítási tervezés részeként.

Az Azure nyilvános IP-k régióspecifikusak, és nem helyezhetők át egyik régióból a másikba. Azonban egy Azure Resource Manager sablon használatával exportálhatja a nyilvános IP-cím meglévő konfigurációját.  Ezután egy másik régióban is elhelyezheti az erőforrást úgy, hogy a nyilvános IP-címet egy sablonba exportálja, módosítja a paramétereket a célrégiónak megfelelően, majd telepíti a sablont az új régióba.  Az Erőforrás-kezelőről és a sablonokról további információt a [Rövid útmutató: Azure Resource Manager-sablonok létrehozása és üzembe helyezése az Azure Portal használatával című témakörben talál.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure nyilvános IP-cím az Azure-régióban, ahonnan át szeretne helyezni.

- Az Azure nyilvános IP-k nem helyezhetők át a régiók között.  Az új nyilvános ip-címet a célrégió erőforrásaihoz kell társítania.

- Nyilvános IP-konfiguráció exportálásához és egy sablon központi telepítéséhez nyilvános IP-cím más régióban történő létrehozásához a Hálózati közreműködő szerepkörvagy magasabb szükséges.

- Azonosítsa a forráshálózati elrendezést és az összes jelenleg használt erőforrást. Ez az elrendezés magában foglalja, de nem kizárólagosan a terheléselosztók, hálózati biztonsági csoportok (NSG- k) és a virtuális hálózatok.

- Ellenőrizze, hogy az Azure-előfizetés lehetővé teszi-e nyilvános IP-k létrehozását a használt célrégióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Győződjön meg arról, hogy az előfizetés elegendő erőforrással rendelkezik a nyilvános IP-k hozzáadásának támogatásához ehhez a folyamathoz.  Lásd: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Felkészülés és mozgás
A következő lépések bemutatják, hogyan készítse elő a nyilvános IP-t a konfigurációs áthelyezésegy Resource Manager-sablon használatával, és helyezze át a nyilvános IP-konfigurációt a célrégióba az Azure Portalhasználatával.

### <a name="export-the-template-and-deploy-from-a-script"></a>A sablon exportálása és üzembe helyezése parancsfájlból

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **erőforráscsoportokba.**
2. Keresse meg a forrásnyilvános IP-címet tartalmazó erőforráscsoportot, és kattintson rá.
3. Válassza > **Beállítások** > **exportálása sablont.**
4. Válassza a **Telepítés lehetőséget** az **Exportálás sablon** panelen.
5. Kattintson **a TEMPLATE** > **Paraméterek szerkesztése parancsra** a **parameters.json** fájl online szerkesztőben való megnyitásához.
8. A nyilvános IP-név paraméterének szerkesztéséhez módosítsa a **paraméteralatti tulajdonságot** > a forrás nyilvános IP-névről**value** a megcélzott nyilvános IP nevére, győződjön meg arról, hogy a név idézőjelek között van:

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
8.  Kattintson a **Mentés** a szerkesztőben gombra.

9.  Kattintson **a TEMPLATE** > **Szerkesztés sablon** ra a **template.json** fájl online szerkesztőben való megnyitásához.

10. A nyilvános IP-címet áthelyező célrégió szerkesztéséhez módosítsa a **helytulajdonságot** az **erőforrások**alatt:

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

11. A régióhely-kódok beszerzéséhez olvassa el az Azure Locations ( Azure Locations ) ( Azure Locations ) ( Azure Locations ) ( Azure Locations ) [(A régió hely](https://azure.microsoft.com/global-infrastructure/locations/)  A régió kódja a terület neve szóközök nélkül, **az USA** = középső**központja**.

12. Ha úgy dönt, módosíthatja a sablon egyéb paramétereit is, és a követelményektől függően nem kötelező:

    * **Sku** - A nyilvános IP-cím skuját a konfigurációban a standardról az alapszintűre vagy az alapparancsra módosíthatja a **sku** > **név** tulajdonságának módosításával a **template.json** fájlban:

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

        Az alapszintű és a szabványos sku nyilvános ips közötti különbségekről a [Nyilvános IP-cím létrehozása, módosítása vagy törlése](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)című témakörben talál további információt:

    * **Nyilvános IP-foglalási módszer** és **tétlen időtúlszállás** – A sablon mindkét beállítását módosíthatja a **publicIPAllocationMethod** tulajdonság **dinamikusról** **statikusra** vagy **statikusra** dinamikusra **dinamikusra.** Az alapjárati időhosszabbítás az **idleTimeoutInMinutes** tulajdonság kívánt értékre történő módosításával módosítható.  Az alapértelmezett érték **4:**

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

        A felosztási módszerekről és az alapjárati időtúlterhelési értékekről a [Nyilvános IP-cím létrehozása, módosítása vagy törlése](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)című témakörben talál további információt.


13. Kattintson a **Mentés** gombra az online szerkesztőben.

14. Kattintson **a BASICS-előfizetés** > **Subscription** elemre annak az előfizetésnek a kiválasztásához, amelyhez a nyilvános célIP-cím lesz telepítve.

15. Kattintson **a BASICS** > **erőforráscsoport** válassza ki az erőforráscsoportot, ahol a cél nyilvános IP lesz telepítve.  Az **Új létrehozása gombra** kattintva új erőforráscsoportot hozhat létre a nyilvános ip-címhez.  Győződjön meg arról, hogy a név nem ugyanaz, mint a meglévő forrás nyilvános IP forráserőforrás-csoportja.

16. Ellenőrizze, hogy a **BASICS** > **hely** be van-e állítva arra a célhelyre, ahol a nyilvános IP-címet telepíteni szeretné.

17. Ellenőrizze a **BEÁLLÍTÁSOK** csoportban, hogy a név megegyezik-e a fenti paraméterszerkesztőben megadott névvel.

18. Jelölje be a jelölőnégyzetet a **FELTÉTELEK ÉS FELTÉTELEK mezőben.**

19. Kattintson a **Vásárlás** gombra a nyilvános cél IP üzembe helyezéséhez.

## <a name="discard"></a>Elvetés

Ha el szeretné vetni a nyilvános célIP-t, törölje a nyilvános célIP-t tartalmazó erőforráscsoportot.  Ehhez jelölje ki az erőforráscsoportot az irányítópulton a portálon, és válassza a **Törlés** lehetőséget az áttekintő lap tetején.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások véglegesítéséhez és a nyilvános IP áthelyezése befejezéséhez törölje a forrás nyilvános IP-címét vagy erőforráscsoportját. Ehhez válassza ki a nyilvános IP-címet vagy erőforráscsoportot az irányítópultról a portálon, és válassza a **Törlés** lehetőséget az egyes lapok tetején.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban áthelyezte az Azure nyilvános IP-címét az egyik régióból a másikba, és megtisztította a forráserőforrásokat.  Ha többet szeretne tudni az erőforrások régiók közötti áthelyezéséről és az Azure-beli vészhelyreállításról:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
