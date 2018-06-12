---
title: Az Azure Logic Apps-tanúsítványokkal B2B üzenetek biztonságos |} Microsoft Docs
description: A vállalati integrációs csomag B2B üzenetek biztonságos tanúsítványok hozzáadása
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 7ba76a15792fe40b2a628b030f06930641d197a3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299334"
---
# <a name="secure-b2b-messages-with-certificates"></a>A tanúsítványok biztonságos B2B üzenetek

Néha szüksége B2B kommunikációs bizalmas. Biztonságos B2B kommunikációt a vállalati integrációs alkalmazások, kifejezetten a logic apps segítségével is hozzáadhat tanúsítványok integrációs fiókját. Tanúsítványok olyan elektronikus kommunikációban résztvevő identitásának ellenőrzéséhez digitális dokumentumok.
Tanúsítványok segítségével biztonságos kommunikációt a következő lehetőségeket biztosítva:

* Üzenet tartalom titkosítása
* Üzenetek digitális aláírását  

Ezeket a tanúsítványokat a vállalati integrációs alkalmazások használhatja:

* Nyilvános tanúsítványokat, amelyek egy hitelesítésszolgáltatótól (CA) kell megvásárolni.
* Személyes tanúsítványok, amelyek saját kezűleg adhat ki. Ezek a tanúsítványok vannak más néven önaláírt tanúsítványokat.

## <a name="upload-a-public-certificate"></a>Nyilvános tanúsítvány feltöltése

Használatához a *nyilvános tanúsítvány* a logikai alkalmazások, amelyek B2B képességek állnak a rendelkezésére, először fel kell tölteni a tanúsítványt integrációs fiókját. A tulajdonságok megadása után a [megállapodások](logic-apps-enterprise-integration-agreements.md) hoz létre, a tanúsítvány segítségével biztosíthatja a B2B üzenetek érhető el.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza ki a fő Azure menü **minden szolgáltatás**. A keresési mezőbe, írja be a "integrációt", és válassza ki **integrációs fiókok**.

   ![Az integráció fiók](media/logic-apps-enterprise-integration-certificates/overview-1.png)  

3. A **integrációs fiókok**, válassza ki az integráció fiókra, amelyhez a tanúsítvány hozzáadása.

   ![Válassza ki a kívánt felvenni a tanúsítványt integrációs fiókot](media/logic-apps-enterprise-integration-certificates/overview-3.png)  

4. Válassza ki a **tanúsítványok** csempére.  

   ![Válassza ki a "Tanúsítványok"](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

5. A **tanúsítványok**, válassza a **Hozzáadás**.

   ![Válassza a "Hozzáadás"](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

6. A **tanúsítvány hozzáadása**, a tanúsítvány adatainak megadása.
   
   1. Adja meg a tanúsítvány **neve**. A tanúsítvány típusát, válassza a **nyilvános**.

   2. A jobb oldalon a **tanúsítvány** válassza ki azt a mappát ikonra. 
   Keresse meg és jelölje ki a feltölteni kívánt tanúsítványfájlt. 
   Amikor elkészült, válassza ki a **OK**.

      ![Nyilvános tanúsítvány feltöltése](media/logic-apps-enterprise-integration-certificates/certificate-3.png)

   Azure az beállításának ellenőrzése után a tanúsítvány feltöltése.

   ![Tekintse meg az új tanúsítvány](media/logic-apps-enterprise-integration-certificates/certificate-4.png) 

## <a name="upload-a-private-certificate"></a>Személyes tanúsítvány feltöltése

Használatához a *személyes tanúsítvány* a logikai alkalmazások, amelyek B2B képességek állnak a rendelkezésére, először fel kell tölteni a tanúsítványt integrációs fiókját. Először hozzáadni kívánt titkos kulcsot is kell [Azure Key Vault](../key-vault/key-vault-get-started.md). 

A tulajdonságok megadása után a [megállapodások](logic-apps-enterprise-integration-agreements.md) hoz létre, a tanúsítvány segítségével biztosíthatja a B2B üzenetek érhető el.

> [!NOTE]
> A személyes tanúsítványok, győződjön meg arról, hogy a megjelenjenek a megfelelő nyilvános tanúsítvány hozzáadása a [AS2 megállapodás](logic-apps-enterprise-integration-as2.md) küldeni és fogadni a beállítások az aláírási és üzenetek titkosítására.

1. [Adja hozzá a titkos kulcsot az Azure Key Vaultba](../key-vault/key-vault-get-started.md#add) , és adjon meg egy **kulcsnév**.
   
2. Engedélyezi az Azure Logic Apps Azure Key Vault a műveletek végrehajtásához. Hozzáférés biztosítása a Logic Apps szolgáltatás egyszerű, a PowerShell paranccsal, [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy), például:

   `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

4. Válassza ki a fő Azure menü **minden szolgáltatás**. A keresési mezőbe, írja be a "integrációt", és válassza ki **integrációs fiókok**.

   ![Az integráció fiók](media/logic-apps-enterprise-integration-certificates/overview-1.png) 

5. A **integrációs fiókok**, válassza ki az integráció fiókra, amelyhez a tanúsítvány hozzáadása.

6. Válassza ki a **tanúsítványok** csempére.  

   ![A tanúsítványok csempe kiválasztása](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

7. A **tanúsítványok**, válassza a **Hozzáadás**.   

   ![Kattintson a Hozzáadás gombra](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

8. A **tanúsítvány hozzáadása**, a tanúsítvány adatainak megadása.
   
   1. Adja meg a tanúsítvány **neve**. A tanúsítvány típusát, válassza a **titkos**.

   2. A jobb oldalon a **tanúsítvány** válassza ki azt a mappát ikonra. 
   Keresse meg és jelölje ki a feltölteni kívánt tanúsítványfájlt. 
   Jelölje be a **erőforráscsoport**, **Key Vault**, és **kulcsnév**. 
   Amikor elkészült, válassza ki a **OK**.

      ![Tanúsítvány hozzáadása](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)

   Azure az beállításának ellenőrzése után a tanúsítvány feltöltése.

   ![Tekintse meg az új tanúsítvány](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)

## <a name="next-steps"></a>További lépések

* [B2B-egyezmény létrehozása](logic-apps-enterprise-integration-agreements.md)
