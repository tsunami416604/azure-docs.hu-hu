---
title: Batch-fiók létrehozása az Azure Portalon | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Azure Batch-fiókot az Azure Portalon nagyméretű párhuzamos számítási feladatok futtatásához a felhőben
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/18/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e526c5f2f446a1ae8134a3e2ca72fcc42024d904
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724094"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Batch-fiók létrehozása az Azure Portalon

Megtudhatja, hogyan hozhat létre Azure Batch-fiókot az [Azure Portalon][azure_portal], és miként választhatja ki a számítási forgatókönyvének legmegfelelőbb fióktulajdonságokat. Megtudhatja, hol találja a fontos fióktulajdonságokat, például a hozzáférési kulcsokat és a fiókok URL-címeit.

További ismereteket a Batch-fiókokról és -forgatókönyvekről a [funkciók áttekintésében](batch-api-basics.md) találhat.

## <a name="create-a-batch-account"></a>Batch-fiók létrehozása

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Jelentkezzen be az [Azure Portalra][azure_portal].

1. Válassza az **Erőforrás létrehozása** > **Számítás** > **Batch szolgáltatás** lehetőséget.

    ![Batch a Piactéren][marketplace_portal]

1. Adja meg az **Új Batch-fiók** beállításait. Lásd az alábbi részleteket.

    ![Batch-fiók létrehozása][account_portal]

    a. **Fióknév**: A választott névnek egyedinek kell lennie abban az Azure-régióban, amelyben az új fiókot létrehozza (lásd alább a **Hely** beállítást). A fiók neve csak kisbetűket vagy számokat tartalmazhat, és 3–24 karakter hosszúnak kell lennie.

    b. **Előfizetés**: A Batch-fiók létrehozására szolgáló előfizetés. Ha csak egy előfizetéssel rendelkezik, ez alapértelmezés szerint be van jelölve.

    c. **Erőforráscsoport**: Kiválaszthat egy meglévő erőforráscsoportot az új Batch-fiókhoz, vagy újat is létrehozhat.

    d. **Hely**: Az az Azure-régió, amelyben a Batch-fiókot létrehozza. Csak az előfizetése és az erőforráscsoportja által támogatott régiók jelennek meg lehetőségként.

    e. **Tárfiók** (nem kötelező): Egy Azure Storage-fiók, amelyet a Batch-fiókhoz társít. A legtöbb Batch-fiókhoz ajánlott a használata. A Batch szolgáltatásban elérhető tárfiók-lehetőségekről további információt [a Batch funkcióinak áttekintésében](batch-api-basics.md#azure-storage-account) talál. A portálon válasszon ki egy már létező tárfiókot, vagy hozzon létre egy újat.

      ![Tárfiók létrehozása][storage_account]

    f. **Készletlefoglalási mód**: A legtöbb forgatókönyvnél fogadja el az alapértelmezett **Batch szolgáltatást**.

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

1. Jelentkezzen be az [Azure Portalra][azure_portal].

1. Kattintson **Az összes szolgáltatás** > **Előfizetések** elemre, majd a Batch-fiókhoz használni kívánt előfizetésre.

1. Az **Előfizetés** lapon válassza az **Erőforrás-szolgáltatók** elemet, majd keressen rá a **Microsoft.Batch** kifejezésre. Ellenőrizze, hogy a **Microsoft.Batch** erőforrás-szolgáltató regisztrálva van-e az előfizetésben. Ha az előfizetés nincs regisztrálva, kattintson a **Regisztrálás** hivatkozásra.

    ![A Microsoft.Batch szolgáltató regisztrálása][register_provider]

1. Az a **előfizetés** lapon jelölje be **hozzáférés-vezérlés (IAM)** > **szerepkör-hozzárendelések** > **szerepkör-hozzárendeléshozzáadása**.

    ![Az előfizetéshez való hozzáférés vezérlése][subscription_access]

1. Az a **szerepkör-hozzárendelés hozzáadása** lapon válassza ki a **közreműködői** szerepkört, és keresse meg a Batch API-t. Keressen rá az egyes sztringekre, addig amíg meg nem találja az API-t:
    1. **MicrosoftAzureBatch**.
    1. **Microsoft Azure Batch**. Újabb Azure AD-bérlők ezt a nevet használhatják.
    1. A **ddbf3205-c6bd-46ae-8127-60eb93363864** a Batch API azonosítója. 

1. Miután megtalálta a Batch API-t, jelölje ki, majd kattintson a **Mentés** gombra.

    ![Batch-engedélyek hozzáadása][add_permission]

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása
Felhasználói előfizetés módban olyan Azure Key Vault szükséges, amely ugyanahhoz az erőforráscsoporthoz tartozik, mint a létrehozandó Batch-fiók. Győződjön meg arról, hogy az erőforráscsoport olyan régióban található, amelyben [elérhető](https://azure.microsoft.com/regions/services/) a Batch szolgáltatás, és amelyet támogat az előfizetése.

1. Az [Azure Portalon][azure_portal] válassza az **Új** > **Biztonság** > **Key Vault** lehetőséget.

1. A **Kulcstartó létrehozása** lapon adja meg a kulcstartó nevét, és hozzon létre a régióban egy erőforráscsoportot a Batch-fiókhoz. A többi beállításnál hagyja meg az alapértelmezett értékeket, majd válassza a **Létrehozás** elemre.

A Batch-fiók felhasználói előfizetés módban való létrehozásakor használja a Key Vault erőforráscsoportját, adja meg a **Felhasználói előfizetés** értéket a készletlefoglalás módjaként, és válassza ki a Key Vaultot.

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
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png

