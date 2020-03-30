---
title: Megosztott hozzáférési aláírás URI-kódbeszerezni a Microsoft Azure-alapú virtuálisgép-lemezképhez | Azure Piactér
description: A cikk ből megtudhatja, hogyan szerezheti be a megosztott hozzáférésű aláírás (SAS) URI-ját a virtuális gép lemezképéhez.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 6fe15fb18d8865911363a4696e44dd7fe1d90c09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277803"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Megosztott hozzáférésű aláírás URI-jának beszereznie a virtuális gép lemezképéhez

A közzétételi folyamat során meg kell adnia egy egységes erőforrás-azonosítót (URI) a termékkódhoz társított minden egyes virtuális merevlemezhez (VHD). A Microsoftnak a minősítési folyamat során hozzá kell férnie ezekhez a virtuális gépekhez. Ez a cikk bemutatja, hogyan hozhat létre egy közös hozzáférésű aláírás (SAS) URI-t az egyes virtuális merevlemezekhez. Ezt az URI-t a Felhőpartner-portál **SK** lapján adja meg.

A Virtuális gépazonosítók SAS URI-inak létrehozásakor tartsa be az alábbi követelményeket:

- Csak a nem felügyelt virtuális gépek támogatottak.
- `List`és `Read` az engedélyek elegendőek. Ne `Write` biztosítson `Delete` és *ne* férhessen hozzá.
- A hozzáférés időtartamának (*lejárati dátumának*) legalább három hétnek kell lennie a SAS URI létrehozásától számítva.
- Az UTC-időváltozatok elleni védelem érdekében állítsa a kezdő dátumot az aktuális dátum előtti napra. Ha például az aktuális dátum 2014.

## <a name="generate-the-sas-url"></a>A SAS URL-címének létrehozása

A SAS URL-cím két közös módon generálható a következő eszközökkel:

- Microsoft Storage Explorer - Grafikus eszköz windowsos, macOS és Linux rendszeren
- Microsoft Azure CLI – Nem Windows rendszerű rendszerű és automatizált vagy folyamatos integrációs környezetekben ajánlott

### <a name="azure-cli"></a>Azure CLI

Az alábbi lépésekkel hozzon létre egy SAS URI-t az Azure CLI-vel.

1. Töltse le és telepítse a [Microsoft Azure CLI-t](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). A verziók elérhetők A Windows, macOS, és a különböző disztribúciók a Linux.
2. Hozzon létre egy`.ps1` PowerShell-fájlt ( fájlkiterjesztés), másolja a következő kódot, majd mentse helyileg.

   ```azurecli-interactive
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```

3. A következő paraméterértékek megadása érdekében szerkesztse a fájlt.  A dátumokat UTC datetime formátumban `2016-10-25T00:00:00Z`kell megadni, például .
   - `<account-name>`- Az Azure tárfiók neve
   - `<account-key>`- Az Azure tárfiók kulcsa
   - `<vhd-name>`- A VHD neve
   - `<start-date>`- Engedély kezdő dátuma VHD hozzáférést. Adjon meg egy dátumot egy nappal az aktuális dátum előtt.
   - `<expiry-date>`- Engedély lejárati dátum a Virtuális merevlemez-hozzáféréshez.  Adjon meg egy dátumot legalább három héttel az aktuális dátum után.

   A következő példa a megfelelő paraméterértékeket mutatja be (az írás időpontjában).

   ```azurecli-interactive
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```

4. Mentse a PowerShell-parancsfájl módosításait.
5. Futtassa ezt a parancsfájlt rendszergazdai jogosultságokkal, hogy hozzon létre egy *SAS-kapcsolati karakterláncot* a tárolószintű hozzáféréshez.  Két alapvető megközelítést használhat:
   - Futtassa a parancsfájlt a konzolról.  Windows rendszerben például az írásgombra kattintva kattintson a parancsfájlra, és válassza a **Futtatás rendszergazdaként**lehetőséget.
   - Futtassa a parancsfájlt egy PowerShell-parancsfájlszerkesztőből, például a [Windows PowerShell ISE-ből,](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)rendszergazdai jogosultságokkal.
     Az alábbiakban bemutatja a szerkesztőn belül létrehozott SAS-kapcsolati karakterláncot.

     ![SAS URI-létrehozás a PowerShell ISE-ben](./media/publishvm_032.png)

6. Másolja az eredményül kapott SAS-kapcsolati karakterláncot, és mentse egy biztonságos helyen lévő szövegfájlba.  Ezt a karakterláncot úgy szerkesztheti, hogy hozzáadja a kapcsolódó VHD helyadatokat a végső SAS URI létrehozásához.
7. Az Azure Portalon keresse meg a blob storage, amely tartalmazza az újonnan létrehozott URI-hoz társított virtuális merevlemezt.
8. Másolja a Blob **szolgáltatás**végponturl-értékét az alábbiak szerint.

    ![Blob-szolgáltatás végpontja az Azure Portalon](./media/publishvm_033.png)

9. A szövegfájl szerkesztése a SAS-kapcsolati karakterlánccal a 6.  A teljes SAS URI-t a következő formátumban fogja létrehozni:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Ha például a VDH neve `TestRGVM2.vhd`a , akkor az eredményül kapott SAS URI a következő lesz:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Ismételje meg ezeket a lépéseket minden egyes virtuális merevlemeznél a közzétenni kívánt sus-okban.

### <a name="microsoft-storage-explorer"></a>Microsoft Tároló kezelő

Az alábbi lépésekkel hozzon létre egy SAS URI-t a Microsoft Azure Storage Explorer segítségével.

1. A [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) letöltése és telepítése.
2. Nyissa meg a felfedezőt, és a bal oldali menüsorban kattintson a **Fiók hozzáadása** ikonra.  Megjelenik **a Csatlakozás az Azure Storage-hoz** párbeszédpanel.
3. Válassza **az Azure-fiók hozzáadása** lehetőséget, és kattintson a **Bejelentkezés**gombra.  Folytassa az Azure-fiókba való bejelentkezéshez szükséges lépéseket.
4. A bal oldali **Intéző** ablaktáblában keresse meg a **tárfiókokat,** és bontsa ki ezt a csomópontot.
5. Kattintson a jobb gombbal a virtuális merevlemezre, és válassza a helyi menü **Megosztási hozzáférési aláírásának beírása** parancsát.

    ![SAS-elem beszereznie az Azure Explorerben](./media/publishvm_034.png)

6. Megjelenik a **Megosztott hozzáférés aláírás párbeszédpanelje.** Adja meg a következő mezők értékeit:
   - **Kezdési időpont** – A virtuális merevlemez-hozzáférés engedélyének kezdő dátuma. Adjon meg egy nappal az aktuális dátum előtt.
   - **Lejárati idő** – A virtuális merevlemez-hozzáférés engedélyének lejárati dátuma.  Adjon meg egy dátumot legalább három héttel az aktuális dátum után.
   - **Engedélyek** – `Read` Jelölje `List` ki a és az engedélyeket.

     ![SAS-párbeszédpanel az Azure Explorerben](./media/publishvm_035.png)

7. Kattintson a **Létrehozás** gombra a virtuális merevlemezhez társított SAS URI létrehozásához.  A párbeszédpanel most antól megjeleníti a művelet részleteit.
8. Másolja az **URL-értéket,** és mentse egy biztonságos helyen lévő szövegfájlba.

    ![SAS URI létrehozása az Azure Explorerben](./media/publishvm_036.png)

    Ez a létrehozott SAS URL-cím a tárolószintű hozzáféréshez.  Ahhoz, hogy ez a konkrét, a társított virtuális merevlemez nevét hozzá kell adni hozzá.

9. A szövegfájl szerkesztése. Szúrja be a VHD-nevet a `vhds` SAS URL-címében lévő karakterlánc után (adjon meg egy sorelőremutató t.  A végső SAS URI formátumúnak kell lennie:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Ha például a VDH neve `TestRGVM2.vhd`a , akkor az eredményül kapott SAS URI a következő lesz:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Ismételje meg ezeket a lépéseket minden egyes virtuális merevlemeznél a közzétenni kívánt sus-okban.

## <a name="verify-the-sas-uri"></a>A SAS URI ellenőrzése

Tekintse át és ellenőrizze az egyes létrehozott SAS URI-kat az alábbi ellenőrzőlista használatával.  Ellenőrizze, hogy:

- Az URI a következő `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + formában érhető el:`<sas-connection-string>`
- Az URI tartalmazza a VHD-lemezképfájlnevét, beleértve a ".vhd" fájlnévkiterjesztést is.
- Az URI közepe felé `sp=rl` jelenik meg. Ez a karakterlánc `Read` `List` jelzi, hogy és a hozzáférés meg van adva.
- Ezt követően `sr=c` is megjelenik. Ez a karakterlánc azt jelzi, hogy a tárolószintű hozzáférés meg van adva.
- Másolja és illessze be az URI-t egy böngészőbe a társított blob letöltésének megkezdéséhez.  (A műveletet a letöltés befejezése előtt megszakíthatja.)

## <a name="next-steps"></a>További lépések

Ha nehézségekbe ütközik egy SAS URI létrehozása, majd olvassa el [a Gyakori SAS URL-problémák](./cpp-common-sas-url-issues.md).  Ellenkező esetben mentse a SAS URI(ka)t egy biztonságos helyre későbbi használatra. A [virtuális gép ajánlatának közzététele a](./cpp-publish-offer.md) Felhőpartner-portálon.
