---
title: "Batch-fiók létrehozása az Azure Portalon | Microsoft Docs"
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
ms.date: 06/20/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 520d1d42d35b25db1a35d4317e9eb616cf5de565
ms.contentlocale: hu-hu
ms.lasthandoff: 08/28/2017

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

A portálon a két *készletfelosztási mód* egyikének használatával hozhat létre Batch-fiókot: a **Batch szolgáltatás** módban vagy az újabb, **felhasználói előfizetés** módban, amely több konfigurálást igényel. További információt a két módról a [funkciók áttekintésében](batch-api-basics.md#account) találhat. A felhasználói előfizetés mód funkcióiról a [blogbejegyzésben](https://blogs.technet.microsoft.com/windowshpc/2017/03/17/azure-batch-vnet-and-custom-image-support-for-virtual-machine-pools/) is olvashat.

## <a name="batch-service-mode"></a>Batch szolgáltatás mód



1. Jelentkezzen be az [Azure Portalra][azure_portal].
2. Kattintson az **Új** > **Számítás** > **Batch-szolgáltatás** elemre.

    ![Batch a Piactéren][marketplace_portal]
3. Megjelenik az **Új Batch-fiók** panel. Az egyes panelelemek leírását alább találja.

    ![Batch-fiók létrehozása][account_portal]

    a. **Fióknév**: A választott Batch-fióknévnek egyedinek kell lennie az Azure-régióban, amelyben az új fiókot létrehozza (lásd **Hely** alább). A fiók neve csak kisbetűket vagy számokat tartalmazhat, és 3–24 karakter hosszúnak kell lennie.

    b. **Előfizetés**: A Batch-fiók létrehozására szolgáló előfizetés. Ha csak egy előfizetéssel rendelkezik, ez alapértelmezés szerint be van jelölve.

    c. **Készletfelosztási mód**: Válassza a **Batch szolgáltatás** lehetőséget.

    c. **Erőforráscsoport**: Kiválaszthat egy meglévő erőforráscsoportot az új Batch-fiókhoz, vagy újat is létrehozhat.

    d. **Hely**: Az az Azure-régió, amelyben a Batch-fiókot létrehozza. Csak az előfizetése és az erőforráscsoportja által támogatott régiók jelennek meg lehetőségként.

    e. **Tárfiók** (nem kötelező): Olyan általános célú Azure Storage-fiók, amelyet a Batch-fiókhoz társít. A legtöbb Batch-fiókhoz ajánlott a használata. További részletekért tekintse meg a lentebb található [Társított Azure Storage-fiók](#linked-azure-storage-account) című szakaszt.

4. A fiók létrehozásához kattintson a **Létrehozás** gombra.

   A portál jelzi a folyamatban lévő üzembe helyezést. A befejezéskor **Az üzembe helyezések sikerültek** értesítés jelenik meg az **Értesítések** területen.

## <a name="user-subscription-mode"></a>Felhasználói előfizetés mód

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Az előfizetés elérésének engedélyezése az Azure Batch számára (egyszeri művelet)
Amikor először hoz létre Batch-fiókot felhasználói előfizetés módban, az alábbi lépéseket követve regisztrálja az előfizetését a Batch szolgáltatásban. (Ha korábban már regisztrált, ugorjon a következő szakaszra.)

1. Jelentkezzen be az [Azure Portalra][azure_portal].

2. Kattintson a **További szolgáltatások** > **Előfizetések** elemre, majd a Batch-fiókhoz használni kívánt előfizetésre.

3. Az **Előfizetés** panelen kattintson a **Hozzáférés-vezérlés (IAM)** > **Hozzáadás** lehetőségre.

    ![Az előfizetéshez való hozzáférés vezérlése][subscription_access]

4. Az **Engedélyek hozzáadása** panelen válassza a **Közreműködő** szerepkört, és keressen rá a Batch API kifejezésre. Keressen rá az egyes karakterláncokra, addig amíg meg nem találja az API-t:
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. Újabb Azure AD-bérlők ezt a nevet használhatják.
    3. A **ddbf3205-c6bd-46ae-8127-60eb93363864** a Batch API azonosítója. 

5. Miután megtalálta a Batch API-t, jelölje ki, majd kattintson a **Mentés** gombra.

    ![Batch-engedélyek hozzáadása][add_permission]

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása
Felhasználói előfizetés módban olyan Azure Key Vault szükséges, amely ugyanahhoz az erőforráscsoporthoz tartozik, mint a létrehozandó Batch-fiók. Győződjön meg arról, hogy az erőforráscsoport olyan régióban található, amelyben [elérhető](https://azure.microsoft.com/regions/services/) a Batch szolgáltatás, és amelyet támogat az előfizetése.

1. Az [Azure Portalon][azure_portal] kattintson az **Új** > **Biztonság és identitás** > **Kulcstartó** elemre.

2. A **Kulcstartó létrehozása** panelen adja meg a kulcstartó nevét, és hozzon létre a régióban egy erőforráscsoportot a Batch-fiókhoz. A többi beállításnál hagyja meg az alapértelmezett értéket, majd kattintson a **Létrehozás** elemre.

### <a name="create-a-batch-account"></a>Batch-fiók létrehozása

1. Az [Azure Portalon][azure_portal] kattintson az **Új** > **Számítás** > **Batch szolgáltatás** lehetőségre.

    ![Batch a Piactéren][marketplace_portal]
3. Megjelenik az **Új Batch-fiók** panel. Az egyes panelelemek leírását alább találja.

    ![Batch-fiók létrehozása][account_portal_byos]

    a. **Fióknév**: A választott Batch-fióknévnek egyedinek kell lennie az Azure-régióban, amelyben az új fiókot létrehozza (lásd **Hely** alább). A fiók neve csak kisbetűket vagy számokat tartalmazhat, és 3–24 karakter hosszúnak kell lennie.

    b. **Előfizetés**: Ha egynél több előfizetéssel rendelkezik, akkor válassza ki azt, amelyet a Batch szolgáltatáshoz regisztrált.

    c. **Készletfeldolgozási mód**: Válassza a **Felhasználói előfizetés** módot.

    d. **Kulcstartó**: válassza ki azt a kulcstartót, amelyet az előző szakaszban leírtak szerint hozott létre a Batch-fiókhoz. Szükség esetén hozzon létre egy új kulcstartót. A kulcstartó kiválasztása után a jelölőnégyzet bejelölésével engedélyezze az Azure Batch számára a kulcstartó elérését.

    c. **Erőforráscsoport**: Válassza ki azt az erőforráscsoportot, amelyben létrehozta a kulcstartót.

    d. **Hely**: Az az Azure-régió, amelyben létrehozta a Batch-fiókhoz tartozó kulcstartót.

    e. **Tárfiók** (nem kötelező): Olyan általános célú Azure Storage-fiók, amelyet a Batch-fiókhoz társít. A legtöbb Batch-fiókhoz ajánlott a használata. További részletekért tekintse meg az alábbi, [Társított Azure Storage-fiók](#linked-azure-storage-account) című szakaszt.

4. A fiók létrehozásához kattintson a **Létrehozás** gombra.

   A portál jelzi a folyamatban lévő üzembe helyezést. A befejezéskor **Az üzembe helyezések sikerültek** értesítés jelenik meg az **Értesítések** területen.



## <a name="view-batch-account-properties"></a>Batch-fiók tulajdonságainak megtekintése
A fiók létrehozása után megnyithatja a **Batch-fiók panelt** a beállítások és tulajdonságok eléréséhez. Az összes fiókbeállítást és tulajdonságot elérheti a Batch-fiók panelének bal oldali menüjéből.

![A Batch-fiók panel az Azure Portalon][account_blade]

* **Batch-fiók URL-cím**: Amikor egy alkalmazást fejleszt a [Batch API-kkal](batch-apis-tools.md#azure-accounts-for-batch-development), szüksége lesz egy fiók URL-címére a Batch-erőforrások eléréséhez. A Batch-fiók URL-címének formátuma a következő:

    `https://<account_name>.<region>.batch.azure.com`

![A Batch-fiók URL-címe a portálon][account_url]

* **Hozzáférési kulcsok** (Batch szolgáltatás mód): Ahhoz, hogy hozzáférjen a Batch-fiókhoz az alkalmazásból, szüksége lesz a fiók hozzáférési kulcsára. (Ez a beállítás nem áll rendelkezésre felhasználói előfizetés módban, amelyben Azure Active Directory-hitelesítést használ.)

    A Batch-fiók hozzáférési kulcsainak megtekintéséhez vagy újbóli létrehozásához írja be a `keys` kifejezést a bal oldali menü **Keresés** mezőjébe a Batch-fiók panelen, majd válassza a **Kulcsok** lehetőséget.

    ![A Batch-fiók kulcsai az Azure Portalon][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>Társított Azure Storage-fiók

Dönthet úgy, hogy általános célú Azure Storage-fiókot csatol a Batch-fiókhoz. A Batch [alkalmazáscsomagok](batch-application-packages.md) funkciója Azure Blob Storage-ot használ, ahogyan a [Batch File Conventions .NET](batch-task-output.md) könyvtár is. Ezek a választható funkciók segítik a Batch-feladatok által futtatott alkalmazások üzembe helyezését és az általuk létrehozott adatok megőrzését.

Érdemes létrehozni egy új Storage-fiókot kifejezetten a Batch-fiók általi használatra.

![Általános célú tárfiók létrehozása][storage_account]

> [!NOTE]
> Az Azure Batch jelenleg kizárólag az általános célú Storage-fiók típusát támogatja. Erről a fióktípusról a [Tudnivalók az Azure Storage-fiókokról](../storage/common/storage-create-storage-account.md) oldal 5. lépésében [Create a storage account] (../storage/common/storage-create-storage-account.md#create-a-storage-account) talál leírást.
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



Ezenkívül, sok kvóta egyszerűen növelhető az Azure Portalra elküldött ingyenes terméktámogatási kéréssel. A kvótanövelések kéréséről részletekért lásd: [Quotas and limits for the Azure Batch service](batch-quota-limit.md) (Az Azure Batch szolgáltatás kvótái és korlátai).

## <a name="other-batch-account-management-options"></a>Egyéb Batch-fiókkezelési lehetőségek
Az Azure Portal használata mellett a következőkkel is létrehozhat és kezelhet Batch-fiókokat:

* [Batch – PowerShell-parancsmagok](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Következő lépések
* A Batch szolgáltatás fogalmairól és funkcióiról további információt [a Batch funkcióinak áttekintésében](batch-api-basics.md) talál. A cikk az elsődleges Batch-erőforrásokat tárgyalja, például a készleteket, a számítási csomópontokat, a feladatokat, a tevékenységeket és a szolgáltatás olyan funkcióinak áttekintését nyújtja, amely lehetővé teszi a nagy méretű számítási feladatok futtatását.
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

