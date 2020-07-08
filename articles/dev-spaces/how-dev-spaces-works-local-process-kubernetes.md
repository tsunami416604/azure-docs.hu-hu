---
title: Helyi folyamatok működése Kubernetes használata esetén
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Ismerteti azokat a folyamatokat, amelyekkel a helyi folyamatok Kubernetes használatával csatlakoztathatók a fejlesztői számítógép a Kubernetes-fürthöz
keywords: Helyi folyamat a Kubernetes, az Azure dev Spaces, a dev Spaces, a Docker, a Kubernetes, az Azure, az AK, az Azure Kubernetes Service, a containers
ms.openlocfilehash: dd126fc55a86b1de115239a31e5adb7b1d264846
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84974398"
---
# <a name="how-local-process-with-kubernetes-works"></a>Helyi folyamatok működése Kubernetes használata esetén

A Kubernetes helyi folyamata lehetővé teszi a kód futtatását és hibakeresését a fejlesztői számítógépen, miközben továbbra is csatlakozik a Kubernetes-fürthöz a többi alkalmazással vagy szolgáltatással. Ha például nagyméretű, több függő szolgáltatásokkal és adatbázisokkal rendelkező, nagy üzemi architektúrával rendelkezik, a függőségek replikálása a fejlesztői számítógépen nehéz lehet. Emellett a belső hurkok fejlesztése során a kód a Kubernetes-fürthöz való létrehozása és üzembe helyezése is lassú, időigényes és nehéz a hibakeresővel való használatra.

A Kubernetes helyi folyamata elkerüli a kód felépítését és üzembe helyezését a fürtön ahelyett, hogy közvetlenül a fejlesztői számítógép és a fürt között hozza létre a kapcsolatot. A fejlesztői számítógép a fürthöz való csatlakoztatása során a hibakeresés lehetővé teszi, hogy a teljes alkalmazás kontextusában gyorsan tesztelje és fejlessze a szolgáltatást, anélkül, hogy Docker-vagy Kubernetes-konfigurációt hozna létre.

A Kubernetes helyi folyamata átirányítja a forgalmat a csatlakoztatott Kubernetes-fürt és a fejlesztői számítógép között. Ez a forgalom átirányítása lehetővé teszi a Kubernetes-fürtön futó fejlesztői számítógép és szolgáltatások programkódjának használatát úgy, mintha ugyanazon a Kubernetes-fürtön belül legyenek. A Kubernetes helyi folyamata lehetővé teszi a környezeti változók és a csatlakoztatott kötetek elérhetővé tételét a Kubernetes-fürtön lévő hüvelyek számára a fejlesztői számítógépén. A fejlesztői számítógép környezeti változóinak és csatlakoztatott köteteinek hozzáférésének biztosításával gyorsan dolgozhat a kódban anélkül, hogy ezeket a függőségeket manuálisan replikálja.

## <a name="using-local-process-with-kubernetes"></a>Helyi folyamat használata a Kubernetes

Ha a Kubernetes helyi folyamatát szeretné használni, a [Visual Studio Code][vs-code] -ot az [Azure dev Spaces][azds-vs-code] és az [Azure Kubernetes Service][az-aks-vs-code] Extensions használatával kell telepíteni és futtatni MacOS vagy Windows 10 rendszeren, valamint a [telepített Azure dev Spaces CLI][azds-cli]-t is. Használhatja a Windows 10 rendszerű [Visual Studio 2019][visual-studio] -et is a *ASP.net és a webes fejlesztéssel* , valamint az *Azure-fejlesztési* számítási feladatokkal, valamint a *AzureDevSpacesTools. LocalKubernetesDebugging* előzetes verzió funkciójának jelzőjét, valamint az [Azure dev Spaces CLI][azds-cli]-t. Ha helyi folyamatot használ a Kubernetes használatával a Kubernetes-fürthöz való csatlakozáshoz, lehetősége van arra, hogy átirányítsa a fürt meglévő Pod-ba irányuló és onnan érkező összes forgalmat a fejlesztői számítógépére.

> [!NOTE]
> Ha helyi folyamatot használ a Kubernetes-ben, a rendszer kéri, hogy adja meg annak a szolgáltatásnak a nevét, amelyről át szeretné irányítani a fejlesztői számítógépét. Ez a beállítás kényelmes módszer az átirányításhoz használt Pod azonosítására. A Kubernetes-fürt és a fejlesztői számítógép közötti összes átirányítás Pod.

Ha a Kubernetes helyi folyamata kapcsolatot létesít a fürttel, akkor a következőket kell tennie:

* Felszólítja, hogy konfigurálja a szolgáltatást a fürtön való lecserélése, a fejlesztői számítógép portja számára a kód használatára, valamint a kód indítási feladatára egyszeri műveletként.
* Lecseréli a tárolót a fürt Pod-tárolójába egy távoli ügynök tárolóval, amely átirányítja a forgalmat a fejlesztői számítógépére.
* A [kubectl portjának futtatása – továbbítsa][kubectl-port-forward] a fejlesztői számítógépét, hogy továbbítsa a forgalmat a fejlesztői számítógépről a fürtön futó távoli ügynök felé.
* A távoli ügynök használatával gyűjti a környezet információit a fürtből. A környezeti információk közé tartoznak a környezeti változók, a látható szolgáltatások, a kötet-csatlakoztatások és a titkos csatolások.
* Beállítja a környezetet a Visual Studióban vagy a Visual Studio Code-ban, így a fejlesztési számítógépen lévő szolgáltatás ugyanazokat a változókat érheti el, mint ha a fürtön futott.  
* Frissíti a gazdagépek fájlját, hogy a fürtön lévő szolgáltatásokat a fejlesztői számítógép helyi IP-címeire képezze. Ezek a gazdagépek bejegyzései lehetővé teszik a fejlesztői számítógépen futó kód számára a fürtön futó más szolgáltatásoknak küldött kéréseket. A Hosts fájl frissítéséhez a Kubernetes helyi folyamata rendszergazdai hozzáférést kér a fejlesztői számítógépen a fürthöz való csatlakozáskor.
* Elindítja a futtatását és a kód hibakeresését a fejlesztői számítógépen. Ha szükséges, a Kubernetes helyi folyamata a szükséges portokat felszabadítja a fejlesztői számítógépen, ha leállítja azokat a szolgáltatásokat vagy folyamatokat, amelyek jelenleg használják ezeket a portokat.

Miután létrehozta a kapcsolatot a fürttel, a tárolókra bontás nélkül futtathatja és hibakeresést végezhet a számítógépen, és a kód közvetlenül tud működni a fürt többi részével. A távoli ügynök által fogadott hálózati forgalom átirányítva a kapcsolat során megadott helyi portra, így a natívan futó kód képes fogadni és feldolgozni a forgalmat. A fürtben lévő környezeti változók, kötetek és titkos kódok elérhetők a fejlesztői számítógépen futó kód számára. Emellett a Kubernetes helyi folyamata által a fejlesztői számítógéphez hozzáadott gazdagépek bejegyzései és továbbítása miatt a kód küldhet hálózati forgalmat a fürtön futó szolgáltatásoknak a fürtből származó szolgáltatások neveivel, és a forgalom a fürtben futó szolgáltatásokra lesz továbbítva. A forgalom a teljes kapcsolódási idő alatt a fejlesztői számítógép és a fürt között lesz átirányítva.

## <a name="diagnostics-and-logging"></a>Diagnosztika és naplózás

Ha helyi folyamatot használ a Kubernetes a fürthöz való csatlakozáshoz, a fürtből származó diagnosztikai naplókat a rendszer naplózza a fejlesztői számítógép [ideiglenes könyvtárába][azds-tmp-dir]. A Visual Studio Code használatával a *diagnosztikai adatok megjelenítése* paranccsal is kinyomtathatja a fürt aktuális környezeti változóit és DNS-bejegyzéseit.

## <a name="limitations"></a>Korlátozások

A Kubernetes helyi folyamata a következő korlátozásokkal rendelkezik:

* A Kubernetes helyi folyamata átirányítja a forgalmat egyetlen szolgáltatásra a fejlesztői számítógépén. A Kubernetes nem használhatja a helyi folyamatot több szolgáltatás egyszerre történő átirányításához.
* A szolgáltatáshoz való kapcsolódáshoz a szolgáltatást egyetlen hüvelyrel kell támogatnia. Nem csatlakozhat több hüvelyrel rendelkező szolgáltatáshoz, például egy replikával rendelkező szolgáltatáshoz.
* A pod csak egyetlen tárolóval rendelkezhet, amely az adott Pod-ban helyi folyamathoz, a sikeres kapcsolódáshoz pedig a Kubernetes használatával működik. A Kubernetes helyi folyamata nem tud olyan szolgáltatásokhoz csatlakozni, amelyeken további tárolók vannak, például az oldalkocsis tárolók, amelyeket a szolgáltatások rácsvonalai fecskendeznek be.
* A Kubernetes helyi folyamatának emelt szintű engedélyekkel kell rendelkeznie a fejlesztői számítógépen való futtatáshoz a gazdagépek fájljának szerkesztéséhez.

## <a name="next-steps"></a>További lépések

A helyi folyamat Kubernetes való használatának megkezdéséhez a helyi fejlesztési számítógéphez a fürthöz való kapcsolódáshoz tekintse meg a [helyi folyamat használata a Kubernetes a Visual Studio Code][local-process-kubernetes-vs-code] használatával című témakört, és [használja a helyi folyamatot a Kubernetes és a Visual Studio][local-process-kubernetes-vs]használatával.

[azds-cli]: how-to/install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[local-process-kubernetes-vs-code]: how-to/local-process-kubernetes-vs-code.md
[local-process-kubernetes-vs]: how-to/local-process-kubernetes-visual-studio.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[visual-studio]: https://www.visualstudio.com/vs/
[vs-code]: https://code.visualstudio.com/download