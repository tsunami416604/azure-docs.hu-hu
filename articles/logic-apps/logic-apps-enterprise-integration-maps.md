---
title: Átalakítás XML XSLT a maps - Azure Logic Apps |} Microsoft Docs
description: XSLT van leképezve az Azure Logic Apps és a vállalati integrációs csomag XML-adatok hozzáadása
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 4b4d626028eed09e9ce6a45fa8fa69859c082da7
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="add-maps-for-xml-data-transform"></a>Az XML-adatok átalakító maps hozzáadása

Vállalati integrációs maps formátumok közötti XML-adatok átalakítására használja. A térkép az XML-dokumentum, amely definiálja az adatokat a dokumentumban, amely egy másik formátumra alakítja át kell. 

## <a name="why-use-maps"></a>A maps miért érdemes használni?

Tegyük fel, hogy rendszeresen kapott B2B rendeléseket és a számlák YYYMMDD formátumot használja, a dátumok ügyfélnek. A szervezet tárolhatja, hogy a MMDDYYY formátumú dátum. Olyan térképet használható *átalakítási* a YYYMMDD dátumformátumra előtt a sorrend, illetve a Számla részletek tárolni a felhasználói tevékenység adatbázisban MMDDYYY be.


## <a name="how-do-i-create-a-map"></a>Hogyan térkép létrehozásához?

BizTalk integrációs-projektek hozhat létre a [vállalati integrációs csomag](logic-apps-enterprise-integration-overview.md "további információ a vállalati integrációs csomag") a Visual Studio 2015. Ezután létrehozhat olyan integrációs térképe fájlt, amely lehetővé teszi az elemek között két XML-sémafájlok vizuálisan hozzárendelését. Ez a projekt létrehozása után fog az XSLT-dokumentum.

Ha a leképezés egy külső összeállításra mutató hivatkozást tartalmaz, majd is fel kell tölteni a integrációs fiókhoz. Össze kell feltöltött meghatározott sorrendben, először a szerelvény és a leképezés, amely a szerelvényre hivatkozik.


## <a name="how-do-i-add-a-map"></a>Hogyan vegyen fel egy társítást?

1. Válassza ki az Azure-portálon **Tallózás**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. A szűrő a keresőmezőbe írja be **integrációs**, majd jelölje be **integrációs fiókok** eredmények listájáról.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Válassza ki a integrációs fiókot, amelyik számára a társítás hozzáadása kívánja.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Válassza ki a **Maps** csempére.

    ![](./media/logic-apps-enterprise-integration-maps/map-1.png)

5. Miután a Maps lap megnyitásakor, válassza ki azt **Hozzáadás**.

    ![](./media/logic-apps-enterprise-integration-maps/map-2.png)  

6. Adjon meg egy **neve** a térképen. A térkép fájl feltöltéséhez válassza ki a mappára a jobb oldalon a **térkép** szövegmezőben. A feltöltési folyamat befejezése után válassza ki a **OK**.

    ![](./media/logic-apps-enterprise-integration-maps/map-3.png)

7. Miután az Azure ad hozzá a térkép integrációs fiókját, bemutatja, hogy a leképezés vagy nem lett-e adva képernyőn üzenetet kap. Miután ezt az üzenetet kap, válassza ki azt a **Maps** szeretné megjeleníteni az újonnan hozzáadott térkép csempére.

    ![](./media/logic-apps-enterprise-integration-maps/map-4.png)


## <a name="how-do-i-add-an-assembly"></a>Hogyan hozzá egy szerelvény?
Ha szeretne feltölteni a szerelvény integrációs-fiók megnyitása.

1. Válassza ki a **szerelvények** csempére.

    ![integrationaccount-assembly-tile](./media/logic-apps-enterprise-integration-maps/assemblytile.png)

2. Miután a szerelvények lap megnyitásakor, válassza ki azt **Hozzáadás**. Adjon meg egy **neve** a szerelvényhez. A szerelvény fájl feltöltéséhez válassza ki a mappára a jobb oldalon a **szerelvény** szövegmezőben. A feltöltési folyamat befejezése után válassza ki a **OK**.

    ![Adja hozzá összeállítása](./media/logic-apps-enterprise-integration-maps/assemblyfile.png)


## <a name="how-do-i-edit-a-map"></a>Hogyan szerkeszthetők a térkép?

Akkor kell új térkép rendelkező fájlt tölthet fel a kívánt módosításokat. A térkép szerkesztéséhez először töltheti le.

Töltse fel, amely lecseréli a meglévő térkép új leképezés, kövesse az alábbi lépéseket.

1. Válassza ki a **Maps** csempére.

2. Után megnyílik a Maps lap, válassza ki a szerkeszteni kívánt leképezés.

3. Az a **Maps** lapon, válassza ki **frissítés**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-1.png)

4. Válassza ki a térkép fájlt, amelyet szeretne feltölteni, majd válassza ki a fájlválasztó **nyitott**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-2.png)

## <a name="how-to-delete-a-map"></a>Hogyan térképre törli?

1. Válassza ki a **Maps** csempére.

2. Után megnyílik a Maps lap, válassza ki a törölni kívánt leképezés.

3. Válasszon **törlése**.

    ![](./media/logic-apps-enterprise-integration-maps/delete.png)

4. Győződjön meg arról, hogy szeretné-e a térkép törlése.

    ![](./media/logic-apps-enterprise-integration-maps/delete-confirmation-1.png)

## <a name="next-steps"></a>További lépések
* [További tudnivalók a vállalati integrációs csomag](logic-apps-enterprise-integration-overview.md "további információ a vállalati integrációs csomag")  
* [További információ a megállapodások](../logic-apps/logic-apps-enterprise-integration-agreements.md "vállalati integrációs megállapodások ismertetése")  
* [További tudnivalók átalakítások](logic-apps-enterprise-integration-transform.md "vállalati integrációs átalakítások ismertetése")  

