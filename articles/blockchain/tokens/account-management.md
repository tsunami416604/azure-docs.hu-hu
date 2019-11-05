---
title: Azure Blockchain tokenek – Fiókkezelés
description: Az Azure Blockchain-tokenek Fiókkezelés használatával csoportokat hozhat létre, és Blockchain-fiókokat csatolhat a Blockchain-műveletekhez való hozzáférés szabályozásához.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: b396b359585c8865c3b08284f4398076f37c7a6e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512963"
---
# <a name="azure-blockchain-tokens-account-management"></a>Azure Blockchain tokenek – Fiókkezelés

[!INCLUDE [Preview note](./includes/preview.md)]

A blockchain-megoldások esetében előfordulhat, hogy a felhasználók eltérő szintű hozzáférést igényelnek az Azure Blockchain tokens szolgáltatással létrehozott jogkivonatokhoz. A legtöbb blockchain forgatókönyvben a főkönyvben meglévő különböző blockchain-fiókokat kell terveznie és telepítenie. Emellett a résztvevők közötti hozzáférés kezelése is szükséges. Az Azure Blockchain-tokenek Fiókkezelés használatával csoportokat hozhat létre, és Blockchain-fiókokat csatolhat a Blockchain-műveletekhez való hozzáférés szabályozásához.

## <a name="blockchain-networks"></a>Blockchain hálózatok

Az Azure Blockchain-tokenek lehetővé teszik a jogkivonatok üzembe helyezését és kezelését Blockchain-hálózatokon keresztül. Egyetlen blockchain vagy több blockchain-főkönyvet is összekapcsolhat a szolgáltatással.

## <a name="accounts"></a>Fiókok

Az Azure Blockchain-tokenekhez kapcsolódó blockchain-hálózatok esetében a szolgáltatás létrehozza és kezeli a fiók titkos kulcs párokat, és elvégzi a tranzakciók aláírását és küldését. Az Azure Blockchain-tokenek identitás-hozzárendelést is biztosítanak a főkönyvben lévő nyilvános kulcsú identitású fiókok egyeztetéséhez.

## <a name="groups"></a>Csoportok

A csoportok segítségével nagy mennyiségű blockchain-fiókot kezelhet a csatlakoztatott hálózatokon. Nyomon követheti és naplózhatja, hogy mely alkalmazások és felhasználók tudják használni a fiókokat az Azure Blockchain tokens API-kon keresztül. Például csoportosíthat olyan fiókokat, amelyek különböző üzletági vagy különböző szerepköröket képviselnek, és blockchain jogkivonatokhoz férnek hozzá.

A csoportokat társíthatja egy Azure Active Directory felhasználóhoz vagy egyszerű szolgáltatáshoz, és ez a rendszerbiztonsági tag jogosult a csoport és a hozzá tartozó fiókok hozzáférésére.  

## <a name="next-steps"></a>További lépések

További információ az elérhető [Azure Blockchain-jogkivonat-sablonokról](templates.md).
