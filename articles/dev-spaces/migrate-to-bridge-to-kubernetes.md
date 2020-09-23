---
title: Migrálás a Kubernetes
services: azure-dev-spaces
ms.date: 09/21/2020
ms.topic: conceptual
description: Ismerteti azokat a folyamatokat, amelyekkel a Power Azure dev Spaces
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, híd a Kubernetes-hoz
ms.openlocfilehash: b585ee20efb7b377a041152996ef41d8c59c539e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995521"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Migrálás a Kubernetes

A Bridge to Kubernetes az Azure dev Spaces szolgáltatással működő számos fejlesztési forgatókönyv esetén is enyhébb súlyozást biztosít. A Bridge to Kubernetes egy ügyféloldali élmény, amely a [Visual Studióban][vs]   és a [Visual Studio Code][vsc]-ban lévő bővítményeket használja.  

A Kubernetes-hez való híd segít a fejlesztési élményben azáltal, hogy lehetővé teszi, hogy egy létesített Kubernetes alkalmazás tartalmazzon egy, a helyi fejlesztési munkaállomáson futó szolgáltatást. A dev Spaces szolgáltatással ellentétben a Kubernetes-híd csökkenti a belső hurok-bonyolultságot a Docker-és Kubernetes-konfigurációk létrehozásához, így a fejlesztők csak a szolgáltatói programkód üzleti logikáját tudják összpontosítani.

A Bridge to Kubernetes segítségével megismételheti a fejlesztői számítógépen futó kódokat, miközben a függőségeket és a Kubernetes-környezet meglévő konfigurációját használja. Ezzel szemben az Azure dev Spaces üzembe helyezi a szolgáltatást a Kubernetes-környezetben, mielőtt távolról felhasználja a szolgáltatását, és megismétli a kódját.

Ez a cikk az Azure dev Spaces és a Bridge és a Kubernetes közötti összehasonlítást, valamint az Azure dev Spaces-ről a Kubernetes-re történő áttelepítésre vonatkozó utasításokat tartalmazza.

## <a name="development-approaches"></a>Fejlesztési módszerek

![Fejlesztési módszerek](media/migrate-to-btk/development-approaches.svg)

Az Azure dev Spaces lehetővé tette, hogy a fejlesztők közvetlenül az AK-fürtön futó kóddal működjenek, így elkerülhető, hogy olyan helyi környezetre van szükség, amely nem hasonlít az üzembe helyezett környezet Kubernetes. Ez a megközelítés javította a fejlesztés bizonyos aspektusait, de bevezette a tanulás és a további fogalmak, például a Docker, a Kubernetes és a Helm megkezdésének előfeltételeit, mielőtt megkezdené az Azure dev Spaces használatát.

A Kubernetes híd lehetővé teszi a fejlesztők számára, hogy közvetlenül a fejlesztői számítógépeken működjenek, miközben a fürt többi részével együttműködnek. Ez a megközelítés kihasználja a programkódok futtatásának ismeretét és sebességét közvetlenül a fejlesztői számítógépeken, a fürt által biztosított függőségek és környezetek megosztása mellett. Ez a megközelítés kihasználja a Kubernetes-ben futó hűséget és skálázást is.

## <a name="feature-comparison"></a>Funkciók összehasonlítása

Az Azure dev Spaces és a Bridge to Kubernetes hasonló funkciókkal rendelkezik, több területen is különböznek:

| Követelmény  | Azure Dev Spaces  | Híd Kubernetes  |
|---------------|-------------------|--------------------------------|
| Azure Kubernetes Service | 15 Azure-régióban | Bármely AK szolgáltatási régió    |
| **Biztonság** |
| Biztonsági hozzáférés szükséges a fürtön  | AK-fürt közreműködői  | Kubernetes RBAC – központi telepítés frissítése   |
| Biztonsági hozzáférés szükséges a fejlesztői számítógépen  | N.A.  | Helyi rendszergazda/sudo   |
| **Használhatóság** |
| Független a Kubernetes és a Docker-összetevőktől  | Nem  | Igen   |
| Módosítások automatikus visszaállítása, hibakeresés utáni  | Nem  | Igen   |
| **Környezetek** |
| Együttműködik a Visual Studio 2019-mel  | Igen  | Igen   |
| Működik a Visual Studio Code-ban  | Igen  | Igen   |
| Együttműködik a parancssori felülettel  | Igen  | Nem   |
| **Operációs rendszer kompatibilitása** |
| Windows 10 rendszeren működik  | Igen  | Igen  |
| Linux rendszeren működik  | Igen  | Igen  |
| MacOS rendszeren működik  | Igen  | Igen  |
| **Képességek** |
| Fejlesztői elkülönítés vagy csoportmunka  | Igen  | Igen  |
| Környezeti változók szelektív felülírása  | Nem  | Igen  |
| Docker és Helm diagram létrehozása  | Igen  | Nem  |
| Kód állandó üzembe helyezése a Kubernetes-ben  | Igen  | Nem  |
| Távoli hibakeresés a Kubernetes Pod-ban  | Igen  | Nem  |
| Helyi hibakeresés, csatlakozás a Kubernetes-hez  | Nem  | Igen  |
| Egyszerre több szolgáltatás hibakeresése ugyanazon a munkaállomáson  | Igen  | Igen  |

## <a name="kubernetes-inner-loop-development"></a>Kubernetes belső hurok fejlesztése

Az Azure dev Spaces és a Bridge közötti legnagyobb különbség a Kubernetes, ahol a kód fut. Az Azure dev Spaces segítségével fejlesztheti és hibakeresést végezhet a webszolgáltatás kódjában, de ehhez a kódot a fürtben kell futtatnia. A Bridge to Kubernetes lehetővé teszi, hogy közvetlenül a fejlesztői számítógépen fejlesszen és végezzen hibakeresést a szolgáltatásban, miközben a Kubernetes-ben futó nagyobb alkalmazások kontextusában is. A Kubernetes-híd kiterjeszti a Kubernetes-fürt kerületét, és lehetővé teszi a helyi folyamatok számára a Kubernetes konfigurációjának öröklését.

![Belső hurok fejlesztése](media/migrate-to-btk/btk-graphic-non-isolated.gif)

A híd Kubernetes való használata esetén a rendszer létrehoz egy hálózati kapcsolatot a fejlesztői számítógép és a fürt között.A csatlakozás élettartama érdekében a rendszer egy proxyt ad hozzá a fürthöz a Kubernetes-telepítés helyett, amely átirányítja a kéréseket a szolgáltatásnak a fejlesztői számítógép felé. A kapcsolat bontásakor az alkalmazás központi telepítése a fürtön futó üzemelő példány eredeti verziójának használatára fog visszatérni. Ez a megközelítés különbözik attól, hogy az Azure dev Spaces hogyan működik, mely kód szinkronizálva van a fürttel, létrehozva, majd futtatva. Az Azure dev Spaces nem állítja vissza a kódot.

A Kubernetes-hez készült Bridge rugalmasan együttműködik a Kubernetes-ben futó alkalmazásokkal, a telepítési módszertől függetlenül. Ha CI/CD-t használ az alkalmazás létrehozásához és futtatásához, függetlenül attól, hogy a létrehozott eszközöket vagy egyéni parancsfájlokat használja-e, továbbra is használhatja a Bridget a Kubernetes a kód fejlesztéséhez és hibakereséséhez.

> [!TIP]
> A [Microsoft Kubernetes bővítmény][kubernetes-extension] lehetővé teszi az Kubernetes-jegyzékek gyors fejlesztését az IntelliSense segítségével, és segít a bevezetési Helm-diagramok létrehozásában.  

### <a name="use-visual-studio-to-transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Áttérés a Kubernetes a Visual Studióval az Azure dev Spaces szolgáltatásból

1. Frissítse a Visual Studio IDE-t a 16,7-es vagy újabb verzióra, és telepítse a hidat a Kubernetes bővítményre a [Visual Studio piactérről][vs-marketplace].
1. Tiltsa le az Azure dev Spaces-vezérlőt az Azure Portal vagy az [Azure dev Spaces CLI][azds-delete]használatával.
1. Távolítsa el a `azds.yaml` fájlt a projektből.
1. Telepítse újra az alkalmazást.
1. Konfigurálja a hidat a telepített alkalmazás Kubernetes. A Kubernetes a Visual Studióban való használatával kapcsolatos további információkért lásd: a [Bridge használata a Kubernetes][use-btk-vs].
1. Indítsa el a hibakeresést a Visual Studióban az újonnan létrehozott híd használatával a Kubernetes hibakeresési profiljához.

### <a name="use-visual-studio-code-to-transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>A Visual Studio Code használata a Kubernetes való áttéréshez az Azure dev Spaces használatával

1. Telepítse a [hidat a Kubernetes bővítményre][vsc-marketplace].
1. Tiltsa le az Azure dev Spaces-vezérlőt az Azure Portal vagy az [Azure dev Spaces CLI][azds-delete]használatával.
1. Távolítsa el a `azds.yaml` fájlt a projektből.
1. Telepítse újra az alkalmazást.
1. Konfigurálja a hidat a telepített alkalmazás Kubernetes. A Kubernetes Visual Studio Code-ban való használatával kapcsolatos további információkért lásd: a [Bridge használata a Kubernetes][use-btk-vsc].
1. Indítsa el a hibakeresést a Visual Studio Code-ban az újonnan létrehozott híd használatával a Kubernetes indítási profilhoz.

## <a name="team-development-in-a-shared-cluster"></a>Csoport fejlesztése megosztott fürtben

A Kubernetes-hez a fejlesztői-specifikus útválasztást is használhatja. Az Azure dev Spaces csapatának fejlesztési forgatókönyve több Kubernetes-névteret használ, hogy elkülönítse a szolgáltatást a többi alkalmazástól a szülő és a gyermek névterek fogalma alapján. A Kubernetes híd ugyanazokat a képességeket kínálja, de a teljesítmény jellemzői és az ugyanazon alkalmazási névtéren belül vannak.

Mind a Kubernetes, mind az Azure dev Spaces esetében a HTTP-fejlécek jelennek meg, és az alkalmazáson keresztül propagálva lesznek. Ha már konfigurálta az alkalmazást, hogy kezelje az Azure dev Spaces-fejlécek propagálását, akkor a fejlécet frissíteni kell. Ha át szeretne térni az Azure dev Spaces-ről a Kubernetes-re, frissítse a konfigurált fejlécet a *azds-Route-as* -től a *Kubernetes-Route-as*értékre.

## <a name="evaluate-bridge-to-kubernetes"></a>Híd kiértékelése a Kubernetes

Ha azt szeretné, hogy a Kubernetes az Azure dev Spaces a fürtben való letiltása előtt kísérletezzen a Bridge használatával, a legegyszerűbb módszer egy új fürt használata. Ha az Azure dev Spaces és a Bridge Kubernetes is ugyanazon a fürtön kísérli meg, akkor az útválasztási funkciók mindkét rendszeren való használata során problémákba ütközik.

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>A Visual Studio használata a Kubernetes-híd kiértékeléséhez

1. Frissítse a Visual Studio IDE-t a 16,7-es vagy újabb verzióra, és telepítse a hidat a Kubernetes bővítményre a [Visual Studio piactérről][vs-marketplace].
1. Hozzon létre egy új AK-fürtöt, és telepítse az alkalmazást. Használhat egy [minta alkalmazást][btk-sample-app]is.
1. Konfigurálja a hidat a telepített alkalmazás Kubernetes. A Kubernetes a Visual Studióban való használatával kapcsolatos további információkért lásd: a [Bridge használata a Kubernetes][use-btk-vs].
1. Indítsa el a hibakeresést a Visual Studióban az újonnan létrehozott híd használatával a Kubernetes hibakeresési profiljához.

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>A Kubernetes-híd kiértékelése a Visual Studio Code használatával

1. Telepítse a [hidat a Kubernetes bővítményre][vsc-marketplace].
1. Hozzon létre egy új AK-fürtöt, és telepítse az alkalmazást. Használhat egy [minta alkalmazást][btk-sample-app]is.
1. Konfigurálja a hidat a telepített alkalmazás Kubernetes. A Kubernetes Visual Studio Code-ban való használatával kapcsolatos további információkért lásd: a [Bridge használata a Kubernetes][use-btk-vsc].
1. Indítsa el a hibakeresést a Visual Studióban az újonnan létrehozott híd használatával a Kubernetes indítási profilhoz.

## <a name="next-steps"></a>Következő lépések

További információ a híd Kubernetes működéséről.

> [!div class="nextstepaction"]
> [Hogyan működik a híd a Kubernetes][how-it-works-bridge-to-kubernetes]


[azds-delete]: how-to/install-dev-spaces.md#remove-azure-dev-spaces-using-the-cli
[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes?view=vs-2019#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes?view=vs-2019#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/