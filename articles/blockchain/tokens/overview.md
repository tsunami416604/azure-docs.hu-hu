---
title: Mi az Azure Blockchain tokenek
description: Az Azure Blockchain tokenek egy platform, mint szolgáltatás (PaaS) a token-kiállítás és -kezelés.
ms.date: 11/04/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: e7c106ffee8ea06c7c0ce738aa8be0aba96a38ed
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240281"
---
# <a name="what-is-azure-blockchain-tokens"></a>Mi az Azure Blockchain Tokens?

[!INCLUDE [Preview note](./includes/preview.md)]

Az Azure Blockchain tokenek egy platform szolgáltatásként (PaaS) a szabványosított tokenkiállítás és -kezelés között blockchain főkönyvek az Azure-ban.

Az Azure Blockchain tokenek használatával szabványosított jogkivonatokat hozhat létre a blockchain-megoldáshoz egy előre elkészített jogkivonatsablon használatával. Saját jogkivonatsablont is írhat a szolgáltatás használatával. Miután létrehozta, azure blockchain tokenek segítségével csatlakozni, és kiadja a jogkivonatok egy blokkláncon. A kiadásután ezután kezelheti a jogkivonatokat több blokklánc-hálózaton keresztül.

## <a name="templates"></a>Sablonok

Azure Blockchain tokenek segítségével válasszon ki egy előre elkészített jogkivonat-sablont, vagy hozzon létre saját jogkivonatsablont. Az Azure Blockchain tokenek támogatja a tokensablon komozitással, amely lehetővé teszi, hogy saját jogkivonatsablont hozzon létre a támogatott viselkedések alapján. A tokensablonok a legtöbb blokklánc-megoldáshoz használhatók, mivel a leggyakrabban használt tokenek hez vannak leképezve. Kezdheti a sablont, személyre szabhatja, és telepítheti a jogkivonatokat a megoldáshoz.

Az Azure Blockchain tokenek sablonjairól az [Azure Blockchain tokenek sablonjairól az Azure Blockchain Tokens sablonok című témakörben olvashat bővebben.](templates.md)

## <a name="management"></a>Kezelés

Az Azure Blockchain tokenek azure portal felügyeleti és API-kat biztosít egy meglévő blokklánc-hálózathoz való csatlakozáshoz. Jelenleg csatlakozhat az [Azure Blockchain szolgáltatáshoz](../service/overview.md) vagy egy másik Ethereum családblokklánchoz.

Miután egy vagy több blokklánc-hálózathoz csatlakozott, az Azure Blockchain Tokenek API-k használatával kibocsáthatja és kezelheti a tokeneket a blockchain-megoldásban való használatra. Az API-k használatával integrálhatja a jogkivonat-kezelést az üzleti alkalmazásokba és a logikába. Például használhatja a REST API-t a jogkivonatok kezeléséhez ahelyett, hogy a tokenek közvetlenül a blokkláncon.

## <a name="blockchains-and-accounts"></a>Blokkláncok és fiókok

Az Azure Blockchain Tokens azure-portál-kezelést és API-kat biztosít új csoportok és új blokklánc-fiókok létrehozásához a csatlakoztatott blokklánc-hálózatokon. Új fiókokat hozhat létre közvetlenül a csatlakoztatott hálózatokon, és az Azure Blockchain tokenek kezeli a fiók személyes kulcsait az Ön nevében. A csoportok használatával csoportosíthatja a különböző blokklánc-fiókokat több hálózatról, és kezelheti a hozzáférés-vezérlést a csoportokon keresztül.

Az Azure Blockchain Tokens fiókkezelésével kapcsolatos további információkért lásd: [Azure Blockchain Tokens fiókkezelés.](account-management.md)

## <a name="token-taxonomy-framework"></a>Token taxonómiai keretrendszer

Az Azure Blockchain tokenek egy szabványalapú alapra épül, a Token Taxonómia-keretrendszerre (TTF). A TTF a [Token Taxonómia Initiative](https://entethalliance.org/participate/token-taxonomy-initiative/) (TTI) token munkacsoportból létrehozott deliverables készlet. A TTI munkacsoport meghatározza a tokenek és azok viselkedésének üzleti taxonómiája, amely az összes főfőkönyvben alkalmazható, beleértve az Ethereum, a Quorum, a Corda és a Hyperledger Fabric. A munkacsoport célja, hogy olyan keretrendszert hozzon létre, amely üzleti szempontból szabványosítja a tokenek használatát az egyszerűsítés és a tokenalapú fejlesztés demokratizálása érdekében. Azáltal, hogy az iparág határozza meg ezeket a jogkivonatokat és azok üzleti szintű viselkedését, a tokenek részletes megvalósítása elleszve a jogkivonatokat manipuláló üzleti logikától.

## <a name="support-and-feedback"></a>Támogatás és visszajelzés

Az Azure Blockchain hírekért látogasson el az [Azure Blockchain blogjára,](https://azure.microsoft.com/blog/topics/blockchain/) hogy naprakész legyen az Azure Blockchain mérnöki csapatának blockchain szolgáltatásajánlatairól és információiról.

Az Azure Blockchain Tokenek előzetes verziójának eléréséhez [lépjen kapcsolatba az Azure Blockchain Tokens csapatával.](https://aka.ms/PreviewForm)

A termékvisszajelzés biztosításához vagy új funkciók igényléséhez szavazzon egy ötletre az [Azure blockchain visszajelzési fórumán](https://aka.ms/blockchainuservoice)keresztül.

## <a name="next-steps"></a>További lépések

További információ az elérhető [Azure Blockchain tokenek sablonjairól.](templates.md)
