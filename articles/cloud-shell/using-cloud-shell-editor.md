---
title: Az Azure Cloud Shell-szerkesztő használata | Microsoft dokumentumok
description: Az Azure Cloud Shell-szerkesztő használatának áttekintése.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 7f597bb5cba1a12bdb93325fe2b877ffc644e3e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60199208"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Az Azure Cloud Shell-szerkesztő használata

Az Azure Cloud Shell tartalmaz egy integrált fájlszerkesztőt, amely a nyílt forráskódú [Monaco Szerkesztőből](https://github.com/Microsoft/monaco-editor)készült. A Cloud Shell szerkesztőjének támogatott funkciói közé tartozik a nyelvi kiemelés, a parancsválaszték és a fájlkezelő.

![Cloud Shell-szerkesztő](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>A szerkesztő megnyitása

Fájlok egyszerű létrehozásához és szerkesztéséhez indítsa el a szerkesztőt a Cloud Shell terminálján, a `code .` parancs futtatásával. Ez a művelet a terminálon beállított aktív munkakönyvtárral nyitja meg a szerkesztőt.

Ha közvetlenül szeretne megnyitni egy fájlt gyorsszerkesztéshez, futtassa a `code <filename>` parancsot a szerkesztő fájlkezelő nélküli megnyitásához.

A szerkesztő felhasználói felületről való megnyitásához kattintson a `{}` szerkesztő ikonra az eszköztárból. Ekkor megnyílik a szerkesztő, és alapértelmezés szerint a `/home/<user>` könyvtárba irányítja a fájlkezelőt.

## <a name="closing-the-editor"></a>A szerkesztő bezárása

A szerkesztő bezárásához `...` nyissa meg a műveletpanelt a `Close editor`szerkesztő jobb felső részén, és válassza a lehetőséget.

![Szerkesztő bezárása](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Parancspaletta

A parancspaletta elindításához használja a `F1` kulcsot, ha a szerkesztőre van beállítva. A parancspaletta megnyitása a műveletpanelen keresztül is elvégezhető.

![Cmd paletta](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Hozzájárulás a monacói szerkesztőhöz

A Cloud Shell szerkesztőnyelv-kiemelési támogatását a [Monaco Editor](https://github.com/Microsoft/monaco-editor)Monarch szintaxisdefinícióinak upstream funkciói támogatják. Ha meg szeretné tudni, hogyan lehet hozzájárulást tenni, olvassa el a [Monaco közreműködői útmutatót](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md).

## <a name="next-steps"></a>További lépések
[Próbálja ki a Bash in Cloud Shell](quickstart.md)
rövid útmutatóját[Az integrált Cloud Shell-eszközök teljes listájának megtekintése](features.md)