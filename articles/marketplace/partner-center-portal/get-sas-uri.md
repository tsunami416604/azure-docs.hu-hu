---
title: Megosztott hozzáférési aláírás URI-jának beletöltése a virtuális gép lemezképéhez | Azure Piactér
description: Ez a cikk bemutatja, hogyan szerezheti be a megosztott hozzáférésű aláírás (SAS) URI-t az egyes virtuális merevlemezekhez (VHD).
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: 50bda733b0bb8b0c98eb69a15ab3000ad278031c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265706"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Megosztott hozzáférésű aláírás URI-jának beszereznie a virtuális gép lemezképéhez

> [!IMPORTANT]
> Az Azure virtuálisgép-ajánlatok kezelését áthelyezzük a Cloud Partner Portalról a Partnerközpontba. Amíg az ajánlatok áttelepítése, kövesse a [Get shared access signature URI a virtuális gép képéhez](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri) a Cloud Partner Portal az ajánlatok kezeléséhez kövesse az utasításokat.

Ez a cikk azt ismerteti, hogyan hozhat létre egy közös hozzáférésű aláírás (SAS) egységes erőforrás-azonosító (URI) minden egyes virtuális merevlemez (VHD).

A közzétételi folyamat során meg kell adnia egy URI-t minden egyes virtuális merevlemezhez, amely a tervekhez van társítva. Ezeket a terveket korábban SK-nak vagy raktározási egységeknek nevezték. A Microsoftnak a minősítési folyamat során hozzá kell férnie ezekhez a virtuális gépekhez. Ezt az URI-t a Partnerközpont **Tervek** lapján adhatja meg.

A Virtuális gépazonosítók SAS URI-inak létrehozásakor kövesse az alábbi követelményeket:

* Csak a nem felügyelt virtuális gépek támogatottak.
* Csak `List` `Read` és engedélyek szükségesek. Ne adjon írási vagy törlési hozzáférést.
* A hozzáférés időtartama (lejárati dátum) legalább három hétnek kell lennie a SAS URI létrehozásától számítva.
* Az UTC-időváltozások elleni védelem érdekében állítsa a kezdő dátumot egy nappal az aktuális dátum előtt. Ha például az aktuális dátum 2019.

## <a name="generate-the-sas-address"></a>A SAS-cím létrehozása

A SAS-címek (URL-címek) létrehozásához két közös eszköz használható:

* **Microsoft Storage Explorer** – Grafikus eszköz elérhető Windows, macOS és Linux.
* **Microsoft Azure CLI** – Nem Windows operációs rendszerekhez és automatizált vagy folyamatos integrációs környezetekhez ajánlott.

### <a name="use-microsoft-storage-explorer"></a>A Microsoft Storage Explorer használata

1. Töltse le és telepítse a [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)alkalmazást.
2. Nyissa meg a felfedezőt, és a bal oldali menüben válassza a **Fiók hozzáadása parancsot.** Megjelenik **a Csatlakozás az Azure Storage-hoz** párbeszédpanel.
3. Válassza **az Azure-fiók hozzáadása lehetőséget,** majd **a Bejelentkezés**lehetőséget. Végezze el az Azure-fiókba való bejelentkezéshez szükséges lépéseket.
4. A bal oldali**Intéző** ablaktáblában nyissa meg a **tárfiókokat,** és bontsa ki ezt a csomópontot.
5. Kattintson a jobb gombbal a virtuális merevlemezre, és válassza **a Megosztási aláírás bekerülése parancsot.**
6. Megjelenik a **Megosztott hozzáférés aláírás** párbeszédpanelje. Töltse ki a következő mezőket:

    * **Kezdési időpont** – A virtuális merevlemez-hozzáférés engedélyezési dátuma. Adjon meg egy nappal az aktuális dátum előtt.
    * **Lejárati idő** – A virtuális merevlemez-hozzáférés engedélyének lejárati dátuma. Adjon meg egy dátumot, amely legalább három héttel az aktuális dátum után van.
    * **Engedélyek** – Válassza ki az Olvasási és lista engedélyeket.
    * **Tárolószintű** – Jelölje be a **Tárolószintű megosztott hozzáférésű hozzáférési aláírás URI-jának létrehozása** jelölőnégyzetet.

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="A Megosztott hozzáférés aláíráspárbeszédpanel ének szemléltetése":::

7. A virtuális merevlemezhez társított SAS-URI létrehozásához válassza a **Létrehozás gombot.** A párbeszédpanel frissül, és megjeleníti a művelet részleteit.
8. Másolja az **URI-t,** és mentse egy biztonságos helyen lévő szövegfájlba.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="A Megosztott hozzáférésű aláírás részleteinek szemléltetése":::

    Ez a létrehozott SAS URI a tárolószintű hozzáféréshez. Ha azt szeretné, hogy ez az adott, szerkesztheti a szövegfájlt, hogy adja hozzá a VHD nevét (következő lépés).

9. Szúrja be a vHDs-nevet a SAS URI-ban a vhds karakterlánc után (adjon meg egy perjelet). A végső SAS URI így kell kinéznie:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`Ha például a VDH neve `TestRGVM2.vhd`a , akkor az eredményül kapott SAS URI a következő lesz:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. Ismételje meg ezeket a lépéseket az egyes virtuális merevlemezek a közzé tenni kívánt tervekben.

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

1. Töltse le és telepítse a [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)szolgáltatást. A verziók elérhetők A Windows, macOS, és a különböző disztribúciók a Linux.
2. Hozzon létre egy PowerShell-fájlt (.ps1 fájlkiterjesztést), másolja a következő kódot, majd mentse helyileg.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. A következő paraméterértékek et használva szerkesztse a fájlt. Adja meg a dátumokat UTC `2020-04-01T00:00:00Z`datetime formátumban, például .

    * `<account-name>`– Az Azure tárfiók neve
    * `<account-key>`– Az Azure tárfiók kulcsa
    * `<vhd-name>`- A VHD neve
    * `<start-date>`– Engedély kezdő dátuma VHD hozzáférés. Adjon meg egy dátumot egy nappal az aktuális dátum előtt.
    * `<expiry-date>`– Engedély lejárati dátum a Virtuális merevlemez-hozzáféréshez. Adja meg a dátumot legalább három héttel az aktuális dátum után.

    Ez a példa a megfelelő paraméterértékeket mutatja (az írás időpontjában):

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Mentse a módosításokat.
5. Az alábbi módszerek egyikével futtassa ezt a parancsfájlt rendszergazdai jogosultságokkal, és hozzon létre egy **SAS-kapcsolati karakterláncot** a tárolószintű hozzáféréshez:

    * Futtassa a parancsfájlt a konzolról. Windows rendszerben kattintson a jobb gombbal a parancsfájlra, és válassza a **Futtatás rendszergazdaként parancsot.**
    * Futtassa a parancsfájlt egy PowerShell-parancsfájl-szerkesztőből, például a [Windows PowerShell ISE-ből.](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise) Ez a képernyő egy SAS-kapcsolati karakterlánc létrehozását mutatja a szerkesztőben:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="SAS-kapcsolati karakterlánc létrehozását szemlélteti a Windows PowerShell ISE-vel":::

6. Másolja a SAS-kapcsolati karakterláncot, és mentse egy biztonságos helyen lévő szövegfájlba. A karakterlánc szerkesztésével adja hozzá a Virtuális merevlemez helyadatait a végső SAS URI létrehozásához.
7. Az Azure Portalon nyissa meg a blob storage, amely tartalmazza az új URI-hoz társított virtuális merevlemezt.
8. Másolja a **Blob szolgáltatás végpontjának URL-címét,** ahogy az az alábbi képernyőképen látható

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="A Blob szolgáltatás végpontjának szemléltetése":::

9. A szövegfájl szerkesztése a SAS-kapcsolati karakterlánccal a 6. Hozza létre a teljes SAS URI-t a következő formátumban:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Ha például a virtuális merevlemez `TestRGVM2.vhd`neve a következő:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Ismételje meg ezeket a lépéseket minden egyes virtuális merevlemeznél a közzétenni kívánt sus-okban.

## <a name="verify-the-sas-uri"></a>A SAS URI ellenőrzése

Tekintse át az egyes létrehozott SAS URI-kat az alábbi ellenőrzőlista segítségével annak ellenőrzéséhez, hogy:

* Az URI így néz ki:`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* Az URI tartalmazza a VHD-lemezképfájlnevét, beleértve a ".vhd" fájlnévkiterjesztést is.
* `sp=rl`az URI közepén jelenik meg. Ez a `Read` karakterlánc `List` azt mutatja, hogy és a hozzáférés meg van adva.
* Amikor `sr=c` megjelenik, ez azt jelenti, hogy a tárolószintű hozzáférés meg van adva.
* Másolja és illessze be az URI-t egy böngészőbe a blob teszteléséhez (a műveletet a letöltés befejezése előtt megszakíthatja).

## <a name="next-step"></a>Következő lépés

Ha nehézségekbe ütközik a SAS URI létrehozása, olvassa el [a Gyakori SAS URL-problémák](https://aka.ms/AzureSAS_URI_FAQ). Ellenkező esetben mentse a SAS URI(ka)t egy biztonságos helyre későbbi használatra. Szüksége lesz rá, hogy közzétegye a virtuális gép ajánlatát a Partner központban.

* [Azure virtuálisgép-ajánlat létrehozása](https://aka.ms/Create_AzureVMoffer)
