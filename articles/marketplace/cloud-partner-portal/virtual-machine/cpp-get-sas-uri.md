---
title: Közös hozzáférésű jogosultságkód URI Azonosítójának lekérése a Microsoft Azure-alapú Virtuálisgép-lemezkép |} A Microsoft Docs
description: Azt ismerteti, hogyan tehet szert a közös hozzáférésű jogosultságkód (SAS) URI-azonosítóját a Virtuálisgép-lemezkép.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: dcfe744cc8ca6f3b3cd201898a79fcce3f24f8d5
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639771"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>A Virtuálisgép-lemezkép közös hozzáférésű jogosultságkód URI Azonosítójának lekérése

A közzétételi folyamat során meg kell adnia egy egységes erőforrás-azonosítója (URI) minden virtuális merevlemez (VHD) a termékváltozatok társított. Ezek a Microsoft számára szükségesek a virtuális merevlemezek eléréséhez a minősítési folyamat során. Ez a cikk bemutatja, hogyan hozhat létre egy közös hozzáférésű jogosultságkód (SAS) URI-t minden egyes virtuális merevlemezhez. Az URI-t kell megadnia a **termékváltozatok** a Cloud Partner portálra a lap. 

A virtuális merevlemezek SAS URI generálásakor tartaniuk az alábbi követelményeknek:

- Csak a nem felügyelt virtuális merevlemezek használata támogatott.
- `List` és `Read` elegendő. Tegye *nem* biztosítanak `Write` vagy `Delete` hozzáférést.
- A hozzáférés időtartama (*lejárati dátuma*) kell lennie legalább három héttel az SAS URI létrehozása során.
- Hozzáférésekkel szemben UTC idő változata létezik, állítsa be a kezdő dátum az aktuális dátum előtt egy nappal. Jelölje be például, ha az aktuális dátum későbbi, 2014. október 6., 10/5/2014.

## <a name="generate-the-sas-url"></a>Az SAS URL-cím létrehozása

Az SAS URL-cím a következő eszközökkel közös kétféleképpen hozható létre:

-   A Microsoft Storage Explorer – Windows, macOS és Linux rendszeren elérhető grafikus eszköz
-   A Microsoft Azure CLI-vel – nem Windows operációs rendszerek és az automatikus és folyamatos integrációs környezetek esetén ajánlott


### <a name="azure-cli"></a>Azure CLI

Az alábbi lépések segítségével hozzon létre egy SAS URI-t az Azure CLI használatával.

1.  Töltse le és telepítse a [a Microsoft Azure CLI-vel](https://azure.microsoft.com/documentation/articles/xplat-cli-install/).  A Windows, macOS és Linux-különböző disztribúciók verziói érhetők el. 
2.  Hozzon létre egy PowerShell-fájlt (`.ps1` kiterjesztése), másolja az alábbi kódot, majd a helyileg menteni.

    ``` powershell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```
    
3.  Szerkessze a fájlt a következő paraméterértékek megadásához.  Dátumok meg kell adni az UTC-dátum és idő formátumban, például `10-25-2016T00:00:00Z`.
    - `<account-name>` -A az Azure storage-fiók neve
    - `<account-key>` -A az Azure storage-fiókkulcs
    - `<vhd-name>` – A virtuális merevlemez neve
    - `<start-date>` -Virtuális merevlemez hozzáférés permission kezdete. Adjon meg egy az aktuális dátum előtt egy nappal korábbi dátum. 
    - `<expiry-date>` -Engedély lejárati dátuma a VHD-hozzáféréshez.  Adjon meg egy dátumot legalább három héttel az aktuális dátumnál. 
 
    Az alábbi példa bemutatja a megfelelő paraméter értékét (a cikk írásának időpontjában).

    ``` powershell
        az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
    ```
 
4. Mentse a módosításokat a PowerShell-szkript.
5. Futtassa ezt a szkriptet, rendszergazdai jogosultságokkal használatával létrehozni egy *SAS kapcsolati karakterlánc* tároló szint eléréshez.  Két alapvető módszerét használhatja:
    - Futtassa a szkriptet a konzolon.  Például Windows, az írási kattintással a parancsfájlt, majd válassza a **Futtatás rendszergazdaként**.
    - Futtassa a parancsfájlt a PowerShell parancsprogram-szerkesztő, például a [Windows PowerShell ISE-ben](https://docs.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise), rendszergazdai jogosultságokkal. 
  A következő azt mutatja be a szerkesztő belül létrehozott SAS kapcsolati karakterláncot. 

    ![SAS URI létrehozása a PowerShell ISE-ben](./media/publishvm_032.png)

6. Másolja az eredményül kapott SAS-kapcsolati karakterláncot, és mentse a fájlt egy biztonságos helyre.  Ez a karakterlánc hozzáadása a társított virtuális merevlemez helyre vonatkozó adatokat, hogy a végső SAS URI létrehozása fog szerkesztése. 
7. Az Azure Portalon lépjen a blob Storage, amely tartalmazza a virtuális Merevlemezhez társított az újonnan létrehozott URI.
8. Másolja az URL-cím értékét a **Blob-szolgáltatásvégpont**lent látható módon.

    ![Az Azure Portallal BLOB-szolgáltatásvégpont](./media/publishvm_033.png)

9. Szerkessze a szövegfájlt a 6. lépés SAS kapcsolati karakterláncra.  A teljes SAS URI-t a következő formátumban lesznek hozhat létre:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Például, ha a rendszerkötetekre végzett neve `TestRGVM2.vhd`, majd az eredményül kapott SAS URI lesz:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Ismételje meg ezeket a lépéseket minden egyes virtuális merevlemezhez a közzétételének termékváltozatban.


### <a name="microsoft-storage-explorer"></a>Microsoft Storage Explorer

A következő lépések segítségével hozzon létre egy SAS URI-t az a Microsoft Azure Storage Explorerrel.

1. A [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) letöltése és telepítése.
2. A tallózó megnyitásához, és kattintson a bal oldali menüsáv a a **fiók hozzáadása** ikonra.  A **csatlakozás az Azure Storage** párbeszédpanel jelenik meg.
3. Válassza az **Azure-fiók hozzáadása** lehetőséget, majd kattintson a **Bejelentkezés** gombra.  Továbbra is szükséges lépéseket, jelentkezzen be az Azure-fiókjával.
4. A bal oldali a **Explorer** ablaktáblán keresse meg a **Tárfiókok** és bontsa ki a csomópontot.
5. Kattintson a jobb gombbal a VHD-t, és válassza a **megosztás hozzáférésű** a helyi menüből. 

    ![Az Azure Explorer SAS-elem lekérése](./media/publishvm_034.png)

6. A **közös hozzáférésű Jogosultságkód** párbeszédpanel jelenik meg. Adja meg az értékeket a következő mezőket:
    - **Kezdési idő** -engedély kezdő dátuma a VHD-hozzáféréshez. Adja meg, amely egy nappal az aktuális dátum előtt dátumát.
    - **Lejárati idő** -engedély lejárati dátuma a VHD-hozzáféréshez.  Adjon meg egy dátumot legalább három héttel az aktuális dátumnál.
    - **Engedélyek** – válassza ki a `Read` és `List` engedélyeket. 

    ![Az Azure Explorer SAS párbeszédpanel](./media/publishvm_035.png)

7. Kattintson a **létrehozás** ehhez a virtuális merevlemezhez tartozó SAS URI létrehozásához.  A párbeszédpanel most Ez a művelet részleteit jeleníti meg. 
8. Másolás a **URL-cím** értékét, és mentse a fájlt egy biztonságos helyre. 

    ![SAS URI létrehozása az Azure Explorer](./media/publishvm_036.png)

    A létrehozott SAS URL-címe van a tároló-szintű hozzáféréshez.  Hogy adott, a társított virtuális merevlemez nevét hozzá kell adni.

9. A szöveges fájl szerkesztésével. Helyezze be a virtuális merevlemez neve után a `vhds` karakterlánc az SAS URL-címben (beleértve a vezető perjellel).  A végső SAS URI-t a következő formátumban kell lennie:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Például, ha a rendszerkötetekre végzett neve `TestRGVM2.vhd`, majd az eredményül kapott SAS URI lesz:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Ismételje meg ezeket a lépéseket minden egyes virtuális merevlemezhez a közzétételének termékváltozatban.


## <a name="verify-the-sas-uri"></a>Ellenőrizze az SAS URI-t

Tekintse át, és ellenőrizze, hogy minden egyes létrehozott SAS URI-t, az alábbi ellenőrzőlista használatával.  Ellenőrizze a következőket:
- Az URI-ja a következő formában:       `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
- Az URI-t a virtuális merevlemez kép filename, többek között a fájlnév-kiterjesztés ".vhd" tartalmazza.
- A középen az URI felé `sp=rl` jelenik meg. Ez a karakterlánc azt jelzi, hogy `Read` és `List` hozzáférés meg van adva.
- Ezután `sr=c` is megjelenik. Ez a karakterlánc, az azt jelzi, hogy a tároló-szintű hozzáférést van megadva.
- Másolja és illessze be az URI-t egy böngészőt, és megkezdheti a társított blob letöltéséhez.  (Megszakíthatja a műveletet a letöltés befejezése előtt.)


## <a name="next-steps"></a>További lépések

Ha egy SAS URI létrehozása nehézségek merülnek fel, tekintse meg [közös SAS URL-címet állít](./cpp-common-sas-url-issues.md).  Ellenkező esetben a SAS URI(s) mentse egy biztonságos helyre későbbi használat céljából. Szükség lesz [a Virtuálisgép-ajánlat közzététele](./cpp-publish-offer.md) az a Cloud Partner portálra.
