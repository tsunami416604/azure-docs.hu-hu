---
title: Azure Blockchain tokenek fiókkezelése
description: Az Azure Blockchain Tokens fiókkezelés használatával csoportokat hozhat létre, és összekapcsolhatja a blokklánc-fiókokat a blokklánc-műveletekhez való hozzáférés szabályozásához.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74326101"
---
# <a name="azure-blockchain-tokens-account-management"></a>Azure Blockchain tokenek fiókkezelése

[!INCLUDE [Preview note](./includes/preview.md)]

A blockchain-megoldás, a felhasználók különböző szintű hozzáférést igényelhetnek az Azure Blockchain tokenek szolgáltatással létrehozott jogkivonatokhoz. A legtöbb blokklánc-forgatókönyvben meg kell terveznie és telepítenie kell a főkönyvben található különböző blokklánc-fiókokat. A résztvevők közötti hozzáférést is kezelnie kell.Az Azure Blockchain Tokens fiókkezelés használatával csoportokat hozhat létre, és összekapcsolhatja a blokklánc-fiókokat a blokklánc-műveletekhez való hozzáférés szabályozásához.

## <a name="blockchain-networks"></a>Blockchain hálózatok

Az Azure Blockchain tokenek lehetővé teszi a tokenek üzembe helyezését és kezelését a blokklánc-hálózatok egy készletén keresztül. Egyetlen blokklánc-főkönyvet vagy több blokklánc-főkönyvet csatlakoztathat a szolgáltatáshoz.

## <a name="accounts"></a>Fiókok

Az Azure Blockchain tokenekhez kapcsolódó blockchain hálózatok esetében a szolgáltatás létrehozza és kezeli a privát-nyilvános kulcspárokat, és tranzakcióaláírást és -küldést hajt végre. Az Azure Blockchain tokenek is biztosít identitásleképezés, hogy megfeleljen a fiókok a nyilvános kulcs identitás a főkönyvben.

## <a name="groups"></a>Csoportok

A csoportok lehetővé teszik nagyszámú blokklánc-fiók kezelését a csatlakoztatott hálózatokon keresztül. Nyomon követheti és naplózhatja, hogy a címtárban lévő mely alkalmazások és felhasználók használhatják a fiókokat az Azure Blockchain tokenek API-kon keresztül. Csoportosíthatja például a fiókok készletét, amelyek különböző üzletágakat vagy különböző szerepköröket és a blokklánc-jogkivonatokhoz való hozzáférést képviselnek.

Egy csoportot is társíthat egy Azure Active Directory-felhasználóhoz vagy egyszerű szolgáltatáshoz, és ez a rendszernévi engedélyeket a csoporthoz és a kapcsolódó fiókokhoz.  

## <a name="next-steps"></a>További lépések

További információ az elérhető [Azure Blockchain tokenek sablonjairól.](templates.md)
