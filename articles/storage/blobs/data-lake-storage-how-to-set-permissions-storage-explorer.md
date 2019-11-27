---
title: 'Azure Storage Explorer: hozzáférés kezelése Azure Data Lake Storage Gen2'
description: Ebben a témakörben megtudhatja, hogyan állíthatja be az engedélyeket Azure Storage Explorer használatával a Azure Data Lake Storage Gen2-kompatibilis Storage-fiókban található fájlokhoz és könyvtárakhoz.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b34103e521def678acce17e3292e04fca95b5e6e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327968"
---
# <a name="use-azure-storage-explorer-to-manage-access-in-azure-data-lake-storage-gen2"></a>Azure Storage Explorer használata a hozzáférés kezeléséhez Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2ban tárolt fájlok támogatják a részletes engedélyeket és a hozzáférés-vezérlési lista (ACL) kezelését. A részletes engedélyek és az ACL-kezelés együttes használata lehetővé teszi, hogy az adataihoz való hozzáférés rendkívül részletességgel kezelhető legyen.

Ebből a cikkből megtudhatja, hogyan használhatja a Azure Storage Explorer:

> [!div class="checklist"]
> * Fájl szintű engedélyek beállítása
> * Könyvtár szintű engedélyek beállítása
> * Felhasználók vagy csoportok hozzáadása hozzáférés-vezérlési listához

## <a name="prerequisites"></a>Előfeltételek

A folyamat legjobb ábrázolásához a [Azure Storage Explorer](data-lake-storage-Explorer.md)gyors üzembe helyezést kell végrehajtania. Ez biztosítja, hogy a Storage-fiók a legmegfelelőbb állapotban legyen (a létrehozott tárolók és az azokhoz feltöltött adattárolók).

## <a name="managing-access"></a>Hozzáférés-kezelés

A tároló gyökerén is beállíthat engedélyeket. Ehhez be kell jelentkeznie Azure Storage Explorerba az egyéni fiókjával (a kapcsolódási karakterlánccal ellentétben). Kattintson a jobb gombbal a tárolóra, és válassza az **engedélyek kezelése**lehetőséget, és hozza létre az **engedély kezelése** párbeszédpanelt.

![Microsoft Azure Storage Explorer – címtár-hozzáférés kezelése](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

Az **engedély kezelése** párbeszédpanel lehetővé teszi a tulajdonos és a tulajdonosok csoport engedélyeinek kezelését. Azt is lehetővé teszi, hogy új felhasználókat és csoportokat vegyen fel a hozzáférés-vezérlési listára, ahol az engedélyeket kezelheti.

Ha új felhasználót vagy csoportot szeretne hozzáadni a hozzáférés-vezérlési listához, válassza a **felhasználó vagy csoport hozzáadása** mezőt.

Adja meg a listához hozzáadni kívánt Azure Active Directory-(HRE-) bejegyzést, majd válassza a **Hozzáadás**lehetőséget.

A felhasználó vagy csoport ekkor megjelenik a **felhasználók és csoportok:** mezőben, így megkezdheti az engedélyeik kezelését.

> [!NOTE]
> Ajánlott eljárás, és javasoljuk, hogy hozzon létre egy biztonsági csoportot a HRE-ben, és tartsa meg az engedélyeket a csoporton az egyes felhasználók helyett. A javaslat részletes ismertetését, valamint az egyéb ajánlott eljárásokat lásd: [ajánlott eljárások Data Lake Storage Gen2hoz](data-lake-storage-best-practices.md).

Az engedélyek két kategóriája adható meg: hozzáférés-vezérlési listák és alapértelmezett ACL-ek.

* **Hozzáférés**: hozzáférés-vezérlési listák vezérlik az objektumokhoz való hozzáférést. A fájlok és könyvtárak egyaránt rendelkeznek hozzáférési ACL-ekkel.

* **Alapértelmezett**: a címtárhoz társított ACL-ek sablonja, amely meghatározza az adott címtárban létrehozott alárendelt elemek hozzáférési ACL-jeit. A fájlok nem rendelkeznek alapértelmezett ACL-ekkel.

Mindkét kategórián belül három engedély van a fájlok vagy könyvtárak hozzárendeléséhez: **olvasás**, **írás**és **végrehajtás**.

>[!NOTE]
> Az itt megadott beállítások megadásakor a rendszer nem állítja be az engedélyeket a címtárban lévő aktuális elemekhez. Ha a fájl már létezik, minden egyes elemhez meg kell adnia az engedélyeket manuálisan.

Az egyes címtárakra, valamint az egyes fájlokra vonatkozó engedélyeket is kezelheti, ami lehetővé teszi a részletes hozzáférés-vezérlést. A könyvtárak és fájlok engedélyeinek kezelésére szolgáló folyamat ugyanaz, mint a fentiekben leírtak szerint. Kattintson a jobb gombbal arra a fájlra vagy könyvtárra, amelyre az engedélyeket szeretné kezelni, és kövesse ugyanezt a folyamatot.

## <a name="next-steps"></a>Következő lépések

Ebben a útmutatóban megtanulta, hogyan állíthatja be a fájlokra és könyvtárakra vonatkozó engedélyeket **Azure Storage Explorer**használatával. Az ACL-ekkel, például az alapértelmezett ACL-ekkel, a hozzáférési ACL-ekkel, azok működésével és a hozzájuk tartozó engedélyekkel kapcsolatos további információkért tekintse meg a tárgyban található fogalmi cikket is.

> [!div class="nextstepaction"]
> [Hozzáférés-vezérlés a 2. generációs Azure Data Lake Storage-ben](data-lake-storage-access-control.md)
