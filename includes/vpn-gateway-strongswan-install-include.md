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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "69520846"
---
Az alábbi konfigurációt használták a következő lépésekhez:

  | | |
  |---|---|
  |Computer| Ubuntu Server 18.04|
  |Függőségek| Alapú strongswan |


A szükséges alapú strongswan-konfiguráció telepítéséhez használja az alábbi parancsokat:

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

Az Azure parancssori felületének telepítéséhez használja az alábbi parancsot:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[További tudnivalók az Azure CLI telepítéséről](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
