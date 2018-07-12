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
ms.openlocfilehash: 5169432af65a046465c9d1ced349687d6fdc8bb7
ms.sourcegitcommit: caebf2bb2fc6574aeee1b46d694a61f8b9243198
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2018
ms.locfileid: "35394026"
---
Python az alkalmazás elindítása során hibát észlel, ha csak egy egyszerű hibalap fogja visszaadni (például) "Az oldal nem lehet megjeleníteni, mert egy belső kiszolgálóhiba történt.").

Python-alkalmazáshibák rögzítésére:

1. Válassza ki az Azure Portalon, a web app alkalmazásban **beállítások**.
2. Az a **beállítások** lapon jelölje be **Alkalmazásbeállítások**.
3. A **Alkalmazásbeállítások**, adja meg a következő kulcs-érték párt:
    * Kulcs: WSGI_LOG
    * Érték: D:\home\site\wwwroot\logs.txt (adja meg a kívánt fájl nevét)

Meg kell jelennie a wwwroot mappában logs.txt fájlban.
