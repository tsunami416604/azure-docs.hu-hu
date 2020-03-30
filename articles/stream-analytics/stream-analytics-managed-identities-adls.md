---
title: Az Azure Stream Analytics hitelesítése az Azure Data Lake Storage Gen1 szolgáltatáshoz
description: Ez a cikk ismerteti, hogyan használható felügyelt identitások az Azure Stream Analytics-feladat hitelesítéséhez az Azure Data Lake Storage Gen1 kimenet.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 01741ea56b9e6f55c1393e88fc7991d410c33119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254377"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>A Stream Analytics hitelesítése az Azure Data Lake Storage Gen1 szolgáltatásnak felügyelt identitások használatával

Az Azure Stream Analytics támogatja a felügyelt identitáshitelesítést az Azure Data Lake Storage (ADLS) Gen1 kimenettel. Az identitás egy felügyelt alkalmazás az Azure Active Directoryban regisztrált, amely egy adott Stream Analytics-feladatot képvisel, és egy célzott erőforrás hitelesítésére használható. A felügyelt identitások kiküszöbölik a felhasználóalapú hitelesítési módszerek korlátait, például a jelszómódosítások vagy a 90 naponta előforduló felhasználói jogkivonat-lejáratok miatt újra kell hitelesíteni őket. Emellett a felügyelt identitások segítenek az Azure Data Lake Storage Gen1 számára kimenetelt kimenetet tartalmazó Stream Analytics-feladat-telepítések automatizálásában.

Ez a cikk három módon engedélyezheti a felügyelt identitást egy Azure Stream Analytics-feladathoz, amely az Azure Data Lake Storage Gen1-re az Azure Portalon, az Azure Resource Manager-sablon üzembe helyezésén és a Visual Studio-hoz készült Azure Stream Analytics-eszközökön keresztül jut el.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Azure portál

1. Először hozzon létre egy új Stream Analytics-feladatot, vagy nyisson meg egy meglévő feladatot az Azure Portalon. A képernyő bal oldalán található menüsorban válassza a **Konfigurálás**csoportban a **Felügyelt identitás** lehetőséget.

   ![A Stream Analytics felügyelt identitásának konfigurálása](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. A jobb oldalon megjelenő ablakban válassza a **Rendszeráltal hozzárendelt felügyelt identitás használata** lehetőséget. Kattintson a **Mentés** egy egyszerű szolgáltatásaz Azure Active Directoryban a Stream Analytics-feladat identitásához. Az újonnan létrehozott identitás életciklusát az Azure kezeli. A Stream Analytics-feladat törlésekor a társított identitást (azaz az egyszerű szolgáltatást) az Azure automatikusan törli.

   A konfiguráció mentésekor a szolgáltatásnév objektumazonosítója (OID) az alábbi módon szerepel a főazonosítóként:

   ![Stream Analytics egyszerű szolgáltatásazonosítója](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   Az egyszerű szolgáltatás neve megegyezik a Stream Analytics-feladat nevével. Ha például a feladat neve **MyASAJob,** akkor a létrehozott egyszerű szolgáltatás neve is **MyASAJob**lesz.

3. Az ADLS Gen1 kimeneti menükezelő kimeneti tulajdonságablakában kattintson a Hitelesítési mód legördülő menüre, és válassza a **Felügyelt identitás **lehetőséget.

4. Töltse ki a többi tulajdonságot. Ha többet szeretne tudni az ADLS-kimenetek létrehozásáról, olvassa [el a Data lake Store kimenet létrehozása adatfolyam-elemzéssel című témakört.](../data-lake-store/data-lake-store-stream-analytics.md) Amikor végzett, kattintson a **Mentés** gombra.

   ![Az Azure Data Lake Storage konfigurálása](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Nyissa meg az ADLS Gen1 Áttekintés lapját, és kattintson az **Adatkezelő gombra.**

   ![A Data Lake storage konfigurálása – áttekintés](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. Az Adatkezelő ablaktáblában válassza az **Access** lehetőséget, és kattintson a **Hozzáadás** gombra a Hozzáférés ablaktáblán.

   ![A Data Lake storage access konfigurálása](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. A Felhasználó vagy **csoport kiválasztása** ablaktábla szövegmezőjébe írja be az egyszerű szolgáltatás nevét. Ne feledje, hogy a szolgáltatásnév egyben a megfelelő Stream Analytics-feladat neve is. Amint elkezdi beírni a fő nevet, az megjelenik a szövegdoboz alatt. Válassza ki a kívánt egyszerű szolgáltatásnevet, és kattintson **a Kijelölés gombra.**

   ![Egyszerű szolgáltatásnév kiválasztása](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. Az **Engedélyek** ablaktáblán jelölje be az **Írás** és **végrehajtás** engedélyeket, és rendelje hozzá **ezt a mappát és az összes gyermekhez.** Ezután kattintson **az Ok gombra.**

   ![Írási és végrehajtási engedélyek kiválasztása](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Az egyszerű szolgáltatás a **Hozzáférés** ablaktáblán **a Hozzárendelt engedélyek** csoportban található az alábbiak szerint. Most már visszaléphet, és elindíthatja a Stream Analytics-feladatot.

   ![Stream Analytics hozzáférési lista a portálon](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   A Data Lake Storage Gen1 fájlrendszerengedélyeiről az [Azure Data Lake Storage Gen1 hozzáférés-vezérlése 1](../data-lake-store/data-lake-store-access-control.md)című témakörben olvashat bővebben.

## <a name="stream-analytics-tools-for-visual-studio"></a>Stream Analytics-eszközök a Visual Studio számára

1. A JobConfig.json fájlban állítsa a **Rendszer által hozzárendelt identitás használata** értéket True **értékre.**

   ![Stream Analytics-feladat konfigurációs felügyelt identitások](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. Az ADLS Gen1 kimeneti menükezelő kimeneti tulajdonságablakában kattintson a Hitelesítési mód legördülő menüre, és válassza a **Felügyelt identitás **lehetőséget.

   ![ADLS kimenet felügyelt identitások](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Töltse ki a többi tulajdonságot, és kattintson a **Mentés gombra.**

4. Kattintson a **Küldés az Azure-ba** elemre a lekérdezésszerkesztőben.

   Amikor elküldi a feladatot, az eszközök két dolgot tesznek:

   * Automatikusan létrehoz egy egyszerű szolgáltatás az Azure Active Directoryban a Stream Analytics-feladat identitásához. Az újonnan létrehozott identitás életciklusát az Azure kezeli. A Stream Analytics-feladat törlésekor a társított identitást (azaz az egyszerű szolgáltatást) az Azure automatikusan törli.

   * Automatikusan állítsa be az **írási** és **végrehajtási** engedélyeket a feladatban használt ADLS Gen1 előtag elérési úthoz, és rendelje hozzá ehhez a mappához és az összes gyermekhez.

5. Az Erőforrás-kezelő sablonok at a következő tulajdonsággal hozhatja létre a [Stream Analytics CI használatával. CD Nuget csomag](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) 1.5.0-s vagy újabb verzió egy buildgépen (a Visual Studio-n kívül). Kövesse a Resource Manager-sablon telepítési lépéseit a következő szakaszban a szolgáltatásegyszerű szolgáltatás lefelvételéhez és a szolgáltatásnévhez való hozzáférés biztosításához a PowerShellen keresztül.

## <a name="resource-manager-template-deployment"></a>Erőforrás-kezelő sablonjának telepítése

1. Felügyelt identitással rendelkező *Microsoft.StreamAnalytics/streamingjobs* erőforrást úgy hozhat létre, hogy a következő tulajdonságot építi fel az Erőforrás-kezelő sablon erőforrásszakaszában:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Ez a tulajdonság arra utasítja az Azure Resource Managert, hogy hozza létre és kezelje az Azure Stream Analytics-feladat identitását.

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
         }
       }
     }
   }
   ```
  
   **Mintafeladat-válasz**

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

   Vegye figyelembe a feladatválaszból a fő azonosítót, hogy hozzáférést biztosítson a szükséges ADLS-erőforráshoz.

   A **bérlői azonosító** az Azure Active Directory-bérlő azonosítója, ahol az egyszerű szolgáltatás jön létre. Az egyszerű szolgáltatás jön létre az Azure-bérlő, amely az előfizetés által megbízhatónak.

   A **típus** a felügyelt identitás típusát jelzi a felügyelt identitások típusai szerint. Csak a Rendszer hezrendelt típus támogatott.

2. Access-t a szolgáltatásnév hez a PowerShell használatával. Ha a PowerShellen keresztül szeretne hozzáférést adni az egyszerű szolgáltatáshoz, hajtsa végre a következő parancsot:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   A **PrincipalId** a szolgáltatásnév objektumazonosítója, és a szolgáltatásnév létrehozása után megjelenik a portál képernyőjén. Ha a feladatot erőforrás-kezelősablon központi telepítésével hozta létre, az objektumazonosító a feladatválasz Identitás tulajdonságában jelenik meg.

   **Példa**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   A fenti PowerShell-parancsról a [Set-AzDataLakeStoreItemAclEntry](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) dokumentációban olvashat bővebben.

## <a name="limitations"></a>Korlátozások
Ez a funkció nem támogatja a következőket:

1. **Több-bérlős hozzáférés:** Az adott Stream Analytics-feladathoz létrehozott egyszerű szolgáltatás azOn az Azure Active Directory-bérlőn lesz, amelyen a feladat létrehozása történt, és nem használható egy másik Azure Active Directory-bérlőn található erőforrással szemben. Ezért csak az MSI-t használhatja az ADLS Gen 1-erőforrásokon, amelyek ugyanabban az Azure Active Directory-bérlőn belül vannak, mint az Azure Stream Analytics-feladat. 

2. **[Felhasználó által hozzárendelt identitás:](../active-directory/managed-identities-azure-resources/overview.md)** nem támogatott. Ez azt jelenti, hogy a felhasználó nem tudja megadni a saját egyszerű szolgáltatását, amelyet a Stream Analytics-feladat használ. Az egyszerű szolgáltatás az Azure Stream Analytics által generált.

## <a name="next-steps"></a>További lépések

* [Adattói Áruház kimenetének létrehozása streamelemzéssel](../data-lake-store/data-lake-store-stream-analytics.md)
* [A Test Stream Analytics lekérdezései helyileg a Visual Studióval](stream-analytics-vs-tools-local-run.md)
* [Élő adatok helyi tesztelése a Visual Studio Azure Stream Analytics eszközeivel](stream-analytics-live-data-local-testing.md) 
