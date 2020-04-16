---
title: Azure Key Vault biztonsági világai | Microsoft dokumentumok
description: Az Azure Key Vault egy több-bérlős szolgáltatás. HSM-ek készletét használja minden Azure-helyen. Egy földrajzi régió minden helye egy kriptográfiai határvonalon osztozik.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 07/03/2017
ms.openlocfilehash: 3584f83c5e1a5e83d069373395227b70c084eae9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428952"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Az Azure Key Vault biztonsági világai és földrajzi határai

Az Azure Key Vault egy több-bérlős szolgáltatás, és hardveres biztonsági modulok (HSM-ek) készletét használja minden Azure-helyen. 

Az azonos földrajzi régióban lévő Azure-helyeken lévő összes HSM azonos kriptográfiai határral rendelkezik (Thales Security World). Például az USA keleti és nyugati része ugyanazt a biztonsági világot használja, mivel az USA földrajzi helyéhez tartoznak. Hasonlóképpen, a japán Azure-helyek ugyanazt a biztonsági világot és az összes Azure-helyet Ausztráliában, Indiában és így tovább. 

## <a name="backup-and-restore-behavior"></a>Biztonsági mentés és visszaállítás viselkedése

Egy kulcs egy Azure-hely egyik kulcstartójából készített biztonsági másolat visszaállítható egy másik Azure-beli helyen lévő kulcstartóba, feltéve, hogy mindkét feltétel teljesül:

- Mindkét Azure-hely ugyanahhoz a földrajzi helyhez tartozik
- Mindkét kulcstartó ugyanahhoz az Azure-előfizetéshez tartozik

Például egy biztonsági mentés egy adott előfizetés egy kulcs egy nyugat-indiai kulcstartóban, csak akkor állítható vissza egy másik kulcstartó ugyanazon az előfizetési és földrajzi helyen; Nyugat-India, Közép-India vagy Dél-India.

## <a name="regions-and-products"></a>Régiók és termékek

- [Azure-régiók](https://azure.microsoft.com/regions/)
- [Microsoft-termékek régiónként](https://azure.microsoft.com/regions/services/)

A régiók biztonsági világokhoz vannak rendelve, amelyek a táblázatok fő címsoraiként jelennek meg:

A termékek régiónkénti cikkben például az Amerika lap tartalmazza az USA keleti **régiója,** az USA középső régiója, az USA nyugati régiója számára az amerikai régióhoz való hozzárendelést. 

>[!NOTE]
>Kivételt képez, hogy az USA DOD EAST és az US DOD CENTRAL saját biztonsági világgal rendelkezik. 

Hasonlóképpen, az **Európa** lapon Észak-Európa és Nyugat-Európa egyaránt az európai régióra térképez. Ugyanez igaz az **ázsiai-csendes-óceáni** lapra is.



