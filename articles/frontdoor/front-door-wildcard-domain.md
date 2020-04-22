---
title: Azure Bejárati ajtó – Helyettesítő karakteres tartományok támogatása
description: Ez a cikk segít megérteni, hogy az Azure Front Door hogyan támogatja a helyettesítő tartományok leképezését és kezelését az egyéni tartományok listájában.
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: 6d8a6d6f0b05b9b7fd0144959c82b6a2c9e659a3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768304"
---
# <a name="wildcard-domains"></a>Helyettesítő tartomány

A csúcstartományokon és altartományokon kívül helyettesítő tartománynevet is leképezhet az Előtér-állomások vagy az egyéni tartományok listájához az Azure Bejárati ajtajának profiljában. Helyettesítő tartományok az Azure bejárati ajtajának konfigurációjában leegyszerűsíti a forgalom útválasztási viselkedését több altartományhoz egy API-hoz, alkalmazáshoz vagy webhelyhez ugyanabból az útválasztási szabályból. Nem kell módosítania a konfigurációt az egyes altartományok külön-külön történő hozzáadásához vagy megadásához. Például megadhatja a művelettervet `customer1.contoso.com` `customer2.contoso.com`a `customerN.contoso.com` alkalmazáshoz, és ugyanazzal az útválasztási szabállyal és a helyettesítő tartomány `*.contoso.com`hozzáadásával adhatja meg.

A helyettesítő tartományok támogatásával javított legfontosabb forgatókönyvek a következők:

- Nem kell az Azure Bejárati ajtajához profil minden altartományát bevonnia, és engedélyeznie kell a HTTPS-t az egyes altartományok tanúsítványának kötéséhez.
- Már nem kell módosítania az éles Azure Bejárati ajtajának konfigurációját, ha egy alkalmazás új altartományt ad hozzá. Korábban hozzá kellett adnia az altartományt, tanúsítványt kellett kötnie hozzá, hozzá kellett kötnie egy webalkalmazás-tűzfal (WAF) házirendet, majd hozzá kellett adnia a tartományt a különböző útválasztási szabályokhoz.

> [!NOTE]
> Jelenleg a helyettesítő tartományok csak API-n, PowerShellen és az Azure CLI-n keresztül támogatottak. Helyettesítő karakteres tartományok hozzáadása és kezelése az Azure Portalon nem érhető el.

## <a name="adding-wildcard-domains"></a>Helyettesítő tartományhozzáadása

Az előtér-állomásokhoz vagy tartományokhoz helyettesítő tartományt adhat hozzá a szakasz alatt. Az altartományokhoz hasonlóan az Azure Bejárati ajtajáellenőrzi, hogy van-e CNAME rekordleképezés a helyettesítő tartományhoz. Ez a DNS-hozzárendelés lehet közvetlen `*.contoso.com` CNAME rekordleképezés, például a rendszerhez. `contoso.azurefd.net` Vagy használhatja az afdverify ideiglenes leképezést. Például `afdverify.contoso.com` a helyettesítő `afdverify.contoso.azurefd.net` karakter CNAME rekordleképezésének érvényesítésére leképezve.

> [!NOTE]
> Az Azure DNS helyettesítő rekordok használatát is támogatja.

A helyettesítő tartományhoz annyi egyszintű altartományt adhat hozzá az előtér-állomásokhoz, amíg az előtér-állomások száma meg nem múlik. Erre a funkcióra a következőkre lehet szükség:

- Másik útvonal definiálása egy altartományhoz, mint a többi tartomány (a helyettesítő tartományból).

- Egy adott altartományhoz más WAF-házirend tartozik. Például `*.contoso.com` lehetővé `foo.contoso.com` teszi a hozzáadást anélkül, hogy újra bizonyítania kellene a tartomány tulajdonjogát. De ez nem `foo.bar.contoso.com` teszi lehetővé, mert ez nem `*.contoso.com`egy szintű aldomain . További `foo.bar.contoso.com` tartománytulajdonjog-ellenőrzés nélküli `*.bar.contosonews.com` hozzáadáshoz hozzá kell adni.

A helyettesítő karakteres tartományokat és azok altartományait bizonyos korlátozásokkal is hozzáadhatja:

- Ha egy helyettesítő tartományt ad hozzá egy Azure Bejárati ajtó profilhoz:
  - A helyettesítő tartomány nem adható hozzá más Azure bejárati ajtó profilhoz.
  - A helyettesítő tartomány első szintű altartományai nem adhatók hozzá egy másik Azure Bejárati ajtó-profilhoz vagy egy Azure Content Delivery Network-profilhoz.
- Ha egy helyettesítő tartomány altartománya hozzáadódik egy Azure Bejárati ajtó profilhoz vagy az Azure Content Delivery Network-profilhoz, akkor a helyettesítő tartomány nem adható hozzá más Azure bejárati ajtó profilokhoz.
- Ha két profil (Az Azure Bejárati ajtaját vagy az Azure Content Delivery Network) rendelkezik a gyökértartomány különböző altartományai, majd helyettesítő tartományok nem adhatók hozzá egyik a profilok.

## <a name="certificate-binding"></a>Tanúsítvány kötése

A helyettesítő tartományHTTPS-forgalmának fogadásához engedélyeznie kell a HTTPS protokollt a helyettesítő tartományban. A helyettesítő tartomány tanúsítványkötéséhez helyettesítő tanúsítvány szükséges. Ez azt illeti, a tanúsítvány tulajdonosnevének is rendelkeznie kell helyettesítő tartományával.

> [!NOTE]
> Jelenleg csak a saját egyéni SSL-tanúsítvány beállítás érhető el a HTTPS engedélyezése helyettesítő tartományokban. Az Azure Front Door által felügyelt tanúsítványok nem használhatók helyettesítő tartományban.

Választhat, hogy ugyanazt a helyettesítő tanúsítványt az Azure Key Vault vagy az Azure Bejárati ajtó által felügyelt tanúsítványok altartományok.

Ha egy olyan helyettesítő tartományhoz ad hozzá altartományt, amelyhez már van hozzá tartozó tanúsítvány, akkor az altartomány HTTPS-je nem tiltható le. Az altartomány a helyettesítő tartomány tanúsítványkötését használja, kivéve, ha egy másik Key Vault vagy az Azure Bejárati ajtó által felügyelt tanúsítvány felülbírálja azt.

## <a name="waf-policies"></a>WAF-házirendek

A WAF-házirendek más tartományokhoz hasonlóan helyettesítő tartományokhoz is csatolhatók. Egy helyettesítő tartomány altartományára más WAF-házirend alkalmazható. Az altartományok esetében meg kell adnia a használni kívánt WAF-házirendet, még akkor is, ha az ugyanaz a házirend, mint a helyettesítő tartomány. Az altartományok *nem* öröklik automatikusan a WAF-házirendet a helyettesítő tartományból.

Ha nem szeretné, hogy egy altartományhoz WAF-házirend fusson, létrehozhat egy üres WAF-házirendet felügyelt vagy egyéni szabálykészlet nélkül.

## <a name="routing-rules"></a>Útválasztási szabályok

Útválasztási szabály konfigurálásakor kijelölhet egy helyettesítő tartományt előtér-állomásként. A helyettesítő tartományok és altartományok eltérő útvonalviselkedését is elláthatja. Ahogy azt az [Azure Bejárati ajtaját, hogyan útvonal egyeztetés,](front-door-route-matching.md)a legspecifikusabb egyezés a tartomány különböző útválasztási szabályok futásidőben választva.

> [!IMPORTANT]
> Az útválasztási szabályok között egyező útvonalmintákat kell látnia, különben az ügyfelek hibákat fognak látni. Például két útválasztási szabályok, mint`*.foo.com/*` a Route 1 (leképezve a`bar.foo.com/somePath/*` háttér-pool A) és a Route 2 (leképezve a b háttér-medence). Ezután egy kérés `bar.foo.com/anotherPath/*`érkezik a számára. Az Azure Bejárati ajtó a 2-es útvonalat választja egy konkrétabb tartományegyezés alapján, csak hogy ne találjon egyező útvonalmintákat az útvonalak között.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozhat létre Azure Bejárati ajtó profilt.](quickstart-create-front-door.md)
- Megtudhatja, hogyan [adhat hozzá egyéni tartományt az Azure Bejárati ajtaján.](front-door-custom-domain.md)
- További információ [aHTTPS engedélyezéséről egyéni tartományban.](front-door-custom-domain-https.md)
