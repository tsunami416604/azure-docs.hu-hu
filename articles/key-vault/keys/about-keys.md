---
title: A kulcsok ismertetése – Azure Key Vault
description: A kulcsok Azure Key Vault REST-felületének és fejlesztői adatainak áttekintése.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 2ae7b28d5e9e7a520ee8cbd090b6681d5ad7015a
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422756"
---
# <a name="about-keys"></a>Információ a kulcsokról

Azure Key Vault két típusú erőforrást biztosít a titkosítási kulcsok tárolásához és kezeléséhez:

|Erőforrás típusa|Kulcsfontosságú védelmi módszerek|Adatsík-végpont alap URL-címe|
|--|--|--|
| **Kulcstartók** | Szoftveres védelemmel ellátott<br/><br/>és<br/><br/>HSM által védett (prémium SKU-val)</li></ul> | https://{Vault-Name}. Vault. Azure. net |
| **Felügyelt HSM-készletek** | HSM által védett | https://{HSM-Name}. managedhsm. Azure. net |
||||

- **Tárolók – a** tárolók alacsony költséghatékonyságú, könnyen telepíthető, több-bérlős, zóna-rugalmas (ahol elérhető), nagy teljesítményű kulcskezelő megoldás, amely a leggyakoribb felhőalapú alkalmazási forgatókönyvekhez megfelelő.
- A **felügyelt HSM** által FELÜGYELt HSM egybérlős, rugalmas (ahol elérhető), magasan elérhető HSM biztosít a titkosítási kulcsok tárolásához és kezeléséhez. A leginkább megfelelő alkalmazások és használati forgatókönyvek esetében, amelyek nagy értékű kulcsokat kezelnek. Emellett segít a legszigorúbb biztonsági, megfelelőségi és szabályozási követelményeknek való megfelelésben. 

> [!NOTE]
> A tárolók lehetővé teszik a titkosítási kulcsok mellett különféle típusú objektumok, például a titkok, a tanúsítványok és a Storage-fiókok kulcsának tárolását és kezelését is.

A Key Vaultban található titkosítási kulcsok JSON webkulcs [JWK] objektumként jelennek meg. A JavaScript Object Notation (JSON) és a JavaScript Object aláírási és titkosítási (JOSE) specifikációi a következők:

-   [JSON-webkulcs (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON webes titkosítás (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON webes algoritmusok (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON webes aláírás (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

Az alap JWK-/JWA-specifikációk a Azure Key Vault és a felügyelt HSM-implementációk egyedi alaptípusának engedélyezésére is kiterjednek. 

HSM-védelemmel ellátott kulcsok (más néven HSM-kulcsok) feldolgozása egy HSM-ben (hardveres biztonsági modul) történik, és mindig az HSM védelmi határa marad. 

- A tárolók az **FIPS 140-2 2. szintű** hitelesített HSM védik a HSM-kulcsok a megosztott HSM háttér-infrastruktúrában való ellátásához. 
- A felügyelt HSM-készletek a **FIPS 140-2 3. szintű** hitelesített HSM-modulokat használják a kulcsok védetté tenni. Mindegyik HSM-készlet egy elkülönített, egybérlős példány, amely saját [biztonsági tartománnyal](../managed-hsm/security-domain.md) rendelkezik, és teljes titkosítási elkülönítést biztosít minden más HSM-készlettől, amely ugyanazokat a hardveres infrastruktúrát is megosztja.

Ezek a kulcsok egyetlen bérlős HSM-készletekben vannak védve. Az RSA, az EC és a szimmetrikus kulcs importálható puha formában, vagy egy támogatott HSM-eszközről is exportálható. A HSM-készletekben is létrehozhat kulcsokat. Amikor HSM-kulcsokat importál a [BYOK (saját kulcs használata) specifikációban](../keys/byok-specification.md)ismertetett módszerrel, az lehetővé teszi a biztonságos szállítási kulcsra vonatkozó anyagok kezelését a felügyelt HSM-készletekbe. 

A földrajzi határokra vonatkozó további információkért lásd: [Microsoft Azure Adatvédelmi központ](https://azure.microsoft.com/support/trust-center/privacy/)

## <a name="key-types-and-protection-methods"></a>A kulcsok típusai és védelmi módszerei

Key Vault támogatja az RSA, az EC és a szimmetrikus kulcsokat. 

### <a name="hsm-protected-keys"></a>HSM-védett kulcsok

|Kulcs típusa|Tárolók (csak prémium SKU)|Felügyelt HSM-készletek|
|--|--|--|--|
**EC-HSM** : elliptikus görbe kulcsa|FIPS 140-2 2. szint HSM|FIPS 140-2 Level 3 HSM
**RSA-HSM** : RSA-kulcs|FIPS 140-2 2. szint HSM|FIPS 140-2 Level 3 HSM
**Oct – HSM** : szimmetrikus|Nem támogatott|FIPS 140-2 Level 3 HSM
||||

### <a name="software-protected-keys"></a>Szoftveres védelemmel ellátott kulcsok

|Kulcs típusa|Kulcstartók|Felügyelt HSM-készletek|
|--|--|--|--|
**RSA** : "szoftveres védelemmel ellátott" RSA-kulcs|FIPS 140-2 1. szint|Nem támogatott
**EC** : "szoftveres védelemmel ellátott" elliptikus görbe kulcsa|FIPS 140-2 1. szint|Nem támogatott
||||

Az egyes kulcshasználat, algoritmusok, műveletek, attribútumok és címkék részleteiért tekintse meg a [kulcs típusát, algoritmusait és műveleteit](about-keys-details.md) .

## <a name="next-steps"></a>További lépések
- [Tudnivalók a Key Vaultról](../general/overview.md)
- [A Managed HSM ismertetése](../managed-hsm/overview.md)
- [Információ a titkos kulcsokról](../secrets/about-secrets.md)
- [Információ a tanúsítványokról](../certificates/about-certificates.md)
- [Key Vault REST API áttekintése](../general/about-keys-secrets-certificates.md)
- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)