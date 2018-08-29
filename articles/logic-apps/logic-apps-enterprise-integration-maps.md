---
title: XML átalakítása az XSLT-leképezések – Azure Logic Apps |} A Microsoft Docs
description: Az Azure Logic Apps Enterprise Integration Pack az XML-átalakítás XSLT-leképezések hozzáadása
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.date: 07/08/2016
ms.openlocfilehash: c5e5e0a0a3f8bd5feedc00d5bbfb76a1453ccc84
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123556"
---
# <a name="add-maps-for-xml-transformation-in-azure-logic-apps-with-enterprise-integration-pack"></a>Az Azure Logic Apps Enterprise Integration Pack az XML-átalakítások leképezések hozzáadása

Vállalati integrációs XML-adatok formátumok közötti átalakítás maps használ. Térkép az XML-dokumentum, amely meghatározza az adatok egy dokumentum, amely egy másik formátumra kell lesz átalakítva. 

## <a name="why-use-maps"></a>Miért érdemes használni a maps?

Tegyük fel, hogy rendszeresen kap B2B rendeléseket és számlákat olyan ügyfelünk, akit a dátumok YYYMMDD formátumot használja. Azonban a szervezet fogja tárolni a MMDDYYY formátumú dátumok. Használhatja a térképet *átalakítása* be a sorrend, illetve számla részleteinek a felhasználói tevékenység adatbázisban való tárolás előtt MMDDYYY YYYMMDD dátumformátum.


## <a name="how-do-i-create-a-map"></a>Hogyan hozhatok létre egy térképet?

BizTalk-integráció a projektek hozhat létre a [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "további tudnivalók az enterprise integration pack") a Visual Studio 2015. Ezután létrehozhat egy integrációs térképe fájl, amellyel vizuálisan leképezése elemeket két XML-séma fájlok között. A projekt elkészítése, után az XSLT-dokumentum.

Ha a leképezés egy külső hivatkozást, majd is kell feltölteni az integrációs fiókba. Kell feltölteni a meghatározott sorrendben, először a szerelvényt és a térkép, amely a szerelvényre.


## <a name="how-do-i-add-a-map"></a>Hogyan adhatok hozzá egy térképet?

1. Az Azure Portalon válassza ki a **Tallózás**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. A szűrő be a keresőmezőbe írja be **integrációs**, majd **integrációs fiókok** az eredmények listájában.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Válassza ki az integrációs fiók, ahová a társítás hozzáadása.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Válassza ki a **Maps** csempére.

    ![](./media/logic-apps-enterprise-integration-maps/map-1.png)

5. Miután a Maps lap megnyitásakor, válassza ki **Hozzáadás**.

    ![](./media/logic-apps-enterprise-integration-maps/map-2.png)  

6. Adjon meg egy **neve** a térképen. A térkép fájl feltöltéséhez válassza ki a mappa ikonra a jobb oldalán a **térkép** szövegmezőben. A feltöltési folyamat befejezése után válassza ki a **OK**.

    ![](./media/logic-apps-enterprise-integration-maps/map-3.png)

7. Miután az Azure ad hozzá a térkép az integrációs fiók, egy megjelenő üzenet, amely bemutatja, hogy a térkép fájl lett hozzáadva, vagy nem kap. Miután ezt az üzenetet kap, válassza ki a **Maps** csempére, hogy meg tudja tekinteni az újonnan hozzáadott leképezés.

    ![](./media/logic-apps-enterprise-integration-maps/map-4.png)


## <a name="how-do-i-add-an-assembly"></a>Hogyan adhatok hozzá egy szerelvény?
Nyissa meg az integrációs fiók, ahol szeretné feltölteni a szerelvényt.

1. Válassza ki a **szerelvények** csempére.

    ![integrationaccount-szerelvény-csempe](./media/logic-apps-enterprise-integration-maps/assemblytile.png)

2. Miután a szerelvények lap megnyitásakor, válassza ki **Hozzáadás**. Adjon meg egy **neve** a szerelvény. A szerelvény fájl feltöltéséhez válassza ki a mappa ikonra a jobb oldalán a **szerelvény** szövegmezőben. A feltöltési folyamat befejezése után válassza ki a **OK**.

    ![szerelvény hozzáadása](./media/logic-apps-enterprise-integration-maps/assemblyfile.png)


## <a name="how-do-i-edit-a-map"></a>Hogyan tudom szerkeszteni egy térképet?

A kívánt módosításokat az új térkép fájlt fel kell tölteni. Először a térkép szerkesztésre töltheti le.

Töltse fel egy új térkép, amely lecseréli a meglévő térkép, kövesse az alábbi lépéseket.

1. Válassza ki a **Maps** csempére.

2. Után megnyílik a Maps-lapot, válassza ki a szerkeszteni kívánt leképezés.

3. Az a **Maps** lapon a **frissítés**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-1.png)

4. A Fájlkereső, válassza a térkép-fájl feltöltéséhez, majd válassza a kívánt **nyílt**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-2.png)

## <a name="how-to-delete-a-map"></a>Hogyan lehet törölni a térképet?

1. Válassza ki a **Maps** csempére.

2. Után megnyílik a Maps-lapot, válassza ki a törölni kívánt leképezés.

3. Válasszon **törlése**.

    ![](./media/logic-apps-enterprise-integration-maps/delete.png)

4. Győződjön meg arról, hogy szeretné-e a leképezés törlése.

    ![](./media/logic-apps-enterprise-integration-maps/delete-confirmation-1.png)

## <a name="next-steps"></a>További lépések
* [További információ az Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "megismerheti a vállalati integrációs csomag")  
* [További tudnivalók a szerződések](../logic-apps/logic-apps-enterprise-integration-agreements.md "megismerheti a vállalati integrációs szerződések")  
* [További tudnivalók a átalakítások](logic-apps-enterprise-integration-transform.md "megismerheti a vállalati integrációs átalakításokat")  

