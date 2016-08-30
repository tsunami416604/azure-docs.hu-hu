<properties
    pageTitle="Azure Batch-fiók létrehozása | Microsoft Azure"
    description="Megtudhatja, hogyan hozhat létre Azure Batch-fiókot az Azure Portalon nagyméretű párhuzamos számítási feladatok futtatásához a felhőben"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/01/2016"
    ms.author="marsma"/>

# Azure Batch-fiókok létrehozása és kezelése az Azure Portalon

> [AZURE.SELECTOR]
- [Azure portál](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)

Az [Azure Portal][azure_portal] biztosítja azokat az eszközöket, amelyek az Azure Batch-fiókok létrehozásához és kezeléséhez szükségesek. Ezek a fiókok nagy méretű párhuzamos számítási feladatok feldolgozásához használhatók. Ebben a cikkben végigvezetjük a Batch-fiókok a portállal való létrehozásának lépésein, és kiemeljük a Batch-fiókok fontos beállításait és tulajdonságait. A Batch segítségével fejlesztett alkalmazásoknak és szolgáltatásoknak például a fiók URL-címére és a hívóbetűre van szükségük a Batch szolgáltatás API-jaival való kommunikációhoz, és ezek az Azure Portalon találhatók meg.

>[AZURE.NOTE] Az Azure Portal jelenleg a Batch szolgáltatás funkcióinak egy részét támogatja, beleértve a fióklétrehozást, a Batch-fiókbeállítások és -tulajdonságok kezelését és a készletek és feladatok létrehozását és megfigyelését. A Batch teljes funkciókészlete a Batch API-kon keresztül érhető el a fejlesztők számára.

## Batch-fiók létrehozása

1. Jelentkezzen be az [Azure Portalra][azure_portal].

2. Kattintson az **Új** > **Virtuális gépek** > **Batch-szolgáltatás** elemre.

    ![Batch a Piactéren][marketplace_portal]

3. Megjelenik az **Új Batch-fiók** panel. Az alábbi *a*–*e* részekben találja az egyes panelelemek leírását.

    ![Batch-fiók létrehozása][account_portal]

    a. **Fiók neve**: A Batch-fiók egyedi neve. A névnek egyedinek kell lennie azon Azure-régióban, ahol a fiók létrejön (lásd az alábbi *Hely* szakaszt). Csak kisbetűket és számokat tartalmazhat, és 3–24 karakter hosszúnak kell lennie.

    b. **Előfizetés**: A Batch-fiók létrehozására szolgáló előfizetés. Ha csak egy előfizetéssel rendelkezik, ez alapértelmezés szerint be van jelölve.

    c. **Erőforráscsoport**: Az új Batch-fiók meglévő erőforráscsoportja, vagy újat is létrehozhat.

    d. **Hely**: Az az Azure-régió, amelyben a Batch-fiókot létrehozza. Csak az előfizetése és az erőforráscsoportja által támogatott régiók jelennek meg lehetőségként.

    e. **Tárfiók** (nem kötelező): Olyan **általános célú** tárfiók, amelyet az új Batch-fiókhoz társít (ahhoz kapcsol). A Batch [alkalmazáscsomagok](batch-application-packages.md) funkciója a társított tárfiókot használja az alkalmazáscsomagok tárolásához és lekéréséhez. A funkcióval kapcsolatban további információkért lásd: [Alkalmazástelepítés Azure Batch-alkalmazáscsomagokkal](batch-application-packages.md).

     > [AZURE.IMPORTANT] A kulcsok újbóli a társított tárfiókokban való létrehozásához speciális szempontokra van szükség. További részletekért tekintse meg az alábbi [A Batch-fiókokkal kapcsolatos szempontok](#considerations-for-batch-accounts) című szakaszt.

4. A fiók létrehozásához kattintson a **Létrehozás** gombra.

  A portál jelezni fogja, hogy **üzembe helyezi** a fiókot, és a befejezéskor egy **Üzembe helyezések sikeresek** értesítés jelenik meg az *Értesítések* területen.

## Batch-fiók tulajdonságainak megtekintése

A Batch-fiók panel a fiók több tulajdonságát jeleníti meg, valamint hozzáférést nyújt további beállításokhoz, például hívóbetűkhöz, kvótákhoz, felhasználókhoz és a tárfiók társításaihoz.

* **Batch-fiók URL-je**: Ez az URL-cím hozzáférést nyújt a Batch-fiókhoz API-k használatakor, például a [Batch REST][api_rest] API vagy a [Batch .NET][api_net] ügyfélkönyvtár használatakor, és a következő formátumot követi:

    `https://<account_name>.<region>.batch.azure.com`

* **Hívóbetűk**: A Batch-fiók hívóbetűinek megtekintéséhez és kezeléséhez kattintson a kulcs ikonra a **Kulcsok kezelése** panel megnyitásához, vagy kattintson az **Összes beállítás** > **Kulcsok** elemre. Hívóbetűre van szükség a Batch szolgáltatás API-jaival való kommunikációhoz, például a [Batch REST][api_rest] API-val vagy a [Batch .NET][api_net] ügyfélkönyvtárral való kommunikációhoz.

    ![A Batch-fiók kulcsai][account_keys]

* **Összes beállítás**: A Batch-fiók összes beállításának kezeléséhez vagy a tulajdonságainak megtekintéséhez kattintson az **Összes beállítás** elemre a **Beállítások** panel megnyitásához. Ez a panel hozzáférést nyújt a fiók összes beállításához és tulajdonságához, beleértve a fiókkvóták megtekintését, a Batch-fiókkal összekapcsolandó Azure Storage-fiók kiválasztását és a felhasználók kezelését.

    ![A Batch-fiók beállítás- és tulajdonságpaneljei][5]

## A Batch-fiókokkal kapcsolatos szempontok

* A [Batch PowerShell-parancsmagokkal](batch-powershell-cmdlets-get-started.md) és a [Batch Management .NET](batch-management-dotnet.md) könyvtárral is létrehozhat és kezelhet Batch-fiókokat.

* Magáért a Batch-fiókért nem kell fizetnie. Azokért az Azure számítási erőforrásokért számítunk fel díjat, amelyeket a Batch megoldások fogyasztanak, valamint a többi szolgáltatás által a számítási feladatok futtatásakor fogyasztott erőforrásokért. Fizetnie kell például a készletekben lévő számítási csomópontokért, és ha az [alkalmazáscsomagok](batch-application-packages.md) funkciót használja, fizetnie kell az alkalmazáscsomag-verziók tárolásához használt Azure Storage-erőforrásokért. További információkért lásd: [Batch díjszabás][batch_pricing].

* Több Batch számítási feladatot futtathat egyetlen Batch-fiókon, vagy eloszthatja a számítási feladatokat különböző Azure-régiókban lévő Batch-fiókok között.

* Ha több nagy méretű Batch számítási feladatot futtat, vegye figyelembe az Azure-előfizetésre és az egyes Batch-fiókokra érvényes bizonyos [Batch szolgáltatáskvótákat és korlátokat](batch-quota-limit.md). A Batch-fiókok aktuális kvótái a fióktulajdonságokban jelennek meg a portálon.

* Ha a Batch-fiókhoz társít (kapcsol) egy tárfiókot, körültekintően járjon el, amikor újból létrehozza a tárfiók hívóbetűit. Csak egyetlen tárfiókhívóbetűt hozzon létre újra, kattintson a **Kulcsok szinkronizálása** gombra a társított tárfiók panelén, várjon 5 percet, hogy a hívóbetűkkel a rendszer feltöltse a készletekben lévő számítási csomópontokat, majd szükség esetén hozza létre újra és szinkronizálja a másik hívóbetűt. Ha mindkét hívóbetűt egyszerre hozza létre újra, a számítási csomópontok nem tudják szinkronizálni egyiket sem, és elveszítik a tárfiókhoz való hozzáférést.

  ![A tárfiók hívóbetűinek újbóli létrehozása][4]

> [AZURE.IMPORTANT] A Batch jelenleg *csak* az **Általános célú** tárfióktípust támogatja, amelynek leírása a [Tudnivalók az Azure Storage-fiókokról](../storage/storage-create-storage-account.md) fejezet 5., [Tárfiók létrehozása](../storage/storage-create-storage-account.md#create-a-storage-account) című szakaszában található. Amikor Azure Storage-fiókot kapcsol a Batch-fiókhoz, *csak* **általános célú** tárfiókot kapcsoljon hozzá.

## Következő lépések

* A Batch szolgáltatás fogalmairól és funkcióiról további információért tekintse meg [az Azure Batch funkcióinak áttekintését](batch-api-basics.md). A cikk az elsődleges Batch-erőforrásokat tárgyalja, például a készleteket, a számítási csomópontokat, a feladatokat, a tevékenységeket és a szolgáltatás olyan funkcióinak áttekintését nyújtja, amely lehetővé teszi a nagy méretű számítási feladatok futtatását.

* Megismerheti a Batch-kompatibilis alkalmazások [Batch .NET ügyfélkönyvtárral](batch-dotnet-get-started.md) való fejlesztésének alapjait. A [bevezető cikk](batch-dotnet-get-started.md) végigvezeti egy működő alkalmazáson, amely a Batch szolgáltatással futtat számítási feladatot több számítási csomóponton, és az Azure Storage-et is használja a számítási feladatok fájlelőkészítéséhez és -lekéréséhez.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "A tárfiók hívóbetűinek újbóli létrehozása"
[5]: ./media/batch-account-create-portal/batch_acct_05.png "A Batch-fiók beállítás- és tulajdonságpaneljei"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG



<!--HONumber=jun16_HO2-->


