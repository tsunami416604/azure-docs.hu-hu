---
title: Az Azure Blockchain szolgáltatás fejlesztésének áttekintése
description: Bevezetés az Azure Blockchain szolgáltatás megoldásainak fejlesztéséről.
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348381"
---
# <a name="azure-blockchain-service-development-overview"></a>Az Azure Blockchain szolgáltatás fejlesztésének áttekintése

Az Azure Blockchain Szolgáltatással konzorciumi blokklánc-hálózatokat hozhat létre, amelyek lehetővé teszik az olyan vállalati forgatókönyveket, mint az eszközkövetés, a digitális token, a hűség és a jutalom, az ellátási lánc pénzügyi és eredete. A következő szakaszok bemutatják az Azure Blockchain szolgáltatás fejlesztését a vállalati blokklánc-megoldások megvalósításához.

## <a name="connecting-to-azure-blockchain-service"></a>Csatlakozás az Azure Blockchain szolgáltatáshoz

A blokklánc-hálózatokhoz különböző típusú ügyfelek léteznek, beleértve a teljes csomópontokat, a könnyű csomópontokat és a távoli ügyfeleket. Az Azure Blockchain Service egy blokklánc-hálózatot hoz létre, amely csomópontokat tartalmaz. Használhatja a különböző ügyfelek, mint az átjáró az Azure Blockchain szolgáltatás blockchain fejlesztéshez. Az Azure Blockchain Service alapfokú hitelesítést vagy hozzáférési kulcsot kínál fejlesztési végpontként. A következő népszerű ügyfelek segítségével csatlakozni.

### <a name="visual-studio-code"></a>Visual Studio Code

Az Azure Blockchain Development Kit Visual Studio Code bővítmény használatával csatlakozhat a konzorciumi tagokhoz. Miután csatlakozott egy konzorciumhoz, intelligens szerződéseket fordíthat, hozhat létre és helyezhet üzembe egy Azure Blockchain Service-konzorciumtagnak.

A kifinomult vállalati blokklánc-megoldások kifejlesztéséhez fejlesztési keretrendszerre van szükség a különböző blokklánc-hálózatokhoz való csatlakozáshoz és az intelligens szerződési életciklusok kezeléséhez. A legtöbb projekt legalább két blokklánc-csomóponton lép kapcsolatba. A fejlesztők helyi blokkláncot használnak a fejlesztés során. Amikor az alkalmazás készen áll a tesztelésre vagy kiadásra, a fejlesztő telepíti a blokklánc-hálózatra. Például a fő nyilvános Ethereum hálózat vagy az Azure Blockchain szolgáltatás. Az Azure Blockchain Development Kit for Ethereum bővítmény a Visual Studio Code szarvasgombát használ. A Truffle egy népszerű blokklánc-fejlesztési keretrendszer, amely decentralizált alkalmazásokat ír, fordít, telepít és tesz le az Ethereum blokkláncokon. Azt is gondolom, szarvasgomba, mint egy keret, amely megpróbálja zökkenőmentesen integrálni intelligens szerződés fejlesztés és a hagyományos webfejlesztés.

További információ: [Rövid útmutató: A Visual Studio-kód használatával csatlakozhat egy Azure Blockchain Service konzorciumi hálózathoz.](connect-vscode.md)

### <a name="metamask"></a>Metamaszk

A MetaMask egy böngészőalapú pénztárca (távoli ügyfél), RPC-ügyfél és alapszintű szerződéskezelő. Eltérően más böngésző pénztárca, MetaMask injektál egy web3 példányt a böngésző JavaScript környezetben, eljáró RPC kliens, amely csatlakozik a különböző Ethereum blockchains (*mainnet*, *Ropsten testnet*, *Kovan testnet*, helyi RPC csomópont, stb.) Beállíthatja az egyéni RPC egyszerűen csatlakozhat az Azure Blockchain Szolgáltatáshoz, és elkezdheti a blokklánc-fejlesztést a Remix használatával.

További információ: [Rövid útmutató: Intelligens egyezmény csatlakoztatása és üzembe helyezése metamaszk használatával](connect-metamask.md)

### <a name="geth"></a>Geth (geth)

Geth a parancssori felület futtatásához a teljes Ethereum csomópont végre Go. Nem kell futtatnia a teljes csomópontot, de elindíthatja az interaktív konzolt, amely javascript-alapú környezetet biztosít, amely javascript API-t tesz ki az Azure Blockchain szolgáltatással való együttműködéshez.

További információ: [Rövid útmutató: Geth használata az Azure Blockchain szolgáltatás tranzakciós csomóponthoz való csatolásához.](connect-geth.md)

## <a name="ethereum-quorum-private-transactions"></a>Ethereum Quorum magántranzakciók

A Quorum egy Ethereum-alapú elosztott főkönyvi protokoll tranzakcióval, valamint szerződési adatvédelemmel és új konszenzusos mechanizmusokkal. A Go-Ethereum legfontosabb fejlesztései a következők:

* **Adatvédelem** – A Quorum támogatja a magántranzakciókat és a magánszerződéseket a nyilvános és a magánállam szétválasztása révén, és peer-to-peer titkosított üzenetváltásokat használ a személyes adatok nak a hálózat résztvevőinek történő irányított továbbításához.
* **Alternatív konszenzusos mechanizmusok** – az engedéllyel rendelkező hálózathoz nincs szükség a munka igazolására vagy a cegyek bizonyítására. A Quorum többféle konszenzusos mechanizmust kínál, amelyeket konzorciumi láncokhoz, például a RAFT-hoz és az IBFT-hez terveztek.Az Azure Blockchain Service az IBFT konszenzusmechanizmust használja.
* **Társengedély –** az intelligens szerződéseket használó csomópontok és társengedélyek biztosítják, hogy csak ismert felek csatlakozhassanak a hálózathoz.
* **Nagyobb teljesítmény** - A Quorum nagyobb teljesítményt nyújt, mint a nyilvános Geth.

## <a name="block-explorers"></a>Felfedezők blokkolása

A blokkkezelők olyan online blokklánc-böngészők, amelyek egyedi blokktartalmat, tranzakciós címadatokat és előzményeket jelenítenek meg. Az alapvető blokkinformációk az Azure Blockchain Szolgáltatás Ban érhető el az Azure Monitor on keresztül. Ha azonban további részletekre van szüksége a fejlesztés során, a blokkkezelők hasznosak lehetnek.  A következő blokkkezelők az Azure Blockchain Szolgáltatással dolgoznak:

* [Epirus Azure Blockchain Szolgáltatáskezelő](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) a Web3 Labs-től
* [BlockScout között](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

Saját blokkkezelőt is létrehozhat a Blockchain Data Manager és az Azure Cosmos DB használatával, [lásd: Oktatóanyag: A Blockchain Data Manager használatával adatokat küldhet az Azure Cosmos DB-nek.](data-manager-cosmosdb.md)

## <a name="tps-measurement"></a>TPS mérés

Mivel a blokkláncot több vállalati forgatókönyvben használják, a másodpercenkénti tranzakciók (TPS) sebessége fontos a szűk keresztmetszetek és a rendszer elégtelenségének elkerülése érdekében. A magas tranzakciós arányokat nehéz fenntartani egy decentralizált blokkláncon belül. A pontos TPS-mérést különböző tényezők befolyásolhatják, például a kiszolgálószál, a tranzakciós várólista mérete, a hálózati késés és a biztonság. Ha meg kell mérni TPS sebesség a fejlesztés során, egy népszerű nyílt forráskódú eszköz [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>További lépések

Próbálja ki az Azure Blockchain Development Kit for Ethereum használatával történő rövid útmutatót, amelyhez az Azure Blockchain Szolgáltatás konzorciumához csatolhat.

> [!div class="nextstepaction"]
> [Az Azure Blockchain szolgáltatáshoz való csatlakozás hoz a Visual Studio-kód dal](connect-vscode.md)