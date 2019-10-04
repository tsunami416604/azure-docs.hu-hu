---
title: LogDownloader – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Az Azure Custom Decision Service által létrehozott naplófájlok letöltése.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: marossi
ROBOTS: NOINDEX
ms.openlocfilehash: 33cc0d0dcf16ff82ac128507566427e123020236
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707232"
---
# <a name="logdownloader"></a>LogDownloader

Töltse le az Azure Custom Decision Service által létrehozott naplófájlokat, és létrehozza a kísérletezéshez használt *. gz* fájlokat.

## <a name="prerequisites"></a>Előfeltételek

- Python 3: Az elérési útra. A nagyméretű fájlok kezelésére a 64 bites verziót javasoljuk.
- A *Microsoft/MWT-DS* adattár: [A](https://github.com/Microsoft/mwt-ds)tárház klónozása.
- Az *Azure-Storage-blob* csomag: A telepítés részleteit a [Python Microsoft Azure Storage könyvtárában](https://github.com/Azure/azure-storage-python#option-1-via-pypi)találhatja meg.
- Adja meg az Azure Storage-beli kapcsolatok karakterláncát a *MWT-DS/DataScience/DS. config fájlban*: Kövesse a *my_app_id: my_connectionString* sablont. Több `app_id`is megadható. `LogDownloader.py`Ha a futtatásakor a bemenet `ds.config` `app_id` nem található a alkalmazásban, `LogDownloader.py` a a `$Default` kapcsolódási karakterláncot használja.

## <a name="usage"></a>Használat

Nyissa `mwt-ds/DataScience` meg a `LogDownloader.py` parancsot, és futtassa a megfelelő argumentumokat a következő kódban részletezett módon:

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>Paraméterek

| Input (Bemenet) | Leírás | Alapértelmezett |
| --- | --- | --- |
| `-h`, `--help` | Jelenítse meg a Súgó üzenetet, és lépjen ki. | |
| `-a APP_ID`, `--app_id APP_ID` | Az alkalmazás azonosítója (azaz az Azure Storage blob-tároló neve). | Kötelező |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | Az adatok letöltésének alapkönyvtára (a rendszer létrehoz egy almappát).  | Kötelező |
| `-s START_DATE`, `--start_date START_DATE` | A letöltés kezdő dátuma (tartalmazza), *éééé-hh-nn* formátumban. | `None` |
| `-e END_DATE`, `--end_date END_DATE` | A letöltés befejezési dátuma (tartalmazza), *éééé-hh-nn* formátumban. | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | A használandó felülírási mód. | |
| | `0`: Soha ne írja felül; kérje meg a felhasználót, hogy jelenleg használják-e a blobokat. | Alapértelmezett |
| | `1`: Kérje meg a felhasználót, hogy a fájlok különböző méretekkel való elvégzése, illetve a Blobok jelenleg való használata esetén hogyan járjon el. | |
| | `2`: Mindig írja felül; jelenleg használt Blobok letöltése. | |
| | `3`: Soha ne írja felül a felülírást, és fűzze hozzá, ha a méret nagyobb, a kérés nélkül; jelenleg használt Blobok letöltése. | |
| | `4`: Soha ne írja felül a felülírást, és fűzze hozzá, ha a méret nagyobb, a kérés nélkül; jelenleg használt Blobok kihagyása. | |
| `--dry_run` | A letöltés nélkül kinyomtathatja, hogy mely Blobok lettek letöltve. | `False` |
| `--create_gzip` | Hozzon létre egy *gzip* -fájlt a Vowpal Wabbit. | `False` |
| `--delta_mod_t DELTA_MOD_T` | Az az időintervallum (másodpercben), amely azt észleli, hogy egy fájl jelenleg használatban van-e. | `3600`másodperc (`1` óra) |
| `--verbose` | További részletek nyomtatása. | `False` |
| `-v VERSION`, `--version VERSION` | A használni kívánt log Downloader-verzió. | |
| | `1`: A nem főtt naplók esetében (csak visszamenőleges kompatibilitás esetén). | Elavult |
| | `2`: A főtt naplókhoz. | Alapértelmezett |

### <a name="examples"></a>Példák

Az Azure Storage blob-tárolóban lévő összes érték letöltésének száraz futtatásához használja a következő kódot:
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

Csak a 2018 `overwrite_mode=4`-as január 1. óta létrehozott naplók letöltéséhez használja a következő kódot:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

Az összes letöltött fájlt egyesítő *gzip* -fájl létrehozásához használja a következő kódot:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```
