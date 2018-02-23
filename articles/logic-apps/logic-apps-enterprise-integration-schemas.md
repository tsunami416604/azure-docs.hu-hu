---
title: "Az XML-érvényesítés - Azure Logic Apps sémák |} Microsoft Docs"
description: "XML-sémák dokumentumok ellenőrzése az Azure Logic Apps és vállalati integrációs csomag"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: db2d9313e443ebc9dd32fcb905b0ae62219e4bbf
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="validate-xml-with-schemas-for-azure-logic-apps-and-the-enterprise-integration-pack"></a>Ellenőrizze az XML a sémák Azure Logic Apps és a vállalati integrációs csomag

Sémák győződjön meg arról, hogy az XML-dokumentumok kap érvényes és a várt adatokat egy előre meghatározott formátumban. Sémák is hozzájárulhat a B2B forgatókönyvek cserélt üzeneteket ellenőrzése.

## <a name="add-a-schema"></a>A séma hozzáadása

1. Válassza ki az Azure-portálon **minden szolgáltatás**.

    ![Azure-portálon "Az összes szolgáltatás"](media/logic-apps-enterprise-integration-schemas/overview-11.png)

2. A szűrő a keresőmezőbe írja be **integrációs**, és válassza ki **integrációs fiókok** eredmények listájáról.

    ![Szűrő keresőmezőbe](media/logic-apps-enterprise-integration-schemas/overview-21.png)

3. Válassza ki a **integrációs fiók** hol szeretné hozzáadni a sémát.

    ![Integrációs fiókok listája](media/logic-apps-enterprise-integration-schemas/overview-31.png)

4. Válassza ki a **sémák** csempére.

    ![Példa integrációs fiók "Sémák"](media/logic-apps-enterprise-integration-schemas/schema-11.png)

### <a name="add-a-schema-file-smaller-than-2-mb"></a>Egy 2 MB-nál kisebb sémafájl hozzáadása

1. Az a **sémák** panelen (a a fent leírt lépésekkel), megnyílik a választható **Hozzáadás**.

    !["Add" sémák panelen](media/logic-apps-enterprise-integration-schemas/schema-21.png)

2. Adjon meg egy nevet a sémát. A fájl feltöltése a ikonja melletti kiválasztásával a **séma** mezőbe. A feltöltési folyamat befejezése után válassza ki a **OK**.

    ![Képernyőkép a "Séma hozzáadása", "Kis fájl" kiemelve](media/logic-apps-enterprise-integration-schemas/schema-31.png)

### <a name="add-a-schema-file-larger-than-2-mb-up-to-8-mb-maximum"></a>Adjon hozzá egy séma fájlt nagyobb, mint 2 MB (legfeljebb 8 MB)

Ezeket a lépéseket attól függően változnak, blob tároló hozzáférési szint: **nyilvános** vagy **nincs névtelen hozzáférés**.

**Ez a hozzáférési szint meghatározásához**

1.  Nyissa meg **Azure Tártallózó**. 

2.  A **Blobtárolók**, válassza ki a kívánt blob-tároló. 

3.  Válassza ki **biztonsági**, **hozzáférési szint**.

Ha a blob hozzáférési szint **nyilvános**, kövesse az alábbi lépéseket.

![Az Azure Tártallózó "Blobtárolók", "Biztonság" és "Nyilvános" kiemelve](media/logic-apps-enterprise-integration-schemas/blob-public.png)

1. A séma feltöltése a tárfiókba, és másolja az URI azonosító.

    ![Storage-fiókkal, és a kijelölt URI](media/logic-apps-enterprise-integration-schemas/schema-blob.png)

2. A **séma hozzáadása**, jelölje be **nagy méretű fájlok**, és adja meg az URI-azonosító található a **tartalom URI** szövegmezőben.

    ![A "Hozzáadás" gombra, és a "Nagy file" kiemelt a sémák](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

Ha a blob hozzáférési szint **nincs névtelen hozzáférés**, kövesse az alábbi lépéseket.

![Az Azure Tártallózó "Blobtárolók", "Biztonság" és "Nincs névtelen hozzáférés" kiemelve](media/logic-apps-enterprise-integration-schemas/blob-1.png)

1. Töltse fel a séma a tárfiókhoz.

    ![Tárfiók](media/logic-apps-enterprise-integration-schemas/blob-3.png)

2. A séma egy közös hozzáférésű jogosultságkódot létrehozni.

    ![A tárfiók, megosztott elérési aláírást lap kiemelve](media/logic-apps-enterprise-integration-schemas/blob-2.png)

3. A **séma hozzáadása**, jelölje be **nagy méretű fájlok**, és adja meg a közös hozzáférésű jogosultságkódot URI a a **tartalom URI** szövegmezőben.

    ![A "Hozzáadás" gombra, és a "Nagy file" kiemelt a sémák](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

4. Az a **sémák** integrációs fiókja panelen az újonnan hozzáadott sémát kell megjelennie.

    ![Integráció fiókját, "Sémák" és az új sémával kiemelve](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>Sémák szerkesztése

1. Válassza ki a **sémák** csempére.

2. Miután a **sémák** panel nyílik, válassza ki a sémát, hogy módosítani szeretné.

3. Az a **sémák** paneljén válassza **szerkesztése**.

    ![Sémák panel](media/logic-apps-enterprise-integration-schemas/edit-12.png)

4. Válassza ki a fájl szerkesztése, majd válassza a kívánt **nyitott**.

    ![Nyissa meg sémafájl szerkesztése](media/logic-apps-enterprise-integration-schemas/edit-31.png)

Az Azure mutat be egy üzenet, hogy a séma sikeresen feltöltve.

## <a name="delete-schemas"></a>Sémák törlése

1. Válassza ki a **sémák** csempére.

2. Miután a **sémák** panel nyílik, válassza ki a törölni kívánt séma.

3. Az a **sémák** paneljén válassza **törlése**.

    ![Sémák panel](media/logic-apps-enterprise-integration-schemas/delete-12.png)

4. Győződjön meg arról, hogy valóban törli a kijelölt séma, válassza a **Igen**.

    ![Jóváhagyást kérő üzenet "Delete séma"](media/logic-apps-enterprise-integration-schemas/delete-21.png)

    Az a **sémák** panelen, a séma lista frissül, és már nem tartalmazza a séma, amelyet törölt.

    ![A fiókhoz, amely a kijelölt "sémák" integráció](media/logic-apps-enterprise-integration-schemas/delete-31.png)

## <a name="next-steps"></a>További lépések
* [További tudnivalók a vállalati integrációs csomag](logic-apps-enterprise-integration-overview.md "további információ a vállalati integrációs csomag").  

