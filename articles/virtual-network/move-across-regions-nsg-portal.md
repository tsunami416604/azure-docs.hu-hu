---
title: Az Azure hálózati biztonsági csoportjának (NSG) áthelyezése egy másik Azure-régióba az Azure Portal használatával
description: Az Azure Resource Manager-sablonnal áthelyezi az Azure hálózati biztonsági csoportját az egyik Azure-régióból a másikba az Azure Portal használatával.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: dce267178c3caf813ccdcac4bba86ccfde3f3421
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647186"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Az Azure hálózati biztonsági csoportjának (NSG) áthelyezése egy másik régióba az Azure Portal használatával

Vannak különböző forgatókönyvek, amelyekben szeretné áthelyezni a meglévő NSG-k egyik régióból a másikba. Előfordulhat például, hogy azonos konfigurációs és biztonsági szabályokkal rendelkező NSG-t szeretne létrehozni a teszteléshez. Előfordulhat, hogy egy NSG-t is át szeretne helyezni egy másik régióba a vész-helyreállítási tervezés részeként.

Az Azure biztonsági csoportok nem helyezhetők át egyik régióból a másikba. Azonban egy Azure Resource Manager sablon használatával exportálhatja az NSG meglévő konfigurációs és biztonsági szabályait.  Ezután egy másik régióban is elhelyezheti az erőforrást úgy, hogy az NSG-t egy sablonba exportálja, módosítja a paramétereket a célrégiónak megfelelően, majd telepíti a sablont az új régióba.  Az Erőforrás-kezelőről és a sablonokról további információt a [Rövid útmutató: Azure Resource Manager-sablonok létrehozása és üzembe helyezése az Azure Portal használatával című témakörben talál.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure hálózati biztonsági csoport az Azure-régióban, ahonnan át szeretne helyezni.

- Az Azure hálózati biztonsági csoportok nem helyezhetők át a régiók között.  Az új NSG-t a célrégió erőforrásaihoz kell társítania.

- NSG-konfiguráció exportálásához és egy sablon központi telepítéséhez nsg-t hozhat létre egy másik régióban, szüksége van a hálózati közreműködőszerepkörvagy magasabb.

- Azonosítsa a forráshálózati elrendezést és az összes jelenleg használt erőforrást. Ez az elrendezés tartalmazza, de nem korlátozódik a terheléselosztók, nyilvános IP-k és a virtuális hálózatok.

- Ellenőrizze, hogy az Azure-előfizetés lehetővé teszi-e, hogy nsg-ket hozzon létre a használt célrégióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Győződjön meg arról, hogy az előfizetés elegendő erőforrással rendelkezik az NSG-k hozzáadásának támogatásához ehhez a folyamathoz.  Lásd: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Felkészülés és mozgás
A következő lépések bemutatják, hogyan készítheti elő a hálózati biztonsági csoportot a konfigurációs és biztonsági szabály áthelyezése egy Erőforrás-kezelő sablon használatával, és helyezze át az NSG konfigurációs és biztonsági szabályokat a célrégióba a portál használatával.


### <a name="export-the-template-and-deploy-from-the-portal"></a>A sablon exportálása és üzembe helyezése a portálról

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **erőforráscsoportokba.**
2. Keresse meg a forrásNSG-t tartalmazó erőforráscsoportot, és kattintson rá.
3. Válassza > **Beállítások** > **exportálása sablont.**
4. Válassza a **Telepítés lehetőséget** az **Exportálás sablon** panelen.
5. Kattintson **a TEMPLATE** > **Paraméterek szerkesztése parancsra** a **parameters.json** fájl online szerkesztőben való megnyitásához.
6. Az NSG-név paraméterének szerkesztéséhez módosítsa az **értéktulajdonságot** a **paraméterek**alatt:

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. Módosítsa a forrás NSG értékét a szerkesztőben egy választott névre a cél NSG számára. Győződjön meg róla, hogy a nevet idézőjelek közé kell tetsznie.

8.  Kattintson a **Mentés** a szerkesztőben gombra.

9.  Kattintson **a TEMPLATE** > **Szerkesztés sablon** ra a **template.json** fájl online szerkesztőben való megnyitásához.

10. Az NSG konfigurációs és biztonsági szabályainak áthelyezését célzó célrégió szerkesztéséhez módosítsa a **helytulajdonságot** az online szerkesztő **erőforrásai** alatt:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
             }
            }
           ]

    ```

11. A régióhely-kódok beszerzéséhez olvassa el az Azure Locations ( Azure Locations ) ( Azure Locations ) ( Azure Locations ) ( Azure Locations ) [(A régió hely](https://azure.microsoft.com/global-infrastructure/locations/)  A régió kódja a terület neve szóközök nélkül, **az USA** = középső**központja**.

12. Ha úgy dönt, módosíthatja a sablon egyéb paramétereit is, és a követelményektől függően nem kötelező:

    * **Biztonsági szabályok** – A **template.json** fájl **securityRules** szakaszához adott vagy eltávolítható szabályokat szerkesztheti:

        ```json
           "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "TCP",
                            "sourcePortRange": "*",
                            "destinationPortRange": "3389",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 300,
                            "direction": "Inbound",
                            "sourcePortRanges": [],
                            "destinationPortRanges": [],
                            "sourceAddressPrefixes": [],
                            "destinationAddressPrefixes": []
                             }
                    },
                ]
            }
        ```

      A célNSG-ben lévő szabályok hozzáadásának vagy eltávolításának befejezéséhez a **template.json** fájl végén található egyéni szabálytípusokat is az alábbi példa formátumában kell szerkesztenie:

      ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
      ```

13. Kattintson a **Mentés** gombra az online szerkesztőben.

14. Kattintson **a BASICS-előfizetés** > **Subscription** elemre annak az előfizetésnek a kiválasztásához, amelyben a cél NSG telepítve lesz.

15. Kattintson **a BASICS** > **erőforráscsoport** válassza ki az erőforráscsoportot, ahol a cél NSG lesz telepítve.  Az **Új létrehozása gombra** kattintva új erőforráscsoportot hozhat létre a cél NSG számára.  Győződjön meg arról, hogy a név nem ugyanaz, mint a meglévő NSG forráserőforrás-csoportja.

16. Ellenőrizze, hogy a **BASICS** > **hely** be van-e állítva arra a célhelyre, ahol az NSG telepítését szeretné.

17. Ellenőrizze a **BEÁLLÍTÁSOK** csoportban, hogy a név megegyezik-e a fenti paraméterszerkesztőben megadott névvel.

18. Jelölje be a jelölőnégyzetet a **FELTÉTELEK ÉS FELTÉTELEK mezőben.**

19. A **célhálózati** biztonsági csoport telepítéséhez kattintson a Vásárlás gombra.

## <a name="discard"></a>Elvetés

Ha el szeretné vetni a cél NSG-t, törölje a cél NSG-t tartalmazó erőforráscsoportot.  Ehhez jelölje ki az erőforráscsoportot az irányítópulton a portálon, és válassza a **Törlés** lehetőséget az áttekintő lap tetején.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások véglegesítéséhez és az NSG áthelyezésének befejezéséhez törölje a forrás NSG-t vagy az erőforráscsoportot. Ehhez jelölje ki a hálózati biztonsági csoportot vagy erőforráscsoportot az irányítópultról a portálon, és válassza a **Törlés** lehetőséget az egyes lapok tetején.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban áthelyezett egy Azure-hálózati biztonsági csoportot egyik régióból a másikba, és megtisztította a forráserőforrásokat.  Ha többet szeretne tudni az erőforrások régiók közötti áthelyezéséről és az Azure-beli vészhelyreállításról:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
