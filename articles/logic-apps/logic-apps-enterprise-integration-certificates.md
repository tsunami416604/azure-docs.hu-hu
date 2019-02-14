---
title: A tanúsítványok – Azure Logic Apps B2B-üzenetek védelme |} A Microsoft Docs
description: Tanúsítványok és az Enterprise Integration Pack Azure Logic Apps B2B-üzenetek védelmére hozzáadása
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
manager: jeconnoc
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.suite: integration
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: 38bc1615c0849a33ddfa5790a66fc05d681ce339
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244929"
---
# <a name="secure-b2b-messages-with-certificates"></a>A tanúsítványok biztonságos B2B-üzenetek

B2B-kommunikáció bizalmasan kell, amikor gondoskodhat B2B kommunikációs a vállalati integrációs alkalmazások, pontosabban a logic apps, tanúsítványokat ad hozzá az integrációs fiók. Tanúsítványok digitális dokumentumok, amely az identitások keresése a résztvevők az elektronikus kommunikáció, és segítenek a biztonságos kommunikáció a következő lehetőségeket biztosítva:

* Üzenet tartalmának titkosítása.
* Üzenetek digitális aláírását. 

Ezeket a tanúsítványokat a vállalati integrációs alkalmazásokban használható:

* [A nyilvános tanúsítványok](https://en.wikipedia.org/wiki/Public_key_certificate), amely a nyilvános interneten vásárolhatja meg kell [hitelesítésszolgáltatói (CA)](https://en.wikipedia.org/wiki/Certificate_authority) azonban nem igénylik bármilyen kulcsok. 

* A privát tanúsítványok vagy [ *önaláírt tanúsítványokat*](https://en.wikipedia.org/wiki/Self-signed_certificate), amely létrehozása, és adja ki saját kezűleg de az is szükség van a titkos kulcsok. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Nyilvános tanúsítvány feltöltése

Használata egy *nyilvános tanúsítvány* a logic apps B2B-funkciói rendelkező, akkor először fel kell töltenie a tanúsítvány az integrációs fiókba. A tulajdonságok megadása után a [szerződések](logic-apps-enterprise-integration-agreements.md) , hogy hoz létre, a tanúsítványt, amellyel biztonságossá teheti a B2B-üzenetek érhető el.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Az Azure fő menüjéből válassza **összes erőforrás**. A keresőmezőbe adja meg az integrációs fiók nevét, és válassza ki a kívánt integrációs fiók.

   ![Keresse meg és válassza ki az integrációs fiók](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. A **összetevők**, válassza ki a **tanúsítványok** csempére.

   ![Válassza a "Tanúsítványok"](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. A **tanúsítványok**, válassza a **Hozzáadás**. A **tanúsítvány hozzáadása**, adja meg ezeket az adatokat a tanúsítványt. Ha elkészült, válassza ki a **OK**.

   | Tulajdonság | Érték | Leírás | 
   |----------|-------|-------------|
   | **Name (Név)** | <*tanúsítvány neve*> | A tanúsítvány neve, amely ebben a példában a "publicCert" | 
   | **Tanúsítvány típusa** | Nyilvános | A tanúsítvány típusa |
   | **Tanúsítvány** | <*certificate-file-name*> | Keresse meg és válassza ki a feltöltendő tanúsítványfájlt, válassza a mappa ikont a a **tanúsítvány** mezőbe. |
   ||||

   ![Válassza az "Add", adja meg a tanúsítvány részletei](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Miután az Azure ellenőrzi a kijelölt, az Azure feltölti a tanúsítványt.

   ![Az Azure új tanúsítvány jeleníti meg.](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Privát tanúsítvány feltöltése

Használata egy *személyes tanúsítvány* a logic apps B2B-funkciói rendelkező, akkor először fel kell töltenie a tanúsítvány az integrációs fiókba. Rendelkezik titkos kulccsal, amely először hozzá kell [Azure Key Vault](../key-vault/key-vault-get-started.md). 

A tulajdonságok megadása után a [szerződések](logic-apps-enterprise-integration-agreements.md) , hogy hoz létre, a tanúsítványt, amellyel biztonságossá teheti a B2B-üzenetek érhető el.

> [!NOTE]
> Privát tanúsítványok esetén győződjön meg arról, hogy a megfelelő nyilvános tanúsítvány megjelenő hozzáadása a [AS2-egyezményt](logic-apps-enterprise-integration-as2.md) **küldési és fogadási** aláírása és titkosítása üzenetek beállításait.

1. [Adja hozzá a titkos kulcsot az Azure Key Vaultba](../key-vault/certificate-scenarios.md#import-a-certificate) , és adja meg egy **kulcsnév**.
   
2. Engedélyezze az Azure Logic Apps műveletek végrehajtása az Azure Key Vaultban. Hozzáférési jogot a Logic Apps szolgáltatás egyszerű, használja a PowerShell-paranccsal [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), például:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Az Azure fő menüjéből válassza **összes erőforrás**. A keresőmezőbe adja meg az integrációs fiók nevét, és válassza ki a kívánt integrációs fiók.

   ![Keresse meg az integrációs fiók](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. A **összetevők**, válassza ki a **tanúsítványok** csempére.  

   ![A tanúsítványok csempe kiválasztása](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. A **tanúsítványok**, válassza a **Hozzáadás**. A **tanúsítvány hozzáadása**, adja meg ezeket az adatokat a tanúsítványt. Ha elkészült, válassza ki a **OK**.

   | Tulajdonság | Érték | Leírás | 
   |----------|-------|-------------|
   | **Name (Név)** | <*tanúsítvány neve*> | A tanúsítvány neve, amely ebben a példában a "privateCert" | 
   | **Tanúsítvány típusa** | Privát | A tanúsítvány típusa |
   | **Tanúsítvány** | <*certificate-file-name*> | Keresse meg és válassza ki a feltöltendő tanúsítványfájlt, válassza a mappa ikont a a **tanúsítvány** mezőbe. | 
   | **Erőforráscsoport** | <*integration-account-resource-group*> | Az integrációs fiók erőforráscsoportot, amely ebben a példában a "MyResourceGroup" | 
   | **Key Vault** | <*key-vault-name*> | Az Azure key vault neve |
   | **Kulcsnév** | <*key-name*> | A kulcs neve |
   ||||

   ![Válassza az "Add", adja meg a tanúsítvány részletei](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Miután az Azure ellenőrzi a kijelölt, az Azure feltölti a tanúsítványt.

   ![Az Azure új tanúsítvány jeleníti meg.](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>További lépések

* [A B2B-egyezmény létrehozása](logic-apps-enterprise-integration-agreements.md)
