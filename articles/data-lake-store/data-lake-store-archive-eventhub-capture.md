---
title: Adatok rögzítése az Event Hubs-ból az Azure Data Lake Storage Gen1-be | Microsoft dokumentumok
description: Az Azure Data Lake Storage Gen1 használatával adatokat rögzíthet az Event Hubs-ból
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bb67c1769510710b368bef4dc0b501f939b3427e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265661"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Az Azure Data Lake Storage Gen1 használatával adatokat rögzíthet az Event Hubs-ból

Megtudhatja, hogy az Azure Data Lake Storage Gen1 használatával hogyan rögzítheti az Azure Event Hubs által fogadott adatokat.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Egy Azure Data Lake Storage Gen1 fiók.** Az Azure Data Lake Storage Gen1 – [Első lépések az Azure Data Lake Storage Gen1 című](data-lake-store-get-started-portal.md)témakörben található.

*  **Eseményközpontok névtere**. További információt az [Eseményközpontok névtere létrehozása című témakörben talál.](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace) Győződjön meg arról, hogy a Data Lake Storage Gen1 fiók és az Event Hubs névtér ugyanabban az Azure-előfizetésben van.


## <a name="assign-permissions-to-event-hubs"></a>Engedélyek hozzárendelése az Eseményközpontokhoz

Ebben a szakaszban hozzon létre egy mappát a fiókban, ahol rögzíteni szeretné az adatokat az Event Hubs.In this section, you create a folder within the account where you want to capture the data from Event Hubs. Az Event Hubs-hoz is hozzárendelheti az engedélyeket, hogy adatokat írhass on a Data Lake Storage Gen1 fiókba. 

1. Nyissa meg a Data Lake Storage Gen1 fiókot, ahol adatokat szeretne rögzíteni az Event Hubs-ból, majd kattintson az **Adatkezelő**re.

    ![Data Lake Storage Gen1 adatkezelő](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Storage Gen1 adatkezelő")

1.  Kattintson **az Új mappa elemre,** majd adja meg annak a mappának a nevét, amelybe rögzíteni szeretné az adatokat.

    ![Új mappa létrehozása a Data Lake Storage Gen1 szolgáltatásban](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Új mappa létrehozása a Data Lake Storage Gen1 szolgáltatásban")

1. Engedélyek hozzárendelése a Data Lake Storage Gen1 gyökerében. 

    a. Kattintson az **Adatkezelő**gombra, jelölje ki a Data Lake Storage Gen1 fiók gyökerét, majd kattintson az **Access**gombra.

    ![Engedélyek hozzárendelése a Data Lake Storage Gen1 gyökéréhez](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Engedélyek hozzárendelése a Data Lake Storage Gen1 gyökéréhez")

    b. Az **Access**csoportban kattintson a **Hozzáadás**gombra, kattintson **a Felhasználó vagy csoport kijelölése**gombra, majd keresse meg a elemet. `Microsoft.EventHubs` 

    ![Engedélyek hozzárendelése a Data Lake Storage Gen1 gyökéréhez](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Engedélyek hozzárendelése a Data Lake Storage Gen1 gyökéréhez")
    
    Kattintson a **Kiválasztás** gombra.

    c. Az **Engedélyek hozzárendelése**csoportban kattintson az **Engedélyek kiválasztása gombra.** A **végrehajtáshoz** **szükséges engedélyek** beállítása. Állítsa **be a Hozzáadás ehhez** a **mappához és az összes gyermekhez parancsot.** Adja meg a **Hozzáadás t** **hozzáférési engedélybejegyzéshez és egy alapértelmezett engedélybejegyzést.**

    > [!IMPORTANT]
    > Amikor új mappahierarchiát hoz létre az Azure Event Hubs által fogadott adatok rögzítéséhez, ez egy egyszerű módja annak, hogy biztosítsa a célmappához való hozzáférést.  A sok gyermekfájlt és mappát tartalmazó legfelső szintű mappa összes gyermekéhez azonban hosszú időt vehet igénybe az engedélyek hozzáadása.  Ha a gyökérmappa nagy számú fájlt és mappát tartalmaz, gyorsabb `Microsoft.EventHubs` lehet **a végrehajtási** engedélyek hozzáadása a végső célmappa elérési útjának minden mappájához. 

    ![Engedélyek hozzárendelése a Data Lake Storage Gen1 gyökéréhez](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Engedélyek hozzárendelése a Data Lake Storage Gen1 gyökéréhez")

    Kattintson az **OK** gombra.

1. Rendeljen engedélyeket a mappához a Data Lake Storage Gen1 fiókban, ahol adatokat szeretne rögzíteni.

    a. Kattintson az **Adatkezelő**gombra, jelölje ki a mappát a Data Lake Storage Gen1 fiókban, majd kattintson az **Access**gombra.

    ![Engedélyek hozzárendelése a Data Lake Storage Gen1 mappához](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Engedélyek hozzárendelése a Data Lake Storage Gen1 mappához")

    b. Az **Access**csoportban kattintson a **Hozzáadás**gombra, kattintson **a Felhasználó vagy csoport kijelölése**gombra, majd keresse meg a elemet. `Microsoft.EventHubs` 

    ![Engedélyek hozzárendelése a Data Lake Storage Gen1 mappához](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Engedélyek hozzárendelése a Data Lake Storage Gen1 mappához")
    
    Kattintson a **Kiválasztás** gombra.

    c. Az **Engedélyek hozzárendelése**csoportban kattintson az **Engedélyek kiválasztása gombra.** Az **engedélyek** beállítása **olvasásra, írásra** és **végrehajtásra.** Állítsa **be a Hozzáadás ehhez** a **mappához és az összes gyermekhez parancsot.** Végül állítsa be a **Hozzáadás másként** **jogosultsági bejegyzést és az alapértelmezett engedélybejegyzést.**

    ![Engedélyek hozzárendelése a Data Lake Storage Gen1 mappához](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Engedélyek hozzárendelése a Data Lake Storage Gen1 mappához")
    
    Kattintson az **OK** gombra. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Az Eseményközpontok konfigurálása adatok rögzítésére a Data Lake Storage Gen1 szolgáltatásba

Ebben a szakaszban egy Eseményközpontot hoz létre egy Eseményközpont névterén belül. Az Event Hubot is konfigurálhatja az Azure Data Lake Storage Gen1-fiókadatainak rögzítésére. Ez a szakasz feltételezi, hogy már létrehozott egy Event Hubs névteret.

1. Az Eseményközpontok névtér **Áttekintő** ablaktábláján kattintson a **+ Eseményközpont gombra.**

    ![Eseményközpont létrehozása](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Eseményközpont létrehozása")

1. Adja meg a következő értékeket az Event Hubs konfigurálásához az adatok data to Data Lake Storage Gen1 számára történő rögzítéséhez.

    ![Eseményközpont létrehozása](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Eseményközpont létrehozása")

    a. Adja meg az Eseményközpont nevét.
    
    b. Ebben az oktatóanyagban állítsa a **partíciók számát** és az **üzenetmegőrzést** az alapértelmezett értékekre.
    
    c. A **Rögzítés** beállítása **Be**beállításra. Állítsa be az **Időablakot** (milyen gyakran a rögzítéshez) és **a Méret ablakot** (az adatok rögzítéséhez az adatméretet). 
    
    d. A **Rögzítésszolgáltató**ban válassza az **Azure Data Lake Store** lehetőséget, majd válassza ki a korábban létrehozott Data Lake Storage Gen1 fiókot. A **Data Lake Path (Data Lake Path)** mezőbe írja be a létrehozott mappa nevét a Data Lake Storage Gen1 fiókban. Csak a mappa relatív elérési útját kell megadnia.

    e. Hagyja a **Mintarögzítési fájlnévformátumokat** az alapértelmezett értékre. Ez a beállítás szabályozza a rögzítési mappa alatt létrehozott mappastruktúrát.

    f. Kattintson **a Létrehozás gombra.**

## <a name="test-the-setup"></a>A beállítás tesztelése

Most már tesztelheti a megoldást az Azure Event Hubra küldött adatokkal. Kövesse az Események küldése az [Azure Event Hubs-ba című útmutatót.](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md) Miután elkezdi az adatok küldését, a Data Lake Storage Gen1-ben a megadott mappastruktúra használatával megjelennek az adatok. Például megjelenik egy mappastruktúra, ahogy az a következő képernyőképen is látható, a Data Lake Storage Gen1 fiókjában.

![Minta EventHub-adatok a Data Lake Storage Gen1 szolgáltatásban](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Minta EventHub-adatok a Data Lake Storage Gen1 szolgáltatásban")

> [!NOTE]
> Még akkor is, ha nem érkeznek üzenetek az Event Hubs-ba, az Event Hubs üres fájlokat ír a Data Lake Storage Gen1 fiókba, csak a fejlécekkel. A fájlok az Event Hubs létrehozásakor megadott időintervallumban vannak megírva.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Adatok elemzése a Data Lake Storage Gen1 szolgáltatásban

Miután az adatok a Data Lake Storage Gen1, futtathatja az analitikus feladatok feldolgozásához és összeroppant az adatok. Tekintse meg [az USQL Avro példa,](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) hogyan kell ezt az Azure Data Lake Analytics használatával.
  

## <a name="see-also"></a>Lásd még
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Adatok másolása az Azure Storage Blobs szolgáltatásból a Data Lake Storage Gen1 szolgáltatásba](data-lake-store-copy-data-azure-storage-blob.md)
