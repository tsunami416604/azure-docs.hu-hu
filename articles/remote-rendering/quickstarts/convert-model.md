---
title: Modell konvertálása
description: Rövid útmutató, amely egy egyéni modell konvertálási lépéseit mutatja be.
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: 7ba8d201c29b5e3835fec52d8c479a388ca07f71
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312991"
---
# <a name="quickstart-convert-a-model-for-rendering"></a>Rövid útmutató: Modell konvertálása rendereléshez

A [rövid útmutatóban: Rendereljen egy modellt unity,](render-model.md)megtanulta, hogyan kell használni a Unity mintaprojekt renderelése egy beépített modell. Ez az útmutató bemutatja, hogyan konvertálhatja saját modelljeit.

A következőket fogja megtanulni:

> [!div class="checklist"]
>
> * Azure blobstorage-fiók beállítása bemenethez és kimenethez
> * 3D modell feltöltése és konvertálása az Azure távoli rendereléséhez
> * Az átalakított 3D modell felvétele renderelésre szolgáló alkalmazásba

## <a name="prerequisites"></a>Előfeltételek

* Teljes [rövid útmutató: Modell renderelése unity](render-model.md)
* Az Azure PowerShell telepítése [(dokumentáció)](https://docs.microsoft.com/powershell/azure/)
  * Rendszergazdai jogosultságokkal rendelkező PowerShell megnyitása
  * Fuss:`Install-Module -Name Az -AllowClobber`

## <a name="overview"></a>Áttekintés

A kiszolgálón lévő megjelenítő nem tud közvetlenül működni a forrásmodell-formátumokban, például az FBX-szel vagy a GLTF-fel. Ehelyett megköveteli, hogy a modell egy saját bináris formátumban legyen.
A konverziós szolgáltatás az Azure blob storage-ból származó modelleket használ fel, és a konvertált modelleket visszaküldi egy megadott Azure blobtároló-tárolóba.

A következők szükségesek:

* Azure-előfizetés
* "StorageV2" fiók az előfizetésben
* Blob storage-tároló a bemeneti modellhez
* Blob storage-tároló a kimeneti adatokhoz
* Konvertálandó modell, [lásd: mintamodellek](../samples/sample-model.md)
  * A támogatott [forrásformátumok](../how-tos/conversion/model-conversion.md#supported-source-formats) listájának megtekintése
  * A mintakonverziós parancsfájl használatához győződjön meg arról, hogy előkészítegy bemeneti mappát, amely tartalmazza a modellt és az összes külső függőséget (például külső textúrákat vagy geometriát)

## <a name="azure-setup"></a>Az Azure beállítása

Ha még nincs fiókja, látogasson [https://azure.microsoft.com/get-started/](https://azure.microsoft.com/get-started/)el a , kattintson az ingyenes fiók opcióra, és kövesse az utasításokat.

Ha rendelkezik Azure-fiókkal, nyissa meg a lehetőséget. [https://ms.portal.azure.com/#home](https://ms.portal.azure.com/#home)

### <a name="storage-account-creation"></a>Tárfiók létrehozása

Blob storage létrehozásához először szüksége van egy tárfiókra.
Ha létre szeretne hozni egyet, kattintson az "Erőforrás létrehozása" gombra:

![Azure – erőforrás hozzáadása](media/azure-add-a-resource.png)

Az új képernyőn válassza a **Tárolás** a bal oldalon, majd **a Storage fiók - blob, fájl, tábla, várólista** a következő oszlopból:

![Azure – tárhely hozzáadása](media/azure-add-storage.png)

Erre a gombra kattintva megjelenik a következő képernyő, amelyen tárolótulajdonságok vannak kitöltve:

![Azure beállítása](media/azure-setup1.png)

Töltse ki az űrlapot a következő módon:

* Hozzon létre egy új erőforráscsoportot a legördülő lista alatti hivatkozásból, és nevezze el ezt a **ARR_Tutorial**
* A **Storage-fiók nevéhez**itt adjon meg egy egyedi nevet. **Ennek a névnek globálisan egyedinek kell lennie,** ellenkező esetben egy üzenet jelenik meg, amely tájékoztatja, hogy a név készen áll. A program a rövid útmutató hatókörében **arrtutorialstorage-nak**nevezzük. Ennek megfelelően le kell cserélnie a nevére minden olyan esemény ebben a rövid útmutatóban.
* Válasszon egy önhöz közeli **helyet.** Ideális esetben ugyanazt a helyet használja, mint a többi rövid útmutatóban a renderelés beállítására.
* **"Standard"** teljesítményre állítva
* "StorageV2 (általános célú v2)" típusú **fiókfajta**
* "Read-access georedundáns tárolás (RA-GRS)" lesz a **replikációs** replikációs
* **"Forró" hozzáférési szint**

A többi lap egyik tulajdonságát sem kell módosítani, így folytathatja a **"Véleményezés + létrehozás"** kifejezést, majd a telepítés befejezéséhez kövesse a lépéseket.

A webhely most tájékoztatja Önt a központi telepítés előrehaladásáról, és a "A telepítés befejeződött" jelentést jelent. A következő lépésekhez kattintson az "Ugrás az **erőforráshoz"** gombra:

![Az Azure Storage létrehozása kész](./media/storage-creation-complete.png)

### <a name="blob-storage-creation"></a>Blob-tároló létrehozása

Ezután két blobtárolóra van szükség, az egyik a bemenethez, a másik a kimenethez.

A fenti "Ugrás az **erőforráshoz"** gombról egy listamenüt tartalmazó panellel rendelkező laphoz jut. Ebben a listában a **"Blob szolgáltatás"** kategóriában kattintson a **"Konténerek"** gombra:

![Azure – tárolók hozzáadása](./media/azure-add-containers.png)

Nyomja meg a **"+ tároló"** gombot a **bemeneti** blob tároló tároló létrehozásához.
Létrehozásakor használja a következő beállításokat:
  
* Név = arrinput
* Nyilvános hozzáférési szint = Privát

A tároló létrehozása után kattintson ismét **a + Container** gombra, és ismételje meg a **kimeneti** tároló beállításait:

* Név = arroutput
* Nyilvános hozzáférési szint = Privát

Most már két blob tárolótárolóval kell rendelkeznie:

![Blob Storage beállítása](./media/blob-setup.png)

## <a name="run-the-conversion"></a>Az átalakítás futtatása

Az eszközkonverziós szolgáltatás hívásának megkönnyítése érdekében egy segédprogram-parancsfájlt biztosítunk. Ez található a *Scripts* mappába, és az úgynevezett **Conversion.ps1**.

Ez a szkript különösen

1. feltölti az adott könyvtárban lévő összes fájlt a helyi lemezről a bemeneti tárolóba
1. meghívja [az eszközkonverziós REST API-t,](../how-tos/conversion/conversion-rest-api.md) amely lekéri az adatokat a bemeneti tárolóból, és konverziót indít, amely konverziós azonosítót ad vissza
1. a konverziós állapot API lekérdezése a beolvasott konverzióazonosítóval, amíg a konverziós folyamat sikeres vagy sikertelen
1. a kimeneti tárolóban lévő konvertált eszközre mutató hivatkozást keresi le

A parancsfájl a *Parancsfájl\arrconfig.json*fájlból olvassa be a konfigurációját. Nyissa meg a JSON-fájlt egy szövegszerkesztőben.

```json
{
    "accountSettings": {
        "arrAccountId": "8*******-****-****-****-*********d7e",
        "arrAccountKey": "R***************************************l04=",
        "region": "<your-region>"
    },
    "renderingSessionSettings": {
        "vmSize": "standard",
        "maxLeaseTime": "1:00:00"
    },
    "assetConversionSettings": {
        "localAssetDirectoryPath": "D:\\tmp\\robot",
        "resourceGroup": "ARR_Tutorial",
        "storageAccountName": "arrexamplestorage",
        "blobInputContainerName": "arrinput",
        "inputFolderPath": "robotConversion",
        "inputAssetPath": "robot.fbx",
        "blobOutputContainerName": "arroutput",
        "outputFolderPath":"converted/robot",
        "outputAssetFileName": "robot.arrAsset"
    }
}
```

A **fiókbeállítások** csoporton (fiókazonosító és kulcs) belüli konfigurációt a Unity [rövid útmutatóval rendelkező modell renderelése](render-model.md)című fázisban lévő hitelesítő adatokhoz hasonlóan kell kitölteni.

Az **assetConversionSettings** csoporton belül győződjön meg arról, hogy módosítsa **az erőforráscsoportot**, **a blobInputContainerName**és **a blobOutputContainerName** értéket a fentiek szerint.
Vegye figyelembe, hogy az **arrtutorialstorage** értéket le kell cserélni a tárfiók létrehozása során kiválasztott egyedi névvel.

Módosítsa a **localAssetDirectoryPath programot** úgy, hogy a lemezen lévő könyvtárra mutasson, amely a konvertálni kívánt modellt tartalmazza. Ügyeljen arra, hogy megfelelően\\elkerülje a fordított perjeleket ("\\\\") az elérési úton dupla fordított perjelekkel (" ").

A **localAssetDirectoryPath** elérési útjáról származó összes adat a **blobInputContainerName blob-tárolóba** kerül az **inputFolderPath**által megadott részelérési út alatt. Így a példa konfiguráció felett a tartalom\\a\\"D: tmp robot" könyvtár lesz feltöltve a blob konténer "arrinput" a tárfiók "arrtutorialstorage" elérési út alatt "robotConversion". A már meglévő fájlok felülíródnak.

Módosítsa **az inputAssetPath-ot** a konvertálandó modell elérési útvonalára – az elérési út a localAssetDirectoryPath-hoz viszonyítva van. Használja a "/"\\helyett a " " részt az útvonalelválasztóként. Tehát egy "robot.fbx" fájl, amely közvetlenül\\a\\"D: tmp robot" használja "robot.fbx".

A modell konvertálása után a rendszer visszaírja a **blobOutputContainerName**által megadott tárolóba. A másodlagos elérési út a választható **outputFolderPath**paraméter megadásával adható meg. A fenti példában az eredményül kapott "robot.arrAsset" lesz másolva a kimeneti blob tároló alatt "konvertált/robot".

A **outputAssetFileName** konfigurációs beállítás határozza meg a konvertált eszköz nevét - a paraméter nem kötelező, és a kimeneti fájlnév egyébként a bemeneti fájl nevéből lesz levezethető. 

Nyisson meg egy PowerShellt, győződjön meg arról, hogy telepítette az *Azure PowerShellt* az [előfeltételekben említettek szerint.](#prerequisites) Ezután jelentkezzen be az előfizetésbe a következő paranccsal, és kövesse a képernyőn megjelenő utasításokat:

```PowerShell
Connect-AzAccount
```

> [!NOTE]
> Abban az esetben, ha a szervezet egynél több előfizetéssel rendelkezik, előfordulhat, hogy meg kell adnia az Előfizetési és bérlői argumentumokat. Részletek a [Connect-AzAccount dokumentációjában](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)találhatók.

Váltás a `azure-remote-rendering\Scripts` könyvtárra, és futtassa a konvertálási parancsfájlt:

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Meg kell látni valami ![ilyesmi: Conversion.ps1](./media/successful-conversion.png)

A konvertálási parancsfájl *egy SAS-URI-t* hoz létre a konvertált modellhez. Most már átmásolhatja ezt az URI-t **modellnévként** a rövid útmutató mintaalkalmazásba (lásd: [Rövid útmutató: Modell renderelése unity-vel).](render-model.md)

![Modell cseréje unity](./media/replace-model-in-unity.png)

 A minta most betöltődik, és teszi az egyéni modell!

## <a name="optional-re-creating-a-sas-uri"></a>Nem kötelező: SAS URI újbóli létrehozása

A konverziós parancsfájl által létrehozott SAS URI csak 24 óráig lesz érvényes. Azonban lejárt a modell után nem kell újra konvertálnia a modellt. Ehelyett létrehozhat egy új SAS-t a portálon a következő lépésekben leírtak szerint:

1. Nyissa meg az [Azure Portalt](https://www.portal.azure.com)
1. Kattintson a **tárfiók** erőforrás: ![Aláírás Access](./media/portal-storage-accounts.png)
1. A következő képernyőn kattintson a **tárolókezelőa** a bal oldali panelen, és keresse meg a kimeneti modell (*.arrAsset* fájl) az *arroutput* blob storage tároló tárolóban. Kattintson a jobb gombbal a fájlra, és ![válassza a **Közös hozzáférésű aláírás beírása** parancsot a helyi menüből: Aláírás-hozzáférés](./media/portal-storage-explorer.png)
1. Megnyílik egy új képernyő, ahol kiválaszthatja a lejárati dátumot. Nyomja le a **Create**billentyűt, és másolja a következő párbeszédpanelen megjelenő URI-t. Ez az új URI felváltja a parancsfájl által létrehozott ideiglenes URI-t.

## <a name="next-steps"></a>További lépések

Most, hogy már ismeri az alapokat, tekintse meg oktatóanyagainkat, hogy alaposabb ismereteket szerezzen.

Ha meg szeretné tudni a modellkonverzió részleteit, tekintse meg [a modellkonverziós REST API-t.](../how-tos/conversion/conversion-rest-api.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Unity-projekt beállítása a semmiből](../tutorials/unity/project-setup.md)
