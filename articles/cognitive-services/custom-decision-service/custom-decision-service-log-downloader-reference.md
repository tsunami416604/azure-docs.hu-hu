---
title: LogDownloader - Azure kognitív szolgáltatások |} Microsoft Docs
description: Töltse le a telepítési naplófájlok tartalmaznak Azure egyéni döntési szolgáltatás hozzák létre.
services: cognitive-services
author: marco-rossi29
manager: marco-rossi29
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: marossi
ms.openlocfilehash: 783b534b3b3f4bb7f5d9f073f491690759edfea5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348986"
---
# <a name="logdownloader"></a>LogDownloader

Töltse le az Azure egyéni döntési szolgáltatás hozzák létre és készítése a naplófájlokat a *.gz* kísérletezhet által használt fájlokat.

## <a name="prerequisites"></a>Előfeltételek

- Python 3: Telepítve és az elérési úton. Azt javasoljuk, hogy a nagy méretű fájlok kezelését, 64 bites.
- A *Microsoft/MOLWT-ds* tárház: [a tárház klónozása](https://github.com/Microsoft/mwt-ds).
- A *azure-storage-blob* csomag: telepítés részleteit, látogasson el [Microsoft Azure Storage kódtára a Pythonhoz](https://github.com/Azure/azure-storage-python#option-1-via-pypi).
- Adja meg az Azure tárolási kapcsolat karakterláncát a *mwt-ds/DataScience/ds.config*: kövesse a *my_app_id: my_connectionString* sablont. Megadhatja, hogy több `app_id`. Amikor futtatja `LogDownloader.py`, ha a bemeneti `app_id` nem található a `ds.config`, `LogDownloader.py` használja a `$Default` kapcsolati karakterláncot.

## <a name="usage"></a>Használat

Ugrás a `mwt-ds/DataScience` , és futtassa `LogDownloader.py` vonatkozó argumentumok, ahogy az az alábbi kódban:

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>Paraméterek

| Input (Bemenet) | Leírás | Alapértelmezett |
| --- | --- | --- |
| `-h`, `--help` | Megjelenítése a Súgó üzenetet, és zárja be. | |
| `-a APP_ID`, `--app_id APP_ID` | Az alkalmazás azonosítója (Ez azt jelenti, hogy az Azure Storage blob tároló neve). | Szükséges |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | A alapkönyvtárának a letöltése (almappa jön létre).  | Szükséges |
| `-s START_DATE`, `--start_date START_DATE` | A letöltés kezdő dátum (tartalmaz), a *éééé-hh-nn* formátumban. | `None` |
| `-e END_DATE`, `--end_date END_DATE` | A letöltés záró dátum (tartalmaz), *éééé-hh-nn* formátumban. | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | A felülírás módot használja. | |
| | `0`: Nem írható felül; Kérje meg, hogy blobok jelenleg használatban van. | Alapértelmezett | |
| | `1`: Kérnie a felhasználót, ha a fájlok mérete eltér, vagy ha a blobok éppen használatban vannak folytatásról. | |
| | `2`: Mindig felülírja a; Töltse le a jelenleg használt blobokat. | |
| | `3`: Soha ne írja felül, és ha a mérete nagyobb, kérése nélkül bővítése Töltse le a jelenleg használt blobokat. | |
| | `4`: Soha ne írja felül, és ha a mérete nagyobb, kérése nélkül bővítése hagyja ki a jelenleg használt blobokat. | |
| `--dry_run` | A nyomtató mely blobok volna le vannak töltve, letöltése nélkül. | `False` |
| `--create_gzip` | Hozzon létre egy *gzip* Vowpal Wabbit fájlt. | `False` |
| `--delta_mod_t DELTA_MOD_T` | Időablak (másodpercben), az észlelése, hogy a fájl jelenleg használatban van. | `3600` mp (`1` óra) |
| `--verbose` | Nyomtatási további részleteket. | `False` |
| `-v VERSION`, `--version VERSION` | A napló letöltő verziót kell használni. | |
| | `1`: A nyers naplók (csak a visszamenőleges kompatibilitás érdekében). | Elavult |
| | `2`: A főtt naplókhoz. | Alapértelmezett |

### <a name="examples"></a>Példák

Az Azure Storage-blob tárolóban az adatok letöltése a próbafuttatást használja a következő kódot:
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

Csak a létrehozása óta 2018. január 1. a naplók letöltéséhez `overwrite_mode=4`, használja a következő kódot:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

Létrehozásához egy *gzip* fájlt a letöltött fájlok egyesítése, használja a következő kódot:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```