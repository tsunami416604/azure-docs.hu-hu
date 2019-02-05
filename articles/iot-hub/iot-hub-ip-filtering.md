---
title: Az Azure IoT Hub IP-kapcsolat szűrők |} A Microsoft Docs
description: Hogyan használható az IP-szűrés blokk-kapcsolatokat, az adott IP-címek az Azure IoT hubra. Letilthatja egyes érkező kapcsolatokat vagy IP-címek tartományát.
author: rezasherafat
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: rezas
ms.openlocfilehash: cd382c0daff79b487f4ecae01ad852f6e57f3a25
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734249"
---
# <a name="use-ip-filters"></a>IP-szűrők használata

Biztonsági fontos szempont alapján az Azure IoT Hub bármely IoT-megoldás a rendszer. Egyes esetekben kell kifejezetten megad az IP-címek eszközök csatlakozhatnak, amelyről a biztonsági konfiguráció részeként. A *IP-szűrő* funkciója lehetővé teszi visszautasítja, vagy meghatározott IPv4-címek érkező forgalmat fogad szabályok konfigurálása.

## <a name="when-to-use"></a>A következő esetekben használja

Amikor hasznos lehet az IoT Hub-végpontok bizonyos IP-címek blokkolása, van két adott használati eseteket:

* Az IoT hub kell forgalom fogadására csak a megadott IP-címről, és elvetheti a minden mást. Például használja az IoT hub- [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) egy IoT hubot és a helyszíni infrastruktúra közötti privát kapcsolatok létesíthetők.

* Az IoT hub rendszergazdája gyanús számítógépként azonosított IP-címekről érkező forgalom elutasítása kell.

## <a name="how-filter-rules-are-applied"></a>Szűrési szabályok alkalmazása

Az IP-szűrési szabályok vonatkoznak az IoT Hub szolgáltatási szint. Ezért az IP-szűrési szabályok vonatkoznak az összes kapcsolat olyan eszközökkel és a háttér-alkalmazásokat bármely támogatott protokoll használatával.

Bármely IP-címet, amely az IoT hub rejecting IP szabály megegyezik a kapcsolódási kísérlet kap egy jogosulatlan 401-es állapotkód és a leírást. A válaszüzenet említse meg az IP-szabályt.

## <a name="default-setting"></a>Alapértelmezett beállítás

Alapértelmezés szerint a **IP-szűrő** rács a portálon az IoT hub, az üres. Ez az alapértelmezett beállítás, az azt jelenti, hogy a központ IP-címeket érkező kapcsolatokat fogad-e. Ez az alapértelmezett beállítás megegyezik egy szabályt, amely elfogadja a 0.0.0.0/0 IP-címtartományt.

![IoT Hub default IP filter settings](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Adja hozzá, vagy egy IP-szűrési szabály szerkesztése

Amikor hozzáad egy IP-szűrési szabály, a rendszer felszólítja a következő értékeket:

* Egy **IP-szűrési szabály nevének** , amely legfeljebb 128 karakter hosszúságú egyedi, kis-és nagybetűket, alfanumerikus karakterláncnak kell lennie. Csak az ASCII 7 bites alfanumerikus karaktereket plusz `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` fogadja.

* Válassza ki a **elutasítása** vagy **fogadja el** , a **művelet** az IP-szűrési szabály.

* Adjon meg egyetlen IPv4-cím vagy IP-címeket a CIDR-jelölésrendszerben egy kódblokkot. Például a CIDR jelölésrendszerben 192.168.100.0/22 jelöli az 1024 IPv4-címek 192.168.100.0 a 192.168.103.255 közötti.

![Egy IoT hubra egy IP-szűrési szabály hozzáadása](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

A szabály menti, után megjelenik egy riasztást arról tájékoztatja, a frissítés folyamatban van.

![Értesítés az IP-szűrési szabály mentése](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

A **Hozzáadás** lehetőség le van tiltva, ha egyenlege eléri a maximális 10 IP-szűrési szabályok.

Meglévő szabály szerkesztéséhez kattintson duplán a szabályt tartalmazó sorhoz.

> [!NOTE]
> Visszautasítja IP címek tudja megakadályozni más Azure-szolgáltatásoktól (például az Azure Stream Analytics, Azure Virtual Machines vagy a portálon a Device Explorer) az IoT hub használata.

> [!WARNING]
> Ha üzenetek olvasásához az IoT hub a IP-szűrés engedélyezve van az Azure Stream Analytics (ASA) használja, használja az Event Hub-kompatibilis névvel és a végpont az IoT hub az ASA kapcsolati karakterláncban.

## <a name="delete-an-ip-filter-rule"></a>Az IP-szűrési szabály törlése

Az IP-szűrési szabály törléséhez válassza ki egy vagy több szabályt a rácson, majd kattintson a **törlése**.

![Egy IoT-központ IP-szűrési szabály törlése](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Lekérése és frissítése az Azure CLI-vel IP-szűrők

Az IoT Hub IP-szűrők lekérje és frissíteni [Azure CLI-vel](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest). 

Az IoT hub az aktuális IP-szűrők lekéréséhez futtassa:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Ez visszaad egy JSON-objektumot, ahol a meglévő IP-szűrők vannak felsorolva a `properties.ipFilterRules` kulcs:

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

Az IoT hub egy új IP-szűrő hozzáadásához futtassa:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Az IoT hub egy meglévő IP-szűrő eltávolításához futtassa:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Vegye figyelembe, hogy `<ipFilterIndexToRemove>` meg kell felelnie az IP-szűrők az IoT hub rendezése `properties.ipFilterRules`.


## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Lekérése és frissítése az Azure PowerShell-lel IP-szűrők

Az IoT Hub IP-szűrők lekérje és beállítása keresztül [Azure PowerShell-lel](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azps-1.2.0). 

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzureRmResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzureRmResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>Frissítse az IP-szűrési szabályok REST használatával

Előfordulhat, hogy lekérni, és az IoT Hub használata az Azure erőforrás-szolgáltató REST-végpont IP-szűrő módosítása. Lásd: `properties.ipFilterRules` a [createorupdate metódust](https://docs.microsoft.com/en-us/rest/api/iothub/iothubresource/createorupdate).


## <a name="ip-filter-rule-evaluation"></a>IP-szűrési szabály értékelése

IP-szűrési szabályok a rendszer sorrendben alkalmazza, és az első szabály, amely az IP-cím megegyezik a elfogadása vagy elutasítása műveletet határozza meg.

Például ha azt szeretné, a tartomány 192.168.100.0/22 címeit fogadja el, és minden más elutasítása, az első szabály a rácson a cím-tartományt 192.168.100.0/22 kell fogadnia. A következő szabályt kell utasítania összes címet a tartomány 0.0.0.0/0 használatával.

A rács az IP-szűrési szabályok sorrendjének módosítása egy sor elején függőleges három pontra kattint, és húzza használatával, és dobja el.

Az új IP-szűrő szabály rendelés mentéséhez kattintson az **mentése**.

![Az IoT Hub IP-szűrési szabályok sorrendjének módosítása](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>További lépések

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Műveletek figyelése](iot-hub-operations-monitoring.md)
* [Az IoT Hub-metrikák](iot-hub-metrics.md)