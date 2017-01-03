---
title: "Azure Batch-fiók létrehozása | Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre Azure Batch-fiókot az Azure Portalon nagyméretű párhuzamos számítási feladatok futtatásához a felhőben"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/19/2016
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 6f05aa3f06c8ec86d58cafd406c2752ddb2eecc1
ms.openlocfilehash: cf5b560028f3f80e3ba46ef96ae7a157dad7847d


---
# <a name="create-an-azure-batch-account-using-the-azure-portal"></a>Azure Batch-fiókok létrehozása az Azure Portalon
> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

Megtudhatja, hogyan hozhat létre Azure Batch-fiókot az [Azure Portalon][azure_portal], és hol találja a legfontosabb fióktulajdonságokat, például a hozzáférési kulcsokat és a fiókok URL-címeit. A Batch díjszabását is ismertetjük, valamint azt, hogyan csatolhat Azure Storage-fiókot a Batch-fiókjához, hogy [alkalmazáscsomagokat](batch-application-packages.md) használhasson, és [megőrizhesse a feladatok és tevékenységek kimenetét](batch-task-output.md).

## <a name="create-a-batch-account"></a>Batch-fiók létrehozása
1. Jelentkezzen be az [Azure Portalra][azure_portal].
2. Kattintson az **Új** > **Számítás** > **Batch-szolgáltatás** elemre.
   
    ![Batch a Piactéren][marketplace_portal]
3. Megjelenik az **Új Batch-fiók** panel. Az alábbi *a* – *e* részekben találja az egyes panelelemek leírását.
   
    ![Batch-fiók létrehozása][account_portal]
   
    a. **Fiók neve**: A Batch-fiók neve. A választott névnek egyedinek kell lennie az Azure-régióban, ahol az új fiókot létrehozza (lásd **Hely** alább). A fiók neve csak kisbetűket vagy számokat tartalmazhat, és 3–24 karakter hosszúnak kell lennie.
   
    b. **Előfizetés**: A Batch-fiók létrehozására szolgáló előfizetés. Ha csak egy előfizetéssel rendelkezik, ez alapértelmezés szerint be van jelölve.
   
    c. **Erőforráscsoport**: Kiválaszthat egy meglévő erőforráscsoportot az új Batch-fiókhoz, vagy újat is létrehozhat.
   
    d. **Hely**: Az az Azure-régió, amelyben a Batch-fiókot létrehozza. Csak az előfizetése és az erőforráscsoportja által támogatott régiók jelennek meg lehetőségként.
   
    e. **Tárfiók** (nem kötelező): Olyan általános célú Azure Storage-fiók, amelyet az új Batch-fiókhoz társít. További részletekért tekintse meg az alábbi, [Társított Azure Storage-fiók](#linked-azure-storage-account) című szakaszt.

4. A fiók létrehozásához kattintson a **Létrehozás** gombra.
   
   A portál jelzi, hogy **üzembe helyezi** a fiókot, és a befejezéskor egy **Üzembe helyezések sikeresek** értesítés jelenik meg az *Értesítések* területen.

## <a name="view-batch-account-properties"></a>Batch-fiók tulajdonságainak megtekintése
A fiók létrehozása után megnyithatja a **Batch-fiók panelt** a beállítások és tulajdonságok eléréséhez. Az összes fiókbeállítást és tulajdonságot elérheti a Batch-fiók panelének bal oldali menüjéből.

![A Batch-fiók panel az Azure Portalon][account_blade]

* **Batch-fiók URL-cím**: Amikor egy alkalmazást fejleszt a [Batch API-kkal](batch-technical-overview.md#batch-development-apis), szüksége lesz egy fiók URL-címére a Batch-erőforrások eléréséhez. A Batch-fiók URL-címének formátuma a következő:
  
    `https://<account_name>.<region>.batch.azure.com`

![A Batch-fiók URL-címe a portálon][account_url]

* **Hozzáférési kulcsok**: Ahhoz, hogy hozzáférjen a Batch-fiókhoz az alkalmazásból, szüksége lesz a fiók hozzáférési kulcsára. A Batch-fiók hozzáférési kulcsainak megtekintéséhez vagy újbóli létrehozásához írja be a `keys` kifejezést a bal oldali menü **Keresés** mezőjébe a Batch-fiók panelen, majd válassza a **Kulcsok** lehetőséget.
  
    ![A Batch-fiók kulcsai az Azure Portalon][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>Társított Azure Storage-fiók

Ahogy korábban említettük, dönthet úgy, hogy általános célú Azure Storage-fiókot csatol a Batch-fiókhoz. A Batch [alkalmazáscsomagok](batch-application-packages.md) funkciója Azure Blob Storage-ot használ, ahogyan a [Batch File Conventions .NET](batch-task-output.md) könyvtár is. Ezek a választható funkciók segítik a Batch-feladatok által futtatott alkalmazások üzembe helyezését és az általuk létrehozott adatok megőrzését.

Érdemes létrehozni egy új Storage-fiókot kifejezetten a Batch-fiók általi használatra.

![„Általános célú” tárfiók létrehozása][storage_account]

> [!NOTE] 
> Az Azure Batch jelenleg kizárólag az általános célú Storage-fiók típusát támogatja. Erről a fióktípusról leírást találhat az 5. lépésben [Create a storage account] (../storage/storage-create-storage-account.md#create-a-storage-account) a [Tudnivalók az Azure Storage-fiókokról](../storage/storage-create-storage-account.md) oldalon.
>
>

> [!WARNING]
> Körültekintően járjon el, amikor újból létrehozza a társított Storage-fiók hozzáférési kulcsait. A Storage-fiókhoz csak egy hozzáférési kulcsot hozzon létre ismét, és kattintson a társított tárfiók panelén a **Kulcsok szinkronizálása** gombra. Várjon öt percet, hogy a rendszer propagálja a hozzáférési kulcsokat a készletekben található számítási csomópontokra, majd szükség esetén hozza létre újra és szinkronizálja a másik hozzáférési kulcsot. Ha mindkét hozzáférési kulcsot egyszerre hozza létre újra, a számítási csomópontok nem tudják szinkronizálni egyiket sem, és elveszítik a Storage-fiókhoz való hozzáférést.
> 
> 

![A tárfiók hozzáférési kulcsainak ismételt létrehozása][4]

## <a name="batch-service-quotas-and-limits"></a>A Bach szolgáltatás kvótái és korlátozásai
Vegye figyelembe, hogy az Azure-előfizetéshez és más Azure-szolgáltatásokhoz hasonlóan a Batch-fiókokra is bizonyos [kvóták és korlátozások](batch-quota-limit.md) érvényesek. A Batch-fiókok aktuális kvótái a **Tulajdonságok** fiókban jelennek meg a portálon.

![Batch-fiókra vonatkozó kvóták az Azure Portalon][quotas]

Vegye figyelembe ezeket a kvótákat a Batch számítási feladatok tervezésekor és bővítésekor. Ha például a készlet nem éri el a számítási csomópontok megadott célszámát, lehet, hogy elérte a Batch-fiók magkvótájának határértékét.

A Batch-fiókok kvótája per régió per előfizetés szerint van megszabva, így alapértelmezés szerint több Batch-fiókkal is rendelkezhet, ha azok különböző régióban vannak. Több Batch számítási feladatot is futtathat egyetlen Batch-fiókon, de el is oszthatja a számítási feladatokat ugyanazon előfizetéshez, de különböző Azure-régiókhoz tartozó Batch-fiókok között.

Ezenkívül, sok kvóta egyszerűen növelhető az Azure Portalra elküldött ingyenes terméktámogatási kéréssel. A kvótanövelések kéréséről részletekért lásd: [Quotas and limits for the Azure Batch service](batch-quota-limit.md) (Az Azure Batch szolgáltatás kvótái és korlátai).

## <a name="other-batch-account-management-options"></a>Egyéb Batch-fiókkezelési lehetőségek
Az Azure Portal használata mellett a következőkkel is létrehozhat és kezelhet Batch-fiókokat:

* [Batch – PowerShell-parancsmagok](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](../xplat-cli-install.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Következő lépések
* A Batch szolgáltatás fogalmairól és funkcióiról további információért tekintse meg [az Azure Batch funkcióinak áttekintését](batch-api-basics.md). A cikk az elsődleges Batch-erőforrásokat tárgyalja, például a készleteket, a számítási csomópontokat, a feladatokat, a tevékenységeket és a szolgáltatás olyan funkcióinak áttekintését nyújtja, amely lehetővé teszi a nagy méretű számítási feladatok futtatását.
* Megismerheti a Batch-kompatibilis alkalmazások [Batch .NET ügyfélkönyvtárral](batch-dotnet-get-started.md) való fejlesztésének alapjait. A [bevezető cikk](batch-dotnet-get-started.md) végigvezeti egy működő alkalmazáson, amely a Batch szolgáltatással futtat számítási feladatot több számítási csomóponton, és az Azure Storage-et is használja a számítási feladatok fájlelőkészítéséhez és -lekéréséhez.

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



<!--HONumber=Jan17_HO1-->


