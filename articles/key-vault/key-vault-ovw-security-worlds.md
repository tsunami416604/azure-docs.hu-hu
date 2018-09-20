---
ms.assetid: ''
title: Az Azure Key Vault biztonsági világai |} A Microsoft Docs
ms.service: key-vault
ms.topic: conceptual
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 13470e85e4fe2741a11fcade3b97d333eb03efb7
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465900"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Az Azure Key Vault biztonsági világok és földrajzi korlátok

Az Azure Key Vault hardveres biztonsági modulokban (HSM) álló készletet használ minden egyes Azure-beli helyen, és a egy több-bérlős szolgáltatás. 

Minden HSM-jeiről ugyanabban a földrajzi régióban található Azure-helyen ossza meg a ugyanolyan titkosítási határ (Thales Biztonságivilág). Például USA keleti RÉGIÓJA és USA nyugati RÉGIÓJA megoszthatja az ugyanazon biztonsági világ mert az Egyesült Államok földrajzi hely tartoznak. Ehhez hasonlóan az összes Azure-helyen a japán ossza meg az azonos biztonsági világához és Ausztrália, India és így tovább az összes Azure-helyen. 

## <a name="backup-and-restore-behavior"></a>Biztonsági mentési és visszaállítási viselkedés

A kulcsok a key vault egy Azure-beli helyen készült biztonsági másolatok visszaállíthatók, egy másik Azure-beli helyen, egy kulcstárolóba mindaddig, amíg az alábbi két feltétel teljesül:

- Mindkettő az Azure-helyen azonos földrajzi helyen tartozik
- A kulcstartók mindegyikét Azure-előfizetéshez tartozik

Például egy adott egy kulcsot a Nyugat-India, a key vault-előfizetés által készített biztonsági másolat csak állítható vissza egy másik kulcstartó ugyanahhoz az előfizetéshez és földrajzi helyet; Nyugat-India, közép-India és Dél-India.

## <a name="regions-and-products"></a>Régiók és termékek

- [Azure-régiók](https://azure.microsoft.com/regions/)
- [A Microsoft-termékek régiók szerint](https://azure.microsoft.com/regions/services/)

Biztonsági világok jelenik meg a táblázatok jelentős fejlécében régióban van leképezve:

A régió cikkben, például a termékek a **Dél-Amerika** lap tartalmaz a keleti NEKÜNK, USA KÖZÉPSŐ RÉGIÓJA, USA nyugati RÉGIÓJA Dél-Amerika régió összes hozzárendelést. 

>[!NOTE]
>Kivétel ez alól, az USA védelmi Minisztériuma – keleti RÉGIÓJA és USA védelmi Minisztériuma – KÖZÉPSŐ rendelkezik-e a saját biztonsági világot. 

Hasonlóképpen, az a **Európa** lapon, Észak-EURÓPÁBAN és Nyugat-EURÓPÁBAN egyaránt leképezése az Európa régiót. Ugyanez érvényes is az a **Ázsia Csendes-óceáni** fülre.



