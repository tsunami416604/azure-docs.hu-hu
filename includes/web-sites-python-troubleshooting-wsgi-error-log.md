---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179723"
---
Ha a Python hibát észlel az alkalmazás indításakor, csak egy egyszerű hibalapot ad vissza (például "Az oldal nem jeleníthető meg, mert belső kiszolgálóhiba történt.").

Python-alkalmazáshibák rögzítése:

1. Az Azure Portalon a webalkalmazásban válassza a **Beállítások lehetőséget.**
2. A **Beállítások** lapon válassza az **Alkalmazásbeállításai lehetőséget**.
3. Az **Alkalmazásbeállítások**csoportban adja meg a következő kulcs/érték párt:
    * Kulcs : WSGI_LOG
    * Érték : D:\home\site\wwwroot\logs.txt (adja meg a választott fájlnevet)

Most a hibák jelennek meg a logs.txt fájlban a wwwroot mappában.
