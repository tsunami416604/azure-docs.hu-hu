---
title: A Azure Data Box Edge eszköz monitorozása | Microsoft Docs
description: Ismerteti, hogyan használható a Azure Portal és a helyi webes felhasználói felület a Azure Data Box Edge figyelésére.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 188f5c6cfbb4650ad1ff767955d064f8e0c3cb70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "60756738"
---
# <a name="monitor-your-azure-data-box-edge"></a>A Azure Data Box Edge figyelése

Ez a cikk a Azure Data Box Edge figyelését ismerteti. Az eszköz figyeléséhez Azure Portal vagy a helyi webes KEZELŐFELÜLETet használhatja. A Azure Portal használatával megtekintheti az eszközök eseményeit, konfigurálhatja és kezelheti a riasztásokat, és megtekintheti a metrikákat. A fizikai eszköz helyi webes felhasználói felületének használatával megtekintheti a különböző eszközök összetevőinek hardveres állapotát.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Az eszközök eseményeinek és a kapcsolódó riasztások megtekintése
> * Eszköz-összetevők hardveres állapotának megtekintése
> * Az eszköz kapacitásának és tranzakciós metrikáinak megtekintése
> * Riasztások konfigurálása és kezelése

## <a name="view-device-events"></a>Eszköz eseményeinek megtekintése

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Hardver állapotának megtekintése

A helyi webes felületen a következő lépésekkel tekintheti meg az eszköz összetevőinek hardveres állapotát.

1. Kapcsolódjon az eszköz helyi webes FELÜLETéhez.
2. Válassza a **karbantartás > hardver állapota**lehetőséget. Megtekintheti a különböző eszközök összetevőinek állapotát.

    ![Hardver állapotának megtekintése](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Metrikák megtekintése

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Riasztások kezelése

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>További lépések 

További tudnivalókat a [sávszélesség-kezeléssel foglalkozó részben](data-box-edge-manage-bandwidth-schedules.md) talál.