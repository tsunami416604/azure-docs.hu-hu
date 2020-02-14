---
title: Biztonságos B2B-üzenetek tanúsítványokkal
description: Tanúsítványok hozzáadása a Azure Logic Appsban található B2B-üzenetek biztonságossá tételéhez a Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: c1b48ae8191e2e5313d9037c791eca73c8a55691
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191379"
---
# <a name="improve-security-for-b2b-messages-by-using-certificates"></a>A B2B-üzenetek biztonságának növelése tanúsítványok használatával

Ha továbbra is meg kell őriznie a B2B-kommunikációt, a vállalati integrációs alkalmazásokban, különösen a Logic Appsben növelheti a B2B-kommunikáció biztonságát, ha tanúsítványokat ad hozzá az integrációs fiókjához. A tanúsítványok olyan digitális dokumentumok, amelyek az elektronikus kommunikáció résztvevői identitásait ellenőrizzék, és segítenek a kommunikációban az alábbi módokon:

* Üzenet tartalmának titkosítása.
* Üzenetek digitális aláírása.

Ezeket a tanúsítványokat a vállalati integrációs alkalmazásokban használhatja:

* [Nyilvános tanúsítványok](https://en.wikipedia.org/wiki/Public_key_certificate), amelyeket nyilvános internetes [hitelesítésszolgáltatótól](https://en.wikipedia.org/wiki/Certificate_authority) kell megvásárolnia, de nincs szükség kulcsokra. 

* Privát tanúsítványok vagy [*önaláírt tanúsítványok*](https://en.wikipedia.org/wiki/Self-signed_certificate), amelyeket Ön hoz létre és állít ki, de titkos kulcsokra is szükség van. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Nyilvános tanúsítvány feltöltése

Ha olyan logikai alkalmazásokban szeretne *nyilvános tanúsítványt* használni, amelyek B2B-képességekkel rendelkeznek, először fel kell töltenie a tanúsítványt az integrációs fiókjába. Miután definiálta a tulajdonságokat a létrehozott [szerződésekben](logic-apps-enterprise-integration-agreements.md) , a tanúsítvány elérhetővé válik a B2B-üzenetek biztonságossá tételéhez.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com). Az Azure fő menüjében válassza az **összes erőforrás**lehetőséget. A keresőmezőbe írja be az integrációs fiók nevét, majd válassza ki a kívánt integrációs fiókot.

   ![Integrációs fiók megkeresése és kiválasztása](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. Az **összetevők**területen válassza a **tanúsítványok** csempét.

   ![Válassza a tanúsítványok lehetőséget.](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. A **tanúsítványok**területen válassza a **Hozzáadás**lehetőséget. A **tanúsítvány hozzáadása**területen adja meg a tanúsítványhoz tartozó adatokat. Ha elkészült, kattintson **az OK gombra**.

   | Tulajdonság | Érték | Leírás | 
   |----------|-------|-------------|
   | **Name (Név)** | <*tanúsítvány neve*> | A tanúsítvány neve, amely ebben a példában a "publicCert". | 
   | **Tanúsítvány típusa** | Nyilvános | A tanúsítvány típusa |
   | **Tanúsítvány** | <*Certificate-file-name*> | A feltölteni kívánt tanúsítványfájl megkereséséhez és kiválasztásához válassza a **tanúsítvány** mező melletti mappa ikont. |
   ||||

   ![Válassza a Hozzáadás lehetőséget, adja meg a tanúsítvány részleteit](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Miután az Azure érvényesíti a kiválasztott beállításokat, az Azure feltölti a tanúsítványt.

   ![Az Azure új tanúsítványt jelenít meg](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Privát tanúsítvány feltöltése

Ha a logikai alkalmazásokban a B2B-képességekkel rendelkező *privát tanúsítványt* szeretne használni, először fel kell töltenie a tanúsítványt az integrációs fiókjába. Szükség van egy titkos kulcsra is, amelyet először a [Azure Key Vaulthoz](../key-vault/key-vault-get-started.md)kell hozzáadnia. 

Miután definiálta a tulajdonságokat a létrehozott [szerződésekben](logic-apps-enterprise-integration-agreements.md) , a tanúsítvány elérhetővé válik a B2B-üzenetek biztonságossá tételéhez.

> [!NOTE]
> Privát tanúsítványok esetén ügyeljen arra, hogy adjon hozzá egy megfelelő nyilvános tanúsítványt, amely megjelenik az [AS2-egyezmény](logic-apps-enterprise-integration-as2.md) **küldési és fogadási** beállításaiban az üzenetek aláírásához és titkosításához.

1. [Adja hozzá a titkos kulcsot Azure Key Vaulthoz](../key-vault/certificate-scenarios.md#import-a-certificate) , és adja meg a **kulcs nevét**.
   
2. Engedélyezi Azure Logic Apps számára, hogy műveleteket hajtson végre a Azure Key Vaulton. Ha hozzáférést szeretne biztosítani az Logic Apps egyszerű szolgáltatáshoz, használja a következő PowerShell [-parancsot: set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), például:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Jelentkezzen be az [Azure Portal](https://portal.azure.com). Az Azure fő menüjében válassza az **összes erőforrás**lehetőséget. A keresőmezőbe írja be az integrációs fiók nevét, majd válassza ki a kívánt integrációs fiókot.

   ![Integrációs fiók megkeresése](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. Az **összetevők**területen válassza a **tanúsítványok** csempét.  

   ![A tanúsítványok csempe kiválasztása](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. A **tanúsítványok**területen válassza a **Hozzáadás**lehetőséget. A **tanúsítvány hozzáadása**területen adja meg a tanúsítványhoz tartozó adatokat. Ha elkészült, kattintson **az OK gombra**.

   | Tulajdonság | Érték | Leírás | 
   |----------|-------|-------------|
   | **Name (Név)** | <*tanúsítvány neve*> | A tanúsítvány neve, amely ebben a példában a "privateCert". | 
   | **Tanúsítvány típusa** | Privát | A tanúsítvány típusa |
   | **Tanúsítvány** | <*Certificate-file-name*> | A feltölteni kívánt tanúsítványfájl megkereséséhez és kiválasztásához válassza a **tanúsítvány** mező melletti mappa ikont. Ha kulcstartót használ a titkos kulcshoz, a feltöltött fájl lesz a nyilvános tanúsítvány. | 
   | **Erőforráscsoport** | <*Integration-Account-Resource-group*> | Az integrációs fiókhoz tartozó erőforráscsoport, amely ebben a példában a "MyResourceGroup". | 
   | **Key Vault** | <*Key-Vault-name*> | Az Azure Key Vault neve |
   | **Kulcs neve** | <*kulcs neve*> | A kulcs neve |
   ||||

   ![Válassza a Hozzáadás lehetőséget, adja meg a tanúsítvány részleteit](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Miután az Azure érvényesíti a kiválasztott beállításokat, az Azure feltölti a tanúsítványt.

   ![Az Azure új tanúsítványt jelenít meg](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Következő lépések

* [B2B-szerződés létrehozása](logic-apps-enterprise-integration-agreements.md)
