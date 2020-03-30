---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ddab6927044ce638e50ff3ad79aa3c35d046c820
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "69520846"
---
Az alábbi lépésekhez a következő konfigurációt használtuk:

  | | |
  |---|---|
  |Computer| Ubuntu Server 18.04|
  |Függőségek| erősSwan |


A következő parancsokkal telepítse a szükséges strongSwan konfigurációt:

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

Az Azure parancssori felületének telepítéséhez használja a következő parancsot:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[További utasítások az Azure CLI telepítéséhez](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
