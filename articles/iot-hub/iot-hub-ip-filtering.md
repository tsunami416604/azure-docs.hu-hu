---
title: Azure IoT Hub IP-kapcsolatszűrői | Microsoft dokumentumok
description: IP-szűrés használatával blokkolja a kapcsolatokat adott IP-címek az Azure IoT hub. Letilthatja az IP-címek egyéni vagy tartományaiból származó kapcsolatokat.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/22/2017
ms.author: robinsh
ms.openlocfilehash: a6bd8a766f3205358a65ef2fd0816643e4261cab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68414284"
---
# <a name="use-ip-filters"></a>IP-szűrők használata

A biztonság az Azure IoT Hubon alapuló bármely IoT-megoldás fontos szempontja. Néha explicit módon meg kell adnia azokat az IP-címeket, amelyekről az eszközök a biztonsági konfiguráció részeként csatlakozhatnak. Az *IP-szűrő* szolgáltatás lehetővé teszi, hogy szabályokat állítson be az adott IPv4-címekről érkező forgalom elutasítására vagy elfogadására.

## <a name="when-to-use"></a>A következő esetekben használja

Két konkrét használati eset van, amikor bizonyos IP-címek esetében hasznos az IoT Hub végpontjainak blokkolása:

* Az IoT hub csak egy adott IP-címtartományból fogadhat forgalmat, és minden mást elutasíthat. Például az IoT-központot az [Azure Express Route-szal](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) használja az IoT-központ és a helyszíni infrastruktúra közötti privát kapcsolatok létrehozásához.

* Az IoT-központ rendszergazdája által gyanúsnak minősített IP-címekről érkező forgalmat el kell utasítania.

## <a name="how-filter-rules-are-applied"></a>A szűrőszabályok alkalmazásának szabályai

Az IP-szűrőszabályok az IoT Hub szolgáltatási szintjén kerülnek alkalmazásra. Ezért az IP-szűrőszabályok az eszközökről és a támogatott protokollt használó háttéralkalmazásokból származó összes kapcsolatra vonatkoznak.

Az IoT hubban egy elutasító IP-szabálynak megfelelő IP-címről tett csatlakozási kísérlet jogosulatlan 401-es állapotkódot és -leírást kap. A válaszüzenet nem említi az IP-szabályt.

## <a name="default-setting"></a>Alapértelmezett beállítás

Alapértelmezés szerint az **IP-szűrő** rács a portálon egy IoT hub üres. Ez az alapértelmezett beállítás azt jelenti, hogy a hub bármely IP-címről fogadja a kapcsolatokat. Ez az alapértelmezett beállítás megegyezik a 0.0.0.0/0 IP-címtartományt elfogadó szabállyal.

![Az IoT Hub alapértelmezett IP-szűrőbeállításai](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>IP-szűrőszabály hozzáadása vagy szerkesztése

IP-szűrőszabály hozzáadásához válassza a **+ IP-szűrőszabály hozzáadása**lehetőséget .

![IP-szűrőszabály hozzáadása IoT-központhoz](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

Miután az **IP-szűrőszabály hozzáadása**lehetőséget választotta, töltse ki a mezőket.

![Ip-szűrőszabály hozzáadása lehetőség kiválasztása után](./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png)

* Adja meg az IP-szűrő szabály **nevét.** Ennek egyedi, kis- és nagybetűket nem megkülönböztető, legfeljebb 128 karakter hosszú alfanumerikus karakterláncnak kell lennie. Csak az ASCII 7 bites alfanumerikus karakterek plusz `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` fogadnak el.

* Adjon meg egyetlen IPv4-címet vagy IP-címblokkot CIDR jelöléssel. A CIDR 192.168.100.0/22 jelölésében például a 1024 IPv4-cím 192.168.100.0-tól 192.168.103.255-ig.

* Az IP-szűrőszabály műveleteként válassza az **Engedélyezés** vagy a **Letiltás** **lehetőséget.**

A mezők kitöltése után válassza a **Mentés** gombot a szabály mentéséhez. Megjelenik egy figyelmeztetés, amely arról tájékoztatja, hogy a frissítés folyamatban van.

![Értesítés az IP-szűrőszabály mentéséről](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

A **Hozzáadás** beállítás le van tiltva, ha eléri a legfeljebb 10 IP-szűrőszabályt.

Meglévő szabály szerkesztéséhez jelölje ki a módosítani kívánt adatokat, módosítsa, majd a **Szerkesztés gombra** a szerkesztés mentéséhez.

> [!NOTE]
> Az IP-címek elutasítása megakadályozhatja, hogy más Azure-szolgáltatások (például az Azure Stream Analytics, az Azure virtuális gépek vagy a portálon lévő Eszközkezelő) kommunikáljon az IoT-központtal.

> [!WARNING]
> Ha az Azure Stream Analytics (ASA) használatával üzeneteket olvas egy IoT-központból, ahol engedélyezve van az IP-szűrés, használja az IoT Hub-kompatibilis nevét és végpontját az ASA-kapcsolati karakterláncban.

## <a name="delete-an-ip-filter-rule"></a>IP-szűrőszabály törlése

IP-szűrőszabály törléséhez jelölje ki a kuka ikont a sorban, majd kattintson a **Mentés gombra.** A rendszer eltávolítja a szabályt, és menti a módosítást.

![IoT Hub IP-szűrőszabályának törlése](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>IP-szűrők lekérése és frissítése az Azure CLI használatával

Az IoT Hub IP-szűrői az [Azure CLI-n](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)keresztül lehívhatók és frissíthetők.

Az IoT Hub aktuális IP-szűrőinek lekéréséhez futtassa a következőket:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Ez egy JSON-objektumot ad vissza, ahol `properties.ipFilterRules` a meglévő IP-szűrők a kulcs alatt vannak felsorolva:

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

Új IP-szűrő hozzáadása az IoT Hubhoz:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Meglévő IP-szűrő eltávolítása az IoT Hubon, futtassa a következőket:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Vegye `<ipFilterIndexToRemove>` figyelembe, hogy meg kell felelnie az IP-szűrők rendezését az IoT Hub.Note that must correspond to the ordering of IP filters in your IoT Hub's. `properties.ipFilterRules`

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>IP-szűrők lekérése és frissítése az Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az IoT Hub IP-szűrői lehívhatók és beállíthatók az [Azure PowerShellen](/powershell/azure/overview)keresztül.

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>IP-szűrőszabályok frissítése rest használatával

Az IoT Hub IP-szűrőjét is lekérheti és módosíthatja az Azure resource Provider REST-végponthasználatával. Lásd `properties.ipFilterRules` a [createorupdate metódusban.](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate)

## <a name="ip-filter-rule-evaluation"></a>IP-szűrőszabály kiértékelése

Az IP-szűrőszabályok sorrendben kerülnek alkalmazásra, és az IP-címnek megfelelő első szabály határozza meg az elfogadási vagy elutasítási műveletet.

Ha például a 192.168.100.0/22 tartományban lévő címeket szeretné elfogadni, és minden mást el szeretne utasítani, a rács első szabályának a 192.168.100.0/22 címtartományt kell elfogadnia. A következő szabálynak a 0.0.0.0/0 tartomány használatával el kell utasítania az összes címet.

Az IP-szűrőszabályok sorrendjét úgy módosíthatja a rácsban, hogy a sor elején a három függőleges elemre kattint, és húzással lehúzhatja a készüléket.

Az új IP-szűrőszabály-sorrend mentéséhez kattintson a **Mentés gombra.**

![Az IoT Hub IP-szűrőszabályainak sorrendjének módosítása](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>További lépések

Az IoT Hub képességeinek további megismeréséhez lásd:

* [Műveletek figyelése](iot-hub-operations-monitoring.md)
* [IoT Hub-metrikák](iot-hub-metrics.md)
