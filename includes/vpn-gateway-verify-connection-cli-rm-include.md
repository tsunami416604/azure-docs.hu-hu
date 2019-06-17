---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0e009354e66ab13cdb9fbc3cf9e4b37e904bdfd1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66121212"
---
Az [az network vpn-connection show](/cli/azure/network/vpn-connection) paranccsal ellenőrizheti, sikeres volt-e a kapcsolat. A példában a „--name” a tesztelni kívánt kapcsolat nevére utal. Ha a kapcsolat létrehozás alatt áll, a kapcsolat állapota „Connecting”. Ha a kapcsolat létrejött, az állapot „Connected” értékűre változik.

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
