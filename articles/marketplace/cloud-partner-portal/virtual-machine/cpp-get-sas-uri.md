---
title: Megosztott hozzáférési aláírás URI-ja a Microsoft Azure-alapú virtuálisgép-rendszerképhez | Azure piactér
description: A cikk azt ismerteti, hogyan kérhető le a virtuálisgép-rendszerkép közös hozzáférésű aláírása (SAS) URI-ja.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: cb6f1772c7c6f9abd268a8cb58550b253f095dbf
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132445"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>A virtuálisgép-rendszerkép közös hozzáférésű aláírási URI-azonosítójának beolvasása

A közzétételi folyamat során meg kell adnia egy egységes erőforrás-azonosítót (URI) az SKU-hoz társított minden virtuális merevlemezhez (VHD). Ezek a Microsoft számára szükségesek a virtuális merevlemezek eléréséhez a minősítési folyamat során. Ez a cikk bemutatja, hogyan hozhatja ki a közös hozzáférésű aláírás (SAS) URI-JÁT minden virtuális merevlemezhez. Ezt az URI-t a Cloud Partner Portal **SKUs (SKU** ) lapján adhatja meg.

Ha SAS URI-ket hoz létre a virtuális merevlemezekhez, a következő követelményeknek kell megfelelnie:

- Csak a nem felügyelt virtuális merevlemezek támogatottak.
- `List` és `Read`engedélyek elegendőek. Ne *adjon meg* `Write` vagy `Delete` hozzáférést.
- A hozzáférés időtartamának (*lejárati dátum*) legalább három héttel a sas URI létrehozása után kell lennie.
- Az UTC-időeltérések elleni védelem érdekében állítsa be a kezdési dátumot az aktuális dátum előtt egy nappal. Ha például az aktuális dátum október 6., 2014, válassza a 10/5/2014 elemet.

## <a name="generate-the-sas-url"></a>SAS URL-cím előállítása

A SAS URL-cím kétféleképpen hozható létre a következő eszközök használatával:

-   Microsoft Storage Explorer – Windows, macOS és Linux rendszerekhez elérhető grafikus eszköz
-   Microsoft Azure CLI – nem Windows rendszerű OSs-hez, illetve automatizált vagy folyamatos integrációs környezetekhez ajánlott


### <a name="azure-cli"></a>Azure CLI

Az alábbi lépéseket követve hozhatja elő az SAS URI-t az Azure CLI használatával.

1. Töltse le és telepítse a [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)-t.  A verziók a Windows, a macOS és a Linux különböző disztribúciói számára érhetők el.
2. Hozzon létre egy PowerShell-fájlt (`.ps1` fájlkiterjesztés), másolja a következő kódot, majd mentse helyileg.

   ``` powershell
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```

3. Szerkessze a fájlt, és adja meg a következő paramétereket.  A dátumokat UTC datetime formátumban kell megadni, például `2016-10-25T00:00:00Z`.
   - `<account-name>` – az Azure Storage-fiók neve
   - `<account-key>` – Azure Storage-fiókjának kulcsa
   - `<vhd-name>` – a VHD-neve
   - `<start-date>` – a VHD-hozzáférés engedélyének kezdő dátuma. Adja meg a dátumot egy nappal az aktuális dátum előtt.
   - `<expiry-date>` – a VHD-hozzáférés engedélyének lejárati dátuma.  Adjon meg legalább három héttel az aktuális dátumon túli dátumot.

   Az alábbi példa a megfelelő paramétereket mutatja be (az írás időpontjában).

   ``` powershell
       az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```

4. Mentse a módosításokat a PowerShell-parancsfájlba.
5. Futtassa ezt a parancsfájlt rendszergazdai jogosultságokkal, hogy létrehozzon egy *sas-kapcsolati karakterláncot* a tároló szintű hozzáféréshez.  Két alapvető megközelítést használhat:
   - Futtassa a szkriptet a-konzolról.  Például a Windows rendszerben írja be a parancsfájlt, majd kattintson a **Futtatás rendszergazdaként**lehetőségre.
   - Futtassa a szkriptet egy PowerShell-parancsfájl szerkesztőjéből, például a [Windows PowerShell integrált parancsprogram-kezelési környezet](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)rendszergazdai jogosultságok használatával.
     Az alábbi táblázat a szerkesztőben létrehozott SAS-kapcsolatok karakterláncát mutatja be.

     ![SAS URI-generálása a PowerShell ISE-ben](./media/publishvm_032.png)

6. Másolja az eredményül kapott SAS-kapcsolódási sztringet, és mentse egy szövegfájlba egy biztonságos helyen.  A karakterlánc szerkesztésével hozzáadja a kapcsolódó VHD-hely adatait a végső SAS URI létrehozásához.
7. A Azure Portal navigáljon az újonnan létrehozott URI-hoz társított virtuális merevlemezt tartalmazó blob Storage-hoz.
8. Másolja a **blob Service végpont**URL-értékét az alább látható módon.

    ![Blob service végpont Azure Portal](./media/publishvm_033.png)

9. Szerkessze a szövegfájlt a 6. lépésből származó SAS-kapcsolatok karakterláncával.  A teljes SAS URI-t a következő formátumban kell létrehoznia:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Ha például a VDH neve `TestRGVM2.vhd`, akkor az eredményül kapott SAS URI a következő lesz:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Ismételje meg ezeket a lépéseket a közzétenni kívánt SKU-k minden virtuális merevlemezén.


### <a name="microsoft-storage-explorer"></a>Microsoft Storage Explorer

A következő lépésekkel hozhatja elő a SAS URI-t a Microsoft Azure Storage Explorer.

1. A [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) letöltése és telepítése.
2. Nyissa meg a Explorert, és a bal oldali menüsorban kattintson a **fiók hozzáadása** ikonra.  Megjelenik a **Kapcsolódás az Azure Storage-hoz** párbeszédpanel.
3. Válassza az **Azure-fiók hozzáadása** lehetőséget, majd kattintson a **Bejelentkezés** gombra.  Folytassa az Azure-fiókba való bejelentkezéshez szükséges lépéseket.
4. A bal oldali **Explorer** ablaktáblán navigáljon a Storage- **fiókjaihoz** , és bontsa ki ezt a csomópontot.
5. Kattintson a jobb gombbal a virtuális merevlemezre, és válassza a **megosztási hozzáférés aláírásának beolvasása** lehetőséget a helyi menüből.

    ![SAS-elemek beolvasása az Azure Explorerben](./media/publishvm_034.png)

6. Megjelenik a **megosztott hozzáférés aláírása** párbeszédpanel. Adja meg a következő mezők értékeit:
   - A VHD-hozzáférés **kezdő** időpontjának kezdési dátuma. Adja meg azt a dátumot, amely egy nappal az aktuális dátum előtt van.
   - **Lejárati idő** – a VHD-hozzáférés engedélyének lejárati dátuma.  Adjon meg legalább három héttel az aktuális dátumon túli dátumot.
   - **Engedélyek** – válassza ki a `Read` és a `List` engedélyeket.

     ![SAS-párbeszédpanel az Azure Explorerben](./media/publishvm_035.png)

7. A **Létrehozás** gombra kattintva hozza létre a hozzá tartozó sas URI-t ehhez a virtuális merevlemezhez.  A párbeszédpanel most megjeleníti a művelet részleteit.
8. Másolja az **URL-címet** , és mentse a fájlt egy biztonságos helyen lévő szövegfájlba.

    ![SAS URI létrehozása az Azure Explorerben](./media/publishvm_036.png)

    Ez a generált SAS URL-cím a tároló szintű hozzáféréshez szükséges.  Ennek érdekében hozzá kell adni a társított VHD-nevet.

9. Szerkessze a szövegfájlt. Szúrja be a VHD-nevet az SAS URL-címében lévő `vhds` sztring után (a kezdő perjelet is beleértve).  A végső SAS URI formátumának a következőket kell tartalmaznia:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Ha például a VDH neve `TestRGVM2.vhd`, akkor az eredményül kapott SAS URI a következő lesz:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Ismételje meg ezeket a lépéseket a közzétenni kívánt SKU-k minden virtuális merevlemezén.


## <a name="verify-the-sas-uri"></a>Az SAS URI ellenőrzése

Tekintse át és ellenőrizze az összes generált SAS URI-t az alábbi ellenőrzőlista használatával.  Ellenőrizze, hogy:
- Az URI a következőket képezi: `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
- Az URI tartalmazza a VHD-rendszerkép fájlnevét, beleértve a ". vhd" fájlnevet.
- Az URI közepe felé `sp=rl` jelenik meg. Ez a karakterlánc azt jelzi, hogy `Read` és `List` hozzáférés van megadva.
- Ekkor `sr=c` is megjelenik. Ez a karakterlánc azt jelzi, hogy a tároló szintű hozzáférés meg van adva.
- Másolja és illessze be az URI-t egy böngészőben a társított blob letöltésének megkezdéséhez.  (A letöltés befejezése előtt megszakíthatja a műveletet.)


## <a name="next-steps"></a>További lépések

Ha problémába ütközik az SAS URI létrehozásakor, tekintse meg az [általános sas URL-címekkel kapcsolatos problémákat](./cpp-common-sas-url-issues.md).  Ellenkező esetben mentse a SAS URI-JÁT biztonságos helyre a későbbi használat érdekében. A virtuálisgép- [ajánlat közzétételére](./cpp-publish-offer.md) a Cloud Partner Portal lesz szükség.
