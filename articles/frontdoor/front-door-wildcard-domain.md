---
title: Azure Bejárati ajtó – Helyettesítő karakteres tartományok támogatása
description: Ez a cikk segítséget nyújt annak megértéséhez, hogy az Azure Front Door hogyan támogatja a helyettesítő tartományok leképezését és kezelését az egyéni tartományok listájában
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: c568c9cc5c57098385cc7399459ec656cdbfc305
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537439"
---
# <a name="wildcard-domains"></a>Helyettesítő tartomány

A csúcstartományokon és altartományokon kívül helyettesítő tartománynevet is leképezhet az előtér-állomások vagy a Bejárati ajtó profil egyéni tartományainak listájára. Ha helyettesítő tartományai vannak a bejárati ajtóban, leegyszerűsíti az API- és alkalmazás- vagy webhely több altartományának forgalom-útválasztási viselkedését ugyanazon útválasztási szabályhoz, anélkül, hogy módosítania kellene a konfigurációt az egyes altartományok hozzáadásához és/vagy az egyes altartományok külön-külön történő megadásához. Például definiálhatja a művelettervet `customer1.contoso.com` `customer2.contoso.com`a `customerN.contoso.com` alkalmazáshoz, és ugyanazt az `*.contoso.com`útválasztási szabályt használhatja helyettesítő tartomány hozzáadásával.

A helyettesítő tartományok támogatásával megoldott legfontosabb forgatókönyvek közé tartoznak a következők:

- Már nem szükséges a bejárati ajtó minden altartományának üzembe helyezéséhez, majd a HTTPS-nek az egyes altartományok tanúsítványkötéséhez szükséges engedélyezése.
- Ha egy alkalmazás új altartományt ad hozzá, akkor már nem kell módosítania az éles bejárati ajtó konfigurációját. Máskülönben korábban hozzá kellett adnia az altartományt, tanúsítványt kellett kötni hozzá, hozzá kellett adni egy webalkalmazás-tűzfal (WAF) házirendet, hozzá kellett adni a tartományt a különböző útválasztási szabályokhoz.

> [!NOTE]
> Jelenleg a helyettesítő tartományok csak az API-n, a PowerShellen és a CLI-n keresztül támogatottak. Helyettesítő karakteres tartományok azure portalon keresztül idáig való hozzáadásának támogatása nem érhető el.

## <a name="adding-wildcard-domains"></a>Helyettesítő tartományhozzáadása

Helyettesítő karakteres tartományt az Előtér-állomások vagy a Tartományok szakaszban is belehet, hogy beszálljon a rendszerbe. Az altartományokhoz hasonlóan a Bejárati ajtó is ellenőrzi, hogy van-e CNAME leképezés a helyettesítő tartományhoz is. Ez a DNS-leképezés lehet `*.contoso.com` egy közvetlen `contoso.azurefd.net` CNAME leképezés, például az afdverify ideiglenes leképezésen keresztül vagy azon keresztül, például `afdverify.contoso.com` a Helyettesítő karakter cname leképezésének érvényesítéséhez `afdverify.contoso.azurefd.net` (az Azure DNS támogatja a helyettesítő rekordokat is).

Ha nem a maximális értéket ütik, a helyettesítő tartományhoz annyi egyszintű altartományt is hozzáadhat az előtér-állomásokhoz. az előtér-gazdagépek száma. Erre a funkcióra akkor lehet szükség, ha egy altartományhoz más útvonalat kell megadni, mint a többi tartományhoz (a helyettesítő tartományból), vagy egy adott altartományhoz más WAF-házirendet kell megadni. Szóval, lehetővé `*.contoso.com` teszi hozzá `foo.contoso.com` anélkül, `foo.bar.contoso.com` hogy ismét bizonyítani domain tulajdonjogát, de nem, mivel ez nem egy szintű aldomain . `*.contoso.com` További `foo.bar.contoso.com` tartománytulajdonjog-ellenőrzés `*.bar.contosonews.com` nélkül hozzá kell adni.

### <a name="limitations"></a>Korlátozások

1. Ha egy helyettesítő tartományt ad hozzá egy adott bejárati ajtó profilhoz, akkor ugyanez nem adható hozzá más bejárati ajtó profilhoz. 
2. Ha egy adott Bejárati ajtó profilban helyettesítő tartományt ad hozzá, akkor a helyettesítő tartomány bármely altartománya nem adható hozzá más bejárati ajtóhoz vagy egy Azure CDN-hez a Microsoft-profilból
3. Ha egy helyettesítő tartomány altartománya egy bejárati ajtó profilban vagy egy Microsoft-profilból származó Azure CDN-ben van hozzáadva, akkor a helyettesítő tartomány nem adható hozzá más Bejárati ajtó profilhoz. 
4. Ha két profil (bejárati ajtó vagy a Microsoft Azure CDN) rendelkezik a gyökértartomány különböző altartományai, majd helyettesítő tartományok nem adhatók hozzá egyik profilhoz sem.

## <a name="certificate-binding-for-wildcard-domains-and-its-subdomains"></a>Tanúsítványkötés helyettesítő tartományokhoz és altartományaihoz

A helyettesítő tartományHTTPS-forgalmának fogadásához engedélyeznie kell a HTTPS protokollt a helyettesítő tartományban. A helyettesítő tartomány tanúsítványkötéséhez helyettesítő tanúsítvány szükséges, azaz a tanúsítvány tulajdonosának nevének is rendelkeznie kell helyettesítő tartománysal.

> [!NOTE]
> Jelenleg csak a saját egyéni SSL-tanúsítvány beállítás érhető el a HTTPS engedélyezése helyettesítő tartományokban. A Bejárati ajtó által felügyelt tanúsítványok nem használhatók helyettesítő tartományban. 

Választhat, hogy ugyanazt a helyettesítő tanúsítványt használja a Key Vault az altartományok, vagy a bejárati ajtó által felügyelt tanúsítványok használata az altartományok is támogatott.
Ha egy helyettesítő tartományhoz altartományt ad hozzá, és a helyettesítő tartományhoz már van tanúsítvány társítva, akkor az altartomány HTTPS-je nem tiltható le. Az altartomány alapértelmezés szerint a helyettesítő tartomány tanúsítványkötését fogja használni, kivéve, ha egy másik Key Vault-tanúsítvány vagy a Bejárati ajtó által felügyelt tanúsítvány felülbírálja.

## <a name="web-application-firewall-for-wildcard-domains-and-its-subdomains"></a>Webes alkalmazás tűzfala helyettesítő tartományokhoz és aldomainjeihez

A WAF-házirendek más tartományokhoz hasonló helyettesítő tartományhoz csatolhatók. Egy helyettesítő tartomány altartományára más WAF-házirend alkalmazható. Az altartományok esetében explicit módon meg kell adnia a használandó WAF-házirendet, és akkor is, ha az ugyanaz a házirend, mint a helyettesítő tartomány. Az altartományok **nem** öröklik automatikusan a WAF-házirendet a helyettesítő tartományból.

Ha van egy forgatókönyv, amelyben nem szeretné, hogy a WAF egy altartományhoz fusson, akkor létrehozhat egy üres WAF-házirendet felügyelt vagy egyéni szabálykészlet nélkül.

## <a name="routing-rules-for-wildcard-domains-and-its-subdomains"></a>Útválasztási szabályok helyettesítő tartományokhoz és altartományaihoz

Útválasztási szabály konfigurálásakor kijelölhet egy helyettesítő tartományt előtér-állomásként. A helyettesítő tartomány és az altartományok esetében is eltérő útvonalviselkedést érhet el. Ahogy azt a [Bejárati ajtó útvonalegyeztetése](front-door-route-matching.md)is leírja, a tartomány legspecifikusabb egyezése a különböző útválasztási szabályok között futásidőben kerül kiválasztásra.

> [!WARNING]
> Ha két útválasztási szabályok, `*.foo.com/*` mint **az 1-es út:** leképezve háttér-pool a és **a 2-es útvonal:** `bar.foo.com/somePath/*` a B háttér-készlet, és ha egy kérelem érkezik `bar.foo.com/anotherPath/*`a, majd az ügyfelek hibákat fog látni, mint a Bejárati ajtó nem talál egyezést mindkét útvonalon. Ez azért van, mert a mi [útvonal-egyezési algoritmus](front-door-route-matching.md), Bejárati ajtó választja Route 2 alapján konkrétabb domain egyezés, de csak azt találja, hogy nincsenek egyező útvonal mintákat. 


## <a name="next-steps"></a>További lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- Megtudhatja, hogyan [adhat hozzá egyéni tartományt a Bejárati ajtón.](front-door-custom-domain.md)
- További információ [aHTTPS engedélyezéséről egyéni tartományban.](front-door-custom-domain-https.md)
