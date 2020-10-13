---
title: Az előfeltételek közé tartozik az azonos fájlban két lap által megosztott fájl | Microsoft Docs
description: Megrendelt előfeltételek üzembe helyezése Azure Data Box
services: databox
author: priestlg
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/15/2020
ms.author: v-grpr
ms.openlocfilehash: 6aaf57d9bcdfb1f350e1d54937e9c705dd32116e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85392478"
---
### <a name="for-service"></a>A szolgáltatás esetén

[!INCLUDE [Data Box service prerequisites](data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Az eszköz esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Rendelkezik egy, az adatközponti hálózatra csatlakoztatott gazdagéppel. A Data Box erről a gépről másolja majd az adatokat. A gazdagépnek egy támogatott operációs rendszert kell futtatnia az [Azure Data Box rendszerkövetelményeinél](../articles/databox/data-box-system-requirements.md) leírtaknak megfelelően.
* Az adatközpontnak nagy sebességű hálózattal kell rendelkeznie. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. Ha egy 10 GbE-kapcsolat nem érhető el, akkor a rendszer egy GbE adatkapcsolatot használ, de a másolási sebesség hatással van.
