---
title: XML ellenőrzése sémákkal
description: Sémák hozzáadása XML-dokumentumok érvényesítéséhez az Azure Logic Apps alkalmazásban a vállalati integrációs csomaggal
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: ade29318fdd1510d03ddd208534258a30b256e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979376"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Az XML érvényességének ellenőrzése sémákkal az Azure Logic Appsben az Enterprise Integration Pack segítségével

Annak ellenőrzéséhez, hogy a dokumentumok érvényes XML-t használnak-e, és a várt adatokat az Azure Logic Apps vállalati integrációs forgatókönyvekhez előre meghatározott formátumban használják-e, a logikai alkalmazás sémákat használhat. A séma is érvényesítheti az üzeneteket, amelyek logikai alkalmazások cseréje a vállalkozások közötti (B2B) forgatókönyvek.

Az integrációs fiókokhoz és az összetevőkhöz, például a sémákhoz kapcsolódó korlátozásokat az [Azure Logic Apps korlátozásai és konfigurációs adatai című témakörben talál.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

* Integrációs [fiók,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) ahol a sémákat és más összetevőket nagyvállalati integrációhoz és vállalaton belüli (B2B) megoldásokhoz tárolja. 

  Ha a séma [2 MB vagy kisebb,](#smaller-schema)a sémát közvetlenül az Azure Portalról is hozzáadhatja az integrációs fiókhoz. Ha azonban a séma nagyobb, mint 2 MB, de nem nagyobb, mint a [séma méretkorlát,](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)feltöltheti a sémát egy Azure-tárfiókba. 
  Ha hozzá szeretné adni a sémát az integrációs fiókhoz, majd az integrációs fiókból csatolhat a tárfiókhoz. 
  Ehhez a feladathoz az alábbiakra van szüksége: 

  * [Azure storage-fiók,](../storage/common/storage-account-overview.md) ahol hozzon létre egy blob tárolóak a séma. További információ a [tárfiók létrehozásáról.](../storage/common/storage-account-create.md) 

  * Blob tároló a séma tárolásához. Ismerje meg, hogyan [hozhat létre blobtárolót.](../storage/blobs/storage-quickstart-blobs-portal.md) 
  Szüksége van a tároló tartalmának URI később, amikor hozzáadja a sémát az integrációs fiókhoz.

  * [Az Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md), amely a tárfiókok és a blobtárolók kezeléséhez használható. 
  A Tárolókezelő használatához itt válasszon az egyik lehetőséget:
  
    * Az Azure Portalon keresse meg és válassza ki a tárfiókot. 
    A tárfiók menüjében válassza a **Tárkezelő lehetőséget.**

    * Az asztali verzióhoz töltse le és telepítse az [Azure Storage Explorert.](https://www.storageexplorer.com/) 
    Ezután csatlakoztassa a Storage Explorert a tárfiókhoz az Első lépések a [Tárházkezelővel](../vs-azure-tools-storage-manage-with-storage-explorer.md)című részben leírt lépésekkel. 
    További információ: [Gyorsútmutató: Blob létrehozása az objektumtárolóban az Azure Storage Explorer használatával](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)című témakörben olvashat bővebben.

Nem kell egy logikai alkalmazás létrehozásakor és hozzáadásasémák. Azonban egy séma használatához a logikai alkalmazás kell csatolni egy integrációs fiók, ahol tárolja a sémát. [Ismerje meg, hogyan kapcsolhat össze logikai alkalmazásokat az integrációs fiókokhoz.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) Ha még nem rendelkezik logikai alkalmazással, ismerje meg, [hogyan hozhat létre logikai alkalmazásokat.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="add-schemas"></a>Sémák felvétele

1. Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a> az Azure-fiókja hitelesítő adataival.

1. Az integrációs fiók megkereséséhez és megnyitásához válassza az Azure főmenüjében a **Minden szolgáltatás**lehetőséget. A keresőmezőbe írja be az "integrációs fiók" kifejezést. Válassza **az Integrációs fiókok lehetőséget.**

   ![Integrációs fiók keresése](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Válassza ki azt az integrációs fiókot, amelyhez hozzá szeretné adni a sémát, például:

   ![Integrációs fiók kiválasztása](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. Az **integrációs**fiók **áttekintése lapján,** az Összetevők csoportban válassza a **Séma** csempét.

   ![Válassza a "Sémák" lehetőséget](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. A **Séma** lap megnyitása után válassza a **Hozzáadás gombot.**

   ![Válassza a "Hozzáadás" lehetőséget](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

A sémafájl (.xsd) mérete alapján kövesse a [legfeljebb 2 MB](#smaller-schema) vagy [2 MB, legfeljebb 8 MB](#larger-schema)méretű séma feltöltésének lépéseit.

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Sémák hozzáadása legfeljebb 2 MB-ig

1. A **Séma hozzáadása**csoportban adja meg a séma nevét. 
   A **Kis fájl** kijelölése. A **Séma** mező mellett válassza a mappa ikonját. Keresse meg és jelölje ki a feltöltendő sémát, például:

   ![Kisebb séma feltöltése](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. Ha készen áll, válassza az **OK gombot.**

   Miután a séma feltöltése befejeződik, a séma megjelenik a **Sémák** listában.

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>2 MB-nál nagyobb sémák hozzáadása

Nagyobb sémák hozzáadásához feltöltheti a sémát egy Azure blobtárolóba az Azure storage-fiókjában. A sémák hozzáadásának lépései eltérőek lehetnek attól függően, hogy a blob tároló nyilvános olvasási hozzáféréssel rendelkezik-e. Ezért először ellenőrizze, hogy a blobtároló rendelkezik-e nyilvános olvasási hozzáféréssel az alábbi lépésekkel: [Nyilvános hozzáférési szint beállítása a blobtárolóhoz](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>A tároló hozzáférési szintjének ellenőrzése

1. Nyissa meg az Azure Storage Explorert. Az Intéző ablakban bontsa ki azure-előfizetését, ha még nem bontotta ki.

1. A **Storage-fiókok** > {*a tárfiók*} > **blobtárolók elemet.** Válassza ki a blob tároló.

1. A blobtároló helyi menüjében válassza **a Nyilvános hozzáférési szint beállítása**lehetőséget.

   * Ha a blobtároló legalább nyilvános hozzáféréssel rendelkezik, válassza **a Mégse**lehetőséget, és kövesse az alábbi lépéseket a lap későbbi részében: [Feltöltés nyilvános hozzáférésű tárolókba](#public-access)

     ![Nyilvános hozzáférés](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Ha a blobtároló nem rendelkezik nyilvános hozzáféréssel, válassza **a Mégse**lehetőséget, és kövesse az alábbi lépéseket a lap későbbi részében: [Feltöltés nyilvános hozzáférés nélküli tárolókba](#public-access)

     ![Nincs nyilvános hozzáférés](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Feltöltés nyilvános hozzáférésű tárolókba

1. Töltse fel a sémát a tárfiókba. 
   A jobb oldali ablakban válassza a **Feltöltés**lehetőséget.

1. A feltöltés befejezése után válassza ki a feltöltött sémát. Az eszköztáron válassza az **URL másolása** lehetőséget, hogy a séma URL-címét másolja.

1. Térjen vissza az Azure portalra, ahol a **Séma hozzáadása** ablaktábla meg van nyitva. 
   Adja meg a szerelvény nevét. 
   Válassza **a Nagyfájl (2 MB-nál nagyobb)** lehetőséget. 

   Most a **Sémamező** helyett a **Tartalom URI-mező** jelenik meg.

1. A **Tartalom URI-mezőbe** illessze be a séma URL-címét. 
   Fejezze be a séma hozzáadását.

Miután a séma feltöltése befejeződik, a séma megjelenik a **Sémák** listában. Az **integrációs**fiók **áttekintése** lapon, az Összetevők csoportban a **Séma** csempe mostantól a feltöltött sémák számát jeleníti meg.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Feltöltés nyilvános hozzáférés nélküli tárolókba

1. Töltse fel a sémát a tárfiókba. 
   A jobb oldali ablakban válassza a **Feltöltés**lehetőséget.

1. A feltöltés befejezése után hozzon létre egy közös hozzáférési aláírást (SAS) a sémához. 
   A séma helyi menüjében válassza **a Megosztott hozzáférésű aláírás beszereznie**lehetőséget.

1. A **Megosztott hozzáférés aláírásablakában** válassza **a Tárolószintű megosztott hozzáférés-aláírás létrehozása létrehozása** > **lehetőséget.** 
   A SAS URL-cím létrehozása után az **URL-cím** mező mellett válassza a **Másolás lehetőséget.**

1. Térjen vissza az Azure portalra, ahol a **Séma hozzáadása** ablaktábla meg van nyitva. Válassza **a Nagyfájl lehetőséget.**

   Most a **Sémamező** helyett a **Tartalom URI-mező** jelenik meg.

1. A **Content URI** mezőbe illessze be a korábban létrehozott SAS-URI-t. Fejezze be a séma hozzáadását.

Miután a séma feltöltése befejeződik, a séma megjelenik a **Sémák** listában. Az **integrációs**fiók **áttekintése** lapon, az Összetevők csoportban a **Séma** csempe mostantól a feltöltött sémák számát jeleníti meg.

## <a name="edit-schemas"></a>Sémák szerkesztése

Meglévő séma frissítéséhez fel kell töltenie egy új sémafájlt, amely tartalmazza a kívánt módosításokat. A meglévő sémát azonban letöltheti szerkesztésre.

1. Az <a href="https://portal.azure.com" target="_blank">Azure Portalon</a>keresse meg és nyissa meg az integrációs fiókot, ha még nem nyitott.

1. Az Azure főmenüjében válassza a **Minden szolgáltatás lehetőséget.** 
   A keresőmezőbe írja be az "integrációs fiók" kifejezést. 
   Válassza **az Integrációs fiókok lehetőséget.**

1. Válassza ki azt az integrációs fiókot, amelyen frissíteni szeretné a sémát.

1. Az **integrációs**fiók **áttekintése lapján,** az Összetevők csoportban válassza a **Séma** csempét.

1. A **Séma** lap megnyitása után válassza ki a sémát. 
   Először töltse le és töltse le a sémát, válassza a **Letöltés**lehetőséget, és mentse a sémát.

1. Ha készen áll a frissített séma feltöltésére, a **Séma** lapon jelölje ki a frissíteni kívánt sémát, és válassza a **Frissítés gombot.**

1. Keresse meg és jelölje ki a feltölteni kívánt frissített sémát. 
   Miután a sémafájl feltöltése befejeződik, a frissített séma megjelenik a **Sémák** listájában.

## <a name="delete-schemas"></a>Sémák törlése

1. Az <a href="https://portal.azure.com" target="_blank">Azure Portalon</a>keresse meg és nyissa meg az integrációs fiókot, ha még nem nyitott.

1. Az Azure főmenüjében válassza a **Minden szolgáltatás lehetőséget.** 
   A keresőmezőbe írja be az "integrációs fiók" kifejezést. 
   Válassza **az Integrációs fiókok lehetőséget.**

1. Válassza ki azt az integrációs fiókot, amelyen törölni szeretné a sémát.

1. Az **integrációs**fiók **áttekintése lapján,** az Összetevők csoportban válassza a **Séma** csempét.

1. A **Séma** lap megnyitása után jelölje ki a sémát, és válassza a **Törlés gombot.**

1. A séma törlésének megerősítéséhez válassza az **Igen**lehetőséget.

## <a name="next-steps"></a>További lépések

* [További információ az Enterprise Integration Pack csomagról](logic-apps-enterprise-integration-overview.md)
* [További információ a térképekről](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [További információ az átalakításokról](../logic-apps/logic-apps-enterprise-integration-transform.md)
