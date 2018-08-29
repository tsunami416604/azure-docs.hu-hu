---
title: Sémák XML-érvényesítés – Azure Logic Apps-alkalmazások hozzáadása |} A Microsoft Docs
description: Hozzon létre az Azure Logic Apps Enterprise Integration Pack az XML-dokumentumok, amely sémák
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.date: 07/29/2016
ms.openlocfilehash: e03346da1c2b77f885c39d5329f990684979c56e
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123073"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML érvényességének ellenőrzése az Azure Logic Apps Enterprise Integration Pack-sémákkal

Sémák erősítse meg, hogy az XML-dokumentumok kap érvényes, és a várt adatokat tartalmaz egy előre definiált formátum. Sémák is segít érvényesíteni a B2B-forgatókönyvek esetében cserélt üzeneteket.

## <a name="add-a-schema"></a>A séma hozzáadása

1. Az Azure Portalon válassza ki a **minden szolgáltatás**.

    ![Az Azure portal, a "Minden szolgáltatás"](media/logic-apps-enterprise-integration-schemas/overview-11.png)

2. A szűrő be a keresőmezőbe írja be **integrációs**, és válassza ki **integrációs fiókok** az eredmények listájában.

    ![Keresőmező szűrése](media/logic-apps-enterprise-integration-schemas/overview-21.png)

3. Válassza ki a **integrációs fiók** hol szeretne hozzáadni a sémát.

    ![Integrációs fiókok listája](media/logic-apps-enterprise-integration-schemas/overview-31.png)

4. Válassza ki a **sémák** csempére.

    ![Példa-integrációs fiók "Sémák"](media/logic-apps-enterprise-integration-schemas/schema-11.png)

### <a name="add-a-schema-file-smaller-than-2-mb"></a>Adjon hozzá egy 2 MB-nál kisebb séma fájlt.

1. Az a **sémák** megnyílik (az előző lépésekben) válassza a panel **Hozzáadás**.

    ![Sémák panelen, a "Hozzáadás"](media/logic-apps-enterprise-integration-schemas/schema-21.png)

2. Adjon meg egy nevet a sémát. A fájl feltöltése a mappa ikont a kiválasztásával a **séma** mezőbe. A feltöltési folyamat befejezése után jelölje ki a **OK**.

    ![Képernyőkép a "Séma hozzáadása", a "kis"fájl kiemelésével](media/logic-apps-enterprise-integration-schemas/schema-31.png)

### <a name="add-a-schema-file-larger-than-2-mb-up-to-8-mb-maximum"></a>Adjon hozzá egy séma (legfeljebb 8 MB maximális) 2 MB-nál nagyobb méretű fájlt

Ezeket a lépéseket szerint különbözik a blob-tároló hozzáférési szint: **nyilvános** vagy **nincs névtelen hozzáférés**.

**Ez a hozzáférési szint meghatározásához**

1.  Nyissa meg **az Azure Storage Explorer**. 

2.  A **Blobtárolók**, válassza ki a kívánt blob-tárolóba. 

3.  Válassza ki **biztonsági**, **hozzáférési szint**.

Ha a blob hozzáférési szint **nyilvános**, kövesse az alábbi lépéseket.

![Az Azure Storage Explorer, a "Blob-tárolók", "Security" és "Nyilvános" kiemelésével](media/logic-apps-enterprise-integration-schemas/blob-public.png)

1. A séma feltöltése a storage-fiókba, és másolja az URI-t.

    ![Storage-fiók, a kiemelt URI](media/logic-apps-enterprise-integration-schemas/schema-blob.png)

2. A **séma hozzáadása**, jelölje be **nagy méretű fájlok**, és adja meg az URI-t a **tartalom URI-JÁT** szövegmező.

    ![Sémák, a "Hozzáadás" gombra, és "Nagy méretű fájl" kiemelésével](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

Ha a blob hozzáférési szint **nincs névtelen hozzáférés**, kövesse az alábbi lépéseket.

![Az Azure Storage Explorer, a "Blob-tárolók", "Security" és "Nincs névtelen hozzáférés" kiemelésével](media/logic-apps-enterprise-integration-schemas/blob-1.png)

1. A séma feltöltése a tárfiókba.

    ![Tárfiók](media/logic-apps-enterprise-integration-schemas/blob-3.png)

2. A séma közös hozzáférési aláírásokat létrehozni.

    ![Storage-fiókot, a közös hozzáférési aláírások lapfülre kiemelésével](media/logic-apps-enterprise-integration-schemas/blob-2.png)

3. A **séma hozzáadása**, jelölje be **nagy méretű fájlok**, és adja meg a közös hozzáférésű jogosultságkód URI az az **tartalom URI-JÁT** szövegmező.

    ![Sémák, a "Hozzáadás" gombra, és "Nagy méretű fájl" kiemelésével](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

4. Az a **sémák** az integrációs fiók panelen, az újonnan hozzáadott séma meg kell jelennie.

    ![Az integrációs fiók "Sémák" és az új sémában kiemelésével](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>Sémák szerkesztése

1. Válassza ki a **sémák** csempére.

2. Miután a **sémák** panel megnyílik, válassza ki a szerkeszteni kívánt séma.

3. Az a **sémák** panelen válassza a **szerkesztése**.

    ![Sémák panel](media/logic-apps-enterprise-integration-schemas/edit-12.png)

4. Válassza ki a fájl szerkesztése, majd válassza a kívánt **nyílt**.

    ![Nyissa meg a sémafájl szerkesztése](media/logic-apps-enterprise-integration-schemas/edit-31.png)

Az Azure jelenít meg az üzenetet, hogy a séma feltöltése sikeresen befejeződött.

## <a name="delete-schemas"></a>Sémák törlése

1. Válassza ki a **sémák** csempére.

2. Miután a **sémák** panel megnyílik, válassza ki a törölni kívánt séma.

3. Az a **sémák** panelen válassza a **törlése**.

    ![Sémák panel](media/logic-apps-enterprise-integration-schemas/delete-12.png)

4. Győződjön meg arról, hogy szeretné-e törli a kijelölt sémát, válassza a **Igen**.

    ![Megerősítő üzenet "Séma törlése"](media/logic-apps-enterprise-integration-schemas/delete-21.png)

    Az a **sémák** panelen a séma lista frissül, és már nem tartalmazza a sémát, amelyet törölt.

    ![Az integrációs fiók, a "Sémák" kiemelésével](media/logic-apps-enterprise-integration-schemas/delete-31.png)

## <a name="next-steps"></a>További lépések
* [További információ az Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "további tudnivalók az enterprise integration pack").  

