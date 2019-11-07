---
title: Mi az Azure Blockchain-tokenek?
description: Az Azure Blockchain-tokenek a tokenek kiállításához és felügyeletéhez használható platformként szolgálnak.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: cd41d52e06a5c1833dca9669881cbe48f362d81d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579751"
---
# <a name="what-is-azure-blockchain-tokens"></a>Mi az Azure Blockchain-token?

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

## <a name="next-steps"></a>További lépések

További információ az elérhető [Azure Blockchain-jogkivonat-sablonokról](templates.md).
