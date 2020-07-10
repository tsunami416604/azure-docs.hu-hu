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
ms.openlocfilehash: 2d0e171807985deaebe8faa625d6b767c2a3efd7
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86218381"
---
Az alábbi konfigurációt használták a következő lépésekhez:

- Számítógép: Ubuntu Server 18,04
- Függőségek: alapú strongswan


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
