---
title: Az Azure Cloud Shell-szerkesztő használatával |} A Microsoft Docs
description: Az Azure Cloud Shell-szerkesztő használata áttekintése.
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: juluk
ms.openlocfilehash: caf6e18a9a30654710f5445ed6ab957a5253d62e
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259885"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Az Azure Cloud Shell-szerkesztő használatával

Az Azure Cloud Shell tartalmaz egy integrált fájl szerkesztőt, kezdve a nyílt forráskódú [Monaco szerkesztő](https://github.com/Microsoft/monaco-editor). A Cloud Shell-szerkesztő támogatja – például megjelenítik nyelvi funkciók, a parancskatalógus és a Fájlkezelőben.

![Cloud Shell-szerkesztő](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>A szerkesztő a megnyitásakor

Egyszerű fájl létrehozásához és szerkesztéséhez, indítsa el a szerkesztőben futtatásával `code .` a Cloud Shell terminálban. Ez a művelet megnyitja a szerkesztő az active Directoryval működő állítsa be a terminálon.

Gyors szerkesztését fájl közvetlenül megnyitásához futtassa `code <filename>` nélkül a Fájlkezelőben a szerkesztő megnyitásához.

A felhasználói felület gomb keresztül-szerkesztő megnyitásához kattintson a `{}` szerkesztő ikonra az eszköztárban. Ez a szerkesztő megnyitásához és a Fájlkezelőben az alapértelmezett a `/home/<user>` könyvtár.

## <a name="closing-the-editor"></a>A szerkesztő bezárása

Zárja be a szerkesztőt, nyissa meg a `...` művelet panel felső közvetlenül a szerkesztőben, majd válassza a `Close editor`.

![Szerkesztő bezárása](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Parancskatalógus

Indítsa el a parancskatalógus, használja a `F1` kulcsát, amikor a fókusz a szerkesztőben van beállítva. Nyissa meg a parancskatalógust is végezhető el a műveleti panelen.

![Cmd paletta](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Hozzájárul a Monaco-szerkesztő

Kiemelés támogatást a Cloud Shell-szerkesztőben a felsőbb rétegbeli funkciók keresztül támogatott a [Monaco szerkesztő](https://github.com/Microsoft/monaco-editor)Monarch szintaxis definíciók kihasználja. Ismerje meg, hogyan terjedelmű, olvassa el a [Monaco közreműködői útmutató](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md).

## <a name="next-steps"></a>További lépések
[Próbálja ki a rövid útmutatóban a Cloud Shell Bash](quickstart.md)
[Cloud Shell az integrált eszközök teljes listájának megtekintése](features.md)