---
title: Az Azure Stream Analytics-feladatot az Azure Data Lake Storage Gen1 kimeneti hitelesítés
description: Ez a cikk ismerteti a Azure Stream Analytics-feladat az Azure Data Lake Storage Gen1 kimeneti hitelesítéséhez felügyelt identitások használatával.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/8/2019
ms.custom: seodec18
ms.openlocfilehash: 9eb66a9000c9add0718c6edf6674a26ce8e479b3
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257977"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Hitelesítés a Stream Analytics használatával az Azure Data Lake Storage Gen1 felügyelt identitások használatával

Az Azure Stream Analytics felügyelt identitás hitelesítés az Azure Data Lake Storage (ADLS) Gen1 kimeneti támogatja. Az identitás az Azure Active Directoryban, amely egy adott Stream Analytics-feladat egy felügyelt alkalmazást, és célzott erőforrás hitelesítéséhez használható. Felügyelt identitások megszüntetheti a felhasználó-alapú hitelesítési módszerek, például a jelszó módosítására vagy a felhasználói jogkivonat lejárhat 90 naponként előforduló miatt hitelesítse magát újra kellene vonatkozó korlátozások. Ezenkívül a felügyelt identitások, amelyek az Azure Data Lake Storage Gen1 kimenete Stream Analytics-feladat központitelepítését automatizálással segítségével.

Ez a cikk bemutatja, háromféleképpen engedélyezni az Azure Stream Analytics-feladat, amely, egy Azure Data Lake Storage Gen1 keresztül az Azure Portalon, az Azure Resource Manager-sablon üzembe helyezése és az Azure Stream Analytics tools for Visual Studio-felügyelt identitást.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Azure Portal

1. Először hozzon létre egy új Stream Analytics-feladat vagy egy meglévő feladat nyissa meg az Azure Portalon. A képernyő bal oldalán található menüsávban válassza **felügyelt identitás** alatt **konfigurálása**.

   ![Stream Analytics felügyelt identitás konfigurálása](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Válassza ki **felügyelt identitás használata rendszer által hozzárendelt** a jobb oldalon megjelenő ablakban. Kattintson a **mentése** identitását a Stream Analytics-feladat az Azure Active Directoryban egy egyszerű szolgáltatás. Az újonnan létrehozott identitás életciklusának fogja felügyelni az Azure-ban. A Stream Analytics-feladat törlése esetén a rendszer automatikusan törli a társított identitás (azaz a szolgáltatásnév) az Azure-ban.

   A konfiguráció mentésekor az objektumazonosító (OID) egyszerű szolgáltatás szerepel a résztvevő-azonosítóval alább látható módon:

   ![Stream Analytics szolgáltatás résztvevő-azonosító](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   A szolgáltatásnév neve megegyezik a Stream Analytics-feladat van. Például, ha a feladat neve **MyASAJob**, a létrehozott szolgáltatásnév neve nem is **MyASAJob**.

3. Az ADLS Gen1 kimeneti fogadó kimeneti tulajdonságok ablakban kattintson a legördülő menüből, és válassza ki a hitelesítési mód ** felügyelt identitás **.

4. Adja meg a további tulajdonságok. Egy ADLS-kimenet létrehozásával kapcsolatos további tudnivalókért lásd: [hozzon létre egy Data lake Store-kimenetet a stream analytics](../data-lake-store/data-lake-store-stream-analytics.md). Ha elkészült, kattintson a **mentése**.

   ![Az Azure Data Lake Storage konfigurálása](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Az ADLS Gen1 áttekintés oldalán keresse meg és kattintson a **adatkezelő**.

   ![Konfigurálja a Data Lake Storage – áttekintés](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. A Data explorer panelen válassza ki **hozzáférés** kattintson **Hozzáadás** a hozzáférési panelen.

   ![Data Lake Storage hozzáférés konfigurálása](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. A szövegmezőbe írja be a **felhasználó vagy csoport kiválasztása** panelen írja be az egyszerű szolgáltatás nevét. Ne feledje, hogy a szolgáltatásnév neve is-e a megfelelő Stream Analytics-feladat nevét. Ahogy elkezd gépelni a szolgáltatásnevet, a szövegmező alatt fog megjelenni. Válassza ki a kívánt szolgáltatásnevet, és kattintson a **kiválasztása**.

   ![Szolgáltatásnév kiválasztása](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. Az a **engedélyek** ablaktáblán jelölje a **írási** és **Execute** engedélyeket, és rendelje hozzá a **Ez a mappa és az összes gyermekeleme**. Kattintson a **Ok**.

   ![Válassza ki az írási és végrehajtási engedélyek](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Az egyszerű szolgáltatás részen **rendelt engedélyek** a a **hozzáférés** panelen, ahogy az alábbi. Most lépjen vissza, és indítsa el a Stream Analytics-feladatot.

   ![Stream Analytics lista a portál elérése](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Data Lake Storage Gen1 fájlrendszerre vonatkozó engedélyekkel kapcsolatos további információkért lásd: [hozzáférés-vezérlés az Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Stream Analytics tools for Visual Studio

1. JobConfig.json, állítsa be **használható rendszer által hozzárendelt identitás** való **igaz**.

   ![Stream Analytics-feladat konfigurációjának felügyelt identitások](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. Az ADLS Gen1 kimeneti fogadó kimeneti tulajdonságok ablakban kattintson a legördülő menüből, és válassza ki a hitelesítési mód ** felügyelt identitás **.

   ![ADLS kimenete a felügyelt identitásokból](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Adja meg a további tulajdonságok, és kattintson a **mentése**.

4. Kattintson a **elküldése az Azure-bA** a Lekérdezésszerkesztő.

   A feladat elküldéséhez, amikor az eszközök két műveletet kell végrehajtania:

   * Automatikusan létrehoz egy egyszerű szolgáltatást a Stream Analytics-feladat identitását az Azure Active Directoryban. Az újonnan létrehozott identitás életciklusának fogja felügyelni az Azure-ban. A Stream Analytics-feladat törlése esetén a rendszer automatikusan törli a társított identitás (azaz a szolgáltatásnév) az Azure-ban.

   * Automatikusan **írási** és **Execute** az ADLS Gen1 engedélyeinek előtag elérési útja a feladat, és rendelje hozzá ezt a mappát, és az összes gyermekeleme.

5. Az alábbi tulajdonság használata a Resource Manager-sablonokat is létrehozhat [Stream Analytics CI. CD Nuget-csomag](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) 1.5.0 verzió vagy újabb (kívül a Visual Studio) build gépen. Kövesse az erőforrás-kezelő a következő szakaszban, a szolgáltatás egyszerű, és hozzáférést biztosít a Powershellen keresztül szolgáltatásnévnek sablon üzembe helyezési lépésein.

## <a name="resource-manager-template-deployment"></a>Resource Manager-sablon üzembe helyezése

1. Létrehozhat egy *Microsoft.StreamAnalytics/streamingjobs* erőforrás, beleértve a következő tulajdonság az erőforrás a Resource Manager-sablon által felügyelt identitással:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Ez a tulajdonság arra utasítja az Azure Resource Manager létrehozása és kezelése az Azure Stream Analytics-feladat az identitás.

   **Mintafeladat**

    ```json
    {
      "Name": "AsaJobWithIdentity",
      "Type": "Microsoft.StreamAnalytics/streamingjobs",
      "Location": "West US",
      "Identity": {
        "Type": "SystemAssigned",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
        "outputs": [
          {
            "name": "string",
            "properties":{
              "datasource": {
                "type": "Microsoft.DataLake/Accounts",
                "properties": {
                  "accountName": "myDataLakeAccountName",
                  "filePathPrefix": "cluster1/logs/{date}/{time}",
                  "dateFormat": "YYYY/MM/DD",
                  "timeFormat": "HH",
                  "authenticationMode": "Msi"
                }
              }
   ```
  
   **Feladat mintaválasz**

   ```json
   {
    "Name": "mySAJob",
    "Type": "Microsoft.StreamAnalytics/streamingjobs",
    "Location": "West US",
    "Identity": {
      "Type": "SystemAssigned",
        "principalId": "GUID",
        "tenantId": "GUID",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
      }
   ```

   Jegyezze fel a résztvevő-azonosító a feladat válaszból hozzáférést biztosítani a szükséges ADLS-erőforráshoz.

   A **Bérlőazonosító** van az Azure Active Directory-bérlő azonosítója, ahol a szolgáltatásnév létrejött. Az egyszerű szolgáltatás létrehozása az Azure-bérlő az előfizetés által megbízhatónak tartott.

   A **típus** felügyelt identitás típusát jelzi a felügyelt identitásokból típusú leírtak szerint. Csak a rendszer adott típus támogatott.

2. Hozzáférést biztosít az egyszerű szolgáltatás PowerShell használatával. Hozzáférést biztosít a Powershellen keresztül egyszerű szolgáltatást, futtassa a következő parancsot:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   A **PrincipalId** a szolgáltatásnév Objektumazonosítóját, és az egyszerű szolgáltatás létrehozása után megjelenik a portál képernyőjén. A feladat egy Resource Manager-sablon üzembe helyezése használatával hozta létre, ha Objektumazonosítóját a feladat válasz az azonosító tulajdonság szerepel.

   **Példa**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   A fenti PowerShell-paranccsal kapcsolatos további információkért tekintse meg a [Set-AzDataLakeStoreItemAclEntry](https://docs.microsoft.com/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) dokumentációját.

## <a name="limitations"></a>Korlátozások
Ez a funkció nem támogatja a következőket:

1.  **Több-bérlős hozzáférés**: Az egyszerű szolgáltatás egy adott Stream Analytics-feladat létrehozása az Azure Active Directory-bérlővel, amelyen a feladat lett létrehozva, és nem használható olyan erőforrások, amelyek egy másik Azure Active Directory-bérlőben található találhatók. Ezért csak használhatja MSI ADLS általános 1 erőforrást, amely az Azure Stream Analytics-feladat, ugyanazon az Azure Active Directory a bérlőn belül. 

2.  **[Felhasználói hozzárendelt identitás](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)**: nem támogatott ez azt jelenti, hogy a felhasználó nem sikerül a saját a Stream Analytics-feladat által használt egyszerű szolgáltatásának megadása. Az egyszerű szolgáltatás Azure Stream Analytics hozza létre. 


## <a name="next-steps"></a>További lépések

* [Hozzon létre egy Data lake Store-kimenetet a stream analytics használatával](../data-lake-store/data-lake-store-stream-analytics.md)
* [Stream Analytics-lekérdezések Visual studióval helyileg tesztelése](stream-analytics-vs-tools-local-run.md)
* [Teszt élő adatok helyileg az Azure Stream Analytics tools for Visual Studio használatával](stream-analytics-live-data-local-testing.md) 
