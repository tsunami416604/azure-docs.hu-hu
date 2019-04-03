---
title: Az Azure Data Lake Storage Gen1 Eseményközpontokból származó adatok rögzítése |} A Microsoft Docs
description: Használja az Azure Data Lake Storage Gen1 Eseményközpontokból származó adatok
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
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884506"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Használja az Azure Data Lake Storage Gen1 Eseményközpontokból származó adatok

Ismerje meg, hogyan használható az Azure Data Lake Storage Gen1 Azure Event Hubs által fogadott adatok.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Az Azure Data Lake Storage Gen1 fiók**. Létrehozásával kapcsolatos utasításokért lásd: [Ismerkedés az Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

*  **Event Hubs-névtér**. Útmutatásért lásd: [Event Hubs-névtér létrehozása](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Győződjön meg arról, hogy a Data Lake Storage Gen1 fiók és az Event Hubs-névtér az Azure-előfizetéshez.


## <a name="assign-permissions-to-event-hubs"></a>Engedélyek hozzárendelése az Event hubs szolgáltatásba

Ebben a szakaszban hozzon létre egy mappát a fiókon belül, ahol szeretné rögzíteni az adatokat az Event hubs Eseményközpontokból. Emellett engedélyeket ad az Event Hubs, hogy azt is írja az adatokat, egy Data Lake Storage Gen1 fiókot. 

1. Nyissa meg a Data Lake Storage Gen1 fiókot, ahol szeretné rögzíteni az Eseményközpontokból származó adatokat, majd kattintson a **adatkezelő**.

    ![Data Lake Storage Gen1 adatkezelő](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Storage Gen1 adatkezelő")

1.  Kattintson a **új mappa** , és írjon be egy nevet a mappára, ahol az adatok rögzítéséhez.

    ![Hozzon létre egy új mappát a Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "hozzon létre egy új mappát a Data Lake Storage Gen1")

1. A Data Lake Storage Gen1 gyökerénél engedélyek hozzárendelése. 

    a. Kattintson a **adatkezelő**, válassza ki a Data Lake Storage Gen1 fiók gyökérkönyvtárában, és kattintson **hozzáférés**.

    ![A Data Lake Storage Gen1 legfelső szintű engedélyeket](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "a Data Lake Storage Gen1 legfelső szintű engedélyek hozzárendelése")

    b. Alatt **hozzáférés**, kattintson a **Hozzáadás**, kattintson a **felhasználó vagy csoport kiválasztása**, és keressen `Microsoft.EventHubs`. 

    ![A Data Lake Storage Gen1 legfelső szintű engedélyeket](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "a Data Lake Storage Gen1 legfelső szintű engedélyek hozzárendelése")
    
    Kattintson a **Kiválasztás** gombra.

    c. A **engedélyek hozzárendelése**, kattintson a **engedélyek kiválasztása**. Állítsa be **engedélyek** való **végrehajtása**. Állítsa be **hozzá** való **ezt a mappát, és az összes gyermekre**. Állítsa be **hozzáadása** való **hozzáférési engedély bejegyzés és alapértelmezett engedély bejegyzés**.

    > [!IMPORTANT]
    > Amikor egy új mappát hierarchia létrehozása az Azure Event Hubs által fogadott adatok rögzítésére, ez egyszerű módja elérhetőségét a rendeltetési mappára.  Azonban hozzáadása engedélyek összes gyermekre a legfelső szintű mappa számos gyermek fájlok és mappák is igénybe vehet egy hosszú ideig.  Ha a gyökérmappában található fájlok és mappák nagy számú tartalmaz, valószínűleg gyorsabb hozzáadása **Execute** engedélyeinek `Microsoft.EventHubs` külön-külön az egyes mappák elérési útját a végső rendeltetési mappára. 

    ![A Data Lake Storage Gen1 legfelső szintű engedélyeket](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "a Data Lake Storage Gen1 legfelső szintű engedélyek hozzárendelése")

    Kattintson az **OK** gombra.

1. Rendelje hozzá kívánja rögzíteni az adatokat a Data Lake Storage Gen1 fiókban mappára vonatkozó engedélyeket.

    a. Kattintson a **adatkezelő**, válassza ki a mappát a Data Lake Storage Gen1 fiókban, és kattintson **hozzáférés**.

    ![A Data Lake Storage Gen1 mappa engedélyeinek hozzárendeléséhez](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "rendelje hozzá a Data Lake Storage Gen1 mappára vonatkozó engedélyeket")

    b. Alatt **hozzáférés**, kattintson a **Hozzáadás**, kattintson a **felhasználó vagy csoport kiválasztása**, és keressen `Microsoft.EventHubs`. 

    ![A Data Lake Storage Gen1 mappa engedélyeinek hozzárendeléséhez](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "rendelje hozzá a Data Lake Storage Gen1 mappára vonatkozó engedélyeket")
    
    Kattintson a **Kiválasztás** gombra.

    c. A **engedélyek hozzárendelése**, kattintson a **engedélyek kiválasztása**. Állítsa be **engedélyek** való **Olvasás, írás,** és **végrehajtása**. Állítsa be **hozzá** való **ezt a mappát, és az összes gyermekre**. Végezetül állítsa **hozzáadása** való **hozzáférési engedély bejegyzés és alapértelmezett engedély bejegyzés**.

    ![A Data Lake Storage Gen1 mappa engedélyeinek hozzárendeléséhez](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "rendelje hozzá a Data Lake Storage Gen1 mappára vonatkozó engedélyeket")
    
    Kattintson az **OK** gombra. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Az Event Hubs-adatok, Data Lake Storage Gen1 konfigurálása

Ebben a szakaszban lévő Event Hubs-névtér Event Hub létrehozása. Emellett konfigurálnia az Event Hubs, az adatok rögzítése egy Azure Data Lake Storage Gen1 fiókba. Ez a szakasz azt feltételezi, hogy már létrehozott Event Hubs-névtér.

1. Az a **áttekintése** az Event Hubs-névtér ablakában kattintson **+ Event Hub**.

    ![Eseményközpont létrehozása](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Eseményközpont létrehozása")

1. Adja meg a következő adatokat a Data Lake Storage Gen1 rögzítheti az Event Hubs konfigurálása.

    ![Eseményközpont létrehozása](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Eseményközpont létrehozása")

    a. Adjon meg egy nevet az Eseményközpontnak.
    
    b. A jelen oktatóanyag esetében állítsa be **partíciószám** és **Üzenetmegőrzés** az alapértelmezett értékükre.
    
    c. Állítsa be **rögzítése** való **a**. Állítsa be a **időtartomány** (hogy milyen gyakran rögzítése) és **Méretablak** (adatméret rögzítése). 
    
    d. A **Capture-szolgáltató**válassza **Azure Data Lake Store** , és válassza ki azt a korábban létrehozott Data Lake Storage Gen1 fiókot. A **Data Lake-útvonal**, adja meg a mappát a Data Lake Storage Gen1 fiókban létrehozott nevét. Csak meg kell adnia azt a mappát a relatív elérési út.

    e. Hagyja a **minta rögzítési fájlformátumok neve** az alapértelmezett értékre. Ez a beállítás szabályozza a mappastruktúra a rögzítési mappa alatt létrehozott.

    f. Kattintson a **Create** (Létrehozás) gombra.

## <a name="test-the-setup"></a>A beállítások ellenőrzése

A megoldás az Azure Event Hubs el most tesztelheti. Kövesse az utasításokat, [események küldése az Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Az adatküldés elindítását követően látja-e az adatok Data Lake Storage Gen1 megjelennek a mappa használata struktúra megadott. Például lásd a mappastruktúrát, ahogyan az alábbi képernyőképen a Data Lake Storage Gen1-fiókban.

![Az EventHub-adatok a Data Lake Storage Gen1 minta](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "minta az EventHub-adatok a Data Lake Storage Gen1")

> [!NOTE]
> Akkor is, ha nem rendelkezik az Event Hubs érkező üzeneteket, az Event Hubs írja az üres fájlok csak a fejlécek a Data Lake Storage Gen1 fiókot. A fájlokat, hogy az Event Hubs létrehozásakor megadott azonos időközönként készültek.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>A Data Lake Storage Gen1 adatok elemzése

Amint az adatok Data Lake Storage Gen1, elemzési feladatok futtatásához folyamat és elemzése a hatékonyabb játékelemzésért az adatokat. Lásd: [USQL Avro példa](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) az ehhez az Azure Data Lake Analytics használatával.
  

## <a name="see-also"></a>Lásd még
* [Biztonságos adattárolás a Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Adatok másolása az Azure Storage-Blobokból a Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
