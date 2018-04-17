---
title: Az Azure Data Lake Store Eseményközpontokból származó adatok rögzítéséhez |} Microsoft Docs
description: Használata Azure Data Lake Store Eseményközpontokból származó adatok rögzítéséhez
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 9f91acf8c26fdec0c8d128f598f218cff091c7aa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-azure-data-lake-store-to-capture-data-from-event-hubs"></a>Használata Azure Data Lake Store Eseményközpontokból származó adatok rögzítéséhez

Ismerje meg, hogyan használható az Azure Data Lake Store az Azure Event Hubs által fogadott adatok rögzítéséhez.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Egy Azure Data Lake Store-fiók**. A fióklétrehozás módjával kapcsolatban tekintse meg [az Azure Data Lake Store használatának első lépéseit ismertető](data-lake-store-get-started-portal.md) témakört.

*  **Az Event Hubs névtér**. Útmutatásért lásd: [az Event Hubs-névtér létrehozása](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Gondoskodjon arról, hogy a Data Lake Store-fiókot és az Event Hubs névtér azonos Azure-előfizetést.


## <a name="assign-permissions-to-event-hubs"></a>Az Event Hubs engedélyek hozzárendelése

Ebben a szakaszban a fiókon belül hol kívánja rögzíteni az adatokat az Event Hubs mappát hoz létre. Akkor is engedélyek hozzárendelése a Event Hubs így adatokat írhat be egy Data Lake Store-fiókba. 

1. Ha szeretné rögzíteni Eseményközpontokból származó adatokat, majd kattintson a Data Lake Store-fiók megnyitása **adatkezelő**.

    ![Data Lake Store adatkezelő](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Store adatkezelő")

2.  Kattintson a **új mappa** és írjon be egy nevet a mappára, ahol az adatok összegyűjtéséhez.

    ![Hozzon létre egy új mappát a Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "hozzon létre egy új mappát a Data Lake Store-ban")

3. A Data Lake Store gyökerében engedélyeket rendelhet. 

    a. Kattintson a **adatkezelő**, válassza ki a Data Lake Store-fiók gyökérkönyvtárában, és kattintson a **hozzáférés**.

    ![Engedélyeket rendelhet a Data Lake Store legfelső szintű](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "engedélyeket rendelhet a Data Lake Store gyökér")

    b. A **hozzáférés**, kattintson a **Hozzáadás**, kattintson a **felhasználó vagy csoport kiválasztása**, majd keresse meg a `Microsoft.EventHubs`. 

    ![Engedélyeket rendelhet a Data Lake Store legfelső szintű](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "engedélyeket rendelhet a Data Lake Store gyökér")
    
    Kattintson a **Kiválasztás** gombra.

    c. A **engedélyek hozzárendelése**, kattintson a **Select engedélyeket**. Állítsa be **engedélyek** való **hajtható végre**. Állítsa be **hozzáadása** való **ezt a mappát, és minden gyermeknek**. Állítsa be **hozzáadni** való **egy hozzáférési engedélybejegyzés és egy alapértelmezett engedélybejegyzés**.

> [!IMPORTANT]
> Az Azure Event Hubs által fogadott adatok rögzítéséhez egy új mappahierarchiában létrehozásakor ez egyszerű módja a célmappa való hozzáférés biztosításához.  Azonban a legfelső szintű mappa minden gyermeknek sok gyermek fájlokhoz és mappákhoz engedélyek hozzáadása a sok időre lehet szükség.  Ha a gyökérmappában található fájlok és mappák sok tartalmaz, akkor lehet gyorsabb hozzáadása **Execute** engedélyeinek `Microsoft.EventHubs` külön-külön történő minden mappa elérési útját a végső rendeltetési mappára. 

    ![Assign permissions for Data Lake Store root](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Assign permissions for Data Lake Store root")

    Click **OK**.

4. Rendelje hozzá a Data Lake Store-fiókjában hol kívánja rögzíteni adatok mappára vonatkozó engedélyeket.

    a. Kattintson a **adatkezelő**, válassza ki a mappát a Data Lake Store-fiókban, és kattintson a **hozzáférés**.

    ![Engedélyeket rendelhet a Data Lake Store-mappa](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "engedélyeket rendelhet a Data Lake Store-mappa")

    b. A **hozzáférés**, kattintson a **Hozzáadás**, kattintson a **felhasználó vagy csoport kiválasztása**, majd keresse meg a `Microsoft.EventHubs`. 

    ![Engedélyeket rendelhet a Data Lake Store-mappa](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "engedélyeket rendelhet a Data Lake Store-mappa")
    
    Kattintson a **Kiválasztás** gombra.

    c. A **engedélyek hozzárendelése**, kattintson a **Select engedélyeket**. Állítsa be **engedélyek** való **Olvasás, írás,** és **hajtható végre**. Állítsa be **hozzáadása** való **ezt a mappát, és minden gyermeknek**. Végezetül be **hozzáadni** való **egy hozzáférési engedélybejegyzés és egy alapértelmezett engedélybejegyzés**.

    ![Engedélyeket rendelhet a Data Lake Store-mappa](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "engedélyeket rendelhet a Data Lake Store-mappa")
    
    Kattintson az **OK** gombra. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-store"></a>Az Event Hubs Data Lake Store-adatok rögzítéséhez konfigurálása

Ebben a szakaszban egy Eseményközpontot, az Event Hubs névtéren belül hoz létre. Konfigurálnia az Event Hubs egy Azure Data Lake Store-fiókhoz adatok rögzítéséhez. Ez a szakasz azt feltételezi, hogy már létrehozta az Event Hubs névtér.

2. Az a **áttekintése** az Event Hubs névtér ablaktáblán kattintson a **+ Eseményközpont**.

    ![Eseményközpont létrehozása](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Eseményközpont létrehozása")

3. Adja meg a következő konfigurálása az Event Hubs Data Lake Store-adatok rögzítéséhez.

    ![Eseményközpont létrehozása](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Eseményközpont létrehozása")

    a. Adja meg az Eseményközpont nevét.
    
    b. Ebben az oktatóanyagban beállítása **Partíciószámnak** és **üzenet megőrzési** az alapértelmezett értékre.
    
    c. Állítsa be **rögzítése** való **a**. Állítsa be a **időkerete** (hogy milyen gyakran rögzítése) és **méret ablakban** (adatméret rögzítéséhez). 
    
    d. A **rögzítése szolgáltató**, jelölje be **Azure Data Lake Store** , és válassza ki a Data Lake Store a korábban létrehozott. A **elérési utat a Data Lake**, adja meg a Data Lake Store-fiók létrehozott mappa nevét. Csak meg kell adnia a relatív elérési utat a mappához.

    e. Hagyja a **minta rögzítési neve formátumok** az alapértelmezett értékre. Ez a beállítás szabályozza a mappaszerkezetet, amely a rögzítési mappában jön létre.

    f. Kattintson a **Create** (Létrehozás) gombra.

## <a name="test-the-setup"></a>A beállítások ellenőrzése

A megoldás adatokat küld az Azure Event Hubs most tesztelheti. Kövesse az utasításokat, [események küldése az Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Az adatküldés elindítását követően az adatokat látja megjelennek a Data Lake Store megadott mappaszerkezet használatával. Például láthatja a mappastruktúrát, ahogy az az alábbi képernyőfelvételen a Data Lake Store-ban.

![Az EventHub-adatokat a Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "minta az EventHub-adatok Data Lake Store-ban")

> [!NOTE]
> Akkor is, ha még nem rendelkezik az Event Hubsban érkező üzenetek, a Event Hubs fejlécét az üres fájlok írja be a Data Lake Store-fiókba. A fájlokat, hogy az Event Hubs létrehozásakor megadott azonos időközönként készültek.
> 
>

## <a name="analyze-data-in-data-lake-store"></a>A Data Lake Store adatok elemzése

Amint az adatok a Data Lake Store, futtathatja analitikai feladatok folyamat és crunch az adatokat. Lásd: [USQL Avro példa](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) ehhez használja az Azure Data Lake Analytics módjáról.
  

## <a name="see-also"></a>Lásd még
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)
* [Adatok másolása az Azure Storage Blobs Data Lake Store-bA](data-lake-store-copy-data-azure-storage-blob.md)
