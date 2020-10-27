---
title: Azure IoT Hub IP-kapcsolatok szűrőinek | Microsoft Docs
description: Az IP-szűrés használata az adott IP-címek és az Azure IoT hub közötti kapcsolatok blokkolására. Blokkolhatja az egyéni IP-címekről vagy IP-címtartományokból érkező kapcsolatokat.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/25/2020
ms.author: robinsh
ms.openlocfilehash: 501b609d745e0a86bc1e00bccae54bb4f6e49376
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545276"
---
# <a name="use-ip-filters"></a>IP-szűrők használata

A biztonság az Azure IoT Hub-alapú IoT-megoldások fontos aspektusa. A biztonsági konfiguráció részeként olykor explicit módon meg kell adni, hogy mely IP-címekről kapcsolódhatnak az eszközök. Az *IP-szűrési* funkció lehetővé teszi, hogy szabályokat konfiguráljon a megadott IPv4-címekről érkező forgalom elutasítására vagy fogadására.

## <a name="when-to-use"></a>A következő esetekben használja

A IoT Hub végpontok bizonyos IP-címekhez való letiltásához két konkrét használati eset van:

* Az IoT hub csak a megadott IP-címtartományból érkező forgalmat fogadja, és minden mást visszautasít. Például az IoT hub és az [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) használatával privát kapcsolatokat hozhat létre az IoT hub és a helyszíni infrastruktúra között.

* El kell utasítania azokat az IP-címekről érkező forgalmat, amelyeket az IoT hub rendszergazdája gyanúsnak talált.

## <a name="how-filter-rules-are-applied"></a>A szűrési szabályok alkalmazásának módja

Az IP-szűrési szabályok a IoT Hub szolgáltatási szinten lesznek alkalmazva. Ezért az IP-szűrési szabályok az eszközök és a háttérbeli alkalmazások összes kapcsolatára érvényesek bármely támogatott protokoll használatával. Azonban az ügyfelek közvetlenül a [beépített Event hub-kompatibilis végpontról](iot-hub-devguide-messages-read-builtin.md) (nem a IoT hub kapcsolati karakterláncon keresztül) olvassák az IP-szűrési szabályokat. 

Minden olyan IP-címről érkező kapcsolódási kísérlet, amely megfelel az IoT hub elutasító IP-szabályának, a rendszer jogosulatlanul 401 állapotkódot és leírást kap. A válaszüzenet nem említi az IP-szabályt. Az IP-címek elutasítása megakadályozhatja, hogy más Azure-szolgáltatások, például az Azure Stream Analytics, az Azure Virtual Machines vagy a Azure Portal Device Explorer a IoT hub használatával.

> [!NOTE]
> Ha Azure Stream Analytics (ASA) protokollt kell használnia egy olyan IoT-hubhoz érkező üzenetek olvasásához, amelyeken engedélyezve van az IP-szűrő, akkor az IoT hub Event hub-kompatibilis nevét és végpontját használva manuálisan adhat hozzá [Event Hubs stream-bemenetet](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) az ASA-ban.

## <a name="default-setting"></a>Alapértelmezett beállítás

Alapértelmezés szerint a IoT hub-portálon található **IP-szűrő** rács üres. Ez az alapértelmezett beállítás azt jelenti, hogy a hub bármely IP-címről fogad kapcsolatokat. Ez az alapértelmezett beállítás egyenértékű egy olyan szabállyal, amely elfogadja a 0.0.0.0/0 IP-címtartományt.

Az IP-szűrési beállítások lapon válassza a **hálózatkezelés** , a **nyilvános hozzáférés** , majd a **kijelölt IP-címtartományok** elemet:

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="Alapértelmezett IP-szűrési beállítások IoT Hub":::

## <a name="add-or-edit-an-ip-filter-rule"></a>IP-szűrési szabály hozzáadása vagy szerkesztése

IP-szűrési szabály hozzáadásához válassza a **+ IP-szűrési szabály hozzáadása** lehetőséget.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="Alapértelmezett IP-szűrési beállítások IoT Hub":::

Az **IP-szűrési szabály hozzáadása** lehetőség kiválasztása után töltse ki a mezőket.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="Alapértelmezett IP-szűrési beállítások IoT Hub":::

* Adjon **nevet** az IP-szűrési szabálynak. A névnek egyedi alfanumerikus sztringnek kell lennie, amely nem különbözteti meg a kis- és nagybetűket, és legfeljebb 128 karakterből áll. Csak az ASCII 7 bites alfanumerikus karakterei és a `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` elfogadottak.

* Adjon meg egy egyedi IPv4-címet vagy egy IP-címblokkot CIDR-jelölésrendszer használatával. A CIDR-jelölés szerint például a 192.168.100.0/22 a 192.168.100.0 és a 192.168.103.255 közötti 1024 IPv4-címet jelöli.

* Állítsa be az **Engedélyezés** vagy a **Letiltás** lehetőséget a szűrési szabály **művelet** értékeként.

A mezők kitöltése után kattintson a **Mentés** gombra a szabály mentéséhez. Riasztás fog megjelenni, amely tájékoztatja, hogy a frissítés folyamatban van.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="Alapértelmezett IP-szűrési beállítások IoT Hub":::

Amikor eléri a maximális 10 IP-szűrési szabályt, a **Hozzáadás** lehetőség le lesz tiltva.

Meglévő szabály szerkesztéséhez jelölje ki a módosítani kívánt adatokat, végezze el a módosítást, majd válassza a **Mentés** lehetőséget a módosítás mentéséhez.

## <a name="delete-an-ip-filter-rule"></a>IP-szűrési szabály törlése

IP-szűrési szabály törléséhez válassza az adott sorban található kuka ikont, majd a **Mentés** lehetőséget. Ezzel törli a szabályt, és menti a módosítást.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="Alapértelmezett IP-szűrési beállítások IoT Hub":::

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>IP-szűrők beolvasása és frissítése az Azure CLI-vel

A IoT Hub IP-szűrőinek lekérhető és frissíthető az [Azure CLI](/cli/azure/)-n keresztül.

A IoT Hub aktuális IP-szűrőinek lekéréséhez futtassa a következőt:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Ez egy JSON-objektumot ad vissza, amelyben a meglévő IP-szűrők a kulcs alatt vannak felsorolva `properties.ipFilterRules` :

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

Ha új IP-szűrőt szeretne hozzáadni a IoT Hubhoz, futtassa a következőt:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

A IoT Hub meglévő IP-szűrőjének eltávolításához futtassa a következőt:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Vegye figyelembe, hogy az `<ipFilterIndexToRemove>` IP-szűrők sorrendjét meg kell egyeznie a IoT hub `properties.ipFilterRules` .

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>IP-szűrők beolvasása és frissítése Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A IoT Hub IP-szűrőinek lekérhető és beállítható [Azure PowerShell](/powershell/azure/).

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

## <a name="update-ip-filter-rules-using-rest"></a>IP-szűrési szabályok frissítése a REST használatával

A IoT Hub IP-szűrőjét az Azure erőforrás-szolgáltató REST-végpontjának használatával is lekérheti és módosíthatja. Lásd: `properties.ipFilterRules` a [createorupdate metódusban](/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-rule-evaluation"></a>IP-szűrési szabály kiértékelése

A rendszer az IP-szűrési szabályokat sorrendben alkalmazza, és az IP-címnek megfelelő első szabály határozza meg az elfogadás vagy az elutasítás műveletét.

Ha például fogadni szeretné a 192.168.100.0/22 tartomány címeit, minden mást viszont elutasítana, a táblázat első szabályának el kell fogadnia a 192.168.100.0/22 címtartományt. A következő szabálynak el kell utasítania minden címet a 0.0.0.0/0 tartomány használatával.

Az IP-szűrési szabályok sorrendjét a sorok elején lévő három, függőlegesen elhelyezett pontra kattintva, húzással módosíthatja.

Az IP-szűrési szabályok új sorrendjének mentéséhez kattintson a **Mentés** gombra.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-rule-order.png" alt-text="Alapértelmezett IP-szűrési beállítások IoT Hub":::

## <a name="next-steps"></a>További lépések

A IoT Hub képességeinek további megismeréséhez lásd:

* [Figyelő IoT Hub](monitor-iot-hub.md)
