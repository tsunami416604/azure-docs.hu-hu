---
title: Azure Stream Analytics hitelesítése Azure Data Lake Storage Gen1
description: Ez a cikk ismerteti a Azure Stream Analytics-feladat az Azure Data Lake Storage Gen1 kimeneti hitelesítéséhez felügyelt identitások használatával.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 01741ea56b9e6f55c1393e88fc7991d410c33119
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254377"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Stream Analytics hitelesítése Azure Data Lake Storage Gen1 felügyelt identitások használatával

Az Azure Stream Analytics felügyelt identitás hitelesítés az Azure Data Lake Storage (ADLS) Gen1 kimeneti támogatja. Az identitás az Azure Active Directoryban, amely egy adott Stream Analytics-feladat egy felügyelt alkalmazást, és célzott erőforrás hitelesítéséhez használható. Felügyelt identitások megszüntetheti a felhasználó-alapú hitelesítési módszerek, például a jelszó módosítására vagy a felhasználói jogkivonat lejárhat 90 naponként előforduló miatt hitelesítse magát újra kellene vonatkozó korlátozások. Ezenkívül a felügyelt identitások, amelyek az Azure Data Lake Storage Gen1 kimenete Stream Analytics-feladat központitelepítését automatizálással segítségével.

Ebből a cikkből megtudhatja, hogyan engedélyezheti a felügyelt identitást egy olyan Azure Stream Analytics feladatokhoz, amely az Azure Portalon, Azure Resource Manager sablonon üzemelő példányon és a Visual studióhoz készült Azure Stream Analytics eszközökön keresztül Azure Data Lake Storage Gen1 kimenetet eredményez

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Azure Portal

1. Először hozzon létre egy új Stream Analytics-feladat vagy egy meglévő feladat nyissa meg az Azure Portalon. A képernyő bal oldalán található menüsávban válassza a **configure (Konfigurálás**) alatt található **felügyelt identitás** elemet.

   ![Stream Analytics felügyelt identitás konfigurálása](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Válassza a **rendszerhez rendelt felügyelt identitás használata** lehetőséget a jobb oldalon megjelenő ablakban. Kattintson a **Mentés** egy egyszerű szolgáltatásra lehetőségre a Azure Active Directory stream Analytics-feladatainak identitásához. Az újonnan létrehozott identitás életciklusának fogja felügyelni az Azure-ban. A Stream Analytics-feladat törlése esetén a rendszer automatikusan törli a társított identitás (azaz a szolgáltatásnév) az Azure-ban.

   A konfiguráció mentésekor az objektumazonosító (OID) egyszerű szolgáltatás szerepel a résztvevő-azonosítóval alább látható módon:

   ![Stream Analytics szolgáltatás résztvevő-azonosító](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   A szolgáltatásnév neve megegyezik a Stream Analytics-feladat van. Ha például a feladatainak neve **MyASAJob**, a létrehozott egyszerű szolgáltatásnév neve is **MyASAJob**.

3. A ADLS Gen1 kimeneti fogadó kimeneti tulajdonságok ablakában kattintson a hitelesítési mód legördülő menüre, és válassza a * * felügyelt identitás * * elemet.

4. Adja meg a további tulajdonságok. Ha többet szeretne megtudni a ADLS-kimenet létrehozásáról, olvassa el a következő témakört: [create a Network Store a stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md). Ha elkészült, kattintson a **Mentés**gombra.

   ![Az Azure Data Lake Storage konfigurálása](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Navigáljon a ADLS Gen1 áttekintés lapjára, és kattintson az **adatkezelő elemre.**

   ![Konfigurálja a Data Lake Storage – áttekintés](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. Az adatkezelő ablaktáblán válassza a **hozzáférés** elemet, majd kattintson a **Hozzáadás** elemre a hozzáférés ablaktáblán.

   ![Data Lake Storage hozzáférés konfigurálása](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. A **felhasználó vagy csoport kiválasztása** ablaktáblán a szövegmezőbe írja be az egyszerű szolgáltatásnév nevét. Ne feledje, hogy a szolgáltatásnév neve is-e a megfelelő Stream Analytics-feladat nevét. Ahogy elkezd gépelni a szolgáltatásnevet, a szövegmező alatt fog megjelenni. Válassza ki a kívánt egyszerű szolgáltatásnév nevét, és kattintson a **kiválasztás**elemre.

   ![Szolgáltatásnév kiválasztása](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. Az **engedélyek** ablaktáblán tekintse át az **írási** és **végrehajtási** engedélyeket, és rendelje hozzá **ehhez a mappához és az összes gyermekhez**. Ezután kattintson **az OK gombra**.

   ![Válassza ki az írási és végrehajtási engedélyek](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Az egyszerű szolgáltatásnév a **hozzáférés** ablaktáblán az alább látható módon szerepel a **hozzárendelt engedélyek** területen. Most lépjen vissza, és indítsa el a Stream Analytics-feladatot.

   ![Stream Analytics lista a portál elérése](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   További információ a Data Lake Storage Gen1 fájlrendszer engedélyeiről: [Access Control a Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>A Visual studióhoz készült Stream Analytics eszközök

1. A JobConfig. JSON fájlban állítsa a **rendszer által hozzárendelt identitás használata** **igaz**értékre.

   ![Stream Analytics feladatok konfigurációjának felügyelt identitások](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. A ADLS Gen1 kimeneti fogadó kimeneti tulajdonságok ablakában kattintson a hitelesítési mód legördülő menüre, és válassza a * * felügyelt identitás * * elemet.

   ![ADLS kimeneti felügyelt identitások](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Töltse ki a többi tulajdonságot, és kattintson a **Mentés**gombra.

4. Kattintson a **Küldés az Azure** -ba lehetőségre a lekérdezés-szerkesztőben.

   A feladatok elküldésekor az eszközök két dolgot tesznek:

   * A automatikusan létrehoz egy szolgáltatásnevet a Stream Analytics-feladatoknak a Azure Active Directoryban való identitásához. Az újonnan létrehozott identitás életciklusának fogja felügyelni az Azure-ban. A Stream Analytics-feladat törlése esetén a rendszer automatikusan törli a társított identitás (azaz a szolgáltatásnév) az Azure-ban.

   * Az **írási** és **végrehajtási** engedélyek automatikus beállítása a feladatokhoz használt ADLS Gen1 előtag elérési útjához, és az összes gyermekhez rendelhető hozzá.

5. A Resource Manager-sablonokat a következő tulajdonsággal hozhatja [stream Analytics CI használatával. CD Nuget-csomag](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) 1.5.0-es vagy újabb verziója egy Build gépen (a Visual studión kívül). A következő szakaszban a Resource Manager-sablon telepítési lépéseit követve szerezze be a szolgáltatásnevet, és adjon hozzáférést a szolgáltatáshoz a PowerShell használatával.

## <a name="resource-manager-template-deployment"></a>Resource Manager-sablon üzembe helyezése

1. A felügyelt identitással rendelkező *Microsoft. StreamAnalytics/streamingjobs-* erőforrások létrehozásához a Resource Manager-sablon Resource (erőforrás) szakaszában a következő tulajdonságot is használhatja:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Ez a tulajdonság arra utasítja az Azure Resource Manager létrehozása és kezelése az Azure Stream Analytics-feladat az identitás.

   **Példa feladatokra**
   
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
         }
       }
     }
   }
   ```
  
   **Példa a feladattípusra**

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
   }
   ```

   Jegyezze fel a résztvevő-azonosító a feladat válaszból hozzáférést biztosítani a szükséges ADLS-erőforráshoz.

   A **bérlő azonosítója** annak az Azure Active Directory bérlőnek az azonosítója, ahol a szolgáltatásnevet létrehozták. Az egyszerű szolgáltatás létrehozása az Azure-bérlő az előfizetés által megbízhatónak tartott.

   A **típus** a felügyelt identitás típusát jelöli a felügyelt identitások típusaiban leírtak szerint. Csak a rendszer adott típus támogatott.

2. Hozzáférést biztosít az egyszerű szolgáltatás PowerShell használatával. Hozzáférést biztosít a Powershellen keresztül egyszerű szolgáltatást, futtassa a következő parancsot:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   A **PrincipalId** az egyszerű szolgáltatásnév azonosítója, és a portál képernyőjén jelenik meg a szolgáltatásnév létrehozása után. A feladat egy Resource Manager-sablon üzembe helyezése használatával hozta létre, ha Objektumazonosítóját a feladat válasz az azonosító tulajdonság szerepel.

   **Példa**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   A fenti PowerShell-paranccsal kapcsolatos további tudnivalókért tekintse meg a [set-AzDataLakeStoreItemAclEntry](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) dokumentációt.

## <a name="limitations"></a>Korlátozások
Ez a funkció nem támogatja a következőket:

1. **Több-bérlős hozzáférés**: az adott stream Analytics feladathoz létrehozott egyszerű szolgáltatásnév azon a Azure Active Directory-bérlőn található, amelyen a feladatot létrehozták, és nem használható olyan erőforráson, amely egy másik Azure Active Directory bérlőn található. Ezért csak az MSI-t használhatja a ADLS 1. generációs erőforrásokhoz, amelyek ugyanabban a Azure Active Directory-bérlőn belül vannak, mint a Azure Stream Analytics feladatokkal. 

2. **[Felhasználóhoz rendelt identitás](../active-directory/managed-identities-azure-resources/overview.md)** : nem támogatott. Ez azt jelenti, hogy a felhasználó nem tudja megadni a saját egyszerű szolgáltatásnevet, amelyet a Stream Analytics feladataihoz használhatnak. Az egyszerű szolgáltatásnevet a Azure Stream Analytics hozza létre.

## <a name="next-steps"></a>Következő lépések

* [A stream Analytics kimenetének létrehozása a adattó-tárolóban](../data-lake-store/data-lake-store-stream-analytics.md)
* [A Visual Studióval helyileg tesztelheti Stream Analytics lekérdezéseit](stream-analytics-vs-tools-local-run.md)
* [Élő adatellenőrzés helyi tesztelése a Visual studióhoz készült Azure Stream Analytics Tools használatával](stream-analytics-live-data-local-testing.md) 
