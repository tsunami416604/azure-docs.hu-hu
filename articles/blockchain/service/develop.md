---
title: Az Azure Blockchain-szolgáltatás fejlesztői áttekintés
description: Bevezetés az Azure Blockchain-szolgáltatások-megoldások fejlesztése.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 388a5d8c80c3e2462602959e9d5cbc1452974d1f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027900"
---
# <a name="azure-blockchain-service-development-overview"></a>Az Azure Blockchain-szolgáltatás fejlesztői áttekintés

Az Azure Blockchain-szolgáltatás consortium ahhoz enterprise helyzetek, például az eszköz követési, digitális jogkivonatot, hűségprogramok használatán keresztül és a fejében, pénzügyi ellátási lánc és származási hely blockchain hálózatot hozhat létre. Ez a cikk az Azure Blockchain-szolgáltatás fejlesztői áttekintés és megvalósításához a blockchain a vállalat számára a legfontosabb témakörök bevezetést.

## <a name="client-connection-to-azure-blockchain-service"></a>Az Azure Blockchain Service-Ügyfélkapcsolat

Nincsenek különböző típusú ügyfelek teljes csomópontokat, a könnyű csomópontok és a távoli ügyfelek például blokklánc hálózatok. Az Azure Blockchain-szolgáltatás, amely tartalmazza a csomópontok blockchain hálózati épít fel. Különböző ügyfelek a blokklánc-fejlesztési használhatja az Azure Blockchain-szolgáltatás átjáró. Az Azure Blockchain-szolgáltatás fejlesztési-végpontként kínál az alapszintű hitelesítés vagy az elérési kulcs. Az alábbiakban használhatja népszerű ügyfelek csatlakozni.

### <a name="metamask"></a>MetaMask

MetaMask egy böngészőalapú wallet (távoli ügyfél), az RPC-ügyfél és az alapszintű szerződés explorer. Ellentétben más böngészőt levéltárcák MetaMask kódtárba a weben 3-példány a böngészőben JavaScript-környezet be, amely csatlakozik a különböző Ethereum blokkláncok RPC-ügyfélként működő (*mainnet*, *Ropsten testnet*, *Kovan testnet*, helyi RPC-csomópont stb.). Beállíthat egyéni RPC egyszerűen való csatlakozáshoz, az Azure Blockchain-szolgáltatás, és indítsa el a blokklánc-fejlesztési Remix használatával.

### <a name="geth"></a>Geth

Geth a parancssori felület implementálva, lépjen teljes Ethereum csomópont futtatásához. Nem kell futtatnia csomópontot teljes, de el is indíthatja az interaktív konzol is közzéteheti az Azure Blockchain-szolgáltatással való kommunikációhoz JavaScript API a JavaScript futtatási környezetet biztosít.

## <a name="development-framework-configuration"></a>Fejlesztési keretrendszer konfigurációja

A kifinomult vállalati blockchain-megoldások fejlesztése, különböző blockchain hálózatokhoz, intelligens szerződés életciklus kezelése, automatizálhatja a tesztelés, üzembe helyezése parancsfájlok intelligens szerződés és egy interaktív konzolt ellátására egy fejlesztési keretrendszer van szükség.

Truffle egy olyan népszerű blockchain fejlesztési keretrendszer, írása, összeállítása, üzembe helyezését és Ethereum blokkláncok decentralizált alkalmazások teszteléséhez. Akkor is felfogható Truffle próbál zökkenőmentesen integrálhat intelligens szerződés fejlesztési és a hagyományos webes fejlesztési keretrendszer.

Még a legkisebb projekt blockchain legalább két csomóponttal kommunikál: Egy, a fejlesztői gépen, és a más jelölő a hálózat, ahol a fejlesztő helyez üzembe alkalmazását. Például a fő nyilvános Ethereum hálózati vagy az Azure Blockchain-szolgáltatás. Truffle biztosít a rendszer minden hálózat összeállítása és üzembe helyezési összetevők kezeléséhez, és úgy valósítja meg úgy, hogy leegyszerűsíti a végső alkalmazás központi telepítését. További információkért lásd: [a rövid útmutató: Truffle használatával csatlakozni egy az Azure Blockchain-szolgáltatás hálózati](connect-truffle.md).

## <a name="ethereum-quorum-private-transaction"></a>Privát Ethereum kvórum-tranzakció

Kvórum nem tranzakciós plus szerződés adatvédelmi és új konszenzus mechanizmusok elosztott Főkönyv Ethereum-alapú protokoll. Go-Ethereum keresztül kulcsfontosságú fejlesztések közé tartozik:

* Adatvédelem – kvórum támogatja a privát tranzakciókat és a privát szerződések keresztül a nyilvános és privát állapot elkülönítése, és már használja a társ-társ titkosított üzenetváltásokban a személyes adatokat a hálózati résztvevők irányított átviteléhez.
* Alternatív konszenzus mechanizmusok - és a koncepció-az-munkahelyi vagy a koncepció-az-kockán caiq permissioned hálózatban nincs szükség. Kvórum több caiq mechanizmust kínálja, amelyek esetében például egyáltalán és IBFT consortium láncok.  Azure Blockchain-szolgáltatások a IBFT konszenzus mechanizmust használ.

* Társviszonyt csatornákban - csomópont és a társ csatornákban intelligens szerződések, csak az ismert felek biztosító használatával csatlakozhat a hálózaton
* Nagyobb teljesítmény - kvórum kínál, mint a nyilvános Geth nagyobb teljesítményt

Lásd: [oktatóanyag: Azure Blockchain-szolgáltatás használatával tranzakció küldése](send-transaction.md) privát tranzakció egy példát.

## <a name="block-explorers"></a>Blokk-tallózók

Blokk szoftverkategóriák olyan online blockchain böngészők, amelyek megjelenítik a tartalom egyedi blokkolása, tranzakció cím adatokat és előzményeket. Alapszintű letiltása az információ az Azure Blockchain-szolgáltatás az Azure monitoron keresztül érhető el, azonban, ha részletesebb tájékoztatást a fejlesztés során, blokk szoftverkategóriák hasznos lehet.  Számos népszerű nyílt forráskódú blokk tallózók is használhatja. A következő egy blokk tallózók, amelyek együttműködnek az Azure Blockchain-szolgáltatás listája:

* [Az Azure Blockchain-szolgáltatás Explorer](https://web3labs.com/azure-offer) a weben 3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>TPS mérési

Blockchain további nagyvállalati forgatókönyvekben használt, második (TPS) sebességű tranzakció fontos szűk keresztmetszetei és a rendszer hatékonysági elkerülése érdekében. Magas tranzakciós sebességet is nehéz lehet fenntartani egy decentralizált blockchain belül. Egy pontos TPS mérési hatással lehet más tényező befolyásolja, például a kiszolgáló szál, a tranzakciós várólista mérete, a hálózati késés és a biztonság. TPS sebesség mérése a fejlesztés során van szüksége, ha egy népszerű, nyílt forráskódú eszköz van [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>További lépések

[Rövid útmutató: Truffle használatával csatlakozni egy az Azure Blockchain-szolgáltatás hálózat](connect-truffle.md)
