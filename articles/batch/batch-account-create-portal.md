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
ms.date: 11/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ebda2f11f93b04a5592d18f8e15c8fc3b560aac3
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
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

> [!NOTE]
> Egy Batch-fiók létrehozásakor általában érdemes az alapértelmezett **Batch szolgáltatás** módot választani, amelynek keretében a készleteket a rendszer a háttérben foglalja le az Azure által felügyelt előfizetésekben. A szintén választható **felhasználói előfizetés** mód esetében, amelynek használata a legtöbb forgatókönyvben már nem javasolt, a Batch virtuális gépei és egyéb erőforrásai közvetlenül az előfizetésben jönnek létre egy készlet létrehozásakor. Ha felhasználói előfizetési módban szeretne létrehozni Batch-fiókot, az előfizetését az Azure Batchben is regisztrálnia kell, és egy Azure Key Vaulttal is társítania kell.

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

![A Batch-fiók lap az Azure Portalon][account_blade]

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
> Körültekintően járjon el, amikor újból létrehozza a társított Storage-fiók hozzáférési kulcsait. A Storage-fiókhoz csak egy hozzáférési kulcsot hozzon létre ismét, és kattintson a társított tárfiók lapján a **Kulcsok szinkronizálása** gombra. Várjon öt percet, hogy a rendszer propagálja a hozzáférési kulcsokat a készletekben található számítási csomópontokra, majd szükség esetén hozza létre újra és szinkronizálja a másik hozzáférési kulcsot. Ha mindkét hozzáférési kulcsot egyszerre hozza létre újra, a számítási csomópontok nem tudják szinkronizálni egyiket sem, és elveszítik a Storage-fiókhoz való hozzáférést.
>
>

![A tárfiók hozzáférési kulcsainak ismételt létrehozása][4]

## <a name="additional-configuration-for-user-subscription-mode"></a>További konfiguráció a felhasználói előfizetés módhoz

Ha felhasználói előfizetési módban szeretne létrehozni Batch-fiókot, végezze el az alábbi kiegészítő lépéseket a fiók létrehozása előtt.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Az előfizetés elérésének engedélyezése az Azure Batch számára (egyszeri művelet)
Amikor először hoz létre Batch-fiókot felhasználói előfizetés módban, regisztrálja az előfizetését a Batch szolgáltatásban. (Ha korábban már regisztrált, ugorjon a következő szakaszra.)

1. Jelentkezzen be az [Azure Portalra][azure_portal].

2. Kattintson a **További szolgáltatások** > **Előfizetések** elemre, majd a Batch-fiókhoz használni kívánt előfizetésre.

3. Az **Előfizetés** lapon kattintson a **Hozzáférés-vezérlés (IAM)** > **Hozzáadás** elemre.

    ![Az előfizetéshez való hozzáférés vezérlése][subscription_access]

4. Az **Engedélyek hozzáadása** lapon válassza a **Közreműködő** szerepkört, és keressen rá a Batch API kifejezésre. Keressen rá az egyes karakterláncokra, addig amíg meg nem találja az API-t:
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. Újabb Azure AD-bérlők ezt a nevet használhatják.
    3. A **ddbf3205-c6bd-46ae-8127-60eb93363864** a Batch API azonosítója. 

5. Miután megtalálta a Batch API-t, jelölje ki, majd kattintson a **Mentés** gombra.

    ![Batch-engedélyek hozzáadása][add_permission]

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása
Felhasználói előfizetés módban olyan Azure Key Vault szükséges, amely ugyanahhoz az erőforráscsoporthoz tartozik, mint a létrehozandó Batch-fiók. Győződjön meg arról, hogy az erőforráscsoport olyan régióban található, amelyben [elérhető](https://azure.microsoft.com/regions/services/) a Batch szolgáltatás, és amelyet támogat az előfizetése.

1. Az [Azure Portalon][azure_portal] kattintson az **Új** > **Biztonság és identitás** > **Kulcstartó** elemre.

2. A **Kulcstartó létrehozása** lapon adja meg a kulcstartó nevét, és hozzon létre a régióban egy erőforráscsoportot a Batch-fiókhoz. A többi beállításnál hagyja meg az alapértelmezett értéket, majd kattintson a **Létrehozás** elemre.




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

