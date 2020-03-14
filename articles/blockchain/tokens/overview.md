---
title: Mi az Azure Blockchain-tokenek?
description: Az Azure Blockchain-tokenek a tokenek kiállításához és felügyeletéhez használható platformként szolgálnak.
ms.date: 11/04/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: e7c106ffee8ea06c7c0ce738aa8be0aba96a38ed
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240281"
---
# <a name="what-is-azure-blockchain-tokens"></a>Mi az Azure Blockchain Tokens?

[!INCLUDE [Preview note](./includes/preview.md)]

Az Azure Blockchain-tokenek az Azure-ban szabványosított jogkivonatok kiállítására és felügyeletére szolgáló platform (Blockchain).

Az Azure Blockchain-jogkivonatok használatával szabványosított jogkivonatokat hozhat létre a Blockchain-megoldáshoz egy előre elkészített jogkivonat-sablon használatával. A szolgáltatás használatával saját jogkivonat-sablont is létrehozhat. A létrehozást követően Azure Blockchain-tokenekkel csatlakozhat, és kibocsáthatja a jogkivonatokat egy Blockchain. A kiállítottak után a tokeneket több blockchain-hálózaton is kezelheti.

## <a name="templates"></a>Sablonok

Az Azure Blockchain-tokenek használatával kiválaszthat egy előre elkészített jogkivonat-sablont, vagy létrehozhat egy saját jogkivonat-sablont. Az Azure Blockchain-tokenek támogatják a jogkivonat-sablonok megírását, amely lehetővé teszi saját jogkivonat-sablon létrehozását a támogatott viselkedések alapján. A token-sablonok a legtöbb blockchain-megoldáshoz használhatók, mivel azok a leggyakrabban használt jogkivonatokra vannak leképezve. Elindíthat egy sablont, személyre szabhatja, és üzembe helyezheti a megoldás jogkivonatait.

Az Azure Blockchain tokens-sablonokkal kapcsolatos további információkért lásd: [Azure Blockchain tokens sablonok](templates.md).

## <a name="management"></a>Kezelés

Az Azure Blockchain-tokenek Azure Portal felügyeletet és API-kat biztosítanak egy meglévő Blockchain-hálózathoz való kapcsolódáshoz. Jelenleg kapcsolódhat az [Azure Blockchain szolgáltatáshoz](../service/overview.md) vagy egy másik Ethereum Family Blockchain.

Ha egy vagy több blockchain-hálózathoz csatlakozik, az Azure Blockchain tokens API-k használatával kiállíthatja és kezelheti a blockchain-megoldásban használható jogkivonatokat. Az API-k használatával integrálhatja a jogkivonat-kezelést az üzleti alkalmazásaiban és a logikában. A jogkivonatok kezeléséhez például használhatja a REST API a jogkivonatok kezelése helyett közvetlenül a blockchain.

## <a name="blockchains-and-accounts"></a>Blokkláncok és fiókok

Az Azure Blockchain-tokenek Azure Portal felügyeletet és API-kat biztosítanak új csoportok és új Blockchain-fiókok létrehozásához a csatlakoztatott Blockchain-hálózatokon. Új fiókokat közvetlenül a csatlakoztatott hálózatokon hozhat létre, és az Azure Blockchain-tokenek az Ön nevében kezelik a fiók titkos kulcsait. A csoportok használatával különböző blockchain-fiókokat csoportosíthat több hálózatból, és a csoportokon keresztül kezelheti a hozzáférés-vezérlést.

Az Azure Blockchain tokenek Fiókkezelés szolgáltatásával kapcsolatos további információkért lásd az [Azure Blockchain tokenek felügyelete](account-management.md)című témakört.

## <a name="token-taxonomy-framework"></a>Jogkivonat-besorolási keretrendszer

Az Azure Blockchain-tokenek a jogkivonat-taxonómiai keretrendszer (TTF) nevű szabványon alapuló alapra épülnek. A TTF a [jogkivonat-taxonómiai kezdeményezés](https://entethalliance.org/participate/token-taxonomy-initiative/) (tti) token munkacsoportból létrehozott végtermékek összessége. A TTI Working Group a tokenek és az azokhoz tartozó viselkedések üzleti besorolását határozza meg, amelyek az összes jelentősebb főkönyvre alkalmazhatók, beleértve a Ethereum, a kvórum, a Corda és a Hyperledger-hálót is. A munkacsoport célja, hogy olyan keretrendszert hozzon létre, amely szabványosítja a jogkivonatok üzleti szempontból való használatát, így egyszerűsítve és demokratizálni a jogkivonat-alapú fejlesztést. Azáltal, hogy az iparág definiálja ezeket a jogkivonatokat és azok viselkedését az üzleti szinten, a jogkivonatok részletes megvalósítása a jogkivonatokat kezelő üzleti logikától el lesz távolítva.

## <a name="support-and-feedback"></a>Támogatás és visszajelzés

Az Azure Blockchain híreket az Azure [Blockchain blogon](https://azure.microsoft.com/blog/topics/blockchain/) tekintheti meg, ahol naprakészen tarthatja a Blockchain szolgáltatás ajánlatait és az Azure Blockchain Engineering csapata információit.

Az Azure Blockchain-tokenek előzetes verziójának eléréséhez [forduljon az Azure Blockchain tokens csapatához](https://aka.ms/PreviewForm).

Ha visszajelzést szeretne küldeni a termékről, vagy új funkciókat szeretne kérni, szavazzon a [blockchain készült Azure visszajelzési fórumán](https://aka.ms/blockchainuservoice)keresztül.

## <a name="next-steps"></a>Következő lépések

További információ az elérhető [Azure Blockchain-jogkivonat-sablonokról](templates.md).
