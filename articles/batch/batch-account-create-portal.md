---
title: Fiók létrehozása az Azure Portalon - Azure Batch | Microsoft dokumentumok
description: Megtudhatja, hogyan hozhat létre Azure Batch-fiókot az Azure Portalon nagyméretű párhuzamos számítási feladatok futtatásához a felhőben
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3723631609a04f6d12abcaac1f9d7733bf3caa01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247643"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Batch-fiók létrehozása az Azure Portalon

Megtudhatja, hogyan hozhat létre Azure Batch-fiókot az [Azure Portalon][azure_portal], és miként választhatja ki a számítási forgatókönyvének legmegfelelőbb fióktulajdonságokat. Megtudhatja, hol találja a fontos fióktulajdonságokat, például a hozzáférési kulcsokat és a fiókok URL-címeit.

További ismereteket a Batch-fiókokról és -forgatókönyvekről a [funkciók áttekintésében](batch-api-basics.md) találhat.

## <a name="create-a-batch-account"></a>Batch-fiók létrehozása

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Jelentkezzen be az [Azure Portalra.][azure_portal]

1. Válassza **az Erőforrás** > **számítási** > kötegszolgáltatás létrehozása**lehetőséget.**

    ![Batch a Piactéren][marketplace_portal]

1. Adja meg az **Új Batch-fiók** beállításait. Lásd az alábbi részleteket.

    ![Batch-fiók létrehozása][account_portal]

    a. **Előfizetés**: A Batch-fiók létrehozására szolgáló előfizetés. Ha csak egy előfizetéssel rendelkezik, ez alapértelmezés szerint be van jelölve.

    b. **Erőforráscsoport**: Kiválaszthat egy meglévő erőforráscsoportot az új Batch-fiókhoz, vagy újat is létrehozhat.

    c. **Fióknév**: A választott névnek egyedinek kell lennie abban az Azure-régióban, amelyben az új fiókot létrehozza (lásd alább a **Hely** beállítást). A fiók neve csak kisbetűket vagy számokat tartalmazhat, és 3–24 karakter hosszúnak kell lennie.

    d. **Hely**: Az az Azure-régió, amelyben a Batch-fiókot létrehozza. Csak az előfizetése és az erőforráscsoportja által támogatott régiók jelennek meg lehetőségként.

    e. **Tárfiók:** Egy opcionális Azure Storage-fiók, amely a Batch-fiókhoz társítva. A legjobb teljesítmény érdekében ajánlott egy általános célú v2 tárfiók használata. A Batch összes tárfiók-beállítását a [Köteg szolgáltatás áttekintése című témakörben találja.](batch-api-basics.md#azure-storage-account) A portálon válasszon ki egy meglévő tárfiókot, vagy hozzon létre egy újat.

      ![Create a storage account][storage_account]

    f. **Készletfoglalási mód**: A **Speciális** beállítások lapon **kötegelt szolgáltatásként** vagy **felhasználói előfizetésként**adhatja meg a készletkiosztási módot. A legtöbb esetben fogadja el az alapértelmezett **Batch szolgáltatást.**

      ![Kötegkészlet felosztási módja][pool_allocation]

1. A fiók létrehozásához kattintson a **Létrehozás** gombra.

## <a name="view-batch-account-properties"></a>Batch-fiók tulajdonságainak megtekintése

A fiók létrehozása után jelölje ki azt a beállításai és tulajdonságai eléréséhez. Az összes fiókbeállítást és tulajdonságot elérheti a bal oldali menüből.

![A Batch-fiók lap az Azure Portalon][account_blade]

* **Batch-fiók neve, URL-címe és kulcsai**: Amikor egy alkalmazást fejleszt a [Batch API-kkal](batch-apis-tools.md#azure-accounts-for-batch-development), szüksége lesz a fiók URL-címére és kulcsára a Batch-erőforrások eléréséhez. (A Batch az Azure Active Directory-hitelesítés használatát is támogatja.)

    A Batch-fiók hozzáférési információnak megtekintéséhez kattintson a **Kulcsok** lehetőségre.

    ![A Batch-fiók kulcsai az Azure Portalon][account_keys]

* A Batch-fiókkal társított tárfiók nevének és kulcsainak megtekintéséhez kattintson a **Storage-fiók** elemre.

* A Batch-fiókra vonatkozó erőforráskvóták megtekintéséhez kattintson a **Kvóták** elemre. További részletek a [Batch szolgáltatás kvótáit és korlátozásait](batch-quota-limit.md) ismertető cikkben tekinthet meg.

## <a name="additional-configuration-for-user-subscription-mode"></a>További konfiguráció a felhasználói előfizetés módhoz

Ha felhasználói előfizetési módban szeretne létrehozni Batch-fiókot, végezze el az alábbi kiegészítő lépéseket a fiók létrehozása előtt.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Az előfizetés elérésének engedélyezése az Azure Batch számára (egyszeri művelet)

Amikor először hoz létre Batch-fiókot felhasználói előfizetés módban, regisztrálja az előfizetését a Batch szolgáltatásban. (Ha korábban már regisztrált, ugorjon a következő szakaszra.)

1. Jelentkezzen be az [Azure Portalra.][azure_portal]

1. Válassza a **Minden szolgáltatás** > **előfizetése**lehetőséget, és válassza ki a Batch-fiókhoz használni kívánt előfizetést.

1. Az **Előfizetés** lapon válassza az **Erőforrás-szolgáltatók** elemet, majd keressen rá a **Microsoft.Batch** kifejezésre. Ellenőrizze, hogy a **Microsoft.Batch** erőforrás-szolgáltató regisztrálva van-e az előfizetésben. Ha az előfizetés nincs regisztrálva, kattintson a **Regisztrálás** hivatkozásra.

    ![A Microsoft.Batch szolgáltató regisztrálása][register_provider]

1. Az **Előfizetés** lapon válassza a **Hozzáférés-vezérlési (IAM)** > **szerepkör-hozzárendelések** > **szerepkör-hozzárendelése**lehetőséget.

    ![Az előfizetéshez való hozzáférés vezérlése][subscription_access]

1. A **Szerepkör-hozzárendelés hozzáadása** lapon válassza ki a **Közreműködői** szerepkört, és keresse meg a Batch API-t. Keressen rá az egyes sztringekre, addig amíg meg nem találja az API-t:
    1. **MicrosoftAzureBatch**.
    1. **Microsoft Azure Batch**. Újabb Azure AD-bérlők ezt a nevet használhatják.
    1. A **ddbf3205-c6bd-46ae-8127-60eb93363864** a Batch API azonosítója.

1. Miután megtalálta a Batch API-t, jelölje ki, majd kattintson a **Mentés** gombra.

    ![Batch-engedélyek hozzáadása][add_permission]

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Felhasználói előfizetés módban olyan Azure Key Vault szükséges, amely ugyanahhoz az erőforráscsoporthoz tartozik, mint a létrehozandó Batch-fiók. Győződjön meg arról, hogy az erőforráscsoport olyan régióban található, amelyben [elérhető](https://azure.microsoft.com/regions/services/) a Batch szolgáltatás, és amelyet támogat az előfizetése.

1. Az [Azure Portalon][azure_portal] válassza az **Új** > **Biztonság** > **Key Vault** lehetőséget.

1. A **Kulcstartó létrehozása** lapon adja meg a kulcstartó nevét, és hozzon létre a régióban egy erőforráscsoportot a Batch-fiókhoz. A többi beállításnál hagyja meg az alapértelmezett értékeket, majd válassza a **Létrehozás** elemre.

A Batch-fiók felhasználói előfizetési módban történő létrehozásakor használja a key vault erőforráscsoportját. Adja meg a **felhasználói előfizetést** készletfoglalási módként, jelölje ki a key vaultot, és jelölje be a jelölőnégyzetet, ha hozzáférést szeretne adni az Azure Batch-nek a key vaulthoz. 

Ha manuálisan szeretné megadni a hozzáférést a kulcstartóhoz, nyissa meg a key vault **Access szabályzatok** szakaszát, és válassza a **Hozzáférési szabályzat hozzáadása lehetőséget,** és keresse meg a **Microsoft Azure Batch elemet.** Miután kiválasztotta, a **titkos engedélyeket** a legördülő menüvel kell konfigurálnia. Az Azure Batch-nek meg kell adni egy minimális **Get**, **List**, **Set**, és **Delete** engedélyeket.

![Titkos engedélyek az Azure Batch-hez](./media/batch-account-create-portal/secret-permissions.png)


> [!NOTE]
> Győződjön meg arról, hogy az **Azure virtuális gépek üzembe helyezéshez** és az Azure Resource Manager a sablon üzembe **helyezéséhez** jelölőnégyzetek be vannak jelölve a csatolt **Key Vault-erőforrás** **Access-szabályzatai** területen.
> 
> ![Kötelező key vault](./media/batch-account-create-portal/key-vault-access-policy.png) hozzáférési szabályzat Ez nem kötelező, ha az Azure Portalon kötegelt fiókot hoz létre. Alapértelmezés szerint a beállítás van kiválasztva.



### <a name="configure-subscription-quotas"></a>Előfizetési kvóták konfigurálása

Az alapkvóták alapértelmezés szerint nincsenek beállítva a felhasználói előfizetési batch fiókokon. Az alapkvótákat manuálisan kell beállítani, mert a szabványos Batch-magkvóták nem vonatkoznak a felhasználói előfizetési módban lévő fiókokra.

1. Az [Azure Portalon][azure_portal]válassza ki a felhasználói előfizetési mód Batch-fiók a beállítások és a tulajdonságok megjelenítéséhez.

1. A bal oldali **menüben** válassza a Kvóták lehetőséget a Batch-fiókhoz társított alapkvóták megtekintéséhez és konfigurálásához.

A [Batch szolgáltatási kvóták és korlátok](batch-quota-limit.md) a felhasználói előfizetési mód alapvető kvótáival kapcsolatos további információkért tekintse meg.

## <a name="other-batch-account-management-options"></a>Egyéb Batch-fiókkezelési lehetőségek

Az Azure Portal használata mellett a többek között a következő eszközökkel is létrehozhat és kezelhet Batch-fiókokat:

* [Batch – PowerShell-parancsmagok](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>További lépések

* A Batch szolgáltatás fogalmairól és funkcióiról további információt [a Batch funkcióinak áttekintésében](batch-api-basics.md) talál. A cikk az elsődleges Batch-erőforrásokat tárgyalja, például a készleteket, a számítási csomópontokat, a feladatokat, a tevékenységeket, és áttekintést nyújt a szolgáltatás nagyméretű számítási feladatokkal kapcsolatos funkcióiról.
* Megismerheti a Batch-kompatibilis alkalmazások [Batch .NET ügyfélkönyvtárral](quick-run-dotnet.md) vagy [Python](quick-run-python.md) segítségével való fejlesztésének alapjait. Ezek a rövid útmutatók végigvezetik egy mintaalkalmazáson, amely a Batch szolgáltatással futtat egy számítási feladatot több számítási csomóponton, és az Azure Storage szolgáltatást is használja a számítási feladatok fájljainak előkészítéséhez és lekéréséhez.

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace-batch.png
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png
