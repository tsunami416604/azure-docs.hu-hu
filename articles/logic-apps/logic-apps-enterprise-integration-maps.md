---
title: XML átalakítása XSLT-leképezésekkel
description: XSLT-leképezések hozzáadása az XML átalakításához az Azure Logic Apps alkalmazásban a vállalati integrációs csomaggal
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: e186b9713c8464f8f37e1e0bf112c4118621925c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979414"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML-átalakítás térképekkel az Azure Logic Appsben az Enterprise Integration Pack segítségével

XML-adatok átviteléhez a vállalati integrációs forgatókönyvek az Azure Logic Apps formátumok között, a logikai alkalmazás használhatja a leképezések, pontosabban bővíthető stíluslap nyelvi átalakítások (XSLT) térképek. A térkép olyan XML-dokumentum, amely leírja, hogyan lehet adatokat konvertálni XML-dokumentumból más formátumba. 

Tegyük fel például, hogy rendszeresen kap B2B rendeléseket vagy számlákat egy YYYMMDD dátumformátumot használó vevőtől. A szervezet azonban az MMDDYYY dátumformátumot használja. Az YYYMMDD dátumformátumot MMDDYYY formátumra átalakító leképezést definiálhatja és használhatja, mielőtt a rendelés vagy a számla adatait tárolnák az ügyféltevékenység-adatbázisban.

Az integrációs fiókokhoz és az összetevőkhöz, például a térképekhez kapcsolódó korlátozásokat az [Azure Logic Apps korlátozási és konfigurációs információi című témakörben talál.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Integrációs [fiók,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) ahol a térképeket és más összetevőket tárolja a vállalati integrációhoz és a vállalatok közötti (B2B) megoldásokhoz.

* Ha a térkép egy külső szerelvényre hivatkozik, akkor *a szerelvényt és a térképet is* fel kell töltenie az integrációs fiókba. Győződjön meg arról, hogy [*először feltölti a szerelvényt,*](#add-assembly)majd töltse fel a szerelvényre hivatkozó térképet.

  Ha a szerelvény 2 MB vagy kisebb, hozzáadhatja a szerelvényaz integrációs fiók *közvetlenül* az Azure Portalon. Ha azonban a szerelvény vagy a térkép nagyobb, mint 2 MB, de nem nagyobb, mint a [szerelvények és térképek méretkorlátja,](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)akkor a következő lehetőségek közül választhat:

  * Az összeállításokhoz szüksége van egy Azure blobtárolóra, ahol feltöltheti a szerelvényt és a tároló helyét. Így később megadhatja ezt a helyet, amikor hozzáadja a szerelvényt az integrációs fiókhoz. 
  Ehhez a feladathoz a következő elemekre van szükség:

    | Elem | Leírás |
    |------|-------------|
    | [Azure tárfiók](../storage/common/storage-account-overview.md) | Ebben a fiókban hozzon létre egy Azure blob-tárolót a szerelvényhez. További információ [a tárfiók létrehozásáról.](../storage/common/storage-account-create.md) |
    | Blobtároló | Ebben a tárolóban feltöltheti a szerelvényt. A tároló helyének is szüksége van, amikor hozzáadja a szerelvényt az integrációs fiókhoz. Ismerje meg, hogyan [hozhat létre blobtárolót.](../storage/blobs/storage-quickstart-blobs-portal.md) |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Ez az eszköz segítségével könnyebben kezelheti a tárfiókokat és a blobtárolókat. A Storage Explorer használatához [töltse le és telepítse az Azure Storage Explorert.](https://www.storageexplorer.com/) Ezután csatlakoztassa a Storage Explorert a tárfiókhoz az Első lépések a [Tárházkezelővel](../vs-azure-tools-storage-manage-with-storage-explorer.md)című részben leírt lépésekkel. További információ: [Gyorsútmutató: Blob létrehozása az objektumtárolóban az Azure Storage Explorer használatával](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)című témakörben olvashat bővebben. <p>Vagy az Azure Portalon keresse meg és válassza ki a tárfiókot. A tárfiók menüjében válassza a **Tárkezelő lehetőséget.** |
    |||

  * A térképek hez jelenleg nagyobb térképeket adhat hozzá az [Azure Logic Apps REST API – Maps](https://docs.microsoft.com/rest/api/logic/maps/createorupdate)használatával.

Nincs szükség logikai alkalmazásra a térképek létrehozásakor és hozzáadásakor. Azonban egy térkép használatához a logikai alkalmazás kell összekapcsolni egy integrációs fiók, ahol tárolja a térképet. [Ismerje meg, hogyan kapcsolhat össze logikai alkalmazásokat az integrációs fiókokhoz.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) Ha még nem rendelkezik logikai alkalmazással, ismerje meg, [hogyan hozhat létre logikai alkalmazásokat.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>Hivatkozott összeállítások hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. Az integrációs fiók megkereséséhez és megnyitásához válassza az Azure főmenüjében a **Minden szolgáltatás**lehetőséget. 
   A keresőmezőbe írja be az "integrációs fiók" kifejezést. 
   Válassza **az Integrációs fiókok lehetőséget.**

   ![Integrációs fiók keresése](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Válassza ki azt az integrációs fiókot, amelyhez hozzá szeretné adni a szerelvényt, például:

   ![Integrációs fiók kiválasztása](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Az integrációs fiók **Áttekintés lapján,** **az Összetevők**csoportban válassza az **Összeállítások csempét.**

   ![Válassza ki a "Szerelvények"](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. Az **Összeállítások** lap megnyitása után válassza a **Hozzáadás gombot.**

   ![Válassza a "Hozzáadás" lehetőséget](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

Az összeállítási fájl méretétől függően kövesse a [legfeljebb 2 MB](#smaller-assembly) vagy [2 MB-nál nagyobb, de legfeljebb 8 MB](#larger-assembly)méretű szerelvény feltöltésének lépéseit.
Az integrációs fiókok összeállítási mennyiségének korlátozásáról az [Azure Logic Apps korlátai és konfigurációja.](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

> [!NOTE]
> Ha módosítja a szerelvényt, akkor azt is frissítenie kell a térképet, hogy a térkép módosult-e vagy sem.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Legfeljebb 2 MB összeállítások hozzáadása

1. A **Kódösszeállítás hozzáadása csoportban**adja meg a szerelvény nevét. A **Kis fájl** kijelölése. A **Szerelvény** mező mellett válassza a mappa ikonját. Keresse meg és válassza ki a feltölteni kívánt szerelvényt, például:

   ![Kisebb összeállítás feltöltése](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   A **Szerelvény neve** tulajdonságban a szerelvény fájlneve automatikusan megjelenik a szerelvény kiválasztása után.

1. Ha készen áll, válassza az **OK gombot.**

   Miután a szerelvényfájl feltöltése befejeződik, a szerelvény megjelenik az **Összeállítások** listában.

   ![Feltöltött összeállítások listája](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   Az integrációs fiók **áttekintése lapján,** az **Összetevők**csoportban az **Összeállítások** csempe most megjeleníti a feltöltött szerelvények számát, például:

   ![Feltöltött szerelvények](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>2 MB-nál nagyobb összeállítások hozzáadása

Nagyobb szerelvények hozzáadásához feltöltheti a szerelvényt egy Azure blobtárolóba az Azure storage-fiókjában. A szerelvények hozzáadásának lépései eltérőek lehetnek attól függően, hogy a blob tároló nyilvános olvasási hozzáféréssel rendelkezik-e. Ezért először ellenőrizze, hogy a blobtároló rendelkezik-e nyilvános olvasási hozzáféréssel az alábbi lépésekkel: [Nyilvános hozzáférési szint beállítása a blobtárolóhoz](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>A tároló hozzáférési szintjének ellenőrzése

1. Nyissa meg az Azure Storage Explorert. Az Intéző ablakban bontsa ki azure-előfizetését, ha még nem bontotta ki.

1. A **Storage-fiókok** > {*a tárfiók*} > **blobtárolók elemet.** Válassza ki a blob tároló.

1. A blobtároló helyi menüjében válassza **a Nyilvános hozzáférési szint beállítása**lehetőséget.

   * Ha a blobtároló legalább nyilvános hozzáféréssel rendelkezik, válassza **a Mégse**lehetőséget, és kövesse az alábbi lépéseket a lap későbbi részében: [Feltöltés nyilvános hozzáférésű tárolókba](#public-access-assemblies)

     ![Nyilvános hozzáférés](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Ha a blobtároló nem rendelkezik nyilvános hozzáféréssel, válassza **a Mégse**lehetőséget, és kövesse az alábbi lépéseket a lap későbbi részében: [Feltöltés nyilvános hozzáférés nélküli tárolókba](#no-public-access-assemblies)

     ![Nincs nyilvános hozzáférés](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Feltöltés nyilvános hozzáférésű tárolókba

1. Töltse fel a szerelvényt a tárfiókba. 
   A jobb oldali ablakban válassza a **Feltöltés**lehetőséget.

1. A feltöltés befejezése után válassza ki a feltöltött szerelvényt. Az eszköztáron válassza az **URL másolása** lehetőséget, hogy másolja az összeállítás URL-címét.

1. Térjen vissza az Azure Portalra, ahol a **Kódösszeállítás hozzáadása** ablaktábla meg van nyitva. 
   Adja meg a szerelvény nevét. 
   Válassza **a Nagyfájl (2 MB-nál nagyobb)** lehetőséget.

   Most a **Tartalom URI** mező jelenik meg, nem pedig a **Szerelvény** mező.

1. A **Tartalom URI-mezőbe** illessze be az összeállítás URL-címét. 
   Fejezd be a szerelvény hozzáadását.

Miután a szerelvény feltöltése befejeződik, a séma megjelenik az **Összeállítások** listában.
Az integrációs fiók **áttekintése** lapon, **az Összetevők**csoportban az **Összeállítások** csempe mostantól a feltöltött szerelvények számát jeleníti meg.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Feltöltés nyilvános hozzáférés nélküli tárolókba

1. Töltse fel a szerelvényt a tárfiókba. 
   A jobb oldali ablakban válassza a **Feltöltés**lehetőséget.

1. A feltöltés befejezése után hozzon létre egy közös hozzáférési aláírást (SAS) a szerelvényhez. 
   A szerelvény helyi menüjében válassza **a Megosztott hozzáférésű aláírás beszereznie**lehetőséget.

1. A **Megosztott hozzáférés aláírásablakában** válassza **a Tárolószintű megosztott hozzáférés-aláírás létrehozása létrehozása** > **lehetőséget.** 
   A SAS URL-cím létrehozása után az **URL-cím** mező mellett válassza a **Másolás lehetőséget.**

1. Térjen vissza az Azure Portalra, ahol a **Kódösszeállítás hozzáadása** ablaktábla meg van nyitva. 
   Adja meg a szerelvény nevét. 
   Válassza **a Nagyfájl (2 MB-nál nagyobb)** lehetőséget.

   Most a **Tartalom URI** mező jelenik meg, nem pedig a **Szerelvény** mező.

1. A **Content URI** mezőbe illessze be a korábban létrehozott SAS-URI-t. Fejezd be a szerelvény hozzáadását.

Miután a szerelvény feltöltése befejeződik, a szerelvény megjelenik a **Sémák** listában. Az integrációs fiók **áttekintése** lapon, **az Összetevők**csoportban az **Összeállítások** csempe mostantól a feltöltött szerelvények számát jeleníti meg.

## <a name="create-maps"></a>Térképek létrehozása

Térképként használható XSLT-dokumentum létrehozásához a Visual Studio 2015 segítségével biztalk integrációs projektet hozhat létre az [Enterprise Integration Pack csomag](logic-apps-enterprise-integration-overview.md)használatával. Ebben a projektben létrehozhat egy integrációs térképfájlt, amely lehetővé teszi az elemek vizuális leképezését két XML-sémafájl között. A projekt létrehozása után egy XSLT-dokumentumot kap.
Az integrációs fiókok térképmennyiségének korlátozásáról az [Azure Logic Apps korlátai és konfigurációja.](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 

## <a name="add-maps"></a>Térképek hozzáadása

Miután feltöltötte a térkép által hivatkozott szerelvényeket, most feltöltheti a térképet.

1. Ha még nem jelentkezett be, jelentkezzen be az [Azure Portalon](https://portal.azure.com) az Azure-fiók hitelesítő adataival. 

1. Ha az integrációs fiók még nincs megnyitva, az Azure főmenüjében válassza a **Minden szolgáltatás**lehetőséget. 
   A keresőmezőbe írja be az "integrációs fiók" kifejezést. 
   Válassza **az Integrációs fiókok lehetőséget.**

   ![Integrációs fiók keresése](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Válassza ki azt az integrációs fiókot, ahová hozzá szeretné adni a térképet, például:

   ![Integrációs fiók kiválasztása](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Az integrációs fiók **Áttekintés lapján,** **az Összetevők**csoportban válassza a Térkép **csempét.**

   ![Válassza a "Térképek" lehetőséget](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. A **Térképek** lap megnyitása után válassza a **Hozzáadás gombot.**

   ![Válassza a "Hozzáadás" lehetőséget](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Legfeljebb 2 MB-os térképek hozzáadása

1. A **Térkép hozzáadása csoportban**adja meg a térkép nevét. 

1. A **Térkép típusa csoportban**válassza ki a következő típust: **Liquid**, **XSLT**, **XSLT 2.0**vagy **XSLT 3.0**.

1. A **Kis fájl** kijelölése. A **Térkép** mező mellett válassza a mappa ikonját. Keresse meg és válassza ki a feltöltött térképet, például:

   ![Térkép feltöltése](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Ha üresen hagyja a **Name** tulajdonságot, a térkép fájlneve automatikusan megjelenik a tulajdonságban a térképfájl kijelölése után. 
   Azonban bármilyen egyedi nevet használhat.

1. Ha készen áll, válassza az **OK gombot.** 
   Miután a térképfájl feltöltése befejeződik, a térkép megjelenik a **Térképek** listában.

   ![Feltöltött térképek listája](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   Az integrációs fiók **Áttekintés lapján,** az **Összetevők**csoportban a **Térképek** csempe most antól a feltöltött térképek számát jeleníti meg, például:

   ![Feltöltött térképek](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>2 MB-nál nagyobb térképek hozzáadása

Jelenleg nagyobb térképek hozzáadásához használja az [Azure Logic Apps REST API – Maps](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

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

## <a name="edit-maps"></a>Térképek szerkesztése

Meglévő térkép frissítéséhez fel kell töltenie egy új térképfájlt, amely tartalmazza a kívánt módosításokat. A meglévő térképet azonban először letöltheti szerkesztésre.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és nyissa meg az integrációs fiókot, ha még nem nyitott.

1. Az Azure főmenüjében válassza a **Minden szolgáltatás lehetőséget.** A keresőmezőbe írja be az "integrációs fiók" kifejezést. Válassza **az Integrációs fiókok lehetőséget.**

1. Válassza ki azt az integrációs fiókot, amelyen frissíteni szeretné a térképet.

1. Az integrációs fiók **Áttekintés lapján,** **az Összetevők**csoportban válassza a Térkép **csempét.**

1. A **Térképek** lap megnyitása után válassza ki a térképet. 
   A térkép letöltéséhez és szerkesztéséhez először válassza a **Letöltés**lehetőséget, és mentse a térképet.

1. Ha készen áll a frissített térkép feltöltésére, a **Térképek** lapon jelölje ki a frissíteni kívánt térképet, és válassza a **Frissítés gombot.**

1. Keresse meg és válassza ki a feltölteni kívánt frissített térképet. 
   Miután a térképfájl feltöltése befejeződik, a frissített térkép megjelenik a **Térképek** listában.

## <a name="delete-maps"></a>Térképek törlése

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és nyissa meg az integrációs fiókot, ha még nem nyitott.

1. Az Azure főmenüjében válassza a **Minden szolgáltatás lehetőséget.** 
   A keresőmezőbe írja be az "integrációs fiók" kifejezést. 
   Válassza **az Integrációs fiókok lehetőséget.**

1. Válassza ki azt az integrációs fiókot, amelyen törölni szeretné a térképet.

1. Az integrációs fiók **Áttekintés lapján,** **az Összetevők**csoportban válassza a Térkép **csempét.**

1. A **Térképek** lap megnyitása után jelölje ki a térképet, és válassza a **Törlés gombot.**

1. A térkép törlésének megerősítéséhez válassza az **Igen**lehetőséget.

## <a name="next-steps"></a>További lépések

* [További információ az Enterprise Integration Pack csomagról](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [További információ a sémákról](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [További információ az átalakításokról](../logic-apps/logic-apps-enterprise-integration-transform.md)
