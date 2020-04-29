---
title: A fejlesztői számítógép csatlakoztatása az AK-fürthöz
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Ismerteti az Azure dev Spaces használatával a fejlesztői számítógép Azure Kubernetes Service-fürthöz való összekapcsolásához szükséges folyamatokat.
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241711"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>A fejlesztői számítógép csatlakoztatása az AK-fürthöz

Az Azure dev Spaces használatával összekapcsolhatja a fejlesztési számítógépét az AK-fürttel, így a kódot futtathatja és hibakeresést végezhet a fejlesztői számítógépen, mintha a fürtön futna. Az Azure dev Spaces átirányítja a forgalmat a csatlakoztatott AK-fürt között úgy, hogy egy olyan Pod-t futtat a fürtön, amely távoli ügynökként működik a fejlesztői gép és a fürt közötti forgalom átirányításához. Ez a forgalom átirányítása lehetővé teszi, hogy az AK-fürtön futó fejlesztői számítógép és szolgáltatások programkódja kommunikáljon úgy, mintha ugyanabban az AK-fürtben lennének. Ez a kapcsolódás lehetővé teszi a kód futtatását és hibakeresését a fejlesztői számítógépen lévő tárolóval vagy anélkül. A fejlesztői számítógép fürthöz csatlakoztatásával gyorsan fejlesztheti alkalmazásait, és teljes körű tesztelést végezhet.

## <a name="connecting-to-your-cluster"></a>Csatlakozás a fürthöz

A [Visual Studio Code][vs-code] használatával csatlakozik a meglévő AK-fürthöz a MacOS vagy Windows 10 rendszeren futtatott [Azure dev Spaces][azds-vs-code] bővítménnyel. Amikor kapcsolatot létesít, lehetősége van arra, hogy átirányítsa a fürtben lévő új vagy meglévő Pod-ra irányuló összes forgalmat a fejlesztői számítógépére.

> [!NOTE]
> Ha a Visual Studio Code-ot használja a fürthöz való csatlakozáshoz, az Azure dev Spaces bővítmény lehetőséget biztosít a szolgáltatás átirányítására a fejlesztői számítógépére. Ez a beállítás kényelmes módszer az átirányításhoz használt Pod azonosítására. Az AK-fürt és a fejlesztői számítógép közötti összes átirányítás Pod.

A fürthöz való csatlakozáshoz nem szükséges, hogy a fürtön engedélyezve legyenek az Azure dev-szóközök. Ehelyett, amikor az Azure dev Spaces bővítmény kapcsolatot létesít a fürttel, a következő:

* Lecseréli a tárolót az AK-fürt Pod-tárolójába egy távoli ügynök tárolóval, amely átirányítja a forgalmat a fejlesztői számítógépére. Új Pod átirányítása esetén az Azure dev Spaces új Pod-t hoz létre az AK-fürtben a távoli ügynökkel.
* A [kubectl portjának futtatása – továbbítsa][kubectl-port-forward] a fejlesztői számítógépét, hogy továbbítsa a forgalmat a fejlesztői számítógépről a fürtön futó távoli ügynök felé.
* A távoli ügynök használatával gyűjti a környezet információit a fürtből. A környezeti információk közé tartoznak a környezeti változók, a látható szolgáltatások, a kötet-csatlakoztatások és a titkos csatolások.
* Beállítja a környezetet a Visual Studio Code-terminálban, így a fejlesztési számítógépen lévő szolgáltatás ugyanazokat a változókat érheti el, mint ha a fürtön futott.  
* Frissíti a gazdagépek fájlját, hogy az AK-fürtön lévő szolgáltatásokat leképezje a fejlesztői számítógép helyi IP-címeire. Ezek a gazdagépek bejegyzései lehetővé teszik a fejlesztői számítógépen futó kód számára a fürtön futó más szolgáltatásoknak küldött kéréseket. A gazdagépek fájljának frissítéséhez az Azure dev Spaces a fürthöz való csatlakozáskor kérni fogja a rendszergazdai hozzáférést a fejlesztői számítógépen.

Ha az Azure dev Spaces szolgáltatás engedélyezve van a fürtön, lehetősége van az [Azure dev Spaces által kínált forgalom átirányítására][how-it-works-routing]is. Az Azure dev Spaces által kínált forgalom átirányítása lehetővé teszi, hogy a szolgáltatás egy gyermek-fejlesztési területen futó példányához kapcsolódjon. A gyermek-fejlesztési terület használatával elkerülhető, hogy mások ne zavarják meg a szülő fejlesztői területét, mivel Ön csak a saját szolgáltatásának alárendelt területére irányuló forgalmat irányítja át, így a szolgáltatás fölérendelt területének példánya nem módosul.

Miután kapcsolódott a fürthöz, a rendszer átirányítja a forgalmat a fejlesztői számítógépre, függetlenül attól, hogy a szolgáltatás fut-e a fejlesztői számítógépen.

## <a name="running-code-on-your-development-computer"></a>Kód futtatása a fejlesztői számítógépen

Miután létrehozta a kapcsolatot az AK-fürttel, a számítógépen natív módon futtathat bármilyen kódot, tárolókra bontás nélkül. A távoli ügynök által fogadott hálózati forgalom átirányítva a kapcsolat során megadott helyi portra, így a natívan futó kód képes fogadni és feldolgozni a forgalmat. A fürtben lévő környezeti változók, kötetek és titkos kódok elérhetők a fejlesztői számítógépen futó kód számára. Emellett az Azure dev Spaces által a fejlesztői számítógéphez hozzáadott gazdagépek bejegyzései és továbbítása miatt a kód küldhet hálózati forgalmat a fürtön futó szolgáltatásoknak a fürtből származó szolgáltatások neveivel, és a forgalom a fürtben futó szolgáltatásokra lesz továbbítva.

Mivel a kód a fejlesztői számítógépen fut, rugalmasan használhatja bármely olyan eszközt, amelyet általában a fejlesztéshez használ a kód futtatásához és hibakereséséhez. A forgalom a teljes kapcsolódási idő alatt a fejlesztői számítógép és a fürt között lesz átirányítva. Ez az állandó kapcsolat lehetővé teszi a kód elindítását, leállítását és újraindítását, ha nincs szükség a kapcsolat újbóli létrehozására.

Emellett az Azure dev Spaces lehetővé teszi a környezeti változók és a csatlakoztatott fájlok a *azds-local. env* fájlon keresztül történő, a fejlesztői számítógépén található hüvelyek számára elérhetővé tételét. Ezt a fájlt használhatja új környezeti változók és kötet-csatlakoztatások létrehozására is.

## <a name="additional-configuration-with-azds-localenv"></a>További konfiguráció a azds-local. env-vel

A *azds-local. env* fájl lehetővé teszi a környezeti változók és csatlakoztatott fájlok replikálását a hüvelyek számára az AK-fürtben. A *azds-local. env* fájlban a következő műveleteket adhatja meg:

* Töltsön le egy kötetet, és állítsa a kötet elérési útját környezeti változóként.
* Töltsön le egy adott fájlt vagy fájlokat egy kötetről, és csatlakoztassa azt a fejlesztői számítógépen.
* Elérhetővé tehet egy szolgáltatást, függetlenül attól, hogy melyik fürthöz csatlakozik.

Íme egy példa a *azds-local. env* fájlra:

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

Az alapértelmezett *azds-local. env* fájl nem jön létre automatikusan, ezért manuálisan kell létrehoznia a fájlt a projekt gyökerében.

## <a name="diagnostics-and-logging"></a>Diagnosztika és naplózás

Az AK-fürthöz való csatlakozáskor a fürtből származó diagnosztikai naplókat a rendszer a fejlesztői számítógép [ideiglenes könyvtárába][azds-tmp-dir]naplózza. A Visual Studio Code használatával a *diagnosztikai adatok megjelenítése* paranccsal is kinyomtathatja az AK-fürt aktuális környezeti változóit és DNS-bejegyzéseit.

## <a name="next-steps"></a>További lépések

A helyi fejlesztési számítógép AK-fürthöz való csatlakoztatásának megkezdéséhez lásd: [a fejlesztői számítógép csatlakoztatása egy AK-fürthöz][connect].

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
