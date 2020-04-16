---
title: Leképezési adatfolyam létrehozása
description: Azure Data Factory-térképészeti adatfolyam létrehozása
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: cd1342ae5912a9a91f1bd8d1bdacecdb5a2bbdb0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416516"
---
# <a name="create-azure-data-factory-data-flow"></a>Azure Data Factory-adatfolyam létrehozása

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Az ADF-ben lévő adatfolyamatok leképezése leképezése leképezése leképezése leképezése az adatok nagy méretekben történő átalakítását biztosítja kódolás nélkül. Az adatátalakítási feladatot az adatfolyam-tervezőben átalakítások sorozatának összeállításával tervezheti meg. Kezdje tetszőleges számú forrásátalakítással, majd adatátalakítási lépésekkel. Ezután töltse ki az adatfolyamot a fogadóval, hogy az eredményeket egy célhelyen landoljon.

Első lépések: először hozzon létre egy új V2 Data Factory az Azure Portalon. Az új gyár létrehozása után kattintson a "& Monitor" csempére a Data Factory felhasználói felületének elindításához.

![Adatfolyam-beállítások](media/data-flow/v2portal.png "adatfolyam létrehozása")

Miután a Data Factory felhasználói felületen, használhatja a minta adatfolyamok. A minták az ADF-sablontárból érhetők el. Az ADF-ben hozza létre a "Folyamat sablonból" lehetőséget, és válassza ki az Adatfolyam kategóriát a sablongyűjteményből.

![Adatfolyam-beállítások](media/data-flow/template.png "adatfolyam létrehozása")

A rendszer kérni fogja az Azure Blob Storage-fiók adatainak megadását.

![Adatfolyam-beállítások](media/data-flow/template2.png "adatfolyam létrehozása 2")

[Az e mintákhoz használt adatok itt találhatók.](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata) Töltse le a mintaadatokat, és tárolja a fájlokat az Azure Blob storage-fiókokban, hogy végrehajthassa a mintákat.

## <a name="create-new-data-flow"></a>Új adatfolyam létrehozása

Az ADF felhasználói felületének "Erőforrás létrehozása" pluszjel" gombjával hozhat létre adatfolyamokat.

![Adatfolyam-beállítások](media/data-flow/newresource.png "Új erőforrás")

## <a name="next-steps"></a>További lépések

Kezdje meg az adatátalakítás kiépítését egy [forrásátalakítással.](data-flow-source.md)
