---
title: SAS URI beszerzése a virtuálisgép-rendszerképhez – Azure Marketplace
description: Közös hozzáférésű aláírás (SAS) URI létrehozása a virtuális merevlemezekhez (VHD) az Azure Marketplace-en.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: a84f287c6303e093d68dd462ccc5cecc34b463cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89144510"
---
# <a name="get-a-sas-uri-for-your-vm-image"></a>SAS URI beszerzése a virtuálisgép-rendszerképhez

A közzétételi folyamat során meg kell adnia egy SAS (Shared Access Signature) URI azonosítót a csomagokhoz társított minden virtuális merevlemezhez (korábban SKU-nak nevezik). A minősítési folyamat során a Microsoftnak hozzá kell férnie a virtuális merevlemezekhez. Ezt az URI-t a partner Center **csomagok** lapján adhatja meg.

Az SAS URI-k létrehozása a VHD-k számára a következő követelményekkel rendelkezik:

- Csak a nem felügyelt virtuális merevlemezeket támogatják.
- Csak a lista és az olvasási engedélyek szükségesek. Ne adjon meg írási vagy törlési hozzáférést.
- A hozzáférés időtartamának (lejárati dátum) legalább három héttel a SAS URI létrehozása után kell lennie.
- Az UTC-időváltozások elleni védelem érdekében állítsa be a kezdési dátumot az aktuális dátum előtt egy nappal. Ha például az aktuális dátum június 16., 2020, válassza a 6/15/2020 elemet.

## <a name="generate-the-sas-address"></a>SAS-címek előállítása

A SAS-cím (URL) létrehozásához két gyakori eszköz használható:

1. **Microsoft Storage Explorer** – grafikus eszköz, amely Windows, MacOS és Linux rendszerekhez érhető el.
2. **Microsoft Azure CLI** – ajánlott nem Windows operációs rendszerekhez, valamint automatizált vagy folyamatos integrációs környezetekhez.

### <a name="using-tool-1-microsoft-storage-explorer"></a>Az 1. eszköz használata: Microsoft Storage Explorer

1. [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)letöltése és telepítése.
2. Nyissa meg a Explorert, és a bal oldali menüben válassza a **fiók hozzáadása**lehetőséget.
3. A **Kapcsolódás az Azure Storage-hoz** párbeszédpanelen válassza az **Azure-fiók hozzáadása** lehetőséget, és jelentkezzen be az Azure-fiókjába.
4. A bal oldali Intéző ablaktáblán bontsa ki a **Storage-fiókok** csomópontot.
5. Kattintson a jobb gombbal a virtuális merevlemezre, majd válassza a **megosztási hozzáférés aláírásának beolvasása**lehetőséget.
6. A **megosztott hozzáférés aláírása** párbeszédpanelen hajtsa végre a következő mezőket:

    1. Kezdési idő – a VHD-hozzáférés engedélyének kezdő dátuma. Adja meg azt a dátumot, amely egy nappal az aktuális dátum előtt van.
    2. Lejárati idő – a VHD-hozzáférés engedélyének lejárati dátuma. Adjon meg legalább három héttel az aktuális dátumon túli dátumot.
    3. Engedélyek – válassza az olvasási és listázási engedélyeket.
    4. Tároló szintje – jelölje be a tároló szintű közös hozzáférésű aláírás URI-azonosítójának előállítása jelölőnégyzetet.

    ![Megosztott hozzáférés aláírása párbeszédpanel.](media/vm/create-sas-uri-storage-explorer.png)

7. Ha a társított SAS URI-t szeretné létrehozni ehhez a VHD-hez, válassza a **Létrehozás**lehetőséget. A párbeszédpanel frissül, és megjeleníti a művelet részleteit.

8. Másolja ki az URI-t, és mentse a fájlt egy biztonságos helyen lévő szövegfájlba.

    ![Az URI másolása.](media/vm/create-sas-uri-shared-access-signature-details.png)

    Ez a generált SAS URI a tároló szintű hozzáféréshez szükséges. A virtuális merevlemez nevének hozzáadásához szerkessze a szövegfájlt.

9. Szúrja be a VHD-nevet a SAS URI-ban lévő VHD-karakterlánc után (adja meg a perjelet). A végső SAS URI-nak így kell kinéznie:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

1. Ismételje meg ezeket a lépéseket a közzétenni kívánt csomagok minden virtuális merevlemezén.

### <a name="using-tool-2-azure-cli"></a>A 2. eszköz használata: Azure CLI

1. Töltse le és telepítse a [Microsoft Azure CL](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)I-t. A verziók a Windows, a macOS és a Linux különböző disztribúciói számára érhetők el.
2. Hozzon létre egy PowerShell-fájlt (. ps1 kiterjesztésű fájlt), másolja a következő kódot, majd mentse helyileg.

    ```JSON
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <vhd-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. Szerkessze a fájlt a következő paraméterérték használatára. Adja meg a dátumokat UTC dátum és idő formátumban, például: 2020-04-01T00:00:00Z.

    - fiók neve – az Azure Storage-fiók neve.
    - Account-Key – az Azure Storage-fiók kulcsa.
    - VHD-név – a VHD neve.
    - Start-Date – engedély kezdő dátuma a VHD-hozzáféréshez. Adja meg a dátumot egy nappal az aktuális dátum előtt.
    - lejárati dátum – a VHD-hozzáférés engedélyének lejárati dátuma. Legalább három héttel az aktuális dátum után adjon meg egy dátumot.

    Íme egy példa a paraméterek megfelelő értékeire (az írás időpontjában):

    `az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name vhds -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’`

1. Mentse a módosításokat.
2. A következő módszerek egyikének használatával futtassa ezt a parancsfájlt rendszergazdai jogosultságokkal SAS-kapcsolati karakterlánc létrehozásához a tároló szintű hozzáféréshez:

    - Futtassa a szkriptet a-konzolról. A Windowsban kattintson a jobb gombbal a parancsfájlra, és válassza a **Futtatás rendszergazdaként**lehetőséget.
    - Futtassa a szkriptet egy PowerShell-parancsfájl szerkesztőjéből, például [Windows PowerShell integrált parancsprogram-kezelési környezet](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Ezen a képernyőn látható az SAS-kapcsolatok karakterláncának létrehozása a szerkesztőben:

    [![SAS-kapcsolatok karakterláncának létrehozása a PowerShell-szerkesztőben](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. Másolja az SAS-kapcsolódási karakterláncot, és mentse a fájlt egy biztonságos helyen lévő szövegfájlba. Szerkessze ezt a karakterláncot a VHD-hely adatainak hozzáadásához a végső SAS URI létrehozásához.
7. A Azure Portal nyissa meg a blob Storage-t, amely tartalmazza az új URI-hoz társított virtuális merevlemezt.
8. Másolja a thebBlob szolgáltatási végpontjának URL-címét:

    ![A blob Service-végpont URL-címének másolása.](media/vm/create-sas-uri-blob-endpoint.png)

9. Szerkessze a szövegfájlt a 6. lépésből származó SAS-kapcsolatok karakterláncával. Hozza létre a teljes SAS URI-t a következő formátumban:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>Az SAS URI ellenőrzése

Győződjön meg arról, hogy az SAS URI-ja közzé van adva a partner Centerben, hogy elkerülje a kéréshez tartozó SAS URI-küldéssel kapcsolatos problémákat. Ez a folyamat nem kötelező, de ajánlott.

- Az URI tartalmazza a VHD-rendszerkép fájlnevét, beleértve a fájlnév kiterjesztését is `.vhd` .
- `Sp=rl` az URI közepe közelében jelenik meg. Ez a karakterlánc megjeleníti az olvasási és listázási hozzáférést.
- Ha `sr=c` megjelenik, az azt jelenti, hogy a tároló szintű hozzáférés meg van adva.
- Másolja és illessze be az URI-t egy böngészőbe a blob letöltéséhez (a letöltés befejezése előtt megszakíthatja a műveletet).

## <a name="next-step"></a>Következő lépés

- Olvassa el [Az Azure-beli virtuális gépek ajánlatának létrehozása](azure-vm-create-offer.md)című cikkből.
