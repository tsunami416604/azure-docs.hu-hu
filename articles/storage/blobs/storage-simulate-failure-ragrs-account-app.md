---
title: Oktatóanyag – Hiba szimulálása az elsődleges régióból származó adatok olvasása esetén
titleSuffix: Azure Storage
description: Szimulálja a hibát az adatok olvasása az elsődleges régióból, ha olvasási hozzáférésű georedundáns tárolás (RA-GRS) engedélyezve van a tárfiók.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: artek
ms.openlocfilehash: 3693ae8c9c425c35ab1a4ebf88b6cd3dfb58f92c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061289"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>Oktatóanyag: Hiba szimulálása az elsődleges régióból származó adatok olvasásakor

Ez az oktatóanyag egy sorozat második része. Ebben megismerheti az [olvasási hozzáférésű georedundáns tárolás](../common/storage-redundancy.md) (RA-GRS) előnyeit egy hiba szimulálásával.

A hiba szimulálásához használhatja a [Statikus műveletterv](#simulate-a-failure-with-an-invalid-static-route) vagy a [Fiddler műveletet.](#simulate-a-failure-with-fiddler) Mindkét módszer lehetővé teszi, hogy szimulálja a hibákat az [olvasási hozzáférés georedundáns](../common/storage-redundancy.md) (RA-GRS) tárfiók elsődleges végpontjára irányuló kérelmek, ami az alkalmazást a másodlagos végpontról olvassa be.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

A sorozat második részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az alkalmazás futtatása és szüneteltetése
> * Hiba [szimulálása érvénytelen statikus útvonallal](#simulate-a-failure-with-an-invalid-static-route) vagy [Hegedűsrel](#simulate-a-failure-with-fiddler)
> * Elsődleges végpont visszaállításának szimulálása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt töltse ki az előző oktatóanyagot: [Az alkalmazásadatok elérhetővé az Azure Storage szolgáltatással.][previous-tutorial]

A statikus útválasztással történő hiba szimulálásához emelt szintű parancssort kell használnia.

A Fiddler használatával történő hiba szimulálásához töltse le és [telepítse a Fiddler-t](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Hiba szimulálása érvénytelen statikus útvonallal

Létrehozhatja az [írásvédett georedundáns](../common/storage-redundancy.md) (RA-GRS) tárfiók elsődleges végpontjába érkező összes kérés egy érvénytelen statikus útvonalát. Ebben az oktatóanyagban a helyi gazdagép a tárfiókba érkező kérések útválasztására szolgáló átjáró. Ha a helyi gazdagépet használja átjáróként, a tárfiók elsődleges végpontjára érkező összes kérés visszatér a gazdagépre, ami hibához vezet. Kövesse az alábbi lépéseket, ha az érvénytelen statikus útvonal használatával szeretné a hibát szimulálni, és az elsődleges végpontot visszaállítani.

### <a name="start-and-pause-the-application"></a>Az alkalmazás elindítása és szüneteltetése

Az [előző oktatóanyagutasításaival][previous-tutorial] indítsa el a mintát, és töltse le a tesztfájlt, és ellenőrizze, hogy az elsődleges tárolóból származik-e. A célplatformtól függően manuálisan szüneteltetheti a mintát, vagy megvárhatja a kérdést.

### <a name="simulate-failure"></a>Hibaszimuláció

Amíg az alkalmazás szünetel, nyisson meg egy parancssort a Windows rendszeren rendszergazdaként, vagy futtasson terminált linuxos gyökérként.

A tárfiók elsődleges végponttartományával kapcsolatos információk a következő parancs parancssorba vagy terminálra történő beírásával, a tárfiók nevének cseréjével. `STORAGEACCOUNTNAME`

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

A későbbi lépésekhez másolja a tárfiók IP-címét egy szövegszerkesztőbe.

A helyi gazdagép IP-címének lekéréséhez írja be az `ipconfig` parancsot a Windows-parancssorba vagy az `ifconfig` parancsot a Linux-terminálba.

Statikus útvonal hozzáadásához írja be a következő parancsot egy Windows parancssorba vagy Linux-terminálba, és cserélje ki `<destination_ip>` a tárfiók IP-címét és `<gateway_ip>` a helyi állomás IP-címét.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

A futó mintával rendelkező ablakban folytassa az alkalmazást, vagy nyomja meg a megfelelő billentyűt a mintafájl letöltéséhez, és ellenőrizze, hogy az másodlagos tárolóból származik-e. Ezután szüneteltetheti a mintát, vagy megvárhatja a kérdést.

### <a name="simulate-primary-endpoint-restoration"></a>Elsődleges végpont visszaállításának szimulálása

Ha azt szeretné szimulálni, hogy az elsődleges végpont ismét működőképessé váljon, törölje az érvénytelen statikus útvonalat az útválasztási táblából. Ez lehetővé teszi, hogy az elsődleges végpont az alapértelmezett átjárón keresztül legyen irányítva. Írja be a következő parancsot egy Windows parancssorba vagy Linux-terminálba.

#### <a name="linux"></a>Linux

```
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route delete <destination_ip>
```

Ezután folytathatja az alkalmazást, vagy megnyomhatja a megfelelő kulcsot a mintafájl újbóli letöltéséhez, ezúttal meggyőződve arról, hogy az ismét az elsődleges tárolóból származik.

## <a name="simulate-a-failure-with-fiddler"></a>Hiba szimulálása a Fiddlerrel

A Fiddler hiba szimulálása érdekében sikertelen választ kell adnia az RA-GRS tárfiók elsődleges végpontjára érkező kérelmekre.

A következő szakaszok bemutatják, hogyan lehet szimulálni egy hiba és az elsődleges végpont helyreállítása hegedűs.

### <a name="launch-fiddler"></a>A Fiddler elindítása

Nyissa meg a Fiddlert, és válassza a **Rules** (Szabályok), majd a **Cutomize Rules** (Szabályok testreszabása) lehetőséget.

![Fiddler-szabályok testreszabása](media/storage-simulate-failure-ragrs-account-app/figure1.png)

A Fiddler ScriptEditor elindítja és megjeleníti a **SampleRules.js** fájlt. Ezzel a fájllal szabható testre a Fiddler.

Illessze be a `OnBeforeResponse` következő kódmintát a függvénybe, és cserélje le `STORAGEACCOUNTNAME` a tárfiók nevét. A mintától függően előfordulhat, `HelloWorld` hogy a letöltött tesztfájl (vagy egy `sampleFile`előtag) nevére is le kell cserélnie. Az új kódot megjegyzéssel kell elhangolni annak érdekében, hogy ne fusson azonnal.

Miután elkészült, válassza **a Fájl** és **mentés** lehetőséget a módosítások mentéséhez. Hagyja nyitva a ScriptEditor ablakot a következő lépésekben való használatra.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error.
        // When you're ready to stop sending back errors, comment these lines of script out again
        //     and save the changes.

        if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;
        }
    */
```

![Testreszabott szabály beillesztése](media/storage-simulate-failure-ragrs-account-app/figure2.png)

### <a name="start-and-pause-the-application"></a>Az alkalmazás elindítása és szüneteltetése

Az [előző oktatóanyagutasításaival][previous-tutorial] indítsa el a mintát, és töltse le a tesztfájlt, és ellenőrizze, hogy az elsődleges tárolóból származik-e. A célplatformtól függően manuálisan szüneteltetheti a mintát, vagy megvárhatja a kérdést.

### <a name="simulate-failure"></a>Hibaszimuláció

Amíg az alkalmazás szünetel, váltson vissza a Fiddler-re, és ne fűzzön megjegyzést a `OnBeforeResponse` függvényben mentett egyéni szabályról. A módosítások mentéséhez válassza a **Fájl** és **mentés** lehetőséget, hogy a szabály érvénybe lépjen. Ez a kód az RA-GRS tárfiókba irányuló kérelmeket keresi, és ha az `503 - Service Unavailable`elérési út tartalmazza a mintafájl nevét, a válaszkódját adja vissza.

A futó mintával rendelkező ablakban folytassa az alkalmazást, vagy nyomja meg a megfelelő billentyűt a mintafájl letöltéséhez, és ellenőrizze, hogy az másodlagos tárolóból származik-e. Ezután szüneteltetheti a mintát, vagy megvárhatja a kérdést.

### <a name="simulate-primary-endpoint-restoration"></a>Elsődleges végpont visszaállításának szimulálása

A Fiddler, távolítsa el vagy véleményezze ki az egyéni szabály újra. Válassza a **Fájl** és **mentés** lehetőséget, ha azt szeretné, hogy a szabály ne legyen érvényben.

A futó mintával rendelkező ablakban folytassa az alkalmazást, vagy nyomja meg a megfelelő billentyűt a mintafájl letöltéséhez, és ellenőrizze, hogy az elsődleges tárolóból származik-e ismét. Ezután kilépa mintából.

## <a name="next-steps"></a>További lépések

A második részben a sorozat, megtanulta, hogy szimulálja a hiba az olvasási hozzáférés georedundáns tárolás.

Ha többet szeretne megtudni az RA-GRS storage működéséről, valamint a kapcsolódó kockázatokról, olvassa el a következő cikket:

> [!div class="nextstepaction"]
> [HA-alkalmazások tervezése RA-GRS használatával](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
