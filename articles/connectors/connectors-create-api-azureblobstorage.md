---
title: Csatlakozás az Azure Blob Storage-hoz
description: Blobok létrehozása és kezelése az Azure-tárfiókokban az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: eb943bfe36be10d1e95d569a5c1bf48563e909c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247357"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Blobok létrehozása és kezelése az Azure Blob Storage-ban az Azure Logic Apps használatával

Ez a cikk bemutatja, hogyan érheti el és kezelheti a blobokként tárolt fájlokat az Azure storage-fiókjában egy logikai alkalmazáson belülről az Azure Blob Storage-összekötővel. Így olyan logikai alkalmazásokat hozhat létre, amelyek automatizálják a fájlok kezeléséhez szükséges feladatokat és munkafolyamatokat. Létrehozhat például olyan logikai alkalmazásokat, amelyek létrehozzák, lekérik, frissítik és törlik a tárfiókban lévő fájlokat.

Tegyük fel, hogy rendelkezik egy Azure-webhelyen frissített eszközzel. amely a logikai alkalmazás eseményindítójaként működik. Ha ez az esemény történik, a logikai alkalmazás frissíthet néhány fájlt a blob storage tárolóban, amely egy művelet a logikai alkalmazásban.

Ha most kezdi meg a logikai alkalmazásokat, tekintse át [az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a Rövid útmutató: Az első logikai alkalmazás létrehozása című ismertetése című ismertetése című ismertetése. [Quickstart: Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md) Az összekötő-specifikus műszaki információk az [Azure Blob Storage-összekötő referencia.](https://docs.microsoft.com/connectors/azureblobconnector/)

> [!IMPORTANT]
> A logikai alkalmazások nem férhetnek hozzá közvetlenül a tűzfalak mögött idús tárfiókokhoz, ha mindkettő ugyanabban a régióban található. Kerülő megoldásként a logikai alkalmazások és a tárfiók különböző régiókban. Az Azure Logic Apps-ből a tűzfalak mögötti tárfiókokhoz való hozzáférés engedélyezéséről a jelen témakör későbbi részében [található.](#storage-firewalls)

<a name="blob-storage-limits"></a>

## <a name="limits"></a>Korlátok

* Alapértelmezés szerint az Azure Blob Storage-műveletek *50 MB vagy kisebb*fájlokat tudnak olvasni vagy írni. Az 50 MB-nál nagyobb, de legfeljebb 1024 MB méretű fájlok kezeléséhez az Azure Blob Storage-műveletek támogatják [az üzenetdarabolást.](../logic-apps/logic-apps-handle-large-messages.md) A **Blob tartalomának beszerezni** művelet implicit módon használja a darabolás.

* Az Azure Blob Storage eseményindítók nem támogatják a darabolást. Fájltartalom igénylésekor az eseményindítók csak az 50 MB-os vagy kisebb fájlokat jelölik ki. Az 50 MB-nál nagyobb fájlok beletöltéséhez kövesse az alábbi mintát:

  * Használjon egy Azure Blob Storage-eseményindítót, amely fájltulajdonságokat ad vissza, például **ha egy blobot adnak hozzá vagy módosítanak (csak tulajdonságok)**.

  * Kövesse az eseményindítót az Azure Blob Storage **Get blob tartalom** művelet, amely beolvassa a teljes fájlt, és implicit módon használja darabolás.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* [Azure-tárfiók és tároló](../storage/blobs/storage-quickstart-blobs-portal.md)

* A logikai alkalmazás, ahol az Azure blob storage-fiók eléréséhez kell hozzáférni. A logikai alkalmazás Azure Blob Storage-eseményindítóval való elindításához [egy üres logikai alkalmazásra](../logic-apps/quickstart-create-first-logic-app-workflow.md)van szükség.

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Blob-tároló eseményindítójának hozzáadása

Az Azure Logic Apps minden logikai alkalmazás kell kezdeni egy [eseményindító,](../logic-apps/logic-apps-overview.md#logic-app-concepts)amely egy adott esemény bekövetkezésekor, vagy ha egy adott feltétel teljesül. Minden alkalommal, amikor az eseményindító aktiválódik, a Logic Apps motor létrehoz egy logikai alkalmazáspéldányt, és megkezdi az alkalmazás munkafolyamatának futtatását.

Ebben a példában bemutatja, hogyan indíthat el egy logikai alkalmazás munkafolyamata a **Blob hozzáadásakor vagy módosításakor (csak tulajdonságok)** eseményindító, amikor egy blob tulajdonságait hozzáadják vagy frissítik a tárolóban.

1. Az [Azure Portalon](https://portal.azure.com) vagy a Visual Studio,hozzon létre egy üres logikai alkalmazás, amely megnyitja a Logic App Designer. Ez a példa az Azure Portalt használja.

2. A keresőmezőbe írja be szűrőként az "azure blob" kifejezést. Az eseményindítók listájában válassza ki a kívánt eseményindítót.

   Ez a példa ezt az eseményindítót használja: **Blob hozzáadásakor vagy módosításakor (csak tulajdonságok)**

   ![Válassza az Azure Blob Storage eseményindítóját](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. Ha a rendszer kéri a kapcsolat részleteit, [hozza létre a blob storage-kapcsolatot most.](#create-connection) Vagy ha a kapcsolat már létezik, adja meg az eseményindítóhoz szükséges információkat.

   Ebben a példában jelölje ki a figyelni kívánt tárolót és mappát.

   1. A **Tároló** mezőben jelölje ki a mappa ikonját.

   2. A mappalistában válassza ki a derékszögű szögletes zárójelet ( **>** ), majd tallózással keresse meg és jelölje ki a kívánt mappát.

      ![Az eseményindítóval használni kívánt tárolómappa kiválasztása](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Adja meg, hogy milyen gyakran ellenőrizze az eseményindító a mappában a módosításokat.

4. Ha elkészült, a tervező eszköztárán válassza a **Mentés gombot.**

5. Most folytassa egy vagy több művelet hozzáadásával a logikai alkalmazáshoz az eseményindító eredményekkel végrehajtani kívánt feladatokhoz.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Blob storage hozzáadása művelet

Az Azure Logic Apps egy [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy lépés a munkafolyamatban, amely követi az eseményindító vagy egy másik művelet. Ebben a példában a logikai alkalmazás az [Ismétlődés eseményindítóval](../connectors/connectors-native-recurrence.md)kezdődik.

1. Az [Azure Portalon](https://portal.azure.com) vagy a Visual Studio, nyissa meg a logikai alkalmazást a Logic App Designer. Ez a példa az Azure Portalt használja.

2. A Logic App Designer ben az eseményindító vagy művelet alatt válassza az **Új lépés lehetőséget.**

   ![Új lépés hozzáadása a logikai alkalmazás munkafolyamatához](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   Ha műveletet szeretne hozzáadni a meglévő lépések közé, vigye az egeret a csatlakozó nyíl fölé. Válassza ki a**+** megjelenő pluszjelet ( ), és válassza **a Művelet hozzáadása**lehetőséget.

3. A keresőmezőbe írja be szűrőként az "azure blob" kifejezést. A műveletek listájában jelölje ki a kívánt műveletet.

   Ez a példa ezt a műveletet használja: **Blob-tartalom beszereznie**

   ![Az Azure Blob Storage-művelet kiválasztása](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. Ha a rendszer kéri a kapcsolat részleteit, [hozza létre most az Azure Blob Storage-kapcsolatot.](#create-connection)
Vagy ha a kapcsolat már létezik, adja meg a művelethez szükséges információkat.

   Ebben a példában jelölje ki a kívánt fájlt.

   1. A **Blob** mezőben jelölje ki a mappa ikonját.
  
      ![A művelettel használandó tárolómappa kiválasztása](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Keresse meg és jelölje ki a kívánt fájlt a blob **azonosítószáma** alapján. Ezt az **azonosítószámot** a blob metaadataiban találja, amelyet a korábban leírt blobstorage-eseményindító ad vissza.

5. Ha elkészült, a tervező eszköztárán válassza a **Mentés gombot.**
A logikai alkalmazás teszteléséhez győződjön meg arról, hogy a kijelölt mappa blobot tartalmaz.

Ebben a példában csak egy blob tartalmát kapja meg. A tartalom megtekintéséhez adjon hozzá egy másik műveletet, amely egy másik összekötő használatával hoz létre egy fájlt a blobtal. Például adjon hozzá egy OneDrive-műveletet, amely a blob tartalma alapján hoz létre egy fájlt.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Csatlakozás tárfiókhoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Amikor a rendszer kéri a kapcsolat létrehozását, adja meg a következő információkat:

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Kapcsolat neve** | Igen | <*kapcsolat neve*> | A kapcsolathoz létrehozandó név |
   | **Tárfiók** | Igen | <*tárfiók*> | Válassza ki a tárfiókot a listából. |
   ||||

   Példa:

   ![Azure Blob tárfiók-kapcsolat létrehozása](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. Ha készen áll, válassza a **Létrehozás lehetőséget.**

1. Miután létrehozta a kapcsolatot, folytassa a [Blob storage eseményindító hozzáadása](#add-trigger) vagy [blobstorage-hozzáadása művelet.](#add-action)

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötővel kapcsolatos további technikai részleteket, például az eseményindítókat, műveleteket és korlátokat az összekötő Swagger-fájlja szerint lásd az [összekötő referencialapján.](https://docs.microsoft.com/connectors/azureblobconnector/)

> [!NOTE]
> [Az integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)lévő logikai alkalmazások esetében az összekötő ISE-címkével ellátott verziója az [ISE-üzenetkorlátokat](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja.

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>Tárolófiókok elérése tűzfalak mögött

Az Azure-tárfiókhoz hálózati biztonságot adhat, ha tűzfal- [és tűzfalszabályokkal](../storage/common/storage-network-security.md)korlátozza a hozzáférést. Ez a beállítás azonban kihívást jelent az Azure és más Microsoft-szolgáltatások számára, amelyeknek hozzá kell férniük a tárfiókhoz. Az adatközpontban a helyi kommunikáció absztrakttá teszi a belső IP-címeket, így nem állíthat be IP-korlátozásokkal rendelkező tűzfalszabályokat. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../storage/common/storage-network-security.md).

Az alábbiakban különböző lehetőségeket kínál a tűzfalak mögötti tárfiókok elérésére az Azure Logic Apps-ből az Azure Blob Storage-összekötő vagy más megoldások használatával:

* Azure Storage Blob-összekötő

  * [Tárfiókok elérése más régiókban](#access-other-regions)
  * [Tárfiókok elérése megbízható virtuális hálózaton keresztül](#access-trusted-virtual-network)

* Egyéb megoldások

  * [Tárfiókok elérése felügyelt identitásokkal rendelkező megbízható szolgáltatásként](#access-trusted-service)
  * [Tárfiókok elérése az Azure API Management en keresztül](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>Problémák a tárfiókok elérésével ugyanabban a régióban

A logikai alkalmazások nem férhetnek hozzá közvetlenül a tűzfalak mögötti tárfiókokhoz, ha mindkettő ugyanabban a régióban található. Kerülő megoldásként helyezze a logikai alkalmazásokat a tárfióktól eltérő régióba, és adjon hozzáférést [a régió felügyelt összekötőinek kimenő IP-címeihez.](../logic-apps/logic-apps-limits-and-config.md#outbound)

> [!NOTE]
> Ez a megoldás nem vonatkozik az Azure Table Storage-összekötőre és az Azure Queue Storage-összekötőre. Ehelyett a table storage vagy a queue storage eléréséhez használja a beépített HTTP-eseményindítót és műveleteket.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>Tárfiókok elérése megbízható virtuális hálózaton keresztül

A tárfiókot egy Azure-beli virtuális hálózatba helyezheti, amelyet ön kezel, majd hozzáadhatja a virtuális hálózatot a megbízható virtuális hálózatok listájához. Ahhoz, hogy a logikai alkalmazás [egy megbízható virtuális hálózaton](../virtual-network/virtual-networks-overview.md)keresztül férjen hozzá a tárfiókhoz, telepítenie kell a logikai alkalmazást egy [integrációs szolgáltatási környezetbe (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)amely csatlakozhat a virtuális hálózat erőforrásaihoz. Ezután hozzáadhatja az ISE alhálózatait a megbízható listához. Az Azure Storage-összekötők, például a Blob Storage-összekötő, közvetlenül hozzáférhetnek a tárolótárolóhoz. Ez a beállítás ugyanaz a tapasztalat, mint az ISE szolgáltatásvégpontjainak használata.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>Tárfiókok elérése felügyelt identitásokkal rendelkező megbízható szolgáltatásként

Ha azt szeretné, hogy a Microsoft megbízható szolgáltatások tűzfalon keresztül férjenek hozzá egy tárfiókhoz, kivételt állíthat be az adott tárfiókon az adott szolgáltatásokhoz. Ez a megoldás lehetővé teszi az Azure-szolgáltatások, amelyek [támogatják a felügyelt identitások hitelesítéshez](../active-directory/managed-identities-azure-resources/overview.md) a tárolófiókok tűzfalak mögötti megbízható szolgáltatások eléréséhez. Pontosabban egy logikai alkalmazás a globális több-bérlős Azure-ban ezek a tárfiókok eléréséhez, először is engedélyezheti a [felügyelt identitás támogatása](../logic-apps/create-managed-service-identity.md) a logikai alkalmazás. Ezután használja a HTTP-műveletet vagy eseményindítót a logikai alkalmazásban, és [állítsa be a hitelesítési típusukat a logikai alkalmazás felügyelt identitásának használatára.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) Ebben a forgatókönyvben *csak* a HTTP-műveletet vagy eseményindítót használhatja.

A kivétel és a felügyelt identitástámogatás beállításához kövesse az alábbi általános lépéseket:

1. A tárfiók **Beállítások**területén válassza a **Tűzfalak és a virtuális hálózatok**lehetőséget. A **Hozzáférés engedélyezése a csoportban**jelölje be a Kijelölt **hálózatok** jelölőnégyzetet, hogy a kapcsolódó beállítások megjelenjenek.

1. A **Kivételek csoportban**jelölje be **A megbízható Microsoft-szolgáltatások hozzáférése a tárfiókhoz jelölőnégyzetet,** majd kattintson a **Mentés gombra.**

   ![Válassza ki a Microsoft megbízható szolgáltatásait engedélyező kivételt](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. A logikai alkalmazás beállításaiban [engedélyezze a felügyelt identitás támogatását.](../logic-apps/create-managed-service-identity.md)

1. A logikai alkalmazás munkafolyamatában adja hozzá és állítsa be a HTTP-műveletet vagy az eseményindítót a tárfiók vagy entitás eléréséhez.

   > [!IMPORTANT]
   > A kimenő HTTP-művelet vagy az Azure Storage-fiókok hívásai, győződjön meg arról, hogy a kérelem fejléce tartalmazza a `x-ms-version` tulajdonság és az API-verzió a művelet, amely a tárfiókon futtatni kívánt. További információ: [Hozzáférés hitelesítése felügyelt identitással](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) és [verziószámozással az Azure Storage-szolgáltatásokhoz.](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests)

1. Ezen a műveleten válassza ki a hitelesítéshez használandó [felügyelt identitást.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Tárfiókok elérése az Azure API Management en keresztül

Ha egy dedikált réteget használ az [API Managementhez,](../api-management/api-management-key-concepts.md)előadhatja a Storage API-t az API Management használatával, és engedélyezheti az utóbbi IP-címeit a tűzfalon keresztül. Alapvetően adja hozzá az API Management által használt Azure virtuális hálózatot a tárfiók tűzfalbeállításához. Ezután használhatja az API Management műveletet vagy a HTTP-műveletet az Azure Storage API-k hívásához. Ha azonban ezt a lehetőséget választja, a hitelesítési folyamatot saját kezűleg kell kezelnie. További információ: [Egyszerű vállalati integrációs architektúra.](https://aka.ms/aisarch)

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
