---
title: Azure IoT DPS IP-kapcsolatszűrők | Microsoft dokumentumok
description: IP-szűrés használatával blokkolja a kapcsolatokat az adott IP-címek az Azure IoT DPS-példány. Letilthatja az IP-címek egyéni vagy tartományaiból származó kapcsolatokat.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284914"
---
# <a name="use-ip-filters"></a>IP-szűrők használata

A biztonság minden IoT-megoldás fontos szempontja. Néha explicit módon meg kell adnia azokat az IP-címeket, amelyekről az eszközök a biztonsági konfiguráció részeként csatlakozhatnak. Az Azure IoT Hub-eszközlétesítési szolgáltatás (DPS) *IP-szűrő* szolgáltatása lehetővé teszi, hogy szabályokat konfiguráljon az adott IPv4-címekről érkező forgalom elutasítására vagy fogadására.

## <a name="when-to-use"></a>A következő esetekben használja

Két konkrét felhasználási eset létezik, ahol hasznos blokkolni a DPS-végponthoz való kapcsolatokat bizonyos IP-címekről:

* A DPS csak egy megadott IP-címtartományból fogadhat forgalmat, és minden mást elutasíthat. A DPS-t például az [Azure Express Route-szal](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) használja a DPS és az eszközök közötti privát kapcsolatok létrehozásához.

* El kell utasítania a DPS-rendszergazda által gyanúsnak minősített IP-címekről érkező forgalmat.

## <a name="how-filter-rules-are-applied"></a>A szűrőszabályok alkalmazásának szabályai

Az IP-szűrőszabályok a DPS-példány szintjén kerülnek alkalmazásra. Ezért az IP-szűrőszabályok az eszközökről és a támogatott protokollt használó háttéralkalmazásokból származó összes kapcsolatra vonatkoznak.

A DPS-példányban egy elutasító IP-szabálynak megfelelő IP-címről érkező csatlakozási kísérlet jogosulatlan 401-es állapotkódot és -leírást kap. A válaszüzenet nem említi az IP-szabályt.

## <a name="default-setting"></a>Alapértelmezett beállítás

Alapértelmezés szerint a DPS **portálip-szűrőrácsa** üres. Ez az alapértelmezett beállítás azt jelenti, hogy a DPS bármely IP-címről fogadja a kapcsolatokat. Ez az alapértelmezett beállítás megegyezik a 0.0.0.0/0 IP-címtartományt elfogadó szabállyal.

![IoT DPS alapértelmezett IP-szűrőbeállításai](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>IP-szűrőszabály hozzáadása vagy szerkesztése

IP-szűrőszabály hozzáadásához válassza a **+ IP-szűrőszabály hozzáadása**lehetőséget .

![IP-szűrőszabály hozzáadása IoT DPS-hez](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Miután az **IP-szűrőszabály hozzáadása**lehetőséget választotta, töltse ki a mezőket.

![Ip-szűrőszabály hozzáadása lehetőség kiválasztása után](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Adja meg az IP-szűrő szabály **nevét.** Ennek egyedi, kis- és nagybetűket nem megkülönböztető, legfeljebb 128 karakter hosszú alfanumerikus karakterláncnak kell lennie. Csak az ASCII 7 bites alfanumerikus karakterek plusz `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` fogadnak el.

* Adjon meg egyetlen IPv4-címet vagy IP-címblokkot CIDR jelöléssel. A CIDR 192.168.100.0/22 jelölésében például a 1024 IPv4-cím 192.168.100.0-tól 192.168.103.255-ig.

* Az IP-szűrőszabály műveleteként válassza az **Engedélyezés** vagy a **Letiltás** **lehetőséget.**

A mezők kitöltése után válassza a **Mentés** gombot a szabály mentéséhez. Megjelenik egy figyelmeztetés, amely arról tájékoztatja, hogy a frissítés folyamatban van.

![Értesítés az IP-szűrőszabály mentéséről](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

A **Hozzáadás** beállítás le van tiltva, ha eléri a legfeljebb 10 IP-szűrőszabályt.

Meglévő szabály szerkesztéséhez jelölje ki a módosítani kívánt adatokat, módosítsa, majd a **Szerkesztés gombra** a szerkesztés mentéséhez.

> [!NOTE]
> Az IP-címek elutasítása megakadályozhatja, hogy más Azure-szolgáltatások kölcsönhatásba lépjenek a DPS-példánnyal.

## <a name="delete-an-ip-filter-rule"></a>IP-szűrőszabály törlése

IP-szűrőszabály törléséhez jelölje ki a kuka ikont a sorban, majd kattintson a **Mentés gombra.** A rendszer eltávolítja a szabályt, és menti a módosítást.

![IoT DPS IP-szűrőszabály törlése](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>IP-szűrőszabályok frissítése a kódban

A DPS IP-szűrő t az Azure resource Provider REST-végponthasználatával kérheti le és módosíthatja. Lásd `properties.ipFilterRules` a [createorupdate metódusban.](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate)

A DPS IP-szűrőszabályainak frissítése jelenleg nem támogatott az Azure CLI vagy az Azure PowerShell számára, de az Azure Resource Manager-sablonokkal megvalósítható. Lásd: [Az Azure Resource Manager-sablonok](../azure-resource-manager/templates/overview.md) útmutatást a Resource Manager-sablonok használatával kapcsolatos útmutatást. Az alábbi sablonpéldák bemutatják a DPS IP-szűrőszabályok létrehozását, szerkesztését és törlését.

### <a name="add-an-ip-filter-rule"></a>IP-szűrőszabály hozzáadása

A következő sablonpélda létrehoz egy új IP-szűrőszabályt "AllowAll" néven, amely elfogadja az összes forgalmat.

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

Frissítse a sablon IP-szűrőszabály-attribútumait a követelmények alapján.

| Attribútum                | Leírás |
| ------------------------ | ----------- |
| **Szűrőnév**           | Adja meg az IP-szűrő szabály nevét. Ennek egyedi, kis- és nagybetűket nem megkülönböztető, legfeljebb 128 karakter hosszú alfanumerikus karakterláncnak kell lennie. Csak az ASCII 7 bites alfanumerikus karaktereket és {'-,",\'":",'/', '.',',',',','+','%,','''''''''''''''''''''''''''''''",'""""",""","@",';',',',',',',','',''',''}, ''}} elfogadják. |
| **Művelet**               | Az elfogadott értékek az **Elfogadás** vagy az **Elutasítás** az IP-szűrőszabály műveleteként. |
| **ipMaszk**               | Adjon meg egyetlen IPv4-címet vagy IP-címblokkot CIDR jelöléssel. A CIDR 192.168.100.0/22 jelölésében például a 1024 IPv4-cím 192.168.100.0-tól 192.168.103.255-ig. |


### <a name="update-an-ip-filter-rule"></a>IP-szűrőszabály frissítése

A következő sablon példa frissíti a korábban bemutatott "AllowAll" NEVŰ IP-szűrőszabályt, hogy az összes forgalmat elutasítsa.

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

### <a name="delete-an-ip-filter-rule"></a>IP-szűrőszabály törlése

A következő sablonpélda törli a DPS-példány összes IP-szűrőszabályát.

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



## <a name="ip-filter-rule-evaluation"></a>IP-szűrőszabály kiértékelése

Az IP-szűrőszabályok sorrendben kerülnek alkalmazásra, és az IP-címnek megfelelő első szabály határozza meg az elfogadási vagy elutasítási műveletet.

Ha például a 192.168.100.0/22 tartományban lévő címeket szeretné elfogadni, és minden mást el szeretne utasítani, a rács első szabályának a 192.168.100.0/22 címtartományt kell elfogadnia. A következő szabálynak a 0.0.0.0/0 tartomány használatával el kell utasítania az összes címet.

Az IP-szűrőszabályok sorrendjét úgy módosíthatja a rácsban, hogy a sor elején a három függőleges elemre kattint, és húzással lehúzhatja a készüléket.

Az új IP-szűrőszabály-sorrend mentéséhez kattintson a **Mentés gombra.**

![A DPS IP-szűrőszabályok sorrendjének módosítása](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>További lépések

Az irányító DPS további megismeréséhez lásd:

* [Az IoT DPS IP-címeinek ismertetése](iot-dps-understand-ip-address.md)
* [A DPS konfigurálása az Azure CLI használatával](how-to-manage-dps-with-cli.md)
* [A DPS-hez való hozzáférés szabályozása](how-to-control-access.md)
