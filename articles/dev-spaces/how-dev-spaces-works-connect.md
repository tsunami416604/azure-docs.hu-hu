---
title: Hogyan működik a fejlesztői számítógép csatlakoztatása az AKS-fürthöz?
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: A fejlesztési számítógép azure Kubernetes-szolgáltatásfürthöz való csatlakoztatásához az Azure Dev Spaces használatával leadott folyamatok ismertetése
keywords: Azure dev spaces, fejlesztői terek, Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241711"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>Hogyan működik a fejlesztői számítógép csatlakoztatása az AKS-fürthöz?

Az Azure Dev Spaces segítségével csatlakoztathatja a fejlesztői számítógépet az AKS-fürthöz, így úgy futtathatja és hibakeresést okozhat a fejlesztői számítógépen, mintha a fürtön futna. Az Azure Dev Spaces átirányítja a forgalmat a csatlakoztatott AKS-fürt által a fürtön, amely egy távoli ügynökként működik, hogy irányítsa át a forgalmat a fejlesztői gép és a fürt között. Ez a forgalomátirányítás lehetővé teszi, hogy a fejlesztői számítógépen és az AKS-fürtön futó szolgáltatások úgy kommunikáljanak, mintha ugyanabban az AKS-fürtben lennének. Ez a kapcsolat azt is lehetővé teszi, hogy a fejlesztői számítógépen tárolóval vagy anélkül is futtatjon és hibakeresést. A fejlesztői számítógép fürthöz való csatlakoztatása segít az alkalmazás gyors fejlesztésében és a végpontok között végzett tesztelésben.

## <a name="connecting-to-your-cluster"></a>Csatlakozás a fürthöz

A meglévő AKS-fürthöz a [Visual Studio-kód][vs-code] használatával csatlakozik a MacOS vagy Windows 10 rendszeren futó [Azure Dev Spaces][azds-vs-code] bővítményrel. Kapcsolat létrehozásakor lehetősége van arra, hogy a fürt új vagy meglévő podjára irányuló összes forgalmat átirányítsa a fejlesztői számítógépre.

> [!NOTE]
> Ha a Visual Studio-kód segítségével csatlakozik a fürthöz, az Azure Dev Spaces bővítmény lehetőséget ad arra, hogy átirányítson egy szolgáltatást a fejlesztői számítógépre. Ez a beállítás egy kényelmes módja annak, hogy azonosítsa a pod átirányításhoz. Az AKS-fürt és a fejlesztői számítógép közötti összes átirányítás egy podhoz van.

A fürthöz való csatlakozáshoz nem szükséges, hogy engedélyezve legyen az Azure Dev Spaces a fürtön. Ehelyett, amikor az Azure Dev Spaces bővítmény kapcsolatot hoz létre a fürthöz, a következőket:

* Lecseréli a tárolót a pod az AKS-fürt egy távoli ügynök tároló, amely átirányítja a forgalmat a fejlesztési számítógépre. Új pod átirányításakor az Azure Dev Spaces egy új podot hoz létre az AKS-fürtben a távoli ügynökkel.
* [Kubectl port-forward][kubectl-port-forward] fut a fejlesztői számítógépen a fejlesztői számítógépről a fürtben futó távoli ügynöknek továbbított forgalmat.
* Környezeti adatokat gyűjt a fürttől a távoli ügynök használatával. Ez a környezeti információ környezeti változókat, látható szolgáltatásokat, kötetcsatolókat és titkos csatlakoztatásokat tartalmaz.
* Beállítja a környezetet a Visual Studio Code terminálon, így a fejlesztői számítógép szolgáltatása ugyanazokat a változókat érheti el, mintha a fürtön futna.  
* Frissíti a hosts fájlt, hogy az AKS-fürt szolgáltatásait a fejlesztői számítógép helyi IP-címeihez rendelje hozzá. Ezek a gazdagépfájl-bejegyzések lehetővé teszik, hogy a fejlesztői számítógépen futó kód a fürtben futó más szolgáltatásokhoz intézze a kérelmeket. A gazdagépfájl frissítéséhez az Azure Dev Spaces rendszergazdai hozzáférést kér a fejlesztői számítógépen, amikor a fürthöz csatlakozik.

Ha engedélyezve van az Azure Dev Spaces a fürtön, akkor az [Azure Dev Spaces által kínált forgalomátirányítás][how-it-works-routing]is használható. Az Azure Dev Spaces által kínált forgalomátirányítás lehetővé teszi, hogy csatlakozzon a szolgáltatás egy gyermek fejlesztési térben futó példányához. A gyermekfejlesztési terület használatával elkerülheti, hogy a szülő fejlesztési területen dolgozó többi eket megzavarja, mivel csak a gyermektér szolgáltatás példányát célzó forgalmat irányítja át, így a szolgáltatás szülőtérpéldánya változatlan marad.

Miután csatlakozott a fürthöz, a rendszer a fejlesztői számítógéphez irányítja a forgalmat, függetlenül attól, hogy a szolgáltatás fut-e a fejlesztői számítógépen.

## <a name="running-code-on-your-development-computer"></a>Kód futtatása a fejlesztői számítógépen

Miután kapcsolatot létesített az AKS-fürttel, bármilyen kódot natív módon futtathat a számítógépen, konténeres kezelés nélkül. A távoli ügynök által fogadott hálózati forgalmat a rendszer átirányítja a kapcsolat során megadott helyi portra, így a natívan futó kód képes elfogadni és feldolgozni a forgalmat. A fürt környezeti változói, kötetei és titkos titkai elérhetővé lesznek a fejlesztői számítógépen futó kód számára. Emellett az Azure Dev Spaces által a fejlesztői számítógéphez hozzáadott gazdagépfájl-bejegyzések és porttovábbítás miatt a kód hálózati forgalmat küldhet a fürtön futó szolgáltatásoknak a fürtből származó szolgáltatásnevek használatával, és hogy a forgalom továbbításra kerül a a fürtben futó szolgáltatások.

Mivel a kód fut a fejlesztői számítógépen, rugalmasan használhatja a fejlesztéshez általában használt eszközt a kód futtatásához és hibakereséséhez. A forgalom a fejlesztői számítógép és a fürt között a teljes kapcsolódási idő alatt kerül átirányításra. Ez az állandó kapcsolat lehetővé teszi a kód indítását, leállítását és újraindítását, amennyire szüksége van, anélkül, hogy újra létre kellene hoznia a kapcsolatot.

Emellett az Azure Dev Spaces lehetővé teszi a környezeti változók és csatlakoztatott fájlok replikálására a fejlesztői számítógép AKS-fürtjében lévő podok számára az *azds-local.env* fájlon keresztül. Ezzel a fájllal új környezeti változókat és kötetcsatlakoztatásokat is létrehozhat.

## <a name="additional-configuration-with-azds-localenv"></a>További konfiguráció az azds-local.env

Az *azds-local.env* fájl lehetővé teszi az AKS-fürtben lévő podok számára elérhető környezeti változók és csatlakoztatott fájlok replikálását. Az *azds-local.env* fájlban a következő műveleteket adhatja meg:

* Töltsön le egy kötetet, és állítsa be az adott kötet elérési útját környezeti változóként.
* Töltsön le egy egyedi fájlt vagy fájlkészletet egy kötetről, és csatlakoztassa a fejlesztői számítógéphez.
* A szolgáltatás elérhetővé téválását, függetlenül attól, hogy a fürthöz kapcsolódik-e.

Íme egy példa *azds-local.env* fájl:

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}

# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

Az alapértelmezett *azds-local.env* fájl nem jön létre automatikusan, ezért manuálisan kell létrehoznia a fájlt a projekt gyökerében.

## <a name="diagnostics-and-logging"></a>Diagnosztika és naplózás

Amikor az AKS-fürthöz csatlakozik, a rendszer a fejlesztői számítógép ideiglenes könyvtárába naplózza a fürt diagnosztikai [naplóit.][azds-tmp-dir] A Visual Studio-kód használatával a *Diagnosztikai adatok megjelenítése* paranccsal kinyomtathatja az aktuális környezeti változókat és DNS-bejegyzéseket az AKS-fürtből.

## <a name="next-steps"></a>További lépések

A helyi fejlesztői számítógép AKS-fürthöz való csatlakoztatásának megkezdéséhez olvassa el [A fejlesztői számítógép csatlakoztatása AKS-fürthöz][connect].

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
