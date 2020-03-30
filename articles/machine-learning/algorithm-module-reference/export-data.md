---
title: 'Adatok exportálása: modul hivatkozási'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja az Adatok exportálása modult az Azure Machine Learningben az eredmények, köztes adatok és munkaadatok mentéséhez a folyamatokból az Azure Machine Learningen kívüli felhőalapú tárolási célállomásokra.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 807771fd4018c9666f059c965370ebc36d0105df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456301"
---
# <a name="export-data-module"></a>Adatok exportálása modul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal eredményeket, köztes adatokat és munkaadatokat menthet a folyamatokból a felhőalapú tárolási célhelyekre. 

Ez a modul támogatja az adatok exportálását a következő felhőalapú adatszolgáltatásokba:

- Azure Blob-tároló
- Azure-fájlmegosztás
- Azure Data Lake
- Azure Data Lake Gen2

Az adatok exportálása előtt először regisztrálnia kell egy adattaboltot az Azure Machine Learning-munkaterületen. További információt az [Access-adatok az Azure storage-szolgáltatásokban című témakörben talál.](../how-to-access-data.md)

## <a name="how-to-configure-export-data"></a>Az adatok exportálásának beállítása

1. Adja hozzá az **Adatok exportálása** modult a tervező folyamatához. Ez a modul a **Bemenet és kimenet** kategóriában található.

1. Csatlakoztassa **az Adatok exportálását** az exportálni kívánt adatokat tartalmazó modulhoz.

1. Válassza **az Adatok exportálása** lehetőséget a **Tulajdonságok** ablaktábla megnyitásához.

1. **Az Adattár**ban válasszon ki egy meglévő adattalapot a legördülő listából. Új adattatente is létrehozhat. Az [Access-adatok az Azure storage-szolgáltatásokban](../how-to-access-data.md)való felkeresésével ellenőrizheti, hogyan.

1. A jelölőnégyzet, **Újragenerálása kimenet**, dönti el, hogy végre a modult, hogy újragenerálja a kimenetet a működési idő. 

    Alapértelmezés szerint nincs bejelölve, ami azt jelenti, hogy ha a modult korábban ugyanazokat a paramétereket hajtották végre, a rendszer újra felhasználja az utolsó futtatás kimenetét a futási idő csökkentése érdekében. 

    Ha be van jelölve, a rendszer újra végrehajtja a modult a kimenet újragenerálásához.

1. Adja meg az elérési utat abban az adattárban, ahol az adatok vannak. Az elérési út relatív elérési út. Az üres elérési utak vagy URL-elérési utak nem engedélyezettek.


1. **A Fájlformátum mezőben**válassza ki azt a formátumot, amelyben az adatokat tárolni szeretné.
 
1. Küldje el a folyamatot.

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
