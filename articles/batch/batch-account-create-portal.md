---
title: Fiók létrehozása a Azure Portalban
description: Megtudhatja, hogyan hozhat létre Azure Batch-fiókot az Azure Portalon nagyméretű párhuzamos számítási feladatok futtatásához a felhőben
ms.topic: how-to
ms.date: 06/10/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1205de2b800588b735aeb20d388ba4b64bc6b078
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84711340"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Batch-fiók létrehozása az Azure Portalon

Ez a témakör bemutatja, hogyan hozhat létre Azure Batch fiókot a [Azure Portalban](https://portal.azure.com), és válassza ki a számítási forgatókönyvnek megfelelő fiók tulajdonságait. Emellett megtudhatja, hol találhatók a fontos fiók tulajdonságai, például a hozzáférési kulcsok és a fiók URL-címei.

A Batch-fiókokkal és-forgatókönyvekkel kapcsolatos háttérért lásd: [Batch szolgáltatás munkafolyamata és erőforrásai](batch-service-workflow-features.md).

## <a name="create-a-batch-account"></a>Batch-fiók létrehozása

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza **az erőforrás létrehozása**, majd a **számítás** és a **Batch szolgáltatás**elemet.

1. Adja meg az **Új Batch-fiók** beállításait. Lásd az alábbi részleteket.

    ![Batch-fiók létrehozása][account_portal]

    a. **Előfizetés**: A Batch-fiók létrehozására szolgáló előfizetés. Ha csak egy előfizetéssel rendelkezik, ez alapértelmezés szerint be van jelölve.

    b. **Erőforráscsoport**: Kiválaszthat egy meglévő erőforráscsoportot az új Batch-fiókhoz, vagy újat is létrehozhat.

    c. **Fióknév**: A választott névnek egyedinek kell lennie abban az Azure-régióban, amelyben az új fiókot létrehozza (lásd alább a **Hely** beállítást). A fiók neve csak kisbetűket vagy számokat tartalmazhat, és 3–24 karakter hosszúnak kell lennie.

    d. **Hely**: Az az Azure-régió, amelyben a Batch-fiókot létrehozza. Csak az előfizetése és az erőforráscsoportja által támogatott régiók jelennek meg lehetőségként.

    e. **Storage-fiók**: egy opcionális Azure Storage-fiók, amelyet a Batch-fiókhoz társít. A legjobb teljesítmény érdekében ajánlott az általános célú v2 Storage-fiók használata. A Batch összes Storage-fiókjának beállításairól a [Batch funkcióinak áttekintése](accounts.md#azure-storage-accounts)című témakörben talál további információt. A portálon válasszon ki egy meglévő Storage-fiókot, vagy hozzon létre egy újat.

      ![Tárfiók létrehozása][storage_account]

    f. **Készlet kiosztási módja**: a **speciális** beállítások lapon megadhatja a készlet lefoglalási módját **Batch szolgáltatásként** vagy **felhasználói előfizetésként**. A legtöbb esetben fogadja el az alapértelmezett **Batch szolgáltatást**.

      ![Batch-készlet lefoglalási módja][pool_allocation]

1. A fiók létrehozásához kattintson a **Létrehozás** gombra.

## <a name="view-batch-account-properties"></a>Batch-fiók tulajdonságainak megtekintése

A fiók létrehozása után jelölje ki azt a beállításai és tulajdonságai eléréséhez. Az összes fiókbeállítást és tulajdonságot elérheti a bal oldali menüből.

> [!NOTE]
> A Batch-fiók neve az azonosító, ezért nem módosítható. Ha módosítania kell egy batch-fiók nevét, törölnie kell a fiókot, és létre kell hoznia egy újat a kívánt névvel.

![A Batch-fiók lap az Azure Portalon][account_blade]

* **Batch-fiók neve, URL-címe és kulcsai**: Amikor egy alkalmazást fejleszt a [Batch API-kkal](batch-apis-tools.md#azure-accounts-for-batch-development), szüksége lesz a fiók URL-címére és kulcsára a Batch-erőforrások eléréséhez. (A Batch az Azure Active Directory-hitelesítés használatát is támogatja.)

    A Batch-fiók hozzáférési információnak megtekintéséhez kattintson a **Kulcsok** lehetőségre.

    ![A Batch-fiók kulcsai az Azure Portalon][account_keys]

* A Batch-fiókkal társított tárfiók nevének és kulcsainak megtekintéséhez kattintson a **Storage-fiók** elemre.

* A Batch-fiókra vonatkozó erőforráskvóták megtekintéséhez kattintson a **Kvóták** elemre. További részletek a [Batch szolgáltatás kvótáit és korlátozásait](batch-quota-limit.md) ismertető cikkben tekinthet meg.

## <a name="additional-configuration-for-user-subscription-mode"></a>További konfiguráció a felhasználói előfizetés módhoz

Ha felhasználói előfizetési módban szeretne létrehozni Batch-fiókot, végezze el az alábbi kiegészítő lépéseket a fiók létrehozása előtt.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Az előfizetés elérésének engedélyezése az Azure Batch számára (egyszeri művelet)

Amikor először hoz létre Batch-fiókot felhasználói előfizetés módban, regisztrálja az előfizetését a Batch szolgáltatásban. (Ha ezt már elvégezte, ugorjon a következő szakaszra.)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza a **minden szolgáltatás**  >  **előfizetések**lehetőséget, majd válassza ki a Batch-fiókhoz használni kívánt előfizetést.

1. Az **Előfizetés** lapon válassza az **Erőforrás-szolgáltatók** elemet, majd keressen rá a **Microsoft.Batch** kifejezésre. Ellenőrizze, hogy a **Microsoft.Batch** erőforrás-szolgáltató regisztrálva van-e az előfizetésben. Ha az előfizetés nincs regisztrálva, kattintson a **Regisztrálás** hivatkozásra.

    ![A Microsoft.Batch szolgáltató regisztrálása][register_provider]

1. Az **előfizetés** lapon válassza a **hozzáférés-vezérlés (iam)**  >  **szerepkör-hozzárendelések**  >  **szerepkör-hozzárendelés hozzáadása**elemet.

    ![Az előfizetéshez való hozzáférés vezérlése][subscription_access]

1. A **szerepkör-hozzárendelés hozzáadása** lapon válassza ki a **közreműködő** vagy **tulajdonos** szerepkört, majd keressen rá a Batch API kifejezésre. Keressen rá az egyes sztringekre, addig amíg meg nem találja az API-t:
    1. **MicrosoftAzureBatch**.
    1. **Microsoft Azure Batch**. Újabb Azure AD-bérlők ezt a nevet használhatják.
    1. A **ddbf3205-c6bd-46ae-8127-60eb93363864** a Batch API azonosítója.

1. Miután megtalálta a Batch API-t, jelölje ki, majd kattintson a **Mentés** gombra.

    ![Batch-engedélyek hozzáadása][add_permission]

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Felhasználói előfizetési módban szükség van egy [Azure Key Vaultra](../key-vault/general/overview.md) . A Key Vaultnak ugyanabban az előfizetésben és régióban kell lennie, mint a létrehozandó batch-fióknak. 

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Új** > **Biztonság** > **Key Vault** lehetőséget.

1. A **Key Vault létrehozása** lapon adja meg a Key Vault nevét, és hozzon létre egy erőforráscsoportot a Batch-fiókhoz használni kívánt régióban. A többi beállításnál hagyja meg az alapértelmezett értékeket, majd válassza a **Létrehozás** elemre.

Ha felhasználói előfizetési módban hozza létre a Batch-fiókot, adja meg a **felhasználói előfizetést** a készlet kiosztási módjaként, jelölje ki a Key Vault, és jelölje be a jelölőnégyzetet, és adja meg Azure batch hozzáférést a Key Vaulthoz.

Ha inkább a Key Vault elérését szeretné manuálisan megadni, lépjen a Key Vault **hozzáférési házirendek** szakaszára, válassza a **hozzáférési házirend hozzáadása** és a **Microsoft Azure batch**keresése lehetőséget. A kiválasztást követően konfigurálnia kell a **titkos engedélyeket** a legördülő menüből. Azure Batch legalább a **Get**, a **List**, a **set**és a **delete** engedélyeket kell megadni.

![A Azure Batchhoz tartozó titkos engedélyek](./media/batch-account-create-portal/secret-permissions.png)

> [!NOTE]
> Győződjön meg arról, hogy az **Azure Virtual Machines a központi telepítéshez** és a **Azure Resource Manager a sablon központi telepítéséhez** jelölőnégyzetek a társított **Key Vault** erőforráshoz tartozó **hozzáférési szabályzatok** területen vannak kiválasztva.
>
> ![Kötelező Key Vault hozzáférési szabályzat](./media/batch-account-create-portal/key-vault-access-policy.png)

### <a name="configure-subscription-quotas"></a>Előfizetési kvóták konfigurálása

A felhasználói előfizetés batch-fiókjaihoz manuálisan kell beállítani az alapszintű kvótákat. Standard szintű Batch fő kvóták nem vonatkoznak felhasználói előfizetési módban lévő fiókokra.

1. A [Azure Portal](https://portal.azure.com)válassza ki a felhasználói előfizetés mód batch-fiókját a beállítások és tulajdonságok megjelenítéséhez.
1. A bal oldali menüben válassza a **kvóták** lehetőséget a Batch-fiókhoz társított alapvető kvóták megtekintéséhez és konfigurálásához.

További információ a felhasználói előfizetési mód alapkvótákkal kapcsolatban: [Batch szolgáltatás kvótái és korlátai](batch-quota-limit.md).

## <a name="other-batch-account-management-options"></a>Egyéb Batch-fiókkezelési lehetőségek

Az Azure Portal használata mellett a többek között a következő eszközökkel is létrehozhat és kezelhet Batch-fiókokat:

* [Batch – PowerShell-parancsmagok](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>További lépések

* Ismerje meg a [Batch szolgáltatás munkafolyamatát és az elsődleges erőforrásokat](batch-service-workflow-features.md) , például a készleteket, a csomópontokat, a feladatokat és a feladatokat.
* Megismerheti a Batch-kompatibilis alkalmazások [Batch .NET ügyfélkönyvtárral](quick-run-dotnet.md) vagy [Python](quick-run-python.md) segítségével való fejlesztésének alapjait. Ezek a rövid útmutatók végigvezetik egy mintaalkalmazáson, amely a Batch szolgáltatással futtat egy számítási feladatot több számítási csomóponton, és az Azure Storage szolgáltatást is használja a számítási feladatok fájljainak előkészítéséhez és lekéréséhez.

[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png
