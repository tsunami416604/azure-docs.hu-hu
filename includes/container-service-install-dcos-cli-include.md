---
title: A DC/OS parancssori felület telepítése | Microsoft Docs
description: A DC/OS parancssori felület telepítése.
services: container-service
documentationcenter: ''
author: rgardler
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Tárolók, mikroszolgáltatások, DC/OS, Azure
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/10/2016
ms.author: rogardle
ms.openlocfilehash: a8ea47f158c0d666340815d2e039995c7483257f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23761967"
---
> [!NOTE]
> Erre a DC/OS-alapú ACS-fürtökkel végzett munka esetén van szükség. A Swarm-alapú ACS-fürtök esetén erre a lépésre nincs szükség.
> 
> 

Először is, [csatlakozzon a DC/OS-alapú ACS-fürthöz](../articles/container-service/container-service-connect.md). Ezután az alábbi parancsok használatával telepítheti a DC/OS parancssori felületet az ügyfélgépre:

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Ha a Python egy régebbi verzióját használja, előfordulhat, hogy „InsecurePlatformWarnings” figyelmeztetésekbe ütközik. Ezek biztonságosan figyelmen kívül hagyhatók.

A parancskörnyezet újraindítása nélküli indításhoz futtassa a következő parancsot:

```bash
source ~/.bashrc
```

Új parancskörnyezet indítása esetén ez a lépés elhagyható.

Most meggyőződhet arról, hogy a parancssori felület telepítve van:

```bash
dcos --help
```