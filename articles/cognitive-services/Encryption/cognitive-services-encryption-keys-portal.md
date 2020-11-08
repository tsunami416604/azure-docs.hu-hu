---
title: Cognitive Services Customer-Managed kulcsai
titleSuffix: Cognitive Services
description: Megtudhatja, hogyan konfigurálhatja az ügyfél által felügyelt kulcsokat a Azure Key Vault segítségével a Azure Portal használatával. Az ügyfél által felügyelt kulcsok lehetővé teszik a hozzáférés-vezérlések létrehozását, elforgatását, letiltását és visszavonását.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: 1d161c82c087fd86a3774f0d121330260b1574e4
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366094"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>Ügyfél által felügyelt kulcsok konfigurálása a Azure Key Vault Cognitive Services

A Customer-Managed kulcsoknak a Cognitive Services Azure Key Vault használatával történő engedélyezésének folyamata termékenként változó. A következő hivatkozásokat használhatja a szolgáltatásra vonatkozó utasításokhoz:

## <a name="vision"></a>Látás

* [Custom Vision inaktív adatok titkosítása](../Custom-Vision-Service/custom-vision-encryption-of-data-at-rest.md)
* [Szembenéző szolgáltatások titkosítása inaktív adatok esetén](../Face/face-encryption-of-data-at-rest.md)
* [Űrlap-felismerő titkosítása inaktív adatok esetén](../form-recognizer/form-recognizer-encryption-of-data-at-rest.md)

## <a name="language"></a>Nyelv

* [Inaktív adatok Language Understanding szolgáltatás titkosítása](../LUIS/luis-encryption-of-data-at-rest.md)
* [QnA Maker inaktív adatok titkosítása](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Inaktív adatok Translator-titkosítása](../translator/translator-encryption-of-data-at-rest.md)

## <a name="decision"></a>Döntés

* [Content Moderator inaktív adatok titkosítása](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [A REST-adatok személyre szabása](../personalizer/personalizer-encryption-of-data-at-rest.md)

## <a name="next-steps"></a>További lépések

* [Mi az Azure Key Vault](../../key-vault/general/overview.md)?
* [Cognitive Services Customer-Managed kulcs kérésének űrlapja](https://aka.ms/cogsvc-cmk)