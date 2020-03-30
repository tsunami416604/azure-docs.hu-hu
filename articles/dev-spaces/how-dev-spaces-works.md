---
title: Az Azure Dev Spaces működése
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Az Azure Dev Spaces energiaellátását előíró folyamatok ismertetése
keywords: Azure dev spaces, fejlesztői terek, Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: 99b0b3309d115b450bfca94871b6defd885349fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234970"
---
# <a name="how-azure-dev-spaces-works"></a>Az Azure Dev Spaces működése

A Kubernetes-alkalmazások fejlesztése kihívást jelenthet. Docker és Kubernetes konfigurációs fájlokat kell. Ki kell találnia, hogyan tesztelheti az alkalmazást helyileg, és más függő szolgáltatásokkal való interakció. Előfordulhat, hogy egyszerre több szolgáltatás fejlesztését és tesztelését kell kezelnie egy fejlesztői csapattal.

Az Azure Dev Spaces többféle lehetőséget kínál a Kubernetes-alkalmazások gyors iterálására és hibakeresésére, valamint a csapatával való együttműködésre. Ez a cikk ismerteti, mire képes az Azure Dev Spaces, és hogyan működik.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Gyorsan iterate és hibakeresés a Kubernetes alkalmazás

Az Azure Dev Spaces csökkenti a Kubernetes-alkalmazás fejlesztésére, tesztelésére és iterészézésére irányuló erőfeszítéseket az AKS-fürt környezetében. Ez a csökkentés lehetővé teszi a fejlesztők számára, hogy az alkalmazások üzleti logikájára összpontosítsanak, és ne konfigurálják szolgáltatásaikat a Kubernetes-ben való futtatáshoz.

### <a name="connect-your-development-machine-to-aks"></a>Csatlakoztassa fejlesztőgépét az AKS-hez

Az Azure Dev Spaces segítségével csatlakoztathatja a fejlesztői számítógépet az AKS-fürthöz, így úgy futtathatja és hibakeresést okozhat a fejlesztői számítógépen, mintha a fürtön futna. Az Azure Dev Spaces átirányítja a forgalmat a csatlakoztatott AKS-fürt által a fürtön, amely egy távoli ügynökként működik, hogy irányítsa át a forgalmat a fejlesztői gép és a fürt között. Ez a forgalomátirányítás lehetővé teszi, hogy a fejlesztői számítógépen és az AKS-fürtön futó szolgáltatások úgy kommunikáljanak, mintha ugyanabban az AKS-fürtben lennének. A fejlesztői gép AKS-hez való csatlakoztatásáról a [Fejlesztői számítógép és az AKS-fürt csatlakoztatása című témakörben talál][how-it-works-connect]további információt.

### <a name="run-your-code-in-aks"></a>A kód futtatása az AKS-ben

A fejlesztési számítógép és az AKS-fürt közötti forgalom átirányítása mellett az Azure Dev Spaces segítségével konfigurálhatja és gyorsan futtathatja a kódot közvetlenül az AKS-ben. A Visual Studio, a Visual Studio-kód vagy az Azure Dev Spaces CLI segítségével az Azure Dev-helyek feltöltik a kódot a fürtbe, majd létreveszik és futtatják azt. Az Azure Dev-terek is intelligensen szinkronizálja a kódváltozásokat, és indítsa újra a szolgáltatást, hogy szükség szerint tükrözze a módosításokat. A kód futtatása közben a buildnaplók és a HTTP-nyomkövetések streamelése az ügyfélhez történik, így nyomon követheti a folyamatot, és diagnosztizálhatja a problémákat. Az Azure Dev Spaces használatával is csatolhatja a hibakeresőt a Visual Studio és a Visual Studio-kód java, node.js és .NET Core szolgáltatásokhoz. További információ: [Hogyan működik a projekt előkészítése az Azure Dev Spaces számára,][how-it-works-prep] [Hogyan működik a kód futtatása az Azure Dev Spaces használatával,][how-it-works-up]valamint A kód azure dev [spaces-szel történő távoli hibakeresése][how-it-works-remote-debugging]című témakörben.

## <a name="team-development"></a>Csoportos fejlesztés

Az Azure Dev Spaces segítségével a csapatok hatékonyan dolgozhatnak az alkalmazásukon ugyanazon az AKS-fürtön anélkül, hogy zavaróak lennek.

### <a name="intelligent-routing-between-dev-spaces"></a>Intelligens útválasztás a fejlesztési terek között

Az Azure Dev Spaces használatával a csapat megoszthatja a felhőalapú natív alkalmazást futtató egyetlen AKS-fürtöt, és elszigetelt fejlesztői tereket hozhat létre, ahol a csapat anélkül fejleszthet, tesztelhet és hibakeresést, hogy megzavarnák a többi fejlesztői tereket. Az alkalmazás alapverziója gyökérfejlesztési területen fut. A csapattagok ezután független gyermekfejlesztői tereket hoznak létre az alkalmazás fejlesztési, tesztelési és hibakeresési módosításai alapján. A fejlesztői terekben az útválasztási képességeken keresztül a gyermekfejlesztői terek továbbíthatják a kérelmeket a gyermek fejlesztési térben futó szolgáltatások és a szülő fejlesztési tér között. Ez az útválasztás lehetővé teszi a fejlesztők számára, hogy a szolgáltatás saját verzióját futtassák, miközben újra felhasználják a szülőterületfüggő szolgáltatásait. Minden gyermektérnek saját egyedi URL-címe van, amelyet mások megoszthatnak és elérhetnek az együttműködés hez. Az útválasztási lehetőségek Azure dev spaces-ben való működéséről az [Útválasztás és az Azure dev spaces működésének című témakörben][how-it-works-routing]talál további információt.

### <a name="live-testing-an-open-pull-request"></a>Nyílt lekéréses kérelem élő tesztelése

A GitHub-műveletek et az Azure Dev Spaces használatával is tesztelheti az alkalmazás módosításait egy lekéréses kérelemben közvetlenül a fürtben, mielőtt egyesülne. Az Azure dev spaces automatikusan telepítheti az alkalmazás felülvizsgálati verzióját a fürtre, lehetővé téve a szerző és a többi csapattag számára, hogy áttekintsék a módosításokat a teljes alkalmazás környezetében. Az Azure Dev Spaces útválasztási képességeit használva az alkalmazás ez a felülvizsgálati verziója is telepítve van a fürtben anélkül, hogy más fejlesztési tereket befolyásolna. Mindezek a képességek lehetővé teszik, hogy magabiztosan jóváhagyja és egyesítse a lekéréses kérelmeket. A GitHub-műveletek és az Azure Dev Spaces példa: [GitHub-műveletek & Azure Kubernetes-szolgáltatás.][pr-flow]

## <a name="next-steps"></a>További lépések

A helyi fejlesztői gép AKS-fürthöz való csatlakoztatásának megkezdéséhez olvassa el [a fejlesztői gép csatlakoztatása AKS-fürthöz][connect].

Az Azure Dev Spaces csapatfejlesztéshez való használatának első lépései az [Azure Dev Spaces rövid útmutatójában.][quickstart-team]

[connect]: how-to/connect.md
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[routing]: #team-development