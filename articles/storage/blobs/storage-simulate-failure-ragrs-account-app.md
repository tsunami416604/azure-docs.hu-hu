---
title: 'Oktatóanyag: Írásvédett redundáns tárolók elérése során fellépő hiba szimulálása az Azure-ban | Microsoft Docs'
description: Írásvédett georedundáns tárolók elérése során fellépő hiba szimulálása
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.openlocfilehash: 0144d68ecfdb1cc3309c462d00fa8f30e66bab34
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441355"
---
# <a name="tutorial-simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Oktatóanyag: Írásvédett redundáns tárolók elérése során fellépő hiba szimulálása

Ez az oktatóanyag egy sorozat második része. Járó előnyök további elsajátíthatja egy [olvasási hozzáférésű georedundáns](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) hiba szimulálása.

Hiba szimulálása érdekében használhat [statikus útválasztás](#simulate-a-failure-with-an-invalid-static-route) vagy [Fiddler](#simulate-a-failure-with-fiddler). Mindkét módszer lehetővé teszi, hogy az elsődleges végpontjába érkező kérések hibájának szimulálásához a [olvasási hozzáférésű georedundáns](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) tárfiók, így az alkalmazás olvassa el a másodlagos végpontból való helyette. 

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

A sorozat második részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az alkalmazás futtatása és szüneteltetése
> * Hiba szimulálása [érvénytelen statikus útvonallal](#simulate-a-failure-with-an-invalid-static-route) vagy [Fiddler](#simulate-a-failure-with-fiddler) 
> * Elsődleges végpont visszaállításának szimulálása

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag elkezdéséhez hajtsa végre az előző oktatóanyagban: [Az alkalmazásadatok magas rendelkezésre állású és az Azure storage][previous-tutorial].

A statikus útválasztás hiba szimulálása fogja használni, egy rendszergazda jogú parancssort.

A Fiddler segítségével hibát szimulálni, töltse le és [Fiddler telepítése](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Hiba szimulálása érvénytelen statikus útvonallal

Létrehozhatja az [írásvédett georedundáns](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) tárfiók elsődleges végpontjába érkező összes kérés egy érvénytelen statikus útvonalát. Ebben az oktatóanyagban a helyi gazdagép a tárfiókba érkező kérések útválasztására szolgáló átjáró. Ha a helyi gazdagépet használja átjáróként, a tárfiók elsődleges végpontjára érkező összes kérés visszatér a gazdagépre, ami hibához vezet. Kövesse az alábbi lépéseket, ha az érvénytelen statikus útvonal használatával szeretné a hibát szimulálni, és az elsődleges végpontot visszaállítani. 

### <a name="start-and-pause-the-application"></a>Az alkalmazás elindítása és szüneteltetése

Útmutatásait a [előző oktatóanyagban] [ previous-tutorial] , indítsa el a mintát, töltse le a teszt, megerősíti, hogy az elsődleges storage-ból származik. A cél platformtól függően Ezután manuálisan a minta szüneteltetheti vagy várjon a parancssorba. 

### <a name="simulate-failure"></a>Hibaszimuláció

Az alkalmazás fel van függesztve, amíg nyisson meg egy parancssort a Windows rendszergazdai vagy linuxon futtassa terminált root felhasználóként.

A storage elsődleges végponttartományával kapcsolatos információk lekérése a parancssorban, illetve a Terminálszolgáltatások, és cserélje le a következő parancs beírásával `STORAGEACCOUNTNAME` a tárfiók nevére.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 

A későbbi lépésekhez másolja a tárfiók IP-címét egy szövegszerkesztőbe.

A helyi gazdagép IP-címének lekéréséhez írja be az `ipconfig` parancsot a Windows-parancssorba vagy az `ifconfig` parancsot a Linux-terminálba. 

Adjon hozzá egy statikus útvonalat a cél gazdagépre, írja be a következő parancsot egy Windows-parancssorba vagy Linux-terminálba, és cserélje le a `<destination_ip>` a tárolási fiók IP-címmel és `<gateway_ip>` a helyi gazdagép IP-címmel.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

Az ablakban futó minta az alkalmazás folytatásához, vagy nyomja le a megfelelő kulcsot a mintafájl letöltéséhez, és győződjön meg arról, hogy a másodlagos tárhelyen származnak. Ezután a minta újra szüneteltetése, vagy várjon, amikor a rendszer kéri. 

### <a name="simulate-primary-endpoint-restoration"></a>Elsődleges végpont visszaállításának szimulálása

Az elsődleges végpont újra működési váljon szimulálása, törölje érvénytelen statikus útvonalát az útválasztási táblából. Ez lehetővé teszi, hogy az elsődleges végpont az alapértelmezett átjárón keresztül legyen irányítva. Írja be a következő parancsot egy Windows-parancssorba vagy Linux-terminálba.

#### <a name="linux"></a>Linux

```
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route delete <destination_ip>
```

Ezután folytathatja az alkalmazás vagy nyomja meg a megfelelő kulccsal, töltse le a mintát a idő megerősíti, hogy ismét származik, amely elsődleges tárolási ismét fájlt.

## <a name="simulate-a-failure-with-fiddler"></a>Hiba szimulálása a Fiddlerrel

A Fiddler hibát szimulálni, egy az RA-GRS tárfiók elsődleges végpontjába érkező kérésekre hibás választ szúr be.

A következő szakaszok jelzik a hibát szimulálni és az elsődleges végpontot visszaállítani a fiddler módjáról.

### <a name="launch-fiddler"></a>A Fiddler elindítása

Nyissa meg a Fiddlert, és válassza a **Rules** (Szabályok), majd a **Cutomize Rules** (Szabályok testreszabása) lehetőséget.

![Fiddler-szabályok testreszabása](media/storage-simulate-failure-ragrs-account-app/figure1.png)

A Fiddler ScriptEditor elindul, és megjeleníti a **SampleRules.js** fájlt. Ezzel a fájllal szabható testre a Fiddler.

Illessze be a következő kódmintát az `OnBeforeResponse` működni, és cserélje le `STORAGEACCOUNTNAME` a tárfiók nevére. A minta függően szükség lehet cserélni `HelloWorld` tesztfájl nevét (vagy egy előtag, mint például `sampleFile`) letöltése. Az új kód megjegyzésként szerepel győződjön meg arról, hogy azonnal nem-e futtatni.

Ha elkészült, válassza ki a **fájl** és **mentése** a módosítások mentéséhez. Hagyja nyitva a következő lépésekben használni a ScriptEditor ablakot.

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

Útmutatásait a [előző oktatóanyagban] [ previous-tutorial] , indítsa el a mintát, töltse le a teszt, megerősíti, hogy az elsődleges storage-ból származik. A cél platformtól függően Ezután manuálisan a minta szüneteltetheti vagy várjon a parancssorba. 

### <a name="simulate-failure"></a>Hibaszimuláció

Amíg az alkalmazás szüneteltetve van, váltson vissza a Fiddler, és állítsa vissza a mentett egyéni szabályt a `OnBeforeResponse` függvény. Ügyeljen arra, hogy válasszon **fájl** és **mentése** a módosítások mentéséhez, így a szabály lép érvénybe. Ez a kód megkeresi az RA-GRS tárfiókba kérelmek, és ha az elérési utat a mintafájl nevét tartalmazza a válaszkódot adja vissza `503 - Service Unavailable`.

Az ablakban futó minta az alkalmazás folytatásához, vagy nyomja le a megfelelő kulcsot a mintafájl letöltéséhez, és győződjön meg arról, hogy a másodlagos tárhelyen származnak. Ezután a minta újra szüneteltetése, vagy várjon, amikor a rendszer kéri. 

### <a name="simulate-primary-endpoint-restoration"></a>Elsődleges végpont visszaállításának szimulálása

A Fiddlerben távolítsa el, vagy újra megjegyzésbe az egyéni szabályt. Válassza ki **fájl** és **mentése** annak érdekében, hogy a szabály már nem lesz érvényben.

Az ablakban futó minta az alkalmazás folytatásához, vagy nyomja le a megfelelő kulcsot a mintafájl letöltéséhez, és győződjön meg arról, hogy származik, amely elsődleges tároló ismét. A minta ezután kiléphet. 

## <a name="next-steps"></a>További lépések

A sorozat második részében megtanulta, írásvédett georedundáns tárolók tesztelésére hibaszimulálást.

További információ az RA-GRS tároló működéséről, valamint a kapcsolódó kockázatokról, olvassa el a következő cikket:

> [!div class="nextstepaction"]
> [HA-alkalmazások tervezése RA-GRS használatával](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
