---
title: Az Azure Stream Analytics-feladatot az Azure Data Lake Storage Gen1 kimeneti hitelesítés
description: Ez a cikk ismerteti a Azure Stream Analytics-feladat az Azure Data Lake Storage Gen1 kimeneti hitelesítéséhez felügyelt identitások használatával.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: bb25f237450a83a34645ad4dfd9a2839c5525c6f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090431"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities-preview"></a>Hitelesítés a Stream Analytics használatával az Azure Data Lake Storage Gen1 felügyelt identitások (előzetes verzió) használatával

Az Azure Stream Analytics felügyelt identitás hitelesítés az Azure Data Lake Storage (ADLS) Gen1 kimeneti támogatja. Az identitás az Azure Active Directoryban, amely egy adott Stream Analytics-feladat egy felügyelt alkalmazást, és célzott erőforrás hitelesítéséhez használható. Felügyelt identitások megszüntetheti a felhasználó-alapú hitelesítési módszerek, például a jelszó módosítására vagy a felhasználói jogkivonat lejárhat 90 naponként előforduló miatt hitelesítse magát újra kellene vonatkozó korlátozások. Ezenkívül a felügyelt identitások, amelyek az Azure Data Lake Storage Gen1 kimenete Stream Analytics-feladat központitelepítését automatizálással segítségével.

Látogasson el a [nyolc új funkciók az Azure Stream Analyticsben](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/) feliratkozás az előzetes verzió és tanulmányozza részletesen az új funkciók a blogbejegyzést.

Ez a cikk bemutatja, két különböző módon az Azure Stream Analytics-feladat, amely egy Azure Data Lake Storage Gen1 a felügyelt identitás engedélyezése: az Azure portal és az Azure Resource Manager-sablon üzembe helyezése révén.

## <a name="enable-managed-identity-with-azure-portal"></a>Felügyelt identitás engedélyezése az Azure Portalon

1. Először hozzon létre egy új Stream Analytics-feladat vagy egy meglévő feladat nyissa meg az Azure Portalon. A képernyő bal oldalán található menüsávban válassza **felügyelt identitás (előzetes verzió)** alatt **konfigurálása**.

   ![Stream Analytics felügyelt identitás előzetes konfigurálása](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Válassza ki **használható rendszer által hozzárendelt felügyelt identitás (előzetes verzió)** a jobb oldalon megjelenő ablakban. Kattintson a **mentése** identitását a Stream Analytics-feladat, az egyszerű szolgáltatás létrehozása az Azure Active Directoryban. Az újonnan létrehozott identitás életciklusának fogja felügyelni az Azure-ban. A Stream Analytics-feladat törlése esetén a rendszer automatikusan törli a társított identitás (azaz a szolgáltatásnév) az Azure-ban.

   A konfiguráció mentésekor az objektumazonosító (OID) egyszerű szolgáltatás szerepel a résztvevő-azonosítóval alább látható módon:

   ![Stream Analytics szolgáltatás résztvevő-azonosító](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   A szolgáltatásnév neve megegyezik a Stream Analytics-feladat van. Például, ha a feladat neve **MyASAJob**, a létrehozott szolgáltatásnév neve nem is **MyASAJob**.

3. Az ADLS Gen1 kimeneti fogadó kimeneti tulajdonságok ablakban kattintson a legördülő menüből, és válassza ki a hitelesítési mód **felügyelt identitás (előzetes verzió)**.

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
                 "accountName": “myDataLakeAccountName",
                 "filePathPrefix": “cluster1/logs/{date}/{time}",
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
   Set-AzureRmDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   A **PrincipalId** a szolgáltatásnév Objektumazonosítóját, és az egyszerű szolgáltatás létrehozása után megjelenik a portál képernyőjén. A feladat egy Resource Manager-sablon üzembe helyezése használatával hozta létre, ha Objektumazonosítóját a feladat válasz az azonosító tulajdonság szerepel.

   **Példa**

   ```powershell
   PS > Set-AzureRmDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   A fenti PowerShell-paranccsal kapcsolatos további információkért tekintse meg a [Set-AzureRmDataLakeStoreItemAclEntry](https://docs.microsoft.com/powershell/module/azurerm.datalakestore/set-azurermdatalakestoreitemaclentry?view=azurermps-6.8.1&viewFallbackFrom=azurermps-4.2.0#optional-parameters) dokumentációját.

## <a name="next-steps"></a>További lépések

* [Hozzon létre egy Data lake Store-kimenetet a stream analytics használatával](../data-lake-store/data-lake-store-stream-analytics.md)
