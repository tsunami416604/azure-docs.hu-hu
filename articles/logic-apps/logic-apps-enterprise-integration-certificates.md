---
title: Biztonságos B2B-üzenetek tanúsítványokkal
description: Tanúsítványok hozzáadása a B2B-üzenetek biztonságossá tétele érdekében az Azure Logic Apps alkalmazásban a vállalati integrációs csomaggal
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: 19a1883685193e80da5f1365ec2a30db0b8754f6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450140"
---
# <a name="improve-security-for-b2b-messages-by-using-certificates"></a>A B2B-üzenetek biztonságának növelése tanúsítványok használatával

Ha bizalmasan kell kezelnie a B2B-kommunikációt, növelheti a B2B-kommunikáció biztonságát a vállalati integrációs alkalmazásokban, különösen a logikai alkalmazásokban, ha tanúsítványokat ad hozzá az integrációs fiókhoz. A tanúsítványok olyan digitális dokumentumok, amelyek ellenőrzik az elektronikus kommunikáció résztvevőinek személyazonosságát, és segítenek a kommunikáció biztonságossá tétele érdekében:

* Üzenet tartalmának titkosítása.
* Üzenetek digitális aláírása.

Ezeket a tanúsítványokat a vállalati integrációs alkalmazásokban használhatja:

* [Nyilvános tanúsítványok](https://en.wikipedia.org/wiki/Public_key_certificate), amelyeket nyilvános internetes [hitelesítésszolgáltatótól (CA)](https://en.wikipedia.org/wiki/Certificate_authority) kell megvásárolnia, de nincs szükség kulcsokra. 

* Saját vagy [*önaláírt tanúsítványok*](https://en.wikipedia.org/wiki/Self-signed_certificate), amelyeket saját maga hoz létre és állít ki, de személyes kulcsokat is igényel. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Nyilvános tanúsítvány feltöltése

Ha *nyilvános tanúsítványt* szeretne használni a B2B-képességekkel rendelkező logikai alkalmazásokban, először fel kell töltenie a tanúsítványt az integrációs fiókba. Miután definiálta a [tulajdonságokat](logic-apps-enterprise-integration-agreements.md) a létrehozott megállapodásokban, a tanúsítvány elérhetővé válik a B2B üzenetek biztonságossá tétele érdekében.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Az Azure főmenüjében válassza az **Összes erőforrás lehetőséget.** A keresőmezőbe írja be az integrációs fiók nevét, majd válassza ki a kívánt integrációs fiókot.

   ![Az integrációs fiók megkeresése és kiválasztása](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. **Az Összetevők csoportban**válassza a **Tanúsítványok csempét.**

   !["Tanúsítványok" kiválasztása](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. A **Tanúsítványok csoportban**válassza a **Hozzáadás**lehetőséget. A **Tanúsítvány hozzáadása csoportban**adja meg ezeket az adatokat a tanúsítványhoz. Ha elkészült, válassza az **OK** gombot.

   | Tulajdonság | Érték | Leírás | 
   |----------|-------|-------------|
   | **Név** | <*tanúsítvány neve*> | A tanúsítvány neve, amely "publicCert" ebben a példában | 
   | **Tanúsítvány típusa** | Nyilvános | A tanúsítvány típusa |
   | **Tanúsítvány** | <*tanúsítványfájlneve*> | A feltölteni kívánt tanúsítványfájl megkereséséhez és kijelöléséhez válassza a Tanúsítvány mező melletti **mappaikont.** |
   ||||

   ![Válassza a "Hozzáadás" lehetőséget, adja meg a tanúsítvány adatait](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Miután az Azure érvényesíti a választást, az Azure feltölti a tanúsítványt.

   ![Az Azure új tanúsítványt jelenít meg](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Privát tanúsítvány feltöltése

Ha a B2B-képességekkel rendelkező logikai alkalmazásokban *privát tanúsítványt* szeretne használni, először fel kell töltenie a tanúsítványt az integrációs fiókba. Emellett rendelkeznie kell egy személyes kulccsal, amelyet először hozzáad az [Azure Key Vaulthoz.](../key-vault/key-vault-get-started.md) 

Miután definiálta a [tulajdonságokat](logic-apps-enterprise-integration-agreements.md) a létrehozott megállapodásokban, a tanúsítvány elérhetővé válik a B2B üzenetek biztonságossá tétele érdekében.

> [!NOTE]
> Privát tanúsítványok esetén győződjön meg arról, hogy hozzáad egy megfelelő nyilvános tanúsítványt, amely megjelenik az [AS2-szerződés](logic-apps-enterprise-integration-as2.md) **Küldési és fogadási** beállításaiban az üzenetek aláírásához és titkosításához.

1. [Adja hozzá személyes kulcsát az Azure Key Vaulthoz,](../key-vault/certificates/certificate-scenarios.md#import-a-certificate) és adjon meg **egy kulcsnevet.**
   
2. Engedélyezze az Azure Logic Apps-t az Azure Key Vault on műveletek végrehajtására. A Logic Apps egyszerű szolgáltatáshoz való hozzáférés engedélyezéséhez használja a [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)PowerShell parancsot, például:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Az Azure főmenüjében válassza az **Összes erőforrás lehetőséget.** A keresőmezőbe írja be az integrációs fiók nevét, majd válassza ki a kívánt integrációs fiókot.

   ![Az integrációs fiók megkeresése](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. **Az Összetevők csoportban**válassza a **Tanúsítványok csempét.**  

   ![A Tanúsítványok csempe kiválasztása](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. A **Tanúsítványok csoportban**válassza a **Hozzáadás**lehetőséget. A **Tanúsítvány hozzáadása csoportban**adja meg ezeket az adatokat a tanúsítványhoz. Ha elkészült, válassza az **OK** gombot.

   | Tulajdonság | Érték | Leírás | 
   |----------|-------|-------------|
   | **Név** | <*tanúsítvány neve*> | A tanúsítvány neve, amely "privateCert" ebben a példában | 
   | **Tanúsítvány típusa** | Privát | A tanúsítvány típusa |
   | **Tanúsítvány** | <*tanúsítványfájlneve*> | A feltölteni kívánt tanúsítványfájl megkereséséhez és kijelöléséhez válassza a Tanúsítvány mező melletti **mappaikont.** Ha a személyes kulcs hoz használ egy kulcstartót, a feltöltött fájl lesz a nyilvános tanúsítvány. | 
   | **Erőforráscsoport** | <*integrációs-fiók-erőforrás-csoport*> | Az integrációs fiók erőforráscsoportja, amely ebben a példában a "MyResourceGroup" | 
   | **Key Vault** | <*kulcstartó neve*> | Az Azure-kulcstartó neve |
   | **Kulcs neve** | <*kulcsnév*> | A kulcs neve |
   ||||

   ![Válassza a "Hozzáadás" lehetőséget, adja meg a tanúsítvány adatait](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Miután az Azure érvényesíti a választást, az Azure feltölti a tanúsítványt.

   ![Az Azure új tanúsítványt jelenít meg](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>További lépések

* [B2B-megállapodás létrehozása](logic-apps-enterprise-integration-agreements.md)
