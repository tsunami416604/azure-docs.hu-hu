---
title: TLS-lezárás Azure Key Vault tanúsítványokkal
description: Ismerje meg, hogyan integrálhatja az Azure Application Gatewayt a Key Vaultval a HTTPS-kompatibilis figyelőkhöz csatolt kiszolgálói tanúsítványokhoz.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 780f2774cb37e3d6d43ed5137c29119c0f63fd0a
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82743693"
---
# <a name="tls-termination-with-key-vault-certificates"></a>TLS-lezárás Key Vault tanúsítványokkal

A [Azure Key Vault](../key-vault/general/overview.md) egy platform által felügyelt titkos tároló, amely a titkok, a kulcsok és a TLS/SSL-tanúsítványok védelmére használható. Az Azure Application Gateway támogatja a HTTPS-kompatibilis figyelőkhöz csatolt kiszolgálói tanúsítványok Key Vault-integrációját. Ez a támogatás a Application Gateway v2 SKU-ra korlátozódik.

Key Vault integráció két modellt kínál a TLS-lezáráshoz:

- Explicit módon megadhatja a figyelőhöz csatolt TLS/SSL-tanúsítványokat. Ez a modell a TLS-/SSL-tanúsítványok átadásának hagyományos módja a TLS-lezárás Application Gateway.
- HTTPS-kompatibilis figyelő létrehozásakor megadhat egy meglévő Key Vault-tanúsítványra vagy titkos kódra mutató hivatkozást is.

Application Gateway integrációja Key Vault számos előnnyel jár, többek között:

- Erősebb biztonság, mivel a TLS/SSL-tanúsítványokat nem kezeli közvetlenül az Application Development csapata. Az integráció lehetővé teszi egy különálló biztonsági csapat számára a következőket:
  * Application Gateway-alkalmazások beállítása.
  * Az Application Gateway életciklusának szabályozása.
  * Engedélyek megadása a kiválasztott Application Gateway-átjárók számára a kulcstartóban tárolt tanúsítványok eléréséhez.
- Meglévő tanúsítványok a Key vaultba való importálásának támogatása. Vagy Key Vault API-k használatával új tanúsítványokat hozhat létre és kezelhet bármely megbízható Key Vault-partnerrel.
- A Key vaultban tárolt tanúsítványok automatikus megújításának támogatása.

Application Gateway jelenleg csak a szoftveresen érvényesített tanúsítványokat támogatja. Hardveres biztonsági modul (HSM) – az érvényesített tanúsítványok nem támogatottak. Ha Application Gateway Key Vault tanúsítványok használatára van konfigurálva, a példányok lekérik a tanúsítványt a Key Vaultról, és helyileg telepítik a TLS-leállításhoz. A példányok is lekérdezik Key Vault 24 órás időközönként a tanúsítvány megújított verziójának lekéréséhez, ha van ilyen. Ha a rendszer frissített tanúsítványt talál, a rendszer automatikusan elforgatja a HTTPS-figyelőhöz társított TLS/SSL-tanúsítványt.

> [!NOTE]
> A Azure Portal csak a kulcstartó tanúsítványait támogatja, a titkokat nem. Application Gateway továbbra is támogatja a kulcstartóra hivatkozó titkokat, de csak olyan nem portálon keresztüli erőforrásokon keresztül, mint például a PowerShell, a CLI, az API, az ARM-sablonok stb. 

## <a name="how-integration-works"></a>Az integráció működése

Application Gateway integrációja Key Vault megköveteli a három lépésből álló konfigurációs folyamatot:

1. **Felhasználó által hozzárendelt felügyelt identitás létrehozása**

   Egy meglévő, felhasználó által hozzárendelt felügyelt identitást hoz létre vagy használ újra, amelyet a Application Gateway a Key Vault tanúsítványok az Ön nevében való lekérésére használ. További információ: [Mi az az Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/overview.md). Ez a lépés új identitást hoz létre a Azure Active Directory bérlőben. Az identitást a személyazonosság létrehozásához használt előfizetés megbízhatónak tekinti.

1. **A Key Vault konfigurálása**

   Ezután importáljon egy meglévő tanúsítványt, vagy hozzon létre egy újat a kulcstartóban. A tanúsítványt az Application gatewayen keresztül futó alkalmazások fogják használni. Ebben a lépésben egy Key Vault-titkos kulcsot is használhat, amely jelszó nélküli, Base-64 kódolású PFX-fájlként van tárolva. Azt javasoljuk, hogy a Key vaultban a tanúsítvány típusú objektumokhoz elérhető automatikus megújítási képesség miatt a tanúsítvány típusát is használja. Miután létrehozott egy tanúsítványt vagy titkos kulcsot, a kulcstartóban definiált hozzáférési szabályzatok segítségével engedélyezheti, hogy az *identitás hozzáférjen a* titkos kulcshoz.
   
   > [!NOTE]
   > Ha az Application Gateway-t az Azure CLI vagy a PowerShell használatával, vagy a Azure Portal központilag telepített Azure-alkalmazáson keresztül telepíti, akkor az SSL-tanúsítványt Base64 kódolású PFX-fájlként tárolja a Key vaultban. Az üzembe helyezés során végre kell hajtania a [Azure Key Vault használata a biztonságos paraméterek értékének](../azure-resource-manager/templates/key-vault-parameter.md)megadásához című témakör lépéseit. 
   >
   > Különösen fontos, hogy a `enabledForTemplateDeployment` `true`következőre legyen beállítva:. Lehet, hogy a tanúsítvány jelszavas vagy jelszóval van elvégezve. Jelszóval rendelkező tanúsítvány esetén a következő példa egy alkalmazás-átjáróhoz tartozó ARM- `sslCertificates` `properties` sablon konfigurációjának lehetséges konfigurációját jeleníti meg. A `appGatewaySSLCertificateData` és `appGatewaySSLCertificatePassword` a értékeit a Key vaultban tekintjük át a következő szakaszban leírtak szerint: [Reference Secrets with Dynamic ID](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id). Kövesse a visszafelé lévő `parameters('secretName')` hivatkozásokat, hogy megtudja, hogyan történik a keresés. Ha a tanúsítvány jelszavas, hagyja ki a `password` bejegyzést.
   >   
   > ```
   > "sslCertificates": [
   >     {
   >         "name": "appGwSslCertificate",
   >         "properties": {
   >             "data": "[parameters('appGatewaySSLCertificateData')]",
   >             "password": "[parameters('appGatewaySSLCertificatePassword')]"
   >         }
   >     }
   > ]
   > ```

1. **Az Application Gateway konfigurálása**

   Az előző két lépés elvégzése után beállíthatja vagy módosíthatja a meglévő Application Gatewayt a felhasználó által hozzárendelt felügyelt identitás használatára. A HTTP-figyelő TLS/SSL-tanúsítványát úgy is beállíthatja, hogy az Key Vault tanúsítvány vagy a titkos azonosító teljes URI-JÁT mutasson.

   ![Key Vault-tanúsítványok](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>További lépések

[A TLS-megszakítás konfigurálása Key Vault tanúsítványokkal a Azure PowerShell használatával](configure-keyvault-ps.md)
