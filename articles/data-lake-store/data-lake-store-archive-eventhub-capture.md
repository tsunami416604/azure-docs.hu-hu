---
title: Event hubs szolgáltatástól érkező adatok rögzítése Azure Data Lake Store-bA |} A Microsoft Docs
description: Használja az Azure Data Lake Store Eseményközpontokból származó adatok
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: bda52acc12aad3cad20143c319f557f11d760c42
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435152"
---
# <a name="use-azure-data-lake-store-to-capture-data-from-event-hubs"></a>Használja az Azure Data Lake Store Eseményközpontokból származó adatok

Ismerje meg, hogyan használható az Azure Data Lake Store az Azure Event Hubs által fogadott adatok.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Egy Azure Data Lake Store-fiókot**. A fióklétrehozás módjával kapcsolatban tekintse meg [az Azure Data Lake Store használatának első lépéseit ismertető](data-lake-store-get-started-portal.md) témakört.

*  **Event Hubs-névtér**. Útmutatásért lásd: [Event Hubs-névtér létrehozása](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Győződjön meg arról, hogy a Data Lake Store-fiók és az Event Hubs-névtér az Azure-előfizetéshez.


## <a name="assign-permissions-to-event-hubs"></a>Engedélyek hozzárendelése az Event hubs szolgáltatásba

Ebben a szakaszban hozzon létre egy mappát a fiókon belül, ahol szeretné rögzíteni az adatokat az Event hubs Eseményközpontokból. Is hozzárendelhet engedélyeket az Event hubs szolgáltatásba, hogy azt is írja az adatokat, egy Data Lake Store-fiókot. 

1. Nyissa meg a Data Lake Store-fiókot, ahol szeretné rögzíteni az Eseményközpontokból származó adatokat, majd kattintson a **adatkezelő**.

    ![Data Lake Store adatkezelő](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Store – adatkezelő")

1.  Kattintson a **új mappa** , és írjon be egy nevet a mappára, ahol az adatok rögzítéséhez.

    ![Hozzon létre egy új mappát a Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "hozzon létre egy új mappát a Data Lake Store")

1. Rendelje hozzá a Data Lake Store gyökerében engedélyeit. 

    a. Kattintson a **adatkezelő**, válassza ki a Data Lake Store-fiók gyökérkönyvtárában, és kattintson **hozzáférés**.

    ![A Data Lake Store legfelső szintű engedélyek hozzárendelése](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "a Data Lake Store legfelső szintű engedélyek hozzárendelése")

    b. Alatt **hozzáférés**, kattintson a **Hozzáadás**, kattintson a **felhasználó vagy csoport kiválasztása**, és keressen `Microsoft.EventHubs`. 

    ![A Data Lake Store legfelső szintű engedélyek hozzárendelése](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "a Data Lake Store legfelső szintű engedélyek hozzárendelése")
    
    Kattintson a **Kiválasztás** gombra.

    c. A **engedélyek hozzárendelése**, kattintson a **engedélyek kiválasztása**. Állítsa be **engedélyek** való **végrehajtása**. Állítsa be **hozzá** való **ezt a mappát, és az összes gyermekre**. Állítsa be **hozzáadása** való **hozzáférési engedély bejegyzés és alapértelmezett engedély bejegyzés**.

    > [!IMPORTANT]
    > Az Azure Event Hubs által fogadott adatok rögzítésére egy új mappahierarchiában létrehozásakor ez egyszerű módja elérhetőségét a rendeltetési mappára.  Azonban hozzáadása engedélyek összes gyermekre a legfelső szintű mappa számos gyermek fájlok és mappák is igénybe vehet egy hosszú ideig.  Ha a gyökérmappában található fájlok és mappák nagy számú tartalmaz, valószínűleg gyorsabb hozzáadása **Execute** engedélyeinek `Microsoft.EventHubs` külön-külön az egyes mappák elérési útját a végső rendeltetési mappára. 

    ![A Data Lake Store legfelső szintű engedélyek hozzárendelése](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "a Data Lake Store legfelső szintű engedélyek hozzárendelése")

    Kattintson az **OK** gombra.

1. Rendelje hozzá a Data Lake Store-fiókban hol kívánja rögzíteni a adatok mappára vonatkozó engedélyeket.

    a. Kattintson a **adatkezelő**, válassza ki a mappát a Data Lake Store-fiókban, és kattintson **hozzáférés**.

    ![Rendelje hozzá a Data Lake Store-mappa engedélyeinek](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "rendelje hozzá a Data Lake Store-mappa engedélyei")

    b. Alatt **hozzáférés**, kattintson a **Hozzáadás**, kattintson a **felhasználó vagy csoport kiválasztása**, és keressen `Microsoft.EventHubs`. 

    ![Rendelje hozzá a Data Lake Store-mappa engedélyeinek](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "rendelje hozzá a Data Lake Store-mappa engedélyei")
    
    Kattintson a **Kiválasztás** gombra.

    c. A **engedélyek hozzárendelése**, kattintson a **engedélyek kiválasztása**. Állítsa be **engedélyek** való **Olvasás, írás,** és **végrehajtása**. Állítsa be **hozzá** való **ezt a mappát, és az összes gyermekre**. Végezetül állítsa **hozzáadása** való **hozzáférési engedély bejegyzés és alapértelmezett engedély bejegyzés**.

    ![Rendelje hozzá a Data Lake Store-mappa engedélyeinek](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "rendelje hozzá a Data Lake Store-mappa engedélyei")
    
    Kattintson az **OK** gombra. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-store"></a>Az Event Hubs-adatok, Data Lake Store konfigurálása

Ebben a szakaszban lévő Event Hubs-névtér Event Hub létrehozása. Is konfigurálhatja az Event Hubs, az adatok egy Azure Data Lake Store-fiókhoz. Ez a szakasz azt feltételezi, hogy már létrehozott Event Hubs-névtér.

1. Az a **áttekintése** az Event Hubs-névtér ablakában kattintson **+ Event Hub**.

    ![Eseményközpont létrehozása](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Eseményközpont létrehozása")

1. Adja meg a következő adatok, Data Lake Store az Event Hubs konfigurálása.

    ![Eseményközpont létrehozása](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Eseményközpont létrehozása")

    a. Adjon meg egy nevet az Eseményközpontnak.
    
    b. A jelen oktatóanyag esetében állítsa be **partíciószám** és **Üzenetmegőrzés** az alapértelmezett értékükre.
    
    c. Állítsa be **rögzítése** való **a**. Állítsa be a **időtartomány** (hogy milyen gyakran rögzítése) és **Méretablak** (adatméret rögzítése). 
    
    d. A **Capture-szolgáltató**válassza **Azure Data Lake Store** , és válassza a Data Lake Store a korábban létrehozott. A **Data Lake-útvonal**, adja meg a mappát a Data Lake Store-fiókban létrehozott nevét. Csak meg kell adnia azt a mappát a relatív elérési út.

    e. Hagyja a **minta rögzítési fájlformátumok neve** az alapértelmezett értékre. Ez a beállítás szabályozza a mappastruktúra a rögzítési mappa alatt létrehozott.

    f. Kattintson a **Create** (Létrehozás) gombra.

## <a name="test-the-setup"></a>A beállítások ellenőrzése

A megoldás az Azure Event Hubs el most tesztelheti. Kövesse az utasításokat, [események küldése az Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Az adatküldés elindítását követően láthatja az adatok megjelennek a Data Lake Store a gyökérmappa-szerkezetében megadott használatával. Például láthatja a mappastruktúrát, a Data Lake Store az alábbi képernyőképen látható módon.

![Az EventHub-adatok a Data Lake Store minta](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "minta az EventHub-adatok a Data Lake Store")

> [!NOTE]
> Akkor is, ha nem rendelkezik az Event Hubs érkező üzeneteket, az Event Hubs írja az üres fájlok csak a fejlécek a Data Lake Store-fiókot. A fájlokat, hogy az Event Hubs létrehozásakor megadott azonos időközönként készültek.
> 
>

## <a name="analyze-data-in-data-lake-store"></a>Data Lake Store az adatok elemzése

Amint az adatok Data Lake Store, elemzési feladatok futtatásához folyamat és elemzése a hatékonyabb játékelemzésért az adatokat. Lásd: [USQL Avro példa](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) az ehhez az Azure Data Lake Analytics használatával.
  

## <a name="see-also"></a>Lásd még
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)
* [Adatok másolása az Azure Storage-Blobokból a Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
