---
title: XML érvényességének ellenőrzése sémákkal – Azure Logic Apps |} A Microsoft Docs
description: Sémák XML-dokumentumok, az Azure Logic Appsben és Enterprise Integration Pack ellenőrzése hozzáadása
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.date: 02/06/2019
ms.openlocfilehash: 03ac2e0f42ff05165aa2313d823710a71c7dffec
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768307"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML érvényességének ellenőrzése az Azure Logic Apps Enterprise Integration Pack-sémákkal

Ellenőrizze, hogy a dokumentum érvényes XML-t használja, és a várt adatokkal rendelkeznek a vállalati integrációs forgatókönyvek az Azure Logic Appsben az előre meghatározott formátumban, a logikai alkalmazás sémák használja. Egy sémát is ellenőrizheti, amely vállalatközi (B2B) forgatókönyvekben a logic apps exchange-üzeneteket.

Integrációs fiókok és az összetevők, például sémákkal kapcsolatos korlátozásairól lásd: [korlátozásai és konfigurációs adatokat az Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

* Egy [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , ahol tárolja a sémák és más összetevőket, vállalati integráció és a vállalatközi (B2B) megoldásokat. 

  Ha a séma [2 MB vagy kisebb](#smaller-schema), adhat hozzá a sémát az integrációs fiók közvetlenül az Azure Portalról. Azonban ha a séma 2 MB-nál nagyobb méretű, de nem nagyobb, mint a [séma méretkorlát](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), feltöltheti a sémát az Azure storage-fiókba. 
  Szeretne hozzáadni a sémát az integrációs fiók, majd hozzákapcsolhatja a tárfiókot az integrációs fiókból. 
  Ebben a feladatban az alábbiakban szükséges elemek: 

  * [Az Azure storage-fiók](../storage/common/storage-account-overview.md) ahol egy blobtárolót hoz létre a séma. Ismerje meg, hogyan [hozzon létre egy tárfiókot](../storage/common/storage-quickstart-create-account.md). 

  * A séma tárolására szolgáló BLOB-tároló. Ismerje meg, hogyan [hozzon létre egy blobtárolót](../storage/blobs/storage-quickstart-blobs-portal.md). 
  Később szüksége lesz a tároló tartalom URI-JÁT a sémát az integrációs fiók hozzáadásakor.

  * [Az Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md), amely blob-tárolók és storage-fiókok felügyeletéhez használhatja. 
  Használhatja a Storage Explorer, válassza ki bármelyik beállítást is választja itt:
  
    * Az Azure Portalon keresse meg és válassza ki a tárfiókját. 
    Válassza ki a tárfiók menüjében **Tártallózó**.

    * Az asztali verzió [töltse le és telepítse az Azure Storage Explorer](https://www.storageexplorer.com/). 
    Ezt követően csatlakoztassa a Storage Explorert a tárfiókhoz a lépéseket követve [Ismerkedés a Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). 
    További tudnivalókért lásd: [a rövid útmutató: Blob létrehozása objektumtárban az Azure Storage Explorerrel](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

Logic Apps-alkalmazás létrehozásakor és sémák hozzáadása nem szükséges. A sémát használja, azonban a logikai alkalmazás kell létrehozhatja, ha integrációs fiókot, ahol a séma tárolja. Ismerje meg, [a logic apps és integrációs fiókok összekapcsolása](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Ha még nem rendelkezik egy logikai alkalmazást, további [létrehozása a logic apps](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-schemas"></a>Sémák hozzáadása

1. Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a> az Azure-fiókja hitelesítő adataival.

1. Keresse meg és nyissa meg az integrációs fiók az Azure fő menüjéből válassza **minden szolgáltatás**. A Keresés mezőbe írja be az "integrációs fiók". Válassza ki **integrációs fiókok**.

   ![Integrációs fiók található](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Válassza ki az integrációs fiók kívánja adja hozzá a séma, például:

   ![Válassza ki az integrációs fiók](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. Az integrációs fiók **áttekintése** lap **összetevők**, jelölje be a **sémák** csempére.

   ![Válassza ki a "Sémák"](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. Miután a **sémák** lapon megnyílik a **Hozzáadás**.

   ![Válassza az "Add"](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

A séma (.xsd) fájl mérete alapján, kövesse a lépéseket, amelyek vagy a séma feltöltése [akár 2 MB](#smaller-schema) vagy [2 MB-nál több, legfeljebb 8 MB méretű](#larger-schema).

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Sémák hozzáadása akár 2 MB

1. A **séma hozzáadása**, adja meg a séma nevét. 
   Tartsa **kis méretű fájlt** kiválasztott. Mellett a **séma** válassza ki a mappa ikont. Keresse meg és válassza ki a sémát videófájl, például:

   ![Kisebb séma feltöltése](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. Amikor elkészült, válassza ki a **OK**.

   A séma a feltöltés befejezését követően a séma megjelenik a **sémák** listája.

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>2 MB-nál több sémák hozzáadása

Nagyobb sémák hozzáadásához a séma feltölthet egy Azure blob-tárolóba az Azure storage-fiókban. A lépések hozzáadásához a maps licenctár-a blobtárolót nyilvános olvasási hozzáféréssel rendelkezik-e. Ezért először ellenőrizze-e a blob-tároló nyilvános olvasási hozzáféréssel rendelkezik az alábbi lépéseket: [Blob-tároló nyilvános hozzáférési szintjének beállítása](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Ellenőrizze a tároló hozzáférési szint

1. Nyissa meg az Azure Storage Explorerben. Ha még nincs kibontva Intéző ablakában bontsa ki az Azure-előfizetésében.

1. Bontsa ki a **Tárfiókok** > {*a storage-fiók*} > **Blobtárolók**. Válassza ki a blob-tárolóba.

1. A blob-tároló helyi menüjén válassza **nyilvános hozzáférési szint beállítása**.

   * Ha a blob-tároló legalább nyilvános hozzáférése van, válassza ki a **Mégse**, és kövesse az alábbi lépéseket a későbbiekben ezen az oldalon: [Nyilvános hozzáférés tárolók feltöltése](#public-access)

     ![Nyilvános hozzáférés](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Ha a blob-tároló nyilvános hozzáférése nem, válassza ki a **Mégse**, és kövesse az alábbi lépéseket a későbbiekben ezen az oldalon: [Nyilvános hozzáférés nélkül tárolók feltöltése](#public-access)

     ![Nincs nyilvános hozzáférés](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Nyilvános hozzáférés tárolók feltöltése

1. A séma feltöltése a tárfiókba. 
   Válassza a jobb oldali ablakban **feltöltése**.

1. Miután befejezte a feltöltés, válassza ki a feltöltött sémát. Az eszköztáron válassza **példány URL-cím** úgy, hogy a séma URL-címet másolja.

1. Térjen vissza az Azure Portalon, a **séma hozzáadása** panel meg nyitva. 
   Adja meg a szerelvény nevét. 
   Válasszon **nagy fájlok (2 MB-nál nagyobb)**. 

   A **tartalom URI-JÁT** be most már megjelenik, helyett a **séma** mezőbe.

1. Az a **tartalom URI-JÁT** mezőbe illessze be a séma URL-CÍMÉT. 
   A séma hozzáadásának befejezéséhez.

A séma a feltöltés befejezését követően a séma megjelenik a **sémák** listája. Az integrációs fiók **áttekintése** lap **összetevők**, a **sémák** csempe most feltöltött sémák számát jeleníti meg.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Nyilvános hozzáférés nélkül tárolók feltöltése

1. A séma feltöltése a tárfiókba. 
   Válassza a jobb oldali ablakban **feltöltése**.

1. Feltöltés után hozzon létre egy közös hozzáférésű jogosultságkód (SAS) a séma. 
   A séma helyi menüjén válassza **közös hozzáférési jogosultságkód igénylése**.

1. Az a **közös hozzáférésű Jogosultságkód** ablaktáblán válassza **Generate tároló-szintű közös hozzáférésű jogosultságkód URI** > **létrehozás**. 
   Az SAS URL-cím generálása lekérdezi a után a **URL-cím** válassza **másolási**.

1. Térjen vissza az Azure Portalon, a **séma hozzáadása** panel meg nyitva. Válasszon **nagy méretű fájlok**.

   A **tartalom URI-JÁT** be most már megjelenik, helyett a **séma** mezőbe.

1. Az a **tartalom URI-JÁT** mezőbe illessze be a korábban létrehozott SAS URI-t. A séma hozzáadásának befejezéséhez.

A séma a feltöltés befejezését követően a séma megjelenik a **sémák** listája. Az integrációs fiók **áttekintése** lap **összetevők**, a **sémák** csempe most feltöltött sémák számát jeleníti meg.

## <a name="edit-schemas"></a>Sémák szerkesztése

Egy meglévő séma frissítésére, akkor töltse fel új sémát fájlt, amely rendelkezik a szükséges módosításokat. Azonban először töltheti le a meglévő sémák szerkesztésre.

1. Az a <a href="https://portal.azure.com" target="_blank">az Azure portal</a>, keresse meg és nyissa meg az integrációs fiók, ha nem már megnyitásához.

1. Az Azure fő menüjéből válassza **minden szolgáltatás**. 
   A Keresés mezőbe írja be az "integrációs fiók". 
   Válassza ki **integrációs fiókok**.

1. Válassza ki az integrációs fiók, ahol szeretné frissíteni a sémát.

1. Az integrációs fiók **áttekintése** lap **összetevők**, jelölje be a **sémák** csempére.

1. Miután a **sémák** lap megnyitásakor, válassza ki a sémát. 
   Töltse le és a séma szerkesztése először válassza a **letöltése**, és a sémát.

1. Amikor készen áll a frissített séma feltöltése a a **sémák** lapon, válassza ki a sémát, frissítése, és válassza a **frissítése**.

1. Keresse meg és válassza ki a feltölteni kívánt frissített sémát. 
   A sémafájl a feltöltés befejezését követően a frissített séma megjelenik a **sémák** listája.

## <a name="delete-schemas"></a>Sémák törlése

1. Az a <a href="https://portal.azure.com" target="_blank">az Azure portal</a>, keresse meg és nyissa meg az integrációs fiók, ha nem már megnyitásához.

1. Az Azure fő menüjéből válassza **minden szolgáltatás**. 
   A Keresés mezőbe írja be az "integrációs fiók". 
   Válassza ki **integrációs fiókok**.

1. Válassza ki az integrációs fiók, ha törölni szeretné a sémát.

1. Az integrációs fiók **áttekintése** lap **összetevők**, jelölje be a **sémák** csempére.

1. Miután a **sémák** lap megnyitásakor, válassza ki a sémát, és válassza ki **törlése**.

1. Győződjön meg arról, hogy szeretné-e a séma törlés, válassza a **Igen**.

## <a name="next-steps"></a>További lépések

* [További információ az Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
* [További tudnivalók a térképek](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [További tudnivalók a átalakítások](../logic-apps/logic-apps-enterprise-integration-transform.md)