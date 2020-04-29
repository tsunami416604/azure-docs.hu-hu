---
title: Az Azure Dev Spaces működése
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Ismerteti azokat a folyamatokat, amelyekkel a Power Azure dev Spaces
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: 99b0b3309d115b450bfca94871b6defd885349fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80234970"
---
# <a name="how-azure-dev-spaces-works"></a>Az Azure Dev Spaces működése

A Kubernetes-alkalmazások fejlesztése kihívást jelenthet. A Docker és a Kubernetes konfigurációs fájljaira van szüksége. Meg kell határoznia, hogyan tesztelheti az alkalmazást helyileg, és hogyan dolgozhat más függő szolgáltatásokkal. Előfordulhat, hogy egyszerre több szolgáltatás fejlesztését és tesztelését kell kezelnie, és egy fejlesztői csapattal kell foglalkoznia.

Az Azure dev Spaces számos lehetőséget kínál a Kubernetes-alkalmazások gyors megismétlésére és hibakeresésére, valamint a csapattal való együttműködésre. Ez a cikk ismerteti az Azure dev Spaces által elvégezhető műveleteket és azok működését.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Kubernetes-alkalmazás gyors megismétlése és hibakeresése

Az Azure dev Spaces szolgáltatás csökkenti a Kubernetes-alkalmazás fejlesztését, tesztelését és iterációját az AK-fürt környezetében. Ez a csökkentési erőfeszítések lehetővé teszik a fejlesztők számára, hogy alkalmazásaikat üzleti logikára összpontosítsanak, és nem konfigurálja a szolgáltatásaikat a Kubernetes-ben való futtatásra.

### <a name="connect-your-development-machine-to-aks"></a>A fejlesztői gép összekötése az AK-val

Az Azure dev Spaces használatával összekapcsolhatja a fejlesztési számítógépét az AK-fürttel, így a kódot futtathatja és hibakeresést végezhet a fejlesztői számítógépen, mintha a fürtön futna. Az Azure dev Spaces átirányítja a forgalmat a csatlakoztatott AK-fürt között úgy, hogy egy olyan Pod-t futtat a fürtön, amely távoli ügynökként működik a fejlesztői gép és a fürt közötti forgalom átirányításához. Ez a forgalom átirányítása lehetővé teszi, hogy az AK-fürtön futó fejlesztői számítógép és szolgáltatások programkódja kommunikáljon úgy, mintha ugyanabban az AK-fürtben lennének. A fejlesztői gép AK-hoz való csatlakoztatásával kapcsolatos további információkért lásd: [a fejlesztői számítógép csatlakoztatása az AK-fürthöz][how-it-works-connect].

### <a name="run-your-code-in-aks"></a>A kód futtatása AK-ban

Amellett, hogy átirányítja a forgalmat a fejlesztői számítógép és az AK-fürt között, az Azure dev Spaces használatával konfigurálhatja és gyorsan futtathatja a kódot az AK-ban. A Visual Studióban, a Visual Studio Code-ban vagy az Azure dev Spaces CLI-vel az Azure dev Spaces feltölti a kódot a fürtbe, majd felépíti és futtatja azt. Az Azure dev Spaces képes intelligensen szinkronizálni a kód módosításait, és újraindítani a szolgáltatást, hogy szükség szerint tükrözze a módosításokat. A kód futtatásakor a rendszer a naplókat és a HTTP-nyomkövetéseket adatfolyamként továbbítja az ügyfélnek, így nyomon követheti a folyamat előrehaladását, és diagnosztizálhatja az esetleges problémákat. Használhatja az Azure dev Spaces szolgáltatást is, hogy a hibakeresőt a Visual Studióban és a Visual Studio Code-ban a Java, a Node. js és a .NET Core szolgáltatásokhoz csatolja. További információkért lásd: [projekt előkészítése az Azure dev Spaces működéséhez][how-it-works-prep], [Hogyan működik a kód az Azure dev Spaces][how-it-works-up]szolgáltatással, és [Hogyan működik a kód távoli hibakeresése az Azure dev Spaces][how-it-works-remote-debugging]szolgáltatással.

## <a name="team-development"></a>Csoportos fejlesztés

Az Azure dev Spaces lehetővé teszi, hogy az egyazon AK-fürtön az alkalmazások hatékonyabban működjenek az alkalmazáson, és ne legyen zavaró.

### <a name="intelligent-routing-between-dev-spaces"></a>Intelligens útválasztás a fejlesztői helyek között

Az Azure dev Spaces használatával a csapat megoszthat egy Felhőbeli natív alkalmazást futtató egyoszlopos fürtöt, és elkülönített fejlesztői helyeket hozhat létre, ahol a csapat fejlesztheti, tesztelheti és hibakeresést végezhet a többi fejlesztői szóközzel való beavatkozás nélkül. Az alkalmazás alapszintű verziója egy gyökérszintű fejlesztői térben fut. A csapattagok ezután létrehozzák a független gyermek-fejlesztési helyeket az alkalmazás fejlesztésének, tesztelésének és hibakeresésének alapvető területe alapján. A dev Spaces útválasztási képességein keresztül a gyermek-fejlesztési területek a gyermek-fejlesztési térben és a szülő fejlesztői térben futó szolgáltatások közötti kérelmeket is átirányítják. Ez az Útválasztás lehetővé teszi a fejlesztők számára, hogy egy szolgáltatás saját verzióját futtassák, miközben a fölérendelt területtől függő szolgáltatások újrafelhasználását. Minden egyes gyermekobjektum saját egyedi URL-címmel rendelkezik, amelyet mások is megoszthatnak és használhatnak együttműködésre. További információ arról, hogyan működik az Útválasztás az Azure dev Spaces-ben: [Hogyan működik az Útválasztás az Azure dev Spaces szolgáltatással][how-it-works-routing].

### <a name="live-testing-an-open-pull-request"></a>Nyitott lekéréses kérelem élő tesztelése

A GitHub-műveleteket az Azure dev Spaces használatával is használhatja, ha az egyesítés előtt közvetlenül a fürtben szeretné tesztelni az alkalmazás módosításait egy lekéréses kérelemben. Az Azure dev Spaces szolgáltatás automatikusan üzembe helyezheti az alkalmazás felülvizsgálati verzióját a fürtön, így a szerző és az egyéb csapattagok is áttekinthetik a módosításokat a teljes alkalmazás környezetében. Az Azure dev Spaces útválasztási képességeinek használatával az alkalmazás felülvizsgálati verziója a fürtön is üzembe helyezhető anélkül, hogy az hatással lenne a többi fejlesztői területre. Az összes funkció lehetővé teszi a pull-kérelmek magabiztos jóváhagyását és egyesítését. A GitHub-műveletekre és az Azure dev Spaces-re vonatkozó példa: [GitHub-műveletek & Azure Kubernetes Service][pr-flow].

## <a name="next-steps"></a>További lépések

A helyi fejlesztési gép AK-fürthöz való csatlakoztatásának megkezdéséhez lásd: [a fejlesztői gép csatlakoztatása egy AK-fürthöz][connect].

Az Azure dev Spaces for Team Development használatának megkezdéséhez tekintse meg a [csapat fejlesztését az Azure dev Spaces][quickstart-team] rövid útmutatójában.

[connect]: how-to/connect.md
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[routing]: #team-development