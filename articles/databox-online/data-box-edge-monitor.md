---
title: Az Azure Data Box Edge-eszköz figyelése |} A Microsoft Docs
description: Az Azure Data Box Edge figyelése az Azure portal és a helyi webes felhasználói felület használatát ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 188f5c6cfbb4650ad1ff767955d064f8e0c3cb70
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002646"
---
# <a name="monitor-your-azure-data-box-edge"></a>Az Azure Data Box Edge figyelése

Ez a cikk ismerteti az Azure Data Box Edge figyelése. Az eszköz figyeléséhez, használhatja az Azure portal vagy a helyi webes felületén. Az Azure portal használatával eszköz események megtekintése, konfigurálása és a riasztások kezelése és metrikákat tekinthet meg. A fizikai eszköz helyi webes felhasználói felület használatával a különböző eszköz összetevők hardver állapotának megtekintése.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Eszköz-események megtekintése és az ezekkel kapcsolatos riasztások
> * Alkatrészek hardverállapot megtekintése
> * Az eszköz a kapacitás és a tranzakciós metrikáinak megtekintése
> * Konfigurálhatja és kezelheti a riasztásokat

## <a name="view-device-events"></a>Eszköz-események megtekintése

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Hardverállapot megtekintése

Az alábbi lépéseket a helyi webes felhasználói Felületét, hogy az eszköz-összetevők hardver állapotának megtekintése.

1. Csatlakozzon a helyi webes felületén az eszközt.
2. Lépjen a **karbantartási > hardverállapot**. A különböző eszköz összetevők állapotát tekintheti meg.

    ![Hardverállapot megtekintése](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Metrikák megtekintése

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Riasztások kezelése

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>További lépések 

További tudnivalókat a [sávszélesség-kezeléssel foglalkozó részben](data-box-edge-manage-bandwidth-schedules.md) talál.