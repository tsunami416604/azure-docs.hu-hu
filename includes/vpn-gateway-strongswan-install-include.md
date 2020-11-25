---
title: fájlbefoglalás
description: fájlbefoglalás
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f02fa49b62a2e3d617617a20518810209d3879b7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026359"
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

[További tudnivalók az Azure CLI telepítéséről](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)