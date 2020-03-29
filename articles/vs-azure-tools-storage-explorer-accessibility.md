---
title: Az Azure Storage Explorer kisegítő lehetőségei (előzetes verzió) | Microsoft dokumentumok
description: Az Azure Storage Explorer kisegítő lehetőségei (előzetes verzió)
services: storage
documentationcenter: na
author: MrayermannMSFT
manager: jinglouMSFT
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2018
ms.author: marayerm
ms.openlocfilehash: aa42d6b898a3cc474575f6c8dae65ff420fc8e30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60457266"
---
# <a name="storage-explorer-accessibility"></a>A Tárterület-kezelő kisegítő lehetőségei

## <a name="screen-readers"></a>Képernyőolvasók

A Storage Explorer támogatja a képernyőolvasó használatát Windows és Mac rendszeren. Az egyes platformokhoz a következő képernyőolvasók használata ajánlott:

Platform | Képernyőolvasó
---------|--------------
Windows  | Nvda
Mac      | Hangalámondás
Linux    | (a képernyőolvasók linuxon nem támogatottak)

Ha a Storage Explorer használatakor kisegítő lehetőségekkel kapcsolatos probléma lép fel, [nyissa meg a Problémát a GitHubon.](https://github.com/Microsoft/AzureStorageExplorer/issues)

## <a name="zoom"></a>Zoom

A Storage Explorer alkalmazásban nagyítással nagyíthatja a szöveget. A nagyításhoz kattintson a Súgó menü **Nagyítás** parancsára. A Súgó menüsegítségével kicsinyítheti és visszaállíthatja a nagyítási szintet az alapértelmezett szintre.

![A súgómenü nagyítási beállításai][0]

A nagyítási beállítás növeli a felhasználói felület legtöbb elemének méretét. Javasoljuk, hogy engedélyezze a nagy szöveg- és nagyítási beállításokat az operációs rendszer számára, hogy az összes felhasználói felületi elem megfelelően méretezve legyen.

## <a name="high-contrast-themes"></a>Kontrasztos témák

A Storage Explorer két kontrasztos témával rendelkezik: **a Kontrasztos fény** és **a Kontrasztos sötétség sötét .** A téma módosításához válasszon a Súgó > témák menüből.

![Témák almenü][1]

A téma beállítása megváltoztatja a legtöbb felhasználói felületi elem színét. Javasoljuk, hogy engedélyezze az operációs rendszer megfelelő kontrasztos téma, hogy minden ui elemek megfelelően színezve.

## <a name="shortcut-keys"></a>Billentyűparancsok

### <a name="window-commands"></a>Ablakparancsok

Parancs       | Billentyűparancsok
--------------|--------------------
Új ablak    | **Vezérlő+Shift+N**
Szerkesztő bezárása  | **Control+F4**
Kilépés          | **Control+Shift+W**

### <a name="navigation-commands"></a>Navigációs parancsok

Parancs                | Billentyűparancsok
-----------------------|----------------------
Fókusz következő panel       | **F6**
Fókusz előző panel   | **Shift+F6**
Explorer               | **Control+Shift+E**
Fiókkezelés     | **Control+Shift+A**
Oldalsáv be- és bekapcsolása        | **Control+B**
Tevékenységnapló           | **Vezérlő+Shift+L**
Műveletek és tulajdonságok | **Control+Shift+P**
Jelenlegi szerkesztő         | **Control+Kezdőlap**
Következő szerkesztő            | **Control+Page Down**
Előző szerkesztő        | **Control+Page Up**

### <a name="zoom-commands"></a>Nagyítási parancsok

Parancs  | Billentyűparancsok
---------|------------------
Nagyítás  | **Control+=**
Kicsinyítés | **Control+-**

### <a name="blob-and-file-share-editor-commands"></a>Blob- és fájlmegosztási szerkesztő parancsai

Parancs | Billentyűparancsok
--------|--------------------
Vissza    | **Alt+Balra nyíl**
hozás | **Alt+Jobbra nyíl**
Fel      | **Alt+Fel nyíl**

### <a name="editor-commands"></a>Szerkesztőparancsok

Parancs | Billentyűparancsok
--------|------------------
Másolás    | **Control+C**
Kivágás     | **Control+X**
Beillesztés   | **Control+V**
Frissítés  | **Control+R**

### <a name="other-commands"></a>Egyéb parancsok

Parancs                | Billentyűparancsok
-----------------------|------------------
Fejlesztői eszközök be- és be- és bekapcsolása | **F12**
Újratöltés                 | **Alt+Vezérlő+R**

[0]: ./media/vs-azure-tools-storage-explorer-accessibility/Zoom.png
[1]: ./media/vs-azure-tools-storage-explorer-accessibility/HighContrast.png
