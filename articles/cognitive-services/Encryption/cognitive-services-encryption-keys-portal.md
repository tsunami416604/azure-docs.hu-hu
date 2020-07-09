---
title: Ügyfél által felügyelt kulcsok Cognitive Services
titleSuffix: Cognitive Services
description: Megtudhatja, hogyan konfigurálhatja az ügyfél által felügyelt kulcsokat a Azure Key Vault segítségével a Azure Portal használatával. Az ügyfél által felügyelt kulcsok lehetővé teszik a hozzáférés-vezérlések létrehozását, elforgatását, letiltását és visszavonását.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: 1369f30c4a3bcb0a391a5f2b2a63191590afd622
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310699"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>Ügyfél által felügyelt kulcsok konfigurálása a Azure Key Vault Cognitive Services

Az ügyfél által felügyelt kulcsok Azure Key Vault használatával történő engedélyezésének folyamata Cognitive Services változó termék alapján. A következő hivatkozásokat használhatja a szolgáltatásra vonatkozó utasításokhoz:

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

* [Mi az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Cognitive Services ügyfél által felügyelt kulcs kérésének űrlapja](https://aka.ms/cogsvc-cmk)