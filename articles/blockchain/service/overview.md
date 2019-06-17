---
title: Az Azure Blockchain-szolgáltatás áttekintése
description: Az Azure Blockchain-szolgáltatás áttekintése
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: e370916d420a7bc4cd16d021c69a2f8609093d30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544708"
---
# <a name="what-is-azure-blockchain-service"></a>Mi az Azure Blockchain-szolgáltatás?

Az Azure Blockchain-szolgáltatás teljes körűen felügyelt Főkönyv szolgáltatása lehetővé teszi, hogy a felhasználók növekszik, és nagy mennyiségű Azure-beli blockchain hálózatok működtetéséhez. Azáltal, hogy egységes vezérlő az infrastruktúra-felügyelettel és is blockchain hálózati cégirányítási, az Azure Blockchain-szolgáltatás biztosítja:

* Egyszerű hálózat telepítési és műveleti
* Beépített konzorciumkezelés
* Az intelligens szerződések ismerős fejlesztői eszközökkel fejleszthet.

Az Azure Blockchain-szolgáltatás célja több Főkönyv protokoll támogatása. Jelenleg az Ethereum támogatást biztosít [kvórum](https://www.jpmorgan.com/Quorum) Főkönyv használatával a [IBFT](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus) konszenzus mechanizmus.

Ezen képességek szinte semmilyen felügyeletet nem igényelnek, és mindegyik további költség nélkül érhető el. Alkalmazások fejlesztése és üzleti logikát helyett időt és erőforrásokat a virtuális gépek és infrastruktúra kezeléséhez lefoglalnia összpontosíthat. Ezenkívül folytathatja az alkalmazás fejlesztését a nyílt forráskódú eszközök és a megoldásokat nyújtsanak az új ismeretek elsajátítása nélkül a választott platformnak.

## <a name="network-deployment-and-operations"></a>Hálózati üzembe helyezése és műveletek

Az Azure Blockchain-szolgáltatás telepítéséhez elvégezhető az Azure Portalon, az Azure CLI, valamint az Azure Blockchain-bővítmény használata a Visual Studio code használatával.  Üzembe helyezési egyszerűsített, beleértve a tranzakciók és az érvényesítési csomópontok, az Azure virtuális hálózatok biztonsági elkülönítés, valamint a szolgáltatás által kezelt tárolási kiépítése.  Emellett új blockchain tag üzembe helyezésekor, felhasználók is létrehozhat, vagy csatlakozzon, konzorcium.  Consortiums tudják biztonságosan kommunikálhatnak egymással egy megosztott blockchain az Azure-előfizetések több felek engedélyezése.  Ez egyszerűbb környezet csökkenti a blockchain hálózati telepítési nap percre.

### <a name="performance-and-service-tiers"></a>Teljesítmény- és szolgáltatási szintek

Az Azure Blockchain-szolgáltatás két szolgáltatásszintet nyújt: *Alapszintű* és *Standard*. Az egyes csomagok kínál a különböző teljesítményt és képességeket támogatja a fejlesztési és tesztelési feladatait akár nagy mértékben horizontálisan éles környezetben blockchain üzembe helyezéséhez. Mindkét csomagban elérhető legalább egy-egy tranzakció csomópontot, és a egy érvényesítő csomópont (alapszintű) vagy a két érvényesítő csomópont (Standard).

![Árképzési szintek](./media/overview/pricing-tiers.png)

Mellett érvényesítő két csomópontot tartalmaz, amely a *Standard* kínál a 2 *virtuális magok* minden tranzakciós és érvényesítő csomópont esetében, mivel az alapszintű csomag egy 1 virtuális mag konfigurációs kínálja.  Azzal tranzakciós és érvényesítő csomópontok 2 virtuális mag, 1 virtuális mag dedikálhatja a kvórum Főkönyv közben a fennmaradó 1 virtuális mag más infrastruktúra-hez kapcsolódó szolgáltatások, éles környezetben az optimális teljesítmény biztosítása a blockchain-feladatok használhatók. A szolgáltatás díjszabását további információkért lásd: [Azure Blockchain-szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Biztonság és karbantartás

Üzembe helyezés az első blockchain-tag, után, hogy lehetővé teszi, hogy a tag, további tranzakciós csomópontok.  Alapértelmezés szerint a tranzakció csomópontok tűzfalszabályok keresztül biztosított, és hozzáférést konfigurálni kell.  Ezenkívül az összes tranzakció csomópontok titkosítása a TLS-n keresztül mozgásban lévő adatok.  Több lehetőség is létezik tranzakció csomópont eléréséhez, beleértve a tűzfalszabályok, alapszintű hitelesítés, hozzáférési kulcsokat, valamint az Azure Active Directory-integráció biztosítása. További információkért lásd: [tranzakció csomópontok konfigurálása](configure-transaction-nodes.md) és [konfigurálása Azure Active Directory hozzáférési](configure-aad.md).

Felügyelt szolgáltatásként az Azure Blockchain-szolgáltatás biztosítja, hogy a blockchain tagcsomópontok telepítve van a legújabb gazdagép operációs rendszer és a tranzakciónapló stack szoftverfrissítések, magas rendelkezésre állású (csak Standard szintű) konfigurált, így nem nagy része a fejlesztési és üzemeltetési hagyományos IaaS blockchain-csomópontok számára szükséges.  A javításokat és frissítéseket további információkért lásd: [támogatott az Azure Blockchain-szolgáltatás Főkönyv verziók](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Monitorozás és naplózás

Emellett az Azure Blockchain-szolgáltatás mérőszámait gazdag Azure Monitor szolgáltatáson keresztül csomópontok CPU, memória és tárhelyhasználat elemzések, valamint a tranzakciók és blokkok célú adatbányászatra sem használja, például blokklánc-hálózati aktivitás hasznos betekintést nyújtó tranzakció várólistamélységének, valamint az aktív kapcsolatok.  Metrikák testre szabható nézeteket biztosít az fontos a blockchain-application insights-bA.  Küszöbértékek emellett riasztásokat kiváltó műveleteket, például egy e-mailben vagy szöveges üzenet küldése, egy logikai alkalmazást, az Azure-függvényt futtató vagy egy egyénileg definiált webhook küldése felhasználók keresztül lehet definiálni.

![Mérőszámok](./media/overview/metrics.png)

Azure Log Analytics segítségével a felhasználók megtekinthetik a kvórum Főkönyv, vagy más fontos információkat, például próbált kapcsolatot a tranzakció csomópontok kapcsolatos naplókat.

## <a name="built-in-consortium-management"></a>Beépített konzorciumkezelés

Az első blockchain tag telepítésekor, vagy csatlakozzon, vagy hozzon létre egy új consortium.  Konzorcium egy olyan logikai csoport kezelhetők a cégirányítási és blockchain tagjai, akik több résztvevős folyamat transact közötti kapcsolatot.  Az Azure Blockchain-szolgáltatás biztosít beépített cégirányítási képernyőolvasókkal előre definiált intelligens szerződések, amelyek meghatározzák, hogy mi a consortium műveletek tagjait is igénybe vehet.  Cégirányítási a vezérlőelemek a consortium rendszergazdája szükség szerint testre szabható. Amikor létrehoz egy új consortium, a blockchain tag az alapértelmezett rendszergazdai konzorcium, így lehetővé teszi a consortium csatlakozni más felek meghívása.  Csak akkor, ha korábban meghívót kapott a konzorcium csatlakozhat.  Konzorcium csatlakoztatáskor a blockchain tagja a cégirányítási ellenőrzések bevezetni a consortium rendszergazdája.

![Consortium kezelése](./media/overview/consortium.png)

Consortium felügyeleti műveleteket, például hozzáadása és eltávolítása a tagok konzorcium a PowerShell és a egy REST API használatával érhetők el. Általános illesztők használatával helyett módosításával és elküldése az intelligens szerződések solidity-alapú konzorcium programozott módon kezelheti. További információkért lásd: [consortium felügyeleti](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Fejlesztés az ismerős fejlesztői eszközökkel

A nyílt forráskódú kvórum Ethereum főkönyv alapján, fejleszthet alkalmazásokat az Azure Blockchain-szolgáltatás ugyanúgy meglévő Ethereum-alkalmazásokat is. Vezető iparági partnerek dolgozik, az Azure Blockchain Development Kit Visual Studio Code-bővítmény lehetővé teszi a fejlesztők így hasznosíthatják a jól ismert eszközökkel, mint például a smart Contract-Truffle Suite. Az Azure Blockchain Development Kit bővítményt használja, fejlesztők létrehozhat, vagy csatlakozhat és a meglévő consortium, hogy megbízható alkalmazásfejlesztést és üzembe helyezése az intelligens szerződések összes egy IDE-ből. Az Azure Blockchain Visual Studio Code-bővítmény használata, hozzon létre vagy egy meglévő consortium kapcsolódni, így létrehozhat és üzembe helyezése az intelligens szerződések összes egy IDE-ből. További információkért lásd: [Azure Blockchain Development Kit a VS Code piactéren](https://aka.ms/vscodebcextension) és a [felhasználói útmutató az Azure Blockchain Development Kit](https://aka.ms/vscodebcextensionwiki ).

## <a name="support-and-feedback"></a>Támogatás és visszajelzés

Súgó vagy visszajelzést szeretne küldeni?

* Látogasson el a [Azure Blockchain blog](https://azure.microsoft.com/blog/topics/blockchain/), [a Microsoft technikai Közösség](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain), és [Azure Blockchain-fórum](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain).
* Ha visszajelzést szeretne küldeni vagy új szolgáltatásokat kérne, hozzon létre egy bejegyzést a [UserVoice-on](https://feedback.azure.com/forums/921130-azure-blockchain-service).

## <a name="next-steps"></a>További lépések

Első lépésként próbálja meg egy rövid, vagy hogy további részleteket az ezekhez az erőforrásokhoz.
* [Hozzon létre az Azure portal használatával blockchain tag](create-member.md) vagy [Azure CLI-vel blockchain tag létrehozása](create-member-cli.md)
* Költségeinek összehasonlításáért és árkalkulációjáért, lásd: a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/blockchain-service).
* Az első alkalmazás használatával az [Azure Blockchain szoftverfejlesztői készlet](https://github.com/Azure-Samples/blockchain-devkit)
* Az Azure Blockchain VSCode-bővítmény [felhasználói útmutató](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)
