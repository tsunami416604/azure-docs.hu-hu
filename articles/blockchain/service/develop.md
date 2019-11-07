---
title: Az Azure Blockchain Service Development áttekintése
description: Bevezetés az Azure Blockchain Service-megoldások fejlesztéséhez.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/11/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: a9444847fb75bdf01cabba98057605afbe03c9fc
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577441"
---
# <a name="azure-blockchain-service-development-overview"></a>Az Azure Blockchain Service Development áttekintése

Az Azure Blockchain szolgáltatással konzorciumi Blockchain-hálózatokat hozhat létre, amelyek lehetővé teszik a nagyvállalati forgatókönyvek, például az eszközök nyomon követését, a digitális jogkivonatot, a lojalitást és a jutalmat, az ellátási lánc pénzügyi Ez a cikk az Azure Blockchain Service Development és a főbb témakörök bevezetését ismerteti a vállalati Blockchain megvalósításához.

## <a name="client-connection-to-azure-blockchain-service"></a>Ügyfél-kapcsolódás az Azure Blockchain szolgáltatáshoz

A blockchain-hálózatokhoz különböző típusú ügyfelek tartoznak, beleértve a teljes csomópontokat, a könnyű csomópontokat és a távoli ügyfeleket. Az Azure Blockchain szolgáltatás olyan Blockchain-hálózatot épít, amely csomópontokat tartalmaz. A Blockchain-fejlesztéshez különböző ügyfeleket használhat az Azure Blockchain Service-hez való átjáróként. Az Azure Blockchain szolgáltatás alapszintű hitelesítési vagy hozzáférési kulcsot biztosít fejlesztési végpontként. A következő népszerű ügyfeleket használhatja a csatlakozási lehetőséggel.

### <a name="metamask"></a>MetaMask

A MetaMask egy böngészőalapú mobiltárca (távoli ügyfél), RPC-ügyfél és alapszintű szerződés Explorer. A többi böngészőbeli pénztárcától eltérően a MetaMask egy web3-példányt helyez el a böngésző JavaScript-környezetében, amely egy olyan RPC-ügyfélként működik, amely különböző Ethereum-blokkláncok (*mainnet*, *Ropsten TestNet*, *Kovan TestNet*, helyi RPC-csomóponttal) csatlakozik. stb.). Az Azure Blockchain szolgáltatáshoz való kapcsolódáshoz egyszerűen beállíthatja az egyéni RPC-t, és megkezdheti a Blockchain fejlesztését a Remix használatával.

### <a name="geth"></a>Geth

A geth az a parancssori felület, amely a Go-ban implementált teljes Ethereum-csomópont futtatására szolgál. Nem kell teljes csomópontot futtatnia, de elindíthatja az interaktív konzolt, amely JavaScript-futtatókörnyezeti környezetet biztosít a JavaScript API-k számára az Azure Blockchain szolgáltatással való kommunikációhoz.

## <a name="development-framework-configuration"></a>Fejlesztési keretrendszer konfigurációja

A kifinomult vállalati blockchain megoldások fejlesztése érdekében fejlesztési keretrendszerre van szükség a különböző blockchain-hálózatokhoz való kapcsolódáshoz, az intelligens szerződések életciklusának kezeléséhez, a tesztelés automatizálásához, az intelligens szerződés parancsfájlokkal való üzembe helyezéséhez és egy interaktív konzol előkészítéséhez.

A szarvasgomba népszerű blockchain-fejlesztési keretrendszer a decentralizált alkalmazások Ethereum blokkláncok történő írásához, fordításához, üzembe helyezéséhez és teszteléséhez. Azt is meggondolhatja, hogy a szarvasgomba olyan keretrendszer, amellyel zökkenőmentesen integrálható az intelligens szerződések fejlesztése és a hagyományos webes fejlesztés.

Még a legkisebb projekt is kommunikál legalább két blockchain-csomóponttal: egyet a fejlesztői gépén, a másik pedig azt a hálózatot jelképezi, ahol a fejlesztő üzembe helyezi az alkalmazást. Például a fő nyilvános Ethereum hálózat vagy az Azure Blockchain szolgáltatás. A szarvasgomba egy rendszert biztosít az egyes hálózatok fordítási és üzembe helyezési összetevőinek kezeléséhez, és így egyszerűsíti a végső alkalmazás üzembe helyezését. További információ: gyors útmutató [: a szarvasgomba használata egy Azure Blockchain Service networkhez való kapcsolódáshoz](connect-truffle.md).

## <a name="ethereum-quorum-private-transaction"></a>Ethereum kvórum privát tranzakciója

A kvórum egy Ethereum elosztott Főkönyv protokoll, amely tranzakciós plusz szerződéses adatvédelmet és új konszenzusi mechanizmusokat is alkalmaz. A go-Ethereum többek között a következők:

* Adatvédelem – a kvórum az állami és a magánjellegű állapot elkülönítésével támogatja a privát tranzakciókat és a privát szerződéseket, és a személyes adatoknak a hálózati résztvevők felé irányított továbbítására szolgáló, társ-társ titkosítású üzenetváltást használ.
* Alternatív Konszenzusi mechanizmusok – az engedéllyel rendelkező hálózatokban nem szükséges a munkahelyi vagy a bizonyítékokkal kapcsolatos konszenzus. A kvórum több konszenzusos mechanizmust kínál, amelyek olyan konzorciumi láncok számára lettek kialakítva, mint például a TUTAJ és a IBFT.  Az Azure Blockchain Services a IBFT konszenzusi mechanizmust használja.

* Társ-jogosultság – a csomópont-és társ-jogosultságok intelligens szerződések használatával, csak az ismert felek csatlakozhatnak a hálózathoz
* Nagyobb teljesítmény – a kvórum nagyobb teljesítményt nyújt, mint a nyilvános geth

Tekintse meg a következő [oktatóanyagot: tranzakció küldése az Azure Blockchain Service](send-transaction.md) -be a privát tranzakciók példája alapján.

## <a name="block-explorers"></a>Felfedezők letiltása

A blokkoló felfedezők olyan online blockchain böngészők, amelyek egyedi blokk tartalmakat, tranzakciós címeket és előzményeket jelenítenek meg. Az alapszintű blokkolási információk az Azure Blockchain szolgáltatásban Azure Monitor keresztül érhetők el, ha azonban részletesebb információra van szüksége a fejlesztés során, a blokkoló felfedezők hasznosak lehetnek.  Népszerű, nyílt forráskódú blokk-kezelők is használhatók. Az alábbi lista az Azure Blockchain szolgáltatással használható blokk-tallózókat tartalmazza:

* [Empirusz Azure Blockchain Service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) a Web3 Labs-ből
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>TPS mérése

Mivel a blockchain több vállalati forgatókönyvben is használatos, a másodpercenkénti tranzakciók (TPS) sebessége fontos a szűk keresztmetszetek és a rendszer elégtelenségének elkerülése érdekében. A magas tranzakciós sebességet nehéz fenntartani egy decentralizált blockchain belül. A pontos TPS-mérést befolyásolhatja különböző tényezők, például a kiszolgálói szál, a tranzakciós várólista mérete, a hálózati késés és a biztonság. Ha a fejlesztés során mérni kell a TPS sebességet, egy népszerű, nyílt forráskódú eszköz is [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>További lépések

[Rövid útmutató: a szarvasgomba használata az Azure Blockchain Service networkhez való kapcsolódáshoz](connect-truffle.md)
