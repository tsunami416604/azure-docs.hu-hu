---
title: Azure Blockchain tokenek – Fiókkezelés
description: Az Azure Blockchain-tokenek Fiókkezelés használatával csoportokat hozhat létre, és Blockchain-fiókokat csatolhat a Blockchain-műveletekhez való hozzáférés szabályozásához.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326101"
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

## <a name="next-steps"></a>Következő lépések

További információ az elérhető [Azure Blockchain-jogkivonat-sablonokról](templates.md).
