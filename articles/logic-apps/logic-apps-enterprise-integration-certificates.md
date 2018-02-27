---
title: "Tanúsítványok használata a vállalati integrációs csomag |} Microsoft Docs"
description: "Tanúsítványok használata a vállalati integrációs csomaggal |} Az Azure Logic Apps alkalmazások"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: cgronlun
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 0357e67a8920a57b2ab8b79ebd8dd3a64d888478
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="learn-about-certificates-and-enterprise-integration-pack"></a>A tanúsítványok és az Enterprise Integration Pack ismertetése
## <a name="overview"></a>Áttekintés
Vállalati integrációs B2B kommunikáció tanúsítványokat használ. A vállalati integrációs alkalmazások két típusú tanúsítványt használhatja:

* Nyilvános tanúsítványokat, amelyek egy hitelesítésszolgáltatótól (CA) kell megvásárolni.
* Személyes tanúsítványok, amelyek saját kezűleg adhat ki. Ezek a tanúsítványok vannak más néven önaláírt tanúsítványokat.

## <a name="what-are-certificates"></a>Mik azok a tanúsítványok?
Tanúsítványok olyan elektronikus kommunikációban résztvevő identitásának ellenőrzésére, hogy és, hogy is biztonságos elektronikus kommunikáció digitális dokumentumok.

## <a name="why-use-certificates"></a>Tanúsítványok miért érdemes használni?
Egyes esetekben B2B kommunikációs kell tartani bizalmas. Vállalati integrációs tanúsítványokat használ, a kommunikáció két módon:

* Az üzenetek tartalmának titkosításával
* Üzenetek digitális aláírásával  

## <a name="upload-a-public-certificate"></a>Nyilvános tanúsítvány feltöltése

Használatához a *nyilvános tanúsítvány* a logic Apps alkalmazásait B2B képességeket, először töltse fel a integrációs fiókjába.  

A tanúsítvány feltöltése után érhető el segítségével biztosíthatja a B2B üzenetek, ha azok tulajdonságai a [megállapodások](logic-apps-enterprise-integration-agreements.md) az Ön által létrehozott.  

A nyilvános tanúsítványokat feltöltése a integrációs fiókjába, az Azure-portálon való bejelentkezés után részletes lépései a következők:

1. Válassza ki **minden szolgáltatás** , és írja be **integrációs** szűrő keresőmezőbe. Válassza ki **integrációs fiókok** eredmények listájáról     
![Válassza ki a Tallózás gombra](media/logic-apps-enterprise-integration-certificates/overview-1.png)  
2. Válassza a kívánt felvenni a tanúsítványt integrációs fiókot.  
![Válassza ki a kívánt felvenni a tanúsítványt integrációs fiókot](media/logic-apps-enterprise-integration-certificates/overview-3.png)  
3. Válassza ki a **tanúsítványok** csempére.  
![A tanúsítványok csempe kiválasztása](media/logic-apps-enterprise-integration-certificates/certificate-1.png)
4. Az a **tanúsítványok** panel, amelyen megnyílik, válassza ki a **Hozzáadás** gombra.   
![Kattintson a Hozzáadás gombra](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
5. Adjon meg egy **neve** a tanúsítványt, és írja a tanúsítványt, majd válassza ki a **nyilvános** a legördülő listából.  
6. Válassza ki a mappára a jobb oldalon a **tanúsítvány** szövegmezőben. A fájlválasztó megnyitása, keresse meg és jelölje ki az integráció fiókjába feltölteni kívánt tanúsítványfájlt.
7. A tanúsítványt, majd válassza ki és **OK** a fájlválasztó a. Ez érvényesíti és feltölti a tanúsítványt az integráció fiókját.
8. Végezetül, a biztonsági a **Hozzáadás tanúsítvány** panelen válassza a **OK** gombra.  
![Az OK gombra](media/logic-apps-enterprise-integration-certificates/certificate-3.png)  
9. Válassza ki a **tanúsítványok** csempére. Az újonnan hozzáadott tanúsítványt kell megjelennie.  
![Tekintse meg az új tanúsítvány](media/logic-apps-enterprise-integration-certificates/certificate-4.png)  

## <a name="upload-a-private-certificate"></a>Személyes tanúsítvány feltöltése

Használatához a *személyes tanúsítvány* a logic apps B2B képességeket, a feltöltheti személyes tanúsítványt integrációs fiókjába az alábbi lépések megtételével

1. [Töltse fel a titkos kulcsot a Key Vault](../key-vault/key-vault-get-started.md "további információ a Key Vault") , és adjon meg egy **kulcs neve** 
   
   > [!TIP]
   > Engedélyeznie kell a Logic Apps a Key Vault műveletek végrehajtásához. Ön hozzáférést biztosíthat a Logic Apps szolgáltatás egyszerű a következő PowerShell-parancs segítségével: `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`  
   > 
   > 

Miután az előző lépésben, a személyes tanúsítvány hozzáadása integrációs fiók.

A személyes tanúsítványok feltöltése a integrációs fiókjába, az Azure-portálon való bejelentkezés után részletes lépései a következők:  
 
1. Válassza ki a integrációs fiókot, amelyhez hozzá szeretné felvenni a tanúsítványt, és válassza ki a **tanúsítványok** csempére.  
![A tanúsítványok csempe kiválasztása](media/logic-apps-enterprise-integration-certificates/certificate-1.png)  
2. Az a **tanúsítványok** panel, amelyen megnyílik, válassza ki a **Hozzáadás** gombra.   
![Kattintson a Hozzáadás gombra](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
3. Adjon meg egy **neve** a tanúsítványt, és válassza ki a tanúsítványt, írja be a **titkos** a legördülő listából.   
4. Válassza ki a mappára a jobb oldalon a **tanúsítvány** szövegmezőben. A fájlválasztó megnyitása után található a megfelelő nyilvános integrációs fiókja feltölteni kívánt tanúsítványt.   
   
   > [!Note]
   > Személyes tanúsítvány hozzáadása során fontos, hogy megjelenítése a megfelelő nyilvános tanúsítvány hozzáadása [AS2 megállapodás](logic-apps-enterprise-integration-as2.md) beállítások aláírásához és titkosításához az üzenetek küldésére és fogadására.
   > 
   >   

5. Válassza ki a **erőforráscsoport**, **Key Vault**, **kulcsnév** válassza ki a **OK** gombra.  
![tanúsítvány hozzáadása](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)  
6. Válassza ki a **tanúsítványok** csempére. Az újonnan hozzáadott tanúsítványt kell megjelennie.
![Tekintse meg az új tanúsítvány](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)  



* [B2B-egyezmény létrehozása](logic-apps-enterprise-integration-agreements.md)  
* [További információ a Key Vault](../key-vault/key-vault-get-started.md "Key Vault megismerése")  

