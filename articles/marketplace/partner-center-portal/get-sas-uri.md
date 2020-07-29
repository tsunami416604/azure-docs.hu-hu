---
title: Virtuálisgép-lemezképek közös hozzáférés-aláírási URI-ja – Azure Marketplace
description: Közös hozzáférésű aláírás (SAS) URI létrehozása a virtuális merevlemezekhez (VHD) az Azure Marketplace-en.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 07/14/2020
ms.openlocfilehash: f3589fb9ae176e04f727f516cca7c18c87dad9e0
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87317501"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>A virtuálisgép-rendszerkép közös hozzáférésű aláírási URI-azonosítójának beolvasása

Ez a cikk azt ismerteti, hogyan hozhatja ki a közös hozzáférésű aláírás (SAS) egységes erőforrás-azonosítóját (URI) az egyes virtuális merevlemezekhez (VHD).

A közzétételi folyamat során meg kell adnia egy URI-t minden olyan virtuális merevlemezhez, amely hozzá van rendelve a csomagokhoz (korábbi nevén SKU). A minősítési folyamat során a Microsoftnak hozzá kell férnie a virtuális merevlemezekhez. Ezt az URI-t a partner Center **csomagok** lapján adhatja meg.

Ha SAS URI-ket hoz létre a virtuális merevlemezekhez, kövesse az alábbi követelményeket:

* Csak a nem felügyelt virtuális merevlemezek támogatottak.
* Csak `List` és `Read` engedélyek szükségesek. Ne adjon meg írási vagy törlési hozzáférést.
* A hozzáférés időtartamának (lejárati dátum) legalább három héttel a SAS URI létrehozása után kell lennie.
* Az UTC-időváltozások elleni védelem érdekében állítsa be a kezdési dátumot az aktuális dátum előtt egy nappal. Ha például az aktuális dátum október 6., 2019, válassza a 10/5/2019 elemet.

## <a name="generate-the-sas-address"></a>SAS-címek előállítása

A SAS-cím (URL) létrehozásához két gyakori eszköz használható:

* **Microsoft Storage Explorer** – grafikus eszköz, amely Windows, MacOS és Linux rendszerekhez érhető el.
* **Microsoft Azure CLI** – ajánlott nem Windows operációs rendszerekhez, valamint automatizált vagy folyamatos integrációs környezetekhez.

### <a name="use-microsoft-storage-explorer"></a>A Microsoft Storage Explorer használata

1. [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)letöltése és telepítése.
2. Nyissa meg a Explorert, és a bal oldali menüben válassza a **fiók hozzáadása**lehetőséget. Megjelenik a **Kapcsolódás az Azure Storage-hoz** párbeszédpanel.
3. Válassza **Az Azure-fiók hozzáadása** lehetőséget, majd **Jelentkezzen**be. Fejezze be az Azure-fiókjába való bejelentkezéshez szükséges lépéseket.
4. A bal**oldali ablaktábla** ablaktábláján lépjen a Storage- **fiókok** elemre, és bontsa ki a csomópontot.
5. Kattintson a jobb gombbal a virtuális merevlemezre, majd válassza a **megosztási hozzáférés aláírásának beolvasása**elemet.
6. Megjelenik a **megosztott hozzáférés aláírása** párbeszédpanel. Hajtsa végre a következő mezőket:

    * **Kezdési idő** – a VHD-hozzáférés engedélyének kezdő dátuma. Adja meg azt a dátumot, amely egy nappal az aktuális dátum előtt van.
    * **Lejárati idő** – a VHD-hozzáférés engedélyének lejárati dátuma. Olyan dátumot adjon meg, amely legalább három héttel az aktuális dátumon túl van.
    * **Engedélyek** – válassza az olvasási és listázási engedélyeket.
    * **Tároló szintje** – jelölje be a **tároló szintű közös hozzáférésű aláírás URI-azonosítójának előállítása** jelölőnégyzetet.

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="A közös hozzáférésű aláírás párbeszédpanel megjelenítése":::

7. Ha a társított SAS URI-t szeretné létrehozni ehhez a VHD-hez, válassza a **Létrehozás**lehetőséget. A párbeszédpanel frissül, és megjeleníti a művelet részleteit.
8. Másolja ki az **URI** -t, és mentse a fájlt egy biztonságos helyen lévő szövegfájlba.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Szemlélteti a közös hozzáférési aláírás részleteit tartalmazó mezőt":::

    Ez a generált SAS URI a tároló szintű hozzáféréshez szükséges. Ha szeretné, a szövegfájl szerkesztésével adja hozzá a VHD-nevet (következő lépés).

9. Szúrja be a VHD-nevet a SAS URI-ban lévő VHD-karakterlánc után (adja meg a perjelet). A végső SAS URI-nak így kell kinéznie:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`Ha például a VDH neve `TestRGVM2.vhd` , akkor az eredményül kapott sas URI a következő lesz:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. Ismételje meg ezeket a lépéseket a közzétenni kívánt csomagok minden virtuális merevlemezén.

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

1. Töltse le és telepítse [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)-t. A verziók a Windows, a macOS és a Linux különböző disztribúciói számára érhetők el.
2. Hozzon létre egy PowerShell-fájlt (. ps1 kiterjesztésű fájlt), másolja a következő kódot, majd mentse helyileg.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Szerkessze a fájlt a következő paraméterérték használatára. Adja meg a dátumokat UTC dátum és idő formátumban, például: `2020-04-01T00:00:00Z` .

    * `<account-name>`– Az Azure Storage-fiók neve
    * `<account-key>`– Az Azure Storage-fiók kulcsa
    * `<vhd-name>`– A virtuális merevlemez neve
    * `<start-date>`– A VHD-hozzáférés engedélyének kezdő dátuma. Adja meg a dátumot egy nappal az aktuális dátum előtt.
    * `<expiry-date>`– A VHD-hozzáférés engedélyének lejárati dátuma. Legalább három héttel az aktuális dátum után adjon meg egy dátumot.

    Ez a példa a megfelelő paraméterek értékeit mutatja be (az írás időpontjában):

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Mentse a módosításokat.
5. A következő módszerek egyikének használatával futtassa ezt a parancsfájlt rendszergazdai jogosultságokkal sas- **kapcsolati karakterlánc** létrehozásához a tároló szintű hozzáféréshez:

    * Futtassa a szkriptet a-konzolról. A Windowsban kattintson a jobb gombbal a parancsfájlra, és válassza a **Futtatás rendszergazdaként**lehetőséget.
    * Futtassa a szkriptet egy PowerShell-parancsfájl szerkesztőjéből, például [Windows PowerShell integrált parancsprogram-kezelési környezet](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Ezen a képernyőn látható az SAS-kapcsolatok karakterláncának létrehozása a szerkesztőben:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Egy SAS-kapcsolatok karakterláncának létrehozását mutatja Windows PowerShell integrált parancsprogram-kezelési környezet":::

6. Másolja az SAS-kapcsolódási karakterláncot, és mentse a fájlt egy biztonságos helyen lévő szövegfájlba. Szerkessze ezt a karakterláncot a VHD-hely adatainak hozzáadásához a végső SAS URI létrehozásához.
7. A Azure Portal nyissa meg a blob Storage-t, amely tartalmazza az új URI-hoz társított virtuális merevlemezt.
8. Másolja az **blob Service-végpont**URL-címét az alábbi képernyőképen látható módon

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Az Blob service végpont bemutatása":::

9. Szerkessze a szövegfájlt a 6. lépésből származó SAS-kapcsolatok karakterláncával. Hozza létre a teljes SAS URI-t a következő formátumban:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Ha például a VHD neve `TestRGVM2.vhd` , az SAS URI a következő lesz:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Ismételje meg ezeket a lépéseket a közzétenni kívánt csomagok minden virtuális merevlemezén.

## <a name="verify-the-sas-uri"></a>Az SAS URI ellenőrzése

Tekintse át az összes létrehozott SAS URI-t az alábbi ellenőrzőlista használatával:

* Az URI a következőképpen néz ki:`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* Az URI tartalmazza a VHD-rendszerkép fájlnevét, beleértve a ". vhd" fájlnevet.
* `sp=rl`az URI közepe közelében jelenik meg. Ez a karakterlánc azt mutatja, hogy a `Read` és a `List` hozzáférés meg van adva.
* Ha `sr=c` megjelenik, az azt jelenti, hogy a tároló szintű hozzáférés meg van adva.
* Másolja és illessze be az URI-t egy böngészőbe a blob letöltéséhez (a letöltés befejezése előtt megszakíthatja a műveletet).

## <a name="next-step"></a>Következő lépés

Ha problémái vannak az SAS URI létrehozásakor, tekintse meg az [általános sas URL-címekkel kapcsolatos](common-sas-uri-issues.md)témakört. Ellenkező esetben mentse a SAS URI-JÁT biztonságos helyre a későbbi használat érdekében. Szüksége lesz rá a virtuálisgép-ajánlat közzétételéhez a partner Centerben.

* [Azure-beli virtuális gép ajánlatának létrehozása](azure-vm-create-offer.md)
