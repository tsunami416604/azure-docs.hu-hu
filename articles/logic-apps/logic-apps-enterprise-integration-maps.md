---
title: XML átalakítása az XSLT-leképezések – Azure Logic Apps |} A Microsoft Docs
description: XSLT képez le az Azure Logic Apps Enterprise Integration Pack az XML-átalakítás hozzáadása
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
manager: carmonm
ms.topic: article
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.date: 02/06/2019
ms.openlocfilehash: f6d778ddbce16c223945d4683bd7a950bd2a0cb0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455802"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Az Azure Logic Apps Enterprise Integration Pack-leképezések XML-átalakítás

XML-adatok átviteléhez a vállalati integrációs forgatókönyvek az Azure Logic Appsben formátumok közötti, a logikai alkalmazás maps, vagy még pontosabban bővíthető stíluslap nyelvi XSLT átalakítások térképek használhat. Térkép az XML-dokumentum, amely ismerteti, hogyan lehet egy másik formátumba alakítsa át adatait egy XML-dokumentumból. 

Tegyük fel, hogy rendszeresen kapott B2B rendeléseket és számlákat olyan ügyfelünk, akit a YYYMMDD dátum formátumot használja. Azonban a szervezet használja a MMDDYYY dátumformátum. Határozza meg, és a egy leképezéssel, amely a YYYMMDD dátumformátum MMDDYYY formátumra alakítja át a sorrend, illetve számla részleteinek a felhasználói tevékenység adatbázisban való tárolás előtt.

Integrációs fiókok és összetevőket, például a maps kapcsolódó korlátozásairól lásd: [korlátozásai és konfigurációs adatokat az Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

* Egy [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , ahol tárolja a maps és más összetevőket, vállalati integráció és a vállalatközi (B2B) megoldásokat.

* Ha a leképezés egy külső szerelvény hivatkozik, hogy feltöltése *a szerelvényt és a térkép* az integrációs fiókba. Győződjön meg arról, hogy *először töltse fel a szerelvény*, majd töltse fel a térképet, amely a szerelvény hivatkozik.

  Ha a szerelvény 2 MB vagy annál kisebb, adhat hozzá a szerelvényt az integrációs fiók *közvetlenül* az Azure Portalról. Azonban a szerelvényt vagy a térkép-e 2 MB-nál nagyobb méretű, de nem nagyobb, mint a [mérete legfeljebb szerelvényeknek vagy a térképek](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), a következőket teheti:

  * Szerelvényeket szüksége lesz egy Azure blob-tárolóba, ahol feltöltheti a szerelvényt és a tároló helye. Ezzel a módszerrel megadhatja, hogy a hely később az integrációs fiók hozzáadásakor a szerelvény. 
  Ebben a feladatban a következőkre van szükség:

    | Elem | Leírás |
    |------|-------------|
    | [Azure Storage-fiók](../storage/common/storage-account-overview.md) | Ezt a fiókot hozzon létre egy Azure blob-tároló a szerelvény. Ismerje meg, [storage-fiók létrehozása](../storage/common/storage-quickstart-create-account.md). |
    | BLOB-tároló | Ebben a tárolóban feltöltheti a szerelvényt. Az integrációs fiók hozzáadásakor a szerelvény is szükséges a tároló helye. Ismerje meg, hogyan [hozzon létre egy blobtárolót](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Ez az eszköz segítségével további egyszerűen tárfiókok kezelését és blobtárolók. Vagy használhatja a Storage Explorer, [töltse le és telepítse az Azure Storage Explorer](https://www.storageexplorer.com/). Ezt követően csatlakoztassa a Storage Explorert a tárfiókhoz a lépéseket követve [Ismerkedés a Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). További tudnivalókért lásd: [a rövid útmutató: Blob létrehozása objektumtárban az Azure Storage Explorerrel](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>Vagy az Azure Portalon keresse meg és válassza ki a tárfiókját. Válassza ki a tárfiók menüjében **Tártallózó**. |
    |||

  * A térképek szolgáltatáshoz, akkor jelenleg használatával adhat hozzá nagyobb maps a [Azure Logic Apps alkalmazások REST API - térképek](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

Logic Apps-alkalmazás létrehozásakor, és a maps hozzáadása nem szükséges. Szeretne használni egy térképet, azonban a logikai alkalmazás kell létrehozhatja, ha integrációs fiókot, ahol azt tárolja. Ismerje meg, [a logic apps és integrációs fiókok összekapcsolása](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Ha még nem rendelkezik egy logikai alkalmazást, további [létrehozása a logic apps](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-referenced-assemblies"></a>Odkazovaná sestavení hozzáadása

1. Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a> az Azure-fiókja hitelesítő adataival.

1. Keresse meg és nyissa meg az integrációs fiók az Azure fő menüjéből válassza **minden szolgáltatás**. 
   A Keresés mezőbe írja be az "integrációs fiók". 
   Válassza ki **integrációs fiókok**.

   ![Integrációs fiók található](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Válassza ki, hol szeretné adja hozzá például a szerelvény integrációs fiók:

   ![Válassza ki az integrációs fiók](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Az integrációs fiók **áttekintése** lap **összetevők**, jelölje be a **szerelvények** csempére.

   ![Válassza ki a "Szerelvényeket."](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. Miután a **szerelvények** lapon megnyílik a **Hozzáadás**.

   ![Válassza az "Add"](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

A szerelvényfájl mérete alapján, kövesse a lépéseket, amelyek vagy szerelvény feltöltése [akár 2 MB](#smaller-assembly) vagy [több mint 2 MB, de csak legfeljebb 8 MB](#larger-assembly).
A szerelvény integrációs fiókok mennyiségek korlátozásairól lásd: [korlátozások és konfiguráció az Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Szerelvények hozzáadása akár 2 MB

1. A **szerelvény hozzáadása**, adja meg a szerelvény nevét. Tartsa **kis méretű fájlt** kiválasztott. Mellett a **szerelvény** válassza ki a mappa ikont. Keresse meg és válassza ki a feltöltendő, például szerelvény:

   ![Töltse fel a kisebb sestavení](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   Az a **szerelvénynév** tulajdonság, a szerelvényfájlnévre automatikusan megjelenik a szerelvény kiválasztása után.

1. Amikor elkészült, válassza ki a **OK**.

   A szerelvényfájl a feltöltés befejezését követően a szerelvény megjelenik a **szerelvények** listája.

   ![Feltöltött szerelvények listája](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   Az integrációs fiók **áttekintése** lap **összetevők**, a **szerelvények** csempe most számát jeleníti meg a feltöltött szerelvényeket, például:

   ![Feltöltött szerelvények](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>2 MB-nál több szerelvények hozzáadása

Nagyobb szerelvények hozzáadásához a szerelvény feltölthet egy Azure blob-tárolóba az Azure storage-fiókban. A szerelvények hozzáadásának lépései eltér a blobtárolót nyilvános olvasási hozzáféréssel rendelkezik-e. Ezért először ellenőrizze-e a blob-tároló nyilvános olvasási hozzáféréssel rendelkezik az alábbi lépéseket: [Blob-tároló nyilvános hozzáférési szintjének beállítása](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Ellenőrizze a tároló hozzáférési szint

1. Nyissa meg az Azure Storage Explorerben. Ha még nincs kibontva Intéző ablakában bontsa ki az Azure-előfizetésében.

1. Bontsa ki a **Tárfiókok** > {*a storage-fiók*} > **Blobtárolók**. Válassza ki a blob-tárolóba.

1. A blob-tároló helyi menüjén válassza **nyilvános hozzáférési szint beállítása**.

   * Ha a blob-tároló legalább nyilvános hozzáférése van, válassza ki a **Mégse**, és kövesse az alábbi lépéseket a későbbiekben ezen az oldalon: [Nyilvános hozzáférés tárolók feltöltése](#public-access-assemblies)

     ![Nyilvános hozzáférés](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Ha a blob-tároló nyilvános hozzáférése nem, válassza ki a **Mégse**, és kövesse az alábbi lépéseket a későbbiekben ezen az oldalon: [Nyilvános hozzáférés nélkül tárolók feltöltése](#no-public-access-assemblies)

     ![Nincs nyilvános hozzáférés](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Nyilvános hozzáférés tárolók feltöltése

1. Töltse fel a tárfiók a szerelvényt. 
   Válassza a jobb oldali ablakban **feltöltése**.

1. Feltöltése után válassza ki a feltöltött sestavení. Az eszköztáron válassza **példány URL-cím** úgy, hogy másolja a szerelvény URL-CÍMÉT.

1. Térjen vissza az Azure Portalon, a **szerelvény hozzáadása** panel meg nyitva. 
   Adja meg a szerelvény nevét. 
   Válasszon **nagy fájlok (2 MB-nál nagyobb)**.

   A **tartalom URI-JÁT** be most már megjelenik, helyett a **szerelvény** mezőbe.

1. Az a **tartalom URI-JÁT** mezőbe illessze be a szerelvény URL-CÍMÉT. 
   A szerelvény hozzáadásának befejezéséhez.

A szerelvény a feltöltés befejezését követően a séma megjelenik a **szerelvények** listája.
Az integrációs fiók **áttekintése** lap **összetevők**, a **szerelvények** csempe most feltöltött szerelvények számát jeleníti meg.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Nyilvános hozzáférés nélkül tárolók feltöltése

1. Töltse fel a tárfiók a szerelvényt. 
   Válassza a jobb oldali ablakban **feltöltése**.

1. Feltöltése után közös hozzáférésű jogosultságkód (SAS) a szerelvény hozza létre. 
   A szerelvény helyi menüjén válassza **közös hozzáférési jogosultságkód igénylése**.

1. Az a **közös hozzáférésű Jogosultságkód** ablaktáblán válassza **Generate tároló-szintű közös hozzáférésű jogosultságkód URI** > **létrehozás**. 
   Az SAS URL-cím generálása lekérdezi a után a **URL-cím** válassza **másolási**.

1. Térjen vissza az Azure Portalon, a **szerelvény hozzáadása** panel meg nyitva. 
   Adja meg a szerelvény nevét. 
   Válasszon **nagy fájlok (2 MB-nál nagyobb)**.

   A **tartalom URI-JÁT** be most már megjelenik, helyett a **szerelvény** mezőbe.

1. Az a **tartalom URI-JÁT** mezőbe illessze be a korábban létrehozott SAS URI-t. A szerelvény hozzáadásának befejezéséhez.

A szerelvény a feltöltés befejezését követően a szerelvény megjelenik a **sémák** listája. Az integrációs fiók **áttekintése** lap **összetevők**, a **szerelvények** csempe most feltöltött szerelvények számát jeleníti meg.

## <a name="create-maps"></a>Térképek létrehozása

Lehetővé teszi egy térképet, az az XSLT-dokumentum létrehozásához, használhatja a Visual Studio 2015 használatával BizTalk-integráció projekt létrehozására szolgáló a [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md). Ebben a projektben egy integrációs térkép fájlt, amely lehetővé teszi elemek között két XML-séma fájlok vizuálisan leképezése hozhat létre. A projekt elkészítése, miután az XSLT-dokumentum kap.
A térkép mennyiségeket az integrációs fiókok korlátozásairól lásd: [korlátozások és konfiguráció az Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). 

## <a name="add-maps"></a>Leképezések hozzáadása

Miután feltölti a térkép hivatkozó bármely szerelvényeket, a térkép most tölthet fel.

1. Ha Ön még nem tette meg, jelentkezzen be a <a href="https://portal.azure.com" target="_blank">az Azure portal</a> az Azure-fiók hitelesítő adataival. 

1. Ha az integrációs fiók még nincs megnyitva, az Azure fő menüjéből, válassza ki a **minden szolgáltatás**. 
   A Keresés mezőbe írja be az "integrációs fiók". 
   Válassza ki **integrációs fiókok**.

   ![Integrációs fiók található](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Válassza ki az integrációs fiók kívánja adja hozzá a térkép, például:

   ![Válassza ki az integrációs fiók](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Az integrációs fiók **áttekintése** lap **összetevők**, jelölje be a **Maps** csempére.

   ![Válassza ki a "Térképek"](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. Miután a **Maps** lapon megnyílik a **Hozzáadás**.

   ![Válassza az "Add"](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Leképezések hozzáadása akár 2 MB

1. A **térkép hozzáadása**, adjon meg egy nevet a térképen. 

1. A **leképezés típusa**, adja meg, például: **Folyékony**, **XSLT**, **XSLT 2.0**, vagy **XSLT 3.0**.

1. Tartsa **kis méretű fájlt** kiválasztott. Mellett a **térkép** válassza ki a mappa ikont. Keresse meg és válassza ki a térkép videófájl, például:

   ![Töltse fel a térkép](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Ha nem törli a **neve** tulajdonság üres, a térkép fájl neve automatikusan megjelenik a tulajdonság automatikusan a térkép-fájl kiválasztása után. 
   Bármely egyedi név is használhatja.

1. Amikor elkészült, válassza ki a **OK**. 
   Feltöltése a leképezés befejeződése után a térkép megjelenik a **Maps** listája.

   ![Feltöltött maps listája](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   Az integrációs fiók **áttekintése** lap **összetevők**, a **térképek** csempe most számát jeleníti meg a feltöltött térképek, például:

   ![Feltöltött térképek](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>2 MB-nál több leképezések hozzáadása

Jelenleg nagyobb maps hozzáadásához használja a [Azure Logic Apps alkalmazások REST API - térképek](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>A maps szerkesztése

Egy meglévő térkép frissíteni, akkor töltse fel egy új térkép fájlt, amely rendelkezik a szükséges módosításokat. Azonban először töltheti le a meglévő térkép szerkesztésre.

1. Az a <a href="https://portal.azure.com" target="_blank">az Azure portal</a>, keresse meg és nyissa meg az integrációs fiók, ha nem már megnyitásához.

1. Az Azure fő menüjéből válassza **minden szolgáltatás**. A Keresés mezőbe írja be az "integrációs fiók". Válassza ki **integrációs fiókok**.

1. Válassza ki az integrációs fiók, ahol szeretné frissíteni a térképet.

1. Az integrációs fiók **áttekintése** lap **összetevők**, jelölje be a **Maps** csempére.

1. Miután a **Maps** lap megnyitásakor, válassza ki a térképet. 
   Töltse le és a leképezés szerkesztése először válassza a **letöltése**, és mentse a térképen.

1. Amikor készen áll a frissített térkép feltölteni kívánt a **Maps** lapra, jelölje be a térkép szeretné frissíteni, és válassza a **frissítése**.

1. Keresse meg és válassza ki a feltölteni kívánt frissített térképet. 
   Feltöltése a leképezés befejeződése után a frissített térkép megjelenik a **Maps** listája.

## <a name="delete-maps"></a>A maps törlése

1. Az a <a href="https://portal.azure.com" target="_blank">az Azure portal</a>, keresse meg és nyissa meg az integrációs fiók, ha nem már megnyitásához.

1. Az Azure fő menüjéből válassza **minden szolgáltatás**. 
   A Keresés mezőbe írja be az "integrációs fiók". 
   Válassza ki **integrációs fiókok**.

1. Válassza ki az integrációs fiók, ha törölni szeretné a térképen.

1. Az integrációs fiók **áttekintése** lap **összetevők**, jelölje be a **Maps** csempére.

1. Miután a **Maps** lap megnyitásakor, válassza ki a térképet, és válassza **törlése**.

1. Annak ellenőrzéséhez, hogy törli a térképen, válassza ki a **Igen**.

## <a name="next-steps"></a>További lépések

* [További információ az Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [További tudnivalók a sémák](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [További tudnivalók a átalakítások](../logic-apps/logic-apps-enterprise-integration-transform.md)