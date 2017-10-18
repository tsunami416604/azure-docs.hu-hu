---
title: "Batch-fiók létrehozása az Azure Portalon | Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre Azure Batch-fiókot az Azure Portalon nagyméretű párhuzamos számítási feladatok futtatásához a felhőben"
services: batch
documentationcenter: 
author: v-dotren
manager: timlt
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/28/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b7629e496f2d73798b94acdc611014a8b3afead7
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Batch-fiók létrehozása az Azure Portalon

> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
>
>

Megtudhatja, hogyan hozhat létre Azure Batch-fiókot az [Azure Portalon][azure_portal], és miként választhatja ki a számítási forgatókönyvének legmegfelelőbb fióktulajdonságokat. Megtudhatja, hol találja a fontos fióktulajdonságokat, például a hozzáférési kulcsokat és a fiókok URL-címeit.

További ismereteket a Batch-fiókokról és -forgatókönyvekről a [funkciók áttekintésében](batch-api-basics.md) találhat.



## <a name="create-a-batch-account"></a>Batch-fiók létrehozása



1. Jelentkezzen be az [Azure Portalra][azure_portal].
2. Kattintson az **Új** elemre, és keresse meg a Marketplace-en a **Batch szolgáltatást**.

    ![Batch a Piactéren][marketplace_portal]
3. Válassza ki a **Batch szolgáltatást**, kattintson a **Létrehozás** elemre, majd adja meg az **Új Batch-fiók** beállításait. Lásd az alábbi részleteket.

    ![Batch-fiók létrehozása][account_portal]

    a. **Fióknév**: A választott névnek egyedinek kell lennie abban az Azure-régióban, amelyben az új fiókot létrehozza (lásd alább a **Hely** beállítást). A fiók neve csak kisbetűket vagy számokat tartalmazhat, és 3–24 karakter hosszúnak kell lennie.

    b. **Előfizetés**: A Batch-fiók létrehozására szolgáló előfizetés. Ha csak egy előfizetéssel rendelkezik, ez alapértelmezés szerint be van jelölve.

    c. **Készletlefoglalási mód**: Ha ez a beállítás megjelenik, fogadja el az alapértelmezett **Batch szolgáltatást**.

    c. **Erőforráscsoport**: Kiválaszthat egy meglévő erőforráscsoportot az új Batch-fiókhoz, vagy újat is létrehozhat.

    d. **Hely**: Az az Azure-régió, amelyben a Batch-fiókot létrehozza. Csak az előfizetése és az erőforráscsoportja által támogatott régiók jelennek meg lehetőségként.

    e. **Tárfiók** (nem kötelező): Olyan általános célú Azure Storage-fiók, amelyet a Batch-fiókhoz társít. A legtöbb Batch-fiókhoz ajánlott a használata. Részletekért lásd az alábbi, a [társított Azure Storage-fiókról](#linked-azure-storage-account) szóló szakaszt.

4. A fiók létrehozásához kattintson a **Létrehozás** gombra.



## <a name="view-batch-account-properties"></a>Batch-fiók tulajdonságainak megtekintése
A fiók létrehozása után kattintson rá a beállításai és tulajdonságai eléréséhez. Az összes fiókbeállítást és tulajdonságot elérheti a bal oldali menüből.

![A Batch-fiók panel az Azure Portalon][account_blade]

* **Batch-fiók URL-címe**: Amikor egy alkalmazást fejleszt a [Batch API-kkal](batch-apis-tools.md#azure-accounts-for-batch-development), szüksége lesz a fiók URL-címére a Batch-erőforrások eléréséhez. A Batch-fiók URL-címének formátuma a következő:

    `https://<account_name>.<region>.batch.azure.com`

![A Batch-fiók URL-címe a portálon][account_url]

* **Hozzáférési kulcsok**: Ahhoz, hogy hozzáférjen a Batch-fiókhoz az alkalmazásból, használhat egy fiókhozzáférési kulcsot. (A Batch az Azure Active Directory-hitelesítés használatát is támogatja.)

    A hozzáférési kulcsok megtekintéséhez vagy újbóli létrehozásához válassza a **Kulcsok** elemet.

    ![A Batch-fiók kulcsai az Azure Portalon][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>Társított Azure Storage-fiók

Egy általános célú Azure Storage-fiókot társíthat a Batch-fiókjához, ami számos esetben hasznos lehet. A Batch [alkalmazáscsomagok](batch-application-packages.md) funkciója Azure Blob Storage-ot használ, ahogyan a [Batch File Conventions .NET](batch-task-output.md) könyvtár is. Ezek a választható funkciók segítik a Batch-feladatok által futtatott alkalmazások üzembe helyezését és az általuk létrehozott adatok megőrzését.

Érdemes létrehozni egy új Storage-fiókot kifejezetten a Batch-fiók általi használatra. Az Azure Batch jelenleg kizárólag az általános célú Storage-fiók típusát támogatja. Erről a fióktípusról [az Azure Storage-fiókokkal kapcsolatos tudnivalók](../storage/common/storage-create-storage-account.md) 5. lépésében, a [Tárfiók létrehozása](../storage/common/storage-create-storage-account.md#create-a-storage-account) alatt talál egy leírást.

![Általános célú tárfiók létrehozása][storage_account]

> [!NOTE]
> Körültekintően járjon el, amikor újból létrehozza a társított Storage-fiók hozzáférési kulcsait. A Storage-fiókhoz csak egy hozzáférési kulcsot hozzon létre ismét, és kattintson a társított tárfiók panelén a **Kulcsok szinkronizálása** gombra. Várjon öt percet, hogy a rendszer propagálja a hozzáférési kulcsokat a készletekben található számítási csomópontokra, majd szükség esetén hozza létre újra és szinkronizálja a másik hozzáférési kulcsot. Ha mindkét hozzáférési kulcsot egyszerre hozza létre újra, a számítási csomópontok nem tudják szinkronizálni egyiket sem, és elveszítik a Storage-fiókhoz való hozzáférést.
>
>

![A tárfiók hozzáférési kulcsainak ismételt létrehozása][4]

## <a name="batch-service-quotas-and-limits"></a>A Bach szolgáltatás kvótái és korlátozásai
Az Azure-előfizetéshez és más Azure-szolgáltatásokhoz hasonlóan a Batch-fiókokra is bizonyos [kvóták és korlátozások](batch-quota-limit.md) érvényesek. A Batch-fiókok aktuális kvótái a **Kvóták** alatt találhatók meg.

![Batch-fiókra vonatkozó kvóták az Azure Portalon][quotas]



Ezenkívül számos kvóta megnövelhető az Azure Portalra elküldött ingyenes terméktámogatási kéréssel. A kvótanövelések kéréséről részletekért lásd: [Quotas and limits for the Azure Batch service](batch-quota-limit.md) (Az Azure Batch szolgáltatás kvótái és korlátai).

## <a name="other-batch-account-management-options"></a>Egyéb Batch-fiókkezelési lehetőségek
Az Azure Portal használata mellett a következőkkel is létrehozhat és kezelhet Batch-fiókokat:

* [Batch – PowerShell-parancsmagok](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Következő lépések
* A Batch szolgáltatás fogalmairól és funkcióiról további információt [a Batch funkcióinak áttekintésében](batch-api-basics.md) talál. A cikk az elsődleges Batch-erőforrásokat tárgyalja, például a készleteket, a számítási csomópontokat, a feladatokat, a tevékenységeket, és áttekintést nyújt a szolgáltatás nagyméretű számítási feladatokkal kapcsolatos funkcióiról.
* Megismerheti a Batch-kompatibilis alkalmazások [Batch .NET ügyfélkönyvtárral](batch-dotnet-get-started.md) vagy [Python](batch-python-tutorial.md) segítségével való fejlesztésének alapjait. Ezek a bevezető cikkek végigvezetik egy működő alkalmazáson, amely a Batch szolgáltatással futtat egy számítási feladatot több számítási csomóponton, és az Azure Storage szolgáltatást is használja a számítási feladatok fájljainak előkészítéséhez és lekéréséhez.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "A tárfiók hozzáférési kulcsainak ismételt létrehozása"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[account_portal_byos]: ./media/batch-account-create-portal/batch_acct_portal_byos.png
