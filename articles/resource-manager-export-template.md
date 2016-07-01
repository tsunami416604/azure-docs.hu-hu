<properties
    pageTitle="Azure Resource Manager-sablonok exportálása | Microsoft Azure"
    description="Az Azure Resource Manager használatával sablonokat exportálhat létező erőforráscsoportokból."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/10/2016"
    ms.author="tomfitz"/>

# Azure Resource Manager-sablonok exportálása létező erőforrásokból

Az Azure Resource Manager-sablonok készítésének megértése ijesztőnek tűnhet. Szerencsére a Resource Manager megkönnyíti ezt a feladatot, mivel az előfizetésében szereplő, már létező erőforrásokból is exportálhat sablonokat. Az így létrehozott sablon használatával megismerheti a sablonok szintaxisát, illetve igény szerint automatizálhatja a megoldás újbóli telepítését.

A jelen oktatóanyag keretében bejelentkezik majd az Azure portálra, létrehoz egy tárfiókot, majd exportálja annak sablonját. Emellett hozzáad majd egy virtuális hálózatot az erőforráscsoport módosításához. Végül pedig exportálja majd az aktuális állapotot jelölő új sablont. Habár a jelen cikk egy egyszerűsített infrastruktúrára összpontosít, ugyanezen lépések használatával egy bonyolultabb megoldás sablonját is exportálhatja.

## Tárfiók létrehozása

1. Az [Azure portálon](https://portal.azure.com) válassza az **Új** > **Adatok és Tárolás** > **Tárfiók** lehetőséget.

      ![tároló létrehozása](./media/resource-manager-export-template/create-storage.png)

2. Hozzon létre egy tárfiókot **storage** névvel, saját monogramjával, valamint az aktuális dátummal. A tárfiók nevének egyedinek kell lennie az egész Azure rendszerben. Ha először egy már használatban lévő nevet ad meg, próbálkozzon meg annak egy variációjával. Erőforráscsoportnak adja meg az **ExportGroup** csoportot. A többi tulajdonság esetén használhatja az alapértelmezett értékeket. Kattintson a **Létrehozás** gombra.

      ![a tároló értékeinek megadása](./media/resource-manager-export-template/provide-storage-values.png)

Miután a telepítés végzett, az előfizetése tartalmazza majd a tárfiókot.

## Telepítés sablonjának exportálása

1. Nyissa meg az új erőforráscsoport paneljét. Megfigyelheti, hogy a panel felsorolja a legutóbbi telepítés eredményét. Kattintson erre a hivatkozásra.

      ![erőforráscsoport panel](./media/resource-manager-export-template/resource-group-blade.png)

2. Megjelennek a csoport telepítéseinek előzményei. Az Ön esetében valószínűleg csak egyetlen telepítés látható itt. Válassza ki ezt a telepítést.

     ![legutóbbi telepítés](./media/resource-manager-export-template/last-deployment.png)

3. Megjelenik a telepítés összegzése. Az összegzés tartalmazza a telepítés, valamint annak műveleteinek állapotát, és a paraméterek számára megadott értékeket. A telepítéshez használt sablon megtekintéséhez válassza a **Sablon megtekintése** lehetőséget.

     ![telepítés összegzésének megtekintése](./media/resource-manager-export-template/deployment-summary.png)

4. A Resource Manager az alábbi öt fájlt kéri le:

   - A sablon infrastruktúráját meghatározó sablont. A tárfiók a portálon keresztül történő létrehozásakor a Resource Manager egy sablon használatával telepítette azt, és elmentette ezt a sablont későbbi felhasználás céljából.

   - Az értékek a telepítés során történő megadásához szükséges paraméterfájlt. Ez tartalmazza az első telepítés során megadott értékeket, de ezek bármelyike módosítható a sablon újbóli telepítése során.

   - A sablon telepítéséhez használható Azure PowerShell-parancsfájlt.

   - A sablon telepítéséhez használható Azure CLI-parancsfájlt.

   - A sablon telepítéséhez használható .NET-osztályt.

     A fájlok a panelen található hivatkozásokon keresztül érhetők el. Alapértelmezés szerint a sablon ki van választva.

       ![sablon megtekintése](./media/resource-manager-export-template/view-template.png)

     Különösen figyeljen oda a sablonra. A sablonnak az alábbihoz hasonlónak kell lennie:

        {      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",      "contentVersion": "1.0.0.0",      "parameters": {        "name": {          "type": "String"        },        "accountType": {          "type": "String"        },        "location": {          "type": "String"        },        "encryptionEnabled": {          "defaultValue": false,          "type": "Bool"        }      },      "resources": [        {          "type": "Microsoft.Storage/storageAccounts",          "sku": {            "name": "[parameters('accountType')]"          },          "kind": "Storage",          "name": "[parameters('name')]",          "apiVersion": "2016-01-01",          "location": "[parameters('location')]",          "properties": {            "encryption": {              "services": {                "blob": {                  "enabled": "[parameters('encryptionEnabled')]"                }              },              "keySource": "Microsoft.Storage"            }          }        }      ]    }

     Figyelje meg, hogy a sablon meghatározza a tárfiók nevét, típusát és helyét. Egy paraméter azt is megadja, hogy engedélyezve van-e a titkosítás. Ennek alapértelmezett értéke: **hamis**. A **resources** (erőforrás) szakaszban láthatja a telepíteni kívánt tárfiók definícióját.

A szögletes zárójelekben olyan kifejezések szerepelnek, amelyek kiértékelése a telepítés során történik meg. A rendszer a sablonban található zárójeles kifejezések segítségével kéri le a paraméterértékeket a telepítés során. Ennél jóval több kifejezést használhat, a jelen cikk későbbi részeiben pedig további kifejezésekre is láthat példát. A kifejezések teljes listáját megtalálhatja az alábbi cikkben: [Az Azure Resource Manager sablonfüggvényei](resource-group-template-functions.md).

A sablonok struktúrájával kapcsolatos további információk: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).

## Virtuális hálózat hozzáadása

Az előző szakaszban letöltött sablon az eredeti telepítés infrastruktúráját tükrözte, de a telepítés után végzett módosításokat nem veszi figyelembe.
A probléma bemutatása érdekében módosítsa az erőforráscsoportot, és adjon hozzá egy virtuális hálózatot a portálon keresztül.

1. Az erőforráscsoport panelen válassza a **Hozzáadás** lehetőséget, majd az elérhető erőforrások közül válassza ki a **virtuális hálózat** lehetőséget.

2. A virtuális hálózatnak adja meg a **VNET** nevet, majd a többi tulajdonság esetén használja az alapértelmezett értékeket. Kattintson a **Létrehozás** gombra.

      ![riasztás beállítása](./media/resource-manager-export-template/create-vnet.png)

3. Miután sikeresen telepítette a virtuális hálózatot az erőforráscsoportba, tekintse meg ismét a telepítési előzményeket. Most már két telepítést láthat itt. Válassza ki az újabb telepítést.

      ![telepítési előzmények](./media/resource-manager-export-template/deployment-history.png)

4. Tekintse meg a telepítés sablonját. Vegye figyelembe, hogy ez a sablon csak a virtuális hálózaton végzett módosításokat határozza meg.

Általában célszerű egyetlen sablonnal dolgozni, amely a megoldás teljes infrastruktúráját telepíti egyetlen műveletben, így nem kell több különböző, telepítendő sablont megjegyezni.


## Erőforráscsoport sablonjának exportálása

Habár minden telepítés csak az erőforráscsoporton végzett módosításokat jeleníti meg, bármikor exportálhat egy olyan sablont, amely megmutatja a teljes erőforráscsoport attribútumait.  

1. Az egyes erőforráscsoportok sablonjának megtekintéséhez válassza a **Sablon exportálása** lehetőséget.

      ![erőforráscsoportok exportálása](./media/resource-manager-export-template/export-resource-group.png)

2. Ismét megjelenik a megoldás újbóli telepítéséhez használható öt fájl, de ezúttal a sablon kicsit más lesz. Ez a sablon csak két paraméterrel rendelkezik: a tárfiók nevével, valamint a virtuális hálózat nevével.

        "parameters": {
          "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
          },
          "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
          }
        },

     A Resource Manager nem kérte le a telepítés során használt sablonokat. Ehelyett létrehozott egy új sablont az erőforrások aktuális konfigurációja alapján. A Resource Manager nem ismeri a paraméterekként megadni kívánt értékeket, így a legtöbb esetben nem módosítható értékeket hoz létre az erőforráscsoport értékei alapján. A tárfiók hely és replikáció paraméterének értéke például a következő:

        "location": "northeurope",
        "tags": {},
        "properties": {
            "accountType": "Standard_RAGRS"
        },

3. A sablon letöltésével helyben is dolgozhat rajta.

      ![sablon letöltése](./media/resource-manager-export-template/download-template.png)

4. Keresse meg a letöltött .zip fájlt, és csomagolja ki annak tartalmát. A letöltött sablon segítségével újból telepítheti az infrastruktúrát.

## További lépések

Gratulálunk! Megtanulta, hogyan exportálhat sablonokat a portálon létrehozott erőforrásokból.

- A jelen oktatóanyag második felében testre szabhatja majd az imént letöltött sablont további paraméterek hozzáadásával, valamint telepítheti majd azt egy parancsfájl segítségével. Lásd: [Exportált sablonok testreszabása és újbóli telepítése](resource-manager-customize-template.md).
- A sablonok PowerShellen keresztül történő exportálásával kapcsolatos információk: [Az Azure PowerShell használata Azure Resource Managerrel](powershell-azure-resource-manager.md).
- A sablonok az Azure parancssori felületen keresztül történő exportálásával kapcsolatos információk: [A Mac, Linux és Windows eszközökhöz készült Azure CLI használata az Azure Resource Manager eszközzel](xplat-cli-azure-resource-manager.md).



<!--HONumber=Jun16_HO2--->


