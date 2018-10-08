---
title: Írásvédett redundáns tárolók elérése során fellépő hiba szimulálása az Azure-ban | Microsoft Docs
description: Írásvédett georedundáns tárolók elérése során fellépő hiba szimulálása
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/23/2017
ms.author: tamram
ms.openlocfilehash: 84ced8a529c2e717dc3e5888466d9a2e1e7e928a
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47180943"
---
# <a name="tutorial-simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Oktatóanyag: Írásvédett redundáns tárolók elérése során fellépő hiba szimulálása

Ez az oktatóanyag egy sorozat második része.  Ebben az oktatóanyagban [Fiddlert](#simulate-a-failure-with-fiddler) vagy [statikus útválasztást](#simulate-a-failure-with-an-invalid-static-route) használhat az [írásvédett georedundáns](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) tárfiók elsődleges végpontjába érkező kérések hibájának szimulálásához, hogy az alkalmazás a másodlagos végpontból végezzen beolvasást.

![Forgatókönyv alkalmazása](media/storage-simulate-failure-ragrs-account-app/scenario.png)

Az oktatóanyag teljesítéséhez el kell végeznie az előző tárolási oktatóanyagot, amely [az alkalmazásadatok magas rendelkezésre állásának biztosítását ismerteti az Azure Storage használatával][previous-tutorial].

A sorozat második részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az alkalmazás futtatása és szüneteltetése
> * Hiba szimulálása [Fiddlerrel](#simulate-a-failure-with-fiddler) vagy [érvénytelen statikus útvonallal](#simulate-a-failure-with-an-invalid-static-route) 
> * Elsődleges végpont visszaállításának szimulálása


## <a name="prerequisites"></a>Előfeltételek

Hiba szimulálása a Fiddler segítségével: 

* A Fiddler letöltése és [telepítése](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="simulate-a-failure-with-fiddler"></a>Hiba szimulálása a Fiddlerrel

Ha a Fiddlerrel szeretne hibát szimulálni, ahhoz az RA-GRS tárfiók elsődleges végpontjába érkező kérésekre hibás választ szúr be.

Kövesse az alábbi lépéseket, ha a Fiddler használatával szeretné a hibát szimulálni, és az elsődleges végpontot visszaállítani.

### <a name="launch-fiddler"></a>A Fiddler elindítása

Nyissa meg a Fiddlert, és válassza a **Rules** (Szabályok), majd a **Cutomize Rules** (Szabályok testreszabása) lehetőséget.

![Fiddler-szabályok testreszabása](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Elindul a Fiddler ScriptEditor a **SampleRules.js** fájllal. Ezzel a fájllal szabható testre a Fiddler. Illessze be a következő kódmintát az `OnBeforeResponse` függvénybe. Az új kód megjegyzésként szerepel, hogy az általa létrehozott logika ne legyen azonnal implementálva. Ha végzett, válassza a **File** (Fájl), majd a **Save** (Mentés) lehetőséget a módosítások mentéséhez.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error. 
        // When you're ready to stop sending back errors, comment these lines of script out again 
        //     and save the changes.

        if ((oSession.hostname == "contosoragrs.blob.core.windows.net") 
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;  
        }
    */
```

![Testreszabott szabály beillesztése](media/storage-simulate-failure-ragrs-account-app/figure2.png)

### <a name="start-and-pause-the-application"></a>Az alkalmazás elindítása és szüneteltetése

Futtassa az alkalmazást az IDE-ben vagy egy szövegszerkesztőben. Miután az alkalmazás elkezd olvasni az elsődleges végpontból, nyomja le az **egyik billentyűt** a konzolablakban az alkalmazás szüneteltetéséhez.

### <a name="simulate-failure"></a>Hibaszimuláció

Most, hogy az alkalmazás szüneteltetve van, törölheti a megjegyzésjelölőket a Fiddlerben az előző lépésben mentett egyéni szabályból. A kódminta megkeresi az RA-GRS tárfiókba érkezett kéréseket, és ha az elérési út tartalmazza a rendszerkép nevét (`HelloWorld`), a következő válaszkódot adja vissza: `503 - Service Unavailable`.

Lépjen a Fiddlerre, és válassza a **Rules** -> **Customize Rules** (Szabályok > Szabályok testreszabása) lehetőséget.  Törölje a megjegyzésjelölőt a következő soroknál, és cserélje le a `STORAGEACCOUNTNAME` értékét a tárfiók nevére. A módosítások mentéséhez válassza a **File** -> **Save** (Fájl > Mentés) lehetőséget. 

> [!NOTE]
> Ha Linuxon futtatja a mintaalkalmazást, újra kell indítania a Fiddlert a **CustomRule.js** fájl minden szerkesztésekor, hogy a Fiddler telepíthesse az egyéni logikát. 
> 
> 


```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

Az alkalmazás folytatásához nyomja le az **egyik billentyűt**.

Miután újra elindult az alkalmazás, az elsődleges végpontra érkező kérések meghiúsulnak. Az alkalmazás ötször próbál meg újracsatlakozni az elsődleges végponthoz. Az öt kísérlet után a másodlagos írásvédett végpontról kéri le a rendszerképet. Miután az alkalmazás 20-szor sikeresen lekéri a rendszerképet a másodlagos végpontról, az alkalmazás megkísérel csatlakozni az elsődleges végponthoz. Ha az elsődleges végpont továbbra sem érhető el, az alkalmazás folytatja a másodlagos végpontból való olvasást. Ez a minta az előző oktatóanyagban ismertetett [áramköri-megszakítós](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) minta.

![Testreszabott szabály beillesztése](media/storage-simulate-failure-ragrs-account-app/figure3.png)

### <a name="simulate-primary-endpoint-restoration"></a>Elsődleges végpont visszaállításának szimulálása

Az előző lépésben beállított egyéni Fiddler-szabálykészlettel az elsődleges végpontra érkező kérések meghiúsulnak. Az elsődleges végpont működésének ismételt szimulálása érdekében eltávolítja az `503`-as hibát beszúró logikát.

Az alkalmazás szüneteltetéséhez nyomja le **valamelyik billentyűt**.

Lépjen a Fiddlerre, és válassza a **Rules** (Szabályok), majd a **Customize Rules** (Szabályok testreszabása) lehetőséget.  Tegye megjegyzésbe, vagy távolítsa el az egyéni logikát az `OnBeforeResponse` függvényben, hogy csak az alapértelmezett függvény maradjon aktív. Válassza a **File** (Fájl), majd a **Save** (Mentés) lehetőséget a módosítások mentéséhez.

![Testreszabott szabály eltávolítása](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Amikor végzett, nyomja le az **egyik billentyűt** az alkalmazás folytatásához. Az alkalmazás 999 beolvasásig folytatja az elsődleges végpontból való olvasást.

![Alkalmazás folytatása](media/storage-simulate-failure-ragrs-account-app/figure4.png)


## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Hiba szimulálása érvénytelen statikus útvonallal 
Létrehozhatja az [írásvédett georedundáns](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) tárfiók elsődleges végpontjába érkező összes kérés egy érvénytelen statikus útvonalát. Ebben az oktatóanyagban a helyi gazdagép a tárfiókba érkező kérések útválasztására szolgáló átjáró. Ha a helyi gazdagépet használja átjáróként, a tárfiók elsődleges végpontjára érkező összes kérés visszatér a gazdagépre, ami hibához vezet. Kövesse az alábbi lépéseket, ha az érvénytelen statikus útvonal használatával szeretné a hibát szimulálni, és az elsődleges végpontot visszaállítani. 

### <a name="start-and-pause-the-application"></a>Az alkalmazás elindítása és szüneteltetése

Futtassa az alkalmazást az IDE-ben vagy egy szövegszerkesztőben. Miután az alkalmazás elkezd olvasni az elsődleges végpontból, nyomja le az **egyik billentyűt** a konzolablakban az alkalmazás szüneteltetéséhez. 

### <a name="simulate-failure"></a>Hibaszimuláció

Amikor az alkalmazás szüneteltetve van, indítson el egy Windows-parancssort rendszergazdaként, vagy futtasson egy Linux-terminált root felhasználóként. A tárfiók elsődleges végponttartományával kapcsolatos adatokért írja be a következő parancsot egy parancssorba vagy terminálba.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 
 Cserélje le a `STORAGEACCOUNTNAME` kifejezést a tárfiókja nevére. A későbbi lépésekhez másolja a tárfiók IP-címét egy szövegszerkesztőbe. A helyi gazdagép IP-címének lekéréséhez írja be az `ipconfig` parancsot a Windows-parancssorba vagy az `ifconfig` parancsot a Linux-terminálba. 

Egy célgazdagép statikus útvonalának hozzáadásához írja be a következő parancsot egy Windows-parancssorba vagy Linux-terminálba. 


# <a name="linuxtablinux"></a>[Linux](#tab/linux)

  route add <destination_ip> gw <gateway_ip>

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

  route add <destination_ip> <gateway_ip>

---
 
Cserélje le a `<destination_ip>` kifejezést a tárfiók IP-címére, illetve a `<gateway_ip>` kifejezést a helyi gazdagép IP-címére. Az alkalmazás folytatásához nyomja le az **egyik billentyűt**.

Miután újra elindult az alkalmazás, az elsődleges végpontra érkező kérések meghiúsulnak. Az alkalmazás ötször próbál meg újracsatlakozni az elsődleges végponthoz. Az öt kísérlet után a másodlagos írásvédett végpontról kéri le a rendszerképet. Miután az alkalmazás 20-szor sikeresen lekéri a rendszerképet a másodlagos végpontról, az alkalmazás megkísérel csatlakozni az elsődleges végponthoz. Ha az elsődleges végpont továbbra sem érhető el, az alkalmazás folytatja a másodlagos végpontból való olvasást. Ez a minta az előző oktatóanyagban ismertetett [áramköri-megszakítós](/azure/architecture/patterns/circuit-breaker.md) minta.

### <a name="simulate-primary-endpoint-restoration"></a>Elsődleges végpont visszaállításának szimulálása

Az elsődleges végpont működésének ismételt szimulálása érdekében törölje az elsődleges végpont statikus útvonalát az útválasztási táblából. Ez lehetővé teszi, hogy az elsődleges végpont az alapértelmezett átjárón keresztül legyen irányítva. 

Egy célgazdagép statikus útvonalának törléséhez írja be a következő parancsot egy Windows-parancssorba vagy Linux-terminálba. 
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)

route del <destination_ip> gw <gateway_ip>

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

route delete <destination_ip>

---

Nyomja le az **egyik billentyűt** az alkalmazás folytatásához. Az alkalmazás 999 beolvasásig folytatja az elsődleges végpontból való olvasást.

![Alkalmazás folytatása](media/storage-simulate-failure-ragrs-account-app/figure4.png)


## <a name="next-steps"></a>További lépések

A sorozat második részében megismerte az írásvédett georedundáns tárolók tesztelésére szolgáló hibaszimulálást, többek között a következőket:

> [!div class="checklist"]
> * Az alkalmazás futtatása és szüneteltetése
> * Hiba szimulálása [Fiddlerrel](#simulate-a-failure-with-fiddler) vagy [érvénytelen statikus útvonallal](#simulate-a-failure-with-an-invalid-static-route) 
> * Elsődleges végpont visszaállításának szimulálása

Ha többet szeretne megtudni az RA-GRS tároló működéséről (és a kapcsolódó kockázatokról), olvassa el az alábbi cikket.

> [!div class="nextstepaction"]
> [HA-alkalmazások tervezése RA-GRS használatával](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md