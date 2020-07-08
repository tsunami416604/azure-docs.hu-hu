---
title: Azure IoT DPS IP-kapcsolatok szűrői | Microsoft Docs
description: Az IP-szűrés használatával blokkolhatja az adott IP-címek kapcsolatait az Azure IoT DPS-példánnyal. Letilthatja az egyes vagy IP-címtartományok kapcsolatait.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79284914"
---
# <a name="use-ip-filters"></a>IP-szűrők használata

A biztonság minden IoT-megoldás fontos aspektusa. Időnként explicit módon meg kell adnia azokat az IP-címeket, amelyekről az eszközök a biztonsági konfiguráció részeként csatlakozni tudnak. Az Azure IoT Hub Device Provisioning Service (DPS) *IP-szűrési* funkciója lehetővé teszi szabályok konfigurálását az egyes IPv4-címekről érkező forgalom elutasításához vagy fogadásához.

## <a name="when-to-use"></a>A következő esetekben használja

Két konkrét használati eset van, ahol hasznos blokkolni egy DPS-végpont kapcsolatait bizonyos IP-címekről:

* A DPS csak a megadott IP-címtartományból érkező forgalmat fogadja, és minden mást visszautasít. Tegyük fel például, hogy a DPS-t az [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) használatával hozza létre magánhálózati kapcsolatot a DPS és az eszközei között.

* El kell utasítania a forgalmat olyan IP-címekről, amelyeket a DPS rendszergazdája gyanúsnak talált.

## <a name="how-filter-rules-are-applied"></a>A szűrési szabályok alkalmazása

Az IP-szűrési szabályok a DPS-példány szintjén lesznek alkalmazva. Ezért az IP-szűrési szabályok az eszközök és a háttérbeli alkalmazások összes kapcsolatára érvényesek bármely támogatott protokoll használatával.

A DPS-példány elutasító IP-szabályának megfelelő IP-címről érkező kapcsolódási kísérletek jogosulatlan 401-as állapotkódot és leírást kapnak. A válaszüzenet nem említi az IP-szabályt.

## <a name="default-setting"></a>Alapértelmezett beállítás

Alapértelmezés szerint a DPS-portálon található **IP-szűrő** rács üres. Ez az alapértelmezett beállítás azt jelenti, hogy a DPS bármely IP-címről fogad kapcsolatokat. Ez az alapértelmezett beállítás egyenértékű egy olyan szabállyal, amely elfogadja a 0.0.0.0/0 IP-címtartományt.

![IoT DPS alapértelmezett IP-szűrési beállítások](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>IP-szűrési szabály hozzáadása vagy szerkesztése

IP-szűrési szabály hozzáadásához válassza az **+ IP-szűrési szabály hozzáadása**lehetőséget.

![IP-szűrési szabály hozzáadása IoT DPS-hez](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Az **IP-szűrési szabály hozzáadása**lehetőség kiválasztását követően töltse ki a mezőket.

![Az IP-szűrési szabály hozzáadása lehetőség kiválasztását követően](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Adja meg az IP-szűrési szabály **nevét** . Ennek egyedi, kis-és nagybetűket nem megkülönböztető, alfanumerikus sztringnek kell lennie legfeljebb 128 karakter hosszú lehet. Csak az ASCII 7 bites alfanumerikus karaktereket és a `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` rendszer fogadja el.

* Adjon meg egyetlen IPv4-címet vagy IP-címet a CIDR-jelölésben. Például a CIDR 192.168.100.0/22 jelölése 1024 a 192.168.100.0 és a 192.168.103.255 közötti IPv4-címeket jelöli.

* Válassza az **Engedélyezés** vagy a **Letiltás** lehetőséget az IP-szűrési szabály **művelete** elemnél.

A mezők kitöltése után kattintson a **Mentés** gombra a szabály mentéséhez. Megjelenik egy riasztás, amely értesíti, hogy a frissítés folyamatban van.

![Értesítés IP-szűrési szabály mentéséről](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

A **Hozzáadás** lehetőség le van tiltva, amikor eléri a legfeljebb 10 IP-szűrési szabályt.

Meglévő szabály szerkesztéséhez válassza ki a módosítani kívánt adatait, végezze el a módosítást, majd válassza a **Mentés** lehetőséget a Szerkesztés mentéséhez.

> [!NOTE]
> Az IP-címek elutasításával megakadályozható, hogy más Azure-szolgáltatások a DPS-példánnyal kommunikálva legyenek.

## <a name="delete-an-ip-filter-rule"></a>IP-szűrési szabály törlése

Ha törölni szeretne egy IP-szűrési szabályt, válassza a Kuka ikont az adott sorban, majd válassza a **Mentés**lehetőséget. A szabály el lett távolítva, és a módosítás mentve lesz.

![IoT DPS IP-szűrési szabály törlése](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>IP-szűrési szabályok frissítése a kódban

A DPS IP-szűrőt az Azure erőforrás-szolgáltató REST-végpontjának használatával kérheti le és módosíthatja. Lásd: `properties.ipFilterRules` a [createorupdate metódusban](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate).

A DPS IP-szűrési szabályok frissítése jelenleg nem támogatott az Azure CLI-ben vagy Azure PowerShell, de Azure Resource Manager-sablonokkal is elvégezhető. A Resource Manager-sablonok használatával kapcsolatos útmutatásért lásd: [Azure Resource Manager sablonok](../azure-resource-manager/templates/overview.md) . Az alábbi példák azt mutatják be, hogyan hozhatja létre, szerkesztheti és törölheti a DPS IP-szűrési szabályait.

### <a name="add-an-ip-filter-rule"></a>IP-szűrési szabály hozzáadása

A következő sablon egy "AllowAll" nevű új IP-szűrési szabályt hoz létre, amely fogadja az összes forgalmat.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

Frissítse a sablon IP-szűrési szabályának attribútumait a követelmények alapján.

| Attribútum                | Leírás |
| ------------------------ | ----------- |
| **FilterName**           | Adja meg az IP-szűrési szabály nevét. Ennek egyedi, kis-és nagybetűket nem megkülönböztető, alfanumerikus sztringnek kell lennie legfeljebb 128 karakter hosszú lehet. Csak az ASCII 7 bites alfanumerikus karakterek ({"-", ":", "/", " \' .", "+", "%", "_", "#", "*", "?"), "!", a (z) "(", ")", ",", "=", "@", ";", "}" és "}" is elfogadva. |
| **Művelet**               | Az elfogadott értékek **elfogadják**   vagy **elutasítja**   az IP-szűrési szabály műveletét. |
| **ipMask**               | Adjon meg egyetlen IPv4-címet vagy IP-címet a CIDR-jelölésben. Például a CIDR 192.168.100.0/22 jelölése 1024 a 192.168.100.0 és a 192.168.103.255 közötti IPv4-címeket jelöli. |


### <a name="update-an-ip-filter-rule"></a>IP-szűrési szabály frissítése

A következő sablon például a "AllowAll" nevű IP-szűrési szabályt frissíti, amely az összes forgalom elutasítása érdekében látható.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>IP-szűrési szabály törlése

A következő sablon például törli a DPS-példány összes IP-szűrési szabályát.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>IP-szűrési szabály kiértékelése

Az IP-szűrési szabályok a sorrendben lesznek alkalmazva, és az IP-címnek megfelelő első szabály határozza meg az elfogadás vagy az elutasítás műveletet.

Ha például a 192.168.100.0/22-es tartományba szeretne címeket fogadni, és minden mást visszautasít, a rács első szabályának el kell fogadnia a 192.168.100.0/22 címtartományt. A következő szabálynak el kell utasítania az összes címet a 0.0.0.0/0 tartomány használatával.

Az IP-szűrési szabályok sorrendjét megváltoztathatja a rácson úgy, hogy a sorok elején lévő három függőleges pontra kattint, és a drag and drop parancsot használja.

Az új IP-szűrési szabály sorrendjének mentéséhez kattintson a **Mentés**gombra.

![A DPS IP-szűrési szabályok sorrendjének módosítása](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>További lépések

A következő témakörben talál további információt:

* [A IoT DPS IP-címeinek ismertetése](iot-dps-understand-ip-address.md)
* [A DPS konfigurálása az Azure CLI használatával](how-to-manage-dps-with-cli.md)
* [A DPS elérésének vezérlése](how-to-control-access.md)
