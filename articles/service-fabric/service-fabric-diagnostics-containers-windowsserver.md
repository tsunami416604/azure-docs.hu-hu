---
title: "Az Azure Service Fabric tárolók figyelése és diagnosztikai |} Microsoft Docs"
description: "Megtudhatja, hogyan figyelése és diagnosztizálása az OMS Szolgáltatáshoz tartozó tárolók megoldással a Microsoft Azure Service Fabric összehangolva tárolók."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 319ee2c0f7492389bc1767aa2669dd273f8cfa1b
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2017
---
# <a name="monitoring-windows-server-containers-with-oms"></a>Windows Server OMS tárolók figyelése

## <a name="oms-containers-solution"></a>OMS-tárolók megoldás

Operations Management Suite (OMS) szolgáltatáshoz használható tárolók figyelés tárolók megoldással rendelkezik. A Service Fabric megoldás mellett ez a megoldás egy remek eszköz, amellyel a Service Fabric vezénylését tároló központi telepítésének figyelése. Íme egy egyszerű példa a megoldás az irányítópult néz:

![Alapszintű OMS irányítópult](./media/service-fabric-diagnostics-containers-windowsserver/oms-containers-dashboard.png)

Gyűjti a naplófájlokat, amelyek az OMS szolgáltatáshoz eszközben kérdezhetők le, és bármely metrikák vagy alatt generált események megjelenítéséhez használható különböző típusú is. A napló típusok tartoznak a következők:

1. ContainerInventory: tároló helye, a nevét, és a képeket információkat jeleníti meg.
2. ContainerImageInventory: információ a központilag telepített lemezképek, többek között az azonosítók vagy mérete
3. ContainerLog: más bejegyzések, adott hibanaplókat és a docker-naplók (stdout, stb.)
4. ContainerServiceLog: docker démon parancsok futtatása
5. Teljesítmény: teljesítményszámlálókat, beleértve a tároló processzor, memória, a hálózati forgalom, a lemez i/o, és a gazdagép gépekről egyéni metrikák

Ez a cikk tároló figyelését a fürt beállításához szükséges lépéseket ismerteti. Az OMS Szolgáltatáshoz tartozó tárolók megoldás kapcsolatos további tudnivalókért tekintse meg a [dokumentáció](../log-analytics/log-analytics-containers.md).

## <a name="1-set-up-a-service-fabric-cluster"></a>1. A Service Fabric-fürt beállítása

Hozzon létre egy fürtöt, az Azure Resource Manager sablonnal található [Itt](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows). Ne felejtse el hozzáadni egy egyedi OMS-munkaterület nevét. Ez a sablon is az alapértelmezett központi telepítése a fürt a preview build a Service Fabric (v255.255), ami azt jelenti, hogy az éles környezetben nem használható, és a Service Fabric-verziója nem frissíthető. Ha úgy dönt, hogy ezzel a sablonnal a hosszú távú vagy éles használja, majd a verziót a stabil verziószáma.

Ha a fürt készen áll, győződjön meg arról, hogy a megfelelő tanúsítvány telepítése, és győződjön meg arról, hogy biztosan csatlakozni tudjanak a fürt.

Győződjön meg arról, hogy az erőforráscsoport beállítva megfelelően a címsor a [Azure-portálon](https://portal.azure.com/) és az üzemelő példány keresése. Az erőforráscsoport a Service Fabric-erőforrások tartalmazza, és Naplóelemzési megoldást, valamint a Service Fabric megoldás is rendelkezik.

A meglévő Service Fabric-fürt módosítását:
* Győződjön meg arról, hogy engedélyezve van-e a Diagnostics (Ha nem, engedélyezze azt [frissítése a virtuálisgép-méretezési csoport](/rest/api/virtualmachinescalesets/create-or-update-a-set))
* Adja hozzá az OMS-munkaterület hozzon létre egy "A Service Fabric Analytics" megoldást az Azure piactéren keresztül
* A Service Fabric-megoldás a megfelelő (által létrehozott ÜVEGVATTA) Azure Storage-táblázatok adatait átvételéhez adatforrások szerkesztése, amely a fürt szerepel az erőforráscsoportban
* Ügynök hozzáadása egy [a virtuálisgép-méretezési csoport kiterjesztése](/powershell/module/azurerm.compute/add-azurermvmssextension) PowerShell vagy a frissítési a virtuálisgép-méretezési (azonos hivatkozásra a fenti, a Resource Manager-sablon módosítása)

## <a name="2-deploy-a-container"></a>2. A tároló üzembe

Miután a fürt készen áll, és meggyőződött róla, hogy hozzá tud férni, telepítse azt egy tároló. Ha úgy döntött, hogy állítsa a sablon által előzetes verzióját használják, is megismerheti a Service Fabric új docker compose funkciót. Figyelembe kell vennie, hogy az első egy tároló lemezképfájlt egy fürtbe, vesz letölti a lemezképet a méretétől függően néhány percig.

## <a name="3-add-the-containers-solution"></a>3. A tárolók megoldás hozzáadása

Az Azure portálon létrehozhat egy tárolók (alatt a figyelés és a felügyeleti kategória) Azure piactéren keresztül. 

![Tárolók megoldás hozzáadása](./media/service-fabric-diagnostics-containers-windowsserver/containers-solution.png)

Az létrehozását lépést, a kérelmek az OMS-munkaterület. Jelölje ki a fenti telepítési hoztak létre. Ezt a lépést hozzáadja az OMS-munkaterület belül tárolók megoldást, és automatikusan felismeri a sablon által telepített OMS-ügynököt. Az ügynök elindul, a tárolók folyamatok, a fürt az adatgyűjtést, és körülbelül 10 – 15 perc, a megoldás világos fel adatokkal, mint a lemezképet a fenti irányítópult megjelennie.

## <a name="4-next-steps"></a>4. Következő lépések

OMS a munkaterületen a hasznos, ha elvégzésére különböző eszközöket biztosít. Ismerheti meg a megoldást az igényeinek megfelelően testre a következő beállításokat:
- Az beszerzése familiarized a [naplófájl keresési és lekérdezése](../log-analytics/log-analytics-log-searches.md) szolgáltatásai által kínált Naplóelemzési
- Konfigurálja az OMS-ügynököt egyes teljesítményszámlálókra átvételéhez (Nyissa meg a munkaterület kezdőlapjára > Beállítások > adatok > Windows-teljesítményszámlálók)
- Konfigurálja az OMS beállítása [riasztás automatikus](../log-analytics/log-analytics-alerts.md) szabályok észlelésére és diagnosztika