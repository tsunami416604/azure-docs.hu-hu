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
ms.openlocfilehash: 1369f30c4a3bcb0a391a5f2b2a63191590afd622
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84310699"
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

* [Mi az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Cognitive Services Customer-Managed kulcs kérésének űrlapja](https://aka.ms/cogsvc-cmk)