---
title: 'Oktatóanyag: Írásvédett redundáns tárolók elérése során fellépő hiba szimulálása az Azure-ban | Microsoft Docs'
description: Írásvédett georedundáns tárolók elérése során fellépő hiba szimulálása
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.openlocfilehash: 0cbb4d2bc6449dc1cf12a374085b429743224995
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872879"
---
# <a name="tutorial-simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Oktatóanyag: Írásvédett redundáns tárolók elérése során fellépő hiba szimulálása

Ez az oktatóanyag egy sorozat második része. Járó előnyök további elsajátíthatja egy [olvasási hozzáférésű georedundáns](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) hiba szimulálása.

Hiba szimulálása érdekében használhat [Fiddler](#simulate-a-failure-with-fiddler) vagy [statikus útválasztás](#simulate-a-failure-with-an-invalid-static-route). Mindkét módszer lehetővé teszi, hogy az elsődleges végpontjába érkező kérések hibájának szimulálásához a [olvasási hozzáférésű georedundáns](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) tárfiók, így az alkalmazás olvassa el a másodlagos végpontból való helyette.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

A sorozat második részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az alkalmazás futtatása és szüneteltetése
> * Hiba szimulálása [Fiddlerrel](#simulate-a-failure-with-fiddler) vagy [érvénytelen statikus útvonallal](#simulate-a-failure-with-an-invalid-static-route) 
> * Elsődleges végpont visszaállításának szimulálása

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag elkezdéséhez hajtsa végre az előző oktatóanyagban: [Az alkalmazásadatok magas rendelkezésre állású és az Azure storage][previous-tutorial].

Hiba szimulálása a Fiddler segítségével: 

* A Fiddler letöltése és [telepítése](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-fiddler"></a>Hiba szimulálása a Fiddlerrel

A Fiddler hibát szimulálni, egy az RA-GRS tárfiók elsődleges végpontjába érkező kérésekre hibás választ szúr be.

A következő szakaszok jelzik a hibát szimulálni és az elsődleges végpontot visszaállítani a fiddler módjáról.

### <a name="launch-fiddler"></a>A Fiddler elindítása

Nyissa meg a Fiddlert, és válassza a **Rules** (Szabályok), majd a **Cutomize Rules** (Szabályok testreszabása) lehetőséget.

![Fiddler-szabályok testreszabása](media/storage-simulate-failure-ragrs-account-app/figure1.png)

A Fiddler ScriptEditor elindul, és megjeleníti a **SampleRules.js** fájlt. Ezzel a fájllal szabható testre a Fiddler.

Illessze be a következő kódmintát az `OnBeforeResponse` függvénybe. Az új kód megjegyzésként szerepel, hogy az általa létrehozott logika ne legyen azonnal implementálva.

Ha elkészült, válassza ki a **fájl** és **mentése** a módosítások mentéséhez.

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

### <a name="interrupting-the-application"></a>Az alkalmazás megszakítása

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python és a Java 7-es verziója](#tab/dotnet-python-java-v7)

Futtassa az alkalmazást az IDE vagy a rendszerhéjat.

Miután az alkalmazás elkezd olvasni az elsődleges végpontból, nyomja le az **egyik billentyűt** a konzolablakban az alkalmazás szüneteltetéséhez.

![Forgatókönyv alkalmazása](media/storage-simulate-failure-ragrs-account-app/scenario.png)

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Futtassa az alkalmazást az IDE vagy a rendszerhéjat.

Szabályozhatja a mintát, mivel nem kell annak érdekében, hogy a hiba szimulálása megszakítási azt. Ehhez gondoskodjon róla, hogy a fájlt töltenek fel a tárfiók a minta futtatása megnyitva **P**.

![Forgatókönyv alkalmazása](media/storage-simulate-failure-ragrs-account-app/Java-put-list-output.png)

---

### <a name="simulate-failure"></a>Hibaszimuláció

Amíg az alkalmazás szüneteltetve van, távolítsa el az egyéni szabály mentettük a fiddler esetében.

A kódminta megkeresi a kérelmeket az RA-GRS tárfiókba, és ha az elérési út tartalmazza a fájl neve `HelloWorld`, a válaszkódot adja vissza `503 - Service Unavailable`.

Lépjen a Fiddlerre, és válassza a **Rules** -> **Customize Rules** (Szabályok > Szabályok testreszabása) lehetőséget.

Cserélje le az alábbi sorokat, állítsa vissza `STORAGEACCOUNTNAME` a tárfiók nevére. A módosítások mentéséhez válassza a **File** -> **Save** (Fájl > Mentés) lehetőséget. 

> [!NOTE]
> Ha Linuxon futtatja a mintaalkalmazást, újra kell indítania a Fiddlert a **CustomRule.js** fájl minden szerkesztésekor, hogy a Fiddler telepíthesse az egyéni logikát.

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python és a Java 7-es verziója](#tab/dotnet-python-java-v7)

Az alkalmazás folytatásához nyomja le az **egyik billentyűt**.

Miután újra elindult az alkalmazás, az elsődleges végpontra érkező kérések meghiúsulnak. Az alkalmazás ötször próbál meg újracsatlakozni az elsődleges végponthoz. Az öt kísérlet után a másodlagos írásvédett végpontról kéri le a rendszerképet. Ha az alkalmazás sikeresen kérdezi le a lemezképet 20 alkalommal megkísérli az elsődleges végponthoz csatlakozik, a másodlagos végpontról. Ha az elsődleges végpont továbbra sem érhető el, az alkalmazás folytatja a másodlagos végpontból való olvasást.

Ez a minta az előző oktatóanyagban ismertetett [áramköri-megszakítós](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) minta.

![Testreszabott szabály beillesztése](media/storage-simulate-failure-ragrs-account-app/figure3.png)

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Most, hogy a hiba az Ön új, adja meg a **G** teszteléséhez a hibát.

Tájékoztatja, hogy használt-e a másodlagos folyamat ellentétben az elsődleges adatcsatorna.

---

### <a name="simulate-primary-endpoint-restoration"></a>Elsődleges végpont visszaállításának szimulálása

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python és a Java 7-es verziója](#tab/dotnet-python-java-v7)

Az előző lépésben beállított egyéni Fiddler-szabálykészlettel az elsődleges végpontra érkező kérések meghiúsulnak.

Az elsődleges végpont működésének ismételt szimulálása érdekében eltávolítja az `503`-as hibát beszúró logikát.

Az alkalmazás szüneteltetéséhez nyomja le **valamelyik billentyűt**.

Lépjen a Fiddlerre, és válassza a **Rules** (Szabályok), majd a **Customize Rules** (Szabályok testreszabása) lehetőséget. 

Tegye megjegyzésbe, vagy távolítsa el az egyéni logikát az `OnBeforeResponse` függvényben, hogy csak az alapértelmezett függvény maradjon aktív.

Válassza a **File** (Fájl), majd a **Save** (Mentés) lehetőséget a módosítások mentéséhez.

![Testreszabott szabály eltávolítása](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Amikor végzett, nyomja le az **egyik billentyűt** az alkalmazás folytatásához. Az alkalmazás 999 beolvasásig folytatja az elsődleges végpontból való olvasást.

![Alkalmazás folytatása](media/storage-simulate-failure-ragrs-account-app/figure4.png)

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Az előző lépésben beállított egyéni Fiddler-szabálykészlettel az elsődleges végpontra érkező kérések meghiúsulnak.

Az elsődleges végpont működésének ismételt szimulálása érdekében eltávolítja az `503`-as hibát beszúró logikát.

Lépjen a Fiddlerre, és válassza a **Rules** (Szabályok), majd a **Customize Rules** (Szabályok testreszabása) lehetőséget.  Tegye megjegyzésbe, vagy távolítsa el az egyéni logikát az `OnBeforeResponse` függvényben, hogy csak az alapértelmezett függvény maradjon aktív.

Válassza a **File** (Fájl), majd a **Save** (Mentés) lehetőséget a módosítások mentéséhez.

Amikor végzett, adja meg a **G** a letöltés teszteléséhez. Az alkalmazás jelentést készít, hogy most már használt-e az elsődleges folyamatot újra.

---

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Hiba szimulálása érvénytelen statikus útvonallal

Létrehozhatja az [írásvédett georedundáns](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) tárfiók elsődleges végpontjába érkező összes kérés egy érvénytelen statikus útvonalát. Ebben az oktatóanyagban a helyi gazdagép a tárfiókba érkező kérések útválasztására szolgáló átjáró. Ha a helyi gazdagépet használja átjáróként, a tárfiók elsődleges végpontjára érkező összes kérés visszatér a gazdagépre, ami hibához vezet. Kövesse az alábbi lépéseket, ha az érvénytelen statikus útvonal használatával szeretné a hibát szimulálni, és az elsődleges végpontot visszaállítani. 

### <a name="start-and-pause-the-application"></a>Az alkalmazás elindítása és szüneteltetése

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python és a Java 7-es verziója](#tab/dotnet-python-java-v7)

Futtassa az alkalmazást az IDE vagy a rendszerhéjat. Miután az alkalmazás elkezd olvasni az elsődleges végpontból, nyomja le az **egyik billentyűt** a konzolablakban az alkalmazás szüneteltetéséhez.

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Mivel a minta szabályozhatja, nem kell megszakítási, annak érdekében, hogy a teszt sikertelen.

Győződjön meg arról, hogy a fájlt töltenek fel a tárfiók a minta futtatása megnyitva **P**.

---

### <a name="simulate-failure"></a>Hibaszimuláció

Amikor az alkalmazás szüneteltetve van, indítson el egy Windows-parancssort rendszergazdaként, vagy futtasson egy Linux-terminált root felhasználóként.

A storage elsődleges végponttartományával kapcsolatos információk lekérdezése egy parancssorba vagy terminálba az alábbi parancs megadásával.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 
 Cserélje le a `STORAGEACCOUNTNAME` kifejezést a tárfiókja nevére. A későbbi lépésekhez másolja a tárfiók IP-címét egy szövegszerkesztőbe.

A helyi gazdagép IP-címének lekéréséhez írja be az `ipconfig` parancsot a Windows-parancssorba vagy az `ifconfig` parancsot a Linux-terminálba. 

Egy célgazdagép statikus útvonalának hozzáadásához írja be a következő parancsot egy Windows-parancssorba vagy Linux-terminálba. 

#### <a name="linux"></a>Linux

`route add <destination_ip> gw <gateway_ip>`

#### <a name="windows"></a>Windows

`route add <destination_ip> <gateway_ip>`

Cserélje le a `<destination_ip>` kifejezést a tárfiók IP-címére, illetve a `<gateway_ip>` kifejezést a helyi gazdagép IP-címére.

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python és a Java 7-es verziója](#tab/dotnet-python-java-v7)

Az alkalmazás folytatásához nyomja le az **egyik billentyűt**.

Miután újra elindult az alkalmazás, az elsődleges végpontra érkező kérések meghiúsulnak. Az alkalmazás megkísérel csatlakozni az elsődleges végpont ötször. Az öt kísérlet után a másodlagos írásvédett végpontról kéri le a rendszerképet. Miután az alkalmazás 20-szor sikeresen lekéri a rendszerképet a másodlagos végpontról, az alkalmazás megkísérel csatlakozni az elsődleges végponthoz. Ha az elsődleges végpont továbbra sem érhető el, az alkalmazás folytatja a másodlagos végpontból való olvasást. Ez a minta az előző oktatóanyagban ismertetett [áramköri-megszakítós](/azure/architecture/patterns/circuit-breaker) minta.

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Most, hogy a hiba az Ön új, adja meg a **G** teszteléséhez a hibát. Tájékoztatja, hogy használt-e a másodlagos folyamat ellentétben az elsődleges adatcsatorna.

---

### <a name="simulate-primary-endpoint-restoration"></a>Elsődleges végpont visszaállításának szimulálása

Az elsődleges végpont működésének ismételt szimulálása érdekében törölje az elsődleges végpont statikus útvonalát az útválasztási táblából. Ez lehetővé teszi, hogy az elsődleges végpont az alapértelmezett átjárón keresztül legyen irányítva.

Egy célgazdagép statikus útvonalának törléséhez írja be a következő parancsot egy Windows-parancssorba vagy Linux-terminálba.

#### <a name="linux"></a>Linux

`route del <destination_ip> gw <gateway_ip>`

#### <a name="windows"></a>Windows

`route delete <destination_ip>`

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python és a Java 7-es verziója](#tab/dotnet-python-java-v7)

Nyomja le az **egyik billentyűt** az alkalmazás folytatásához. Az alkalmazás 999 beolvasásig folytatja az elsődleges végpontból való olvasást.

![Alkalmazás folytatása](media/storage-simulate-failure-ragrs-account-app/figure4.png)


# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Adja meg **G** a letöltés teszteléséhez. Az alkalmazás jelentést készít, hogy most már használt-e az elsődleges folyamatot újra.

![Alkalmazás folytatása](media/storage-simulate-failure-ragrs-account-app/java-get-pipeline-example-v10.png)

---

## <a name="next-steps"></a>További lépések

A sorozat második részében megtanulta, írásvédett georedundáns tárolók tesztelésére hibaszimulálást.

További információ az RA-GRS tároló működéséről, valamint a kapcsolódó kockázatokról, olvassa el a következő cikket:

> [!div class="nextstepaction"]
> [HA-alkalmazások tervezése RA-GRS használatával](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
